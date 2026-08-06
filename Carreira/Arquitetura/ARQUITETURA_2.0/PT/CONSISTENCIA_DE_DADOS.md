
# 2. "Dentro de um único banco de dados, utilizaria transações ACID"

Quando toda a operação acontece no mesmo banco, o próprio banco resolve a consistência.

* Usando exemplo de tranferencia de saldo numa conta para outra. 
`Ambas acontecem` ou `nenhuma acontece`.

É exatamente isso que a transação ACID garante.

No Spring:

```java
@Transactional
public void transferir(...) {

    debitar();

    creditar();

}
```

Se qualquer operação falhar:

```
ROLLBACK
```

Tudo volta ao estado anterior.

---

# O que significa ACID?
| Propriedade          | Descrição                                                                                                       |
| -------------------- | --------------------------------------------------------------------------------------------------------------- |
| **A — Atomicidade**  | Tudo ou nada. A transação é concluída integralmente (**commit**) ou totalmente desfeita (**rollback**).         |
| **C — Consistência** | Garante que as regras e restrições do banco permaneçam válidas. Se uma operação as violar, ocorre **rollback**. |
| **I — Isolamento**   | Garante que transações concorrentes não interfiram incorretamente umas nas outras durante a execução.           |
| **D — Durabilidade** | Após o **commit**, os dados persistidos não podem ser perdidos, mesmo em caso de falha do sistema.              |

---

# 3. "Constraints"

Constraints são regras impostas pelo banco.

Exemplo:


| Constraint      | Função                                                                          |
| --------------- | ------------------------------------------------------------------------------- |
| **PRIMARY KEY** | Identifica unicamente cada registro da tabela e não permite valores nulos.      |
| **UNIQUE**      | Impede valores duplicados em uma ou mais colunas.                               |
| **CHECK**       | Garante que os valores atendam a uma condição definida.                         |
| **FOREIGN KEY** | Garante a integridade referencial, exigindo que o registro referenciado exista. |
| **NOT NULL**    | Impede que uma coluna obrigatória receba valores nulos.                         |

**Essas constraints impedem que o banco de dados assuma estados inválidos.**

### Exemplos

| Situação                                               | Constraint                 | Resultado                                                            |
| ------------------------------------------------------ | -------------------------- | -------------------------------------------------------------------- |
| Impedir e-mails duplicados                             | `UNIQUE(email)`            | O banco rejeita registros com o mesmo e-mail.                        |
| Garantir que um pedido pertença a um cliente existente | `FOREIGN KEY (cliente_id)` | O banco impede a criação de pedidos com um `cliente_id` inexistente. 

---

# 4. "Controle de concorrência"

Em sistemas distribuídos, vários usuários alteram os mesmos dados ao mesmo tempo.


## Lock pessimista

#### Bloqueia o registro.

> Enquanto uma transação trabalha, ninguém altera aquele registro.

- _*É seguro,mas reduz concorrência.*_

---

## Lock otimista

Mais comum em Spring.

> Não bloqueia o registro enquanto ele está sendo editado.
>
> Em vez disso, ele verifica na hora de salvar se outra transação modificou o registro antes.

- isso é feito com a anotação `@Version`

> Quando o Hibernate vê que nenhuma linha foi atualizada, ele conclui:
>
> "Alguém alterou esse registro depois que você o leu."
>
>Então ele lança a exceção: `OptimisticLockException`

---


# Como responder em uma entrevista

> A primeira decisão é entender qual nível de consistência o negócio exige. Se toda a operação ocorre em um único banco, utilizo transações ACID, constraints e controle de concorrência, como optimistic locking com `@Version` ou locks pessimistas quando apropriado. Em microsserviços, não existe uma transação única entre serviços, então normalmente adoto consistência eventual. Para isso, publico eventos de negócio usando o Outbox Pattern para evitar perda de mensagens, implemento consumidores idempotentes para lidar com reentregas e, quando uma operação envolve múltiplos serviços, utilizo Sagas com ações compensatórias para manter a consistência do processo como um todo.
