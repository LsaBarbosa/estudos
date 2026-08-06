Lucas, **Performance de SQL** é um tópico central para entrevistas Java, principalmente em **Banco de Dados, SQL, índices, transações, consistência, JPA, Redis, cache, lazy loading, DevOps e observabilidade**.  Também está alinhado ao seu objetivo de fortalecer **banco de dados, performance, arquitetura e prática em Java**.

# 🇧🇷 Versão em Português — Mapa Mental em Tabelas

## 1. Visão geral

| Nó central                      | Resumo                                                                                                                         |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **Performance de SQL**          | Conjunto de práticas para fazer consultas SQL executarem mais rápido, consumirem menos CPU, memória e I/O, e escalarem melhor. |
| Objetivo principal              | Reduzir tempo de resposta e carga no banco.                                                                                    |
| Onde impacta                    | APIs REST, relatórios, jobs, microsserviços, telas paginadas, integrações.                                                     |
| Principais causas de lentidão   | Falta de índice, query mal escrita, excesso de dados, joins ruins, N+1, locks, paginação ruim.                                 |
| Ferramenta principal de análise | `EXPLAIN`, `EXPLAIN ANALYZE` ou plano de execução.                                                                             |
| Regra mental                    | Antes de otimizar código Java, valide se o SQL e o plano de execução estão corretos.                                           |

---

## 2. Mapa mental principal

| Centro              | Ramos principais         |
| ------------------- | ------------------------ |
| **Performance SQL** | Índices                  |
|                     | Plano de execução        |
|                     | Filtros eficientes       |
|                     | Joins bem modelados      |
|                     | Paginação correta        |
|                     | Evitar N+1               |
|                     | Transações curtas        |
|                     | Menos dados trafegados   |
|                     | Estatísticas atualizadas |
|                     | Observabilidade          |

---

## 3. Fluxo mental para analisar uma query lenta

| Etapa | Pergunta                                                                  |
| ----- | ------------------------------------------------------------------------- |
| 1     | A query está retornando dados demais?                                     |
| 2     | O filtro usa colunas indexadas?                                           |
| 3     | O banco está fazendo `index scan` ou `full table scan`?                   |
| 4     | Existe `JOIN` desnecessário?                                              |
| 5     | Existe `ORDER BY` caro?                                                   |
| 6     | A paginação usa `OFFSET` muito alto?                                      |
| 7     | A aplicação está causando N+1?                                            |
| 8     | Existe lock ou transação longa?                                           |
| 9     | O plano de execução mostra gargalo em CPU, I/O ou memória?                |
| 10    | A query precisa mesmo ser síncrona ou poderia ser cacheada/materializada? |

---

## 4. Principais vilões

| Problema                  | Sintoma                          | Possível solução                                    |
| ------------------------- | -------------------------------- | --------------------------------------------------- |
| Falta de índice           | Query lenta com muitos registros | Criar índice adequado                               |
| Índice errado             | Índice existe, mas não é usado   | Revisar ordem das colunas e seletividade            |
| `SELECT *`                | Retorna colunas desnecessárias   | Usar projeção                                       |
| `LIKE '%texto%'`          | Não usa índice B-tree comum      | Full-text search, trigram index ou outra estratégia |
| Função na coluna filtrada | Índice ignorado                  | Evitar função no lado da coluna                     |
| N+1 queries               | Muitas queries pequenas          | `JOIN FETCH`, EntityGraph, batch loading            |
| `OFFSET` alto             | Página 10000 lenta               | Keyset pagination                                   |
| Transação longa           | Locks e timeout                  | Reduzir escopo transacional                         |
| Join ruim                 | Alto custo no plano              | Índices em FKs e revisão da modelagem               |
| Baixo hit de cache        | Muitos acessos repetidos         | Cache Redis/CDN/materialized view                   |

---

# 5. Índices

## 5.1 Conceito

| Conceito   | Explicação                                                                 |
| ---------- | -------------------------------------------------------------------------- |
| **Índice** | Estrutura auxiliar usada pelo banco para localizar dados mais rapidamente. |
| Analogia   | Como o índice de um livro: evita ler todas as páginas.                     |
| Benefício  | Acelera buscas, joins, ordenações e filtros.                               |
| Custo      | Ocupa espaço e torna `INSERT`, `UPDATE` e `DELETE` mais caros.             |
| Regra      | Índice melhora leitura, mas pode prejudicar escrita.                       |

---

## 5.2 Quando criar índice

| Situação                               | Índice recomendado? | Motivo                          |
| -------------------------------------- | ------------------: | ------------------------------- |
| Coluna usada frequentemente em `WHERE` |                 Sim | Ajuda filtro                    |
| Coluna usada em `JOIN`                 |                 Sim | Ajuda relacionamento            |
| Coluna usada em `ORDER BY`             |        Sim, depende | Pode evitar ordenação cara      |
| Coluna com baixa cardinalidade         |             Depende | Pode não ser seletiva           |
| Coluna raramente consultada            |                 Não | Custo pode não compensar        |
| Tabela pequena                         |             Depende | Full scan pode ser barato       |
| Coluna muito atualizada                |             Cuidado | Índice aumenta custo de escrita |

---

## 5.3 Exemplo de índice simples

```sql
CREATE INDEX idx_customer_email
ON customers (email);
```

| Parte                | Explicação              |
| -------------------- | ----------------------- |
| `CREATE INDEX`       | Cria um índice          |
| `idx_customer_email` | Nome do índice          |
| `customers`          | Tabela indexada         |
| `email`              | Coluna usada em filtros |

Consulta beneficiada:

```sql
SELECT id, name, email
FROM customers
WHERE email = 'user@email.com';
```

---

## 5.4 Índice composto

```sql
CREATE INDEX idx_orders_customer_status_created_at
ON orders (customer_id, status, created_at);
```

| Coluna        | Por que está no índice               |
| ------------- | ------------------------------------ |
| `customer_id` | Filtra pedidos de um cliente         |
| `status`      | Filtra situação do pedido            |
| `created_at`  | Ajuda ordenação ou intervalo de data |

Consulta beneficiada:

```sql
SELECT id, status, total, created_at
FROM orders
WHERE customer_id = 100
  AND status = 'PAID'
ORDER BY created_at DESC;
```

---

## 5.5 Ordem das colunas no índice composto

| Índice                              | Query                                       |                              Usa bem? |
| ----------------------------------- | ------------------------------------------- | ------------------------------------: |
| `(customer_id, status, created_at)` | `WHERE customer_id = ?`                     |                                   Sim |
| `(customer_id, status, created_at)` | `WHERE customer_id = ? AND status = ?`      |                                   Sim |
| `(customer_id, status, created_at)` | `WHERE status = ?`                          |                            Nem sempre |
| `(customer_id, status, created_at)` | `WHERE created_at > ?`                      |                            Nem sempre |
| `(customer_id, status, created_at)` | `WHERE customer_id = ? ORDER BY created_at` | Depende, porque `status` está no meio |

Regra prática: em índice composto, a ordem importa. Coloque primeiro as colunas mais usadas em filtros de igualdade e com boa seletividade.

---

# 6. Plano de execução

## 6.1 Conceito

| Conceito              | Explicação                                                                       |
| --------------------- | -------------------------------------------------------------------------------- |
| **Plano de execução** | Caminho escolhido pelo banco para executar a query.                              |
| Serve para            | Entender se o banco usa índice, scan completo, join caro, sort em memória/disco. |
| Comando comum         | `EXPLAIN` ou `EXPLAIN ANALYZE`.                                                  |
| Importância           | Sem plano de execução, otimização vira chute.                                    |

---

## 6.2 Exemplo

```sql
EXPLAIN ANALYZE
SELECT id, name, email
FROM customers
WHERE email = 'user@email.com';
```

| O que observar            | Interpretação                                              |
| ------------------------- | ---------------------------------------------------------- |
| `Seq Scan`                | Banco está lendo a tabela toda                             |
| `Index Scan`              | Banco está usando índice                                   |
| `Bitmap Index Scan`       | Banco usa índice e depois acessa blocos da tabela          |
| `Nested Loop`             | Pode ser bom para poucos dados, ruim para muitos           |
| `Hash Join`               | Comum para juntar volumes maiores                          |
| `Sort`                    | Ordenação explícita, pode ser cara                         |
| Tempo real                | Mostra quanto a query realmente levou                      |
| Linhas estimadas vs reais | Se estiver muito diferente, estatísticas podem estar ruins |

---

# 7. Queries SARGable

## 7.1 Conceito

| Termo          | Significado                                                             |
| -------------- | ----------------------------------------------------------------------- |
| **SARGable**   | Query escrita de forma que o banco consiga usar índices eficientemente. |
| Ideia          | Evitar manipular a coluna indexada dentro do filtro.                    |
| Exemplo ruim   | `WHERE LOWER(email) = 'x@email.com'`                                    |
| Exemplo melhor | Salvar email normalizado ou usar índice funcional, dependendo do banco. |

---

## 7.2 Exemplo ruim vs bom

| Ruim                                    | Problema                                                       |
| --------------------------------------- | -------------------------------------------------------------- |
| `WHERE DATE(created_at) = '2026-06-17'` | Aplica função na coluna e pode impedir uso eficiente de índice |

Melhor:

```sql
WHERE created_at >= '2026-06-17 00:00:00'
  AND created_at <  '2026-06-18 00:00:00'
```

| Melhor                 | Benefício                              |
| ---------------------- | -------------------------------------- |
| Usa intervalo          | Permite usar índice em `created_at`    |
| Evita função na coluna | Query fica mais amigável ao otimizador |
| Mais escalável         | Funciona melhor em tabelas grandes     |

---

# 8. `SELECT *` vs projeção

## 8.1 Problema

```sql
SELECT *
FROM orders
WHERE customer_id = 100;
```

| Problema                     | Explicação                               |
| ---------------------------- | ---------------------------------------- |
| Traz colunas desnecessárias  | Mais I/O                                 |
| Aumenta tráfego de rede      | Mais bytes entre banco e aplicação       |
| Pode impedir index-only scan | Banco precisa acessar tabela             |
| Acopla aplicação ao schema   | Mudanças na tabela afetam mais o consumo |

---

## 8.2 Melhor

```sql
SELECT id, status, total, created_at
FROM orders
WHERE customer_id = 100;
```

| Benefício        | Explicação                              |
| ---------------- | --------------------------------------- |
| Menos dados      | Reduz I/O                               |
| Mais rápido      | Menor custo de leitura                  |
| Mais claro       | A query declara o que precisa           |
| Melhor para APIs | Retorna apenas dados necessários ao DTO |

---

# 9. Joins

## 9.1 Conceito

| Conceito    | Explicação                                                      |
| ----------- | --------------------------------------------------------------- |
| `JOIN`      | Combina dados de tabelas relacionadas.                          |
| Risco       | Join sem índice pode ficar muito caro.                          |
| Boa prática | Indexar chaves estrangeiras usadas em join.                     |
| Atenção     | Nem todo relacionamento precisa ser carregado em toda consulta. |

---

## 9.2 Exemplo

```sql
SELECT o.id, o.total, c.name
FROM orders o
JOIN customers c ON c.id = o.customer_id
WHERE o.status = 'PAID';
```

Índices úteis:

```sql
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_orders_customer_id ON orders(customer_id);
```

| Índice                   | Ajuda em              |
| ------------------------ | --------------------- |
| `idx_orders_status`      | Filtrar pedidos pagos |
| `idx_orders_customer_id` | Join com clientes     |
| PK de `customers.id`     | Normalmente já existe |

---

# 10. N+1 no Java/JPA

## 10.1 Conceito

| Conceito      | Explicação                                                                            |
| ------------- | ------------------------------------------------------------------------------------- |
| **N+1**       | A aplicação faz 1 query principal e depois N queries adicionais para relacionamentos. |
| Onde acontece | Muito comum com JPA/Hibernate e relacionamentos `LAZY`.                               |
| Sintoma       | API parece simples, mas dispara dezenas/centenas de queries.                          |
| Solução       | `JOIN FETCH`, DTO projection, `@EntityGraph`, batch size ou query específica.         |

---

## 10.2 Exemplo de problema

```java
List<Order> orders = orderRepository.findAll();

for (Order order : orders) {
    System.out.println(order.getCustomer().getName());
}
```

| Etapa     | O que acontece                                    |
| --------- | ------------------------------------------------- |
| 1         | Busca todos os pedidos                            |
| 2         | Para cada pedido, acessa `customer`               |
| 3         | Hibernate dispara uma query adicional por cliente |
| Resultado | 1 + N queries                                     |

---

## 10.3 Solução com `JOIN FETCH`

```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    @Query("""
        SELECT o
        FROM Order o
        JOIN FETCH o.customer
        WHERE o.status = :status
    """)
    List<Order> findByStatusWithCustomer(@Param("status") OrderStatus status);
}
```

| Parte                      | Explicação                              |
| -------------------------- | --------------------------------------- |
| `JOIN FETCH`               | Carrega o relacionamento na mesma query |
| `o.customer`               | Evita query extra ao acessar cliente    |
| `WHERE o.status = :status` | Limita o volume retornado               |
| Resultado                  | Reduz N+1                               |

---

# 11. Paginação

## 11.1 Paginação com `OFFSET`

```sql
SELECT id, total, created_at
FROM orders
ORDER BY created_at DESC
LIMIT 20 OFFSET 100000;
```

| Problema                 | Explicação                                      |
| ------------------------ | ----------------------------------------------- |
| `OFFSET` alto            | Banco precisa pular muitos registros            |
| Custo crescente          | Página 1 é rápida, página 10000 é lenta         |
| Pode gerar instabilidade | Dados novos podem mudar a posição dos registros |

---

## 11.2 Keyset pagination

```sql
SELECT id, total, created_at
FROM orders
WHERE created_at < '2026-06-17 10:00:00'
ORDER BY created_at DESC
LIMIT 20;
```

| Benefício                    | Explicação                                  |
| ---------------------------- | ------------------------------------------- |
| Mais eficiente               | Usa comparação com último registro visto    |
| Melhor para scroll infinito  | Ideal para feeds, timelines, listas grandes |
| Evita pular muitos registros | Não depende de `OFFSET` alto                |
| Requer ordenação estável     | Normalmente usa `created_at` + `id`         |

Versão mais robusta:

```sql
SELECT id, total, created_at
FROM orders
WHERE (created_at, id) < ('2026-06-17 10:00:00', 5000)
ORDER BY created_at DESC, id DESC
LIMIT 20;
```

---

# 12. Ordenação

| Situação                        | Risco                     | Solução                                  |
| ------------------------------- | ------------------------- | ---------------------------------------- |
| `ORDER BY` em coluna sem índice | Sort caro                 | Criar índice se fizer sentido            |
| Ordenar muitos dados            | Alto uso de memória/disco | Filtrar antes de ordenar                 |
| Ordenar campo calculado         | Pode impedir índice       | Avaliar coluna materializada             |
| Ordenação instável              | Paginação inconsistente   | Adicionar critério secundário, como `id` |

Exemplo:

```sql
CREATE INDEX idx_orders_created_at_id
ON orders(created_at DESC, id DESC);
```

---

# 13. Agregações

## 13.1 Exemplo caro

```sql
SELECT customer_id, COUNT(*), SUM(total)
FROM orders
GROUP BY customer_id;
```

| Risco              | Explicação                                                 |
| ------------------ | ---------------------------------------------------------- |
| Varre muitos dados | Pode ser caro em tabela grande                             |
| Agrupa volume alto | Usa CPU/memória                                            |
| Pode impactar API  | Relatórios pesados não deveriam bloquear transações online |

---

## 13.2 Alternativas

| Estratégia               | Quando usar                            |
| ------------------------ | -------------------------------------- |
| Índice adequado          | Filtros seletivos antes do agrupamento |
| Materialized view        | Relatório pesado e repetido            |
| Tabela de resumo         | Métricas pré-calculadas                |
| Processamento assíncrono | Jobs/eventos atualizando agregados     |
| Cache                    | Resultado muda pouco ou tolera atraso  |

---

# 14. `COUNT(*)`

| Situação                     | Atenção                                     |
| ---------------------------- | ------------------------------------------- |
| `COUNT(*)` em tabela pequena | Normalmente aceitável                       |
| `COUNT(*)` em tabela gigante | Pode ser caro                               |
| Página com total exato       | Pode gerar query pesada                     |
| Scroll infinito              | Pode evitar total exato                     |
| Dashboard                    | Pode usar agregação/cache/materialized view |

Exemplo comum em Spring Data:

```java
Page<Order> page = orderRepository.findByStatus(OrderStatus.PAID, pageable);
```

| Ponto          | Explicação                                         |
| -------------- | -------------------------------------------------- |
| `Page<T>`      | Geralmente executa query de dados + query de count |
| `Slice<T>`     | Pode evitar `COUNT(*)`                             |
| Uso de `Slice` | Bom para scroll infinito ou “carregar mais”        |

---

# 15. Transações e locks

## 15.1 Conceito

| Conceito  | Explicação                                     |
| --------- | ---------------------------------------------- |
| Transação | Unidade lógica de trabalho no banco            |
| Lock      | Bloqueio usado para proteger consistência      |
| Problema  | Transações longas seguram locks por mais tempo |
| Impacto   | Lentidão, timeout, deadlock, filas de espera   |

---

## 15.2 Boas práticas

| Boa prática                                | Motivo                                    |
| ------------------------------------------ | ----------------------------------------- |
| Manter transações curtas                   | Reduz tempo de lock                       |
| Não chamar API externa dentro da transação | Evita segurar conexão/lock esperando rede |
| Buscar só o necessário                     | Reduz tempo de processamento              |
| Indexar filtros de update/delete           | Evita lock em muitos registros            |
| Escolher isolamento corretamente           | Evita excesso de bloqueio                 |
| Monitorar deadlocks                        | Detecta problemas de concorrência         |

---

## 15.3 Exemplo ruim

```java
@Transactional
public void payOrder(Long orderId) {
    Order order = orderRepository.findById(orderId)
            .orElseThrow();

    paymentGateway.charge(order); // chamada externa dentro da transação

    order.markAsPaid();
}
```

| Problema                            | Explicação                     |
| ----------------------------------- | ------------------------------ |
| Chamada externa dentro da transação | Pode demorar ou falhar         |
| Conexão fica presa                  | Reduz throughput               |
| Lock pode durar mais                | Outras operações podem esperar |

---

## 15.4 Melhor direção

| Estratégia                                 | Explicação                   |
| ------------------------------------------ | ---------------------------- |
| Separar chamada externa da escrita crítica | Reduz tempo transacional     |
| Usar eventos/outbox                        | Melhora confiabilidade       |
| Atualizar estado em transação curta        | Menos lock                   |
| Usar idempotência                          | Evita duplicidade em retries |

---

# 16. Filtros eficientes

| Filtro                        | Risco                                   | Melhor abordagem                     |
| ----------------------------- | --------------------------------------- | ------------------------------------ |
| `WHERE LOWER(email) = ?`      | Pode ignorar índice comum               | Normalizar email ou índice funcional |
| `WHERE DATE(created_at) = ?`  | Função na coluna                        | Usar intervalo de data               |
| `WHERE name LIKE '%abc%'`     | Prefixo curinga impede B-tree eficiente | Full-text/trigram/search engine      |
| `WHERE status != 'CANCELLED'` | Baixa seletividade                      | Avaliar filtro positivo              |
| `WHERE id IN (...)` gigante   | Query pesada                            | Batch, tabela temporária ou join     |
| `OR` em muitas colunas        | Plano ruim                              | Separar queries ou ajustar índices   |

---

# 17. Cardinalidade e seletividade

| Conceito           | Explicação                                             |
| ------------------ | ------------------------------------------------------ |
| Cardinalidade      | Quantidade de valores distintos em uma coluna          |
| Seletividade       | Capacidade do filtro de reduzir linhas                 |
| Alta seletividade  | Filtra poucos registros, geralmente bom para índice    |
| Baixa seletividade | Filtra muitos registros, índice pode não ajudar        |
| Exemplo bom        | `email`, `cpf`, `order_id`                             |
| Exemplo fraco      | `active = true`, `gender`, `status` com poucos valores |

---

## Exemplo

| Coluna       | Valores distintos |             Índice costuma ajudar? |
| ------------ | ----------------: | ---------------------------------: |
| `email`      |           Milhões |                                Sim |
| `cpf`        |           Milhões |                                Sim |
| `status`     |                 5 |                            Depende |
| `active`     |                 2 |                   Geralmente pouco |
| `created_at` |            Muitos | Sim, especialmente com range/order |

---

# 18. Normalização vs desnormalização

| Estratégia        | Vantagem                           | Risco                                   |
| ----------------- | ---------------------------------- | --------------------------------------- |
| Normalização      | Evita duplicidade e inconsistência | Mais joins                              |
| Desnormalização   | Melhora leitura                    | Risco de inconsistência                 |
| View              | Simplifica consulta                | Pode não melhorar performance por si só |
| Materialized view | Acelera leitura pesada             | Precisa refresh                         |
| Tabela de leitura | Boa para CQRS/read model           | Mais complexidade operacional           |

---

# 19. Performance SQL em APIs Java

| Camada          | Cuidados                                            |
| --------------- | --------------------------------------------------- |
| Controller      | Não retornar payload gigante                        |
| Service         | Evitar loops que disparam queries                   |
| Repository      | Criar queries específicas para o caso de uso        |
| JPA/Hibernate   | Cuidado com lazy loading, N+1 e flush desnecessário |
| DTO             | Usar projeção para buscar somente o necessário      |
| Banco           | Índices, plano de execução e estatísticas           |
| Observabilidade | Logar queries lentas, métricas e tracing            |

---

# 20. Exemplo completo: Spring Data com projeção

## 20.1 DTO

```java
public record OrderSummaryResponse(
        Long id,
        String status,
        BigDecimal total,
        LocalDateTime createdAt
) {
}
```

## 20.2 Repository

```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    @Query("""
        SELECT new com.example.orders.OrderSummaryResponse(
            o.id,
            CAST(o.status AS string),
            o.total,
            o.createdAt
        )
        FROM Order o
        WHERE o.customer.id = :customerId
          AND o.status = :status
        ORDER BY o.createdAt DESC
    """)
    List<OrderSummaryResponse> findOrderSummaries(
            @Param("customerId") Long customerId,
            @Param("status") OrderStatus status
    );
}
```

## 20.3 Índice sugerido

```sql
CREATE INDEX idx_orders_customer_status_created
ON orders (customer_id, status, created_at DESC);
```

## 20.4 Leitura técnica

| Parte                      | Explicação                               |
| -------------------------- | ---------------------------------------- |
| DTO projection             | Evita carregar entidade inteira          |
| `WHERE customer_id`        | Filtra pelo dono dos pedidos             |
| `WHERE status`             | Reduz o conjunto                         |
| `ORDER BY created_at DESC` | Ordena por data                          |
| Índice composto            | Ajuda filtro + ordenação                 |
| Evita `SELECT *`           | Menos dados trafegados                   |
| Evita N+1                  | Não acessa relacionamento depois em loop |

---

# 21. Exemplo ruim vs melhor em JPA

## 21.1 Ruim

```java
List<Order> orders = orderRepository.findAll();

return orders.stream()
        .filter(order -> order.getStatus() == OrderStatus.PAID)
        .map(OrderResponse::from)
        .toList();
```

| Problema          | Explicação                          |
| ----------------- | ----------------------------------- |
| Busca tudo        | Carrega registros desnecessários    |
| Filtra em memória | Banco deveria filtrar               |
| Pode gerar N+1    | Mapper pode acessar relacionamentos |
| Consome memória   | Lista grande na JVM                 |
| Escala mal        | Quanto mais dados, pior             |

---

## 21.2 Melhor

```java
List<OrderSummaryResponse> orders =
        orderRepository.findOrderSummaries(customerId, OrderStatus.PAID);
```

| Melhoria                   | Explicação                    |
| -------------------------- | ----------------------------- |
| Filtra no banco            | Menos dados retornados        |
| Usa projeção               | Não carrega entidade completa |
| Usa índice                 | Melhor plano de execução      |
| Reduz memória na aplicação | Menos objetos Java            |
| Mais previsível            | Query alinhada ao caso de uso |

---

# 22. Connection pool

| Conceito         | Explicação                                     |
| ---------------- | ---------------------------------------------- |
| Pool de conexões | Conjunto de conexões reutilizáveis com o banco |
| Exemplo comum    | HikariCP no Spring Boot                        |
| Problema         | Pool pequeno demais causa fila                 |
| Outro problema   | Pool grande demais sobrecarrega o banco        |
| Sintoma          | Timeout esperando conexão                      |
| Cuidado          | Query lenta prende conexão por mais tempo      |

Regra mental:

| Se a query fica lenta           | Então                           |
| ------------------------------- | ------------------------------- |
| Conexão fica ocupada mais tempo | Menos requisições são atendidas |
| Pool começa a esgotar           | APIs começam a dar timeout      |
| Banco recebe pressão maior      | Latência aumenta                |
| Sistema degrada em cascata      | Outros serviços também sofrem   |

---

# 23. Observabilidade de SQL

| Métrica/log                   | O que mostra                       |
| ----------------------------- | ---------------------------------- |
| Query lenta                   | SQLs acima de determinado tempo    |
| Tempo médio por query         | Latência média                     |
| P95/P99                       | Lentidão percebida em cauda        |
| Número de queries por request | Detecta N+1                        |
| Uso de conexão                | Saturação do pool                  |
| Locks/deadlocks               | Problemas de concorrência          |
| Rows scanned                  | Quantidade de linhas lidas         |
| Rows returned                 | Quantidade de linhas retornadas    |
| Cache hit do banco            | Eficiência de buffer/cache interno |
| CPU/I/O do banco              | Gargalo físico/lógico              |

---

# 24. Checklist rápido de otimização

| Pergunta                         | Ação                                 |
| -------------------------------- | ------------------------------------ |
| Está usando `SELECT *`?          | Trocar por colunas necessárias       |
| Tem filtro em coluna sem índice? | Avaliar índice                       |
| Tem função na coluna filtrada?   | Reescrever filtro                    |
| Tem paginação com `OFFSET` alto? | Considerar keyset pagination         |
| Tem N+1?                         | Usar `JOIN FETCH`, projeção ou batch |
| Tem transação longa?             | Reduzir escopo                       |
| Tem join sem índice?             | Indexar FK                           |
| Tem sort caro?                   | Avaliar índice para ordenação        |
| Tem relatório pesado online?     | Cache/materialized view/job          |
| O plano foi analisado?           | Usar `EXPLAIN ANALYZE`               |

---

# 25. Quando não criar índice

| Situação                      | Motivo                            |
| ----------------------------- | --------------------------------- |
| Tabela muito pequena          | Full scan pode ser suficiente     |
| Coluna pouco consultada       | Custo não compensa                |
| Coluna com baixa seletividade | Índice pode não ajudar            |
| Muitas escritas na tabela     | Índice aumenta custo de escrita   |
| Índice duplicado              | Desperdício de espaço             |
| Índice nunca usado            | Deve ser removido após análise    |
| Query precisa de redesenho    | Índice não corrige modelagem ruim |

---

# 26. Erros comuns em entrevistas

| Erro                                | Melhor resposta                                           |
| ----------------------------------- | --------------------------------------------------------- |
| “É só criar índice”                 | Índice ajuda, mas deve ser validado com plano de execução |
| “`SELECT *` não tem problema”       | Em escala, aumenta I/O, rede e memória                    |
| “JPA sempre otimiza tudo”           | JPA pode esconder queries ruins e N+1                     |
| “Paginação com OFFSET resolve tudo” | OFFSET alto degrada                                       |
| “Cache resolve query lenta”         | Cache ajuda leitura repetida, mas não corrige SQL ruim    |
| “Banco é gargalo sempre”            | Pode ser query, índice, lock, pool, rede ou aplicação     |
| “Transação longa não afeta leitura” | Pode afetar locks, conexões e concorrência                |

---

# 🇺🇸 English Version — Mind Map in Tables

## 1. Overview

| Central node        | Summary                                                                               |
| ------------------- | ------------------------------------------------------------------------------------- |
| **SQL Performance** | Practices used to make SQL queries faster, cheaper and more scalable.                 |
| Main goal           | Reduce response time and database load.                                               |
| Impacts             | REST APIs, reports, jobs, microservices, paginated screens and integrations.          |
| Common causes       | Missing indexes, bad queries, excessive data, poor joins, N+1, locks, bad pagination. |
| Main analysis tool  | `EXPLAIN`, `EXPLAIN ANALYZE` or execution plan.                                       |
| Mental rule         | Before optimizing Java code, verify the SQL and execution plan.                       |

---

## 2. Main mind map

| Center              | Main branches      |
| ------------------- | ------------------ |
| **SQL Performance** | Indexes            |
|                     | Execution plan     |
|                     | Efficient filters  |
|                     | Proper joins       |
|                     | Correct pagination |
|                     | Avoid N+1          |
|                     | Short transactions |
|                     | Less data transfer |
|                     | Updated statistics |
|                     | Observability      |

---

## 3. Slow query analysis flow

| Step | Question                                                     |
| ---- | ------------------------------------------------------------ |
| 1    | Is the query returning too much data?                        |
| 2    | Are filters using indexed columns?                           |
| 3    | Is the database doing an index scan or a full table scan?    |
| 4    | Is there an unnecessary join?                                |
| 5    | Is `ORDER BY` expensive?                                     |
| 6    | Is pagination using a high `OFFSET`?                         |
| 7    | Is the application causing N+1 queries?                      |
| 8    | Are there locks or long transactions?                        |
| 9    | Does the execution plan show CPU, I/O or memory bottlenecks? |
| 10   | Should this query be cached, precomputed or asynchronous?    |

---

## 4. Main performance killers

| Problem                     | Symptom                         | Possible solution                        |
| --------------------------- | ------------------------------- | ---------------------------------------- |
| Missing index               | Slow query on large tables      | Create the right index                   |
| Wrong index                 | Index exists but is not used    | Review column order and selectivity      |
| `SELECT *`                  | Too much data returned          | Use projection                           |
| `LIKE '%text%'`             | B-tree index usually not useful | Full-text search or specific index       |
| Function on filtered column | Index may be ignored            | Rewrite predicate                        |
| N+1 queries                 | Too many small queries          | `JOIN FETCH`, EntityGraph, batch loading |
| High `OFFSET`               | Deep pages become slow          | Keyset pagination                        |
| Long transaction            | Locks and timeouts              | Reduce transaction scope                 |
| Poor join                   | Expensive execution plan        | Index foreign keys                       |
| Repeated expensive reads    | High database pressure          | Cache or materialized view               |

---

# 5. Indexes

## 5.1 Concept

| Concept   | Explanation                                                        |
| --------- | ------------------------------------------------------------------ |
| **Index** | Auxiliary data structure used by the database to find rows faster. |
| Analogy   | Like a book index: it avoids reading every page.                   |
| Benefit   | Speeds up lookups, joins, sorting and filtering.                   |
| Cost      | Uses storage and makes writes more expensive.                      |
| Rule      | Indexes improve reads but may hurt writes.                         |

---

## 5.2 When to create an index

| Situation                         | Recommended? | Reason                      |
| --------------------------------- | -----------: | --------------------------- |
| Column frequently used in `WHERE` |          Yes | Helps filtering             |
| Column used in `JOIN`             |          Yes | Helps relationships         |
| Column used in `ORDER BY`         |      Depends | May avoid expensive sorting |
| Low-cardinality column            |      Depends | May not be selective        |
| Rarely queried column             |           No | Cost may not pay off        |
| Small table                       |      Depends | Full scan may be cheap      |
| Frequently updated column         |   Be careful | Index increases write cost  |

---

## 5.3 Composite index

```sql
CREATE INDEX idx_orders_customer_status_created_at
ON orders (customer_id, status, created_at);
```

| Column        | Reason                      |
| ------------- | --------------------------- |
| `customer_id` | Filters by customer         |
| `status`      | Filters by order status     |
| `created_at`  | Helps date range or sorting |

Query:

```sql
SELECT id, status, total, created_at
FROM orders
WHERE customer_id = 100
  AND status = 'PAID'
ORDER BY created_at DESC;
```

---

# 6. Execution plan

| Concept            | Explanation                                                 |
| ------------------ | ----------------------------------------------------------- |
| **Execution plan** | The path chosen by the database to run the query.           |
| Used for           | Checking indexes, scans, joins, sort operations and costs.  |
| Common command     | `EXPLAIN` or `EXPLAIN ANALYZE`.                             |
| Importance         | Without an execution plan, optimization is mostly guessing. |

Example:

```sql
EXPLAIN ANALYZE
SELECT id, name, email
FROM customers
WHERE email = 'user@email.com';
```

| What to check            | Meaning                                        |
| ------------------------ | ---------------------------------------------- |
| `Seq Scan`               | Full table scan                                |
| `Index Scan`             | Index is being used                            |
| `Nested Loop`            | Can be good for small sets, bad for large sets |
| `Hash Join`              | Common for larger joins                        |
| `Sort`                   | Explicit sorting, possibly expensive           |
| Estimated vs actual rows | Big differences may indicate bad statistics    |

---

# 7. SARGable queries

| Term           | Meaning                                                   |
| -------------- | --------------------------------------------------------- |
| **SARGable**   | Query written in a way that allows efficient index usage. |
| Main idea      | Avoid applying functions to indexed columns in filters.   |
| Bad example    | `WHERE DATE(created_at) = '2026-06-17'`                   |
| Better example | Use a date range.                                         |

Better:

```sql
WHERE created_at >= '2026-06-17 00:00:00'
  AND created_at <  '2026-06-18 00:00:00'
```

| Benefit                   | Explanation             |
| ------------------------- | ----------------------- |
| Uses range filtering      | Helps index usage       |
| Avoids function on column | Easier for optimizer    |
| Scales better             | Better for large tables |

---

# 8. Projection instead of `SELECT *`

Bad:

```sql
SELECT *
FROM orders
WHERE customer_id = 100;
```

Better:

```sql
SELECT id, status, total, created_at
FROM orders
WHERE customer_id = 100;
```

| Benefit              | Explanation                       |
| -------------------- | --------------------------------- |
| Less I/O             | Reads fewer columns               |
| Less network traffic | Sends fewer bytes                 |
| Better memory usage  | Fewer objects/data in application |
| Clearer contract     | Query declares what it needs      |

---

# 9. Joins

| Concept         | Explanation                                                |
| --------------- | ---------------------------------------------------------- |
| `JOIN`          | Combines related tables.                                   |
| Risk            | Join without proper indexes may become expensive.          |
| Best practice   | Index foreign keys used in joins.                          |
| Attention point | Do not load relationships that the use case does not need. |

Example:

```sql
SELECT o.id, o.total, c.name
FROM orders o
JOIN customers c ON c.id = o.customer_id
WHERE o.status = 'PAID';
```

Useful indexes:

```sql
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_orders_customer_id ON orders(customer_id);
```

---

# 10. N+1 in Java/JPA

| Concept     | Explanation                                                                    |
| ----------- | ------------------------------------------------------------------------------ |
| **N+1**     | Application runs 1 main query and then N additional queries for relationships. |
| Common with | JPA/Hibernate and lazy relationships.                                          |
| Symptom     | Simple endpoint triggers many SQL queries.                                     |
| Solution    | `JOIN FETCH`, DTO projection, `@EntityGraph`, batch size or custom query.      |

Problem:

```java
List<Order> orders = orderRepository.findAll();

for (Order order : orders) {
    System.out.println(order.getCustomer().getName());
}
```

Solution:

```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    @Query("""
        SELECT o
        FROM Order o
        JOIN FETCH o.customer
        WHERE o.status = :status
    """)
    List<Order> findByStatusWithCustomer(@Param("status") OrderStatus status);
}
```

---

# 11. Pagination

## 11.1 Problem with high `OFFSET`

```sql
SELECT id, total, created_at
FROM orders
ORDER BY created_at DESC
LIMIT 20 OFFSET 100000;
```

| Problem         | Explanation                        |
| --------------- | ---------------------------------- |
| High `OFFSET`   | Database must skip many rows       |
| Increasing cost | Page 1 is fast, page 10000 is slow |
| Instability     | New rows may shift page results    |

---

## 11.2 Keyset pagination

```sql
SELECT id, total, created_at
FROM orders
WHERE (created_at, id) < ('2026-06-17 10:00:00', 5000)
ORDER BY created_at DESC, id DESC
LIMIT 20;
```

| Benefit                  | Explanation                          |
| ------------------------ | ------------------------------------ |
| More efficient           | Uses last seen row                   |
| Good for infinite scroll | Works well for feeds and large lists |
| Avoids skipping rows     | Does not depend on high `OFFSET`     |
| Requires stable ordering | Usually `created_at` plus `id`       |

---

# 12. Transactions and locks

| Concept     | Explanation                                     |
| ----------- | ----------------------------------------------- |
| Transaction | Logical unit of work in the database.           |
| Lock        | Mechanism used to protect consistency.          |
| Problem     | Long transactions hold locks longer.            |
| Impact      | Slowness, timeout, deadlock and waiting queues. |

Bad:

```java
@Transactional
public void payOrder(Long orderId) {
    Order order = orderRepository.findById(orderId)
            .orElseThrow();

    paymentGateway.charge(order);

    order.markAsPaid();
}
```

| Problem                          | Explanation                      |
| -------------------------------- | -------------------------------- |
| External call inside transaction | Network delay holds DB resources |
| Connection remains busy          | Lower throughput                 |
| Locks may last longer            | Other operations may wait        |

---

# 13. SQL performance in Java APIs

| Layer         | Concerns                                          |
| ------------- | ------------------------------------------------- |
| Controller    | Avoid huge payloads                               |
| Service       | Avoid loops that trigger queries                  |
| Repository    | Create queries specific to the use case           |
| JPA/Hibernate | Watch for lazy loading, N+1 and unnecessary flush |
| DTO           | Use projection to fetch only needed data          |
| Database      | Indexes, execution plan and statistics            |
| Observability | Slow query logs, metrics and tracing              |

---

# 14. Complete Java/Spring example

## 14.1 DTO

```java
public record OrderSummaryResponse(
        Long id,
        String status,
        BigDecimal total,
        LocalDateTime createdAt
) {
}
```

## 14.2 Repository

```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    @Query("""
        SELECT new com.example.orders.OrderSummaryResponse(
            o.id,
            CAST(o.status AS string),
            o.total,
            o.createdAt
        )
        FROM Order o
        WHERE o.customer.id = :customerId
          AND o.status = :status
        ORDER BY o.createdAt DESC
    """)
    List<OrderSummaryResponse> findOrderSummaries(
            @Param("customerId") Long customerId,
            @Param("status") OrderStatus status
    );
}
```

## 14.3 Suggested index

```sql
CREATE INDEX idx_orders_customer_status_created
ON orders (customer_id, status, created_at DESC);
```

| Improvement          | Explanation                       |
| -------------------- | --------------------------------- |
| DTO projection       | Does not load the full entity     |
| Filter in database   | Avoids filtering in Java memory   |
| Composite index      | Helps filtering and sorting       |
| Less network traffic | Only required fields are returned |
| Less memory usage    | Fewer Java objects                |

---

# 15. Quick checklist

| Question                                | Action                                |
| --------------------------------------- | ------------------------------------- |
| Is the query using `SELECT *`?          | Select only required columns          |
| Is there a filter without index?        | Consider an index                     |
| Is there a function on filtered column? | Rewrite the predicate                 |
| Is there high `OFFSET` pagination?      | Consider keyset pagination            |
| Is there N+1?                           | Use `JOIN FETCH`, projection or batch |
| Is the transaction too long?            | Reduce transaction scope              |
| Is there a join without index?          | Index the foreign key                 |
| Is sorting expensive?                   | Consider index for ordering           |
| Is a heavy report running online?       | Use cache/materialized view/job       |
| Was the execution plan checked?         | Run `EXPLAIN ANALYZE`                 |

---

# Revisão rápida

| Pergunta                                 | Resposta curta                                                                 |
| ---------------------------------------- | ------------------------------------------------------------------------------ |
| O que é performance de SQL?              | Fazer consultas executarem rápido e com menor custo de recursos.               |
| O que é índice?                          | Estrutura que ajuda o banco a encontrar dados mais rapidamente.                |
| Índice sempre melhora performance?       | Não. Melhora leitura, mas pode piorar escrita e ocupar espaço.                 |
| O que é plano de execução?               | Caminho que o banco escolhe para executar a query.                             |
| O que é `Seq Scan`?                      | Leitura sequencial da tabela. Pode ser ruim em tabelas grandes.                |
| O que é N+1?                             | Uma query principal mais N queries adicionais para relacionamentos.            |
| Como evitar `SELECT *`?                  | Usando projeções e selecionando apenas colunas necessárias.                    |
| Por que `OFFSET` alto é ruim?            | O banco precisa pular muitos registros antes de retornar a página.             |
| O que é keyset pagination?               | Paginação baseada no último registro visto.                                    |
| Por que transação longa é perigosa?      | Segura conexão e locks por mais tempo.                                         |
| Cache resolve SQL ruim?                  | Não necessariamente. Pode aliviar leitura repetida, mas a query continua ruim. |
| Primeira coisa a olhar numa query lenta? | Plano de execução e volume de dados retornado.                                 |

---

# Exercícios progressivos

| Nível         | Exercício                                                                                       |
| ------------- | ----------------------------------------------------------------------------------------------- |
| Básico        | Explique com suas palavras o que é um índice e qual seu custo.                                  |
| Básico        | Reescreva uma query `SELECT *` para retornar apenas colunas necessárias.                        |
| Básico        | Explique a diferença entre `Seq Scan` e `Index Scan`.                                           |
| Intermediário | Crie um índice para uma query que filtra por `customer_id`, `status` e ordena por `created_at`. |
| Intermediário | Explique por que `WHERE DATE(created_at) = ?` pode ser ruim.                                    |
| Intermediário | Mostre como evitar N+1 em um relacionamento `Order -> Customer`.                                |
| Intermediário | Compare `Page<T>` e `Slice<T>` no Spring Data.                                                  |
| Avançado      | Transforme uma paginação com `OFFSET` alto em keyset pagination.                                |
| Avançado      | Analise uma query lenta usando `EXPLAIN ANALYZE` e identifique o gargalo.                       |
| Avançado      | Modele uma estratégia para relatório pesado usando materialized view ou tabela de resumo.       |
| Avançado      | Explique como uma query lenta pode esgotar o pool de conexões da aplicação Java.                |
| Avançado      | Descreva uma abordagem para investigar lentidão: API → JPA → SQL → índice → plano → métricas.   |
