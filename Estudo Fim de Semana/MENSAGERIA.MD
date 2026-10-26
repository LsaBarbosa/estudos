# Kafka
## Conceitos Kafka
| Conceito | O que é | Trade-off | Uso real em produção |
|---|---|---|---|
| **Broker** | Instância/servidor Kafka responsável por armazenar partições e atender producers e consumers. Vários brokers formam um cluster. | Mais brokers aumentam disponibilidade e capacidade, mas também aumentam custo operacional e complexidade do cluster. | Cluster com 3+ brokers distribuindo partições e réplicas entre máquinas diferentes. |
| **Topic** | Canal lógico onde eventos de determinado domínio são publicados. Ex.: `pedido-criado`. | Muitos topics melhoram separação e governança, mas aumentam administração, ACLs, schemas e monitoramento. | Separar eventos como `pedido-criado`, `pagamento-aprovado`, `estoque-reservado`. |
| **Partition** | Subdivisão de um topic. É a unidade de paralelismo, armazenamento e ordenação no Kafka. | Mais partitions aumentam throughput e paralelismo, mas elevam overhead, rebalances e custo de administração. | Topic de pagamentos com 12 partitions permitindo até 12 consumers ativos simultaneamente no mesmo grupo. |
| **Offset** | Posição sequencial de um registro dentro de uma partition. O consumer usa offsets para saber até onde processou. | Commit cedo pode causar perda lógica; commit tarde pode causar reprocessamento. | Consumer processa evento e confirma offset somente após concluir a operação com sucesso. |
| **Partition Key** | Chave usada pelo producer para determinar em qual partition o evento será gravado. | Boa key mantém ordenação por entidade; uma key ruim pode criar **hot partition**. | `pedidoId` como key para manter todos os eventos do mesmo pedido na mesma partition. |
| **Producer** | Aplicação que publica registros no Kafka. | Maior garantia de entrega (`acks`, retries, idempotência) normalmente aumenta custo/latência. | `pedido-service` publica `PedidoCriadoEvent` usando `KafkaTemplate`. |
| **Consumer Group** | Conjunto de consumers que cooperam na leitura de um topic. Dentro do grupo, cada partition fica atribuída a no máximo um consumer. | Mais consumers aumentam paralelismo apenas enquanto houver partitions disponíveis. | 6 pods do `pagamento-service` com o mesmo `group.id`, dividindo as partitions. |
| **Rebalanceamento** | Redistribuição das partitions entre consumers quando a composição do grupo muda. | Garante redistribuição automática, mas pode interromper temporariamente consumo e aumentar latência. | Pod entra ou sai do Kubernetes e Kafka redistribui suas partitions entre os consumers restantes. |

## Replicação e durabilidade
| Conceito | O que é | Trade-off | Uso real em produção |
|---|---|---|---|
| **Leader e Replicas** | Cada partition possui um leader responsável por leitura/escrita e pode possuir réplicas em outros brokers. | Replicação aumenta disponibilidade, porém aumenta uso de disco, rede e custo de infraestrutura. | Partition leader no Broker 1 e réplicas nos Brokers 2 e 3. Se o leader falhar, uma réplica pode assumir. |
| **Replication Factor** | Número total de cópias de cada partition no cluster. | RF maior melhora tolerância a falhas, mas multiplica armazenamento e tráfego de replicação. | `replication.factor=3` é comum em ambientes produtivos críticos. |
| **acks** | Define quanto o producer espera o Kafka confirmar antes de considerar uma escrita bem-sucedida. | Mais segurança implica potencial aumento de latência. | Sistemas financeiros costumam priorizar `acks=all`; telemetria menos crítica pode aceitar garantias menores. |

## Garantias de entrega
| Conceito | O que é | Trade-off | Uso real em produção |
|---|---|---|---|
| **At-most-once** | Uma mensagem é processada no máximo uma vez. Pode ser perdida, mas não é reprocessada. | Evita duplicação, porém aceita perda. | Métricas ou telemetria onde perder algumas mensagens é aceitável. |
| **At-least-once** | A mensagem será processada uma ou mais vezes. Pode ocorrer duplicação. | Maior confiabilidade, mas exige idempotência no consumer. | Pagamentos, pedidos, estoque e fluxos de negócio em geral. |
| **Exactly-once** | Busca produzir efeitos exatamente uma vez dentro de um escopo transacional suportado pelo Kafka. | Mais complexidade e custo; não resolve automaticamente efeitos externos como banco, REST ou e-mail. | Pipelines Kafka → Kafka, Kafka Streams e processamento transacional com offsets + produção de novos registros. |
| **DLT — Dead Letter Topic** | Topic separado para mensagens que falharam após retries ou são consideradas não processáveis. | Evita travar o fluxo principal, mas exige operação, observabilidade e estratégia de reprocessamento. | Evento com schema inválido ou erro permanente é enviado para `pedido-criado.DLT`. |
 
 
```text
                         Kafka
                           |
          +----------------+----------------+
          |                |                |
          v                v                v
 Event-Driven         Microservices     Spring Kafka
 Architecture
          |                |                |
          v                v                v
Eventual Consistency   Desacoplamento  @KafkaListener
          |                |            KafkaTemplate
          v                v
        Saga          Resiliência
          |                |
          v                v
     Compensação      Retry / DLT
                           |
                           v
                     Idempotência
                           |
                           v
                      At-least-once
                           |
                           v
                    Transactional
                       Outbox
```
