# Collections em Python

## Sumário

- [1. Visão geral](#1-visão-geral)
- [2. Classificação das principais coleções](#2-classificação-das-principais-coleções)
- [3. List](#3-list)
  - [3.1 Características](#31-características)
  - [3.2 Criação](#32-criação)
  - [3.3 Acesso e slicing](#33-acesso-e-slicing)
  - [3.4 Principais métodos](#34-principais-métodos)
  - [3.5 List comprehension](#35-list-comprehension)
  - [3.6 Cópia de listas](#36-cópia-de-listas)
  - [3.7 Quando usar](#37-quando-usar)
- [4. Tuple](#4-tuple)
  - [4.1 Características](#41-características)
  - [4.2 Criação](#42-criação)
  - [4.3 Packing e unpacking](#43-packing-e-unpacking)
  - [4.4 Quando usar](#44-quando-usar)
- [5. Set](#5-set)
  - [5.1 Características](#51-características)
  - [5.2 Criação](#52-criação)
  - [5.3 Principais métodos](#53-principais-métodos)
  - [5.4 Operações matemáticas](#54-operações-matemáticas)
  - [5.5 Set comprehension](#55-set-comprehension)
  - [5.6 Quando usar](#56-quando-usar)
- [6. Frozenset](#6-frozenset)
- [7. Dict](#7-dict)
  - [7.1 Características](#71-características)
  - [7.2 Criação](#72-criação)
  - [7.3 Acesso aos valores](#73-acesso-aos-valores)
  - [7.4 Principais métodos](#74-principais-métodos)
  - [7.5 Iteração](#75-iteração)
  - [7.6 Dictionary comprehension](#76-dictionary-comprehension)
  - [7.7 Merge de dicionários](#77-merge-de-dicionários)
  - [7.8 Quando usar](#78-quando-usar)
- [8. Módulo collections](#8-módulo-collections)
  - [8.1 Counter](#81-counter)
  - [8.2 defaultdict](#82-defaultdict)
  - [8.3 deque](#83-deque)
  - [8.4 namedtuple](#84-namedtuple)
  - [8.5 ChainMap](#85-chainmap)
- [9. collections.abc](#9-collectionsabc)
- [10. Mutabilidade e imutabilidade](#10-mutabilidade-e-imutabilidade)
- [11. Hashability](#11-hashability)
- [12. Shallow copy vs deep copy](#12-shallow-copy-vs-deep-copy)
- [13. Complexidade das principais operações](#13-complexidade-das-principais-operações)
- [14. Comparação entre as coleções](#14-comparação-entre-as-coleções)
- [15. Padrões e casos de uso](#15-padrões-e-casos-de-uso)
- [16. Armadilhas comuns](#16-armadilhas-comuns)
- [17. Boas práticas](#17-boas-práticas)
- [18. Perguntas de entrevista](#18-perguntas-de-entrevista)
- [19. Exercícios práticos](#19-exercícios-práticos)
- [20. Resumo para revisão rápida](#20-resumo-para-revisão-rápida)

---

# 1. Visão geral

Em Python, **collections** são estruturas utilizadas para armazenar, organizar e manipular conjuntos de dados.

As quatro estruturas nativas mais importantes são:

- `list`
- `tuple`
- `set`
- `dict`

Além delas, Python fornece o módulo padrão:

```python
collections
```

que contém estruturas especializadas como:

- `Counter`
- `defaultdict`
- `deque`
- `namedtuple`
- `ChainMap`

Escolher a coleção correta depende principalmente de:

- necessidade de preservar ordem;
- existência ou não de duplicatas;
- necessidade de acesso por índice;
- necessidade de acesso por chave;
- mutabilidade;
- performance das operações;
- necessidade de verificar pertencimento rapidamente.

---

# 2. Classificação das principais coleções

| Estrutura | Ordenada | Mutável | Duplicatas | Acesso por índice | Chave/valor |
|---|---:|---:|---:|---:|---:|
| `list` | Sim | Sim | Sim | Sim | Não |
| `tuple` | Sim | Não | Sim | Sim | Não |
| `set` | Não deve ser tratado como sequencial | Sim | Não | Não | Não |
| `frozenset` | Não deve ser tratado como sequencial | Não | Não | Não | Não |
| `dict` | Sim, preserva inserção | Sim | Chaves não | Não | Sim |

> Em Python moderno, `dict` preserva a ordem de inserção. Ainda assim, sua finalidade principal é representar um mapeamento chave → valor.

---

# 3. List

Uma `list` é uma coleção:

- ordenada;
- mutável;
- indexada;
- que aceita valores duplicados;
- que pode armazenar diferentes tipos.

## 3.1 Características

```python
valores = [10, 20, 30]
```

Uma lista pode conter diferentes tipos:

```python
dados = [
    10,
    "Python",
    True,
    3.14
]
```

Apesar disso, em código de produção costuma ser preferível manter coleções semanticamente homogêneas quando possível.

Exemplo:

```python
usuarios = ["Lucas", "Maria", "João"]
```

---

## 3.2 Criação

### Literal

```python
numeros = [1, 2, 3]
```

### Construtor

```python
numeros = list((1, 2, 3))
```

### Criando lista a partir de outro iterável

```python
texto = "python"

letras = list(texto)

print(letras)
```

Resultado:

```text
['p', 'y', 't', 'h', 'o', 'n']
```

---

## 3.3 Acesso e slicing

### Índice

```python
linguagens = ["Java", "Python", "Go"]

print(linguagens[0])
```

Resultado:

```text
Java
```

### Índices negativos

```python
print(linguagens[-1])
```

Resultado:

```text
Go
```

### Slicing

Sintaxe:

```python
lista[inicio:fim:passo]
```

Exemplo:

```python
numeros = [0, 1, 2, 3, 4, 5]

print(numeros[1:4])
```

Resultado:

```text
[1, 2, 3]
```

Invertendo:

```python
print(numeros[::-1])
```

---

## 3.4 Principais métodos

### append()

Adiciona um elemento ao final.

```python
numeros = [1, 2]

numeros.append(3)
```

Resultado:

```python
[1, 2, 3]
```

### extend()

Adiciona vários elementos.

```python
numeros = [1, 2]

numeros.extend([3, 4])
```

Resultado:

```python
[1, 2, 3, 4]
```

Diferença importante:

```python
numeros = [1, 2]

numeros.append([3, 4])
```

Resultado:

```python
[1, 2, [3, 4]]
```

Enquanto:

```python
numeros = [1, 2]

numeros.extend([3, 4])
```

Resultado:

```python
[1, 2, 3, 4]
```

### insert()

Insere em uma posição específica.

```python
numeros = [1, 3]

numeros.insert(1, 2)
```

### remove()

Remove a primeira ocorrência de um valor.

```python
numeros = [1, 2, 3]

numeros.remove(2)
```

Se o elemento não existir:

```python
ValueError
```

### pop()

Remove e retorna um elemento.

```python
numeros = [10, 20, 30]

valor = numeros.pop()
```

Resultado:

```text
valor = 30
numeros = [10, 20]
```

Também pode receber índice:

```python
valor = numeros.pop(0)
```

### clear()

```python
numeros.clear()
```

Remove todos os elementos.

### index()

```python
linguagens = ["Java", "Python", "Go"]

indice = linguagens.index("Python")
```

Resultado:

```text
1
```

### count()

```python
numeros = [1, 1, 2, 3]

print(numeros.count(1))
```

Resultado:

```text
2
```

### sort()

Ordena a própria lista.

```python
numeros = [3, 1, 2]

numeros.sort()
```

Resultado:

```python
[1, 2, 3]
```

Ordem decrescente:

```python
numeros.sort(reverse=True)
```

### sorted()

Cria uma nova lista ordenada.

```python
numeros = [3, 1, 2]

ordenados = sorted(numeros)
```

A lista original permanece intacta.

---

## 3.5 List comprehension

List comprehension fornece uma maneira compacta de criar listas.

Forma geral:

```python
[expressao for elemento in iteravel]
```

Exemplo:

```python
quadrados = [numero ** 2 for numero in range(5)]
```

Resultado:

```python
[0, 1, 4, 9, 16]
```

Com condição:

```python
pares = [
    numero
    for numero in range(10)
    if numero % 2 == 0
]
```

Resultado:

```python
[0, 2, 4, 6, 8]
```

Transformação:

```python
nomes = ["lucas", "maria", "joao"]

nomes_formatados = [
    nome.capitalize()
    for nome in nomes
]
```

---

## 3.6 Cópia de listas

Isto não cria uma nova lista independente:

```python
lista_a = [1, 2, 3]

lista_b = lista_a
```

As duas variáveis apontam para o mesmo objeto.

```python
lista_b.append(4)

print(lista_a)
```

Resultado:

```python
[1, 2, 3, 4]
```

Cópia rasa:

```python
lista_b = lista_a.copy()
```

ou:

```python
lista_b = lista_a[:]
```

ou:

```python
lista_b = list(lista_a)
```

---

## 3.7 Quando usar

Use `list` quando:

- a ordem importa;
- elementos podem se repetir;
- é necessário acessar valores por posição;
- a coleção será modificada;
- append no final será frequente;
- deseja iterar sequencialmente pelos valores.

---

# 4. Tuple

Uma `tuple` é semelhante a uma lista, porém é **imutável**.

## 4.1 Características

```python
coordenada = (10, 20)
```

Características:

- ordenada;
- indexada;
- permite duplicatas;
- imutável.

---

## 4.2 Criação

```python
linguagens = ("Java", "Python", "Go")
```

Uma tupla com um único elemento precisa da vírgula:

```python
valor = (10,)
```

Sem a vírgula:

```python
valor = (10)
```

é apenas um `int`.

Também podemos escrever:

```python
coordenada = 10, 20
```

Python automaticamente cria uma tuple.

---

## 4.3 Packing e unpacking

### Packing

```python
usuario = ("Lucas", 30, "Brasil")
```

### Unpacking

```python
nome, idade, pais = usuario
```

Também pode ser usado diretamente:

```python
x, y = 10, 20
```

Troca de valores:

```python
x, y = y, x
```

### Unpacking com *

```python
numeros = (1, 2, 3, 4, 5)

primeiro, *meio, ultimo = numeros
```

Resultado:

```text
primeiro = 1
meio = [2, 3, 4]
ultimo = 5
```

---

## 4.4 Quando usar

Use `tuple` quando:

- os dados não devem ser alterados;
- deseja representar um agrupamento fixo;
- pretende usar a coleção como chave de um `dict`, desde que seus elementos também sejam hashable;
- quer comunicar semanticamente que o conjunto de valores é imutável.

Exemplo:

```python
coordenada = (22.9, 43.2)
```

---

# 5. Set

`set` representa um conjunto de elementos únicos.

## 5.1 Características

Características:

- mutável;
- não permite duplicatas;
- não oferece acesso posicional por índice;
- otimizado para testes de pertencimento;
- suporta operações matemáticas de conjuntos.

Exemplo:

```python
numeros = {1, 2, 3}
```

---

## 5.2 Criação

```python
numeros = {1, 2, 3}
```

Para criar um set vazio:

```python
numeros = set()
```

Isto:

```python
{}
```

cria um `dict`, e não um `set`.

Removendo duplicatas:

```python
numeros = [1, 1, 2, 2, 3, 3]

unicos = set(numeros)
```

Resultado:

```python
{1, 2, 3}
```

---

## 5.3 Principais métodos

### add()

```python
linguagens = {"Java", "Python"}

linguagens.add("Go")
```

### update()

```python
linguagens.update(["C#", "Rust"])
```

### remove()

```python
linguagens.remove("Java")
```

Se não existir, lança:

```python
KeyError
```

### discard()

```python
linguagens.discard("Java")
```

Se não existir, não lança erro.

### pop()

Remove e retorna um elemento arbitrário.

```python
valor = linguagens.pop()
```

Não deve ser utilizado quando a lógica depende de uma ordem específica.

### clear()

```python
linguagens.clear()
```

---

## 5.4 Operações matemáticas

Considere:

```python
a = {1, 2, 3}
b = {3, 4, 5}
```

### União

```python
a | b
```

ou:

```python
a.union(b)
```

Resultado:

```python
{1, 2, 3, 4, 5}
```

### Interseção

```python
a & b
```

ou:

```python
a.intersection(b)
```

Resultado:

```python
{3}
```

### Diferença

```python
a - b
```

Resultado:

```python
{1, 2}
```

### Diferença simétrica

Elementos que estão em apenas um dos conjuntos:

```python
a ^ b
```

Resultado:

```python
{1, 2, 4, 5}
```

### Subset

```python
a.issubset(b)
```

### Superset

```python
a.issuperset(b)
```

### Disjoint

Verifica se não existem elementos em comum:

```python
a.isdisjoint(b)
```

---

## 5.5 Set comprehension

```python
quadrados = {
    numero ** 2
    for numero in range(10)
}
```

---

## 5.6 Quando usar

Use `set` quando:

- precisa eliminar duplicatas;
- precisa verificar pertencimento rapidamente;
- a ordem posicional não é relevante;
- precisa executar operações matemáticas de conjuntos.

Exemplo:

```python
usuarios_permitidos = {
    "lucas",
    "maria",
    "joao"
}

if "lucas" in usuarios_permitidos:
    print("Usuário permitido")
```

---

# 6. Frozenset

`frozenset` é a versão **imutável** de `set`.

```python
permissoes = frozenset({
    "READ",
    "WRITE"
})
```

Não é possível:

```python
permissoes.add("DELETE")
```

Uso comum:

- valores imutáveis;
- elementos de outros sets;
- chaves de dicionários.

Exemplo:

```python
cache = {}

chave = frozenset({"A", "B"})

cache[chave] = "resultado"
```

---

# 7. Dict

`dict` representa um mapeamento entre:

```text
chave → valor
```

Exemplo:

```python
usuario = {
    "nome": "Lucas",
    "idade": 30
}
```

## 7.1 Características

Um `dict`:

- é mutável;
- possui chaves únicas;
- preserva a ordem de inserção;
- permite acesso eficiente por chave;
- exige chaves hashable.

---

## 7.2 Criação

Literal:

```python
usuario = {
    "nome": "Lucas",
    "idade": 30
}
```

Construtor:

```python
usuario = dict(
    nome="Lucas",
    idade=30
)
```

A partir de pares:

```python
usuario = dict([
    ("nome", "Lucas"),
    ("idade", 30)
])
```

---

## 7.3 Acesso aos valores

Usando `[]`:

```python
usuario["nome"]
```

Se a chave não existir:

```python
KeyError
```

Usando `get()`:

```python
usuario.get("nome")
```

Se não existir:

```python
None
```

Podemos fornecer valor padrão:

```python
usuario.get("cidade", "Não informado")
```

---

## 7.4 Principais métodos

### keys()

```python
usuario.keys()
```

### values()

```python
usuario.values()
```

### items()

```python
usuario.items()
```

Retorna pares:

```text
(chave, valor)
```

### update()

```python
usuario.update({
    "cidade": "Rio de Janeiro"
})
```

### pop()

```python
idade = usuario.pop("idade")
```

### popitem()

Remove o último par inserido:

```python
usuario.popitem()
```

### setdefault()

Retorna o valor de uma chave.

Caso a chave não exista, cria com um valor padrão.

```python
usuario.setdefault("ativo", True)
```

### clear()

```python
usuario.clear()
```

---

## 7.5 Iteração

Por padrão:

```python
for chave in usuario:
    print(chave)
```

Iterando pelas chaves:

```python
for chave in usuario.keys():
    print(chave)
```

Valores:

```python
for valor in usuario.values():
    print(valor)
```

Chave e valor:

```python
for chave, valor in usuario.items():
    print(chave, valor)
```

---

## 7.6 Dictionary comprehension

```python
quadrados = {
    numero: numero ** 2
    for numero in range(5)
}
```

Resultado:

```python
{
    0: 0,
    1: 1,
    2: 4,
    3: 9,
    4: 16
}
```

Com condição:

```python
pares = {
    numero: numero ** 2
    for numero in range(10)
    if numero % 2 == 0
}
```

---

## 7.7 Merge de dicionários

Python moderno permite:

```python
config_padrao = {
    "timeout": 30,
    "debug": False
}

config_usuario = {
    "debug": True
}

config = config_padrao | config_usuario
```

Resultado:

```python
{
    "timeout": 30,
    "debug": True
}
```

Também é possível:

```python
config_padrao |= config_usuario
```

---

## 7.8 Quando usar

Use `dict` quando:

- os dados possuem uma chave identificadora;
- precisa buscar valores por chave;
- deseja representar estruturas ou entidades;
- precisa criar índices;
- precisa armazenar configurações;
- precisa implementar cache ou lookup.

Exemplo:

```python
usuarios_por_id = {
    1: "Lucas",
    2: "Maria"
}
```

Busca:

```python
usuario = usuarios_por_id[1]
```

---

# 8. Módulo collections

Importação:

```python
from collections import (
    Counter,
    defaultdict,
    deque,
    namedtuple,
    ChainMap
)
```

---

# 8.1 Counter

`Counter` é uma especialização de `dict` utilizada para contar ocorrências.

```python
from collections import Counter

linguagens = [
    "Java",
    "Python",
    "Java",
    "Python",
    "Python"
]

contador = Counter(linguagens)
```

Resultado:

```python
Counter({
    "Python": 3,
    "Java": 2
})
```

Acesso:

```python
contador["Python"]
```

Resultado:

```text
3
```

Se a chave não existir:

```python
contador["Go"]
```

Resultado:

```text
0
```

### most_common()

```python
contador.most_common(1)
```

Resultado:

```python
[("Python", 3)]
```

Caso de uso:

- frequência de palavras;
- contagem de eventos;
- ranking de ocorrências;
- análise de logs.

---

# 8.2 defaultdict

`defaultdict` cria automaticamente um valor padrão quando uma chave não existe.

Exemplo com lista:

```python
from collections import defaultdict

usuarios_por_cidade = defaultdict(list)

usuarios_por_cidade["Rio"].append("Lucas")
usuarios_por_cidade["Rio"].append("Maria")
```

Resultado:

```python
{
    "Rio": ["Lucas", "Maria"]
}
```

Sem `defaultdict`, seria comum:

```python
usuarios_por_cidade = {}

if "Rio" not in usuarios_por_cidade:
    usuarios_por_cidade["Rio"] = []

usuarios_por_cidade["Rio"].append("Lucas")
```

### Contador

```python
contador = defaultdict(int)

contador["python"] += 1
```

Como:

```python
int()
```

retorna:

```python
0
```

a chave começa automaticamente com zero.

---

# 8.3 deque

`deque` significa:

```text
double-ended queue
```

É otimizada para inserções e remoções nas duas extremidades.

```python
from collections import deque

fila = deque()
```

Adicionar à direita:

```python
fila.append("A")
```

Adicionar à esquerda:

```python
fila.appendleft("B")
```

Remover da direita:

```python
fila.pop()
```

Remover da esquerda:

```python
fila.popleft()
```

### Por que não usar list como fila?

Uma operação:

```python
lista.pop(0)
```

normalmente exige deslocar os elementos restantes.

Já:

```python
deque.popleft()
```

é projetado para remoção eficiente no início.

### Fila FIFO

```python
fila = deque()

fila.append("cliente-1")
fila.append("cliente-2")

primeiro = fila.popleft()
```

### Pilha LIFO

```python
pilha = deque()

pilha.append("A")
pilha.append("B")

ultimo = pilha.pop()
```

### maxlen

```python
ultimos_eventos = deque(maxlen=3)

ultimos_eventos.append(1)
ultimos_eventos.append(2)
ultimos_eventos.append(3)
ultimos_eventos.append(4)
```

Resultado:

```python
deque([2, 3, 4], maxlen=3)
```

Muito útil para:

- buffers;
- histórico limitado;
- sliding window;
- filas;
- BFS.

---

# 8.4 namedtuple

`namedtuple` cria objetos semelhantes a tuplas, mas com campos nomeados.

```python
from collections import namedtuple

Usuario = namedtuple(
    "Usuario",
    ["nome", "idade"]
)

usuario = Usuario(
    nome="Lucas",
    idade=30
)
```

Acesso:

```python
usuario.nome
usuario.idade
```

Também continua sendo possível:

```python
usuario[0]
```

Por ser baseada em tuple, é imutável.

Hoje, dependendo do contexto, também é comum utilizar:

```python
dataclasses
```

Exemplo:

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class Usuario:
    nome: str
    idade: int
```

---

# 8.5 ChainMap

`ChainMap` permite visualizar vários dicionários como um único mapeamento.

```python
from collections import ChainMap

config_usuario = {
    "timeout": 10
}

config_padrao = {
    "timeout": 30,
    "debug": False
}

config = ChainMap(
    config_usuario,
    config_padrao
)
```

Busca:

```python
config["timeout"]
```

Resultado:

```text
10
```

A busca ocorre da esquerda para a direita.

Uso comum:

- configurações em camadas;
- overrides;
- ambientes;
- escopos.

---

# 9. collections.abc

O módulo:

```python
collections.abc
```

define interfaces abstratas relacionadas a coleções.

Exemplos:

```python
from collections.abc import (
    Iterable,
    Iterator,
    Sequence,
    MutableSequence,
    Mapping,
    MutableMapping,
    Set
)
```

Conceitos importantes:

### Iterable

Objeto que pode ser percorrido.

```python
for item in objeto:
    ...
```

### Iterator

Objeto que produz elementos através de:

```python
next()
```

### Sequence

Representa uma sequência indexada.

Exemplos:

- `list`
- `tuple`
- `str`

### Mapping

Representa um mapeamento chave → valor.

Exemplo:

```python
dict
```

Esse conhecimento é especialmente importante em:

- type hints;
- APIs genéricas;
- design de bibliotecas;
- entrevistas sobre o modelo de dados Python.

---

# 10. Mutabilidade e imutabilidade

### Mutáveis

Podem ser alterados depois da criação:

```python
list
dict
set
```

Exemplo:

```python
lista = [1, 2]

lista.append(3)
```

O mesmo objeto foi alterado.

### Imutáveis

Não podem ser modificados depois da criação:

```python
tuple
frozenset
str
int
float
```

Exemplo:

```python
tupla = (1, 2)
```

Não existe:

```python
tupla.append(3)
```

---

# 11. Hashability

Um objeto **hashable** possui um valor de hash estável durante sua existência.

Objetos hashable podem ser utilizados como:

- chaves de `dict`;
- elementos de `set`.

Exemplos normalmente hashable:

```python
int
str
float
tuple
frozenset
```

Exemplos não hashable:

```python
list
dict
set
```

Inválido:

```python
dicionario = {
    [1, 2]: "valor"
}
```

Resultado:

```text
TypeError: unhashable type: 'list'
```

Válido:

```python
dicionario = {
    (1, 2): "valor"
}
```

Uma tuple só será hashable se seus elementos também forem hashable.

---

# 12. Shallow copy vs deep copy

Considere:

```python
original = [
    [1, 2],
    [3, 4]
]
```

## Shallow copy

```python
copia = original.copy()
```

Cria uma nova lista externa, mas os objetos internos continuam compartilhados.

```python
copia[0].append(99)

print(original)
```

Resultado:

```python
[
    [1, 2, 99],
    [3, 4]
]
```

## Deep copy

```python
import copy

copia = copy.deepcopy(original)
```

Agora os objetos internos também são copiados.

Use `deepcopy` com critério, pois:

- pode consumir mais memória;
- pode ser mais lento;
- nem sempre é necessário.

---

# 13. Complexidade das principais operações

Valores abaixo representam a complexidade média/típica esperada das implementações padrão.

## List

| Operação | Complexidade típica |
|---|---:|
| `lista[i]` | O(1) |
| `append()` | O(1) amortizado |
| `pop()` no final | O(1) |
| `insert(0, x)` | O(n) |
| `pop(0)` | O(n) |
| `x in lista` | O(n) |
| `remove(x)` | O(n) |
| `sort()` | O(n log n) |

## Dict

| Operação | Complexidade média |
|---|---:|
| `d[chave]` | O(1) |
| Inserção | O(1) |
| Remoção | O(1) |
| `chave in d` | O(1) |

Em situações patológicas envolvendo colisões de hash, a complexidade pode degradar.

## Set

| Operação | Complexidade média |
|---|---:|
| `x in conjunto` | O(1) |
| `add()` | O(1) |
| `remove()` | O(1) |

## Deque

| Operação | Complexidade típica |
|---|---:|
| `append()` | O(1) |
| `appendleft()` | O(1) |
| `pop()` | O(1) |
| `popleft()` | O(1) |

---

# 14. Comparação entre as coleções

## List vs Tuple

Use:

```python
list
```

quando precisa modificar os valores.

Use:

```python
tuple
```

quando os valores representam uma estrutura fixa ou não devem mudar.

Exemplo:

```python
usuarios = ["Lucas", "Maria"]
```

versus:

```python
coordenada = (-22.9, -43.2)
```

---

## List vs Set

Use `list` quando:

- ordem importa;
- duplicatas importam;
- precisa acessar por índice.

Use `set` quando:

- unicidade importa;
- pertencimento rápido importa;
- não precisa de acesso posicional.

Exemplo:

```python
lista = [1, 1, 2, 2]
```

versus:

```python
conjunto = {1, 2}
```

---

## List vs Deque

Use `list` principalmente quando:

- trabalha com acesso por índice;
- adiciona/remove principalmente no final.

Use `deque` quando:

- adiciona/remove nas duas extremidades;
- implementa filas;
- implementa BFS;
- precisa de buffer limitado.

---

## Dict vs Set

As duas estruturas são baseadas em hashing.

`dict`:

```text
chave → valor
```

`set`:

```text
somente chave/elemento
```

Exemplo:

```python
usuarios = {
    1: "Lucas",
    2: "Maria"
}
```

versus:

```python
ids_ativos = {1, 2}
```

---

# 15. Padrões e casos de uso

## Remover duplicatas

```python
valores = [1, 1, 2, 3, 3]

unicos = list(set(valores))
```

Atenção: não utilize esse padrão se depender semanticamente da ordem original.

Uma alternativa que preserva ordem é:

```python
unicos = list(dict.fromkeys(valores))
```

---

## Agrupamento

Problema:

```python
usuarios = [
    ("Lucas", "Rio"),
    ("Maria", "São Paulo"),
    ("João", "Rio")
]
```

Com `defaultdict`:

```python
from collections import defaultdict

por_cidade = defaultdict(list)

for nome, cidade in usuarios:
    por_cidade[cidade].append(nome)
```

---

## Contagem

```python
from collections import Counter

eventos = [
    "LOGIN",
    "LOGIN",
    "LOGOUT",
    "LOGIN"
]

contador = Counter(eventos)
```

---

## Cache simples

```python
cache = {}

def buscar_usuario(usuario_id):
    if usuario_id in cache:
        return cache[usuario_id]

    usuario = carregar_usuario(usuario_id)

    cache[usuario_id] = usuario

    return usuario
```

---

## Fila

```python
from collections import deque

fila = deque()

fila.append("A")
fila.append("B")

while fila:
    item = fila.popleft()
    print(item)
```

---

## Pilha

Para pilha simples, uma `list` normalmente é suficiente:

```python
pilha = []

pilha.append("A")
pilha.append("B")

item = pilha.pop()
```

---

# 16. Armadilhas comuns

## 16.1 Usar lista como argumento padrão mutável

Evite:

```python
def adicionar(valor, valores=[]):
    valores.append(valor)

    return valores
```

O mesmo objeto é reutilizado entre chamadas.

Exemplo:

```python
print(adicionar(1))
print(adicionar(2))
```

Pode resultar em:

```text
[1]
[1, 2]
```

Prefira:

```python
def adicionar(valor, valores=None):
    if valores is None:
        valores = []

    valores.append(valor)

    return valores
```

---

## 16.2 Modificar coleção durante iteração

Evite:

```python
numeros = [1, 2, 3, 4]

for numero in numeros:
    if numero % 2 == 0:
        numeros.remove(numero)
```

Uma alternativa:

```python
numeros = [
    numero
    for numero in numeros
    if numero % 2 != 0
]
```

---

## 16.3 Confundir append com extend

```python
lista.append([3, 4])
```

Resultado:

```python
[1, 2, [3, 4]]
```

Enquanto:

```python
lista.extend([3, 4])
```

Resultado:

```python
[1, 2, 3, 4]
```

---

## 16.4 Criar set vazio incorretamente

Errado:

```python
conjunto = {}
```

Isso cria:

```python
dict
```

Correto:

```python
conjunto = set()
```

---

## 16.5 Usar pop(0) em uma fila

Evite:

```python
fila = []

fila.append(item)
fila.pop(0)
```

Para filas frequentes, prefira:

```python
from collections import deque

fila = deque()

fila.append(item)
fila.popleft()
```

---

## 16.6 Confundir atribuição com cópia

```python
lista_b = lista_a
```

não cria uma cópia.

As duas variáveis apontam para o mesmo objeto.

---

# 17. Boas práticas

## Escolha pela semântica

Não escolha uma estrutura apenas por hábito.

Pergunte:

1. Preciso preservar ordem?
2. Preciso de duplicatas?
3. Preciso modificar a estrutura?
4. Preciso de lookup rápido?
5. Preciso acessar por índice?
6. Preciso adicionar/remover no início frequentemente?
7. Preciso representar chave → valor?

---

## Use comprehensions quando melhorarem a clareza

Bom:

```python
pares = [
    numero
    for numero in numeros
    if numero % 2 == 0
]
```

Evite comprehensions excessivamente complexas.

Se a expressão exige várias condições ou regras, um `for` explícito pode ser mais legível.

---

## Prefira get() quando a ausência da chave for esperada

```python
cidade = usuario.get(
    "cidade",
    "Não informado"
)
```

Em vez de:

```python
try:
    cidade = usuario["cidade"]
except KeyError:
    cidade = "Não informado"
```

Entretanto, se a ausência da chave representar um erro de domínio ou programação, `[]` pode ser mais apropriado.

---

## Use defaultdict para agrupamentos

Em vez de:

```python
if chave not in grupos:
    grupos[chave] = []

grupos[chave].append(valor)
```

Pode utilizar:

```python
from collections import defaultdict

grupos = defaultdict(list)

grupos[chave].append(valor)
```

---

## Use Counter para frequências

Em vez de criar manualmente:

```python
contador = {}

for item in itens:
    contador[item] = contador.get(item, 0) + 1
```

Pode utilizar:

```python
from collections import Counter

contador = Counter(itens)
```

---

# 18. Perguntas de entrevista

## 1. Qual a diferença entre list e tuple?

Resposta:

`list` é mutável, enquanto `tuple` é imutável. Ambas preservam ordem, permitem duplicatas e oferecem acesso por índice. Tuple é útil para representar estruturas fixas e, se todos os seus elementos forem hashable, pode ser usada como chave de dicionário.

---

## 2. Qual a diferença entre list e set?

Resposta:

Lista preserva ordem, aceita duplicatas e permite acesso por índice. Set mantém elementos únicos e é otimizado para testes de pertencimento, geralmente com complexidade média O(1).

---

## 3. Qual a diferença entre remove(), pop() e del?

### remove()

Remove pelo valor:

```python
lista.remove(10)
```

### pop()

Remove por índice e retorna o valor:

```python
valor = lista.pop(0)
```

### del

Remove por índice ou slice:

```python
del lista[0]
```

---

## 4. Por que dict é rápido para lookup?

Porque é implementado usando uma estrutura baseada em hash table.

A chave é convertida em um hash, utilizado para localizar eficientemente onde o valor está armazenado.

Complexidade média:

```text
O(1)
```

---

## 5. O que significa um objeto ser hashable?

Significa que o objeto pode fornecer um hash estável durante sua vida útil e pode ser comparado para igualdade.

Objetos hashable podem ser usados como:

- chave de `dict`;
- elemento de `set`.

---

## 6. Por que list não pode ser chave de dict?

Porque `list` é mutável e, portanto, não é hashable.

---

## 7. Quando utilizar deque em vez de list?

Quando existem inserções ou remoções frequentes no início da coleção.

Exemplo:

```python
deque.popleft()
```

é muito mais apropriado para uma fila do que:

```python
list.pop(0)
```

---

## 8. Qual a diferença entre get() e [] em dict?

```python
d["chave"]
```

lança `KeyError` se a chave não existir.

```python
d.get("chave")
```

retorna `None` ou um valor padrão.

---

## 9. Qual a diferença entre shallow copy e deep copy?

Shallow copy cria uma nova coleção externa, mas mantém referências aos objetos internos.

Deep copy copia recursivamente os objetos internos.

---

## 10. O que é defaultdict?

É uma especialização de `dict` que cria automaticamente um valor padrão para chaves ausentes através de uma função factory.

Exemplo:

```python
defaultdict(list)
```

---

## 11. O que é Counter?

É uma especialização de dicionário para contagem de objetos hashable.

Exemplo:

```python
Counter(["A", "A", "B"])
```

Resultado:

```python
Counter({
    "A": 2,
    "B": 1
})
```

---

## 12. list comprehension é sempre melhor que for?

Não.

Ela é excelente para transformações e filtros simples.

Quando existe lógica complexa, múltiplas condições ou efeitos colaterais, um loop explícito costuma ser mais legível.

---

## 13. Dict mantém ordem?

Sim.

Em Python moderno, `dict` preserva a ordem de inserção.

Ainda assim, seu propósito principal continua sendo realizar mapeamento por chave.

---

## 14. O que é unpacking?

É a distribuição dos elementos de um iterável entre variáveis.

```python
nome, idade = ("Lucas", 30)
```

Também pode utilizar:

```python
primeiro, *restante = [1, 2, 3, 4]
```

---

## 15. O que acontece ao fazer lista_b = lista_a?

Não é criada uma nova lista.

As duas variáveis passam a referenciar o mesmo objeto.

---

# 19. Exercícios práticos

## Exercício 1

Dada a lista:

```python
numeros = [
    1, 2, 2, 3, 4, 4, 5
]
```

Crie uma nova coleção contendo apenas valores únicos.

---

## Exercício 2

Conte quantas vezes cada palavra aparece:

```python
palavras = [
    "java",
    "python",
    "python",
    "java",
    "python",
    "go"
]
```

Tente resolver:

1. manualmente com `dict`;
2. usando `Counter`.

---

## Exercício 3

Agrupe usuários por cidade:

```python
usuarios = [
    ("Lucas", "Rio"),
    ("Maria", "São Paulo"),
    ("João", "Rio"),
    ("Ana", "Curitiba")
]
```

Resultado esperado:

```python
{
    "Rio": [
        "Lucas",
        "João"
    ],
    "São Paulo": [
        "Maria"
    ],
    "Curitiba": [
        "Ana"
    ]
}
```

Tente utilizar:

```python
defaultdict(list)
```

---

## Exercício 4

Implemente uma fila usando:

```python
deque
```

Ela deve suportar:

```text
enqueue
dequeue
peek
is_empty
```

---

## Exercício 5

Dadas duas listas:

```python
backend = [
    "Lucas",
    "Maria",
    "João"
]

python = [
    "Lucas",
    "Ana",
    "João"
]
```

Descubra quem pertence aos dois grupos.

Utilize operações de `set`.

---

## Exercício 6

Dado:

```python
usuarios = [
    {
        "id": 1,
        "nome": "Lucas"
    },
    {
        "id": 2,
        "nome": "Maria"
    }
]
```

Crie um dicionário:

```python
{
    1: {
        "id": 1,
        "nome": "Lucas"
    },
    2: {
        "id": 2,
        "nome": "Maria"
    }
}
```

Utilize dictionary comprehension.

---

# 20. Resumo para revisão rápida

## List

```python
lista = []

lista.append(valor)
lista.extend(iteravel)
lista.insert(indice, valor)

lista.remove(valor)

valor = lista.pop()

lista.sort()

nova_lista = sorted(lista)
```

Características:

```text
ordenada
mutável
duplicatas
indexada
```

---

## Tuple

```python
tupla = (
    valor1,
    valor2
)
```

Características:

```text
ordenada
imutável
duplicatas
indexada
```

---

## Set

```python
conjunto = set()

conjunto.add(valor)
conjunto.remove(valor)
conjunto.discard(valor)
```

Operações:

```python
a | b
a & b
a - b
a ^ b
```

Características:

```text
elementos únicos
lookup rápido
sem acesso por índice
mutável
```

---

## Frozenset

```python
conjunto = frozenset([
    1,
    2,
    3
])
```

Características:

```text
elementos únicos
imutável
hashable quando aplicável
```

---

## Dict

```python
d = {}

d[chave] = valor

valor = d.get(chave)

for chave, valor in d.items():
    ...
```

Características:

```text
chave → valor
chaves únicas
mutável
lookup médio O(1)
preserva ordem de inserção
```

---

## Counter

```python
from collections import Counter

contador = Counter(itens)
```

Uso:

```text
frequência / contagem
```

---

## defaultdict

```python
from collections import defaultdict

grupos = defaultdict(list)
```

Uso:

```text
agrupamentos
valores padrão
```

---

## deque

```python
from collections import deque

fila = deque()

fila.append(valor)

valor = fila.popleft()
```

Uso:

```text
fila
pilha
buffer
sliding window
BFS
```

---

# Checklist de domínio

Antes de considerar Collections dominado, você deve saber explicar sem consultar documentação:

- [ ] diferença entre `list`, `tuple`, `set` e `dict`;
- [ ] mutabilidade vs imutabilidade;
- [ ] `append()` vs `extend()`;
- [ ] `remove()` vs `pop()`;
- [ ] slicing;
- [ ] list comprehension;
- [ ] set comprehension;
- [ ] dictionary comprehension;
- [ ] operações de conjuntos;
- [ ] `dict.get()`;
- [ ] `dict.items()`;
- [ ] `Counter`;
- [ ] `defaultdict`;
- [ ] `deque`;
- [ ] `namedtuple`;
- [ ] hashability;
- [ ] shallow copy vs deep copy;
- [ ] por que `deque` é melhor para filas;
- [ ] complexidade média de lookup em `dict` e `set`;
- [ ] complexidade de `list.pop(0)`;
- [ ] como escolher a coleção correta para cada problema.

---

# Mapa mental

```text
Python Collections
│
├── Sequence
│   ├── list
│   │   ├── ordenada
│   │   ├── mutável
│   │   └── indexada
│   │
│   └── tuple
│       ├── ordenada
│       ├── imutável
│       └── indexada
│
├── Set
│   ├── set
│   │   ├── único
│   │   ├── mutável
│   │   └── hashing
│   │
│   └── frozenset
│       ├── único
│       └── imutável
│
├── Mapping
│   └── dict
│       ├── chave → valor
│       ├── hashing
│       └── lookup O(1) médio
│
└── collections
    ├── Counter
    │   └── frequência
    │
    ├── defaultdict
    │   └── valor padrão
    │
    ├── deque
    │   └── fila / pilha
    │
    ├── namedtuple
    │   └── tuple nomeada
    │
    └── ChainMap
        └── múltiplos mappings
```
