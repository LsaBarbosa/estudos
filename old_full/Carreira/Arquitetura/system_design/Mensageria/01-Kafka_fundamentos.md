# Kafka — Etapa 1: Fundamentos

Lucas, pense no Kafka como um **log distribuído de eventos**: aplicações publicam acontecimentos, o Kafka os armazena de forma ordenada em partições e outras aplicações os consomem no próprio ritmo.

---

## 1. Event streaming

**Event streaming** é a captura, o armazenamento e o processamento contínuo de eventos conforme eles acontecem.

Um evento representa algo que ocorreu no domínio:

```text
PedidoCriado
```

Normalmente, um evento contém:

```json
{
  "pedidoId": "PED-123",
  "clienteId": "CLI-10",
  "valor": 250.00,
  "status": "CRIADO"
}
```

No Kafka, o evento também é chamado de **record** ou **message**.

**Em entrevista:**

> Event streaming é uma arquitetura em que eventos são publicados continuamente e processados de forma assíncrona por diferentes sistemas.

---

## 2. Broker

Um **broker** é uma instância, ou servidor, do Kafka.

Ele é responsável por:

- receber eventos dos producers;
- armazenar partições;
- entregar eventos aos consumers;
- replicar dados;
- responder às requisições dos clientes.

Um ambiente Kafka normalmente possui vários brokers:

```text
Kafka Cluster
├── Broker 1
├── Broker 2
└── Broker 3
```

As partições dos tópicos são distribuídas entre esses brokers. Isso permite escalabilidade e tolerância a falhas. O conjunto de brokers forma o **cluster Kafka**. 

**Em entrevista:**

> Broker é o servidor Kafka que armazena partições e atende requisições de produção e consumo.

---

## 3. Topic

Um **topic** é uma categoria lógica na qual eventos relacionados são publicados.

Exemplos:

```text
pedidos-criados
pagamentos-aprovados
```

O topic funciona como um agrupamento de eventos do mesmo contexto.

```text
Producer
    |
    v
Topic: pedidos-criados
```

Um topic pode ter:

- vários producers;
- vários consumers;
- vários consumer groups;
- várias partições.

Os eventos não ficam fisicamente armazenados apenas no “topic”. Eles ficam armazenados nas **partições que pertencem ao topic**. 

**Em entrevista:**

> Topic é a categoria lógica usada para organizar eventos; fisicamente, seus dados ficam distribuídos em partições.

---

## 4. Partition

Uma **partition** é uma parte física e ordenada de um topic.

Exemplo:

```text
Topic: pedidos-criados

Partition 0: E1 → E4 → E7
Partition 1: E2 → E5 → E8

```

As partições possuem dois objetivos principais:

1. **Escalabilidade:** os dados podem ser distribuídos entre brokers.
2. **Paralelismo:** diferentes consumers podem processar partições simultaneamente.

Dentro de uma partição, os eventos formam um log ordenado e imutável: novos eventos são adicionados ao final.

A ordenação é garantida **dentro da partição**, não entre todas as partições do topic.
**Em entrevista:**

> Partition é a unidade de armazenamento, ordenação e paralelismo do Kafka.

---

## 5. Offset

O **offset** é a posição numérica de um evento dentro de uma partição.

```text
Partition 0

Offset 0 → Pedido A
Offset 1 → Pedido B
Offset 2 → Pedido C
Offset 3 → Pedido D
```

O offset:

- é sequencial;
- pertence a uma partição;
- identifica a posição do evento;
- ajuda o consumer a saber de onde continuar.

O offset `10` da partition `0` não é o mesmo que o offset `10` da partition `1`.

O consumer group pode salvar, ou **commitar**, seu offset. Caso o consumer seja reiniciado, ele retoma o processamento a partir da posição registrada. Também é possível voltar para offsets antigos e reprocessar eventos. 

**Atenção para entrevista:**

O offset não é um identificador global do evento. Sua identificação é contextual:

```text
topic + partition + offset
```

---

## 6. Producer

O **producer** é a aplicação que publica eventos em um topic.

Exemplo em um sistema de pedidos:

```text
Order Service
     |
     | PedidoCriado
     v
Topic: pedidos-criados
```

O producer:

1. cria o evento;
2. serializa seus dados;
3. escolhe ou determina a partição;
4. envia o evento ao broker responsável;
5. recebe uma confirmação conforme a configuração utilizada.

A escolha da partição pode considerar:

- uma partição informada explicitamente;
- uma chave;
- a estratégia do partitioner.

O producer é responsável por publicar os registros e participar da decisão de qual partição receberá cada evento.

**Em entrevista:**

> Producer é o cliente Kafka responsável por serializar e publicar eventos nos topics.

---

## 7. Consumer

O **consumer** é a aplicação que lê eventos de um ou mais topics.

```text
Topic: pedidos-criados
          |
          v
     Estoque Service
```

O consumer normalmente executa um ciclo:

```text
poll → recebe eventos → processa → commit do offset
```

Ele controla sua posição de leitura. Por isso, consumir um evento não significa necessariamente removê-lo do Kafka.

Consumidores diferentes podem ler os mesmos eventos, desde que pertençam a grupos diferentes. 

**Em entrevista:**

> Consumer é o cliente que busca eventos das partições, processa os registros e controla sua posição por meio dos offsets.

---

## 8. Consumer group

Um **consumer group** é um conjunto de consumers que trabalham juntos para processar um topic.

Dentro do mesmo grupo:

- cada partição é atribuída a apenas um consumer por vez;
- os eventos são divididos entre os consumers;
- não há processamento paralelo da mesma partição por dois membros do grupo.

Exemplo:

```text
Topic com 4 partições

Consumer Group: estoque-service

Consumer A → Partition 0 e 1
Consumer B → Partition 2 e 3
```

Se houver mais consumers do que partições:

```text
4 partições
5 consumers
```

Um consumer ficará sem partição e, portanto, ocioso.

Grupos diferentes recebem os mesmos eventos independentemente:

```text
pedidos-criados
├── Group estoque-service
├── Group faturamento-service
└── Group notificacao-service
```

Cada grupo mantém seus próprios offsets. 


**Em entrevista:**

> Consumer group fornece escalabilidade horizontal: as partições são distribuídas entre os membros do grupo, e cada grupo consome o topic de maneira independente.

---

## 9. Chave e ordenação

Um evento pode possuir uma **key**.

```text
Key: pedidoId
Value: dados do pedido
```

Exemplo:

```text
Key = PED-100
```

Eventos com a mesma chave são normalmente enviados para a mesma partição.

```text
PED-100 → Partition 1
PED-200 → Partition 2
PED-100 → Partition 1
```

Isso é importante quando a ordem dos eventos de uma entidade precisa ser preservada:

```text
PedidoCriado
PagamentoAprovado
PedidoEnviado
PedidoEntregue
```

Usando `pedidoId` como chave, todos os eventos daquele pedido tendem a permanecer na mesma partição e, portanto, mantêm a ordem de gravação.

Kafka não garante ordenação global entre partições. A garantia é por **topic-partition**.  

**Em entrevista:**

> A chave influencia o particionamento. Eventos com a mesma chave vão para a mesma partição, permitindo preservar a ordem por entidade.

---

## 10. Retenção

**Retenção** define por quanto tempo ou até qual tamanho os eventos permanecem armazenados.

Diferentemente de uma fila tradicional, Kafka normalmente não remove o evento imediatamente após o consumo.

```text
Producer publica
Consumer processa
Evento continua no topic
```

Isso permite:

- reprocessamento;
- auditoria;
- recuperação após falhas;
- inclusão de novos consumidores;
- reconstrução de estados.

As políticas principais são:

### `delete`

Remove segmentos antigos quando atingem determinado tempo ou tamanho.

```text
Manter eventos por 7 dias
```

### `compact`

Mantém principalmente o valor mais recente para cada chave.

```text
cliente-10 → nome atualizado
cliente-20 → endereço atualizado
```

Também é possível combinar `delete` e `compact`. 


**Em entrevista:**

> Retenção determina por quanto tempo ou tamanho os registros ficam disponíveis, independentemente de já terem sido consumidos.

---

# Fluxo completo de um evento

Considere o topic `pedidos` com três partições.

```text
Order Service
     |
     | Evento:
     | key = PED-100
     | value = PedidoCriado
     v
Producer
     |
     | calcula a partição usando a key
     v
Topic: pedidos
     |
     ├── Partition 0
     ├── Partition 1 ← evento armazenado no offset 57
     └── Partition 2
             |
             v
Consumer Group: estoque-service
             |
             v
Consumer responsável pela Partition 1
             |
             v
Processa o evento
             |
             v
Commita o offset 58
```

Em sequência:

1. O `Order Service` cria o evento.
2. O producer serializa chave e valor.
3. A chave `PED-100` determina a partição.
4. O broker líder da partição recebe o evento.
5. O evento é acrescentado ao final do log.
6. O Kafka atribui um offset, por exemplo `57`.
7. A partição está atribuída a um consumer do grupo.
8. O consumer busca e processa o evento.
9. O grupo registra o próximo offset que deverá consumir.
10. O evento continua armazenado até a política de retenção removê-lo.

## Resposta pronta para entrevista

> Um producer publica um evento em um topic. O Kafka utiliza a chave ou sua estratégia de particionamento para selecionar uma partição. O broker responsável acrescenta o evento ao log da partição e atribui um offset sequencial. Dentro de um consumer group, essa partição é atribuída a apenas um consumer por vez. O consumer lê, processa e registra seu offset para continuar posteriormente. O evento não é removido pelo consumo, permanecendo disponível conforme a política de retenção.
