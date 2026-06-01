# Checklist para entrevistas — Estruturas de Dados

---

## 1. Explica como escolher uma estrutura de dados?

> Um desenvolvedor Senior não deve decorar estruturas de dados. Deve saber escolher a estrutura adequada para cada cenário.

Antes de escolher, eu avalio:

| Critério       | Pergunta                        |
| -------------- | ------------------------------- |
| Leitura        | Quantas consultas serão feitas? |
| Escrita        | Quantas inserções/atualizações? |
| Busca          | Preciso buscar por chave?       |
| Ordenação      | Preciso manter ordenado?        |
| Memória        | Qual o volume de dados?         |
| Concorrência   | Haverá múltiplas threads?       |
| Latência       | Qual o SLA/P99 esperado?        |
| Escalabilidade | Vai crescer para milhões?       |

Exemplo:

Se preciso encontrar um usuário pelo ID:

```java
Map<Long, User>
```

Complexidade:

```text
O(1)
```

Não faz sentido usar:

```java
List<User>
```

pois a busca seria:

```text
O(n)
```

---

## 2. Entende complexidade computacional (Big O)?

> Estruturas de dados são avaliadas principalmente pela complexidade das operações.

### Complexidades mais importantes

| Complexidade | Nome         |
| ------------ | ------------ |
| O(1)         | Constante    |
| O(log n)     | Logarítmica  |
| O(n)         | Linear       |
| O(n log n)   | Quase linear |
| O(n²)        | Quadrática   |

---


### Resposta Senior

> Eu não decoro apenas Big O. Eu relaciono complexidade com volume real. Uma busca O(n) em 10 registros é irrelevante. Em 100 milhões de registros pode inviabilizar o sistema.

---

## 3. Sabe quando usar List, Set e Map?

### Definição

| LIST | SET         |Map|
| ------------ | ------------ | ------------ |
|Mantém ordem         |    Ordem com implementação da interface (treeSet, LinkedHashSet) | Associação Chave -> valor
| Permite duplicados     | Não permite duplicados  |
 
### Quando Usar
 | LIST | SET         |Map
| ------------ | ------------ |------------ |
|sequência de elementos        |    Ordem com interface (treeSet, LinkedHashSet) | lookup rápido
| Paginação  |UUnicidade  |cache;
| Ordenação  |Remoção de duplicidade  | indexação.

### Exemplo
 | LIST | SET         |Map
| ------------ | ------------ | ------------ |
| ```List <Order>```   |```Set <Permission>```   |```Map <Long, Customer>```

### Busca
 | LIST | Set         |Map
| ------------ | ------------ | ------------ |
| ```for (User user : users)```   |```Set <Permission>```   |```map.get(id)```
| Complexidade O(n)  |``` Complexidade O(1) no HashSer```   |``` Complexidade O(1)```
|| ``` Complexidade O(logn) no TreeSet``` 

### Resposta Senior

> Se preciso localizar elementos por chave, geralmente uso Map. Se preciso apenas armazenar sequência, uso List. Se preciso garantir unicidade, uso Set.

---

## 4. Entende ArrayList internamente?

### Estrutura

 | Bom | Ruim         |
| ------------ | ------------ |
|     Acesso por indece  O(1)        |  inserção e remoção no meio ou inicio O(n)            | 
|   inserções no final    O(1)        |               | 



Quando adiciona ou remove no meio ou início todos os elementos precisam ser deslocados.


```java
* Não é ideal para filas, pois a fila sendo FIFO, a complexidade pode chegar a O(n).
* Para fila o ieal é ArrayDeque
```
---

## 5. Entende HashMap internamente?
---

### Estrutura
* Estrutura de chave-valoe baseada em tabela hash
  * Internamente tenta transformar chave em indice de array para otimizar o acesso
```text
HashMap
 └── table: Node<K,V>[]
      ├── [0] null
      ├── [1] Node -> Node -> Node
      ├── [2] null
      ├── [3] Node
      └── ...

Cada posição do array é chamada de bucket
Cada elemento armazenado é um NODE
```
* Cada entrada guarda 

```
static class Node<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;
}
``` 

| Campo | Função         |
| ------------ | ------------ |
| Hash         | hash calculado da chave    |
|Key|Chave|
|Value| valor|
|nexr| próximo nó em caso de colisão|

### Complexidade

 Acontece por que o hash leva direto ao bucket
| Operação      | Complexidade média |
| ------------- | -----------------: |
| `put`         |             `O(1)` |
| `get`         |             `O(1)` |
| `remove`      |             `O(1)` |
| `containsKey` |             `O(1)` |
 

---

### Colisão

Colisão acontece quando duas chaves diferentes caem no mesmo bucket.

 * O HashMap resolve a colisão:
    * Lista ligada, inicialmente
    * Arvore Rubro Negra, quando cresce muito

---

### Fluxo resumido

```text
1. Calcula hashCode da chave
2. Mistura os bits do hash
3. Calcula o índice do bucket
4. Se o bucket estiver vazio:
   - cria novo Node
5. Se o bucket já tiver elementos:
   - compara hash
   - compara key com equals
   - se encontrar a mesma chave, atualiza o valor
   - se não encontrar, adiciona novo Node
6. Se passar do limite de carga, redimensiona a tabela
```

---

### Resposta Senior

> O HashMap fornece busca média O(1), mas depende de uma boa distribuição do hash. Muitas colisões degradam desempenho. Desde o Java 8 buckets muito grandes são convertidos para árvores balanceadas.

---

## 6. Entende equals() e hashCode()?

> Pergunta obrigatória para quem usa HashMap e HashSet.

### HashCode
 * Ferramenta usada na jvm para montar uma tabela de hash de modo correto.
    * Nessa tabela são armazenadas infos com um numero hash, calculado com base nas propriedades da informação
    * Permite velocidade ao buscar uma info com base no hash
    * Somente uma info deve estar no `bucket`

  * Resise ou Rehashing:
    * Ao passar de 12 elementos ele redimensiona
    * Os elementos são redistribuidos entre os buckets 

### Equals
 * Quando mains de uma info está no memso bucket temos `COLISÂO` na tabela hash
    * Por meio do Equals define se as infos são iguais ou não
    * A ideia do Equals é garantir que os objetos são iguais segundo suas propriedades
    
### OBS
 * Se duas chaves são iguais por meio do `equals()` elas devem ter o mesmo `hashCode()`
 * Duas chaves podem ter o mesmo hash e não serem iguais
  

## Diferenças
| Estrutura           | Thread-safe | Aceita null | Uso comum                  |
| ------------------- | ----------: | ----------: | -------------------------- |
| `HashMap`           |         Não |         Sim | Uso geral sem concorrência |
| `Hashtable`         | Sim, legado |         Não | Código antigo              |
| `ConcurrentHashMap` |         Sim |         Não | Concorrência real          |

---



### Resposta Senior

> O HashMap em Java é baseado em uma tabela hash, implementada internamente como um array de buckets. Cada bucket armazena nós com hash, chave, valor e referência para o próximo nó. Ao inserir ou buscar uma chave, o Java calcula o hashCode(), aplica uma função de espalhamento e determina o índice do bucket. Em média, operações como put, get e remove são O(1). Em caso de colisão, os elementos ficam em lista ligada, mas desde o Java 8 buckets muito grandes podem ser convertidos em árvore rubro-negra, reduzindo o pior caso de O(n) para O(log n). Para funcionar corretamente, chaves customizadas precisam respeitar o contrato entre equals() e hashCode(): se dois objetos são iguais por equals, devem ter o mesmo hashCode.

---

## 7. Entende árvores?

### Problema

Listas não escalam bem para buscas ordenadas.


---


## 9. Entende filas e pilhas?

### QUEUE

| Implementação         | Estrutura interna       | Ordenação  | Observação
| --------------------- | ----------------------- | ---------- |---------- |
| LinkedList            | Lista duplamente ligada | FIFO       |
| ArrayDeque            | Array circular (conecta o ultimo elemento ao primeiro)          | FIFO       | insere e remove no inicio e fim
| PriorityQueue         | Heap Binário            | Prioridade |Remove pela prioridade definida (ordem natura ou Comparator, nunca por ordem de inserção)
| ConcurrentLinkedQueue | Lista lock-free + Compare and swap (CAS)       | FIFO       | Não bloqueia Threads, não usa Sychronized nem ReentrantLock
| ArrayBlockingQueue    | Array fixo              | FIFO       |Bloqueia a thread, não consome cpu, acorda a thread automaticamente.
| LinkedBlockingQueue   | Lista ligada            | FIFO       |2 locks um para inserção e um remoção. Permite simultaneidade entre produtor e consumidor

#### Big-O comparativo
| Estrutura          | Inserção | Remoção  | Consulta |
| ------------------ | -------- | -------- | -------- |
| Queue (LinkedList) | O(1)     | O(1)     | O(1)     |
| Queue (ArrayDeque) | O(1)     | O(1)     | O(1)     |
| PriorityQueue      | O(log n) | O(log n) | O(1)     |
| Stack              | O(1)     | O(1)     | O(1)     |

### Concorrência
| Estrutura               | Bloqueia Threads? | Limite de tamanho | Uso típico                   |
| ----------------------- | ----------------- | ----------------- | ---------------------------- |
| `ConcurrentLinkedQueue` | Não               | Ilimitado         | Alta concorrência sem espera |
| `LinkedBlockingQueue`   | Sim               | Configurável      | Producer/Consumer            |

| Operação | ConcurrentLinkedQueue | LinkedBlockingQueue |
| -------- | --------------------- | ------------------- |
| offer    | O(1)                  | O(1)                |
| poll     | O(1)                  | O(1)                |
| peek     | O(1)                  | O(1)                |
| put      | Não existe            | O(1)                |
| take     | Não existe            | O(1)                |


ConcurrentLinkedQueue é uma fila concorrente lock-free baseada em CAS. Ela não bloqueia threads e oferece alto throughput para cenários onde produtores e consumidores não precisam esperar uns pelos outros. Já LinkedBlockingQueue implementa BlockingQueue, utiliza locks e conditions internamente, e permite operações bloqueantes como put() e take(). É a escolha clássica para o padrão Producer-Consumer, pois coordena naturalmente threads produtoras e consumidoras sem polling ativo. Em geral, ConcurrentLinkedQueue prioriza desempenho; LinkedBlockingQueue prioriza coordenação entre threads.

---

## 11. Sabe justificar trade-offs?

Essa é a diferença entre Pleno e Senior.

---

| Estrutura      | Estrutura Interna       | Busca           | Inserção no Final | Inserção no Meio | Remoção  | Ordenação | Vantagens                                                                                 | Desvantagens                                                       |
| -------------- | ----------------------- | --------------- | ----------------- | ---------------- | -------- | --------- | ----------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| **ArrayList**  | Array Dinâmico          | O(1) por índice | O(1) amortizado   | O(n)             | O(n)     | Não       | Acesso extremamente rápido por índice, excelente cache locality, baixo consumo de memória | Inserções e remoções no meio exigem deslocamento dos elementos     |
| **LinkedList** | Lista Duplamente Ligada | O(n)            | O(1)              | O(1)*            | O(1)*    | Não       | Inserção e remoção eficientes quando o nó já é conhecido                                  | Busca lenta, alto consumo de memória, péssima cache locality       |
| **HashMap**    | Tabela Hash             | O(1) médio      | O(1)              | N/A              | O(1)     | Não       | Lookup extremamente rápido, ideal para pesquisas por chave                                | Consome mais memória, não mantém ordem, depende de hashCode/equals |
| **TreeMap**    | Árvore Rubro-Negra      | O(log n)        | O(log n)          | O(log n)         | O(log n) | Sim       | Mantém os dados ordenados automaticamente                                                 | Mais lento que HashMap, estrutura mais complexa     

### Consumo de memória

| Estrutura  | Consumo    |
| ---------- | ---------- |
| ArrayList  | Baixo      |
| HashMap    | Médio      |
| TreeMap    | Alto       |
| LinkedList | Muito Alto |

---

### Resposta Senior

> Não existe estrutura melhor. Existe estrutura mais adequada para determinado padrão de acesso, volume, concorrência e requisitos de ordenação.

---

## 12. Relaciona Estruturas de Dados com Arquitetura?

| Problema Arquitetural | Estrutura de Dados | Exemplo Real        |
| --------------------- | ------------------ | ------------------- |
| Cache local           | Hash Table         | ConcurrentHashMap   |
| Cache distribuído     | Hash Table         | Redis               |
| Fila de processamento | Queue              | RabbitMQ, SQS       |
| Scheduler             | Priority Queue     | Quartz              |
| Busca textual         | Inverted Index     | Elasticsearch       |
| Streaming             | Append-Only Log    | Kafka               |
| Banco Relacional      | B-Tree             | PostgreSQL, MySQL   |
| Banco NoSQL           | LSM Tree           | Cassandra, RocksDB  |
| Rate Limiting         | HashMap + Counter  | API Gateway         |
| Leaderboard           | Sorted Set         | Redis               |
| Sessões               | Hash Table         | Redis Session Store |
| Mensageria            | Queue + Log        | Kafka, RabbitMQ     |
