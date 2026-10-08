# Conceitos para Estudar — Java, SOLID, Design Patterns, Spring e Backend

Material de revisão com **respostas curtas para estudo e entrevistas**, organizado por pré-requisitos. A sequência vai dos fundamentos de Java à arquitetura distribuída e à operação em produção.

> Siga a ordem dos capítulos e use os sumários para revisão. Aplique os conceitos em pequenos exercícios e testes desde o início; o capítulo de testes consolida também as integrações com Spring. Detalhes dependentes de versão ou de banco precisam ser conferidos no ambiente utilizado.

<a id="sumario-geral"></a>

## Sumário Geral

### Etapa 1 — Java, orientação a objetos e design

- [01. Fundamentos de Java](#java-fundamentos-sumario)
- [02. Programação Orientada a Objetos em Java](#java-programacao-orientada-a-objetos-em-java-sumario)
- [03. Java Collections](#java-java-collections-sumario)
- [04. Java Funcional e APIs Modernas](#java-funcional-sumario)
- [05. SOLID e Princípios de Design](#design-solid-sumario)
- [06. Design Patterns — Padrões de Projeto](#design-patterns-sumario)

### Etapa 2 — Execução, concorrência e build

- [07. JVM Memory & Garbage Collection](#java-jvm-memory-garbage-collection-sumario)
- [08. Concorrência em Java](#java-concorrencia-em-java-sumario)
- [09. Build e Dependências — Maven e Gradle](#build-dependencias-sumario)

### Etapa 3 — Persistência e comunicação HTTP

- [10. SQL & Bancos Relacionais](#sql-sql-bancos-relacionais-sumario)
- [11. HTTP e APIs REST](#http-rest-sumario)

### Etapa 4 — Spring e testes automatizados

- [12. Ecossistema Spring — Visão Geral](#spring-ecossistema-spring-visao-geral-sumario)
- [13. Spring Core](#spring-spring-core-sumario)
- [14. Spring Boot](#spring-spring-boot-sumario)
- [15. Spring MVC](#spring-spring-mvc-sumario)
- [16. Spring Data](#spring-spring-data-sumario)
- [17. Spring Security](#spring-spring-security-sumario)
- [18. Testes Automatizados em Java e Spring](#testes-java-spring-sumario)

### Etapa 5 — Arquitetura e sistemas distribuídos

- [19. Arquitetura de Software](#arquitetura-arquitetura-de-software-sumario)
- [20. Microsserviços](#microservicos-microservicos-sumario)
- [21. Kafka](#kafka-kafka-sumario)
- [22. Spring Cloud](#spring-spring-cloud-sumario)

### Etapa 6 — Containers, AWS e entrega contínua

- [23. Docker e Kubernetes](#containers-kubernetes-sumario)
- [24. AWS](#aws-aws-sumario)
- [25. CI/CD](#cicd-cicd-sumario)

> Cada tópico possui seu próprio sumário. Após cada resposta há atalhos para **↑ Sumário do tópico** e **↑ Sumário geral**.

---

<a id="java-fundamentos-sumario"></a>

# 01. Fundamentos de Java

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#java-fundamentos-visao-geral)
- [JDK, JVM e Runtime](#java-fundamentos-jdk-jvm-e-runtime)
- [Compilação, Bytecode e Portabilidade](#java-fundamentos-compilacao-bytecode-e-portabilidade)
- [Tipos Primitivos e Referências](#java-fundamentos-tipos-primitivos-e-referencias)
- [Passagem por Valor](#java-fundamentos-passagem-por-valor)
- [Wrappers, Autoboxing e Unboxing](#java-fundamentos-wrappers-autoboxing-e-unboxing)
- [== vs equals](#java-fundamentos-vs-equals)
- [String, Imutabilidade e String Pool](#java-fundamentos-string-imutabilidade-e-string-pool)
- [StringBuilder vs StringBuffer](#java-fundamentos-stringbuilder-vs-stringbuffer)
- [final, finally e finalize](#java-fundamentos-final-finally-e-finalize)
- [Exceções Checked e Unchecked](#java-fundamentos-excecoes-checked-e-unchecked)
- [throw, throws e Preservação da Causa](#java-fundamentos-throw-throws-e-preservacao-da-causa)
- [try-with-resources e AutoCloseable](#java-fundamentos-try-with-resources-e-autocloseable)
- [Generics e Segurança de Tipos](#java-fundamentos-generics-e-seguranca-de-tipos)
- [Wildcards e PECS](#java-fundamentos-wildcards-e-pecs)
- [Type Erasure](#java-fundamentos-type-erasure)
- [BigDecimal e Valores Monetários](#java-fundamentos-bigdecimal-e-valores-monetarios)
- [java.time e Fusos Horários](#java-fundamentos-java-time-e-fusos-horarios)
- [Annotations e Reflection](#java-fundamentos-annotations-e-reflection)
- [Resumo de Entrevista](#java-fundamentos-resumo-de-entrevista)

---

<a id="java-fundamentos-visao-geral"></a>

## Visão Geral

> Java combina tipagem estática, orientação a objetos e execução de bytecode na JVM. Para dominar frameworks, começo por tipos, referências, contratos de objetos, exceções e gerenciamento de recursos.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-jdk-jvm-e-runtime"></a>

## JDK, JVM e Runtime

> A JVM executa bytecode. O JDK reúne ferramentas como compilador, depurador e runtime; JRE é o conceito de ambiente necessário para executar uma aplicação, cuja distribuição varia conforme o fornecedor e a versão.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-compilacao-bytecode-e-portabilidade"></a>

## Compilação, Bytecode e Portabilidade

> O compilador transforma código Java em bytecode, executado por uma JVM compatível. A portabilidade depende também da versão alvo e de dependências nativas; um binário compilado para uma versão mais nova não roda automaticamente em uma JVM antiga.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-tipos-primitivos-e-referencias"></a>

## Tipos Primitivos e Referências

> Primitivos armazenam valores como números e booleanos. Variáveis de referência apontam para objetos e podem conter `null`; várias referências podem alcançar o mesmo objeto, permitindo alterações compartilhadas quando ele é mutável.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-passagem-por-valor"></a>

## Passagem por Valor

> Java sempre passa argumentos por valor. Para objetos, copia o valor da referência: alterar o objeto pode ser observado pelo chamador, mas reatribuir o parâmetro não troca a variável original do chamador.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-wrappers-autoboxing-e-unboxing"></a>

## Wrappers, Autoboxing e Unboxing

> Wrappers como `Integer` representam primitivos como objetos. Conversões automáticas facilitam APIs genéricas, mas podem gerar alocações e `NullPointerException` ao desempacotar `null`; comparo valores de wrappers com `equals`, não com `==`.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-vs-equals"></a>

## == vs equals

> Para primitivos, `==` compara valores; para referências, compara identidade do objeto. `equals` pode expressar igualdade lógica, mas sua implementação herdada de `Object` também compara identidade. O método `equals` não precisa chamar `hashCode`.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-string-imutabilidade-e-string-pool"></a>

## String, Imutabilidade e String Pool

> `String` é imutável: operações produzem outro resultado sem alterar o conteúdo original. Literais podem compartilhar instâncias no pool, mas isso não torna `==` adequado para comparar texto; uso `equals`.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-stringbuilder-vs-stringbuffer"></a>

## StringBuilder vs StringBuffer

> `StringBuilder` é adequado para construir texto mutável dentro de um mesmo fluxo. `StringBuffer` sincroniza suas operações e tem custo adicional; uma sequência de várias chamadas ainda pode exigir coordenação para ser atômica.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-final-finally-e-finalize"></a>

## final, finally e finalize

> `final` impede reatribuição, sobrescrita ou herança conforme o uso, mas não torna um objeto profundamente imutável. `finally` executa a limpeza ao sair de um `try`, salvo encerramentos abruptos. `finalize` é um mecanismo legado desaconselhado; uso `AutoCloseable` para recursos.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-excecoes-checked-e-unchecked"></a>

## Exceções Checked e Unchecked

> Checked exceptions exigem tratamento ou declaração em `throws`. Unchecked exceptions derivam de `RuntimeException` e não têm essa exigência; a escolha deve refletir o contrato da API. `Error` geralmente representa problemas graves e não deve ser capturado indiscriminadamente.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-throw-throws-e-preservacao-da-causa"></a>

## throw, throws e Preservação da Causa

> `throw` lança uma exceção; `throws` declara exceções que podem escapar do método. Ao traduzir uma falha, preservo sua causa e acrescento contexto útil, evitando capturar e ignorar erros ou registrar a mesma falha em todas as camadas.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-try-with-resources-e-autocloseable"></a>

## try-with-resources e AutoCloseable

> `try-with-resources` fecha recursos `AutoCloseable`, como arquivos e conexões JDBC, inclusive quando ocorre exceção. Os recursos são fechados na ordem inversa de abertura; o GC não substitui esse controle de liberação.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-generics-e-seguranca-de-tipos"></a>

## Generics e Segurança de Tipos

> Generics permitem reutilizar código com tipos verificados pelo compilador, como `List<Pedido>`. Evitam casts desnecessários, mas `List<Integer>` não é subtipo de `List<Number>`; a invariância impede inserções incompatíveis.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-wildcards-e-pecs"></a>

## Wildcards e PECS

> `? extends T` é útil quando uma estrutura fornece valores como `T`; `? super T`, quando recebe valores `T`. PECS significa Producer Extends, Consumer Super e orienta assinaturas flexíveis sem perder segurança de tipos.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-type-erasure"></a>

## Type Erasure

> Na implementação usual de Generics, o compilador apaga parâmetros de tipo e insere casts quando necessários. Por isso não posso escrever `new T()` ou testar `instanceof List<String>`; certas informações genéricas ainda existem nos metadados para reflexão.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-bigdecimal-e-valores-monetarios"></a>

## BigDecimal e Valores Monetários

> `BigDecimal` permite aritmética decimal com escala e arredondamento explícitos. Para dinheiro, evito construir a partir de `double` e defino a regra de arredondamento; `equals` considera a escala, enquanto `compareTo` compara o valor numérico.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-java-time-e-fusos-horarios"></a>

## java.time e Fusos Horários

> `Instant` representa um instante na linha do tempo; `LocalDate` uma data sem horário; `LocalDateTime` não inclui fuso. Uso `ZonedDateTime` quando regras de fuso importam e `Clock` injetável para testes previsíveis.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-annotations-e-reflection"></a>

## Annotations e Reflection

> Annotations carregam metadados usados por compiladores, ferramentas ou frameworks. Reflection inspeciona tipos e membros em execução, mas reduz verificações estáticas e pode esbarrar em encapsulamento; evito usá-la onde uma API explícita resolve.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-fundamentos-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Java exige entender valores, referências, igualdade, exceções e tipos antes dos frameworks. Uso Generics para segurança, objetos imutáveis quando possível, `try-with-resources` para recursos externos e APIs adequadas para datas e valores monetários.

[↑ Sumário do tópico](#java-fundamentos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-sumario"></a>

# 02. Programação Orientada a Objetos em Java

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#java-programacao-orientada-a-objetos-em-java-visao-geral)
- [Classe e Objeto](#java-programacao-orientada-a-objetos-em-java-exemplo)
- [Construtores e Inicialização Válida](#java-programacao-orientada-a-objetos-em-java-construtores-e-inicializacao-valida)
- [Modificadores de Acesso e static](#java-programacao-orientada-a-objetos-em-java-modificadores-de-acesso-e-static)
- [Encapsulamento e Invariantes](#java-programacao-orientada-a-objetos-em-java-trade-off)
- [Abstração, Interface e Classe Abstrata](#java-programacao-orientada-a-objetos-em-java-regra-pratica)
- [Herança e Relação IS-A](#java-programacao-orientada-a-objetos-em-java-regra-pratica-2)
- [Polimorfismo e Sobrescrita](#java-programacao-orientada-a-objetos-em-java-overriding-sobrescrita)
- [Overloading vs Overriding](#java-programacao-orientada-a-objetos-em-java-overloading-vs-overriding)
- [Interface: Métodos Abstratos, default e static](#java-programacao-orientada-a-objetos-em-java-interface-metodos-abstratos-default-e-static)
- [Composição e Delegação](#java-programacao-orientada-a-objetos-em-java-3-delegacao)
- [Associação, Agregação e Composição](#java-programacao-orientada-a-objetos-em-java-observacao-pratica)
- [Acoplamento e Coesão](#java-programacao-orientada-a-objetos-em-java-objetivo)
- [equals, hashCode e toString](#java-programacao-orientada-a-objetos-em-java-cuidado)
- [Records, Sealed Classes e Enums](#java-programacao-orientada-a-objetos-em-java-3-enums)
- [Imutabilidade e Cópia Defensiva](#java-programacao-orientada-a-objetos-em-java-imutabilidade-e-copia-defensiva)
- [Modelagem de Objetos Válidos](#java-programacao-orientada-a-objetos-em-java-4-encapsular-invariantes)
- [Refatoração de Modelos OO](#java-programacao-orientada-a-objetos-em-java-correlacao-com-concorrencia)
- [Resumo de Entrevista](#java-programacao-orientada-a-objetos-em-java-resposta-completa-de-entrevista)
- [Resposta de 30 segundos](#java-programacao-orientada-a-objetos-em-java-resposta-de-30-segundos)

---

<a id="java-programacao-orientada-a-objetos-em-java-visao-geral"></a>

## Visão Geral

> Programação Orientada a Objetos é um paradigma que organiza o sistema em objetos com estado e comportamento. Em Java, os principais conceitos são encapsulamento, abstração, herança e polimorfismo. Na prática, eu uso POO para modelar responsabilidades, proteger invariantes e reduzir acoplamento, normalmente favorecendo abstrações e composição quando isso melhora a flexibilidade.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-exemplo"></a>

## Classe e Objeto

> Uma classe define estrutura e comportamento; o objeto é uma instância concreta dessa classe. Eu procuro criar objetos já em estado válido e colocar comportamento próximo do estado que ele controla, evitando expor atributos sem necessidade.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-construtores-e-inicializacao-valida"></a>

## Construtores e Inicialização Válida

> O construtor estabelece o estado inicial do objeto e pode validar invariantes. Evito publicar `this` durante a construção, pois outro código pode observar um objeto parcialmente inicializado.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-modificadores-de-acesso-e-static"></a>

## Modificadores de Acesso e static

> `private`, acesso de pacote, `protected` e `public` controlam exposição. `static` associa um membro à classe, não a uma instância; estado estático mutável pode criar dependência global e problemas de concorrência.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-trade-off"></a>

## Encapsulamento e Invariantes

> Encapsulamento não é apenas colocar atributos como private. É controlar como o estado pode mudar e proteger invariantes. Eu prefiro expor operações de negócio em vez de setters genéricos, porque isso reduz estados inválidos e acoplamento com a implementação interna.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-regra-pratica"></a>

## Abstração, Interface e Classe Abstrata

> Abstração significa expor a capacidade essencial e esconder detalhes de implementação. Em Java, interfaces são ótimas para contratos e desacoplamento. Classes abstratas fazem sentido quando subclasses compartilham estado ou comportamento base e existe uma relação de especialização clara.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-regra-pratica-2"></a>

## Herança e Relação IS-A

> Herança modela uma relação IS-A. Eu uso quando existe uma especialização real e a subclasse pode substituir a superclasse preservando seu contrato. Evito usar herança apenas para reaproveitar código, porque ela aumenta o acoplamento entre pai e filhos.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-overriding-sobrescrita"></a>

## Polimorfismo e Sobrescrita

> Polimorfismo permite tratar diferentes implementações através de um mesmo contrato. Em Java, quando uma referência da interface aponta para uma implementação concreta, o método sobrescrito é resolvido em runtime através de dynamic dispatch.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-overloading-vs-overriding"></a>

## Overloading vs Overriding

> Overloading cria métodos com mesmo nome e parâmetros diferentes, resolvidos conforme tipos conhecidos na compilação. Overriding redefine um método de instância herdado e participa do despacho dinâmico; mudar apenas o retorno não cria uma sobrecarga válida.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-interface-metodos-abstratos-default-e-static"></a>

## Interface: Métodos Abstratos, default e static

> Interfaces podem declarar métodos abstratos e oferecer métodos `default`, `static` e auxiliares privados. A classe implementadora precisa cumprir os métodos abstratos aplicáveis, mas uma interface não se limita a métodos sem implementação.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-3-delegacao"></a>

## Composição e Delegação

> Composição representa relação HAS-A e permite montar comportamentos através de dependências. Eu normalmente prefiro composição quando quero reutilizar comportamento ou trocar implementações, porque ela reduz o acoplamento estrutural criado pela herança.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-observacao-pratica"></a>

## Associação, Agregação e Composição

> Associação representa relacionamento geral entre objetos. Agregação é uma relação todo-parte mais fraca, em que a parte pode existir sozinha. Composição é mais forte e normalmente implica que a parte pertence ao ciclo de vida do todo.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-objetivo"></a>

## Acoplamento e Coesão

> Eu procuro baixa dependência entre componentes e alta coesão dentro de cada classe. Baixo acoplamento facilita substituição e evolução; alta coesão mantém responsabilidades relacionadas no mesmo lugar.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-cuidado"></a>

## equals, hashCode e toString

> equals define igualdade lógica, hashCode precisa respeitar essa igualdade para estruturas hash e toString fornece uma representação textual útil para diagnóstico. Em entidades mutáveis, também tomo cuidado para não alterar campos usados em hashCode enquanto o objeto está dentro de HashMap ou HashSet.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-3-enums"></a>

## Records, Sealed Classes e Enums

> Records sintetizam componentes, acessores e métodos como `equals` e `hashCode`, mas sua imutabilidade é rasa. Sealed classes e interfaces restringem subtipos permitidos. Enums representam um conjunto fechado de constantes que também podem ter estado e comportamento.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-imutabilidade-e-copia-defensiva"></a>

## Imutabilidade e Cópia Defensiva

> Um objeto imutável não muda seu estado observável após a construção. Uso campos finais, validação e controle de referências mutáveis; uma lista dentro de um record ainda exige cópia e atenção aos seus elementos para evitar alterações externas.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-4-encapsular-invariantes"></a>

## Modelagem de Objetos Válidos

> Em modelagem OO eu procuro programar para abstrações, favorecer composição quando não existe uma relação IS-A legítima e manter invariantes dentro dos próprios objetos. Isso reduz acoplamento e evita que regras de negócio fiquem espalhadas.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-correlacao-com-concorrencia"></a>

## Refatoração de Modelos OO

> Em produção eu uso POO para concentrar regras e responsabilidades, não apenas para criar hierarquias. Se encontro muitos condicionais por tipo, avalio polimorfismo. Se há setters permitindo estados inválidos, reforço encapsulamento. Se a hierarquia ficou profunda, normalmente considero composição e delegação.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-resposta-completa-de-entrevista"></a>

## Resumo de Entrevista

> Uso POO para organizar estado e comportamento, proteger invariantes e manter responsabilidades coesas. Prefiro contratos claros e composição; adoto herança quando há substituição válida do tipo base. Records e tipos imutáveis ajudam na representação de dados sem dispensar validação.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-resposta-de-30-segundos"></a>

## Resposta de 30 segundos

> POO em Java organiza o software em objetos com estado e comportamento. Os pilares principais são encapsulamento, abstração, herança e polimorfismo. Eu uso encapsulamento para proteger invariantes, abstrações para desacoplar implementações e polimorfismo para permitir comportamentos diferentes através do mesmo contrato. Herança eu reservo para relações IS-A reais; quando quero flexibilidade e reutilização de comportamento, geralmente prefiro composição.

[↑ Sumário do tópico](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-sumario"></a>

# 03. Java Collections

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#java-java-collections-visao-geral)
- [Complexidade de Tempo e Espaço](#java-java-collections-complexidade-de-tempo-e-espaco)
- [List, Set, Queue, Deque e Map](#java-java-collections-6-map)
- [ArrayList e Complexidade](#java-java-collections-complexidade-tipica)
- [LinkedList e Trade-offs](#java-java-collections-trade-off)
- [Contrato de equals e hashCode](#java-java-collections-problema-classico)
- [Chaves Mutáveis em HashMap e HashSet](#java-java-collections-chaves-mutaveis-em-hashmap-e-hashset)
- [HashSet](#java-java-collections-1-hashset)
- [LinkedHashSet](#java-java-collections-linkedhashset)
- [TreeSet](#java-java-collections-3-treeset)
- [HashMap](#java-java-collections-1-hashmap)
- [Buckets e Colisões no HashMap](#java-java-collections-detalhe-de-entrevista)
- [LinkedHashMap](#java-java-collections-linkedhashmap)
- [TreeMap](#java-java-collections-3-treemap)
- [Comparable vs Comparator](#java-java-collections-comparable-vs-comparator)
- [PriorityQueue](#java-java-collections-complexidade-tipica-2)
- [ArrayDeque](#java-java-collections-2-arraydeque)
- [Iteradores Fail-fast e Remoção Segura](#java-java-collections-3-remocao-segura)
- [Coleções Não Modificáveis e de Tamanho Fixo](#java-java-collections-armadilha-classica)
- [CopyOnWriteArrayList](#java-java-collections-uso-comum)
- [BlockingQueue](#java-java-collections-3-blockingqueue)
- [Como escolher a Collection](#java-java-collections-como-escolher-a-collection)
- [Diagnóstico de Latência em Collections](#java-java-collections-cenario-7-latencia-degradando-com-collection-grande)
- [Resumo de Entrevista](#java-java-collections-resposta-completa-de-entrevista)

---

<a id="java-java-collections-visao-geral"></a>

## Visão Geral

> O Java Collections Framework fornece estruturas de dados padronizadas para armazenar e manipular objetos. As principais abstrações são List, Set, Queue, Deque e Map. A escolha entre elas depende de requisitos como ordem, duplicidade, acesso por índice, busca por chave, ordenação e concorrência.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-complexidade-de-tempo-e-espaco"></a>

## Complexidade de Tempo e Espaço

> Big O descreve como custo cresce com o tamanho da entrada, não o tempo exato em segundos. Distingo caso médio, pior caso e custo amortizado; localidade de memória e tamanho dos objetos também afetam desempenho real.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-6-map"></a>

## List, Set, Queue, Deque e Map

> List representa uma sequência e permite duplicados. Set representa elementos únicos. Queue e Deque são voltadas ao processamento ordenado de elementos. Map representa associações chave-valor e não herda de Collection.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-complexidade-tipica"></a>

## ArrayList e Complexidade

> ArrayList é baseada em array dinâmico. Possui acesso por índice em O(1) e inserção no final em O(1) amortizado. Inserções e remoções no meio são O(n) porque podem exigir deslocamento dos elementos.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-trade-off"></a>

## LinkedList e Trade-offs

> LinkedList é uma lista duplamente encadeada. É eficiente para operações nas extremidades, mas acesso por índice é O(n). Na prática, ArrayList costuma ser preferível para listas gerais por ter melhor localidade de memória e menor overhead.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-problema-classico"></a>

## Contrato de equals e hashCode

> equals define igualdade lógica e hashCode determina a distribuição em estruturas baseadas em hashing. Se dois objetos são iguais segundo equals, obrigatoriamente devem produzir o mesmo hashCode.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-chaves-mutaveis-em-hashmap-e-hashset"></a>

## Chaves Mutáveis em HashMap e HashSet

> Alterar campos usados em `equals` ou `hashCode` depois da inserção pode impedir que o objeto seja encontrado no bucket esperado. Prefiro chaves estáveis e imutáveis; igualdade e hashing precisam permanecer coerentes durante a permanência na estrutura.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-1-hashset"></a>

## HashSet

> HashSet é indicado quando preciso garantir unicidade e não preciso de ordenação. Em condições normais, add, contains e remove possuem custo próximo de O(1).

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-linkedhashset"></a>

## LinkedHashSet

> LinkedHashSet combina unicidade com ordem de inserção previsível. Costuma manter operações de hash em O(1) médio, com memória adicional para registrar a ordem.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-3-treeset"></a>

## TreeSet

> TreeSet mantém os elementos ordenados e oferece operações em O(log n). Eu usaria quando preciso simultaneamente de unicidade e ordenação.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-1-hashmap"></a>

## HashMap

> HashMap é uma estrutura chave-valor baseada em hashing. Em condições normais, get e put são O(1) em média. A qualidade de hashCode e o contrato com equals são fundamentais para funcionamento correto e desempenho.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-detalhe-de-entrevista"></a>

## Buckets e Colisões no HashMap

> HashMap distribui entradas em buckets utilizando hashCode. Quando existem colisões, equals é usado para distinguir as chaves. Em implementações modernas, buckets excessivamente congestionados podem ser convertidos em árvores para evitar degradação severa.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-linkedhashmap"></a>

## LinkedHashMap

> LinkedHashMap mantém ordem previsível de inserção ou, quando configurado, de acesso. Pode apoiar políticas simples de LRU, mas não é thread-safe nem substitui automaticamente um cache com expiração e controle de concorrência.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-3-treemap"></a>

## TreeMap

> TreeMap é indicado quando preciso de um mapa cujas chaves permaneçam ordenadas e quando operações de navegação por intervalo são importantes.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-comparable-vs-comparator"></a>

## Comparable vs Comparator

> Comparable define a ordenação natural do tipo. Comparator define estratégias externas e permite múltiplas formas de ordenação sem modificar a classe.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-complexidade-tipica-2"></a>

## PriorityQueue

> PriorityQueue mantém o menor elemento segundo a ordenação configurada na cabeça. `peek` custa O(1), enquanto inserção e retirada da cabeça custam O(log n); sua iteração não é totalmente ordenada. Uso quando preciso processar repetidamente o próximo item prioritário.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-2-arraydeque"></a>

## ArrayDeque

> ArrayDeque é uma estrutura eficiente para fila ou pilha. Para implementar uma stack moderna em Java, geralmente prefiro ArrayDeque em vez da classe legada Stack.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-3-remocao-segura"></a>

## Iteradores Fail-fast e Remoção Segura

> Iteradores fail-fast podem detectar alterações estruturais indevidas e lançar `ConcurrentModificationException`, inclusive em uma única thread. Essa detecção não é garantia de segurança concorrente. Quando suportado, uso `Iterator.remove` ou operações como `removeIf`, respeitando o contrato da coleção.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-armadilha-classica"></a>

## Coleções Não Modificáveis e de Tamanho Fixo

> `Collections.unmodifiableList` cria uma visão não modificável, mas alterações na lista original continuam visíveis. `List.of` impede mudanças estruturais e rejeita elementos nulos; os objetos contidos ainda podem ser mutáveis. `Arrays.asList` tem tamanho fixo e compartilha o array original.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-uso-comum"></a>

## CopyOnWriteArrayList

> CopyOnWriteArrayList favorece workloads com muitas leituras e poucas escritas. Leituras são simples, mas cada modificação exige copiar a estrutura interna, então não é adequada para alta taxa de escrita.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-3-blockingqueue"></a>

## BlockingQueue

> BlockingQueue é útil para coordenação entre produtores e consumidores, porque fornece operações que podem bloquear quando a fila está vazia ou cheia, ajudando também no controle de backpressure local.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-como-escolher-a-collection"></a>

## Como escolher a Collection

> Eu escolho a Collection a partir do padrão de acesso e das garantias necessárias. Para lookup por chave geralmente começo com HashMap, para sequência com ArrayList, para unicidade com HashSet, para ordenação com TreeMap ou TreeSet e para fila ou pilha com ArrayDeque. Depois considero concorrência e requisitos específicos.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-cenario-7-latencia-degradando-com-collection-grande"></a>

## Diagnóstico de Latência em Collections

> Em produção eu não escolho Collection apenas pelo tipo de dado, mas pelo padrão de acesso. Se há lookup frequente por chave, por exemplo, uma List pode gerar O(n) repetidamente enquanto um HashMap oferece O(1) médio. Também considero memória, ordenação, taxa de escrita, concorrência e volume.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-java-collections-resposta-completa-de-entrevista"></a>

## Resumo de Entrevista

> Escolho a coleção pelo acesso e pelas garantias necessárias: sequência com ArrayList, chave-valor com HashMap, unicidade com Set e fila com Deque. Considero ordem, custo de operações, memória e concorrência, mantendo o contrato entre equals e hashCode.

[↑ Sumário do tópico](#java-java-collections-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-funcional-sumario"></a>

# 04. Java Funcional e APIs Modernas

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#java-funcional-visao-geral)
- [Interfaces Funcionais](#java-funcional-interfaces-funcionais)
- [Lambdas e Method References](#java-funcional-lambdas-e-method-references)
- [Predicate, Function, Consumer e Supplier](#java-funcional-predicate-function-consumer-e-supplier)
- [Stream vs Collection](#java-funcional-stream-vs-collection)
- [Operações Intermediárias e Terminais](#java-funcional-operacoes-intermediarias-e-terminais)
- [map vs flatMap](#java-funcional-map-vs-flatmap)
- [reduce vs collect](#java-funcional-reduce-vs-collect)
- [Collectors, groupingBy e toMap](#java-funcional-collectors-groupingby-e-tomap)
- [Stream.toList e Mutabilidade](#java-funcional-stream-tolist-e-mutabilidade)
- [Optional](#java-funcional-optional)
- [Streams Paralelas](#java-funcional-streams-paralelas)
- [Resumo de Entrevista](#java-funcional-resumo-de-entrevista)

---

<a id="java-funcional-visao-geral"></a>

## Visão Geral

> Java permite combinar objetos com funções para transformar dados de forma declarativa. Lambdas, interfaces funcionais, Streams e Optional ajudam a expressar intenção, desde que eu controle efeitos colaterais e não esconda operações caras.

[↑ Sumário do tópico](#java-funcional-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-funcional-interfaces-funcionais"></a>

## Interfaces Funcionais

> Uma interface funcional possui um único método abstrato relevante para seu contrato e pode receber uma lambda. Métodos `default` e `static` não impedem isso; `@FunctionalInterface` pede ao compilador que verifique a regra.

[↑ Sumário do tópico](#java-funcional-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-funcional-lambdas-e-method-references"></a>

## Lambdas e Method References

> Lambdas implementam comportamentos curtos; method references reutilizam métodos compatíveis, como `Pedido::getId`. Variáveis locais capturadas precisam ser finais ou efetivamente finais, mas o objeto referenciado ainda pode ser mutável.

[↑ Sumário do tópico](#java-funcional-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-funcional-predicate-function-consumer-e-supplier"></a>

## Predicate, Function, Consumer e Supplier

> `Predicate<T>` testa uma condição, `Function<T,R>` transforma um valor, `Consumer<T>` executa uma ação e `Supplier<T>` fornece um valor. São contratos comuns para composição de comportamento sem criar interfaces para cada caso.

[↑ Sumário do tópico](#java-funcional-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-funcional-stream-vs-collection"></a>

## Stream vs Collection

> Collection armazena dados; Stream descreve um pipeline de processamento sobre uma fonte. Streams normalmente são consumidas uma única vez e não alteram a fonte por si mesmas; efeitos colaterais dentro de lambdas podem comprometer o resultado.

[↑ Sumário do tópico](#java-funcional-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-funcional-operacoes-intermediarias-e-terminais"></a>

## Operações Intermediárias e Terminais

> Operações intermediárias, como `filter` e `map`, constroem o pipeline de forma preguiçosa. Operações terminais, como `collect`, `count` e `findFirst`, iniciam o processamento; algumas podem encerrar antes de percorrer todos os elementos.

[↑ Sumário do tópico](#java-funcional-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-funcional-map-vs-flatmap"></a>

## map vs flatMap

> `map` transforma cada elemento em outro valor. `flatMap` transforma cada elemento em um fluxo e combina os resultados, como converter uma lista de pedidos em um fluxo único de seus itens.

[↑ Sumário do tópico](#java-funcional-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-funcional-reduce-vs-collect"></a>

## reduce vs collect

> `reduce` combina valores em um resultado, como uma soma, usando uma operação associativa. `collect` acumula em estruturas ou resultados especializados; para coleções mutáveis, prefiro collectors em vez de modificar um acumulador compartilhado em `reduce`.

[↑ Sumário do tópico](#java-funcional-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-funcional-collectors-groupingby-e-tomap"></a>

## Collectors, groupingBy e toMap

> Collectors permitem agrupar, particionar e construir mapas. Em `toMap`, defino como resolver chaves duplicadas quando elas são possíveis; em grandes agrupamentos, considero memória e volume antes de carregar tudo na aplicação.

[↑ Sumário do tópico](#java-funcional-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-funcional-stream-tolist-e-mutabilidade"></a>

## Stream.toList e Mutabilidade

> `Stream.toList()` retorna uma lista não modificável. `Collectors.toList()` não garante uma implementação nem mutabilidade específicas; quando preciso de uma lista mutável, uso uma construção explícita como `toCollection(ArrayList::new)`.

[↑ Sumário do tópico](#java-funcional-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-funcional-optional"></a>

## Optional

> `Optional` representa a possibilidade de ausência, principalmente em retornos. Uso `map`, `flatMap` e `orElseThrow` para expressar o fluxo; `orElseGet` calcula o fallback sob demanda, enquanto o argumento de `orElse` é avaliado mesmo se houver valor.

[↑ Sumário do tópico](#java-funcional-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-funcional-streams-paralelas"></a>

## Streams Paralelas

> Streams paralelas dividem trabalho, mas introduzem custo de coordenação e podem disputar o pool compartilhado. Avalio volume, associatividade, ausência de estado compartilhado e medições reais; paralelizar chamadas bloqueantes sem controle pode saturar dependências.

[↑ Sumário do tópico](#java-funcional-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-funcional-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Uso lambdas e Streams para transformações claras, com operações sem efeitos colaterais quando possível. Distingo avaliação preguiçosa, redução, coleta e ausência de valor; só adoto paralelismo quando os ganhos foram medidos e os recursos estão controlados.

[↑ Sumário do tópico](#java-funcional-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-solid-sumario"></a>

# 05. SOLID e Princípios de Design

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#design-solid-visao-geral)
- [S — Single Responsibility Principle (SRP)](#design-solid-s-single-responsibility-principle-srp)
- [O — Open/Closed Principle (OCP)](#design-solid-o-open-closed-principle-ocp)
- [L — Liskov Substitution Principle (LSP)](#design-solid-l-liskov-substitution-principle-lsp)
- [I — Interface Segregation Principle (ISP)](#design-solid-i-interface-segregation-principle-isp)
- [D — Dependency Inversion Principle (DIP)](#design-solid-d-dependency-inversion-principle-dip)
- [DIP vs Dependency Injection vs IoC](#design-solid-dip-vs-dependency-injection-vs-ioc)
- [SOLID na Prática](#design-solid-solid-na-pratica)
- [DRY, KISS e YAGNI](#design-solid-dry-kiss-e-yagni)
- [Code Smells e Refatoração](#design-solid-code-smells-e-refatoracao)
- [Resumo de Entrevista](#design-solid-resumo-de-entrevista)

---

<a id="design-solid-visao-geral"></a>

## Visão Geral

> SOLID reúne cinco princípios para organizar responsabilidades e dependências. Uso esses princípios para facilitar mudanças e testes, sem criar interfaces e camadas que não resolvam uma necessidade concreta.

[↑ Sumário do tópico](#design-solid-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-solid-s-single-responsibility-principle-srp"></a>

## S — Single Responsibility Principle (SRP)

> Uma classe deve concentrar responsabilidades que mudam pelo mesmo motivo, associadas ao mesmo grupo de interessados. Por exemplo, separo cálculo de cobrança da geração de relatórios. SRP não significa que toda classe deve ter apenas um método. [Referência](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)

[↑ Sumário do tópico](#design-solid-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-solid-o-open-closed-principle-ocp"></a>

## O — Open/Closed Principle (OCP)

> Um componente deve permitir extensões sem exigir mudanças recorrentes na lógica estável. Por exemplo, adiciono uma estratégia de desconto por uma nova implementação. Isso não proíbe corrigir código existente nem exige prever toda variação futura. [Referência](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)

[↑ Sumário do tópico](#design-solid-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-solid-l-liskov-substitution-principle-lsp"></a>

## L — Liskov Substitution Principle (LSP)

> Uma implementação deve poder substituir seu tipo base preservando o contrato esperado. Não deve exigir precondições mais fortes nem prometer resultados mais fracos; se uma conta não permite saque, não deve implementar um contrato que garante essa operação. [Referência](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)

[↑ Sumário do tópico](#design-solid-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-solid-i-interface-segregation-principle-isp"></a>

## I — Interface Segregation Principle (ISP)

> Um cliente deve depender de contratos focados no que utiliza. Separar consulta e escrita pode evitar implementações obrigadas a oferecer operações sem sentido; o objetivo é coesão do contrato, não multiplicar interfaces artificialmente. [Referência](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)

[↑ Sumário do tópico](#design-solid-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-solid-d-dependency-inversion-principle-dip"></a>

## D — Dependency Inversion Principle (DIP)

> Regras de alto nível e detalhes devem depender de abstrações estáveis, cujos contratos não dependem da infraestrutura. Um caso de uso pode depender de `PagamentoGateway`, enquanto o adaptador HTTP implementa esse contrato. [Referência](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)

[↑ Sumário do tópico](#design-solid-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-solid-dip-vs-dependency-injection-vs-ioc"></a>

## DIP vs Dependency Injection vs IoC

> DIP orienta a direção das dependências no código. Dependency Injection fornece dependências de fora do objeto. IoC é a inversão mais ampla do controle; usar injeção com uma classe concreta não garante, sozinho, o cumprimento de DIP.

[↑ Sumário do tópico](#design-solid-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-solid-solid-na-pratica"></a>

## SOLID na Prática

> Em um checkout, separo cálculo, persistência e pagamento por responsabilidade, uso estratégias para variações e contratos coerentes para integrações. Avalio o resultado pela facilidade de testar e alterar regras, não pela quantidade de classes criadas.

[↑ Sumário do tópico](#design-solid-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-solid-dry-kiss-e-yagni"></a>

## DRY, KISS e YAGNI

> DRY evita duplicar conhecimento de negócio; KISS favorece soluções compreensíveis; YAGNI evita construir funcionalidades apenas imaginadas. Código parecido nem sempre representa a mesma regra: abstrair cedo demais também pode aumentar acoplamento.

[↑ Sumário do tópico](#design-solid-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-solid-code-smells-e-refatoracao"></a>

## Code Smells e Refatoração

> Code smells são sinais de possível problema, como classes com muitas responsabilidades, métodos longos e regras duplicadas. Refatoração melhora a estrutura preservando comportamento observável; uso testes e mudanças pequenas para reduzir o risco.

[↑ Sumário do tópico](#design-solid-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-solid-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Aplico SOLID para tornar responsabilidades e contratos claros e reduzir o impacto das mudanças. Combino esses princípios com simplicidade e testes, criando abstrações onde existem variações reais ou fronteiras relevantes.

[↑ Sumário do tópico](#design-solid-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-sumario"></a>

# 06. Design Patterns — Padrões de Projeto

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#design-patterns-visao-geral)
- [Categorias GoF](#design-patterns-categorias-gof)
- [Factory Method — Criacional](#design-patterns-factory-method-criacional)
- [Abstract Factory — Criacional](#design-patterns-abstract-factory-criacional)
- [Builder — Criacional](#design-patterns-builder-criacional)
- [Prototype — Criacional](#design-patterns-prototype-criacional)
- [Singleton — Criacional](#design-patterns-singleton-criacional)
- [Adapter — Estrutural](#design-patterns-adapter-estrutural)
- [Bridge — Estrutural](#design-patterns-bridge-estrutural)
- [Composite — Estrutural](#design-patterns-composite-estrutural)
- [Decorator — Estrutural](#design-patterns-decorator-estrutural)
- [Facade — Estrutural](#design-patterns-facade-estrutural)
- [Flyweight — Estrutural](#design-patterns-flyweight-estrutural)
- [Proxy — Estrutural](#design-patterns-proxy-estrutural)
- [Chain of Responsibility — Comportamental](#design-patterns-chain-of-responsibility-comportamental)
- [Command — Comportamental](#design-patterns-command-comportamental)
- [Interpreter — Comportamental](#design-patterns-interpreter-comportamental)
- [Iterator — Comportamental](#design-patterns-iterator-comportamental)
- [Mediator — Comportamental](#design-patterns-mediator-comportamental)
- [Memento — Comportamental](#design-patterns-memento-comportamental)
- [Observer — Comportamental](#design-patterns-observer-comportamental)
- [State — Comportamental](#design-patterns-state-comportamental)
- [Strategy — Comportamental](#design-patterns-strategy-comportamental)
- [Template Method — Comportamental](#design-patterns-template-method-comportamental)
- [Visitor — Comportamental](#design-patterns-visitor-comportamental)
- [Strategy vs State vs Template Method](#design-patterns-strategy-vs-state-vs-template-method)
- [Adapter vs Decorator vs Proxy vs Facade](#design-patterns-adapter-vs-decorator-vs-proxy-vs-facade)
- [Repository, DTO e Mapper](#design-patterns-repository-dto-e-mapper)
- [Padrão de Projeto vs Padrão Arquitetural](#design-patterns-padrao-de-projeto-vs-padrao-arquitetural)
- [Quando Não Aplicar um Padrão](#design-patterns-quando-nao-aplicar-um-padrao)
- [Resumo de Entrevista](#design-patterns-resumo-de-entrevista)

---

<a id="design-patterns-visao-geral"></a>

## Visão Geral

> Design Patterns descrevem soluções recorrentes para problemas de design, com contexto e consequências. Não são código pronto nem obrigações: primeiro identifico o problema e depois avalio se o padrão simplifica a solução.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-categorias-gof"></a>

## Categorias GoF

> Os 23 padrões GoF dividem-se em criacionais, estruturais e comportamentais. Os primeiros organizam criação de objetos; os estruturais, sua composição; os comportamentais, a distribuição de responsabilidades e a colaboração entre objetos.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-factory-method-criacional"></a>

## Factory Method — Criacional

> Define um método de criação que subclasses podem especializar para escolher o produto concreto. Separo a lógica que usa o objeto da escolha de sua implementação; uma função estática que instancia objetos não é, por isso só, o padrão GoF.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-abstract-factory-criacional"></a>

## Abstract Factory — Criacional

> Cria famílias de objetos relacionados por meio de contratos comuns. É útil quando componentes precisam variar juntos e permanecer compatíveis, mas adicionar um novo tipo de produto exige alterar as fábricas da família.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-builder-criacional"></a>

## Builder — Criacional

> Separa a construção de um objeto complexo de sua representação. Ajuda com muitos parâmetros e etapas, mantendo a validação no resultado final; para objetos simples, um construtor claro pode bastar.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-prototype-criacional"></a>

## Prototype — Criacional

> Cria objetos a partir de cópias de um modelo existente. É útil quando a configuração inicial é cara ou complexa; preciso distinguir cópia rasa e profunda para não compartilhar estado mutável por acidente.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-singleton-criacional"></a>

## Singleton — Criacional

> Controla a criação para disponibilizar uma única instância em determinado escopo de execução. Pode introduzir estado global e dificultar testes; singleton do Spring é por bean e container, não uma garantia de instância única no sistema distribuído.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-adapter-estrutural"></a>

## Adapter — Estrutural

> Traduz uma interface existente para o contrato esperado pelo cliente. Uso, por exemplo, para integrar um provedor externo a uma porta da aplicação, mantendo detalhes de tradução fora das regras de negócio.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-bridge-estrutural"></a>

## Bridge — Estrutural

> Separa uma abstração de sua implementação para que ambas possam variar independentemente. É útil quando duas dimensões gerariam muitas subclasses combinadas; o custo é introduzir uma camada adicional de delegação.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-composite-estrutural"></a>

## Composite — Estrutural

> Representa estruturas em árvore e permite tratar folhas e conjuntos pelo mesmo contrato. Serve para hierarquias como pastas e arquivos, desde que as operações comuns façam sentido para os dois tipos.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-decorator-estrutural"></a>

## Decorator — Estrutural

> Acrescenta comportamento envolvendo um objeto com outro do mesmo contrato. Permite combinar recursos como compressão e criptografia sem muitas subclasses, mas cadeias extensas podem dificultar depuração e entendimento da ordem.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-facade-estrutural"></a>

## Facade — Estrutural

> Oferece uma interface simples para um subsistema mais complexo. Reduz o conhecimento exigido dos clientes, mas deve permanecer focada para não concentrar regras e virar uma classe responsável por tudo.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-flyweight-estrutural"></a>

## Flyweight — Estrutural

> Compartilha estado intrínseco entre muitos objetos para economizar memória, mantendo o estado variável fora do objeto compartilhado. Só compensa quando a quantidade de instâncias e a economia justificam a complexidade.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-proxy-estrutural"></a>

## Proxy — Estrutural

> Representa outro objeto e controla seu acesso, podendo aplicar segurança, carregamento tardio ou interceptação. Spring AOP usa proxies em diversos recursos; chamadas que não atravessam o proxy podem escapar desse comportamento.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-chain-of-responsibility-comportamental"></a>

## Chain of Responsibility — Comportamental

> Encadeia handlers que processam ou encaminham uma solicitação. É útil para filtros e validações; a ordem e a regra de interrupção devem ficar explícitas para evitar caminhos inesperados.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-command-comportamental"></a>

## Command — Comportamental

> Encapsula uma solicitação como objeto, separando quem pede de quem executa. Facilita filas, histórico e agendamento; suporte a desfazer exige lógica própria e não aparece automaticamente ao adotar o padrão.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-interpreter-comportamental"></a>

## Interpreter — Comportamental

> Representa regras de uma linguagem e interpreta expressões dessa gramática. Pode servir para linguagens simples de regras, mas gramáticas grandes normalmente exigem ferramentas de parsing mais apropriadas.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-iterator-comportamental"></a>

## Iterator — Comportamental

> Permite percorrer uma estrutura sem expor sua representação interna. Em Java, `Iterator` oferece esse contrato; suporte a remoção e comportamento diante de modificações dependem da implementação.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-mediator-comportamental"></a>

## Mediator — Comportamental

> Centraliza a coordenação entre objetos que interagiriam diretamente de forma complexa. Reduz dependências entre participantes, mas o mediador precisa de limites claros para não acumular toda a lógica.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-memento-comportamental"></a>

## Memento — Comportamental

> Captura um estado para permitir restauração sem expor detalhes internos ao cliente. É útil em histórico ou desfazer, mas snapshots grandes têm custo de memória e não revertem automaticamente efeitos externos.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-observer-comportamental"></a>

## Observer — Comportamental

> Permite notificar interessados quando um objeto muda ou emite um evento. Reduz dependência direta dos observadores, mas exige tratar falhas e ciclo de vida; não garante assincronia, persistência nem entrega distribuída.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-state-comportamental"></a>

## State — Comportamental

> Encapsula comportamentos que variam conforme o estado do objeto. Ajuda em fluxos como pedido pendente, pago e cancelado, tornando transições explícitas e reduzindo condicionais espalhados.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-strategy-comportamental"></a>

## Strategy — Comportamental

> Encapsula algoritmos intercambiáveis sob um mesmo contrato, como cálculo de frete ou desconto. Favorece composição e OCP; o cliente ou uma política escolhe qual estratégia usar.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-template-method-comportamental"></a>

## Template Method — Comportamental

> Define o esqueleto de um algoritmo em uma classe base e permite especializar etapas. Reutiliza o fluxo por herança, mas cria acoplamento com a superclasse; Strategy oferece variação por composição.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-visitor-comportamental"></a>

## Visitor — Comportamental

> Separa operações de uma estrutura estável de tipos, como uma árvore sintática. Facilita adicionar operações, mas novos tipos de elemento normalmente exigem atualizar os visitantes; uso quando essa troca favorece o domínio.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-strategy-vs-state-vs-template-method"></a>

## Strategy vs State vs Template Method

> Strategy varia o algoritmo escolhido; State varia o comportamento conforme o estado e suas transições; Template Method preserva um fluxo e permite especializar etapas por herança. Escolho pela natureza da variação, não pela semelhança das classes.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-adapter-vs-decorator-vs-proxy-vs-facade"></a>

## Adapter vs Decorator vs Proxy vs Facade

> Adapter traduz contratos; Decorator acrescenta comportamento; Proxy controla acesso; Facade simplifica um subsistema. Todos podem usar delegação, mas sua intenção é diferente.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-repository-dto-e-mapper"></a>

## Repository, DTO e Mapper

> Repository oferece acesso orientado ao domínio à persistência; DTO transporta dados entre fronteiras; Mapper converte representações. São padrões frequentes em aplicações Java, mas não pertencem ao catálogo GoF e não devem duplicar camadas sem benefício.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-padrao-de-projeto-vs-padrao-arquitetural"></a>

## Padrão de Projeto vs Padrão Arquitetural

> Padrões de projeto tratam colaborações entre componentes e objetos. Padrões arquiteturais tratam organização e propriedades mais amplas; Saga e Outbox resolvem coordenação e consistência distribuída e são estudados nos capítulos de arquitetura e microsserviços.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-quando-nao-aplicar-um-padrao"></a>

## Quando Não Aplicar um Padrão

> Evito um padrão quando ele cria mais indireção do que resolve ou antecipa variações sem evidência. A solução deve continuar fácil de explicar, testar e modificar; conhecer o nome não substitui avaliar o custo.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="design-patterns-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Escolho padrões pelo problema: Strategy para algoritmos variáveis, Builder para construção complexa, Adapter para integração e Proxy para interceptação. Explico sempre o ganho, o custo e por que uma solução mais simples seria ou não suficiente.

[↑ Sumário do tópico](#design-patterns-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-sumario"></a>

# 07. JVM Memory & Garbage Collection

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#java-jvm-memory-garbage-collection-visao-geral)
- [Heap](#java-jvm-memory-garbage-collection-1-heap)
- [Stack e Variáveis Locais](#java-jvm-memory-garbage-collection-2-stack)
- [Metaspace e Memória Nativa](#java-jvm-memory-garbage-collection-metaspace-e-memoria-nativa)
- [Memória Fora da Heap](#java-jvm-memory-garbage-collection-memoria-fora-da-heap)
- [Class Loading e JIT](#java-jvm-memory-garbage-collection-class-loading-e-jit)
- [Alcançabilidade e GC Roots](#java-jvm-memory-garbage-collection-2-regra-de-alcancabilidade)
- [Garbage Collector](#java-jvm-memory-garbage-collection-garbage-collector)
- [Fluxo Geracional](#java-jvm-memory-garbage-collection-1-fluxo-geracional)
- [Stop-the-World, Throughput e Latência](#java-jvm-memory-garbage-collection-stop-the-world-throughput-e-latencia)
- [G1: Young, Mixed e Full GC](#java-jvm-memory-garbage-collection-3-full-gc)
- [G1 vs ZGC](#java-jvm-memory-garbage-collection-2-zgc)
- [Memory Leak em Java](#java-jvm-memory-garbage-collection-memory-leak-em-java)
- [OutOfMemoryError vs StackOverflowError](#java-jvm-memory-garbage-collection-outofmemoryerror-vs-stackoverflowerror)
- [Heap Dump](#java-jvm-memory-garbage-collection-1-heap-dump)
- [Thread Dump](#java-jvm-memory-garbage-collection-2-thread-dump)
- [JFR, JMC e GC Logs](#java-jvm-memory-garbage-collection-jfr-jmc-e-gc-logs)
- [Tuning de Heap e Escolha do Coletor](#java-jvm-memory-garbage-collection-zgc)
- [Resumo de Entrevista](#java-jvm-memory-garbage-collection-resposta-completa-de-entrevista)

---

<a id="java-jvm-memory-garbage-collection-visao-geral"></a>

## Visão Geral

> A JVM possui diferentes áreas de memória. A Heap armazena objetos e é gerenciada pelo Garbage Collector, enquanto cada thread possui sua própria Stack para execução dos métodos. Em produção, começo investigando métricas e GC logs e, quando necessário, uso Heap Dump e Thread Dump para chegar à causa.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-1-heap"></a>

## Heap

> A Heap é a área compartilhada entre as threads onde ficam objetos e arrays. Ela é gerenciada pelo Garbage Collector. Se não houver espaço suficiente para novas alocações e o GC não conseguir recuperar memória suficiente, pode ocorrer `OutOfMemoryError: Java heap space`.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-2-stack"></a>

## Stack e Variáveis Locais

> Cada thread possui uma pilha lógica de execução com frames de métodos, variáveis locais e operandos. Referências locais podem apontar para objetos na heap; não são metadados de classe no Metaspace. Uma profundidade excessiva de chamadas pode provocar `StackOverflowError`.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-metaspace-e-memoria-nativa"></a>

## Metaspace e Memória Nativa

> No HotSpot, Metaspace guarda metadados de classes em memória nativa. Variáveis locais pertencem aos frames, não ao Metaspace; muitos class loaders retidos podem provocar crescimento dessa área e falhas de memória.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-memoria-fora-da-heap"></a>

## Memória Fora da Heap

> Eu não atribuiria automaticamente um aumento de memória do processo à Heap. Se a Heap permanece estável, investigaria Metaspace, Direct Buffers, stacks das threads, quantidade de threads e outras áreas nativas da JVM.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-class-loading-e-jit"></a>

## Class Loading e JIT

> Class loaders carregam classes; a JVM verifica, prepara e inicializa tipos conforme necessário. O JIT compila trechos frequentes para código nativo, então warm-up e perfil de execução influenciam medições de performance.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-2-regra-de-alcancabilidade"></a>

## Alcançabilidade e GC Roots

> O Garbage Collector determina a vida de um objeto através de reachability. Se existe um caminho de referências fortes partindo de algum GC Root até o objeto, ele continua vivo. Quando esse caminho deixa de existir, o objeto fica elegível para coleta.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-garbage-collector"></a>

## Garbage Collector

> O Garbage Collector gerencia a memória da Heap. Ele identifica objetos que deixaram de ser alcançáveis a partir dos GC Roots e recupera essa memória. Coletores como G1 e ZGC utilizam estratégias diferentes para equilibrar throughput, latência e consumo de recursos.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-1-fluxo-geracional"></a>

## Fluxo Geracional

> Em coletores como G1, Eden e Survivor pertencem à Young Generation. Objetos normalmente nascem no Eden; sobreviventes podem passar por Survivor e ser promovidos à Old. Esse é um modelo de estudo, não uma sequência obrigatória para todos os objetos e coletores.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-stop-the-world-throughput-e-latencia"></a>

## Stop-the-World, Throughput e Latência

> Em uma pausa stop-the-world, threads da aplicação param para uma etapa coordenada da JVM. Throughput mede trabalho realizado por tempo; latência mede duração de operações. A escolha do coletor depende do equilíbrio desses objetivos e dos recursos disponíveis.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-3-full-gc"></a>

## G1: Young, Mixed e Full GC

> O G1 divide a Heap em regiões e tenta controlar as pausas escolhendo quais regiões coletar. Ele possui Young Collections e Mixed Collections, que também podem recuperar regiões selecionadas da Old. Full GC é diferente: envolve a Heap inteira e tende a produzir uma pausa muito mais relevante.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-2-zgc"></a>

## G1 vs ZGC

> O G1 procura equilibrar throughput e metas de pausa. Já o ZGC prioriza baixa latência e realiza uma parcela maior do trabalho concorrentemente. O trade-off deve ser medido na carga real, observando CPU, memória, throughput e os SLOs de latência.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-memory-leak-em-java"></a>

## Memory Leak em Java

> Um leak pode ocorrer quando referências mantêm objetos vivos além do necessário, mesmo com GC. Exemplos incluem caches sem limite, listeners esquecidos e uso incorreto de ThreadLocal; investigaria retenção e caminhos até GC Roots.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-outofmemoryerror-vs-stackoverflowerror"></a>

## OutOfMemoryError vs StackOverflowError

> `OutOfMemoryError` pode indicar esgotamento da heap ou de outros recursos, como Metaspace e criação de threads. `StackOverflowError` geralmente indica profundidade excessiva de chamadas; a mensagem e as métricas determinam a investigação correta.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-1-heap-dump"></a>

## Heap Dump

> Heap Dump mostra os objetos presentes na Heap e suas relações de referência. Eu o utilizaria para descobrir quais objetos estão retendo memória e qual caminho de referências até um GC Root impede que sejam coletados.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-2-thread-dump"></a>

## Thread Dump

> Thread Dump mostra o estado das threads e suas stacks naquele instante. É útil para investigar bloqueios, deadlocks, contenção ou entender onde as threads estão gastando tempo.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-jfr-jmc-e-gc-logs"></a>

## JFR, JMC e GC Logs

> Java Flight Recorder coleta eventos de execução com configuração adequada ao diagnóstico, e Java Mission Control ajuda a analisá-los. GC logs mostram frequência, duração e ocupação das coletas; combino essas evidências antes de aplicar tuning.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-zgc"></a>

## Tuning de Heap e Escolha do Coletor

> Aumentar a Heap pode reduzir pressão de GC quando a aplicação realmente precisa de mais espaço, mas não resolve retenção indevida. Da mesma forma, mudar para ZGC não deve ser uma decisão automática: eu escolheria o coletor com base em metas de latência, throughput e medições da carga real.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-resposta-completa-de-entrevista"></a>

## Resumo de Entrevista

> A JVM mantém objetos na heap, pilhas de execução por thread e metadados em áreas próprias. O GC recupera objetos sem caminhos fortes a partir de GC Roots. Para diagnosticar problemas, observo GC, memória e latência; uso dumps e JFR antes de alterar heap ou coletor.

[↑ Sumário do tópico](#java-jvm-memory-garbage-collection-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-sumario"></a>

# 08. Concorrência em Java

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#java-concorrencia-em-java-visao-geral)
- [Concorrência vs Paralelismo](#java-concorrencia-em-java-concorrencia-vs-paralelismo)
- [Thread: Platform vs Virtual](#java-concorrencia-em-java-1-thread)
- [Estados da Thread](#java-concorrencia-em-java-estados-da-thread)
- [Runnable](#java-concorrencia-em-java-2-runnable)
- [Callable](#java-concorrencia-em-java-3-callable)
- [ExecutorService e Thread Pools](#java-concorrencia-em-java-executorservice-e-thread-pools)
- [Future](#java-concorrencia-em-java-4-future)
- [CompletableFuture](#java-concorrencia-em-java-trade-off)
- [Interrupção e Cancelamento](#java-concorrencia-em-java-interrupcao-e-cancelamento)
- [Atomicidade, Visibilidade e Ordenação](#java-concorrencia-em-java-atomicidade-visibilidade-e-ordenacao)
- [Java Memory Model](#java-concorrencia-em-java-java-memory-model)
- [Happens-before](#java-concorrencia-em-java-2-happens-before)
- [Race Condition](#java-concorrencia-em-java-race-condition)
- [Deadlock](#java-concorrencia-em-java-deadlock)
- [Livelock](#java-concorrencia-em-java-livelock)
- [Starvation](#java-concorrencia-em-java-starvation)
- [Contention](#java-concorrencia-em-java-contention)
- [synchronized](#java-concorrencia-em-java-trade-off-2)
- [volatile](#java-concorrencia-em-java-bom-para)
- [ReentrantLock](#java-concorrencia-em-java-trade-off-3)
- [Semaphore e CountDownLatch](#java-concorrencia-em-java-semaphore-e-countdownlatch)
- [AtomicInteger](#java-concorrencia-em-java-trade-off-4)
- [CAS e LongAdder](#java-concorrencia-em-java-cas-e-longadder)
- [ConcurrentHashMap](#java-concorrencia-em-java-5-concurrenthashmap)
- [ThreadLocal e Limpeza](#java-concorrencia-em-java-threadlocal-e-limpeza)
- [Virtual Threads: Ganhos e Limites](#java-concorrencia-em-java-5-trade-offs)
- [Virtual Threads: Pinning no Java 21 e 24+](#java-concorrencia-em-java-java-24)
- [Virtual Threads e Controle de Concorrência](#java-concorrencia-em-java-virtual-threads-e-controle-de-concorrencia)
- [Diagnóstico em Produção](#java-concorrencia-em-java-diagnostico-em-producao)
- [Resumo de Entrevista](#java-concorrencia-em-java-resposta-completa-de-entrevista)

---

<a id="java-concorrencia-em-java-visao-geral"></a>

## Visão Geral

> Concorrência em Java é a execução coordenada de múltiplas tarefas durante o mesmo período. O Java fornece abstrações como Threads, ExecutorService, locks, atomics e coleções concorrentes. O principal desafio é coordenar acesso a estado compartilhado garantindo atomicidade, visibilidade e ordenação sem gerar contenção excessiva.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-concorrencia-vs-paralelismo"></a>

## Concorrência vs Paralelismo

> Concorrência permite avançar em várias tarefas durante o mesmo período; paralelismo executa trabalho simultaneamente em recursos distintos. Uma aplicação pode ser concorrente mesmo com apenas um núcleo disponível.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-1-thread"></a>

## Thread: Platform vs Virtual

> Uma Thread representa um fluxo de execução. Platform Threads são relativamente caras porque dependem de threads do sistema operacional, enquanto Virtual Threads são muito mais leves e gerenciadas pela JVM.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-estados-da-thread"></a>

## Estados da Thread

> Os estados Java incluem NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING e TERMINATED. RUNNABLE não significa uso contínuo de CPU; BLOCKED está ligado à aquisição de monitor, enquanto outras esperas podem aparecer em estados diferentes.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-2-runnable"></a>

## Runnable

> Runnable representa uma tarefa sem retorno. É adequada quando eu quero executar uma ação concorrentemente, mas não preciso receber um resultado da operação.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-3-callable"></a>

## Callable

> Callable é semelhante ao Runnable, mas permite retornar um resultado e lançar exceptions. Quando submetido a um ExecutorService, normalmente recebo um Future representando o resultado futuro.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-executorservice-e-thread-pools"></a>

## ExecutorService e Thread Pools

> ExecutorService separa submissão de tarefas de sua execução e permite gerenciar resultados e encerramento. Em pools de platform threads, dimensiono capacidade, fila e política de rejeição; filas sem limite podem transformar sobrecarga em consumo crescente de memória.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-4-future"></a>

## Future

> Future representa o resultado de uma operação assíncrona. O principal cuidado é que `get()` pode bloquear até a operação terminar.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-trade-off"></a>

## CompletableFuture

> CompletableFuture permite compor operações assíncronas sem bloquear explicitamente a cada etapa. É útil para combinar tarefas independentes, mas fluxos excessivamente complexos podem prejudicar legibilidade e observabilidade.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-interrupcao-e-cancelamento"></a>

## Interrupção e Cancelamento

> Interrupção é um pedido cooperativo, não uma parada forçada. Trato `InterruptedException` propagando-a ou restaurando o sinal quando apropriado; cancelar um Future não garante que todo trabalho ou efeito externo tenha sido interrompido.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-atomicidade-visibilidade-e-ordenacao"></a>

## Atomicidade, Visibilidade e Ordenação

> Atomicidade impede observar uma operação parcialmente concluída; visibilidade garante que atualizações sejam percebidas; ordenação define restrições sobre a observação das operações. São propriedades diferentes: enxergar um contador não torna seu incremento atômico.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-java-memory-model"></a>

## Java Memory Model

> O JMM define quais efeitos de memória podem ser observados entre threads e quais sincronizações estabelecem garantias. Sem uma relação adequada de happens-before, acesso compartilhado pode apresentar resultados que não seguem a intuição da ordem escrita no código.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-2-happens-before"></a>

## Happens-before

> Happens-before define quando os efeitos de uma operação são garantidamente visíveis para outra. Por exemplo, liberar um monitor acontece antes de uma aquisição posterior do mesmo monitor, e uma escrita volatile acontece antes de uma leitura posterior da mesma variável.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-race-condition"></a>

## Race Condition

> O resultado depende de uma intercalação incorreta entre tarefas, como duas threads lerem o mesmo saldo e gravarem atualizações conflitantes. Protejo a operação composta e suas invariantes com sincronização ou desenho que evite estado compartilhado.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-deadlock"></a>

## Deadlock

> Deadlock ocorre quando tarefas esperam circularmente por recursos e nenhuma progride. Uso ordem consistente de aquisição, regiões críticas pequenas e, quando adequado, tentativas com timeout; thread dumps ajudam a localizar o ciclo.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-livelock"></a>

## Livelock

> No livelock, tarefas continuam agindo, mas suas reações impedem progresso útil. Reduzo tentativas simétricas com backoff, jitter ou uma regra de coordenação que permita a uma delas avançar.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-starvation"></a>

## Starvation

> Starvation ocorre quando uma tarefa espera indefinidamente porque outras monopolizam os recursos. Investigo justiça, prioridades, tamanho das regiões críticas e filas; políticas justas podem reduzir esse risco, com possível perda de throughput.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-contention"></a>

## Contention

> Contention é a disputa pelo mesmo recurso, como lock, conexão ou fila. Reduzo a seção crítica, particiono estado ou limito concorrência, medindo o gargalo antes de adicionar threads.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-trade-off-2"></a>

## synchronized

> synchronized fornece exclusão mútua e garantia de visibilidade através do monitor. É simples para proteger regiões críticas, mas alta contenção sobre o mesmo monitor pode reduzir paralelismo e throughput.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-bom-para"></a>

## volatile

> volatile garante visibilidade e ordenação de memória para uma variável, mas não transforma operações compostas em atômicas. Funciona bem para flags e estados simples, mas não substitui locks ou atomics em read-modify-write.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-trade-off-3"></a>

## ReentrantLock

> ReentrantLock fornece exclusão mútua e opções como `tryLock`, espera com timeout, interrupção e Conditions. Após adquirir o lock, libero-o em `finally`; seu controle adicional tem custo de complexidade e não torna uma região concorrida automaticamente mais rápida.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-semaphore-e-countdownlatch"></a>

## Semaphore e CountDownLatch

> Semaphore limita acessos simultâneos por permissões, sendo útil para proteger uma dependência. CountDownLatch permite esperar até um conjunto de tarefas sinalizar conclusão; seu contador não é reutilizável após chegar a zero.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-trade-off-4"></a>

## AtomicInteger

> AtomicInteger é adequado para operações atômicas simples sobre um inteiro compartilhado. Evita locking explícito em muitos casos, mas não substitui locks quando preciso manter invariantes envolvendo múltiplos estados.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-cas-e-longadder"></a>

## CAS e LongAdder

> Compare-and-set atualiza um valor apenas se o estado esperado ainda for válido, podendo repetir sob disputa. LongAdder distribui atualizações para reduzir contenção em contadores, mas sua soma durante atualizações não é uma leitura atômica global adequada para invariantes.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-5-concurrenthashmap"></a>

## ConcurrentHashMap

> ConcurrentHashMap é um Map thread-safe otimizado para concorrência e fornece operações compostas atômicas como putIfAbsent, compute e merge.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-threadlocal-e-limpeza"></a>

## ThreadLocal e Limpeza

> ThreadLocal associa um valor a cada thread, sem torná-lo automaticamente compartilhável ou seguro. Em pools, faço limpeza com `remove` ao fim do uso para evitar retenção e vazamento de contexto; muitas Virtual Threads também podem multiplicar o custo por thread.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-5-trade-offs"></a>

## Virtual Threads: Ganhos e Limites

> Virtual Threads aumentam principalmente a escalabilidade de workloads I/O-bound. Elas não tornam CPU-bound mais rápido e não removem limites externos. Se meu banco possui 30 conexões, criar milhares de Virtual Threads não aumenta esse limite.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-java-24"></a>

## Virtual Threads: Pinning no Java 21 e 24+

> No Java 21, uma Virtual Thread podia ficar presa à carrier ao bloquear dentro de synchronized. A partir do JDK 24 esse caso foi resolvido. No Java 25, synchronized normalmente não causa mais esse pinning, embora ele ainda possa ocorrer em chamadas native ou foreign. [Java 21](https://docs.oracle.com/en/java/javase/21/core/virtual-threads.html) · [Java 25](https://docs.oracle.com/en/java/javase/25/core/virtual-threads.html)

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-virtual-threads-e-controle-de-concorrencia"></a>

## Virtual Threads e Controle de Concorrência

> Virtual Threads são apropriadas para muitas tarefas bloqueantes de I/O e normalmente são criadas por tarefa, sem pool para reutilização. Limito acesso a recursos escassos com semáforos ou pools das dependências, em vez de assumir concorrência ilimitada.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-diagnostico-em-producao"></a>

## Diagnóstico em Produção

> Em produção eu começo observando o sintoma e as métricas. Para suspeita de bloqueio ou contenção, utilizo Thread Dumps ou JFR para identificar estados das threads, locks e stacks. Também verifico pools e dependências externas antes de atribuir o problema simplesmente ao número de threads.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="java-concorrencia-em-java-resposta-completa-de-entrevista"></a>

## Resumo de Entrevista

> Coordeno tarefas com executores e protejo estado compartilhado conforme as garantias de atomicidade, visibilidade e ordenação. Escolho locks, atomics e coleções concorrentes pelas invariantes envolvidas. Virtual Threads ajudam cargas bloqueantes de I/O, mas não removem limites de CPU ou das dependências.

[↑ Sumário do tópico](#java-concorrencia-em-java-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="build-dependencias-sumario"></a>

# 09. Build e Dependências — Maven e Gradle

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#build-dependencias-visao-geral)
- [Maven e pom.xml](#build-dependencias-maven-e-pom-xml)
- [Ciclo de Vida do Maven](#build-dependencias-ciclo-de-vida-do-maven)
- [Scopes compile, runtime, provided e test](#build-dependencias-scopes-compile-runtime-provided-e-test)
- [Dependências Transitivas e Conflitos](#build-dependencias-dependencias-transitivas-e-conflitos)
- [dependencies vs dependencyManagement](#build-dependencias-dependencies-vs-dependencymanagement)
- [BOM e Parent POM](#build-dependencias-bom-e-parent-pom)
- [Plugins e Surefire vs Failsafe](#build-dependencias-plugins-e-surefire-vs-failsafe)
- [Gradle e Tasks](#build-dependencias-gradle-e-tasks)
- [Wrappers, Versões e Toolchains](#build-dependencias-wrappers-versoes-e-toolchains)
- [JAR, Executable JAR e WAR](#build-dependencias-jar-executable-jar-e-war)
- [Resumo de Entrevista](#build-dependencias-resumo-de-entrevista)

---

<a id="build-dependencias-visao-geral"></a>

## Visão Geral

> Ferramentas de build automatizam compilação, testes, empacotamento e resolução de dependências. Em Java, entender o processo ajuda a diagnosticar diferenças entre ambiente local, pipeline e produção.

[↑ Sumário do tópico](#build-dependencias-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="build-dependencias-maven-e-pom-xml"></a>

## Maven e pom.xml

> O `pom.xml` descreve o projeto, dependências, plugins e configurações de build. Maven usa convenções e fases de ciclo de vida; plugins executam tarefas ligadas a essas fases.

[↑ Sumário do tópico](#build-dependencias-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="build-dependencias-ciclo-de-vida-do-maven"></a>

## Ciclo de Vida do Maven

> `compile` compila, `test` executa testes configurados nessa fase, `package` empacota e `verify` executa verificações posteriores. `install` publica no repositório local; `deploy`, no remoto. Executar uma fase inclui as anteriores do mesmo ciclo de vida.

[↑ Sumário do tópico](#build-dependencias-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="build-dependencias-scopes-compile-runtime-provided-e-test"></a>

## Scopes compile, runtime, provided e test

> `compile` disponibiliza a dependência na compilação e execução; `runtime`, na execução e testes, sem colocá-la na compilação principal. `provided` é esperada no ambiente de execução; `test` fica restrita à compilação e execução dos testes. [Referência](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html)

[↑ Sumário do tópico](#build-dependencias-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="build-dependencias-dependencias-transitivas-e-conflitos"></a>

## Dependências Transitivas e Conflitos

> Uma biblioteca pode trazer outras dependências transitivamente. Maven normalmente resolve conflitos pela definição mais próxima e, em empate, pela primeira declaração; uso `dependency:tree`, exclusões e versões gerenciadas para tornar o resultado explícito.

[↑ Sumário do tópico](#build-dependencias-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="build-dependencias-dependencies-vs-dependencymanagement"></a>

## dependencies vs dependencyManagement

> `dependencies` declara o que o módulo usa. `dependencyManagement` centraliza versões e configurações para quando essas dependências forem usadas, sem adicioná-las automaticamente ao classpath.

[↑ Sumário do tópico](#build-dependencias-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="build-dependencias-bom-e-parent-pom"></a>

## BOM e Parent POM

> BOM reúne versões compatíveis de dependências e pode ser importado em `dependencyManagement`. Parent POM também permite herdar configuração de build e plugins; importar um BOM não equivale a herdar todo um parent.

[↑ Sumário do tópico](#build-dependencias-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="build-dependencias-plugins-e-surefire-vs-failsafe"></a>

## Plugins e Surefire vs Failsafe

> Plugins executam tarefas do build. Surefire normalmente cuida de testes unitários; Failsafe organiza testes de integração e sua verificação, quando configurado. O nome da fase não garante sozinho que todos os testes existentes serão executados.

[↑ Sumário do tópico](#build-dependencias-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="build-dependencias-gradle-e-tasks"></a>

## Gradle e Tasks

> Gradle modela o build como um grafo de tasks e oferece scripts Kotlin ou Groovy. Os conceitos de dependências, versões, isolamento e reprodutibilidade continuam importantes, mesmo com uma configuração mais programável.

[↑ Sumário do tópico](#build-dependencias-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="build-dependencias-wrappers-versoes-e-toolchains"></a>

## Wrappers, Versões e Toolchains

> Wrappers fixam a versão da ferramenta; toolchains selecionam o JDK usado no build. Também alinho a versão alvo, por exemplo com `--release`, porque a versão do Java instalado não garante compatibilidade do artefato produzido.

[↑ Sumário do tópico](#build-dependencias-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="build-dependencias-jar-executable-jar-e-war"></a>

## JAR, Executable JAR e WAR

> JAR empacota classes e recursos; um JAR executável pode incluir inicialização e dependências conforme a ferramenta. WAR é voltado ao modelo de aplicação web em container Servlet. A forma de empacotar deve corresponder ao ambiente de deploy.

[↑ Sumário do tópico](#build-dependencias-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="build-dependencias-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Trato build e dependências como parte do comportamento da aplicação. Controlo versões, scopes, plugins e JDK, verifico conflitos transitivos e produzo um artefato reproduzível antes de promovê-lo entre ambientes.

[↑ Sumário do tópico](#build-dependencias-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-sumario"></a>

# 10. SQL & Bancos Relacionais

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral — SQL](#sql-visao-geral-sql)
- [SELECT, INSERT, UPDATE e DELETE](#sql-sql-bancos-relacionais-select-insert-update-e-delete)
- [Primary Key, Unique e Foreign Key](#sql-primary-key-unique-e-foreign-key)
- [NOT NULL, CHECK e Integridade](#sql-sql-bancos-relacionais-not-null-check-e-integridade)
- [Normalização vs Desnormalização](#sql-normalizacao-vs-desnormalizacao)
- [NULL](#sql-null)
- [Ordem Lógica de uma Consulta](#sql-sql-bancos-relacionais-ordem-logica-de-uma-consulta)
- [INNER JOIN, LEFT JOIN, RIGHT JOIN e FULL JOIN](#sql-inner-join-left-join-right-join-e-full-join)
- [WHERE vs HAVING](#sql-where-vs-having)
- [GROUP BY](#sql-group-by)
- [CTE vs Subquery](#sql-cte-vs-subquery)
- [Window Functions](#sql-window-functions)
- [EXISTS vs IN vs JOIN](#sql-exists-vs-in-vs-join)
- [NOT IN e NULL](#sql-sql-bancos-relacionais-not-in-e-null)
- [UNION vs UNION ALL](#sql-union-vs-union-all)
- [DELETE vs TRUNCATE vs DROP](#sql-delete-vs-truncate-vs-drop)
- [Prepared Statements e SQL Injection](#sql-prepared-statements-e-sql-injection)
- [Índices](#sql-indices)
- [Índice Composto e Ordem das Colunas](#sql-indice-composto-e-ordem-das-colunas)
- [Índice de Cobertura, Parcial e Funcional](#sql-sql-bancos-relacionais-indice-de-cobertura-parcial-e-funcional)
- [Seletividade e Cardinalidade](#sql-seletividade-e-cardinalidade)
- [Sargabilidade](#sql-sargabilidade)
- [Quando um índice pode não ser usado](#sql-quando-um-indice-pode-nao-ser-usado)
- [Execution Plan](#sql-execution-plan)
- [Otimização de Query em Produção](#sql-otimizacao-de-query-em-producao)
- [Paginação OFFSET vs Keyset](#sql-paginacao-offset-vs-keyset)
- [N+1 Queries](#sql-n-1-queries)
- [ACID](#sql-acid)
- [Isolation Levels](#sql-isolation-levels)
- [Dirty Read, Non-repeatable Read e Phantom Read](#sql-sql-bancos-relacionais-dirty-read-non-repeatable-read-e-phantom-read)
- [Lost Update e Write Skew](#sql-sql-bancos-relacionais-lost-update-e-write-skew)
- [MVCC](#sql-mvcc)
- [PostgreSQL vs Oracle: Isolamento](#sql-sql-bancos-relacionais-postgresql-vs-oracle-isolamento)
- [Locks](#sql-locks)
- [Optimistic vs Pessimistic Concurrency](#sql-optimistic-vs-pessimistic-concurrency)
- [Atualização Condicional Atômica](#sql-sql-bancos-relacionais-atualizacao-condicional-atomica)
- [Deadlock no Banco](#sql-deadlock-no-banco)
- [Transação Longa](#sql-transacao-longa)
- [Connection Pool e Saturação](#sql-sql-bancos-relacionais-connection-pool-e-saturacao)
- [Resumo de Entrevista](#sql-resposta-completa-de-entrevista-sql)

---

<a id="sql-visao-geral-sql"></a>

## Visão Geral — SQL

> SQL é a linguagem usada para consultar e manipular dados em bancos relacionais. Em uma entrevista, eu separo o tema em modelagem, consultas, índices, transações e concorrência. Para performance, não olho apenas a query: considero cardinalidade, seletividade, índices, volume de dados e o plano de execução.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-select-insert-update-e-delete"></a>

## SELECT, INSERT, UPDATE e DELETE

> `SELECT` consulta dados; `INSERT` adiciona linhas; `UPDATE` altera; `DELETE` remove. Defino filtros e limites transacionais antes de executar mudanças, considerando constraints, índices e quantas linhas realmente serão afetadas.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-primary-key-unique-e-foreign-key"></a>

## Primary Key, Unique e Foreign Key

> Primary Key identifica unicamente cada linha e não permite `NULL`. Uma constraint `UNIQUE` também garante unicidade, mas sua semântica de `NULL` pode variar entre bancos. Foreign Key garante integridade referencial entre tabelas. Eu uso constraints no banco porque consistência não deve depender apenas da aplicação.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-not-null-check-e-integridade"></a>

## NOT NULL, CHECK e Integridade

> `NOT NULL` exige preenchimento; `CHECK` restringe valores conforme uma expressão; chaves e unicidade completam a integridade. Regras que o banco consegue garantir devem permanecer protegidas contra acessos concorrentes e outros clientes além da aplicação.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-normalizacao-vs-desnormalizacao"></a>

## Normalização vs Desnormalização

> Normalização reduz redundância e anomalias de atualização, normalmente separando entidades e relacionamentos em tabelas coerentes. Desnormalização duplica ou pré-calcula dados para reduzir joins ou acelerar leitura. Eu começo normalizado e desnormalizo apenas quando existe um requisito de performance comprovado, aceitando o custo adicional de consistência.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-null"></a>

## NULL

> `NULL` representa ausência ou valor desconhecido e não deve ser comparado com `=`. Uso `IS NULL` e `IS NOT NULL`. Também lembro que lógica com `NULL` é ternária — true, false e unknown — e isso afeta filtros, joins e agregações.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-ordem-logica-de-uma-consulta"></a>

## Ordem Lógica de uma Consulta

> Como modelo de raciocínio, considero FROM/JOIN, WHERE, GROUP BY, HAVING, SELECT e ORDER BY, seguidos da limitação do resultado. O otimizador pode escolher outra ordem física, preservando a semântica da consulta.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-inner-join-left-join-right-join-e-full-join"></a>

## INNER JOIN, LEFT JOIN, RIGHT JOIN e FULL JOIN

> `INNER JOIN` retorna somente linhas com correspondência entre os lados. `LEFT JOIN` preserva todas as linhas da tabela à esquerda e retorna `NULL` quando não existe correspondência à direita. `RIGHT JOIN` faz o inverso e `FULL JOIN` preserva linhas dos dois lados. Na prática, escolho o JOIN de acordo com a semântica do relacionamento, não por performance presumida.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-where-vs-having"></a>

## WHERE vs HAVING

> `WHERE` filtra linhas antes da agregação. `HAVING` filtra grupos depois do `GROUP BY`. Sempre que o filtro não depende de uma função agregadora, prefiro colocá-lo no `WHERE` para reduzir o conjunto de dados antes da agregação.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-group-by"></a>

## GROUP BY

> `GROUP BY` agrupa linhas para aplicar funções agregadoras como `COUNT`, `SUM`, `AVG`, `MIN` e `MAX`. Um cuidado importante é que colunas selecionadas fora de agregações precisam respeitar as regras de agrupamento do banco. Em produção também observo o custo de ordenar ou agregar grandes volumes.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-cte-vs-subquery"></a>

## CTE vs Subquery

> CTE melhora legibilidade e permite estruturar consultas complexas, além de suportar recursão em bancos que oferecem CTE recursiva. Subquery pode ser suficiente para casos pequenos. Não assumo que CTE é automaticamente mais rápida ou mais lenta: o otimizador pode tratá-las de maneiras diferentes, então performance deve ser validada no plano.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-window-functions"></a>

## Window Functions

> Window Functions calculam valores sobre um conjunto relacionado de linhas sem colapsar o resultado como um `GROUP BY`. Exemplos são `ROW_NUMBER`, `RANK`, `LAG`, `LEAD` e agregações com `OVER`. São muito úteis para ranking, comparação com linha anterior e acumulados sem recorrer a várias subqueries.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-exists-vs-in-vs-join"></a>

## EXISTS vs IN vs JOIN

> `EXISTS`, `IN` e `JOIN` expressam necessidades diferentes e otimizadores modernos podem gerar planos equivalentes em alguns casos. Uso `EXISTS` quando quero testar existência, `JOIN` quando preciso combinar dados e `IN` quando a semântica de pertencimento é clara. Para performance, comparo os planos em vez de aplicar uma regra universal.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-not-in-e-null"></a>

## NOT IN e NULL

> Se o conjunto de `NOT IN` contiver `NULL`, comparações podem resultar em unknown e excluir linhas inesperadamente. Para testar ausência, `NOT EXISTS` costuma expressar melhor a intenção, desde que a correlação e a regra para valores nulos estejam corretas.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-union-vs-union-all"></a>

## UNION vs UNION ALL

> `UNION` combina resultados removendo duplicados; `UNION ALL` apenas concatena os conjuntos. Como remover duplicados exige trabalho adicional, prefiro `UNION ALL` quando duplicidade é aceitável ou impossível pela lógica do domínio.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-delete-vs-truncate-vs-drop"></a>

## DELETE vs TRUNCATE vs DROP

> `DELETE` remove linhas e permite filtro com `WHERE`. `TRUNCATE` remove os dados da tabela de forma mais ampla e costuma ter comportamento e logging diferentes conforme o banco. `DROP` remove o próprio objeto da estrutura. Detalhes de transação, identidade e rollback variam entre SGBDs, então evito generalizar além disso.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-prepared-statements-e-sql-injection"></a>

## Prepared Statements e SQL Injection

> Para evitar SQL Injection, não concateno entrada do usuário na query. Uso parâmetros através de prepared statements ou APIs que façam binding corretamente. Isso separa dado de código SQL; validação de entrada continua importante, mas não substitui parametrização.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-indices"></a>

## Índices

> Índice é uma estrutura auxiliar que reduz o custo de localizar dados, normalmente evitando varrer toda a tabela. O trade-off é que índices ocupam espaço e aumentam o custo de `INSERT`, `UPDATE` e `DELETE`, porque também precisam ser mantidos. Portanto, não considero 'mais índices' automaticamente melhor.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-indice-composto-e-ordem-das-colunas"></a>

## Índice Composto e Ordem das Colunas

> Em um índice composto, a ordem das colunas é importante. Eu defino a ordem com base nos padrões reais de filtro, join e ordenação. Em índices B-tree, consultas tendem a aproveitar melhor os prefixos iniciais do índice; por isso um índice em `(cliente_id, status, data)` não equivale a um índice em `(status, cliente_id, data)`.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-indice-de-cobertura-parcial-e-funcional"></a>

## Índice de Cobertura, Parcial e Funcional

> Um índice pode incluir dados suficientes para reduzir acesso à tabela, cobrir apenas parte das linhas ou indexar expressões, quando o banco permite. A utilidade depende da consulta e do SGBD; validação por plano real é indispensável.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-seletividade-e-cardinalidade"></a>

## Seletividade e Cardinalidade

> Cardinalidade descreve quantos valores distintos existem em uma coluna e seletividade indica o quanto um predicado reduz o conjunto de linhas. Índices tendem a ser mais úteis quando a consulta elimina uma parcela relevante dos dados. Para colunas com poucos valores distintos, o otimizador pode preferir um scan dependendo da distribuição e do volume.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-sargabilidade"></a>

## Sargabilidade

> Uma condição sargable permite que o banco use um índice de forma eficiente para localizar o intervalo de dados. Por exemplo, aplicar uma função diretamente sobre a coluna filtrada pode impedir ou reduzir o uso do índice. Eu tento escrever o predicado de forma que a coluna indexada permaneça pesquisável e valido o resultado no execution plan.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-quando-um-indice-pode-nao-ser-usado"></a>

## Quando um índice pode não ser usado

> Um índice pode não ser escolhido quando o filtro retorna grande parte da tabela, quando existe conversão ou função sobre a coluna indexada, quando a estatística está ruim ou quando outro plano é estimado como mais barato. Por isso eu confirmo com o plano de execução em vez de assumir que criar um índice garante seu uso.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-execution-plan"></a>

## Execution Plan

> O execution plan mostra como o otimizador pretende executar uma consulta: scans, seeks, joins, ordenações, estimativas de linhas e custos relativos. Para investigar query lenta, eu comparo estimativa com quantidade real de linhas, verifico scans inesperados, joins caros, sorts, índices ausentes e estatísticas. O objetivo é encontrar a causa, não apenas 'forçar índice'.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-otimizacao-de-query-em-producao"></a>

## Otimização de Query em Produção

> Eu começo pela evidência: latência, frequência, volume retornado e execution plan. Depois verifico filtros, joins, índices, cardinalidade, estatísticas, quantidade de linhas lidas versus retornadas e possíveis operações O(n) desnecessárias. Só então considero reescrever a query, criar ou alterar índice, paginar ou remodelar o acesso.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-paginacao-offset-vs-keyset"></a>

## Paginação OFFSET vs Keyset

> Paginação com `OFFSET/LIMIT` é simples, mas offsets altos podem exigir que o banco percorra e descarte muitas linhas e podem gerar inconsistência visual quando os dados mudam entre páginas. Keyset pagination usa a última chave ordenada como cursor e costuma escalar melhor para grandes volumes, desde que exista ordenação estável e índice adequado.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-n-1-queries"></a>

## N+1 Queries

> N+1 acontece quando uma consulta inicial carrega uma coleção de registros e depois são executadas consultas adicionais para cada item. O efeito é aumento de round-trips e latência. Eu detecto por logs ou APM e resolvo alterando a estratégia de carregamento, usando joins, batch ou queries específicas conforme o caso.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-acid"></a>

## ACID

> ACID significa Atomicidade, Consistência, Isolamento e Durabilidade. Atomicidade garante que a transação seja aplicada por inteiro ou revertida; consistência preserva regras e constraints; isolamento controla interferência entre transações concorrentes; durabilidade garante que um commit confirmado sobreviva a falhas conforme as garantias do banco.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-isolation-levels"></a>

## Isolation Levels

> Os níveis clássicos são `READ UNCOMMITTED`, `READ COMMITTED`, `REPEATABLE READ` e `SERIALIZABLE`. Quanto maior o isolamento, menor a possibilidade de fenômenos como dirty read, non-repeatable read e phantom read, mas potencialmente maior o custo de concorrência. O comportamento exato depende do banco e do uso de locks ou MVCC.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-dirty-read-non-repeatable-read-e-phantom-read"></a>

## Dirty Read, Non-repeatable Read e Phantom Read

> Dirty read lê alterações ainda não confirmadas. Non-repeatable read observa mudança na mesma linha entre leituras; phantom read observa mudança no conjunto de linhas que satisfaz um filtro. A prevenção depende do isolamento e da implementação do banco.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-lost-update-e-write-skew"></a>

## Lost Update e Write Skew

> Lost update ocorre quando uma escrita sobrescreve indevidamente outra. Write skew pode ocorrer quando transações alteram linhas diferentes após ler uma condição comum, violando uma regra conjunta; versão em uma única linha não resolve toda invariante entre várias linhas.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-mvcc"></a>

## MVCC

> MVCC, Multi-Version Concurrency Control, mantém versões de linhas para permitir que leituras e escritas concorram com menos bloqueio em determinados níveis de isolamento. Isso melhora concorrência, mas não elimina conflitos, locks ou necessidade de entender o isolamento específico do banco.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-postgresql-vs-oracle-isolamento"></a>

## PostgreSQL vs Oracle: Isolamento

> Ambos usam READ COMMITTED como padrão em configurações usuais, mas as garantias não são intercambiáveis. PostgreSQL trata READ UNCOMMITTED como READ COMMITTED e oferece REPEATABLE READ; Oracle não oferece todos os mesmos níveis. Verifico snapshots, conflitos e necessidade de retry na documentação do banco. [PostgreSQL](https://www.postgresql.org/docs/current/transaction-iso.html) · [Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/26/cncpt/data-concurrency-and-consistency.html)

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-locks"></a>

## Locks

> Locks coordenam acesso concorrente aos dados para preservar consistência. Podem existir locks de leitura e escrita em diferentes granularidades conforme o banco. O problema aparece quando transações seguram locks por tempo demais, aumentando espera, latência e risco de deadlock. Por isso mantenho transações curtas e acesso aos recursos em ordem previsível.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-optimistic-vs-pessimistic-concurrency"></a>

## Optimistic vs Pessimistic Concurrency

> Concorrência otimista assume que conflitos são raros e detecta alteração concorrente, por exemplo usando uma coluna de versão. Concorrência pessimista bloqueia o recurso antes da alteração. Eu prefiro otimista quando conflitos são raros e pessimista quando o custo de conflito é alto e a contenção esperada justifica o bloqueio.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-atualizacao-condicional-atomica"></a>

## Atualização Condicional Atômica

> Posso proteger uma regra com uma única atualização, como decrementar estoque apenas quando ele é suficiente. Verifico a quantidade de linhas afetadas e uso transação e constraints conforme necessário, evitando o intervalo vulnerável entre consultar e atualizar separadamente.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-deadlock-no-banco"></a>

## Deadlock no Banco

> Deadlock ocorre quando transações criam uma espera circular por locks. O banco normalmente detecta o ciclo e aborta uma das transações. Para reduzir a ocorrência, mantenho transações curtas, adquiro recursos em ordem consistente, garanto bons índices para evitar bloquear linhas desnecessárias e preparo a aplicação para retry da transação vítima quando apropriado.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-transacao-longa"></a>

## Transação Longa

> Transações longas mantêm recursos, versões ou locks por mais tempo, aumentando contenção e consumo do banco. Em serviços backend, procuro manter o boundary transacional apenas ao redor do trabalho que realmente precisa ser atômico e evito chamadas remotas lentas dentro da transação sempre que possível.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-connection-pool-e-saturacao"></a>

## Connection Pool e Saturação

> O pool reutiliza conexões e limita quantas operações acessam o banco simultaneamente. Observo espera por conexão, transações longas e capacidade do banco; aumentar o pool pode apenas transferir a sobrecarga para o servidor.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="sql-resposta-completa-de-entrevista-sql"></a>

## Resumo de Entrevista

> Em bancos relacionais, combino modelagem e constraints com consultas, índices e transações. Escolho isolamento e controle de concorrência conforme as invariantes. Para performance, parto de queries reais e planos de execução, considerando volume, estatísticas, locks e custo de leitura e escrita.

[↑ Sumário do tópico](#sql-sql-bancos-relacionais-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-sumario"></a>

# 11. HTTP e APIs REST

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#http-rest-visao-geral)
- [Recursos, URIs e Contratos](#http-rest-recursos-uris-e-contratos)
- [GET, POST, PUT, PATCH e DELETE](#http-rest-get-post-put-patch-e-delete)
- [Métodos Seguros e Idempotentes](#http-rest-metodos-seguros-e-idempotentes)
- [Códigos de Status HTTP](#http-rest-codigos-de-status-http)
- [Headers, Content-Type e Accept](#http-rest-headers-content-type-e-accept)
- [REST Stateless](#http-rest-rest-stateless)
- [Paginação, Filtros e Ordenação](#http-rest-paginacao-filtros-e-ordenacao)
- [Erros e Problem Details](#http-rest-erros-e-problem-details)
- [OpenAPI e Documentação de Contrato](#http-rest-openapi-e-documentacao-de-contrato)
- [Idempotency Key em APIs](#http-rest-idempotency-key-em-apis)
- [Cache HTTP, ETag e If-Match](#http-rest-cache-http-etag-e-if-match)
- [TLS e Segurança de Transporte](#http-rest-tls-e-seguranca-de-transporte)
- [DNS, Conexões e Timeouts](#http-rest-dns-conexoes-e-timeouts)
- [REST, RPC e gRPC](#http-rest-rest-rpc-e-grpc)
- [Resumo de Entrevista](#http-rest-resumo-de-entrevista)

---

<a id="http-rest-visao-geral"></a>

## Visão Geral

> HTTP define a troca de mensagens entre cliente e servidor; REST é um estilo arquitetural baseado em recursos e interface uniforme. Uma API JSON sobre HTTP não se torna REST apenas por usar URLs e controllers.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-recursos-uris-e-contratos"></a>

## Recursos, URIs e Contratos

> Modelo recursos com identificadores estáveis e contratos claros de entrada, saída e erro. Evito expor diretamente entidades de persistência, pois isso acopla a API ao schema e pode revelar dados indevidos.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-get-post-put-patch-e-delete"></a>

## GET, POST, PUT, PATCH e DELETE

> `GET` consulta; `POST` submete processamento ou criação; `PUT` cria ou substitui a representação do recurso alvo; `PATCH` aplica alterações parciais; `DELETE` solicita remoção. O significado deve seguir o contrato HTTP e a regra de negócio.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-metodos-seguros-e-idempotentes"></a>

## Métodos Seguros e Idempotentes

> Métodos seguros, como `GET`, não solicitam mudança de estado de negócio. `PUT` e `DELETE` são idempotentes por semântica, embora respostas possam variar; `POST` e `PATCH` podem receber mecanismos de idempotência, mas ela não é garantida pelo método.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-codigos-de-status-http"></a>

## Códigos de Status HTTP

> `200` indica sucesso, `201` criação e `204` sucesso sem corpo; `400` entrada inválida, `401` ausência de autenticação válida, `403` proibição e `404` recurso não encontrado. `409` sinaliza conflito, `429` excesso de requisições e `5xx` falhas do servidor.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-headers-content-type-e-accept"></a>

## Headers, Content-Type e Accept

> `Content-Type` informa o formato enviado; `Accept` indica formatos aceitos na resposta. Headers também transportam autenticação, cache e rastreamento; trato esses valores como parte do contrato e não como substitutos da autorização.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-rest-stateless"></a>

## REST Stateless

> Uma requisição deve trazer o contexto necessário para ser entendida sem depender de uma sessão conversacional no servidor. Stateless não significa ausência de banco nem de estado de negócio; significa reduzir dependência de contexto de interação armazenado entre chamadas.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-paginacao-filtros-e-ordenacao"></a>

## Paginação, Filtros e Ordenação

> Defino limites de página, filtros permitidos e ordenação estável. Para grandes volumes, cursores podem ser mais eficientes que offsets; um desempate único, como `id`, evita ambiguidades quando vários registros compartilham a mesma data.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-erros-e-problem-details"></a>

## Erros e Problem Details

> Padronizo respostas de erro com código, descrição útil e identificador de rastreamento. Problem Details oferece um formato comum para APIs HTTP; evito retornar stack traces, detalhes internos ou informações sensíveis.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-openapi-e-documentacao-de-contrato"></a>

## OpenAPI e Documentação de Contrato

> OpenAPI descreve rotas, parâmetros, schemas, respostas e mecanismos de segurança. A documentação deve evoluir com a API e apoiar validações; um contrato publicado sem testes de compatibilidade pode ficar desatualizado.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-idempotency-key-em-apis"></a>

## Idempotency Key em APIs

> Uma chave idempotente permite reconhecer tentativas da mesma operação. Registro chave, resultado e vínculo com a requisição de forma atômica, definindo expiração e tratamento de chamadas concorrentes; reutilizar a chave com outro conteúdo deve ser detectado.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-cache-http-etag-e-if-match"></a>

## Cache HTTP, ETag e If-Match

> `Cache-Control` orienta reutilização de respostas e `ETag` identifica uma versão da representação. `If-None-Match` ajuda na revalidação; `If-Match` condiciona uma alteração à versão esperada e pode impedir atualizações perdidas.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-tls-e-seguranca-de-transporte"></a>

## TLS e Segurança de Transporte

> TLS protege confidencialidade e integridade em trânsito e autentica o servidor por certificados. Isso não substitui autorização, validação de entrada nem proteção dos dados depois que chegam à aplicação.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-dns-conexoes-e-timeouts"></a>

## DNS, Conexões e Timeouts

> Uma chamada remota pode gastar tempo em resolução DNS, conexão, TLS e leitura. Configuro limites adequados a cada cliente e um prazo total da operação, porque limitar só a leitura pode deixar outras etapas sem proteção.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-rest-rpc-e-grpc"></a>

## REST, RPC e gRPC

> REST enfatiza recursos e semântica HTTP; RPC expressa chamadas de operações. gRPC oferece contratos tipados e streaming com Protobuf em seu uso comum, mas exige avaliar suporte dos clientes, infraestrutura e estratégia de evolução do contrato.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="http-rest-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Projeto APIs com recursos, contratos, status e erros consistentes. Considero autenticação, autorização, idempotência, paginação, compatibilidade e timeouts desde o início, porque uma chamada HTTP também é uma fronteira sujeita a falhas.

[↑ Sumário do tópico](#http-rest-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-ecossistema-spring-visao-geral-sumario"></a>

# 12. Ecossistema Spring — Visão Geral

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Resumo do Ecossistema Spring](#spring-ecossistema-spring-visao-geral-resposta-de-60-segundos-sobre-o-ecossistema)

---

<a id="spring-ecossistema-spring-visao-geral-resposta-de-60-segundos-sobre-o-ecossistema"></a>

## Resumo do Ecossistema Spring

> Spring Core oferece IoC e beans; Boot simplifica configuração e operação; MVC trata HTTP; Data integra persistência; Security controla acesso; Cloud integra padrões distribuídos. Estudo primeiro as bases de Java, HTTP e banco para compreender as garantias e os limites dessas abstrações.

[↑ Sumário do tópico](#spring-ecossistema-spring-visao-geral-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-sumario"></a>

# 13. Spring Core

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#spring-spring-core-visao-geral)
- [IoC e Dependency Injection](#spring-spring-core-regra-pratica)
- [Injeção por Construtor vs Campo](#spring-spring-core-injecao-por-construtor-vs-campo)
- [Bean, Container e Ciclo de Vida](#spring-spring-core-5-ciclo-de-vida)
- [@Component, @Service, @Repository e @Bean](#spring-spring-core-component-service-repository-e-bean)
- [@Configuration e Component Scan](#spring-spring-core-configuration-e-component-scan)
- [Escopos singleton, prototype e request](#spring-spring-core-escopos-singleton-prototype-e-request)
- [@Primary, @Qualifier e @Lazy](#spring-spring-core-trade-off)
- [Proxies e Self-invocation](#spring-spring-core-trade-off-2)
- [AOP, JDK Proxy e Proxy de Classe](#spring-spring-core-aop-jdk-proxy-e-proxy-de-classe)
- [@Async e Contexto de Execução](#spring-spring-core-async-e-contexto-de-execucao)
- [Dependência Circular](#spring-spring-core-dependencia-circular)
- [Configuração e Eventos Locais](#spring-spring-core-atencao)
- [Resumo de Entrevista](#spring-spring-core-resposta-completa-de-entrevista)

---

<a id="spring-spring-core-visao-geral"></a>

## Visão Geral

> Spring Core fornece o container de IoC e o mecanismo de Dependency Injection. Em vez de as classes criarem diretamente suas dependências, o container gerencia esses objetos como beans e injeta as dependências necessárias. Isso reduz acoplamento e facilita testes, configuração e substituição de implementações.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-regra-pratica"></a>

## IoC e Dependency Injection

> IoC é a inversão do controle de criação e gerenciamento dos objetos para o container. Dependency Injection é a forma como o container fornece essas dependências. Prefiro constructor injection porque deixa o contrato explícito e facilita testes.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-injecao-por-construtor-vs-campo"></a>

## Injeção por Construtor vs Campo

> Injeção por construtor explicita dependências obrigatórias e permite campos finais e testes diretos. Injeção em campo esconde parte do contrato e depende de infraestrutura para preenchimento; uso setters quando a dependência for realmente opcional e fizer sentido.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-5-ciclo-de-vida"></a>

## Bean, Container e Ciclo de Vida

> Bean é um objeto gerenciado pelo container Spring. Seu ciclo pode incluir instanciação, injeção, pós-processamento, inicialização e destruição. O escopo singleton é por definição de bean e container; não garante thread safety e não justifica guardar estado de requisição em services.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-component-service-repository-e-bean"></a>

## @Component, @Service, @Repository e @Bean

> `@Component` marca um componente detectável por varredura; `@Service` e `@Repository` expressam papéis especializados. `@Bean` registra explicitamente o resultado de um método, sendo útil para bibliotecas externas e criação controlada.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-configuration-e-component-scan"></a>

## @Configuration e Component Scan

> `@Configuration` organiza definições de beans. Component scan descobre componentes nos pacotes configurados; escaneamento excessivo ou incorreto pode carregar configurações indevidas ou deixar dependências sem registro.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-escopos-singleton-prototype-e-request"></a>

## Escopos singleton, prototype e request

> Singleton reutiliza um bean no container; prototype cria instâncias a cada solicitação ao container; request associa um bean à requisição web. Injetar um prototype diretamente em singleton não gera uma instância nova em cada chamada do método.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-trade-off"></a>

## @Primary, @Qualifier e @Lazy

> Quando existem múltiplos beans do mesmo tipo, uso `@Primary` para definir o padrão ou `@Qualifier` para selecionar explicitamente. `@Lazy` posterga inicialização, mas deve ser usado com critério.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-trade-off-2"></a>

## Proxies e Self-invocation

> Spring usa proxies em recursos como transações, cache e segurança. O proxy intercepta chamadas ao bean e aplica comportamento adicional. Um cuidado clássico é self-invocation, porque chamadas internas podem não passar pelo proxy.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-aop-jdk-proxy-e-proxy-de-classe"></a>

## AOP, JDK Proxy e Proxy de Classe

> AOP separa preocupações transversais como transações e métricas. No modelo usual do Spring, proxies baseados em interfaces ou subclasses interceptam chamadas; métodos e classes finais podem limitar proxies por subclassificação, e chamadas internas não passam por um novo proxy.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-async-e-contexto-de-execucao"></a>

## @Async e Contexto de Execução

> `@Async` delega execução a um executor quando a chamada atravessa o mecanismo configurado. A transação e os ThreadLocals do chamador não são propagados automaticamente para a outra thread; configuro erros, capacidade e contexto explicitamente.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-dependencia-circular"></a>

## Dependência Circular

> Uma dependência circular aparece quando componentes precisam uns dos outros para serem construídos ou usados. Em geral, reviso responsabilidades e extraio a colaboração adequada; usar inicialização tardia pode adiar o problema sem corrigir o acoplamento.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-atencao"></a>

## Configuração e Eventos Locais

> Spring permite externalizar configuração, ativar configurações por profiles e publicar eventos locais. Eu diferencio eventos dentro do processo de mensageria distribuída.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-core-resposta-completa-de-entrevista"></a>

## Resumo de Entrevista

> Spring Core fornece IoC, injeção de dependências e gerenciamento de beans. Prefiro injeção por construtor, entendo escopos e evito estado mutável compartilhado. Como transações e outros recursos podem depender de proxies, considero quais chamadas realmente passam pela interceptação.

[↑ Sumário do tópico](#spring-spring-core-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-sumario"></a>

# 14. Spring Boot

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#spring-spring-boot-visao-geral)
- [@SpringBootApplication](#spring-spring-boot-springbootapplication)
- [Starters e Dependency Management](#spring-spring-boot-starters-e-dependency-management)
- [Auto-configuration e Back-off](#spring-spring-boot-2-back-off)
- [@ConfigurationProperties vs @Value](#spring-spring-boot-3-profiles)
- [Profiles e Configuração Externa](#spring-spring-boot-profiles-e-configuracao-externa)
- [DevTools](#spring-spring-boot-devtools)
- [Servidor Embarcado](#spring-spring-boot-embedded-server)
- [Actuator e Segurança dos Endpoints](#spring-spring-boot-3-seguranca-dos-endpoints)
- [Startup e Diagnóstico](#spring-spring-boot-startup-e-diagnostico)
- [Resumo de Entrevista](#spring-spring-boot-resposta-completa-de-entrevista)

---

<a id="spring-spring-boot-visao-geral"></a>

## Visão Geral

> Spring Boot não substitui o Spring Framework. Ele reduz configuração manual através de auto-configuration, starters, servidor embarcado, configuração externa e ferramentas operacionais como Actuator.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-springbootapplication"></a>

## @SpringBootApplication

> Combina a configuração do Boot, habilitação de auto-configuração e component scan. O pacote da classe principal influencia a varredura padrão; posicioná-la corretamente evita componentes não encontrados ou configuração inesperada.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-starters-e-dependency-management"></a>

## Starters e Dependency Management

> Starters agrupam dependências para capacidades como web ou persistência; o gerenciamento de versões do Boot mantém um conjunto compatível. Um starter não implementa sozinho a funcionalidade, e sobrescrever versões exige verificar compatibilidade.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-2-back-off"></a>

## Auto-configuration e Back-off

> Auto-configuration é condicional. O Boot observa classpath, propriedades e beans existentes e registra defaults quando fazem sentido. Se eu forneço uma configuração própria, muitas auto-configurações fazem back-off.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-3-profiles"></a>

## @ConfigurationProperties vs @Value

> Para configuração externa, prefiro `@ConfigurationProperties` quando existe um grupo de propriedades. Isso melhora tipagem, validação e manutenção em comparação com espalhar `@Value`.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-profiles-e-configuracao-externa"></a>

## Profiles e Configuração Externa

> Profiles selecionam configurações para condições específicas, como ambientes. Externalizo valores e entendo a precedência das fontes; segredos não devem entrar no repositório nem ser tratados como simples diferenças de arquivo entre profiles.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-devtools"></a>

## DevTools

> DevTools auxilia o desenvolvimento com recursos como reinicialização ao detectar mudanças relevantes. É uma ferramenta de produtividade local e não substitui build reproduzível, testes nem uma estratégia de atualização em produção.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-embedded-server"></a>

## Servidor Embarcado

> O servidor embarcado permite empacotar aplicação e servidor juntos, simplificando deploy e operação.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-3-seguranca-dos-endpoints"></a>

## Actuator e Segurança dos Endpoints

> Actuator fornece endpoints operacionais e integração com métricas. Em produção eu exponho somente o necessário, protejo endpoints sensíveis e uso health/readiness/liveness e métricas para observabilidade.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-startup-e-diagnostico"></a>

## Startup e Diagnóstico

> Para diagnosticar auto-configuration eu verifico condições, classpath, propriedades e beans existentes. O comportamento normalmente deriva de uma condition satisfeita ou não satisfeita.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-boot-resposta-completa-de-entrevista"></a>

## Resumo de Entrevista

> Spring Boot simplifica a configuração com starters, auto-configuração condicional e gerenciamento de dependências. Externalizo propriedades, valido configurações e uso Actuator para operação. Para diagnosticar startup, verifico condições, beans, classpath e segurança dos endpoints expostos.

[↑ Sumário do tópico](#spring-spring-boot-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-sumario"></a>

# 15. Spring MVC

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#spring-spring-mvc-visao-geral)
- [DispatcherServlet e HttpMessageConverter](#spring-spring-mvc-4-httpmessageconverter)
- [Responsabilidades do Controller](#spring-spring-mvc-regra-pratica)
- [@Controller vs @RestController](#spring-spring-mvc-controller-vs-restcontroller)
- [@PathVariable, @RequestParam e @RequestBody](#spring-spring-mvc-pathvariable-requestparam-e-requestbody)
- [Bean Validation](#spring-spring-mvc-validacao)
- [@RestControllerAdvice e Tratamento de Erros](#spring-spring-mvc-2-controlleradvice)
- [Filter vs Interceptor](#spring-spring-mvc-interceptor)
- [Spring MVC vs WebFlux](#spring-spring-mvc-regra-pratica-2)
- [Resumo de Entrevista](#spring-spring-mvc-resposta-completa-de-entrevista)

---

<a id="spring-spring-mvc-visao-geral"></a>

## Visão Geral

> Spring MVC é o framework web baseado em Servlet do Spring. Ele usa o DispatcherServlet como front controller, resolve o handler, executa o controller e converte request e response através de message converters.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-4-httpmessageconverter"></a>

## DispatcherServlet e HttpMessageConverter

> O DispatcherServlet recebe a requisição, usa HandlerMapping para encontrar o controller e HandlerAdapter para executá-lo. Os HttpMessageConverters fazem a conversão entre o corpo HTTP e objetos Java.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-regra-pratica"></a>

## Responsabilidades do Controller

> Controller deve lidar principalmente com rota, input, validação, status HTTP e output. Regra de negócio relevante fica em serviços ou no domínio.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-controller-vs-restcontroller"></a>

## @Controller vs @RestController

> `@Controller` pode retornar nomes de views e também corpos com `@ResponseBody`. `@RestController` combina essas duas anotações e normalmente serializa o retorno diretamente no corpo HTTP, sendo comum em APIs.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-pathvariable-requestparam-e-requestbody"></a>

## @PathVariable, @RequestParam e @RequestBody

> `@PathVariable` lê partes da rota; `@RequestParam`, parâmetros da requisição; `@RequestBody`, o corpo convertido por message converters. Escolho cada origem conforme o contrato e valido entradas antes de executar regras de negócio.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-validacao"></a>

## Bean Validation

> Uso Bean Validation para regras estruturais do input. Regras de negócio permanecem na camada responsável pelo domínio.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-2-controlleradvice"></a>

## @RestControllerAdvice e Tratamento de Erros

> Centralizo tratamento HTTP de exceptions com `@RestControllerAdvice`, transformando exceptions de aplicação em respostas consistentes sem vazar detalhes internos.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-interceptor"></a>

## Filter vs Interceptor

> Filter atua no nível Servlet e é adequado para preocupações HTTP gerais. Interceptor atua dentro do Spring MVC e consegue trabalhar com o handler selecionado.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-regra-pratica-2"></a>

## Spring MVC vs WebFlux

> Escolho MVC ou WebFlux com base no stack inteiro. Se minhas dependências são bloqueantes, MVC costuma ser mais simples e coerente. WebFlux faz mais sentido quando o fluxo é realmente não bloqueante de ponta a ponta.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-mvc-resposta-completa-de-entrevista"></a>

## Resumo de Entrevista

> Spring MVC processa HTTP com DispatcherServlet, controllers e conversores. Valido entradas, delego regras de negócio e padronizo erros. Distingo filtros de interceptadores e escolho MVC ou WebFlux considerando o comportamento bloqueante ou não bloqueante de toda a integração.

[↑ Sumário do tópico](#spring-spring-mvc-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-sumario"></a>

# 16. Spring Data

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#spring-spring-data-visao-geral)
- [Spring Data vs JPA vs Hibernate](#spring-spring-data-spring-data-vs-jpa-vs-hibernate)
- [Spring Data Commons](#spring-spring-data-spring-data-commons)
- [Repositories, Derived Queries e @Query](#spring-spring-data-4-query)
- [Persistence Context e Dirty Checking](#spring-spring-data-3-dirty-checking)
- [Estados da Entidade](#spring-spring-data-estados-da-entidade)
- [persist, merge e save](#spring-spring-data-persist-merge-e-save)
- [Flush vs Commit](#spring-spring-data-flush-vs-commit)
- [@Transactional e Limites da Transação](#spring-spring-data-4-rollback)
- [REQUIRED, REQUIRES_NEW e NESTED](#spring-spring-data-required-requires-new-e-nested)
- [Rollback: RuntimeException, Error e Checked Exception](#spring-spring-data-rollback-runtimeexception-error-e-checked-exception)
- [Self-invocation e @Transactional](#spring-spring-data-self-invocation-e-transactional)
- [readOnly e Transações Assíncronas](#spring-spring-data-readonly-e-transacoes-assincronas)
- [N+1 e Estratégias de Fetching](#spring-spring-data-cuidado)
- [LAZY, EAGER e LazyInitializationException](#spring-spring-data-lazy-eager-e-lazyinitializationexception)
- [Cascade vs orphanRemoval](#spring-spring-data-cascade-vs-orphanremoval)
- [Open Session in View](#spring-spring-data-open-session-in-view)
- [Page vs Slice](#spring-spring-data-slice)
- [Optimistic vs Pessimistic Locking](#spring-spring-data-2-pessimistic-lock)
- [Projections, Specifications e Auditing](#spring-spring-data-auditing)
- [Resumo de Entrevista](#spring-spring-data-resposta-completa-de-entrevista)

---

<a id="spring-spring-data-visao-geral"></a>

## Visão Geral

> Spring Data reúne abstrações de acesso a dados para diferentes tecnologias. Spring Data JPA adiciona repositories sobre JPA; JPA é uma especificação de persistência e Hibernate é uma implementação comum. O módulo escolhido determina as garantias disponíveis.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-spring-data-vs-jpa-vs-hibernate"></a>

## Spring Data vs JPA vs Hibernate

> JPA define o contrato de ORM, Hibernate implementa esse contrato e Spring Data JPA adiciona repositories e integração com Spring.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-spring-data-commons"></a>

## Spring Data Commons

> Spring Data Commons fornece abstrações compartilhadas pelos módulos, como metadados de repositories, paginação, ordenação e componentes de auditoria. É a base comum; as operações específicas e suas garantias dependem do módulo usado.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-4-query"></a>

## Repositories, Derived Queries e @Query

> Repositories reduzem boilerplate. Derived queries funcionam bem para casos simples; para consultas mais complexas uso `@Query`, Specifications ou queries dedicadas.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-3-dirty-checking"></a>

## Persistence Context e Dirty Checking

> O persistence context gerencia entidades. Quando uma entidade está managed, o provider pode detectar alterações por dirty checking e sincronizá-las no flush ou commit.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-estados-da-entidade"></a>

## Estados da Entidade

> Uma entidade pode estar transient, managed, detached ou removed. Dirty checking acompanha entidades gerenciadas; alterar uma entidade detached não garante atualização automática. Conhecer o estado evita usar `save` ou `merge` como soluções genéricas para qualquer situação.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-persist-merge-e-save"></a>

## persist, merge e save

> `persist` torna uma entidade nova gerenciada; `merge` copia estado para uma instância gerenciada e retorna essa instância. No Spring Data JPA, `save` decide conforme a identificação de entidade nova; o objeto recebido por `merge` não se torna automaticamente gerenciado.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-flush-vs-commit"></a>

## Flush vs Commit

> Flush sincroniza alterações do contexto de persistência com o banco, podendo executar SQL antes do commit. Commit conclui a transação; um flush bem-sucedido não significa que os dados estejam confirmados nem que um rollback posterior seja impossível.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-4-rollback"></a>

## @Transactional e Limites da Transação

> `@Transactional` define o boundary transacional e normalmente funciona via proxy. Também considero propagation, isolation e rollback. Evito `REQUIRES_NEW` sem necessidade porque cria transação independente e pode aumentar uso do pool.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-required-requires-new-e-nested"></a>

## REQUIRED, REQUIRES_NEW e NESTED

> REQUIRED participa da transação existente ou cria uma; REQUIRES_NEW cria outra transação física independente. NESTED pode usar savepoints dentro da mesma transação quando suportado. Suporte depende do transaction manager e do banco; independência também aumenta demanda por conexões.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-rollback-runtimeexception-error-e-checked-exception"></a>

## Rollback: RuntimeException, Error e Checked Exception

> Na configuração padrão, Spring faz rollback para `RuntimeException` e `Error`, mas não para checked exceptions. Posso definir `rollbackFor` ou outras regras; capturar e ignorar a falha pode impedir que o interceptor perceba a necessidade de rollback. [Referência](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/rolling-back.html)

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-self-invocation-e-transactional"></a>

## Self-invocation e @Transactional

> No modo padrão por proxies, uma chamada interna com `this` não atravessa o interceptor transacional. Assim, anotar o método chamado não cria automaticamente a transação esperada; reorganizo a colaboração entre beans ou uso controle transacional explícito quando apropriado. [Referência](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html)

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-readonly-e-transacoes-assincronas"></a>

## readOnly e Transações Assíncronas

> `readOnly=true` é uma indicação de otimização, não uma proibição universal de escrita. Em transações vinculadas à thread, criar uma tarefa assíncrona não transfere a transação do chamador; defino o limite transacional no fluxo que realmente executa o trabalho.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-cuidado"></a>

## N+1 e Estratégias de Fetching

> N+1 ocorre quando uma query principal dispara várias queries adicionais para relacionamentos. Detecto por logs/APM e resolvo conforme o caso com fetch join, EntityGraph, projection ou query dedicada.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-lazy-eager-e-lazyinitializationexception"></a>

## LAZY, EAGER e LazyInitializationException

> LAZY posterga carregamento quando suportado; EAGER exige que o dado seja disponibilizado, mas não garante uma única query. Acessar uma associação não inicializada fora do contexto pode falhar; escolho o fetch pelo caso de uso, evitando resolver tudo com EAGER.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-cascade-vs-orphanremoval"></a>

## Cascade vs orphanRemoval

> Cascade propaga operações de persistência entre entidades conforme o mapeamento. `orphanRemoval` remove dependentes quando deixam a associação prevista pelo contrato; nenhum dos dois equivale automaticamente a `ON DELETE CASCADE` no banco.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-open-session-in-view"></a>

## Open Session in View

> Manter o contexto de persistência durante a resposta permite carregamentos tardios, mas pode esconder N+1 e SQL fora do limite de serviço planejado. Prefiro preparar os dados necessários dentro de uma fronteira clara e medir o efeito dessa configuração.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-slice"></a>

## Page vs Slice

> Uso `Page` quando preciso do total e `Slice` quando basta saber se existe próxima página, evitando count caro em alguns cenários.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-2-pessimistic-lock"></a>

## Optimistic vs Pessimistic Locking

> Optimistic locking detecta conflito por versão e funciona bem quando conflitos são raros. Pessimistic locking bloqueia no banco e pode ser necessário quando o custo do conflito é alto, mas aumenta contenção.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-auditing"></a>

## Projections, Specifications e Auditing

> Projections reduzem dados carregados, Specifications ajudam em filtros dinâmicos e auditing automatiza metadados de criação e alteração.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-data-resposta-completa-de-entrevista"></a>

## Resumo de Entrevista

> Spring Data JPA reduz código de acesso a dados sobre JPA e seu provider. Entendo entidades, contexto de persistência, fetching e transações, incluindo proxies e rollback. Investigo N+1, índices, pool e locks porque o ORM não elimina o custo do banco.

[↑ Sumário do tópico](#spring-spring-data-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-sumario"></a>

# 17. Spring Security

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#spring-spring-security-visao-geral)
- [Authentication vs Authorization](#spring-spring-security-authentication-vs-authorization)
- [SecurityFilterChain](#spring-spring-security-security-filter-chain)
- [AuthenticationManager, Provider e UserDetailsService](#spring-spring-security-4-userdetailsservice)
- [PasswordEncoder e Hash de Senhas](#spring-spring-security-passwords)
- [CSRF vs CORS](#spring-spring-security-2-cors)
- [JWT e OAuth2 Resource Server](#spring-spring-security-jwt-e-oauth2-resource-server)
- [OAuth2 vs OpenID Connect](#spring-spring-security-oauth2-vs-openid-connect)
- [JWT: Validação e Limites](#spring-spring-security-jwt-validacao-e-limites)
- [Segurança de Método e de Recurso](#spring-spring-security-seguranca-de-metodo-e-de-recurso)
- [Sessão vs Stateless com Bearer Token](#spring-spring-security-sessao-vs-stateless-com-bearer-token)
- [Resumo de Entrevista](#spring-spring-security-resposta-completa-de-entrevista)

---

<a id="spring-spring-security-visao-geral"></a>

## Visão Geral

> Spring Security atua principalmente através de uma cadeia de filtros antes de a requisição chegar ao controller. Ele estabelece a identidade autenticada no SecurityContext e depois aplica regras de autorização.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-authentication-vs-authorization"></a>

## Authentication vs Authorization

> Authentication valida identidade. Authorization decide se essa identidade pode acessar determinado recurso ou executar determinada operação.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-security-filter-chain"></a>

## SecurityFilterChain

> Spring Security é filter-based no stack Servlet. A `SecurityFilterChain` define mecanismos e regras aplicados antes de a requisição alcançar o MVC.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-4-userdetailsservice"></a>

## AuthenticationManager, Provider e UserDetailsService

> O AuthenticationManager coordena o processo e delega a AuthenticationProviders. Em autenticação tradicional, o provider pode usar UserDetailsService para carregar o usuário e PasswordEncoder para validar a senha.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-passwords"></a>

## PasswordEncoder e Hash de Senhas

> Senhas devem ser armazenadas com password hashing apropriado, nunca reversível ou em texto puro. Spring abstrai isso com PasswordEncoder.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-2-cors"></a>

## CSRF vs CORS

> CSRF protege contra requisições forjadas quando credenciais são enviadas automaticamente pelo browser. CORS é uma política de browser para acesso entre origens e não substitui autenticação ou autorização.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-jwt-e-oauth2-resource-server"></a>

## JWT e OAuth2 Resource Server

> JWT é um formato de token, enquanto OAuth2 define fluxos de autorização. Em um Resource Server, Spring Security valida o bearer token, cria a Authentication e usa claims ou scopes para autorização.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-oauth2-vs-openid-connect"></a>

## OAuth2 vs OpenID Connect

> OAuth2 trata autorização delegada; OpenID Connect acrescenta uma camada de identidade sobre OAuth2. Access token é destinado ao recurso protegido; ID token descreve autenticação para o cliente e não deve ser usado indistintamente como token da API.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-jwt-validacao-e-limites"></a>

## JWT: Validação e Limites

> Decodificar um JWT não valida sua autenticidade. Verifico assinatura, algoritmo permitido, emissor, audiência e validade; um JWT assinado não é necessariamente criptografado, e revogação exige estratégia além de apenas conferir a assinatura.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-seguranca-de-metodo-e-de-recurso"></a>

## Segurança de Método e de Recurso

> Regras como `@PreAuthorize` protegem operações quando a segurança de método está habilitada. Também valido propriedade e permissões sobre o recurso solicitado; estar autenticado ou possuir um papel não garante acesso a qualquer registro.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-sessao-vs-stateless-com-bearer-token"></a>

## Sessão vs Stateless com Bearer Token

> Sessões permitem manter e revogar estado no servidor; bearer tokens podem reduzir consultas ao servidor de sessão, com outras exigências de validade e revogação. O armazenamento e o envio das credenciais determinam riscos como CSRF; usar JWT não elimina esse risco por definição.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-security-resposta-completa-de-entrevista"></a>

## Resumo de Entrevista

> Spring Security aplica autenticação e autorização por mecanismos adequados ao stack. Diferencio OAuth2, OIDC e JWT, valido tokens e protejo credenciais. Considero autorização por recurso, CSRF, CORS e menor privilégio, sem presumir que a proteção do gateway cobre todas as operações.

[↑ Sumário do tópico](#spring-spring-security-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-sumario"></a>

# 18. Testes Automatizados em Java e Spring

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#testes-java-spring-visao-geral)
- [Teste Unitário](#testes-java-spring-teste-unitario)
- [JUnit e Assertions](#testes-java-spring-junit-e-assertions)
- [Arrange, Act, Assert](#testes-java-spring-arrange-act-assert)
- [Mock, Stub, Fake e Spy](#testes-java-spring-mock-stub-fake-e-spy)
- [Mockito](#testes-java-spring-mockito)
- [Testes Parametrizados e Casos de Borda](#testes-java-spring-testes-parametrizados-e-casos-de-borda)
- [TDD](#testes-java-spring-tdd)
- [Teste de Integração](#testes-java-spring-teste-de-integracao)
- [Testcontainers](#testes-java-spring-testcontainers)
- [@SpringBootTest](#testes-java-spring-springboottest)
- [@WebMvcTest e MockMvc](#testes-java-spring-webmvctest-e-mockmvc)
- [@DataJpaTest](#testes-java-spring-datajpatest)
- [Testes de Contrato](#testes-java-spring-testes-de-contrato)
- [Testes End-to-End](#testes-java-spring-testes-end-to-end)
- [Pirâmide de Testes](#testes-java-spring-piramide-de-testes)
- [Testes Assíncronos e de Concorrência](#testes-java-spring-testes-assincronos-e-de-concorrencia)
- [Cobertura e Mutation Testing](#testes-java-spring-cobertura-e-mutation-testing)
- [Flakiness, Relógio e Estado Compartilhado](#testes-java-spring-flakiness-relogio-e-estado-compartilhado)
- [Testes de Carga e Performance](#testes-java-spring-testes-de-carga-e-performance)
- [Resumo de Entrevista](#testes-java-spring-resumo-de-entrevista)

---

<a id="testes-java-spring-visao-geral"></a>

## Visão Geral

> Testes automatizados verificam comportamento e reduzem o risco de regressões. Escolho o menor escopo que consegue revelar a falha relevante, combinando testes unitários, integração, contrato e jornadas completas.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-teste-unitario"></a>

## Teste Unitário

> Valida uma unidade de comportamento com execução rápida e dependências controladas. Para regras puras, instancio os objetos diretamente; subir todo o Spring para testar uma função simples aumenta o custo sem melhorar a evidência.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-junit-e-assertions"></a>

## JUnit e Assertions

> JUnit organiza execução, ciclo de vida e parâmetros dos testes. As assertions devem verificar o resultado observável e casos relevantes, não reproduzir a implementação nem apenas confirmar que o método não lançou erro.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-arrange-act-assert"></a>

## Arrange, Act, Assert

> Arrange prepara o cenário; Act executa o comportamento; Assert verifica o resultado. Essa organização deixa a intenção clara e facilita identificar se a falha está nos dados, na ação ou na expectativa.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-mock-stub-fake-e-spy"></a>

## Mock, Stub, Fake e Spy

> Stub fornece respostas controladas; mock verifica interações; fake oferece uma implementação simplificada funcional; spy observa ou substitui parte de um objeto real. Escolho pelo que o teste precisa demonstrar, evitando simular todos os detalhes internos.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-mockito"></a>

## Mockito

> Mockito cria dublês e configura respostas para dependências. Uso verificações de interação quando o efeito importante é uma chamada externa; excesso de `verify` pode acoplar o teste à organização interna e tornar refatorações frágeis.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-testes-parametrizados-e-casos-de-borda"></a>

## Testes Parametrizados e Casos de Borda

> Testes parametrizados exercitam a mesma regra com diferentes entradas. Incluo limites, ausência, duplicidade e entradas inválidas quando fazem parte do contrato, em vez de testar apenas o caminho feliz.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-tdd"></a>

## TDD

> TDD alterna um teste que falha, a implementação mínima para fazê-lo passar e refatoração com os testes verdes. Ajuda a explicitar comportamento e feedback rápido, mas não substitui análise de requisitos nem testes de integração.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-teste-de-integracao"></a>

## Teste de Integração

> Verifica a interação entre componentes reais, como aplicação e banco. É importante para transações, mapeamentos, SQL e serialização, que podem funcionar com mocks e falhar no ambiente real.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-testcontainers"></a>

## Testcontainers

> Testcontainers executa dependências reais em containers durante testes. Aproxima banco e broker do ambiente de produção; escolho versões adequadas, isolo dados e considero o tempo de inicialização e a disponibilidade do runtime de containers.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-springboottest"></a>

## @SpringBootTest

> Carrega o contexto da aplicação para testar integração entre componentes Spring. É útil para fluxos que precisam desse contexto, mas seu custo pede uso seletivo; o ambiente web configurado determina como HTTP será exercitado.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-webmvctest-e-mockmvc"></a>

## @WebMvcTest e MockMvc

> `@WebMvcTest` carrega uma fatia voltada à camada MVC. MockMvc testa o processamento HTTP do Spring sem iniciar um servidor de rede real; valido rotas, serialização, erros e segurança aplicável, controlando as dependências do controller.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-datajpatest"></a>

## @DataJpaTest

> Carrega uma fatia de persistência JPA para testar entidades, repositories e queries. Um banco em memória pode esconder diferenças de dialeto, locks e constraints; para esses casos, uso o mesmo tipo de banco da produção.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-testes-de-contrato"></a>

## Testes de Contrato

> Verificam expectativas entre consumidor e provedor, como campos, status e schemas. Ajudam a detectar incompatibilidade sem subir todo o sistema, mas não garantem disponibilidade nem o funcionamento completo de uma jornada.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-testes-end-to-end"></a>

## Testes End-to-End

> Exercitam uma jornada completa através das fronteiras relevantes. Priorizo fluxos críticos, pois são mais lentos e sensíveis a ambiente; não substituo toda a validação de regras por uma grande suíte E2E.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-piramide-de-testes"></a>

## Pirâmide de Testes

> Distribui a validação entre muitos testes rápidos e menos testes amplos e caros. A proporção depende dos riscos do sistema; o objetivo é feedback confiável com custo sustentável, não cumprir uma porcentagem fixa.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-testes-assincronos-e-de-concorrencia"></a>

## Testes Assíncronos e de Concorrência

> Uso coordenação e condições observáveis com prazo limite, evitando depender de pausas arbitrárias. Para consumidores, testo reentrega, duplicidade e falha entre persistência e confirmação, pois esses cenários revelam problemas de consistência.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-cobertura-e-mutation-testing"></a>

## Cobertura e Mutation Testing

> Cobertura mostra o código executado, sem garantir assertions úteis. Mutation testing altera pequenas partes do código para verificar se os testes detectam a mudança; melhora a avaliação da suíte, mas aumenta custo de execução.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-flakiness-relogio-e-estado-compartilhado"></a>

## Flakiness, Relógio e Estado Compartilhado

> Testes instáveis podem depender de horário, rede, concorrência ou dados de outro teste. Uso `Clock` controlado, isolamento de estado e expectativas com limites claros para remover a causa, em vez de repetir o teste até passar.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-testes-de-carga-e-performance"></a>

## Testes de Carga e Performance

> Testes de carga medem comportamento sob volume definido; stress busca limites e soak observa execução prolongada. Avalio latência de cauda, erros, throughput e saturação com dados representativos, sem confundir número de usuários com requisições por segundo.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="testes-java-spring-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Testo regras com unidades pequenas, integrações com componentes reais e contratos entre serviços. Reservo E2E para jornadas críticas e uso cobertura como indicador, mantendo foco em falhas que os testes realmente conseguem detectar.

[↑ Sumário do tópico](#testes-java-spring-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-de-software-sumario"></a>

# 19. Arquitetura de Software

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral — Arquitetura de Software](#arquitetura-visao-geral-arquitetura-de-software)
- [Requisitos Funcionais e Não Funcionais](#arquitetura-arquitetura-de-software-requisitos-funcionais-e-nao-funcionais)
- [Trade-off Arquitetural](#arquitetura-trade-off-arquitetural)
- [ADR e C4 Model](#arquitetura-arquitetura-de-software-adr-e-c4-model)
- [DDD e Arquitetura](#arquitetura-ddd-e-arquitetura)
- [Linguagem Ubíqua e Context Map](#arquitetura-arquitetura-de-software-linguagem-ubiqua-e-context-map)
- [Bounded Context](#arquitetura-bounded-context)
- [Entidade vs Value Object](#arquitetura-entidade-vs-value-object)
- [Agregado e Aggregate Root](#arquitetura-agregado-e-aggregate-root)
- [Application Service vs Domain Service](#arquitetura-arquitetura-de-software-application-service-vs-domain-service)
- [Arquitetura em Camadas](#arquitetura-arquitetura-em-camadas)
- [Arquitetura Hexagonal](#arquitetura-arquitetura-hexagonal)
- [Clean Architecture](#arquitetura-clean-architecture)
- [Arquitetura Hexagonal vs Clean Architecture](#arquitetura-arquitetura-hexagonal-vs-clean-architecture)
- [Monólito](#arquitetura-monolito)
- [Monólito Modular](#arquitetura-monolito-modular)
- [Microsserviços](#arquitetura-microservicos)
- [Monólito vs Microsserviços](#arquitetura-monolito-vs-microservicos)
- [Quando extrair um Microsserviço](#arquitetura-quando-extrair-um-microservico)
- [Síncrono vs Assíncrono](#arquitetura-sincrono-vs-assincrono)
- [API REST vs Mensageria](#arquitetura-api-rest-vs-mensageria)
- [Arquitetura Orientada a Mensagens](#arquitetura-arquitetura-orientada-a-mensagens)
- [Arquitetura Orientada a Eventos](#arquitetura-arquitetura-orientada-a-eventos)
- [Evento vs Comando](#arquitetura-evento-vs-comando)
- [At-most-once, At-least-once e Exactly-once](#arquitetura-at-most-once-at-least-once-e-exactly-once)
- [Consistência Forte vs Eventual](#arquitetura-consistencia-forte-vs-eventual)
- [CAP Theorem](#arquitetura-cap-theorem)
- [PACELC](#arquitetura-arquitetura-de-software-pacelc)
- [Banco por Serviço](#arquitetura-banco-por-servico)
- [Shared Database — Trade-off](#arquitetura-shared-database-trade-off)
- [Replicação e Read Replica](#arquitetura-replicacao-e-read-replica)
- [Particionamento vs Sharding](#arquitetura-arquitetura-de-software-particionamento-vs-sharding)
- [Sharding](#arquitetura-sharding)
- [CQRS](#arquitetura-cqrs)
- [CQRS vs Read Replica](#arquitetura-cqrs-vs-read-replica)
- [Event Sourcing](#arquitetura-event-sourcing)
- [Saga](#arquitetura-saga)
- [Saga — Orquestração vs Coreografia](#arquitetura-saga-orquestracao-vs-coreografia)
- [Outbox Pattern](#arquitetura-outbox-pattern)
- [Idempotência](#arquitetura-idempotencia)
- [API Gateway](#arquitetura-api-gateway)
- [BFF — Backend for Frontend](#arquitetura-bff-backend-for-frontend)
- [Service Discovery](#arquitetura-service-discovery)
- [Load Balancing](#arquitetura-load-balancing)
- [Stateless vs Stateful](#arquitetura-stateless-vs-stateful)
- [Escalabilidade Vertical vs Horizontal](#arquitetura-escalabilidade-vertical-vs-horizontal)
- [Alta Disponibilidade](#arquitetura-alta-disponibilidade)
- [Tolerância a Falhas](#arquitetura-tolerancia-a-falhas)
- [Retry, Timeout e Circuit Breaker na Arquitetura](#arquitetura-retry-timeout-e-circuit-breaker-na-arquitetura)
- [Bulkhead](#arquitetura-bulkhead)
- [Degradação Graciosa](#arquitetura-degradacao-graciosa)
- [Rate Limiting](#arquitetura-rate-limiting)
- [Backpressure](#arquitetura-backpressure)
- [Cache](#arquitetura-cache)
- [Cache-Aside](#arquitetura-cache-aside)
- [Cache Stampede](#arquitetura-cache-stampede)
- [Observabilidade](#arquitetura-observabilidade)
- [Logs, Métricas e Traces](#arquitetura-logs-metricas-e-traces)
- [SLO, SLI e SLA](#arquitetura-slo-sli-e-sla)
- [P95, P99 e Média](#arquitetura-p95-p99-e-media)
- [Arquitetura e Segurança](#arquitetura-arquitetura-e-seguranca)
- [Versionamento de API](#arquitetura-versionamento-de-api)
- [Backward Compatibility](#arquitetura-backward-compatibility)
- [Strangler Fig Pattern](#arquitetura-strangler-fig-pattern)
- [Anti-Corruption Layer](#arquitetura-anti-corruption-layer)
- [12-Factor App](#arquitetura-12-factor-app)
- [Arquitetura Cloud-Native](#arquitetura-arquitetura-cloud-native)
- [Kubernetes na Arquitetura](#arquitetura-kubernetes-na-arquitetura)
- [Arquitetura Evolutiva](#arquitetura-arquitetura-evolutiva)
- [Resumo de Entrevista](#arquitetura-resposta-completa-de-entrevista-arquitetura)

---

<a id="arquitetura-visao-geral-arquitetura-de-software"></a>

## Visão Geral — Arquitetura de Software

> Arquitetura de software define como responsabilidades, componentes, dados e integrações são organizados para atender requisitos funcionais e não funcionais. Em entrevista, eu procuro deixar claro que arquitetura não é escolher tecnologia, mas tomar decisões estruturais considerando trade-offs como escalabilidade, disponibilidade, consistência, complexidade, custo e capacidade de evolução.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-de-software-requisitos-funcionais-e-nao-funcionais"></a>

## Requisitos Funcionais e Não Funcionais

> Requisitos funcionais descrevem comportamentos; não funcionais definem propriedades como latência, disponibilidade, segurança e custo. Transformo expressões vagas como “rápido” em metas mensuráveis e condições de carga para comparar alternativas.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-trade-off-arquitetural"></a>

## Trade-off Arquitetural

> Toda decisão arquitetural troca uma propriedade por outra. Mais consistência pode reduzir disponibilidade; mais abstração pode reduzir acoplamento, mas aumentar complexidade; mais serviços aumentam autonomia, mas também operação. Em entrevista, eu tento explicitar qual requisito estou priorizando e qual custo estou aceitando.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-de-software-adr-e-c4-model"></a>

## ADR e C4 Model

> ADR registra uma decisão, contexto, alternativas e consequências. C4 organiza visões de contexto, containers e componentes, com detalhe de código quando útil; uso documentação suficiente para orientar decisões e evolução, sem diagramar tudo.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-ddd-e-arquitetura"></a>

## DDD e Arquitetura

> Domain-Driven Design ajuda quando o problema de negócio é complexo. Eu destaco linguagem ubíqua, bounded contexts, entidades, value objects, agregados e serviços de domínio. DDD não significa obrigatoriamente microsserviços. Pode ser aplicado em monólito modular e, na prática, ajuda mais a organizar o domínio do que a escolher infraestrutura.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-de-software-linguagem-ubiqua-e-context-map"></a>

## Linguagem Ubíqua e Context Map

> Linguagem ubíqua é o vocabulário compartilhado entre negócio e desenvolvimento dentro de um contexto. Context map mostra relações entre contextos e seus contratos; um bounded context não precisa corresponder exatamente a um microsserviço.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-bounded-context"></a>

## Bounded Context

> Bounded Context, em DDD, define uma fronteira dentro da qual um modelo de domínio possui significado consistente. Em arquitetura de microsserviços, ele ajuda a encontrar limites mais naturais para serviços. Um mesmo termo pode ter modelos diferentes em contextos distintos. Eu evito compartilhar o mesmo modelo de domínio entre serviços porque isso aumenta acoplamento.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-entidade-vs-value-object"></a>

## Entidade vs Value Object

> Entidade possui identidade própria ao longo do tempo. Value Object é definido por seus valores e normalmente é imutável. Por exemplo, um `Pedido` tende a ser entidade; `Dinheiro` pode ser um value object. Value Objects ajudam a encapsular regras e reduzir uso de tipos primitivos sem significado de negócio.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-agregado-e-aggregate-root"></a>

## Agregado e Aggregate Root

> Agregado é um conjunto de objetos de domínio tratado como uma unidade de consistência. A Aggregate Root é o ponto de entrada para alterações internas. Regras do agregado devem ser preservadas dentro dessa fronteira. Agregados muito grandes aumentam contenção e custo transacional, então tento mantê-los focados nas invariantes que realmente precisam de consistência forte.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-de-software-application-service-vs-domain-service"></a>

## Application Service vs Domain Service

> Application service coordena um caso de uso, permissões e transação. Domain service expressa uma regra de negócio que não cabe naturalmente em uma entidade ou value object; evito colocar toda a modelagem em serviços sem comportamento no domínio.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-em-camadas"></a>

## Arquitetura em Camadas

> Arquitetura em camadas separa responsabilidades em níveis como apresentação, aplicação, domínio e infraestrutura. O principal benefício é organização e separação de responsabilidades. O trade-off é que, quando aplicada rigidamente, pode gerar acoplamento entre camadas e fluxos artificiais. Eu uso quando o domínio e a complexidade justificam essa separação, sem transformar a estrutura em burocracia.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-hexagonal"></a>

## Arquitetura Hexagonal

> Arquitetura Hexagonal organiza o sistema em torno do domínio e usa portas e adaptadores para isolar infraestrutura. As portas representam contratos de entrada e saída; os adaptadores conectam HTTP, banco, mensageria ou APIs externas. O principal ganho é desacoplamento e testabilidade. O custo é mais abstração e mais classes, então aplico principalmente onde a independência de infraestrutura agrega valor.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-clean-architecture"></a>

## Clean Architecture

> Clean Architecture organiza dependências apontando para regras de negócio mais internas. Frameworks, banco e interface ficam nas bordas. O objetivo é manter o domínio independente de detalhes externos. O trade-off é aumento de abstrações e mapeamentos. Eu considero útil em sistemas com regra de negócio relevante e longa vida útil, mas evito aplicar de forma dogmática em CRUDs simples.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-hexagonal-vs-clean-architecture"></a>

## Arquitetura Hexagonal vs Clean Architecture

> As duas buscam reduzir dependência de infraestrutura e proteger o domínio. Hexagonal enfatiza portas e adaptadores; Clean Architecture enfatiza círculos ou camadas com dependências apontando para dentro. Na prática, os princípios se sobrepõem bastante. Eu escolho a modelagem que deixa as dependências explícitas sem criar abstrações desnecessárias.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-monolito"></a>

## Monólito

> Monólito é uma aplicação implantada como uma unidade única. Ele não é sinônimo de código ruim. Pode ser simples de desenvolver, testar, implantar e operar, especialmente no início. O problema aparece quando cresce sem modularização, gerando acoplamento e dificuldade de evolução. Eu começo simples e só distribuo quando existe uma razão concreta.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-monolito-modular"></a>

## Monólito Modular

> Monólito modular mantém um único deploy, mas separa o sistema em módulos com fronteiras claras e dependências controladas. Ele preserva simplicidade operacional enquanto reduz acoplamento interno. Para muitos sistemas, é um excelente passo antes de microsserviços, porque permite descobrir boundaries sem assumir de imediato o custo de distribuição.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-microservicos"></a>

## Microsserviços

> Microsserviços dividem o sistema em serviços implantáveis de forma independente, normalmente alinhados a capacidades de negócio. Os benefícios incluem autonomia de deploy, isolamento de falhas e escalabilidade independente. O custo é alto: rede, observabilidade distribuída, consistência eventual, versionamento, segurança, tracing, DevOps e operação. Eu uso microsserviços quando esses benefícios compensam a complexidade distribuída.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-monolito-vs-microservicos"></a>

## Monólito vs Microsserviços

> Eu não trato microsserviços como evolução obrigatória do monólito. Monólito reduz complexidade operacional e favorece consistência local. Microsserviços ganham autonomia, escalabilidade e isolamento, mas introduzem falhas de rede e consistência distribuída. A decisão depende de escala organizacional, domínios independentes, frequência de deploy, requisitos de disponibilidade e capacidade operacional do time.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-quando-extrair-um-microservico"></a>

## Quando extrair um Microsserviço

> Eu consideraria extrair um serviço quando existe uma fronteira de domínio clara e uma necessidade concreta, como escala independente, ritmo de deploy diferente, isolamento de falha, requisitos de segurança específicos ou ownership por outro time. Não extraio apenas porque uma classe ficou grande. Primeiro procuro um boundary estável e mensuro o custo operacional da separação.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-sincrono-vs-assincrono"></a>

## Síncrono vs Assíncrono

> Comunicação síncrona é mais simples quando o chamador precisa de resposta imediata, mas cria acoplamento temporal e propaga latência e falhas. Comunicação assíncrona reduz esse acoplamento e absorve picos, porém exige lidar com consistência eventual, idempotência e observabilidade. Eu escolho com base na necessidade de resposta e no comportamento esperado em falhas.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-api-rest-vs-mensageria"></a>

## API REST vs Mensageria

> REST é adequado para interação síncrona em que o chamador precisa de resposta imediata. Mensageria é melhor quando quero desacoplar tempo, absorver picos ou propagar eventos. Muitas arquiteturas usam ambos: comando síncrono para uma operação e evento assíncrono para comunicar consequências.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-orientada-a-mensagens"></a>

## Arquitetura Orientada a Mensagens

> Mensageria desacopla produtores e consumidores no tempo e permite bufferizar carga. O trade-off é lidar com entrega duplicada, ordenação, retries, DLQ e observabilidade. Eu considero idempotência do consumidor e semântica de ack parte do contrato de produção.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-orientada-a-eventos"></a>

## Arquitetura Orientada a Eventos

> Arquitetura orientada a eventos comunica mudanças de estado através de eventos, reduzindo acoplamento temporal entre produtores e consumidores. Ela melhora extensibilidade e integração assíncrona, mas traz consistência eventual, duplicidade, ordenação, observabilidade e tratamento de falhas. Eu considero idempotência e semântica de entrega parte obrigatória do design.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-evento-vs-comando"></a>

## Evento vs Comando

> Comando expressa intenção: `CriarPedido`. Evento expressa um fato que já aconteceu: `PedidoCriado`. Um comando normalmente tem um destinatário esperado e pode falhar; um evento pode ter zero ou vários consumidores. Misturar esses conceitos costuma gerar contratos confusos em arquiteturas orientadas a mensagens.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-at-most-once-at-least-once-e-exactly-once"></a>

## At-most-once, At-least-once e Exactly-once

> At-most-once prioriza não duplicar, mas pode perder mensagem. At-least-once reduz risco de perda, mas permite duplicidade e exige idempotência. Exactly-once geralmente depende de escopo e garantias específicas da tecnologia; eu evito tratá-lo como garantia mágica end-to-end sem analisar banco, broker e efeitos externos.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-consistencia-forte-vs-eventual"></a>

## Consistência Forte vs Eventual

> Consistência forte precisa de uma garantia definida, como linearizabilidade: uma leitura iniciada após uma escrita concluída observa essa escrita ou uma posterior. Consistência eventual permite divergência temporária, mas prevê convergência sem novas atualizações. Isso não é sinônimo dos níveis de isolamento de transações.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-cap-theorem"></a>

## CAP Theorem

> CAP afirma que, durante uma partição de rede, não é possível garantir simultaneamente consistência linearizável e disponibilidade para toda requisição a nós não falhos. A decisão é como cada operação reage à partição; não é simplesmente escolher dois de três atributos em qualquer situação.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-de-software-pacelc"></a>

## PACELC

> PACELC acrescenta que, mesmo sem partição, sistemas replicados podem trocar latência por consistência. Durante partição, aparece a escolha entre disponibilidade e consistência; fora dela, avalio quanto a coordenação custa para a garantia exigida.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-banco-por-servico"></a>

## Banco por Serviço

> Em microsserviços, cada serviço deve idealmente controlar seu próprio modelo e persistência. Compartilhar diretamente o mesmo schema cria acoplamento forte e dificulta deploy independente. Integração entre contextos deve acontecer por APIs ou eventos, embora migração gradual possa exigir compromissos temporários.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-shared-database-trade-off"></a>

## Shared Database — Trade-off

> Banco compartilhado simplifica joins e transações no início, mas aumenta acoplamento entre serviços e permite que um serviço dependa do schema interno de outro. Se a arquitetura exige autonomia real, eu procuro separar ownership dos dados e evitar acesso cruzado direto.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-replicacao-e-read-replica"></a>

## Replicação e Read Replica

> Read replicas podem aumentar capacidade de leitura e isolar carga analítica, mas introduzem possibilidade de replication lag. Uma leitura imediatamente após escrita pode retornar estado antigo. Portanto, eu só uso replica quando o caso de uso tolera essa consistência e faço roteamento de leitura conscientemente.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-de-software-particionamento-vs-sharding"></a>

## Particionamento vs Sharding

> Particionamento divide logicamente ou fisicamente os dados, inclusive dentro do mesmo banco. Sharding distribui partições entre instâncias ou nós; pode ampliar capacidade, mas adiciona roteamento, rebalanceamento e dificuldade em consultas e transações entre shards.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-sharding"></a>

## Sharding

> Sharding divide dados entre múltiplas partições ou bancos usando uma shard key. Ele permite escalar volume e throughput, mas complica joins globais, transações, rebalanceamento e hotspots. A escolha da shard key é crítica. Eu só considero sharding quando uma única instância ou cluster já não atende com alternativas mais simples.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-cqrs"></a>

## CQRS

> CQRS separa modelos ou caminhos de escrita e leitura. Pode variar desde handlers separados até armazenamentos distintos. O benefício é otimizar cada lado para necessidades diferentes e reduzir acoplamento entre modelos. O trade-off é maior complexidade, duplicação de modelos e possível consistência eventual. Eu não aplico CQRS apenas porque existem comandos e queries.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-cqrs-vs-read-replica"></a>

## CQRS vs Read Replica

> Read replica é uma estratégia de infraestrutura para escalar leitura sobre um modelo semelhante. CQRS é uma decisão arquitetural que separa modelos ou caminhos de leitura e escrita. Eles podem coexistir, mas resolvem problemas diferentes.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-event-sourcing"></a>

## Event Sourcing

> Event Sourcing persiste a sequência de eventos como fonte de verdade, em vez de armazenar apenas o estado atual. Isso oferece histórico completo e reconstrução de estado, mas aumenta bastante a complexidade de versionamento de eventos, replay, snapshots, debugging e evolução do modelo. É útil em domínios que realmente precisam desse histórico, não como padrão geral.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-saga"></a>

## Saga

> Saga coordena uma transação de negócio distribuída através de várias transações locais, usando ações compensatórias quando uma etapa falha. Pode ser por coreografia, via eventos, ou orquestração, com um coordenador explícito. Ela não fornece rollback ACID global; fornece uma estratégia de consistência de negócio entre serviços.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-saga-orquestracao-vs-coreografia"></a>

## Saga — Orquestração vs Coreografia

> Na orquestração, um componente central decide os próximos passos, o que facilita visualizar o fluxo, mas aumenta dependência no orquestrador. Na coreografia, serviços reagem a eventos, reduzindo controle central, porém o fluxo pode ficar mais difícil de rastrear. Eu escolho conforme complexidade do processo e necessidade de governança do fluxo.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-outbox-pattern"></a>

## Outbox Pattern

> Outbox Pattern resolve o problema de atualizar o banco e publicar um evento sem depender de uma transação distribuída. A aplicação grava a mudança de negócio e um registro de outbox na mesma transação local. Depois, outro processo publica o evento. O trade-off é lidar com publicação assíncrona, deduplicação e limpeza da outbox.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-idempotencia"></a>

## Idempotência

> Uma operação idempotente pode ser repetida sem produzir efeitos adicionais indevidos. Em sistemas distribuídos isso é essencial porque retries e redelivery acontecem. Implemento com chave idempotente, unique constraint, registro de mensagens processadas ou lógica de negócio naturalmente idempotente, dependendo do caso.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-api-gateway"></a>

## API Gateway

> API Gateway centraliza preocupações de borda, como roteamento, autenticação, rate limiting e observabilidade. Ele pode simplificar clientes, mas não deve concentrar regras de negócio nem se tornar um novo monólito. Segurança e autorização internas ainda podem ser necessárias dependendo do trust boundary.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-bff-backend-for-frontend"></a>

## BFF — Backend for Frontend

> BFF cria uma camada backend específica para as necessidades de um tipo de cliente, como web ou mobile. Ele reduz lógica de composição no frontend e permite contratos mais adequados, mas adiciona mais um serviço para operar. Eu uso quando os clientes possuem necessidades suficientemente diferentes para justificar essa camada.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-service-discovery"></a>

## Service Discovery

> Service Discovery permite localizar instâncias dinamicamente. Em plataformas como Kubernetes, esse recurso normalmente já existe através de Services e DNS. Eu evito adicionar um registry dedicado quando a plataforma já resolve o problema adequadamente.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-load-balancing"></a>

## Load Balancing

> Load balancing distribui requisições entre múltiplas instâncias para melhorar capacidade e disponibilidade. Pode ocorrer no cliente, gateway, proxy ou infraestrutura. O algoritmo precisa considerar health e, em alguns casos, afinidade ou pesos. Load balancing não substitui autoscaling nem resiliência.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-stateless-vs-stateful"></a>

## Stateless vs Stateful

> Serviço stateless não depende de estado de sessão local entre requisições, facilitando balanceamento e escala horizontal. Stateful pode ser necessário em alguns domínios, mas exige estratégia de replicação, afinidade ou armazenamento externo. Eu evito estado de sessão em memória local quando a aplicação precisa escalar horizontalmente.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-escalabilidade-vertical-vs-horizontal"></a>

## Escalabilidade Vertical vs Horizontal

> Escala vertical aumenta recursos de uma instância; é simples, mas tem limites físicos e pode manter um ponto único de falha. Escala horizontal adiciona instâncias, aumentando capacidade e resiliência, porém exige que estado, sessão, cache e dados sejam projetados para distribuição. Eu prefiro aplicações stateless quando quero escalar horizontalmente com simplicidade.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-alta-disponibilidade"></a>

## Alta Disponibilidade

> Alta disponibilidade busca manter o serviço operacional apesar de falhas. Normalmente envolve redundância, múltiplas instâncias, health checks, failover, replicação e eliminação de single points of failure. HA não é apenas subir duas instâncias; dependências como banco, fila e DNS também precisam ser consideradas.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-tolerancia-a-falhas"></a>

## Tolerância a Falhas

> Tolerância a falhas significa continuar oferecendo comportamento aceitável mesmo quando componentes falham. Isso pode envolver redundância, retries controlados, circuit breaker, fallback, filas e degradação graciosa. O objetivo é evitar que uma falha local vire falha sistêmica.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-retry-timeout-e-circuit-breaker-na-arquitetura"></a>

## Retry, Timeout e Circuit Breaker na Arquitetura

> Timeout limita quanto tempo um serviço espera; retry tenta recuperar falhas transitórias; circuit breaker evita insistir em uma dependência degradada. Eles são complementares. Retry deve respeitar idempotência, usar backoff e jitter e caber dentro do budget de latência. Configuração errada pode amplificar uma falha em cascata.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-bulkhead"></a>

## Bulkhead

> Bulkhead isola recursos para impedir que uma dependência degradada consuma toda a capacidade da aplicação. Posso aplicar com pools separados, semáforos ou limites de concorrência. O objetivo é conter falha e preservar capacidade para outras funções do sistema.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-degradacao-graciosa"></a>

## Degradação Graciosa

> Degradação graciosa mantém funcionalidades essenciais quando partes não críticas estão indisponíveis. Por exemplo, permitir checkout mesmo se recomendações estiverem fora. O fallback precisa ser explícito e observável; esconder falhas silenciosamente pode gerar dados incorretos ou mascarar incidentes.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-rate-limiting"></a>

## Rate Limiting

> Rate limiting controla quantas operações são aceitas em determinado período. Ele protege capacidade, reduz abuso e evita sobrecarga de dependências. Pode ser por usuário, API key, IP ou tenant. É importante definir também resposta apropriada, normalmente `429`, e política de retry do cliente.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-backpressure"></a>

## Backpressure

> Backpressure evita que produtores gerem trabalho mais rápido do que consumidores ou dependências conseguem processar. Pode ser implementado com filas limitadas, controle de concorrência, rate limiting ou mecanismos reativos. Sem backpressure, o sistema tende a acumular memória, threads ou conexões até falhar.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-cache"></a>

## Cache

> Cache troca consistência e memória por menor latência e menor carga na origem. O principal desafio não é colocar dados no cache, mas invalidá-los corretamente. Eu defino estratégia de TTL, chave, tamanho, eviction e comportamento em miss ou indisponibilidade. Também observo risco de cache stampede.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-cache-aside"></a>

## Cache-Aside

> No padrão cache-aside, a aplicação consulta o cache; em miss busca a origem e popula o cache. É simples e comum, mas pode gerar stampede quando muitas requisições perdem a mesma chave. TTL, locking por chave ou refresh antecipado podem ajudar conforme o cenário.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-cache-stampede"></a>

## Cache Stampede

> Cache stampede ocorre quando uma chave expira e muitas requisições consultam a origem simultaneamente. Isso pode derrubar a dependência. Estratégias incluem TTL com jitter, single-flight/locking por chave, refresh antecipado e stale-while-revalidate.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-observabilidade"></a>

## Observabilidade

> Observabilidade permite entender o comportamento interno do sistema a partir de sinais externos. Eu considero três pilares principais: logs, métricas e traces. Em sistemas distribuídos, correlation ID, trace ID, p95/p99, taxa de erro e saturação são fundamentais para localizar gargalos e falhas entre serviços.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-logs-metricas-e-traces"></a>

## Logs, Métricas e Traces

> Logs ajudam a entender eventos específicos; métricas mostram comportamento agregado ao longo do tempo; traces mostram o caminho de uma requisição entre componentes. Eu uso os três de forma complementar. Um trace pode apontar o serviço lento, uma métrica mostrar frequência e um log explicar o erro daquela execução.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-slo-sli-e-sla"></a>

## SLO, SLI e SLA

> SLI é a métrica observada, como disponibilidade ou latência. SLO é o objetivo interno para essa métrica, por exemplo 99,9% de disponibilidade. SLA é o compromisso formal com consequência contratual. Arquitetura deve ser orientada pelos SLOs porque eles ajudam a justificar redundância, capacidade e investimento operacional.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-p95-p99-e-media"></a>

## P95, P99 e Média

> P95 indica que aproximadamente 95% das observações ficam naquele valor ou abaixo; P99 cobre 99%. Percentis mostram a cauda que a média pode esconder. Não somo percentis de serviços nem faço média de P99s para obter o percentil global.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-e-seguranca"></a>

## Arquitetura e Segurança

> Segurança deve ser tratada como requisito arquitetural. Eu considero autenticação, autorização, least privilege, secrets, criptografia em trânsito e em repouso, trust boundaries, auditoria e proteção de endpoints administrativos. Segurança apenas no gateway ou frontend não é suficiente para todos os cenários.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-versionamento-de-api"></a>

## Versionamento de API

> Versionamento é necessário quando preciso evoluir contrato sem quebrar consumidores. Estratégias incluem URI, header ou negociação de conteúdo. Prefiro mudanças backward compatible quando possível. Em microsserviços, também trato eventos como contratos versionados e evito mudanças destrutivas.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-backward-compatibility"></a>

## Backward Compatibility

> Backward compatibility significa que consumidores antigos continuam funcionando após evolução do produtor. Em APIs e eventos, prefiro adicionar campos opcionais, evitar remover ou renomear campos abruptamente e coordenar mudanças em etapas. Isso reduz necessidade de deploy sincronizado.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-strangler-fig-pattern"></a>

## Strangler Fig Pattern

> Strangler Fig permite substituir um sistema legado gradualmente, desviando funcionalidades para novos componentes enquanto o legado continua operando. É útil para migração incremental porque reduz risco de big bang. O desafio é manter integração e consistência durante o período de coexistência.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-anti-corruption-layer"></a>

## Anti-Corruption Layer

> Anti-Corruption Layer protege um domínio contra modelos externos ou legados. Em vez de espalhar tipos e regras do sistema externo, crio uma camada que traduz contratos para o modelo interno. Isso reduz acoplamento e facilita substituição futura da integração.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-12-factor-app"></a>

## 12-Factor App

> Os princípios 12-Factor são úteis para aplicações cloud-native: configuração externa, processos stateless, logs como fluxo de eventos, dependências explícitas e paridade entre ambientes. Eu não trato como regra absoluta, mas como um conjunto útil de práticas para facilitar deploy e operação.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-cloud-native"></a>

## Arquitetura Cloud-Native

> Cloud-native não significa apenas hospedar na nuvem. Envolve projetar para automação, elasticidade, observabilidade, resiliência, configuração externa e infraestrutura dinâmica. Containers e Kubernetes podem fazer parte, mas a arquitetura precisa lidar corretamente com falhas, estado e operação.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-kubernetes-na-arquitetura"></a>

## Kubernetes na Arquitetura

> Kubernetes resolve problemas de orquestração, como scheduling, service discovery, rollout, health checks e scaling. Ele não resolve regra de negócio, consistência de dados, idempotência ou arquitetura de domínio. Eu o trato como plataforma de execução, não como substituto de decisões arquiteturais.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-arquitetura-evolutiva"></a>

## Arquitetura Evolutiva

> Arquitetura evolutiva aceita que decisões mudam conforme o sistema cresce. Eu procuro decisões reversíveis, módulos bem definidos, métricas e fitness functions que ajudem a detectar degradação arquitetural. O objetivo não é prever tudo, mas manter capacidade de adaptação.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="arquitetura-resposta-completa-de-entrevista-arquitetura"></a>

## Resumo de Entrevista

> Arquitetura organiza responsabilidades e dependências para atender requisitos de negócio e qualidade. Começo por domínio, volume, latência, disponibilidade e custo, depois comparo alternativas. Em distribuição, considero consistência, idempotência, resiliência e observabilidade, registrando as consequências das decisões.

[↑ Sumário do tópico](#arquitetura-arquitetura-de-software-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-microservicos-sumario"></a>

# 20. Microsserviços

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral — Microsserviços](#microservicos-visao-geral-microservicos)
- [Quando usar Microsserviços](#microservicos-quando-usar-microservicos)
- [Quando não usar Microsserviços](#microservicos-quando-nao-usar-microservicos)
- [Monólito Modular vs Microsserviços](#microservicos-monolito-modular-vs-microservicos)
- [Boundaries de Serviço](#microservicos-boundaries-de-servico)
- [Banco por Serviço](#microservicos-banco-por-servico)
- [Shared Database — Trade-off](#microservicos-shared-database-trade-off)
- [Comunicação Síncrona](#microservicos-comunicacao-sincrona)
- [Comunicação Assíncrona](#microservicos-comunicacao-assincrona)
- [REST vs Mensageria](#microservicos-rest-vs-mensageria)
- [Consistência Distribuída](#microservicos-consistencia-distribuida)
- [Saga](#microservicos-saga)
- [Saga — Orquestração vs Coreografia](#microservicos-saga-orquestracao-vs-coreografia)
- [Outbox Pattern](#microservicos-outbox-pattern)
- [Idempotência em Microsserviços](#microservicos-idempotencia-em-microservicos)
- [Inbox e Consumidor Idempotente](#microservicos-microservicos-inbox-e-consumidor-idempotente)
- [CDC vs Polling da Outbox](#microservicos-microservicos-cdc-vs-polling-da-outbox)
- [Timeout](#microservicos-timeout)
- [Budget de Latência e Deadline](#microservicos-microservicos-budget-de-latencia-e-deadline)
- [Resultado Desconhecido Após Timeout](#microservicos-microservicos-resultado-desconhecido-apos-timeout)
- [Retry](#microservicos-retry)
- [Circuit Breaker](#microservicos-circuit-breaker)
- [Estados do Circuit Breaker](#microservicos-microservicos-estados-do-circuit-breaker)
- [Bulkhead](#microservicos-bulkhead)
- [Fallback](#microservicos-fallback)
- [API Gateway](#microservicos-api-gateway)
- [Service Discovery](#microservicos-service-discovery)
- [Load Balancing](#microservicos-load-balancing)
- [Stateless](#microservicos-stateless)
- [Escalabilidade Horizontal](#microservicos-escalabilidade-horizontal)
- [Alta Disponibilidade](#microservicos-alta-disponibilidade)
- [Observabilidade em Microsserviços](#microservicos-observabilidade-em-microservicos)
- [Tracing Distribuído](#microservicos-tracing-distribuido)
- [Trace Context e Propagação](#microservicos-microservicos-trace-context-e-propagacao)
- [P95 e P99](#microservicos-p95-e-p99)
- [SLO e Error Budget](#microservicos-slo-e-error-budget)
- [Rate Limiting](#microservicos-rate-limiting)
- [Backpressure](#microservicos-backpressure)
- [Cache em Microsserviços](#microservicos-cache-em-microservicos)
- [Cache Stampede](#microservicos-cache-stampede)
- [Versionamento de API](#microservicos-versionamento-de-api)
- [Backward Compatibility](#microservicos-backward-compatibility)
- [Deploy Independente](#microservicos-deploy-independente)
- [Falha em Cascata](#microservicos-falha-em-cascata)
- [Degradação Graciosa](#microservicos-degradacao-graciosa)
- [Configuração Distribuída](#microservicos-configuracao-distribuida)
- [Segurança entre Serviços](#microservicos-seguranca-entre-servicos)
- [Resumo de Entrevista](#microservicos-resposta-completa-de-entrevista-microservicos)

---

<a id="microservicos-visao-geral-microservicos"></a>

## Visão Geral — Microsserviços

> Microsserviços dividem uma aplicação em serviços menores, implantáveis de forma independente e normalmente alinhados a capacidades de negócio. O principal ganho é autonomia de deploy, escala e ownership; o principal custo é a complexidade distribuída, como rede, observabilidade, consistência eventual, segurança, versionamento e operação.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-quando-usar-microservicos"></a>

## Quando usar Microsserviços

> Eu usaria microsserviços quando existem fronteiras de domínio claras e uma necessidade concreta de autonomia, como escalabilidade independente, ciclos de deploy diferentes, isolamento de falhas, requisitos de segurança distintos ou times com ownership separado. Não considero microsserviços uma evolução obrigatória de todo monólito.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-quando-nao-usar-microservicos"></a>

## Quando não usar Microsserviços

> Eu evitaria microsserviços quando o domínio ainda não está claro, o time é pequeno, o volume não exige distribuição ou a capacidade operacional é baixa. Nesses casos, um monólito modular normalmente entrega mais velocidade com menos complexidade.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-monolito-modular-vs-microservicos"></a>

## Monólito Modular vs Microsserviços

> Monólito modular mantém um único deploy, mas separa o código por módulos e boundaries claros. Microsserviços levam essa separação para processos e deploys independentes. Eu costumo preferir monólito modular enquanto autonomia operacional e de escala ainda não justificarem a distribuição.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-boundaries-de-servico"></a>

## Boundaries de Serviço

> Eu defino boundaries com base em capacidade de negócio e ownership de dados, não por tabelas ou camadas técnicas. Um bom serviço tende a encapsular seu próprio modelo, regras e persistência, reduzindo necessidade de chamadas excessivas entre serviços.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-banco-por-servico"></a>

## Banco por Serviço

> Em microsserviços, cada serviço deve idealmente possuir ownership dos próprios dados. Compartilhar diretamente o mesmo schema cria acoplamento forte e permite que um serviço dependa da estrutura interna de outro. A integração deve ocorrer por APIs ou eventos.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-shared-database-trade-off"></a>

## Shared Database — Trade-off

> Banco compartilhado simplifica joins e transações no início, mas reduz autonomia e aumenta acoplamento entre serviços. Pode ser aceitável em migração gradual, mas eu trataria como compromisso temporário se o objetivo real é independência entre serviços.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-comunicacao-sincrona"></a>

## Comunicação Síncrona

> Comunicação síncrona é adequada quando o chamador precisa de resposta imediata. O custo é acoplamento temporal: se a dependência está lenta ou indisponível, o chamador sente diretamente. Por isso uso timeout, circuit breaker e observabilidade em chamadas remotas.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-comunicacao-assincrona"></a>

## Comunicação Assíncrona

> Comunicação assíncrona desacopla produtor e consumidor no tempo e ajuda a absorver picos. Em troca, introduz consistência eventual, duplicidade, ordenação, retry, DLQ e necessidade de idempotência. Eu uso quando o fluxo tolera resposta posterior ou quando desacoplamento temporal é importante.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-rest-vs-mensageria"></a>

## REST vs Mensageria

> REST é melhor quando preciso de resposta imediata e um contrato request-response claro. Mensageria é melhor para propagação de eventos, processamento assíncrono e absorção de picos. Em muitos sistemas uso os dois: comando síncrono para iniciar uma operação e evento assíncrono para comunicar consequências.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-consistencia-distribuida"></a>

## Consistência Distribuída

> Em microsserviços eu evito tentar reproduzir uma transação ACID global entre serviços. Normalmente cada serviço mantém sua transação local e a consistência entre serviços é coordenada de forma eventual, usando eventos, Saga, Outbox e operações idempotentes.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-saga"></a>

## Saga

> Saga coordena uma operação de negócio distribuída por meio de várias transações locais. Se uma etapa falha, ações compensatórias podem desfazer efeitos de negócio anteriores. Pode ser implementada por orquestração ou coreografia.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-saga-orquestracao-vs-coreografia"></a>

## Saga — Orquestração vs Coreografia

> Na orquestração existe um coordenador explícito do fluxo, o que facilita entendimento e controle. Na coreografia os serviços reagem a eventos, reduzindo centralização, mas o fluxo pode ficar mais difícil de rastrear. Eu escolho conforme complexidade e necessidade de governança do processo.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-outbox-pattern"></a>

## Outbox Pattern

> Outbox resolve o problema de atualizar o banco e publicar um evento de forma consistente sem transação distribuída. A aplicação grava a mudança de negócio e o registro da outbox na mesma transação local. Depois, outro processo publica a mensagem.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-idempotencia-em-microservicos"></a>

## Idempotência em Microsserviços

> Idempotência garante que uma operação possa ser repetida sem gerar efeitos adicionais indevidos. Ela é importante em retries e mensageria. Eu implemento com chave idempotente, unique constraint, tabela de eventos processados ou lógica naturalmente idempotente.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-microservicos-inbox-e-consumidor-idempotente"></a>

## Inbox e Consumidor Idempotente

> Registro o identificador do evento recebido e a mudança de negócio na mesma transação local, com unicidade para impedir processamento duplicado. Só confirmo o consumo após o commit; efeitos externos exigem sua própria estratégia, como outra outbox ou chave idempotente.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-microservicos-cdc-vs-polling-da-outbox"></a>

## CDC vs Polling da Outbox

> Polling consulta registros pendentes da outbox; CDC acompanha mudanças confirmadas no log do banco. CDC pode reduzir consultas de varredura, mas exige infraestrutura e operação próprias. Em ambos, considero reentrega, ordenação e limpeza dos registros.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-timeout"></a>

## Timeout

> Timeout limita quanto tempo um serviço espera por uma dependência. Sem timeout, threads, conexões e recursos podem ficar presos indefinidamente. O timeout deve respeitar o budget total da requisição e ser coerente com retries.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-microservicos-budget-de-latencia-e-deadline"></a>

## Budget de Latência e Deadline

> Em chamadas sequenciais, somo os orçamentos de cada etapa; em paralelas, considero o caminho mais lento e o custo de coordenação. Reservo margem para processamento local e retries e propago o prazo restante. Não somo P99s como se fossem um P99 total exato.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-microservicos-resultado-desconhecido-apos-timeout"></a>

## Resultado Desconhecido Após Timeout

> Timeout informa que a resposta não chegou no prazo, não que o servidor deixou de executar a operação. Para efeitos como cobrança, consulto o estado ou repito com a mesma chave idempotente antes de iniciar uma nova operação.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-retry"></a>

## Retry

> Retry deve ser usado apenas para falhas transitórias e quando a operação suporta reexecução. Eu limito tentativas, uso backoff e jitter e verifico idempotência. Retry mal configurado pode amplificar uma indisponibilidade.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-circuit-breaker"></a>

## Circuit Breaker

> Circuit Breaker interrompe temporariamente chamadas para uma dependência com alta taxa de falha, evitando desperdício de recursos e falha em cascata. Ele normalmente passa por estados fechado, aberto e semiaberto.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-microservicos-estados-do-circuit-breaker"></a>

## Estados do Circuit Breaker

> Fechado permite chamadas e mede falhas; aberto rejeita rapidamente as chamadas protegidas; semiaberto libera tentativas de avaliação. Se a dependência se recupera, volta a fechar; se a falha persiste, abre novamente. Ele não substitui timeout nem limite de concorrência.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-bulkhead"></a>

## Bulkhead

> Bulkhead isola recursos para impedir que a falha de uma dependência consuma toda a capacidade da aplicação. Pode ser implementado com pools separados, semáforos ou limites de concorrência.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-fallback"></a>

## Fallback

> Fallback fornece uma resposta alternativa quando uma dependência falha. Eu só uso quando existe uma resposta de negócio válida. Fallback silencioso pode mascarar incidentes e retornar informação incorreta.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-api-gateway"></a>

## API Gateway

> API Gateway centraliza preocupações de borda, como roteamento, autenticação, rate limiting, headers e observabilidade. Evito colocar regra de negócio no gateway para não criar um novo monólito central.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-service-discovery"></a>

## Service Discovery

> Service discovery permite localizar instâncias dinamicamente. Em Kubernetes, normalmente uso os mecanismos nativos de Services e DNS antes de adicionar um registry dedicado.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-load-balancing"></a>

## Load Balancing

> Load balancing distribui tráfego entre instâncias disponíveis. Pode existir no cliente, gateway, proxy ou infraestrutura. É complementar a health checks, autoscaling e resiliência.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-stateless"></a>

## Stateless

> Serviços stateless não dependem de estado de sessão local entre requisições, o que facilita scale-out e failover. Estado durável deve ficar em sistemas apropriados, como banco, cache ou storage externo.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-escalabilidade-horizontal"></a>

## Escalabilidade Horizontal

> Escalabilidade horizontal adiciona instâncias para aumentar capacidade. Para funcionar bem, preciso evitar estado local, compartilhar apenas o necessário e garantir que banco, cache e mensageria também suportem o crescimento.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-alta-disponibilidade"></a>

## Alta Disponibilidade

> Alta disponibilidade exige eliminar single points of failure em toda a cadeia: aplicação, banco, broker, rede e infraestrutura. Subir duas instâncias da aplicação não é suficiente se uma dependência crítica continua única.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-observabilidade-em-microservicos"></a>

## Observabilidade em Microsserviços

> Em microsserviços eu considero logs, métricas e traces obrigatórios para investigação. Correlation ID e trace ID ajudam a seguir uma requisição entre serviços, enquanto p95, p99, taxa de erro e saturação mostram comportamento agregado.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-tracing-distribuido"></a>

## Tracing Distribuído

> Tracing distribuído mostra o caminho de uma requisição por múltiplos serviços e o tempo gasto em cada etapa. É especialmente útil para descobrir qual dependência está elevando latência ou causando falha.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-microservicos-trace-context-e-propagacao"></a>

## Trace Context e Propagação

> Um trace relaciona spans de uma operação entre serviços. Propago o contexto por headers ou metadados de mensagens e configuro sua continuidade em tarefas assíncronas; correlation ID sozinho não mede durações nem reconstrói automaticamente toda a árvore de chamadas.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-p95-e-p99"></a>

## P95 e P99

> P95 e P99 indicam os limites abaixo dos quais ficam aproximadamente 95% e 99% das latências observadas. Acompanho-os por operação e dependência, junto com volume e erros; a média de percentis ou a soma dos P99s não produz o P99 de ponta a ponta.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-slo-e-error-budget"></a>

## SLO e Error Budget

> SLO define um objetivo de confiabilidade ou latência. Error budget representa quanto de indisponibilidade ou erro ainda é aceitável dentro desse objetivo. Eu uso isso para equilibrar velocidade de entrega e investimento em confiabilidade.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-rate-limiting"></a>

## Rate Limiting

> Rate limiting protege o serviço e suas dependências contra excesso de requisições. Pode ser aplicado por usuário, token, tenant ou IP. Também preciso definir como clientes devem reagir, normalmente usando `429` e política de retry apropriada.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-backpressure"></a>

## Backpressure

> Backpressure controla o fluxo quando produtores geram trabalho mais rápido que consumidores conseguem processar. Pode ser implementado com filas limitadas, semáforos, rate limiting ou mecanismos reativos. Sem backpressure, o sistema tende a acumular trabalho até esgotar recursos.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-cache-em-microservicos"></a>

## Cache em Microsserviços

> Cache reduz latência e carga na origem, mas introduz risco de dados desatualizados e problemas de invalidação. Eu defino TTL, chave, política de eviction e comportamento em miss. Cache deve ser tratado como otimização, não como fonte de verdade.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-cache-stampede"></a>

## Cache Stampede

> Cache stampede ocorre quando uma chave muito acessada expira e várias requisições vão simultaneamente à origem. Estratégias comuns incluem TTL com jitter, locking por chave, single-flight e refresh antecipado.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-versionamento-de-api"></a>

## Versionamento de API

> Versionamento é necessário quando preciso evoluir contratos sem quebrar consumidores. Prefiro mudanças backward compatible sempre que possível, como adicionar campos opcionais em vez de remover ou renomear campos de forma abrupta.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-backward-compatibility"></a>

## Backward Compatibility

> Backward compatibility permite que consumidores antigos continuem funcionando enquanto o produtor evolui. Isso reduz deploy coordenado entre serviços e é especialmente importante em APIs e eventos assíncronos.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-deploy-independente"></a>

## Deploy Independente

> Deploy independente é um dos principais objetivos de microsserviços, mas só existe de verdade quando contratos, banco, configuração e dependências também estão desacoplados. Se todo deploy exige coordenação entre serviços, a arquitetura ainda está fortemente acoplada.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-falha-em-cascata"></a>

## Falha em Cascata

> Falha em cascata acontece quando a degradação de um serviço consome recursos de serviços dependentes e propaga o problema. Timeout, circuit breaker, bulkhead, rate limiting e degradação graciosa ajudam a limitar esse efeito.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-degradacao-graciosa"></a>

## Degradação Graciosa

> Degradação graciosa mantém funcionalidades essenciais mesmo quando componentes secundários falham. Um exemplo é continuar permitindo checkout se recomendações estiverem indisponíveis. O fallback precisa ser explícito e observável.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-configuracao-distribuida"></a>

## Configuração Distribuída

> Configuração distribuída centraliza propriedades comuns a vários serviços, mas cria uma dependência operacional adicional. Eu avalio disponibilidade, versionamento, segurança de secrets e comportamento de startup quando a fonte de configuração está indisponível.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-seguranca-entre-servicos"></a>

## Segurança entre Serviços

> Segurança entre serviços envolve autenticação, autorização, criptografia em trânsito, least privilege e proteção de secrets. Mesmo em rede interna, eu não assumo confiança irrestrita sem analisar o trust boundary.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="microservicos-resposta-completa-de-entrevista-microservicos"></a>

## Resumo de Entrevista

> Microsserviços oferecem autonomia de deploy e escala, com custos de rede, dados e operação. Defino fronteiras de domínio e contratos claros, combino transações locais com Saga e Outbox e protejo dependências com limites. Só distribuo quando os benefícios justificam essa complexidade.

[↑ Sumário do tópico](#microservicos-microservicos-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-sumario"></a>

# 21. Kafka

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral — Kafka](#kafka-visao-geral-kafka)
- [Broker](#kafka-broker)
- [KRaft e Controller](#kafka-kafka-kraft-e-controller)
- [Topic](#kafka-topic)
- [Partition](#kafka-partition)
- [Offset](#kafka-offset)
- [Partition Key](#kafka-partition-key)
- [Producer](#kafka-producer)
- [Consumer](#kafka-consumer)
- [Consumer Group](#kafka-consumer-group)
- [Mais Partições que Consumidores](#kafka-mais-particoes-que-consumidores)
- [Mais Consumidores que Partições](#kafka-mais-consumidores-que-particoes)
- [Ordenação](#kafka-ordenacao)
- [Leader e Replicas](#kafka-leader-e-replicas)
- [Replication Factor](#kafka-replication-factor)
- [ISR](#kafka-isr)
- [acks=0, 1 e all](#kafka-acks-0-1-e-all)
- [min.insync.replicas](#kafka-min-insync-replicas)
- [At-most-once](#kafka-at-most-once)
- [At-least-once](#kafka-at-least-once)
- [Exactly-once](#kafka-exactly-once)
- [Transações Kafka e read_committed](#kafka-kafka-transacoes-kafka-e-read-committed)
- [Producer Idempotente](#kafka-producer-idempotente)
- [Consumer Idempotente](#kafka-consumer-idempotente)
- [Commit de Offset](#kafka-commit-de-offset)
- [Auto Commit vs Manual Commit](#kafka-auto-commit-vs-manual-commit)
- [Rebalance](#kafka-rebalance)
- [max.poll.interval.ms e Processamento Lento](#kafka-kafka-max-poll-interval-ms-e-processamento-lento)
- [Cooperative Rebalancing](#kafka-cooperative-rebalancing)
- [Consumer Lag](#kafka-consumer-lag)
- [Lag em Produção](#kafka-lag-em-producao)
- [Retention](#kafka-retention)
- [Log Compaction](#kafka-log-compaction)
- [Replay](#kafka-replay)
- [DLQ](#kafka-dlq)
- [Retry com Kafka](#kafka-retry-com-kafka)
- [Retry Topics e Ordenação](#kafka-kafka-retry-topics-e-ordenacao)
- [Poison Pill](#kafka-poison-pill)
- [Schema Evolution](#kafka-schema-evolution)
- [Schema Registry](#kafka-schema-registry)
- [Compatibilidade Backward, Forward e Full](#kafka-kafka-compatibilidade-backward-forward-e-full)
- [Kafka vs RabbitMQ](#kafka-kafka-vs-rabbitmq)
- [Kafka vs SQS](#kafka-kafka-vs-sqs)
- [Kafka vs Banco de Dados](#kafka-kafka-vs-banco-de-dados)
- [Kafka e Outbox](#kafka-kafka-e-outbox)
- [Kafka Connect e Kafka Streams](#kafka-kafka-kafka-connect-e-kafka-streams)
- [Kafka e Spring](#kafka-kafka-e-spring)
- [@KafkaListener](#kafka-kafkalistener)
- [Concorrência no Spring Kafka](#kafka-concorrencia-no-spring-kafka)
- [Partições e Escalabilidade](#kafka-particoes-e-escalabilidade)
- [Hot Partition](#kafka-hot-partition)
- [Batching](#kafka-batching)
- [Backpressure no Consumidor](#kafka-backpressure-no-consumidor)
- [Observabilidade Kafka](#kafka-observabilidade-kafka)
- [Falha de Broker](#kafka-falha-de-broker)
- [Kafka e Ordenação por Entidade](#kafka-kafka-e-ordenacao-por-entidade)
- [Kafka e Transação com Banco](#kafka-kafka-e-transacao-com-banco)
- [Resumo de Entrevista](#kafka-resposta-completa-de-entrevista-kafka)

---

<a id="kafka-visao-geral-kafka"></a>

## Visão Geral — Kafka

> Apache Kafka é uma plataforma distribuída de streaming de eventos baseada em log append-only. Produtores publicam registros em tópicos, tópicos são divididos em partições e consumidores leem os eventos mantendo posição por offset. Kafka é muito forte em throughput, retenção e processamento distribuído de eventos.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-broker"></a>

## Broker

> Broker é um servidor Kafka que armazena partições e atende produtores e consumidores. Um cluster é composto por múltiplos brokers para distribuir carga e fornecer redundância.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-kraft-e-controller"></a>

## KRaft e Controller

> KRaft mantém metadados do cluster usando um quorum de controllers baseado em Raft. Brokers armazenam e servem os registros; controllers coordenam metadados e liderança. Essa distinção ajuda a separar problemas do plano de controle e do fluxo de dados.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-topic"></a>

## Topic

> Topic é uma categoria lógica de eventos. Um tópico é dividido em partições, e cada registro pertence a uma dessas partições. A escolha do número de partições influencia paralelismo, throughput e ordenação.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-partition"></a>

## Partition

> Partition é um log ordenado e append-only dentro de um tópico. A ordem é garantida dentro de uma mesma partição, não globalmente entre todas as partições do tópico.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-offset"></a>

## Offset

> Offset identifica a posição de um registro dentro de uma partição. O consumidor usa offsets para saber até onde processou. O offset não é um identificador global do evento; ele só faz sentido dentro daquela partição.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-partition-key"></a>

## Partition Key

> A partition key influencia em qual partição um evento será gravado. Eventos com a mesma chave tendem a ir para a mesma partição, o que ajuda a preservar ordenação por entidade, como `orderId`. O trade-off é risco de hotspot se poucas chaves concentram muito volume.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-producer"></a>

## Producer

> Producer envia eventos para tópicos e escolhe partição com base na key, estratégia de particionamento ou distribuição padrão. Em produção, considero `acks`, retries, idempotência do producer, batching e compressão.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-consumer"></a>

## Consumer

> Consumer lê registros das partições e controla o progresso através de offsets. O processamento deve considerar falhas, commit de offset, idempotência e tempo máximo de processamento.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-consumer-group"></a>

## Consumer Group

> Consumer group permite paralelizar consumo de um tópico. Dentro do mesmo grupo, uma partição é atribuída a no máximo um consumidor ativo por vez. Grupos diferentes recebem o mesmo fluxo de eventos de forma independente.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-mais-particoes-que-consumidores"></a>

## Mais Partições que Consumidores

> Se existem mais partições do que consumidores no grupo, alguns consumidores processam várias partições. Isso aumenta o paralelismo potencial até o número de consumidores atingir o número de partições.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-mais-consumidores-que-particoes"></a>

## Mais Consumidores que Partições

> Se há mais consumidores que partições no mesmo grupo, consumidores excedentes ficam ociosos porque uma partição não é processada simultaneamente por dois consumidores do mesmo grupo.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-ordenacao"></a>

## Ordenação

> Kafka garante ordenação apenas dentro de uma partição. Se preciso manter ordem por cliente ou pedido, uso uma key estável para que eventos relacionados caiam na mesma partição. Isso reduz paralelismo para aquela chave, mas preserva sequência.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-leader-e-replicas"></a>

## Leader e Replicas

> Cada partição possui um leader e pode possuir replicas. Produtores e consumidores trabalham principalmente com o leader. As replicas mantêm cópias para permitir failover quando o leader fica indisponível.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-replication-factor"></a>

## Replication Factor

> Replication factor define quantas cópias de cada partição existem no cluster. Um fator maior aumenta tolerância a falhas, mas também aumenta uso de disco e tráfego de replicação.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-isr"></a>

## ISR

> ISR, In-Sync Replicas, representa replicas suficientemente sincronizadas com o leader. Esse conjunto é importante para decisões de durabilidade e failover.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-acks-0-1-e-all"></a>

## acks=0, 1 e all

> `acks=0` não espera confirmação; `acks=1` espera o leader; `acks=all` espera as réplicas atualmente no ISR. `min.insync.replicas` estabelece o mínimo de réplicas sincronizadas para aceitar a escrita com `acks=all`; não é o número fixo de confirmações quando o ISR é maior. [Referência](https://kafka.apache.org/41/design/design/)

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-min-insync-replicas"></a>

## min.insync.replicas

> `min.insync.replicas` define o número mínimo de replicas em sincronia necessário para aceitar gravações quando o producer usa `acks=all`. Essa combinação ajuda a evitar confirmar escrita com redundância insuficiente.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-at-most-once"></a>

## At-most-once

> At-most-once prioriza evitar duplicidade, mas pode perder mensagens. Isso ocorre, por exemplo, se o offset é confirmado antes de o processamento ser concluído e a aplicação falha depois.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-at-least-once"></a>

## At-least-once

> At-least-once prioriza não perder mensagens, mas permite duplicidade. É comum confirmar o offset depois do processamento. Se a aplicação processa e falha antes do commit, o evento pode ser reentregue. Por isso idempotência é importante.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-exactly-once"></a>

## Exactly-once

> Exactly-once em Kafka depende do escopo. Kafka oferece mecanismos como producer idempotente e transações para fluxos Kafka-to-Kafka. Isso não significa automaticamente exactly-once end-to-end quando existem efeitos externos, como banco de dados ou chamadas HTTP.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-transacoes-kafka-e-read-committed"></a>

## Transações Kafka e read_committed

> Uma transação Kafka pode agrupar publicações e offsets no próprio Kafka. `read_committed` evita entregar registros de transações abortadas ou ainda abertas como resultados confirmados; isso não inclui automaticamente o commit de um banco externo. [Referência](https://kafka.apache.org/41/design/design/)

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-producer-idempotente"></a>

## Producer Idempotente

> Producer idempotente evita duplicatas causadas por retries do próprio producer dentro das garantias do Kafka. Ele não resolve sozinho duplicidade de negócio causada por reprocessamento no consumidor.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-consumer-idempotente"></a>

## Consumer Idempotente

> Consumer idempotente garante que reentrega do mesmo evento não produza efeito duplicado. Estratégias comuns são chave idempotente, unique constraint, tabela de eventos processados ou operação naturalmente idempotente.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-commit-de-offset"></a>

## Commit de Offset

> O offset confirmado indica de onde o grupo deve retomar; normalmente registro o próximo offset a consumir. Confirmar antes de concluir pode perder processamento; confirmar depois pode gerar reentrega. Com execução paralela, não avanço além de registros anteriores ainda incompletos.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-auto-commit-vs-manual-commit"></a>

## Auto Commit vs Manual Commit

> Auto commit simplifica configuração, mas reduz controle sobre o ponto exato em que o progresso é confirmado. Manual commit permite alinhar offset ao processamento, porém exige mais cuidado com erros e rebalances.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-rebalance"></a>

## Rebalance

> Rebalance redistribui partições entre consumidores de um grupo quando consumidores entram, saem ou quando metadados mudam. Durante esse processo pode haver pausa no consumo. Rebalances frequentes prejudicam estabilidade e throughput.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-max-poll-interval-ms-e-processamento-lento"></a>

## max.poll.interval.ms e Processamento Lento

> O intervalo máximo entre chamadas de poll limita quanto tempo um consumidor pode ficar sem avançar o ciclo esperado. Processamento demorado pode provocar perda da atribuição; ajusto tamanho dos lotes e execução sem confirmar offsets antes de concluir o trabalho.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-cooperative-rebalancing"></a>

## Cooperative Rebalancing

> Cooperative rebalancing reduz impacto ao mover partições de forma mais incremental, evitando parar todo o grupo quando possível. É útil para reduzir pausas em grupos maiores.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-consumer-lag"></a>

## Consumer Lag

> Consumer lag expressa a distância entre a posição de referência do log e a posição do consumidor, conforme a métrica. Lag por offset confirmado pode incluir trabalho já processado sem commit; comparo tendência, idade dos eventos, throughput e erros para interpretar o atraso.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-lag-em-producao"></a>

## Lag em Produção

> Eu monitoro lag por partição e grupo, junto com throughput, tempo de processamento, erros e rebalances. Lag isolado não basta: preciso entender se está crescendo continuamente ou apenas absorvendo um pico temporário.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-retention"></a>

## Retention

> Kafka retém eventos por tempo ou tamanho, independentemente de terem sido consumidos. Isso diferencia Kafka de filas tradicionais e permite replay. O trade-off é uso de armazenamento.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-log-compaction"></a>

## Log Compaction

> Log compaction remove versões antigas de uma chave de forma assíncrona, preservando seu estado mais recente conforme a política. Duplicatas por chave podem coexistir antes da limpeza; registros com valor nulo, chamados tombstones, representam exclusão e também têm retenção configurada.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-replay"></a>

## Replay

> Replay significa reposicionar o consumidor em offsets anteriores e reprocessar eventos. Isso é útil para reconstrução de estado e correção de consumidores, mas exige processamento idempotente e cuidado com efeitos externos.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-dlq"></a>

## DLQ

> DLQ armazena mensagens que não puderam ser processadas após a política de retry. Ela evita bloquear indefinidamente o consumo, mas exige estratégia de inspeção, correção e reprocessamento. DLQ não deve virar depósito permanente de erros ignorados.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-retry-com-kafka"></a>

## Retry com Kafka

> Retry pode ser imediato ou com atraso através de tópicos de retry, dependendo da arquitetura. Eu evito loop agressivo no mesmo evento porque isso bloqueia a partição e reduz throughput. Também considero idempotência e limite de tentativas.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-retry-topics-e-ordenacao"></a>

## Retry Topics e Ordenação

> Mover uma mensagem com falha para outro tópico pode permitir que mensagens seguintes avancem, quebrando a ordem de negócio por chave. Defino se o fluxo deve parar, tolerar reordenação ou usar controle de versão antes de escolher a estratégia de retry.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-poison-pill"></a>

## Poison Pill

> Poison pill é uma mensagem que falha repetidamente, por exemplo por payload inválido. Sem tratamento, ela pode bloquear o progresso da partição. Estratégias incluem DLQ, tratamento específico de desserialização e limites de retry.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-schema-evolution"></a>

## Schema Evolution

> Eventos são contratos. Para evoluir schema, prefiro mudanças backward compatible, como adicionar campos opcionais. Formatos como Avro, Protobuf ou JSON Schema podem ser usados com registry para governar compatibilidade.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-schema-registry"></a>

## Schema Registry

> Schema Registry centraliza schemas e regras de compatibilidade. Ele ajuda produtores e consumidores a evoluírem sem quebrar contratos, mas adiciona mais um componente operacional.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-compatibilidade-backward-forward-e-full"></a>

## Compatibilidade Backward, Forward e Full

> Backward permite ao leitor novo ler dados antigos; forward permite ao leitor antigo ler dados novos; full exige as duas direções. As regras dependem do formato e da configuração do registry, e compatibilidade estrutural não garante semântica de negócio preservada.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-vs-rabbitmq"></a>

## Kafka vs RabbitMQ

> Kafka é orientado a log distribuído, retenção e alto throughput de eventos, com replay natural. RabbitMQ é tradicionalmente orientado a filas e roteamento de mensagens. A escolha depende do padrão: streaming e histórico favorecem Kafka; filas de trabalho e roteamento complexo podem favorecer RabbitMQ.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-vs-sqs"></a>

## Kafka vs SQS

> Kafka oferece log particionado, replay e controle detalhado de consumo. SQS é uma fila gerenciada com modelo operacional mais simples. Eu escolho Kafka quando preciso de streaming, múltiplos consumidores independentes, replay e alto throughput; SQS quando quero desacoplamento por fila com menor custo operacional.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-vs-banco-de-dados"></a>

## Kafka vs Banco de Dados

> Kafka não substitui banco transacional. Ele é excelente como log de eventos e backbone de integração, enquanto banco relacional continua melhor para consultas transacionais e consistência de dados de negócio. Os dois frequentemente coexistem.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-e-outbox"></a>

## Kafka e Outbox

> Outbox é uma estratégia comum para publicar eventos no Kafka de forma consistente com uma transação de banco. A aplicação grava negócio e outbox na mesma transação e depois um publicador envia o evento ao Kafka.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-kafka-connect-e-kafka-streams"></a>

## Kafka Connect e Kafka Streams

> Kafka Connect integra fontes e destinos por conectores. Kafka Streams é uma biblioteca para processar fluxos em aplicações, com operações e estado particionado. Ambos reduzem trabalho de infraestrutura, mas exigem entender contratos, falhas e garantias de processamento.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-e-spring"></a>

## Kafka e Spring

> No ecossistema Spring, `spring-kafka` fornece producer, consumer, listeners, configuração de serializers, error handling e integração com transactions. Mesmo usando abstrações do Spring, eu continuo entendendo partitions, offsets, rebalances e delivery semantics.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafkalistener"></a>

## @KafkaListener

> `@KafkaListener` simplifica criação de consumers no Spring. Eu ainda configuro group id, concorrência, ack mode, error handler e desserialização de acordo com a necessidade do serviço.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-concorrencia-no-spring-kafka"></a>

## Concorrência no Spring Kafka

> A concorrência do listener permite múltiplos consumers dentro da mesma instância. O paralelismo efetivo continua limitado pelo número de partições. Aumentar threads acima do número de partições não aumenta throughput do grupo.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-particoes-e-escalabilidade"></a>

## Partições e Escalabilidade

> Número de partições define o teto de paralelismo de consumo dentro de um consumer group. Mais partições aumentam paralelismo potencial, mas também aumentam metadados, arquivos, custo de rebalance e complexidade operacional.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-hot-partition"></a>

## Hot Partition

> Hot partition ocorre quando uma ou poucas partições recebem volume muito maior, normalmente por distribuição desigual de keys. Isso limita throughput mesmo com muitos consumidores. Eu reviso estratégia de chave e distribuição de carga.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-batching"></a>

## Batching

> Producer batching agrupa registros para melhorar throughput e reduzir overhead de rede. O trade-off é introduzir pequena latência adicional enquanto o batch é formado. Compressão também pode reduzir rede ao custo de CPU.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-backpressure-no-consumidor"></a>

## Backpressure no Consumidor

> Se o processamento downstream é mais lento que o consumo, o lag cresce. Eu controlo concorrência, pausas de partição, capacidade de dependências e throughput de processamento em vez de simplesmente aumentar consumers sem analisar o gargalo.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-observabilidade-kafka"></a>

## Observabilidade Kafka

> Em produção, monitoro lag, throughput, taxa de erro, rebalances, tempo de processamento, utilização de disco, estado de replicas e latência de produce/fetch. Para consumidores, lag por partição é uma das métricas mais importantes.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-falha-de-broker"></a>

## Falha de Broker

> Quando um broker falha, partições com replicas adequadas podem eleger novos leaders e continuar operando. A disponibilidade depende de replication factor, ISR e configurações de durabilidade.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-e-ordenacao-por-entidade"></a>

## Kafka e Ordenação por Entidade

> Uma chave estável ajuda a manter eventos da entidade na mesma partição, preservando a ordem do log. A aplicação deve manter essa ordem no processamento; execução paralela, tópicos de retry e mudanças no particionamento podem alterar a sequência observada.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-kafka-e-transacao-com-banco"></a>

## Kafka e Transação com Banco

> Não existe uma transação ACID simples envolvendo Kafka e banco relacional de forma genérica. Para consistência entre os dois, eu normalmente uso Outbox ou desenho idempotente, em vez de depender de transação distribuída.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="kafka-resposta-completa-de-entrevista-kafka"></a>

## Resumo de Entrevista

> Kafka organiza eventos em tópicos particionados com retenção e offsets. Projeto chaves, replicação e consumo considerando ordenação, paralelismo e durabilidade. Controlo commits, duplicidade, retries e lag; garantias transacionais do Kafka não tornam efeitos em bancos e APIs automaticamente atômicos.

[↑ Sumário do tópico](#kafka-kafka-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-sumario"></a>

# 22. Spring Cloud

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#spring-spring-cloud-importante)
- [Configuração Distribuída](#spring-spring-cloud-configuracao-distribuida)
- [Service Discovery](#spring-spring-cloud-service-discovery)
- [Spring Cloud Gateway](#spring-spring-cloud-cuidado)
- [OpenFeign](#spring-spring-cloud-openfeign)
- [Resiliência: Timeout, Retry, Circuit Breaker e Bulkhead](#spring-spring-cloud-4-bulkhead)
- [Spring Cloud Stream](#spring-spring-cloud-trade-off)
- [Observabilidade Distribuída](#spring-spring-cloud-observabilidade-distribuida)
- [Resumo de Entrevista](#spring-spring-cloud-resposta-completa-de-entrevista)

---

<a id="spring-spring-cloud-importante"></a>

## Visão Geral

> Spring Cloud complementa Spring Boot com padrões e integrações para sistemas distribuídos, como configuração centralizada, discovery, gateway, load balancing, clients declarativos, resiliência e mensageria.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-configuracao-distribuida"></a>

## Configuração Distribuída

> Config Server centraliza propriedades de múltiplos serviços. É útil para consistência e versionamento, mas passa a ser parte crítica da infraestrutura e precisa de alta disponibilidade e segurança.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-service-discovery"></a>

## Service Discovery

> Service discovery permite localizar instâncias dinamicamente. Em Kubernetes, avalio primeiro os mecanismos nativos antes de adicionar registry dedicado.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-cuidado"></a>

## Spring Cloud Gateway

> Gateway centraliza preocupações de borda, como roteamento, filtros e políticas transversais. Evito colocar regra de negócio nele.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-openfeign"></a>

## OpenFeign

> OpenFeign simplifica o client HTTP, mas não remove propriedades de rede. Continuo configurando timeout, tratamento de erro, resiliência e observabilidade.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-4-bulkhead"></a>

## Resiliência: Timeout, Retry, Circuit Breaker e Bulkhead

> Em chamadas remotas combino timeout, circuit breaker e, quando faz sentido, retry com backoff e jitter. Retry precisa respeitar idempotência. Bulkhead impede que uma dependência degradada consuma todos os recursos locais.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-trade-off"></a>

## Spring Cloud Stream

> Spring Cloud Stream facilita integração com mensageria, mas não elimina a necessidade de conhecer o broker. Com Kafka, ainda preciso entender partições, consumer groups, offsets e semântica de entrega.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-observabilidade-distribuida"></a>

## Observabilidade Distribuída

> Em sistemas distribuídos, métricas isoladas não bastam. Correlaciono logs, métricas e traces para localizar onde latência ou erro foi introduzido na cadeia.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="spring-spring-cloud-resposta-completa-de-entrevista"></a>

## Resumo de Entrevista

> Spring Cloud integra configuração, descoberta, gateway, comunicação e resiliência para sistemas distribuídos. Essas abstrações não eliminam falhas de rede nem garantem consistência. Evito duplicar capacidades já oferecidas pela plataforma e acompanho o comportamento da cadeia com observabilidade.

[↑ Sumário do tópico](#spring-spring-cloud-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-sumario"></a>

# 23. Docker e Kubernetes

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral](#containers-kubernetes-visao-geral)
- [Imagem vs Container vs Máquina Virtual](#containers-kubernetes-imagem-vs-container-vs-maquina-virtual)
- [Dockerfile, Layers e Build Context](#containers-kubernetes-dockerfile-layers-e-build-context)
- [Multi-stage Build e Runtime Java](#containers-kubernetes-multi-stage-build-e-runtime-java)
- [Tags e Digests](#containers-kubernetes-tags-e-digests)
- [Volumes e Persistência](#containers-kubernetes-volumes-e-persistencia)
- [Docker Compose](#containers-kubernetes-docker-compose)
- [Cluster, Node e Control Plane](#containers-kubernetes-cluster-node-e-control-plane)
- [Pod](#containers-kubernetes-pod)
- [Deployment e ReplicaSet](#containers-kubernetes-deployment-e-replicaset)
- [Service, DNS e Entrada de Tráfego](#containers-kubernetes-service-dns-e-entrada-de-trafego)
- [ConfigMap e Secret](#containers-kubernetes-configmap-e-secret)
- [Requests, Limits e OOMKilled](#containers-kubernetes-requests-limits-e-oomkilled)
- [Heap Java Dentro do Container](#containers-kubernetes-heap-java-dentro-do-container)
- [Startup, Readiness e Liveness Probes](#containers-kubernetes-startup-readiness-e-liveness-probes)
- [Graceful Shutdown](#containers-kubernetes-graceful-shutdown)
- [HPA e Autoscaling](#containers-kubernetes-hpa-e-autoscaling)
- [StatefulSet, Job e CronJob](#containers-kubernetes-statefulset-job-e-cronjob)
- [RBAC e NetworkPolicy](#containers-kubernetes-rbac-e-networkpolicy)
- [Diagnóstico de Pods](#containers-kubernetes-diagnostico-de-pods)
- [Resumo de Entrevista](#containers-kubernetes-resumo-de-entrevista)

---

<a id="containers-kubernetes-visao-geral"></a>

## Visão Geral

> Containers empacotam aplicação e dependências em um ambiente de execução isolado. Kubernetes coordena esses workloads declarativamente; nenhum dos dois elimina a necessidade de tratar persistência, encerramento, capacidade e falhas na aplicação.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-imagem-vs-container-vs-maquina-virtual"></a>

## Imagem vs Container vs Máquina Virtual

> Imagem é o pacote de camadas e configuração; container é sua execução isolada, compartilhando o kernel do host. Máquina virtual normalmente possui seu próprio sistema operacional convidado, com outra relação entre isolamento e custo.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-dockerfile-layers-e-build-context"></a>

## Dockerfile, Layers e Build Context

> Dockerfile descreve como construir uma imagem. A ordem das etapas influencia reaproveitamento de cache; mantenho o contexto pequeno e não copio segredos para camadas, pois removê-los depois pode deixá-los no histórico da imagem.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-multi-stage-build-e-runtime-java"></a>

## Multi-stage Build e Runtime Java

> Separo compilação e execução para levar à imagem final apenas o necessário. Uso runtime compatível com o JAR e executo com usuário sem privilégios quando possível, reduzindo tamanho e permissões do processo.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-tags-e-digests"></a>

## Tags e Digests

> Tags podem apontar para imagens diferentes ao longo do tempo. Digest identifica o conteúdo da imagem e permite fixar exatamente o artefato executado; associá-lo ao commit facilita auditoria e rollback.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-volumes-e-persistencia"></a>

## Volumes e Persistência

> Dados gravados apenas na camada gravável do container podem desaparecer com sua substituição. Uso volumes ou serviços externos para estado durável, com estratégia explícita de backup e recuperação.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-docker-compose"></a>

## Docker Compose

> Compose descreve vários serviços, redes e volumes para executar ambientes com múltiplos containers. É útil no desenvolvimento e em cenários compatíveis, mas não fornece sozinho as mesmas capacidades de um orquestrador distribuído.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-cluster-node-e-control-plane"></a>

## Cluster, Node e Control Plane

> O cluster reúne o plano de controle e os nós que executam workloads. Kubernetes compara estado desejado e observado para reconciliar recursos; disponibilidade exige também planejar falhas de nós, zonas e dependências.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-pod"></a>

## Pod

> Pod é a menor unidade de implantação do Kubernetes e pode conter containers que compartilham rede e volumes. Pods são substituíveis; seu endereço e armazenamento temporário não devem ser tratados como identidade durável da aplicação.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-deployment-e-replicaset"></a>

## Deployment e ReplicaSet

> Deployment declara versões e quantidade desejada de réplicas e gerencia rollout por ReplicaSets. Ter réplicas não garante alta disponibilidade se todas dependem do mesmo ponto de falha ou não estão distribuídas adequadamente.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-service-dns-e-entrada-de-trafego"></a>

## Service, DNS e Entrada de Tráfego

> Service oferece um destino estável para alcançar Pods selecionados e pode ser descoberto por DNS. A entrada externa depende de recursos e controladores apropriados, como LoadBalancer, Ingress ou Gateway API.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-configmap-e-secret"></a>

## ConfigMap e Secret

> ConfigMap armazena configuração não sensível; Secret é destinado a dados sensíveis. Codificação base64 não é criptografia: controlo RBAC, proteção em repouso e acesso dos workloads, sem colocar credenciais em imagens ou repositórios.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-requests-limits-e-oomkilled"></a>

## Requests, Limits e OOMKilled

> Requests orientam o agendamento; limits restringem uso de recursos. CPU pode sofrer throttling, enquanto ultrapassar memória pode encerrar o container; `OOMKilled` pelo sistema não é a mesma coisa que uma exceção `OutOfMemoryError` lançada pela JVM.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-heap-java-dentro-do-container"></a>

## Heap Java Dentro do Container

> O consumo total inclui heap, Metaspace, stacks, buffers diretos e outras áreas nativas. Deixo margem além de `-Xmx` e observo o limite do container; aumentar a heap sem essa análise pode provocar encerramento por falta de memória.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-startup-readiness-e-liveness-probes"></a>

## Startup, Readiness e Liveness Probes

> Startup probe permite completar a inicialização antes das demais verificações. Readiness controla se o Pod recebe tráfego; liveness pode reiniciar o container. Uma dependência temporariamente indisponível não deve, por padrão, provocar reinícios em massa por liveness. [Referência](https://kubernetes.io/docs/concepts/workloads/pods/probes/)

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-graceful-shutdown"></a>

## Graceful Shutdown

> No encerramento, a aplicação deve parar de receber novos trabalhos, concluir ou interromper com segurança os atuais e liberar recursos. Alinho o prazo de término do Kubernetes com HTTP, consumidores e tarefas em execução para reduzir perdas durante rollouts.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-hpa-e-autoscaling"></a>

## HPA e Autoscaling

> HPA ajusta réplicas com base em métricas configuradas. Escalar Pods não aumenta automaticamente conexões de banco ou capacidade do broker; escolho sinais úteis, requests adequados e limites coerentes com as dependências.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-statefulset-job-e-cronjob"></a>

## StatefulSet, Job e CronJob

> StatefulSet atende workloads que precisam de identidade estável e associação com volumes. Job executa trabalho até sua conclusão; CronJob agenda execuções. Mesmo com agendamento, desenho tarefas idempotentes para tolerar falhas e reexecuções.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-rbac-e-networkpolicy"></a>

## RBAC e NetworkPolicy

> RBAC limita ações sobre a API do Kubernetes. NetworkPolicy restringe tráfego entre workloads quando o plugin de rede oferece suporte; uso ambos com permissões mínimas e separação de responsabilidades.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-diagnostico-de-pods"></a>

## Diagnóstico de Pods

> Começo por eventos, estado e motivo de término, depois logs, probes, recursos e conectividade. `CrashLoopBackOff` indica reinícios repetidos; não explica sozinho a causa, que pode estar na aplicação, configuração, permissões ou dependências.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="containers-kubernetes-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Empacoto Java em imagens reproduzíveis e configuro recursos, probes e encerramento coerentes. Em Kubernetes, separo execução, rede, configuração e persistência, investigando falhas por evidências antes de aumentar réplicas ou memória.

[↑ Sumário do tópico](#containers-kubernetes-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-aws-sumario"></a>

# 24. AWS

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral — AWS](#aws-visao-geral-aws)
- [Região e Availability Zone](#aws-regiao-e-availability-zone)
- [Alta Disponibilidade Multi-AZ](#aws-alta-disponibilidade-multi-az)
- [Multi-Region](#aws-multi-region)
- [Shared Responsibility Model](#aws-shared-responsibility-model)
- [IAM](#aws-iam)
- [IAM User vs Role](#aws-iam-user-vs-role)
- [IAM Policy](#aws-iam-policy)
- [Identity-based vs Resource-based Policy](#aws-identity-based-vs-resource-based-policy)
- [STS e AssumeRole](#aws-sts-e-assumerole)
- [VPC](#aws-vpc)
- [Subnet Pública vs Privada](#aws-subnet-publica-vs-privada)
- [Internet Gateway](#aws-internet-gateway)
- [NAT Gateway](#aws-nat-gateway)
- [Security Group vs NACL](#aws-security-group-vs-nacl)
- [Route Table](#aws-route-table)
- [VPC Endpoint](#aws-vpc-endpoint)
- [EC2](#aws-ec2)
- [Auto Scaling Group](#aws-auto-scaling-group)
- [ALB vs NLB](#aws-alb-vs-nlb)
- [EBS](#aws-ebs)
- [EFS](#aws-efs)
- [S3](#aws-s3)
- [Consistência do S3](#aws-aws-consistencia-do-s3)
- [S3 Versioning](#aws-s3-versioning)
- [S3 Lifecycle](#aws-s3-lifecycle)
- [S3 Presigned URL](#aws-s3-presigned-url)
- [CloudFront](#aws-cloudfront)
- [Route 53](#aws-route-53)
- [RDS](#aws-rds)
- [RDS Multi-AZ: Instância vs Cluster](#aws-rds-multi-az)
- [RDS Read Replica](#aws-rds-read-replica)
- [Aurora](#aws-aurora)
- [DynamoDB](#aws-dynamodb)
- [DynamoDB Partition Key](#aws-dynamodb-partition-key)
- [DynamoDB Sort Key](#aws-dynamodb-sort-key)
- [DynamoDB GSI e LSI](#aws-dynamodb-gsi-e-lsi)
- [DynamoDB Consistency](#aws-dynamodb-consistency)
- [DynamoDB On-Demand vs Provisioned](#aws-dynamodb-on-demand-vs-provisioned)
- [ElastiCache](#aws-elasticache)
- [Lambda](#aws-lambda)
- [Lambda Cold Start](#aws-lambda-cold-start)
- [Lambda SnapStart](#aws-aws-lambda-snapstart)
- [Lambda Concurrency](#aws-lambda-concurrency)
- [Lambda Idempotência](#aws-lambda-idempotencia)
- [ECS](#aws-ecs)
- [ECS Service vs Task](#aws-ecs-service-vs-task)
- [Fargate](#aws-fargate)
- [EKS](#aws-eks)
- [ECS vs EKS](#aws-ecs-vs-eks)
- [ECS vs Lambda](#aws-ecs-vs-lambda)
- [API Gateway](#aws-api-gateway)
- [API Gateway vs ALB](#aws-api-gateway-vs-alb)
- [SQS](#aws-sqs)
- [SQS Standard vs FIFO](#aws-sqs-standard-vs-fifo)
- [SQS Visibility Timeout](#aws-sqs-visibility-timeout)
- [SQS DLQ](#aws-sqs-dlq)
- [SQS Long Polling](#aws-sqs-long-polling)
- [SNS](#aws-sns)
- [SNS + SQS](#aws-sns-sqs)
- [EventBridge](#aws-eventbridge)
- [SNS vs EventBridge](#aws-sns-vs-eventbridge)
- [SQS vs Kafka](#aws-sqs-vs-kafka)
- [Amazon MSK](#aws-aws-amazon-msk)
- [CloudWatch](#aws-cloudwatch)
- [CloudTrail](#aws-cloudtrail)
- [KMS](#aws-kms)
- [Secrets Manager vs Parameter Store](#aws-secrets-manager-vs-parameter-store)
- [WAF](#aws-waf)
- [CloudFormation](#aws-cloudformation)
- [AWS CDK](#aws-aws-cdk)
- [Infrastructure as Code](#aws-infrastructure-as-code)
- [RTO e RPO](#aws-rto-e-rpo)
- [Disaster Recovery](#aws-disaster-recovery)
- [Well-Architected Framework](#aws-well-architected-framework)
- [Cost Optimization](#aws-cost-optimization)
- [NAT Gateway Cost](#aws-nat-gateway-cost)
- [Resumo de Entrevista](#aws-resposta-completa-de-entrevista-aws)

---

<a id="aws-visao-geral-aws"></a>

## Visão Geral — AWS

> Em entrevistas eu organizo AWS em cinco blocos: compute, networking, storage/database, integração assíncrona e segurança/observabilidade. O ponto principal não é decorar serviços, mas saber escolher entre alternativas considerando disponibilidade, escalabilidade, segurança, custo e operação.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-regiao-e-availability-zone"></a>

## Região e Availability Zone

> Uma Region é uma área geográfica da AWS composta por múltiplas Availability Zones. AZs são locais isolados dentro da mesma região, com conectividade de baixa latência entre si. Para alta disponibilidade regional, normalmente distribuo recursos por mais de uma AZ.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-alta-disponibilidade-multi-az"></a>

## Alta Disponibilidade Multi-AZ

> Multi-AZ reduz impacto da falha de uma zona. Em aplicações web, isso normalmente significa múltiplas instâncias distribuídas entre AZs, balanceador e dependências também redundantes. Não adianta colocar a aplicação em duas AZs se banco ou outro componente crítico continua como ponto único de falha.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-multi-region"></a>

## Multi-Region

> Multi-Region é usado quando requisitos de disponibilidade, disaster recovery, latência global ou residência de dados justificam operar em mais de uma região. O custo e a complexidade aumentam porque preciso tratar replicação, roteamento, consistência e failover entre regiões.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-shared-responsibility-model"></a>

## Shared Responsibility Model

> No modelo de responsabilidade compartilhada, a AWS é responsável pela segurança da nuvem, como datacenters, hardware e infraestrutura física; o cliente é responsável pela segurança na nuvem, como IAM, configuração de rede, dados, patches conforme o serviço e controles da aplicação.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-iam"></a>

## IAM

> IAM controla autenticação e autorização dentro da AWS. Eu trabalho com users, roles e policies, sempre seguindo least privilege: conceder apenas as permissões necessárias, pelo menor tempo e escopo possíveis.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-iam-user-vs-role"></a>

## IAM User vs Role

> IAM User representa uma identidade persistente. IAM Role é uma identidade assumível, normalmente temporária, usada por aplicações, serviços AWS ou usuários federados. Para workloads em EC2, ECS ou Lambda, prefiro roles em vez de access keys estáticas.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-iam-policy"></a>

## IAM Policy

> IAM Policy define permissões com elementos como `Effect`, `Action`, `Resource` e condições. A autorização efetiva resulta da combinação de policies aplicáveis, lembrando que um `Deny` explícito prevalece sobre `Allow`.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-identity-based-vs-resource-based-policy"></a>

## Identity-based vs Resource-based Policy

> Identity-based policy é anexada a user, group ou role. Resource-based policy é anexada ao próprio recurso, como bucket S3 ou fila SQS. Resource policies são especialmente úteis em acesso cross-account.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sts-e-assumerole"></a>

## STS e AssumeRole

> AWS STS emite credenciais temporárias. `AssumeRole` permite que uma identidade assuma uma role e receba permissões temporárias. Isso é importante para acesso cross-account e para evitar distribuição de credenciais permanentes.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-vpc"></a>

## VPC

> VPC é a rede virtual isolada da AWS. Dentro dela defino CIDR, subnets, route tables, gateways e security groups. Eu normalmente separo recursos públicos e privados conforme a necessidade de exposição.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-subnet-publica-vs-privada"></a>

## Subnet Pública vs Privada

> Uma subnet é pública quando sua tabela de rotas oferece caminho direto para um Internet Gateway. Para uma instância IPv4 acessar a internet por esse caminho, também precisa de endereço público e regras compatíveis. Subnets privadas não têm essa rota direta.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-internet-gateway"></a>

## Internet Gateway

> Internet Gateway conecta uma VPC à internet. Para uma instância ter acesso público, além do IGW é necessário roteamento adequado, endereço público ou equivalente e regras de segurança compatíveis.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-nat-gateway"></a>

## NAT Gateway

> NAT Gateway permite que recursos em subnets privadas iniciem conexões para a internet sem aceitar conexões de entrada iniciadas externamente. É comum para updates ou chamadas externas, mas possui custo relevante, principalmente por volume processado.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-security-group-vs-nacl"></a>

## Security Group vs NACL

> Security Group é stateful e atua no nível da interface de rede ou recurso. NACL é stateless e atua no nível da subnet. Em geral, Security Groups são o principal mecanismo de controle entre workloads; NACLs adicionam uma camada de controle mais ampla.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-route-table"></a>

## Route Table

> Route Table define para onde o tráfego de uma subnet deve ser encaminhado. Exemplos são rota local da VPC, Internet Gateway, NAT Gateway, Transit Gateway ou Peering. Problemas de conectividade frequentemente começam pela análise das rotas.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-vpc-endpoint"></a>

## VPC Endpoint

> VPC Endpoint permite acessar determinados serviços AWS sem passar pela internet pública. Isso melhora a postura de segurança e pode reduzir dependência de NAT para alguns acessos. Existem endpoints do tipo Gateway e Interface, dependendo do serviço.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-ec2"></a>

## EC2

> EC2 fornece máquinas virtuais sob demanda. Em entrevista, eu considero tipo de instância, AMI, storage, networking, autoscaling e estratégia de substituição. Para aplicações stateless, prefiro tratar instâncias como descartáveis e automatizar bootstrap e deploy.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-auto-scaling-group"></a>

## Auto Scaling Group

> Auto Scaling Group mantém uma quantidade desejada de instâncias e pode ajustar capacidade conforme métricas. Ele também substitui instâncias não saudáveis. Normalmente combino ASG com Load Balancer e health checks.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-alb-vs-nlb"></a>

## ALB vs NLB

> Application Load Balancer opera em camada 7 e entende HTTP/HTTPS, permitindo routing por host e path. Network Load Balancer opera em camada 4 e é indicado para TCP/UDP/TLS, alta performance e cenários que exigem características de rede mais baixas.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-ebs"></a>

## EBS

> EBS é block storage normalmente associado a EC2. É adequado para discos de sistema, banco autogerenciado e workloads que precisam de storage em bloco. Persistência e performance dependem do tipo de volume e da configuração.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-efs"></a>

## EFS

> EFS é um filesystem gerenciado acessível por múltiplas instâncias, útil quando várias máquinas precisam compartilhar arquivos. O modelo e a performance são diferentes de EBS, que é block storage.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-s3"></a>

## S3

> S3 é object storage altamente durável e escalável. Eu uso para arquivos, backups, artefatos, data lakes e conteúdo estático. Não é filesystem tradicional nem banco relacional.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-aws-consistencia-do-s3"></a>

## Consistência do S3

> S3 oferece consistência forte para operações de leitura e listagem após escritas bem-sucedidas nos escopos documentados. Isso não torna várias alterações de objetos uma transação única nem elimina caches externos, como uma CDN, que podem servir conteúdo anterior. [Referência](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-s3-versioning"></a>

## S3 Versioning

> Versioning mantém múltiplas versões de um objeto e ajuda na recuperação de exclusões ou sobrescritas acidentais. O trade-off é maior consumo de storage, então normalmente combino com lifecycle policies.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-s3-lifecycle"></a>

## S3 Lifecycle

> Lifecycle permite mover objetos para classes de storage mais econômicas ou expirá-los automaticamente conforme idade. É importante para reduzir custo em dados que envelhecem.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-s3-presigned-url"></a>

## S3 Presigned URL

> Presigned URL fornece acesso temporário a um objeto S3 sem tornar o bucket público. É útil para upload ou download direto pelo cliente, reduzindo tráfego pela aplicação.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-cloudfront"></a>

## CloudFront

> CloudFront é a CDN da AWS. Ele distribui conteúdo a partir de edge locations, reduzindo latência e carga na origem. Pode usar S3, ALB ou outras origens e integrar com controles como WAF.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-route-53"></a>

## Route 53

> Route 53 é o serviço DNS da AWS. Além de resolução DNS, oferece políticas de roteamento como weighted, latency-based e failover. É útil em arquiteturas multi-region e estratégias de recuperação.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-rds"></a>

## RDS

> RDS é o serviço gerenciado para bancos relacionais. A AWS gerencia provisioning, backups e parte da operação conforme configuração. Eu ainda preciso cuidar de schema, índices, queries, capacidade e arquitetura de acesso.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-rds-multi-az"></a>

## RDS Multi-AZ: Instância vs Cluster

> Em uma implantação Multi-AZ de instância, o standby serve ao failover e não às leituras. Em um Multi-AZ DB cluster, há instâncias que também atendem leitura. Confirmo a modalidade antes de afirmar se Multi-AZ aumenta a capacidade de consulta. [Referência](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.html)

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-rds-read-replica"></a>

## RDS Read Replica

> Read Replica é usada para escalar leitura e pode ter replicação assíncrona. Como pode existir lag, eu não assumo leitura imediatamente consistente após uma escrita. Multi-AZ e Read Replica resolvem problemas diferentes.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-aurora"></a>

## Aurora

> Aurora é um banco relacional compatível com MySQL e PostgreSQL, com storage distribuído gerenciado pela AWS. Pode oferecer alta disponibilidade e escala de leitura, mas continua exigindo bom design de schema, índices e queries.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-dynamodb"></a>

## DynamoDB

> DynamoDB é um banco NoSQL key-value/document totalmente gerenciado, projetado para baixa latência e escala horizontal. O design começa pelo padrão de acesso, porque partition key, sort key e índices determinam como os dados serão distribuídos e consultados.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-dynamodb-partition-key"></a>

## DynamoDB Partition Key

> Partition key influencia como os itens são distribuídos internamente. Uma chave com distribuição ruim pode criar hot partitions e limitar throughput. Por isso escolho uma chave com boa cardinalidade e distribuição.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-dynamodb-sort-key"></a>

## DynamoDB Sort Key

> Sort key permite armazenar múltiplos itens sob a mesma partition key e fazer consultas ordenadas ou por intervalo dentro dessa partição. É útil para séries temporais, eventos ou entidades relacionadas.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-dynamodb-gsi-e-lsi"></a>

## DynamoDB GSI e LSI

> GSI cria uma nova chave de acesso com partition/sort key alternativas e é mais flexível. LSI compartilha a mesma partition key da tabela e define outra sort key, com restrições maiores. Eu escolho de acordo com os padrões de acesso necessários.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-dynamodb-consistency"></a>

## DynamoDB Consistency

> DynamoDB suporta leituras eventualmente consistentes por padrão e, em contextos suportados, leituras fortemente consistentes. Strong consistency custa mais capacidade, então escolho conforme a necessidade do caso de uso.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-dynamodb-on-demand-vs-provisioned"></a>

## DynamoDB On-Demand vs Provisioned

> On-Demand simplifica workloads imprevisíveis porque a capacidade é ajustada pelo serviço. Provisioned permite definir capacidade e pode ser mais econômico em carga previsível. A decisão depende de perfil de tráfego e custo.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-elasticache"></a>

## ElastiCache

> ElastiCache oferece mecanismos gerenciados compatíveis com Valkey, Redis OSS e Memcached. Uso para cache e outros acessos de baixa latência conforme o mecanismo, definindo TTL, invalidação, capacidade e comportamento em falhas. [Referência](https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/SelectEngine.html)

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-lambda"></a>

## Lambda

> Lambda executa funções sob demanda sem gerenciar servidores. É adequada para processamento orientado a eventos, automações e APIs de carga compatível. O trade-off envolve cold start, limites de execução, concorrência e observabilidade.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-lambda-cold-start"></a>

## Lambda Cold Start

> Cold start é o tempo adicional para inicializar um novo ambiente de execução. Em Java pode ser mais perceptível dependendo do framework e do tamanho da aplicação. Eu reduzo dependências e trabalho no startup e avalio provisioned concurrency quando o requisito justificar.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-aws-lambda-snapstart"></a>

## Lambda SnapStart

> SnapStart reutiliza um snapshot de inicialização em runtimes e configurações compatíveis, reduzindo parte do custo de cold start. Valido conexões, credenciais e dados que precisam ser únicos após a restauração, além das restrições do serviço para o runtime escolhido. [Referência](https://docs.aws.amazon.com/lambda/latest/dg/snapstart.html)

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-lambda-concurrency"></a>

## Lambda Concurrency

> Concurrency representa quantas execuções podem ocorrer simultaneamente. É importante para capacidade e também para proteger dependências downstream. Aumentar concorrência do Lambda não aumenta automaticamente a capacidade do banco ou API chamada.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-lambda-idempotencia"></a>

## Lambda Idempotência

> Como eventos podem ser reentregues ou retries acontecerem, funções Lambda devem ser idempotentes quando produzem efeitos externos. Uso chave idempotente, conditional writes ou registro de processamento conforme o cenário.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-ecs"></a>

## ECS

> ECS é o serviço de orquestração de containers da AWS. Permite executar tasks e services e integrar com Load Balancer, autoscaling e IAM. É mais simples operacionalmente que Kubernetes para muitos workloads AWS-native.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-ecs-service-vs-task"></a>

## ECS Service vs Task

> Task é uma execução de uma task definition. Service mantém uma quantidade desejada de tasks em execução e integra health check, deployment e autoscaling.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-fargate"></a>

## Fargate

> Fargate permite executar containers em ECS ou EKS sem gerenciar diretamente instâncias EC2. Simplifica operação e isolamento de capacidade, com trade-off de custo e menor controle sobre a infraestrutura subjacente.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-eks"></a>

## EKS

> EKS fornece Kubernetes com plano de controle gerenciado pela AWS. A responsabilidade pelos nós varia conforme o modo de operação; continuo responsável por workloads, permissões, configuração e dados, mesmo quando parte maior da infraestrutura é automatizada.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-ecs-vs-eks"></a>

## ECS vs EKS

> ECS é mais simples e integrado ao ecossistema AWS. EKS oferece o ecossistema e a portabilidade do Kubernetes, mas aumenta complexidade operacional. Eu escolheria EKS quando existe necessidade real de Kubernetes; caso contrário, ECS pode ser suficiente.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-ecs-vs-lambda"></a>

## ECS vs Lambda

> ECS é mais apropriado para serviços long-running e workloads containerizados com maior controle do runtime. Lambda é adequada para execução orientada a eventos e curta duração. A escolha depende do modelo de execução, duração, dependências e operação.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-api-gateway"></a>

## API Gateway

> API Gateway fornece gerenciamento de APIs com roteamento, autenticação, throttling e integração com Lambda ou backends HTTP. É especialmente comum em arquiteturas serverless. Para serviços containerizados simples, um ALB pode ser mais direto.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-api-gateway-vs-alb"></a>

## API Gateway vs ALB

> API Gateway oferece API management, throttling, authorization e integração serverless. ALB é um load balancer HTTP de camada 7 e costuma ser mais simples para rotear tráfego a ECS ou EC2. A escolha depende das funcionalidades necessárias.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sqs"></a>

## SQS

> SQS é uma fila gerenciada para desacoplamento assíncrono. Produtores enviam mensagens e consumidores processam independentemente. É útil para absorver picos, retry e reduzir acoplamento temporal entre serviços.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sqs-standard-vs-fifo"></a>

## SQS Standard vs FIFO

> SQS Standard oferece entrega pelo menos uma vez e ordenação best-effort. FIFO ordena por grupo de mensagens e oferece deduplicação dentro de suas condições, mas o consumidor ainda deve tolerar reprocessamento, por exemplo após falha antes da exclusão da mensagem.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sqs-visibility-timeout"></a>

## SQS Visibility Timeout

> Visibility Timeout esconde temporariamente uma mensagem após ela ser recebida. Se o consumidor não a remover antes do timeout, ela pode ficar disponível novamente. O valor precisa considerar o tempo esperado de processamento.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sqs-dlq"></a>

## SQS DLQ

> Dead Letter Queue recebe mensagens que falharam repetidamente após o limite configurado. Ela evita retry infinito e facilita inspeção, mas precisa de processo de análise e redrive.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sqs-long-polling"></a>

## SQS Long Polling

> Long polling espera por mensagens durante um intervalo em vez de retornar imediatamente vazio. Isso reduz requests desnecessários e custo quando a fila tem baixo volume.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sns"></a>

## SNS

> SNS é um serviço pub/sub. Um publisher envia uma mensagem para um tópico e múltiplos subscribers podem recebê-la. É adequado para fan-out.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sns-sqs"></a>

## SNS + SQS

> Combinar SNS com SQS permite fan-out com isolamento por consumidor. Cada serviço recebe sua própria fila, podendo processar em ritmo diferente e aplicar retry e DLQ independentemente.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-eventbridge"></a>

## EventBridge

> EventBridge é um event bus gerenciado com regras de roteamento baseadas no conteúdo do evento e integração ampla com serviços AWS e SaaS. É útil quando o roteamento é mais rico que um simples fan-out.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sns-vs-eventbridge"></a>

## SNS vs EventBridge

> SNS é excelente para pub/sub e fan-out simples. EventBridge oferece regras de roteamento, event buses e integração ampla. Eu escolho conforme necessidade de filtragem, roteamento e governança.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-sqs-vs-kafka"></a>

## SQS vs Kafka

> SQS é uma fila gerenciada simples, ótima para desacoplamento com baixa operação. Kafka oferece log particionado, replay, consumer groups e streaming de alto throughput. Escolho Kafka quando preciso de histórico, replay e múltiplos consumidores independentes; SQS quando uma fila de trabalho é suficiente.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-aws-amazon-msk"></a>

## Amazon MSK

> Amazon MSK oferece Apache Kafka gerenciado, reduzindo parte da operação do cluster. A aplicação ainda precisa definir partições, retenção, segurança, chaves, commits e idempotência; serviço gerenciado não elimina o design do fluxo de eventos.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-cloudwatch"></a>

## CloudWatch

> CloudWatch centraliza métricas, logs, alarms e dashboards de serviços AWS. Em produção, monitoro latência, taxa de erro, saturação, throttling, filas, CPU, memória quando disponível e métricas de negócio.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-cloudtrail"></a>

## CloudTrail

> CloudTrail registra eventos de API e atividades na conta AWS. É fundamental para auditoria, investigação de mudanças e segurança. Não substitui logs de aplicação.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-kms"></a>

## KMS

> KMS gerencia chaves criptográficas e integra com diversos serviços AWS. Eu uso para criptografia de dados e secrets, controlando quem pode usar cada chave através de IAM e key policies.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-secrets-manager-vs-parameter-store"></a>

## Secrets Manager vs Parameter Store

> Secrets Manager é focado em secrets e oferece recursos como rotação integrada em vários cenários. Parameter Store é útil para configuração e também pode armazenar valores seguros. Eu escolho considerando rotação, integração e custo.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-waf"></a>

## WAF

> AWS WAF aplica regras de camada 7 para proteger aplicações web contra padrões maliciosos, bots e ataques comuns. Pode ser associado a serviços como CloudFront e ALB. Ele complementa, mas não substitui segurança da aplicação.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-cloudformation"></a>

## CloudFormation

> CloudFormation permite declarar infraestrutura como código usando templates. Isso melhora repetibilidade, versionamento e automação. O trade-off é lidar com ciclo de vida e dependências dos stacks.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-aws-cdk"></a>

## AWS CDK

> AWS CDK permite definir infraestrutura usando linguagens de programação e sintetiza templates de CloudFormation. Ele melhora abstração e reutilização, mas continua baseado no modelo de recursos e deployment do CloudFormation.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-infrastructure-as-code"></a>

## Infrastructure as Code

> IaC torna infraestrutura versionável, reproduzível e auditável. Eu evito alterações manuais em produção porque geram drift. Seja com Terraform, CloudFormation ou CDK, o objetivo é manter infraestrutura dentro do processo de entrega.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-rto-e-rpo"></a>

## RTO e RPO

> RTO é quanto tempo o sistema pode ficar indisponível até recuperação. RPO é quanto de dados pode ser perdido em termos de tempo. Esses requisitos determinam estratégia de backup, replicação, failover e custo.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-disaster-recovery"></a>

## Disaster Recovery

> Estratégias de DR variam de backup/restore até warm standby e active-active. Quanto menores RTO e RPO, maior o custo e a complexidade. Eu começo pelos requisitos de negócio antes de escolher a estratégia.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-well-architected-framework"></a>

## Well-Architected Framework

> O AWS Well-Architected Framework organiza boas práticas em pilares como excelência operacional, segurança, confiabilidade, eficiência de performance, otimização de custos e sustentabilidade. Eu o uso como estrutura para justificar decisões, não como checklist decorado.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-cost-optimization"></a>

## Cost Optimization

> Otimização de custo envolve right sizing, autoscaling, desligar recursos ociosos, storage adequado, compromissos de uso quando a carga é previsível e atenção a data transfer. A arquitetura mais barata é a que atende requisitos sem excesso.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-nat-gateway-cost"></a>

## NAT Gateway Cost

> NAT Gateway possui custo por hora e por volume processado. Em arquiteturas com muito acesso a serviços AWS, VPC Endpoints podem reduzir tráfego pelo NAT e melhorar segurança, dependendo do padrão de uso.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="aws-resposta-completa-de-entrevista-aws"></a>

## Resumo de Entrevista

> Escolho serviços AWS pelos requisitos de computação, rede, dados e integração. Uso IAM e credenciais temporárias, considero redundância e recuperação e automatizo infraestrutura. Serviços gerenciados reduzem trabalho operacional, mas continuam exigindo atenção a segurança, capacidade, consistência e custo.

[↑ Sumário do tópico](#aws-aws-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-cicd-sumario"></a>

# 25. CI/CD

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)

- [Visão Geral — CI/CD](#cicd-visao-geral-ci-cd)
- [Continuous Integration](#cicd-continuous-integration)
- [Continuous Delivery vs Continuous Deployment](#cicd-continuous-delivery-vs-continuous-deployment)
- [Pipeline CI/CD](#cicd-pipeline-ci-cd)
- [Fail Fast](#cicd-fail-fast)
- [Maven em CI/CD](#cicd-maven-em-ci-cd)
- [Gradle em CI/CD](#cicd-gradle-em-ci-cd)
- [Maven Wrapper e Gradle Wrapper](#cicd-maven-wrapper-e-gradle-wrapper)
- [Build Reprodutível](#cicd-build-reprodutivel)
- [Artefato Imutável](#cicd-artefato-imutavel)
- [Nexus e Artifactory](#cicd-nexus-e-artifactory)
- [Versionamento de Artefato](#cicd-versionamento-de-artefato)
- [Snapshot vs Release](#cicd-snapshot-vs-release)
- [Git Strategy](#cicd-git-strategy)
- [Trunk-Based Development](#cicd-trunk-based-development)
- [Pull Request e Code Review](#cicd-pull-request-e-code-review)
- [Branch Protection](#cicd-branch-protection)
- [Testes Unitários na Pipeline](#cicd-testes-unitarios-na-pipeline)
- [Testes de Integração](#cicd-testes-de-integracao)
- [Testcontainers](#cicd-testcontainers)
- [Contract Testing](#cicd-contract-testing)
- [End-to-End Tests](#cicd-end-to-end-tests)
- [Test Pyramid](#cicd-test-pyramid)
- [Flaky Tests](#cicd-flaky-tests)
- [Quality Gate](#cicd-quality-gate)
- [SonarQube](#cicd-sonarqube)
- [Cobertura de Testes](#cicd-cobertura-de-testes)
- [SAST](#cicd-sast)
- [SCA](#cicd-sca)
- [DAST](#cicd-dast)
- [SBOM](#cicd-sbom)
- [Supply Chain Security](#cicd-supply-chain-security)
- [Secrets no Pipeline](#cicd-secrets-no-pipeline)
- [Docker Build em CI](#cicd-docker-build-em-ci)
- [Multi-stage Docker Build](#cicd-multi-stage-docker-build)
- [Imagem Docker Imutável](#cicd-imagem-docker-imutavel)
- [Image Scanning](#cicd-image-scanning)
- [Registry](#cicd-registry)
- [Environment Promotion](#cicd-environment-promotion)
- [Configuração por Ambiente](#cicd-configuracao-por-ambiente)
- [Feature Flag](#cicd-feature-flag)
- [Rolling Deployment](#cicd-rolling-deployment)
- [Blue-Green Deployment](#cicd-blue-green-deployment)
- [Canary Deployment](#cicd-canary-deployment)
- [Rolling vs Blue-Green vs Canary](#cicd-rolling-vs-blue-green-vs-canary)
- [Rollback](#cicd-rollback)
- [Roll Forward](#cicd-roll-forward)
- [Database Migration](#cicd-database-migration)
- [Flyway e Liquibase](#cicd-flyway-e-liquibase)
- [Zero-Downtime Database Migration](#cicd-zero-downtime-database-migration)
- [Expand and Contract](#cicd-expand-and-contract)
- [Backward Compatibility no Deploy](#cicd-backward-compatibility-no-deploy)
- [Health Check Pós-Deploy](#cicd-health-check-pos-deploy)
- [Smoke Test](#cicd-smoke-test)
- [Observabilidade Pós-Deploy](#cicd-observabilidade-pos-deploy)
- [Automated Rollback](#cicd-automated-rollback)
- [Approval Gate](#cicd-approval-gate)
- [Pipeline as Code](#cicd-pipeline-as-code)
- [Jenkins](#cicd-jenkins)
- [GitHub Actions](#cicd-github-actions)
- [GitLab CI](#cicd-gitlab-ci)
- [Runner Self-hosted vs Hosted](#cicd-runner-self-hosted-vs-hosted)
- [Cache na Pipeline](#cicd-cache-na-pipeline)
- [Paralelismo na Pipeline](#cicd-paralelismo-na-pipeline)
- [Artifacts entre Jobs](#cicd-artifacts-entre-jobs)
- [Idempotência do Deploy](#cicd-idempotencia-do-deploy)
- [Infraestrutura como Código no CI/CD](#cicd-infraestrutura-como-codigo-no-ci-cd)
- [GitOps](#cicd-gitops)
- [CI/CD em Kubernetes](#cicd-ci-cd-em-kubernetes)
- [CI/CD em ECS](#cicd-ci-cd-em-ecs)
- [Java e Container Memory](#cicd-java-e-container-memory)
- [Graceful Shutdown](#cicd-graceful-shutdown)
- [Readiness e Liveness](#cicd-readiness-e-liveness)
- [Deploy de Java com Migração de Banco](#cicd-deploy-de-java-com-migracao-de-banco)
- [Dependency Management](#cicd-dependency-management)
- [Pipeline para Microsserviços](#cicd-pipeline-para-microservicos)
- [Pipeline Monorepo](#cicd-pipeline-monorepo)
- [Pipeline Failure — Como Investigar](#cicd-pipeline-failure-como-investigar)
- [Lead Time](#cicd-lead-time)
- [Deployment Frequency](#cicd-deployment-frequency)
- [Change Failure Rate](#cicd-change-failure-rate)
- [MTTR e Failed Deployment Recovery Time](#cicd-mttr)
- [Deployment Rework Rate](#cicd-cicd-deployment-rework-rate)
- [DORA Metrics](#cicd-dora-metrics)
- [CI/CD e Segurança](#cicd-ci-cd-e-seguranca)
- [CI/CD e Compliance](#cicd-ci-cd-e-compliance)
- [Resumo de Entrevista](#cicd-resposta-completa-de-entrevista-ci-cd)

---

<a id="cicd-visao-geral-ci-cd"></a>

## Visão Geral — CI/CD

> CI/CD automatiza integração, validação, empacotamento e entrega de software. Para Java, eu penso no fluxo completo: checkout, build Maven ou Gradle, testes, análise estática, geração do artefato, criação da imagem, publicação em registry e deployment. O objetivo é reduzir risco, feedback time e trabalho manual, mantendo releases reproduzíveis e auditáveis.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-continuous-integration"></a>

## Continuous Integration

> Continuous Integration significa integrar mudanças pequenas e frequentes em uma branch compartilhada, executando build e validações automaticamente. O foco é detectar regressões cedo. Para Java, normalmente incluo compilação, testes unitários, análise estática e, quando necessário, testes de integração.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-continuous-delivery-vs-continuous-deployment"></a>

## Continuous Delivery vs Continuous Deployment

> Continuous Delivery mantém o software sempre em estado implantável, mas o deploy em produção pode depender de aprovação. Continuous Deployment leva isso adiante e promove automaticamente para produção quando todos os gates passam. A diferença principal é o último passo de promoção.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-pipeline-ci-cd"></a>

## Pipeline CI/CD

> Uma pipeline típica para Java tem estágios como checkout, build, testes unitários, análise estática, testes de integração, empacotamento, publicação do artefato ou imagem, deployment e validação pós-deploy. Eu separo stages para falhar cedo e economizar tempo e recursos.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-fail-fast"></a>

## Fail Fast

> Fail fast significa executar primeiro as validações mais rápidas e baratas. Por exemplo, compilar e rodar testes unitários antes de testes de integração ou deploy. Isso reduz feedback time e custo de pipeline.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-maven-em-ci-cd"></a>

## Maven em CI/CD

> Em projetos Maven, eu costumo usar comandos reprodutíveis como `mvn clean verify` para compilar, testar e executar verificações configuradas. Também uso Maven Wrapper quando disponível para garantir a versão esperada da ferramenta no ambiente de CI.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-gradle-em-ci-cd"></a>

## Gradle em CI/CD

> Com Gradle, prefiro usar o Gradle Wrapper, como `./gradlew build`, para garantir versão consistente entre desenvolvedor e CI. Também observo cache, paralelismo e tasks de teste separadas quando a pipeline cresce.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-maven-wrapper-e-gradle-wrapper"></a>

## Maven Wrapper e Gradle Wrapper

> Wrappers evitam depender da versão instalada globalmente no runner. Isso melhora reprodutibilidade porque a própria aplicação define qual versão do Maven ou Gradle deve ser utilizada.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-build-reprodutivel"></a>

## Build Reprodutível

> Um build reproduzível deve produzir o mesmo resultado a partir da mesma revisão e configuração. Para isso, fixo versões, uso wrappers, evito dependências dinâmicas, controlo plugins e não dependo de estado local do runner.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-artefato-imutavel"></a>

## Artefato Imutável

> Eu prefiro construir o artefato uma única vez e promover o mesmo binário ou imagem entre ambientes. Rebuildar em cada ambiente pode gerar diferenças de dependência ou configuração e quebra a garantia de que o que foi testado é exatamente o que chegou à produção.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-nexus-e-artifactory"></a>

## Nexus e Artifactory

> Nexus e Artifactory são repositórios de artefatos. Em Java, armazenam JARs, BOMs e outros pacotes, além de poderem atuar como proxy de dependências externas. Eles ajudam em versionamento, disponibilidade, auditoria e controle da supply chain.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-versionamento-de-artefato"></a>

## Versionamento de Artefato

> Eu evito sobrescrever versões publicadas. Normalmente uso versão única por build, associada a commit ou release. Isso facilita rollback, rastreabilidade e reprodução de incidentes.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-snapshot-vs-release"></a>

## Snapshot vs Release

> Em Maven, `SNAPSHOT` representa uma versão em desenvolvimento que pode mudar ao longo do tempo, enquanto uma release deve ser imutável. Para produção, uso versões imutáveis e rastreáveis.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-git-strategy"></a>

## Git Strategy

> A estratégia de Git deve reduzir branches longas e integração tardia. Trunk-based development favorece branches curtas e integração frequente. Git Flow pode funcionar em alguns contextos, mas adiciona mais branches e coordenação. Eu escolho de acordo com frequência de release e maturidade do time.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-trunk-based-development"></a>

## Trunk-Based Development

> Trunk-based development incentiva branches pequenas e curtas, integradas rapidamente ao trunk. Isso reduz divergência, conflitos e tempo até integração. Feature flags ajudam quando funcionalidades ainda não estão prontas para exposição.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-pull-request-e-code-review"></a>

## Pull Request e Code Review

> Pull Request serve como gate técnico e de colaboração. Eu combino review com validações automáticas para evitar depender apenas de revisão humana. Branch protection deve impedir merge quando build, testes ou checks obrigatórios falham.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-branch-protection"></a>

## Branch Protection

> Branch protection normalmente exige review, checks verdes e bloqueia push direto em branches críticas. Isso reduz mudanças não auditadas e garante que a política de qualidade seja aplicada de forma consistente.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-testes-unitarios-na-pipeline"></a>

## Testes Unitários na Pipeline

> Testes unitários devem rodar cedo porque são rápidos e dão feedback imediato. Eles validam comportamento isolado e ajudam a impedir regressões antes de etapas mais caras.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-testes-de-integracao"></a>

## Testes de Integração

> Testes de integração validam interação entre componentes reais, como banco, fila ou serviços. São mais caros e lentos, então normalmente ficam em estágio posterior ao unit test, mas antes de promover o artefato.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-testcontainers"></a>

## Testcontainers

> Testcontainers permite subir dependências reais, como PostgreSQL, Kafka ou Redis, durante testes de integração. Isso reduz diferenças entre mock e ambiente real, embora aumente tempo e consumo de recursos da pipeline.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-contract-testing"></a>

## Contract Testing

> Contract tests verificam compatibilidade entre produtor e consumidor sem exigir um ambiente end-to-end completo. São úteis em microsserviços para detectar breaking changes cedo e reduzir dependência de testes integrados frágeis.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-end-to-end-tests"></a>

## End-to-End Tests

> Testes end-to-end validam o fluxo completo, mas são lentos e mais suscetíveis a flakiness. Eu mantenho poucos E2E focados em jornadas críticas e deixo a maior parte da cobertura em testes unitários, integração e contrato.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-test-pyramid"></a>

## Test Pyramid

> A test pyramid prioriza muitos testes unitários, uma quantidade menor de integração e poucos end-to-end. O objetivo é equilibrar velocidade, confiança e custo de manutenção.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-flaky-tests"></a>

## Flaky Tests

> Teste flaky falha de forma não determinística e reduz confiança na pipeline. Eu trato flakiness como defeito: investigo concorrência, timing, dependência externa ou estado compartilhado em vez de simplesmente adicionar retry indefinido.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-quality-gate"></a>

## Quality Gate

> Quality gate define condições mínimas para promoção, como testes aprovados, análise estática aceitável e ausência de vulnerabilidades críticas. O gate deve bloquear problemas reais, não virar uma coleção de métricas arbitrárias.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-sonarqube"></a>

## SonarQube

> SonarQube analisa qualidade e segurança do código, como bugs potenciais, code smells e vulnerabilidades. Eu uso como sinal automatizado dentro da pipeline, mas não substituo revisão técnica nem testes.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-cobertura-de-testes"></a>

## Cobertura de Testes

> Cobertura mede código executado pelos testes, não qualidade dos testes. Eu uso coverage para identificar áreas sem validação, mas evito metas cegas que incentivem testes sem valor.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-sast"></a>

## SAST

> SAST analisa código-fonte ou bytecode procurando vulnerabilidades sem executar a aplicação. É útil cedo na pipeline para detectar padrões inseguros.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-sca"></a>

## SCA

> Software Composition Analysis identifica vulnerabilidades conhecidas em dependências e componentes de terceiros. Em Java é especialmente relevante por causa do grande ecossistema Maven e Gradle.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-dast"></a>

## DAST

> DAST testa a aplicação em execução, simulando ataques ou comportamentos maliciosos. Normalmente roda em ambiente implantado e complementa SAST e SCA.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-sbom"></a>

## SBOM

> SBOM é uma lista estruturada dos componentes e dependências que formam o software. Ajuda a responder rapidamente se uma aplicação usa uma biblioteca vulnerável e melhora rastreabilidade da supply chain.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-supply-chain-security"></a>

## Supply Chain Security

> Segurança da supply chain inclui dependências, plugins, imagens, runners, registry e processo de build. Eu evito binários não verificados, mantenho dependências controladas, aplico scanning e protejo credenciais do pipeline.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-secrets-no-pipeline"></a>

## Secrets no Pipeline

> Secrets nunca devem ficar hardcoded no repositório ou nos logs. Uso secret stores da plataforma de CI/CD ou serviços como Secrets Manager e Vault e limito acesso por ambiente e job.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-docker-build-em-ci"></a>

## Docker Build em CI

> Na pipeline, construo a imagem Docker depois que código e testes passam. A imagem deve ser versionada de forma imutável, por exemplo com tag de commit, e publicada em registry.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-multi-stage-docker-build"></a>

## Multi-stage Docker Build

> Multi-stage build permite separar a etapa de compilação da imagem final. Em Java, posso compilar com Maven ou Gradle em uma etapa e copiar apenas o JAR e runtime necessário para a imagem final, reduzindo tamanho e superfície de ataque.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-imagem-docker-imutavel"></a>

## Imagem Docker Imutável

> Eu não reutilizo a mesma tag mutável para representar builds diferentes em produção. Prefiro tags imutáveis por commit ou versão e, quando possível, referência por digest.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-image-scanning"></a>

## Image Scanning

> Image scanning identifica vulnerabilidades conhecidas no sistema operacional e nas dependências da imagem. Eu incluo esse check antes da promoção e também reavalio imagens já publicadas porque novas CVEs surgem depois do build.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-registry"></a>

## Registry

> Registry armazena imagens versionadas, como ECR ou outro registry. Além do armazenamento, considero políticas de retenção, scanning, permissões e imutabilidade de tags.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-environment-promotion"></a>

## Environment Promotion

> Eu promovo o mesmo artefato entre dev, staging e produção, mudando apenas configuração externa. Isso mantém rastreabilidade e reduz diferenças entre ambientes.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-configuracao-por-ambiente"></a>

## Configuração por Ambiente

> Configuração específica de ambiente deve ser externa ao artefato, por exemplo variáveis, ConfigMap, Secrets ou serviço de configuração. O código e a imagem devem permanecer iguais.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-feature-flag"></a>

## Feature Flag

> Feature flag desacopla deploy de release. Posso colocar código em produção desabilitado e ativá-lo gradualmente. O cuidado é evitar flags permanentes e combinações demais, que aumentam complexidade.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-rolling-deployment"></a>

## Rolling Deployment

> Rolling deployment substitui instâncias gradualmente. Tem baixo custo de infraestrutura e é simples, mas versões antiga e nova coexistem durante o rollout, então compatibilidade entre versões é importante.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-blue-green-deployment"></a>

## Blue-Green Deployment

> Blue-Green mantém dois ambientes: o atual e o novo. Após validação, o tráfego é trocado para o novo ambiente. O rollback é rápido, mas exige capacidade duplicada durante a transição.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-canary-deployment"></a>

## Canary Deployment

> Canary envia uma pequena parcela do tráfego para a nova versão e aumenta gradualmente se as métricas estiverem saudáveis. Reduz blast radius, mas exige bom roteamento e observabilidade.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-rolling-vs-blue-green-vs-canary"></a>

## Rolling vs Blue-Green vs Canary

> Rolling é simples e econômico, mas mistura versões. Blue-Green facilita rollback rápido com custo de capacidade duplicada. Canary reduz risco expondo progressivamente, porém exige automação e métricas maduras. Eu escolho conforme criticidade, custo e capacidade operacional.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-rollback"></a>

## Rollback

> Rollback retorna rapidamente para uma versão conhecida. Para funcionar, o artefato anterior deve estar disponível e mudanças de banco precisam ser compatíveis. Nem todo incidente permite rollback simples, principalmente quando dados já foram transformados.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-roll-forward"></a>

## Roll Forward

> Roll forward corrige o problema com uma nova versão em vez de voltar. É útil quando rollback de schema ou dados é arriscado. Em sistemas modernos, muitas equipes preferem roll forward para mudanças irreversíveis.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-database-migration"></a>

## Database Migration

> Migração de banco deve fazer parte do processo de entrega, usando ferramentas como Flyway ou Liquibase. Eu versiono scripts, executo de forma controlada e evito mudanças destrutivas incompatíveis com versões ainda em execução.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-flyway-e-liquibase"></a>

## Flyway e Liquibase

> Flyway e Liquibase versionam mudanças de schema e permitem aplicar migrations de forma automatizada. O objetivo é tratar banco como parte versionada da aplicação e evitar alterações manuais em produção.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-zero-downtime-database-migration"></a>

## Zero-Downtime Database Migration

> Para zero downtime, faço mudanças backward compatible em etapas. Primeiro adiciono coluna ou estrutura nova sem remover a antiga, depois atualizo aplicação para usar o novo formato, migro dados e só por último removo o legado quando nenhuma versão depende dele.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-expand-and-contract"></a>

## Expand and Contract

> Expand and Contract é uma estratégia de evolução de schema em duas fases. Expand adiciona estruturas compatíveis; Contract remove estruturas antigas somente depois que todos os consumidores migraram. É essencial em rolling deployments.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-backward-compatibility-no-deploy"></a>

## Backward Compatibility no Deploy

> Durante rollout, versões antiga e nova podem rodar juntas. Por isso APIs, eventos e schema precisam permanecer compatíveis durante a transição. Breaking changes exigem estratégia em múltiplas etapas.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-health-check-pos-deploy"></a>

## Health Check Pós-Deploy

> Depois do deployment, verifico readiness, liveness e health checks relevantes. O objetivo é confirmar que a nova versão está apta a receber tráfego e que dependências críticas estão funcionando.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-smoke-test"></a>

## Smoke Test

> Smoke test é um conjunto pequeno de testes após o deploy para validar fluxos essenciais. Ele detecta rapidamente problemas de configuração, rota, banco ou dependência antes de liberar tráfego completo.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-observabilidade-pos-deploy"></a>

## Observabilidade Pós-Deploy

> Deployment não termina quando o container sobe. Eu observo erro, latência, p95/p99, CPU, memória, GC, pool de conexões e métricas de negócio para confirmar que a nova versão está saudável.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-automated-rollback"></a>

## Automated Rollback

> Rollback automático pode ser disparado quando métricas ou health checks ultrapassam limites durante canary ou rollout. Eu uso com critérios bem definidos para evitar rollback por ruído.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-approval-gate"></a>

## Approval Gate

> Approval gate adiciona aprovação manual antes de promover para ambiente crítico. Faz sentido em organizações reguladas ou quando o risco exige controle adicional. Em fluxos maduros e bem automatizados, o objetivo pode ser reduzir gates manuais desnecessários.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-pipeline-as-code"></a>

## Pipeline as Code

> Pipeline as Code mantém a definição da pipeline versionada junto do código ou em repositório controlado. Isso facilita review, histórico, reprodução e evolução do processo de entrega.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-jenkins"></a>

## Jenkins

> Jenkins é um servidor de automação flexível e extensível. Permite pipelines complexas via Jenkinsfile, mas exige operação, atualização de plugins, segurança e manutenção de agentes.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-github-actions"></a>

## GitHub Actions

> GitHub Actions integra workflows diretamente ao repositório. É conveniente para CI/CD e automação, mas eu considero segurança de actions de terceiros, permissões do token, secrets e custo dos runners.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-gitlab-ci"></a>

## GitLab CI

> GitLab CI define pipelines em arquivo versionado e integra build, test e deploy à plataforma. O conceito é semelhante a outras ferramentas: stages, jobs, runners, artifacts e environments.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-runner-self-hosted-vs-hosted"></a>

## Runner Self-hosted vs Hosted

> Runner hospedado simplifica operação. Self-hosted oferece mais controle de rede, performance e ferramentas, mas aumenta responsabilidade de patching, isolamento e segurança.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-cache-na-pipeline"></a>

## Cache na Pipeline

> Cache acelera pipelines reaproveitando dependências ou outputs intermediários. Em Java, cache de Maven ou Gradle pode reduzir bastante o tempo. O cuidado é evitar cache incorreto ou não invalidado, que pode mascarar problemas.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-paralelismo-na-pipeline"></a>

## Paralelismo na Pipeline

> Stages independentes podem rodar em paralelo, como testes unitários, análise estática e scanning. Isso reduz lead time, desde que não aumente excessivamente custo ou competição por recursos.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-artifacts-entre-jobs"></a>

## Artifacts entre Jobs

> Artifacts permitem transportar outputs de um estágio para outro, como JAR, relatórios ou pacotes. Prefiro passar o artefato construído em vez de reconstruí-lo em cada job.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-idempotencia-do-deploy"></a>

## Idempotência do Deploy

> Deploy idempotente significa que executar o mesmo processo novamente leva ao mesmo estado desejado sem efeitos colaterais indevidos. Isso é importante para retries automáticos e recuperação de falhas.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-infraestrutura-como-codigo-no-ci-cd"></a>

## Infraestrutura como Código no CI/CD

> IaC integra mudanças de infraestrutura ao mesmo processo de review e automação. Eu executo validação e plan antes de aplicar mudanças, mantendo auditabilidade e reduzindo drift manual.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-gitops"></a>

## GitOps

> GitOps usa Git como fonte declarativa do estado desejado e um controlador aplica mudanças no ambiente. É comum em Kubernetes. O benefício é auditabilidade e reconciliação contínua; o trade-off é adicionar outro modelo operacional.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-ci-cd-em-kubernetes"></a>

## CI/CD em Kubernetes

> Em Kubernetes, a pipeline normalmente constrói e publica a imagem e depois atualiza manifests, Helm chart ou configuração GitOps. O rollout deve considerar readiness, strategy, recursos, autoscaling e rollback.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-ci-cd-em-ecs"></a>

## CI/CD em ECS

> Em ECS, a pipeline publica a imagem no registry, cria uma nova task definition e atualiza o service. Eu acompanho health checks e deployment status e mantenho revisão anterior disponível para rollback.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-java-e-container-memory"></a>

## Java e Container Memory

> Ao implantar Java em containers, considero o limite total de memória, heap, metaspace, stacks e memória nativa. `-Xmx` não deve consumir todo o limite do container porque a JVM precisa de memória fora da heap.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-graceful-shutdown"></a>

## Graceful Shutdown

> Graceful shutdown permite que a instância pare de aceitar novas requisições e termine trabalho em andamento antes de encerrar. É importante em rolling deploys para evitar requests interrompidas.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-readiness-e-liveness"></a>

## Readiness e Liveness

> Readiness indica se a instância está pronta para receber tráfego. Liveness indica se o processo está saudável o suficiente para continuar vivo. Confundir os dois pode causar reinícios desnecessários ou tráfego para instâncias não prontas.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-deploy-de-java-com-migracao-de-banco"></a>

## Deploy de Java com Migração de Banco

> Eu separo mudança de schema destrutiva da mudança de aplicação. Primeiro aplico schema compatível, depois implanto código novo, valido a execução e só removo estruturas antigas em uma release posterior.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-dependency-management"></a>

## Dependency Management

> Em Java, mantenho versões controladas por BOM ou dependency management, evito ranges dinâmicos e monitoro vulnerabilidades. Reprodutibilidade depende de saber exatamente quais versões entraram no build.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-pipeline-para-microservicos"></a>

## Pipeline para Microsserviços

> Cada microsserviço deve ter pipeline independente sempre que possível. Um deploy de um serviço não deveria exigir rebuild de todos os outros. Contratos, testes e versionamento devem permitir autonomia.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-pipeline-monorepo"></a>

## Pipeline Monorepo

> Em monorepo, evito rebuildar tudo quando apenas um módulo mudou. Posso detectar paths impactados e executar pipelines seletivas, mantendo também uma validação global quando necessário.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-pipeline-failure-como-investigar"></a>

## Pipeline Failure — Como Investigar

> Eu identifico primeiro o estágio que falhou: build, teste, análise, publicação ou deploy. Depois separo erro de código de erro de infraestrutura, verifico logs, artefatos, versão das ferramentas, credenciais e diferenças de ambiente. Evito simplesmente reexecutar até passar sem entender a causa.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-lead-time"></a>

## Lead Time

> Lead time mede quanto tempo uma mudança leva desde o commit até produção. Pipelines lentas aumentam batch size e atrasam feedback. Eu reduzo lead time com fail fast, paralelismo, cache e testes bem distribuídos.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-deployment-frequency"></a>

## Deployment Frequency

> Deployment frequency mostra com que frequência o time consegue colocar mudanças em produção. Frequência alta normalmente depende de automação, testes confiáveis e mudanças pequenas.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-change-failure-rate"></a>

## Change Failure Rate

> Change failure rate mede a proporção de deploys que causam incidente, rollback ou correção urgente. É um indicador importante da qualidade do processo de entrega.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-mttr"></a>

## MTTR e Failed Deployment Recovery Time

> MTTR é uma medida genérica de tempo médio de recuperação, cujo escopo precisa ser definido. DORA usa Failed Deployment Recovery Time para a recuperação de deploys que falham e exigem intervenção imediata; distingo esse recorte dos demais incidentes operacionais.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-cicd-deployment-rework-rate"></a>

## Deployment Rework Rate

> Mede a proporção de deploys não planejados realizados em consequência de incidentes em produção. Complementa a taxa de falha ao mostrar quanto da atividade de entrega está sendo consumida por recuperação e correções urgentes.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-dora-metrics"></a>

## DORA Metrics

> O modelo DORA considera cinco métricas: change lead time, deployment frequency, failed deployment recovery time, change failure rate e deployment rework rate. Avalio entrega e instabilidade no contexto de uma aplicação ou serviço, evitando usá-las como ranking individual. [Referência](https://dora.dev/guides/dora-metrics/)

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-ci-cd-e-seguranca"></a>

## CI/CD e Segurança

> Pipeline possui acesso a código, artefatos, ambientes e credenciais, então é parte da superfície de ataque. Eu aplico least privilege, runners isolados, actions ou plugins confiáveis, secrets protegidos, branch protection e auditoria.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-ci-cd-e-compliance"></a>

## CI/CD e Compliance

> Em ambientes regulados, a pipeline pode registrar aprovações, artefatos, evidências de testes, versão implantada e histórico de mudanças. Automação melhora auditabilidade quando cada passo deixa evidência rastreável.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="cicd-resposta-completa-de-entrevista-ci-cd"></a>

## Resumo de Entrevista

> CI/CD automatiza build, testes, segurança, empacotamento e entrega. Promovo o mesmo artefato, evoluo schemas de forma compatível e escolho a estratégia de deploy conforme risco e observabilidade. Após implantar, verifico comportamento e mantenho uma estratégia viável de recuperação.

[↑ Sumário do tópico](#cicd-cicd-sumario) · [↑ Sumário geral](#sumario-geral)

---
