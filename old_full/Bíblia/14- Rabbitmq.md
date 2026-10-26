# RabbitMQ — Mensageria e Event-Driven

Para RabbitMQ, o modelo mental mais importante é diferente do Kafka:

**Producer → Exchange → Binding → Queue → Consumer**

Kafka é fortemente orientado a log, partition, offset e replay. RabbitMQ é mais orientado a **roteamento, filas, entrega e processamento de mensagens**.

## 1. Tabela — conceito, trade-off e caso de uso

| Item | Conceito objetivo | Trade-off | Caso de uso |
|---|---|---|---|
| **Broker** | Servidor RabbitMQ responsável por exchanges, queues, bindings e entrega das mensagens. | Alta disponibilidade aumenta custo operacional. | Broker central de mensageria. |
| **Producer / Publisher** | Aplicação que publica mensagens em um exchange. | Publicar sem confirmação pode aumentar risco de perda em falhas. | Serviço de pedidos publica uma mensagem. |
| **Exchange** | Recebe mensagens do Producer e decide para quais queues encaminhá-las. | Topologias complexas aumentam dificuldade de operação e debugging. | Roteamento de pagamentos, notificações e pedidos. |
| **Queue** | Buffer onde mensagens permanecem até serem entregues aos Consumers. | Backlogs muito grandes consomem recursos e podem indicar Consumer lento. | Fila `payment-processing`. |
| **Binding** | Regra que conecta um exchange a uma queue ou outro exchange. | Muitas bindings tornam o roteamento mais difícil de entender. | `orders.*` direcionado a uma queue. |
| **Routing Key** | Valor usado pelo exchange para decidir o destino da mensagem. | Estratégia ruim pode gerar roteamento incorreto ou acoplamento excessivo. | `order.created`, `payment.approved`. |
| **Consumer** | Aplicação que recebe e processa mensagens da queue. | Consumers lentos acumulam mensagens e aumentam latência. | Worker de processamento de pagamento. |
| **Competing Consumers** | Vários Consumers consomem da mesma queue para dividir trabalho. | Aumenta throughput, mas pode afetar ordering. | Escalar processamento horizontalmente. |
| **Direct Exchange** | Encaminha mensagens quando routing key corresponde exatamente à binding key. | Menos flexível que Topic Exchange. | Separar `payment` de `shipping`. |
| **Topic Exchange** | Faz roteamento por padrões de routing keys. | Mais flexibilidade, porém maior complexidade de topologia. | `order.created`, `order.cancelled`, `order.*`. |
| **Fanout Exchange** | Envia cada mensagem para todas as queues associadas, ignorando routing key. | Replica mensagens e aumenta processamento/storage. | Broadcast de evento para vários sistemas. |
| **Headers Exchange** | Roteia de acordo com headers da mensagem. | Menos comum e mais difícil de visualizar que routing keys. | Roteamento por múltiplos atributos. |
| **ACK** | Confirma ao RabbitMQ que a mensagem foi processada. | ACK precoce pode causar perda se aplicação falhar depois. | Confirmar processamento bem-sucedido. |
| **NACK / Reject** | Informa que a mensagem não foi processada. Pode requeue ou rejeitar. | Requeue infinito pode criar loop de falha. | Erro temporário ou poison message. |
| **Publisher Confirm** | Broker confirma ao Producer que recebeu/processou a publicação conforme suas garantias. | Adiciona controle e alguma latência. | Mensagem crítica que não pode ser silenciosamente perdida. |
| **Prefetch** | Limita quantidade de mensagens entregues e ainda não confirmadas a um Consumer. | Muito baixo limita throughput; muito alto aumenta memória e mensagens em voo. | Controlar backpressure. |
| **Ordering** | Uma queue possui ordem de mensagens, mas múltiplos Consumers e redeliveries podem alterar a ordem efetiva de processamento. | Preservar estritamente ordem reduz paralelismo. | Processar eventos sequenciais por recurso. |
| **Single Active Consumer** | Mantém apenas um Consumer ativo por queue e promove outro em caso de falha. | Preserva melhor a ordem, mas reduz paralelismo. | Fluxos que precisam de processamento sequencial. |
| **Redelivery** | Mensagem não confirmada pode voltar para a queue e ser entregue novamente. | Pode causar processamento duplicado. | Consumer caiu antes do ACK. |
| **At-most-once** | Mensagem pode ser perdida, mas evita reprocessamento. | Risco de perda. | Dados descartáveis. |
| **At-least-once** | Mensagem pode ser entregue novamente até ser confirmada. | Exige idempotência. | Pedidos e pagamentos. |
| **Exactly-once** | Efeito exatamente uma vez não é garantia geral automática do RabbitMQ. | Precisa de coordenação/idempotência fora do broker. | Implementado no nível da aplicação quando necessário. |
| **Idempotência** | Reprocessar a mesma mensagem sem duplicar efeitos de negócio. | Exige identificação e armazenamento de estado. | Evitar cobrança duplicada. |
| **Backpressure** | Controlar ritmo de entrega para não sobrecarregar Consumer/downstream. | Menor pressão pode aumentar backlog. | API ou banco mais lento que o Producer. |
| **Retry** | Tentar novamente uma operação que falhou temporariamente. | Retry imediato pode gerar loop e sobrecarga. | Timeout temporário de API. |
| **Retry Queue** | Queue intermediária usada para adiar nova tentativa. | Aumenta topologia e pode afetar ordering. | Retry com TTL e DLX. |
| **DLX** | Dead Letter Exchange recebe mensagens dead-lettered e as roteia novamente. | Precisa de estratégia operacional clara. | Mensagens rejeitadas ou expiradas. |
| **DLQ** | Queue destino de mensagens que não puderam ser processadas normalmente. | Se ninguém monitora, vira apenas depósito de erros. | Poison messages. |
| **TTL** | Tempo máximo que uma mensagem ou queue pode permanecer válida. | Valor inadequado pode expirar mensagens legítimas. | Retry atrasado ou mensagens temporárias. |
| **Poison Message** | Mensagem que falha repetidamente de forma determinística. | Pode bloquear/repetir continuamente se não houver tratamento. | Payload inválido. |
| **Quorum Queue** | Queue replicada usando consenso para maior segurança e disponibilidade dos dados. | Mais latência e recursos que cenários menos resilientes. | Mensagens críticas em produção. |

RabbitMQ suporta acknowledgements manuais e automáticos; com acknowledgements manuais, `prefetch` limita quantas mensagens podem ficar entregues e ainda não confirmadas, ajudando a evitar sobrecarga do Consumer. 

---

# 2. Exchange, Queue e Binding

O fluxo básico é:

```text
Producer
   ↓
Exchange
   ↓
Binding
   ↓
Queue
   ↓
Consumer
```

O Producer normalmente publica no **exchange**, não diretamente para uma queue.

O exchange decide o destino.

Isso permite separar:

**quem produz**

de

**quem consome**.

---

# 3. Direct Exchange

Direct Exchange utiliza correspondência exata da routing key.

Exemplo:

```text
routing key = payment
```

Binding:

```text
payment
   ↓
payment.queue
```

É adequado quando o roteamento é simples e explícito.

---

# 4. Topic Exchange

Topic Exchange permite padrões.

Exemplo:

```text
order.created
order.cancelled
order.shipped
```

Podemos criar:

```text
order.*
```

para consumir vários tipos de eventos relacionados.

É muito útil quando temos múltiplas categorias de mensagens.

---

# 5. Fanout Exchange

Fanout envia a mensagem para todas as queues associadas.

Por exemplo:

```text
OrderCreated
      ↓
Fanout Exchange
   /    |      \
  ↓     ↓       ↓
Email Analytics Fraud
```

Isso é útil para broadcast.

Cada Consumer recebe sua própria cópia através de sua queue.

---

# 6. Queue

Queue é onde a mensagem aguarda processamento.

Imagine:

```text
Queue

M1
M2
M3
M4
M5
```

Um Consumer começa a processar:

```text
M1
```

depois:

```text
M2
```

e assim por diante.

Mas quando adicionamos concorrência, a ordem de **processamento concluído** pode mudar.

---

# 7. Competing Consumers

Vários Consumers podem consumir a mesma queue:

```text
             Queue
          /    |    \
         ↓     ↓     ↓
Consumer A   B       C
```

Isso aumenta throughput.

RabbitMQ normalmente distribui mensagens entre Consumers ativos da queue. 

Mas imagine:

```text
Consumer A → mensagem 1 → processamento lento

Consumer B → mensagem 2 → processamento rápido
```

A mensagem 2 pode terminar antes da mensagem 1.

Portanto:

> **ordem de entrega não significa necessariamente ordem de conclusão quando existe concorrência.**

---

# 8. Ordering

RabbitMQ consegue preservar a ordem de uma queue em condições normais de entrega.

Mas ordering fica mais complexo com:

- múltiplos Consumers;
- NACK;
- requeue;
- redelivery;
- retries;
- processamento assíncrono.

Se ordenação estrita for essencial, uma opção é usar **Single Active Consumer**.

Com ele, apenas um Consumer por vez recebe mensagens da queue; se ele falhar, outro assume. 

Trade-off:

```text
mais ordering
     ↓
menos paralelismo
```

---

# 9. ACK

Com acknowledgement manual:

```text
RabbitMQ
   ↓
Consumer
   ↓
processa
   ↓
ACK
```

ACK significa:

> **processei essa mensagem com sucesso.**

Depois disso, RabbitMQ pode removê-la da queue.

Para sistemas críticos, é normalmente importante confirmar somente **depois** do processamento relevante.

---

# 10. ACK cedo demais

Imagine:

```text
recebe mensagem
     ↓
ACK
     ↓
processa pagamento
     ↓
CRASH
```

RabbitMQ acredita que a mensagem foi concluída.

Ela já foi confirmada.

Resultado:

> potencial perda do processamento.

Por isso o momento do ACK é uma decisão importante.

---

# 11. Redelivery

Agora:

```text
recebe mensagem
     ↓
processa
     ↓
CRASH
     ↓
não enviou ACK
```

RabbitMQ pode disponibilizar novamente aquela entrega.

Outro Consumer pode receber:

```text
mesma mensagem
```

Isso é redelivery.

Consequência:

> **duplicidade é possível.**

Por isso Consumers devem ser idempotentes.

---

# 12. Idempotência

Imagine:

```text
messageId = ABC123
```

Primeiro processamento:

```text
ABC123
  ↓
cobra pagamento
  ↓
registra ABC123
```

Redelivery:

```text
ABC123
  ↓
já processado
  ↓
não cobra novamente
```

Uma estratégia comum é utilizar:

```text
messageId
+
UNIQUE constraint
```

ou uma tabela de Inbox/deduplicação.

---

# 13. NACK e Reject

Quando Consumer não consegue processar:

```text
NACK
```

ou:

```text
Reject
```

podem informar ao RabbitMQ que a entrega falhou.

Uma decisão importante é:

```text
requeue = true
```

ou:

```text
requeue = false
```

---

# 14. Cuidado com requeue infinito

Imagine uma mensagem inválida:

```text
Mensagem X
   ↓
falha
   ↓
NACK + requeue
   ↓
Mensagem X
   ↓
falha
   ↓
NACK + requeue
```

Isso pode virar um loop infinito.

Além de desperdiçar recursos, pode prejudicar o processamento das outras mensagens.

Por isso poison messages precisam de tratamento explícito.

---

# 15. Prefetch

Prefetch é um dos conceitos mais importantes do RabbitMQ.

Imagine:

```text
prefetch = 10
```

RabbitMQ permite que aquele Consumer tenha até aproximadamente dez entregas sem ACK ao mesmo tempo, conforme a configuração aplicada.

Isso cria um limite de mensagens em voo. 

---

# 16. Prefetch e Backpressure

Imagine:

```text
RabbitMQ
100.000 mensagens
```

E Consumer consegue processar apenas:

```text
50 por segundo
```

Sem controle, podemos sobrecarregar:

- memória;
- threads;
- pool de conexões;
- banco;
- APIs externas.

Prefetch ajuda a limitar:

```text
quantas mensagens
      ↓
podem ficar em processamento
```

Trade-off:

```text
prefetch baixo
→ mais controle
→ possivelmente menos throughput

prefetch alto
→ mais throughput
→ mais mensagens em voo
→ maior memória e pressão
```

A documentação do RabbitMQ ressalta que aumentar prefetch pode melhorar throughput, mas também aumenta mensagens entregues e ainda não processadas. 

---

# 17. RabbitMQ não tem Consumer Lag igual ao Kafka

Essa diferença é importante.

Kafka pensa naturalmente em:

```text
log end offset
-
consumer offset
=
consumer lag
```

RabbitMQ trabalha principalmente com:

```text
messages ready
messages unacknowledged
consumer capacity
delivery rate
ack rate
```

Então, para saber se Consumer está atrasado, normalmente monitoramos:

**crescimento da queue**.

Se:

```text
Producer = 10.000 mensagens/minuto
Consumer = 5.000 mensagens/minuto
```

a quantidade de mensagens prontas cresce continuamente.

Esse backlog é um sinal de incapacidade de processamento.

RabbitMQ também expõe uma métrica de **consumer capacity**, útil para avaliar se adicionar Consumers ou ajustar processamento/prefetch pode ajudar. 

---

# 18. At-most-once

Conceitualmente:

```text
recebe
 ↓
confirma cedo
 ↓
processa
```

Se ocorrer falha depois da confirmação:

```text
mensagem pode ser perdida
```

Temos:

**zero ou uma execução.**

É adequado somente quando perda é aceitável.

---

# 19. At-least-once

Modelo típico:

```text
recebe
 ↓
processa
 ↓
ACK
```

Se o Consumer cair entre:

```text
processamento
```

e:

```text
ACK
```

a mensagem pode ser entregue novamente.

Portanto:

**at-least-once exige idempotência.**

Para queues críticas, RabbitMQ recomenda acknowledgements manuais; Quorum Queues também são projetadas para cenários onde segurança de dados é prioridade. 

---

# 20. Exactly-once

RabbitMQ não transforma automaticamente a operação:

```text
receber mensagem
+
UPDATE PostgreSQL
+
chamar Stripe
+
enviar e-mail
```

em exatamente uma execução.

Imagine:

```text
cobra Stripe
   ↓
Stripe sucesso
   ↓
Consumer crash
   ↓
sem ACK
```

A mensagem volta.

RabbitMQ não sabe automaticamente que Stripe já cobrou.

Precisamos pensar em:

- idempotency key;
- deduplicação;
- Inbox;
- Outbox;
- UNIQUE constraint;
- estado de negócio.

Portanto:

> **exactly-once end-to-end é um problema da arquitetura, não apenas do broker.**

---

# 21. Publisher Confirm

ACK do Consumer e Publisher Confirm são coisas diferentes.

### Consumer ACK

```text
Consumer
   ↓
RabbitMQ

"processei"
```

### Publisher Confirm

```text
RabbitMQ
   ↓
Producer

"recebi sua publicação"
```

Publisher Confirms são importantes quando o Producer precisa saber se uma publicação foi aceita com as garantias relevantes do broker.

Em Quorum Queues, confirms são emitidos após a mensagem ser replicada para um quorum e considerada segura no contexto da queue. 

Memorize:

**Publisher Confirm protege o lado da publicação.**

**Consumer ACK protege o lado do consumo.**

---

# 22. Poison Message

Poison Message é uma mensagem que nunca consegue ser processada corretamente.

Por exemplo:

```text
JSON inválido
```

ou:

```text
referência inexistente
```

ou:

```text
regra impossível de executar
```

Fazer retry infinitamente normalmente é um erro.

---

# 23. Retry

Retry funciona bem para erros transitórios.

Exemplos:

```text
HTTP 503
timeout
database temporarily unavailable
```

Uma estratégia melhor que retry agressivo é:

```text
tentativa
   ↓
backoff
   ↓
nova tentativa
```

Mas precisamos limitar quantidade de retries.

---

# 24. Retry Queue

RabbitMQ permite arquiteturas usando:

```text
Main Queue
   ↓
falha
   ↓
Retry Queue
   ↓
TTL
   ↓
Dead Letter Exchange
   ↓
Main Queue
```

A mensagem fica temporariamente na Retry Queue.

Quando o TTL expira, ela pode ser dead-lettered para outro exchange e retornar ao fluxo.

Isso cria:

**retry com atraso.**

---

# 25. DLX

DLX significa:

**Dead Letter Exchange.**

Uma mensagem pode ser dead-lettered, por exemplo, quando é rejeitada sem requeue, expira pelo TTL, ultrapassa limite da queue ou, em quorum queues, excede delivery limit. 

Fluxo:

```text
Main Queue
    ↓
NACK / reject
requeue = false
    ↓
DLX
    ↓
DLQ
```

O DLX é um exchange normal usado para rotear essas mensagens.

---

# 26. DLQ

DLQ é a queue final onde mensagens problemáticas podem ficar para análise.

```text
Main Queue
   ↓
Retry
   ↓
Retry
   ↓
DLX
   ↓
DLQ
```

Mas:

> colocar algo na DLQ não resolve o problema.

É necessário:

- monitorar;
- criar alertas;
- identificar causa;
- corrigir;
- reprocessar quando apropriado.

---

# 27. TTL

TTL significa Time To Live.

Podemos utilizar para mensagens que não devem viver indefinidamente.

Também é muito usado para implementar:

**delayed retry**.

Exemplo:

```text
Retry Queue
TTL = 30 segundos
       ↓
expira
       ↓
DLX
       ↓
Main Queue
```

---

# 28. Quorum Queue

Quorum Queue é uma opção importante para cargas críticas.

Ela mantém réplicas da queue utilizando consenso.

O objetivo é maior:

**segurança de dados e disponibilidade.**

Mas isso tem custo.

A própria documentação alerta para maior latência inerente ao consenso e recomenda Quorum Queues quando segurança de dados realmente importa. 

---

# 29. RabbitMQ x Kafka — modelo mental

Uma forma importante de diferenciar:

### Kafka

```text
Topic
  ↓
Partition
  ↓
Offset
  ↓
Consumer Group
```

Fortes conceitos:

**log, retenção, replay, particionamento e streaming.**

### RabbitMQ

```text
Exchange
   ↓
Routing
   ↓
Queue
   ↓
ACK
   ↓
Consumer
```

Fortes conceitos:

**routing, filas, competing consumers, acknowledgements e entrega de trabalho.**

---

# 30. Quando RabbitMQ costuma fazer sentido

RabbitMQ é especialmente forte quando precisamos de:

- filas de trabalho;
- roteamento flexível;
- processamento assíncrono;
- competing consumers;
- request/reply assíncrono;
- retries;
- prioridades;
- entrega orientada a tarefas.

Exemplo:

```text
GenerateInvoice
      ↓
RabbitMQ
      ↓
invoice.queue
      ↓
Workers
```

Um worker executa o trabalho.

---

# Resposta objetiva para entrevista

> RabbitMQ é um message broker orientado principalmente a filas e roteamento. O Producer publica normalmente em um Exchange, e através de Bindings e Routing Keys o Exchange direciona a mensagem para uma ou mais Queues. Consumers processam essas mensagens e podem escalar através do padrão de Competing Consumers.
>
> Os principais tipos de Exchange que considero são Direct para routing exato, Topic para padrões de routing key e Fanout para broadcast.
>
> No consumo, presto bastante atenção em acknowledgements e prefetch. Com ACK manual, só confirmo a mensagem depois do processamento relevante. Se o Consumer cair antes do ACK, a mensagem pode ser entregue novamente, então normalmente projeto Consumers idempotentes. Prefetch é importante para controlar quantas mensagens ficam em voo e evitar sobrecarregar banco, APIs ou o próprio Consumer. 
>
> Para ordering, sei que uma queue possui ordem de entrega, mas múltiplos Consumers, requeues e redeliveries podem alterar a ordem efetiva de processamento. Se ordenação estrita for necessária, posso avaliar Single Active Consumer, sabendo que isso reduz paralelismo. 
>
> Para mensagens que falham, diferencio erros transitórios de poison messages. Posso usar retry com backoff, Retry Queues, TTL, Dead Letter Exchange e DLQ, evitando requeue infinito. 
>
> Também diferencio Consumer ACK de Publisher Confirm: ACK confirma processamento do lado do Consumer, enquanto Publisher Confirm dá ao Producer evidência de que a publicação foi aceita pelo broker com as garantias configuradas.
>
> Em semântica de entrega, considero at-least-once mais comum em fluxos críticos, junto com idempotência. RabbitMQ sozinho não garante exatamente uma execução de efeitos externos como banco, pagamento ou e-mail; para isso ainda preciso de mecanismos como idempotency key, Inbox, Outbox ou deduplicação.
>
> Então, para mim, dominar RabbitMQ significa principalmente entender **Exchange, Queue, Routing, ACK/NACK, Prefetch, Competing Consumers, Ordering, Idempotência, Retry e DLQ**, e não apenas saber publicar e consumir mensagens.
