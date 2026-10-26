Lucas, segue o conteúdo reorganizado em **quadros/tabelas** para revisão rápida antes de entrevistas.

---

# Checklist para entrevistas — SQL em formato de tabela

## 1. Visão geral de SQL

| Tema                   | Explicação objetiva                                                                                     | Ponto de entrevista                                                                            |
| ---------------------- | ------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| SQL                    | Linguagem declarativa usada para consultar, manipular, definir e controlar dados em bancos relacionais. | Você descreve **o resultado desejado**, não o passo a passo de execução.                       |
| Banco de dados         | Decide o plano de execução.                                                                             | O otimizador escolhe índices, ordem de leitura, algoritmo de join e estratégia de execução.    |
| Importância em backend | Impacta performance, consistência, concorrência e segurança.                                            | SQL não é apenas `SELECT`; envolve modelagem, índices, transações, locks e planos de execução. |

### Resposta de entrevista

| Resposta                                                                                                                                                                                                                                                                                                                                                                                                 |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SQL é uma linguagem declarativa para trabalhar com dados relacionais. Em vez de controlar o fluxo de execução como em Java, eu descrevo o conjunto de dados que quero obter ou modificar, e o otimizador do banco decide a melhor estratégia de execução. Em sistemas reais, SQL envolve modelagem, joins, índices, transações, concorrência, consistência, performance e leitura de planos de execução. |

---

# 2. Categorias de comandos SQL

| Categoria | Nome                         | Finalidade           | Exemplos                      |
| --------- | ---------------------------- | -------------------- | ----------------------------- |
| DQL       | Data Query Language          | Consultar dados      | `SELECT`                      |
| DML       | Data Manipulation Language   | Manipular dados      | `INSERT`, `UPDATE`, `DELETE`  |
| DDL       | Data Definition Language     | Definir estrutura    | `CREATE`, `ALTER`, `DROP`     |
| DCL       | Data Control Language        | Controlar permissões | `GRANT`, `REVOKE`             |
| TCL       | Transaction Control Language | Controlar transações | `BEGIN`, `COMMIT`, `ROLLBACK` |

### Cuidados em produção

| Comando              | Risco                             |
| -------------------- | --------------------------------- |
| `ALTER TABLE`        | Pode gerar lock em tabela grande. |
| `DROP TABLE`         | Remove estrutura e dados.         |
| `TRUNCATE`           | Remove todos os dados da tabela.  |
| `DELETE` sem `WHERE` | Pode apagar todos os registros.   |
| `GRANT` excessivo    | Pode expor dados sensíveis.       |

---

# 3. SELECT

| Conceito        | Explicação                                |
| --------------- | ----------------------------------------- |
| Projeção        | Escolher quais colunas retornar.          |
| Filtro          | Usar `WHERE` para restringir linhas.      |
| Join            | Combinar tabelas relacionadas.            |
| Agregação       | Usar `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`. |
| Agrupamento     | Usar `GROUP BY`.                          |
| Filtro de grupo | Usar `HAVING`.                            |
| Ordenação       | Usar `ORDER BY`.                          |
| Paginação       | Usar `LIMIT/OFFSET` ou keyset pagination. |

## Ordem lógica do SELECT

| Ordem | Etapa          |
| ----: | -------------- |
|     1 | `FROM`         |
|     2 | `JOIN`         |
|     3 | `WHERE`        |
|     4 | `GROUP BY`     |
|     5 | `HAVING`       |
|     6 | `SELECT`       |
|     7 | `ORDER BY`     |
|     8 | `LIMIT/OFFSET` |

### Diferença entre WHERE e HAVING

| Cláusula | Quando executa        | Uso                        |
| -------- | --------------------- | -------------------------- |
| `WHERE`  | Antes do agrupamento  | Filtra linhas individuais. |
| `HAVING` | Depois do agrupamento | Filtra grupos agregados.   |

---

# 4. JOINs

| Tipo de JOIN      | O que retorna                                        | Uso comum                                   | Cuidado                                                            |
| ----------------- | ---------------------------------------------------- | ------------------------------------------- | ------------------------------------------------------------------ |
| `INNER JOIN`      | Apenas registros com correspondência nos dois lados. | Buscar entidades relacionadas obrigatórias. | Exclui registros sem relação.                                      |
| `LEFT JOIN`       | Todos da esquerda e correspondentes da direita.      | Buscar registros mesmo sem associação.      | Filtro errado no `WHERE` pode virar comportamento de `INNER JOIN`. |
| `RIGHT JOIN`      | Todos da direita e correspondentes da esquerda.      | Menos comum.                                | Geralmente pode ser reescrito como `LEFT JOIN`.                    |
| `FULL OUTER JOIN` | Todos dos dois lados.                                | Comparação entre conjuntos.                 | Nem todo banco suporta diretamente.                                |
| `CROSS JOIN`      | Produto cartesiano.                                  | Combina todas as linhas entre tabelas.      | Pode explodir o volume de dados.                                   |

## Erro comum com LEFT JOIN

| Situação                               | Exemplo                                         | Problema                                                 |
| -------------------------------------- | ----------------------------------------------- | -------------------------------------------------------- |
| Filtro da tabela da direita no `WHERE` | `WHERE o.status = 'PAID'`                       | Remove linhas onde `o` é `NULL`, parecendo `INNER JOIN`. |
| Filtro dentro do `ON`                  | `ON o.customer_id = c.id AND o.status = 'PAID'` | Preserva os registros da esquerda mesmo sem pedido pago. |

---

# 5. GROUP BY, HAVING e agregações

| Conceito   | Explicação                             | Exemplo de uso                  |
| ---------- | -------------------------------------- | ------------------------------- |
| `GROUP BY` | Agrupa linhas por uma ou mais colunas. | Total de pedidos por cliente.   |
| `COUNT()`  | Conta linhas.                          | Quantidade de pedidos.          |
| `SUM()`    | Soma valores.                          | Total vendido.                  |
| `AVG()`    | Calcula média.                         | Ticket médio.                   |
| `MIN()`    | Menor valor.                           | Menor preço.                    |
| `MAX()`    | Maior valor.                           | Maior compra.                   |
| `HAVING`   | Filtra grupos.                         | Clientes com mais de 5 pedidos. |

## Erro comum

| Erro                                                                   | Por que é problema                                         |
| ---------------------------------------------------------------------- | ---------------------------------------------------------- |
| Selecionar coluna que não está no `GROUP BY` nem em função agregadora. | A query fica inválida ou ambígua em bancos mais rigorosos. |

---

# 6. Subqueries, CTEs, Views e Materialized Views

| Recurso           | O que é                      | Vantagem                         | Cuidado                                         |
| ----------------- | ---------------------------- | -------------------------------- | ----------------------------------------------- |
| Subquery          | Query dentro de outra query. | Simples para filtros.            | Pode prejudicar legibilidade.                   |
| CTE               | Query nomeada com `WITH`.    | Melhora organização da consulta. | Dependendo do banco, pode afetar otimização.    |
| View              | Consulta salva no banco.     | Reutilização e padronização.     | Pode esconder complexidade.                     |
| Materialized View | Resultado salvo fisicamente. | Boa para relatórios pesados.     | Precisa de `refresh`; pode ter dados defasados. |

## Quando usar

| Cenário                  | Melhor opção      |
| ------------------------ | ----------------- |
| Filtro simples           | Subquery          |
| Query complexa em etapas | CTE               |
| Consulta reutilizável    | View              |
| Relatório pesado         | Materialized View |

---

# 7. Índices

| Tipo de índice   | Finalidade                                      | Exemplo de uso             |
| ---------------- | ----------------------------------------------- | -------------------------- |
| B-Tree           | Índice comum para igualdade, range e ordenação. | `WHERE customer_id = ?`    |
| Índice simples   | Uma coluna.                                     | `customer_id`              |
| Índice composto  | Mais de uma coluna.                             | `(customer_id, status)`    |
| Índice único     | Garante unicidade.                              | `email UNIQUE`             |
| Índice parcial   | Indexa apenas parte da tabela.                  | `WHERE status = 'PENDING'` |
| Índice funcional | Indexa resultado de função.                     | `LOWER(email)`             |

## Trade-offs dos índices

| Benefício              | Custo                                        |
| ---------------------- | -------------------------------------------- |
| Acelera filtros.       | Aumenta uso de armazenamento.                |
| Acelera joins.         | Deixa `INSERT` mais caro.                    |
| Acelera ordenações.    | Deixa `UPDATE` e `DELETE` mais caros.        |
| Pode evitar full scan. | Pode ser inútil se a seletividade for baixa. |

## Índice composto

| Índice                  | Ajuda bem em                           | Pode não ajudar bem em            |
| ----------------------- | -------------------------------------- | --------------------------------- |
| `(customer_id, status)` | `WHERE customer_id = ?`                | `WHERE status = ?`                |
| `(customer_id, status)` | `WHERE customer_id = ? AND status = ?` | Filtro apenas pela segunda coluna |

---

# 8. Plano de execução

| Item no plano    | Significado                           | Interpretação                                                                          |
| ---------------- | ------------------------------------- | -------------------------------------------------------------------------------------- |
| `Seq Scan`       | Leitura sequencial da tabela.         | Pode ser ruim em tabela grande, mas aceitável em tabela pequena ou baixa seletividade. |
| `Index Scan`     | Uso de índice.                        | Geralmente bom para filtros seletivos.                                                 |
| `Nested Loop`    | Join por laço aninhado.               | Bom para poucos registros; ruim se multiplicar muito.                                  |
| `Hash Join`      | Join usando hash em memória.          | Bom para grandes volumes, mas consome memória.                                         |
| `Merge Join`     | Join com dados ordenados.             | Pode ser eficiente quando há ordenação adequada.                                       |
| `Sort`           | Ordenação explícita.                  | Pode ser caro em grandes volumes.                                                      |
| Linhas estimadas | Estimativa do otimizador.             | Se muito diferente do real, estatísticas podem estar desatualizadas.                   |
| Linhas reais     | Resultado real com `EXPLAIN ANALYZE`. | Ajuda a localizar gargalos reais.                                                      |

## O que observar

| Verificação                      | Por quê                                                |
| -------------------------------- | ------------------------------------------------------ |
| Está usando índice?              | Ajuda a entender se o filtro é seletivo.               |
| Existe full table scan?          | Pode indicar ausência de índice ou baixa seletividade. |
| Existe sort caro?                | Pode impactar CPU e memória.                           |
| Estimativa difere muito do real? | Pode indicar estatísticas ruins.                       |
| Join está multiplicando linhas?  | Pode gerar explosão de dados.                          |
| Qual etapa tem maior tempo?      | Indica onde otimizar primeiro.                         |

---

# 9. Transações e ACID

| Princípio    | Significado                                           | Exemplo prático                                                          |
| ------------ | ----------------------------------------------------- | ------------------------------------------------------------------------ |
| Atomicidade  | Tudo executa ou nada executa.                         | Transferência bancária precisa debitar e creditar.                       |
| Consistência | Dados permanecem válidos.                             | Saldo não pode ficar inválido.                                           |
| Isolamento   | Transações concorrentes não interferem indevidamente. | Duas compras simultâneas não devem vender o mesmo estoque indevidamente. |
| Durabilidade | Após `COMMIT`, dado persiste.                         | Pedido confirmado não deve sumir após falha.                             |

## Comandos principais

| Comando    | Função               |
| ---------- | -------------------- |
| `BEGIN`    | Inicia transação.    |
| `COMMIT`   | Confirma alterações. |
| `ROLLBACK` | Desfaz alterações.   |

---

# 10. Níveis de isolamento

| Nível              | Protege contra                    | Custo                                          |
| ------------------ | --------------------------------- | ---------------------------------------------- |
| `READ UNCOMMITTED` | Quase nada.                       | Maior concorrência, menor consistência.        |
| `READ COMMITTED`   | Dirty read.                       | Equilíbrio comum em muitos bancos.             |
| `REPEATABLE READ`  | Dirty read e non-repeatable read. | Mais isolamento, mais contenção.               |
| `SERIALIZABLE`     | Maior isolamento.                 | Menor concorrência e maior chance de conflito. |

## Problemas de concorrência

| Problema            | Explicação                                               |
| ------------------- | -------------------------------------------------------- |
| Dirty Read          | Ler dado ainda não commitado por outra transação.        |
| Non-repeatable Read | Ler a mesma linha duas vezes e obter valores diferentes. |
| Phantom Read        | Reexecutar uma consulta e aparecerem ou sumirem linhas.  |

---

# 11. Locks, deadlocks e concorrência

| Conceito            | Explicação                                           | Cuidado                                         |
| ------------------- | ---------------------------------------------------- | ----------------------------------------------- |
| Lock                | Bloqueio usado para proteger dados concorrentes.     | Pode gerar espera.                              |
| Lock de escrita     | Ocorre em `UPDATE`, `DELETE` ou alterações de linha. | Outras transações podem ficar bloqueadas.       |
| `SELECT FOR UPDATE` | Lê e bloqueia linhas para futura atualização.        | Útil, mas deve ser usado com transações curtas. |
| Deadlock            | Duas transações esperam uma pela outra.              | O banco aborta uma transação.                   |

## Como reduzir deadlock

| Prática                                       | Motivo                                        |
| --------------------------------------------- | --------------------------------------------- |
| Acessar registros sempre na mesma ordem.      | Evita ciclos de espera.                       |
| Manter transações curtas.                     | Reduz tempo de lock.                          |
| Não chamar APIs externas dentro da transação. | Evita segurar lock por muito tempo.           |
| Criar índices adequados.                      | Evita locks em volume maior que o necessário. |
| Usar retry controlado.                        | Deadlock pode ser transitório.                |
| Garantir idempotência.                        | Permite retry seguro.                         |

---

# 12. Modelagem relacional

| Conceito      | Explicação                                   |
| ------------- | -------------------------------------------- |
| Tabela        | Representa uma entidade ou relação.          |
| Coluna        | Representa um atributo.                      |
| Linha         | Representa um registro.                      |
| Primary Key   | Identifica unicamente uma linha.             |
| Foreign Key   | Garante relacionamento válido entre tabelas. |
| Cardinalidade | Define relação entre entidades.              |
| Constraint    | Regra de integridade aplicada pelo banco.    |

## Relacionamentos

| Tipo | Exemplo                 | Modelagem                                  |
| ---- | ----------------------- | ------------------------------------------ |
| 1:1  | Usuário e perfil único. | FK única.                                  |
| 1:N  | Cliente e pedidos.      | FK em `orders` apontando para `customers`. |
| N:N  | Pedido e produtos.      | Tabela intermediária `order_items`.        |

---

# 13. Normalização e desnormalização

| Conceito        | Objetivo                                 | Benefício                            | Cuidado                    |
| --------------- | ---------------------------------------- | ------------------------------------ | -------------------------- |
| Normalização    | Reduzir redundância.                     | Menos inconsistência.                | Pode exigir mais joins.    |
| Desnormalização | Melhorar leitura ou preservar histórico. | Mais performance em alguns cenários. | Pode gerar inconsistência. |

## Exemplo prático

| Situação                             | Decisão                                           |
| ------------------------------------ | ------------------------------------------------- |
| Dados atuais do cliente              | Normalizar em tabela `customers`.                 |
| Nome do cliente no momento do pedido | Pode desnormalizar como `customer_name_snapshot`. |
| Relatórios pesados                   | Pode usar tabela agregada ou materialized view.   |
| Dados financeiros históricos         | Snapshot pode ser necessário.                     |

---

# 14. Constraints

| Constraint    | Função                       | Exemplo                                |
| ------------- | ---------------------------- | -------------------------------------- |
| `PRIMARY KEY` | Identifica linha unicamente. | `id BIGSERIAL PRIMARY KEY`             |
| `FOREIGN KEY` | Garante relação válida.      | `customer_id REFERENCES customers(id)` |
| `UNIQUE`      | Impede duplicidade.          | `email UNIQUE`                         |
| `NOT NULL`    | Impede ausência de valor.    | `name NOT NULL`                        |
| `CHECK`       | Impõe regra de validação.    | `total_amount >= 0`                    |

## Importância

| Motivo         | Explicação                                      |
| -------------- | ----------------------------------------------- |
| Integridade    | O banco bloqueia dados inválidos.               |
| Segurança      | Não depende apenas da aplicação.                |
| Consistência   | Evita órfãos, duplicados e valores inválidos.   |
| Confiabilidade | Protege mesmo se outro sistema acessar o banco. |

---

# 15. NULL

| Conceito           | Explicação                                         |
| ------------------ | -------------------------------------------------- |
| `NULL`             | Ausência de valor.                                 |
| Não é zero         | `NULL` não significa `0`.                          |
| Não é string vazia | `NULL` não significa `''`.                         |
| Não é falso        | `NULL` não significa `false`.                      |
| Lógica ternária    | SQL trabalha com verdadeiro, falso e desconhecido. |

## Comparações

| Errado               | Correto                  |
| -------------------- | ------------------------ |
| `deleted_at = NULL`  | `deleted_at IS NULL`     |
| `deleted_at <> NULL` | `deleted_at IS NOT NULL` |

## Cuidado com NOT IN

| Situação                                 | Risco                 | Alternativa        |
| ---------------------------------------- | --------------------- | ------------------ |
| `NOT IN` com subquery que retorna `NULL` | Resultado inesperado. | Usar `NOT EXISTS`. |

---

# 16. IN, EXISTS, JOIN e NOT EXISTS

| Recurso      | Uso                                                | Cuidado                                         |
| ------------ | -------------------------------------------------- | ----------------------------------------------- |
| `IN`         | Verifica se valor está em um conjunto.             | Pode ter problema com `NULL` em alguns casos.   |
| `EXISTS`     | Verifica se existe ao menos uma linha relacionada. | Bom para semântica de existência.               |
| `JOIN`       | Combina linhas entre tabelas.                      | Pode duplicar registros.                        |
| `NOT EXISTS` | Verifica ausência de relação.                      | Geralmente mais seguro que `NOT IN` com `NULL`. |

## Escolha prática

| Pergunta                            | Melhor opção |
| ----------------------------------- | ------------ |
| Cliente tem ao menos um pedido?     | `EXISTS`     |
| Cliente está em uma lista de IDs?   | `IN`         |
| Quero dados do cliente e do pedido? | `JOIN`       |
| Cliente não tem pedido?             | `NOT EXISTS` |

---

# 17. Paginação

| Tipo              | Como funciona                     | Vantagem                         | Cuidado                    |
| ----------------- | --------------------------------- | -------------------------------- | -------------------------- |
| `LIMIT/OFFSET`    | Pula linhas e retorna uma página. | Simples.                         | Degrada com offset alto.   |
| Keyset pagination | Usa último registro como cursor.  | Melhor para grandes volumes.     | Precisa ordenação estável. |
| Cursor pagination | Variante orientada a cursor.      | Boa para APIs e scroll infinito. | Mais complexa que offset.  |

## Comparação

| Critério                         | OFFSET/LIMIT | Keyset    |
| -------------------------------- | ------------ | --------- |
| Simplicidade                     | Alta         | Média     |
| Performance em páginas profundas | Baixa        | Alta      |
| Consistência com dados mudando   | Menor        | Maior     |
| Ir diretamente para página 100   | Fácil        | Difícil   |
| Scroll infinito                  | Aceitável    | Excelente |

---

# 18. Performance em SQL

| Problema                  | Por que é ruim                    | Solução comum                                |
| ------------------------- | --------------------------------- | -------------------------------------------- |
| `SELECT *`                | Traz dados desnecessários.        | Selecionar apenas colunas necessárias.       |
| Função em coluna indexada | Pode impedir uso de índice comum. | Índice funcional ou normalização do dado.    |
| `LIKE '%texto'`           | Dificulta uso de B-Tree.          | Full-text search ou índice apropriado.       |
| `OR` mal usado            | Pode dificultar plano eficiente.  | Reescrever query ou usar índices adequados.  |
| OFFSET alto               | Banco descarta muitas linhas.     | Keyset pagination.                           |
| Join desnecessário        | Aumenta custo e volume.           | Trazer apenas relações necessárias.          |
| N+1                       | Muitas queries pequenas.          | `JOIN FETCH`, `EntityGraph`, DTO projection. |
| Transação longa           | Aumenta locks.                    | Reduzir escopo transacional.                 |

---

# 19. N+1 com JPA/Hibernate

| Item        | Explicação                                                                                  |
| ----------- | ------------------------------------------------------------------------------------------- |
| Problema    | Uma query principal busca N registros, e depois uma query extra é feita para cada registro. |
| Causa comum | Lazy loading acessado dentro de loop.                                                       |
| Impacto     | Centenas ou milhares de queries invisíveis.                                                 |
| Sintoma     | Endpoint lento mesmo com query inicial simples.                                             |

## Soluções

| Solução        | Quando usar                                             |
| -------------- | ------------------------------------------------------- |
| `JOIN FETCH`   | Quando precisa carregar associação junto com entidade.  |
| `EntityGraph`  | Quando quer controlar fetch sem escrever JPQL complexo. |
| DTO Projection | Quando endpoint precisa apenas de alguns campos.        |
| Batch size     | Quando quer reduzir quantidade de queries lazy.         |
| Query nativa   | Quando precisa de controle SQL específico.              |

---

# 20. DELETE, TRUNCATE e DROP

| Comando    | Remove                       | Aceita `WHERE`? | Mantém estrutura? | Cuidado                                             |
| ---------- | ---------------------------- | --------------: | ----------------: | --------------------------------------------------- |
| `DELETE`   | Linhas específicas ou todas. |             Sim |               Sim | Pode ser lento em grande volume.                    |
| `TRUNCATE` | Todas as linhas.             |             Não |               Sim | Pode resetar sequence/identity dependendo do banco. |
| `DROP`     | Tabela inteira.              |             Não |               Não | Remove estrutura e dados.                           |

---

# 21. Tipos de dados

| Tipo de dado           | Recomendação               | Cuidado                                      |
| ---------------------- | -------------------------- | -------------------------------------------- |
| Dinheiro               | `NUMERIC` ou `DECIMAL`     | Evitar `FLOAT` e `DOUBLE`.                   |
| Data sem hora          | `DATE`                     | Não usar texto para data.                    |
| Data e hora            | `TIMESTAMP`                | Avaliar timezone.                            |
| Data com timezone      | `TIMESTAMP WITH TIME ZONE` | Importante em sistemas globais.              |
| Texto curto            | `VARCHAR(n)`               | Definir tamanho quando fizer sentido.        |
| Texto longo            | `TEXT`                     | Avaliar busca e indexação.                   |
| Booleano               | `BOOLEAN`                  | Evitar `'S'/'N'` sem necessidade.            |
| Identificador numérico | `BIGINT`                   | Bom para índices sequenciais.                |
| Identificador público  | `UUID`                     | Pode impactar índice se aleatório e massivo. |

---

# 22. SQL em Java/Spring

| Abordagem       | Vantagem                         | Cuidado                                       |
| --------------- | -------------------------------- | --------------------------------------------- |
| JDBC            | Mais controle e previsibilidade. | Mais boilerplate.                             |
| Spring Data JPA | Produtividade.                   | Queries implícitas, N+1 e lazy loading.       |
| JPQL            | Orientado a entidades.           | Não é SQL puro.                               |
| Native Query    | Poder total do banco.            | Acoplamento ao banco.                         |
| QueryDSL        | Queries tipadas.                 | Mais complexidade.                            |
| jOOQ            | SQL tipado e poderoso.           | Curva de aprendizado e dependência adicional. |

## Pontos de entrevista Java/Spring

| Tema             | O que explicar                           |
| ---------------- | ---------------------------------------- |
| `@Transactional` | Controle transacional no Spring.         |
| Lazy loading     | Pode gerar N+1.                          |
| Fetch join       | Carrega associação na mesma query.       |
| DTO projection   | Evita carregar entidade completa.        |
| Native query     | Útil para SQL específico ou performance. |
| Flyway/Liquibase | Versionamento de schema.                 |

---

# 23. Idempotência no banco

| Conceito      | Explicação                                                    |
| ------------- | ------------------------------------------------------------- |
| Idempotência  | Repetir uma operação não deve gerar efeito duplicado.         |
| Uso comum     | Pagamentos, retries, Kafka, APIs REST e integrações externas. |
| Como garantir | Constraint única + transação.                                 |
| Exemplo       | `idempotency_key UNIQUE`.                                     |

## Estratégias

| Estratégia                        | Uso                                           |
| --------------------------------- | --------------------------------------------- |
| `UNIQUE` em chave de idempotência | Impede duplicidade.                           |
| `ON CONFLICT DO NOTHING`          | Ignora repetição segura.                      |
| `ON CONFLICT DO UPDATE`           | Atualiza registro existente.                  |
| Tabela de controle de eventos     | Evita processar o mesmo evento duas vezes.    |
| Retry idempotente                 | Permite tentar novamente sem duplicar efeito. |

---

# 24. Migrações de banco

| Tema               | Explicação                                             |
| ------------------ | ------------------------------------------------------ |
| Migration          | Evolução versionada do schema.                         |
| Ferramentas comuns | Flyway e Liquibase.                                    |
| Objetivo           | Controlar alterações no banco com rastreabilidade.     |
| Risco              | Lock, perda de dados, incompatibilidade com aplicação. |

## Estratégia segura para coluna NOT NULL em tabela grande

| Etapa | Ação                                       |
| ----: | ------------------------------------------ |
|     1 | Adicionar coluna como nullable.            |
|     2 | Fazer backfill em lotes.                   |
|     3 | Ajustar aplicação para preencher a coluna. |
|     4 | Validar dados.                             |
|     5 | Adicionar constraint `NOT NULL`.           |

## Cuidados em produção

| Cuidado                                 | Motivo                                   |
| --------------------------------------- | ---------------------------------------- |
| Evitar mudanças destrutivas diretas.    | Pode quebrar versão antiga da aplicação. |
| Planejar rollback.                      | Reduz risco operacional.                 |
| Avaliar locks.                          | Evita indisponibilidade.                 |
| Fazer backfill em lotes.                | Evita travar tabela grande.              |
| Garantir compatibilidade entre versões. | Ajuda em deploy contínuo.                |

---

# 25. Segurança em SQL

| Risco                      | Explicação                           | Solução                                   |
| -------------------------- | ------------------------------------ | ----------------------------------------- |
| SQL Injection              | Entrada do usuário manipula a query. | PreparedStatement ou parâmetros nomeados. |
| Usuário admin na aplicação | Alto impacto em caso de invasão.     | Menor privilégio.                         |
| Logs com dados sensíveis   | Vazamento de informação.             | Mascarar ou não logar dados pessoais.     |
| Falta de auditoria         | Dificulta rastreabilidade.           | Auditar operações sensíveis.              |
| Falta de constraints       | Permite inconsistência.              | Usar validações no banco.                 |

## Práticas recomendadas

| Prática                                    | Benefício                          |
| ------------------------------------------ | ---------------------------------- |
| Não concatenar SQL com entrada do usuário. | Evita injection.                   |
| Usar parâmetros.                           | Separa dado de comando.            |
| Aplicar menor privilégio.                  | Limita impacto.                    |
| Revisar migrations.                        | Evita alterações perigosas.        |
| Proteger dados pessoais.                   | Ajuda em segurança e conformidade. |

---

# 26. Resposta de nível Senior sobre SQL

| Resposta                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SQL é uma competência central em sistemas backend. Saber escrever `SELECT` não é suficiente. Um desenvolvedor Senior precisa entender modelagem relacional, constraints, joins, índices, transações, isolamento, locks, planos de execução e impactos de performance. Em aplicações Java com Spring, também precisa compreender como JPA/Hibernate gera SQL, como evitar N+1, quando usar native query, quando usar projeções e como projetar migrações seguras. SQL bem escrito melhora performance, consistência e confiabilidade. SQL mal escrito pode derrubar uma API, gerar inconsistência financeira ou causar lock em produção. |

---

# 27. O que mais cai em entrevistas

| Área        | Tópicos                                                                                              |
| ----------- | ---------------------------------------------------------------------------------------------------- |
| Consultas   | `SELECT`, `WHERE`, `JOIN`, `GROUP BY`, `HAVING`, `ORDER BY`, `LIMIT/OFFSET`, subqueries, CTEs.       |
| Modelagem   | Primary key, foreign key, cardinalidade, normalização, desnormalização, constraints, tipos de dados. |
| Performance | Índices, plano de execução, full table scan, cardinalidade, seletividade, paginação, N+1.            |
| Transações  | ACID, isolamento, locks, deadlocks, rollback, consistência.                                          |
| Java/Spring | `@Transactional`, JPA, Hibernate, lazy loading, fetch join, native query, Flyway/Liquibase.          |

---

# 28. Pleno vs Senior em SQL

| Tema        | Pleno                                   | Senior                                                        |
| ----------- | --------------------------------------- | ------------------------------------------------------------- |
| Queries     | Escreve consultas com joins e filtros.  | Otimiza consultas considerando volume e plano de execução.    |
| Índices     | Cria índices simples.                   | Avalia cardinalidade, seletividade, custo de escrita e plano. |
| JPA         | Usa repositories.                       | Entende SQL gerado, N+1, fetch strategy e projeções.          |
| Transações  | Usa `@Transactional`.                   | Entende locks, isolamento, rollback, deadlock e concorrência. |
| Modelagem   | Cria tabelas e relacionamentos básicos. | Modela pensando em integridade, evolução e performance.       |
| Migrações   | Escreve scripts simples.                | Planeja deploy seguro, rollback, backfill e compatibilidade.  |
| Performance | Resolve lentidão evidente.              | Antecipa gargalos e lê `EXPLAIN ANALYZE`.                     |
| Produção    | Atua em problemas simples.              | Avalia impacto operacional, lock, volume e risco.             |

---

# 29. Principais erros em sistemas reais

| Erro                           | Impacto                    | Correção                                     |
| ------------------------------ | -------------------------- | -------------------------------------------- |
| `SELECT *` em endpoint crítico | Mais I/O, rede e memória.  | Selecionar apenas campos necessários.        |
| Falta de índice em FK          | Joins e deletes lentos.    | Indexar FKs usadas em joins/filtros.         |
| Índices demais                 | Escrita lenta.             | Remover índices inúteis.                     |
| OFFSET alto                    | Paginação lenta.           | Usar keyset pagination.                      |
| N+1 no Hibernate               | Muitas queries invisíveis. | `JOIN FETCH`, `EntityGraph`, DTO projection. |
| Transações longas              | Locks e contenção.         | Reduzir escopo transacional.                 |
| Migration perigosa             | Pode travar produção.      | Planejar rollout em etapas.                  |
| Falta de constraints           | Dados inválidos.           | Usar `NOT NULL`, `UNIQUE`, `CHECK`, FK.      |
| Uso errado de `NULL`           | Bugs sutis.                | Usar `IS NULL`, `IS NOT NULL`, `NOT EXISTS`. |
| Desnormalização sem estratégia | Inconsistência.            | Definir origem da verdade e sincronização.   |

---

# 30. Checklist final para entrevista SQL

## Conceitos base

| Pergunta                                 | Você precisa saber responder? |
| ---------------------------------------- | ----------------------------- |
| Explica SQL como linguagem declarativa?  | Sim                           |
| Diferencia DDL, DML, DQL, DCL e TCL?     | Sim                           |
| Entende ordem lógica do `SELECT`?        | Sim                           |
| Sabe diferença entre `WHERE` e `HAVING`? | Sim                           |
| Entende `NULL` corretamente?             | Sim                           |

## Consultas

| Pergunta                                                         | Você precisa saber responder? |
| ---------------------------------------------------------------- | ----------------------------- |
| Sabe usar `INNER JOIN`, `LEFT JOIN`, `FULL JOIN` e `CROSS JOIN`? | Sim                           |
| Entende quando `JOIN` duplica linhas?                            | Sim                           |
| Sabe usar `GROUP BY` e agregações?                               | Sim                           |
| Sabe usar subquery, CTE e view?                                  | Sim                           |
| Sabe diferença entre `IN`, `EXISTS`, `JOIN` e `NOT EXISTS`?      | Sim                           |

## Modelagem

| Pergunta                                        | Você precisa saber responder? |
| ----------------------------------------------- | ----------------------------- |
| Sabe modelar tabelas relacionais?               | Sim                           |
| Entende PK, FK, `UNIQUE`, `CHECK` e `NOT NULL`? | Sim                           |
| Sabe cardinalidade 1:N e N:N?                   | Sim                           |
| Entende normalização?                           | Sim                           |
| Sabe quando desnormalizar?                      | Sim                           |

## Performance

| Pergunta                              | Você precisa saber responder? |
| ------------------------------------- | ----------------------------- |
| Sabe criar índice com critério?       | Sim                           |
| Entende índice composto?              | Sim                           |
| Entende seletividade e cardinalidade? | Sim                           |
| Sabe ler `EXPLAIN`?                   | Sim                           |
| Sabe evitar `SELECT *`?               | Sim                           |
| Entende paginação por keyset?         | Sim                           |
| Sabe identificar gargalos de query?   | Sim                           |

## Transações e concorrência

| Pergunta                                                      | Você precisa saber responder? |
| ------------------------------------------------------------- | ----------------------------- |
| Entende ACID?                                                 | Sim                           |
| Sabe níveis de isolamento?                                    | Sim                           |
| Sabe explicar dirty read, non-repeatable read e phantom read? | Sim                           |
| Entende locks?                                                | Sim                           |
| Sabe lidar com deadlocks?                                     | Sim                           |
| Sabe quando usar `SELECT FOR UPDATE`?                         | Sim                           |

## Java/Spring

| Pergunta                                    | Você precisa saber responder? |
| ------------------------------------------- | ----------------------------- |
| Entende `@Transactional`?                   | Sim                           |
| Entende como JPA gera SQL?                  | Sim                           |
| Sabe explicar N+1?                          | Sim                           |
| Sabe usar `JOIN FETCH`?                     | Sim                           |
| Sabe quando usar DTO projection?            | Sim                           |
| Sabe quando usar native query?              | Sim                           |
| Entende migrations com Flyway ou Liquibase? | Sim                           |

---

# Quadro resumo para revisão rápida

| Tema       | Palavra-chave   | O que lembrar na entrevista                                                   |
| ---------- | --------------- | ----------------------------------------------------------------------------- |
| SQL        | Declarativo     | Você descreve o resultado; o banco decide o plano.                            |
| SELECT     | Ordem lógica    | `FROM`, `JOIN`, `WHERE`, `GROUP BY`, `HAVING`, `SELECT`, `ORDER BY`, `LIMIT`. |
| JOIN       | Semântica       | JOIN errado pode duplicar ou remover linhas.                                  |
| GROUP BY   | Agregação       | `WHERE` filtra linhas; `HAVING` filtra grupos.                                |
| Índices    | Trade-off       | Melhoram leitura, pioram escrita.                                             |
| Plano      | EXPLAIN         | Não basta criar índice; precisa validar o plano.                              |
| Transação  | ACID            | Consistência depende de commit, rollback, isolamento e locks.                 |
| Isolamento | Concorrência    | Mais isolamento geralmente reduz throughput.                                  |
| Locks      | Produção        | Transações longas geram contenção.                                            |
| Modelagem  | Integridade     | Banco deve proteger regras estruturais.                                       |
| NULL       | Lógica ternária | Usar `IS NULL`, não `= NULL`.                                                 |
| Paginação  | Keyset          | Melhor que OFFSET alto em grandes volumes.                                    |
| JPA        | N+1             | ORM não elimina necessidade de saber SQL.                                     |
| Migration  | Deploy seguro   | Alteração de schema precisa considerar lock e compatibilidade.                |
| Segurança  | Injection       | Nunca concatenar entrada do usuário em SQL.                                   |

---

# Resposta final curta para entrevista

| Resposta                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Tenho uma visão prática de SQL além da sintaxe básica. Entendo que SQL é uma linguagem declarativa e que a performance depende fortemente de modelagem, índices, volume de dados, cardinalidade, plano de execução e padrão de acesso. Em sistemas Java com Spring, também considero como o ORM gera consultas, como evitar N+1, como usar transações corretamente e como planejar migrations seguras. Para mim, SQL bem usado é uma ferramenta de consistência e performance; SQL mal usado é uma das principais causas de lentidão, locks e inconsistência em produção. |
