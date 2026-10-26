Lucas, em **System Design**, o objetivo não é memorizar desenhos prontos. É conseguir **transformar requisitos ambíguos em uma arquitetura justificável**, explicando capacidade, dados, consistência, escalabilidade, resiliência, segurança e trade-offs.

# FASE 16 — System Design

## 1. Tabela — conceito, trade-off e caso de uso

| Item | Conceito objetivo | Trade-off | Caso de uso |
|---|---|---|---|
| **Requirements** | Identificar requisitos funcionais e não funcionais antes de desenhar a solução. | Detalhar demais consome tempo; detalhar pouco pode levar a uma arquitetura errada. | Definir se um chat precisa de grupos, histórico, presença e mensagens offline. |
| **Functional Requirements** | O que o sistema precisa fazer. | Mais funcionalidades aumentam escopo e complexidade. | URL Shortener precisa criar e resolver URLs curtas. |
| **Non-functional Requirements** | Qualidades esperadas: disponibilidade, latência, escala, segurança, durabilidade etc. | Garantias mais fortes normalmente custam mais infraestrutura e complexidade. | Payment precisa alta consistência e auditabilidade. |
| **Constraints** | Restrições técnicas ou de negócio que limitam as opções arquiteturais. | Podem impedir a solução tecnicamente ideal. | Cloud obrigatória, PostgreSQL existente, requisito regulatório. |
| **Capacity Estimation** | Estimar tráfego, armazenamento, bandwidth e crescimento. | Estimativas são aproximações; excesso gera overengineering, falta gera gargalos. | Calcular requests por segundo de um URL Shortener. |
| **API Design** | Definir contratos externos e principais operações do sistema. | APIs muito genéricas dificultam evolução; específicas demais aumentam quantidade de endpoints. | `POST /payments`, `GET /orders/{id}`. |
| **Data Model** | Definir entidades, relacionamentos, índices, particionamento e ownership. | Normalização melhora consistência; desnormalização melhora leitura, mas aumenta sincronização. | Order, OrderItem, Payment. |
| **High-Level Architecture** | Identificar componentes principais e seus relacionamentos. | Dividir demais cria complexidade distribuída; dividir pouco reduz autonomia e escalabilidade específica. | API Gateway → Services → Database → Broker. |
| **Load Balancer** | Distribui tráfego entre múltiplas instâncias. | Precisa de health checking e estratégia de balanceamento. | Distribuir requests entre vários pods. |
| **Cache** | Mantém dados próximos do consumidor para reduzir latência e carga no banco. | Cria problemas de invalidação, stale data e consistência. | Cache de URLs curtas ou catálogo. |
| **CDN** | Distribui conteúdo através de pontos geograficamente próximos dos usuários. | Caching e invalidação ficam mais complexos. | Netflix, Instagram, arquivos estáticos. |
| **Database Scaling** | Escalar armazenamento por replicas, partitioning, sharding ou outras técnicas. | Aumenta complexidade operacional e consistência. | Grandes volumes de usuários ou pedidos. |
| **Replication** | Mantém cópias dos dados em múltiplos nós. | Replicação assíncrona pode gerar leitura stale; síncrona aumenta latência. | Alta disponibilidade e read replicas. |
| **Partitioning / Sharding** | Divide dados entre diferentes nós. | Queries entre shards, rebalanceamento e escolha da shard key ficam difíceis. | Milhões de usuários distribuídos por `userId`. |
| **Queue / Message Broker** | Desacopla produtores e consumidores através de processamento assíncrono. | Introduz eventual consistency, duplicidade e necessidade de retries. | Notification System, processamento de pedidos. |
| **Scaling** | Estratégias para aumentar capacidade conforme demanda. | Mais replicas não resolvem gargalos em banco, lock ou dependência externa. | Escalar API horizontalmente. |
| **Vertical Scaling** | Aumentar CPU/RAM da mesma máquina. | Simples, mas possui limite físico e pode aumentar blast radius. | Banco pequeno/médio inicialmente. |
| **Horizontal Scaling** | Adicionar mais instâncias. | Exige aplicação stateless ou coordenação de estado. | APIs web de alto tráfego. |
| **Consistency** | Definir quais garantias existem sobre leitura e escrita. | Consistência mais forte normalmente exige mais coordenação e latência. | Payment exige mais consistência que feed social. |
| **Strong Consistency** | Leituras refletem imediatamente o estado confirmado segundo a garantia adotada. | Maior coordenação e potencial redução de disponibilidade. | Saldo, reserva crítica. |
| **Eventual Consistency** | Estados podem divergir temporariamente, mas convergem posteriormente. | Usuário pode observar estado temporariamente desatualizado. | Feed, analytics, notificações. |
| **Reliability** | Projetar para continuar funcionando mesmo diante de falhas. | Redundância, retries e replicação aumentam custo. | Serviço continuar operando após falha de uma instância. |
| **Availability** | Percentual de tempo em que o sistema consegue atender requisições. | Alta disponibilidade aumenta redundância e custo operacional. | Sistemas 24 por 7. |
| **Fault Tolerance** | Capacidade de continuar operando mesmo quando componentes falham. | Replica infraestrutura e aumenta complexidade. | Multi-AZ, replicas e failover. |
| **Idempotency** | Repetir uma operação não produz efeitos adicionais indevidos. | Requer armazenamento de chave/estado. | Payment e criação de pedido. |
| **Observability** | Logs, metrics e traces para entender comportamento e diagnosticar incidentes. | Aumenta custo de armazenamento e instrumentação. | p99 alto em checkout. |
| **Security** | Considerar autenticação, autorização, criptografia, secrets e proteção contra abuso. | Segurança adiciona processamento e complexidade operacional. | OAuth2, RBAC, encryption at rest. |
| **Rate Limiting** | Controlar quantidade de requisições por cliente/período. | Pode rejeitar clientes legítimos em picos. | APIs públicas e login. |
| **Trade-offs** | Explicar conscientemente o que é ganho e perdido por cada decisão. | Não existe arquitetura que maximize todas as características simultaneamente. | Consistência forte versus disponibilidade/latência. |

---

# 2. O processo mais importante

Em entrevista, siga sempre uma ordem.

```text
1. Requirements
2. Constraints
3. Capacity estimation
4. API
5. Data model
6. High-level architecture
7. Scaling
8. Consistency
9. Reliability
10. Observability
11. Security
12. Trade-offs
```

O objetivo é impedir que você comece imediatamente desenhando:

```text
Kafka
Redis
Kubernetes
Microservices
```

sem saber qual problema está tentando resolver.

---

# 3. Requirements

Comece separando:

```text
Functional Requirements
```

de:

```text
Non-functional Requirements
```

Em um URL Shortener, requisitos funcionais poderiam ser:

```text
criar URL curta
redirecionar
custom alias
expiração
```

Requisitos não funcionais:

```text
baixa latência
alta disponibilidade
bilhões de redirects
durabilidade
```

A arquitetura nasce dessas respostas.

---

# 4. Constraints

Pergunte por restrições.

Exemplos:

```text
quantidade de usuários
regiões
latência máxima
budget
cloud
compliance
tecnologias existentes
```

Imagine:

```text
Payment System
```

com requisito:

```text
não pode processar
pagamento duplicado
```

Isso muda completamente a arquitetura.

Agora idempotência passa a ser uma preocupação central.

---

# 5. Capacity estimation

Não precisa fazer matemática perfeita.

Precisa demonstrar ordem de grandeza.

Imagine:

```text
100 milhões de usuários

10 milhões de requests/dia
```

Requests por segundo médios:

```text
10.000.000
÷
86.400
≈
116 RPS
```

Mas você não deve projetar apenas para média.

Pode existir:

```text
peak = 10x
```

Então:

```text
≈ 1.160 RPS
```

Também estime:

```text
storage
bandwidth
records/day
growth/year
```

Isso ajuda a justificar decisões.

---

# 6. API

Antes de desenhar todos os componentes, defina os contratos principais.

URL Shortener:

```http
POST /urls
```

retorna:

```text
shortCode
```

Depois:

```http
GET /{shortCode}
```

retorna:

```text
redirect
```

Payment:

```http
POST /payments
Idempotency-Key: abc123
```

Essa etapa expõe importantes decisões de domínio.

---

# 7. Data Model

Agora pense nos dados.

URL Shortener:

```text
short_code
original_url
created_at
expires_at
user_id
```

Payment:

```text
payment_id
order_id
status
amount
currency
idempotency_key
created_at
```

Pergunte:

```text
Qual é a primary key?

Quais queries são críticas?

Quais índices preciso?

Preciso relacionamentos?

Preciso particionar?

Qual é o source of truth?
```

---

# 8. High-Level Architecture

Somente agora desenhe a arquitetura.

Por exemplo:

```text
Client
  ↓
Load Balancer
  ↓
API
  ↓
Cache
  ↓
Database
```

Ou:

```text
Client
   ↓
API
   ↓
Order Service
   ↓
Database
   ↓
Outbox
   ↓
Kafka
   ↓
Payment / Inventory
```

Primeiro mantenha o desenho simples.

Depois aprofunde os gargalos.

---

# 9. Scaling

A primeira pergunta é:

> Onde está o gargalo?

Pode ser:

```text
CPU
database
storage
network
external API
connection pool
message processing
```

Para aplicação stateless:

```text
1 instance
   ↓
N instances
   ↓
Load Balancer
```

é simples.

Mas se o gargalo está no banco:

```text
100 application instances
```

podem apenas:

```text
sobrecarregar ainda mais
o mesmo database
```

---

# 10. Cache

Cache aparece muito em entrevistas.

Exemplo:

```text
Request
   ↓
Redis
   ↓
cache hit
```

Se não existir:

```text
cache miss
   ↓
Database
   ↓
Redis
```

O cache reduz:

```text
latência
carga no banco
```

Mas introduz:

```text
invalidação
TTL
consistência
cache stampede
hot keys
```

Uma boa resposta não é apenas:

> "Colocaria Redis."

É explicar:

> **o que está sendo cacheado e como o cache ficará consistente.**

---

# 11. Database Scaling

Comece simples:

```text
Single Primary Database
```

Depois, se necessário:

```text
Primary
   ↓
Read Replicas
```

Se o volume continuar crescendo:

```text
Partitioning
```

ou:

```text
Sharding
```

Mas sharding traz problemas como:

```text
cross-shard queries
rebalancing
hot partitions
distributed transactions
```

Então não use cedo demais.

---

# 12. Shard Key

Se você escolher:

```text
userId
```

como shard key:

```text
hash(userId)
     ↓
Shard
```

todos os dados de um usuário podem ficar próximos.

Mas se escolher mal:

```text
country
```

e 80% dos usuários forem de um único país:

```text
Shard 1
████████████

Shard 2
██

Shard 3
█
```

você cria um hot shard.

Escolher shard key é uma decisão arquitetural importante.

---

# 13. Consistency

Pergunte:

> Esse dado precisa estar imediatamente consistente?

Payment:

```text
saldo
payment status
inventory reservation
```

pode exigir garantias fortes.

Feed:

```text
like count
views
recommendations
```

pode aceitar eventual consistency.

Não existe motivo para pagar o custo de consistência forte se o negócio não exige.

---

# 14. Reliability

Agora assuma:

```text
everything fails
```

Pergunte:

```text
E se a API cair?

E se o database cair?

E se Kafka ficar indisponível?

E se a mensagem duplicar?

E se uma região inteira cair?
```

Use conceitos como:

```text
replication
timeout
retry
circuit breaker
bulkhead
idempotency
DLQ
multi-AZ
failover
```

Mas sempre associados a um problema concreto.

---

# 15. Observability

Seu desenho deveria responder:

> Como vou descobrir que está quebrado?

Considere:

```text
Logs
Metrics
Traces
```

E métricas como:

```text
Latency
Traffic
Errors
Saturation
```

Além de métricas específicas:

```text
payments_failed
notifications_pending
booking_conflicts
message_delivery_latency
```

---

# 16. Security

Em entrevista, não deixe segurança para fora do desenho.

Pense em:

```text
Authentication
Authorization
Encryption in transit
Encryption at rest
Secrets
Rate Limit
Audit
PII
Network isolation
```

Em Payment System:

```text
audit
idempotency
tokenization
access control
```

podem ser centrais.

---

# 17. Trade-offs

Essa é uma das partes mais importantes.

Você deveria conseguir dizer coisas como:

> Escolhi Redis para reduzir latência de leitura, aceitando a complexidade de invalidação.

Ou:

> Escolhi consistência eventual nesse fluxo porque disponibilidade e throughput são mais importantes que leitura imediatamente atualizada.

Ou:

> Começaria com PostgreSQL em vez de sharding porque o volume atual não justifica a complexidade operacional.

Isso demonstra maturidade arquitetural.

---

# 18. Como praticar cada problema

Não memorize:

```text
Netflix =
Kafka + Cassandra + CDN
```

Pratique assim:

```text
Problema
   ↓
Requisitos
   ↓
estimativas
   ↓
primeira solução simples
   ↓
encontre gargalo
   ↓
evolua a arquitetura
```

Cada decisão precisa responder a um requisito.

---

# 19. URL Shortener

Principais assuntos:

```text
ID generation
redirect latency
cache
database
expiration
high read/write ratio
```

Ótimo para aprender:

```text
cache
partitioning
capacity estimation
```

---

# 20. Notification System

Principais desafios:

```text
Email
SMS
Push
```

Fluxo:

```text
Producer
   ↓
Queue
   ↓
Workers
   ↓
Provider
```

Precisamos pensar em:

```text
retry
rate limit
provider failure
DLQ
idempotency
preferences
```

Excelente para aprender processamento assíncrono.

---

# 21. Payment System

Um dos melhores exercícios.

Principais problemas:

```text
idempotency
consistency
ledger
audit
reconciliation
external providers
retry
duplicate payment
```

Em pagamentos:

> **não processe retry sem pensar primeiro em idempotência.**

---

# 22. Booking Platform

Hotel, ingresso ou assento.

Problema central:

```text
2 usuários
   ↓
mesmo recurso
   ↓
ao mesmo tempo
```

Aqui entram:

```text
concurrency
locks
reservation timeout
optimistic locking
pessimistic locking
consistency
```

Excelente para estudar concorrência distribuída.

---

# 23. Chat System

Desafios:

```text
WebSocket
presence
message ordering
offline messages
fan-out
delivery status
```

Perguntas:

```text
Como garantir ordem?

Como armazenar mensagens?

Como saber quem está online?

Como entregar depois?
```

---

# 24. Uber

Desafios importantes:

```text
geospatial indexing
location updates
matching
real-time events
high write throughput
```

É um problema excelente para discutir:

```text
partitioning
streaming
geo queries
eventual consistency
```

---

# 25. Netflix

Principais áreas:

```text
video storage
CDN
encoding
metadata
recommendation
availability
```

O maior volume não está necessariamente na API.

Está em:

```text
video delivery
```

Então CDN se torna fundamental.

Isso mostra por que capacity estimation vem antes da arquitetura.

---

# 26. Instagram

Desafios:

```text
image/video storage
feed
followers
likes
CDN
fan-out
```

Uma decisão clássica é:

```text
fan-out on write
```

versus:

```text
fan-out on read
```

Para usuários comuns, gerar feed antecipadamente pode ser ótimo.

Para uma celebridade com milhões de seguidores:

```text
fan-out on write
```

pode ficar caro.

---

# 27. Rate Limiter

Excelente exercício para aprender:

```text
Token Bucket
Leaky Bucket
Fixed Window
Sliding Window
Redis
distributed counters
```

Perguntas importantes:

```text
limite por usuário?

por IP?

por API key?

global?

por região?
```

---

# 28. O que diferencia Senior de Staff em System Design

Senior normalmente consegue:

```text
desenhar componentes
escolher tecnologias
resolver gargalos
```

Staff precisa também considerar:

```text
evolução futura
ownership
dependências entre times
blast radius
operabilidade
custos
migração
simplicidade
trade-offs organizacionais
```

Ou seja, deixa de olhar apenas para:

```text
arquitetura técnica
```

e começa a olhar também para:

```text
arquitetura sociotécnica
```

---

# Resposta objetiva para entrevista

> Em System Design eu começo pelos requisitos funcionais e não funcionais, porque eles determinam as decisões arquiteturais. Depois identifico restrições e faço estimativas de ordem de grandeza para tráfego, armazenamento e crescimento, evitando projetar a solução apenas pela média.
>
> Em seguida defino os principais contratos de API e o modelo de dados, incluindo queries críticas, índices e ownership. Só então desenho uma arquitetura de alto nível, começando pela solução mais simples que satisfaça os requisitos.
>
> A partir daí identifico gargalos e discuto estratégias de escala, como horizontal scaling, cache, replicas, partitioning ou processamento assíncrono, sem introduzir essas soluções antes de existir necessidade.
>
> Também discuto explicitamente consistência e confiabilidade. Nem todo dado precisa de consistência forte; dependendo do negócio posso aceitar consistência eventual. Para falhas considero timeout, retry, idempotência, redundância e failover.
>
> Incluo observabilidade desde o desenho, com logs, métricas, traces e indicadores de negócio, além de segurança, autenticação, autorização, encryption e rate limiting.
>
> E finalizo sempre expondo os trade-offs. Para mim, uma boa resposta de System Design não é aquela com mais tecnologias, mas aquela em que **cada componente existe para atender um requisito específico e cada decisão possui um custo conscientemente aceito**.
