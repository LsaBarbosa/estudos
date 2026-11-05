# ACID
- **Atomicidade** : Transação é confirmada por completo ou é desfeita.
- **Consistência**: Regras do banco devem permanecer válidas.
- **Isolamento**: Controla como transações concorrentes interagem.
- **Durabilidade** : Após commit, dados confirmados sobrevivem a falhas.

# Isolamento
## Níveis de Isolamento
- **Read Uncommitted**: Permite o menor isolamento, pode admitir leituras não confirmada.
- **Read Committed**: Impede Dirty Reads.
- **Repeatable** **Read**: Impede Dirty Reads e Non-Repeatable Reads.

### Java
```java

@Transactional(
    isolation = Isolation.READ_COMMITTED
)
public void processarPedido(Long pedidoId) {
    // Operações transacionais
}
```

# Anomalia de Leitura

### Dirty Read: 
Quando uma transação Lẽ dados modificados por outra transação que não commitou.

  - T1: Atualiza um saldo para 500 e executa rollback.
  - T2: Lê saldo 500 e atualiza um estado inválido.
    
  - O Read Commited impede o dirty read. 

### Non-Repeatable Read
Quando a mesma transação consulta o mesmo registro duas vezes e obtém valores diferentes, por causa de um commit realizado por outra transação.

  - T1: busca saldo e recebe o valor 100. E atransação permanece aberta.
  - T2: faz um update e agora o valor alterado é 300.
  - T1 consulta novamente e recebe o valor 300.
 
- O Repeatable Read impede essa anomalia.

### Lost Update
Quando duas transações calculam alterações a partir de um mesmo estado e uma sobrescreve a outra.

  - @Transactional sozinho não garante prevenção de lost updade  

  - T1: Lê estoque = 10 e diminui 3 unidades, resultado = 7.
  - T2: Lê estoque = 10 e diminui 4 unidades, resultado = 6.
  - A venda de T1 foi perdida, pela sobrescrita de T2 com base numa leitura desatualizada.
 
  - Soluções
      1. Optimistic locking com @Version.
      2. Pessimistic locking com SELECT FOR UPDATE.
      3. Atualização condicional atômica.
      4. Isolamento adequado com tratamento de conflitos.

### Deadlock
Acontece quando duas ou mais transações aguardam  recurso uma da outra, formando ciclos de dependência 

  - T1: bloqueia conta A e tenta bloquear B., T1 possui lock A
  - T2: bloqueia conta B e tenta bloquear A, T2 possui lock B
  - Dependência circular, nenhuma das duas consegue avançar

    - O banco pode abortar uma das transações para permitir que as demais avancem 


# Optmistc e Pessimistc Concurrency
- **Optimistic Locking**
  - Assume que conflitos são relativamente raros
  - Detecta conflitos na atualização e pode exigir retry.
  - Uso de @Version para versão da transação

 ### Java
```java

@Entity
public class Produto {

    @Id
    private Long id;

    private Integer estoque;

    @Version
    private Long version;
}
```
 ### Hibernate
   - O hibernate faz o equivalente a
```sql
UPDATE produtos
SET estoque = 7,
    version = 2
WHERE id = 10
  AND version = 1;
```
  - Se outra transação ja tive atualizado para versão 2, a condição 1 não corresponderá a nenhuma linha
  - Isso impede que uma alteração baseada em uma versão obsoleta sobrescreva silenciosamente a mais recente
  - O Hibernate detecta o conflito e gera uma exceção de optimistic locking.

- **Pessimistic Locking**
  -  A transação adquire o bloqueio antes de realizar a alteração.
  -  Adequado quando quando a disputa é frequente
