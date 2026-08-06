# O que são índices no PostgreSQL?

> Índices são estruturas de dados utilizadas para acelerar a busca por registros em uma tabela, evitando que o banco precise percorrer todas as linhas durante uma consulta.
>
> Melhoram a performance de leitura, mas aumentam o tempo de operações de escrita, pois INSERT, UPDATE e DELETE, também precisam ser atualizados.
>
> Por isso, eu criaria índices apenas para colunas frequentemente utilizadas em filtros, JOINs, ordenações ou constraints.

A busca passa de um **Table Scan** para um **Index Scan**, reduzindo significativamente o número de páginas lidas.

---

# Como você utiliza EXPLAIN ANALYZE?

> Utilizado para entender como Banco de dasdos executa uma consulta e identificar possíveis gargalos de desempenho.
>
> Informando operações como Sequential Scan, Index Scan, Nested Loop, Hash Join e Sort, além do tempo gasto em cada etapa e a quantidade de linhas processadas.
>
> Em vez de otimizar consultas por tentativa e erro, utilizaria o EXPLAIN ANALYZE para basear as decisões em evidências.

### Exemplo

```sql
EXPLAIN ANALYZE
SELECT *
FROM pedidos
WHERE cliente_id = 10;
```

Pode retornar:

```text
Seq Scan ou Index Scan
```

Se aparecer:

```text
Seq Scan

numa tabela enorme, pode indicar falta de índice.
```
---

# O que é JOIN?

> JOIN é a operação utilizada para combinar registros de duas ou mais tabelas com base em uma condição de relacionamento.
>
> Os tipos mais comuns são INNER JOIN, que retorna apenas registros com correspondência em ambas as tabelas, e LEFT JOIN, que retorna todos os registros da tabela da esquerda, mesmo que não exista correspondência na tabela da direita.
>
> Para obter boa performance, é importante que as colunas utilizadas no JOIN estejam indexadas quando apropriado, reduzindo a quantidade de leituras necessárias.

### INNER JOIN

```sql
SELECT *
FROM cliente c
JOIN pedido p
ON c.id = p.cliente_id;
```

Somente clientes com pedidos.

---

### LEFT JOIN

```sql
SELECT *
FROM cliente c
LEFT JOIN pedido p
ON c.id = p.cliente_id;
```

Todos os clientes.

Mesmo quem nunca comprou.

---

# O que são Locks?

> Locks controlam o acesso concorrente aos dados e garante a consistência das transações.
>
> Enquanto uma transação altera um registro, outras transações são impedidas de modificá-lo até o commit ou rollback.
>
> O PostgreSQL utiliza diferentes tipos de bloqueios, como locks de linha, de tabela e locks internos para operações administrativas.
>
> Em ambientes concorrentes, eu monitoraria situações de lock prolongado e deadlocks, pois eles podem aumentar a latência ou bloquear outras transações.

---

# O que são Isolation Levels?

> Isolation Level define o grau de isolamento entre transações concorrentes, equilibrando consistência dos dados e desempenho.
>
> O padrão do PostgreSQL é Read Committed, no qual cada consulta enxerga apenas dados já confirmados por outras transações.
>
> Em cenários que exigem maior consistência, podem ser utilizados níveis como Repeatable Read ou Serializable, que reduzem anomalias de concorrência, mas aumentam a possibilidade de bloqueios ou conflitos.
>
> A escolha do nível de isolamento depende dos requisitos do negócio e não existe um nível ideal para todos os casos.

### Principais níveis

| Nível            | Evita                                |
| ---------------- | ------------------------------------ |
| Read Uncommitted | praticamente não usado no PostgreSQL |
| Read Committed   | Dirty Read                           |
| Repeatable Read  | Non Repeatable Read                  |
| Serializable     | Phantom Read                         |

---


# O que é o problema N+1?

> Quando uma consulta busca por uma lista de registros e em seguida realiza outra busca para cada item dessa lista, aumentando a quantidade de consulta no banco.
>
> Bucar pedidos de clientes. ele busca os clientes depois uma busca para cada lista de pedido de cada cliente
>
> Em aplicações Spring Data JPA, esse problema costuma ocorrer devido ao carregamento lazy de relacionamentos.
>
> Para evitá-lo, utilizo estratégias como `JOIN FETCH`, `EntityGraph` ou consultas específicas projetadas para carregar apenas os dados necessários.


## Como resolver o N+1?

### 1. JOIN FETCH

```java
@Query("""
SELECT c
FROM Cliente c
JOIN FETCH c.pedidos
""")
```

Carrega clientes e pedidos em uma única consulta.

**Vantagem:** elimina o N+1.

**Trade-off:** pode retornar linhas duplicadas para o mesmo cliente devido ao JOIN, sendo comum utilizar `DISTINCT`.

---

### 2. EntityGraph

```java
@EntityGraph(attributePaths = "pedidos")
List<Cliente> findAll();
```

Permite informar quais relacionamentos devem ser carregados apenas naquela consulta, mantendo o relacionamento `LAZY` no restante da aplicação.

---

# Dica para entrevista

Uma pergunta muito comum é:

> **"Como você investigaria uma consulta lenta no PostgreSQL?"**

Uma resposta sólida seria:

> Eu começaria executando o **EXPLAIN ANALYZE** para entender o plano de execução da consulta. Verificaria se o banco está utilizando **Index Scan** ou realizando **Sequential Scan**, analisaria os tipos de **JOIN**, o número de linhas processadas e o tempo gasto em cada etapa. Também avaliaria a existência de índices adequados, estatísticas desatualizadas, problemas como **N+1**, bloqueios (**locks**) ou transações longas. A partir dessas evidências, decidiria se a melhor solução é criar ou ajustar índices, reescrever a consulta, revisar o modelo de acesso aos dados ou alterar a estratégia de carregamento utilizada pela aplicação.
