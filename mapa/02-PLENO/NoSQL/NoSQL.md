



Lucas, segue um resumo em **formato de mapa mental com tabelas**, em **português e inglês**, sobre:

- Document Store
- Key-Value Store
- Graph Database
- Search Engine

# Versão em Português

## Mapa mental geral

| Tópico central | Ideia principal | Melhor uso | Exemplo de tecnologia |
|---|---|---|---|
| **Document Store** | Armazena dados como documentos, geralmente JSON/BSON | Dados flexíveis, catálogos, perfis, pedidos | MongoDB, Couchbase, DynamoDB |
| **Key-Value Store** | Armazena dados como chave → valor | Cache, sessão, tokens, contadores | Redis, Memcached, DynamoDB |
| **Graph Database** | Armazena dados como nós e relacionamentos | Redes, recomendações, antifraude, conexões complexas | Neo4j, Amazon Neptune |
| **Search Engine** | Otimizado para busca textual e consulta rápida | Pesquisa, filtros, autocomplete, logs | Elasticsearch, OpenSearch, Solr |

---

## 1. Document Store

| Aspecto | Resumo |
|---|---|
| **Conceito** | Banco que armazena registros em formato de documento, geralmente parecido com JSON. |
| **Estrutura mental** | `Coleção → Documento → Campos aninhados` |
| **Modelo de dados** | Flexível. Cada documento pode ter campos diferentes. |
| **Quando usar** | Quando os dados têm estrutura variável ou evoluem com frequência. |
| **Exemplos de uso** | Perfil de usuário, pedido de e-commerce, catálogo de produtos, configurações. |
| **Vantagem principal** | Facilidade para modelar objetos próximos ao formato usado pela aplicação. |
| **Cuidado principal** | Pode gerar duplicação de dados e inconsistência se a modelagem for mal feita. |
| **Exemplo Java/Spring** | Spring Data MongoDB. |

### Exemplo mental

| Conceito | Exemplo |
|---|---|
| Coleção | `clientes` |
| Documento | Um cliente específico |
| Campos | `nome`, `email`, `enderecos`, `preferencias` |

```json
{
  "id": "123",
  "nome": "Lucas",
  "email": "lucas@email.com",
  "enderecos": [
    {
      "cidade": "São Paulo",
      "tipo": "residencial"
    }
  ]
}
```

---

## 2. Key-Value Store

| Aspecto | Resumo |
|---|---|
| **Conceito** | Banco que armazena um valor associado a uma chave única. |
| **Estrutura mental** | `Chave → Valor` |
| **Modelo de dados** | Muito simples e rápido. |
| **Quando usar** | Quando você precisa recuperar dados rapidamente pela chave. |
| **Exemplos de uso** | Cache, sessão de usuário, token JWT bloqueado, rate limit, ranking. |
| **Vantagem principal** | Alta performance para leitura e escrita simples. |
| **Cuidado principal** | Não é ideal para consultas complexas ou relacionamentos. |
| **Exemplo Java/Spring** | Spring Data Redis. |

### Exemplo mental

| Chave | Valor |
|---|---|
| `user:123` | Dados do usuário |
| `session:abc` | Dados da sessão |
| `product:456:cache` | Produto em cache |
| `rate-limit:user:123` | Quantidade de requisições |

```text
user:123 -> {"nome": "Lucas", "plano": "premium"}
```

---

## 3. Graph Database

| Aspecto | Resumo |
|---|---|
| **Conceito** | Banco focado em entidades e seus relacionamentos. |
| **Estrutura mental** | `Nó → Relacionamento → Nó` |
| **Modelo de dados** | Baseado em grafos. |
| **Quando usar** | Quando a relação entre os dados é tão importante quanto os próprios dados. |
| **Exemplos de uso** | Redes sociais, recomendações, dependências, antifraude, permissões complexas. |
| **Vantagem principal** | Excelente para navegar relações profundas. |
| **Cuidado principal** | Pode não ser a melhor escolha para consultas tabulares simples. |
| **Exemplo Java/Spring** | Spring Data Neo4j. |

### Exemplo mental

| Nó 1 | Relacionamento | Nó 2 |
|---|---|---|
| Cliente | comprou | Produto |
| Pessoa | conhece | Pessoa |
| Conta | transferiu para | Conta |
| Usuário | pertence ao | Grupo |

```text
(Lucas) -[:COMPROU]-> (Notebook)
(Lucas) -[:CONHECE]-> (Mariana)
(ContaA) -[:TRANSFERIU]-> (ContaB)
```

---

## 4. Search Engine

| Aspecto | Resumo |
|---|---|
| **Conceito** | Sistema otimizado para busca textual, filtros, ranking e análise de dados. |
| **Estrutura mental** | `Índice → Documento indexado → Consulta` |
| **Modelo de dados** | Dados indexados para busca rápida. |
| **Quando usar** | Quando o usuário precisa pesquisar textos, aplicar filtros e ordenar por relevância. |
| **Exemplos de uso** | Busca de produtos, autocomplete, logs, auditoria, pesquisa em documentos. |
| **Vantagem principal** | Busca rápida, flexível e com relevância textual. |
| **Cuidado principal** | Normalmente não deve ser usado como banco principal transacional. |
| **Exemplo Java/Spring** | Spring Data Elasticsearch, OpenSearch Java Client. |

### Exemplo mental

| Entrada | Uso |
|---|---|
| Texto digitado | Buscar produtos, artigos, usuários |
| Filtros | Preço, categoria, data, status |
| Ranking | Ordenar por relevância |
| Autocomplete | Sugerir termos enquanto o usuário digita |

```text
Busca: "notebook gamer"
Filtros: categoria = eletrônicos, preço < 5000
Resultado: documentos mais relevantes
```

---

## Comparação rápida

| Critério | Document Store | Key-Value Store | Graph Database | Search Engine |
|---|---|---|---|---|
| **Foco principal** | Documentos flexíveis | Acesso rápido por chave | Relacionamentos | Busca textual |
| **Modelo** | JSON/BSON | Chave-valor | Nós e arestas | Índices |
| **Consulta complexa** | Média | Baixa | Alta para relações | Alta para busca |
| **Performance** | Boa | Muito alta | Boa para grafos | Muito alta para pesquisa |
| **Ideal para transações complexas** | Depende | Não | Depende | Não |
| **Exemplo comum** | MongoDB | Redis | Neo4j | Elasticsearch |

---

## Como escolher

| Situação | Melhor opção |
|---|---|
| Preciso guardar objetos flexíveis parecidos com JSON | **Document Store** |
| Preciso de cache extremamente rápido | **Key-Value Store** |
| Preciso analisar conexões entre entidades | **Graph Database** |
| Preciso fazer busca textual avançada | **Search Engine** |
| Preciso de consistência forte e relacionamentos tabulares | Banco relacional como PostgreSQL/MySQL |
| Preciso buscar produtos por texto e filtros | Search Engine + banco principal |
| Preciso salvar sessão de usuário | Key-Value Store |
| Preciso detectar fraude por conexões entre contas | Graph Database |

---

# English Version

## General mind map

| Central topic | Main idea | Best use case | Technology example |
|---|---|---|---|
| **Document Store** | Stores data as documents, usually JSON/BSON | Flexible data, catalogs, profiles, orders | MongoDB, Couchbase, DynamoDB |
| **Key-Value Store** | Stores data as key → value | Cache, sessions, tokens, counters | Redis, Memcached, DynamoDB |
| **Graph Database** | Stores data as nodes and relationships | Networks, recommendations, fraud detection, complex connections | Neo4j, Amazon Neptune |
| **Search Engine** | Optimized for text search and fast querying | Search, filters, autocomplete, logs | Elasticsearch, OpenSearch, Solr |

---

## 1. Document Store

| Aspect | Summary |
|---|---|
| **Concept** | A database that stores records as documents, usually similar to JSON. |
| **Mental structure** | `Collection → Document → Nested fields` |
| **Data model** | Flexible. Each document may have different fields. |
| **When to use** | When data structure changes frequently or is naturally document-based. |
| **Use cases** | User profile, e-commerce order, product catalog, settings. |
| **Main advantage** | Easy to model objects close to the application format. |
| **Main concern** | Can cause data duplication and inconsistency if poorly modeled. |
| **Java/Spring example** | Spring Data MongoDB. |

### Mental example

| Concept | Example |
|---|---|
| Collection | `customers` |
| Document | A specific customer |
| Fields | `name`, `email`, `addresses`, `preferences` |

```json
{
  "id": "123",
  "name": "Lucas",
  "email": "lucas@email.com",
  "addresses": [
    {
      "city": "São Paulo",
      "type": "home"
    }
  ]
}
```

---

## 2. Key-Value Store

| Aspect | Summary |
|---|---|
| **Concept** | A database that stores a value associated with a unique key. |
| **Mental structure** | `Key → Value` |
| **Data model** | Very simple and fast. |
| **When to use** | When you need to retrieve data quickly by key. |
| **Use cases** | Cache, user session, blocked JWT token, rate limit, ranking. |
| **Main advantage** | Very high performance for simple reads and writes. |
| **Main concern** | Not ideal for complex queries or relationships. |
| **Java/Spring example** | Spring Data Redis. |

### Mental example

| Key | Value |
|---|---|
| `user:123` | User data |
| `session:abc` | Session data |
| `product:456:cache` | Cached product |
| `rate-limit:user:123` | Request counter |

```text
user:123 -> {"name": "Lucas", "plan": "premium"}
```

---

## 3. Graph Database

| Aspect | Summary |
|---|---|
| **Concept** | A database focused on entities and their relationships. |
| **Mental structure** | `Node → Relationship → Node` |
| **Data model** | Graph-based. |
| **When to use** | When relationships between data are as important as the data itself. |
| **Use cases** | Social networks, recommendations, dependencies, fraud detection, complex permissions. |
| **Main advantage** | Excellent for traversing deep relationships. |
| **Main concern** | May not be the best option for simple tabular queries. |
| **Java/Spring example** | Spring Data Neo4j. |

### Mental example

| Node 1 | Relationship | Node 2 |
|---|---|---|
| Customer | bought | Product |
| Person | knows | Person |
| Account | transferred to | Account |
| User | belongs to | Group |

```text
(Lucas) -[:BOUGHT]-> (Laptop)
(Lucas) -[:KNOWS]-> (Mariana)
(AccountA) -[:TRANSFERRED_TO]-> (AccountB)
```

---

## 4. Search Engine

| Aspect | Summary |
|---|---|
| **Concept** | A system optimized for text search, filtering, ranking and data exploration. |
| **Mental structure** | `Index → Indexed document → Query` |
| **Data model** | Indexed data for fast search. |
| **When to use** | When users need to search text, apply filters and sort by relevance. |
| **Use cases** | Product search, autocomplete, logs, auditing, document search. |
| **Main advantage** | Fast and flexible search with textual relevance. |
| **Main concern** | Usually should not be used as the main transactional database. |
| **Java/Spring example** | Spring Data Elasticsearch, OpenSearch Java Client. |

### Mental example

| Input | Usage |
|---|---|
| Typed text | Search products, articles, users |
| Filters | Price, category, date, status |
| Ranking | Sort by relevance |
| Autocomplete | Suggest terms while the user types |

```text
Search: "gaming laptop"
Filters: category = electronics, price < 5000
Result: most relevant documents
```

---

## Quick comparison

| Criteria | Document Store | Key-Value Store | Graph Database | Search Engine |
|---|---|---|---|---|
| **Main focus** | Flexible documents | Fast key-based access | Relationships | Text search |
| **Model** | JSON/BSON | Key-value | Nodes and edges | Indexes |
| **Complex queries** | Medium | Low | High for relationships | High for search |
| **Performance** | Good | Very high | Good for graphs | Very high for search |
| **Best for complex transactions** | It depends | No | It depends | No |
| **Common example** | MongoDB | Redis | Neo4j | Elasticsearch |

---

## How to choose

| Situation | Best option |
|---|---|
| I need to store flexible JSON-like objects | **Document Store** |
| I need extremely fast cache | **Key-Value Store** |
| I need to analyze connections between entities | **Graph Database** |
| I need advanced text search | **Search Engine** |
| I need strong consistency and tabular relationships | Relational database like PostgreSQL/MySQL |
| I need to search products by text and filters | Search Engine + main database |
| I need to store user sessions | Key-Value Store |
| I need to detect fraud through account connections | Graph Database |