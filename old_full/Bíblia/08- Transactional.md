# 3.2 Transações — Spring + Banco de Dados

O ponto central é separar três responsabilidades:

```text
@Transactional
      ↓
define a fronteira transacional

Propagation
      ↓
define como transações se relacionam

Isolation
      ↓
define quanto uma transação enxerga
das outras transações concorrentes
```

No Spring, `@Transactional` é implementado normalmente através de **AOP + Proxy + TransactionInterceptor + TransactionManager**. Por padrão, usa `Propagation.REQUIRED`, isolamento `DEFAULT`, é read-write e faz rollback automático para `RuntimeException` e `Error`, mas não para checked exceptions. 

---

## 1. Conceitos principais

| Item | Conceito objetivo | Trade-off | Caso de uso |
|---|---|---|---|
| **Transaction** | Unidade de trabalho que deve respeitar propriedades ACID e ser confirmada ou revertida como conjunto. | Transações longas seguram recursos e podem aumentar contenção. | Atualizar pedido, pagamento e estoque atomicamente. |
| **`@Transactional`** | Define declarativamente uma fronteira transacional em métodos/classes Spring. | Depende normalmente de proxy; uso incorreto pode fazer a transação não ser aplicada. | Métodos da camada de serviço com múltiplas operações de banco. |
| **Propagation** | Define o comportamento quando um método transacional chama outro e já existe ou não uma transação. | Algumas estratégias criam mais conexões/transações ou aumentam complexidade. | Auditoria independente, composição de serviços. |
| **Isolation** | Controla o quanto uma transação pode observar mudanças concorrentes de outras transações. | Quanto maior o isolamento, geralmente maior a contenção/custo. | Processos financeiros, estoque e atualizações concorrentes. |
| **Rollback** | Desfaz as mudanças da transação quando ocorre uma falha definida pelas regras transacionais. | Rollback incorretamente configurado pode confirmar alterações parciais. | Falha após alterar várias entidades. |
| **`rollbackFor`** | Define exceções adicionais que devem provocar rollback. | Usar regras muito amplas pode esconder decisões importantes de negócio. | Checked exception que representa falha transacional. |
| **`readOnly`** | Indica que a transação é voltada para leitura e permite possíveis otimizações no transaction manager/ORM. | É principalmente uma indicação; não deve ser tratada como mecanismo universal de segurança contra escrita. | Consultas e relatórios. |
| **Timeout** | Limita quanto tempo uma transação pode executar. | Valor baixo gera rollbacks legítimos; valor alto permite transações presas consumindo recursos. | Evitar operação de banco bloqueada indefinidamente. |
| **Dirty Read** | Ler uma alteração de outra transação que ainda não realizou commit. | Pode trabalhar com dados que depois serão revertidos. | Anomalia normalmente indesejada. |
| **Non-repeatable Read** | Ler a mesma linha duas vezes e obter valores diferentes porque outra transação fez update e commit. | A transação perde estabilidade sobre registros já lidos. | Processamento que lê o mesmo registro várias vezes. |
| **Phantom Read** | Repetir uma consulta por condição e encontrar novas ou menos linhas após alterações concorrentes. | Pode alterar resultados de cálculos baseados em conjuntos. | Consulta `WHERE status = 'PENDING'` executada duas vezes. |
| **Lost Update** | Duas transações leem o mesmo estado, modificam e uma atualização sobrescreve silenciosamente a outra. | Pode causar perda real de dados. | Saldo, estoque, contador, limite financeiro. |
| **Optimistic Lock** | Detecta conflito no momento do update, normalmente usando `@Version`. | Em alta contenção pode gerar muitos retries/conflitos. | Sistemas com mais leitura que escrita concorrente. |
| **Pessimistic Lock** | Bloqueia o registro para impedir alterações concorrentes durante a operação. | Maior contenção e risco de deadlock. | Operações críticas com alta probabilidade de conflito. |

---

# 2. Como `@Transactional` funciona

Considere:

```java
@Service
public class TransferService {

    @Transactional
    public void transfer(...) {

        debitAccount();

        creditAccount();

        saveTransfer();
    }
}
```

O objetivo é:

```text
BEGIN

debitAccount()
creditAccount()
saveTransfer()

      ↓

sucesso
  ↓
COMMIT
```

Se ocorrer uma falha que provoque rollback:

```text
BEGIN

debitAccount()
creditAccount()
      ↓
exception

      ↓

ROLLBACK
```

Assim, não queremos:

```text
conta A debitada
+
conta B não creditada
```

A transação define uma **unidade atômica de negócio**.

---

# 3. `@Transactional` e Proxy

Esse detalhe é muito importante para entrevistas.

Spring normalmente implementa `@Transactional` usando proxy:

```text
Caller
  ↓
Spring Proxy
  ↓
abre transação
  ↓
método real
  ↓
commit / rollback
```

Conceitualmente:

```text
TransferServiceProxy
        ↓
TransactionInterceptor
        ↓
TransactionManager
        ↓
TransferService
```

Por isso existe o conhecido problema de **self-invocation**.

```java
public void process() {
    save();
}

@Transactional
public void save() {
}
```

A chamada:

```java
this.save();
```

não passa novamente pelo proxy.

Assim, em proxy mode, a annotation do método interno pode não iniciar a transação esperada. O suporte declarativo do Spring é normalmente proxy-based e transações imperativas ficam associadas à thread atual. 

---

# 4. Propagation

Propagation responde:

> **O que acontece com a transação quando um método chama outro método transacional?**

## Principais opções

| Propagation | Com transação existente | Sem transação | Uso típico |
|---|---|---|---|
| **REQUIRED** | Participa da atual | Cria nova | Padrão; regra de negócio comum |
| **REQUIRES_NEW** | Suspende atual e cria outra | Cria nova | Auditoria independente |
| **SUPPORTS** | Participa | Executa sem transação | Operação que pode ou não participar |
| **MANDATORY** | Participa | Lança exceção | Método que obrigatoriamente deve ser chamado dentro de transação |
| **NOT_SUPPORTED** | Suspende atual | Executa sem transação | Operação explicitamente não transacional |
| **NEVER** | Lança exceção | Executa normalmente | Garantir ausência de transação |
| **NESTED** | Cria transação aninhada/savepoint quando suportado | Comporta-se como REQUIRED | Rollback parcial em cenários específicos |

Esses são os comportamentos definidos pelo Spring; `REQUIRED` é o padrão. `NESTED` depende do suporte do transaction manager e normalmente utiliza savepoints. 

---

# 5. REQUIRED

É o caso mais comum.

```java
@Transactional
public void createOrder() {
    paymentService.pay();
}
```

Se `pay()` também usa:

```java
@Transactional
```

com `REQUIRED`:

```text
createOrder()
     ↓
Transaction A
     ↓
pay()
     ↓
mesma Transaction A
```

Se qualquer parte relevante falhar:

```text
Transaction A
     ↓
ROLLBACK
```

Esse comportamento é apropriado quando todas as operações pertencem à **mesma unidade de negócio**.

---

# 6. REQUIRES_NEW

Agora imagine:

```java
@Transactional
public void processPayment() {

    payment();

    auditService.register();
}
```

E:

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void register() {
}
```

Fluxo:

```text
Transaction A
processPayment()

     ↓ suspende A

Transaction B
register audit

     ↓ commit B

retoma A
```

Se posteriormente:

```text
Transaction A
ROLLBACK
```

a auditoria já pode ter realizado:

```text
Transaction B
COMMIT
```

Isso pode ser útil para:

- auditoria;
- histórico;
- registro independente.

Mas existe custo: `REQUIRES_NEW` utiliza uma transação física independente e pode exigir outra conexão enquanto a transação externa permanece suspensa. 

---

# 7. NESTED x REQUIRES_NEW

Não são iguais.

### REQUIRES_NEW

```text
Transaction A
   ↓
suspensa

Transaction B
   ↓
independente
```

### NESTED

Conceitualmente:

```text
Transaction A
   ↓
SAVEPOINT
   ↓
operação interna
```

Se a operação interna falhar:

```text
ROLLBACK TO SAVEPOINT
```

sem necessariamente desfazer tudo.

`NESTED` depende do suporte do transaction manager; no Spring, o caso clássico é JDBC com `DataSourceTransactionManager`. 

---

# 8. Isolation

Isolation responde:

> **Quanto uma transação pode enxergar das alterações feitas por outras transações concorrentes?**

Quanto maior o isolamento:

```text
mais consistência
      ↑

mais isolamento
      ↑

potencialmente mais locks,
espera ou conflitos
```

O Spring usa `Isolation.DEFAULT` por padrão, delegando ao banco/configuração subjacente. 

---

# 9. Isolation Levels

| Isolation | Dirty Read | Non-repeatable Read | Phantom Read | Trade-off |
|---|---|---|---|---|
| **READ UNCOMMITTED** | Pode ocorrer | Pode ocorrer | Pode ocorrer | Maior concorrência, menor isolamento |
| **READ COMMITTED** | Evita | Pode ocorrer | Pode ocorrer | Bom equilíbrio e muito utilizado |
| **REPEATABLE READ** | Evita | Evita | Pode ocorrer no modelo JDBC/SQL tradicional | Maior consistência |
| **SERIALIZABLE** | Evita | Evita | Evita | Maior isolamento e maior custo |

Essa é a semântica clássica dos níveis JDBC. Bancos modernos podem implementar esses níveis usando MVCC e apresentar garantias adicionais específicas. 

---

# 10. Dirty Read

Imagine:

```text
Transaction A

saldo = 100
↓
UPDATE saldo = 0
↓
ainda sem COMMIT
```

Transaction B lê:

```text
saldo = 0
```

Depois A:

```text
ROLLBACK
```

Na realidade:

```text
saldo = 100
```

B trabalhou com um valor que **nunca chegou a existir de forma confirmada**.

Isso é Dirty Read.

`READ COMMITTED` já impede esse problema.

---

# 11. Non-repeatable Read

Transaction A:

```text
SELECT saldo
↓
100
```

Transaction B:

```text
UPDATE saldo = 50
COMMIT
```

Transaction A novamente:

```text
SELECT saldo
↓
50
```

Dentro da mesma transação:

```text
primeira leitura = 100
segunda leitura = 50
```

Isso é Non-repeatable Read.

No modelo clássico, `REPEATABLE READ` impede essa anomalia. 

---

# 12. Phantom Read

Transaction A:

```sql
SELECT *
FROM orders
WHERE status = 'PENDING';
```

Resultado:

```text
10 registros
```

Transaction B:

```sql
INSERT INTO orders (...)
status = 'PENDING';

COMMIT;
```

Transaction A repete:

```sql
SELECT *
FROM orders
WHERE status = 'PENDING';
```

Agora:

```text
11 registros
```

A nova linha é o:

**phantom**.

No modelo clássico JDBC, `SERIALIZABLE` evita dirty reads, non-repeatable reads e phantom reads. 

---

# 13. Lost Update

Essa é uma das anomalias mais importantes para sistemas reais.

Estado inicial:

```text
estoque = 10
```

Transaction A:

```text
READ estoque = 10
```

Transaction B:

```text
READ estoque = 10
```

A calcula:

```text
10 - 1 = 9
```

B calcula:

```text
10 - 2 = 8
```

A grava:

```text
estoque = 9
```

B grava:

```text
estoque = 8
```

Resultado:

```text
8
```

Mas o correto seria:

```text
7
```

A alteração da Transaction A foi perdida.

---

# 14. Como evitar Lost Update

No Hibernate, uma solução muito utilizada é **Optimistic Locking** com:

```java
@Version
private Long version;
```

Imagine:

```text
id = 1
estoque = 10
version = 5
```

Transaction A e B carregam:

```text
version = 5
```

A atualiza:

```sql
UPDATE product
SET estoque = 9,
    version = 6
WHERE id = 1
  AND version = 5;
```

Sucesso.

B tenta:

```sql
UPDATE product
SET estoque = 8,
    version = 6
WHERE id = 1
  AND version = 5;
```

Mas agora:

```text
version atual = 6
```

Então:

```text
0 rows updated
```

O Hibernate detecta o conflito em vez de sobrescrever silenciosamente a alteração anterior. O versionamento otimista existe justamente para detectar atualizações concorrentes e prevenir o padrão last-commit-wins. 

---

# 15. `readOnly`

Exemplo:

```java
@Transactional(readOnly = true)
public List<Customer> findAll() {
    return repository.findAll();
}
```

O objetivo é informar:

> Essa unidade de trabalho é destinada a leitura.

Pode permitir otimizações no transaction manager, JDBC ou Hibernate.

Mas atenção:

```text
readOnly
≠
garantia absoluta de que
nenhum UPDATE poderá acontecer
```

A própria abstração Spring trata `readOnly` como uma flag que permite otimizações conforme o recurso/transaction manager utilizado. 

Use principalmente para expressar intenção e permitir otimizações em fluxos de consulta.

---

# 16. Timeout

Exemplo:

```java
@Transactional(timeout = 5)
public void process() {
}
```

Isso define um timeout transacional de:

```text
5 segundos
```

A intenção é impedir que uma operação permaneça indefinidamente consumindo:

```text
connection
locks
threads
recursos do banco
```

O timeout padrão é definido pelo sistema transacional subjacente, ou pode não existir quando não há suporte. 

---

# 17. Rollback

Por padrão:

```text
RuntimeException
      ↓
ROLLBACK

Error
      ↓
ROLLBACK
```

Mas:

```text
checked Exception
      ↓
normalmente NÃO provoca
rollback por padrão
``` 


Exemplo:

```java
@Transactional
public void process() throws IOException {
}
```

Uma `IOException`, apenas por ser checked exception, não provoca automaticamente rollback segundo a regra padrão.

Se precisar:

```java
@Transactional(rollbackFor = Exception.class)
```

ou, preferencialmente, uma exceção específica:

```java
@Transactional(
    rollbackFor = PaymentException.class
)
```

---

# 18. Cuidado ao capturar exceções

Considere:

```java
@Transactional
public void process() {

    try {
        repository.save(...);
        payment();
    } catch (Exception e) {
        log.error("Erro", e);
    }
}
```

Se você captura a exceção e não a propaga:

```text
Spring Proxy
     ↓
método terminou normalmente
```

O Spring pode não perceber a falha como motivo para rollback.

Uma regra prática é:

> **Não engula exceções que deveriam invalidar a transação.**

Se necessário, propague a exceção ou marque explicitamente a transação como rollback-only.

---

# 19. Onde colocar `@Transactional`

Normalmente faz mais sentido na **camada de serviço**, em torno da unidade de negócio:

```java
@Service
class CheckoutService {

    @Transactional
    public void checkout() {

        createOrder();

        updateStock();

        createPayment();
    }
}
```

E não espalhar indiscriminadamente em cada repository:

```text
Transaction
│
├── createOrder
├── updateStock
└── createPayment
```

Assim, a fronteira transacional representa melhor a:

> **unidade atômica de negócio.**

---

# 20. Mapa mental para entrevistas

Memorize:

```text
@Transactional
      ↓
fronteira da transação


Propagation
      ↓
como transações se relacionam


Isolation
      ↓
o que uma transação
enxerga das outras


Rollback
      ↓
quando desfazer


readOnly
      ↓
intenção de leitura


Timeout
      ↓
limite de execução
```

E:

```text
READ UNCOMMITTED
      ↓
mais permissivo

READ COMMITTED
      ↓

REPEATABLE READ
      ↓

SERIALIZABLE
      ↓
mais isolado
```

Quanto maior o isolamento, em geral, maior a proteção contra anomalias e maior o potencial custo de concorrência.

---

# Resposta objetiva para entrevista

Se perguntarem **"Como você trabalha com transações no Spring?"**, uma resposta consistente seria:

> Eu normalmente defino a fronteira transacional na camada de serviço, envolvendo uma unidade completa de negócio. O Spring implementa `@Transactional` através de infraestrutura AOP e proxy, usando um `TransactionManager` para abrir, confirmar ou reverter a transação. Por isso também tomo cuidado com self-invocation, porque uma chamada interna pode não passar pelo proxy. 
>
> Em propagation, o padrão é `REQUIRED`, que participa de uma transação existente ou cria uma nova. Uso `REQUIRES_NEW` somente quando preciso de uma transação realmente independente, por exemplo para um registro de auditoria que deve persistir mesmo se a operação externa sofrer rollback. 
>
> Isolation controla quais alterações concorrentes uma transação pode enxergar. `READ COMMITTED` evita dirty reads; `REPEATABLE READ` também evita non-repeatable reads no modelo tradicional; e `SERIALIZABLE` fornece o maior nível de isolamento, evitando também phantom reads, mas com maior custo de concorrência. 
>
> Também presto atenção a lost updates. Quando duas transações podem alterar a mesma entidade, frequentemente utilizo optimistic locking com `@Version`, fazendo o Hibernate detectar conflitos em vez de permitir que uma atualização sobrescreva silenciosamente a outra. 
>
> Por padrão, Spring faz rollback para `RuntimeException` e `Error`, mas não para checked exceptions, então uso `rollbackFor` quando a regra exigir. `readOnly` expressa uma transação voltada à leitura e pode permitir otimizações, enquanto `timeout` limita quanto tempo a transação pode permanecer executando. 
>
> Então, para mim, trabalhar corretamente com transações significa definir bem **a fronteira de negócio, propagation, isolation, regras de rollback e estratégia de concorrência**, em vez de apenas adicionar `@Transactional` nos métodos.
