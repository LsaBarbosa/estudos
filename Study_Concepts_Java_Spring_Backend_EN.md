# Study Concepts — Java, SOLID, Design Patterns, Spring, and Backend

Review material with **short answers for study and interviews**, organized by prerequisites. The sequence progresses from Java fundamentals to distributed architecture and production operations.

> Follow the chapter order and use the tables of contents for review. Practice the concepts through small exercises and tests from the beginning; the testing chapter also consolidates Spring integrations. Version-specific and database-specific details should be checked against the environment in use.

<a id="sumario-geral"></a>

## Master Table of Contents

### Stage 1 — Java, Object-Oriented Programming, and Design

- [01. Java Fundamentals](#java-fundamentos-sumario)
- [02. Object-Oriented Programming in Java](#java-programacao-orientada-a-objetos-em-java-sumario)
- [03. Java Collections](#java-java-collections-sumario)
- [04. Functional Java and Modern APIs](#java-funcional-sumario)
- [05. SOLID and Design Principles](#design-solid-sumario)
- [06. Design Patterns](#design-patterns-sumario)

### Stage 2 — Runtime, Concurrency, and Builds

- [07. JVM Memory & Garbage Collection](#java-jvm-memory-garbage-collection-sumario)
- [08. Concurrency in Java](#java-concorrencia-em-java-sumario)
- [09. Builds and Dependencies — Maven and Gradle](#build-dependencias-sumario)

### Stage 3 — Persistence and HTTP Communication

- [10. SQL & Relational Databases](#sql-sql-bancos-relacionais-sumario)
- [11. HTTP and REST APIs](#http-rest-sumario)

### Stage 4 — Spring and Automated Testing

- [12. Spring Ecosystem — Overview](#spring-ecossistema-spring-visao-geral-sumario)
- [13. Spring Core](#spring-spring-core-sumario)
- [14. Spring Boot](#spring-spring-boot-sumario)
- [15. Spring MVC](#spring-spring-mvc-sumario)
- [16. Spring Data](#spring-spring-data-sumario)
- [17. Spring Security](#spring-spring-security-sumario)
- [18. Automated Testing in Java and Spring](#testes-java-spring-sumario)

### Stage 5 — Architecture and Distributed Systems

- [19. Software Architecture](#arquitetura-arquitetura-de-software-sumario)
- [20. Microservices](#microservicos-microservicos-sumario)
- [21. Kafka](#kafka-kafka-sumario)
- [22. Spring Cloud](#spring-spring-cloud-sumario)

### Stage 6 — Containers, AWS, and Continuous Delivery

- [23. Docker and Kubernetes](#containers-kubernetes-sumario)
- [24. AWS](#aws-aws-sumario)
- [25. CI/CD](#cicd-cicd-sumario)

> Each chapter has its own table of contents. After each answer, shortcuts return to **↑ Topic Contents** and **↑ Master Contents**.

---

<a id="java-fundamentos-sumario"></a>

# 01. Java Fundamentals

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#java-fundamentos-visao-geral)
- [JDK, JVM, and Runtime](#java-fundamentos-jdk-jvm-e-runtime)
- [Compilation, Bytecode, and Portability](#java-fundamentos-compilacao-bytecode-e-portabilidade)
- [Primitive Types and References](#java-fundamentos-tipos-primitivos-e-referencias)
- [Pass-by-Value](#java-fundamentos-passagem-por-valor)
- [Wrappers, Autoboxing, and Unboxing](#java-fundamentos-wrappers-autoboxing-e-unboxing)
- [== vs equals](#java-fundamentos-vs-equals)
- [String, Immutability, and String Pool](#java-fundamentos-string-imutabilidade-e-string-pool)
- [StringBuilder vs StringBuffer](#java-fundamentos-stringbuilder-vs-stringbuffer)
- [final, finally, and finalize](#java-fundamentos-final-finally-e-finalize)
- [Checked and Unchecked Exceptions](#java-fundamentos-excecoes-checked-e-unchecked)
- [throw, throws, and Preserving the Cause](#java-fundamentos-throw-throws-e-preservacao-da-causa)
- [try-with-resources and AutoCloseable](#java-fundamentos-try-with-resources-e-autocloseable)
- [Generics and Type Safety](#java-fundamentos-generics-e-seguranca-de-tipos)
- [Wildcards and PECS](#java-fundamentos-wildcards-e-pecs)
- [Type Erasure](#java-fundamentos-type-erasure)
- [BigDecimal and Monetary Values](#java-fundamentos-bigdecimal-e-valores-monetarios)
- [java.time and Time Zones](#java-fundamentos-java-time-e-fusos-horarios)
- [Annotations and Reflection](#java-fundamentos-annotations-e-reflection)
- [Interview Summary](#java-fundamentos-resumo-de-entrevista)

---

<a id="java-fundamentos-visao-geral"></a>

## Overview

> Java combines static typing, object-oriented programming, and bytecode execution on the JVM. To master frameworks, I start with types, references, object contracts, exceptions, and resource management.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-jdk-jvm-e-runtime"></a>

## JDK, JVM, and Runtime

> The JVM executes bytecode. The JDK includes tools such as a compiler, debugger, and runtime; the JRE refers to the environment required to run an application, whose distribution varies by vendor and version.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-compilacao-bytecode-e-portabilidade"></a>

## Compilation, Bytecode, and Portability

> The compiler converts Java code into bytecode executed by a compatible JVM. Portability also depends on the target version and native dependencies; a binary compiled for a newer version does not automatically run on an older JVM.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-tipos-primitivos-e-referencias"></a>

## Primitive Types and References

> Primitives hold values such as numbers and booleans. Reference variables refer to objects and may contain `null`; multiple references can point to the same object, allowing shared changes when that object is mutable.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-passagem-por-valor"></a>

## Pass-by-Value

> Java always passes arguments by value. For objects, it copies the reference value: changes to the object can be observed by the caller, but reassigning the parameter does not change the caller's original variable.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-wrappers-autoboxing-e-unboxing"></a>

## Wrappers, Autoboxing, and Unboxing

> Wrappers such as `Integer` represent primitive values as objects. Automatic conversions simplify generic APIs but may create allocations and throw `NullPointerException` when unboxing `null`; I compare wrapper values with `equals`, not `==`.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-vs-equals"></a>

## == vs equals

> For primitives, `==` compares values; for references, it compares object identity. `equals` can express logical equality, but the implementation inherited from `Object` also compares identity. The `equals` method does not need to call `hashCode`.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-string-imutabilidade-e-string-pool"></a>

## String, Immutability, and String Pool

> `String` is immutable: operations produce a new result without changing the original content. Literals may share instances in the string pool, but this does not make `==` appropriate for text comparison; I use `equals`.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-stringbuilder-vs-stringbuffer"></a>

## StringBuilder vs StringBuffer

> `StringBuilder` is suitable for building mutable text within a single execution flow. `StringBuffer` synchronizes its operations and has extra overhead; a sequence of several calls may still need coordination to be atomic.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-final-finally-e-finalize"></a>

## final, finally, and finalize

> Depending on its use, `final` prevents reassignment, overriding, or inheritance, but does not make an object deeply immutable. `finally` performs cleanup when exiting a `try`, except during abrupt termination. `finalize` is a discouraged legacy mechanism; I use `AutoCloseable` for resources.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-excecoes-checked-e-unchecked"></a>

## Checked and Unchecked Exceptions

> Checked exceptions must be handled or declared with `throws`. Unchecked exceptions derive from `RuntimeException` and have no such requirement; the choice should reflect the API contract. `Error` generally represents serious problems and should not be caught indiscriminately.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-throw-throws-e-preservacao-da-causa"></a>

## throw, throws, and Preserving the Cause

> `throw` throws an exception; `throws` declares exceptions that may escape a method. When translating a failure, I preserve its cause and add useful context, avoiding swallowed errors or logging the same failure at every layer.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-try-with-resources-e-autocloseable"></a>

## try-with-resources and AutoCloseable

> `try-with-resources` closes `AutoCloseable` resources, such as files and JDBC connections, even when an exception occurs. Resources close in reverse order of opening; GC does not replace explicit resource cleanup.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-generics-e-seguranca-de-tipos"></a>

## Generics and Type Safety

> Generics allow code reuse with compiler-checked types, such as `List<Pedido>`. They eliminate unnecessary casts, but `List<Integer>` is not a subtype of `List<Number>`; invariance prevents incompatible insertions.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-wildcards-e-pecs"></a>

## Wildcards and PECS

> `? extends T` is useful when a structure produces values of type `T`; `? super T` is useful when it consumes values of type `T`. PECS means Producer Extends, Consumer Super and guides flexible, type-safe signatures.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-type-erasure"></a>

## Type Erasure

> In the usual implementation of generics, the compiler erases type parameters and inserts casts as needed. That is why I cannot write `new T()` or test `instanceof List<String>`; some generic type information still exists in metadata for reflection.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-bigdecimal-e-valores-monetarios"></a>

## BigDecimal and Monetary Values

> `BigDecimal` supports decimal arithmetic with explicit scale and rounding. For money, I avoid constructing it from `double` and specify the rounding policy; `equals` takes scale into account, while `compareTo` compares numeric value.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-java-time-e-fusos-horarios"></a>

## java.time and Time Zones

> `Instant` represents a point on the timeline; `LocalDate` represents a date without time; `LocalDateTime` includes no time zone. I use `ZonedDateTime` when time-zone rules matter and inject a `Clock` for predictable tests.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-annotations-e-reflection"></a>

## Annotations and Reflection

> Annotations carry metadata used by compilers, tools, or frameworks. Reflection inspects types and members at runtime, but reduces static checks and can conflict with encapsulation; I avoid it when an explicit API suffices.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-fundamentos-resumo-de-entrevista"></a>

## Interview Summary

> Java requires understanding values, references, equality, exceptions, and types before frameworks. I use generics for type safety, immutable objects when possible, `try-with-resources` for external resources, and appropriate APIs for dates and monetary values.

[↑ Topic Contents](#java-fundamentos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-sumario"></a>

# 02. Object-Oriented Programming in Java

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#java-programacao-orientada-a-objetos-em-java-visao-geral)
- [Class and Object](#java-programacao-orientada-a-objetos-em-java-exemplo)
- [Constructors and Valid Initialization](#java-programacao-orientada-a-objetos-em-java-construtores-e-inicializacao-valida)
- [Access Modifiers and static](#java-programacao-orientada-a-objetos-em-java-modificadores-de-acesso-e-static)
- [Encapsulation and Invariants](#java-programacao-orientada-a-objetos-em-java-trade-off)
- [Abstraction, Interfaces, and Abstract Classes](#java-programacao-orientada-a-objetos-em-java-regra-pratica)
- [Inheritance and the IS-A Relationship](#java-programacao-orientada-a-objetos-em-java-regra-pratica-2)
- [Polymorphism and Method Overriding](#java-programacao-orientada-a-objetos-em-java-overriding-sobrescrita)
- [Overloading vs Overriding](#java-programacao-orientada-a-objetos-em-java-overloading-vs-overriding)
- [Interfaces: Abstract, default, and static Methods](#java-programacao-orientada-a-objetos-em-java-interface-metodos-abstratos-default-e-static)
- [Composition and Delegation](#java-programacao-orientada-a-objetos-em-java-3-delegacao)
- [Association, Aggregation, and Composition](#java-programacao-orientada-a-objetos-em-java-observacao-pratica)
- [Coupling and Cohesion](#java-programacao-orientada-a-objetos-em-java-objetivo)
- [equals, hashCode, and toString](#java-programacao-orientada-a-objetos-em-java-cuidado)
- [Records, Sealed Classes, and Enums](#java-programacao-orientada-a-objetos-em-java-3-enums)
- [Immutability and Defensive Copying](#java-programacao-orientada-a-objetos-em-java-imutabilidade-e-copia-defensiva)
- [Modeling Valid Objects](#java-programacao-orientada-a-objetos-em-java-4-encapsular-invariantes)
- [Refactoring OO Models](#java-programacao-orientada-a-objetos-em-java-correlacao-com-concorrencia)
- [Interview Summary](#java-programacao-orientada-a-objetos-em-java-resposta-completa-de-entrevista)
- [30-Second Answer](#java-programacao-orientada-a-objetos-em-java-resposta-de-30-segundos)

---

<a id="java-programacao-orientada-a-objetos-em-java-visao-geral"></a>

## Overview

> Object-oriented programming organizes a system into objects with state and behavior. In Java, its main concepts are encapsulation, abstraction, inheritance, and polymorphism. In practice, I use OOP to model responsibilities, protect invariants, and reduce coupling, usually favoring abstractions and composition when they improve flexibility.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-exemplo"></a>

## Class and Object

> A class defines structure and behavior; an object is a concrete instance of that class. I aim to create objects in a valid state and keep behavior close to the state it controls, avoiding unnecessary exposure of fields.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-construtores-e-inicializacao-valida"></a>

## Constructors and Valid Initialization

> A constructor establishes an object's initial state and can validate invariants. I avoid publishing `this` during construction because other code could observe a partially initialized object.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-modificadores-de-acesso-e-static"></a>

## Access Modifiers and static

> `private`, package-private, `protected`, and `public` control visibility. `static` associates a member with a class rather than an instance; mutable static state can introduce global dependencies and concurrency issues.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-trade-off"></a>

## Encapsulation and Invariants

> Encapsulation is more than marking fields `private`: it controls how state can change and protects invariants. I prefer exposing business operations rather than generic setters because this reduces invalid states and coupling to internal implementation.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-regra-pratica"></a>

## Abstraction, Interfaces, and Abstract Classes

> Abstraction exposes essential capabilities while hiding implementation details. In Java, interfaces are useful for contracts and decoupling. Abstract classes make sense when subclasses share state or base behavior and have a clear specialization relationship.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-regra-pratica-2"></a>

## Inheritance and the IS-A Relationship

> Inheritance models an IS-A relationship. I use it when specialization is genuine and a subclass can substitute for its superclass without violating the contract. I avoid inheritance solely for code reuse because it increases coupling between parents and children.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-overriding-sobrescrita"></a>

## Polymorphism and Method Overriding

> Polymorphism allows different implementations to be used through the same contract. In Java, when an interface reference points to a concrete implementation, the overridden method is resolved at runtime through dynamic dispatch.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-overloading-vs-overriding"></a>

## Overloading vs Overriding

> Overloading defines methods with the same name but different parameters, resolved according to types known at compile time. Overriding redefines an inherited instance method and uses dynamic dispatch; changing only the return type does not create a valid overload.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-interface-metodos-abstratos-default-e-static"></a>

## Interfaces: Abstract, default, and static Methods

> Interfaces can declare abstract methods and provide `default`, `static`, and private helper methods. An implementing class must fulfill the applicable abstract methods, but an interface is not limited to methods without implementations.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-3-delegacao"></a>

## Composition and Delegation

> Composition represents a HAS-A relationship and builds behavior through dependencies. I normally prefer composition when reusing behavior or swapping implementations because it reduces the structural coupling introduced by inheritance.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-observacao-pratica"></a>

## Association, Aggregation, and Composition

> Association is a general relationship between objects. Aggregation is a weaker whole-part relationship where a part can exist independently. Composition is stronger and generally means a part belongs to the lifecycle of the whole.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-objetivo"></a>

## Coupling and Cohesion

> I aim for low dependency between components and high cohesion within each class. Low coupling simplifies replacement and evolution; high cohesion keeps related responsibilities together.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-cuidado"></a>

## equals, hashCode, and toString

> `equals` defines logical equality, `hashCode` must respect that equality for hash-based structures, and `toString` provides useful diagnostic text. With mutable entities, I also avoid changing fields used by `hashCode` while the object is inside a `HashMap` or `HashSet`.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-3-enums"></a>

## Records, Sealed Classes, and Enums

> Records synthesize components, accessors, and methods such as `equals` and `hashCode`, but they are only shallowly immutable. Sealed classes and interfaces restrict permitted subtypes. Enums represent a closed set of constants that may also have state and behavior.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-imutabilidade-e-copia-defensiva"></a>

## Immutability and Defensive Copying

> An immutable object does not change its observable state after construction. I use final fields, validation, and control of mutable references; a list inside a record still needs copying and attention to its elements to prevent external mutation.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-4-encapsular-invariantes"></a>

## Modeling Valid Objects

> In OO modeling, I program to abstractions, favor composition where no legitimate IS-A relationship exists, and keep invariants inside the objects themselves. This reduces coupling and prevents business rules from being scattered.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-correlacao-com-concorrencia"></a>

## Refactoring OO Models

> In production, I use OOP to concentrate rules and responsibilities, not merely create hierarchies. If I find many conditionals based on type, I consider polymorphism. If setters allow invalid states, I strengthen encapsulation. If a hierarchy has grown deep, I generally consider composition and delegation.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-resposta-completa-de-entrevista"></a>

## Interview Summary

> I use OOP to organize state and behavior, protect invariants, and keep responsibilities cohesive. I prefer clear contracts and composition, adopting inheritance when valid substitution is possible. Records and immutable types help represent data but do not eliminate the need for validation.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-programacao-orientada-a-objetos-em-java-resposta-de-30-segundos"></a>

## 30-Second Answer

> OOP in Java organizes software into objects with state and behavior. Its main pillars are encapsulation, abstraction, inheritance, and polymorphism. I use encapsulation to protect invariants, abstractions to decouple implementations, and polymorphism to support different behaviors through the same contract. I reserve inheritance for genuine IS-A relationships; for flexibility and behavior reuse, I generally prefer composition.

[↑ Topic Contents](#java-programacao-orientada-a-objetos-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-sumario"></a>

# 03. Java Collections

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#java-java-collections-visao-geral)
- [Time and Space Complexity](#java-java-collections-complexidade-de-tempo-e-espaco)
- [List, Set, Queue, Deque, and Map](#java-java-collections-6-map)
- [ArrayList and Complexity](#java-java-collections-complexidade-tipica)
- [LinkedList and Trade-offs](#java-java-collections-trade-off)
- [The equals and hashCode Contract](#java-java-collections-problema-classico)
- [Mutable Keys in HashMap and HashSet](#java-java-collections-chaves-mutaveis-em-hashmap-e-hashset)
- [HashSet](#java-java-collections-1-hashset)
- [LinkedHashSet](#java-java-collections-linkedhashset)
- [TreeSet](#java-java-collections-3-treeset)
- [HashMap](#java-java-collections-1-hashmap)
- [Buckets and Collisions in HashMap](#java-java-collections-detalhe-de-entrevista)
- [LinkedHashMap](#java-java-collections-linkedhashmap)
- [TreeMap](#java-java-collections-3-treemap)
- [Comparable vs Comparator](#java-java-collections-comparable-vs-comparator)
- [PriorityQueue](#java-java-collections-complexidade-tipica-2)
- [ArrayDeque](#java-java-collections-2-arraydeque)
- [Fail-fast Iterators and Safe Removal](#java-java-collections-3-remocao-segura)
- [Unmodifiable and Fixed-size Collections](#java-java-collections-armadilha-classica)
- [CopyOnWriteArrayList](#java-java-collections-uso-comum)
- [BlockingQueue](#java-java-collections-3-blockingqueue)
- [How to Choose a Collection](#java-java-collections-como-escolher-a-collection)
- [Diagnosing Collection-related Latency](#java-java-collections-cenario-7-latencia-degradando-com-collection-grande)
- [Interview Summary](#java-java-collections-resposta-completa-de-entrevista)

---

<a id="java-java-collections-visao-geral"></a>

## Overview

> The Java Collections Framework provides standardized data structures for storing and manipulating objects. Its main abstractions are List, Set, Queue, Deque, and Map. The choice depends on requirements such as ordering, duplicates, index access, key-based lookup, sorting, and concurrency.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-complexidade-de-tempo-e-espaco"></a>

## Time and Space Complexity

> Big O describes how cost grows with input size, not exact time in seconds. I distinguish average case, worst case, and amortized cost; memory locality and object size also affect real-world performance.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-6-map"></a>

## List, Set, Queue, Deque, and Map

> List represents a sequence and allows duplicates. Set represents unique elements. Queue and Deque support ordered element processing. Map represents key-value associations and does not extend Collection.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-complexidade-tipica"></a>

## ArrayList and Complexity

> ArrayList is backed by a dynamic array. Index access is O(1), and appending is amortized O(1). Inserting or removing in the middle is O(n) because elements may need to shift.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-trade-off"></a>

## LinkedList and Trade-offs

> LinkedList is a doubly linked list. Operations at its ends are efficient, but indexed access is O(n). In practice, ArrayList is usually preferable for general-purpose lists because of better memory locality and lower overhead.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-problema-classico"></a>

## The equals and hashCode Contract

> `equals` defines logical equality, while `hashCode` determines distribution in hash-based structures. If two objects are equal according to `equals`, they must return the same `hashCode`.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-chaves-mutaveis-em-hashmap-e-hashset"></a>

## Mutable Keys in HashMap and HashSet

> Changing fields used by `equals` or `hashCode` after insertion can prevent an object from being found in its expected bucket. I prefer stable, immutable keys; equality and hashing must remain consistent while objects are stored.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-1-hashset"></a>

## HashSet

> HashSet is useful when I need uniqueness without ordering. Under normal conditions, `add`, `contains`, and `remove` have near-O(1) cost.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-linkedhashset"></a>

## LinkedHashSet

> LinkedHashSet combines uniqueness with predictable insertion order. Hash operations generally remain O(1) on average, with additional memory to track ordering.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-3-treeset"></a>

## TreeSet

> TreeSet keeps elements sorted and offers O(log n) operations. I use it when I need both uniqueness and ordering.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-1-hashmap"></a>

## HashMap

> HashMap is a hash-based key-value structure. Under normal conditions, `get` and `put` are O(1) on average. Good `hashCode` implementations and compliance with the `equals` contract are essential for correct behavior and performance.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-detalhe-de-entrevista"></a>

## Buckets and Collisions in HashMap

> HashMap distributes entries into buckets using `hashCode`. When collisions occur, `equals` distinguishes keys. In modern implementations, heavily populated buckets may be converted into trees to prevent severe degradation.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-linkedhashmap"></a>

## LinkedHashMap

> LinkedHashMap preserves predictable insertion order or, when configured, access order. It can support simple LRU policies but is not thread-safe and does not automatically replace a cache with expiration and concurrency controls.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-3-treemap"></a>

## TreeMap

> TreeMap is useful when keys must stay sorted and range-navigation operations are important.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-comparable-vs-comparator"></a>

## Comparable vs Comparator

> Comparable defines a type's natural ordering. Comparator defines external strategies and supports multiple orderings without changing the class.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-complexidade-tipica-2"></a>

## PriorityQueue

> PriorityQueue keeps the smallest element according to its configured ordering at the head. `peek` costs O(1), while insertion and removal of the head cost O(log n); iteration is not fully sorted. I use it when repeatedly processing the next highest-priority item.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-2-arraydeque"></a>

## ArrayDeque

> ArrayDeque is an efficient queue or stack implementation. For a modern stack in Java, I generally prefer ArrayDeque over the legacy Stack class.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-3-remocao-segura"></a>

## Fail-fast Iterators and Safe Removal

> Fail-fast iterators may detect improper structural modifications and throw `ConcurrentModificationException`, even on a single thread. This detection does not guarantee thread safety. Where supported, I use `Iterator.remove` or operations such as `removeIf`, following the collection's contract.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-armadilha-classica"></a>

## Unmodifiable and Fixed-size Collections

> `Collections.unmodifiableList` creates an unmodifiable view, but changes to the original list remain visible. `List.of` prevents structural changes and rejects null elements; contained objects may still be mutable. `Arrays.asList` has a fixed size and shares the original array.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-uso-comum"></a>

## CopyOnWriteArrayList

> CopyOnWriteArrayList favors workloads with many reads and few writes. Reads are straightforward, but every modification copies the internal structure, making it unsuitable for high write rates.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-3-blockingqueue"></a>

## BlockingQueue

> BlockingQueue is useful for producer-consumer coordination because it provides operations that may block when the queue is empty or full, also helping with local backpressure control.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-como-escolher-a-collection"></a>

## How to Choose a Collection

> I choose collections based on access patterns and required guarantees. For key lookups I typically start with HashMap; for sequences, ArrayList; for uniqueness, HashSet; for ordering, TreeMap or TreeSet; and for queues or stacks, ArrayDeque. Then I consider concurrency and specific requirements.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-cenario-7-latencia-degradando-com-collection-grande"></a>

## Diagnosing Collection-related Latency

> In production, I choose a collection based not only on data type but also on access patterns. With frequent key lookups, for example, a List may repeatedly incur O(n) while a HashMap provides average O(1). I also consider memory, ordering, write rate, concurrency, and volume.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-java-collections-resposta-completa-de-entrevista"></a>

## Interview Summary

> I choose collections based on access patterns and guarantees: ArrayList for sequences, HashMap for key-value data, Set for uniqueness, and Deque for queues. I consider ordering, operation cost, memory, and concurrency while preserving the `equals`/`hashCode` contract.

[↑ Topic Contents](#java-java-collections-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-funcional-sumario"></a>

# 04. Functional Java and Modern APIs

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#java-funcional-visao-geral)
- [Functional Interfaces](#java-funcional-interfaces-funcionais)
- [Lambdas and Method References](#java-funcional-lambdas-e-method-references)
- [Predicate, Function, Consumer, and Supplier](#java-funcional-predicate-function-consumer-e-supplier)
- [Stream vs Collection](#java-funcional-stream-vs-collection)
- [Intermediate and Terminal Operations](#java-funcional-operacoes-intermediarias-e-terminais)
- [map vs flatMap](#java-funcional-map-vs-flatmap)
- [reduce vs collect](#java-funcional-reduce-vs-collect)
- [Collectors, groupingBy, and toMap](#java-funcional-collectors-groupingby-e-tomap)
- [Stream.toList and Mutability](#java-funcional-stream-tolist-e-mutabilidade)
- [Optional](#java-funcional-optional)
- [Parallel Streams](#java-funcional-streams-paralelas)
- [Interview Summary](#java-funcional-resumo-de-entrevista)

---

<a id="java-funcional-visao-geral"></a>

## Overview

> Java combines objects with functions to transform data declaratively. Lambdas, functional interfaces, Streams, and Optional help communicate intent, provided I control side effects and do not hide expensive operations.

[↑ Topic Contents](#java-funcional-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-funcional-interfaces-funcionais"></a>

## Functional Interfaces

> A functional interface has one abstract method relevant to its contract and can accept a lambda. `default` and `static` methods do not prevent this; `@FunctionalInterface` asks the compiler to enforce the rule.

[↑ Topic Contents](#java-funcional-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-funcional-lambdas-e-method-references"></a>

## Lambdas and Method References

> Lambdas implement short behaviors; method references reuse compatible methods, such as `Pedido::getId`. Captured local variables must be final or effectively final, but the referenced object may still be mutable.

[↑ Topic Contents](#java-funcional-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-funcional-predicate-function-consumer-e-supplier"></a>

## Predicate, Function, Consumer, and Supplier

> `Predicate<T>` tests a condition, `Function<T,R>` transforms a value, `Consumer<T>` performs an action, and `Supplier<T>` supplies a value. These are common contracts for behavior composition without creating a custom interface for every case.

[↑ Topic Contents](#java-funcional-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-funcional-stream-vs-collection"></a>

## Stream vs Collection

> A Collection stores data; a Stream describes a processing pipeline over a source. Streams are generally consumed only once and do not modify their source by themselves; side effects inside lambdas can compromise results.

[↑ Topic Contents](#java-funcional-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-funcional-operacoes-intermediarias-e-terminais"></a>

## Intermediate and Terminal Operations

> Intermediate operations such as `filter` and `map` build a pipeline lazily. Terminal operations such as `collect`, `count`, and `findFirst` initiate processing; some can finish before traversing all elements.

[↑ Topic Contents](#java-funcional-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-funcional-map-vs-flatmap"></a>

## map vs flatMap

> `map` transforms each element into another value. `flatMap` transforms each element into a stream and flattens the results, such as converting a list of orders into a single stream of their items.

[↑ Topic Contents](#java-funcional-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-funcional-reduce-vs-collect"></a>

## reduce vs collect

> `reduce` combines values into a result, such as a sum, using an associative operation. `collect` accumulates values into structures or specialized results; for mutable collections, I prefer collectors over mutating a shared accumulator in `reduce`.

[↑ Topic Contents](#java-funcional-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-funcional-collectors-groupingby-e-tomap"></a>

## Collectors, groupingBy, and toMap

> Collectors can group, partition, and build maps. With `toMap`, I specify how to handle duplicate keys when possible; for large groupings, I consider memory and data volume before loading everything into the application.

[↑ Topic Contents](#java-funcional-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-funcional-stream-tolist-e-mutabilidade"></a>

## Stream.toList and Mutability

> `Stream.toList()` returns an unmodifiable list. `Collectors.toList()` does not guarantee a particular implementation or mutability; when I need a mutable list, I use an explicit collector such as `toCollection(ArrayList::new)`.

[↑ Topic Contents](#java-funcional-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-funcional-optional"></a>

## Optional

> `Optional` represents possible absence, mainly in return values. I use `map`, `flatMap`, and `orElseThrow` to express control flow; `orElseGet` computes a fallback on demand, whereas an argument passed to `orElse` is evaluated even when a value exists.

[↑ Topic Contents](#java-funcional-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-funcional-streams-paralelas"></a>

## Parallel Streams

> Parallel streams divide work but add coordination overhead and may contend for a shared pool. I assess data volume, associativity, absence of shared state, and real measurements; parallelizing blocking calls without controls can saturate dependencies.

[↑ Topic Contents](#java-funcional-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-funcional-resumo-de-entrevista"></a>

## Interview Summary

> I use lambdas and Streams for clear transformations, preferably without side effects. I distinguish lazy evaluation, reduction, collection, and absence of values; I adopt parallelism only when gains have been measured and resources are controlled.

[↑ Topic Contents](#java-funcional-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-solid-sumario"></a>

# 05. SOLID and Design Principles

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#design-solid-visao-geral)
- [S — Single Responsibility Principle (SRP)](#design-solid-s-single-responsibility-principle-srp)
- [O — Open/Closed Principle (OCP)](#design-solid-o-open-closed-principle-ocp)
- [L — Liskov Substitution Principle (LSP)](#design-solid-l-liskov-substitution-principle-lsp)
- [I — Interface Segregation Principle (ISP)](#design-solid-i-interface-segregation-principle-isp)
- [D — Dependency Inversion Principle (DIP)](#design-solid-d-dependency-inversion-principle-dip)
- [DIP vs Dependency Injection vs IoC](#design-solid-dip-vs-dependency-injection-vs-ioc)
- [SOLID in Practice](#design-solid-solid-na-pratica)
- [DRY, KISS, and YAGNI](#design-solid-dry-kiss-e-yagni)
- [Code Smells and Refactoring](#design-solid-code-smells-e-refatoracao)
- [Interview Summary](#design-solid-resumo-de-entrevista)

---

<a id="design-solid-visao-geral"></a>

## Overview

> SOLID comprises five principles for structuring responsibilities and dependencies. I use them to simplify changes and testing without creating interfaces and layers that serve no concrete purpose.

[↑ Topic Contents](#design-solid-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-solid-s-single-responsibility-principle-srp"></a>

## S — Single Responsibility Principle (SRP)

> A class should group responsibilities that change for the same reason and concern the same stakeholders. For example, I separate billing calculations from report generation. SRP does not mean each class must have only one method. [Reference](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)

[↑ Topic Contents](#design-solid-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-solid-o-open-closed-principle-ocp"></a>

## O — Open/Closed Principle (OCP)

> A component should support extensions without repeatedly modifying stable logic. For example, I add a new discount strategy through another implementation. This does not prohibit fixing existing code or require anticipating every future variation. [Reference](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)

[↑ Topic Contents](#design-solid-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-solid-l-liskov-substitution-principle-lsp"></a>

## L — Liskov Substitution Principle (LSP)

> An implementation must be able to substitute for its base type while preserving the expected contract. It must not require stronger preconditions or promise weaker outcomes; if an account does not allow withdrawals, it should not implement a contract that guarantees withdrawals. [Reference](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)

[↑ Topic Contents](#design-solid-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-solid-i-interface-segregation-principle-isp"></a>

## I — Interface Segregation Principle (ISP)

> A client should depend on contracts focused on what it uses. Separating reads from writes can avoid forcing implementations to expose meaningless operations; the goal is cohesive contracts, not an artificial proliferation of interfaces. [Reference](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)

[↑ Topic Contents](#design-solid-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-solid-d-dependency-inversion-principle-dip"></a>

## D — Dependency Inversion Principle (DIP)

> High-level rules and implementation details should depend on stable abstractions whose contracts do not depend on infrastructure. A use case may depend on `PagamentoGateway` while an HTTP adapter implements that contract. [Reference](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)

[↑ Topic Contents](#design-solid-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-solid-dip-vs-dependency-injection-vs-ioc"></a>

## DIP vs Dependency Injection vs IoC

> DIP governs the direction of dependencies in code. Dependency Injection supplies dependencies from outside an object. IoC is the broader inversion of control; injecting a concrete class does not, by itself, satisfy DIP.

[↑ Topic Contents](#design-solid-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-solid-solid-na-pratica"></a>

## SOLID in Practice

> In a checkout, I separate calculation, persistence, and payment by responsibility, use strategies for variations, and define coherent contracts for integrations. I evaluate the design by how easily rules can be tested and changed, not by the number of classes created.

[↑ Topic Contents](#design-solid-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-solid-dry-kiss-e-yagni"></a>

## DRY, KISS, and YAGNI

> DRY avoids duplicating business knowledge; KISS favors understandable solutions; YAGNI avoids building speculative features. Similar-looking code does not always encode the same rule; premature abstraction can also increase coupling.

[↑ Topic Contents](#design-solid-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-solid-code-smells-e-refatoracao"></a>

## Code Smells and Refactoring

> Code smells are potential warning signs, such as classes with too many responsibilities, long methods, and duplicated rules. Refactoring improves structure while preserving observable behavior; I rely on tests and small changes to reduce risk.

[↑ Topic Contents](#design-solid-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-solid-resumo-de-entrevista"></a>

## Interview Summary

> I apply SOLID to clarify responsibilities and contracts and reduce the impact of changes. I combine these principles with simplicity and testing, introducing abstractions where real variations or meaningful boundaries exist.

[↑ Topic Contents](#design-solid-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-sumario"></a>

# 06. Design Patterns

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#design-patterns-visao-geral)
- [GoF Categories](#design-patterns-categorias-gof)
- [Factory Method — Creational](#design-patterns-factory-method-criacional)
- [Abstract Factory — Creational](#design-patterns-abstract-factory-criacional)
- [Builder — Creational](#design-patterns-builder-criacional)
- [Prototype — Creational](#design-patterns-prototype-criacional)
- [Singleton — Creational](#design-patterns-singleton-criacional)
- [Adapter — Structural](#design-patterns-adapter-estrutural)
- [Bridge — Structural](#design-patterns-bridge-estrutural)
- [Composite — Structural](#design-patterns-composite-estrutural)
- [Decorator — Structural](#design-patterns-decorator-estrutural)
- [Facade — Structural](#design-patterns-facade-estrutural)
- [Flyweight — Structural](#design-patterns-flyweight-estrutural)
- [Proxy — Structural](#design-patterns-proxy-estrutural)
- [Chain of Responsibility — Behavioral](#design-patterns-chain-of-responsibility-comportamental)
- [Command — Behavioral](#design-patterns-command-comportamental)
- [Interpreter — Behavioral](#design-patterns-interpreter-comportamental)
- [Iterator — Behavioral](#design-patterns-iterator-comportamental)
- [Mediator — Behavioral](#design-patterns-mediator-comportamental)
- [Memento — Behavioral](#design-patterns-memento-comportamental)
- [Observer — Behavioral](#design-patterns-observer-comportamental)
- [State — Behavioral](#design-patterns-state-comportamental)
- [Strategy — Behavioral](#design-patterns-strategy-comportamental)
- [Template Method — Behavioral](#design-patterns-template-method-comportamental)
- [Visitor — Behavioral](#design-patterns-visitor-comportamental)
- [Strategy vs State vs Template Method](#design-patterns-strategy-vs-state-vs-template-method)
- [Adapter vs Decorator vs Proxy vs Facade](#design-patterns-adapter-vs-decorator-vs-proxy-vs-facade)
- [Repository, DTO, and Mapper](#design-patterns-repository-dto-e-mapper)
- [Design Pattern vs Architectural Pattern](#design-patterns-padrao-de-projeto-vs-padrao-arquitetural)
- [When Not to Apply a Pattern](#design-patterns-quando-nao-aplicar-um-padrao)
- [Interview Summary](#design-patterns-resumo-de-entrevista)

---

<a id="design-patterns-visao-geral"></a>

## Overview

> Design patterns describe recurring solutions to design problems, including their context and consequences. They are neither ready-made code nor mandatory practices: I first identify the problem and then assess whether a pattern simplifies the solution.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-categorias-gof"></a>

## GoF Categories

> The 23 GoF patterns are divided into creational, structural, and behavioral categories. Creational patterns organize object creation; structural patterns organize object composition; behavioral patterns organize responsibilities and collaboration among objects.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-factory-method-criacional"></a>

## Factory Method — Creational

> Defines a creation method that subclasses can specialize to select a concrete product. I separate the code using an object from the choice of its implementation; a static function that instantiates objects is not necessarily the GoF pattern.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-abstract-factory-criacional"></a>

## Abstract Factory — Creational

> Creates families of related objects through common contracts. It is useful when components must vary together and remain compatible, but adding a new product type requires modifying the factories in that family.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-builder-criacional"></a>

## Builder — Creational

> Separates the construction of a complex object from its representation. It helps with numerous parameters and steps while retaining validation of the final result; a clear constructor may be sufficient for simple objects.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-prototype-criacional"></a>

## Prototype — Creational

> Creates objects by copying an existing prototype. This is useful when initial configuration is expensive or complex; I distinguish shallow and deep copying to avoid accidentally sharing mutable state.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-singleton-criacional"></a>

## Singleton — Creational

> Controls creation to expose a single instance within a given execution scope. It can introduce global state and complicate testing; a Spring singleton is scoped to a bean definition and container, not a guarantee of one instance across a distributed system.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-adapter-estrutural"></a>

## Adapter — Structural

> Translates an existing interface into the contract expected by a client. For example, I use it to integrate an external provider with an application port, keeping translation details out of business rules.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-bridge-estrutural"></a>

## Bridge — Structural

> Separates an abstraction from its implementation so both can vary independently. This is useful when two dimensions would otherwise generate many combined subclasses; the cost is an extra layer of delegation.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-composite-estrutural"></a>

## Composite — Structural

> Represents tree structures and lets clients treat leaves and composites through the same contract. It suits hierarchies such as folders and files, provided their shared operations make sense for both types.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-decorator-estrutural"></a>

## Decorator — Structural

> Adds behavior by wrapping an object in another object with the same contract. It supports combinations such as compression and encryption without many subclasses, but long decorator chains can complicate debugging and operation order.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-facade-estrutural"></a>

## Facade — Structural

> Provides a simple interface to a more complex subsystem. It reduces what clients need to know but should remain focused to avoid concentrating rules in a do-everything class.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-flyweight-estrutural"></a>

## Flyweight — Structural

> Shares intrinsic state among many objects to save memory while keeping variable state outside the shared object. It is worthwhile only when the instance count and memory savings justify the complexity.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-proxy-estrutural"></a>

## Proxy — Structural

> Represents another object and controls access to it, potentially applying security, lazy loading, or interception. Spring AOP uses proxies for various features; calls that bypass the proxy may also bypass that behavior.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-chain-of-responsibility-comportamental"></a>

## Chain of Responsibility — Behavioral

> Chains handlers that process or forward a request. It is useful for filters and validation; ordering and stopping rules must be explicit to prevent unexpected paths.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-command-comportamental"></a>

## Command — Behavioral

> Encapsulates a request as an object, separating the requester from the executor. It facilitates queues, history, and scheduling; undo support requires its own logic and does not come automatically with the pattern.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-interpreter-comportamental"></a>

## Interpreter — Behavioral

> Represents rules in a language and interprets expressions in its grammar. It may work for simple rule languages, but large grammars generally call for more suitable parsing tools.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-iterator-comportamental"></a>

## Iterator — Behavioral

> Traverses a structure without exposing its internal representation. In Java, `Iterator` provides this contract; removal support and behavior under modification depend on the implementation.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-mediator-comportamental"></a>

## Mediator — Behavioral

> Centralizes coordination between objects that would otherwise interact directly in complex ways. It reduces dependencies among participants, but needs clear boundaries to avoid accumulating all the logic.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-memento-comportamental"></a>

## Memento — Behavioral

> Captures state so it can be restored without exposing internal details to clients. It is useful for history and undo, but large snapshots consume memory and do not automatically reverse external effects.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-observer-comportamental"></a>

## Observer — Behavioral

> Notifies interested parties when an object changes or emits an event. It reduces direct dependency on observers but requires failure and lifecycle handling; it does not guarantee asynchrony, persistence, or distributed delivery.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-state-comportamental"></a>

## State — Behavioral

> Encapsulates behavior that varies with an object's state. It helps with flows such as pending, paid, and canceled orders by making transitions explicit and reducing scattered conditionals.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-strategy-comportamental"></a>

## Strategy — Behavioral

> Encapsulates interchangeable algorithms under a common contract, such as shipping or discount calculations. It favors composition and OCP; the client or a policy selects the strategy.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-template-method-comportamental"></a>

## Template Method — Behavioral

> Defines an algorithm's skeleton in a base class while allowing individual steps to be specialized. It reuses a flow through inheritance but couples code to the superclass; Strategy provides variation through composition.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-visitor-comportamental"></a>

## Visitor — Behavioral

> Separates operations from a stable structure of types, such as a syntax tree. It simplifies adding operations, but adding element types generally requires changing visitors; I use it when that trade-off suits the domain.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-strategy-vs-state-vs-template-method"></a>

## Strategy vs State vs Template Method

> Strategy changes the chosen algorithm; State changes behavior based on state and transitions; Template Method preserves a flow while specializing steps through inheritance. I choose based on the nature of the variation, not class resemblance.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-adapter-vs-decorator-vs-proxy-vs-facade"></a>

## Adapter vs Decorator vs Proxy vs Facade

> Adapter translates contracts; Decorator adds behavior; Proxy controls access; Facade simplifies a subsystem. All may use delegation, but their intentions differ.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-repository-dto-e-mapper"></a>

## Repository, DTO, and Mapper

> Repository provides domain-oriented persistence access; DTO transfers data across boundaries; Mapper converts representations. These are common patterns in Java applications but are not in the GoF catalog and should not duplicate layers without benefit.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-padrao-de-projeto-vs-padrao-arquitetural"></a>

## Design Pattern vs Architectural Pattern

> Design patterns address collaboration among objects and components. Architectural patterns address broader organization and qualities; Saga and Outbox address distributed coordination and consistency and are covered in the architecture and microservices chapters.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-quando-nao-aplicar-um-padrao"></a>

## When Not to Apply a Pattern

> I avoid a pattern when it creates more indirection than value or anticipates variations without evidence. The solution must remain easy to explain, test, and modify; knowing a pattern's name is not a substitute for assessing its cost.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="design-patterns-resumo-de-entrevista"></a>

## Interview Summary

> I choose patterns based on the problem: Strategy for variable algorithms, Builder for complex construction, Adapter for integration, and Proxy for interception. I always explain the benefit, cost, and why a simpler solution would or would not suffice.

[↑ Topic Contents](#design-patterns-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-sumario"></a>

# 07. JVM Memory & Garbage Collection

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#java-jvm-memory-garbage-collection-visao-geral)
- [Heap](#java-jvm-memory-garbage-collection-1-heap)
- [Stack and Local Variables](#java-jvm-memory-garbage-collection-2-stack)
- [Metaspace and Native Memory](#java-jvm-memory-garbage-collection-metaspace-e-memoria-nativa)
- [Off-Heap Memory](#java-jvm-memory-garbage-collection-memoria-fora-da-heap)
- [Class Loading and JIT](#java-jvm-memory-garbage-collection-class-loading-e-jit)
- [Reachability and GC Roots](#java-jvm-memory-garbage-collection-2-regra-de-alcancabilidade)
- [Garbage Collector](#java-jvm-memory-garbage-collection-garbage-collector)
- [Generational Flow](#java-jvm-memory-garbage-collection-1-fluxo-geracional)
- [Stop-the-World, Throughput, and Latency](#java-jvm-memory-garbage-collection-stop-the-world-throughput-e-latencia)
- [G1: Young, Mixed, and Full GC](#java-jvm-memory-garbage-collection-3-full-gc)
- [G1 vs ZGC](#java-jvm-memory-garbage-collection-2-zgc)
- [Memory Leaks in Java](#java-jvm-memory-garbage-collection-memory-leak-em-java)
- [OutOfMemoryError vs StackOverflowError](#java-jvm-memory-garbage-collection-outofmemoryerror-vs-stackoverflowerror)
- [Heap Dump](#java-jvm-memory-garbage-collection-1-heap-dump)
- [Thread Dump](#java-jvm-memory-garbage-collection-2-thread-dump)
- [JFR, JMC, and GC Logs](#java-jvm-memory-garbage-collection-jfr-jmc-e-gc-logs)
- [Heap Tuning and Collector Selection](#java-jvm-memory-garbage-collection-zgc)
- [Interview Summary](#java-jvm-memory-garbage-collection-resposta-completa-de-entrevista)

---

<a id="java-jvm-memory-garbage-collection-visao-geral"></a>

## Overview

> The JVM has different memory areas. The heap stores objects and is managed by the garbage collector, while each thread has its own stack for method execution. In production, I start by investigating metrics and GC logs and use heap and thread dumps when necessary to identify the root cause.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-1-heap"></a>

## Heap

> The heap is a shared area where objects and arrays reside. It is managed by the garbage collector. If space is insufficient for new allocations and GC cannot reclaim enough memory, `OutOfMemoryError: Java heap space` may occur.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-2-stack"></a>

## Stack and Local Variables

> Each thread has a logical execution stack with method frames, local variables, and operands. Local references can point to heap objects; they are not class metadata stored in Metaspace. Excessive call depth can cause `StackOverflowError`.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-metaspace-e-memoria-nativa"></a>

## Metaspace and Native Memory

> In HotSpot, Metaspace stores class metadata in native memory. Local variables belong to stack frames, not Metaspace; too many retained class loaders can cause this area to grow and trigger memory failures.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-memoria-fora-da-heap"></a>

## Off-Heap Memory

> I would not automatically attribute an increase in process memory to the heap. If heap usage remains stable, I would investigate Metaspace, direct buffers, thread stacks, thread counts, and other native JVM memory areas.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-class-loading-e-jit"></a>

## Class Loading and JIT

> Class loaders load classes; the JVM verifies, prepares, and initializes types as needed. The JIT compiles frequently executed code into native instructions, so warm-up and execution profiles influence performance measurements.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-2-regra-de-alcancabilidade"></a>

## Reachability and GC Roots

> The garbage collector determines object lifetime through reachability. If a strong-reference path exists from a GC Root to an object, that object remains alive. When the path no longer exists, the object becomes eligible for collection.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-garbage-collector"></a>

## Garbage Collector

> The garbage collector manages heap memory. It identifies objects no longer reachable from GC Roots and reclaims their memory. Collectors such as G1 and ZGC use different strategies to balance throughput, latency, and resource consumption.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-1-fluxo-geracional"></a>

## Generational Flow

> In collectors such as G1, Eden and Survivor belong to the Young Generation. Objects are normally allocated in Eden; survivors may move through Survivor spaces and be promoted to Old. This is a learning model, not a mandatory sequence for all objects and collectors.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-stop-the-world-throughput-e-latencia"></a>

## Stop-the-World, Throughput, and Latency

> During a stop-the-world pause, application threads stop for a coordinated JVM operation. Throughput measures work performed per unit of time; latency measures the duration of operations. Collector choice depends on balancing these goals against available resources.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-3-full-gc"></a>

## G1: Young, Mixed, and Full GC

> G1 divides the heap into regions and attempts to control pauses by selecting regions to collect. It performs Young and Mixed collections; Mixed collections can also reclaim selected Old regions. Full GC is different: it involves the entire heap and tends to cause a much more significant pause.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-2-zgc"></a>

## G1 vs ZGC

> G1 aims to balance throughput and pause-time goals. ZGC prioritizes low latency and performs more work concurrently. Trade-offs should be measured under real workloads, observing CPU, memory, throughput, and latency SLOs.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-memory-leak-em-java"></a>

## Memory Leaks in Java

> A leak can happen when references keep objects alive longer than needed, despite GC. Examples include unbounded caches, forgotten listeners, and improper use of ThreadLocal; I would investigate retention and paths to GC Roots.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-outofmemoryerror-vs-stackoverflowerror"></a>

## OutOfMemoryError vs StackOverflowError

> `OutOfMemoryError` can indicate exhaustion of heap or other resources, such as Metaspace or thread creation. `StackOverflowError` generally indicates excessive call depth; the message and metrics guide the correct investigation.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-1-heap-dump"></a>

## Heap Dump

> A heap dump shows the objects on the heap and their reference relationships. I use it to find which objects retain memory and which reference path to a GC Root prevents collection.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-2-thread-dump"></a>

## Thread Dump

> A thread dump shows thread states and their stacks at a particular moment. It is useful for investigating blocking, deadlocks, contention, or where threads are spending time.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-jfr-jmc-e-gc-logs"></a>

## JFR, JMC, and GC Logs

> Java Flight Recorder collects runtime events with diagnostic-appropriate configuration, and Java Mission Control helps analyze them. GC logs show collection frequency, duration, and memory occupancy; I combine this evidence before tuning.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-zgc"></a>

## Heap Tuning and Collector Selection

> Increasing heap size may reduce GC pressure when the application truly needs more space, but it does not fix unintended retention. Similarly, switching to ZGC should not be automatic: I would select a collector based on latency targets, throughput, and actual workload measurements.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-jvm-memory-garbage-collection-resposta-completa-de-entrevista"></a>

## Interview Summary

> The JVM stores objects on the heap, per-thread execution stacks, and metadata in separate areas. GC reclaims objects without strong-reference paths from GC Roots. To diagnose problems, I observe GC, memory, and latency and use dumps and JFR before changing heap size or collector.

[↑ Topic Contents](#java-jvm-memory-garbage-collection-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-sumario"></a>

# 08. Concurrency in Java

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#java-concorrencia-em-java-visao-geral)
- [Concurrency vs Parallelism](#java-concorrencia-em-java-concorrencia-vs-paralelismo)
- [Threads: Platform vs Virtual](#java-concorrencia-em-java-1-thread)
- [Thread States](#java-concorrencia-em-java-estados-da-thread)
- [Runnable](#java-concorrencia-em-java-2-runnable)
- [Callable](#java-concorrencia-em-java-3-callable)
- [ExecutorService and Thread Pools](#java-concorrencia-em-java-executorservice-e-thread-pools)
- [Future](#java-concorrencia-em-java-4-future)
- [CompletableFuture](#java-concorrencia-em-java-trade-off)
- [Interruption and Cancellation](#java-concorrencia-em-java-interrupcao-e-cancelamento)
- [Atomicity, Visibility, and Ordering](#java-concorrencia-em-java-atomicidade-visibilidade-e-ordenacao)
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
- [Semaphore and CountDownLatch](#java-concorrencia-em-java-semaphore-e-countdownlatch)
- [AtomicInteger](#java-concorrencia-em-java-trade-off-4)
- [CAS and LongAdder](#java-concorrencia-em-java-cas-e-longadder)
- [ConcurrentHashMap](#java-concorrencia-em-java-5-concurrenthashmap)
- [ThreadLocal and Cleanup](#java-concorrencia-em-java-threadlocal-e-limpeza)
- [Virtual Threads: Benefits and Limitations](#java-concorrencia-em-java-5-trade-offs)
- [Virtual Threads: Pinning in Java 21 and 24+](#java-concorrencia-em-java-java-24)
- [Virtual Threads and Concurrency Control](#java-concorrencia-em-java-virtual-threads-e-controle-de-concorrencia)
- [Production Diagnostics](#java-concorrencia-em-java-diagnostico-em-producao)
- [Interview Summary](#java-concorrencia-em-java-resposta-completa-de-entrevista)

---

<a id="java-concorrencia-em-java-visao-geral"></a>

## Overview

> Concurrency in Java is the coordinated execution of multiple tasks over the same period. Java offers abstractions such as Threads, ExecutorService, locks, atomics, and concurrent collections. The main challenge is coordinating shared-state access while guaranteeing atomicity, visibility, and ordering without excessive contention.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-concorrencia-vs-paralelismo"></a>

## Concurrency vs Parallelism

> Concurrency allows multiple tasks to make progress over the same period; parallelism performs work simultaneously on distinct computing resources. An application can be concurrent even with only one CPU core.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-1-thread"></a>

## Threads: Platform vs Virtual

> A Thread represents a flow of execution. Platform threads are relatively expensive because they depend on operating-system threads, while virtual threads are much lighter and managed by the JVM.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-estados-da-thread"></a>

## Thread States

> Java thread states include NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, and TERMINATED. RUNNABLE does not imply continuous CPU use; BLOCKED concerns monitor acquisition, while other waits can appear under different states.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-2-runnable"></a>

## Runnable

> Runnable represents a task without a return value. It is appropriate when I want to execute an action concurrently but do not need the result.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-3-callable"></a>

## Callable

> Callable is similar to Runnable but can return a result and throw exceptions. When submitted to an ExecutorService, it normally provides a Future representing the eventual result.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-executorservice-e-thread-pools"></a>

## ExecutorService and Thread Pools

> ExecutorService separates task submission from execution and manages results and shutdown. With platform-thread pools, I size capacity, queues, and rejection policies; unbounded queues can turn overload into steadily increasing memory consumption.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-4-future"></a>

## Future

> Future represents the result of an asynchronous operation. The main concern is that `get()` may block until the operation completes.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-trade-off"></a>

## CompletableFuture

> CompletableFuture composes asynchronous operations without explicitly blocking at each step. It is useful for combining independent tasks, but overly complex flows can impair readability and observability.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-interrupcao-e-cancelamento"></a>

## Interruption and Cancellation

> Interruption is a cooperative request, not a forced stop. I handle `InterruptedException` by propagating it or restoring the interrupt flag where appropriate; canceling a Future does not guarantee that all work or external side effects have stopped.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-atomicidade-visibilidade-e-ordenacao"></a>

## Atomicity, Visibility, and Ordering

> Atomicity prevents observing a partially completed operation; visibility ensures updates are observed; ordering constrains how operations may be observed. These are distinct properties: seeing a counter does not make incrementing it atomic.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-java-memory-model"></a>

## Java Memory Model

> The JMM defines which memory effects threads can observe and which synchronization actions establish guarantees. Without an appropriate happens-before relationship, shared access can yield outcomes that violate the intuitive source-code order.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-2-happens-before"></a>

## Happens-before

> Happens-before specifies when the effects of an operation are guaranteed to be visible to another. For example, releasing a monitor happens-before a later acquisition of that monitor, and a volatile write happens-before a subsequent read of the same variable.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-race-condition"></a>

## Race Condition

> A race condition occurs when the outcome depends on an incorrect interleaving of tasks, such as two threads reading the same balance and writing conflicting updates. I protect compound operations and their invariants with synchronization or a design that avoids shared state.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-deadlock"></a>

## Deadlock

> Deadlock occurs when tasks wait cyclically for resources and none can progress. I use consistent acquisition order, small critical sections, and, when appropriate, timed attempts; thread dumps help identify the cycle.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-livelock"></a>

## Livelock

> In livelock, tasks keep taking actions, but their reactions prevent useful progress. I reduce symmetric retries with backoff, jitter, or a coordination rule that allows one task to advance.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-starvation"></a>

## Starvation

> Starvation occurs when a task waits indefinitely because others monopolize resources. I investigate fairness, priorities, critical-section length, and queues; fair policies may reduce risk at a possible throughput cost.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-contention"></a>

## Contention

> Contention is competition for the same resource, such as a lock, connection, or queue. I shorten critical sections, partition state, or limit concurrency, measuring the bottleneck before adding threads.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-trade-off-2"></a>

## synchronized

> `synchronized` provides mutual exclusion and visibility guarantees through a monitor. It is straightforward for protecting critical sections, but high contention on the same monitor may reduce parallelism and throughput.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-bom-para"></a>

## volatile

> `volatile` guarantees visibility and memory ordering for a variable but does not make compound operations atomic. It works well for flags and simple states, but does not replace locks or atomics for read-modify-write operations.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-trade-off-3"></a>

## ReentrantLock

> ReentrantLock provides mutual exclusion and options such as `tryLock`, timed waiting, interruption, and Conditions. After acquiring the lock, I release it in `finally`; the additional control increases complexity and does not automatically speed up contended sections.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-semaphore-e-countdownlatch"></a>

## Semaphore and CountDownLatch

> Semaphore uses permits to limit simultaneous access, helping protect a dependency. CountDownLatch waits until a set of tasks signals completion; its count cannot be reused after reaching zero.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-trade-off-4"></a>

## AtomicInteger

> AtomicInteger is appropriate for simple atomic operations on a shared integer. It avoids explicit locking in many cases but does not replace locks when invariants span multiple pieces of state.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-cas-e-longadder"></a>

## CAS and LongAdder

> Compare-and-set updates a value only if the expected state still matches, potentially retrying under contention. LongAdder distributes updates to reduce contention on counters, but its sum during concurrent updates is not an atomic global read suitable for invariants.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-5-concurrenthashmap"></a>

## ConcurrentHashMap

> ConcurrentHashMap is a thread-safe Map optimized for concurrency and provides atomic compound operations such as putIfAbsent, compute, and merge.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-threadlocal-e-limpeza"></a>

## ThreadLocal and Cleanup

> ThreadLocal associates a value with each thread without automatically making it shareable or safe. In pools, I call `remove` after use to avoid retention and context leakage; large numbers of virtual threads can also multiply per-thread costs.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-5-trade-offs"></a>

## Virtual Threads: Benefits and Limitations

> Virtual threads mainly improve scalability for I/O-bound workloads. They do not speed up CPU-bound work or remove external limits. If a database has 30 connections, creating thousands of virtual threads does not increase that limit.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-java-24"></a>

## Virtual Threads: Pinning in Java 21 and 24+

> In Java 21, a virtual thread could pin its carrier when blocking inside `synchronized`. This case was resolved starting with JDK 24. In Java 25, `synchronized` normally no longer causes that pinning, though native or foreign calls can still do so. [Java 21](https://docs.oracle.com/en/java/javase/21/core/virtual-threads.html) · [Java 25](https://docs.oracle.com/en/java/javase/25/core/virtual-threads.html)

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-virtual-threads-e-controle-de-concorrencia"></a>

## Virtual Threads and Concurrency Control

> Virtual threads suit many blocking I/O tasks and are typically created per task rather than pooled for reuse. I limit access to scarce resources with semaphores or dependency connection pools instead of assuming unlimited concurrency.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-diagnostico-em-producao"></a>

## Production Diagnostics

> In production, I start with symptoms and metrics. For suspected blocking or contention, I use thread dumps or JFR to identify thread states, locks, and stacks. I also inspect pools and external dependencies before blaming thread count alone.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="java-concorrencia-em-java-resposta-completa-de-entrevista"></a>

## Interview Summary

> I coordinate tasks with executors and protect shared state according to atomicity, visibility, and ordering requirements. I choose locks, atomics, and concurrent collections based on the invariants involved. Virtual threads help blocking I/O workloads but do not remove CPU or dependency limits.

[↑ Topic Contents](#java-concorrencia-em-java-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="build-dependencias-sumario"></a>

# 09. Builds and Dependencies — Maven and Gradle

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#build-dependencias-visao-geral)
- [Maven and pom.xml](#build-dependencias-maven-e-pom-xml)
- [Maven Lifecycle](#build-dependencias-ciclo-de-vida-do-maven)
- [compile, runtime, provided, and test Scopes](#build-dependencias-scopes-compile-runtime-provided-e-test)
- [Transitive Dependencies and Conflicts](#build-dependencias-dependencias-transitivas-e-conflitos)
- [dependencies vs dependencyManagement](#build-dependencias-dependencies-vs-dependencymanagement)
- [BOM and Parent POM](#build-dependencias-bom-e-parent-pom)
- [Plugins and Surefire vs Failsafe](#build-dependencias-plugins-e-surefire-vs-failsafe)
- [Gradle and Tasks](#build-dependencias-gradle-e-tasks)
- [Wrappers, Versions, and Toolchains](#build-dependencias-wrappers-versoes-e-toolchains)
- [JAR, Executable JAR, and WAR](#build-dependencias-jar-executable-jar-e-war)
- [Interview Summary](#build-dependencias-resumo-de-entrevista)

---

<a id="build-dependencias-visao-geral"></a>

## Overview

> Build tools automate compilation, testing, packaging, and dependency resolution. In Java, understanding the process helps diagnose differences among local development, pipelines, and production environments.

[↑ Topic Contents](#build-dependencias-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="build-dependencias-maven-e-pom-xml"></a>

## Maven and pom.xml

> `pom.xml` describes the project, its dependencies, plugins, and build configuration. Maven uses conventions and lifecycle phases; plugins perform tasks bound to those phases.

[↑ Topic Contents](#build-dependencias-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="build-dependencias-ciclo-de-vida-do-maven"></a>

## Maven Lifecycle

> `compile` compiles code, `test` runs tests configured for that phase, `package` builds the package, and `verify` runs later checks. `install` publishes to the local repository; `deploy` publishes remotely. Running a phase also executes preceding phases in the same lifecycle.

[↑ Topic Contents](#build-dependencias-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="build-dependencias-scopes-compile-runtime-provided-e-test"></a>

## compile, runtime, provided, and test Scopes

> `compile` makes dependencies available at compile time and runtime; `runtime` makes them available at runtime and during tests, but not main compilation. `provided` means the runtime environment is expected to supply the dependency; `test` is limited to test compilation and execution. [Reference](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html)

[↑ Topic Contents](#build-dependencias-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="build-dependencias-dependencias-transitivas-e-conflitos"></a>

## Transitive Dependencies and Conflicts

> A library may bring other dependencies transitively. Maven normally resolves conflicts using the nearest definition and, for ties, the first declaration; I use `dependency:tree`, exclusions, and managed versions to make the result explicit.

[↑ Topic Contents](#build-dependencias-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="build-dependencias-dependencies-vs-dependencymanagement"></a>

## dependencies vs dependencyManagement

> `dependencies` declares what a module uses. `dependencyManagement` centralizes versions and settings for dependencies when they are used without automatically adding them to the classpath.

[↑ Topic Contents](#build-dependencias-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="build-dependencias-bom-e-parent-pom"></a>

## BOM and Parent POM

> A BOM groups compatible dependency versions and may be imported into `dependencyManagement`. A Parent POM can also provide inherited build and plugin configuration; importing a BOM does not amount to inheriting an entire parent.

[↑ Topic Contents](#build-dependencias-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="build-dependencias-plugins-e-surefire-vs-failsafe"></a>

## Plugins and Surefire vs Failsafe

> Plugins run build tasks. Surefire generally handles unit tests; Failsafe organizes integration tests and their verification when configured. The phase name alone does not guarantee that every existing test will execute.

[↑ Topic Contents](#build-dependencias-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="build-dependencias-gradle-e-tasks"></a>

## Gradle and Tasks

> Gradle models builds as a task graph and offers Kotlin or Groovy scripts. Dependency, versioning, isolation, and reproducibility concerns remain important despite its more programmable configuration.

[↑ Topic Contents](#build-dependencias-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="build-dependencias-wrappers-versoes-e-toolchains"></a>

## Wrappers, Versions, and Toolchains

> Wrappers pin the build-tool version; toolchains select the JDK for the build. I also align the target version, for example with `--release`, because the installed Java version does not guarantee compatibility of the generated artifact.

[↑ Topic Contents](#build-dependencias-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="build-dependencias-jar-executable-jar-e-war"></a>

## JAR, Executable JAR, and WAR

> A JAR packages classes and resources; an executable JAR may include startup configuration and dependencies depending on the tooling. A WAR targets the Servlet-container web application model. Packaging must match the deployment environment.

[↑ Topic Contents](#build-dependencias-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="build-dependencias-resumo-de-entrevista"></a>

## Interview Summary

> I treat builds and dependencies as part of application behavior. I control versions, scopes, plugins, and JDKs, inspect transitive conflicts, and produce reproducible artifacts before promoting them across environments.

[↑ Topic Contents](#build-dependencias-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-sumario"></a>

# 10. SQL & Relational Databases

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [SQL Overview](#sql-visao-geral-sql)
- [SELECT, INSERT, UPDATE, and DELETE](#sql-sql-bancos-relacionais-select-insert-update-e-delete)
- [Primary Key, Unique, and Foreign Key](#sql-primary-key-unique-e-foreign-key)
- [NOT NULL, CHECK, and Integrity](#sql-sql-bancos-relacionais-not-null-check-e-integridade)
- [Normalization vs Denormalization](#sql-normalizacao-vs-desnormalizacao)
- [NULL](#sql-null)
- [Logical Query Processing Order](#sql-sql-bancos-relacionais-ordem-logica-de-uma-consulta)
- [INNER JOIN, LEFT JOIN, RIGHT JOIN, and FULL JOIN](#sql-inner-join-left-join-right-join-e-full-join)
- [WHERE vs HAVING](#sql-where-vs-having)
- [GROUP BY](#sql-group-by)
- [CTE vs Subquery](#sql-cte-vs-subquery)
- [Window Functions](#sql-window-functions)
- [EXISTS vs IN vs JOIN](#sql-exists-vs-in-vs-join)
- [NOT IN and NULL](#sql-sql-bancos-relacionais-not-in-e-null)
- [UNION vs UNION ALL](#sql-union-vs-union-all)
- [DELETE vs TRUNCATE vs DROP](#sql-delete-vs-truncate-vs-drop)
- [Prepared Statements and SQL Injection](#sql-prepared-statements-e-sql-injection)
- [Indexes](#sql-indices)
- [Composite Indexes and Column Order](#sql-indice-composto-e-ordem-das-colunas)
- [Covering, Partial, and Functional Indexes](#sql-sql-bancos-relacionais-indice-de-cobertura-parcial-e-funcional)
- [Selectivity and Cardinality](#sql-seletividade-e-cardinalidade)
- [Sargability](#sql-sargabilidade)
- [When an Index May Not Be Used](#sql-quando-um-indice-pode-nao-ser-usado)
- [Execution Plan](#sql-execution-plan)
- [Optimizing Queries in Production](#sql-otimizacao-de-query-em-producao)
- [OFFSET vs Keyset Pagination](#sql-paginacao-offset-vs-keyset)
- [N+1 Queries](#sql-n-1-queries)
- [ACID](#sql-acid)
- [Isolation Levels](#sql-isolation-levels)
- [Dirty Reads, Non-repeatable Reads, and Phantom Reads](#sql-sql-bancos-relacionais-dirty-read-non-repeatable-read-e-phantom-read)
- [Lost Update and Write Skew](#sql-sql-bancos-relacionais-lost-update-e-write-skew)
- [MVCC](#sql-mvcc)
- [PostgreSQL vs Oracle: Isolation](#sql-sql-bancos-relacionais-postgresql-vs-oracle-isolamento)
- [Locks](#sql-locks)
- [Optimistic vs Pessimistic Concurrency](#sql-optimistic-vs-pessimistic-concurrency)
- [Atomic Conditional Updates](#sql-sql-bancos-relacionais-atualizacao-condicional-atomica)
- [Database Deadlocks](#sql-deadlock-no-banco)
- [Long-running Transactions](#sql-transacao-longa)
- [Connection Pools and Saturation](#sql-sql-bancos-relacionais-connection-pool-e-saturacao)
- [Interview Summary](#sql-resposta-completa-de-entrevista-sql)

---

<a id="sql-visao-geral-sql"></a>

## SQL Overview

> SQL is the language used to query and manipulate relational database data. In interviews, I divide the subject into modeling, queries, indexes, transactions, and concurrency. For performance, I look beyond the query itself and consider cardinality, selectivity, indexes, data volume, and execution plans.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-select-insert-update-e-delete"></a>

## SELECT, INSERT, UPDATE, and DELETE

> `SELECT` retrieves data; `INSERT` adds rows; `UPDATE` changes them; and `DELETE` removes them. I define filters and transactional boundaries before making changes, accounting for constraints, indexes, and the actual number of affected rows.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-primary-key-unique-e-foreign-key"></a>

## Primary Key, Unique, and Foreign Key

> A primary key uniquely identifies each row and does not allow `NULL`. A `UNIQUE` constraint also enforces uniqueness, but its `NULL` semantics may vary by database. A foreign key enforces referential integrity between tables. I use database constraints because consistency must not rely solely on the application.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-not-null-check-e-integridade"></a>

## NOT NULL, CHECK, and Integrity

> `NOT NULL` requires a value; `CHECK` restricts values according to an expression; keys and uniqueness provide further integrity. Rules the database can enforce should stay protected against concurrent access and clients other than the application.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-normalizacao-vs-desnormalizacao"></a>

## Normalization vs Denormalization

> Normalization reduces redundancy and update anomalies, usually separating entities and relationships into coherent tables. Denormalization duplicates or precomputes data to reduce joins or speed up reads. I start with a normalized design and denormalize only when a proven performance requirement exists, accepting the additional consistency cost.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-null"></a>

## NULL

> `NULL` represents absence or an unknown value and should not be compared with `=`. I use `IS NULL` and `IS NOT NULL`. SQL logic is three-valued—true, false, and unknown—which affects filtering, joins, and aggregation.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-ordem-logica-de-uma-consulta"></a>

## Logical Query Processing Order

> As a reasoning model, I consider FROM/JOIN, WHERE, GROUP BY, HAVING, SELECT, and ORDER BY, followed by result limiting. The optimizer may choose a different physical order while preserving query semantics.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-inner-join-left-join-right-join-e-full-join"></a>

## INNER JOIN, LEFT JOIN, RIGHT JOIN, and FULL JOIN

> `INNER JOIN` returns only rows that match on both sides. `LEFT JOIN` preserves all rows from the left table and returns `NULL` for missing matches on the right. `RIGHT JOIN` does the reverse, while `FULL JOIN` preserves unmatched rows from either side. I choose joins based on relationship semantics, not assumed performance.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-where-vs-having"></a>

## WHERE vs HAVING

> `WHERE` filters rows before aggregation. `HAVING` filters groups after `GROUP BY`. When a predicate does not depend on an aggregate function, I prefer `WHERE` to reduce the data set before aggregation.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-group-by"></a>

## GROUP BY

> `GROUP BY` groups rows for aggregate functions such as `COUNT`, `SUM`, `AVG`, `MIN`, and `MAX`. Nonaggregated selected columns must comply with the database's grouping rules. In production, I also consider the cost of sorting or aggregating large volumes.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-cte-vs-subquery"></a>

## CTE vs Subquery

> CTEs improve readability and help structure complex queries, including recursion where recursive CTEs are supported. Subqueries may suffice for small cases. I do not assume a CTE is automatically faster or slower: optimizers may treat them differently, so I validate performance through the execution plan.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-window-functions"></a>

## Window Functions

> Window functions calculate values over related rows without collapsing the result as `GROUP BY` does. Examples include `ROW_NUMBER`, `RANK`, `LAG`, `LEAD`, and aggregates using `OVER`. They are useful for rankings, comparisons with prior rows, and running totals without multiple subqueries.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-exists-vs-in-vs-join"></a>

## EXISTS vs IN vs JOIN

> `EXISTS`, `IN`, and `JOIN` express different needs, although modern optimizers may generate equivalent plans in some cases. I use `EXISTS` to test existence, `JOIN` to combine data, and `IN` where membership semantics are clear. For performance, I compare plans rather than follow a universal rule.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-not-in-e-null"></a>

## NOT IN and NULL

> If a `NOT IN` set contains `NULL`, comparisons may evaluate to unknown and unexpectedly filter out rows. For checking absence, `NOT EXISTS` often communicates intent better, provided the correlation and null-handling rules are correct.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-union-vs-union-all"></a>

## UNION vs UNION ALL

> `UNION` combines result sets while removing duplicates; `UNION ALL` simply concatenates them. Since duplicate removal adds work, I prefer `UNION ALL` when duplicates are acceptable or impossible by domain logic.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-delete-vs-truncate-vs-drop"></a>

## DELETE vs TRUNCATE vs DROP

> `DELETE` removes rows and supports a `WHERE` filter. `TRUNCATE` removes table data more broadly, with database-specific behavior and logging. `DROP` removes the database object itself. Transaction, identity, and rollback details differ among database systems, so I avoid overgeneralizing.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-prepared-statements-e-sql-injection"></a>

## Prepared Statements and SQL Injection

> To prevent SQL injection, I do not concatenate user input into queries. I use parameters through prepared statements or APIs that perform binding correctly. This separates data from SQL code; input validation remains important but does not replace parameterization.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-indices"></a>

## Indexes

> An index is an auxiliary structure that reduces data lookup cost, often avoiding a full table scan. Its trade-off is disk space and additional `INSERT`, `UPDATE`, and `DELETE` work to maintain the index. More indexes are not automatically better.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-indice-composto-e-ordem-das-colunas"></a>

## Composite Indexes and Column Order

> Column order matters in a composite index. I choose it based on actual filter, join, and ordering patterns. In B-tree indexes, queries tend to make better use of leading prefixes; an index on `(cliente_id, status, data)` is not equivalent to `(status, cliente_id, data)`.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-indice-de-cobertura-parcial-e-funcional"></a>

## Covering, Partial, and Functional Indexes

> An index may contain enough data to reduce table access, cover only some rows, or index expressions where supported. Its usefulness depends on the query and DBMS; validation against a real execution plan is essential.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-seletividade-e-cardinalidade"></a>

## Selectivity and Cardinality

> Cardinality describes the number of distinct values in a column, while selectivity describes how much a predicate reduces the row set. Indexes tend to help when the query eliminates a significant fraction of rows. For low-cardinality columns, the optimizer may prefer a scan depending on distribution and volume.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-sargabilidade"></a>

## Sargability

> A sargable condition lets the database use an index efficiently to locate a data range. Applying a function directly to an indexed column, for example, can prevent or reduce index usage. I aim to keep indexed columns searchable in predicates and confirm results in the execution plan.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-quando-um-indice-pode-nao-ser-usado"></a>

## When an Index May Not Be Used

> An index may be skipped when a filter returns most rows, a function or conversion is applied to the indexed column, statistics are poor, or another plan is estimated to be cheaper. I check the execution plan rather than assume creating an index guarantees its use.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-execution-plan"></a>

## Execution Plan

> The execution plan shows how the optimizer intends to execute a query: scans, seeks, joins, sorts, row estimates, and relative costs. For a slow query, I compare estimates against actual row counts and check unexpected scans, expensive joins, sorts, missing indexes, and statistics. The goal is finding the cause, not merely forcing an index.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-otimizacao-de-query-em-producao"></a>

## Optimizing Queries in Production

> I begin with evidence: latency, frequency, returned data volume, and execution plans. I then inspect filters, joins, indexes, cardinality, statistics, rows read versus returned, and unnecessary O(n) work. Only then do I consider rewriting queries, changing indexes, pagination, or remodeling access.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-paginacao-offset-vs-keyset"></a>

## OFFSET vs Keyset Pagination

> `OFFSET/LIMIT` pagination is simple, but high offsets can force the database to scan and discard many rows and produce inconsistent page views as data changes. Keyset pagination uses the last sorted key as a cursor and generally scales better for large data sets, given stable ordering and appropriate indexes.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-n-1-queries"></a>

## N+1 Queries

> N+1 occurs when an initial query loads a collection of records and additional queries run for each item. This increases round trips and latency. I detect it through logs or APM and address it using joins, batching, or specialized queries and loading strategies as appropriate.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-acid"></a>

## ACID

> ACID stands for Atomicity, Consistency, Isolation, and Durability. Atomicity ensures the entire transaction applies or rolls back; consistency preserves rules and constraints; isolation controls interference among concurrent transactions; durability ensures a confirmed commit survives failures according to the database guarantees.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-isolation-levels"></a>

## Isolation Levels

> The classic levels are `READ UNCOMMITTED`, `READ COMMITTED`, `REPEATABLE READ`, and `SERIALIZABLE`. Higher isolation reduces the possibility of dirty reads, non-repeatable reads, and phantom reads, potentially at greater concurrency cost. Exact behavior depends on the database and its locking or MVCC implementation.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-dirty-read-non-repeatable-read-e-phantom-read"></a>

## Dirty Reads, Non-repeatable Reads, and Phantom Reads

> A dirty read observes uncommitted changes. A non-repeatable read sees a change to the same row between reads; a phantom read sees a change in the set of rows matching a filter. Prevention depends on isolation level and database implementation.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-lost-update-e-write-skew"></a>

## Lost Update and Write Skew

> A lost update happens when one write improperly overwrites another. Write skew can occur when transactions update different rows after reading a shared condition, violating a joint rule; versioning one row does not solve every cross-row invariant.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-mvcc"></a>

## MVCC

> MVCC, or Multi-Version Concurrency Control, retains row versions so reads and writes can proceed with less blocking at certain isolation levels. It improves concurrency but does not eliminate conflicts, locks, or the need to understand database-specific isolation.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-postgresql-vs-oracle-isolamento"></a>

## PostgreSQL vs Oracle: Isolation

> Both commonly default to READ COMMITTED, but their guarantees are not interchangeable. PostgreSQL treats READ UNCOMMITTED as READ COMMITTED and supports REPEATABLE READ; Oracle does not offer exactly the same set of levels. I check snapshots, conflicts, and retry requirements in each database's documentation. [PostgreSQL](https://www.postgresql.org/docs/current/transaction-iso.html) · [Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/26/cncpt/data-concurrency-and-consistency.html)

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-locks"></a>

## Locks

> Locks coordinate concurrent access to data to preserve consistency. Read and write locks may exist at different granularities depending on the database. Problems arise when transactions hold locks too long, increasing wait time, latency, and deadlock risk. I keep transactions short and acquire resources in a predictable order.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-optimistic-vs-pessimistic-concurrency"></a>

## Optimistic vs Pessimistic Concurrency

> Optimistic concurrency assumes conflicts are rare and detects concurrent changes, for example with a version column. Pessimistic concurrency locks resources before modifying them. I favor optimistic control when conflicts are rare and pessimistic control when conflict cost is high and expected contention justifies locking.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-atualizacao-condicional-atomica"></a>

## Atomic Conditional Updates

> I can enforce a rule with one update, such as reducing inventory only when sufficient stock exists. I check affected-row counts and use transactions and constraints as needed, avoiding the vulnerable gap between separate read and write operations.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-deadlock-no-banco"></a>

## Database Deadlocks

> Deadlocks occur when transactions form a circular wait for locks. Databases generally detect the cycle and abort one transaction. To reduce them, I keep transactions short, acquire resources consistently, use appropriate indexes to avoid locking unnecessary rows, and prepare to retry a victim transaction when appropriate.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-transacao-longa"></a>

## Long-running Transactions

> Long transactions hold resources, versions, or locks longer, increasing database contention and resource consumption. In backend services, I keep transactional boundaries around only the work that requires atomicity and avoid slow remote calls within a transaction where possible.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-sql-bancos-relacionais-connection-pool-e-saturacao"></a>

## Connection Pools and Saturation

> Connection pools reuse connections and limit concurrent database operations. I monitor waits for connections, long transactions, and database capacity; increasing pool size may simply shift the overload to the server.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="sql-resposta-completa-de-entrevista-sql"></a>

## Interview Summary

> With relational databases, I combine data modeling and constraints with queries, indexes, and transactions. I select isolation and concurrency control based on invariants. For performance, I start with actual queries and execution plans, considering volume, statistics, locks, and read/write costs.

[↑ Topic Contents](#sql-sql-bancos-relacionais-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-sumario"></a>

# 11. HTTP and REST APIs

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#http-rest-visao-geral)
- [Resources, URIs, and Contracts](#http-rest-recursos-uris-e-contratos)
- [GET, POST, PUT, PATCH, and DELETE](#http-rest-get-post-put-patch-e-delete)
- [Safe and Idempotent Methods](#http-rest-metodos-seguros-e-idempotentes)
- [HTTP Status Codes](#http-rest-codigos-de-status-http)
- [Headers, Content-Type, and Accept](#http-rest-headers-content-type-e-accept)
- [REST Statelessness](#http-rest-rest-stateless)
- [Pagination, Filtering, and Sorting](#http-rest-paginacao-filtros-e-ordenacao)
- [Errors and Problem Details](#http-rest-erros-e-problem-details)
- [OpenAPI and Contract Documentation](#http-rest-openapi-e-documentacao-de-contrato)
- [Idempotency Keys in APIs](#http-rest-idempotency-key-em-apis)
- [HTTP Caching, ETag, and If-Match](#http-rest-cache-http-etag-e-if-match)
- [TLS and Transport Security](#http-rest-tls-e-seguranca-de-transporte)
- [DNS, Connections, and Timeouts](#http-rest-dns-conexoes-e-timeouts)
- [REST, RPC, and gRPC](#http-rest-rest-rpc-e-grpc)
- [Interview Summary](#http-rest-resumo-de-entrevista)

---

<a id="http-rest-visao-geral"></a>

## Overview

> HTTP defines message exchange between clients and servers; REST is an architectural style based on resources and a uniform interface. An HTTP API returning JSON does not become RESTful merely by using URLs and controllers.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-recursos-uris-e-contratos"></a>

## Resources, URIs, and Contracts

> I model resources with stable identifiers and clear input, output, and error contracts. I avoid directly exposing persistence entities because that couples the API to the database schema and may reveal unintended data.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-get-post-put-patch-e-delete"></a>

## GET, POST, PUT, PATCH, and DELETE

> `GET` retrieves; `POST` submits processing or creation; `PUT` creates or replaces the target resource representation; `PATCH` applies partial changes; `DELETE` requests removal. The meaning should follow HTTP contracts and business rules.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-metodos-seguros-e-idempotentes"></a>

## Safe and Idempotent Methods

> Safe methods such as `GET` do not request business-state changes. `PUT` and `DELETE` are idempotent by semantics, although responses may vary; `POST` and `PATCH` can implement idempotency mechanisms, but the methods do not guarantee idempotency.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-codigos-de-status-http"></a>

## HTTP Status Codes

> `200` indicates success, `201` creation, and `204` success without a body; `400` invalid input, `401` missing valid authentication, `403` forbidden access, and `404` resource not found. `409` denotes conflict, `429` too many requests, and `5xx` server errors.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-headers-content-type-e-accept"></a>

## Headers, Content-Type, and Accept

> `Content-Type` identifies the submitted format; `Accept` specifies acceptable response formats. Headers also carry authentication, caching, and tracing metadata; I treat them as part of the contract, not substitutes for authorization.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-rest-stateless"></a>

## REST Statelessness

> Each request should carry the context needed to be understood without relying on conversational session state on the server. Stateless does not mean having no database or business state; it means reducing dependency on interaction context stored between calls.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-paginacao-filtros-e-ordenacao"></a>

## Pagination, Filtering, and Sorting

> I define page limits, allowed filters, and stable sorting. For large data sets, cursors may be more efficient than offsets; a unique tie-breaker such as `id` avoids ambiguity when multiple records share the same date.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-erros-e-problem-details"></a>

## Errors and Problem Details

> I standardize error responses with a code, useful description, and trace identifier. Problem Details provides a common format for HTTP APIs; I avoid returning stack traces, internal details, or sensitive information.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-openapi-e-documentacao-de-contrato"></a>

## OpenAPI and Contract Documentation

> OpenAPI describes routes, parameters, schemas, responses, and security mechanisms. Documentation should evolve with the API and support validation; a published contract without compatibility tests can become outdated.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-idempotency-key-em-apis"></a>

## Idempotency Keys in APIs

> An idempotency key identifies retries of the same operation. I record the key, result, and associated request atomically, defining expiration and concurrent-call handling; reuse of a key with different content should be detected.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-cache-http-etag-e-if-match"></a>

## HTTP Caching, ETag, and If-Match

> `Cache-Control` guides response reuse and `ETag` identifies a representation version. `If-None-Match` supports revalidation; `If-Match` conditions a change on the expected version and can prevent lost updates.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-tls-e-seguranca-de-transporte"></a>

## TLS and Transport Security

> TLS protects confidentiality and integrity in transit and authenticates the server using certificates. It does not replace authorization, input validation, or protection of data after it reaches the application.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-dns-conexoes-e-timeouts"></a>

## DNS, Connections, and Timeouts

> A remote call can spend time resolving DNS, establishing a connection, negotiating TLS, and reading data. I set limits appropriate to each client as well as an overall deadline because limiting only read time can leave other stages unprotected.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-rest-rpc-e-grpc"></a>

## REST, RPC, and gRPC

> REST emphasizes resources and HTTP semantics; RPC expresses operation calls. gRPC commonly offers typed contracts and streaming with Protobuf, but requires evaluating client support, infrastructure, and contract-evolution strategy.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="http-rest-resumo-de-entrevista"></a>

## Interview Summary

> I design APIs with consistent resources, contracts, statuses, and errors. I consider authentication, authorization, idempotency, pagination, compatibility, and timeouts from the start because an HTTP call is also a failure-prone boundary.

[↑ Topic Contents](#http-rest-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-ecossistema-spring-visao-geral-sumario"></a>

# 12. Spring Ecosystem — Overview

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Spring Ecosystem Summary](#spring-ecossistema-spring-visao-geral-resposta-de-60-segundos-sobre-o-ecossistema)

---

<a id="spring-ecossistema-spring-visao-geral-resposta-de-60-segundos-sobre-o-ecossistema"></a>

## Spring Ecosystem Summary

> Spring Core provides IoC and beans; Boot simplifies configuration and operations; MVC handles HTTP; Data integrates persistence; Security controls access; Cloud integrates distributed-system patterns. I study Java, HTTP, and database fundamentals first to understand the guarantees and limitations of these abstractions.

[↑ Topic Contents](#spring-ecossistema-spring-visao-geral-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-core-sumario"></a>

# 13. Spring Core

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#spring-spring-core-visao-geral)
- [IoC and Dependency Injection](#spring-spring-core-regra-pratica)
- [Constructor vs Field Injection](#spring-spring-core-injecao-por-construtor-vs-campo)
- [Beans, Containers, and Lifecycle](#spring-spring-core-5-ciclo-de-vida)
- [@Component, @Service, @Repository, and @Bean](#spring-spring-core-component-service-repository-e-bean)
- [@Configuration and Component Scanning](#spring-spring-core-configuration-e-component-scan)
- [singleton, prototype, and request Scopes](#spring-spring-core-escopos-singleton-prototype-e-request)
- [@Primary, @Qualifier, and @Lazy](#spring-spring-core-trade-off)
- [Proxies and Self-invocation](#spring-spring-core-trade-off-2)
- [AOP, JDK Proxies, and Class-based Proxies](#spring-spring-core-aop-jdk-proxy-e-proxy-de-classe)
- [@Async and Execution Context](#spring-spring-core-async-e-contexto-de-execucao)
- [Circular Dependencies](#spring-spring-core-dependencia-circular)
- [Configuration and Local Events](#spring-spring-core-atencao)
- [Interview Summary](#spring-spring-core-resposta-completa-de-entrevista)

---

<a id="spring-spring-core-visao-geral"></a>

## Overview

> Spring Core provides the IoC container and Dependency Injection mechanism. Instead of classes creating dependencies directly, the container manages those objects as beans and injects the required dependencies. This reduces coupling and simplifies tests, configuration, and implementation replacement.

[↑ Topic Contents](#spring-spring-core-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-core-regra-pratica"></a>

## IoC and Dependency Injection

> IoC transfers control over object creation and management to the container. Dependency Injection is how the container supplies those dependencies. I prefer constructor injection because it makes the contract explicit and simplifies testing.

[↑ Topic Contents](#spring-spring-core-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-core-injecao-por-construtor-vs-campo"></a>

## Constructor vs Field Injection

> Constructor injection makes mandatory dependencies explicit and supports final fields and straightforward tests. Field injection hides part of the contract and relies on infrastructure to populate fields; I use setters when a dependency is genuinely optional and the design calls for it.

[↑ Topic Contents](#spring-spring-core-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-core-5-ciclo-de-vida"></a>

## Beans, Containers, and Lifecycle

> A bean is an object managed by the Spring container. Its lifecycle may include instantiation, dependency injection, post-processing, initialization, and destruction. Singleton scope applies per bean definition and container; it does not guarantee thread safety or justify storing request state in services.

[↑ Topic Contents](#spring-spring-core-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-core-component-service-repository-e-bean"></a>

## @Component, @Service, @Repository, and @Bean

> `@Component` marks a component for scanning; `@Service` and `@Repository` express specialized roles. `@Bean` explicitly registers an object returned from a method and is useful for external libraries and controlled creation.

[↑ Topic Contents](#spring-spring-core-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-core-configuration-e-component-scan"></a>

## @Configuration and Component Scanning

> `@Configuration` organizes bean definitions. Component scanning discovers components in configured packages; scanning too broadly or incorrectly can load unintended configurations or omit required dependencies.

[↑ Topic Contents](#spring-spring-core-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-core-escopos-singleton-prototype-e-request"></a>

## singleton, prototype, and request Scopes

> Singleton reuses a bean within the container; prototype creates an instance per container request; request scope associates a bean with an HTTP request. Injecting a prototype directly into a singleton does not create a new instance on every method call.

[↑ Topic Contents](#spring-spring-core-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-core-trade-off"></a>

## @Primary, @Qualifier, and @Lazy

> When multiple beans share a type, I use `@Primary` to establish a default or `@Qualifier` for explicit selection. `@Lazy` delays initialization but should be used deliberately.

[↑ Topic Contents](#spring-spring-core-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-core-trade-off-2"></a>

## Proxies and Self-invocation

> Spring uses proxies for features such as transactions, caching, and security. The proxy intercepts bean calls and adds behavior. A classic pitfall is self-invocation because internal calls may not pass through the proxy.

[↑ Topic Contents](#spring-spring-core-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-core-aop-jdk-proxy-e-proxy-de-classe"></a>

## AOP, JDK Proxies, and Class-based Proxies

> AOP separates cross-cutting concerns such as transactions and metrics. In Spring's usual model, interface-based or subclass-based proxies intercept calls; final methods and classes may limit subclass proxies, and internal calls do not go through another proxy.

[↑ Topic Contents](#spring-spring-core-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-core-async-e-contexto-de-execucao"></a>

## @Async and Execution Context

> `@Async` delegates execution to an executor when calls pass through the configured mechanism. The caller's transaction and ThreadLocals are not automatically propagated to the other thread; I configure error handling, capacity, and context explicitly.

[↑ Topic Contents](#spring-spring-core-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-core-dependencia-circular"></a>

## Circular Dependencies

> Circular dependencies occur when components need each other to be constructed or used. I generally review responsibilities and extract the appropriate collaboration; lazy initialization may defer the problem without fixing the coupling.

[↑ Topic Contents](#spring-spring-core-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-core-atencao"></a>

## Configuration and Local Events

> Spring supports externalized configuration, profile-specific configuration, and local event publication. I distinguish in-process events from distributed messaging.

[↑ Topic Contents](#spring-spring-core-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-core-resposta-completa-de-entrevista"></a>

## Interview Summary

> Spring Core provides IoC, dependency injection, and bean management. I prefer constructor injection, understand scopes, and avoid mutable shared state. Since transactions and other features may depend on proxies, I consider which calls actually pass through interception.

[↑ Topic Contents](#spring-spring-core-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-boot-sumario"></a>

# 14. Spring Boot

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#spring-spring-boot-visao-geral)
- [@SpringBootApplication](#spring-spring-boot-springbootapplication)
- [Starters and Dependency Management](#spring-spring-boot-starters-e-dependency-management)
- [Auto-configuration and Back-off](#spring-spring-boot-2-back-off)
- [@ConfigurationProperties vs @Value](#spring-spring-boot-3-profiles)
- [Profiles and External Configuration](#spring-spring-boot-profiles-e-configuracao-externa)
- [DevTools](#spring-spring-boot-devtools)
- [Embedded Server](#spring-spring-boot-embedded-server)
- [Actuator and Endpoint Security](#spring-spring-boot-3-seguranca-dos-endpoints)
- [Startup and Diagnostics](#spring-spring-boot-startup-e-diagnostico)
- [Interview Summary](#spring-spring-boot-resposta-completa-de-entrevista)

---

<a id="spring-spring-boot-visao-geral"></a>

## Overview

> Spring Boot does not replace the Spring Framework. It reduces manual configuration through auto-configuration, starters, embedded servers, external configuration, and operational tools such as Actuator.

[↑ Topic Contents](#spring-spring-boot-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-boot-springbootapplication"></a>

## @SpringBootApplication

> Combines Boot configuration, enabling auto-configuration, and component scanning. The main class's package influences default scanning; placing it correctly prevents missing components or unexpected configuration.

[↑ Topic Contents](#spring-spring-boot-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-boot-starters-e-dependency-management"></a>

## Starters and Dependency Management

> Starters group dependencies for capabilities such as web or persistence; Boot's version management maintains a compatible set. A starter does not implement the feature by itself, and overriding versions requires compatibility checks.

[↑ Topic Contents](#spring-spring-boot-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-boot-2-back-off"></a>

## Auto-configuration and Back-off

> Auto-configuration is conditional. Boot examines the classpath, properties, and existing beans and registers defaults when appropriate. When I supply custom configuration, many auto-configurations back off.

[↑ Topic Contents](#spring-spring-boot-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-boot-3-profiles"></a>

## @ConfigurationProperties vs @Value

> For external configuration, I prefer `@ConfigurationProperties` for groups of properties. It improves typing, validation, and maintainability compared with scattering `@Value` annotations.

[↑ Topic Contents](#spring-spring-boot-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-boot-profiles-e-configuracao-externa"></a>

## Profiles and External Configuration

> Profiles select configurations for specific conditions, such as environments. I externalize values and understand configuration-source precedence; secrets should not be committed to repositories or treated merely as differences between profile files.

[↑ Topic Contents](#spring-spring-boot-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-boot-devtools"></a>

## DevTools

> DevTools assists development with features such as restarting when relevant changes are detected. It is a local productivity tool, not a substitute for reproducible builds, tests, or a production update strategy.

[↑ Topic Contents](#spring-spring-boot-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-boot-embedded-server"></a>

## Embedded Server

> An embedded server packages the application and server together, simplifying deployment and operations.

[↑ Topic Contents](#spring-spring-boot-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-boot-3-seguranca-dos-endpoints"></a>

## Actuator and Endpoint Security

> Actuator provides operational endpoints and metrics integration. In production, I expose only what is necessary, secure sensitive endpoints, and use health/readiness/liveness checks and metrics for observability.

[↑ Topic Contents](#spring-spring-boot-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-boot-startup-e-diagnostico"></a>

## Startup and Diagnostics

> To diagnose auto-configuration, I check conditions, classpath, properties, and existing beans. Behavior typically results from whether a condition was met.

[↑ Topic Contents](#spring-spring-boot-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-boot-resposta-completa-de-entrevista"></a>

## Interview Summary

> Spring Boot simplifies configuration with starters, conditional auto-configuration, and dependency management. I externalize properties, validate settings, and use Actuator for operations. To diagnose startup, I inspect conditions, beans, classpath, and exposed endpoint security.

[↑ Topic Contents](#spring-spring-boot-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-mvc-sumario"></a>

# 15. Spring MVC

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#spring-spring-mvc-visao-geral)
- [DispatcherServlet and HttpMessageConverter](#spring-spring-mvc-4-httpmessageconverter)
- [Controller Responsibilities](#spring-spring-mvc-regra-pratica)
- [@Controller vs @RestController](#spring-spring-mvc-controller-vs-restcontroller)
- [@PathVariable, @RequestParam, and @RequestBody](#spring-spring-mvc-pathvariable-requestparam-e-requestbody)
- [Bean Validation](#spring-spring-mvc-validacao)
- [@RestControllerAdvice and Error Handling](#spring-spring-mvc-2-controlleradvice)
- [Filter vs Interceptor](#spring-spring-mvc-interceptor)
- [Spring MVC vs WebFlux](#spring-spring-mvc-regra-pratica-2)
- [Interview Summary](#spring-spring-mvc-resposta-completa-de-entrevista)

---

<a id="spring-spring-mvc-visao-geral"></a>

## Overview

> Spring MVC is Spring's Servlet-based web framework. It uses DispatcherServlet as a front controller, resolves handlers, invokes controllers, and converts requests and responses through message converters.

[↑ Topic Contents](#spring-spring-mvc-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-mvc-4-httpmessageconverter"></a>

## DispatcherServlet and HttpMessageConverter

> DispatcherServlet receives the request, uses HandlerMapping to find a controller, and HandlerAdapter to invoke it. HttpMessageConverters transform HTTP request and response bodies into and from Java objects.

[↑ Topic Contents](#spring-spring-mvc-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-mvc-regra-pratica"></a>

## Controller Responsibilities

> A controller should mainly handle routes, input, validation, HTTP status, and output. Significant business rules belong in services or the domain.

[↑ Topic Contents](#spring-spring-mvc-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-mvc-controller-vs-restcontroller"></a>

## @Controller vs @RestController

> `@Controller` may return view names or response bodies with `@ResponseBody`. `@RestController` combines those annotations and normally serializes return values directly into the HTTP body, making it common for APIs.

[↑ Topic Contents](#spring-spring-mvc-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-mvc-pathvariable-requestparam-e-requestbody"></a>

## @PathVariable, @RequestParam, and @RequestBody

> `@PathVariable` reads route segments, `@RequestParam` reads request parameters, and `@RequestBody` reads the body converted by message converters. I select each source according to the API contract and validate input before applying business rules.

[↑ Topic Contents](#spring-spring-mvc-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-mvc-validacao"></a>

## Bean Validation

> I use Bean Validation for structural input constraints. Business rules remain in the layer responsible for the domain.

[↑ Topic Contents](#spring-spring-mvc-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-mvc-2-controlleradvice"></a>

## @RestControllerAdvice and Error Handling

> I centralize HTTP exception handling with `@RestControllerAdvice`, translating application exceptions into consistent responses without exposing internal details.

[↑ Topic Contents](#spring-spring-mvc-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-mvc-interceptor"></a>

## Filter vs Interceptor

> A Filter operates at the Servlet level and suits general HTTP concerns. An Interceptor runs within Spring MVC and can work with the selected handler.

[↑ Topic Contents](#spring-spring-mvc-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-mvc-regra-pratica-2"></a>

## Spring MVC vs WebFlux

> I choose MVC or WebFlux based on the entire technology stack. If dependencies are blocking, MVC is generally simpler and more coherent. WebFlux makes more sense when the flow is genuinely nonblocking end to end.

[↑ Topic Contents](#spring-spring-mvc-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-mvc-resposta-completa-de-entrevista"></a>

## Interview Summary

> Spring MVC processes HTTP through DispatcherServlet, controllers, and converters. I validate inputs, delegate business rules, and standardize errors. I distinguish filters from interceptors and select MVC or WebFlux based on whether the entire integration is blocking or nonblocking.

[↑ Topic Contents](#spring-spring-mvc-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-sumario"></a>

# 16. Spring Data

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#spring-spring-data-visao-geral)
- [Spring Data vs JPA vs Hibernate](#spring-spring-data-spring-data-vs-jpa-vs-hibernate)
- [Spring Data Commons](#spring-spring-data-spring-data-commons)
- [Repositories, Derived Queries, and @Query](#spring-spring-data-4-query)
- [Persistence Context and Dirty Checking](#spring-spring-data-3-dirty-checking)
- [Entity States](#spring-spring-data-estados-da-entidade)
- [persist, merge, and save](#spring-spring-data-persist-merge-e-save)
- [Flush vs Commit](#spring-spring-data-flush-vs-commit)
- [@Transactional and Transaction Boundaries](#spring-spring-data-4-rollback)
- [REQUIRED, REQUIRES_NEW, and NESTED](#spring-spring-data-required-requires-new-e-nested)
- [Rollback: RuntimeException, Error, and Checked Exceptions](#spring-spring-data-rollback-runtimeexception-error-e-checked-exception)
- [Self-invocation and @Transactional](#spring-spring-data-self-invocation-e-transactional)
- [readOnly and Asynchronous Transactions](#spring-spring-data-readonly-e-transacoes-assincronas)
- [N+1 and Fetching Strategies](#spring-spring-data-cuidado)
- [LAZY, EAGER, and LazyInitializationException](#spring-spring-data-lazy-eager-e-lazyinitializationexception)
- [Cascade vs orphanRemoval](#spring-spring-data-cascade-vs-orphanremoval)
- [Open Session in View](#spring-spring-data-open-session-in-view)
- [Page vs Slice](#spring-spring-data-slice)
- [Optimistic vs Pessimistic Locking](#spring-spring-data-2-pessimistic-lock)
- [Projections, Specifications, and Auditing](#spring-spring-data-auditing)
- [Interview Summary](#spring-spring-data-resposta-completa-de-entrevista)

---

<a id="spring-spring-data-visao-geral"></a>

## Overview

> Spring Data provides data-access abstractions for different technologies. Spring Data JPA adds repositories on top of JPA; JPA is a persistence specification and Hibernate is a common implementation. The selected module determines the available guarantees.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-spring-data-vs-jpa-vs-hibernate"></a>

## Spring Data vs JPA vs Hibernate

> JPA defines the ORM contract, Hibernate implements that contract, and Spring Data JPA adds repositories and Spring integration.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-spring-data-commons"></a>

## Spring Data Commons

> Spring Data Commons provides abstractions shared by modules, such as repository metadata, pagination, sorting, and auditing components. It is the common foundation; specific operations and guarantees depend on the module used.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-4-query"></a>

## Repositories, Derived Queries, and @Query

> Repositories reduce boilerplate. Derived queries work well for simple cases; for more complex queries I use `@Query`, Specifications, or dedicated queries.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-3-dirty-checking"></a>

## Persistence Context and Dirty Checking

> The persistence context manages entities. When an entity is managed, the provider can detect changes through dirty checking and synchronize them during flush or commit.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-estados-da-entidade"></a>

## Entity States

> An entity can be transient, managed, detached, or removed. Dirty checking tracks managed entities; changing a detached entity does not guarantee an automatic update. Understanding state avoids treating `save` or `merge` as a universal fix.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-persist-merge-e-save"></a>

## persist, merge, and save

> `persist` makes a new entity managed; `merge` copies state into a managed instance and returns that instance. In Spring Data JPA, `save` chooses an operation based on whether the entity is new; the object passed to `merge` does not automatically become managed.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-flush-vs-commit"></a>

## Flush vs Commit

> Flush synchronizes persistence-context changes with the database and may execute SQL before commit. Commit completes the transaction; a successful flush does not mean data is committed or that a later rollback is impossible.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-4-rollback"></a>

## @Transactional and Transaction Boundaries

> `@Transactional` defines transactional boundaries and usually works through proxies. I also consider propagation, isolation, and rollback. I avoid unnecessary `REQUIRES_NEW` because it creates an independent transaction and may increase connection pool usage.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-required-requires-new-e-nested"></a>

## REQUIRED, REQUIRES_NEW, and NESTED

> REQUIRED joins an existing transaction or creates one; REQUIRES_NEW creates a separate physical transaction. NESTED may use savepoints within the same transaction where supported. Support depends on the transaction manager and database; independence also increases connection demand.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-rollback-runtimeexception-error-e-checked-exception"></a>

## Rollback: RuntimeException, Error, and Checked Exceptions

> By default, Spring rolls back on `RuntimeException` and `Error`, but not checked exceptions. I can configure `rollbackFor` or other rules; catching and ignoring a failure may prevent the interceptor from recognizing that rollback is required. [Reference](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/rolling-back.html)

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-self-invocation-e-transactional"></a>

## Self-invocation and @Transactional

> In the default proxy-based mode, an internal call through `this` does not pass through the transactional interceptor. Annotating the called method therefore does not automatically create the expected transaction; I reorganize collaboration among beans or use explicit transaction control where appropriate. [Reference](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html)

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-readonly-e-transacoes-assincronas"></a>

## readOnly and Asynchronous Transactions

> `readOnly=true` is an optimization hint, not a universal prohibition on writes. In thread-bound transactions, creating an asynchronous task does not transfer the caller's transaction; I define the transactional boundary in the flow actually doing the work.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-cuidado"></a>

## N+1 and Fetching Strategies

> N+1 occurs when a main query triggers many additional queries for relationships. I detect it using logs/APM and address it with fetch joins, EntityGraphs, projections, or dedicated queries as appropriate.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-lazy-eager-e-lazyinitializationexception"></a>

## LAZY, EAGER, and LazyInitializationException

> LAZY defers loading where supported; EAGER requires data to be made available but does not guarantee a single query. Accessing an uninitialized association outside the context can fail; I select fetching based on the use case instead of making everything EAGER.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-cascade-vs-orphanremoval"></a>

## Cascade vs orphanRemoval

> Cascade propagates persistence operations between entities according to mappings. `orphanRemoval` deletes dependents when they leave the association specified by the contract; neither is automatically the same as database `ON DELETE CASCADE`.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-open-session-in-view"></a>

## Open Session in View

> Keeping the persistence context open during response handling enables lazy loads but may hide N+1 queries and SQL outside the intended service boundary. I prefer preparing necessary data within a clear boundary and measuring the impact of this configuration.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-slice"></a>

## Page vs Slice

> I use `Page` when the total count is needed and `Slice` when knowing whether another page exists is enough, avoiding expensive counts in some scenarios.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-2-pessimistic-lock"></a>

## Optimistic vs Pessimistic Locking

> Optimistic locking detects conflicts through versions and works well when conflicts are rare. Pessimistic locking locks data in the database and may be needed when conflict costs are high, but it increases contention.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-auditing"></a>

## Projections, Specifications, and Auditing

> Projections reduce the data loaded, Specifications help with dynamic filters, and auditing automates creation and modification metadata.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-data-resposta-completa-de-entrevista"></a>

## Interview Summary

> Spring Data JPA reduces data-access code built on JPA and its provider. I understand entities, persistence contexts, fetching, and transactions, including proxies and rollback. I investigate N+1, indexes, connection pools, and locks because ORM does not eliminate database costs.

[↑ Topic Contents](#spring-spring-data-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-security-sumario"></a>

# 17. Spring Security

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#spring-spring-security-visao-geral)
- [Authentication vs Authorization](#spring-spring-security-authentication-vs-authorization)
- [SecurityFilterChain](#spring-spring-security-security-filter-chain)
- [AuthenticationManager, Provider, and UserDetailsService](#spring-spring-security-4-userdetailsservice)
- [PasswordEncoder and Password Hashing](#spring-spring-security-passwords)
- [CSRF vs CORS](#spring-spring-security-2-cors)
- [JWT and OAuth2 Resource Server](#spring-spring-security-jwt-e-oauth2-resource-server)
- [OAuth2 vs OpenID Connect](#spring-spring-security-oauth2-vs-openid-connect)
- [JWT: Validation and Limitations](#spring-spring-security-jwt-validacao-e-limites)
- [Method-level and Resource-level Security](#spring-spring-security-seguranca-de-metodo-e-de-recurso)
- [Sessions vs Stateless Bearer Tokens](#spring-spring-security-sessao-vs-stateless-com-bearer-token)
- [Interview Summary](#spring-spring-security-resposta-completa-de-entrevista)

---

<a id="spring-spring-security-visao-geral"></a>

## Overview

> Spring Security primarily operates through a filter chain before requests reach the controller. It establishes the authenticated identity in SecurityContext and then applies authorization rules.

[↑ Topic Contents](#spring-spring-security-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-security-authentication-vs-authorization"></a>

## Authentication vs Authorization

> Authentication verifies identity. Authorization decides whether that identity may access a resource or perform an operation.

[↑ Topic Contents](#spring-spring-security-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-security-security-filter-chain"></a>

## SecurityFilterChain

> Spring Security is filter-based on the Servlet stack. `SecurityFilterChain` specifies the mechanisms and rules applied before a request reaches Spring MVC.

[↑ Topic Contents](#spring-spring-security-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-security-4-userdetailsservice"></a>

## AuthenticationManager, Provider, and UserDetailsService

> AuthenticationManager coordinates authentication and delegates to AuthenticationProviders. In traditional authentication, a provider may use UserDetailsService to load the user and PasswordEncoder to verify a password.

[↑ Topic Contents](#spring-spring-security-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-security-passwords"></a>

## PasswordEncoder and Password Hashing

> Passwords must be stored using appropriate password hashing, never as plaintext or reversibly encrypted values. Spring abstracts this through PasswordEncoder.

[↑ Topic Contents](#spring-spring-security-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-security-2-cors"></a>

## CSRF vs CORS

> CSRF protection addresses forged requests when credentials are sent automatically by the browser. CORS is a browser policy governing cross-origin access and does not replace authentication or authorization.

[↑ Topic Contents](#spring-spring-security-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-security-jwt-e-oauth2-resource-server"></a>

## JWT and OAuth2 Resource Server

> JWT is a token format, while OAuth2 defines authorization flows. In a Resource Server, Spring Security validates the bearer token, creates Authentication, and uses claims or scopes for authorization.

[↑ Topic Contents](#spring-spring-security-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-security-oauth2-vs-openid-connect"></a>

## OAuth2 vs OpenID Connect

> OAuth2 addresses delegated authorization; OpenID Connect adds an identity layer over OAuth2. An access token is intended for the protected resource; an ID token describes authentication to the client and should not be used interchangeably as an API token.

[↑ Topic Contents](#spring-spring-security-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-security-jwt-validacao-e-limites"></a>

## JWT: Validation and Limitations

> Decoding a JWT does not prove its authenticity. I verify the signature, allowed algorithm, issuer, audience, and expiration; a signed JWT is not necessarily encrypted, and revocation needs more than signature verification.

[↑ Topic Contents](#spring-spring-security-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-security-seguranca-de-metodo-e-de-recurso"></a>

## Method-level and Resource-level Security

> Rules such as `@PreAuthorize` protect operations when method security is enabled. I also validate ownership and permissions for the requested resource; being authenticated or holding a role does not grant access to every record.

[↑ Topic Contents](#spring-spring-security-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-security-sessao-vs-stateless-com-bearer-token"></a>

## Sessions vs Stateless Bearer Tokens

> Sessions allow server-side state and revocation; bearer tokens can reduce session-server lookups but introduce different validity and revocation requirements. Credential storage and transmission determine risks such as CSRF; using JWT does not inherently eliminate that risk.

[↑ Topic Contents](#spring-spring-security-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-security-resposta-completa-de-entrevista"></a>

## Interview Summary

> Spring Security applies authentication and authorization with mechanisms appropriate to the stack. I distinguish OAuth2, OIDC, and JWT, validate tokens, and protect credentials. I consider resource authorization, CSRF, CORS, and least privilege without assuming the gateway protects every operation.

[↑ Topic Contents](#spring-spring-security-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-sumario"></a>

# 18. Automated Testing in Java and Spring

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#testes-java-spring-visao-geral)
- [Unit Tests](#testes-java-spring-teste-unitario)
- [JUnit and Assertions](#testes-java-spring-junit-e-assertions)
- [Arrange, Act, Assert](#testes-java-spring-arrange-act-assert)
- [Mock, Stub, Fake, and Spy](#testes-java-spring-mock-stub-fake-e-spy)
- [Mockito](#testes-java-spring-mockito)
- [Parameterized Tests and Edge Cases](#testes-java-spring-testes-parametrizados-e-casos-de-borda)
- [TDD](#testes-java-spring-tdd)
- [Integration Tests](#testes-java-spring-teste-de-integracao)
- [Testcontainers](#testes-java-spring-testcontainers)
- [@SpringBootTest](#testes-java-spring-springboottest)
- [@WebMvcTest and MockMvc](#testes-java-spring-webmvctest-e-mockmvc)
- [@DataJpaTest](#testes-java-spring-datajpatest)
- [Contract Tests](#testes-java-spring-testes-de-contrato)
- [End-to-End Tests](#testes-java-spring-testes-end-to-end)
- [Test Pyramid](#testes-java-spring-piramide-de-testes)
- [Asynchronous and Concurrency Tests](#testes-java-spring-testes-assincronos-e-de-concorrencia)
- [Coverage and Mutation Testing](#testes-java-spring-cobertura-e-mutation-testing)
- [Flakiness, Clocks, and Shared State](#testes-java-spring-flakiness-relogio-e-estado-compartilhado)
- [Load and Performance Testing](#testes-java-spring-testes-de-carga-e-performance)
- [Interview Summary](#testes-java-spring-resumo-de-entrevista)

---

<a id="testes-java-spring-visao-geral"></a>

## Overview

> Automated tests verify behavior and reduce regression risk. I choose the smallest scope that can reveal the relevant failure, combining unit, integration, contract, and end-to-end tests.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-teste-unitario"></a>

## Unit Tests

> Validate a unit of behavior quickly with controlled dependencies. For pure business rules, I instantiate objects directly; starting all of Spring to test a simple function adds cost without improving the evidence.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-junit-e-assertions"></a>

## JUnit and Assertions

> JUnit organizes test execution, lifecycle, and parameters. Assertions should verify observable outcomes and relevant cases rather than mirror implementation or merely confirm that no exception was thrown.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-arrange-act-assert"></a>

## Arrange, Act, Assert

> Arrange prepares the scenario; Act executes the behavior; Assert verifies the result. This structure clarifies intent and helps determine whether a failure is in the input, action, or expectation.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-mock-stub-fake-e-spy"></a>

## Mock, Stub, Fake, and Spy

> A stub provides controlled responses; a mock verifies interactions; a fake supplies a simplified working implementation; a spy observes or partially overrides a real object. I choose based on what the test must demonstrate and avoid simulating every internal detail.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-mockito"></a>

## Mockito

> Mockito creates test doubles and configures dependency responses. I verify interactions when an external call is the important effect; excessive `verify` usage can couple tests to internal structure and make refactoring fragile.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-testes-parametrizados-e-casos-de-borda"></a>

## Parameterized Tests and Edge Cases

> Parameterized tests exercise the same rule with different inputs. I cover boundaries, absence, duplicates, and invalid data where the contract requires them rather than testing only the happy path.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-tdd"></a>

## TDD

> TDD cycles through a failing test, minimal code to pass it, and refactoring with tests green. It clarifies expected behavior and provides quick feedback but does not replace requirements analysis or integration tests.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-teste-de-integracao"></a>

## Integration Tests

> Verify interactions between real components, such as application code and a database. They matter for transactions, mappings, SQL, and serialization, which may pass with mocks and fail in real environments.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-testcontainers"></a>

## Testcontainers

> Testcontainers runs real dependencies in containers during tests. It makes databases and brokers more like production; I choose suitable versions, isolate test data, and account for startup time and container-runtime availability.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-springboottest"></a>

## @SpringBootTest

> Loads the application context to test interactions among Spring components. It suits flows that need that context, but its cost calls for selective use; the configured web environment determines how HTTP is exercised.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-webmvctest-e-mockmvc"></a>

## @WebMvcTest and MockMvc

> `@WebMvcTest` loads a slice focused on MVC. MockMvc tests Spring HTTP processing without starting a real network server; I validate routes, serialization, errors, and applicable security while controlling controller dependencies.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-datajpatest"></a>

## @DataJpaTest

> Loads a JPA persistence slice to test entities, repositories, and queries. An in-memory database may hide differences in dialects, locks, and constraints; for those cases, I use the same database type as production.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-testes-de-contrato"></a>

## Contract Tests

> Verify expectations between consumers and providers, such as fields, statuses, and schemas. They help detect incompatibilities without starting the entire system but do not guarantee availability or a complete journey's behavior.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-testes-end-to-end"></a>

## End-to-End Tests

> Exercise a complete user journey across relevant boundaries. I prioritize critical flows because they are slower and environment-sensitive; I do not replace all rule validation with a large E2E suite.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-piramide-de-testes"></a>

## Test Pyramid

> Distributes verification across many fast tests and fewer broad, expensive tests. Proportions depend on system risks; the goal is reliable feedback at sustainable cost, not a fixed percentage.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-testes-assincronos-e-de-concorrencia"></a>

## Asynchronous and Concurrency Tests

> I use coordination and observable conditions with deadlines instead of arbitrary sleeps. For consumers, I test redelivery, duplicates, and failures between persistence and acknowledgment because these reveal consistency issues.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-cobertura-e-mutation-testing"></a>

## Coverage and Mutation Testing

> Coverage shows which code ran but does not guarantee meaningful assertions. Mutation testing makes small code changes to check whether tests detect them; it improves suite evaluation at the cost of longer execution.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-flakiness-relogio-e-estado-compartilhado"></a>

## Flakiness, Clocks, and Shared State

> Flaky tests may depend on time, networks, concurrency, or another test's data. I use controlled `Clock` instances, state isolation, and clear deadlines to eliminate causes instead of rerunning until green.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-testes-de-carga-e-performance"></a>

## Load and Performance Testing

> Load tests measure behavior under defined volume; stress tests seek limits; soak tests assess extended operation. I evaluate tail latency, errors, throughput, and saturation using representative data, without confusing user counts with requests per second.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="testes-java-spring-resumo-de-entrevista"></a>

## Interview Summary

> I test business rules with small units, integrations with real components, and contracts between services. I reserve E2E for critical journeys and use coverage as an indicator while focusing on failures the tests can actually detect.

[↑ Topic Contents](#testes-java-spring-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-arquitetura-de-software-sumario"></a>

# 19. Software Architecture

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Software Architecture Overview](#arquitetura-visao-geral-arquitetura-de-software)
- [Functional and Nonfunctional Requirements](#arquitetura-arquitetura-de-software-requisitos-funcionais-e-nao-funcionais)
- [Architectural Trade-offs](#arquitetura-trade-off-arquitetural)
- [ADRs and the C4 Model](#arquitetura-arquitetura-de-software-adr-e-c4-model)
- [DDD and Architecture](#arquitetura-ddd-e-arquitetura)
- [Ubiquitous Language and Context Maps](#arquitetura-arquitetura-de-software-linguagem-ubiqua-e-context-map)
- [Bounded Context](#arquitetura-bounded-context)
- [Entity vs Value Object](#arquitetura-entidade-vs-value-object)
- [Aggregate and Aggregate Root](#arquitetura-agregado-e-aggregate-root)
- [Application Service vs Domain Service](#arquitetura-arquitetura-de-software-application-service-vs-domain-service)
- [Layered Architecture](#arquitetura-arquitetura-em-camadas)
- [Hexagonal Architecture](#arquitetura-arquitetura-hexagonal)
- [Clean Architecture](#arquitetura-clean-architecture)
- [Hexagonal vs Clean Architecture](#arquitetura-arquitetura-hexagonal-vs-clean-architecture)
- [Monolith](#arquitetura-monolito)
- [Modular Monolith](#arquitetura-monolito-modular)
- [Microservices](#arquitetura-microservicos)
- [Monolith vs Microservices](#arquitetura-monolito-vs-microservicos)
- [When to Extract a Microservice](#arquitetura-quando-extrair-um-microservico)
- [Synchronous vs Asynchronous Communication](#arquitetura-sincrono-vs-assincrono)
- [REST APIs vs Messaging](#arquitetura-api-rest-vs-mensageria)
- [Message-Driven Architecture](#arquitetura-arquitetura-orientada-a-mensagens)
- [Event-Driven Architecture](#arquitetura-arquitetura-orientada-a-eventos)
- [Event vs Command](#arquitetura-evento-vs-comando)
- [At-most-once, At-least-once, and Exactly-once](#arquitetura-at-most-once-at-least-once-e-exactly-once)
- [Strong vs Eventual Consistency](#arquitetura-consistencia-forte-vs-eventual)
- [CAP Theorem](#arquitetura-cap-theorem)
- [PACELC](#arquitetura-arquitetura-de-software-pacelc)
- [Database per Service](#arquitetura-banco-por-servico)
- [Shared Database — Trade-off](#arquitetura-shared-database-trade-off)
- [Replication and Read Replicas](#arquitetura-replicacao-e-read-replica)
- [Partitioning vs Sharding](#arquitetura-arquitetura-de-software-particionamento-vs-sharding)
- [Sharding](#arquitetura-sharding)
- [CQRS](#arquitetura-cqrs)
- [CQRS vs Read Replica](#arquitetura-cqrs-vs-read-replica)
- [Event Sourcing](#arquitetura-event-sourcing)
- [Saga](#arquitetura-saga)
- [Saga — Orchestration vs Choreography](#arquitetura-saga-orquestracao-vs-coreografia)
- [Outbox Pattern](#arquitetura-outbox-pattern)
- [Idempotency](#arquitetura-idempotencia)
- [API Gateway](#arquitetura-api-gateway)
- [BFF — Backend for Frontend](#arquitetura-bff-backend-for-frontend)
- [Service Discovery](#arquitetura-service-discovery)
- [Load Balancing](#arquitetura-load-balancing)
- [Stateless vs Stateful](#arquitetura-stateless-vs-stateful)
- [Vertical vs Horizontal Scaling](#arquitetura-escalabilidade-vertical-vs-horizontal)
- [High Availability](#arquitetura-alta-disponibilidade)
- [Fault Tolerance](#arquitetura-tolerancia-a-falhas)
- [Retries, Timeouts, and Circuit Breakers in Architecture](#arquitetura-retry-timeout-e-circuit-breaker-na-arquitetura)
- [Bulkhead](#arquitetura-bulkhead)
- [Graceful Degradation](#arquitetura-degradacao-graciosa)
- [Rate Limiting](#arquitetura-rate-limiting)
- [Backpressure](#arquitetura-backpressure)
- [Caching](#arquitetura-cache)
- [Cache-Aside](#arquitetura-cache-aside)
- [Cache Stampede](#arquitetura-cache-stampede)
- [Observability](#arquitetura-observabilidade)
- [Logs, Metrics, and Traces](#arquitetura-logs-metricas-e-traces)
- [SLO, SLI, and SLA](#arquitetura-slo-sli-e-sla)
- [P95, P99, and Mean](#arquitetura-p95-p99-e-media)
- [Architecture and Security](#arquitetura-arquitetura-e-seguranca)
- [API Versioning](#arquitetura-versionamento-de-api)
- [Backward Compatibility](#arquitetura-backward-compatibility)
- [Strangler Fig Pattern](#arquitetura-strangler-fig-pattern)
- [Anti-Corruption Layer](#arquitetura-anti-corruption-layer)
- [12-Factor App](#arquitetura-12-factor-app)
- [Cloud-Native Architecture](#arquitetura-arquitetura-cloud-native)
- [Kubernetes in Architecture](#arquitetura-kubernetes-na-arquitetura)
- [Evolutionary Architecture](#arquitetura-arquitetura-evolutiva)
- [Interview Summary](#arquitetura-resposta-completa-de-entrevista-arquitetura)

---

<a id="arquitetura-visao-geral-arquitetura-de-software"></a>

## Software Architecture Overview

> Software architecture defines how responsibilities, components, data, and integrations are organized to meet functional and nonfunctional requirements. In interviews, I emphasize that architecture is not simply choosing technologies, but making structural decisions that balance scalability, availability, consistency, complexity, cost, and evolvability.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-arquitetura-de-software-requisitos-funcionais-e-nao-funcionais"></a>

## Functional and Nonfunctional Requirements

> Functional requirements describe behavior; nonfunctional requirements define qualities such as latency, availability, security, and cost. I translate vague terms such as "fast" into measurable targets and load conditions to compare options.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-trade-off-arquitetural"></a>

## Architectural Trade-offs

> Every architectural decision trades one quality for another. Stronger consistency may reduce availability; more abstraction may reduce coupling but add complexity; more services improve autonomy but increase operational burden. In interviews, I make the prioritized requirement and accepted cost explicit.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-arquitetura-de-software-adr-e-c4-model"></a>

## ADRs and the C4 Model

> An ADR records a decision, its context, alternatives, and consequences. C4 structures views of context, containers, and components, adding code-level detail when useful; I document enough to guide decisions and evolution without diagramming everything.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-ddd-e-arquitetura"></a>

## DDD and Architecture

> Domain-Driven Design helps when business complexity is significant. I emphasize ubiquitous language, bounded contexts, entities, value objects, aggregates, and domain services. DDD does not require microservices: it also works in modular monoliths and is more about organizing the domain than choosing infrastructure.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-arquitetura-de-software-linguagem-ubiqua-e-context-map"></a>

## Ubiquitous Language and Context Maps

> Ubiquitous language is the vocabulary shared by business and engineering within a context. A context map shows relationships between contexts and their contracts; a bounded context does not need to correspond exactly to one microservice.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-bounded-context"></a>

## Bounded Context

> In DDD, a bounded context is a boundary within which a domain model has consistent meaning. In microservice architecture, it helps identify more natural service boundaries. The same term can have different models across contexts. I avoid sharing one domain model across services because that increases coupling.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-entidade-vs-value-object"></a>

## Entity vs Value Object

> An entity maintains its own identity over time. A value object is defined by its values and is usually immutable. For example, `Pedido` is generally an entity, while `Dinheiro` can be a value object. Value objects encapsulate rules and reduce primitive types that lack business meaning.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-agregado-e-aggregate-root"></a>

## Aggregate and Aggregate Root

> An aggregate is a group of domain objects treated as one consistency boundary. The aggregate root is the entry point for internal changes. Aggregate rules must be preserved within that boundary. Very large aggregates increase contention and transaction costs, so I focus them on invariants that truly require strong consistency.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-arquitetura-de-software-application-service-vs-domain-service"></a>

## Application Service vs Domain Service

> An application service coordinates a use case, permissions, and transactions. A domain service expresses business logic that does not naturally belong in an entity or value object; I avoid putting the entire model in services while leaving the domain behaviorless.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-arquitetura-em-camadas"></a>

## Layered Architecture

> Layered architecture separates responsibilities into levels such as presentation, application, domain, and infrastructure. Its main benefit is organization and separation of concerns. Applied too rigidly, it can introduce layer coupling and artificial flows. I use it where domain complexity justifies separation without turning it into bureaucracy.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-arquitetura-hexagonal"></a>

## Hexagonal Architecture

> Hexagonal Architecture centers the system on the domain and uses ports and adapters to isolate infrastructure. Ports define incoming and outgoing contracts; adapters connect HTTP, databases, messaging, or external APIs. The main advantages are decoupling and testability. More classes and abstractions are the cost, so I use it where infrastructure independence brings value.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-clean-architecture"></a>

## Clean Architecture

> Clean Architecture directs dependencies toward inner business rules. Frameworks, databases, and interfaces sit at the edges to keep the domain independent of external details. The trade-off is additional abstractions and mappings. I find it useful for long-lived systems with meaningful business rules but avoid applying it dogmatically to simple CRUD.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-arquitetura-hexagonal-vs-clean-architecture"></a>

## Hexagonal vs Clean Architecture

> Both seek to reduce infrastructure dependency and protect the domain. Hexagonal emphasizes ports and adapters; Clean Architecture emphasizes concentric layers and inward dependencies. In practice, their principles overlap significantly. I choose the model that makes dependencies explicit without unnecessary abstractions.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-monolito"></a>

## Monolith

> A monolith is an application deployed as one unit. It is not synonymous with bad code. It can be simpler to develop, test, deploy, and operate, especially early on. Problems arise when it grows without modularization, increasing coupling and slowing evolution. I start simple and distribute only for a concrete reason.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-monolito-modular"></a>

## Modular Monolith

> A modular monolith preserves one deployment unit while dividing the system into modules with clear boundaries and controlled dependencies. It retains operational simplicity while reducing internal coupling. For many systems it is an excellent step before microservices, allowing boundaries to emerge without immediately assuming distribution costs.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-microservicos"></a>

## Microservices

> Microservices split a system into independently deployable services, usually aligned with business capabilities. Benefits include deployment autonomy, failure isolation, and independent scaling. Costs are substantial: networks, distributed observability, eventual consistency, versioning, security, tracing, DevOps, and operations. I use microservices when benefits justify distributed complexity.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-monolito-vs-microservicos"></a>

## Monolith vs Microservices

> I do not treat microservices as the mandatory next step for monoliths. A monolith reduces operational complexity and supports local consistency. Microservices provide autonomy, scaling, and isolation but introduce network failures and distributed consistency. The choice depends on organizational scale, independent domains, deployment frequency, availability requirements, and team operational maturity.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-quando-extrair-um-microservico"></a>

## When to Extract a Microservice

> I consider extraction when a clear domain boundary meets a concrete need, such as independent scaling, different release cadence, failure isolation, distinct security requirements, or ownership by another team. I do not extract solely because a class grew large. I first seek a stable boundary and measure the operational cost.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-sincrono-vs-assincrono"></a>

## Synchronous vs Asynchronous Communication

> Synchronous communication is simpler when the caller needs an immediate response, but creates temporal coupling and propagates latency and failures. Asynchronous communication reduces that coupling and absorbs spikes but requires eventual consistency, idempotency, and observability. I decide based on response needs and expected failure behavior.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-api-rest-vs-mensageria"></a>

## REST APIs vs Messaging

> REST suits synchronous interactions requiring immediate responses. Messaging is preferable for temporal decoupling, absorbing spikes, or propagating events. Many architectures use both: a synchronous command for an operation and an asynchronous event to announce its consequences.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-arquitetura-orientada-a-mensagens"></a>

## Message-Driven Architecture

> Messaging decouples producers and consumers in time and buffers load. Trade-offs include duplicates, ordering, retries, dead-letter queues, and observability. I consider consumer idempotency and acknowledgment semantics part of the production contract.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-arquitetura-orientada-a-eventos"></a>

## Event-Driven Architecture

> Event-driven architecture communicates state changes through events, reducing temporal coupling between producers and consumers. It improves extensibility and asynchronous integration but introduces eventual consistency, duplicates, ordering, observability, and failure handling. I consider idempotency and delivery semantics mandatory design concerns.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-evento-vs-comando"></a>

## Event vs Command

> A command expresses an intention: `CriarPedido`. An event expresses an accomplished fact: `PedidoCriado`. A command normally has an intended recipient and may fail; an event may have zero or many consumers. Mixing these concepts often produces confusing messaging contracts.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-at-most-once-at-least-once-e-exactly-once"></a>

## At-most-once, At-least-once, and Exactly-once

> At-most-once prioritizes avoiding duplicates but may lose messages. At-least-once reduces loss risk but permits duplicates, requiring idempotency. Exactly-once generally applies within a technology-specific scope and its guarantees; I never treat it as a magic end-to-end guarantee without analyzing databases, brokers, and external side effects.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-consistencia-forte-vs-eventual"></a>

## Strong vs Eventual Consistency

> Strong consistency needs a defined guarantee, such as linearizability: a read that starts after a completed write observes that write or a later one. Eventual consistency allows temporary divergence while promising convergence when no new updates occur. These concepts are not synonyms for database transaction isolation levels.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-cap-theorem"></a>

## CAP Theorem

> CAP states that during a network partition, a system cannot simultaneously guarantee linearizable consistency and availability of every request to non-failing nodes. The decision concerns how each operation responds to a partition, not simply choosing two of three qualities in all circumstances.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-arquitetura-de-software-pacelc"></a>

## PACELC

> PACELC adds that even without partitions, replicated systems may trade latency for consistency. During a partition, the choice involves availability versus consistency; otherwise, I assess the coordination cost of the required guarantee.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-banco-por-servico"></a>

## Database per Service

> In microservices, each service should ideally own its model and persistence. Direct schema sharing creates strong coupling and hinders independent deployment. Integration between contexts should use APIs or events, although gradual migration may require temporary compromises.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-shared-database-trade-off"></a>

## Shared Database — Trade-off

> A shared database simplifies joins and transactions initially, but increases service coupling and lets one service depend on another's internal schema. If genuine autonomy is required, I separate data ownership and avoid direct cross-service access.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-replicacao-e-read-replica"></a>

## Replication and Read Replicas

> Read replicas can increase read capacity and isolate analytical load but introduce potential replication lag. A read immediately following a write may return stale state. I use replicas only when the use case tolerates that consistency level and route reads intentionally.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-arquitetura-de-software-particionamento-vs-sharding"></a>

## Partitioning vs Sharding

> Partitioning divides data logically or physically, potentially within one database. Sharding distributes partitions among instances or nodes; it can increase capacity but adds routing, rebalancing, and difficulty with cross-shard queries and transactions.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-sharding"></a>

## Sharding

> Sharding distributes data across partitions or databases using a shard key. It scales volume and throughput but complicates global joins, transactions, rebalancing, and hotspots. Shard-key selection is critical. I consider sharding only when a single instance or cluster no longer suffices after simpler options are evaluated.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-cqrs"></a>

## CQRS

> CQRS separates read and write models or paths. It ranges from distinct handlers to separate data stores. It can optimize each side for different needs and reduce model coupling. Trade-offs include complexity, model duplication, and possible eventual consistency. I do not apply CQRS merely because commands and queries exist.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-cqrs-vs-read-replica"></a>

## CQRS vs Read Replica

> A read replica is an infrastructure approach for scaling reads of a similar model. CQRS is an architectural choice separating read and write models or paths. They can coexist but address different problems.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-event-sourcing"></a>

## Event Sourcing

> Event Sourcing persists a sequence of events as the source of truth rather than only current state. It provides complete history and state reconstruction but significantly increases event-versioning, replay, snapshotting, debugging, and model-evolution complexity. It is appropriate where that history is genuinely required, not as a default.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-saga"></a>

## Saga

> A Saga coordinates a distributed business transaction across local transactions, using compensating actions when steps fail. It can use choreography through events or orchestration through an explicit coordinator. It does not provide a global ACID rollback; it provides business consistency across services.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-saga-orquestracao-vs-coreografia"></a>

## Saga — Orchestration vs Choreography

> In orchestration, a central component chooses next steps, making the flow easier to visualize but increasing dependency on the orchestrator. In choreography, services react to events, reducing central control but making flows harder to trace. I choose based on process complexity and governance needs.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-outbox-pattern"></a>

## Outbox Pattern

> The Outbox Pattern addresses updating a database and publishing an event without relying on a distributed transaction. The application stores the business change and an outbox record in one local transaction. A separate process later publishes the event. Trade-offs include asynchronous publication, deduplication, and outbox cleanup.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-idempotencia"></a>

## Idempotency

> An idempotent operation can be retried without unintended additional effects. This matters in distributed systems because retries and redelivery occur. I use idempotency keys, unique constraints, processed-message records, or naturally idempotent business logic depending on the case.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-api-gateway"></a>

## API Gateway

> An API Gateway centralizes edge concerns such as routing, authentication, rate limiting, and observability. It can simplify clients but should not hold business rules or become a new monolith. Internal security and authorization may still be needed depending on trust boundaries.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-bff-backend-for-frontend"></a>

## BFF — Backend for Frontend

> A BFF adds a backend layer tailored to one client type, such as web or mobile. It reduces frontend composition logic and enables better-suited contracts but adds a service to operate. I use it when client needs differ enough to justify that layer.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-service-discovery"></a>

## Service Discovery

> Service Discovery locates instances dynamically. Platforms such as Kubernetes typically already provide this through Services and DNS. I avoid dedicated registries when the platform solves the problem adequately.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-load-balancing"></a>

## Load Balancing

> Load balancing distributes requests across instances to improve capacity and availability. It can happen in clients, gateways, proxies, or infrastructure. Algorithms must account for health and sometimes affinity or weights. Load balancing does not replace autoscaling or resilience.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-stateless-vs-stateful"></a>

## Stateless vs Stateful

> A stateless service does not rely on local session state between requests, simplifying load balancing and horizontal scaling. Stateful services may be required in some domains but need replication, affinity, or external storage. I avoid in-memory local session state when horizontal scaling is necessary.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-escalabilidade-vertical-vs-horizontal"></a>

## Vertical vs Horizontal Scaling

> Vertical scaling adds resources to an instance; it is simple but has physical limits and may preserve a single point of failure. Horizontal scaling adds instances, increasing capacity and resilience, but requires state, sessions, caches, and data to support distribution. I favor stateless applications for simpler horizontal scaling.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-alta-disponibilidade"></a>

## High Availability

> High availability aims to keep a service operating despite failures. It usually involves redundancy, multiple instances, health checks, failover, replication, and removing single points of failure. HA is not merely launching two instances; dependencies such as databases, queues, and DNS matter too.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-tolerancia-a-falhas"></a>

## Fault Tolerance

> Fault tolerance means preserving acceptable behavior when components fail. It may involve redundancy, controlled retries, circuit breakers, fallbacks, queues, and graceful degradation. The goal is preventing local failures from becoming systemic failures.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-retry-timeout-e-circuit-breaker-na-arquitetura"></a>

## Retries, Timeouts, and Circuit Breakers in Architecture

> Timeouts bound waiting; retries attempt to recover transient failures; circuit breakers avoid repeatedly calling degraded dependencies. They complement one another. Retries must respect idempotency, use backoff and jitter, and fit within the latency budget. Incorrect configuration can amplify cascading failures.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-bulkhead"></a>

## Bulkhead

> Bulkheads isolate resources so one degraded dependency cannot exhaust the application's capacity. I use separate pools, semaphores, or concurrency limits to contain failure and preserve resources for other features.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-degradacao-graciosa"></a>

## Graceful Degradation

> Graceful degradation preserves essential features when noncritical parts are unavailable, such as allowing checkout even if recommendations fail. Fallbacks must be explicit and observable; silently hiding failures can produce incorrect data or conceal incidents.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-rate-limiting"></a>

## Rate Limiting

> Rate limiting controls how many operations are accepted over a period. It protects capacity, reduces abuse, and prevents dependency overload. Limits may apply per user, API key, IP, or tenant. I also define an appropriate response, usually `429`, and client retry policies.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-backpressure"></a>

## Backpressure

> Backpressure prevents producers from creating work faster than consumers or dependencies can process it. Bounded queues, concurrency controls, rate limiting, or reactive mechanisms can enforce it. Without backpressure, memory, threads, or connections accumulate until failure.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-cache"></a>

## Caching

> Caching trades consistency and memory for reduced latency and origin load. The hard part is not populating the cache but invalidating entries correctly. I specify TTL, keys, size, eviction, and miss or failure behavior. I also assess cache-stampede risk.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-cache-aside"></a>

## Cache-Aside

> With cache-aside, the application checks the cache; on a miss, it reads the source and populates the cache. This is simple and common but may cause stampedes when many requests miss the same key. TTL, per-key locking, or proactive refresh can help.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-cache-stampede"></a>

## Cache Stampede

> A cache stampede occurs when a key expires and many requests reach the source simultaneously, potentially overwhelming it. Strategies include jittered TTLs, single-flight or per-key locks, early refresh, and stale-while-revalidate.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-observabilidade"></a>

## Observability

> Observability helps explain internal system behavior through external signals. I consider logs, metrics, and traces its three main pillars. In distributed systems, correlation IDs, trace IDs, p95/p99, error rates, and saturation are essential for identifying bottlenecks and cross-service failures.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-logs-metricas-e-traces"></a>

## Logs, Metrics, and Traces

> Logs explain specific events; metrics show aggregate behavior over time; traces show a request's path through components. I use all three together. A trace can identify a slow service, a metric show frequency, and a log explain the error in that execution.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-slo-sli-e-sla"></a>

## SLO, SLI, and SLA

> An SLI is an observed metric such as availability or latency. An SLO is the internal target for that metric, for example 99.9% availability. An SLA is a formal commitment with contractual consequences. SLOs should guide architectural choices because they justify redundancy, capacity, and operational investment.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-p95-p99-e-media"></a>

## P95, P99, and Mean

> P95 indicates that roughly 95% of observations are at or below that value; P99 covers 99%. Percentiles reveal the tail that averages can hide. I do not add service percentiles or average P99 values to derive a global percentile.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-arquitetura-e-seguranca"></a>

## Architecture and Security

> Security is an architectural requirement. I consider authentication, authorization, least privilege, secrets, encryption in transit and at rest, trust boundaries, audit trails, and administrative endpoint protection. Security only at the gateway or frontend is not enough for every scenario.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-versionamento-de-api"></a>

## API Versioning

> Versioning is needed when contracts must evolve without breaking consumers. Options include URIs, headers, and content negotiation. I prefer backward-compatible changes where possible. In microservices, I also version event contracts and avoid destructive changes.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-backward-compatibility"></a>

## Backward Compatibility

> Backward compatibility means older consumers keep working after the producer evolves. For APIs and events, I prefer adding optional fields, avoiding abrupt removal or renaming, and coordinating changes in stages. This reduces synchronized deployments.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-strangler-fig-pattern"></a>

## Strangler Fig Pattern

> Strangler Fig gradually replaces a legacy system by routing features to new components while the legacy system continues running. Incremental migration reduces big-bang risk. The challenge is preserving integration and consistency during coexistence.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-anti-corruption-layer"></a>

## Anti-Corruption Layer

> An Anti-Corruption Layer protects a domain from legacy or external models. Instead of spreading external types and rules throughout the application, I translate contracts into the internal model at a dedicated boundary. This reduces coupling and eases future replacement.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-12-factor-app"></a>

## 12-Factor App

> The 12-Factor principles are useful for cloud-native applications: externalized configuration, stateless processes, logs as event streams, explicit dependencies, and environment parity. I view them as useful deployment and operational practices rather than absolute rules.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-arquitetura-cloud-native"></a>

## Cloud-Native Architecture

> Cloud-native does not simply mean hosting in the cloud. It means designing for automation, elasticity, observability, resilience, external configuration, and dynamic infrastructure. Containers and Kubernetes may help, but the architecture must still handle failure, state, and operations correctly.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-kubernetes-na-arquitetura"></a>

## Kubernetes in Architecture

> Kubernetes addresses orchestration needs such as scheduling, service discovery, rollouts, health checks, and scaling. It does not solve business rules, data consistency, idempotency, or domain architecture. I treat it as an execution platform rather than a replacement for architectural decisions.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-arquitetura-evolutiva"></a>

## Evolutionary Architecture

> Evolutionary architecture accepts that decisions change as systems grow. I aim for reversible decisions, well-defined modules, metrics, and fitness functions that reveal architectural degradation. The goal is not predicting everything but preserving adaptability.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="arquitetura-resposta-completa-de-entrevista-arquitetura"></a>

## Interview Summary

> Architecture organizes responsibilities and dependencies to meet business and quality requirements. I start with domain, volume, latency, availability, and cost, then compare options. For distributed systems, I consider consistency, idempotency, resilience, and observability while recording decision consequences.

[↑ Topic Contents](#arquitetura-arquitetura-de-software-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-microservicos-sumario"></a>

# 20. Microservices

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Microservices Overview](#microservicos-visao-geral-microservicos)
- [When to Use Microservices](#microservicos-quando-usar-microservicos)
- [When Not to Use Microservices](#microservicos-quando-nao-usar-microservicos)
- [Modular Monolith vs Microservices](#microservicos-monolito-modular-vs-microservicos)
- [Service Boundaries](#microservicos-boundaries-de-servico)
- [Database per Service](#microservicos-banco-por-servico)
- [Shared Database — Trade-off](#microservicos-shared-database-trade-off)
- [Synchronous Communication](#microservicos-comunicacao-sincrona)
- [Asynchronous Communication](#microservicos-comunicacao-assincrona)
- [REST vs Messaging](#microservicos-rest-vs-mensageria)
- [Distributed Consistency](#microservicos-consistencia-distribuida)
- [Saga](#microservicos-saga)
- [Saga — Orchestration vs Choreography](#microservicos-saga-orquestracao-vs-coreografia)
- [Outbox Pattern](#microservicos-outbox-pattern)
- [Idempotency in Microservices](#microservicos-idempotencia-em-microservicos)
- [Inbox and Idempotent Consumers](#microservicos-microservicos-inbox-e-consumidor-idempotente)
- [CDC vs Outbox Polling](#microservicos-microservicos-cdc-vs-polling-da-outbox)
- [Timeout](#microservicos-timeout)
- [Latency Budget and Deadline](#microservicos-microservicos-budget-de-latencia-e-deadline)
- [Unknown Outcome After Timeout](#microservicos-microservicos-resultado-desconhecido-apos-timeout)
- [Retry](#microservicos-retry)
- [Circuit Breaker](#microservicos-circuit-breaker)
- [Circuit Breaker States](#microservicos-microservicos-estados-do-circuit-breaker)
- [Bulkhead](#microservicos-bulkhead)
- [Fallback](#microservicos-fallback)
- [API Gateway](#microservicos-api-gateway)
- [Service Discovery](#microservicos-service-discovery)
- [Load Balancing](#microservicos-load-balancing)
- [Statelessness](#microservicos-stateless)
- [Horizontal Scaling](#microservicos-escalabilidade-horizontal)
- [High Availability](#microservicos-alta-disponibilidade)
- [Observability in Microservices](#microservicos-observabilidade-em-microservicos)
- [Distributed Tracing](#microservicos-tracing-distribuido)
- [Trace Context and Propagation](#microservicos-microservicos-trace-context-e-propagacao)
- [P95 and P99](#microservicos-p95-e-p99)
- [SLO and Error Budget](#microservicos-slo-e-error-budget)
- [Rate Limiting](#microservicos-rate-limiting)
- [Backpressure](#microservicos-backpressure)
- [Caching in Microservices](#microservicos-cache-em-microservicos)
- [Cache Stampede](#microservicos-cache-stampede)
- [API Versioning](#microservicos-versionamento-de-api)
- [Backward Compatibility](#microservicos-backward-compatibility)
- [Independent Deployment](#microservicos-deploy-independente)
- [Cascading Failure](#microservicos-falha-em-cascata)
- [Graceful Degradation](#microservicos-degradacao-graciosa)
- [Distributed Configuration](#microservicos-configuracao-distribuida)
- [Service-to-Service Security](#microservicos-seguranca-entre-servicos)
- [Interview Summary](#microservicos-resposta-completa-de-entrevista-microservicos)

---

<a id="microservicos-visao-geral-microservicos"></a>

## Microservices Overview

> Microservices divide an application into smaller, independently deployable services typically aligned with business capabilities. Main benefits are deployment autonomy, scaling, and ownership; the main cost is distributed complexity involving networks, observability, eventual consistency, security, versioning, and operations.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-quando-usar-microservicos"></a>

## When to Use Microservices

> I use microservices when clear domain boundaries and concrete autonomy requirements exist, such as independent scaling, different deployment cycles, failure isolation, distinct security requirements, or separate team ownership. I do not consider them a mandatory evolution for every monolith.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-quando-nao-usar-microservicos"></a>

## When Not to Use Microservices

> I avoid microservices when the domain is unclear, the team is small, workload does not justify distribution, or operational capability is limited. In those situations, a modular monolith usually delivers faster with less complexity.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-monolito-modular-vs-microservicos"></a>

## Modular Monolith vs Microservices

> A modular monolith has one deployment but separates code into modules with clear boundaries. Microservices extend that separation to independent processes and deployments. I generally prefer a modular monolith until scaling or operational autonomy warrants distribution.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-boundaries-de-servico"></a>

## Service Boundaries

> I define boundaries based on business capabilities and data ownership, not tables or technical layers. A well-defined service encapsulates its own model, rules, and persistence, reducing excessive cross-service calls.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-banco-por-servico"></a>

## Database per Service

> Each microservice should ideally own its data. Directly sharing a schema introduces strong coupling and allows one service to depend on another's internal structure. Integration should happen through APIs or events.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-shared-database-trade-off"></a>

## Shared Database — Trade-off

> A shared database initially simplifies joins and transactions but reduces autonomy and increases coupling. It may be acceptable during incremental migration, but I treat it as a temporary compromise when true service independence is the goal.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-comunicacao-sincrona"></a>

## Synchronous Communication

> Synchronous communication suits calls that require an immediate response. Its cost is temporal coupling: if a dependency is slow or unavailable, the caller experiences that directly. I therefore apply timeouts, circuit breakers, and observability to remote calls.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-comunicacao-assincrona"></a>

## Asynchronous Communication

> Asynchronous communication decouples producers and consumers in time and helps absorb spikes. It introduces eventual consistency, duplicates, ordering, retries, DLQs, and idempotency requirements. I use it when responses can arrive later or temporal decoupling matters.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-rest-vs-mensageria"></a>

## REST vs Messaging

> REST works better for immediate responses with a clear request-response contract. Messaging suits event propagation, asynchronous processing, and peak absorption. Many systems use both: a synchronous command to initiate an operation and an asynchronous event to communicate its consequences.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-consistencia-distribuida"></a>

## Distributed Consistency

> In microservices, I avoid trying to recreate a global ACID transaction across services. Each service generally keeps local transactions, while cross-service consistency is coordinated eventually using events, Sagas, Outbox, and idempotent operations.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-saga"></a>

## Saga

> A Saga coordinates a distributed business operation through several local transactions. If a step fails, compensating actions may undo earlier business effects. It can be implemented through orchestration or choreography.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-saga-orquestracao-vs-coreografia"></a>

## Saga — Orchestration vs Choreography

> Orchestration has an explicit flow coordinator, simplifying understanding and control. In choreography, services react to events, reducing centralization but making the flow harder to trace. I decide based on process complexity and governance needs.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-outbox-pattern"></a>

## Outbox Pattern

> Outbox addresses consistently updating a database and publishing an event without a distributed transaction. The application writes the business change and outbox record in the same local transaction. Another process then publishes the message.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-idempotencia-em-microservicos"></a>

## Idempotency in Microservices

> Idempotency allows an operation to be repeated without unintended additional effects. It matters for retries and messaging. I implement it with idempotency keys, unique constraints, processed-event tables, or naturally idempotent logic.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-microservicos-inbox-e-consumidor-idempotente"></a>

## Inbox and Idempotent Consumers

> I store the received event ID and business change in the same local transaction, enforcing uniqueness to prevent duplicate processing. I acknowledge consumption only after commit; external effects need their own approach, such as another outbox or idempotency key.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-microservicos-cdc-vs-polling-da-outbox"></a>

## CDC vs Outbox Polling

> Polling reads pending outbox records; CDC follows committed changes in the database log. CDC may reduce scanning queries but requires dedicated infrastructure and operations. For both, I account for redelivery, ordering, and record cleanup.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-timeout"></a>

## Timeout

> A timeout limits how long a service waits for a dependency. Without one, threads, connections, and other resources may remain blocked indefinitely. It must respect the request's overall time budget and be consistent with retries.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-microservicos-budget-de-latencia-e-deadline"></a>

## Latency Budget and Deadline

> For sequential calls, I add the time budgets of individual steps; for parallel calls, I consider the slowest path and coordination cost. I reserve time for local processing and retries and propagate the remaining deadline. Adding P99 values does not yield an exact total P99.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-microservicos-resultado-desconhecido-apos-timeout"></a>

## Unknown Outcome After Timeout

> A timeout means the response missed its deadline, not that the server stopped executing the operation. For effects such as charging a payment, I check the state or retry using the same idempotency key before starting a new operation.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-retry"></a>

## Retry

> Retries are appropriate for transient failures when operations support re-execution. I limit attempts, use backoff and jitter, and check idempotency. Poorly configured retries can amplify outages.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-circuit-breaker"></a>

## Circuit Breaker

> A circuit breaker temporarily stops calls to a dependency with a high failure rate, preventing wasted resources and cascading failures. It typically transitions through closed, open, and half-open states.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-microservicos-estados-do-circuit-breaker"></a>

## Circuit Breaker States

> Closed allows calls and measures failures; open rejects protected calls quickly; half-open permits trial requests. If the dependency recovers, the breaker closes; if failures persist, it opens again. A circuit breaker does not replace timeouts or concurrency limits.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-bulkhead"></a>

## Bulkhead

> Bulkheads isolate resources so one failing dependency cannot consume all application capacity. They can use separate pools, semaphores, or concurrency limits.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-fallback"></a>

## Fallback

> A fallback provides an alternative response when a dependency fails. I use it only when there is a valid business response. Silent fallback can mask incidents and return incorrect information.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-api-gateway"></a>

## API Gateway

> An API Gateway centralizes edge concerns such as routing, authentication, rate limiting, headers, and observability. I keep business logic out of the gateway to avoid creating a new central monolith.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-service-discovery"></a>

## Service Discovery

> Service discovery dynamically locates service instances. On Kubernetes, I generally use native Services and DNS before introducing a dedicated registry.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-load-balancing"></a>

## Load Balancing

> Load balancing spreads traffic across available instances. It can happen on clients, gateways, proxies, or infrastructure and complements health checks, autoscaling, and resilience.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-stateless"></a>

## Statelessness

> Stateless services do not rely on local session state between requests, simplifying scaling out and failover. Durable state belongs in appropriate systems such as databases, caches, or external storage.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-escalabilidade-horizontal"></a>

## Horizontal Scaling

> Horizontal scaling adds instances to increase capacity. To make it effective, I avoid local state, share only necessary data, and ensure the database, cache, and messaging can also handle growth.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-alta-disponibilidade"></a>

## High Availability

> High availability requires removing single points of failure throughout the chain: applications, databases, brokers, networks, and infrastructure. Two application instances are not enough if a critical dependency remains a singleton.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-observabilidade-em-microservicos"></a>

## Observability in Microservices

> For microservices, I consider logs, metrics, and traces essential for investigations. Correlation and trace IDs follow requests across services, while p95, p99, error rates, and saturation expose aggregate behavior.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-tracing-distribuido"></a>

## Distributed Tracing

> Distributed tracing shows a request's path across multiple services and the time spent in each step. It is especially useful for identifying dependencies that increase latency or cause failures.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-microservicos-trace-context-e-propagacao"></a>

## Trace Context and Propagation

> A trace connects spans from an operation across services. I propagate context through headers or message metadata and preserve continuity across asynchronous tasks; a correlation ID alone does not measure durations or automatically reconstruct an entire call tree.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-p95-e-p99"></a>

## P95 and P99

> P95 and P99 are thresholds at or below which approximately 95% and 99% of observed latencies fall. I track them per operation and dependency alongside volume and errors; averaging percentiles or summing P99s does not produce end-to-end P99.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-slo-e-error-budget"></a>

## SLO and Error Budget

> An SLO sets a reliability or latency target. An error budget represents how much error or downtime remains acceptable under that target. I use it to balance release velocity against reliability investment.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-rate-limiting"></a>

## Rate Limiting

> Rate limiting protects services and dependencies from excessive requests. It can be enforced per user, token, tenant, or IP. I also define expected client behavior, usually involving `429` and an appropriate retry policy.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-backpressure"></a>

## Backpressure

> Backpressure controls flow when producers generate work faster than consumers can process it. Bounded queues, semaphores, rate limits, or reactive mechanisms can provide it. Without backpressure, work accumulates until resources are exhausted.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-cache-em-microservicos"></a>

## Caching in Microservices

> Caching reduces latency and origin load but creates stale-data and invalidation risks. I specify TTL, keys, eviction, and cache-miss behavior. A cache is an optimization, not the source of truth.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-cache-stampede"></a>

## Cache Stampede

> A cache stampede occurs when a frequently accessed key expires and simultaneous requests hit the origin. Common strategies include jittered TTLs, per-key locking, single-flight, and early refresh.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-versionamento-de-api"></a>

## API Versioning

> Versioning is necessary when contracts must evolve without breaking consumers. I prefer backward-compatible changes whenever possible, such as adding optional fields instead of suddenly removing or renaming existing fields.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-backward-compatibility"></a>

## Backward Compatibility

> Backward compatibility allows older consumers to keep working while producers evolve. It reduces coordinated deployments and is especially important for APIs and asynchronous events.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-deploy-independente"></a>

## Independent Deployment

> Independent deployment is a key microservices goal, but is real only when contracts, databases, configuration, and dependencies are also decoupled. If every deployment requires coordination, the architecture remains strongly coupled.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-falha-em-cascata"></a>

## Cascading Failure

> Cascading failure occurs when degradation in one service consumes dependent services' resources and spreads the problem. Timeouts, circuit breakers, bulkheads, rate limits, and graceful degradation can limit the impact.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-degradacao-graciosa"></a>

## Graceful Degradation

> Graceful degradation keeps core features operational when secondary components fail. For example, checkout can continue when recommendations are unavailable. Fallbacks must be explicit and observable.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-configuracao-distribuida"></a>

## Distributed Configuration

> Distributed configuration centralizes properties used by multiple services but creates another operational dependency. I evaluate availability, versioning, secret security, and startup behavior when the configuration source is unavailable.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-seguranca-entre-servicos"></a>

## Service-to-Service Security

> Service-to-service security involves authentication, authorization, encryption in transit, least privilege, and secret protection. Even on an internal network, I do not assume unrestricted trust without evaluating the trust boundary.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="microservicos-resposta-completa-de-entrevista-microservicos"></a>

## Interview Summary

> Microservices provide independent deployment and scaling at the cost of network, data, and operational complexity. I define domain boundaries and clear contracts, combine local transactions with Sagas and Outbox, and protect dependencies with limits. I distribute only when benefits justify that complexity.

[↑ Topic Contents](#microservicos-microservicos-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-kafka-sumario"></a>

# 21. Kafka

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Kafka Overview](#kafka-visao-geral-kafka)
- [Broker](#kafka-broker)
- [KRaft and Controller](#kafka-kafka-kraft-e-controller)
- [Topic](#kafka-topic)
- [Partition](#kafka-partition)
- [Offset](#kafka-offset)
- [Partition Key](#kafka-partition-key)
- [Producer](#kafka-producer)
- [Consumer](#kafka-consumer)
- [Consumer Group](#kafka-consumer-group)
- [More Partitions Than Consumers](#kafka-mais-particoes-que-consumidores)
- [More Consumers Than Partitions](#kafka-mais-consumidores-que-particoes)
- [Ordering](#kafka-ordenacao)
- [Leader and Replicas](#kafka-leader-e-replicas)
- [Replication Factor](#kafka-replication-factor)
- [ISR](#kafka-isr)
- [acks=0, 1, and all](#kafka-acks-0-1-e-all)
- [min.insync.replicas](#kafka-min-insync-replicas)
- [At-most-once](#kafka-at-most-once)
- [At-least-once](#kafka-at-least-once)
- [Exactly-once](#kafka-exactly-once)
- [Kafka Transactions and read_committed](#kafka-kafka-transacoes-kafka-e-read-committed)
- [Idempotent Producer](#kafka-producer-idempotente)
- [Idempotent Consumer](#kafka-consumer-idempotente)
- [Offset Commit](#kafka-commit-de-offset)
- [Auto Commit vs Manual Commit](#kafka-auto-commit-vs-manual-commit)
- [Rebalance](#kafka-rebalance)
- [max.poll.interval.ms and Slow Processing](#kafka-kafka-max-poll-interval-ms-e-processamento-lento)
- [Cooperative Rebalancing](#kafka-cooperative-rebalancing)
- [Consumer Lag](#kafka-consumer-lag)
- [Lag in Production](#kafka-lag-em-producao)
- [Retention](#kafka-retention)
- [Log Compaction](#kafka-log-compaction)
- [Replay](#kafka-replay)
- [DLQ](#kafka-dlq)
- [Retries with Kafka](#kafka-retry-com-kafka)
- [Retry Topics and Ordering](#kafka-kafka-retry-topics-e-ordenacao)
- [Poison Pill](#kafka-poison-pill)
- [Schema Evolution](#kafka-schema-evolution)
- [Schema Registry](#kafka-schema-registry)
- [Backward, Forward, and Full Compatibility](#kafka-kafka-compatibilidade-backward-forward-e-full)
- [Kafka vs RabbitMQ](#kafka-kafka-vs-rabbitmq)
- [Kafka vs SQS](#kafka-kafka-vs-sqs)
- [Kafka vs Database](#kafka-kafka-vs-banco-de-dados)
- [Kafka and Outbox](#kafka-kafka-e-outbox)
- [Kafka Connect and Kafka Streams](#kafka-kafka-kafka-connect-e-kafka-streams)
- [Kafka and Spring](#kafka-kafka-e-spring)
- [@KafkaListener](#kafka-kafkalistener)
- [Concurrency in Spring Kafka](#kafka-concorrencia-no-spring-kafka)
- [Partitions and Scalability](#kafka-particoes-e-escalabilidade)
- [Hot Partition](#kafka-hot-partition)
- [Batching](#kafka-batching)
- [Consumer Backpressure](#kafka-backpressure-no-consumidor)
- [Kafka Observability](#kafka-observabilidade-kafka)
- [Broker Failure](#kafka-falha-de-broker)
- [Kafka and Per-Entity Ordering](#kafka-kafka-e-ordenacao-por-entidade)
- [Kafka and Database Transactions](#kafka-kafka-e-transacao-com-banco)
- [Interview Summary](#kafka-resposta-completa-de-entrevista-kafka)

---

<a id="kafka-visao-geral-kafka"></a>

## Kafka Overview

> Apache Kafka is a distributed event-streaming platform based on an append-only log. Producers publish records to topics, topics are split into partitions, and consumers read events while tracking positions through offsets. Kafka excels at throughput, retention, and distributed event processing.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-broker"></a>

## Broker

> A broker is a Kafka server that stores partitions and serves producers and consumers. A cluster has multiple brokers to distribute load and provide redundancy.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-kafka-kraft-e-controller"></a>

## KRaft and Controller

> KRaft maintains cluster metadata through a Raft-based controller quorum. Brokers store and serve records; controllers coordinate metadata and leadership. This distinction helps separate control-plane problems from data-flow problems.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-topic"></a>

## Topic

> A topic is a logical category of events. It is divided into partitions, and each record belongs to one partition. Partition count influences parallelism, throughput, and ordering.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-partition"></a>

## Partition

> A partition is an ordered, append-only log within a topic. Ordering is guaranteed within a partition, not globally across all topic partitions.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-offset"></a>

## Offset

> An offset identifies a record's position within a partition. Consumers use offsets to track progress. An offset is not a global event identifier; it is meaningful only within its partition.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-partition-key"></a>

## Partition Key

> A partition key influences which partition stores an event. Events with the same key generally land in the same partition, helping preserve per-entity order such as `orderId`. The trade-off is hotspot risk when a small number of keys generate most traffic.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-producer"></a>

## Producer

> A producer sends events to topics and chooses partitions using keys, partitioning strategies, or default distribution. In production, I consider `acks`, retries, producer idempotency, batching, and compression.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-consumer"></a>

## Consumer

> A consumer reads partition records and tracks progress through offsets. Processing must account for failures, offset commits, idempotency, and maximum processing time.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-consumer-group"></a>

## Consumer Group

> A consumer group allows parallel consumption of a topic. Within a group, each partition is assigned to at most one active consumer at a time. Different groups consume the same event stream independently.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-mais-particoes-que-consumidores"></a>

## More Partitions Than Consumers

> When a group has more partitions than consumers, some consumers process multiple partitions. This increases potential parallelism until consumer count reaches partition count.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-mais-consumidores-que-particoes"></a>

## More Consumers Than Partitions

> When a group has more consumers than partitions, extra consumers remain idle because two consumers in the same group cannot process a partition simultaneously.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-ordenacao"></a>

## Ordering

> Kafka guarantees ordering only within a partition. To preserve order per customer or order, I use a stable key so related events land in the same partition. This reduces parallelism for that key but preserves sequence.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-leader-e-replicas"></a>

## Leader and Replicas

> Each partition has a leader and may have replicas. Producers and consumers primarily interact with the leader. Replicas maintain copies to enable failover if the leader becomes unavailable.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-replication-factor"></a>

## Replication Factor

> Replication factor determines how many copies of each partition exist in the cluster. Higher values improve fault tolerance but consume more disk and replication bandwidth.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-isr"></a>

## ISR

> ISR, or In-Sync Replicas, is the set of replicas sufficiently synchronized with the leader. It matters for durability and failover decisions.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-acks-0-1-e-all"></a>

## acks=0, 1, and all

> `acks=0` waits for no acknowledgment; `acks=1` waits for the leader; `acks=all` waits for replicas currently in ISR. `min.insync.replicas` sets the minimum synchronized replicas required to accept writes with `acks=all`; it is not a fixed acknowledgment count when ISR is larger. [Reference](https://kafka.apache.org/41/design/design/)

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-min-insync-replicas"></a>

## min.insync.replicas

> `min.insync.replicas` defines the minimum synchronized replica count needed to accept writes when a producer uses `acks=all`. Together, these settings help prevent acknowledging writes with insufficient redundancy.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-at-most-once"></a>

## At-most-once

> At-most-once prioritizes avoiding duplicates but can lose messages, for example when an offset is committed before processing completes and the application crashes afterward.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-at-least-once"></a>

## At-least-once

> At-least-once prioritizes preventing loss but permits duplicates. The offset is commonly committed after processing. If an application processes a message and crashes before committing, delivery may repeat. Hence idempotency matters.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-exactly-once"></a>

## Exactly-once

> Exactly-once in Kafka depends on scope. Kafka offers idempotent producers and transactions for Kafka-to-Kafka workflows. That does not automatically guarantee end-to-end exactly-once when external effects such as databases or HTTP calls are involved.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-kafka-transacoes-kafka-e-read-committed"></a>

## Kafka Transactions and read_committed

> A Kafka transaction can group publications and offsets within Kafka. `read_committed` prevents records from aborted or still-open transactions from being delivered as committed results; this does not automatically include a separate database commit. [Reference](https://kafka.apache.org/41/design/design/)

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-producer-idempotente"></a>

## Idempotent Producer

> An idempotent producer prevents duplicates caused by its own retries within Kafka's guarantees. It does not solve business duplicates caused by consumer reprocessing.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-consumer-idempotente"></a>

## Idempotent Consumer

> An idempotent consumer ensures redelivery of the same event does not produce a duplicate effect. Common strategies include idempotency keys, unique constraints, processed-event tables, or naturally idempotent operations.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-commit-de-offset"></a>

## Offset Commit

> A committed offset specifies where a group resumes; it normally stores the next offset to consume. Committing before completion can lose work; committing afterward can cause redelivery. With parallel execution, I do not advance past earlier incomplete records.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-auto-commit-vs-manual-commit"></a>

## Auto Commit vs Manual Commit

> Auto commit simplifies configuration but offers less control over the exact progress checkpoint. Manual commit can align offsets with processing but requires more careful handling of errors and rebalances.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-rebalance"></a>

## Rebalance

> Rebalancing redistributes a group's partitions when consumers join or leave or metadata changes. Consumption may pause during the process. Frequent rebalances harm stability and throughput.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-kafka-max-poll-interval-ms-e-processamento-lento"></a>

## max.poll.interval.ms and Slow Processing

> The maximum interval between poll calls limits how long a consumer may take before advancing the expected poll cycle. Slow processing can cause assignment loss; I tune batch sizes and execution without committing offsets before work completes.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-cooperative-rebalancing"></a>

## Cooperative Rebalancing

> Cooperative rebalancing reduces disruption by moving partitions incrementally where possible, rather than stopping the entire group. It can reduce pauses in larger groups.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-consumer-lag"></a>

## Consumer Lag

> Consumer lag describes the distance between a reference log position and the consumer position, depending on the metric. Committed-offset lag may include processed but uncommitted work; I compare trends, event age, throughput, and errors when interpreting delays.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-lag-em-producao"></a>

## Lag in Production

> I monitor lag per partition and group alongside throughput, processing time, errors, and rebalances. Lag alone is insufficient: I must determine whether it grows continuously or reflects a temporary spike.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-retention"></a>

## Retention

> Kafka retains events based on time or size regardless of whether they have been consumed. This distinguishes it from traditional queues and enables replay. Storage use is the trade-off.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-log-compaction"></a>

## Log Compaction

> Log compaction asynchronously removes older versions of a key, preserving its latest state according to policy. Duplicate keys may coexist before cleanup; records with null values, called tombstones, represent deletion and have configurable retention.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-replay"></a>

## Replay

> Replay means seeking a consumer to earlier offsets and processing events again. This supports state reconstruction and consumer fixes, but requires idempotent processing and care with external effects.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-dlq"></a>

## DLQ

> A dead-letter queue stores messages that cannot be processed after retry policy exhaustion. It prevents indefinite blocking but requires inspection, fixes, and reprocessing processes. A DLQ should not become a permanent dumping ground for ignored failures.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-retry-com-kafka"></a>

## Retries with Kafka

> Retries may run immediately or with delay via retry topics, depending on the design. I avoid aggressive loops on the same event because they block a partition and reduce throughput. I also consider idempotency and attempt limits.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-kafka-retry-topics-e-ordenacao"></a>

## Retry Topics and Ordering

> Moving a failed event to another topic can let later events proceed, breaking business ordering by key. I decide whether the flow must stop, can tolerate reordering, or needs version controls before selecting a retry strategy.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-poison-pill"></a>

## Poison Pill

> A poison pill is a message that repeatedly fails, for example due to an invalid payload. Without handling, it can block partition progress. Strategies include DLQs, dedicated deserialization-error handling, and retry limits.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-schema-evolution"></a>

## Schema Evolution

> Events are contracts. To evolve schemas, I favor backward-compatible changes such as adding optional fields. Formats such as Avro, Protobuf, and JSON Schema can be paired with registries to govern compatibility.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-schema-registry"></a>

## Schema Registry

> A schema registry centralizes schemas and compatibility rules. It helps producers and consumers evolve without breaking contracts but adds an operational component.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-kafka-compatibilidade-backward-forward-e-full"></a>

## Backward, Forward, and Full Compatibility

> Backward compatibility lets a new reader consume old data; forward compatibility lets an old reader consume new data; full compatibility requires both. Rules depend on format and registry configuration, and structural compatibility does not guarantee preserved business semantics.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-kafka-vs-rabbitmq"></a>

## Kafka vs RabbitMQ

> Kafka is centered on distributed logs, retention, high event throughput, and natural replay. RabbitMQ is traditionally queue- and message-routing-oriented. The decision follows workload: event streaming and history favor Kafka, while work queues and complex routing may favor RabbitMQ.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-kafka-vs-sqs"></a>

## Kafka vs SQS

> Kafka offers partitioned logs, replay, and fine-grained consumption control. SQS is a managed queue with a simpler operational model. I choose Kafka for streaming, multiple independent consumers, replay, and high throughput; SQS for queue-based decoupling with less operational overhead.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-kafka-vs-banco-de-dados"></a>

## Kafka vs Database

> Kafka does not replace a transactional database. It is excellent as an event log and integration backbone; relational databases remain better suited for transactional queries and business-data consistency. Both frequently coexist.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-kafka-e-outbox"></a>

## Kafka and Outbox

> Outbox is a common strategy for publishing Kafka events consistently with a database transaction. The application writes business data and the outbox record together, then a publisher sends the event to Kafka.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-kafka-kafka-connect-e-kafka-streams"></a>

## Kafka Connect and Kafka Streams

> Kafka Connect integrates sources and sinks through connectors. Kafka Streams is a library for stream processing within applications, including partitioned operations and state. Both reduce infrastructure code but still require understanding contracts, failures, and processing guarantees.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-kafka-e-spring"></a>

## Kafka and Spring

> Within the Spring ecosystem, `spring-kafka` provides producers, consumers, listeners, serializers, error handling, and transaction integration. Despite Spring abstractions, I still understand partitions, offsets, rebalances, and delivery semantics.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-kafkalistener"></a>

## @KafkaListener

> `@KafkaListener` simplifies writing Spring consumers. I still configure group ID, concurrency, acknowledgment mode, error handlers, and deserialization according to service needs.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-concorrencia-no-spring-kafka"></a>

## Concurrency in Spring Kafka

> Listener concurrency starts multiple consumers within the same application instance. Effective parallelism remains limited by partition count. Increasing threads beyond the number of partitions does not improve group throughput.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-particoes-e-escalabilidade"></a>

## Partitions and Scalability

> Partition count sets the upper limit on parallel consumption within a group. More partitions increase potential parallelism but also metadata, files, rebalance overhead, and operational complexity.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-hot-partition"></a>

## Hot Partition

> A hot partition receives disproportionately high traffic, typically due to uneven key distribution. It limits throughput even with many consumers. I revisit key strategy and workload distribution.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-batching"></a>

## Batching

> Producer batching groups records to improve throughput and lower network overhead. The trade-off is additional latency while batches form. Compression also saves bandwidth at a CPU cost.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-backpressure-no-consumidor"></a>

## Consumer Backpressure

> If downstream processing is slower than consumption, lag grows. I control concurrency, partition pauses, dependency capacity, and processing throughput rather than adding consumers without examining the bottleneck.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-observabilidade-kafka"></a>

## Kafka Observability

> In production, I monitor lag, throughput, error rates, rebalances, processing time, disk use, replica health, and produce/fetch latency. Per-partition lag is particularly important for consumers.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-falha-de-broker"></a>

## Broker Failure

> When a broker fails, partitions with suitable replicas can elect new leaders and continue operating. Availability depends on replication factor, ISR, and durability settings.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-kafka-e-ordenacao-por-entidade"></a>

## Kafka and Per-Entity Ordering

> A stable key keeps an entity's events in the same partition, preserving log order. Applications must preserve that order while processing; parallel execution, retry topics, and repartitioning changes may alter the observed sequence.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-kafka-e-transacao-com-banco"></a>

## Kafka and Database Transactions

> There is no generic simple ACID transaction spanning Kafka and a relational database. To achieve consistency, I normally use Outbox or idempotent designs rather than rely on distributed transactions.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="kafka-resposta-completa-de-entrevista-kafka"></a>

## Interview Summary

> Kafka structures events into retained, partitioned topics with offsets. I design keys, replication, and consumption around ordering, parallelism, and durability. I control commits, duplicates, retries, and lag; Kafka's transactional guarantees do not automatically make database and API effects atomic.

[↑ Topic Contents](#kafka-kafka-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-cloud-sumario"></a>

# 22. Spring Cloud

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#spring-spring-cloud-importante)
- [Distributed Configuration](#spring-spring-cloud-configuracao-distribuida)
- [Service Discovery](#spring-spring-cloud-service-discovery)
- [Spring Cloud Gateway](#spring-spring-cloud-cuidado)
- [OpenFeign](#spring-spring-cloud-openfeign)
- [Resilience: Timeout, Retry, Circuit Breaker, and Bulkhead](#spring-spring-cloud-4-bulkhead)
- [Spring Cloud Stream](#spring-spring-cloud-trade-off)
- [Distributed Observability](#spring-spring-cloud-observabilidade-distribuida)
- [Interview Summary](#spring-spring-cloud-resposta-completa-de-entrevista)

---

<a id="spring-spring-cloud-importante"></a>

## Overview

> Spring Cloud complements Spring Boot with patterns and integrations for distributed systems, including centralized configuration, discovery, gateways, load balancing, declarative clients, resilience, and messaging.

[↑ Topic Contents](#spring-spring-cloud-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-cloud-configuracao-distribuida"></a>

## Distributed Configuration

> Config Server centralizes properties for multiple services. It helps with consistency and versioning but becomes critical infrastructure that requires high availability and security.

[↑ Topic Contents](#spring-spring-cloud-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-cloud-service-discovery"></a>

## Service Discovery

> Service discovery dynamically locates instances. On Kubernetes, I first evaluate built-in mechanisms before adding a dedicated registry.

[↑ Topic Contents](#spring-spring-cloud-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-cloud-cuidado"></a>

## Spring Cloud Gateway

> Gateway centralizes edge concerns such as routing, filters, and cross-cutting policies. I keep business rules out of it.

[↑ Topic Contents](#spring-spring-cloud-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-cloud-openfeign"></a>

## OpenFeign

> OpenFeign simplifies HTTP client code but does not remove network limitations. I still configure timeouts, error handling, resilience, and observability.

[↑ Topic Contents](#spring-spring-cloud-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-cloud-4-bulkhead"></a>

## Resilience: Timeout, Retry, Circuit Breaker, and Bulkhead

> For remote calls, I combine timeouts, circuit breakers, and, where appropriate, retries with backoff and jitter. Retries must respect idempotency. Bulkheads prevent a degraded dependency from exhausting all local resources.

[↑ Topic Contents](#spring-spring-cloud-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-cloud-trade-off"></a>

## Spring Cloud Stream

> Spring Cloud Stream simplifies messaging integration but does not eliminate the need to understand the broker. With Kafka, I still need to know partitions, consumer groups, offsets, and delivery semantics.

[↑ Topic Contents](#spring-spring-cloud-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-cloud-observabilidade-distribuida"></a>

## Distributed Observability

> In distributed systems, isolated metrics are insufficient. I correlate logs, metrics, and traces to locate where errors or latency were introduced along a request chain.

[↑ Topic Contents](#spring-spring-cloud-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="spring-spring-cloud-resposta-completa-de-entrevista"></a>

## Interview Summary

> Spring Cloud integrates configuration, discovery, gateways, communication, and resilience for distributed systems. These abstractions do not eliminate network failures or guarantee consistency. I avoid duplicating platform-native capabilities and monitor the entire interaction chain through observability.

[↑ Topic Contents](#spring-spring-cloud-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-sumario"></a>

# 23. Docker and Kubernetes

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [Overview](#containers-kubernetes-visao-geral)
- [Image vs Container vs Virtual Machine](#containers-kubernetes-imagem-vs-container-vs-maquina-virtual)
- [Dockerfile, Layers, and Build Context](#containers-kubernetes-dockerfile-layers-e-build-context)
- [Multi-stage Builds and Java Runtime](#containers-kubernetes-multi-stage-build-e-runtime-java)
- [Tags and Digests](#containers-kubernetes-tags-e-digests)
- [Volumes and Persistence](#containers-kubernetes-volumes-e-persistencia)
- [Docker Compose](#containers-kubernetes-docker-compose)
- [Cluster, Node, and Control Plane](#containers-kubernetes-cluster-node-e-control-plane)
- [Pod](#containers-kubernetes-pod)
- [Deployment and ReplicaSet](#containers-kubernetes-deployment-e-replicaset)
- [Service, DNS, and Inbound Traffic](#containers-kubernetes-service-dns-e-entrada-de-trafego)
- [ConfigMap and Secret](#containers-kubernetes-configmap-e-secret)
- [Requests, Limits, and OOMKilled](#containers-kubernetes-requests-limits-e-oomkilled)
- [Java Heap Inside a Container](#containers-kubernetes-heap-java-dentro-do-container)
- [Startup, Readiness, and Liveness Probes](#containers-kubernetes-startup-readiness-e-liveness-probes)
- [Graceful Shutdown](#containers-kubernetes-graceful-shutdown)
- [HPA and Autoscaling](#containers-kubernetes-hpa-e-autoscaling)
- [StatefulSet, Job, and CronJob](#containers-kubernetes-statefulset-job-e-cronjob)
- [RBAC and NetworkPolicy](#containers-kubernetes-rbac-e-networkpolicy)
- [Pod Diagnostics](#containers-kubernetes-diagnostico-de-pods)
- [Interview Summary](#containers-kubernetes-resumo-de-entrevista)

---

<a id="containers-kubernetes-visao-geral"></a>

## Overview

> Containers package applications and dependencies in isolated runtime environments. Kubernetes orchestrates these workloads declaratively; neither removes the need to manage persistence, shutdown, capacity, and application failures.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-imagem-vs-container-vs-maquina-virtual"></a>

## Image vs Container vs Virtual Machine

> An image is a bundle of layers and configuration; a container is an isolated execution of that image sharing the host kernel. A virtual machine typically runs its own guest operating system, with different isolation and overhead characteristics.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-dockerfile-layers-e-build-context"></a>

## Dockerfile, Layers, and Build Context

> A Dockerfile describes image construction. Step order affects cache reuse; I keep build contexts small and never copy secrets into image layers because deleting them later may leave them in image history.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-multi-stage-build-e-runtime-java"></a>

## Multi-stage Builds and Java Runtime

> I separate compilation from execution so the final image contains only what is needed. I use a runtime compatible with the JAR and run as a nonprivileged user where possible to reduce image size and process permissions.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-tags-e-digests"></a>

## Tags and Digests

> Tags may point to different images over time. A digest identifies the image content and pins the exact artifact executed; linking it to a commit simplifies auditing and rollbacks.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-volumes-e-persistencia"></a>

## Volumes and Persistence

> Data stored only in a container's writable layer may disappear when it is replaced. I use volumes or external services for durable state, with explicit backup and recovery strategies.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-docker-compose"></a>

## Docker Compose

> Compose describes services, networks, and volumes for running multi-container environments. It is useful for development and compatible scenarios but does not itself provide the same capabilities as a distributed orchestrator.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-cluster-node-e-control-plane"></a>

## Cluster, Node, and Control Plane

> A cluster includes the control plane and the nodes running workloads. Kubernetes reconciles desired and observed states; high availability also requires planning for node, zone, and dependency failures.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-pod"></a>

## Pod

> A Pod is the smallest deployable unit in Kubernetes and may contain containers sharing networking and volumes. Pods are replaceable; their addresses and temporary storage are not durable application identities.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-deployment-e-replicaset"></a>

## Deployment and ReplicaSet

> A Deployment declares versions and desired replica counts and manages rollouts through ReplicaSets. Multiple replicas do not guarantee high availability if they share a failure point or are distributed poorly.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-service-dns-e-entrada-de-trafego"></a>

## Service, DNS, and Inbound Traffic

> A Service supplies a stable destination for selected Pods and can be discovered through DNS. External access depends on appropriate resources and controllers, such as LoadBalancer, Ingress, or Gateway API.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-configmap-e-secret"></a>

## ConfigMap and Secret

> ConfigMap stores nonsensitive configuration; Secret stores sensitive data. Base64 encoding is not encryption: I control RBAC, encryption at rest, and workload access without embedding credentials in images or repositories.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-requests-limits-e-oomkilled"></a>

## Requests, Limits, and OOMKilled

> Requests inform scheduling; limits constrain resource use. CPU may be throttled, while exceeding memory can terminate the container; an OS-level `OOMKilled` is not the same as a JVM-thrown `OutOfMemoryError`.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-heap-java-dentro-do-container"></a>

## Java Heap Inside a Container

> Total memory includes heap, Metaspace, stacks, direct buffers, and other native areas. I leave headroom beyond `-Xmx` and monitor container limits; blindly increasing heap size may trigger out-of-memory termination.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-startup-readiness-e-liveness-probes"></a>

## Startup, Readiness, and Liveness Probes

> A startup probe allows initialization before other checks apply. Readiness determines whether a Pod receives traffic; liveness may restart a container. Temporary dependency unavailability should not, by default, trigger mass restarts through liveness probes. [Reference](https://kubernetes.io/docs/concepts/workloads/pods/probes/)

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-graceful-shutdown"></a>

## Graceful Shutdown

> During shutdown, an application should stop accepting new work, safely complete or interrupt active tasks, and release resources. I align Kubernetes termination deadlines with HTTP, consumers, and running tasks to reduce loss during rollouts.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-hpa-e-autoscaling"></a>

## HPA and Autoscaling

> HPA adjusts replica counts based on configured metrics. Scaling Pods does not automatically increase database connections or broker capacity; I choose useful signals, appropriate requests, and limits aligned with dependencies.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-statefulset-job-e-cronjob"></a>

## StatefulSet, Job, and CronJob

> StatefulSet supports workloads needing stable identity and persistent volume associations. A Job runs work to completion; a CronJob schedules runs. Even with scheduling, I design idempotent tasks to tolerate failures and reruns.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-rbac-e-networkpolicy"></a>

## RBAC and NetworkPolicy

> RBAC restricts actions on the Kubernetes API. NetworkPolicy restricts traffic between workloads when supported by the network plugin; I apply both using least privilege and separation of responsibilities.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-diagnostico-de-pods"></a>

## Pod Diagnostics

> I start with events, status, and termination reason, then investigate logs, probes, resources, and connectivity. `CrashLoopBackOff` indicates repeated restarts, not a root cause; the cause may be application code, configuration, permissions, or dependencies.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="containers-kubernetes-resumo-de-entrevista"></a>

## Interview Summary

> I package Java into reproducible images and configure appropriate resources, probes, and graceful shutdown. In Kubernetes, I separate execution, networking, configuration, and persistence and investigate failures using evidence before increasing replicas or memory.

[↑ Topic Contents](#containers-kubernetes-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-aws-sumario"></a>

# 24. AWS

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [AWS Overview](#aws-visao-geral-aws)
- [Regions and Availability Zones](#aws-regiao-e-availability-zone)
- [Multi-AZ High Availability](#aws-alta-disponibilidade-multi-az)
- [Multi-Region](#aws-multi-region)
- [Shared Responsibility Model](#aws-shared-responsibility-model)
- [IAM](#aws-iam)
- [IAM User vs Role](#aws-iam-user-vs-role)
- [IAM Policy](#aws-iam-policy)
- [Identity-based vs Resource-based Policies](#aws-identity-based-vs-resource-based-policy)
- [STS and AssumeRole](#aws-sts-e-assumerole)
- [VPC](#aws-vpc)
- [Public vs Private Subnets](#aws-subnet-publica-vs-privada)
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
- [S3 Consistency](#aws-aws-consistencia-do-s3)
- [S3 Versioning](#aws-s3-versioning)
- [S3 Lifecycle](#aws-s3-lifecycle)
- [S3 Presigned URLs](#aws-s3-presigned-url)
- [CloudFront](#aws-cloudfront)
- [Route 53](#aws-route-53)
- [RDS](#aws-rds)
- [RDS Multi-AZ: Instance vs Cluster](#aws-rds-multi-az)
- [RDS Read Replica](#aws-rds-read-replica)
- [Aurora](#aws-aurora)
- [DynamoDB](#aws-dynamodb)
- [DynamoDB Partition Key](#aws-dynamodb-partition-key)
- [DynamoDB Sort Key](#aws-dynamodb-sort-key)
- [DynamoDB GSI and LSI](#aws-dynamodb-gsi-e-lsi)
- [DynamoDB Consistency](#aws-dynamodb-consistency)
- [DynamoDB On-Demand vs Provisioned](#aws-dynamodb-on-demand-vs-provisioned)
- [ElastiCache](#aws-elasticache)
- [Lambda](#aws-lambda)
- [Lambda Cold Start](#aws-lambda-cold-start)
- [Lambda SnapStart](#aws-aws-lambda-snapstart)
- [Lambda Concurrency](#aws-lambda-concurrency)
- [Lambda Idempotency](#aws-lambda-idempotencia)
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
- [RTO and RPO](#aws-rto-e-rpo)
- [Disaster Recovery](#aws-disaster-recovery)
- [Well-Architected Framework](#aws-well-architected-framework)
- [Cost Optimization](#aws-cost-optimization)
- [NAT Gateway Cost](#aws-nat-gateway-cost)
- [Interview Summary](#aws-resposta-completa-de-entrevista-aws)

---

<a id="aws-visao-geral-aws"></a>

## AWS Overview

> For interviews, I organize AWS into five areas: compute, networking, storage/databases, asynchronous integration, and security/observability. The main point is not memorizing service names but choosing among alternatives based on availability, scalability, security, cost, and operations.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-regiao-e-availability-zone"></a>

## Regions and Availability Zones

> An AWS Region is a geographic area containing multiple Availability Zones. AZs are isolated locations within one region, connected with low latency. For regional high availability, I generally distribute resources across multiple AZs.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-alta-disponibilidade-multi-az"></a>

## Multi-AZ High Availability

> Multi-AZ reduces the impact of a zone failure. For web applications, it typically means instances spread across AZs, a load balancer, and redundant dependencies. Putting applications in two AZs achieves little if the database or another critical component remains a single point of failure.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-multi-region"></a>

## Multi-Region

> Multi-Region is appropriate when availability, disaster recovery, global latency, or data residency requirements justify operating in multiple regions. Cost and complexity rise because replication, routing, consistency, and cross-region failover must be managed.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-shared-responsibility-model"></a>

## Shared Responsibility Model

> Under the shared responsibility model, AWS secures the cloud, including data centers, hardware, and physical infrastructure; customers secure what they run in the cloud, including IAM, network settings, data, service-dependent patches, and application controls.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-iam"></a>

## IAM

> IAM controls authentication and authorization within AWS. I work with users, roles, and policies under least privilege: granting only necessary permissions for the smallest practical duration and scope.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-iam-user-vs-role"></a>

## IAM User vs Role

> An IAM User is a persistent identity. An IAM Role is an assumable identity, usually temporary, used by applications, AWS services, or federated users. For EC2, ECS, and Lambda workloads, I prefer roles over static access keys.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-iam-policy"></a>

## IAM Policy

> An IAM Policy defines permissions through elements such as `Effect`, `Action`, `Resource`, and conditions. Effective authorization comes from combining applicable policies, remembering that an explicit `Deny` takes precedence over `Allow`.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-identity-based-vs-resource-based-policy"></a>

## Identity-based vs Resource-based Policies

> Identity-based policies attach to users, groups, or roles. Resource-based policies attach to resources such as S3 buckets or SQS queues. Resource policies are especially useful for cross-account access.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-sts-e-assumerole"></a>

## STS and AssumeRole

> AWS STS issues temporary credentials. `AssumeRole` allows an identity to assume a role and receive temporary permissions. This is important for cross-account access and avoiding distribution of permanent credentials.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-vpc"></a>

## VPC

> A VPC is an isolated AWS virtual network. Within it, I configure CIDR ranges, subnets, route tables, gateways, and security groups. I normally separate public and private resources based on exposure requirements.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-subnet-publica-vs-privada"></a>

## Public vs Private Subnets

> A subnet is public when its route table provides a direct route to an Internet Gateway. An IPv4 instance also needs a public address and compatible rules to use that path to the internet. Private subnets have no such direct route.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-internet-gateway"></a>

## Internet Gateway

> An Internet Gateway connects a VPC to the internet. Public access for an instance also requires correct routes, a public address or equivalent, and appropriate security rules.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-nat-gateway"></a>

## NAT Gateway

> A NAT Gateway lets resources in private subnets initiate internet connections without accepting externally initiated inbound connections. It is common for updates or outbound API calls but can be expensive, especially at high traffic volumes.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-security-group-vs-nacl"></a>

## Security Group vs NACL

> Security Groups are stateful and operate at the network-interface or resource level. NACLs are stateless and operate at the subnet level. Security Groups usually provide the main workload-level controls; NACLs add broader subnet controls.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-route-table"></a>

## Route Table

> A route table determines where subnet traffic is sent. Destinations may use the VPC local route, Internet Gateway, NAT Gateway, Transit Gateway, or peering. Connectivity investigations often begin with route analysis.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-vpc-endpoint"></a>

## VPC Endpoint

> A VPC Endpoint allows access to supported AWS services without traversing the public internet. It improves security posture and can reduce reliance on NAT for some traffic. Gateway and Interface endpoint types exist depending on the service.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-ec2"></a>

## EC2

> EC2 provides on-demand virtual machines. In interviews, I consider instance types, AMIs, storage, networking, autoscaling, and replacement strategy. For stateless applications, I treat instances as disposable and automate bootstrap and deployment.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-auto-scaling-group"></a>

## Auto Scaling Group

> An Auto Scaling Group maintains a desired instance count and can adjust capacity based on metrics. It also replaces unhealthy instances. I typically combine it with load balancers and health checks.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-alb-vs-nlb"></a>

## ALB vs NLB

> Application Load Balancer operates at layer 7 and understands HTTP/HTTPS, supporting host- and path-based routing. Network Load Balancer operates at layer 4 and is suited to TCP/UDP/TLS, high performance, and lower-level networking requirements.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-ebs"></a>

## EBS

> EBS is block storage normally attached to EC2. It suits system disks, self-managed databases, and workloads requiring block devices. Persistence and performance depend on volume type and configuration.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-efs"></a>

## EFS

> EFS is a managed filesystem accessible from multiple instances, useful when machines must share files. Its model and performance differ from EBS, which provides block storage.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-s3"></a>

## S3

> S3 is highly durable, scalable object storage. I use it for files, backups, artifacts, data lakes, and static content. It is not a traditional filesystem or relational database.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-aws-consistencia-do-s3"></a>

## S3 Consistency

> S3 provides strong read and list consistency after successful writes within documented scopes. This does not make changes across multiple objects a single transaction or eliminate external caches such as CDNs that may serve earlier content. [Reference](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-s3-versioning"></a>

## S3 Versioning

> Versioning preserves multiple versions of an object and helps recover from accidental deletion or overwriting. The trade-off is higher storage use, so I usually combine it with lifecycle policies.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-s3-lifecycle"></a>

## S3 Lifecycle

> Lifecycle rules move aging objects to cheaper storage classes or automatically expire them according to age. They are useful for reducing costs of older data.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-s3-presigned-url"></a>

## S3 Presigned URLs

> A presigned URL grants temporary access to an S3 object without making its bucket public. It supports direct client upload or download, reducing application traffic.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-cloudfront"></a>

## CloudFront

> CloudFront is AWS's CDN. It distributes content from edge locations, reducing latency and origin load. It can use S3, ALB, or other origins and integrate with controls such as WAF.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-route-53"></a>

## Route 53

> Route 53 is AWS's DNS service. Beyond DNS resolution, it offers routing policies such as weighted, latency-based, and failover. It is useful for multi-region architectures and recovery strategies.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-rds"></a>

## RDS

> RDS is a managed relational database service. AWS handles provisioning, backups, and some operations depending on configuration. I am still responsible for schemas, indexes, queries, capacity, and access architecture.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-rds-multi-az"></a>

## RDS Multi-AZ: Instance vs Cluster

> In a Multi-AZ DB instance deployment, the standby supports failover rather than reads. A Multi-AZ DB cluster has instances that can also serve reads. I verify the deployment type before claiming Multi-AZ improves read capacity. [Reference](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.html)

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-rds-read-replica"></a>

## RDS Read Replica

> Read replicas scale reads and may replicate asynchronously. Because lag may occur, I do not assume immediately consistent reads after writes. Multi-AZ and read replicas solve different problems.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-aurora"></a>

## Aurora

> Aurora is a MySQL- and PostgreSQL-compatible relational database with AWS-managed distributed storage. It can offer high availability and read scaling but still requires good schema, index, and query design.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-dynamodb"></a>

## DynamoDB

> DynamoDB is a fully managed NoSQL key-value/document database designed for low latency and horizontal scaling. Design begins with access patterns because partition keys, sort keys, and indexes determine data distribution and querying.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-dynamodb-partition-key"></a>

## DynamoDB Partition Key

> A partition key influences how items are distributed internally. Poor key distribution can create hot partitions and constrain throughput. I choose keys with good cardinality and distribution.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-dynamodb-sort-key"></a>

## DynamoDB Sort Key

> A sort key stores multiple items under one partition key and supports ordered or range queries within that partition. It is useful for time series, events, or related entities.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-dynamodb-gsi-e-lsi"></a>

## DynamoDB GSI and LSI

> A GSI provides a new access key using alternative partition/sort keys and is more flexible. An LSI shares the table's partition key and defines another sort key, with tighter restrictions. I select based on necessary access patterns.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-dynamodb-consistency"></a>

## DynamoDB Consistency

> DynamoDB uses eventually consistent reads by default and supports strongly consistent reads in supported contexts. Strong consistency consumes more capacity, so I select it according to use-case needs.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-dynamodb-on-demand-vs-provisioned"></a>

## DynamoDB On-Demand vs Provisioned

> On-Demand simplifies unpredictable workloads by having the service adjust capacity. Provisioned lets me specify capacity and may be more economical for predictable traffic. I decide based on traffic patterns and cost.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-elasticache"></a>

## ElastiCache

> ElastiCache provides managed engines compatible with Valkey, Redis OSS, and Memcached. I use it for caching and other low-latency access according to the engine, defining TTL, invalidation, capacity, and failure behavior. [Reference](https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/SelectEngine.html)

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-lambda"></a>

## Lambda

> Lambda runs functions on demand without managing servers. It suits event-driven processing, automation, and APIs with compatible workloads. Trade-offs include cold starts, execution limits, concurrency, and observability.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-lambda-cold-start"></a>

## Lambda Cold Start

> A cold start is the extra time needed to initialize a new execution environment. With Java it may be more noticeable depending on frameworks and application size. I reduce dependencies and startup work and consider provisioned concurrency when requirements justify it.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-aws-lambda-snapstart"></a>

## Lambda SnapStart

> SnapStart reuses an initialization snapshot with compatible runtimes and configurations to reduce part of cold-start cost. I verify connections, credentials, and data that must remain unique after restoration, along with service limitations for the selected runtime. [Reference](https://docs.aws.amazon.com/lambda/latest/dg/snapstart.html)

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-lambda-concurrency"></a>

## Lambda Concurrency

> Concurrency is the number of simultaneous function executions. It matters both for capacity and protecting downstream dependencies. Raising Lambda concurrency does not automatically increase the capacity of the databases or APIs it calls.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-lambda-idempotencia"></a>

## Lambda Idempotency

> Because events can be redelivered and retries may occur, Lambda functions creating external effects should be idempotent. I use idempotency keys, conditional writes, or processing records depending on the scenario.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-ecs"></a>

## ECS

> ECS is AWS's container orchestration service. It runs tasks and services and integrates with load balancers, autoscaling, and IAM. For many AWS-native workloads, it is operationally simpler than Kubernetes.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-ecs-service-vs-task"></a>

## ECS Service vs Task

> A task is a running instance of a task definition. A service maintains a desired number of tasks and integrates health checking, deployment, and autoscaling.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-fargate"></a>

## Fargate

> Fargate runs containers on ECS or EKS without directly managing EC2 instances. It simplifies operations and capacity isolation but has cost and lower-level infrastructure-control trade-offs.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-eks"></a>

## EKS

> EKS provides Kubernetes with a managed AWS control plane. Responsibility for nodes depends on the operating mode; I remain responsible for workloads, permissions, configuration, and data even when more infrastructure is automated.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-ecs-vs-eks"></a>

## ECS vs EKS

> ECS is simpler and tightly integrated with AWS. EKS offers the Kubernetes ecosystem and portability but increases operational complexity. I would choose EKS for a genuine Kubernetes requirement; otherwise, ECS may suffice.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-ecs-vs-lambda"></a>

## ECS vs Lambda

> ECS better suits long-running services and containerized workloads requiring greater runtime control. Lambda suits event-driven, short-lived execution. The choice depends on execution model, duration, dependencies, and operations.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-api-gateway"></a>

## API Gateway

> API Gateway provides API management, including routing, authentication, throttling, and integration with Lambda or HTTP backends. It is common in serverless architectures. An ALB may be more direct for simple containerized services.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-api-gateway-vs-alb"></a>

## API Gateway vs ALB

> API Gateway adds API management, throttling, authorization, and serverless integration. An ALB is a layer-7 HTTP load balancer and often provides simpler routing to ECS or EC2. I choose based on required features.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-sqs"></a>

## SQS

> SQS is a managed queue for asynchronous decoupling. Producers send messages and consumers process them independently. It helps absorb spikes, support retries, and reduce temporal coupling among services.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-sqs-standard-vs-fifo"></a>

## SQS Standard vs FIFO

> SQS Standard provides at-least-once delivery with best-effort ordering. FIFO orders messages within message groups and supports deduplication under its documented conditions, but consumers must still tolerate reprocessing, such as after failing before deleting a message.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-sqs-visibility-timeout"></a>

## SQS Visibility Timeout

> Visibility Timeout temporarily hides a message after it is received. If a consumer does not delete it before the timeout, it may become available again. The value should reflect expected processing time.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-sqs-dlq"></a>

## SQS DLQ

> A dead-letter queue receives messages that fail repeatedly beyond a configured limit. It prevents endless retries and supports inspection but requires an analysis and redrive process.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-sqs-long-polling"></a>

## SQS Long Polling

> Long polling waits for messages instead of immediately returning an empty result. It reduces unnecessary requests and costs when the queue has low traffic.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-sns"></a>

## SNS

> SNS is a pub/sub service. A publisher sends a message to a topic and multiple subscribers can receive it. It is suitable for fan-out.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-sns-sqs"></a>

## SNS + SQS

> Combining SNS with SQS provides fan-out with consumer isolation. Each service gets its own queue, allowing independent processing rates, retries, and DLQs.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-eventbridge"></a>

## EventBridge

> EventBridge is a managed event bus with content-based routing rules and broad integration with AWS and SaaS services. It is useful when routing needs go beyond simple fan-out.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-sns-vs-eventbridge"></a>

## SNS vs EventBridge

> SNS excels at straightforward pub/sub and fan-out. EventBridge offers routing rules, event buses, and broad integrations. I choose based on filtering, routing, and governance needs.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-sqs-vs-kafka"></a>

## SQS vs Kafka

> SQS is a simple managed queue, useful for decoupling with low operational overhead. Kafka offers partitioned logs, replay, consumer groups, and high-throughput streaming. I choose Kafka for history, replay, and multiple independent consumers, and SQS when a work queue suffices.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-aws-amazon-msk"></a>

## Amazon MSK

> Amazon MSK provides managed Apache Kafka, reducing some cluster operations. Applications still need to define partitions, retention, security, keys, commits, and idempotency; managed service does not remove event-flow design decisions.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-cloudwatch"></a>

## CloudWatch

> CloudWatch centralizes metrics, logs, alarms, and dashboards for AWS services. In production, I monitor latency, error rates, saturation, throttling, queues, CPU, memory when available, and business metrics.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-cloudtrail"></a>

## CloudTrail

> CloudTrail records API events and activity in an AWS account. It is fundamental for audits, change investigations, and security, but does not replace application logs.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-kms"></a>

## KMS

> KMS manages cryptographic keys and integrates with many AWS services. I use it to encrypt data and secrets while controlling key usage through IAM and key policies.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-secrets-manager-vs-parameter-store"></a>

## Secrets Manager vs Parameter Store

> Secrets Manager focuses on secrets and provides features such as integrated rotation in many scenarios. Parameter Store handles configuration and can also store secure values. I choose based on rotation, integration, and cost.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-waf"></a>

## WAF

> AWS WAF applies layer-7 rules to protect web applications from malicious patterns, bots, and common attacks. It can be associated with CloudFront and ALB. It complements but does not replace application security.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-cloudformation"></a>

## CloudFormation

> CloudFormation defines infrastructure as code through templates, improving repeatability, versioning, and automation. The trade-off is managing stack lifecycles and dependencies.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-aws-cdk"></a>

## AWS CDK

> AWS CDK defines infrastructure using programming languages and synthesizes CloudFormation templates. It improves abstraction and reuse but still follows CloudFormation's resource and deployment model.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-infrastructure-as-code"></a>

## Infrastructure as Code

> IaC makes infrastructure versionable, reproducible, and auditable. I avoid manual production changes because they introduce drift. With Terraform, CloudFormation, or CDK, the goal is keeping infrastructure within the delivery process.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-rto-e-rpo"></a>

## RTO and RPO

> RTO is the maximum acceptable recovery time after an outage. RPO is the maximum acceptable data loss measured in time. They determine backup, replication, failover, and cost strategies.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-disaster-recovery"></a>

## Disaster Recovery

> Disaster recovery strategies range from backup and restore to warm standby and active-active. Lower RTOs and RPOs generally increase cost and complexity. I start with business requirements before choosing an approach.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-well-architected-framework"></a>

## Well-Architected Framework

> The AWS Well-Architected Framework organizes best practices into pillars including operational excellence, security, reliability, performance efficiency, cost optimization, and sustainability. I use it to structure decision rationale, not as a memorized checklist.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-cost-optimization"></a>

## Cost Optimization

> Cost optimization involves right-sizing, autoscaling, stopping idle resources, appropriate storage, usage commitments for predictable workloads, and attention to data transfer. The cheapest suitable architecture is one that meets requirements without excess.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-nat-gateway-cost"></a>

## NAT Gateway Cost

> NAT Gateway has hourly and processed-data costs. In architectures with frequent AWS-service access, VPC Endpoints may reduce NAT traffic and improve security depending on usage patterns.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="aws-resposta-completa-de-entrevista-aws"></a>

## Interview Summary

> I choose AWS services based on compute, networking, data, and integration requirements. I apply IAM and temporary credentials, plan for redundancy and recovery, and automate infrastructure. Managed services reduce operational work but still require attention to security, capacity, consistency, and cost.

[↑ Topic Contents](#aws-aws-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-cicd-sumario"></a>

# 25. CI/CD

## Topic Contents

[↑ Master Contents](#sumario-geral)

- [CI/CD Overview](#cicd-visao-geral-ci-cd)
- [Continuous Integration](#cicd-continuous-integration)
- [Continuous Delivery vs Continuous Deployment](#cicd-continuous-delivery-vs-continuous-deployment)
- [CI/CD Pipeline](#cicd-pipeline-ci-cd)
- [Fail Fast](#cicd-fail-fast)
- [Maven in CI/CD](#cicd-maven-em-ci-cd)
- [Gradle in CI/CD](#cicd-gradle-em-ci-cd)
- [Maven Wrapper and Gradle Wrapper](#cicd-maven-wrapper-e-gradle-wrapper)
- [Reproducible Builds](#cicd-build-reprodutivel)
- [Immutable Artifacts](#cicd-artefato-imutavel)
- [Nexus and Artifactory](#cicd-nexus-e-artifactory)
- [Artifact Versioning](#cicd-versionamento-de-artefato)
- [Snapshot vs Release](#cicd-snapshot-vs-release)
- [Git Strategy](#cicd-git-strategy)
- [Trunk-Based Development](#cicd-trunk-based-development)
- [Pull Requests and Code Review](#cicd-pull-request-e-code-review)
- [Branch Protection](#cicd-branch-protection)
- [Unit Tests in the Pipeline](#cicd-testes-unitarios-na-pipeline)
- [Integration Tests](#cicd-testes-de-integracao)
- [Testcontainers](#cicd-testcontainers)
- [Contract Testing](#cicd-contract-testing)
- [End-to-End Tests](#cicd-end-to-end-tests)
- [Test Pyramid](#cicd-test-pyramid)
- [Flaky Tests](#cicd-flaky-tests)
- [Quality Gate](#cicd-quality-gate)
- [SonarQube](#cicd-sonarqube)
- [Test Coverage](#cicd-cobertura-de-testes)
- [SAST](#cicd-sast)
- [SCA](#cicd-sca)
- [DAST](#cicd-dast)
- [SBOM](#cicd-sbom)
- [Supply Chain Security](#cicd-supply-chain-security)
- [Secrets in the Pipeline](#cicd-secrets-no-pipeline)
- [Docker Builds in CI](#cicd-docker-build-em-ci)
- [Multi-stage Docker Builds](#cicd-multi-stage-docker-build)
- [Immutable Docker Images](#cicd-imagem-docker-imutavel)
- [Image Scanning](#cicd-image-scanning)
- [Registry](#cicd-registry)
- [Environment Promotion](#cicd-environment-promotion)
- [Environment-specific Configuration](#cicd-configuracao-por-ambiente)
- [Feature Flag](#cicd-feature-flag)
- [Rolling Deployment](#cicd-rolling-deployment)
- [Blue-Green Deployment](#cicd-blue-green-deployment)
- [Canary Deployment](#cicd-canary-deployment)
- [Rolling vs Blue-Green vs Canary](#cicd-rolling-vs-blue-green-vs-canary)
- [Rollback](#cicd-rollback)
- [Roll Forward](#cicd-roll-forward)
- [Database Migrations](#cicd-database-migration)
- [Flyway and Liquibase](#cicd-flyway-e-liquibase)
- [Zero-Downtime Database Migration](#cicd-zero-downtime-database-migration)
- [Expand and Contract](#cicd-expand-and-contract)
- [Backward Compatibility During Deployment](#cicd-backward-compatibility-no-deploy)
- [Post-deployment Health Checks](#cicd-health-check-pos-deploy)
- [Smoke Tests](#cicd-smoke-test)
- [Post-deployment Observability](#cicd-observabilidade-pos-deploy)
- [Automated Rollback](#cicd-automated-rollback)
- [Approval Gate](#cicd-approval-gate)
- [Pipeline as Code](#cicd-pipeline-as-code)
- [Jenkins](#cicd-jenkins)
- [GitHub Actions](#cicd-github-actions)
- [GitLab CI](#cicd-gitlab-ci)
- [Self-hosted vs Hosted Runners](#cicd-runner-self-hosted-vs-hosted)
- [Pipeline Caching](#cicd-cache-na-pipeline)
- [Pipeline Parallelism](#cicd-paralelismo-na-pipeline)
- [Artifacts Between Jobs](#cicd-artifacts-entre-jobs)
- [Idempotent Deployment](#cicd-idempotencia-do-deploy)
- [Infrastructure as Code in CI/CD](#cicd-infraestrutura-como-codigo-no-ci-cd)
- [GitOps](#cicd-gitops)
- [CI/CD on Kubernetes](#cicd-ci-cd-em-kubernetes)
- [CI/CD on ECS](#cicd-ci-cd-em-ecs)
- [Java and Container Memory](#cicd-java-e-container-memory)
- [Graceful Shutdown](#cicd-graceful-shutdown)
- [Readiness and Liveness](#cicd-readiness-e-liveness)
- [Java Deployment with Database Migration](#cicd-deploy-de-java-com-migracao-de-banco)
- [Dependency Management](#cicd-dependency-management)
- [Microservice Pipelines](#cicd-pipeline-para-microservicos)
- [Monorepo Pipelines](#cicd-pipeline-monorepo)
- [Investigating Pipeline Failures](#cicd-pipeline-failure-como-investigar)
- [Lead Time](#cicd-lead-time)
- [Deployment Frequency](#cicd-deployment-frequency)
- [Change Failure Rate](#cicd-change-failure-rate)
- [MTTR and Failed Deployment Recovery Time](#cicd-mttr)
- [Deployment Rework Rate](#cicd-cicd-deployment-rework-rate)
- [DORA Metrics](#cicd-dora-metrics)
- [CI/CD and Security](#cicd-ci-cd-e-seguranca)
- [CI/CD and Compliance](#cicd-ci-cd-e-compliance)
- [Interview Summary](#cicd-resposta-completa-de-entrevista-ci-cd)

---

<a id="cicd-visao-geral-ci-cd"></a>

## CI/CD Overview

> CI/CD automates software integration, validation, packaging, and delivery. For Java, I consider the full flow: checkout, Maven or Gradle build, tests, static analysis, artifact creation, image build, registry publication, and deployment. The goals are lower risk, faster feedback, less manual work, and reproducible, auditable releases.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-continuous-integration"></a>

## Continuous Integration

> Continuous Integration means frequently merging small changes into a shared branch while automatically running builds and checks. Its focus is early regression detection. In Java, I typically include compilation, unit tests, static analysis, and integration tests where needed.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-continuous-delivery-vs-continuous-deployment"></a>

## Continuous Delivery vs Continuous Deployment

> Continuous Delivery keeps software deployable at all times, but production deployment may require approval. Continuous Deployment goes further and automatically promotes to production when all gates pass. The main distinction is that final promotion step.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-pipeline-ci-cd"></a>

## CI/CD Pipeline

> A typical Java pipeline includes checkout, build, unit tests, static analysis, integration tests, packaging, artifact or image publication, deployment, and post-deployment validation. I separate stages to fail early and save time and resources.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-fail-fast"></a>

## Fail Fast

> Fail fast means running the quickest and least expensive checks first, such as compilation and unit tests before integration tests or deployment. This reduces feedback time and pipeline cost.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-maven-em-ci-cd"></a>

## Maven in CI/CD

> For Maven projects, I use reproducible commands such as `mvn clean verify` to compile, test, and run configured checks. I also use Maven Wrapper where available to ensure the expected Maven version in CI.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-gradle-em-ci-cd"></a>

## Gradle in CI/CD

> With Gradle, I prefer Gradle Wrapper, such as `./gradlew build`, for consistent versions across developers and CI. I also consider caching, parallelism, and separate test tasks as pipelines grow.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-maven-wrapper-e-gradle-wrapper"></a>

## Maven Wrapper and Gradle Wrapper

> Wrappers avoid depending on globally installed build-tool versions on runners. They improve reproducibility because the application defines the Maven or Gradle version to use.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-build-reprodutivel"></a>

## Reproducible Builds

> A reproducible build should produce the same result from the same revision and configuration. I pin versions, use wrappers, avoid dynamic dependencies, control plugins, and do not depend on local runner state.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-artefato-imutavel"></a>

## Immutable Artifacts

> I prefer building an artifact once and promoting the same binary or image across environments. Rebuilding per environment can change dependencies or settings and breaks the guarantee that the tested artifact is the one reaching production.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-nexus-e-artifactory"></a>

## Nexus and Artifactory

> Nexus and Artifactory are artifact repositories. In Java, they store JARs, BOMs, and other packages and may proxy external dependencies. They improve versioning, availability, auditing, and software supply-chain controls.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-versionamento-de-artefato"></a>

## Artifact Versioning

> I avoid overwriting published versions. I normally use a unique version per build associated with a commit or release. This supports rollback, traceability, and incident reproduction.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-snapshot-vs-release"></a>

## Snapshot vs Release

> In Maven, `SNAPSHOT` denotes a development version that may change over time, while a release should be immutable. For production, I use immutable, traceable versions.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-git-strategy"></a>

## Git Strategy

> A Git strategy should reduce long-lived branches and late integration. Trunk-based development favors short branches and frequent integration. Git Flow can suit some contexts but adds branches and coordination. I select based on release frequency and team maturity.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-trunk-based-development"></a>

## Trunk-Based Development

> Trunk-based development encourages small, short-lived branches that merge quickly into the trunk. It reduces divergence, conflicts, and time to integration. Feature flags help when functionality is not ready to be exposed.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-pull-request-e-code-review"></a>

## Pull Requests and Code Review

> Pull requests are technical and collaboration gates. I combine human review with automated validation rather than rely on review alone. Branch protection should prevent merges when builds, tests, or required checks fail.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-branch-protection"></a>

## Branch Protection

> Branch protection typically requires reviews and passing checks while blocking direct pushes to critical branches. It reduces unaudited changes and applies quality policies consistently.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-testes-unitarios-na-pipeline"></a>

## Unit Tests in the Pipeline

> Unit tests should run early because they are fast and provide immediate feedback. They verify isolated behavior and help prevent regressions before expensive stages.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-testes-de-integracao"></a>

## Integration Tests

> Integration tests validate interactions among real components such as databases, queues, or services. They cost more and run longer, so I usually put them after unit tests but before artifact promotion.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-testcontainers"></a>

## Testcontainers

> Testcontainers starts real dependencies such as PostgreSQL, Kafka, or Redis during integration tests. It reduces the gap between mocks and real environments but increases pipeline execution time and resource usage.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-contract-testing"></a>

## Contract Testing

> Contract tests verify producer-consumer compatibility without requiring a complete end-to-end environment. They help microservices detect breaking changes early and reduce reliance on fragile integrated tests.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-end-to-end-tests"></a>

## End-to-End Tests

> E2E tests validate complete flows but are slow and more prone to flakiness. I keep a small set focused on critical user journeys and cover most behavior with unit, integration, and contract tests.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-test-pyramid"></a>

## Test Pyramid

> The test pyramid emphasizes numerous unit tests, fewer integration tests, and a small number of E2E tests. The goal is balancing speed, confidence, and maintenance cost.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-flaky-tests"></a>

## Flaky Tests

> A flaky test fails nondeterministically and undermines pipeline confidence. I treat flakiness as a defect and investigate concurrency, timing, external dependencies, or shared state rather than adding unlimited retries.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-quality-gate"></a>

## Quality Gate

> A quality gate defines minimum promotion conditions such as passing tests, acceptable static analysis, and absence of critical vulnerabilities. Gates should prevent real problems instead of accumulating arbitrary metrics.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-sonarqube"></a>

## SonarQube

> SonarQube analyzes code quality and security, including potential bugs, code smells, and vulnerabilities. I use it as an automated pipeline signal, not a replacement for technical review or tests.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-cobertura-de-testes"></a>

## Test Coverage

> Coverage measures code executed by tests, not test quality. I use it to identify untested areas but avoid blind targets that reward low-value tests.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-sast"></a>

## SAST

> Static Application Security Testing analyzes source code or bytecode to find vulnerabilities without executing the application. It is useful early in pipelines for detecting unsafe patterns.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-sca"></a>

## SCA

> Software Composition Analysis identifies known vulnerabilities in dependencies and third-party components. It is especially important for Java given the extensive Maven and Gradle ecosystem.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-dast"></a>

## DAST

> Dynamic Application Security Testing tests a running application by simulating attacks or malicious behavior. It typically runs in a deployed environment and complements SAST and SCA.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-sbom"></a>

## SBOM

> An SBOM is a structured inventory of components and dependencies used in software. It helps quickly determine whether an application contains a vulnerable library and improves supply-chain traceability.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-supply-chain-security"></a>

## Supply Chain Security

> Software supply-chain security covers dependencies, plugins, images, runners, registries, and builds. I avoid unverified binaries, control dependencies, scan artifacts, and protect pipeline credentials.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-secrets-no-pipeline"></a>

## Secrets in the Pipeline

> Secrets must never be hardcoded in repositories or logged. I use CI/CD platform secret stores or services such as Secrets Manager and Vault and restrict access by environment and job.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-docker-build-em-ci"></a>

## Docker Builds in CI

> I build Docker images after code checks and tests pass. The image should have an immutable version identifier, such as a commit tag, and be published to a registry.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-multi-stage-docker-build"></a>

## Multi-stage Docker Builds

> Multi-stage builds separate compilation from the final runtime image. For Java, I can build with Maven or Gradle in one stage and copy only the JAR and required runtime into the final image, reducing size and attack surface.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-imagem-docker-imutavel"></a>

## Immutable Docker Images

> I avoid reusing a mutable tag for different production builds. I prefer immutable tags based on commits or versions and, where possible, digest references.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-image-scanning"></a>

## Image Scanning

> Image scanning identifies known vulnerabilities in operating systems and image dependencies. I include the check before promotion and reassess previously published images because new CVEs may appear after the build.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-registry"></a>

## Registry

> A registry stores versioned images, such as ECR or another container registry. Besides storage, I consider retention policies, scanning, permissions, and tag immutability.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-environment-promotion"></a>

## Environment Promotion

> I promote the same artifact across development, staging, and production, changing only external configuration. This preserves traceability and reduces environment differences.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-configuracao-por-ambiente"></a>

## Environment-specific Configuration

> Environment-specific configuration should remain external to the artifact through variables, ConfigMaps, Secrets, or a configuration service. Code and images should stay identical.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-feature-flag"></a>

## Feature Flag

> Feature flags decouple deployment from feature release. I can deploy disabled code and enable it gradually. I avoid permanent flags and excessive combinations that increase complexity.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-rolling-deployment"></a>

## Rolling Deployment

> A rolling deployment replaces instances gradually. It requires relatively little extra infrastructure and is simple, but old and new versions coexist during rollout, so compatibility matters.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-blue-green-deployment"></a>

## Blue-Green Deployment

> Blue-Green uses two environments: current and new. After validation, traffic switches to the new one. Rollback can be fast, but transition requires duplicated capacity.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-canary-deployment"></a>

## Canary Deployment

> A canary deployment routes a small share of traffic to a new version and gradually increases it while metrics remain healthy. It limits blast radius but requires reliable routing and observability.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-rolling-vs-blue-green-vs-canary"></a>

## Rolling vs Blue-Green vs Canary

> Rolling is simple and economical but mixes versions. Blue-Green enables quick rollback at the cost of duplicate capacity. Canary reduces risk through progressive exposure but demands mature automation and metrics. I choose based on criticality, cost, and operational capability.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-rollback"></a>

## Rollback

> Rollback quickly restores a known version. Previous artifacts must remain available and database changes must be compatible. Not every incident allows a simple rollback, especially after data transformations.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-roll-forward"></a>

## Roll Forward

> Roll forward fixes an issue by deploying a new version instead of reverting. It helps when schema or data rollback is risky. Many modern teams favor rolling forward for irreversible changes.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-database-migration"></a>

## Database Migrations

> Database migration should be part of delivery using tools such as Flyway or Liquibase. I version scripts, execute them in a controlled way, and avoid destructive changes incompatible with still-running versions.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-flyway-e-liquibase"></a>

## Flyway and Liquibase

> Flyway and Liquibase version schema changes and apply migrations automatically. The goal is treating the database as a versioned part of the application and avoiding manual production changes.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-zero-downtime-database-migration"></a>

## Zero-Downtime Database Migration

> For zero downtime, I make backward-compatible changes in stages. First I add new columns or structures without removing old ones, then update the application to use the new format, migrate data, and remove legacy structures only after no version depends on them.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-expand-and-contract"></a>

## Expand and Contract

> Expand and Contract is a two-phase schema-evolution strategy. Expand adds compatible structures; Contract removes old structures only after all consumers have migrated. This is essential for rolling deployments.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-backward-compatibility-no-deploy"></a>

## Backward Compatibility During Deployment

> During rollouts, old and new versions may run together. APIs, events, and schemas therefore must stay compatible throughout the transition. Breaking changes require a multi-step approach.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-health-check-pos-deploy"></a>

## Post-deployment Health Checks

> After deployment, I verify readiness, liveness, and relevant health checks. The goal is to confirm that the new version can receive traffic and that critical dependencies work.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-smoke-test"></a>

## Smoke Tests

> A smoke test is a small set of post-deployment checks for essential flows. It quickly detects routing, configuration, database, or dependency problems before full traffic exposure.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-observabilidade-pos-deploy"></a>

## Post-deployment Observability

> Deployment does not end when a container starts. I monitor errors, latency, p95/p99, CPU, memory, GC, connection pools, and business metrics to confirm the new version is healthy.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-automated-rollback"></a>

## Automated Rollback

> Automatic rollback can trigger when metrics or health checks exceed thresholds during a canary or rollout. I use carefully defined criteria to avoid reverting because of noise.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-approval-gate"></a>

## Approval Gate

> An approval gate adds manual authorization before promotion to a critical environment. It makes sense in regulated organizations or higher-risk changes. Mature, well-automated workflows may instead seek to reduce unnecessary manual gates.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-pipeline-as-code"></a>

## Pipeline as Code

> Pipeline as Code versions the pipeline definition alongside application code or in a controlled repository. This simplifies review, history, reproducibility, and delivery-process evolution.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-jenkins"></a>

## Jenkins

> Jenkins is a flexible, extensible automation server. Jenkinsfiles support complex pipelines, but the platform requires operations, plugin updates, security controls, and agent maintenance.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-github-actions"></a>

## GitHub Actions

> GitHub Actions integrates workflows into repositories. It is convenient for CI/CD and automation, but I consider third-party action security, token permissions, secrets, and runner costs.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-gitlab-ci"></a>

## GitLab CI

> GitLab CI defines pipelines in a versioned file and integrates build, testing, and deployment with the platform. Core concepts match other tools: stages, jobs, runners, artifacts, and environments.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-runner-self-hosted-vs-hosted"></a>

## Self-hosted vs Hosted Runners

> Hosted runners simplify operations. Self-hosted runners offer more network, performance, and tooling control but increase responsibility for patching, isolation, and security.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-cache-na-pipeline"></a>

## Pipeline Caching

> Caching speeds up pipelines by reusing dependencies or intermediate outputs. Maven and Gradle caches can significantly reduce Java build times. I guard against stale or improperly invalidated caches that conceal problems.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-paralelismo-na-pipeline"></a>

## Pipeline Parallelism

> Independent stages can run in parallel, such as unit tests, static analysis, and vulnerability scanning. This reduces lead time as long as resource contention and cost remain controlled.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-artifacts-entre-jobs"></a>

## Artifacts Between Jobs

> Artifacts carry outputs such as JARs, reports, or packages from one stage to another. I prefer passing a previously built artifact to rebuilding it in each job.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-idempotencia-do-deploy"></a>

## Idempotent Deployment

> Idempotent deployment means rerunning the same process reaches the same desired state without unintended side effects. This is important for automatic retries and failure recovery.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-infraestrutura-como-codigo-no-ci-cd"></a>

## Infrastructure as Code in CI/CD

> IaC brings infrastructure changes into the same review and automation process. I validate and plan changes before applying them, preserving auditability and reducing manual drift.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-gitops"></a>

## GitOps

> GitOps uses Git as the declarative source of desired state, which a controller applies to the environment. It is common on Kubernetes. It provides auditable, continuous reconciliation but adds another operational model.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-ci-cd-em-kubernetes"></a>

## CI/CD on Kubernetes

> On Kubernetes, a pipeline typically builds and publishes an image and then updates manifests, Helm charts, or GitOps configuration. Rollouts must consider readiness, strategy, resources, autoscaling, and rollback.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-ci-cd-em-ecs"></a>

## CI/CD on ECS

> On ECS, the pipeline publishes an image to a registry, creates a new task definition, and updates the service. I monitor health checks and deployment status and keep previous revisions available for rollback.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-java-e-container-memory"></a>

## Java and Container Memory

> When deploying Java in containers, I account for total memory limits, heap, Metaspace, stacks, and native memory. `-Xmx` should not consume the entire container limit because the JVM also requires off-heap memory.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-graceful-shutdown"></a>

## Graceful Shutdown

> Graceful shutdown lets an instance stop accepting new requests and complete active work before terminating. It is important during rolling deployments to prevent interrupted requests.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-readiness-e-liveness"></a>

## Readiness and Liveness

> Readiness indicates whether an instance is ready to receive traffic. Liveness indicates whether the process is healthy enough to keep running. Confusing them can cause unnecessary restarts or route traffic to unready instances.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-deploy-de-java-com-migracao-de-banco"></a>

## Java Deployment with Database Migration

> I separate destructive schema changes from application changes. I first apply a compatible schema, deploy the new code, validate execution, and remove old structures only in a later release.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-dependency-management"></a>

## Dependency Management

> In Java, I control dependency versions through BOMs or dependency management, avoid dynamic ranges, and monitor vulnerabilities. Reproducibility depends on knowing exactly which versions went into the build.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-pipeline-para-microservicos"></a>

## Microservice Pipelines

> Each microservice should have an independent pipeline where possible. Deploying one service should not require rebuilding all others. Contracts, tests, and versioning should preserve autonomy.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-pipeline-monorepo"></a>

## Monorepo Pipelines

> In a monorepo, I avoid rebuilding everything when only one module changes. I can detect affected paths and run selective pipelines while retaining global verification where necessary.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-pipeline-failure-como-investigar"></a>

## Investigating Pipeline Failures

> I first identify the failing stage: build, tests, analysis, publication, or deployment. Then I distinguish code failures from infrastructure problems and examine logs, artifacts, tool versions, credentials, and environment differences. I avoid repeatedly rerunning a job until it passes without understanding the cause.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-lead-time"></a>

## Lead Time

> Lead time measures how long a change takes from commit to production. Slow pipelines increase batch size and delay feedback. I reduce lead time through fail-fast checks, parallelism, caching, and well-distributed tests.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-deployment-frequency"></a>

## Deployment Frequency

> Deployment frequency measures how often a team can release changes to production. Higher frequency generally depends on automation, reliable tests, and small changes.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-change-failure-rate"></a>

## Change Failure Rate

> Change failure rate measures the proportion of deployments that cause incidents, rollbacks, or urgent fixes. It is an important indicator of delivery quality.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-mttr"></a>

## MTTR and Failed Deployment Recovery Time

> MTTR is a general average recovery-time measure whose scope must be defined. DORA uses Failed Deployment Recovery Time for recovering failed deployments requiring immediate intervention; I distinguish that from other operational incidents.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-cicd-deployment-rework-rate"></a>

## Deployment Rework Rate

> Measures the proportion of unplanned deployments resulting from production incidents. It complements the failure rate by showing how much delivery activity is consumed by recovery and urgent fixes.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-dora-metrics"></a>

## DORA Metrics

> DORA uses five metrics: change lead time, deployment frequency, failed deployment recovery time, change failure rate, and deployment rework rate. I assess delivery and instability within the context of an application or service rather than use them to rank individuals. [Reference](https://dora.dev/guides/dora-metrics/)

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-ci-cd-e-seguranca"></a>

## CI/CD and Security

> A pipeline accesses code, artifacts, environments, and credentials, making it part of the attack surface. I apply least privilege, isolated runners, trusted actions or plugins, protected secrets, branch protection, and audits.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-ci-cd-e-compliance"></a>

## CI/CD and Compliance

> In regulated environments, pipelines can record approvals, artifacts, test evidence, deployed versions, and change history. Automation improves auditability when each step produces traceable evidence.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---

<a id="cicd-resposta-completa-de-entrevista-ci-cd"></a>

## Interview Summary

> CI/CD automates builds, tests, security, packaging, and delivery. I promote the same artifact, evolve schemas compatibly, and choose deployment strategies based on risk and observability. After deployment, I verify behavior and maintain a practical recovery strategy.

[↑ Topic Contents](#cicd-cicd-sumario) · [↑ Master Contents](#sumario-geral)

---
