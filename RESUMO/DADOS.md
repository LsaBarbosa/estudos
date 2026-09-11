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

