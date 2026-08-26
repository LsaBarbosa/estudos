# PostgreSQL — Índices, planos de execução e Locks  
## Resumo para Leitura em Voz Alta e entrevistas

## 1. Índices

Índices existem para reduzir o custo de localizar dados.

Sem índice adequado, o PostgreSQL pode precisar percorrer grande parte da tabela.

Mas índice não é gratuito.

Ele ocupa espaço e adiciona custo em `INSERT`, `UPDATE` e `DELETE`, porque além da tabela, o banco também precisa manter o índice atualizado.

Portanto:

**mais índices não significa automaticamente mais performance.**

O objetivo é criar índices alinhados às consultas realmente executadas. 

---

## 2. B-Tree

B-Tree é o índice padrão e mais importante do PostgreSQL.

Ele mantém os valores ordenados em uma árvore balanceada.

É adequado principalmente para:

igualdade,

intervalos,

comparações,

e ordenação.

Por exemplo:

igual,

maior que,

menor que,

`BETWEEN`,

e muitos casos de `ORDER BY`.

Para entrevistas, memorize:

**B-Tree é a primeira opção para a maioria das consultas tradicionais.** 

---

## 3. Hash Index

Hash Index é focado principalmente em comparação por igualdade.

A ideia é transformar o valor em um hash e utilizar esse resultado para localizar a entrada.

Ele pode servir para consultas do tipo:

`id igual a valor`.

Mas não é adequado para:

intervalos,

maior que,

menor que,

ou ordenação.

Na maioria das aplicações, B-Tree continua sendo mais versátil.

---

## 4. GIN

GIN significa Generalized Inverted Index.

Ele é muito útil quando uma coluna contém vários elementos que precisam ser pesquisados individualmente.

Exemplos comuns são:

arrays,

JSONB,

e full-text search.

Mentalmente, pense:

**B-Tree procura o valor da coluna.**

**GIN é muito bom para procurar elementos dentro de estruturas compostas.**

Internamente, GIN relaciona chaves aos registros onde essas chaves aparecem. 

---

## 5. GiST

GiST significa Generalized Search Tree.

Ele é uma estrutura extensível usada para tipos de consultas mais especializadas.

É muito encontrado em:

dados geográficos,

geometria,

ranges,

similaridade,

e alguns tipos de busca textual.

Uma forma simples de diferenciar:

**GIN é muito forte em pertencimento e elementos compostos.**

**GiST é muito flexível para estruturas, proximidade e operadores especializados.** 

---

## 6. Composite Index

Composite Index é um índice que possui várias colunas.

Por exemplo:

índice em `status` e `created_at`.

A ordem das colunas importa.

Para B-Tree, as colunas iniciais normalmente possuem papel fundamental para limitar a região do índice que precisa ser examinada.

Por isso, não devemos simplesmente colocar várias colunas em qualquer ordem.

Precisamos analisar os filtros e ordenações das consultas reais. 

---

## 7. Partial Index

Partial Index indexa apenas as linhas que satisfazem determinada condição.

Imagine uma tabela com milhões de pedidos, mas apenas uma pequena parte está pendente.

Podemos criar um índice apenas sobre:

pedidos com status pendente.

Isso reduz:

tamanho do índice,

custo de manutenção,

e espaço.

É excelente quando as consultas se concentram em um subconjunto previsível dos dados.

---

## 8. Covering Index

Covering Index tenta manter no próprio índice todas as colunas necessárias para determinada consulta.

No PostgreSQL podemos utilizar `INCLUDE` para adicionar colunas que não fazem parte da chave principal do índice.

Isso pode permitir um **Index Only Scan**, evitando acessar diretamente a tabela em determinados casos.

Mas não é garantia absoluta.

O PostgreSQL também precisa verificar se a visibilidade das linhas permite responder apenas pelo índice.

A ideia principal é:

**se o índice já contém tudo que preciso, talvez eu não precise buscar a linha na tabela.**

---

# 9. EXPLAIN

`EXPLAIN` mostra o plano que o PostgreSQL pretende utilizar para executar uma consulta.

Ele não está simplesmente mostrando o SQL.

Ele mostra decisões do otimizador, como:

Sequential Scan,

Index Scan,

Bitmap Scan,

Nested Loop,

Hash Join,

e Merge Join.

Para troubleshooting de banco, uma das perguntas mais importantes é:

**qual plano o banco escolheu e por quê?**

---

## 10. EXPLAIN ANALYZE

`EXPLAIN ANALYZE` vai além.

Ele realmente executa a consulta e compara estimativas com valores observados.

Você deve prestar atenção em:

estimated rows,

actual rows,

loops,

tempo,

e operações mais caras.

Uma diferença muito grande entre:

linhas estimadas

e

linhas reais

pode indicar problemas de estatística, distribuição de dados ou estimativa do planner.

Um cuidado importante:

**EXPLAIN ANALYZE executa a consulta.**

Portanto, deve ser usado com atenção especialmente em comandos que alteram dados.

---

# 11. pg_stat_statements

`pg_stat_statements` permite observar estatísticas agregadas das queries executadas no banco.

É extremamente útil para encontrar:

queries executadas muitas vezes,

queries com alto tempo total,

queries lentas,

e operações que mais consomem recursos.

O raciocínio é:

`EXPLAIN ANALYZE` aprofunda uma consulta específica.

`pg_stat_statements` ajuda a descobrir **quais consultas merecem investigação primeiro**.

---

# 12. Sequential Scan

Sequential Scan significa percorrer a tabela sequencialmente.

Um erro comum é pensar:

**Sequential Scan sempre é ruim.**

Não é.

Se a tabela é pequena ou a consulta precisa retornar grande parte das linhas, ler sequencialmente pode ser mais barato do que acessar um índice e depois buscar milhares de linhas na tabela.

A pergunta correta é:

**para essa quantidade de dados, esse plano faz sentido?**

---

## 13. Index Scan e Bitmap Scan

Index Scan utiliza um índice para encontrar registros e depois acessa as linhas correspondentes na tabela.

É muito interessante quando a consulta possui boa seletividade.

Ou seja:

existem muitas linhas na tabela,

mas precisamos de poucas.

Bitmap Scan aparece frequentemente em um cenário intermediário.

O PostgreSQL encontra várias posições através do índice, monta um bitmap e depois acessa as páginas da tabela de forma mais eficiente.

Regra mental:

**pouquíssimas linhas: Index Scan pode ser ótimo.**

**quantidade intermediária: Bitmap Scan pode ser interessante.**

**grande parte da tabela: Sequential Scan pode ganhar.**

---

# 14. Nested Loop

Nested Loop é um algoritmo de join.

Conceitualmente:

para cada linha de uma tabela,

procura correspondências na outra.

Pode ser excelente quando:

uma das entradas é pequena,

e existe um bom índice na outra tabela.

Mas pode ficar muito caro quando executa milhares ou milhões de buscas internas.

Em `EXPLAIN ANALYZE`, preste bastante atenção em:

**loops.**

---

## 15. Hash Join

Hash Join normalmente cria uma estrutura hash a partir de uma das entradas.

Depois utiliza essa estrutura para procurar correspondências da outra entrada.

É muito eficiente principalmente para joins de igualdade envolvendo conjuntos maiores.

Mentalmente:

uma tabela vira hash,

a outra é percorrida,

e o banco procura correspondências pelo hash.

O trade-off é consumo de memória e, quando não cabe adequadamente, possível uso adicional de disco.

---

## 16. Merge Join

Merge Join trabalha muito bem quando os dois conjuntos estão ordenados pelas colunas utilizadas no join.

O banco percorre os dois conjuntos ordenados simultaneamente.

Pode ser eficiente em grandes volumes.

Mas pode exigir ordenação antes do join quando os dados ainda não estão disponíveis na ordem necessária.

Para entrevista:

**Nested Loop combina bem com poucos registros e bons índices.**

**Hash Join é muito comum para igualdade em conjuntos maiores.**

**Merge Join aproveita entradas ordenadas.**

---

# 17. MVCC

MVCC significa Multi-Version Concurrency Control.

É um dos conceitos centrais do PostgreSQL.

Em vez de fazer toda leitura bloquear escrita e toda escrita bloquear leitura, o PostgreSQL trabalha com versões dos registros e snapshots.

Isso permite alto nível de concorrência.

Em termos simplificados:

uma transação pode enxergar uma versão consistente dos dados enquanto outra está realizando alterações.

Por isso:

**MVCC reduz a necessidade de bloquear leitores contra escritores.** 

---

# 18. Optimistic Lock

Optimistic Lock parte da ideia de que conflitos serão relativamente raros.

Em vez de bloquear o registro desde o começo, permitimos que as transações trabalhem.

Na hora de atualizar, verificamos se outra transação alterou o dado.

Com Hibernate, isso normalmente é implementado com:

`@Version`.

Se duas transações carregaram a versão cinco, a primeira atualiza para seis.

Quando a segunda tenta atualizar esperando ainda a versão cinco, o conflito é detectado.

Isso evita Lost Update. 

Memorize:

**Optimistic Lock detecta conflito.**

---

# 19. Pessimistic Lock

Pessimistic Lock parte da ideia oposta:

**o conflito é provável, então vou bloquear o recurso.**

Um exemplo conceitual é utilizar:

`SELECT FOR UPDATE`.

Outra transação que precise de um lock incompatível poderá ter que esperar.

Pode ser apropriado para operações muito críticas e concorridas.

Mas aumenta:

contenção,

tempo de espera,

e risco de deadlock.

Memorize:

**Optimistic detecta depois.**

**Pessimistic bloqueia antes.**

---

# 20. Lock Contention

Lock contention acontece quando várias transações disputam os mesmos recursos.

Imagine:

```text
Transaction A
     ↓
segura lock

Transaction B ── espera

Transaction C ── espera

Transaction D ── espera
```

Isso pode provocar:

aumento de latência,

queda de throughput,

timeouts,

e filas dentro do banco.

Por isso, transações devem permanecer abertas pelo menor tempo necessário.

No PostgreSQL, `pg_locks` ajuda a investigar locks existentes e contenção. 

---

# 21. Deadlock

Deadlock acontece quando duas ou mais transações ficam esperando umas pelas outras.

Por exemplo:

Transação A bloqueia o registro 1 e espera o registro 2.

Transação B bloqueia o registro 2 e espera o registro 1.

Nenhuma consegue continuar.

PostgreSQL detecta deadlocks e aborta uma das transações para permitir progresso. 

Uma das principais formas de evitar deadlock é:

**adquirir locks sempre em uma ordem consistente.**

Por exemplo, sempre atualizar contas por ID crescente.

---

# Resposta curta para entrevista

Se perguntarem como você trabalha com performance e concorrência no PostgreSQL, uma resposta objetiva seria:

> Para performance, eu começo entendendo o padrão das consultas e escolhendo índices de acordo com ele. B-Tree atende a maioria dos casos de igualdade, range e ordenação. Para estruturas como JSONB, arrays ou full-text, posso avaliar GIN; para casos especializados como dados geográficos e ranges, GiST. Também considero índices compostos, parciais e covering indexes conforme o padrão de acesso.
>
> Para investigar performance, utilizo `pg_stat_statements` para identificar queries relevantes e depois `EXPLAIN ANALYZE` para verificar o plano real, comparando estimated rows com actual rows e analisando operações como Sequential Scan, Index Scan, Bitmap Scan e os diferentes algoritmos de join.
>
> Também não considero Sequential Scan automaticamente um problema. Para tabelas pequenas ou consultas que retornam grande parte dos dados, ele pode ser a decisão correta do planner.
>
> Em concorrência, PostgreSQL utiliza MVCC para permitir que leituras e escritas convivam melhor. Quando preciso evitar Lost Update, posso usar Optimistic Lock com `@Version`; quando preciso bloquear explicitamente um recurso crítico, posso utilizar Pessimistic Lock.
>
> Também monitoro lock contention e deadlocks, mantendo transações curtas e tentando adquirir recursos sempre na mesma ordem.
>
> Então o raciocínio que eu sigo é: **identificar as queries mais relevantes, analisar o plano de execução, escolher índices pelo padrão de acesso e controlar concorrência sem introduzir locks desnecessários**.
