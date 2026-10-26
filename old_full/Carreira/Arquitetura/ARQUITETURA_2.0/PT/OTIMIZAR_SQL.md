# Consumo de Recursos.
> Primeiro é necessário descobrir **quais consultas realmente estão consumindo recursos**.
>
> Serve como observabilidade em SQL

| Banco de Dados           | Equivalente ao `pg_stat_statements`                      | Função                                                                                       |
| ------------------------ | -------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| **PostgreSQL**           | `pg_stat_statements`                                     | Estatísticas agregadas das consultas executadas.                                             |
| **Oracle Database**      | Automatic Workload Repository (AWR), Statspack e `V$SQL` | Coleta estatísticas de SQL, consumo de CPU, I/O, tempo de execução e plano de execução.      | 

Sem métricas, qualquer otimização é apenas um palpite.

---


# Índices adequados
 As principais técnicas de otimização de SQL, independentemente do banco de dados (PostgreSQL, MySQL, SQL Server, Oracle, etc.), são:

## 1. Criar índices adequados

Acelera buscas, filtros, JOINs e ordenações.

* Índices: simples | composto | único | cobrindo (covering index)

---

## Filtrar o mais cedo possível

Reduza a quantidade de registros antes de realizar JOINs.

| Situação     | Ordem            |
| ------------ | ---------------- |
| ❌ **Ruim**   | `JOIN` → `WHERE` |
| ✅ **Melhor** | `WHERE` → `JOIN` |
 

(O otimizador geralmente faz isso, mas consultas bem escritas facilitam.)

---

## 4. Otimizar JOINs
| Prática                                                        | Por quê?                                                                                                                              | Trade-off                                                                                                                                         |
| -------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Utilizar as colunas corretas                                   | Evita relacionamentos incorretos, duplicações e aumento desnecessário do volume de dados processados.                                 | Pode exigir revisão do modelo de dados e das regras de relacionamento entre as tabelas.                                                           |
| Garantir índices nas chaves de junção                          | Reduz o custo de localizar os registros relacionados, principalmente em tabelas grandes.                                              | Índices aumentam o consumo de armazenamento e o custo de operações de escrita, como `INSERT`, `UPDATE` e `DELETE`.                                |
| Evitar `JOINs` desnecessários                                  | Diminui o volume de dados processados, o uso de memória e o tempo de execução da consulta.                                            | Remover um `JOIN` pode exigir outra consulta ou processamento adicional na aplicação.                                                             |
| Escolher corretamente `INNER JOIN`, `LEFT JOIN`, `EXISTS` etc. | Permite utilizar a operação mais adequada para a necessidade da consulta e ajuda o banco a gerar um plano de execução mais eficiente. | Uma escolha mais eficiente pode tornar a consulta menos intuitiva ou alterar o conjunto de resultados caso a semântica não seja bem compreendida. |


---

## 5. Evitar consultas N+1

| Solução                       | Como funciona                                                                                                                               | Vantagem                                                                                 | Trade-off                                                                                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`JOIN FETCH`**              | Carrega a entidade principal e seus relacionamentos em uma única consulta SQL utilizando `JOIN FETCH`.                                      | Os clientes e seus pedidos são carregados em uma única consulta.                         | Pode gerar várias linhas repetidas para a mesma entidade devido ao `JOIN`. Geralmente é necessário utilizar `DISTINCT` e ter cuidado com paginação. |
| **`EntityGraph`**             | Permite definir quais relacionamentos devem ser carregados em uma consulta específica, sem alterar o `fetch` padrão da entidade.            | Evita escrever `JOIN FETCH` em várias consultas e mantém o repositório mais declarativo. | Pode carregar um volume grande de dados quando muitos relacionamentos são incluídos simultaneamente.                                                |
| **Duas consultas planejadas** | Executa uma consulta para buscar as entidades principais e outra para buscar todos os relacionamentos de uma só vez (normalmente com `IN`). | Evita o problema de N+1 sem gerar um resultado com muitas linhas duplicadas.             | Exige processamento adicional na aplicação para agrupar e associar os dados retornados.                                                             |
---

## Utilizar paginação

Evite retornar milhares de registros.

Utilize:

```sql
LIMIT

OFFSET
```

ou técnicas de **Keyset Pagination (Seek Method)** para grandes volumes.

---


## 13. Escolher corretamente o tipo de índice

Nem todo índice resolve todos os problemas.

Exemplos:

* Índice simples
* Índice composto
* Índice parcial
* Índice por expressão
* Índice Full Text
* Índice espacial

---

## Analisar o plano de execução

Ferramentas como:

* EXPLAIN
* EXPLAIN ANALYZE
* Execution Plan

permitem verificar:

* scans
* joins
* ordenações
* uso de índices
* operações caras

---

## Particionar tabelas grandes

Divide grandes volumes em partes menores.

Exemplos:

* por data
* por região
* por cliente

---

## Utilizar operações em lote (Batch)

Evite: 1000 INSERTs

Prefira: 1 INSERT com 1000 linhas


ou operações em lote.

---

# Fluxo recomendado para otimização

```text
Aplicação lenta
        │
        ▼
Identificar consultas caras (pg_stat_statements)
        │
        ▼
Analisar plano de execução (EXPLAIN ANALYZE)
        │
        ▼
Verificar scans, joins, cardinalidade, memória e I/O
        │
        ▼
Corrigir causa raiz
(índices, SQL, estatísticas, modelo, particionamento)
        │
        ▼
Analisar infraestrutura
(pool, autovacuum, locks, bloat, checkpoints, memória)
        │
        ▼
Validar em ambiente com carga representativa
```

## Relação com Spring Boot

Em aplicações Spring Boot, a otimização do PostgreSQL não se limita ao banco de dados. É necessário observar também como a aplicação acessa os dados:

* Evitar o problema de **N+1** no Hibernate.
* Utilizar projeções (DTOs) quando não for necessário carregar entidades completas.
* Configurar corretamente o **HikariCP**, evitando pools de conexão excessivamente grandes ou pequenos.
* Definir transações (`@Transactional`) com o menor tempo possível para reduzir contenção e locks.
* Monitorar consultas lentas com ferramentas como Micrometer, OpenTelemetry e logs do Hibernate.
* Utilizar cache (como Redis) apenas para consultas realmente frequentes e que tolerem eventual desatualização dos dados.

Uma boa otimização é resultado da combinação entre **consultas eficientes, modelo de dados adequado, configuração correta do PostgreSQL e uma camada de acesso a dados bem implementada na aplicação Spring Boot**.
