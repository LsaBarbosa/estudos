# Isolamento
- **Lost Update**: Quando duas transações acessam simultaneamente e uma das alterações é perdida
  - T1 e T2 acessam simultaneamente Saldo de 100, T1 tira 100, T2 tira 200, T1 escreve 900 e T2 800
  - Resultado saldo 800 pois T1 foi perdida
```text
concorrência
     ↓
transações simultâneas
     ↓
anomalias (Dirty Read, Non-Repeatable Read, Phantom Read)
     ↓
isolamento (Atomic UPDATE, Serializable )/ locks (Pessimistc, Optmistic / MVCC
```
 ---
## Anomalias de concorrência
| Problema                | O que acontece                                                             | Exemplo simples                                                                                         |
| ----------------------- | -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| **Dirty Read**          | Uma transação lê algo que outra transação ainda não confirmou com `COMMIT` | T1 altera saldo para `500`; T2 lê `500`; depois T1 faz `ROLLBACK`                                       |
| **Non-Repeatable Read** | A mesma linha retorna valores diferentes dentro da mesma transação         | T1 lê saldo `1000`; T2 altera para `800` e commita; T1 lê novamente e recebe `800`                      |
| **Phantom Read**        | A mesma consulta retorna um **conjunto diferente de linhas**               | T1 encontra 10 pedidos pendentes; T2 insere outro pedido e commita; T1 consulta novamente e encontra 11 |

## Estratégias de concorrência
| Estratégia              | O que é                                                                              | Quando usar                                                       | Trade-off                                                  | Exemplo comum                                            |
| ----------------------- | ------------------------------------------------------------------------------------ | ----------------------------------------------------------------- | ---------------------------------------------------------- | -------------------------------------------------------- |
| **Optimistic Locking**  | Detecta se outra transação modificou o registro desde a leitura                      | Conflitos normalmente raros                                       | Boa concorrência, mas exige tratar conflito/retry          | Alteração de cadastro, pedido, produto                   |
| **Pessimistic Locking** | Bloqueia o recurso antes da alteração                                                | Conflitos frequentes ou recurso muito disputado                   | Esperas, contenção, timeout e possibilidade de deadlock    | Último ingresso, reserva de assento                      |
| **Atomic UPDATE**       | Valida e altera o dado numa única instrução SQL                                      | Quando a regra pode ser expressa diretamente no `UPDATE`          | Muito eficiente, mas menos flexível para regras complexas  | Baixar estoque, incrementar contador                     |
| **Serializable**        | Usa o nível de isolamento para impedir interferências incompatíveis entre transações | Quando a consistência precisa abranger várias operações/consultas | Maior custo de concorrência e possibilidade de abort/retry | Operações financeiras ou alocações extremamente críticas |
- **Optimistic Locking**: 
    - Parte do princípio de que conflitos são raros. 
    - A aplicação deixa as transações trabalharem e, na hora do UPDATE, verifica se outra transação alterou o registro. 
    - Em JPA/Hibernate, normalmente é feito com @Version
 - **Atomic UPDATE**: 
    - Faz leitura, validação e alteração em uma única operação SQL. 
    - Evita o ciclo vulnerável SELECT → Java → UPDATE.
    - Feito com @Lock(LockModeType.PESSIMISTIC_WRITE)
- **Serializable**
    - O banco garante um resultado equivalente a executar as transações uma depois da outra, mesmo que elas tenham ocorrido concorrentemente.

| Optimistic | Pessimistic |
|---|---|
| Detecta conflito | Evita simultaneidade sobre o registro |
| Normalmente usa `@Version` | Usa lock no banco |
| Não segura lock durante toda a lógica | Pode manter lock até `COMMIT` |
| Excelente para baixa contenção | Útil para alta contenção |
| Conflito gera erro | Concorrente pode esperar |
| Maior concorrência | Pode reduzir throughput |
| Pode exigir retry | Pode gerar espera/deadlock |

```text
                 Concorrência
                      │
          ┌───────────┴───────────┐
          ▼                       ▼
      READ/WRITE              WRITE/WRITE
          │                       │
          ▼                       ▼
         MVCC                  conflito
                                  │
                    ┌─────────────┼─────────────┐
                    ▼             ▼             ▼
               operação      optimistic    pessimistic
                atômica         lock           lock
                                   │
                                   ▼
                               @Version
```
Primeiro eu identificaria qual invariável precisa ser protegida e
qual é o nível de contenção esperado.

Se a alteração puder ser expressa através de um UPDATE condicional
atômico, eu tenderia a preferi-lo.

Para entidades com baixa disputa, optimistic locking através de
@Version é uma boa opção.

Quando existe alta contenção e preciso serializar o acesso a um
recurso específico, avaliaria pessimistic locking.

Também analisaria o isolation level do banco, duração da transação,
índices, possibilidade de deadlock, política de timeout e retry.

---
## Níveis de Isolamento
 | Nível                | O que é                                                                | Quando usar                                                                       | Trade-off                                                                              | Exemplo comum                                                                        |
| -------------------- | ---------------------------------------------------------------------- | --------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| **READ UNCOMMITTED** | Permite enxergar alterações ainda não commitadas                       | Casos muito específicos onde consistência importa pouco                           | Máxima concorrência, mínima consistência; pode haver Dirty Read                        | Consultas analíticas extremamente permissivas; pouco usado em sistemas transacionais |
| **READ COMMITTED**   | Só permite ler dados já commitados                                     | Aplicações corporativas comuns                                                    | Bom equilíbrio entre concorrência e consistência; ainda pode haver Non-Repeatable Read | APIs CRUD, sistemas administrativos, e-commerce                                      |
| **REPEATABLE READ**  | Mantém uma visão mais estável dos dados já lidos durante a transação   | Quando várias leituras dentro da mesma transação precisam permanecer consistentes | Mais custo/contenção ou uso de versões MVCC                                            | Processamento financeiro, geração de cálculos baseados em várias leituras            |
| **SERIALIZABLE**     | Oferece comportamento equivalente à execução sequencial das transações | Invariantes extremamente críticas e alta necessidade de consistência              | Mais conflitos, bloqueios/aborts, retries e menor throughput                           | Reservas críticas, alocação exclusiva de recursos                                    |
---
    
# MVCC - Multi-Version Concurrency Control
> Estratégia em que tranaações leem e escrevem dados Concorrentemente com menos bloqueio entre leitores e escritores

> Ao inves de sobrescrever uma linha existente o banco mantem versões dessa linha e decide qual versão cada transação pode enxergar.

> Apenas Read x Writer, se Writer X Writer ainda pode gerar contenção e locks


| Isolation        | Dirty Read | Non-Repeatable Read |                                Phantom Read |
| ---------------- | ---------: | ------------------: | ------------------------------------------: |
| READ UNCOMMITTED |       Pode |                Pode |                                        Pode |
| READ COMMITTED   |     Impede |                Pode |                                        Pode |
| REPEATABLE READ  |     Impede |              Impede | depende da implementação/semântica do banco |
| SERIALIZABLE     |     Impede |              Impede |                                      Impede |

Lucas, aqui está a tabela focada exatamente nos pontos que você pediu:

| Tema | Conceito | Vantagem | Trade-off | Uso real em produção |
|---|---|---|---|---|
| **Particionamento** | Divide uma tabela grande em partes menores chamadas **partições**, normalmente por `RANGE`, `HASH` ou `LIST`. Para a aplicação, muitas vezes continua existindo uma única tabela lógica. | Melhora manutenção, pode reduzir o volume de dados lido, facilita arquivamento e remoção de dados antigos. | Aumenta complexidade de modelagem, exige escolher bem a chave de particionamento e pode piorar queries que não usam essa chave. | Tabelas de **logs, pedidos, transações e auditoria** particionadas por mês ou ano usando `created_at`. |
| **Partition Pruning** | Otimização em que o banco identifica quais partições podem ser ignoradas antes de executar a consulta. | Reduz leitura de dados, I/O e CPU. Pode melhorar bastante queries em tabelas muito grandes. | Só funciona bem quando o predicado da query permite ao banco identificar a partição correta. Queries mal escritas podem impedir o pruning. | `WHERE created_at BETWEEN '2026-09-01' AND '2026-09-30'` em uma tabela particionada por `created_at`, fazendo o PostgreSQL consultar apenas a partição de setembro. |
| **Sharding** | Divide horizontalmente os dados entre **bancos/nós diferentes**. Cada shard contém apenas uma parte do dataset. | Permite escalar armazenamento e escrita horizontalmente, distribuindo carga entre várias instâncias. | Aumenta muito a complexidade: routing, migrations, JOINs, transações distribuídas, observabilidade, backup, rebalancing e queries globais. | Plataformas com dezenas ou centenas de milhões de clientes distribuindo dados por `customer_id`, `tenant_id` ou `account_id`. |
| **Hotspot** | Ocorre quando um shard recebe muito mais dados ou requisições que os outros. | Não é uma vantagem em si; é um problema que precisa ser evitado. Detectá-lo ajuda a melhorar distribuição e capacidade. | Pode saturar CPU, storage, conexões ou I/O de um shard enquanto outros permanecem ociosos. | Um sistema shardeado por `empresa_id` onde um único grande cliente gera 70% das requisições e concentra toda a carga em um shard. |
| **Boa Shard Key** | Chave usada para determinar em qual shard o dado ficará. Deve considerar **cardinalidade, distribuição e padrões de acesso**. | Permite localizar diretamente o shard correto, reduz `scatter-gather` e mantém dados relacionados próximos. | Uma escolha ruim é difícil de corrigir depois. Pode gerar hotspots, consultas cross-shard e rebalancing caro. | Usar `customer_id` quando quase todas as operações são feitas por cliente, permitindo que pedidos, pagamentos e histórico fiquem no mesmo shard. |

## Resumo para memorizar

A relação entre eles é:

```text
PARTICIONAMENTO
      |
      | divide uma tabela
      v
Partitions
      |
      v
Partition Pruning
evita consultar partitions desnecessárias
```

Enquanto:

```text
SHARDING
   |
   | divide dados entre bancos
   v
Shard 1   Shard 2   Shard 3
              |
              v
          Shard Key
              |
      precisa distribuir bem
              |
              v
        evitar Hotspot
```

### O ponto mais importante de cada um

| Tema | Pergunta mental |
|---|---|
| Particionamento | **Como dividir uma tabela grande?** |
| Partition Pruning | **Quais partições posso ignorar nesta query?** |
| Sharding | **Como distribuir dados entre vários bancos?** |
| Hotspot | **Algum shard está recebendo carga demais?** |
| Shard Key | **Qual chave determina onde o dado ficará?** |
 

