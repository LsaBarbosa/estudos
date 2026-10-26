Lucas, segue um resumo em **estilo mapa mental com tabelas**, em **português e inglês**, sobre **DRY, KISS e YAGNI**.

# Versão em Português

## Mapa mental geral

| Princípio | Significado              | Ideia principal                               | Objetivo                                     |
| --------- | ------------------------ | --------------------------------------------- | -------------------------------------------- |
| **DRY**   | Don’t Repeat Yourself    | Não duplique conhecimento/regra de negócio    | Reduzir inconsistência e retrabalho          |
| **KISS**  | Keep It Simple, Stupid   | Mantenha a solução simples                    | Evitar complexidade desnecessária            |
| **YAGNI** | You Aren’t Gonna Need It | Não implemente algo antes da necessidade real | Evitar código inútil e antecipação excessiva |

---

# 1. DRY — Don’t Repeat Yourself

## Conceito

| Aspecto                | Resumo                                                                   |
| ---------------------- | ------------------------------------------------------------------------ |
| **Ideia**              | Cada regra ou conhecimento deve existir em apenas um lugar confiável.    |
| **Foco**               | Evitar duplicação de lógica, regra de negócio, validação e configuração. |
| **Problema que evita** | Alterar uma regra em um lugar e esquecer outro.                          |
| **Risco**              | Aplicar DRY cedo demais pode gerar abstrações ruins.                     |

## Exemplo ruim

```java
public class PaymentService {

    public void payWithPix(BigDecimal amount) {
        if (amount.compareTo(BigDecimal.ZERO) <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }

        // pagar com Pix
    }

    public void payWithCard(BigDecimal amount) {
        if (amount.compareTo(BigDecimal.ZERO) <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }

        // pagar com cartão
    }
}
```

## Exemplo melhor

```java
public class PaymentService {

    public void payWithPix(BigDecimal amount) {
        validatePositiveAmount(amount);
        // pagar com Pix
    }

    public void payWithCard(BigDecimal amount) {
        validatePositiveAmount(amount);
        // pagar com cartão
    }

    private void validatePositiveAmount(BigDecimal amount) {
        if (amount.compareTo(BigDecimal.ZERO) <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }
    }
}
```

## Atenção importante

| Duplicação                                 | Deve remover?  | Motivo                             |
| ------------------------------------------ | -------------- | ---------------------------------- |
| Mesma regra de negócio repetida            | Sim            | Pode gerar inconsistência          |
| Mesmo trecho técnico repetido muitas vezes | Geralmente sim | Pode virar método/utilitário       |
| Código parecido, mas com regras diferentes | Cuidado        | Pode gerar abstração errada        |
| Código repetido só uma vez                 | Nem sempre     | Pode ser cedo demais para abstrair |

---

# 2. KISS — Keep It Simple, Stupid

## Conceito

| Aspecto                | Resumo                                                           |
| ---------------------- | ---------------------------------------------------------------- |
| **Ideia**              | Prefira a solução mais simples que resolva bem o problema atual. |
| **Foco**               | Clareza, manutenção e baixo custo cognitivo.                     |
| **Problema que evita** | Overengineering, excesso de padrões e arquitetura desnecessária. |
| **Risco**              | Confundir simplicidade com código pobre ou sem design.           |

## Exemplo ruim

```java
public interface AmountValidationStrategy {
    boolean isValid(BigDecimal amount);
}

public class PositiveAmountValidationStrategy implements AmountValidationStrategy {
    public boolean isValid(BigDecimal amount) {
        return amount.compareTo(BigDecimal.ZERO) > 0;
    }
}
```

Para uma validação simples, isso pode ser exagerado.

## Exemplo melhor

```java
public void validateAmount(BigDecimal amount) {
    if (amount.compareTo(BigDecimal.ZERO) <= 0) {
        throw new IllegalArgumentException("Amount must be positive");
    }
}
```

## Como aplicar KISS

| Situação               | Decisão simples                                |
| ---------------------- | ---------------------------------------------- |
| Uma regra simples      | Método direto                                  |
| Poucas variações       | `if` claro pode ser suficiente                 |
| Muitas variações reais | Strategy pode fazer sentido                    |
| API pequena            | Estrutura simples de pacotes                   |
| Sistema complexo       | Simplicidade na separação de responsabilidades |

---

# 3. YAGNI — You Aren’t Gonna Need It

## Conceito

| Aspecto                | Resumo                                                                           |
| ---------------------- | -------------------------------------------------------------------------------- |
| **Ideia**              | Não implemente funcionalidades “talvez úteis” antes de existir necessidade real. |
| **Foco**               | Entregar o necessário agora.                                                     |
| **Problema que evita** | Código morto, complexidade futura imaginária e desperdício de tempo.             |
| **Risco**              | Confundir YAGNI com falta de planejamento.                                       |

## Exemplo ruim

```java
public class PaymentService {

    public void pay(Payment payment) {
        // pagamento atual
    }

    public void payWithCrypto(Payment payment) {
        // talvez um dia precise
    }

    public void payWithInternationalGateway(Payment payment) {
        // talvez no futuro
    }

    public void scheduleRecurringPayment(Payment payment) {
        // ainda não existe requisito
    }
}
```

## Exemplo melhor

```java
public class PaymentService {

    public void pay(Payment payment) {
        // regra necessária agora
    }
}
```

## Quando aplicar YAGNI

| Situação                                    | Melhor decisão                                            |
| ------------------------------------------- | --------------------------------------------------------- |
| “Talvez o cliente peça isso”                | Não implementar agora                                     |
| “No futuro podemos ter outro gateway”       | Criar design simples, sem implementar gateway inexistente |
| “Pode ser que precise escalar para milhões” | Medir antes de otimizar                                   |
| “Vamos deixar tudo genérico”                | Evitar generalização prematura                            |
| “Existe requisito claro”                    | Implementar                                               |

---

# Comparação rápida

| Critério               | DRY                           | KISS                                    | YAGNI                              |
| ---------------------- | ----------------------------- | --------------------------------------- | ---------------------------------- |
| **Combate**            | Duplicação                    | Complexidade                            | Antecipação                        |
| **Pergunta principal** | Estou repetindo conhecimento? | Dá para resolver de forma mais simples? | Isso é realmente necessário agora? |
| **Excesso comum**      | Abstração prematura           | Simplificação pobre                     | Falta de preparação mínima         |
| **Boa aplicação**      | Centralizar regras repetidas  | Escolher design claro                   | Implementar só o necessário        |
| **Risco se ignorar**   | Inconsistência                | Código difícil                          | Código inútil                      |

---

# Relação entre eles

| Combinação             | Explicação                                                                    |
| ---------------------- | ----------------------------------------------------------------------------- |
| **DRY + KISS**         | Remova duplicação sem criar abstrações complexas demais.                      |
| **KISS + YAGNI**       | Resolva o problema atual com a menor complexidade viável.                     |
| **DRY + YAGNI**        | Não crie abstração antes de existir duplicação real.                          |
| **DRY + KISS + YAGNI** | Código simples, sem repetição relevante e sem funcionalidades desnecessárias. |

---

# Aplicação em Java/Spring

| Camada               | Aplicação prática                                                                 |
| -------------------- | --------------------------------------------------------------------------------- |
| **Controller**       | Não repetir validações de entrada; manter endpoints simples.                      |
| **Service/Use Case** | Evitar lógica duplicada; não antecipar fluxos inexistentes.                       |
| **Domain**           | Centralizar regras de negócio importantes.                                        |
| **Repository**       | Evitar queries repetidas; não criar métodos genéricos demais sem uso.             |
| **DTO**              | Criar apenas os DTOs necessários para entrada e saída reais.                      |
| **Mapper**           | Usar mapper quando conversões se repetem; evitar mapper complexo sem necessidade. |
| **Configuração**     | Não duplicar propriedades; não criar configurações futuras sem uso.               |

---

# Erros comuns

| Erro                                         | Princípio afetado | Correção                       |
| -------------------------------------------- | ----------------- | ------------------------------ |
| Copiar e colar regra de negócio              | DRY               | Centralizar regra              |
| Criar Strategy para uma única variação       | KISS/YAGNI        | Usar método simples            |
| Criar campos “para uso futuro”               | YAGNI             | Remover até existir requisito  |
| Criar abstração antes da repetição real      | DRY mal aplicado  | Esperar padrão real aparecer   |
| Criar arquitetura complexa para CRUD simples | KISS              | Reduzir camadas desnecessárias |
| Otimizar performance sem medir               | YAGNI/KISS        | Medir primeiro                 |

---

# Perguntas de revisão

| Pergunta                                      | Resposta curta                                               |
| --------------------------------------------- | ------------------------------------------------------------ |
| O que significa DRY?                          | Não repetir conhecimento ou regra de negócio.                |
| O que significa KISS?                         | Manter a solução simples e compreensível.                    |
| O que significa YAGNI?                        | Não implementar algo antes da necessidade real.              |
| DRY significa nunca repetir código?           | Não. Significa evitar duplicação relevante de conhecimento.  |
| KISS significa código sem arquitetura?        | Não. Significa arquitetura simples para o problema.          |
| YAGNI significa não planejar?                 | Não. Significa não implementar o que ainda não é necessário. |
| Qual princípio evita overengineering?         | KISS e YAGNI.                                                |
| Qual princípio evita inconsistência de regra? | DRY.                                                         |

---

# English Version

## General mind map

| Principle | Meaning                  | Main idea                                    | Goal                                      |
| --------- | ------------------------ | -------------------------------------------- | ----------------------------------------- |
| **DRY**   | Don’t Repeat Yourself    | Do not duplicate knowledge or business rules | Reduce inconsistency and rework           |
| **KISS**  | Keep It Simple, Stupid   | Keep the solution simple                     | Avoid unnecessary complexity              |
| **YAGNI** | You Aren’t Gonna Need It | Do not implement before there is a real need | Avoid useless code and premature features |

---

# 1. DRY — Don’t Repeat Yourself

| Aspect     | Summary                                                                |
| ---------- | ---------------------------------------------------------------------- |
| **Idea**   | Each rule or piece of knowledge should exist in one reliable place.    |
| **Focus**  | Avoid duplicated logic, business rules, validations and configuration. |
| **Avoids** | Updating a rule in one place and forgetting another.                   |
| **Risk**   | Applying DRY too early can create poor abstractions.                   |

```java
public class PaymentService {

    public void payWithPix(BigDecimal amount) {
        validatePositiveAmount(amount);
        // pay with Pix
    }

    public void payWithCard(BigDecimal amount) {
        validatePositiveAmount(amount);
        // pay with card
    }

    private void validatePositiveAmount(BigDecimal amount) {
        if (amount.compareTo(BigDecimal.ZERO) <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }
    }
}
```

---

# 2. KISS — Keep It Simple, Stupid

| Aspect     | Summary                                                                 |
| ---------- | ----------------------------------------------------------------------- |
| **Idea**   | Prefer the simplest solution that correctly solves the current problem. |
| **Focus**  | Clarity, maintainability and low cognitive cost.                        |
| **Avoids** | Overengineering, excessive patterns and unnecessary architecture.       |
| **Risk**   | Confusing simplicity with poor design.                                  |

```java
public void validateAmount(BigDecimal amount) {
    if (amount.compareTo(BigDecimal.ZERO) <= 0) {
        throw new IllegalArgumentException("Amount must be positive");
    }
}
```

| Situation            | Simple decision                  |
| -------------------- | -------------------------------- |
| One simple rule      | Direct method                    |
| Few variations       | Clear `if` may be enough         |
| Many real variations | Strategy may make sense          |
| Small API            | Simple package structure         |
| Complex system       | Simple responsibility separation |

---

# 3. YAGNI — You Aren’t Gonna Need It

| Aspect     | Summary                                                                  |
| ---------- | ------------------------------------------------------------------------ |
| **Idea**   | Do not build “maybe useful” features before there is a real requirement. |
| **Focus**  | Deliver only what is needed now.                                         |
| **Avoids** | Dead code, imagined future complexity and wasted effort.                 |
| **Risk**   | Confusing YAGNI with lack of planning.                                   |

```java
public class PaymentService {

    public void pay(Payment payment) {
        // current required behavior
    }
}
```

| Situation                                   | Better decision                                            |
| ------------------------------------------- | ---------------------------------------------------------- |
| “Maybe the client will ask for this”        | Do not implement now                                       |
| “In the future we may have another gateway” | Keep design simple, do not implement a nonexistent gateway |
| “It may need to scale to millions”          | Measure before optimizing                                  |
| “Let’s make everything generic”             | Avoid premature generalization                             |
| “There is a clear requirement”              | Implement                                                  |

---

# Quick comparison

| Criteria            | DRY                       | KISS                 | YAGNI                         |
| ------------------- | ------------------------- | -------------------- | ----------------------------- |
| **Fights**          | Duplication               | Complexity           | Premature implementation      |
| **Main question**   | Am I repeating knowledge? | Can this be simpler? | Is this needed now?           |
| **Common excess**   | Premature abstraction     | Poor simplification  | No minimal planning           |
| **Good use**        | Centralize repeated rules | Choose clear design  | Implement only what is needed |
| **Risk if ignored** | Inconsistency             | Hard codebase        | Useless code                  |

---

# Review questions

| Question                                   | Short answer                                              |
| ------------------------------------------ | --------------------------------------------------------- |
| What does DRY mean?                        | Do not repeat knowledge or business rules.                |
| What does KISS mean?                       | Keep the solution simple and understandable.              |
| What does YAGNI mean?                      | Do not implement before there is a real need.             |
| Does DRY mean never repeating code?        | No. It means avoiding relevant knowledge duplication.     |
| Does KISS mean no architecture?            | No. It means simple architecture for the problem.         |
| Does YAGNI mean no planning?               | No. It means not implementing unnecessary features early. |
| Which principle avoids overengineering?    | KISS and YAGNI.                                           |
| Which principle avoids rule inconsistency? | DRY.                                                      |
