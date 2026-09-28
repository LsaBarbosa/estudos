# Java Collections

<a id="indice"></a>

## Índice

- [1. Visão Geral](#visao-geral)
- [2. Hierarquia Principal](#hierarquia)
  - [2.1 Collection](#collection)
  - [2.2 List](#list)
  - [2.3 Set](#set)
  - [2.4 Queue](#queue)
  - [2.5 Deque](#deque)
  - [2.6 Map](#map)
- [3. List](#list-detalhes)
  - [3.1 ArrayList](#arraylist)
  - [3.2 LinkedList](#linkedlist)
  - [3.3 ArrayList vs LinkedList](#arraylist-vs-linkedlist)
- [4. Set](#set-detalhes)
  - [4.1 HashSet](#hashset)
  - [4.2 LinkedHashSet](#linkedhashset)
  - [4.3 TreeSet](#treeset)
  - [4.4 HashSet vs LinkedHashSet vs TreeSet](#comparacao-set)
- [5. Map](#map-detalhes)
  - [5.1 HashMap](#hashmap)
  - [5.2 LinkedHashMap](#linkedhashmap)
  - [5.3 TreeMap](#treemap)
  - [5.4 HashMap vs LinkedHashMap vs TreeMap](#comparacao-map)
- [6. Queue e Deque](#queue-deque)
  - [6.1 PriorityQueue](#priorityqueue)
  - [6.2 ArrayDeque](#arraydeque)
- [7. Hashing: equals e hashCode](#equals-hashcode)
  - [7.1 Contrato de equals](#equals)
  - [7.2 Contrato de hashCode](#hashcode)
  - [7.3 Colisões](#colisoes)
- [8. Internals do HashMap](#hashmap-internals)
  - [8.1 Buckets](#buckets)
  - [8.2 Load Factor](#load-factor)
  - [8.3 Resize](#resize)
  - [8.4 Treeification](#treeification)
- [9. Ordenação](#ordenacao)
  - [9.1 Comparable](#comparable)
  - [9.2 Comparator](#comparator)
- [10. Iteração](#iteracao)
  - [10.1 Iterator](#iterator)
  - [10.2 Fail-fast](#fail-fast)
  - [10.3 Remoção segura](#remocao-segura)
- [11. Imutabilidade e Views](#imutabilidade)
  - [11.1 Collections.unmodifiableList](#unmodifiable)
  - [11.2 List.of / Set.of / Map.of](#factory-methods)
  - [11.3 Arrays.asList](#arrays-as-list)
- [12. Collections Concorrentes](#collections-concorrentes)
  - [12.1 ConcurrentHashMap](#concurrenthashmap)
  - [12.2 CopyOnWriteArrayList](#copyonwritearraylist)
  - [12.3 BlockingQueue](#blockingqueue)
- [13. Complexidade das Principais Estruturas](#complexidade)
- [14. Como escolher a Collection](#escolha)
- [15. Armadilhas comuns de entrevista](#armadilhas)
- [16. Cenários de Produção](#producao)
- [17. Resposta Completa de Entrevista](#resposta-completa)
- [18. Mapa Mental](#mapa-mental)

---

<a id="visao-geral"></a>
# 1. Visão Geral

O **Java Collections Framework** fornece interfaces e implementações para armazenar, organizar e manipular grupos de objetos.

Os principais grupos são:

```text
Collection
│
├── List
├── Set
└── Queue
    └── Deque

Map
```

> **Importante:** `Map` faz parte do Collections Framework, mas **não herda de `Collection`**.

A escolha da estrutura deve considerar principalmente:

- necessidade de ordem;
- duplicidade;
- busca por chave;
- custo de inserção e remoção;
- acesso por índice;
- ordenação;
- concorrência;
- volume de dados.

### Resposta de entrevista

> O Java Collections Framework fornece estruturas de dados padronizadas para armazenar e manipular objetos. As principais abstrações são List, Set, Queue, Deque e Map. A escolha entre elas depende de requisitos como ordem, duplicidade, acesso por índice, busca por chave, ordenação e concorrência.

[↑ Voltar ao índice](#indice)

---

<a id="hierarquia"></a>
# 2. Hierarquia Principal

```text
Iterable
   │
Collection
   │
   ├── List
   │    ├── ArrayList
   │    └── LinkedList
   │
   ├── Set
   │    ├── HashSet
   │    ├── LinkedHashSet
   │    └── SortedSet
   │         └── NavigableSet
   │              └── TreeSet
   │
   └── Queue
        ├── PriorityQueue
        └── Deque
             ├── ArrayDeque
             └── LinkedList

Map
├── HashMap
├── LinkedHashMap
└── SortedMap
     └── NavigableMap
          └── TreeMap
```

<a id="collection"></a>
## 2.1 Collection

`Collection<E>` representa um grupo de elementos.

Operações comuns:

```java
add()
remove()
contains()
size()
isEmpty()
clear()
iterator()
```

---

<a id="list"></a>
## 2.2 List

Características:

- mantém sequência;
- permite elementos duplicados;
- permite acesso por índice;
- normalmente preserva ordem de inserção.

Exemplos:

```java
ArrayList
LinkedList
```

---

<a id="set"></a>
## 2.3 Set

Características:

- não permite elementos duplicados segundo `equals`;
- não fornece acesso por índice.

Exemplos:

```java
HashSet
LinkedHashSet
TreeSet
```

---

<a id="queue"></a>
## 2.4 Queue

Representa estruturas orientadas ao processamento de elementos.

Operações importantes:

```java
offer()
poll()
peek()
```

Em geral, é preferível usar essas operações às alternativas que lançam exceção:

| Retorna valor especial | Lança exceção |
|---|---|
| `offer()` | `add()` |
| `poll()` | `remove()` |
| `peek()` | `element()` |

---

<a id="deque"></a>
## 2.5 Deque

`Deque` significa **Double Ended Queue**.

Permite inserir e remover dos dois lados:

```java
addFirst()
addLast()
removeFirst()
removeLast()
peekFirst()
peekLast()
```

Pode funcionar como:

```text
Queue
ou
Stack
```

---

<a id="map"></a>
## 2.6 Map

Armazena pares:

```text
chave → valor
```

Exemplo:

```java
Map<Long, Cliente> clientes;
```

A chave é utilizada para localizar o valor.

Características:

- chaves são únicas;
- valores podem ser repetidos;
- cada implementação possui garantias diferentes de ordem e complexidade.

### Resposta de entrevista

> List representa uma sequência e permite duplicados. Set representa elementos únicos. Queue e Deque são voltadas ao processamento ordenado de elementos. Map representa associações chave-valor e não herda de Collection.

[↑ Voltar ao índice](#indice)

---

<a id="list-detalhes"></a>
# 3. List

<a id="arraylist"></a>
## 3.1 ArrayList

`ArrayList` utiliza internamente um **array redimensionável**.

```java
List<String> nomes = new ArrayList<>();
```

### Vantagens

- acesso por índice rápido;
- boa localidade de memória;
- iteração eficiente;
- normalmente é a implementação padrão de `List`.

### Complexidade típica

```text
get(index)          → O(1)
add(final)          → O(1) amortizado
contains            → O(n)
remove(final)       → O(1)
insert/remover meio → O(n)
```

Inserções ou remoções no meio exigem deslocamento de elementos.

### Resposta de entrevista

> ArrayList é baseada em array dinâmico. Possui acesso por índice em O(1) e inserção no final em O(1) amortizado. Inserções e remoções no meio são O(n) porque podem exigir deslocamento dos elementos.

---

<a id="linkedlist"></a>
## 3.2 LinkedList

`LinkedList` é uma **lista duplamente encadeada**.

Cada nó mantém referências para:

```text
anterior ← nó → próximo
```

Também implementa `Deque`.

### Complexidade típica

```text
acesso por índice → O(n)
inserção nas pontas → O(1)
remoção nas pontas  → O(1)
```

### Trade-off

Cada elemento possui overhead adicional devido às referências entre os nós.

Além disso, possui pior localidade de memória que um `ArrayList`.

### Resposta de entrevista

> LinkedList é uma lista duplamente encadeada. É eficiente para operações nas extremidades, mas acesso por índice é O(n). Na prática, ArrayList costuma ser preferível para listas gerais por ter melhor localidade de memória e menor overhead.

---

<a id="arraylist-vs-linkedlist"></a>
## 3.3 ArrayList vs LinkedList

| Característica | ArrayList | LinkedList |
|---|---|---|
| Estrutura | Array dinâmico | Lista duplamente encadeada |
| `get(index)` | O(1) | O(n) |
| Inserir no final | O(1) amortizado | O(1) |
| Inserir no início | O(n) | O(1) |
| Memória | Menor overhead | Maior overhead |
| Cache locality | Melhor | Pior |
| Implementa Deque | Não | Sim |

### Regra prática

> Para uma `List` comum, comece considerando `ArrayList`. Use `LinkedList` quando as características de deque/lista encadeada realmente forem relevantes.

[↑ Voltar ao índice](#indice)

---

<a id="set-detalhes"></a>
# 4. Set

<a id="hashset"></a>
## 4.1 HashSet

`HashSet` armazena elementos únicos utilizando hashing.

Internamente é baseado em uma estrutura equivalente a um `HashMap`.

Características:

- não garante ordem de iteração;
- busca, inserção e remoção normalmente O(1);
- depende corretamente de `equals()` e `hashCode()`.

```java
Set<String> ids = new HashSet<>();
```

### Resposta de entrevista

> HashSet é indicado quando preciso garantir unicidade e não preciso de ordenação. Em condições normais, add, contains e remove possuem custo próximo de O(1).

---

<a id="linkedhashset"></a>
## 4.2 LinkedHashSet

Mantém a unicidade do `Set`, mas também preserva a **ordem de inserção**.

```java
Set<String> nomes = new LinkedHashSet<>();
```

### Trade-off

Possui overhead adicional para manter a ordem.

---

<a id="treeset"></a>
## 4.3 TreeSet

Mantém os elementos **ordenados**.

Baseia-se em uma árvore balanceada.

Complexidade típica:

```text
add      → O(log n)
remove   → O(log n)
contains → O(log n)
```

A ordenação pode vir de:

```java
Comparable
```

ou:

```java
Comparator
```

### Resposta de entrevista

> TreeSet mantém os elementos ordenados e oferece operações em O(log n). Eu usaria quando preciso simultaneamente de unicidade e ordenação.

---

<a id="comparacao-set"></a>
## 4.4 HashSet vs LinkedHashSet vs TreeSet

| Estrutura | Duplicados | Ordem | Complexidade típica |
|---|---:|---|---|
| HashSet | Não | Não garantida | O(1) |
| LinkedHashSet | Não | Inserção | O(1) |
| TreeSet | Não | Ordenada | O(log n) |

### Regra mental

```text
unicidade apenas        → HashSet
unicidade + inserção    → LinkedHashSet
unicidade + ordenação   → TreeSet
```

[↑ Voltar ao índice](#indice)

---

<a id="map-detalhes"></a>
# 5. Map

<a id="hashmap"></a>
## 5.1 HashMap

`HashMap` armazena associações:

```text
chave → valor
```

```java
Map<Long, Cliente> clientes = new HashMap<>();
```

Características:

- não garante ordem;
- permite uma chave `null`;
- permite valores `null`;
- busca média por chave próxima de O(1);
- depende do contrato de `equals()` e `hashCode()`.

### Resposta de entrevista

> HashMap é uma estrutura chave-valor baseada em hashing. Em condições normais, get e put são O(1) em média. A qualidade de hashCode e o contrato com equals são fundamentais para funcionamento correto e desempenho.

---

<a id="linkedhashmap"></a>
## 5.2 LinkedHashMap

Mantém as características de `HashMap`, adicionando uma estrutura ligada para controlar ordem.

Pode preservar:

- ordem de inserção;
- ordem de acesso, se configurado.

```java
Map<K, V> map = new LinkedHashMap<>();
```

Também pode ser útil como base para estratégias simples de **LRU cache**.

### Trade-off

Consome mais memória que `HashMap` para manter a ordem.

---

<a id="treemap"></a>
## 5.3 TreeMap

Mantém as chaves ordenadas.

Complexidade:

```text
put    → O(log n)
get    → O(log n)
remove → O(log n)
```

Permite operações navegacionais:

```java
firstKey()
lastKey()
floorKey()
ceilingKey()
higherKey()
lowerKey()
```

### Resposta de entrevista

> TreeMap é indicado quando preciso de um mapa cujas chaves permaneçam ordenadas e quando operações de navegação por intervalo são importantes.

---

<a id="comparacao-map"></a>
## 5.4 HashMap vs LinkedHashMap vs TreeMap

| Estrutura | Ordem | Busca típica | Uso comum |
|---|---|---:|---|
| HashMap | Não garantida | O(1) | lookup geral |
| LinkedHashMap | Inserção/acesso | O(1) | ordem previsível / LRU |
| TreeMap | Ordenada | O(log n) | intervalos e navegação |

### Regra mental

```text
lookup rápido            → HashMap
lookup + ordem           → LinkedHashMap
lookup + ordenação       → TreeMap
```

[↑ Voltar ao índice](#indice)

---

<a id="queue-deque"></a>
# 6. Queue e Deque

<a id="priorityqueue"></a>
## 6.1 PriorityQueue

`PriorityQueue` organiza elementos por prioridade.

Normalmente utiliza um **heap binário**.

```java
Queue<Integer> fila = new PriorityQueue<>();
```

O elemento retornado por:

```java
peek()
poll()
```

é o de maior prioridade segundo sua ordenação.

### Importante

Iterar sobre uma `PriorityQueue` **não significa receber todos os elementos em ordem de prioridade**.

### Complexidade típica

```text
peek   → O(1)
offer  → O(log n)
poll   → O(log n)
```

### Resposta de entrevista

> PriorityQueue é adequada quando preciso recuperar repetidamente o elemento de maior prioridade. Ela não representa uma lista totalmente ordenada para iteração.

---

<a id="arraydeque"></a>
## 6.2 ArrayDeque

Implementação eficiente de `Deque`.

É normalmente preferível a `Stack` para implementar uma pilha moderna.

Como stack:

```java
Deque<String> stack = new ArrayDeque<>();

stack.push("A");
stack.pop();
stack.peek();
```

Como fila:

```java
queue.offerLast(valor);
queue.pollFirst();
```

### Resposta de entrevista

> ArrayDeque é uma estrutura eficiente para fila ou pilha. Para implementar uma stack moderna em Java, geralmente prefiro ArrayDeque em vez da classe legada Stack.

[↑ Voltar ao índice](#indice)

---

<a id="equals-hashcode"></a>
# 7. Hashing: equals e hashCode

Esse é um dos pontos mais importantes de Collections em entrevistas.

<a id="equals"></a>
## 7.1 Contrato de equals

`equals()` define **igualdade lógica**.

O contrato inclui:

- reflexivo;
- simétrico;
- transitivo;
- consistente;
- `x.equals(null)` deve retornar `false`.

Exemplo:

```java
cliente1.equals(cliente2)
```

pode representar que dois objetos diferentes em memória representam logicamente o mesmo cliente.

---

<a id="hashcode"></a>
## 7.2 Contrato de hashCode

Regra fundamental:

```text
se a.equals(b) == true
então
a.hashCode() == b.hashCode()
```

O inverso não precisa ser verdadeiro:

```text
mesmo hashCode
≠
necessariamente iguais
```

### Problema clássico

Se sobrescrever:

```java
equals()
```

normalmente também deve sobrescrever:

```java
hashCode()
```

Caso contrário, estruturas como:

```java
HashMap
HashSet
```

podem se comportar incorretamente.

### Resposta de entrevista

> equals define igualdade lógica e hashCode determina a distribuição em estruturas baseadas em hashing. Se dois objetos são iguais segundo equals, obrigatoriamente devem produzir o mesmo hashCode.

---

<a id="colisoes"></a>
## 7.3 Colisões

Colisão ocorre quando objetos diferentes produzem hashes que levam ao mesmo bucket.

```text
Objeto A ─┐
          ├── bucket X
Objeto B ─┘
```

Isso é esperado em estruturas hash.

O `HashMap` precisa então distinguir as chaves utilizando também `equals()`.

### Boa implementação de hashCode

Busca distribuir os objetos adequadamente entre buckets.

Um hashCode ruim pode gerar:

```text
muitas colisões
      ↓
mais comparação
      ↓
queda de desempenho
```

[↑ Voltar ao índice](#indice)

---

<a id="hashmap-internals"></a>
# 8. Internals do HashMap

<a id="buckets"></a>
## 8.1 Buckets

Conceitualmente:

```text
hashCode da chave
       ↓
transformação do hash
       ↓
índice do bucket
       ↓
bucket
```

Um bucket pode conter mais de uma entrada devido a colisões.

---

<a id="load-factor"></a>
## 8.2 Load Factor

O `HashMap` possui um **load factor**, cujo valor padrão normalmente é:

```text
0.75
```

Ele define quando a estrutura deve expandir sua capacidade.

Conceitualmente:

```text
threshold = capacity × loadFactor
```

Quando o número de elementos ultrapassa o threshold, pode ocorrer resize.

### Trade-off

Load factor maior:

- menos memória;
- potencialmente mais colisões.

Load factor menor:

- mais memória;
- potencialmente menos colisões.

---

<a id="resize"></a>
## 8.3 Resize

Quando a capacidade não é mais suficiente, o `HashMap` aumenta sua tabela interna.

Resize pode ter custo relevante porque a estrutura precisa reorganizar suas entradas.

### Implicação prática

Se o volume esperado é conhecido, informar uma capacidade inicial adequada pode reduzir resizes desnecessários.

Mas:

> não faça tuning de capacidade sem necessidade ou medição.

---

<a id="treeification"></a>
## 8.4 Treeification

Em implementações modernas do `HashMap`, buckets com muitas colisões podem ser convertidos de estrutura encadeada para uma árvore balanceada.

Conceitualmente:

```text
poucas colisões
     ↓
lista de entradas

muitas colisões
     ↓
árvore balanceada
```

Isso evita degradação extrema da busca.

### Detalhe de entrevista

Os thresholds clássicos do `HashMap` moderno incluem:

```text
TREEIFY_THRESHOLD = 8
UNTREEIFY_THRESHOLD = 6
MIN_TREEIFY_CAPACITY = 64
```

Não é necessário memorizar esses números para usar Collections corretamente, mas eles aparecem em entrevistas mais profundas.

### Resposta de entrevista

> HashMap distribui entradas em buckets utilizando hashCode. Quando existem colisões, equals é usado para distinguir as chaves. Em implementações modernas, buckets excessivamente congestionados podem ser convertidos em árvores para evitar degradação severa.

[↑ Voltar ao índice](#indice)

---

<a id="ordenacao"></a>
# 9. Ordenação

<a id="comparable"></a>
## 9.1 Comparable

Define a **ordem natural** da própria classe.

```java
class Cliente implements Comparable<Cliente> {

    @Override
    public int compareTo(Cliente outro) {
        return this.nome.compareTo(outro.nome);
    }
}
```

Utilizado quando existe uma ordenação considerada natural para o tipo.

---

<a id="comparator"></a>
## 9.2 Comparator

Define ordenação **externamente** ao objeto.

```java
Comparator<Cliente> porIdade =
        Comparator.comparingInt(Cliente::getIdade);
```

Permite múltiplas estratégias:

```java
Comparator.comparing(Cliente::getNome)
          .thenComparing(Cliente::getIdade);
```

### Comparable vs Comparator

| Comparable | Comparator |
|---|---|
| Implementado pela própria classe | Externo à classe |
| `compareTo()` | `compare()` |
| Ordem natural | Estratégias múltiplas |

### Resposta de entrevista

> Comparable define a ordenação natural do tipo. Comparator define estratégias externas e permite múltiplas formas de ordenação sem modificar a classe.

[↑ Voltar ao índice](#indice)

---

<a id="iteracao"></a>
# 10. Iteração

<a id="iterator"></a>
## 10.1 Iterator

`Iterator` fornece navegação sequencial:

```java
Iterator<String> iterator = lista.iterator();

while (iterator.hasNext()) {
    String valor = iterator.next();
}
```

---

<a id="fail-fast"></a>
## 10.2 Fail-fast

Muitas collections tradicionais detectam modificações estruturais inesperadas durante iteração.

Exemplo problemático:

```java
for (String item : lista) {
    lista.remove(item);
}
```

Pode ocorrer:

```text
ConcurrentModificationException
```

### Importante

`ConcurrentModificationException` **não significa necessariamente acesso por múltiplas threads**.

Também pode acontecer em código single-thread quando a collection é modificada de forma incompatível durante a iteração.

---

<a id="remocao-segura"></a>
## 10.3 Remoção segura

Com `Iterator`:

```java
Iterator<String> iterator = lista.iterator();

while (iterator.hasNext()) {
    String item = iterator.next();

    if (deveRemover(item)) {
        iterator.remove();
    }
}
```

Ou utilizando:

```java
lista.removeIf(this::deveRemover);
```

### Resposta de entrevista

> Muitas collections utilizam iteradores fail-fast. Modificar diretamente a estrutura durante uma iteração pode gerar ConcurrentModificationException. Para remoção durante iteração posso usar Iterator.remove ou operações apropriadas da própria Collection.

[↑ Voltar ao índice](#indice)

---

<a id="imutabilidade"></a>
# 11. Imutabilidade e Views

<a id="unmodifiable"></a>
## 11.1 Collections.unmodifiableList

```java
List<String> original = new ArrayList<>();
List<String> view = Collections.unmodifiableList(original);
```

A `view` não permite:

```java
view.add(...)
```

Mas se `original` mudar:

```java
original.add("A");
```

a mudança pode aparecer na `view`.

Portanto:

> `unmodifiable` não significa necessariamente objeto profundamente imutável.

---

<a id="factory-methods"></a>
## 11.2 List.of / Set.of / Map.of

Exemplo:

```java
List<String> nomes = List.of("Ana", "João");
```

A coleção retornada não permite modificações estruturais:

```java
nomes.add("Lucas"); // UnsupportedOperationException
```

Essas factories são úteis para representar coleções fixas.

### Atenção

Elas também possuem restrições específicas, como não aceitar `null`.

---

<a id="arrays-as-list"></a>
## 11.3 Arrays.asList

```java
List<String> lista = Arrays.asList("A", "B", "C");
```

O tamanho fica vinculado ao array.

É possível:

```java
lista.set(0, "X");
```

Mas normalmente não:

```java
lista.add("D");
lista.remove("A");
```

Essas operações geram:

```text
UnsupportedOperationException
```

### Armadilha clássica

```java
Arrays.asList(...)
```

não cria um `ArrayList` redimensionável comum.

### Resposta de entrevista

> Collections.unmodifiableList cria uma visão não modificável da coleção original, enquanto List.of cria uma coleção estruturalmente imutável. Arrays.asList cria uma lista de tamanho fixo ligada ao array original.

[↑ Voltar ao índice](#indice)

---

<a id="collections-concorrentes"></a>
# 12. Collections Concorrentes

<a id="concurrenthashmap"></a>
## 12.1 ConcurrentHashMap

Mapa thread-safe otimizado para concorrência.

```java
ConcurrentHashMap<String, Cliente> cache =
        new ConcurrentHashMap<>();
```

Operações importantes:

```java
putIfAbsent()
compute()
computeIfAbsent()
merge()
```

Evita serializar todo acesso em um único lock global.

### Importante

Prefira operações atômicas da própria API.

Evite:

```java
if (!map.containsKey(key)) {
    map.put(key, value);
}
```

Prefira:

```java
map.putIfAbsent(key, value);
```

---

<a id="copyonwritearraylist"></a>
## 12.2 CopyOnWriteArrayList

Em cada modificação relevante, cria uma nova cópia do array interno.

Excelente quando:

```text
muitas leituras
pouquíssimas escritas
```

Ruim quando:

```text
muitas escritas
```

porque copiar o array possui custo relevante.

### Uso comum

- listas de listeners;
- configurações que mudam raramente;
- estruturas read-mostly.

### Resposta de entrevista

> CopyOnWriteArrayList favorece workloads com muitas leituras e poucas escritas. Leituras são simples, mas cada modificação exige copiar a estrutura interna, então não é adequada para alta taxa de escrita.

---

<a id="blockingqueue"></a>
## 12.3 BlockingQueue

Representa uma fila capaz de bloquear produtores ou consumidores.

Exemplo:

```java
BlockingQueue<Evento> queue =
        new ArrayBlockingQueue<>(100);
```

Consumidor:

```java
Evento evento = queue.take();
```

Se estiver vazia:

```text
consumidor espera
```

Produtor:

```java
queue.put(evento);
```

Se estiver cheia:

```text
produtor espera
```

Muito útil em:

```text
Producer / Consumer
```

### Resposta de entrevista

> BlockingQueue é útil para coordenação entre produtores e consumidores, porque fornece operações que podem bloquear quando a fila está vazia ou cheia, ajudando também no controle de backpressure local.

[↑ Voltar ao índice](#indice)

---

<a id="complexidade"></a>
# 13. Complexidade das Principais Estruturas

| Estrutura | Busca | Inserção | Remoção | Ordenada |
|---|---:|---:|---:|---|
| ArrayList | O(n) | O(1) amortizado no final | O(n) no meio | Não |
| LinkedList | O(n) | O(1) nas pontas | O(1) nas pontas | Não |
| HashSet | O(1) médio | O(1) médio | O(1) médio | Não |
| LinkedHashSet | O(1) médio | O(1) médio | O(1) médio | Inserção |
| TreeSet | O(log n) | O(log n) | O(log n) | Sim |
| HashMap | O(1) médio | O(1) médio | O(1) médio | Não |
| LinkedHashMap | O(1) médio | O(1) médio | O(1) médio | Inserção/acesso |
| TreeMap | O(log n) | O(log n) | O(log n) | Sim |
| PriorityQueue | O(n)* | O(log n) | O(log n)** | Prioridade |

\* Busca arbitrária.  
\** `poll()` para remover o elemento prioritário.

### Observação importante

Big-O não é tudo.

Na prática também importam:

```text
cache locality
alocações
overhead por objeto
concorrência
volume de dados
padrão de acesso
```

[↑ Voltar ao índice](#indice)

---

<a id="escolha"></a>
# 14. Como escolher a Collection

Use esta sequência mental:

```text
Preciso chave → valor?
        │
        ├── Sim → Map
        │          │
        │          ├── sem ordem → HashMap
        │          ├── ordem inserção → LinkedHashMap
        │          └── ordenado → TreeMap
        │
        └── Não
            │
            ├── Preciso unicidade?
            │      │
            │      ├── Sim
            │      │    ├── sem ordem → HashSet
            │      │    ├── inserção → LinkedHashSet
            │      │    └── ordenado → TreeSet
            │      │
            │      └── Não
            │           │
            │           ├── acesso por índice → ArrayList
            │           ├── fila/pilha → ArrayDeque
            │           └── prioridade → PriorityQueue
```

### Resposta de entrevista

> Eu escolho a Collection a partir do padrão de acesso e das garantias necessárias. Para lookup por chave geralmente começo com HashMap, para sequência com ArrayList, para unicidade com HashSet, para ordenação com TreeMap ou TreeSet e para fila ou pilha com ArrayDeque. Depois considero concorrência e requisitos específicos.

[↑ Voltar ao índice](#indice)

---

<a id="armadilhas"></a>
# 15. Armadilhas comuns de entrevista

## `HashMap` é thread-safe?

```text
Não.
```

Para uso concorrente, considere:

```java
ConcurrentHashMap
```

---

## `Map` herda de `Collection`?

```text
Não.
```

Ambos fazem parte do Collections Framework, mas possuem hierarquias diferentes.

---

## `HashSet` usa `HashMap` internamente?

Conceitualmente e na implementação padrão moderna:

```text
Sim.
```

Os elementos do Set são armazenados como chaves do mapa.

---

## `ArrayList` é sempre melhor que `LinkedList`?

Não.

Mas para listas gerais, `ArrayList` costuma ser melhor devido a:

- acesso aleatório;
- menor overhead;
- melhor localidade de memória.

---

## `HashMap` mantém ordem?

```text
Não há garantia de ordem.
```

Se ordem de inserção for necessária:

```java
LinkedHashMap
```

---

## `TreeMap` é mais rápido que `HashMap`?

Não para lookup geral.

```text
HashMap → O(1) médio
TreeMap → O(log n)
```

Use `TreeMap` quando a **ordenação ou navegação por chave** for necessária.

---

## Se dois objetos têm mesmo hashCode, eles são iguais?

```text
Não.
```

Podem ser apenas uma colisão.

Mas:

```text
equals == true
→ hashCode obrigatoriamente igual
```

---

## `ConcurrentModificationException` significa múltiplas threads?

```text
Não necessariamente.
```

Pode ocorrer até em uma única thread por modificação estrutural inadequada durante iteração.

---

## `Collections.unmodifiableList` é imutável?

Não necessariamente.

Ela impede alterações através daquela referência, mas pode refletir mudanças feitas na coleção original.

[↑ Voltar ao índice](#indice)

---

<a id="producao"></a>
# 16. Cenários de Produção

## Cenário 1 — Lookup frequente por ID

```java
List<Cliente>
```

com busca frequente:

```java
clientes.stream()
        .filter(c -> c.getId().equals(id))
```

Custo aproximado:

```text
O(n)
```

Se o lookup por ID domina o workload:

```java
Map<Long, Cliente>
```

pode ser mais adequado:

```text
O(1) médio
```

---

## Cenário 2 — Remover duplicados

Em vez de:

```java
List
+
contains repetidamente
```

considere:

```java
HashSet
```

se ordem não for requisito.

---

## Cenário 3 — Cache simples com ordem de acesso

`LinkedHashMap` configurado com access order pode ser usado como base para um LRU simples.

Mas em produção:

> para caching real, normalmente prefira bibliotecas especializadas com política de expiração, limite, métricas e concorrência apropriadas.

---

## Cenário 4 — Múltiplas threads acessando HashMap

Não faça simplesmente:

```java
Map<K, V> map = new HashMap<>();
```

se existir mutação concorrente sem coordenação.

Considere:

```java
ConcurrentHashMap
```

e operações atômicas como:

```java
computeIfAbsent()
putIfAbsent()
merge()
```

---

## Cenário 5 — Lista muito lida e raramente alterada

Considere:

```java
CopyOnWriteArrayList
```

Mas somente quando a proporção for claramente:

```text
leituras >>> escritas
```

---

## Cenário 6 — Producer / Consumer

Utilize:

```java
BlockingQueue
```

para coordenação local entre produtores e consumidores.

Isso evita criar loops manuais do tipo:

```java
while (queue.isEmpty()) {
}
```

que desperdiçam CPU.

---

## Cenário 7 — Latência degradando com collection grande

Investigue:

- escolha incorreta da estrutura;
- hashCode ruim;
- excesso de colisões;
- resize excessivo;
- cópias desnecessárias;
- complexidade O(n) escondida;
- ordenação desnecessária;
- contenção concorrente.

### Resposta de entrevista

> Em produção eu não escolho Collection apenas pelo tipo de dado, mas pelo padrão de acesso. Se há lookup frequente por chave, por exemplo, uma List pode gerar O(n) repetidamente enquanto um HashMap oferece O(1) médio. Também considero memória, ordenação, taxa de escrita, concorrência e volume.

[↑ Voltar ao índice](#indice)

---

<a id="resposta-completa"></a>
# 17. Resposta Completa de Entrevista

Se perguntarem:

**“O que você entende sobre Collections em Java?”**

Uma resposta objetiva pode ser:

> O Java Collections Framework fornece estruturas padronizadas para armazenar e manipular objetos. As principais abstrações são List, Set, Queue, Deque e Map, lembrando que Map faz parte do framework, mas não herda de Collection.
>
> Para listas, ArrayList normalmente é minha primeira opção por oferecer acesso por índice em O(1), boa localidade de memória e inserção no final em O(1) amortizado. LinkedList é uma lista duplamente encadeada e faz mais sentido quando preciso das características de Deque ou operações específicas nas extremidades.
>
> Para unicidade, uso Set. HashSet oferece operações O(1) em média sem garantir ordem, LinkedHashSet preserva ordem de inserção e TreeSet mantém os elementos ordenados em O(log n).
>
> Para chave-valor, HashMap é a escolha comum com get e put O(1) em média. LinkedHashMap adiciona ordem previsível e TreeMap mantém as chaves ordenadas com operações O(log n).
>
> Em estruturas baseadas em hash, equals e hashCode são fundamentais. Objetos iguais segundo equals precisam possuir o mesmo hashCode, e colisões são tratadas internamente pelo mapa.
>
> Também considero concorrência. HashMap não é thread-safe, então em cenários concorrentes posso utilizar ConcurrentHashMap e preferir operações atômicas como putIfAbsent ou compute.
>
> Em produção, a escolha depende do padrão de acesso. Eu avalio necessidade de lookup, ordenação, duplicidade, acesso por índice, concorrência, consumo de memória e complexidade das operações antes de escolher a estrutura.

[↑ Voltar ao índice](#indice)

---

<a id="mapa-mental"></a>
# 18. Mapa Mental

```text
JAVA COLLECTIONS
│
├── List
│   ├── ArrayList
│   │    ├── array dinâmico
│   │    ├── get O(1)
│   │    └── inserção meio O(n)
│   │
│   └── LinkedList
│        ├── lista duplamente encadeada
│        ├── acesso O(n)
│        └── também é Deque
│
├── Set
│   ├── HashSet
│   │    └── unicidade + O(1) médio
│   ├── LinkedHashSet
│   │    └── unicidade + ordem inserção
│   └── TreeSet
│        └── unicidade + ordenação O(log n)
│
├── Map
│   ├── HashMap
│   │    └── chave/valor + O(1) médio
│   ├── LinkedHashMap
│   │    └── chave/valor + ordem
│   └── TreeMap
│        └── chave/valor + ordenação O(log n)
│
├── Queue / Deque
│   ├── PriorityQueue
│   │    └── prioridade
│   └── ArrayDeque
│        └── fila ou pilha
│
├── Hashing
│   ├── equals
│   ├── hashCode
│   ├── buckets
│   ├── colisões
│   ├── load factor
│   └── resize
│
├── Ordenação
│   ├── Comparable
│   └── Comparator
│
├── Iteração
│   ├── Iterator
│   ├── fail-fast
│   └── ConcurrentModificationException
│
└── Concorrência
    ├── ConcurrentHashMap
    ├── CopyOnWriteArrayList
    └── BlockingQueue
```

---

# Resumo rápido para revisão

| Conceito | O que lembrar |
|---|---|
| ArrayList | Array dinâmico, acesso O(1), inserção no meio O(n) |
| LinkedList | Lista encadeada, acesso O(n), boa nas extremidades |
| HashSet | Unicidade, O(1) médio |
| LinkedHashSet | Unicidade + ordem de inserção |
| TreeSet | Unicidade + ordenação, O(log n) |
| HashMap | Chave-valor, O(1) médio |
| LinkedHashMap | HashMap + ordem |
| TreeMap | Chaves ordenadas, O(log n) |
| PriorityQueue | Recuperação por prioridade |
| ArrayDeque | Fila ou pilha eficiente |
| equals/hashCode | Fundamentais para HashMap/HashSet |
| Comparable | Ordem natural |
| Comparator | Estratégia externa de ordenação |
| Iterator | Percorre coleção |
| Fail-fast | Detecta algumas modificações estruturais indevidas |
| ConcurrentHashMap | Map thread-safe |
| CopyOnWriteArrayList | Muitas leituras, poucas escritas |
| BlockingQueue | Producer/Consumer |

