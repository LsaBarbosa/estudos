# FASE 10 — Observabilidade

Lucas, em observabilidade o ponto central é responder:

> **Como eu sei que o sistema está saudável, degradado ou quebrado — e como descubro rapidamente onde está o problema?**

Não basta coletar CPU e memória. Uma arquitetura madura conecta **logs, métricas, traces, indicadores técnicos, métricas de negócio e objetivos de serviço**.

## 1. Tabela — conceito, trade-off e caso de uso

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **Observability** | Capacidade de entender o estado interno do sistema a partir dos sinais que ele produz. | Mais instrumentação gera custo de armazenamento, processamento e operação. | Diagnóstico de produção, incidentes, capacity planning. |
| **Logs** | Registros discretos de acontecimentos da aplicação. | Muito detalhe gera volume e custo; pouco detalhe dificulta diagnóstico. | Erros, eventos de negócio, auditoria, debugging. |
| **Metrics** | Medições numéricas agregadas ao longo do tempo. | Excelente para tendências e alertas, mas perde detalhes individuais de requests. | Latência, erros, throughput, uso de recursos. |
| **Traces** | Representam o caminho completo de uma operação através de vários componentes, divididos em spans. | Alto volume pode exigir sampling e armazenamento especializado. | Identificar onde uma request distribuída ficou lenta. |
| **Span** | Unidade individual de trabalho dentro de um trace. | Muitos spans aumentam volume de telemetria. | HTTP call, query SQL, publicação Kafka. |
| **Trace ID** | Identificador que correlaciona todos os spans pertencentes à mesma operação distribuída. | Exige propagação correta entre serviços. | Seguir uma request por vários microsserviços. |
| **Correlation ID** | Identificador utilizado para correlacionar logs e operações relacionadas. | Precisa ser propagado consistentemente. | Buscar todos os logs relacionados a um pedido/request. |
| **OpenTelemetry** | Padrão e ecossistema vendor-neutral para produzir, coletar e exportar traces, métricas e logs. | Adiciona infraestrutura e precisa de configuração de sampling/exportação. | Instrumentação padronizada de microsserviços Java. |
| **OpenTelemetry Java Agent** | Agente que instrumenta aplicações Java automaticamente por bytecode, com pouca ou nenhuma alteração de código. | Menos controle fino que instrumentação manual; adiciona algum overhead. | Instrumentar Spring Boot, JDBC, HTTP clients rapidamente. |
| **OpenTelemetry Spring Boot Starter** | Integra OpenTelemetry diretamente com aplicações Spring Boot. | Menos instrumentação out-of-the-box que o Java Agent em alguns cenários. | Aplicações Spring Boot e native images. |
| **Micrometer** | Facade/abstração para métricas e observações no ecossistema Java/Spring. | Mais uma camada de abstração, mas facilita trocar backends. | Spring Boot Actuator + Prometheus. |
| **Prometheus** | Sistema para coleta e armazenamento de métricas em séries temporais, com PromQL. | Alta cardinalidade pode aumentar custo significativamente. | Métricas de aplicações, Kubernetes e infraestrutura. |
| **Grafana** | Plataforma para visualizar e correlacionar dados de observabilidade. | Dashboard demais pode gerar ruído e manutenção. | Dashboards, alertas, análise de métricas/logs/traces. |
| **Loki** | Backend de logs da stack Grafana, indexando principalmente labels em vez do conteúdo completo. | Labels com alta cardinalidade devem ser evitadas. | Centralização e busca de logs. |
| **Tempo** | Backend distribuído de tracing da Grafana. | Traces podem gerar grande volume e exigir sampling/storage. | Distributed tracing integrado com Grafana. |
| **Jaeger** | Plataforma especializada em distributed tracing. | Adiciona infraestrutura específica para coleta e consulta de traces. | Diagnóstico de latência e dependências distribuídas. |
| **Latency** | Tempo necessário para executar uma operação. | Média pode esconder problemas; percentis são normalmente mais úteis. | p50, p95 e p99 de APIs. |
| **Traffic** | Quantidade de trabalho recebido pelo sistema. | Alto tráfego não significa problema sozinho; precisa de contexto de capacidade. | Requests por segundo, mensagens por segundo. |
| **Errors** | Quantidade ou taxa de operações que falham. | Precisa distinguir erro técnico de erro esperado de negócio. | Taxa de HTTP 5xx, falhas de integração. |
| **Saturation** | Quanto os recursos estão próximos do limite de capacidade. | Um único indicador pode não representar toda a saturação. | Thread pool, connection pool, CPU, filas. |
| **Business Metrics** | Métricas que representam resultado ou comportamento do negócio. | Exigem alinhamento técnico e de produto sobre significado. | `orders_created`, `payments_failed`. |
| **SLI** | Indicador quantitativo que mede algum aspecto do nível de serviço entregue. | SLI mal escolhido pode não refletir experiência real do usuário. | Disponibilidade, latência, taxa de sucesso. |
| **SLO** | Objetivo definido para determinado SLI. | Objetivo agressivo demais aumenta custo e reduz velocidade de mudança. | 99,9% das requests disponíveis no mês. |
| **SLA** | Acordo com usuários/clientes que inclui consequências se determinados níveis de serviço não forem atingidos. | Cria obrigação comercial/contratual. | Compensação financeira se disponibilidade ficar abaixo de 99,5%. |
| **Error Budget** | Quantidade de falha permitida pelo SLO. | Pode limitar velocidade de releases quando o orçamento está sendo consumido rapidamente. | Balancear confiabilidade e velocidade de entrega. |

O suporte Java atual do OpenTelemetry considera **traces, métricas e logs estáveis**, e existem opções de instrumentação tanto por Java Agent quanto por Spring Boot Starter. 

---

# 2. Os três pilares

O modelo clássico é:

```text
Observability
│
├── Logs
├── Metrics
└── Traces
```

Eles respondem perguntas diferentes.

### Logs

Respondem:

> **O que aconteceu?**

Exemplo:

```text
Payment rejected
orderId=123
paymentId=999
reason=INSUFFICIENT_FUNDS
traceId=abc123
```

Logs possuem detalhes de eventos específicos.

---

### Metrics

Respondem:

> **Com que frequência e em que intensidade isso está acontecendo?**

Exemplo:

```text
payments_failed_total = 542
```

ou:

```text
checkout_duration_p99 = 1.8s
```

Métricas são melhores para dashboards, tendências e alertas.

---

### Traces

Respondem:

> **Onde essa operação passou e onde ela ficou lenta?**

Exemplo:

```text
POST /checkout
      │
      ├── Order Service       30ms
      │
      ├── Inventory Service   80ms
      │
      └── Payment Service    1.4s
```

Agora sabemos que:

```text
Payment Service
```

é o principal responsável pela latência.

Traces representam o caminho de uma operação distribuída e são compostos por spans. 

---

# 3. Correlacionando os três sinais

O poder real aparece quando os sinais são conectados.

Imagine um dashboard:

```text
p99 aumentou
      ↓
métrica
```

Você seleciona o período:

```text
p99 = 2.5s
```

Depois abre um trace lento:

```text
Trace ID = abc123
```

Descobre:

```text
Payment Service
      ↓
PostgreSQL query
      ↓
1.8 segundos
```

Depois procura nos logs:

```text
traceId = abc123
```

e encontra o erro correspondente.

Fluxo:

```text
Metric
   ↓
Trace
   ↓
Logs
```

Esse é um dos objetivos mais importantes de uma plataforma de observabilidade moderna.

Tempo, por exemplo, possui integração para correlacionar traces com métricas e logs. 

---

# 4. Logs estruturados

Evite depender apenas de:

```text
Erro ao processar pedido
```

Prefira logs estruturados com contexto:

```json
{
  "level": "ERROR",
  "service": "payment-service",
  "orderId": "123",
  "paymentId": "999",
  "traceId": "abc123",
  "error": "TIMEOUT",
  "durationMs": 2500
}
```

Isso facilita:

```text
busca
correlação
agregação
alertas
```

---

# 5. Cuidado com logs

Mais logs não significa automaticamente melhor observabilidade.

Evite:

```text
logar tudo
```

principalmente:

```text
senha
token
dados pessoais sensíveis
payloads gigantes
```

Também evite transformar todo loop em:

```text
INFO
INFO
INFO
INFO
```

Isso cria:

```text
custo
ruído
storage
dificuldade de encontrar o que importa
```

---

# 6. Loki

Loki é um backend para centralização de logs.

Uma característica importante é que ele não indexa todo o conteúdo das linhas como um mecanismo tradicional de full-text indexing.

Ele organiza streams principalmente utilizando labels. 

Exemplo:

```text
service=payment-service
environment=prod
cluster=br-prod
```

E depois podemos consultar:

```text
service="payment-service"
```

---

# 7. Cardinalidade em logs e métricas

Esse é um conceito importante para observabilidade.

Imagine criar uma label:

```text
userId
```

com:

```text
10 milhões de usuários
```

Agora você pode gerar milhões de séries diferentes.

Isso é:

**high cardinality.**

Labels ou tags normalmente devem utilizar valores com quantidade controlada.

Bom:

```text
environment=prod
region=sa-east-1
status=success
```

Perigoso:

```text
userId=928374628
requestId=abcdef...
orderId=9283812
```

IDs individuais normalmente pertencem melhor a:

```text
logs
traces
structured metadata
```

e não como labels de métricas.

---

# 8. Métricas — não pare em CPU e memória

CPU e memória são importantes.

Mas elas respondem principalmente:

> **como a máquina está?**

E não necessariamente:

> **como o serviço está para o usuário?**

Imagine:

```text
CPU = 20%
Memory = 40%
```

mas:

```text
Payment API
100% timeout
```

A infraestrutura parece saudável.

O produto está quebrado.

Por isso um modelo importante é:

```text
Latency
Traffic
Errors
Saturation
```

---

# 9. Latency

Latency mede o tempo das operações.

Evite olhar apenas:

```text
average latency
```

Imagine:

```text
99 requests = 50ms
1 request = 10 segundos
```

A média pode parecer aceitável.

Mas existe um usuário sofrendo com uma request de 10 segundos.

Por isso observamos:

```text
p50
p95
p99
```

Exemplo:

```text
p50 = 80ms
p95 = 250ms
p99 = 1.5s
```

Google SRE recomenda frequentemente trabalhar com distribuições e percentis, porque médias podem esconder long tails importantes. 

---

# 10. Traffic

Traffic representa o volume de trabalho.

Exemplos:

```text
HTTP requests / segundo

Kafka messages / segundo

orders / minuto

checkout / minuto
```

Se:

```text
latência aumentou
```

é importante saber se:

```text
tráfego também aumentou
```

Isso muda completamente o diagnóstico.

---

# 11. Errors

Precisamos medir:

```text
error rate
```

e não apenas contar erros absolutos.

Exemplo:

```text
100 erros
```

pode parecer muito.

Mas:

```text
100 erros / 10.000.000 requests
```

é muito diferente de:

```text
100 erros / 200 requests
```

Uma métrica mais útil:

```text
error_rate =
failed_requests
/
total_requests
```

---

# 12. Saturation

Saturation responde:

> **Qual recurso está perto do limite?**

Exemplos:

```text
CPU
memory pressure
thread pool
connection pool
Kafka consumer lag
queue depth
database connections
disk I/O
```

Imagine:

```text
HikariCP

max = 30
active = 30
pending = 150
```

O problema pode não ser CPU.

Pode ser:

```text
connection pool saturado
```

---

# 13. Métricas de JVM

Micrometer consegue expor métricas importantes relacionadas à JVM, incluindo memória, GC, CPU e threads. 

Exemplos:

```text
jvm.memory.used

jvm.gc.pause

jvm.threads.live

process.cpu.usage
```

Isso conecta diretamente observabilidade ao estudo de JVM.

---

# 14. Métricas de negócio

Esse é um ponto que diferencia muito uma visão Senior de uma visão Tech Lead.

Não basta saber:

```text
CPU = 40%
memory = 2GB
```

Precisamos saber:

```text
quantos pedidos estão sendo criados?

quantos pagamentos estão falhando?

quantos checkouts estão sendo abandonados?
```

Exemplos:

```text
orders_created_total

payments_failed_total

orders_cancelled_total

checkout_duration_seconds
```

---

# 15. Exemplo de incidente

Imagine:

```text
CPU normal
memory normal
HTTP 200 normal
```

Mas:

```text
orders_created
↓ 70%
```

Isso pode indicar um problema real de negócio.

Talvez:

```text
checkout quebrado
```

mas tecnicamente os endpoints ainda retornam `200`.

Sem métricas de negócio, a equipe pode considerar o sistema:

```text
healthy
```

quando na prática ele não está cumprindo seu objetivo.

---

# 16. Micrometer

Micrometer é muito utilizado no ecossistema Spring.

Uma forma simples de pensar é:

```text
Application
    ↓
Micrometer
    ↓
MeterRegistry
    ↓
Prometheus
Datadog
etc.
```

Exemplo conceitual:

```java
counter.increment();
```

para:

```text
orders_created
```

O Micrometer também possui a API de `Observation`, permitindo instrumentar uma operação uma vez e gerar diferentes sinais, como métricas e tracing dependendo dos handlers configurados. 

---

# 17. Prometheus

Prometheus coleta e armazena métricas como séries temporais.

Exemplo:

```text
http_server_requests_seconds_count{
    method="GET",
    status="200",
    service="order-service"
}
```

Cada métrica possui:

```text
nome
+
labels
+
timestamp
+
valor
```

Prometheus utiliza:

```text
PromQL
```

para consulta e agregação das métricas. 

---

# 18. Grafana

Grafana é normalmente a camada de visualização.

Arquitetura típica:

```text
Application
    ↓
Prometheus
    ↓
Grafana
```

E podemos adicionar:

```text
Loki
    ↓
logs

Tempo
    ↓
traces
```

Então:

```text
              Grafana
             /   |   \
            /    |    \
Prometheus      Loki    Tempo
 metrics        logs    traces
```

Isso permite uma experiência integrada de investigação.

---

# 19. OpenTelemetry

OpenTelemetry resolve principalmente o problema de:

> **Como instrumentar aplicações sem ficar preso a um fornecedor específico?**

Podemos pensar:

```text
Java Application
      ↓
OpenTelemetry
      ↓
OTLP
      ↓
Collector
      ↓
backend
```

O backend pode ser:

```text
Tempo
Jaeger
Prometheus-compatible backend
ou plataforma comercial
```

No Java, o OpenTelemetry possui APIs para:

```text
TracerProvider
MeterProvider
LoggerProvider
ContextPropagators
```

ou seja, traces, métricas, logs e propagação de contexto. 

---

# 20. Java Agent

Uma opção muito poderosa é usar:

```text
OpenTelemetry Java Agent
```

Ele consegue instrumentar automaticamente diversas bibliotecas.

Exemplos:

```text
Spring MVC
HTTP clients
JDBC
Kafka
```

sem precisar adicionar spans manualmente em cada ponto.

O Java Agent é atualmente a opção padrão recomendada pela própria documentação do OpenTelemetry para instrumentar aplicações Spring Boot quando suas características atendem ao cenário. 

---

# 21. Automatic x Manual Instrumentation

Auto instrumentation é excelente para:

```text
HTTP
JDBC
Kafka
frameworks
```

Mas ela não conhece seu negócio.

Por exemplo, ela pode enxergar:

```text
POST /checkout
```

mas não sabe:

```text
checkout de cliente premium
```

ou:

```text
pedido cancelado por fraude
```

Por isso normalmente combinamos:

```text
automatic instrumentation
+
custom instrumentation
```

---

# 22. Distributed Tracing

Imagine:

```text
API Gateway
     ↓
Order Service
     ↓
Payment Service
     ↓
Bank API
```

Uma única operação possui:

```text
Trace ID = abc123
```

Cada etapa possui um:

```text
Span
```

Exemplo:

```text
Trace abc123

GET /checkout              1.8s
│
├── Order Service          1.7s
│
├── Inventory              120ms
│
└── Payment                1.4s
    │
    └── Bank API           1.3s
```

Agora fica muito mais fácil identificar o gargalo.

---

# 23. Tempo e Jaeger

Tanto Tempo quanto Jaeger podem atuar como backends de distributed tracing.

Tempo é fortemente integrado à stack Grafana e permite correlacionar traces com logs e métricas. 

Jaeger possui componentes para receber, armazenar, consultar e visualizar traces e atualmente recomenda OpenTelemetry Collector para coleta padronizada de telemetria. 

Não é necessário memorizar qual ferramenta é “melhor”.

Para entrevista, saiba:

```text
OpenTelemetry
=
instrumentação e padrão


Tempo / Jaeger
=
backend de tracing
```

---

# 24. SLI

SLI significa:

**Service Level Indicator.**

É uma medida quantitativa.

Por exemplo:

```text
99.96% availability
```

ou:

```text
99% das requests
< 300ms
```

Google SRE define SLI como uma medida quantitativa cuidadosamente definida de algum aspecto do nível de serviço. 

---

# 25. SLO

SLO significa:

**Service Level Objective.**

É o objetivo desejado para um SLI.

Exemplo:

```text
SLI atual
99.96% availability


SLO
>= 99.9%
```

Então:

```text
99.96%
>
99.9%
```

estamos cumprindo o objetivo.

---

# 26. SLA

SLA significa:

**Service Level Agreement.**

É um compromisso com usuários ou clientes.

Exemplo:

```text
SLA

99.5% availability
```

Se a disponibilidade ficar abaixo disso, podem existir consequências:

```text
crédito
multa
compensação
obrigação contratual
```

Essa é a principal diferença entre SLO e SLA.

Um SLO é um objetivo operacional.

Um SLA inclui compromisso e consequências quando o nível não é atendido. 

---

# 27. SLI x SLO x SLA

Memorize:

```text
SLI
 ↓
o que estou medindo?


SLO
 ↓
qual é minha meta?


SLA
 ↓
o que prometi formalmente?
```

Exemplo:

```text
SLI
99.96% availability


SLO
99.9%


SLA
99.5%
```

---

# 28. Error Budget

Se o SLO é:

```text
99.9%
```

então aceitamos aproximadamente:

```text
0.1%
```

de erro dentro da janela considerada.

Esse é o:

**error budget.**

A ideia é equilibrar:

```text
Reliability
     ↕
Innovation
```

Se estamos muito dentro do SLO:

```text
podemos assumir mais risco
```

Se estamos queimando rapidamente o error budget:

```text
precisamos priorizar confiabilidade
```

Google SRE recomenda SLOs realistas em vez de tentar atingir 100%, justamente porque o error budget ajuda a equilibrar confiabilidade e velocidade de mudança. 

---

# 29. Como um Tech Lead responde: “o sistema está saudável?”

Uma resposta fraca seria:

> CPU está baixa e os pods estão UP.

Uma resposta melhor:

```text
Infraestrutura
│
├── CPU
├── Memory
├── Disk
└── Network

Aplicação
│
├── Latency
├── Traffic
├── Errors
└── Saturation

Dependências
│
├── Database
├── Kafka
└── External APIs

Negócio
│
├── orders_created
├── checkout_conversion
├── payments_failed
└── orders_cancelled

SLO
│
├── availability
├── latency
└── error budget
```

Isso mostra a saúde do sistema sob várias perspectivas.

---

# 30. Mapa mental

Para memorizar:

```text
Observability
│
├── Logs
│     ↓
│   o que aconteceu?
│
├── Metrics
│     ↓
│   quanto / com que frequência?
│
└── Traces
      ↓
    onde aconteceu?
```

Depois:

```text
Golden Signals

Latency
Traffic
Errors
Saturation
```

E acima disso:

```text
Business Metrics
       ↓
O sistema está entregando
valor para o negócio?
```

E finalmente:

```text
SLI
 ↓
medição

SLO
 ↓
meta

SLA
 ↓
compromisso
```

---

# Resposta objetiva para entrevista

> Para mim, observabilidade significa conseguir inferir a saúde e o comportamento interno do sistema através dos sinais que ele produz. Eu trabalho principalmente com logs, métricas e traces.
>
> Logs ajudam a entender eventos específicos, métricas mostram tendências e permitem alertas, e traces permitem seguir uma requisição através de vários serviços e identificar onde ocorreu latência ou falha.
>
> Em Java e Spring, posso utilizar Micrometer para métricas e observações e OpenTelemetry para padronizar traces, métricas e logs. OpenTelemetry também oferece instrumentação automática através do Java Agent e integração com Spring Boot. 
>
> Para métricas, eu não observo apenas CPU e memória. Considero principalmente latency, traffic, errors e saturation. Em latência prefiro analisar percentis como p95 e p99, porque médias podem esconder long tails.
>
> Também considero métricas de negócio, como `orders_created`, `payments_failed`, `checkout_duration` e `orders_cancelled`, porque uma aplicação pode estar tecnicamente saudável e ainda assim não estar entregando valor para o negócio.
>
> Para a stack, uma combinação comum é Prometheus para métricas, Loki para logs, Tempo ou Jaeger para traces e Grafana para visualização e correlação. 
>
> Por fim, utilizo SLI, SLO e SLA para transformar observabilidade em objetivos mensuráveis. SLI é o indicador medido, SLO é a meta desejada e SLA é o compromisso formal que pode ter consequências quando não é cumprido. 
>
> Então, para avaliar a saúde de um sistema, eu olho **infraestrutura, golden signals, dependências, métricas de negócio e cumprimento dos SLOs**, e não apenas se os pods estão `UP`.
