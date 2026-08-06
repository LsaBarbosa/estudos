# Kafka — Etapa 3: Garantias de entrega

Lucas, as garantias do Kafka respondem a duas perguntas principais:

1. **O evento pode ser perdido?**
2. **O evento pode ser processado mais de uma vez?**

Esses problemas podem ocorrer tanto no **producer**, durante a publicação, quanto no **consumer**, durante o processamento e o commit do offset.

---

## 1. `acks`

`acks` define quantos brokers precisam confirmar a gravação antes de o producer considerar o envio bem-sucedido.

### `acks=0`

O producer envia o evento e não espera confirmação.

```text
Producer → Broker
Producer considera enviado imediatamente
```

**Risco:** o evento pode ser perdido sem que o producer saiba, pois ele não recebe confirmação nem consegue identificar corretamente falhas para realizar retries. 

### `acks=1`

O líder da partição confirma após gravar localmente, sem esperar a replicação completa.

```text
Producer → Líder grava → ACK
                     ↓
              Replicação posterior
```

**Risco:** se o líder falhar depois do ACK, mas antes da replicação, o evento poderá ser perdido. 

### `acks=all`

O líder espera todas as réplicas sincronizadas, chamadas de **ISR**, confirmarem.

É a configuração mais segura. Sua eficácia depende também de configurações como:

```properties
min.insync.replicas=2
```

Isso determina o número mínimo de réplicas sincronizadas necessárias para aceitar a gravação.

**Em entrevista:**

> `acks` controla o nível de durabilidade da publicação. `acks=all` oferece a garantia mais forte, enquanto `acks=0` prioriza desempenho, mas permite perdas silenciosas.

---

## 2. Retries

**Retries** são novas tentativas de envio realizadas pelo producer quando ocorre uma falha temporária.

Exemplos:

* indisponibilidade momentânea do broker;
* timeout;
* mudança do líder da partição;
* falha temporária de rede.

```text
Producer envia evento
        |
        v
Broker grava o evento
        |
        X resposta não chega
        |
Producer tenta novamente
```

Nesse cenário, o primeiro envio pode ter sido gravado, mas o producer não recebeu a resposta. Ao reenviar, o mesmo evento pode ser gravado novamente.

```text
Partition 0

Offset 20 → PedidoCriado
Offset 21 → PedidoCriado
```

Portanto:

* sem retries, aumenta o risco de perda;
* com retries sem idempotência, aumenta o risco de duplicação;
* com retries e idempotência, o Kafka consegue detectar o reenvio.

Os retries continuam até o envio ser bem-sucedido, ocorrer um erro definitivo ou o limite de entrega ser atingido. 
**Em entrevista:**

> Retries reduzem perdas causadas por falhas temporárias, mas podem gerar duplicações quando o broker gravou a mensagem e apenas a confirmação foi perdida.

---

## 3. Idempotência do producer

Uma operação é **idempotente** quando executá-la mais de uma vez produz o mesmo resultado de executá-la uma única vez.

No Kafka:

```properties
enable.idempotence=true
```

O producer recebe uma identificação e associa números sequenciais aos lotes enviados. O broker utiliza essas informações para reconhecer retransmissões e impedir que o mesmo lote seja gravado novamente.

```text
Primeira tentativa:
Producer → sequência 10 → Broker grava

Retry:
Producer → sequência 10 → Broker identifica duplicação
```

A idempotência do producer exige configurações compatíveis, incluindo:

```properties
acks=all
retries > 0
max.in.flight.requests.per.connection <= 5
```

Nos clientes Kafka atuais, a idempotência fica habilitada por padrão quando não existem configurações conflitantes. ([Kafka][1])

### Limite importante

A idempotência do producer evita duplicações causadas pelos **retries internos do producer**.

Ela não evita que a própria aplicação publique logicamente o mesmo evento duas vezes:

```java
kafkaTemplate.send("pagamentos", evento);
kafkaTemplate.send("pagamentos", evento);
```

Nesse caso, são duas chamadas diferentes e ambas podem ser aceitas.

**Em entrevista:**

> O producer idempotente elimina duplicações causadas por retransmissões, mas não corrige publicações duplicadas realizadas pela lógica da aplicação.

---

## 4. At-most-once

**At-most-once** significa:

```text
Processado no máximo uma vez
```

O evento pode ser processado uma vez ou nenhuma vez, mas não deve ser repetido.

Isso normalmente acontece quando o consumer confirma o offset **antes** de processar o evento:

```text
Consumer lê
    ↓
Commita offset
    ↓
Processa evento
```

Problema:

```text
Consumer lê
    ↓
Commita offset
    ↓
Consumer falha
    ↓
Evento não foi processado
```

Quando o consumer reiniciar, continuará depois do offset confirmado. O evento será ignorado, causando perda de processamento. ([Kafka][2])

### Quando pode ser aceitável

* métricas não críticas;
* telemetria;
* logs que toleram pequenas perdas;
* eventos em que duplicação seria mais prejudicial que perda.

**Em entrevista:**

> No at-most-once, o offset é confirmado antes do processamento. Isso evita reprocessamento, mas pode causar perda caso o consumer falhe após o commit.

---

## 5. At-least-once

**At-least-once** significa:

```text
Processado pelo menos uma vez
```

O consumer processa o evento e somente depois confirma o offset:

```text
Consumer lê
    ↓
Processa evento
    ↓
Commita offset
```

Problema:

```text
Consumer lê
    ↓
Atualiza o banco
    ↓
Consumer falha antes do commit
    ↓
Evento é lido novamente
```

O evento não é perdido, mas pode ser processado novamente.

Exemplo:

```text
Primeiro processamento → cobrança de R$ 100
Reprocessamento        → nova cobrança de R$ 100
```

Esse é o modelo mais comum porque, em aplicações críticas, geralmente é preferível receber uma duplicação tratável a perder definitivamente um evento. O Kafka documenta esse comportamento como consequência de processar antes de salvar a posição. ([Kafka][3])

**Em entrevista:**

> At-least-once evita perda porque o offset só é confirmado após o processamento, mas uma falha entre o processamento e o commit pode causar duplicação.

---

## 6. Exactly-once

**Exactly-once** significa que o resultado do processamento deve aparecer uma única vez, mesmo que ocorram falhas ou retries.

```text
Evento consumido
      +
Resultado produzido
      +
Offset atualizado
      =
Operação atômica
```

No Kafka, essa garantia é mais precisa quando chamada de:

> **Exactly-once processing semantics**

Ela funciona principalmente em fluxos Kafka para Kafka:

```text
Topic de entrada
      ↓
Consumer processa
      ↓
Topic de saída
```

O Kafka pode registrar na mesma transação:

* os novos eventos produzidos;
* os offsets consumidos.

Se a transação falhar, nenhuma dessas alterações é considerada confirmada. Se for concluída, todas são confirmadas juntas. ([Kafka][3])

### Limitação importante

Exactly-once do Kafka não torna automaticamente idempotentes efeitos externos como:

* envio de e-mail;
* chamada REST;
* cobrança em gateway de pagamento;
* atualização em banco externo.

Para esses casos, é necessário combinar Kafka com:

* idempotent consumer;
* deduplicação;
* chave única no banco;
* transactional outbox;
* inbox pattern;
* suporte transacional do sistema externo.

**Em entrevista:**

> Exactly-once no Kafka é obtido ao gravar resultados e offsets atomicamente em uma transação. A garantia é direta em fluxos Kafka para Kafka; efeitos externos exigem cooperação do sistema de destino.

---

## 7. Transações

Transações permitem que um producer publique eventos em diferentes topics ou partições de forma atômica.

```text
beginTransaction()

Produz evento A
Produz evento B
Atualiza offsets

commitTransaction()
```

Os resultados possíveis são:

```text
Todos confirmados
ou
Todos abortados
```

Exemplo:

```text
Topic: pagamentos-aprovados
Topic: pedidos-confirmados
```

Sem transação:

```text
Pagamento publicado
Pedido não publicado
```

Com transação:

```text
Pagamento + pedido são confirmados juntos
```

Um producer transacional utiliza uma identificação:

```properties
transactional.id=order-processing-service
```

Essa identificação também permite que uma nova instância impeça uma instância antiga ou travada de continuar publicando dentro da mesma identidade transacional. Configurar `transactional.id` também implica o uso da idempotência do producer. ([Kafka][1])

**Em entrevista:**

> Transações garantem atomicidade entre múltiplas publicações e, opcionalmente, o commit dos offsets consumidos.

---

## 8. `read_committed`

Um consumer pode definir como tratar eventos produzidos transacionalmente.

```properties
isolation.level=read_committed
```

Com `read_committed`, o consumer recebe:

* eventos não transacionais;
* eventos pertencentes a transações concluídas com sucesso.

Ele não recebe eventos de transações abortadas.

```text
Transação A → COMMIT → visível
Transação B → ABORT  → ignorada
Transação C → aberta → aguardada
```

Sem essa configuração, o padrão `read_uncommitted` pode retornar eventos de transações que posteriormente serão abortadas. ([Kafka][4])

### Relação com exactly-once

Para um fluxo exactly-once:

```properties
enable.auto.commit=false
isolation.level=read_committed
```

O offset deve ser incluído na mesma transação dos eventos produzidos. ([Kafka][3])

**Em entrevista:**

> `read_committed` impede que consumers processem eventos pertencentes a transações abortadas ou ainda não concluídas.

---

## 9. Idempotent consumer

Um **idempotent consumer** pode receber o mesmo evento várias vezes, mas aplica seu efeito apenas uma vez.

Considere:

```json
{
  "eventId": "evt-9845",
  "type": "PagamentoAprovado",
  "pedidoId": "PED-100"
}
```

O consumer verifica se o `eventId` já foi processado:

```text
Evento recebido
      ↓
eventId já processado?
      ├── Sim → ignora
      └── Não → processa e registra eventId
```

Exemplo com banco:

```sql
CREATE TABLE processed_events (
    event_id VARCHAR(100) PRIMARY KEY,
    processed_at TIMESTAMP NOT NULL
);
```

O registro do evento e a alteração de negócio devem ocorrer na mesma transação local:

```text
BEGIN

INSERT processed_events
UPDATE pedido

COMMIT
```

Caso o evento seja recebido novamente, a chave primária impedirá um novo processamento.

Outra possibilidade é tornar a própria operação naturalmente idempotente:

```sql
UPDATE pedido
SET status = 'PAGO'
WHERE id = 'PED-100';
```

Repetir essa atualização produz o mesmo estado final. A própria documentação do Kafka cita o uso de chaves primárias como forma de tornar atualizações repetidas idempotentes. ([Kafka][2])

**Em entrevista:**

> Idempotent consumer garante que mensagens repetidas não produzam efeitos repetidos, normalmente usando um identificador único e persistente para cada evento.

---

## 10. Deduplicação

**Deduplicação** é a identificação e remoção lógica de eventos repetidos.

Ela pode ocorrer em dois níveis.

### No producer

O Kafka utiliza:

* producer ID;
* número de sequência;
* idempotência.

Isso trata duplicações causadas por retries.

### No consumer

A aplicação utiliza um identificador de negócio:

```text
eventId
operationId
paymentId
orderId + eventType
```

Exemplo:

```text
eventId = evt-100

Primeira recepção → processa
Segunda recepção  → identifica duplicação e ignora
```

### Onde armazenar os identificadores

* tabela relacional;
* Redis;
* armazenamento local transacional;
* inbox table.

O armazenamento precisa durar pelo menos o período no qual o evento pode ser reprocessado. Também é necessário evitar uma operação não atômica como:

```text
1. Verifica se existe
2. Processa
3. Registra como processado
```

Dois consumers concorrentes podem passar pela verificação ao mesmo tempo. Uma restrição única ou operação atômica deve decidir qual processamento é válido.

**Em entrevista:**

> Deduplicação utiliza um identificador único para reconhecer eventos repetidos. A verificação e o registro precisam ser atômicos para evitar condições de corrida.

---

# Onde uma perda pode ocorrer

## No producer

```text
Producer → Broker
```

Pode haver perda quando:

* `acks=0` e o broker não recebe o evento;
* `acks=1` e o líder falha antes da replicação;
* os retries terminam e a aplicação ignora o erro;
* o evento falha antes mesmo de ser enviado ao Kafka;
* a aplicação atualiza o banco, mas falha antes de publicar o evento.

O último caso é conhecido como problema de **dual write**:

```text
Banco atualizado
      ↓
Aplicação falha
      ↓
Evento não publicado
```

---

## No consumer

Pode haver perda lógica quando o offset é confirmado antes do processamento:

```text
Commit do offset
      ↓
Falha
      ↓
Evento nunca processado
```

Esse é o principal risco do modelo **at-most-once**. ([Kafka][2])

---

# Onde uma duplicação pode ocorrer

## No producer

```text
Broker grava
      ↓
ACK não chega
      ↓
Producer reenvia
```

Sem idempotência, o mesmo evento pode ser armazenado duas vezes.

---

## No consumer

```text
Consumer processa
      ↓
Falha antes do commit
      ↓
Evento é consumido novamente
```

Esse é o principal risco do modelo **at-least-once**. ([Kafka][2])

---

## Na própria aplicação

```text
Aplicação chama send()
Aplicação chama send() novamente
```

A idempotência nativa do producer não identifica necessariamente que essas duas publicações representam o mesmo evento de negócio.

Nesse caso, deve existir um `eventId`, `operationId` ou outra chave de deduplicação.

---

# Comparação das garantias

| Garantia            | Ordem das operações                            |              Pode perder? |                            Pode duplicar? |
| ------------------- | ---------------------------------------------- | ------------------------: | ----------------------------------------: |
| At-most-once        | Commit → processamento                         |                       Sim |                           Normalmente não |
| At-least-once       | Processamento → commit                         | Não, em condições normais |                                       Sim |
| Exactly-once        | Resultado + offset na mesma transação          |                       Não |                Não no escopo transacional |
| Idempotent consumer | Processa somente eventos ainda não registrados |                       Não | Recebe duplicado, mas não repete o efeito |

---

# Fluxo recomendado

```text
Producer
   |
   | acks=all
   | retries habilitados
   | enable.idempotence=true
   v
Kafka
   |
   v
Consumer
   |
   | processa evento
   | operação idempotente
   | deduplicação por eventId
   v
Commit do offset
```

Para Kafka para Kafka:

```text
Consumir
   +
Produzir resultado
   +
Confirmar offset
   =
Uma única transação
```

Consumer de saída:

```properties
isolation.level=read_committed
```

---

# Resposta pronta para entrevista

> Perdas e duplicações podem ocorrer tanto na publicação quanto no consumo. No producer, configurações fracas de `acks` podem causar perda, enquanto retries sem idempotência podem gerar duplicações quando o evento foi gravado, mas o ACK não chegou. No consumer, confirmar o offset antes do processamento oferece at-most-once e pode causar perda. Processar antes de confirmar oferece at-least-once e pode gerar duplicação se houver falha antes do commit. Exactly-once em fluxos Kafka para Kafka utiliza transações para confirmar atomicamente os resultados e os offsets, enquanto efeitos em bancos, APIs ou gateways externos exigem consumidores idempotentes e mecanismos de deduplicação.

[1]: https://kafka.apache.org/43/configuration/producer-configs/ "Producer Configs | Apache Kafka"
[2]: https://kafka.apache.org/41/design/design/ "Design | Apache Kafka"
[3]: https://kafka.apache.org/43/design/design/ "Design | Apache Kafka"
[4]: https://kafka.apache.org/41/configuration/consumer-configs/ "Consumer Configs | Apache Kafka"
