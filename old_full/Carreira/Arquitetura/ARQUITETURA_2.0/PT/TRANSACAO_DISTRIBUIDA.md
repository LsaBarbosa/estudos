## O que é uma transação distribuída?

Uma **transação distribuída** acontece quando uma única operação de negócio precisa alterar **mais de um recurso independente**, mas todos esses recursos precisam permanecer consistentes.


# O problema

Dentro de um único banco é simples:

```text
BEGIN

Atualiza conta A
Atualiza conta B

COMMIT
```

Se ocorrer erro:

```text
ROLLBACK
```

Tudo volta ao estado anterior.

Mas imagine:

```text
Serviço Pedido
↓

Banco A

Serviço Estoque
↓

Banco B

Serviço Pagamento
↓

Banco C
```

Cada banco possui sua própria transação.

Não existe um único `COMMIT` que englobe todos.

Por isso surgem as transações distribuídas.

---


# Como resolver?

Existem duas abordagens clássicas.

---

# 1. Two-Phase Commit (2PC)

>Ela tenta fazer todos confirmarem ou todos cancelarem.
>
> Existe um componente que controla toda a operação chamado: `Coordenador`

---

## Fase 1 — Prepare

O coordenador pergunta para todos:`"Você consegue confirmar essa transação?"`

Cada participante executa tudo internamente, mas ainda **não faz commit**. `Todos ficam aguardando.`

---

## Fase 2 — Commit

Se todos responderem: `OK`

O `coordenador` envia: `COMMIT` para todos.

Agora todos gravam definitivamente.
 

### E se um deles responder "não" é ROLLBACK para todos.

Assim ninguém confirma.

---

### TRADE-OFF

> "Forte acoplamento, bloqueios e dependência do coordenador."
* Todos precisam participar da mesma transação isso reduz a independência dos microsserviços.
* Enquanto aguardam a decisão final, os participantes mantêm recursos bloqueados.
* Se o coordenador cai, serviços ficam esperando
---


Por isso, costuma ser evitado em arquiteturas modernas.

---

# A alternativa: Saga

> "Em microsserviços, geralmente prefiro uma Saga."

* Saga é um padrão para manter consistência **sem uma transação global**.

* Cada serviço executa apenas sua **transação local**.



#### E se algo der errado?

A solução é executar **ações compensatórias**.
* Ela desfaz logicamente o efeito anterior

Perceba que não é um `ROLLBACK` do banco. É uma **nova transação**, que altera o estado para refletir o cancelamento.

---

# Como implementar Saga no Spring?

Há duas formas principais.

## 1. Coreografia (Choreography)
 

| Categoria        | Itens                                                                                         |
| ---------------- | --------------------------------------------------------------------------------------------- |
| **Definição**    | • Não existe coordenador central.<br>• Cada serviço publica eventos e reage aos eventos recebidos.<br>• Cada serviço conhece apenas os eventos relevantes para ele.  |
| **Vantagens**    | • Baixo acoplamento.<br>• Fácil escalar.<br>• Sem ponto único de falha.                       |
| **Desvantagens** | • Fluxos complexos ficam difíceis de acompanhar.<br>• Debug e observabilidade exigem atenção. |


---

## 2. Orquestração (Orchestration)
| Aspecto          | Descrição                                                                                            |
| ---------------- | ---------------------------------------------------------------------------------------------------- |
| **Definição**    | Existe um **orquestrador** que decide a sequência das etapas e coordena a execução do processo.      |
| **Vantagens**    | • Fluxo centralizado.<br>• Mais simples de monitorar.<br>• Melhor para processos longos e complexos. |
| **Desvantagens** | • Introduz um componente central.<br>• Pode aumentar o acoplamento ao orquestrador.                  |

---

# Relação com Spring Boot

Em aplicações Spring Boot é comum utilizar:

* **`@Transactional`** para garantir atomicidade **dentro de um único banco de dados**.
* **Spring for Apache Kafka** para troca de eventos entre microsserviços.
* **Transactional Outbox Pattern** para publicar eventos de forma confiável após uma transação local.
* **Consumers idempotentes** para lidar com reentregas de mensagens.
* **Saga** (por coreografia ou orquestração) para coordenar transações distribuídas.
* Ferramentas como **Temporal**, **Camunda**, **Orkes Conductor** ou **Axon Framework** quando há necessidade de orquestrar fluxos complexos.

---

# Resumo para entrevistas

* Uma transação distribuída envolve alterações em múltiplos recursos independentes, como diferentes bancos de dados ou microsserviços.
* O **Two-Phase Commit (2PC)** busca garantir que todos confirmem ou todos cancelem uma transação, mas introduz forte acoplamento, bloqueios e dependência de um coordenador.
* Em arquiteturas de microsserviços, a abordagem mais comum é o padrão **Saga**, no qual cada serviço executa sua própria transação local.
* Quando ocorre uma falha, **não há rollback global**; em vez disso, são executadas **transações compensatórias** que desfazem logicamente os efeitos das etapas anteriores.
* No ecossistema Spring, é comum combinar **`@Transactional`**, **Kafka**, **Outbox Pattern**, **idempotência** e **Saga** para obter consistência eventual entre serviços distribuídos.
