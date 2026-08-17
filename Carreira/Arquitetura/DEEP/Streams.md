# Streams
> Streams são uma abstração declarativa para processamento de dados sobre uma fonte.
> 
> As operações intermediárias constroem uma pipeline lazy, enquanto uma operação terminal dispara o processamento.Essa execução lazy permite processar os elementos através da pipeline e aproveitar short-circuiting, em vez de obrigatoriamente materializar uma coleção intermediária após cada operação.
>
> Eu diferencio operações stateless, como `map` e `filter`, de operações stateful, como `sorted` e `distinct`, porque estas últimas podem exigir buffering, memória e coordenação, principalmente em paralelo.
>
> Também tento manter pipelines livres de side effects. Isso se torna ainda mais importante com Parallel Streams, porque estado mutável compartilhado introduz problemas de concorrência.
>
> Eu não utilizo `parallelStream()` apenas porque está disponível. Avalio o tamanho da entrada, o custo computacional por elemento, facilidade de particionamento, necessidade de ordenação, efeitos colaterais e principalmente o modelo de concorrência da aplicação.
>
> Em relação a `Optional`, eu o vejo principalmente como uma ferramenta para modelar ausência em retornos. Evito `get()` indiscriminado, e normalmente não utilizo Optional como atributo de entidade JPA nem como parâmetro apenas para representar argumento opcional.

| Tema | Conceito | Explicação breve | Exemplo / ponto-chave |---
|---|---|---|---|
| **`map()` x `flatMap()`** | **Transformação x achatamento** | `map()` transforma **1 elemento em 1 resultado**. `flatMap()` transforma um elemento em uma estrutura/Stream e depois **remove um nível de aninhamento**. | `Stream<List<Item>>` → `flatMap(List::stream)` → `Stream<Item>` |
| **Stateless x Stateful** | **Sem estado x com estado entre elementos** | Operações stateless processam cada elemento independentemente. Stateful precisam lembrar elementos anteriores ou armazenar parte da pipeline. | Stateless: `map`, `filter`. Stateful: `distinct`, `sorted`. Stateful pode exigir **buffering, memória e coordenação**. |
| **Short-circuiting** | **Encerramento antecipado** | Algumas operações conseguem produzir o resultado sem percorrer toda a fonte. Isso funciona em conjunto com lazy evaluation. | `limit(10)`, `findFirst()`, `findAny()`, `anyMatch()`, `allMatch()`, `noneMatch()` |
| **`peek()`** | **Observação da pipeline** | É indicado principalmente para diagnóstico/debug. Não deve ser usado como mecanismo principal para alterar objetos, salvar dados ou implementar regra de negócio. | Prefira `map()` para transformação e uma operação explícita para efeitos colaterais. |
| **`reduce()` e associatividade** | **Redução de vários valores para um** | Para funcionar corretamente em paralelo, a operação de redução deve ser associativa, pois partes diferentes podem ser calculadas separadamente e depois combinadas. | Soma é associativa: `(a+b)+c = a+(b+c)`. Subtração não é. |
| **`reduce()` x `collect()`** | **Redução x acumulação mutável** | `reduce()` é adequado para produzir um valor final, normalmente de forma imutável. `collect()` é próprio para acumular elementos em estruturas mutáveis. | `reduce(BigDecimal.ZERO, BigDecimal::add)` x `collect(toList())` |
| **`Stream.toList()`** | **Resultado não modificável** | O `toList()` do Stream retorna uma lista não modificável. Não se deve tentar adicionar ou remover elementos posteriormente. | `stream.toList().add(x)` → `UnsupportedOperationException` |
| **`parallelStream()`** | **Processamento paralelo com overhead** | Paralelizar envolve dividir o trabalho, agendar tarefas, executar em múltiplas threads e combinar resultados. Portanto, não significa automaticamente melhor performance. | Só compensa quando o ganho do processamento supera o custo do paralelismo. |
| **CPU-bound x I/O-bound** | **Uso adequado do paralelismo** | Parallel Streams tendem a se encaixar melhor em operações CPU-bound independentes. I/O bloqueante pode ocupar workers esperando rede, banco ou filesystem. | Bom: cálculo pesado. Ruim: várias chamadas HTTP bloqueantes dentro de `parallelStream()`. |
| **Estado mutável + Parallel Stream** | **Race condition** | Múltiplas threads modificando o mesmo objeto mutável podem produzir corrupção de dados, resultados incompletos ou comportamento não determinístico. | Evitar `parallelStream().forEach(lista::add)` com `ArrayList`. Prefira `map(...).toList()`. |
| **`orElse()` x `orElseGet()`** | **Eager x lazy fallback** | `orElse()` avalia o argumento antes da chamada, mesmo quando o `Optional` possui valor. `orElseGet()` recebe um `Supplier` executado somente se estiver vazio. | `orElse(buscarBanco())` pode consultar o banco desnecessariamente. `orElseGet(this::buscarBanco)` não. |

--- 
| Conceito | Frase para memorizar |
|---|---|
| `map` | **Transforma um elemento em outro.** |
| `flatMap` | **Transforma e achata estruturas aninhadas.** |
| Stateless | **Não depende dos elementos anteriores.** |
| Stateful | **Precisa manter estado/buffer da pipeline.** |
| Short-circuit | **Pode terminar antes de consumir todo o Stream.** |
| `peek` | **Serve para observar, não para regra de negócio.** |
| `reduce` | **Combina elementos em um resultado; em paralelo, exige associatividade.** |
| `collect` | **Acumula elementos em estruturas como `List`, `Set` e `Map`.** |
| `toList()` | **Retorna lista não modificável.** |
| `parallelStream` | **Paralelismo tem custo; precisa ser medido.** |
| CPU-bound | **Melhor candidato para Parallel Stream.** |
| I/O-bound | **Pode bloquear workers e saturar o pool.** |
| Estado compartilhado | **Parallel Stream + mutabilidade compartilhada = risco de race condition.** |
| `orElse` | **Fallback eager.** |
| `orElseGet` | **Fallback lazy.** |
