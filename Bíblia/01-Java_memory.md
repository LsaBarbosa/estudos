# JVM Memory & Garbage Collection

<a id="indice"></a>

## Índice

- [1. Visão geral](#visao-geral)
- [2. Heap vs Stack](#heap-vs-stack)
    - [2.1 Heap](#heap)
        - [Resposta de entrevista — Heap](#resposta-heap)
    - [2.2 Stack](#stack)
        - [Resposta de entrevista — Stack](#resposta-stack)
- [3. Reachability e GC Roots](#reachability-gc-roots)
    - [GC Roots](#gc-roots)
    - [Regra de alcançabilidade](#regra-alcancabilidade)
    - [Resposta de entrevista — Reachability](#resposta-reachability)
- [4. Young e Old Generation](#young-old)
    - [Fluxo geracional](#fluxo-geracional)
    - [Resposta de entrevista — Young/Old](#resposta-young-old)
- [5. Garbage Collector](#garbage-collector)
    - [Resposta de entrevista — GC](#resposta-gc)
- [6. G1 GC](#g1-gc)
    - [6.1 Young Collection](#young-collection)
    - [6.2 Mixed Collection](#mixed-collection)
    - [6.3 Full GC](#full-gc)
    - [Resposta de entrevista — G1](#resposta-g1)
- [7. G1 vs ZGC](#g1-vs-zgc)
    - [7.1 G1](#g1)
    - [7.2 ZGC](#zgc)
    - [Resposta de entrevista — G1 vs ZGC](#resposta-g1-zgc)
- [8. Trade-offs](#trade-offs)
    - [8.1 Xmx](#xmx)
        - [Aumentar Xmx](#aumentar-xmx)
        - [Diminuir Xmx](#diminuir-xmx)
    - [8.2 Escolha do coletor](#escolha-coletor)
- [9. Heap não é toda a memória do processo](#memoria-processo)
    - [Memória fora da Heap](#fora-heap)
    - [Resposta de entrevista — Memória do processo](#resposta-memoria-processo)
- [10. Heap Dump vs Thread Dump](#heap-vs-thread-dump)
    - [10.1 Heap Dump](#heap-dump)
        - [O que investigar](#heap-dump-investigar)
        - [Resposta de entrevista — Heap Dump](#resposta-heap-dump)
    - [10.2 Thread Dump](#thread-dump)
        - [O que investigar](#thread-dump-investigar)
        - [Resposta de entrevista — Thread Dump](#resposta-thread-dump)
- [11. Investigação em produção](#producao)
    - [11.1 Métricas e GC Logs](#metricas-gc)
    - [11.2 Heap após GC continua crescendo](#heap-crescendo)
    - [11.3 Processo cresce, mas Heap estável](#processo-cresce)
    - [11.4 Heap estável, mas latência aumenta](#latencia-aumenta)
    - [11.5 Tuning](#tuning)
- [12. Resposta completa para entrevista](#resposta-completa)

---

# JVM Memory & Garbage Collection

## Visão geral

- **Heap** → onde ficam objetos e arrays.
- **Stack** → acompanha a execução de cada thread.
- **Young / Old Generation** → divisão geracional da Heap para otimizar a coleta.
- **GC** → identifica objetos que não estão mais alcançáveis e recupera memória.
- **G1 / ZGC** → coletores com objetivos e trade-offs diferentes.
- **Heap Dump** → investigação de objetos e retenção de memória.
- **Thread Dump** → investigação de execução, espera, bloqueios e contenção.

> **Resumo para entrevista:**  
> A JVM possui diferentes áreas de memória. A Heap armazena objetos e é gerenciada pelo Garbage Collector, enquanto cada thread possui sua própria Stack para execução dos métodos. Em produção, problemas de memória e GC normalmente são investigados primeiro por métricas e logs e, quando necessário, por Heap Dump e Thread Dump.

---

# Heap vs Stack

## Heap

- Área de memória **compartilhada entre as threads**.
- Armazena principalmente:
    - objetos;
    - arrays.
- É gerenciada pelo **Garbage Collector**.
- Pode ocorrer:

```text
OutOfMemoryError: Java heap space
```

quando a JVM não consegue atender novas alocações dentro da Heap disponível.

### Resposta de entrevista

> A Heap é a área compartilhada entre as threads onde ficam objetos e arrays. Ela é gerenciada pelo Garbage Collector. Se a JVM não conseguir liberar ou encontrar espaço suficiente para novas alocações, pode ocorrer `OutOfMemoryError: Java heap space`.

---

## Stack

- Cada **thread possui sua própria Stack**.
- Contém os **stack frames** das chamadas de métodos.
- Está relacionada a:
    - variáveis locais;
    - parâmetros;
    - referências locais;
    - estado necessário para execução dos métodos.
- Um frame deixa de existir quando o método retorna.
- Pode ocorrer:

```text
StackOverflowError
```

normalmente por profundidade excessiva de chamadas, sendo recursão infinita um caso clássico.

### Resposta de entrevista

> Cada thread possui sua própria Stack, que acompanha a execução dos métodos por meio de stack frames. Uma profundidade excessiva de chamadas, como em uma recursão infinita, pode provocar `StackOverflowError`.

---

# Reachability e GC Roots

O GC não simplesmente verifica se uma variável foi definida como `null`.

Ele determina se um objeto continua **alcançável a partir de GC Roots**.

Exemplos de GC Roots incluem referências originadas de:

- threads ativas;
- variáveis locais presentes nas stacks;
- referências estáticas;
- estruturas internas da JVM/JNI.

### Regra principal

```text
GC Root
   │
   ▼
Objeto A
   │
   ▼
Objeto B
```

Se existe um caminho de referências fortes:

```text
GC Root → A → B
```

`A` e `B` continuam alcançáveis.

Se um objeto não possui mais caminho a partir dos GC Roots:

```text
GC Roots ──X──> Objeto
```

ele se torna **elegível para coleta**.

### Resposta de entrevista

> O Garbage Collector determina a vida de um objeto através de reachability. Se existe um caminho de referências fortes partindo de algum GC Root até o objeto, ele continua vivo. Quando esse caminho deixa de existir, o objeto fica elegível para coleta.

---

# Young e Old Generation

Coletores geracionais exploram a hipótese de que **muitos objetos possuem vida curta**.

A Heap é dividida logicamente de acordo com a idade dos objetos.

Fluxo simplificado:

```text
Alocação
   ↓
Eden
   ↓
Young GC
   ↓
Survivor
   ↓
sobrevive a várias coletas
   ↓
Old Generation
```

1. O objeto normalmente é alocado na **Eden**, dentro da Young Generation.
2. Durante uma coleta da Young Generation, objetos não alcançáveis podem ser recuperados.
3. Objetos que continuam vivos podem ser movidos para áreas **Survivor**.
4. Após sobreviverem a coletas suficientes, podem ser promovidos para a **Old Generation**.

> Esse modelo é uma simplificação conceitual; detalhes de alocação e promoção variam conforme o coletor.

### Resposta de entrevista

> Coletores geracionais dividem a Heap conforme a idade dos objetos. Objetos normalmente começam na Young Generation e, caso sobrevivam a várias coletas, podem ser promovidos para a Old Generation. Isso funciona bem porque muitas aplicações criam uma grande quantidade de objetos de vida curta.

---

# Garbage Collector

O GC é responsável por **recuperar memória ocupada por objetos que não estão mais alcançáveis**.

Ele não elimina necessariamente um objeto assim que ele deixa de ser usado pelo código.

A JVM determina a alcançabilidade e o coletor decide **quando e como realizar o trabalho de coleta**.

### Resposta de entrevista

> O Garbage Collector gerencia automaticamente a memória da Heap. Ele identifica objetos que não estão mais alcançáveis a partir dos GC Roots e recupera essa memória. Coletores diferentes, como G1 e ZGC, utilizam estratégias diferentes para equilibrar throughput, latência e consumo de recursos.

---

# G1 GC

O **G1 — Garbage First** divide a Heap em **regiões**.

Ele procura fornecer uma combinação de:

- bom throughput;
- pausas relativamente controladas;
- coleta incremental da Old Generation.

O G1 continua sendo o coletor padrão na maioria das configurações do HotSpot e busca manter pausas relativamente pequenas sem abandonar throughput. :chatgpt-content-reference{index="0"}

## Tipos importantes de coleta

### Young Collection

Coleta regiões da **Young Generation**.

```text
Eden + Survivor
      ↓
Young GC
```

---

### Mixed Collection

Coleta:

```text
Young
  +
regiões selecionadas da Old
```

Depois do ciclo de marcação concorrente, o G1 pode selecionar regiões da Old Generation com boa oportunidade de recuperação de memória e incluí-las em Mixed Collections. :chatgpt-content-reference{index="1"}

> **Mixed Collection ≠ Full GC**

---

### Full GC

É um mecanismo de fallback do G1.

Envolve a Heap inteira e realiza uma operação **Stop-The-World**, podendo gerar uma pausa significativamente maior. :chatgpt-content-reference{index="2"}

### Resposta de entrevista

> O G1 divide a Heap em regiões e tenta controlar o tempo das pausas escolhendo quais regiões coletar. Ele possui Young Collections e Mixed Collections, que também podem recuperar regiões da Old Generation. Full GC é diferente: é um mecanismo de fallback que envolve a Heap inteira e tende a produzir pausas mais relevantes.

---

# G1 vs ZGC

## G1

Objetivo principal:

```text
equilíbrio
Throughput ←→ Latência
```

Características:

- Heap baseada em regiões;
- parte do trabalho ocorre concorrentemente;
- possui fases **Stop-The-World**;
- busca cumprir uma meta de pausa;
- tende a fornecer boa relação entre throughput e latência.

---

## ZGC

Objetivo principal:

```text
baixa latência
```

Características:

- executa grande parte do trabalho concorrentemente;
- busca manter pausas extremamente pequenas;
- no Java moderno, o ZGC é **geracional** — o modo não geracional foi removido a partir do JDK 24. :chatgpt-content-reference{index="3"}
- pode sacrificar algum throughput em troca das características de baixa latência. A documentação do JDK 25 descreve o ZGC justamente como um coletor voltado a aplicações com requisitos de baixa latência, com custo potencial de throughput. :chatgpt-content-reference{index="4"}

### Resposta de entrevista

> O G1 procura equilibrar throughput e metas de pausa, enquanto o ZGC é voltado principalmente para aplicações com requisitos rigorosos de baixa latência. O ZGC realiza uma parcela maior do trabalho concorrentemente, reduzindo as pausas, mas o trade-off pode aparecer em throughput e consumo de recursos. A escolha deve ser baseada na carga e nos SLOs da aplicação.

---

# Trade-offs

## `-Xmx`

Define o **limite máximo da Java Heap**.

### Aumentar `Xmx`

**Vantagens:**

- mais espaço para objetos vivos;
- mais espaço entre ciclos de coleta;
- pode reduzir pressão de GC em determinados workloads.

**Trade-offs:**

- aumenta o limite de memória disponível para a JVM;
- pode esconder temporariamente um problema de retenção;
- não corrige memory leak;
- deve ser analisado considerando a memória total disponível para o processo/container.

### Resposta de entrevista

> Aumentar a Heap pode reduzir pressão de GC quando a aplicação realmente precisa de mais espaço, mas não resolve retenção indevida de objetos. Se a memória continua crescendo mesmo depois das coletas, aumentar `Xmx` pode apenas postergar o problema.

---

## Diminuir `Xmx`

**Vantagem:**

- controla melhor o limite de memória da Heap.

**Trade-offs:**

- menor espaço para alocações;
- potencial aumento da frequência de GC;
- maior risco de `OutOfMemoryError`.

---

# Heap não é toda a memória do processo

Um ponto importante em produção:

```text
Memória do processo JVM
│
├── Heap
├── Metaspace
├── Thread Stacks
├── Code Cache
├── Direct / Native Buffers
└── outras estruturas nativas da JVM
```

Portanto:

```text
processo consumindo muita RAM
              ≠
Heap necessariamente grande
```

### Resposta de entrevista

> Eu não atribuiria automaticamente um aumento de memória do processo à Heap. Se a Heap permanece estável, eu investigaria memória nativa, Metaspace, Direct Buffers, stacks das threads e outras estruturas da JVM.

---

# Heap Dump vs Thread Dump

> Ambos são fotografias de aspectos diferentes da JVM em determinado momento.

| Ferramenta | Mostra | Responde principalmente |
|---|---|---|
| **Heap Dump** | Objetos e relações de referência | O que está ocupando memória e quem mantém esses objetos vivos? |
| **Thread Dump** | Threads, estados e stacks | Onde as threads estão executando, esperando ou bloqueadas? |

---

## Heap Dump

Utilizado principalmente para investigar:

- uso excessivo de Heap;
- retenção de objetos;
- memory leaks;
- objetos dominantes;
- caminhos até GC Roots.

Perguntas importantes:

```text
Quais objetos estão consumindo memória?
```

```text
Quem está mantendo esses objetos vivos?
```

```text
Qual é o caminho até um GC Root?
```

### Resposta de entrevista

> Heap Dump mostra os objetos presentes na Heap e suas relações de referência. Eu o utilizaria principalmente para descobrir quais objetos estão retendo memória e qual caminho de referências, até um GC Root, impede que sejam coletados.

---

## Thread Dump

Mostra:

- threads existentes;
- estado das threads;
- stack traces;
- threads bloqueadas;
- threads esperando;
- possíveis deadlocks;
- pontos de contenção.

### Resposta de entrevista

> Thread Dump mostra o estado das threads e suas stacks naquele instante. É útil para investigar bloqueios, deadlocks, contenção ou entender onde as threads estão gastando tempo.

---

# Investigação em produção

A ordem é importante:

```text
Métricas / GC Logs
        ↓
Identificar padrão
        ↓
Heap Dump / Thread Dump
        ↓
Encontrar causa
        ↓
Só depois ajustar JVM/GC
```

## 1. Começo por métricas e GC logs

Observo:

- frequência das coletas;
- duração das pausas;
- Heap utilizada;
- Heap utilizada **após GC**;
- taxa de alocação;
- CPU;
- comportamento da Old Generation;
- p95 / p99 da aplicação.

---

## 2. Heap após GC continua crescendo

Se observo:

```text
GC
↓
Heap cai pouco

GC
↓
Heap cai menos

GC
↓
baseline continua crescendo
```

há indício de **retenção crescente de objetos**.

Nesse caso:

> Eu coletaria um Heap Dump e investigaria quais objetos continuam alcançáveis, quais possuem maior retained size e quais caminhos até GC Roots estão mantendo esses objetos vivos.

---

## 3. Processo cresce, mas Heap estável

Não culpo o GC imediatamente.

Investigo:

- Metaspace;
- Direct Buffers;
- memória nativa;
- quantidade de threads;
- tamanho das stacks;
- bibliotecas nativas.

---

## 4. Heap estável, mas latência aumenta

Se:

```text
Heap OK
GC OK
p99 ↑
```

investigo execução das threads.

Uso **Thread Dumps** para procurar:

- bloqueios;
- contenção;
- threads esperando I/O;
- saturação de pools;
- deadlocks.

---

## 5. Somente depois penso em tuning

Depois de identificar o comportamento:

```text
causa
 ↓
evidência
 ↓
tuning
```

Posso avaliar:

- `Xmx`;
- `Xms`;
- G1;
- ZGC;
- metas de pausa;
- arquitetura da aplicação.

> **Regra de produção:** tuning deve ser consequência do diagnóstico, e não substituto para ele.

---

# Resposta completa para entrevista

Se perguntarem **“O que você entende de Java Memory e Garbage Collection?”**, uma resposta objetiva pode ser:

> A JVM utiliza diferentes áreas de memória. A Heap é compartilhada entre as threads e armazena objetos e arrays, enquanto cada thread possui sua própria Stack para acompanhar a execução dos métodos.
>
> Na Heap, coletores geracionais trabalham com Young e Old Generation. Objetos normalmente começam na Young e, quando sobrevivem a várias coletas, podem ser promovidos para a Old.
>
> O Garbage Collector determina quais objetos podem ser coletados através de reachability a partir dos GC Roots. Se não existe mais um caminho de referências fortes até determinado objeto, ele fica elegível para coleta.
>
> Em relação aos coletores, o G1 busca equilibrar throughput e metas de pausa, enquanto o ZGC prioriza baixa latência realizando uma parcela maior do trabalho concorrentemente.
>
> Em produção, eu começo analisando métricas e GC logs. Se a ocupação da Heap após GC continua crescendo, parto para Heap Dump para investigar retenção e caminhos até GC Roots. Se a Heap está estável, mas há problema de latência, utilizo Thread Dumps para investigar bloqueios, contenção ou saturação. Só depois dessas evidências considero tuning de Heap ou mudança de coletor.

Essa última resposta é a que eu usaria como **resposta-base de 1–2 minutos**. A partir dela, o entrevistador provavelmente vai aprofundar em `GC Roots`, `G1`, `ZGC`, `memory leak`, `Heap Dump` ou investigação de produção.