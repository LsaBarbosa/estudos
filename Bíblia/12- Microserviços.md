Lucas, em **Microsserviços**, o ponto principal é entender que o desafio não é criar APIs REST entre aplicações. O desafio é definir **fronteiras corretas, ownership de dados e regras, autonomia de deploy e consistência entre serviços distribuídos**.

## 1. Microsserviços — conceitos, trade-offs e casos de uso

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **Microservice** | Serviço autônomo responsável por uma capacidade de negócio, com regras e dados sob seu controle. | Ganha autonomia, mas introduz rede, falhas distribuídas, observabilidade e consistência entre serviços. | Orders, Payments, Customers, Shipping. |
| **Service Boundary** | Define onde termina a responsabilidade de um serviço e começa a de outro. | Boundary pequeno demais gera comunicação excessiva; grande demais reduz autonomia. | Separar `Order` de `Payment` quando possuem ciclos e regras independentes. |
| **Bounded Context** | Limite dentro do qual determinado modelo e linguagem de domínio possuem significado consistente. | Exige bom conhecimento do domínio; boundaries errados geram acoplamento distribuído. | `Customer` pode ter modelos diferentes em vendas, suporte e cobrança. |
| **Business Capability** | Capacidade de negócio usada como referência para decompor serviços. | Nem toda funcionalidade merece um serviço separado. | Payment, Fraud, Shipping, Customer Management. |
| **Data Ownership** | Cada dado possui um serviço responsável por controlá-lo. Outros serviços não devem alterá-lo diretamente. | Consultas entre domínios ficam mais complexas. | Customer Service é proprietário dos dados de Customer. |
| **Rule Ownership** | A regra de negócio deve pertencer ao serviço responsável pelo conceito que ela protege. | Algumas regras atravessam domínios e exigem coordenação distribuída. | Payment Service decide regras de pagamento; Order Service decide estado do pedido. |
| **Database per Service** | Os dados persistentes de um serviço são privados e acessados através do próprio serviço. | Evita acoplamento, mas joins e transações entre serviços ficam mais difíceis. | Order DB exclusiva do Order Service. |
| **Shared Database** | Vários serviços acessam diretamente as mesmas tabelas/schema. | Simplifica joins e ACID inicialmente, mas cria forte acoplamento de runtime e desenvolvimento. | Útil principalmente como etapa transitória de modernização; deve ser avaliado com cuidado. |
| **Independent Deployment** | Um serviço deve poder ser alterado e implantado sem exigir deploy coordenado dos demais. | Requer contratos compatíveis e versionamento cuidadoso. | Atualizar Payment Service sem redeploy do Order Service. |
| **Loose Coupling** | Serviços conhecem contratos, não detalhes internos ou banco uns dos outros. | Pode exigir duplicação controlada de dados e comunicação assíncrona. | Order conhece API/eventos de Customer, não suas tabelas. |
| **High Cohesion** | Regras e dados que mudam juntos devem ficar próximos, idealmente dentro da mesma boundary. | Boundary grande demais pode virar um monólito distribuído internamente. | Pedido, itens e regras de alteração de pedido no mesmo domínio. |
| **Chatty Services** | Serviços precisam conversar excessivamente para realizar uma única operação. | Aumenta latência, fragilidade e dependência de disponibilidade. Pode indicar boundary incorreta. | Order fazendo dezenas de chamadas ao Customer durante uma operação. |
| **Cross-service Query** | Consulta precisa combinar dados pertencentes a vários serviços. | Não existe mais um `JOIN` simples entre bancos privados. | Tela contendo Customer + Orders + Payments. |
| **Cross-service Transaction** | Uma operação de negócio modifica dados de vários serviços. | Transação ACID local deixa de resolver tudo; normalmente exige consistência eventual/Saga. | Criar pedido, reservar estoque e processar pagamento. |
| **Eventual Consistency** | Serviços podem ficar temporariamente inconsistentes enquanto eventos/processamentos convergem o estado. | Aplicação precisa aceitar estados intermediários e lidar com falhas/retries. | Pedido criado enquanto pagamento ainda está sendo processado. |
| **Polyglot Persistence** | Cada serviço pode escolher o armazenamento adequado ao próprio problema. | Aumenta custo operacional e variedade tecnológica. | PostgreSQL para Order e outra tecnologia para busca ou dados específicos. |

Uma boa boundary tende a produzir serviços **independentemente implantáveis, pouco acoplados e sem comunicação excessivamente “chatty”**. Se dividir duas funcionalidades cria chamadas constantes e dependência forte, isso pode indicar que elas deveriam permanecer na mesma boundary. 

---

# 2. Como definir o MS

```text
Quem é responsável por essa regra?

Quem é proprietário desses dados?

Esses dados precisam mudar atomicamente?

Essas funcionalidades mudam juntas?

Precisam escalar separadamente?

Precisam ser implantadas separadamente?
```

Se duas funcionalidades:

- mudam sempre juntas;
- compartilham invariantes fortes;
- fazem inúmeras chamadas entre si;
- precisam ser implantadas juntas;

talvez elas **não sejam dois serviços**.

---

# 3. Service Boundary

Imagine um domínio de e-commerce:

```text
Customer | Order  
```

Uma decomposição possível:

```text
Customer Service      Order Service
│                       │ 
├── Customer            ├── Order
├── Address             ├── OrderItem
└── Customer rules      ├── Order rules
```

O importante é que cada boundary represente uma **capacidade coesa de negócio**.

---

# 4. Bounded Context

- DDD ajuda bastante na definição dessas fronteiras.

- Cada bounded context pode ter seu próprio modelo. 
- O princípio de soberania de dados em microsserviços está diretamente relacionado a essa ideia: o serviço deve possuir seu modelo, seus dados e seu comportamento. 

---

# 5. Data Ownership

- Cada MS é responsável por seus dados
- A comunucação deve ser por APi externa ou eventos (mensageria)
 
> Somente o serviço proprietário deve alterar diretamente seu estado persistente.w

> A Microsoft recomenda explicitamente que serviços não compartilhem diretamente seus armazenamentos e que cada serviço gerencie seus dados privados. 

> A ideia de Database per Service é justamente manter os dados persistentes privados ao serviço. Isso reduz acoplamento e permite que alterações internas do schema não obriguem outros serviços a mudar. 

---

# 6. Por que Shared Database é perigoso?

Arquitetura:

```text
Service A ─┐
Service B ─┼── Shared Database
Service C ─┘
```
- parece inicialmente simples é possivel:
  - JOIN | foreign key | transaction ACID

- Mas se um serviço altera uma coluna, e outro serviço dependa temos um problema
  - Mudança que deveria ser local exige coordenação entre equipes.
---

# 7 Database per Service cria novos problemas

Separar bancos resolve acoplamento.

Mas como trade transação distribuida.
- Não há mais @Transaction
- Agora há necessidade de implementar padrões como saga, CQRS
- Consistencia eventual

Database per Service melhora autonomia, mas transações e consultas que atravessam serviços ficam mais complexas. 

---

# 13. Cross-service Query

Imagine uma tela:

```text
Customer Dashboard

Name
Orders
Payments
```

Os dados pertencem a três serviços.

Uma opção é:

```text
API / BFF
│
├── Customer Service
├── Order Service
└── Payment Service
```

e depois compor a resposta.

Isso é **API Composition**.

Em cenários de leitura pesada, também podemos criar uma projeção:

```text
CustomerUpdated ─┐
OrderCreated ─────┼──► Read Model
PaymentApproved ──┘
```

Essa é uma aplicação possível de CQRS/materialized views. 

---

# 14. Cross-service Transaction

Imagine:

```text
Create Order
    ↓
reserve stock
    ↓
process payment
    ↓
create shipment
```

No monólito com um banco:

```text
BEGIN
...
COMMIT
```

poderia resolver.

Em microsserviços:

```text
Order DB
Inventory DB
Payment DB
Shipping DB
```

não existe uma única transação local envolvendo tudo.

Então frequentemente precisamos trabalhar com:

```text
local transactions
+
events/messages
+
compensation
+
eventual consistency
```

Um padrão importante para isso é:

```text
Saga
```

Por isso microsserviços alteram profundamente o modelo de consistência da aplicação. 

---

# 15. Consistência eventual

Imagine:

```text
Order
status = CREATED
```

e alguns milissegundos depois:

```text
Payment
status = APPROVED
```

e depois:

```text
Order
status = CONFIRMED
```

Existe um período em que:

```text
Order = CREATED
Payment = APPROVED
```

Esse estado intermediário pode ser perfeitamente normal.

Isso é parte da realidade de:

**eventual consistency.**

O sistema precisa modelar os estados intermediários conscientemente.

Não adianta fingir que toda operação distribuída continua tendo a mesma semântica de uma transação ACID local.

---

# 16. Autonomia de dados

Um princípio importante é:

```text
Customer Service
     ↓
Customer Model
Customer Rules
Customer DB
```

```text
Order Service
     ↓
Order Model
Order Rules
Order DB
```

```text
Payment Service
     ↓
Payment Model
Payment Rules
Payment DB
```

Essa combinação produz:

> **soberania do serviço.**

O serviço possui:

```text
comportamento
+
modelo
+
dados
```

do domínio pelo qual é responsável. 

---

# 17. O erro do "monólito distribuído"

Imagine:

```text
Order Service
    ↓
Customer Service
    ↓
Payment Service
    ↓
Inventory Service
    ↓
Shipping Service
```

e todos precisam estar disponíveis para concluir qualquer requisição.

Além disso:

```text
deploy A exige deploy B

B conhece tabelas de C

C conhece implementação de D
```

Você obteve:

```text
complexidade de microservices
```

sem obter:

```text
autonomia de microservices
```

Isso é frequentemente chamado de:

**distributed monolith.**

Um bom teste é:

```text
Os serviços conseguem evoluir
e ser implantados independentemente?
```

Se a resposta for não, as boundaries precisam ser reavaliadas.

---

# 18. Mapa mental para definir uma boundary

Ao avaliar um novo serviço, faça estas perguntas:

```text
Qual capacidade de negócio ele representa?

Qual regra ele possui?

Quais dados ele possui?

Quem pode alterar esses dados?

Esses dados precisam mudar juntos?

Quem precisa conversar com ele?

A comunicação ficará chatty?

Ele pode ser implantado independentemente?

Ele precisa escalar independentemente?

Qual é o impacto se ele ficar indisponível?
```

Se você consegue responder essas perguntas, está realmente discutindo arquitetura de microsserviços.

Se a discussão está apenas em:

```text
Feign
REST
Docker
Spring Cloud
```

você ainda está discutindo implementação.

---

# 19. Decisão importante: talvez não seja um microserviço

Não existe obrigação de transformar cada conceito em serviço.

Às vezes:

```text
Order
OrderItem
OrderStatus
OrderValidation
```

devem permanecer juntos.

Dividir demais gera:

**nan services** ou serviços excessivamente pequenos.

O objetivo não é:

> criar o máximo de serviços possível.

É:

> encontrar boundaries que maximizem coesão e autonomia e minimizem acoplamento.

---

# 20. Resposta objetiva para entrevista

> Para mim, o ponto principal de microsserviços não é REST ou Spring Cloud, mas a definição correta das boundaries.
>
> Eu procuro decompor serviços por capacidades de negócio ou bounded contexts, mantendo alta coesão dentro do serviço e baixo acoplamento entre serviços. Uma boa boundary precisa deixar claro quem possui determinada regra e quem é proprietário de determinado dado.
>
> Também aplico data ownership. Se o Customer Service é proprietário do Customer, outros serviços não devem acessar ou alterar diretamente suas tabelas. A comunicação precisa acontecer através do contrato do serviço ou através de eventos. Por isso normalmente utilizamos o princípio de Database per Service. 
>
> Isso não significa obrigatoriamente um servidor de banco físico por microsserviço; pode existir separação por database ou schema, desde que os dados permaneçam privados e o ownership seja respeitado. 
>
> Eu evito Shared Database porque ele cria acoplamento de schema, runtime e deploy. Uma mudança em uma tabela pode obrigar vários serviços a mudar e uma transação de um serviço pode interferir em outro. 
>
> Também observo se os serviços estão excessivamente chatty. Se dois serviços precisam conversar constantemente ou sempre são implantados juntos, isso pode indicar uma boundary incorreta. 
>
> O trade-off é que, ao separar dados, perdemos joins e transações ACID simples entre domínios. Então precisamos lidar com API Composition, eventos, Saga, CQRS e consistência eventual dependendo do problema.
>
> Portanto, eu considero um microsserviço bem definido quando ele possui **responsabilidade clara, regras e dados próprios, contrato explícito e capacidade de evoluir e ser implantado com o mínimo de coordenação com os demais serviços**.
