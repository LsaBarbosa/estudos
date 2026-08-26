# FASE 13 — Cloud AWS

Lucas, para um desenvolvedor Java Senior/Tech Lead, o objetivo não é decorar o catálogo da AWS. É conseguir olhar para um requisito e decidir **onde executar a aplicação, como expô-la, onde persistir dados, como proteger credenciais, como integrar serviços e como garantir disponibilidade e observabilidade**.

## 1. AWS — conceitos, trade-offs e casos de uso

| Serviço | Conceito objetivo | Trade-off | Caso de uso |
|---|---|---|---|
| **IAM** | Controla autenticação e autorização na AWS através de identidades, roles e policies. | Policies mal configuradas podem dar permissões excessivas; exige disciplina de least privilege. | Permitir que uma aplicação acesse somente determinado S3 ou SQS.  |
| **VPC** | Rede virtual logicamente isolada onde recursos AWS podem ser executados, com subnets, rotas, gateways e controles de rede. | Aumenta segurança e controle, mas networking incorreto é uma causa frequente de indisponibilidade. | ALB público e aplicações/bancos em subnets privadas.  |
| **EC2** | Servidores virtuais sob demanda com controle de CPU, memória, SO, rede e storage. | Grande flexibilidade, mas você gerencia mais infraestrutura, patching e capacity planning. | Aplicações legadas ou workloads que exigem controle do sistema operacional.  |
| **ALB** | Application Load Balancer distribui tráfego HTTP/HTTPS entre targets saudáveis e permite regras de roteamento. | Adiciona custo e componente de rede, mas melhora disponibilidade e distribuição de carga. | `/orders` → Order Service e `/payments` → Payment Service.  |
| **Route 53** | Serviço de DNS para registro de domínio, resolução, roteamento e health checks. | DNS possui TTL e propagação; não substitui load balancing interno da aplicação. | `api.company.com` apontando para um ALB e failover entre endpoints.  |
| **S3** | Object Storage altamente escalável baseado em buckets e objetos. | Não é filesystem tradicional nem banco relacional. | Uploads, documentos, imagens, backups, data lake e arquivos estáticos.  |
| **RDS** | Banco relacional gerenciado para engines como PostgreSQL, MySQL, Oracle e SQL Server. AWS administra várias tarefas operacionais como backups e patching. | Mais simples operacionalmente, mas com menos controle que gerenciar o banco diretamente em EC2 e com custo do serviço gerenciado. | PostgreSQL de uma API Spring Boot.  |
| **ElastiCache** | Cache/data store distribuído em memória gerenciado, com Valkey, Redis OSS e Memcached. | Aumenta performance, mas introduz problemas de invalidação, consistência e memória. | Cache de consultas, sessões, dados acessados frequentemente.  |
| **SQS** | Fila gerenciada e durável para desacoplar produtores e consumidores. | Comunicação fica assíncrona e exige tratar duplicidade, retry, DLQ e idempotência. | Processar pedidos, notificações ou jobs em background.  |
| **SNS** | Pub/Sub gerenciado onde uma mensagem publicada em um tópico pode ser entregue a vários subscribers. | Menos apropriado quando cada mensagem precisa ser consumida por apenas um worker; frequentemente combinado com SQS. | Fan-out de `OrderCreated` para várias filas/consumidores.  |
| **EventBridge** | Barramento de eventos que recebe eventos, aplica regras e os encaminha para diferentes targets. | Muito flexível, mas excesso de eventos e regras pode dificultar rastreabilidade da arquitetura. | Integração orientada a eventos entre aplicações, AWS services e SaaS.  |
| **Lambda** | Compute serverless executado sob demanda, normalmente disparado por eventos ou chamadas. | Reduz gestão de infraestrutura, mas possui características próprias de execução, limites e comportamento de startup. | Processamento de arquivos S3, consumers SQS e automações event-driven.  |
| **ECS** | Orquestrador de containers totalmente gerenciado pela AWS. Pode executar workloads sobre Fargate ou outras capacidades. | Mais simples operacionalmente que Kubernetes, mas mais específico do ecossistema AWS. | Microsserviços Java conteinerizados sem necessidade de Kubernetes.  |
| **EKS** | Kubernetes gerenciado pela AWS. A AWS administra principalmente componentes do control plane e oferece diferentes modelos de operação. | Grande flexibilidade e ecossistema Kubernetes, mas maior complexidade operacional que ECS. | Empresas já padronizadas em Kubernetes.  |
| **CloudWatch** | Plataforma AWS para métricas, logs, dashboards, alarmes e observabilidade de recursos e aplicações. | Telemetria excessiva aumenta custo e ruído; exige políticas de retenção e alertas bem definidos. | Alarmar sobre erros, CPU, latência, logs e saúde operacional.  |
| **Secrets Manager** | Armazena, recupera e pode rotacionar credenciais, API keys, tokens e outros secrets. | Possui custo e exige chamadas/permissões adequadas para acesso aos segredos. | Senha do PostgreSQL, API key e OAuth token.  |
| **KMS** | Serviço para criar e controlar chaves criptográficas usadas para criptografar ou assinar dados. | Exige entender key policies, IAM e gestão do ciclo de vida das chaves. | Criptografia de S3, RDS, Secrets Manager e dados da própria aplicação.  |

---

# 2. IAM — comece por segurança

IAM precisa ser entendido antes de quase todos os outros serviços.

A pergunta é:

**Quem pode fazer o quê em qual recurso?**

Por exemplo, uma aplicação que processa pedidos pode precisar:

```text
Order Service
     │
     ├── READ/WRITE → SQS orders
     │
     ├── READ       → Secrets Manager
     │
     └── NÃO PODE   → outros recursos
```

A ideia central é **least privilege**.

Não entregue:

```text
AdministratorAccess
```

para uma aplicação porque é mais fácil.

Prefira roles temporárias e policies limitadas aos recursos necessários. IAM é justamente o mecanismo central da AWS para autenticação e autorização de principals e recursos. 

---

# 3. VPC — a base da rede

Uma arquitetura típica pode ser:

```text
Internet
   │
   ▼
Route 53
   │
   ▼
ALB
   │
   ▼
┌──────────────────────── VPC ───────────────────────┐
│                                                   │
│  Public Subnets                                   │
│       │                                           │
│      ALB                                          │
│       │                                           │
│  Private Subnets                                  │
│       │                                           │
│   ECS / EKS / EC2                                 │
│       │                                           │
│   Private DB Subnets                              │
│       │                                           │
│      RDS                                          │
│                                                   │
└───────────────────────────────────────────────────┘
```

A VPC permite controlar endereço IP, subnets, rotas e conectividade. Em produção, é comum separar componentes expostos à internet de aplicações e bancos internos. 

---

# 4. EC2

EC2 é a opção mais próxima de uma máquina virtual tradicional.

Você escolhe:

```text
CPU
Memory
Storage
Operating System
Networking
```

e executa sua aplicação.

Isso oferece controle, mas também aumenta responsabilidade operacional.

Para um Java corporativo legado, EC2 pode fazer sentido.

Para aplicações novas e conteinerizadas, normalmente também avaliamos:

```text
ECS
ou
EKS
```

porque diminuem a necessidade de administrar diretamente as instâncias usadas pela aplicação. 

---

# 5. ALB

O ALB distribui requests entre múltiplos targets.

Imagine:

```text
                  ALB
                   │
          ┌────────┼────────┐
          ▼        ▼        ▼
       API 1     API 2     API 3
```

Se um target fica unhealthy:

```text
API 2 ✗
```

o ALB pode deixar de direcionar tráfego para ele.

Também podemos fazer roteamento por path:

```text
/orders/*
      ↓
Order Service


/payments/*
      ↓
Payment Service
```

O ALB trabalha com listeners, regras e target groups. 

---

# 6. Route 53

Route 53 é principalmente DNS.

Um fluxo típico:

```text
api.company.com
      ↓
Route 53
      ↓
ALB
```

Além de DNS, pode trabalhar com health checks e estratégias de roteamento e failover. 

Memorize:

**Route 53 resolve nome e roteamento DNS.**

**ALB distribui requests entre aplicações/targets.**

---

# 7. S3

S3 é Object Storage.

Não pense nele como:

```text
HD remoto
```

nem como:

```text
PostgreSQL
```

O modelo é:

```text
Bucket
  │
  ├── Object A
  ├── Object B
  └── Object C
```

É excelente para:

```text
arquivos
imagens
documentos
backups
data lake
artefatos
```

O S3 também oferece recursos como versionamento e políticas de acesso. 

---

# 8. RDS

Para uma aplicação Spring Boot usando PostgreSQL:

```text
Spring Boot
     ↓
RDS PostgreSQL
```

é uma arquitetura bastante comum.

A AWS gerencia várias tarefas como:

```text
backups
patching
failure detection
recovery
```

e oferece opções de alta disponibilidade e read replicas dependendo da configuração. 

Mas:

> RDS ser gerenciado não significa que performance do banco deixou de ser responsabilidade da aplicação.

Você ainda precisa entender:

```text
queries
indexes
locks
connection pool
EXPLAIN ANALYZE
```

A própria AWS mantém o cliente responsável pelo query tuning. 

---

# 9. ElastiCache

Imagine:

```text
Application
    │
    ├── cache hit ──► ElastiCache
    │
    └── cache miss ─► RDS
```

Sem cache:

```text
100.000 leituras
      ↓
RDS
```

Com cache:

```text
muitas leituras
      ↓
ElastiCache
```

reduzindo carga e latência.

Mas agora surgem problemas como:

```text
TTL
cache invalidation
stale data
cache stampede
consistência
```

Por isso cache precisa resolver um problema concreto, não ser adicionado automaticamente. 

---

# 10. SQS

SQS é uma fila.

Imagine:

```text
Order Service
      ↓
     SQS
      ↓
Payment Worker
```

O produtor não precisa esperar o consumer terminar.

Isso oferece:

```text
desacoplamento
buffer de carga
processamento assíncrono
```

Mas você precisa pensar em:

```text
retry
DLQ
visibility timeout
duplicidade
idempotência
```

SQS existe justamente para integrar e desacoplar componentes distribuídos. 

---

# 11. SNS

SNS é muito associado ao modelo:

**publish/subscribe.**

Imagine:

```text
                OrderCreated
                     │
                     ▼
                 SNS Topic
                /    |     \
               /     |      \
              ▼      ▼       ▼
          SQS A    SQS B    Lambda
```

Uma publicação pode ser distribuída para múltiplos subscribers.

Esse padrão é conhecido como:

**fan-out.** 

---

# 12. SQS x SNS

Para memorizar:

```text
SQS
 ↓
fila
 ↓
consumer processa mensagens
```

Enquanto:

```text
SNS
 ↓
topic
 ↓
fan-out
 ↓
vários subscribers
```

Uma arquitetura muito comum combina os dois:

```text
               SNS
             /  |  \
            /   |   \
          SQS  SQS  SQS
           ↓    ↓    ↓
          A     B     C
```

Assim, cada consumidor possui sua própria fila.

---

# 13. EventBridge

EventBridge é orientado a **roteamento de eventos**.

Imagine:

```text
OrderCreated
      ↓
EventBridge
      ↓
rules
 ┌────┼─────┐
 ▼    ▼     ▼
Lambda SQS  outro serviço
```

Uma regra pode dizer:

```text
source = order-service
eventType = OrderCreated
```

e direcionar apenas eventos correspondentes para determinados targets.

EventBridge também suporta eventos provenientes de serviços AWS, aplicações próprias e integrações externas. 

---

# 14. SQS x SNS x EventBridge

Essa comparação é importante:

| Necessidade | Serviço |
|---|---|
| Quero uma fila para processamento assíncrono | **SQS** |
| Quero publicar uma mensagem para vários subscribers | **SNS** |
| Quero rotear eventos por regras e atributos | **EventBridge** |

Eles podem inclusive trabalhar juntos.

Não são necessariamente concorrentes.

---

# 15. Lambda

Lambda é compute serverless.

O modelo é:

```text
Evento
  ↓
Lambda
  ↓
executa código
  ↓
termina
```

Exemplo:

```text
S3 upload
   ↓
Lambda
   ↓
processa imagem
```

ou:

```text
SQS
 ↓
Lambda
 ↓
processa mensagem
```

A AWS gerencia servidores, scaling e infraestrutura subjacente. 

É uma excelente opção para workloads:

```text
event-driven
intermitentes
jobs pequenos
automação
```

Mas não significa que toda aplicação deva virar função serverless.

---

# 16. ECS

Para uma aplicação Java Docker:

```text
Spring Boot
    ↓
Docker
    ↓
ECS
```

O ECS gerencia:

```text
deployment
tasks
services
scaling
placement
```

Você pode executá-lo sobre diferentes opções de capacidade, incluindo Fargate, em que não precisa administrar servidores diretamente. 

Para uma empresa fortemente AWS e sem requisito explícito de Kubernetes, ECS pode reduzir bastante a complexidade operacional.

---

# 17. EKS

EKS é Kubernetes gerenciado pela AWS.

```text
Spring Boot
    ↓
Container
    ↓
Kubernetes
    ↓
EKS
```

Você continua trabalhando com conceitos como:

```text
Pod
Deployment
Service
ConfigMap
Secret
HPA
Ingress/Gateway
```

mas a AWS gerencia componentes importantes da infraestrutura Kubernetes. 

---

# 18. ECS x EKS

Essa decisão aparece bastante em arquitetura.

| ECS | EKS |
|---|---|
| Orquestração AWS-native | Kubernetes |
| Menor complexidade | Maior flexibilidade |
| Integração profunda com AWS | Ecossistema Kubernetes |
| Curva de aprendizagem menor | Curva maior |
| Menor portabilidade conceitual | Kubernetes é amplamente adotado |

Uma forma simples de responder:

> Se preciso executar containers na AWS sem necessidade concreta de Kubernetes, considero ECS. Se a organização já padronizou Kubernetes, possui expertise ou precisa do ecossistema Kubernetes, considero EKS.

---

# 19. CloudWatch

CloudWatch representa boa parte da observabilidade nativa AWS.

Você pode trabalhar com:

```text
Metrics
Logs
Alarms
Dashboards
APM
```

Exemplo:

```text
ALB error rate
      ↓
CloudWatch Metric
      ↓
Alarm
      ↓
SNS
      ↓
On-call
```

CloudWatch também recebe métricas automaticamente de diversos serviços AWS e permite métricas customizadas. 

Conecte isso ao módulo de observabilidade:

```text
Latency
Traffic
Errors
Saturation
```

---

# 20. Secrets Manager

Nunca faça:

```java
String password = "ProdPassword123";
```

nem:

```yaml
database:
  password: ProdPassword123
```

versionado junto com a aplicação.

Uma arquitetura melhor:

```text
Application
     ↓
IAM Role
     ↓
Secrets Manager
     ↓
Database credentials
```

Além de armazenamento, Secrets Manager suporta gerenciamento do lifecycle e rotação de segredos. 

---

# 21. KMS

KMS resolve um problema diferente.

Secrets Manager guarda:

```text
passwords
tokens
API keys
```

KMS gerencia:

```text
cryptographic keys
```

Então:

```text
Secrets Manager
       ↓
pode usar KMS
       ↓
encryption
```

KMS também é integrado a vários serviços AWS para criptografia de dados. 

Memorize:

**Secrets Manager gerencia secrets.**

**KMS gerencia chaves criptográficas.**

---

# 22. Arquitetura Java típica na AWS

Uma arquitetura possível para uma API Java corporativa:

```text
                   Internet
                      │
                      ▼
                  Route 53
                      │
                      ▼
                     ALB
                      │
            ┌─────────┴─────────┐
            ▼                   ▼
        ECS / EKS           ECS / EKS
        Order API           Payment API
            │                   │
            ├─────── SQS ───────┤
            │                   │
            ▼                   ▼
      ElastiCache             RDS
            │
            ▼
           RDS

       S3 ← arquivos/documentos

Secrets Manager ← credenciais
KMS            ← criptografia
IAM            ← autorização
CloudWatch     ← observabilidade
```

O valor não está em decorar cada caixa.

Está em explicar **por que ela existe**.

---

# 23. Alta disponibilidade

Um desenho de produção normalmente também pensa em:

```text
Region
  │
  ├── Availability Zone A
  │
  ├── Availability Zone B
  │
  └── Availability Zone C
```

O objetivo é evitar:

```text
1 instance
1 AZ
1 single point of failure
```

ALB, RDS e workloads de compute podem ser configurados para aproveitar múltiplas Availability Zones dependendo da arquitetura. O próprio Well-Architected Framework trata reliability como capacidade de o workload executar sua função correta e consistentemente e se recuperar de falhas. 

---

# 24. O que realmente estudar

Não memorize apenas:

```text
S3 = storage
SQS = queue
RDS = database
```

Treine perguntas arquiteturais como:

> A aplicação deve ficar pública ou privada?

> Como ela recebe tráfego?

> O que acontece se uma instância morrer?

> O banco precisa de alta disponibilidade?

> A operação precisa ser síncrona?

> Posso colocar uma fila?

> Como trato retry e DLQ?

> Onde armazeno credenciais?

> Como criptografo os dados?

> Como monitoro erros e latência?

> Como escalo?

> Qual é o custo dessa decisão?

Essas perguntas se alinham muito mais com o AWS Well-Architected Framework, que avalia arquiteturas pelos pilares de **Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization e Sustainability**. 

---

# Resposta objetiva para entrevista

> Na AWS eu procuro pensar primeiro em arquitetura e requisitos, e depois escolher os serviços.
>
> Para segurança, utilizo IAM aplicando least privilege, Secrets Manager para credenciais e tokens e KMS para gerenciamento de chaves criptográficas. Para networking, utilizo VPC para isolamento, subnets adequadas para workloads públicos e privados, Route 53 para DNS e ALB para distribuir tráfego entre instâncias ou containers. 
>
> Para compute, posso escolher EC2 quando preciso de maior controle, ECS quando quero executar containers com menor complexidade operacional dentro da AWS, ou EKS quando existe uma necessidade concreta de Kubernetes. Lambda entra principalmente em workloads serverless e orientados a eventos. 
>
> Para dados, utilizo RDS para banco relacional, ElastiCache quando existe necessidade real de cache ou acesso em memória e S3 para object storage. 
>
> Para integração assíncrona, diferencio SQS, SNS e EventBridge: SQS é principalmente fila, SNS permite fan-out para vários subscribers e EventBridge permite roteamento de eventos baseado em regras. 
>
> Para operação, utilizo CloudWatch para métricas, logs, dashboards e alarmes. E em todas essas decisões considero disponibilidade, segurança, performance, custo e capacidade de recuperação.
>
> Então, para mim, dominar AWS não significa decorar serviços. Significa conseguir **desenhar uma arquitetura segura, resiliente, observável e escalável e justificar os trade-offs de cada escolha**. 
