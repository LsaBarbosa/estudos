Lucas, em **Code Review** o ponto principal é revisar o impacto da mudança no sistema, e não apenas estilo de código. Formatação, imports e convenções deveriam ser resolvidos em grande parte por ferramentas automáticas; o review humano deve focar em **correção, arquitetura, risco e manutenção**.

# 1. Code Review — conceito, trade-off e caso de uso

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **Code Review** | Análise técnica de uma mudança antes de integrá-la, buscando correctness, segurança, manutenção e aderência à arquitetura. | Review superficial deixa defeitos passarem; review excessivamente burocrático reduz velocidade. | Pull Requests e mudanças críticas. |
| **Architecture** | Verificar se a solução respeita boundaries, dependências, responsabilidades e decisões arquiteturais existentes. | Exigir abstrações demais pode gerar overengineering. | Evitar Controller acessando banco diretamente ou domínio dependendo de infraestrutura. |
| **Domain** | Validar se regras e invariantes de negócio estão corretas e no componente apropriado. | Reviewer precisa compreender o domínio, não apenas Java. | Regra de cancelamento, limite, pagamento ou estoque. |
| **Concurrency** | Analisar acesso simultâneo a estado, atomicidade, thread safety, race conditions, locks e idempotência. | Sincronização excessiva reduz throughput; pouca sincronização gera inconsistência. | Contadores, estoque, processamento paralelo, consumers. |
| **Transactions** | Verificar fronteira transacional, rollback, propagation, locks e consistência do estado. | Transações grandes aumentam lock contention; pequenas demais podem quebrar atomicidade. | Checkout envolvendo várias alterações locais. |
| **Security** | Procurar falhas de autenticação, autorização, validação, exposição de dados, injection e secrets. | Controles excessivos podem aumentar complexidade, mas falhas podem ser críticas. | Endpoint novo, upload, SQL, integração externa. |
| **Performance** | Avaliar complexidade, queries, N+1, uso de memória, chamadas remotas, loops e volume de dados. | Otimização prematura também aumenta complexidade. | Endpoint executando centenas de queries ou processando milhões de objetos. |
| **Observability** | Garantir que erros e comportamentos importantes possam ser diagnosticados através de logs, métricas e traces. | Telemetria excessiva aumenta custo e ruído. | Nova integração externa ou fluxo crítico. |
| **Testing** | Verificar se testes cobrem comportamento relevante, edge cases, falhas e regressões. | Testes excessivamente acoplados à implementação dificultam refactoring. | Alteração em regra financeira ou integração. |
| **Maintainability** | Avaliar clareza, coesão, acoplamento, complexidade e facilidade de evolução. | Buscar perfeição pode atrasar entregas sem benefício proporcional. | Classes muito grandes, duplicação de regra, dependências desnecessárias. |
| **Correctness** | Confirmar que o código realmente atende ao requisito e funciona nos cenários esperados. | Focar apenas em happy path deixa bugs de produção passarem. | Validar comportamento em erro, null, concorrência e estados inválidos. |
| **Backward Compatibility** | Verificar se APIs, eventos e schemas continuam compatíveis com consumidores existentes. | Compatibilidade pode exigir período de transição e código temporário. | Alterar contrato REST ou evento Kafka. |
| **Failure Handling** | Avaliar comportamento quando dependências falham, atrasam ou retornam resultados inesperados. | Adicionar retries/fallbacks indiscriminadamente pode piorar falhas. | API externa, Kafka, banco, cache. |
| **Data Integrity** | Garantir constraints, validações e invariantes necessárias no banco e aplicação. | Constraints mais rígidas exigem migrations cuidadosas. | Duplicidade de pagamento, email único, controle de saldo. |

---

# 2. Architecture

Um review arquitetural pergunta:

> **Essa mudança pertence realmente a esse componente?**

Exemplo problemático:

```java
@RestController
class OrderController {

    @Autowired
    JdbcTemplate jdbc;

    @PostMapping("/orders")
    void create() {
        jdbc.update(...);
        // regra de negócio
    }
}
```

O problema não é sintaxe.

É responsabilidade.

Mentalmente:

```text
Controller
   ↓
Application / Service
   ↓
Domain
   ↓
Repository / Adapter
```

No review, verificaria:

- dependência entre módulos;
- direção das dependências;
- boundaries;
- acoplamento;
- responsabilidade das classes;
- possível duplicação de domínio.

---

# 3. Domain

Uma implementação pode estar tecnicamente correta em Java e errada para o negócio.

Exemplo:

```java
if (order.getStatus() != CANCELLED) {
    order.cancel();
}
```

A pergunta não é apenas:

> Esse `if` funciona?

É:

> **Quais estados realmente permitem cancelamento?**

Talvez:

```text
CREATED
PAID
SHIPPED
DELIVERED
```

tenham regras completamente diferentes.

No review de domínio, procuro:

- invariantes;
- estados válidos;
- regras duplicadas;
- comportamento escondido no Controller;
- regras no componente errado.

---

# 4. Concurrency

Considere:

```java
if (product.getStock() > 0) {
    product.setStock(product.getStock() - 1);
}
```

Em uma única thread parece correto.

Com duas transações:

```text
Transaction A
reads stock = 1

Transaction B
reads stock = 1

A → stock = 0
B → stock = 0
```

Foram vendidas duas unidades.

No review, pergunto:

- existe estado compartilhado?
- operação precisa ser atômica?
- existe optimistic lock?
- pessimistic lock?
- constraint?
- consumer é idempotente?
- coleção é thread-safe?

Esse tipo de problema normalmente é mais importante do que nome de variável.

---

# 5. Transactions

Considere:

```java
@Transactional
public void checkout() {

    createOrder();

    updateInventory();

    paymentClient.pay();
}
```

Perguntas de review:

```text
Qual parte é realmente local?

O que acontece se Payment der timeout?

A transação fica aberta durante HTTP?

Existe compensação?

Qual rollback esperado?
```

Manter uma transação aberta enquanto chama rede pode ser perigoso:

```text
DB transaction
     ↓
locks
     ↓
HTTP remoto
     ↓
3 segundos
```

Durante esses três segundos, locks e conexão podem continuar ocupados.

O review deve analisar a **fronteira transacional**, não apenas verificar se existe `@Transactional`.

---

# 6. Security

Toda entrada externa deve ser considerada não confiável.

No review, verificaria:

```text
Authentication
Authorization
Input Validation
SQL Injection
XSS
CSRF
SSRF
Secrets
Sensitive Data
```

Exemplo:

```java
@GetMapping("/customers/{id}")
Customer find(@PathVariable Long id) {
    return repository.findById(id);
}
```

Pergunta:

> O usuário autenticado realmente pode acessar **qualquer** customer pelo ID?

Pode existir um problema de:

**Broken Access Control.**

Outro exemplo:

```java
String query =
    "SELECT * FROM users WHERE name = '" + name + "'";
```

Problema:

**SQL Injection.**

---

# 7. Performance

Um review de performance deve procurar riscos proporcionais ao cenário.

Exemplo:

```java
List<Order> orders = repository.findAll();

for (Order order : orders) {
    order.getCustomer().getName();
}
```

Possível:

```text
1 query de Orders
+
N queries de Customer
```

N+1.

Também analiso:

- complexidade `O(n²)`;
- consultas dentro de loops;
- paginação;
- índices;
- payloads muito grandes;
- processamento desnecessário;
- chamadas remotas sequenciais;
- criação excessiva de objetos.

Mas sem cair em otimização prematura.

---

# 8. Observability

Imagine:

```java
try {
    paymentClient.pay();
} catch (Exception e) {
    throw new PaymentException();
}
```

A pergunta é:

> Em produção, consigo descobrir o que aconteceu?

Talvez seja necessário preservar:

```text
orderId
paymentId
error type
dependency
traceId
duration
```

Também considero:

- métricas de sucesso e falha;
- latency;
- tracing;
- correlation ID;
- logs estruturados.

Mas nunca:

```java
log.info("token={}", token);
```

Observabilidade também precisa respeitar segurança.

---

# 9. Testing

No review, não verifico apenas:

> Existe teste?

Verifico:

> **O teste prova o comportamento importante?**

Exemplo de regra:

```text
Payment aprovado
→ Order vira PAID
```

Precisamos talvez testar:

```text
sucesso

payment recusado

timeout

duplicidade

estado inválido
```

Em alterações críticas, avalio:

```text
Unit Test
Integration Test
Contract Test
```

dependendo do risco.

---

# 10. Maintainability

Código funciona hoje, mas a pergunta é:

> **Quanto vai custar alterar isso daqui a seis meses?**

Sinais de alerta:

```text
classe com 1.500 linhas

método com 200 linhas

20 parâmetros

if/else crescente

regra duplicada

dependência circular

nomes genéricos

Utils com tudo
```

Procuro:

```text
High Cohesion
Low Coupling
responsabilidades claras
interfaces úteis
fluxo compreensível
```

Mas sem exigir abstrações sem necessidade.

---

# 11. Backward Compatibility

Esse ponto é fundamental em microsserviços.

Imagine mudar:

```json
{
  "customerId": 10
}
```

para:

```json
{
  "clientId": 10
}
```

Pode parecer apenas renomear uma propriedade.

Mas consumidores existentes podem quebrar.

O mesmo vale para:

```text
REST
Kafka events
database schemas
libraries
```

Em review, pergunto:

> Essa mudança é backward compatible?

Se não for:

> Qual é a estratégia de migração?

---

# 12. Failure Handling

Código distribuído precisa ser revisado considerando falhas.

Exemplo:

```java
paymentClient.pay();
```

Perguntas:

```text
Existe timeout?

O erro é retryable?

Retry é idempotente?

Tem Circuit Breaker?

Qual comportamento se o serviço estiver fora?

Existe fallback válido?
```

Um reviewer Senior não olha apenas o happy path.

Olha principalmente:

> **O que acontece quando isso dá errado?**

---

# 13. Ordem prática para revisar um Pull Request

Eu utilizaria mentalmente algo próximo de:

```text
1. Correctness
        ↓
2. Domain
        ↓
3. Architecture
        ↓
4. Data / Transactions
        ↓
5. Concurrency
        ↓
6. Security
        ↓
7. Failure Handling
        ↓
8. Performance
        ↓
9. Observability
        ↓
10. Tests
        ↓
11. Maintainability
```

Formatação deveria estar muito abaixo disso.

---

# 14. O que automatizar

Ferramentas deveriam assumir grande parte de:

```text
formatting
imports
lint
style
code conventions
static analysis
```

Por exemplo:

```text
Spotless
Checkstyle
Sonar
SpotBugs
Error Prone
IDE inspections
```

Assim o reviewer humano pode dedicar atenção a:

```text
design
correctness
risk
business
architecture
```

---

# 15. Comentários de Code Review

Um bom comentário não deveria ser apenas:

> “Está errado.”

Prefira explicar:

```text
problema
+
risco
+
possível direção
```

Exemplo:

> Esse `findById` seguido de `save` pode sofrer Lost Update quando duas requisições alterarem o mesmo pedido simultaneamente. Vale avaliar `@Version` ou outra estratégia de concorrência porque essa operação depende do estado previamente lido.

Isso melhora não apenas aquele Pull Request, mas também o conhecimento da equipe.

---

# 16. Evitar preferência pessoal

Outro ponto importante:

```text
"Eu faria diferente"
```

não significa:

```text
"Precisa mudar"
```

Diferencie:

### Blocking

```text
bug
security
data loss
architecture violation
correctness
```

### Improvement

```text
maintainability
simplificação
performance relevante
```

### Nit

```text
nome
estilo
preferência
```

Isso evita transformar review em disputa de gosto pessoal.

---

# Resposta objetiva para entrevista

> Eu vejo Code Review como uma análise de risco e qualidade da mudança, não apenas uma revisão de estilo. Formatação, imports e convenções eu prefiro deixar para ferramentas automáticas.
>
> Primeiro verifico correctness e domínio: se a implementação realmente atende ao requisito e preserva as invariantes de negócio. Depois avalio arquitetura, boundaries, acoplamento e direção das dependências.
>
> Também presto bastante atenção em concorrência e transações, principalmente race conditions, lost updates, idempotência, fronteiras transacionais e locks.
>
> Em segurança, verifico autenticação, autorização, validação de entrada, exposição de dados e vulnerabilidades como injection e broken access control.
>
> Para performance, procuro problemas como N+1, consultas em loops, processamento desnecessário e chamadas remotas excessivas. Em observabilidade, verifico se a mudança poderá ser diagnosticada em produção através de logs, métricas e traces.
>
> Também avalio testes, failure handling, backward compatibility e maintainability.
>
> O principal para mim é que um bom Code Review deve responder: **essa mudança está correta, segura, observável, sustentável e pode entrar em produção sem introduzir um risco desnecessário?**
