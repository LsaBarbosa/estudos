# 5.2 Arquiteturas de Software

Lucas, para nível Senior, o mais importante não é decorar nomes de arquiteturas. É conseguir explicar **qual problema cada estilo resolve, onde ele adiciona complexidade e quando não vale a pena usá-lo**.

## 1. Tabela — conceito, trade-off e caso de uso

| Arquitetura / Padrão | Conceito objetivo | Trade-off | Caso de uso |
|---|---|---|---|
| **Layered Architecture** | Organiza o sistema em camadas, normalmente apresentação, aplicação/serviço, domínio e persistência. | Simples e fácil de entender, mas pode gerar acoplamento entre camadas e domínio anêmico. | CRUDs, sistemas corporativos tradicionais, aplicações de baixa/média complexidade. |
| **Clean Architecture** | Mantém regras de negócio no centro e faz dependências apontarem para dentro. Infraestrutura depende do domínio, não o contrário. | Mais classes, interfaces e mapeamentos. Pode ser excesso para sistemas simples. | Sistemas de negócio complexos e de longa vida útil. |
| **Hexagonal Architecture** | Isola o domínio através de Ports and Adapters. O domínio define portas e infraestrutura implementa adapters. | Mais abstrações e adapters. Exige disciplina para não vazar detalhes externos para o domínio. | Sistemas com várias integrações, banco, mensageria e APIs externas. |
| **Onion Architecture** | Organiza o sistema em camadas concêntricas, mantendo domínio no centro e dependências apontando para dentro. | Muito parecida com Clean/Hexagonal; pode gerar complexidade estrutural desnecessária. | Sistemas domain-centric e com necessidade de independência tecnológica. |
| **Modular Monolith** | Uma única aplicação implantável, mas dividida internamente em módulos bem isolados. | Mantém simplicidade operacional, mas exige disciplina para impedir dependências indevidas entre módulos. | Sistemas médios/grandes antes de existir necessidade real de microsserviços. |
| **Microservices** | Divide o sistema em serviços independentes, geralmente alinhados a capacidades de negócio, com deploy e evolução independentes. | Aumenta drasticamente complexidade distribuída: rede, observabilidade, consistência, deploy e operação. | Organizações grandes com domínios independentes e necessidade real de escala/autonomia. |
| **Event-Driven Architecture** | Componentes se comunicam através da publicação e consumo de eventos. | Introduz consistência eventual, duplicidade, ordenação, tracing e dificuldade de debugging. | Integração assíncrona, processamento desacoplado, alta escala. |
| **CQRS** | Separa o modelo de escrita, Commands, do modelo de leitura, Queries. | Duplica modelos e aumenta sincronização/consistência entre lados de leitura e escrita. | Sistemas onde leitura e escrita possuem requisitos muito diferentes. |
| **Event Sourcing** | Estado atual é reconstruído a partir de uma sequência imutável de eventos de domínio. | Grande complexidade em versionamento de eventos, reconstrução, storage e evolução do modelo. | Auditoria completa, histórico temporal, domínios onde cada mudança precisa ser preservada. |

---

# 2. Layered Architecture

É provavelmente a arquitetura mais conhecida em aplicações Spring.

Um exemplo clássico:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

Cada camada possui uma responsabilidade.

### Controller

Cuida de:

```text
HTTP
request
response
status code
validação de entrada
```

### Service

Cuida de:

```text
regra de negócio
orquestração
transação
```

### Repository

Cuida de:

```text
persistência
queries
banco de dados
```

A vantagem é a simplicidade.

O problema aparece quando:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Entity JPA
```

vira apenas uma passagem de dados sem uma modelagem real de domínio.

Outro problema comum é a camada de negócio depender diretamente de detalhes de infraestrutura.

Por exemplo:

```java
@Service
public class PaymentService {

    private final StripeClient stripeClient;
}
```

Agora o negócio conhece diretamente Stripe.

Isso aumenta acoplamento.

---

# 3. Clean Architecture

Clean Architecture tenta proteger o núcleo do sistema.

A regra fundamental é:

> **As dependências devem apontar para o domínio.**

Mentalmente:

```text
Infrastructure
      ↓
Application
      ↓
Domain
```

E não:

```text
Domain
   ↓
Hibernate
Stripe
Kafka
AWS
```

Exemplo:

```java
public interface PaymentGateway {

    PaymentResult pay(Payment payment);
}
```

O domínio ou aplicação depende de:

```text
PaymentGateway
```

A infraestrutura implementa:

```java
class StripePaymentGateway
        implements PaymentGateway {
}
```

Assim:

```text
Application
     ↓
PaymentGateway
     ↑
StripeAdapter
```

Se amanhã Stripe virar outro provider, a regra de negócio sofre menos impacto.

---

# 4. Regra de dependência da Clean Architecture

Imagine estas camadas:

```text
Controllers
     ↓
Use Cases
     ↓
Domain
```

E externamente:

```text
Database
Kafka
HTTP Clients
Frameworks
```

O domínio não deveria saber:

```text
Spring
Hibernate
Kafka
PostgreSQL
AWS
```

Idealmente ele conhece:

```text
Entidades de domínio
Value Objects
Regras
Interfaces necessárias
```

Essa independência facilita:

- testes;
- evolução;
- troca de infraestrutura;
- entendimento das regras de negócio.

Mas existe um custo:

```text
interfaces
DTOs
mappers
adapters
use cases
```

Se o sistema é um CRUD muito simples, isso pode ser excesso.

---

# 5. Hexagonal Architecture

Hexagonal Architecture também é conhecida como:

**Ports and Adapters.**

O centro é a aplicação ou domínio.

Ao redor temos portas.

E nas extremidades temos adapters.

```text
               REST Controller
                     │
                     ↓
                Input Port
                     │
                     ↓
                  Domain
                     │
             Output Port
              /      |      \
             /       |       \
        Database    Kafka    External API
        Adapter    Adapter    Adapter
```

---

# 6. Ports e Adapters

### Input Port

Define o que a aplicação oferece.

Por exemplo:

```java
public interface CreateOrderUseCase {

    OrderResult execute(CreateOrderCommand command);
}
```

Um REST Controller é um adapter que chama essa porta.

```text
HTTP
 ↓
Controller
 ↓
CreateOrderUseCase
```

### Output Port

Define algo que a aplicação precisa.

```java
public interface OrderRepository {

    void save(Order order);
}
```

A infraestrutura implementa:

```java
class JpaOrderRepository
        implements OrderRepository {
}
```

Então:

```text
Domain/Application
      ↓
OrderRepository
      ↑
JpaOrderRepository
```

Isso é DIP aplicado arquiteturalmente.

---

# 7. Clean x Hexagonal x Onion

Essas arquiteturas são muito parecidas em objetivo.

Todas tentam:

```text
proteger domínio
      +
inverter dependências
      +
isolar infraestrutura
```

As diferenças estão principalmente na forma de organizar e explicar a arquitetura.

Uma forma simples de memorizar:

### Clean Architecture

Foco em:

```text
dependency rule
use cases
boundaries
```

### Hexagonal Architecture

Foco em:

```text
ports
adapters
entrada
saída
```

### Onion Architecture

Foco em:

```text
camadas concêntricas
domínio no centro
dependências apontando para dentro
```

Em entrevista, não vale a pena tratá-las como arquiteturas completamente opostas.

Elas compartilham praticamente a mesma filosofia:

> **o domínio não deveria depender da infraestrutura.**

---

# 8. Onion Architecture

Mentalmente:

```text
┌─────────────────────────────┐
│ Infrastructure              │
│  ┌───────────────────────┐  │
│  │ Application Services  │  │
│  │   ┌───────────────┐   │  │
│  │   │ Domain        │   │  │
│  │   └───────────────┘   │  │
│  └───────────────────────┘  │
└─────────────────────────────┘
```

O domínio fica no centro.

Infraestrutura fica nas camadas externas.

As dependências apontam:

```text
fora
 ↓
dentro
```

Nunca o contrário.

---

# 9. Modular Monolith

Modular Monolith é extremamente importante porque existe uma falsa dicotomia:

```text
Monolith
versus
Microservices
```

Um monólito não precisa ser:

```text
bagunçado
acoplado
sem modularidade
```

Podemos ter:

```text
Application
│
├── Orders
│
├── Payments
│
├── Customers
│
└── Shipping
```

Cada módulo possui:

```text
API interna
domínio
serviços
persistência
```

E módulos não deveriam acessar internamente uns aos outros de qualquer maneira.

---

# 10. Modular Monolith x Monolith tradicional

Um monólito mal modularizado pode ser:

```text
CustomerService
     ↓
OrderRepository
     ↓
PaymentEntity
     ↓
ShippingService
     ↓
qualquer coisa
```

Tudo conhece tudo.

No Modular Monolith:

```text
Orders Module
      ↓
Orders API


Payments Module
      ↓
Payments API
```

Comunicação entre módulos ocorre por contratos explícitos.

Assim você obtém:

- deploy simples;
- transações locais;
- menos infraestrutura distribuída;
- modularidade;
- possibilidade futura de extração de serviços.

Para muitos sistemas, é uma excelente arquitetura inicial.

---

# 11. Microservices

Microservices dividem o sistema em serviços independentes.

Exemplo:

```text
Customer Service

Order Service

Payment Service

Shipping Service
```

Cada serviço pode ter:

```text
código independente
deploy independente
banco próprio
escala independente
ownership independente
```

---

# 12. Principal vantagem dos Microservices

Não é simplesmente:

> "Microservices escalam melhor."

Monólitos também podem escalar horizontalmente.

Uma das principais vantagens é:

> **autonomia organizacional e independência de evolução.**

Por exemplo:

```text
Payments Team
      ↓
Payment Service
      ↓
deploy independente
```

Enquanto:

```text
Orders Team
      ↓
Order Service
      ↓
deploy independente
```

Isso faz diferença em organizações grandes.

---

# 13. O custo dos Microservices

Com microservices, uma chamada Java:

```java
paymentService.pay();
```

pode virar:

```text
Order Service
      ↓
network
      ↓
Payment Service
```

Agora precisamos pensar em:

```text
timeout
retry
circuit breaker
network failure
authentication
serialization
observability
distributed tracing
versionamento de contrato
```

Além disso:

```text
Transaction local
```

vira frequentemente:

```text
Distributed consistency
```

Então aparece:

```text
Saga
Outbox
Idempotency
Eventual Consistency
```

Microservices compram autonomia pagando com complexidade distribuída.

---

# 14. Quando usar Microservices

Faz mais sentido quando existem necessidades reais como:

```text
equipes independentes
deploy independente
domínios bem separados
escala diferente por componente
ciclos de release diferentes
isolamento operacional
```

Faz menos sentido quando:

```text
equipe pequena
produto pequeno
domínio ainda instável
baixa escala
```

Nesse caso um Modular Monolith frequentemente é mais simples.

---

# 15. Event-Driven Architecture

Na arquitetura orientada a eventos, componentes comunicam mudanças através de eventos.

Exemplo:

```text
Order Service
     ↓
OrderCreated
     ↓
Kafka
     ↓
 ┌───────────┬────────────┐
 ↓           ↓            ↓
Payment   Shipping     Analytics
```

O produtor não precisa conhecer diretamente todos os consumidores.

Isso gera:

```text
low coupling
+
asynchronous processing
```

---

# 16. Commands x Events

Essa diferença é importante.

Command representa intenção:

```text
CreateOrder
ProcessPayment
CancelOrder
```

Normalmente:

> alguém deve fazer algo.

Evento representa algo que já aconteceu:

```text
OrderCreated
PaymentApproved
OrderCancelled
```

Normalmente:

> algo aconteceu.

Uma boa regra de nomenclatura:

```text
Command
→ verbo imperativo


Event
→ fato no passado
```

---

# 17. Trade-offs de Event-Driven Architecture

A grande vantagem é desacoplamento.

Mas surgem problemas como:

```text
mensagem duplicada
ordenação
consistência eventual
reprocessamento
schema evolution
observabilidade
dead-letter queues
```

Por isso consumidores geralmente precisam ser:

```text
idempotentes
```

Ou seja:

processar o mesmo evento duas vezes não deve gerar duas alterações indevidas.

---

# 18. CQRS

CQRS significa:

**Command Query Responsibility Segregation.**

A ideia é separar:

```text
Commands
     ↓
alteram estado
```

de:

```text
Queries
    ↓
consultam estado
```

Modelo tradicional:

```text
Order
 ↓
mesmo modelo
para leitura e escrita
```

Com CQRS:

```text
Write Model
    ↓
Commands


Read Model
    ↓
Queries
```

---

# 19. Quando CQRS ajuda

Imagine uma plataforma em que escrever pedido exige:

```text
validação
regra de estoque
pagamento
consistência
```

Mas a consulta precisa:

```text
buscar milhões de pedidos
filtrar
agregar
gerar dashboard
```

Os requisitos são diferentes.

CQRS permite otimizar:

```text
Write Model
```

para consistência e domínio.

Enquanto:

```text
Read Model
```

pode ser otimizado para consulta.

---

# 20. CQRS não exige Microservices

Esse é um erro comum.

Podemos usar CQRS dentro de:

```text
Monolith
```

ou:

```text
Modular Monolith
```

Não é obrigatório possuir:

```text
Kafka
microservices
event sourcing
```

CQRS é principalmente:

> **separar responsabilidades de leitura e escrita.**

---

# 21. Event Sourcing

Event Sourcing muda a forma como armazenamos estado.

Modelo tradicional:

```text
Account

balance = 100
```

Só armazenamos o estado atual.

Com Event Sourcing:

```text
AccountCreated
      ↓
MoneyDeposited +100
      ↓
MoneyWithdrawn -30
      ↓
MoneyDeposited +30
```

Estado atual:

```text
100
```

é calculado aplicando os eventos.

---

# 22. Estado derivado de eventos

Mentalmente:

```text
Event 1
 +
Event 2
 +
Event 3
 +
Event 4
     ↓
Current State
```

Portanto os eventos são:

```text
source of truth
```

e não apenas notificações.

Essa diferença é essencial.

Event-Driven Architecture pode usar eventos sem Event Sourcing.

---

# 23. Event-Driven x Event Sourcing

Não são a mesma coisa.

### Event-Driven

Eventos são usados para:

```text
comunicação
```

Exemplo:

```text
OrderCreated
→ Kafka
→ Payment
```

O banco ainda pode armazenar:

```text
order.status = CREATED
```

### Event Sourcing

Eventos são usados para:

```text
persistir o próprio estado
```

O estado atual é reconstruído a partir deles.

---

# 24. Event Sourcing — vantagens

Você ganha histórico completo.

É possível saber:

```text
o que aconteceu
quando aconteceu
em qual ordem aconteceu
```

Também pode ser possível reconstruir o estado em determinado momento.

Exemplo:

```text
Saldo atual
```

ou:

```text
Saldo em 15 de março
```

aplicando somente eventos até aquele momento.

---

# 25. Event Sourcing — custo

O custo é alto.

Precisamos pensar em:

```text
versionamento de eventos
migração de schema
replay
snapshots
eventual consistency
projections
storage crescente
debugging
```

Eventos históricos não podem simplesmente ser alterados como registros comuns.

Por isso Event Sourcing deve ser utilizado quando o histórico possui valor real para o domínio.

Não apenas porque parece arquiteturalmente sofisticado.

---

# 26. CQRS + Event Sourcing

Eles aparecem juntos com frequência, mas são independentes.

Uma arquitetura pode ser:

```text
Command
   ↓
Write Model
   ↓
Event Store
   ↓
Events
   ↓
Projection
   ↓
Read Model
   ↓
Query
```

Aqui:

```text
CQRS
```

separa leitura e escrita.

Enquanto:

```text
Event Sourcing
```

persiste alterações como eventos.

Mas você pode usar:

```text
CQRS sem Event Sourcing
```

e:

```text
Event Sourcing sem CQRS completo
```

---

# 27. Comparação rápida

| Necessidade | Arquitetura mais provável |
|---|---|
| CRUD simples | Layered |
| Domínio complexo | Clean / Hexagonal / Onion |
| Boa modularidade sem distribuição | Modular Monolith |
| Autonomia de equipes e deploy | Microservices |
| Integração assíncrona e desacoplada | Event-Driven |
| Leitura e escrita muito diferentes | CQRS |
| Histórico completo como fonte da verdade | Event Sourcing |

---

# 28. Como eu escolheria na prática

Um caminho razoável seria:

```text
Sistema pequeno
     ↓
Layered
```

Se o domínio crescer:

```text
Layered
   ↓
Clean / Hexagonal
```

Se houver vários domínios dentro da aplicação:

```text
Modular Monolith
```

Se depois existir necessidade concreta de autonomia operacional:

```text
Modular Monolith
      ↓
Microservices
```

E, apenas se o domínio exigir:

```text
Event-Driven
CQRS
Event Sourcing
```

A ideia é:

> **a arquitetura deve evoluir junto com o problema.**

---

# Resposta objetiva para entrevista

> Eu escolho arquitetura considerando principalmente complexidade do domínio, necessidade de independência entre módulos, escala e autonomia das equipes.
>
> Para sistemas simples, Layered Architecture costuma ser suficiente. Quando o domínio possui maior complexidade, prefiro princípios de Clean ou Hexagonal Architecture para manter regras de negócio independentes de frameworks e infraestrutura, usando Ports and Adapters e Dependency Inversion.
>
> Para sistemas maiores, considero Modular Monolith uma opção importante porque oferece modularidade mantendo simplicidade operacional e transações locais. Só partiria para Microservices quando existisse uma necessidade real de deploy independente, autonomia de equipes ou escalabilidade específica, porque microservices introduzem problemas distribuídos como timeout, observabilidade, consistência eventual e falhas de rede.
>
> Event-Driven Architecture ajuda a desacoplar componentes através de eventos, mas exige idempotência, tratamento de duplicidade e observabilidade.
>
> CQRS separa modelos de leitura e escrita quando eles possuem necessidades muito diferentes. Event Sourcing, por outro lado, utiliza eventos como fonte da verdade para reconstruir o estado e só considero quando o histórico completo das mudanças possui valor real para o negócio.
>
> O principal para mim é **não escolher arquitetura pela tecnologia ou tendência, mas pelo problema e pelos trade-offs que o sistema realmente possui**.
