# FASE 21 — Performance Engineering

Lucas, o ponto central em Performance Engineering é **não otimizar por intuição**. O processo correto é partir de um sintoma observável, levantar hipótese, medir com profiling, encontrar a causa raiz, corrigir e depois provar a melhoria com benchmark.

## 1. Conceitos, trade-offs e casos de uso

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **Performance Engineering** | Disciplina de medir, diagnosticar e melhorar latência, throughput, uso de recursos e escalabilidade. | Otimização aumenta complexidade se feita sem evidência. | Investigar aumento de p99 após deploy. |
| **Sintoma** | Manifestação observada do problema: lentidão, erro, CPU alta, OOM, throughput baixo. | Sintoma não é necessariamente causa raiz. | API passou de 200 ms para 2 s. |
| **Métrica** | Evidência quantitativa usada para dimensionar e localizar o problema. | Métricas erradas podem levar a hipóteses erradas. | p95, p99, CPU, GC pause, pool saturation. |
| **Hipótese** | Explicação testável para o comportamento observado. | A hipótese pode estar errada; precisa ser validada por dados. | “CPU aumentou por serialização excessiva.” |
| **Profiling** | Medição detalhada de onde CPU, memória, locks ou tempo estão sendo consumidos. | Possui overhead e precisa ser realizado representando o workload real. | Descobrir método responsável por 40% da CPU. |
| **Root Cause** | Causa fundamental responsável pelo sintoma. | Corrigir apenas sintomas pode deslocar o gargalo sem resolver o problema. | N+1 gerando saturação do banco. |
| **Correção** | Mudança aplicada especificamente sobre a causa identificada. | Toda otimização pode introduzir regressão ou novo trade-off. | DTO Projection para eliminar carregamento excessivo. |
| **Benchmark** | Comparação controlada antes/depois para comprovar impacto da alteração. | Benchmark irreal produz conclusões enganosas. | Confirmar redução de p99 após otimização. |
| **JFR** | Java Flight Recorder coleta eventos da JVM e aplicação, como CPU, GC, allocations, threads e locks. | Perfis mais detalhados aumentam overhead; algumas opções específicas são mais caras. | Diagnóstico de JVM em produção. |
| **async-profiler** | Sampling profiler de baixo overhead para CPU, allocations, native memory e contended locks. | Normalmente exige acesso adequado ao processo/SO e interpretação correta dos profiles. | Flame Graph para localizar hot methods. |
| **JMeter** | Ferramenta de load testing capaz de gerar carga e medir latência, throughput e erros. | Testes grandes podem consumir muitos recursos no próprio gerador de carga. | Simular milhares de chamadas HTTP. |
| **Gatling** | Ferramenta de performance test-as-code com definição de cenários e modelos de carga. | Exige modelar corretamente usuários e workload; teste irreal leva a resultados irreais. | Load test versionado junto ao projeto. |
| **k6** | Ferramenta de load testing orientada a código, com cenários, métricas e thresholds. | É necessário dimensionar corretamente carga, VUs e infraestrutura do gerador. | Validar `p95 < 300 ms` no CI/CD. |
| **CPU** | Tempo de processamento consumido executando código. | Mais CPU pode indicar carga legítima ou ineficiência; percentual isolado não basta. | Loop caro, serialização, regex, criptografia. |
| **Memory** | Consumo de Heap e memória nativa da aplicação. | Aumentar memória pode mascarar leak em vez de resolvê-lo. | OOM, crescimento contínuo do heap. |
| **GC** | Garbage Collector recupera objetos que não são mais alcançáveis. | GC excessivo consome CPU e pode aumentar latência; tuning prematuro pode esconder allocation pressure. | Pausas altas ou GC muito frequente. |
| **Threads** | Unidades concorrentes de execução. | Threads demais aumentam memória, scheduling e contention; poucas podem limitar throughput. | Thread pool saturado ou milhares de threads bloqueadas. |
| **Locks** | Sincronização usada para controlar acesso concorrente a recursos compartilhados. | Contention reduz paralelismo e aumenta latência. | Muitas threads esperando pelo mesmo `synchronized`. |
| **Database** | Banco frequentemente concentra custo de I/O, queries, locks e conexões. | Otimização exige avaliar query, índice, plano e padrão de acesso, não apenas aumentar pool. | N+1, query lenta, connection pool saturado. |
| **Network** | Latência, bandwidth, DNS, TLS e chamadas remotas influenciam o tempo total. | Fora do processo Java, então CPU profiling sozinho pode não revelar o gargalo. | API aguardando 900 ms por serviço externo. |
| **Serialization** | Conversão de objetos para JSON, Avro, Protobuf etc. | Payloads grandes e conversões excessivas consomem CPU, memória e rede. | API retornando objetos enormes com Jackson. |

---

# 2. Pipeline mental de diagnóstico

Esse fluxo precisa ficar automático:

```text
Sintoma
   ↓
Métrica
   ↓
Hipótese
   ↓
Profiling
   ↓
Root Cause
   ↓
Correção
   ↓
Benchmark
```

### Exemplo

Você percebe:

```text
p99
200 ms → 1.5 s
```

Depois do deploy.

Primeiro observa as métricas:

```text
CPU aumentou
GC normal
DB normal
```

Hipótese:

> Existe processamento CPU-bound novo.

Executa profiling:

```text
async-profiler
        ↓
Flame Graph
        ↓
ObjectMapper serialization
        ↓
45% CPU
```

Descobre que o novo endpoint está serializando uma estrutura muito maior.

Corrige:

```text
Entity gigante
     ↓
DTO Projection
     ↓
payload menor
```

Depois roda novamente o teste:

```text
Antes
p99 = 1.5 s

Depois
p99 = 280 ms
```

Isso é Performance Engineering.

---

# 3. JFR

JFR, ou Java Flight Recorder, é uma das primeiras ferramentas que eu consideraria para diagnosticar uma aplicação Java.

Ele consegue registrar informações como:

```text
CPU
GC
allocations
threads
locks
exceptions
I/O
JVM internals
```

A grande vantagem é que foi projetado para observação de aplicações Java com baixo overhead; a Oracle informa que gravações contínuas padrão geralmente têm impacto muito baixo, enquanto configurações de profiling mais detalhadas custam mais. 

Mentalmente:

```text
Aplicação Java
     ↓
JFR recording
     ↓
JDK Mission Control
     ↓
análise
```

---

# 4. async-profiler

Quando preciso aprofundar CPU ou allocations, `async-profiler` é especialmente útil.

Ele pode analisar:

```text
CPU
allocations
native memory
locks
page faults
context switches
```

e mostrar stacks Java, nativas e até kernel em determinados ambientes. 

Um dos resultados mais conhecidos é o:

```text
Flame Graph
```

Conceitualmente:

```text
largura da função
        ↓
quanto aparece nas amostras
```

Se uma função ocupa grande parte do gráfico, vale investigar aquele call path.

---

# 5. CPU alta

CPU alta não significa automaticamente:

> preciso de mais pods.

Primeiro pergunte:

```text
Onde CPU está sendo consumida?
```

Pode ser:

```text
JSON serialization
loops
regex
compression
encryption
GC
JIT
lock spinning
código novo
```

Uma abordagem:

```text
CPU alta
   ↓
JFR / async-profiler
   ↓
Hot Methods
   ↓
stack trace
   ↓
causa
```

---

# 6. Memory

Quando memória cresce, diferencie:

```text
Heap
Metaspace
Thread Stacks
Direct Buffers
Code Cache
Native Memory
```

Também diferencie:

```text
memory pressure
```

de:

```text
memory leak
```

Se a memória cresce:

```text
8 GB
 ↓ GC
3 GB
```

pode ser comportamento normal.

Se ocorre:

```text
3 GB
 ↓
4 GB
 ↓
5 GB
 ↓
6 GB
```

mesmo depois de ciclos relevantes de GC, existe indício de retenção.

A investigação pode incluir:

```text
JFR
allocation profiling
heap dump
async-profiler alloc
```

---

# 7. GC

GC pode causar dois problemas principais:

```text
CPU elevada
```

ou:

```text
latency / pauses
```

Mas a causa nem sempre é o collector.

Por exemplo:

```text
aplicação cria
10 GB/s de objetos
       ↓
GC precisa trabalhar
constantemente
```

Nesse cenário, trocar:

```text
G1 → ZGC
```

sem investigar allocations pode não resolver a causa.

Primeiro investigue:

```text
allocation rate
heap occupancy
GC frequency
pause duration
live set
```

O JFR registra eventos específicos para análise de Garbage Collection. 

---

# 8. Threads e Locks

Imagine:

```text
CPU = 25%
```

mas:

```text
latência = 5 segundos
```

Pode existir:

```text
200 threads
   ↓
WAITING
   ↓
mesmo lock
```

Então CPU baixa não significa sistema saudável.

O problema pode ser:

```text
contention
```

Exemplo:

```java
synchronized void process() {
    expensiveOperation();
}
```

Cem threads chegam:

```text
Thread 1 → executando

Thread 2 ─┐
Thread 3 ─┼→ esperando
Thread 4 ─┘
```

`async-profiler` consegue realizar profiling de contended locks, enquanto JFR também fornece eventos úteis de threads e sincronização. 

---

# 9. Database

Muitas vezes o problema atribuído ao Java está no banco.

Exemplo:

```text
GET /orders
     ↓
2 segundos
```

Trace mostra:

```text
Java = 40 ms

PostgreSQL = 1.8 s
```

Nesse cenário, profiling de CPU Java provavelmente não é a prioridade.

Investigue:

```text
query
EXPLAIN ANALYZE
indexes
N+1
locks
connection pool
round trips
```

Por isso Performance Engineering precisa correlacionar aplicação, banco e infraestrutura.

---

# 10. Network

Outro exemplo:

```text
Checkout = 3 segundos
```

mas:

```text
Order Service     40ms
Database          60ms
Payment API      2.7s
```

O problema não está necessariamente no código Java.

Pode estar em:

```text
network latency
TLS handshake
DNS
remote service
timeouts
connection reuse
payload
```

Distributed tracing ajuda muito nessa investigação.

---

# 11. Serialization

Serialization costuma ser subestimada.

Imagine:

```text
JPA Entity
   ↓
Jackson
   ↓
JSON de 5 MB
```

Você paga em:

```text
CPU
allocations
GC
network
latência
```

Uma solução pode ser:

```text
DTO Projection
     ↓
somente campos necessários
     ↓
JSON menor
```

Performance normalmente envolve corrigir o **volume de trabalho**, não apenas tornar o mesmo trabalho alguns microssegundos mais rápido.

---

# 12. Load Test não é Profiling

Essa diferença é importante.

### Load testing

Responde:

> Como o sistema se comporta sob determinada carga?

Ferramentas:

```text
JMeter
Gatling
k6
```

### Profiling

Responde:

> Onde a aplicação está gastando recursos?

Ferramentas:

```text
JFR
async-profiler
```

Portanto:

```text
k6
 ↓
descobre que p99 piorou

async-profiler
 ↓
ajuda a descobrir por quê
```

---

# 13. JMeter

JMeter é uma ferramenta madura para criar testes de carga.

Pode simular:

```text
HTTP
APIs
banco
outros protocolos
```

Um ponto importante: a própria documentação recomenda criar/debugar o plano pela interface gráfica, mas executar testes de carga reais em **CLI mode**, não pela GUI. 

É útil quando já existe conhecimento ou infraestrutura de testes baseada em JMeter.

---

# 14. Gatling

Gatling segue uma abordagem fortemente orientada a:

```text
test as code
```

Ele trabalha com:

```text
scenarios
virtual users
injection profiles
open workload
closed workload
assertions
```

e possui SDKs incluindo Java. 

É especialmente interessante quando você quer manter testes de performance versionados junto ao software.

---

# 15. k6

k6 também trabalha muito bem com Performance Testing as Code.

Podemos definir:

```text
scenarios
virtual users
arrival rate
metrics
thresholds
```

Por exemplo:

```text
p95 < 300ms
errors < 1%
```

Se o threshold não for cumprido:

```text
teste falha
```

Isso permite integrar performance ao CI/CD. 

---

# 16. Tipos de testes importantes

Não pense apenas em:

```text
Load Test
```

Existem diferentes perguntas.

### Smoke Test

```text
Pouca carga
```

Pergunta:

> O teste e o sistema funcionam?

### Load Test

```text
Carga esperada
```

Pergunta:

> O sistema suporta o tráfego normal?

### Stress Test

```text
além da capacidade esperada
```

Pergunta:

> Onde o sistema começa a degradar?

### Spike Test

```text
aumento abrupto
```

Pergunta:

> Como o sistema reage a picos?

### Soak Test

```text
carga por longo período
```

Pergunta:

> Existe leak ou degradação gradual?

---

# 17. Cuidado com benchmark ruim

Um benchmark precisa representar o sistema real.

Teste:

```text
localhost
1 usuário
banco vazio
sem cache real
```

não prova que:

```text
produção
5.000 req/s
100 milhões de registros
```

terá o mesmo comportamento.

Durante testes de carga, monitore também:

```text
CPU
Memory
GC
DB
connection pool
threads
network
errors
p95
p99
```

Não olhe apenas:

```text
requests por segundo
```

---

# 18. Performance é trade-off

Uma otimização frequentemente troca um recurso por outro.

Exemplo:

```text
Cache
 ↓
menos banco
 ↓
mais memória
```

Outro:

```text
mais threads
 ↓
mais concorrência
 ↓
mais memory/context switching
```

Outro:

```text
compression
 ↓
menos network
 ↓
mais CPU
```

Outro:

```text
batch processing
 ↓
mais throughput
 ↓
maior latência individual
```

Por isso não existe:

> “essa implementação é mais performática”

sem completar:

> **em qual dimensão e sob qual workload?**

---

# 19. Mapa mental para diagnóstico

Para entrevista, memorize:

```text
Latência alta
│
├── CPU alta?
│     ↓
│   JFR / async-profiler
│
├── GC alto?
│     ↓
│   allocations / heap / GC
│
├── Threads esperando?
│     ↓
│   thread dump / lock profiling
│
├── Banco lento?
│     ↓
│   EXPLAIN ANALYZE / queries / locks
│
├── Rede?
│     ↓
│   tracing / latency / timeout
│
└── Payload grande?
      ↓
    serialization / network
```

---

# Resposta objetiva para entrevista

> Em Performance Engineering, eu evito otimização baseada em intuição. Trabalho com um pipeline de diagnóstico: começo pelo sintoma, observo métricas, formulo uma hipótese, faço profiling, encontro a root cause, aplico uma correção e depois valido o resultado com benchmark.
>
> Para aplicações Java, utilizo JFR para ter uma visão ampla de CPU, GC, memória, threads e comportamento da JVM. Quando preciso aprofundar CPU, allocations ou lock contention, posso usar async-profiler e analisar Flame Graphs. 
>
> Também procuro identificar em qual dimensão está o gargalo. CPU alta pode ser código ou serialização; memória pode indicar allocation pressure ou leak; GC pode ser consequência de excesso de objetos; threads podem estar bloqueadas por locks; e muitas vezes a causa está fora da JVM, como banco, rede ou serviço remoto.
>
> Para validar capacidade, utilizo ferramentas como JMeter, Gatling ou k6. O importante é modelar um workload realista e definir critérios objetivos, como throughput, error rate e percentis de latência. k6, por exemplo, permite transformar metas como `p95` e taxa de erros em thresholds de aprovação do teste. 
>
> Então, para mim, Performance Engineering significa **medir antes de otimizar, localizar o gargalo com evidência e comprovar a melhoria depois da correção**.
