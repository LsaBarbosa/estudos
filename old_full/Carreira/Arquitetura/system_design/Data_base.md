# ACID

## A → Atomicidade
 - Ou todas as operações da transação são concluídas, ou nenhuma delas é aplicada.
 - O banco registra informações suficientes para confirmar ou desfazer uma transação
 - **_@ Transaction_** no Spring não faz rollback em qualquer exceção.
   - Em configurações tradicionais do Spring, exceções não verificadas normalmente provocam rollback:
   - Exceções verificadas podem exigir configuração:
               
          @Transactional(rollbackFor = Exception.class)
              public void processar() throws Exception {
           }
### Como o banco implementa
 - Dependendo do banco, a atomicidade pode utilizar mecanismos como:
    * logs de transação, registros de undo, versões anteriores das linhas, páginas temporárias e recuperação após falhas
## C → Consistência
 - Uma transação deve levar o banco de um estado válido para outro estado válido, preservando as regras e invariantes do domínio.
   - Invariantes são regras de negócio como um saldo nunca poder ser negativo.
   - Uso de Constraint (restrição)
   

 - A consistência é uma responsabilidade compartilhada entre:
    * aplicação, modelo de domínio, constraints do banco, transações, chaves estrangeiras, índices únicos eregras de validação.
### ACID Consistency não é CAP Consistency
 - **Consistência do ACID**: Preservação das regras e invariantes do banco.

 - **Consistência do CAP**: Todos os nós de um sistema distribuído observam o mesmo valor mais recente.
## I → Isolamento
Transações concorrentes não devem interferir incorretamente umas nas outras.
 - O nível de isolamento determina o equilíbrio entre: Consistência e Concorrência.


 - Quanto maior o isolamento:
     menor a possibilidade de anomalias, maior o custo de coordenação, maior a possibilidade de bloqueios, menor pode ser o throughput.

## D → Durabilidade
- Depois que o banco confirma o commit, os dados devem sobreviver a falhas: reinicialização, queda do servidor, etc.


- Como é implementada

      transaction log;
      write-ahead logging;
      redo log;
      flush para armazenamento persistente;
      checkpoints;
      recuperação após falhas.

- _Em uma estratégia baseada em Write-Ahead Log, o banco registra a alteração no log antes de considerar a transação confirmada._

## Resumo
**_Atomicidade_** garante que uma transação seja aplicada integralmente ou desfeita integralmente. Em uma transferência, não posso debitar uma conta sem creditar a outra. Caso alguma etapa falhe, o banco executa rollback e retorna ao estado anterior.

**_Consistência_** significa preservar os invariantes do domínio antes e depois da transação. Ela pode ser garantida por regras da aplicação e mecanismos do banco, como primary keys, foreign keys, unique constraints e check constraints. Não deve ser confundida com consistência no contexto do teorema CAP.

**_Isolamento_** controla como alterações concorrentes ficam visíveis entre transações. Níveis mais fortes evitam mais anomalias, mas normalmente aumentam bloqueios, abortos ou custo de processamento.

**_Durabilidade_** garante que uma transação confirmada permaneça persistida mesmo após uma falha do processo ou reinicialização. Normalmente isso é obtido por logs de transação e mecanismos de recuperação. Durabilidade não substitui backup ou disaster recovery.
# Concorrência 
## Dirty Read
- Uma transação lê uma alteração de outra transação que ainda não realizou COMMIT.

Sequência:

      Transação A: UPDATE conta SET saldo = 2000
      Transação B: SELECT saldo → 2000
      Transação A: ROLLBACK

      Transação B utilizou um valor que nunca sexistiu oficialmente

## Non-Repeatable Read
- Uma transação lê a mesma linha duas vezes e obtém valores diferentes porque outra transação alterou e confirmou a linha entre as leituras.

Sequência:

      Transação A: le saldo = 1000
      Transação B: altera saldo para 900
      Transação B: commit
      Transação A: le saldo  = 9000

      Dentro da mesma transação, a mesma consulta sobre a mesma linha retornou valores diferentes.

## Phantom Read
- Uma transação executa a mesma consulta por condição duas vezes, mas encontra um conjunto diferente de linhas.

Sequência:

      Primeira execução: 10 pedidos
      Outra transação insere um novo pedido
      Segunda execução: 11 pedidos

      A nova linha é chamada informalmente de “fantasma”, pois apareceu entre duas leituras da mesma transação.


## Lost Update
- Duas transações leem o mesmo valor e realizam atualizações baseadas nele. Uma atualização sobrescreve a outra.s.
  
Sequência:

      Estoque inical 10 unidades
      T1 lê estoque = 10
      T2 lê estoque = 10

      T1 vende 3 → grava estoque = 7
      T2 vende 4 → grava estoque = 6

      resultado correto seria 3

Soluções

      atualização atômica;
      optimistic locking;
      pessimistic locking;
      nível de isolamento mais forte;
      controle explícito de versão.

## Write Skew
- Duas transações leem um conjunto de dados válido e alteram linhas diferentes. Individualmente, cada alteração parece válida, mas o resultado combinado viola uma regra.

Sequência:

        Estado inical Medico A e B estão de plantão, a regra é que deve haver ao menos 1
        T1 verifica que B está de plantão
        T2 verifica que A está de plantão
      
        T1 remove A do plantão
        T2 remove B do plantão
      
        T1 commit
        T2 commit

        Nenhum médico de plantão
- _**Não houve necessariamente atualização da mesma linha, mas a regra global foi violada.**_
# Níveis de isolamento de transações

Os níveis de isolamento definem o quanto uma transação fica protegida das alterações realizadas por outras transações concorrentes.

Do isolamento mais fraco para o mais forte:

```text
READ UNCOMMITTED
        ↓
READ COMMITTED
        ↓
REPEATABLE READ
        ↓
SERIALIZABLE
```

## Tabela comparativa

| Nível              | Conceito                                                                                                                            | O que a transação consegue enxergar                                                                              | Anomalias normalmente evitadas                              | Anomalias que ainda podem ocorrer                                                                                 | Concorrência |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- | ------------ |
| `READ UNCOMMITTED` | Permite que uma transação leia alterações realizadas por outras transações antes do `COMMIT`. É o nível com menor isolamento.       | Pode enxergar alterações ainda não confirmadas por outras transações.                                            | Nenhuma das principais anomalias é necessariamente evitada. | Dirty read, non-repeatable read, phantom read e outras inconsistências.                                           | Muito alta   |
| `READ COMMITTED`   | Cada comando SQL enxerga apenas dados que já estavam confirmados no momento em que o comando começou.                               | Enxerga somente dados confirmados, mas comandos diferentes da mesma transação podem enxergar estados diferentes. | Dirty read.                                                 | Non-repeatable read, phantom read e, dependendo do banco e da operação, lost update.                              | Alta         |
| `REPEATABLE READ`  | Mantém uma visão consistente dos dados lidos durante toda a transação, impedindo que a mesma leitura retorne valores diferentes.    | As leituras normalmente utilizam uma visão estável criada durante a transação.                                   | Dirty read e non-repeatable read.                           | Phantom read, write skew e algumas formas de lost update, dependendo da implementação do banco.                   | Média        |
| `SERIALIZABLE`     | Executa as transações de forma que o resultado final seja equivalente a uma execução sequencial, mesmo que ocorram simultaneamente. | Enxerga dados de forma compatível com uma ordem serial válida entre as transações.                               | Principais anomalias de leitura e escrita concorrente.      | Não permite inconsistências de serialização, mas pode gerar abortos, bloqueios, conflitos e necessidade de retry. | Menor        |


---


## Anomalias por nível

A tabela abaixo representa o comportamento tradicional definido pelo padrão SQL. Implementações específicas podem oferecer garantias adicionais.

| Anomalia            | `READ UNCOMMITTED` | `READ COMMITTED` |     `REPEATABLE READ`    |    `SERIALIZABLE`   |
| ------------------- | :----------------: | :--------------: | :----------------------: | :-----------------: |
| Dirty read          |    Pode ocorrer    |      Evitada     |          Evitada         |       Evitada       |
| Non-repeatable read |    Pode ocorrer    |   Pode ocorrer   |          Evitada         |       Evitada       |
| Phantom read        |    Pode ocorrer    |   Pode ocorrer   | Pode ocorrer pelo padrão |       Evitada       |
| Lost update         |    Pode ocorrer    |   Pode ocorrer   |     Depende do banco     | Normalmente evitada |
| Write skew          |    Pode ocorrer    |   Pode ocorrer   |       Pode ocorrer       |       Evitada       |

---

## Resumo para decisão

| Necessidade                                                 | Nível mais apropriado                                      |
| ----------------------------------------------------------- | ---------------------------------------------------------- |
| Consulta aproximada sem criticidade                         | `READ UNCOMMITTED`, quando realmente suportado e aceitável |
| Aplicação CRUD transacional comum                           | `READ COMMITTED`                                           |
| Leituras consistentes durante toda a transação              | `REPEATABLE READ`                                          |
| Proteção de invariantes críticas                            | `SERIALIZABLE`                                             |
| Evitar bloqueio durante atualização de uma linha específica | `SELECT ... FOR UPDATE`, dependendo do caso                |
| Evitar conflito de edição com controle pela aplicação       | Optimistic locking com coluna de versão                    |

---

## Resumo para entrevista

| Nível              | Resposta objetiva                                                                                                                 |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------- |
| `READ UNCOMMITTED` | Permite leitura de dados ainda não confirmados e pode gerar dirty reads.                                                          |
| `READ COMMITTED`   | Lê apenas dados confirmados, mas consultas repetidas podem retornar valores diferentes.                                           |
| `REPEATABLE READ`  | Mantém leituras estáveis durante a transação, mas o tratamento de phantom reads e write skew depende do banco.                    |
| `SERIALIZABLE`     | Garante resultado equivalente a uma execução sequencial, podendo usar bloqueios, abortos e retries para preservar a serialização. |

## Regra mental

```text
Mais isolamento
      ↓
Mais consistência
      ↓
Menos anomalias
      ↓
Maior possibilidade de bloqueio ou aborto
      ↓
Menor concorrência em cenários de contenção
```
# Locks
- Lock é um bloqueio sobre um recurso.

**_Lock compartilhado_** : Normalmente associado à leitura.

        Várias transações podem compartilhar determinados locks de leitura.

**_Lock exclusivo_** : Normalmente associado à escrita.

    Impede atualizações concorrentes incompatíveis sobre o mesmo recurso.

Problema

Locks longos aumentam:

    contenção;
    tempo de espera;
    risco de deadlock;
    latência;
    redução de throughput.
# MVCC Multi-Version Concurrency Control

O banco mantém versões dos dados para que leitores possam visualizar um snapshot consistente sem necessariamente bloquear escritores.

Exemplo conceitual:

        Versão 1: saldo = R$ 1.000,00
        Versão 2: saldo = R$ 900,00
    
        Uma transação mais antiga pode continuar vendo a versão 1, enquanto outra transação já confirmou a versão 2.

Benefícios

        leitores bloqueiam menos escritores;
        maior concorrência;
        snapshots consistentes;
        melhor desempenho em cargas de leitura.
Limitações

MVCC não elimina:

        conflitos de escrita;
        deadlocks;
        write skew;
        necessidade de limpeza de versões antigas;
        necessidade de níveis adequados de isolamento.
        Entrevista

**_MVCC mantém múltiplas versões das linhas, permitindo que leitores trabalhem sobre snapshots sem bloquear necessariamente os escritores. Isso melhora a concorrência, mas não elimina conflitos entre escritas nem garante sozinho todos os invariantes de negócio._**
# Concorrência
- Ocorre quando duas ou mais transações tentam alterar o mesmo dado simultaneamente.

      Lost update: uma atualização sobrescreve a outra.
      Deadlock: duas transações ficam esperando recursos bloqueados entre si.
      Dados inconsistentes: o valor final não representa corretamente todas as operações.

  - **_Optimistic Locking:_** usa @Version para detectar se outro processo alterou o registro antes da confirmação.
          
        @Version
        private Long version;
        ---------------------
          Usado quando:
              conflitos são relativamente raros;
              bloquear antecipadamente seria caro;
              é aceitável repetir a operação;
              há mais leitura que escrita concorrente.

  - **_Pessimistic Locking:_** bloqueia o registro no banco enquanto a transação está sendo executada.

        @Lock(LockModeType.PESSIMISTIC_WRITE)
        Optional<Produto> findById(UUID id);
  
        Indicado quando
            conflitos são frequentes;
            a operação é crítica;
            repetir a operação é caro;
            a contenção é conhecida e controlada.
    - Pode gerar: Deadlock, timeout, aumento de latência 


  - **_Atualização atômica:_** executa a validação e a alteração diretamente no banco.
    
          UPDATE produto
          SET estoque = estoque - 1
          WHERE id = ? AND estoque > 0;

_Em geral, utiliza-se optimistic locking quando conflitos são raros e pessimistic locking quando várias transações disputam frequentemente o mesmo registro._
# Read Réplica
### São cópias de um banco principal usadas principalmente para operações de leitura.

## Funcionamento

* **Primary/Master:** recebe escritas, como `INSERT`, `UPDATE` e `DELETE`.
* **Réplicas:** recebem uma cópia das alterações do banco principal e atendem consultas `SELECT`.

```text
Aplicação
   ├── Escritas ──> Banco principal
   └── Leituras ──> Réplicas de leitura
```

O objetivo é distribuir consultas entre vários bancos, reduzindo a carga do principal e aumentando a capacidade de leitura.

## Principal problema: atraso de replicação

Por ser uma ação assíncrona pode demorar de milesegundos a segundos a replicaçao.  

Exemplo:

1. O usuário altera seu endereço no banco principal.
2. A aplicação consulta imediatamente uma réplica.
3. A réplica ainda retorna o endereço antigo.

Esse comportamento é chamado de **replication lag** e resulta em **consistência eventual**.

## Relação com Spring Boot

Em uma aplicação Spring Boot, normalmente são configurados dois `DataSource`:

* um para escrita;
* outro para leitura.

A aplicação pode direcionar consultas usando um `AbstractRoutingDataSource`.

```java
@Transactional
public void atualizarProduto(Produto produto) {
    repository.save(produto); // Banco principal
}

@Transactional(readOnly = true)
public Produto buscarProduto(UUID id) {
    return repository.findById(id).orElseThrow(); // Réplica
}
```

O `readOnly = true` não direciona automaticamente para uma réplica. É necessário configurar o roteamento entre os bancos.



> **Resumo:** read replicas aumentam a escalabilidade de leitura, mas introduzem atraso de replicação e consistência eventual.





# Resumo para entrevista
> ACID representa atomicidade, consistência, isolamento e durabilidade. Atomicidade garante tudo ou nada; consistência preserva os invariantes do domínio; isolamento controla a interferência entre transações concorrentes; e durabilidade garante que um commit sobreviva a falhas.
>
> Em relação ao isolamento, os principais níveis são Read Uncommitted, Read Committed, Repeatable Read e Serializable. Quanto maior o isolamento, menor a possibilidade de anomalias, mas maior pode ser o custo de coordenação, bloqueio ou abortos.
>
> Eu não escolho o nível de isolamento isoladamente. Analiso o invariante de negócio, o padrão de concorrência e a implementação do banco. Para evitar lost updates, por exemplo, posso utilizar atualização atômica, optimistic locking com versionamento ou pessimistic locking. Em sistemas distribuídos, uma transação local não atravessa automaticamente microserviços, então utilizo estratégias como Saga, Transactional Outbox e idempotência.

---

# 15. Perguntas comuns de entrevista

## “Qual é a diferença entre atomicidade e consistência?”

**Atomicidade**:

```text
A transação acontece por completo ou é desfeita.
```

**Consistência**:

```text
As regras e invariantes permanecem válidos.
```

---

## “Read Committed impede lost update?”

Não necessariamente.

Ele impede dirty reads, mas duas transações ainda podem ler o mesmo valor e tentar atualizá-lo. A proteção depende:

- da query;
- dos locks;
- do mecanismo do banco;
- do uso de versionamento;
- do nível efetivo de isolamento.

---

## “Quando você usaria Serializable?”

Quando:

- o invariante é crítico;
- anomalias não podem ser toleradas;
- a contenção é administrável;
- a aplicação sabe tratar abortos e retries;
- alternativas mais específicas não resolvem adequadamente.

---

## “Optimistic ou pessimistic locking?”

**Optimistic locking**:

- conflitos raros;
- não bloqueia antecipadamente;
- detecta conflito na atualização;
- exige retry ou retorno de erro.

**Pessimistic locking**:

- conflitos frequentes;
- bloqueia o recurso;
- reduz atualizações concorrentes;
- aumenta risco de espera e deadlock.

---

## “Como evitar overselling?”

Possíveis estratégias:

```sql
UPDATE produto
SET estoque = estoque - :quantidade
WHERE id = :id
  AND estoque >= :quantidade;
```

Ou:

- optimistic locking;
- `SELECT FOR UPDATE`;
- serializable;
- reserva temporária com expiração;
- fila para serializar o processamento em casos específicos.

---

## “Por que não usar Serializable em tudo?”

Porque pode aumentar:

- contenção;
- latência;
- abortos;
- retries;
- consumo de recursos;
- dificuldade de escalar escritas concorrentes.

O nível deve ser proporcional ao risco da operação.

---

# 16. Mapa mental

```text
TRANSAÇÕES
│
├── ACID
│   ├── Atomicidade
│   │   └── Tudo ou nada
│   │
│   ├── Consistência
│   │   └── Preserva invariantes
│   │
│   ├── Isolamento
│   │   └── Controla concorrência
│   │
│   └── Durabilidade
│       └── Commit sobrevive a falhas
│
├── ANOMALIAS
│   ├── Dirty read
│   ├── Non-repeatable read
│   ├── Phantom read
│   ├── Lost update
│   └── Write skew
│
├── ISOLAMENTO
│   ├── Read Uncommitted
│   ├── Read Committed
│   ├── Repeatable Read
│   └── Serializable
│
├── CONTROLE DE CONCORRÊNCIA
│   ├── MVCC
│   ├── Locks
│   ├── Optimistic locking
│   ├── Pessimistic locking
│   └── Atualização atômica
│
├── JAVA/SPRING
│   ├── @Transactional
│   ├── Isolation
│   ├── Rollback
│   ├── Proxy
│   ├── Self-invocation
│   └── @Version
│
└── SISTEMAS DISTRIBUÍDOS
    ├── Transação local
    ├── Saga
    ├── Transactional Outbox
    ├── Idempotência
    └── Compensação
```

# Resumo para revisão

| Conceito | Definição curta |
|---|---|
| Transação | Unidade lógica de trabalho |
| Atomicidade | Tudo ou nada |
| Consistência | Preserva regras do domínio |
| Isolamento | Controla concorrência |
| Durabilidade | Commit não desaparece após falha |
| Dirty read | Ler dado sem commit |
| Non-repeatable read | A mesma linha muda entre leituras |
| Phantom read | O conjunto de linhas muda |
| Lost update | Uma escrita sobrescreve outra |
| Write skew | Escritas diferentes violam uma regra global |
| MVCC | Mantém versões para permitir snapshots |
| Optimistic locking | Detecta conflito por versão |
| Pessimistic locking | Bloqueia antes de modificar |
| Serializable | Resultado equivalente a execução sequencial |
| Outbox | Salva dado e evento na mesma transação local |