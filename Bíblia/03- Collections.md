Lucas, em **Collections** o nível Senior não é apenas saber escolher `List`, `Set` ou `Map`. Você precisa entender **estrutura de dados, complexidade, consumo de memória, comportamento em colisões e concorrência**.

## 1. Collections — conceitos, trade-offs e casos de uso

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **ArrayList** | `List` baseada internamente em um array redimensionável. Acesso por índice é direto. | Excelente leitura e locality de memória. Inserções/remoções no meio exigem deslocar elementos. Resize exige criar um array maior e copiar referências. | Lista padrão quando há muitas leituras e acesso por índice. |
| **LinkedList** | Lista duplamente encadeada. Cada elemento possui referências para o anterior e o próximo. | Inserção/remoção é barata **quando o nó já foi localizado**, mas busca por índice é `O(n)` e existe overhead de memória por nó. | Deque ou casos específicos com operações frequentes nas extremidades. |
| **HashMap** | Tabela hash baseada em buckets. Utiliza hash da chave para localizar o bucket e `equals` para identificar a chave correta. | `get` e `put` são `O(1)` em média, mas colisões podem degradar desempenho. Usa memória adicional para tabela e nodes. | Busca rápida por chave. |
| **ConcurrentHashMap** | Hash table thread-safe projetada para permitir alto grau de concorrência. Leituras normalmente não exigem um lock global e atualizações são coordenadas em nível mais granular. | Mais overhead e complexidade interna que `HashMap`. Operações envolvendo múltiplos passos devem utilizar APIs atômicas apropriadas. | Cache compartilhado, registry e estado concorrente. |
| **HashSet** | Implementação de `Set` baseada internamente em um `HashMap`. O elemento do Set é armazenado como chave. | Mesmos trade-offs de hashing do `HashMap`. Não mantém ordem. | Eliminar duplicados e testar existência rapidamente. |
| **TreeMap** | `Map` ordenado implementado utilizando uma árvore Red-Black. | `get`, `put` e `remove` são `O(log n)`, mais lentos que um bom `HashMap`, mas mantém ordenação. | Range queries, navegação ordenada, `floorKey`, `ceilingKey`. |
| **PriorityQueue** | Fila de prioridade implementada como heap binário armazenado em array. O elemento de maior prioridade fica na raiz. | Inserir/remover topo custa `O(log n)`. Não significa que iterar sobre ela retorna todos os elementos ordenados. | Scheduler, top K, processamento por prioridade. |
| **CopyOnWriteArrayList** | Variante thread-safe de lista que cria uma nova cópia do array a cada modificação. | Leituras são excelentes, mas escrita é muito cara em CPU e memória. | Coleção pequena com muitas leituras e pouquíssimas alterações, como listeners. |

`ArrayList` é oficialmente uma implementação de array redimensionável; `LinkedList` é duplamente encadeada; `PriorityQueue` usa heap; e `TreeMap` usa árvore Red-Black. 

---

# 2. Como o `HashMap` funciona internamente

Esse é o ponto mais importante do tema.

Pense neste fluxo:

```text
key
 ↓
hashCode()
 ↓
hash
 ↓
índice do bucket
 ↓
Node
 ↓
hash + equals()
 ↓
valor
```

Considere:

```java
map.put("Lucas", 100);
```

Internamente, de maneira simplificada, acontece:

### 1. `hashCode()`

A JVM chama:

```java
key.hashCode()
```

Imagine:

```text
"Lucas".hashCode()
        ↓
     73621234
```

O `hashCode` **não representa diretamente a posição no array**.

---

### 2. Hash spreading

O `HashMap` aplica uma transformação ao `hashCode`.

Simplificando a implementação:

```java
h ^ (h >>> 16)
```

O objetivo é misturar bits altos e baixos do hash.

Isso melhora a distribuição quando o tamanho da tabela utiliza potência de dois. A implementação do OpenJDK faz exatamente esse espalhamento antes de selecionar o bucket. 

---

### 3. Bucket

Internamente existe algo conceitualmente parecido com:

```java
Node<K,V>[] table;
```

Cada posição do array é um **bucket**.

O índice é obtido aproximadamente assim:

```java
index = (table.length - 1) & hash;
```

Então:

```text
hash
 ↓
index = 5

table

0
1
2
3
4
5 → Node
6
7
```

---

# 3. O que é um `Node`

Uma entrada do `HashMap` contém essencialmente:

```text
Node
├── hash
├── key
├── value
└── next
```

No OpenJDK, a estrutura básica realmente possui esses quatro campos. 

Conceitualmente:

```java
class Node<K,V> {

    int hash;

    K key;

    V value;

    Node<K,V> next;
}
```

O `next` existe porque diferentes chaves podem chegar ao mesmo bucket.

Isso nos leva às colisões.

---

# 4. Colisão

Uma colisão acontece quando duas chaves diferentes acabam no mesmo bucket.

Exemplo:

```text
Chave A
  ↓
hash
  ↓
bucket 5


Chave B
  ↓
hash
  ↓
bucket 5
```

O resultado pode inicialmente ser algo parecido com:

```text
bucket 5

Node A
  ↓
Node B
  ↓
Node C
```

Historicamente, isso funciona como uma lista encadeada de nodes.

Por isso o `HashMap` não pode depender apenas de `hashCode`.

---

# 5. `hashCode` x `equals`

Essa relação precisa estar muito clara em entrevista.

Primeiro o `HashMap` utiliza o hash para descobrir **onde procurar**.

Depois utiliza a chave para determinar **qual elemento naquele bucket é realmente o procurado**.

Simplificando:

```text
hashCode
    ↓
localiza bucket

equals
    ↓
identifica chave
```

Por isso existe o contrato:

> Se dois objetos são iguais de acordo com `equals`, eles precisam produzir o mesmo `hashCode`.

Por outro lado:

```text
mesmo hashCode
```

não significa necessariamente:

```text
equals == true
```

Colisões são permitidas.

---

# 6. Exemplo importante

Considere:

```java
public class User {

    private Long id;

    @Override
    public boolean equals(Object obj) {
        // compara id
    }

    @Override
    public int hashCode() {
        return Objects.hash(id);
    }
}
```

Se:

```java
user1.equals(user2)
```

retornar:

```text
true
```

então:

```java
user1.hashCode() == user2.hashCode()
```

também precisa ser verdadeiro.

Caso contrário, estruturas como `HashMap` e `HashSet` podem se comportar incorretamente.

---

# 7. Colisão e árvore Red-Black

Se muitas chaves caírem no mesmo bucket, uma lista encadeada ficaria cara.

Imagine:

```text
bucket

Node
 ↓
Node
 ↓
Node
 ↓
Node
 ↓
Node
 ↓
...
```

A busca poderia chegar a:

```text
O(n)
```

Por isso o Java moderno pode transformar um bucket muito congestionado em uma **árvore Red-Black**.

```text
Antes

Node
 ↓
Node
 ↓
Node
 ↓
Node
```

pode virar:

```text
        Node
       /    \
    Node    Node
    /         \
 Node         Node
```

Isso reduz a busca no bucket, em condições adequadas, para aproximadamente:

```text
O(log n)
```

No JDK 25, o código do `HashMap` mantém `TREEIFY_THRESHOLD = 8`, `UNTREEIFY_THRESHOLD = 6` e `MIN_TREEIFY_CAPACITY = 64`. Assim, um bucket muito congestionado pode ser treeificado quando atinge o limiar, mas tabelas pequenas preferem redimensionar antes de criar a árvore. 

---

# 8. Load Factor

Outro conceito obrigatório:

```text
Load Factor
```

O valor padrão é:

```text
0.75
```

Ele representa o quanto a tabela pode se encher antes de ocorrer um resize.

Exemplo:

```text
capacity = 16
loadFactor = 0.75
```

Threshold:

```text
16 × 0.75 = 12
```

Quando o número de elementos ultrapassa o threshold, o `HashMap` precisa aumentar sua tabela. A documentação oficial define capacidade como número de buckets e confirma `0.75` como o load factor padrão. 

---

# 9. Por que não usar Load Factor 1?

Existe um trade-off.

Load factor maior:

```text
menos memória desperdiçada
```

mas:

```text
mais elementos por bucket
       ↓
mais colisões
       ↓
busca potencialmente mais cara
```

Load factor menor:

```text
mais buckets
 ↓
menos colisões
```

mas:

```text
mais memória
```

O valor `0.75` é utilizado como um bom equilíbrio entre espaço e tempo. 

---

# 10. Resize

Imagine:

```text
HashMap

capacity = 16
threshold = 12
```

Quando o mapa cresce além do threshold:

```text
resize()
```

A tabela aumenta.

Conceitualmente:

```text
Node[16]

     ↓ resize

Node[32]
```

Os elementos precisam ser redistribuídos para a nova tabela.

Isso possui custo.

Portanto, se você já sabe que terá milhões de elementos, pode ser vantajoso informar uma capacidade esperada adequada e evitar vários resizes.

Nas versões atuais existe inclusive:

```java
HashMap.newHashMap(expectedSize);
```

para criar um mapa dimensionado para a quantidade esperada de mappings. 

---

# 11. Complexidades principais

| Collection | Busca | Inserção | Remoção | Observação |
|---|---:|---:|---:|---|
| `ArrayList` | `O(1)` por índice | `O(1)` amortizado no final | `O(n)` no meio | Pode precisar mover elementos |
| `LinkedList` | `O(n)` | `O(1)` se o node já estiver localizado | `O(1)` se localizado | Encontrar o node normalmente custa `O(n)` |
| `HashMap` | `O(1)` média | `O(1)` média | `O(1)` média | Depende de bom hashing |
| `HashSet` | `O(1)` média | `O(1)` média | `O(1)` média | Baseado em `HashMap` |
| `TreeMap` | `O(log n)` | `O(log n)` | `O(log n)` | Mantém ordenação |
| `PriorityQueue` | `peek`: `O(1)` | `O(log n)` | `poll`: `O(log n)` | Heap binário |
| `CopyOnWriteArrayList` | `get`: `O(1)` | `O(n)` | `O(n)` | Escrita copia o array |

Essas complexidades são o modelo que você deve levar para entrevista. Detalhes podem depender da operação específica e da distribuição dos dados.

---

# 12. `ArrayList` x `LinkedList`

Uma pergunta clássica é:

> "Se LinkedList possui inserção O(1), ela é melhor para muitas inserções?"

Não necessariamente.

Suponha:

```java
list.add(50_000, value);
```

Em uma `LinkedList`, primeiro precisamos encontrar a posição:

```text
percorrer nodes
      ↓
O(n)
```

Depois inserir:

```text
O(1)
```

Resultado dominante:

```text
O(n)
```

Além disso, cada node da `LinkedList` precisa manter:

```text
value
previous
next
```

Enquanto `ArrayList` mantém referências contíguas em um array.

Na prática, `ArrayList` é a escolha padrão para grande parte das listas.

---

# 13. `HashSet` internamente

Um detalhe importante de entrevista:

> `HashSet` utiliza `HashMap` internamente.

Conceitualmente:

```java
HashSet<String> set;
```

pode ser imaginado como:

```text
HashMap

elemento → objeto marcador
```

O elemento do `HashSet` vira a **chave** do `HashMap`.

Por isso `HashSet` também depende fortemente de:

```text
hashCode
equals
```

A própria documentação confirma que `HashSet` é respaldado por uma instância de `HashMap`. 

---

# 14. `TreeMap`

`TreeMap` não utiliza hashing como mecanismo principal.

Ele utiliza:

```text
Red-Black Tree
```

Portanto:

```text
HashMap
 ↓
hash
 ↓
O(1) médio
```

enquanto:

```text
TreeMap
 ↓
comparação
 ↓
Red-Black Tree
 ↓
O(log n)
```

O benefício do `TreeMap` é manter as chaves ordenadas.

Isso permite operações como:

```java
firstKey();
lastKey();
floorKey(key);
ceilingKey(key);
higherKey(key);
lowerKey(key);
```

Então:

> Quer apenas lookup rápido? `HashMap`.

> Precisa de ordenação e navegação por faixa? `TreeMap`.

---

# 15. `PriorityQueue`

`PriorityQueue` utiliza um **binary heap**.

Internamente ele também é armazenado em array.

Exemplo conceitual:

```text
       1
      / \
     3   2
    / \
   8   5
```

Para uma min-heap:

```text
parent <= children
```

O menor elemento fica na raiz.

Por isso:

```java
queue.peek();
```

é barato.

Quando inserimos:

```text
novo elemento
     ↓
sift up
```

Quando removemos a raiz:

```text
último elemento vai para raiz
             ↓
         sift down
```

O código atual do OpenJDK utiliza justamente operações `siftUp` e `siftDown`. 

---

# 16. `ConcurrentHashMap`

`HashMap` **não é thread-safe**. A documentação exige sincronização externa caso várias threads o acessem e pelo menos uma modifique estruturalmente o mapa. 

Por isso, em cenários concorrentes existe:

```java
ConcurrentHashMap
```

Ele não simplesmente coloca um:

```java
synchronized
```

em todo o mapa.

A implementação moderna utiliza técnicas mais granulares, combinando operações atômicas e sincronização localizada quando necessário, inclusive estruturas especiais para buckets convertidos em árvores. 

Isso permite:

```text
Thread A ──► bucket A

Thread B ──► bucket B

Thread C ──► leitura

Thread D ──► bucket D
```

com maior concorrência do que um lock global.

---

# 17. Operações compostas em `ConcurrentHashMap`

Mesmo usando uma collection concorrente, pense na atomicidade da **operação inteira**.

Evite raciocinar assim:

```java
if (!map.containsKey(key)) {
    map.put(key, value);
}
```

Entre:

```java
containsKey()
```

e:

```java
put()
```

outra thread pode alterar o mapa.

Prefira:

```java
map.putIfAbsent(key, value);
```

ou:

```java
map.computeIfAbsent(key, this::loadValue);
```

Quando a intenção semântica for essa.

---

# 18. `CopyOnWriteArrayList`

Essa collection é interessante porque utiliza uma estratégia radical:

```text
leitura
 ↓
array atual
 ↓
muito barata
```

Mas uma escrita:

```text
array atual
    ↓
cria novo array
    ↓
copia elementos
    ↓
aplica alteração
    ↓
publica novo array
```

Por isso ela é excelente quando:

```text
leituras >>>>>>>>>>> escritas
```

e ruim quando:

```text
muitas escritas
```

Seu iterator trabalha sobre um snapshot e não enxerga alterações posteriores à criação do iterator. 

Um exemplo clássico é uma lista de:

```text
listeners
observers
configurações raramente alteradas
```

---

# 19. Mapa mental para escolher

```text
Preciso de List?
│
├── uso geral / acesso por índice
│       └── ArrayList
│
├── operações Deque
│       └── LinkedList ou, frequentemente, ArrayDeque
│
└── muitas leituras concorrentes e raríssimas escritas
        └── CopyOnWriteArrayList


Preciso de Map?
│
├── lookup rápido
│       └── HashMap
│
├── acesso concorrente
│       └── ConcurrentHashMap
│
└── chaves ordenadas
        └── TreeMap


Preciso evitar duplicados?
│
└── HashSet


Preciso processar por prioridade?
│
└── PriorityQueue
```

---

# 20. Resposta objetiva para entrevista

Se o entrevistador perguntar **"Como funcionam as principais Collections e principalmente o HashMap?"**, eu responderia:

> As Collections do Java possuem estruturas internas diferentes e por isso têm características de performance diferentes. `ArrayList` utiliza um array redimensionável e oferece acesso por índice em O de um, enquanto `LinkedList` utiliza nodes duplamente encadeados e acesso por posição é O de n.
>
> O `HashMap` é uma tabela hash. Quando adicionamos uma chave, ele obtém o `hashCode`, aplica uma transformação no hash e utiliza esse valor para determinar o bucket. Dentro do bucket, compara o hash e depois a chave, utilizando `equals`, para localizar a entrada correta.
>
> Como chaves diferentes podem chegar ao mesmo bucket, existem colisões. Inicialmente elas podem ser tratadas com nodes encadeados e, quando um bucket fica muito congestionado e a tabela já possui tamanho suficiente, o Java pode convertê-lo em uma árvore Red-Black, reduzindo o pior caso de busca naquele bucket.
>
> `HashMap` trabalha normalmente com complexidade O de um para `get` e `put`, assumindo boa distribuição dos hashes. O load factor padrão é 0,75 e, quando o threshold é ultrapassado, ocorre resize da tabela.
>
> O contrato entre `equals` e `hashCode` é fundamental: objetos iguais precisam produzir o mesmo hash code.
>
> `HashSet` utiliza um `HashMap` internamente. `TreeMap` usa árvore Red-Black e oferece operações O de log n mantendo ordenação. `PriorityQueue` utiliza heap. Para concorrência, `HashMap` não é thread-safe; normalmente utilizaria `ConcurrentHashMap`, aproveitando operações atômicas como `putIfAbsent` e `computeIfAbsent`.
>
> Então eu escolho uma Collection considerando estrutura de dados, complexidade, ordenação, padrão de leitura e escrita e necessidade de concorrência.