

# 1. Kafka é um log distribuído de eventos organizado em tópicos

> Kafka é um log distribuído de eventos organizado em tópicos.

# O que é um tópico?

##### Um tópico é apenas uma categoria de eventos.

#### Exemplo em Spring

Producer

```java
kafkaTemplate.send("pedidos", pedido);
```

Consumer

```java
@KafkaListener(topics = "pedidos")
public void consumir(Pedido pedido){
}
```

---

# Cada tópico é dividido em partições
- Cada partição possui seu próprio log.
- Permitem paralelismo, aumentando o Throughput
- Partition key determina em qual partição a mensagem será gravada (gera ordenação).
- Cada partição possui um broker líder.

#### O offset identifica a posição do evento **dentro da partição**.
  - Cada partição possui sua própria sequência.
  - A ordenação é garantida apenas dentro da mesma partição.
  - Os consumers controlam o offset (commitam mensagens já processadas)
  - No Spring Kafka, isso pode ser automático (`enable-auto-commit`) ou manual por meio de `Acknowledgment`, permitindo maior controle sobre quando um evento é considerado processado.


Visualmente:
#### Partição
```
+-----------+ +-----------+
|Partition0 | |Partition0 |
+-----------+ +-----------+
```
#### Chave 
```java
kafkaTemplate.send("pedidos", pedidoId, pedido);
```
pedidoId é a chave (key).

---

# 4. O broker líder grava o evento

> O broker líder dessa partição grava o evento sequencialmente no log.

- Cada partição possui um broker líder.

- Todo Producer conversa apenas com o líder. Nunca diretamente com as réplicas.

- O líder replica para outros brokers, se falhar promove um LEADER

#### ISR (In-Sync Replicas)

- Dependendo da configuração (`acks` e `min.insync.replicas`), o producer pode aguardar a confirmação de todas as réplicas sincronizadas antes de considerar a gravação bem-sucedida.

---
 
# Consumer Group
### Regra importante
#### Dentro do mesmo grupo:
- **Uma partição só pode ser processada por um consumer ao mesmo tempo.**

- Isso evita processamento duplicado dentro do mesmo grupo.
#### Grupos diferentes
- Cada grupo mantém seus próprios offsets.
- Consumidores de grupos distintos podem consumir da mesma partição.
---
 
## Mensagem continua armazenada após consumida
 

Isso permite:

* replay
* auditoria
* reprocessamento
* recuperação após falhas
* novos consumidores lendo eventos antigos

> O Kafka remove mensagens apenas quando a política de retenção é atingida.

---

# Fluxo completo do Kafka

```text
                Producer (Spring Boot)
                       │
        kafkaTemplate.send("pedidos", key, pedido)
                       │
                       ▼
             Escolha da partição (hash da key)
                       │
                       ▼
               Broker Líder da Partição
                       │
             Grava sequencialmente no log
                       │
                       ▼
          Replica para os Brokers Followers
                       │
                       ▼
          Evento recebe um Offset na partição
                       │
                       ▼
         Consumer Group recebe a partição
                       │
                       ▼
      @KafkaListener processa o evento em Java
                       │
                       ▼
        Consumer confirma (commit) o Offset
                       │
                       ▼
   Evento permanece armazenado até a retenção expirar
```

# Relação com Spring Boot e Java

No desenvolvimento com Spring Boot:

* **Producer (`KafkaTemplate`)**: publica eventos em tópicos.
* **Broker Kafka**: recebe, persiste e replica os eventos.
* **Topic**: organiza eventos por domínio de negócio.
* **Partition**: permite paralelismo e escalabilidade.
* **Offset**: identifica a posição de cada evento na partição.
* **Consumer (`@KafkaListener`)**: processa os eventos.
* **Consumer Group**: distribui partições entre consumidores para escalar o processamento.
* **Commit de Offset**: registra até onde o consumer processou os eventos.
* **Retention**: mantém os eventos disponíveis para replay e auditoria mesmo após o consumo.

Esses conceitos formam a base para entender recursos mais avançados do Kafka, como **garantias de entrega (at-most-once, at-least-once e exactly-once), idempotência do producer, transações, rebalancing, compactação de logs, particionamento por chave, Dead Letter Topics (DLT) e integração com Spring Kafka para tratamento de falhas e retries**.
