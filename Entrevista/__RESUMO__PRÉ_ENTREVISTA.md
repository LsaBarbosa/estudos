# Exceptions

- Exceptions como parte do design da aplicação.
  - Primeiro separo exceptions de domínio de exceptions técnicas.  
    - `Exceptions de domínio`: representam situações semanticamente relevantes
    - `Exceptions técnicas` : representam falhas de infraestrutura.
      
  - Evito capturar exceptions indiscriminadamente em todas as camadas e deixo a propagação acontecer até um ponto que realmente tenha contexto para recuperar, traduzir ou responder à falha.

- Em aplicações Spring, centralizo a tradução para HTTP utilizando RestControllerAdvice.
  - O domínio não conhece status HTTP. A camada HTTP transforma exceptions em respostas coerentes, usando ProblemDetail e códigos de erro estáveis.

- Também diferencio validação, conflito, recurso inexistente e falha inesperada.
  - Para erros inesperados, considero logging estruturado, trace ID, métricas e observabilidade.
  - E evito expor stack traces ou detalhes internos ao cliente.
---
# Spring Core e JPA

- Spring core é baseado em `Inversion of Control IoC e Dependency Injection`, a aplicação declara o que precisa por meio de construtores.
  - `ApplicationContext` funciona como IoC Container, criando e gerenciando os Beans, resolvendo dependências, controlando scopes e lifecycle e permitindo que infraestrutura como AOP e proxies seja aplicada.
  - `Component scanning` encontra classes anotadas com estereótipos como Component, Service e Repository.
    - Podemos registrar Beans explicitamente através de métodos Bean, principalmente quando queremos controlar a construção ou integrar classes externas.
        
  - `Exceptions técnicas` : representam falhas de infraestrutura.
      
- JPA é uma especificação de ORM, enquanto Hibernate é uma de suas principais implementações.
  - O `EntityManager`: gerencia um Persistence Context. Dentro desse contexto, as entidades podem estar em estados como transient, managed, detached e removed.

- Do ponto de vista de performance, eu tomo cuidado principalmente com N mais um, carregamento Eager excessivo, associações Lazy utilizadas fora do fetch plan esperado, paginação combinada com fetch de coleções, crescimento do Persistence Context e operações em massa.

  - Não utilizaria Eager como solução automática para N mais um.

  - Escolheria a estratégia de consulta de acordo com o caso de uso, utilizando Join Fetch, Entity Graph, batch fetching.
 ---
# Resiliência

  - **Falha ou Lentidão** em chaamdas: defino timeouts explícitos e evito chamadas bloqueadas indefinidamente.
  - **Falhas transitórias**: posso utilizar `retry` com limite, quando a operação for segura para repetição ou possuir idempotência.
  - **Falhas Contínuas**: utilizo `circuit breaker` para evitar continuar 
pressionando um serviço degradado.
  - **Garantia de consistência** entre uma transação local e publicação de eventos, posso utilizar `Transactional Outbox`. 
  - **Impedir Consumo** de todos os recusos uso `bulkhead`
  - **Excesso de tráfego**: `Rate limiting` ajuda a proteger serviços .
  - **Reduzir acoplamento temporal**, considero `comunicação assíncrona` através de `filas ou eventos`.
    - Devo tratar duplicidade, idempotência, retry, Dead Letter Queue e eventual consistency.
#### Observabilidade
Eu monitoraria latência, taxa de erro, retries, circuit breakers, filas, pools de conexão e recursos da aplicação, utilizando logs estruturados, métricas e distributed tracing.
--- 

# Microsserviços 
- Cada serviço é **dono dos próprios dados**, evitando banco compartilhado.
  - Quando um serviço precisa de informações de outro, uso contratos bem definidos, normalmente APIs ou eventos.

- Na comunicação entre serviços:
  - **HTTP ou gRPC quando a resposta é necessária imediatamente**
  - **Comunicação assíncrona com Kafka ou filas**, quando quero desacoplamento temporal, absorção de picos ou processamento eventual.

- Evito cadeias síncronas longas, pois aumentam latência e reduzem a disponibilidade.
- Para resiliência, aplico mecanismos como **timeout, circuit breaker, bulkhead, rate limiting e retry com critério**.

- `@Transactional` local não resolve o problema distribuído. Nesse cenário, trabalho com **Saga, compensações, Transactional Outbox, idempotência e eventual consistency**.

- Em microsserviços uso **logs estruturados, métricas e distributed tracing**, com `traceId`, para conseguir acompanhar uma requisição atravessando vários serviços.
--- 
