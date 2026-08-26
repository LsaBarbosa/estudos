# FASE 8 — Kafka e Event-Driven

Lucas, para Kafka em nível Senior, o principal não é decorar `Producer`, `Consumer` e `Topic`. É entender **como particionamento determina paralelismo e ordenação, como offsets permitem replay, como consumer groups distribuem trabalho e como sua aplicação lida com duplicidade, atraso e falhas de processamento**.

## 1. Tabela — conceitos, trade-offs e casos de uso

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **Broker** | Servidor Kafka responsável por armazenar partitions e atender Producers e Consumers. | Mais brokers aumentam disponibilidade e capacidade, mas também infraestrutura e operação. | Cluster Kafka distribuído. |
| **Topic** | Log lógico onde eventos relacionados são publicados. É dividido em uma ou mais partitions. | Muitos topics aumentam isolamento, mas também metadata e complexidade operacional. | `orders`, `payments`, `customers`. |
| **Partition** | Unidade física de armazenamento, ordenação e paralelismo de um topic. | Mais partitions aumentam paralelismo, mas elevam overhead e tornam ordenação global impossível entre partitions. | Distribuir milhões de eventos entre Consumers. |
| **Offset** | Posição de um record dentro de uma partition. | Permite controlar consumo e replay, mas commits incorretos podem causar perda ou duplicação de processamento. | Retomar consumo após restart. |
| **Producer** | Cliente que publica eventos nos topics. | Configurações de `acks`, retries, idempotência e batching afetam latência, throughput e durabilidade. | Publicar `OrderCreated`. |
| **Consumer** | Cliente que lê records das partitions. | Processamento lento aumenta consumer lag e pode provocar rebalances se o ciclo de polling não for respeitado. | Processar pedidos ou pagamentos. |
| **Consumer Group** | Conjunto de Consumers cooperando para consumir um topic. Cada partition é atribuída a no máximo um Consumer do grupo por vez. | Mais Consumers que partitions não aumentam paralelismo útil. | Escalar horizontalmente processamento. |
| **Ordering** | Kafka preserva ordem dentro de uma mesma topic-partition, não entre todas as partitions. | Ordenação global exigiria reduzir paralelismo, por exemplo usando uma única partition. | Eventos do mesmo pedido em ordem. |
| **Replay** | Reprocessar records reposicionando o Consumer para offsets anteriores. | Reprocessamento pode repetir efeitos colaterais; consumidores precisam ser idempotentes. | Recalcular projeções ou corrigir bug de processamento. |
| **Partitioning** | Estratégia que decide em qual partition cada record será escrito. | Uma distribuição ruim pode criar hot partitions. | Distribuir pedidos por `orderId`. |
| **Partition Key** | Chave utilizada para direcionar eventos relacionados à mesma partition. | Chave com baixa cardinalidade pode concentrar tráfego; mudar quantidade de partitions pode alterar o mapeamento futuro das keys. | `orderId`, `customerId`, `accountId`. |
| **Rebalancing** | Redistribuição das partitions entre Consumers de um mesmo grupo. | Durante mudança de ownership pode haver pausa, aumento temporário de lag e necessidade de tratar offsets corretamente. | Consumer entra, sai ou falha. |
| **Backpressure** | Controle quando a taxa de entrada é maior que a capacidade de processamento do consumidor/downstream. | Reduzir ingestão protege o sistema, mas aumenta lag. | Banco lento, API externa indisponível. |
| **Consumer Lag** | Diferença entre a posição final do log e a posição já consumida/commitada pelo Consumer Group. | Lag crescente indica que consumo não acompanha produção, mas lag zero sozinho não garante processamento saudável. | Monitorar capacidade de Consumers. |
| **At-most-once** | Record pode ser perdido, mas não deve ser processado novamente. | Menor risco de duplicidade, maior risco de perda. | Métricas ou dados descartáveis em alguns cenários. |
| **At-least-once** | Record não deve ser perdido, mas pode ser processado mais de uma vez. | Exige idempotência. | Semântica mais comum em sistemas de negócio. |
| **Exactly-once** | Kafka consegue coordenar processamento transacional em determinados fluxos Kafka-to-Kafka. | Mais complexidade e custo; não transforma automaticamente banco, e-mail ou API externa em exactly-once. | Kafka Streams e pipelines Kafka transacionais. |
| **Idempotência** | Processar novamente a mesma mensagem sem produzir um segundo efeito de negócio indevido. | Requer chave de deduplicação ou controle de estado. | Evitar cobrança duplicada. |
| **Poison Message** | Mensagem que falha repetidamente de forma determinística e impede progresso normal do Consumer. | Retry infinito pode bloquear uma partition inteira. | Payload inválido ou regra impossível de processar. |
| **Retry** | Tentar novamente após uma falha transitória. | Retry imediato pode bloquear a partition e sobrecarregar uma dependência já degradada. | Timeout temporário em API externa. |
| **Retry Topic** | Encaminhar eventos falhos para outro topic e tentar novamente posteriormente. | Evita bloquear o topic principal, mas pode quebrar a ordenação original. | Retry com backoff. |
| **DLQ** | Dead Letter Queue/Topic para mensagens que não puderam ser processadas depois das estratégias previstas. | Evita bloquear fluxo principal, mas mensagem na DLQ ainda precisa de observação e tratamento. | Payload inválido ou falha persistente. |
| **Parking Lot** | Destino mais definitivo para mensagens que já esgotaram retries automáticos e precisam de intervenção. | Exige processo operacional de recuperação. | Erro que precisa de análise humana. |
| **Manual Recovery** | Correção e reprocessamento controlado de mensagens problemáticas. | Mais seguro em falhas críticas, porém exige operação e ferramentas. | Corrigir evento e republicar. |

Kafka define as partitions como a unidade de ordenação: records com a mesma key são direcionados à mesma partition pelo particionamento usual, e os consumidores leem uma partition na ordem em que os records foram armazenados. Um Consumer Group distribui essas partitions entre seus membros. 

---

# 2. Broker, Topic e Partition

O modelo básico é:

```text
Kafka Cluster
│
├── Broker 1
├── Broker 2
└── Broker 3
```

Dentro do cluster existem topics:

```text
orders
payments
customers
```

Um topic é dividido em partitions:

```text
orders

Partition 0
Partition 1
Partition 2
Partition 3
```

A partition é uma das unidades mais importantes do Kafka porque determina:

**armazenamento, ordenação e paralelismo.**

---

# 3. Producer e Consumer

O Producer publica eventos:

```text
Order Service
      ↓
Producer
      ↓
Kafka
      ↓
orders topic
```

O Consumer lê:

```text
orders topic
      ↓
Consumer
      ↓
Payment Service
```

Eles são desacoplados.

O Producer não precisa saber qual Consumer irá processar aquele evento.

---

# 4. Offset

Dentro de cada partition os records possuem posições chamadas offsets.

Exemplo:

```text
Partition 0

Offset 0 → OrderCreated
Offset 1 → PaymentApproved
Offset 2 → OrderShipped
```

O offset representa:

> **a posição do record naquela partition.**

E o Consumer mantém sua posição para saber até onde avançou. 

Esse conceito permite algo fundamental:

**replay.**

---

# 5. Replay

Kafka não remove a mensagem simplesmente porque um Consumer a leu.

Os records permanecem conforme a política de retenção do topic.

Então podemos reposicionar um Consumer:

```text
offset atual = 5000

        ↓ reset

offset = 3000
```

e consumir novamente:

```text
3000
3001
3002
...
```

Isso é extremamente poderoso para:

reprocessamento,

correção de bugs,

reconstrução de projeções,

auditoria,

e recuperação.

Mas gera uma consequência:

> **se você pode processar novamente, seu Consumer deve estar preparado para duplicidade.**

---

# 6. Consumer Group

Considere:

```text
Topic: orders

P0
P1
P2
P3
```

E:

```text
Consumer Group: payment-service

Consumer A
Consumer B
```

Kafka pode distribuir:

```text
Consumer A
├── P0
└── P1

Consumer B
├── P2
└── P3
```

Dentro de um Consumer Group, cada partition é atribuída a um Consumer por vez. 

Isso cria paralelismo.

---

# 7. Mais Consumers não significa sempre mais velocidade

Imagine:

```text
4 partitions

10 consumers
```

Só existem quatro unidades de trabalho paralelas.

Então aproximadamente:

```text
Consumer 1 → P0
Consumer 2 → P1
Consumer 3 → P2
Consumer 4 → P3

Consumers 5 a 10
→ sem partition útil
```

Para um determinado topic dentro do grupo:

> **o paralelismo máximo de consumo é limitado pela quantidade de partitions.**

Isso é muito importante em capacity planning.

---

# 8. Ordering

Kafka **não garante ordenação global do topic** quando existem múltiplas partitions.

Ele garante ordem dentro de cada partition. 

Então:

```text
Partition 0

A
B
C
```

garante:

```text
A → B → C
```

Mas:

```text
P0 → A, C

P1 → B
```

não fornece uma ordem global confiável:

```text
A → B → C
```

---

# 9. Partition Key — exemplo do pedido

Imagine:

```text
orderId = 123
```

Eventos:

```text
OrderCreated
PaymentApproved
OrderShipped
```

Se todos utilizarem:

```text
key = 123
```

o particionamento pode mantê-los na mesma partition:

```text
Partition 7

Offset 100
OrderCreated

Offset 101
PaymentApproved

Offset 102
OrderShipped
```

Assim preservamos:

> **ordenação por agregado.**

Não precisamos necessariamente de:

> ordenação global de todos os pedidos.

Essa é uma decisão arquitetural muito importante.

---

# 10. Estratégia de Partition Key

Uma boa key precisa equilibrar duas coisas:

```text
ordenação
+
distribuição
```

Por exemplo:

```text
orderId
```

costuma ter boa cardinalidade e mantém eventos do mesmo pedido juntos.

Mas imagine usar:

```text
country
```

com apenas:

```text
BR
US
AR
```

Agora podemos criar poucas partitions extremamente quentes.

Isso é:

**hot partition.**

Então:

> uma boa partition key deve preservar a relação necessária sem destruir a distribuição de carga.

---

# 11. Cuidado ao aumentar partitions

Existe um detalhe importante para entrevistas mais avançadas.

Se o particionamento depende de algo conceitualmente como:

```text
hash(key)
    ↓
número de partitions
```

aumentar a quantidade de partitions pode alterar para onde **novos records** de uma key serão direcionados.

Então não assuma que aumentar partitions é sempre uma operação transparente quando existe dependência forte de ordering por key.

Se essa garantia é crítica, a estratégia de particionamento e evolução do topic precisam ser planejadas.

---

# 12. Rebalancing

Imagine:

```text
Consumer A
Consumer B
Consumer C
```

Se o Consumer B morrer:

```text
Consumer B
    ↓
offline
```

suas partitions precisam ir para:

```text
Consumer A
ou
Consumer C
```

Isso gera um:

**rebalance.**

Também pode ocorrer quando:

um Consumer entra,

um Consumer sai,

ou partitions são adicionadas.

A documentação atual do Kafka inclusive possui um protocolo de rebalance incremental mais recente para reduzir o impacto desses eventos, mas o conceito continua sendo redistribuir ownership das partitions entre membros do grupo. 

---

# 13. Por que rebalance importa

Durante um rebalance precisamos cuidar de:

```text
offsets
processamento em andamento
ownership das partitions
```

Imagine:

```text
Consumer A

processando offset 500
        ↓
rebalance
        ↓
Partition vai para Consumer B
```

Se o offset foi gerenciado incorretamente:

```text
evento pode ser reprocessado
```

ou, dependendo da estratégia:

```text
evento pode ser perdido
```

Por isso offset management e rebalancing estão diretamente relacionados.

---

# 14. Consumer Lag

Imagine:

```text
último offset da partition
= 10.000
```

Consumer está em:

```text
8.000
```

Então aproximadamente:

```text
lag = 2.000
```

O próprio tooling do Kafka mostra `CURRENT-OFFSET`, `LOG-END-OFFSET` e `LAG` para Consumer Groups. 

Lag aumentando continuamente pode significar:

```text
produção
   ↓↓↓↓↓↓↓↓↓

consumo
   ↓↓↓
```

Ou seja:

> **o Consumer não está acompanhando a taxa de produção.**

---

# 15. Por que Consumer Lag cresce?

Possíveis causas:

```text
Consumer lento

API externa lenta

Banco lento

CPU alta

GC

poucos Consumers

poucas partitions

mensagem pesada

erro/retry excessivo

hot partition
```

Então:

> consumer lag é um sintoma.

Não necessariamente a causa.

---

# 16. Backpressure

Imagine:

```text
Kafka
100.000 eventos/minuto

        ↓

Consumer
20.000 eventos/minuto
```

O Consumer não consegue acompanhar.

Nesse caso precisamos controlar pressão.

No modelo Kafka, Consumers puxam dados e podem controlar quanto processam usando mecanismos como tamanho do lote, `poll`, pausa e retomada das partitions e capacidade interna de processamento. Configurações como `max.poll.records` também ajudam a limitar quantos records são entregues por chamada ao consumer. 

Mas existe uma regra importante:

> **Backpressure não deve significar criar infinitas threads para tentar acompanhar Kafka.**

Se o downstream suporta apenas:

```text
100 chamadas simultâneas
```

a aplicação precisa respeitar esse limite.

---

# 17. At-most-once

At-most-once significa:

```text
0 ou 1 processamento
```

A mensagem pode ser perdida.

Mas você tenta evitar reprocessamento.

Conceitualmente:

```text
recebe evento
    ↓
commit offset
    ↓
processa
```

Se acontecer:

```text
commit
 ↓
crash antes de processar
```

o evento pode não ser processado novamente.

Ou seja:

```text
sem duplicidade
mas
possível perda
```

---

# 18. At-least-once

At-least-once significa:

```text
1 ou mais processamentos
```

O objetivo é:

> **não perder o evento.**

Conceitualmente:

```text
recebe
   ↓
processa
   ↓
commit offset
```

Agora imagine:

```text
processou pagamento
       ↓
crash
       ↓
não commitou offset
```

Quando voltar:

```text
mesmo evento
é consumido novamente
```

Resultado:

**duplicidade possível.**

Kafka normalmente trabalha muito bem com esse modelo e a aplicação precisa ser idempotente. 

---

# 19. Idempotência

Esse conceito é obrigatório com Event-Driven.

Imagine:

```text
PaymentApproved
eventId = ABC123
```

Primeira execução:

```text
ABC123
 ↓
processa
 ↓
salva eventId
```

Segunda execução:

```text
ABC123
 ↓
já processado
 ↓
ignora
```

Uma implementação pode utilizar:

```text
eventId
+
UNIQUE constraint
```

por exemplo.

Então:

> **At-least-once + idempotência é uma estratégia extremamente comum.**

---

# 20. Exactly-once

Exactly-once precisa ser explicado com cuidado.

Kafka possui suporte a produtores idempotentes e transações e consegue fornecer exactly-once para pipelines específicos, especialmente **read-process-write entre Kafka topics**. Kafka Streams suporta `exactly_once_v2`. 

Mas:

```text
Kafka exactly-once
```

não significa automaticamente:

```text
Kafka
+
PostgreSQL
+
Stripe
+
e-mail
+
outra API

=
everything exactly once
```

Essa é uma das respostas mais importantes do tema.

---

# 21. Exactly-once e efeitos externos

Imagine:

```text
Kafka Event
    ↓
Consumer
    ↓
cobra cartão
    ↓
Stripe respondeu sucesso
    ↓
Consumer crash
    ↓
offset não foi salvo
```

Depois:

```text
mesmo evento
    ↓
reprocessado
```

Kafka sozinho não sabe automaticamente:

```text
Stripe já cobrou?
```

Então podemos precisar de:

```text
idempotency key
Outbox
Inbox
deduplication
transação no banco
```

Kafka documenta exatamente essa limitação: coordenar offsets Kafka com sistemas externos é um problema diferente de transações realizadas somente dentro do Kafka. 

---

# 22. Poison Message

Poison Message é um evento que falha consistentemente.

Exemplo:

```text
Evento 100
→ sucesso

Evento 101
→ erro

retry
→ erro

retry
→ erro

retry
→ erro
```

Se simplesmente tentarmos infinitamente:

```text
Partition
   ↓
bloqueada no evento 101
```

e:

```text
102
103
104
105
```

podem nunca progredir.

Por isso precisamos de uma estratégia explícita.

---

# 23. Retry

Retry é adequado principalmente para erros transitórios.

Por exemplo:

```text
HTTP 503
timeout
database temporarily unavailable
```

Estratégia:

```text
tentativa 1
   ↓
espera
   ↓
tentativa 2
   ↓
espera maior
   ↓
tentativa 3
```

Idealmente utilizando:

**backoff.**

O problema é que retry dentro do mesmo Consumer pode bloquear o processamento daquela partition.

---

# 24. Retry Topic

Uma alternativa:

```text
orders
   ↓
falhou
   ↓
orders-retry
```

Depois:

```text
delay
 ↓
retry consumer
 ↓
processa novamente
```

Isso permite que o Consumer principal continue avançando.

Mas há um trade-off muito importante:

> **você pode perder a ordenação original.**

Por exemplo:

```text
OrderCreated
→ falhou
→ Retry Topic

PaymentApproved
→ topic principal
→ processado
```

Agora `PaymentApproved` pode ser processado antes de `OrderCreated`.

Então Retry Topic precisa ser escolhido conscientemente quando ordering é importante.

---

# 25. DLQ

DLQ significa:

**Dead Letter Queue**, normalmente implementada como outro topic.

Fluxo:

```text
evento
 ↓
processamento
 ↓
retry
 ↓
retry
 ↓
falhou definitivamente
 ↓
DLQ
```

A vantagem é:

```text
fluxo principal continua
```

Mas DLQ não significa:

```text
problema resolvido
```

Significa:

> **o problema saiu do caminho principal e agora precisa ser tratado operacionalmente.**

Kafka Connect, por exemplo, possui suporte explícito a Dead Letter Queue para registros que não podem ser processados normalmente. 

---

# 26. Parking Lot

Parking Lot normalmente representa um estágio ainda mais explícito de intervenção.

Podemos ter:

```text
Main Topic
    ↓
Retry Topic
    ↓
Retry novamente
    ↓
DLQ
    ↓
análise automática
    ↓
Parking Lot
```

Parking Lot significa aproximadamente:

> **não continue tentando automaticamente; alguém precisa investigar.**

Pode acontecer quando:

payload está incorreto,

regra de negócio é impossível,

referência externa não existe,

ou existe bug na aplicação.

---

# 27. Manual Recovery

Depois de investigar:

```text
Parking Lot
     ↓
corrige problema
     ↓
corrige payload se apropriado
     ↓
republica
```

Ou:

```text
corrige aplicação
     ↓
deploy
     ↓
replay
```

O processo precisa ser auditável.

Evite simplesmente apagar uma mensagem porque:

> "estava dando erro."

Em sistemas financeiros, pedidos ou pagamentos, isso pode significar perda real de negócio.

---

# 28. Estratégia prática para poison messages

Um bom fluxo pode ser:

```text
Evento
  ↓
Processa
  ↓
Falhou
  ↓
Erro transitório?
 ┌───────┴───────┐
sim              não
 ↓                ↓
Retry          DLQ/Parking
 ↓
Backoff
 ↓
esgotou retries?
 ↓
DLQ
 ↓
investigação
 ↓
manual recovery
```

E sempre decidir conscientemente:

```text
ordering é obrigatório?
idempotência existe?
retry pode duplicar efeito?
posso avançar a partition?
```

---

# 29. Mapa mental do Kafka

Para memorizar:

```text
Producer
   ↓
Topic
   ↓
Partition
   ↓
Offset
   ↓
Consumer
   ↓
Consumer Group
```

E os conceitos avançados:

```text
Partition Key
     ↓
define distribuição
e ordering


Consumer Group
     ↓
define paralelismo


Offset
     ↓
define posição
e replay


Consumer Lag
     ↓
mede atraso


Rebalance
     ↓
redistribui partitions
```

Nas falhas:

```text
At-most-once
→ pode perder


At-least-once
→ pode duplicar


Exactly-once
→ garantias transacionais
em contextos específicos
```

E:

```text
Poison Message
     ↓
Retry
     ↓
Retry Topic
     ↓
DLQ
     ↓
Parking Lot
     ↓
Manual Recovery
```

---

# Resposta objetiva para entrevista

> Kafka é uma plataforma de streaming distribuído baseada principalmente em topics e partitions. Producers publicam eventos e Consumers os processam através de Consumer Groups. As partitions são fundamentais porque determinam paralelismo e ordenação. Kafka garante ordering dentro de uma partition, não ordenação global do topic. 
>
> Por isso eu presto bastante atenção à partition key. Se eventos como `OrderCreated`, `PaymentApproved` e `OrderShipped` utilizam o mesmo `orderId` como key, consigo mantê-los na mesma partition e preservar ordenação por agregado.
>
> Consumers mantêm sua posição através de offsets, o que permite retomar o processamento e também realizar replay. Dentro de um Consumer Group, as partitions são distribuídas entre os Consumers, então o número de partitions também limita o paralelismo máximo daquele grupo. 
>
> Em produção, monitoro principalmente consumer lag e rebalancing. Lag crescente normalmente significa que o Consumer não está acompanhando a produção, podendo ser causado por processamento lento, downstream degradado, poucas partitions ou hot partitions.
>
> Em semântica de entrega, normalmente considero at-least-once junto com consumidores idempotentes. At-most-once pode perder mensagens. Kafka também oferece exactly-once em cenários transacionais específicos, especialmente Kafka-to-Kafka, mas isso não significa exactly-once automático para efeitos externos como banco, pagamento ou e-mail. Para esses casos ainda penso em idempotência, deduplicação, Outbox ou Inbox. 
>
> Para poison messages, diferencio falha transitória de falha permanente. Posso utilizar retry com backoff, Retry Topics, DLQ, Parking Lot e recuperação manual. Também considero o impacto dessas estratégias sobre ordering, porque tirar uma mensagem da partition principal pode permitir que eventos posteriores sejam processados antes dela.
>
> Então, para mim, dominar Kafka significa principalmente entender **partitioning, ordering, offsets, replay, consumer groups, lag, rebalancing, idempotência e tratamento de falhas**, e não apenas saber publicar e consumir mensagens.
