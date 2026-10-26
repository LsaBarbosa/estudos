# Programação Orientada Objeto

## Encapsulamento: Protege regras de negócio.

1. Não é `setSaldo()`, é `depositar()` com validações que protegem as regras de negócio.
2. Uso do princípio `Tell, Dont Ask`, em que se delega a classe a responsabilidade.
   - → Errado  → if(valor >= 0 ) {setSaldo(valor)}.
   - → Correto → depositar(valor)

## Abstração: Expor o que deve ser feito, sem revelar como será feito.

1. `UserService` que por meio de composição tem a interface `UserProvider`,não sabe como é feito o o save.
2. Uso do princípio da `Dependency Inversion`:
   - » Módulos de alto nivel não dependem de detalhes de baixo nível
   - » Ambos dependem de abstrações.

## Polimorfismo: Diferentes implementações atendem os mesmo contrato.

1. Princípio Open close, novo comportamento não altera diversas classes, uso de interface.
2. `DatabaseProvider` e `AWSProvider` implementam a mesma interface `UserProvider`, implementando o save()
   - → Com 2 beans o spring não sabe como injetar. Pode-se usar:
     - »`@Qualifier` para escolha fixa  ou `Strategy` para escolha dinâmica

   - → Com `Strategy` o Spring pode injetar as implementações com um `MAP`:

     ```java
     private final Map<String, UserProvider> providers;
     ```
     - » implementações recebem o nome no `@Component("database")` que é a chave no map.
     - » UserProvider provider = providers.get(destination); passando a chave o map define o provider.

     - → Sobrescrita (override): Método iguais em classes diferentes,a escolha depende do objeto real
     - → Sobrecarga  (Overload): Método diferentes na mesma classe, mas com parametros diferentes

| Conceito    | O que muda                        | Quando é escolhido      |
| ----------- | --------------------------------- | ----------------------- |
| Sobrescrita | A implementação/classe            | Em tempo de execução    |
| Sobrecarga  | Os parâmetros                     | Em tempo de compilação  |
| Strategy    | O objeto que executará a operação | Pela regra da aplicação |

## Herança: Uma classe especializa a outra, uma relação de `é um`.

1. Quando uma `ConflictException extends RuntimeException`, uma é a outra, ou quando ocorre com Repository que extends a JpaRepository.
2. Princípio de Substituição de Liskov: Uma subclass deve poder substituir a super class sem quebrar o sistema.

### Classe Abstrata

- Serve:
  - Como modelo base com métodos comuns para classes filhas

- Trade-Off:
  - Limita herança multipla, pois class abstrata so pode ser extendida ma única
  - Rigidez, umar a abstrata muda todas as filhas

- Pode conter:
  - Métodos Abstratos ou Concretos
  - Atributos Protected, Private, Public
  - Membros Estáticos ou não Estáticos

- Métodos estáticos:
  - Não possuem implementação apena a assinatura
  - Podendo ser Sobrescritos(@overrider) para quem a extende
  - Ou mantendo o original com metodo super().

### Interface

- Serve:
  - Como composição
  - Define contrato de comportamento
  - Polimorfismo

- Trade-Off:
  - Aumenta a quantidade de arquivos e o nível de abstração, dificultando às vezes a navegação rápida

- Não contêm:
  - Métodos Concretos
  - Atributos Protected, Private

- Métodos estáticos e/ou final:
  - Não possuem implementação apena a assinatura
  - Podendo ser Sobrescritos(@overrider) para quem a extende
  - Permite palavra reservada default

## Composição: Construir uma classe usando outos objetos, a relação `tem um`.

1. Permite substituir comportamentos sem criar hierarquia de classes.
2. Uso da injeção de dependencia:
   - → Sem injeção, a classe cria a dependencia : private final UserProvider provider = new DatabaseUserProvider();
     - Dificulta troca e testes pois fica fortemente acoplado
   - → Com injeção: A classe não cria o objeto, ela declara " para funcionar preciso do objeto"
     - o spring encontra a implementação e fornece o objeto com base nas anotações (@Component,@Service,...)
   - → Uso do princípio da `Dependency Inversion`:

---

# Equals() & HashCode()

## Conceito.

| Método       | Responsabilidade                                                               |
| ------------ | ------------------------------------------------------------------------------ |
| `==`         | Compara se duas referências apontam para o **mesmo objeto**                    |
| `equals()`   | Compara se dois objetos são **logicamente equivalentes**                       |
| `hashCode()` | Gera um número utilizado para localizar objetos em estruturas baseadas em hash |

1. O HashCode encontra o Bucket, Equals() confirma se é o objeto procurado.

```java
UUID id = UUID.randomUUID();
Product product1 = new Product(id, "Notebook");
Product product2 = new Product(id, "Notebook");
System.out.println(product1 == product2);      // false
System.out.println(product1.equals(product2)); // true
```
```
customer1.hashCode()
        ↓
determina o bucket
        ↓
procura objetos naquele bucket
        ↓
usa equals() para verificar igualdade
```
```
hashCode()
    ↓
onde procurar

equals()
    ↓
é realmente o mesmo objeto lógico?
```
---

# Collection

| Interface  | Característica                     | Duplicados | Ordem                    | Uso comum                                |
| ---------- | ---------------------------------- | ---------: | ------------------------ | ---------------------------------------- |
| `List`     | Sequência de elementos             |        Sim | Mantém posição           | Retorno de APIs, resultados de consultas |
| `Set`      | Elementos únicos                   |        Não | Depende da implementação | IDs, permissões, dias da semana          |
| `Map<K,V>` | Associação chave/valor             |  Chave não | Depende da implementação | Cache, agrupamentos, índices             |
| `Queue`    | Elementos aguardando processamento |        Sim | Normalmente FIFO         | Tarefas e processamento                  |
| `Deque`    | Inserção e remoção nas duas pontas |        Sim | Sequencial               | Fila ou pilha                            |

## `List`

Mantém a **ordem de inserção**, permite elementos duplicados e oferece acesso por posição.

| Implementação          | Acesso por índice |                             Inserção/remoção | Pontos fortes                                                                 | Trade-offs                                                                   | Uso recomendado                                                    |
| ---------------------- | ----------------: | -------------------------------------------: | ----------------------------------------------------------------------------- | ---------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| `ArrayList`            |              O(1) |  Final: O(1) amortizado<br>Início/meio: O(n) | Acesso rápido por índice; boa localidade de memória; menor overhead           | Inserções e remoções no início ou meio deslocam elementos                    | Escolha padrão para listas com muitas leituras                     |
| `LinkedList`           |              O(n) | Extremidades: O(1)<br>Busca da posição: O(n) | Inserções e remoções eficientes nas extremidades; também implementa `Deque`   | Acesso por índice lento; maior consumo de memória; baixa localidade de cache | Filas e deques quando suas operações específicas forem necessárias |
| `CopyOnWriteArrayList` |              O(1) |                                         O(n) | Iteração e leitura seguras sem bloqueio; iteradores trabalham sobre snapshots | Cada alteração cria uma nova cópia do array; alto custo de memória e escrita | Cenários concorrentes com muitas leituras e raras alterações       |

> `LinkedList` não torna automaticamente inserções no meio rápidas: primeiro é necessário localizar a posição, o que custa O(n).

---

## `Set`

Armazena elementos **únicos**, não permitindo duplicatas segundo `equals()` e `hashCode()` ou conforme o comparador utilizado.

| Implementação         | Ordem                             | `add` / `contains` / `remove` | Pontos fortes                                                  | Trade-offs                                                                        | Uso recomendado                                       |
| --------------------- | --------------------------------- | ----------------------------: | -------------------------------------------------------------- | --------------------------------------------------------------------------------- | ----------------------------------------------------- |
| `HashSet`             | Não garante                       |                 O(1) esperado | Unicidade e busca rápida; escolha padrão                       | Não preserva ordem; depende da implementação correta de `equals()` e `hashCode()` | Conjuntos sem necessidade de ordenação                |
| `LinkedHashSet`       | Ordem de inserção                 |                 O(1) esperado | Unicidade preservando a ordem de inserção                      | Consome mais memória que `HashSet`                                                | Conjuntos previsíveis para iteração e retorno de APIs |
| `TreeSet`             | Ordem natural ou por `Comparator` |                      O(log n) | Mantém os elementos ordenados; permite consultas por intervalo | Mais lento que estruturas baseadas em hash; elementos precisam ser comparáveis    | Dados únicos que precisam permanecer ordenados        |
| `EnumSet`             | Ordem de declaração do enum       |                          O(1) | Muito eficiente em memória e desempenho                        | Aceita somente elementos do mesmo tipo `enum`; não aceita `null`                  | Flags, permissões e conjuntos de valores enumerados   |
| `CopyOnWriteArraySet` | Ordem de inserção                 |  Busca: O(n)<br>Escrita: O(n) | Leitura e iteração seguras em concorrência                     | Escritas caras; busca linear                                                      | Pequenos conjuntos concorrentes com raras alterações  |

---

## `Map`

Armazena associações entre **chaves e valores**. As chaves são únicas, mas diferentes chaves podem apontar para valores iguais.

| Implementação       | Ordem                                    | `get` / `put` / `remove` | Pontos fortes                                                         | Trade-offs                                                                     | Uso recomendado                                           |
| ------------------- | ---------------------------------------- | -----------------------: | --------------------------------------------------------------------- | ------------------------------------------------------------------------------ | --------------------------------------------------------- |
| `HashMap`           | Não garante                              |            O(1) esperado | Busca rápida; escolha padrão para mapas                               | Não é thread-safe; depende de `equals()` e `hashCode()` corretos               | Mapeamento geral sem necessidade de ordenação             |
| `LinkedHashMap`     | Inserção ou acesso                       |            O(1) esperado | Ordem previsível; suporta política baseada em acesso                  | Consome mais memória que `HashMap`                                             | Cache LRU e respostas com ordem previsível                |
| `TreeMap`           | Ordem natural das chaves ou `Comparator` |                 O(log n) | Mantém as chaves ordenadas; suporta consultas por intervalo           | Operações mais lentas que mapas baseados em hash                               | Rankings, intervalos e navegação ordenada por chave       |
| `ConcurrentHashMap` | Não garante                              |            O(1) esperado | Acesso concorrente eficiente; leituras não bloqueiam o mapa inteiro   | Não permite chave ou valor `null`; operações compostas exigem métodos atômicos | Estado compartilhado entre múltiplas threads              |
| `EnumMap`           | Ordem de declaração do enum              |                     O(1) | Muito eficiente; representação interna compacta                       | Somente aceita chaves de um único tipo `enum`; não permite chave `null`        | Estratégias e configurações indexadas por enum            |
| `WeakHashMap`       | Não garante                              |            O(1) esperado | Remove entradas quando as chaves deixam de possuir referências fortes | Entradas podem desaparecer após atuação do Garbage Collector                   | Metadados e caches associados ao ciclo de vida de objetos |

---

## Resumo para escolha

| Necessidade                           | Implementação indicada |
| ------------------------------------- | ---------------------- |
| Lista para uso geral                  | `ArrayList`            |
| Fila ou deque                         | `ArrayDeque`           |
| Lista concorrente com muitas leituras | `CopyOnWriteArrayList` |
| Conjunto para uso geral               | `HashSet`              |
| Conjunto preservando inserção         | `LinkedHashSet`        |
| Conjunto ordenado                     | `TreeSet`              |
| Conjunto de enums                     | `EnumSet`              |
| Mapa para uso geral                   | `HashMap`              |
| Mapa preservando ordem                | `LinkedHashMap`        |
| Mapa ordenado por chave               | `TreeMap`              |
| Mapa concorrente                      | `ConcurrentHashMap`    |
| Mapa com chaves enum                  | `EnumMap`              |

---

# Threads e Paralelismo

| Conceito          | Definição                                                                            | Exemplo                                        |
| ----------------- | ------------------------------------------------------------------------------------ | ---------------------------------------------- |
| **Thread**        | Unidade de execução dentro de um processo Java.                                      | Thread atendendo uma requisição HTTP.          |
| **Concorrência**  | Várias tarefas progridem no mesmo período, alternando ou executando simultaneamente. | Processar requisição enquanto envia e-mail.    |
| **Paralelismo**   | Tarefas executam literalmente ao mesmo tempo em diferentes núcleos.                  | Processar grandes cálculos em vários núcleos.  |
| **Assincronismo** | O chamador não espera a tarefa terminar.                                             | Disparar um e-mail e devolver a resposta HTTP. |
| **Thread pool**   | Conjunto controlado de threads reutilizáveis.                                        | `ThreadPoolTaskExecutor`.                      |

* Concorrencia é lidar com multiplas tarefas, Paralelismo é executar multiplas tarefas

## Regra prática

- Visibilidade: Uma thread consegue ver a alteração feita por outra thread.
- Atomicidade: Operação acontece por completo sem um thread interferir no meio.
| Recurso         |                       Visibilidade |                      Atomicidade | Observação                                            |
| --------------- | ---------------------------------: | -------------------------------: | ----------------------------------------------------- |
| `synchronized`  |                                Sim |                              Sim | Desde que todas as threads usem o mesmo monitor       |
| `volatile`      |                                Sim |   Apenas leitura/escrita simples | Não protege operações como `++`                       |
| `AtomicInteger` |                                Sim | Sim para suas operações atômicas | Não protege múltiplos estados relacionados            |
| `Lock`          |                                Sim |                              Sim | Desde que todas as threads usem o mesmo objeto `Lock` |
| Imutabilidade   | Sim, quando publicada corretamente |                   Não há mutação | Evita o problema em vez de sincronizá-lo              |

1. Um contador: AtomicInteger ou LongAdder.
2. Vários campos que precisam mudar juntos: synchronized ou Lock.
3. Apenas publicação/visibilidade de estado: volatile.
4. Melhor solução, quando possível: objetos imutáveis.
- `Synchronized` quando:
  - Vários campos precisam ser atualizados juntos. || Existe uma regra de negócio que deve ser indivisível.
  - O bloqueio é simples. ||   Não precisa de timeout ou interrupção avançada.
- `Volatile` quando:
  - Há uma variável simples de estado. || Uma thread escreve e outras leem.
  - A nova decisão não depende do valor anterior.  || Não existe uma operação composta.

```text
Preciso compartilhar um estado entre threads?
            │
            ├── Não → nenhuma sincronização necessária
            │
            └── Sim
                 │
                 ├── Posso torná-lo imutável?
                 │       └── Sim → prefira imutabilidade
                 │
                 ├── É apenas uma flag ou referência?
                 │       └── Sim → volatile
                 │
                 ├── É uma operação simples sobre um valor?
                 │       └── Sim → AtomicInteger/AtomicLong
                 │
                 └── Há múltiplas operações ou estados?
                         └── synchronized ou Lock
```

## apis 

| API                 | Quando usar                                                 |
| ------------------- | ----------------------------------------------------------- |
| `Runnable`          | Tarefa sem retorno                                          |
| `Callable<T>`       | Tarefa com retorno e possibilidade de checked exception     |
| `Future<T>`         | Representa resultado futuro, normalmente obtido com `get()` |
| `Executor`          | Abstração para execução de tarefas                          |
| `ExecutorService`   | Pool de threads, submissão e encerramento                   |
| `CompletableFuture` | Composição de tarefas assíncronas                           |
| `ForkJoinPool`      | Divisão recursiva de tarefas CPU-bound                      |
| `AtomicInteger`     | Operação atômica sobre um valor                             |
| `ConcurrentHashMap` | Mapa preparado para acesso concorrente                      |
| `Semaphore`         | Limita quantidade de acessos simultâneos                    |
| `CountDownLatch`    | Espera um conjunto de tarefas terminar                      |

---

# JVM e Memória

## Heap, Stack e Metasapce

* Objetos ficam no Heap, enquanto cada thread possui sua própria Stack.
* A Metaspace armazena informações das classes carregadas pela JVM.

| Área          | O que guarda                                        | Problema comum                      |
| ------------- | --------------------------------------------------- | ----------------------------------- |
| **Heap**      | Objetos e arrays                                    | `OutOfMemoryError: Java heap space` |
| **Stack**     | Chamadas de métodos, variáveis locais e referências | `StackOverflowError`                |
| **Metaspace** | Metadados das classes                               | `OutOfMemoryError: Metaspace`       |

## Garbage Collector

* O Garbage Collector não remove objetos apenas porque não estão sendo usados naquele momento.
* Ele remove objetos que não possuem mais referências alcançáveis a partir dos GC Roots.

| GC              | Melhor uso                      | Trade-off                               |
| --------------- | ------------------------------- | --------------------------------------- |
| **G1 GC**       | Aplicações Spring Boot em geral | Equilibra latência e throughput         |
| **Parallel GC** | Processamentos batch            | Maior throughput, pausas maiores        |
| **ZGC**         | Sistemas sensíveis à latência   | Menores pausas, maior custo operacional |
| **Serial GC**   | Aplicações pequenas             | Simples, mas não escala bem             |

Eu começaria com o G1, que é adequado para a maioria das APIs Spring Boot,
e somente trocaria o collector com base em métricas e testes de carga.

## Erros

1. Memory Leak
   - Memory leak em Java ocorre quando objetos que não são mais necessários continuam alcançáveis e, por isso, o Garbage Collector não consegue removê-los.
2. OutOfMemoryError não é apenas Heap
   - Java heap space: objetos demais;
   - Metaspace: classes demais ou classloader leak;
   - Unable to create native thread: muitas threads ou pouca memória nativa;
   - Direct buffer memory: buffers fora do Heap;
   - GC overhead limit: JVM passa quase todo o tempo coletando memória.

## Trade-Offs

| Heap maior                       | Heap menor                  |
| -------------------------------- | --------------------------- |
| Menos coletas frequentes         | GC mais frequente           |
| Suporta mais objetos vivos       | Menor consumo               |
| Pode aumentar duração das pausas | Maior risco de OOM          |
| Pode esconder memory leaks       | Mais fácil atingir o limite |
A configuração ideal é resultado de teste de carga, comportamento do GC, quantidade de objetos vivos e limite do ambiente.

---

# Exceptions

## Fluxo

```text
Controller
    ↓
Service executa regra de negócio
    ↓
Service lança uma custom exception
    ↓
@RestControllerAdvice captura
    ↓
Retorna status HTTP + JSON padronizado
```

Lançar exceções específicas de negócio na camada de serviço, normalmente estendendo RuntimeException,
e tratá-las de forma centralizada com @RestControllerAdvice. O handler converte cada exceção em um status HTTP adequado e uma resposta padronizada, sem expor detalhes sensíveis.

| Tema                | O que responder                                                       |
| ------------------- | --------------------------------------------------------------------- |
| Checked exception   | Exige `try/catch` ou declaração com `throws`. Exemplo: `IOException`. |
| Unchecked exception | Estende `RuntimeException` e não exige tratamento obrigatório.        |
| `throw`             | Lança uma exceção.                                                    |
| `throws`            | Declara que um método pode propagar uma checked exception.            |
| `finally`           | Executa independentemente de sucesso ou falha.                        |
| Try-with-resources  | Fecha automaticamente arquivos, streams e conexões.                   |
| Custom exception    | Representa uma falha específica do domínio.                           |
| Exception global    | Centraliza o tratamento com `@RestControllerAdvice`.                  |

## Trade-off

| Abordagem                | Vantagem                                                                     | Desvantagem                                                                             |
| ------------------------ | ---------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| **Checked exceptions**   | Obrigam o chamador a tratar ou declarar a falha com `try/catch` ou `throws`. | Podem poluir assinaturas e gerar muitos blocos `try/catch`.                             |
| **Unchecked exceptions** | Mantêm o código mais limpo e funcionam bem para exceções de negócio.         | Podem se propagar até a camada web quando não existe tratamento adequado.               |
| **Handler global**       | Padroniza as respostas de erro e mantém os controllers menores.              | Pode se tornar uma classe grande e difícil de manter caso concentre muitos tratamentos. |

## Quiz

| Pergunta                                                   | Resposta para entrevista                                                                                                                                                        |
| ---------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Checked ou unchecked para regra de negócio?**            | Normalmente **unchecked**, porque falhas de negócio não costumam ser tecnicamente recuperáveis pelo chamador. Também se integram ao rollback padrão do Spring.                  |
| **Você coloca `try/catch` no controller?**                 | Evito. Deixo a exceção subir e centralizo o tratamento com `@RestControllerAdvice`.                                                                                             |
| **Pode capturar `Exception`?**                             | Sim, como fallback na fronteira da aplicação. Porém, não deve substituir handlers específicos. Deve registrar a causa internamente e retornar uma mensagem genérica ao cliente. |
| **Exception deve carregar status HTTP?**                   | Prefiro que a exception represente apenas o domínio. O adaptador web ou handler global decide o status HTTP, reduzindo o acoplamento com o protocolo.                           |
| **Qual o problema de lançar `RuntimeException` genérica?** | Ela não comunica o significado da falha e dificulta tratamento específico, testes, manutenção e observabilidade.                                                                |

| Tema                     | Resposta                                                                                             |
| ------------------------ | ---------------------------------------------------------------------------------------------------- |
| **Estratégia utilizada** | Uso custom exceptions para representar falhas de negócio, normalmente estendendo `RuntimeException`. |
| **Onde lançar**          | A camada de serviço identifica a falha e lança a exceção.                                            |
| **Onde tratar**          | Um `@RestControllerAdvice` converte a exceção em uma resposta HTTP padronizada.                      |
| **Controllers**          | Evito blocos `try/catch` nos controllers.                                                            |
| **Segurança**            | Não exponho stack trace, mensagens de banco ou detalhes internos.                                    |
| **Causa original**       | Preservo a exception original ao traduzir a falha.                                                   |
| **Transações**           | Considero que unchecked exceptions provocam rollback por padrão no Spring.                           |
