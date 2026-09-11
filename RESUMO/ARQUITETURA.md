# Microserviço
## Microserviço Fundamento
| Tema | Conceito | Vantagem | Trade-off | Uso real em produção |
|---|---|---|---|---|
| **Microserviço** | Estilo arquitetural em que o sistema é dividido em serviços independentes, normalmente alinhados a capacidades de negócio ou bounded contexts. | Deploy independente, escalabilidade por serviço, isolamento de responsabilidades, evolução independente. | Mais complexidade operacional, rede, observabilidade, consistência distribuída, CI/CD, segurança e troubleshooting. | E-commerce separando `Order`, `Payment`, `Inventory`, `Shipping`, cada um com ciclo de vida próprio. |
| **Database per Service** | Cada microserviço é proprietário dos seus dados e seu banco ou schema lógico. Outros serviços não acessam diretamente suas tabelas. | Reduz acoplamento, preserva autonomia, permite tecnologias de persistência diferentes. | Consultas distribuídas ficam mais difíceis; joins entre serviços deixam de existir; exige eventos, APIs, CQRS ou materialized views. | `Order Service` usa PostgreSQL; `Catalog Service` usa MongoDB; `Cache Service` usa Redis. |
| **Comunicação assíncrona** | Serviços trocam mensagens ou eventos através de broker sem esperar resposta imediata. | Menor acoplamento temporal, maior resiliência, melhor escalabilidade, vários consumidores. | Consistência eventual, duplicidade, ordenação, reprocessamento, tracing e debugging mais difíceis. | Kafka publica `OrderCreated`; Payment, Inventory e Notification processam separadamente. |
| **Comunicação síncrona** | Um serviço chama outro diretamente e aguarda uma resposta. Ex.: HTTP REST ou gRPC. | Simples de entender, fácil para operações request/response, resposta imediata. | Acoplamento temporal, latência acumulada, cascata de falhas. | `Order Service` consulta `Customer Service` para validar cliente antes de executar uma operação. |
| **Transação distribuída** | Operação de negócio que envolve múltiplos serviços e múltiplos bancos. | Permite representar processos de negócio distribuídos. | Não existe um `@Transactional` único cobrindo todos os serviços; rollback global é difícil. | Criar pedido, reservar estoque, cobrar pagamento e confirmar envio. |
| **Consistência eventual** | Os serviços podem ficar temporariamente inconsistentes, mas convergem para um estado consistente após processamento de eventos. | Favorece disponibilidade, escalabilidade e desacoplamento. | Dados podem estar momentaneamente defasados; exige tratamento explícito no domínio e UX. | Pedido aparece como `PAYMENT_PENDING` durante alguns segundos até receber `PaymentApproved`. |

## Microserviço Transações
| Tema | Conceito | Vantagem | Trade-off | Uso real em produção |
|---|---|---|---|---|
| **Idempotência** | Executar a mesma operação mais de uma vez produz o mesmo efeito que executá-la uma única vez. | Evita cobranças, reservas ou alterações duplicadas. Fundamental em sistemas com retries e mensageria. | Exige identificador único, persistência de estado e controle adicional. | Kafka entrega `PaymentRequested` duas vezes, mas Payment processa somente uma cobrança usando `eventId`. |
| **Transactional Outbox** | Alteração do domínio e registro do evento são persistidos na mesma transação local. Posteriormente o evento é publicado. | Evita inconsistência entre banco e broker. | Adiciona tabela Outbox, processo de publicação, cleanup e observabilidade. | Salvar `Order` e `OrderCreated` na mesma transação PostgreSQL; Debezium publica posteriormente no Kafka. |
| **Inbox Pattern** | Consumidor registra quais mensagens já processou antes ou durante o processamento. | Evita processamento duplicado e facilita idempotência. | Crescimento da tabela Inbox, cleanup, custo adicional por mensagem. | `Payment Service` recebe `OrderCreated`, verifica `messageId`; se já existir em `processed_messages`, ignora. |

```text
Outbox
→ garante que o produtor não perca o evento.

Inbox
→ garante que o consumidor não processe o mesmo evento várias vezes.
```
### SAGA
| Coreografia | Orquestração |
|---|---|
| serviços reagem a eventos | coordenador controla fluxo |
| menor acoplamento central | fluxo explícito |
| simples para poucos passos | melhor para workflows complexos |
| difícil visualizar fluxos grandes | maior dependência do orquestrador |
| eventos conduzem processo | comandos conduzem processo |
## Resiliência
| Tema | Conceito | Vantagem | Trade-off | Uso real em produção |
|---|---|---|---|---|
| **Retry** | Reexecuta uma operação após uma falha transitória. | Recupera erros temporários automaticamente. | Pode aumentar carga e provocar retry storm. Deve usar backoff e jitter. | Retry de leitura em API externa após `503 Service Unavailable`. |
| **Timeout** | Limita quanto tempo uma chamada pode esperar por resposta. | Evita threads ou conexões presas indefinidamente. | Timeout curto demais causa falsos erros; longo demais aumenta saturação. | Payment Client aguarda no máximo 2 segundos pelo gateway externo. |
| **Circuit Breaker** | Interrompe temporariamente chamadas para um serviço com alta taxa de falha. | Evita cascata de falhas e reduz pressão sobre serviço degradado. | Exige thresholds corretos; pode bloquear chamadas durante recuperação parcial. | Após 50% de falhas no Payment Service, circuito abre e falha rapidamente. |
| **Bulkhead** | Isola recursos entre dependências ou operações. | Uma dependência problemática não consome todos os recursos da aplicação. | Mais configuração e menor compartilhamento de recursos. | Separar pool de threads de Payment e Inventory. |
| **API Gateway** | Ponto de entrada para clientes, responsável por roteamento e políticas transversais. | Centraliza autenticação, rate limit, TLS, routing e observabilidade. | Pode virar gargalo ou monólito se acumular regra de negócio. | AWS API Gateway, Kong, NGINX ou Spring Cloud Gateway na frente dos serviços. |
| **Service Discovery** | Mecanismo que permite localizar dinamicamente instâncias disponíveis de serviços. | Evita dependência de IP fixo e suporta escalabilidade dinâmica. | Introduz infraestrutura e resolução dinâmica. | Kubernetes Service + DNS resolve `payment-service` para pods disponíveis. |
#### Fluxo 
```text
Request
   ↓
Timeout
   ↓
falhou?
   ↓
Retry com backoff
   ↓
muitas falhas?
   ↓
Circuit Breaker OPEN
```

## Containerização Microserviço
| Tema | Conceito | Vantagem | Trade-off | Uso real em produção |
|---|---|---|---|---|
| **Container** | Processo isolado empacotado em uma imagem reproduzível. Docker é a tecnologia mais conhecida. | Portabilidade, isolamento, ambiente reproduzível, deploy simples. | Exige gerenciamento de imagens, recursos, segurança, logs e configuração externa. | Cada Spring Boot é empacotado como imagem OCI e executado em containers. |
| **Kubernetes** | Plataforma para orquestração de containers. Gerencia deploy, replicação, recuperação, rede e configuração. | Auto-healing, scaling, rolling update, service discovery, configuração declarativa. | Alta complexidade operacional e curva de aprendizado. | Executar 10 réplicas de Payment Service com Deployment + Service + HPA. |

## Observabilidade
| Tema | Conceito | Vantagem | Trade-off | Uso real em produção |
|---|---|---|---|---|
| **Distributed Tracing** | Rastreamento de uma requisição através de vários serviços usando `traceId` e `spanId`. | Permite descobrir onde ocorreu latência ou falha dentro de um fluxo distribuído. | Tem custo de instrumentação, armazenamento e sampling. | OpenTelemetry rastreia Gateway → Order → Payment → Inventory. |
| **Logs** | Registro estruturado de eventos internos da aplicação. | Ajuda investigação de falhas, auditoria e diagnóstico. | Alto volume, custo de armazenamento e necessidade de correlação. | Logs JSON enviados para Elasticsearch, Loki, Datadog ou Splunk. |
 
 
