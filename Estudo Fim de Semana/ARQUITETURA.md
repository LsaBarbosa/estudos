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

 # Hexagonal
| Conceito | O que é | Trade-off | Uso real em produção |
|---|---|---|---|
| **Core** | Núcleo da aplicação. Contém principalmente **domínio, regras de negócio e casos de uso**, evitando dependência direta de banco, HTTP, Kafka, Spring Data etc. | Aumenta o isolamento e testabilidade, mas exige disciplina para não deixar detalhes de infraestrutura vazarem para o Core. Em sistemas CRUD simples pode adicionar abstrações desnecessárias. | Em um `payment-service`, contém regras como validar pagamento, aprovar/rejeitar transações e coordenar o caso de uso, sem conhecer PostgreSQL, Kafka ou API antifraude. |
| **Ports** | Contratos que definem como o Core se comunica com o exterior. Representam **capacidades** oferecidas ou requeridas pela aplicação. Em Java, normalmente são interfaces. | Adicionam abstrações e classes/interfaces extras. Criar uma Port para qualquer operação trivial pode gerar overengineering. | Interfaces como `CreatePaymentUseCase`, `PaymentRepositoryPort`, `FraudCheckPort` e `EventPublisherPort`. |
| **Input Port / Inbound Port** | Define uma **operação que a aplicação disponibiliza**. Normalmente representa um caso de uso. | Pode parecer redundante quando existe apenas um Controller e uma implementação simples. Torna-se útil quando existem múltiplos mecanismos de entrada ou necessidade de forte desacoplamento. | `CreateOrderUseCase`, que pode ser chamado por REST, Kafka Consumer, GraphQL ou Scheduler sem alterar a regra de negócio. |
| **Output Port / Outbound Port** | Define uma **capacidade externa necessária ao Core**. O Core define o contrato, mas não conhece a implementação tecnológica. | Pode introduzir abstrações que não trazem benefício quando a infraestrutura dificilmente mudará e não há necessidade de isolamento para testes. | `PaymentRepositoryPort`, `FraudCheckPort`, `NotificationPort`, `EventPublisherPort`. Podem ser implementados por PostgreSQL, REST, Kafka, SNS etc. |
| **Adapter** | Implementação que conecta uma Port a uma tecnologia ou mecanismo concreto. | Adiciona uma camada de tradução entre Core e infraestrutura. Frequentemente exige DTOs, mappers e conversões adicionais. | Controller REST, Consumer Kafka, Repository JPA, cliente HTTP, produtor Kafka, integração com S3 etc. |
| **Inbound Adapter** | Adapter que **inicia uma interação com o Core**, chamando uma Input Port. | Pode haver alguma duplicação de tradução/validação quando o mesmo caso de uso possui diversos canais de entrada. | `OrderController`, `KafkaOrderConsumer`, `SqsListener`, `GraphQLResolver`, Scheduler. Todos podem chamar o mesmo `CreateOrderUseCase`. |
| **Outbound Adapter** | Implementa uma Output Port e conecta o Core a recursos externos. | Requer mapeamento entre modelo de domínio e modelo tecnológico. Pode aumentar a quantidade de classes. | `JpaPaymentRepositoryAdapter`, `KafkaEventPublisherAdapter`, `FraudHttpAdapter`, `S3DocumentAdapter`. |
| **Direção da dependência** | As dependências de código devem apontar **para o Core**, e não do Core para infraestrutura. O Core conhece abstrações; infraestrutura conhece e implementa essas abstrações. | Exige controle arquitetural. Um simples `@Autowired JpaRepository` dentro do caso de uso pode quebrar a separação. | `CreatePaymentService → PaymentRepositoryPort ← JpaPaymentRepositoryAdapter`. O caso de uso conhece a Port; JPA fica no adapter. |

## Corelações
| Conceito | Correlação com Arquitetura Hexagonal | Trade-off | Uso real em produção |
|---|---|---|---|
| **Dependency Inversion — DIP** | É um dos principais fundamentos da Hexagonal. O Core não depende de implementações de baixo nível; depende de abstrações definidas de acordo com suas necessidades. | Exige criação de interfaces e separação entre abstração e implementação. Aplicado indiscriminadamente pode gerar abstrações sem valor. | `PaymentService` depende de `PaymentRepositoryPort`, e `JpaPaymentRepositoryAdapter` implementa essa Port. O domínio não conhece JPA. |
| **Dependency Injection — DI** | É o mecanismo utilizado para fornecer ao Core as implementações concretas das Ports. **DI não é DIP**: DI injeta dependências; DIP define a direção arquitetural dessas dependências. | Normalmente requer um container IoC como Spring ou configuração manual de objetos. Uma aplicação excessivamente dependente do container pode dificultar a leitura do fluxo. | O Spring injeta `JpaPaymentRepositoryAdapter` onde existe uma dependência `PaymentRepositoryPort`. |
| **Spring** | Deve atuar principalmente como **framework de infraestrutura e composição**, não como elemento central do domínio. Spring MVC, Spring Data, Kafka e WebClient normalmente ficam nos adapters. | Isolar completamente Spring aumenta configuração e quantidade de classes. Em projetos pequenos, algum acoplamento pragmático pode ser aceitável. | `@RestController` no inbound adapter, Spring Data no persistence adapter, `KafkaTemplate` no outbound adapter e `@Configuration` realizando o wiring. |
| **Testes** | Ports tornam o Core facilmente testável, pois dependências externas podem ser substituídas por mocks, stubs ou fakes. | Mocks em excesso podem produzir testes frágeis ou distantes do comportamento real. Nem tudo deve ser mockado. | Um `CreateOrderService` pode ser testado com Mockito sem PostgreSQL, Kafka, HTTP Server ou Spring Context. |
| **Testcontainers** | Complementa Hexagonal testando os **outbound adapters contra infraestrutura real**, enquanto o Core continua sendo testado isoladamente. | Testes são mais lentos que unitários e necessitam Docker/container runtime. Também exigem gerenciamento do ciclo dos containers. | Testar `JpaPaymentRepositoryAdapter` contra PostgreSQL real, `KafkaEventPublisherAdapter` contra Kafka e adapters Redis contra Redis real. |
```text
Arquitetura Hexagonal
        │
        ├── Ports & Adapters
        │
        ├── Dependency Inversion
        │        │
        │        └── Core depende de abstrações
        │
        ├── Dependency Injection
        │        │
        │        └── conecta implementação à abstração
        │
        ├── Spring
        │        │
        │        └── composição + adapters
        │
        └── Testabilidade
                 │
                 ├── Core → Unit Tests
                 │
                 └── Adapter → Integration Tests
                                  │
                                  └── Testcontainers
```                                
 
