
# Manifesto Ágil — Revisão em Tabelas

## 1. Ideia central

| Ponto                 | Resumo                                                         |
| --------------------- | -------------------------------------------------------------- |
| O que é               | Base filosófica dos métodos ágeis                              |
| O que não é           | Não é Scrum, Kanban, XP ou uma metodologia fechada             |
| Foco principal        | Entregar valor cedo, receber feedback e adaptar                |
| Aplicação em software | Desenvolver de forma incremental, colaborativa e com qualidade |
| Mentalidade           | Menos burocracia, mais aprendizado e entrega real              |

---

## 2. Por que o Manifesto Ágil surgiu

| Problema tradicional          | Resposta ágil                       |
| ----------------------------- | ----------------------------------- |
| Planejamento muito rígido     | Planejamento adaptativo             |
| Cliente distante              | Colaboração frequente               |
| Documentação excessiva        | Software funcionando como evidência |
| Mudanças vistas como problema | Mudanças tratadas como aprendizado  |
| Processo pesado               | Times mais autônomos                |
| Entrega só no final           | Entregas pequenas e frequentes      |

---

# 3. Os 4 valores do Manifesto Ágil

## Valor 1 — Indivíduos e interações acima de processos e ferramentas

| Aspecto               | Explicação                                                                |
| --------------------- | ------------------------------------------------------------------------- |
| Ideia principal       | Pessoas conversando bem geram mais valor do que apenas seguir ferramentas |
| Não significa         | Abandonar processo ou ferramenta                                          |
| Significa             | Priorizar comunicação, colaboração e entendimento                         |
| Exemplo em Java       | Dev, QA, PO e arquitetura discutem a regra antes da implementação         |
| Risco quando ignorado | O time segue o Jira, mas entrega algo errado                              |

---

## Valor 2 — Software funcionando acima de documentação abrangente

| Aspecto               | Explicação                                                       |
| --------------------- | ---------------------------------------------------------------- |
| Ideia principal       | Progresso real é software executável, testável e validável       |
| Não significa         | Não documentar                                                   |
| Significa             | Documentar o necessário e entregar algo funcionando              |
| Exemplo em Java       | Endpoint Spring Boot com regra implementada, testes e validações |
| Risco quando ignorado | Muita especificação, pouca entrega real                          |

---

## Valor 3 — Colaboração com o cliente acima de negociação de contratos

| Aspecto               | Explicação                                                                   |
| --------------------- | ---------------------------------------------------------------------------- |
| Ideia principal       | Cliente e time técnico devem colaborar durante o desenvolvimento             |
| Não significa         | Ignorar contrato                                                             |
| Significa             | Ajustar entendimento conforme o produto evolui                               |
| Exemplo em Java       | Refinar regras de pagamento, cancelamento, estorno e auditoria com o negócio |
| Risco quando ignorado | Entregar exatamente o combinado, mas sem resolver o problema real            |

---

## Valor 4 — Responder a mudanças acima de seguir um plano

| Aspecto               | Explicação                                                           |
| --------------------- | -------------------------------------------------------------------- |
| Ideia principal       | Mudanças são naturais em produtos de software                        |
| Não significa         | Trabalhar sem planejamento                                           |
| Significa             | Planejar, mas adaptar quando surgirem novas informações              |
| Exemplo em Java       | Alterar arquitetura ou backlog após descobrir gargalo de performance |
| Risco quando ignorado | Continuar seguindo um plano que já não gera valor                    |

---

# 4. Os 12 princípios agrupados

## Grupo 1 — Entrega de valor

| Princípio            | Resumo                              |
| -------------------- | ----------------------------------- |
| Satisfazer o cliente | Entregar valor cedo e continuamente |
| Entregas frequentes  | Liberar software em ciclos curtos   |
| Software funcionando | Principal medida de progresso       |

**Aplicação prática:**
Uma funcionalidade só está realmente avançada quando pode ser executada, testada e validada.

---

## Grupo 2 — Adaptação

| Princípio        | Resumo                                         |
| ---------------- | ---------------------------------------------- |
| Aceitar mudanças | Mudanças podem gerar vantagem competitiva      |
| Ajustar o plano  | O plano deve acompanhar o aprendizado          |
| Reduzir rigidez  | Arquitetura e processo devem permitir evolução |

**Aplicação prática:**
Usar baixo acoplamento, testes automatizados e interfaces bem definidas ajuda a mudar o sistema com menor risco.

---

## Grupo 3 — Colaboração

| Princípio                        | Resumo                                                 |
| -------------------------------- | ------------------------------------------------------ |
| Negócio e desenvolvimento juntos | Produto e tecnologia precisam conversar frequentemente |
| Comunicação direta               | Conversas claras reduzem ambiguidade                   |
| Alinhamento constante            | Evita retrabalho e entendimento incorreto              |

**Aplicação prática:**
Antes de codificar uma regra complexa, o time transforma a regra em exemplos e critérios de aceite.

---

## Grupo 4 — Times e autonomia

| Princípio         | Resumo                                          |
| ----------------- | ----------------------------------------------- |
| Pessoas motivadas | Times precisam de ambiente adequado e confiança |
| Auto-organização  | O time decide como executar o trabalho          |
| Responsabilidade  | Autonomia exige maturidade técnica              |

**Aplicação prática:**
Um time sênior decide como testar, refatorar, versionar APIs, lidar com débitos técnicos e melhorar arquitetura.

---

## Grupo 5 — Qualidade técnica

| Princípio          | Resumo                              |
| ------------------ | ----------------------------------- |
| Excelência técnica | Código bom facilita mudança         |
| Bom design         | Arquitetura limpa aumenta agilidade |
| Simplicidade       | Evitar complexidade desnecessária   |

**Aplicação prática:**
Agilidade sem qualidade vira pressa. Pressa gera bugs, retrabalho e dívida técnica.

---

## Grupo 6 — Ritmo sustentável e melhoria contínua

| Princípio            | Resumo                                                          |
| -------------------- | --------------------------------------------------------------- |
| Ritmo sustentável    | O time deve conseguir manter produtividade sem desgaste extremo |
| Inspeção e adaptação | O time revisa como trabalha                                     |
| Melhoria contínua    | Ajustes frequentes melhoram entrega e processo                  |

**Aplicação prática:**
Retrospectivas devem gerar ações reais: melhorar testes, reduzir histórias grandes, automatizar deploy ou revisar refinamento.

---

# 5. Manifesto Ágil aplicado a Java/Spring Boot

| Situação                  | Aplicação ágil                                                |
| ------------------------- | ------------------------------------------------------------- |
| Criar API REST            | Entregar versão pequena, testável e evolutiva                 |
| Regra de negócio complexa | Conversar com negócio antes de codificar                      |
| Mudança de requisito      | Avaliar impacto, valor e risco                                |
| Arquitetura               | Evitar acoplamento desnecessário                              |
| Testes                    | Garantir segurança para mudar                                 |
| Deploy                    | Automatizar para reduzir risco                                |
| Observabilidade           | Logs, métricas e health checks fazem parte da entrega         |
| Documentação              | Registrar decisões relevantes, não tudo de forma burocrática  |
| Time                      | Desenvolvedores participam das decisões técnicas e de produto |

---

# 6. O que memorizar

| Conceito             | Resumo curto                                                                    |
| -------------------- | ------------------------------------------------------------------------------- |
| Manifesto Ágil       | Valores e princípios para desenvolver software com adaptação e entrega de valor |
| Agilidade            | Capacidade de aprender, adaptar e entregar continuamente                        |
| Scrum                | Um framework que pode aplicar princípios ágeis                                  |
| Software funcionando | Principal evidência de progresso                                                |
| Mudança              | Parte natural do desenvolvimento de software                                    |
| Simplicidade         | Fazer o necessário, com qualidade, sem excesso                                  |
| Qualidade técnica    | Base para conseguir mudar rápido                                                |
| Colaboração          | Reduz erro de entendimento e retrabalho                                         |

---

# 7. Não confundir

| Ideia errada                           | Correção                                                  |
| -------------------------------------- | --------------------------------------------------------- |
| Ágil é não planejar                    | Ágil é planejar de forma adaptativa                       |
| Ágil é não documentar                  | Ágil é documentar o necessário                            |
| Ágil é fazer rápido de qualquer jeito  | Ágil exige qualidade técnica                              |
| Scrum é o mesmo que ágil               | Scrum é apenas um framework ágil                          |
| Mudança sempre deve ser aceita         | Mudança deve ser analisada por valor, custo e risco       |
| Software funcionando dispensa conversa | Software funcionando precisa de alinhamento com o negócio |
| Autonomia é ausência de liderança      | Autonomia exige responsabilidade e direção clara          |

---

# 8. Perguntas e respostas

## 1. O que é o Manifesto Ágil?

| Pergunta                  | Resposta                                                                                                                                        |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| O que é o Manifesto Ágil? | É um conjunto de valores e princípios que orienta o desenvolvimento de software de forma colaborativa, adaptativa e focada em entrega de valor. |

---

## 2. O Manifesto Ágil é uma metodologia?

| Pergunta                            | Resposta                                                                                                                      |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| O Manifesto Ágil é uma metodologia? | Não. Ele é uma base de valores e princípios. Scrum, Kanban e XP são métodos ou frameworks que podem aplicar esses princípios. |

---

## 3. Qual a diferença entre ser ágil e usar Scrum?

| Pergunta                                      | Resposta                                                                                                                                                                |
| --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Qual a diferença entre ser ágil e usar Scrum? | Ser ágil é seguir uma mentalidade de entrega, adaptação, colaboração e melhoria contínua. Usar Scrum é aplicar um framework específico com papéis, eventos e artefatos. |

---

## 4. O Manifesto Ágil rejeita documentação?

| Pergunta                               | Resposta                                                                                                                                             |
| -------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| O Manifesto Ágil rejeita documentação? | Não. Ele apenas valoriza mais software funcionando do que documentação extensa. A documentação deve existir quando for útil, objetiva e sustentável. |

---

## 5. Por que software funcionando é mais importante que documentação abrangente?

| Pergunta                                        | Resposta                                                                                                                        |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Por que software funcionando é mais importante? | Porque software funcionando permite validar valor real, testar hipóteses, receber feedback e medir progresso de forma concreta. |

---

## 6. O que significa responder a mudanças?

| Pergunta                              | Resposta                                                                                                                                         |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| O que significa responder a mudanças? | Significa adaptar plano, backlog, arquitetura ou solução quando novas informações mostram que outro caminho gera mais valor ou reduz mais risco. |

---

## 7. Mudança deve sempre ser aceita?

| Pergunta                        | Resposta                                                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| Mudança deve sempre ser aceita? | Não. Mudanças devem ser avaliadas considerando valor de negócio, custo técnico, prazo, risco e impacto no produto. |

---

## 8. Como qualidade técnica ajuda na agilidade?

| Pergunta                      | Resposta                                                                                                                               |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Como qualidade técnica ajuda? | Código bem testado, simples e bem estruturado permite mudanças mais rápidas e seguras. Sem qualidade, cada mudança se torna arriscada. |

---

## 9. Como testes automatizados apoiam o Manifesto Ágil?

| Pergunta                          | Resposta                                                                                                                                     |
| --------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Como testes automatizados ajudam? | Eles dão segurança para alterar código, reduzem regressões, aceleram feedback e ajudam o time a entregar software funcionando com qualidade. |

---

## 10. O que significa simplicidade no contexto ágil?

| Pergunta              | Resposta                                                                                                                                             |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| O que é simplicidade? | É fazer o necessário para entregar valor, evitando complexidade, arquitetura excessiva, documentação inútil e funcionalidades sem necessidade clara. |

---

## 11. Qual o papel do cliente ou área de negócio?

| Pergunta                 | Resposta                                                                                              |
| ------------------------ | ----------------------------------------------------------------------------------------------------- |
| Qual o papel do cliente? | Colaborar com o time, esclarecer regras, validar entregas e ajudar a priorizar o que gera mais valor. |

---

## 12. Como aplicar isso como Java Sênior?

| Pergunta                       | Resposta                                                                                                                                                                             |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Como aplicar como Java Sênior? | Entregando software pequeno e validável, escrevendo testes, protegendo regras de domínio, reduzindo acoplamento, colaborando com negócio e ajudando o time a melhorar continuamente. |




Lucas, here is the **English version**, keeping the same concise review-table format.

# Agile Manifesto — Review Tables

## 1. Core Idea

| Point                | Summary                                                  |
| -------------------- | -------------------------------------------------------- |
| What it is           | The philosophical foundation of Agile methods            |
| What it is not       | It is not Scrum, Kanban, XP, or a closed methodology     |
| Main focus           | Deliver value early, receive feedback, and adapt         |
| Software application | Develop incrementally, collaboratively, and with quality |
| Mindset              | Less bureaucracy, more learning and real delivery        |

---

## 2. Why the Agile Manifesto Emerged

| Traditional Problem            | Agile Response                            |
| ------------------------------ | ----------------------------------------- |
| Rigid planning                 | Adaptive planning                         |
| Customer distant from the team | Frequent collaboration                    |
| Excessive documentation        | Working software as evidence of progress  |
| Changes seen as problems       | Changes treated as learning opportunities |
| Heavy processes                | More autonomous teams                     |
| Delivery only at the end       | Small and frequent deliveries             |

---

# 3. The 4 Values of the Agile Manifesto

## Value 1 — Individuals and interactions over processes and tools

| Aspect            | Explanation                                                                      |
| ----------------- | -------------------------------------------------------------------------------- |
| Main idea         | People communicating well generate more value than simply following tools        |
| It does not mean  | Abandoning processes or tools                                                    |
| It means          | Prioritizing communication, collaboration, and shared understanding              |
| Java example      | Developer, QA, PO, and architect discuss the business rule before implementation |
| Risk when ignored | The team follows Jira correctly but delivers the wrong solution                  |

---

## Value 2 — Working software over comprehensive documentation

| Aspect            | Explanation                                                                  |
| ----------------- | ---------------------------------------------------------------------------- |
| Main idea         | Real progress is executable, testable, and validatable software              |
| It does not mean  | Not documenting anything                                                     |
| It means          | Documenting what is necessary and delivering something that works            |
| Java example      | A Spring Boot endpoint with business logic, validations, and automated tests |
| Risk when ignored | Too much specification and little real delivery                              |

---

## Value 3 — Customer collaboration over contract negotiation

| Aspect            | Explanation                                                                    |
| ----------------- | ------------------------------------------------------------------------------ |
| Main idea         | The customer and the technical team should collaborate throughout development  |
| It does not mean  | Ignoring contracts                                                             |
| It means          | Adjusting understanding as the product evolves                                 |
| Java example      | Refining payment, cancellation, refund, and audit rules with the business team |
| Risk when ignored | Delivering exactly what was agreed, but not solving the real problem           |

---

## Value 4 — Responding to change over following a plan

| Aspect            | Explanation                                                                 |
| ----------------- | --------------------------------------------------------------------------- |
| Main idea         | Changes are natural in software products                                    |
| It does not mean  | Working without planning                                                    |
| It means          | Planning, but adapting when new information appears                         |
| Java example      | Changing architecture or backlog after discovering a performance bottleneck |
| Risk when ignored | Continuing to follow a plan that no longer creates value                    |

---

# 4. The 12 Principles Grouped by Theme

## Group 1 — Value Delivery

| Principle            | Summary                              |
| -------------------- | ------------------------------------ |
| Satisfy the customer | Deliver value early and continuously |
| Frequent deliveries  | Release software in short cycles     |
| Working software     | The main measure of progress         |

**Practical application:**
A feature is only truly progressing when it can be executed, tested, and validated.

---

## Group 2 — Adaptation

| Principle       | Summary                                         |
| --------------- | ----------------------------------------------- |
| Welcome change  | Changes can create competitive advantage        |
| Adjust the plan | The plan should evolve with learning            |
| Reduce rigidity | Architecture and process should allow evolution |

**Practical application:**
Low coupling, automated tests, and well-defined interfaces help the system change with less risk.

---

## Group 3 — Collaboration

| Principle                         | Summary                                            |
| --------------------------------- | -------------------------------------------------- |
| Business and development together | Product and technology need frequent communication |
| Direct communication              | Clear conversations reduce ambiguity               |
| Continuous alignment              | Avoids rework and misunderstanding                 |

**Practical application:**
Before coding a complex rule, the team turns the rule into examples and acceptance criteria.

---

## Group 4 — Teams and Autonomy

| Principle         | Summary                                    |
| ----------------- | ------------------------------------------ |
| Motivated people  | Teams need the right environment and trust |
| Self-organization | The team decides how to execute the work   |
| Responsibility    | Autonomy requires technical maturity       |

**Practical application:**
A senior team decides how to test, refactor, version APIs, handle technical debt, and improve architecture.

---

## Group 5 — Technical Quality

| Principle            | Summary                              |
| -------------------- | ------------------------------------ |
| Technical excellence | Good code makes change easier        |
| Good design          | Clean architecture increases agility |
| Simplicity           | Avoid unnecessary complexity         |

**Practical application:**
Agility without quality becomes haste. Haste creates bugs, rework, and technical debt.

---

## Group 6 — Sustainable Pace and Continuous Improvement

| Principle                 | Summary                                                       |
| ------------------------- | ------------------------------------------------------------- |
| Sustainable pace          | The team should maintain productivity without extreme burnout |
| Inspection and adaptation | The team reviews how it works                                 |
| Continuous improvement    | Frequent adjustments improve delivery and process             |

**Practical application:**
Retrospectives should generate real actions: improving tests, reducing large stories, automating deployment, or improving refinement.

---

# 5. Agile Manifesto Applied to Java/Spring Boot

| Situation             | Agile Application                                          |
| --------------------- | ---------------------------------------------------------- |
| Creating a REST API   | Deliver a small, testable, and evolvable version           |
| Complex business rule | Talk to the business before coding                         |
| Requirement change    | Evaluate impact, value, and risk                           |
| Architecture          | Avoid unnecessary coupling                                 |
| Tests                 | Provide safety for change                                  |
| Deployment            | Automate to reduce risk                                    |
| Observability         | Logs, metrics, and health checks are part of delivery      |
| Documentation         | Record relevant decisions, not everything bureaucratically |
| Team                  | Developers participate in technical and product decisions  |

---

# 6. What to Memorize

| Concept           | Short Summary                                                                    |
| ----------------- | -------------------------------------------------------------------------------- |
| Agile Manifesto   | Values and principles for developing software with adaptation and value delivery |
| Agility           | Ability to learn, adapt, and deliver continuously                                |
| Scrum             | A framework that can apply Agile principles                                      |
| Working software  | The main evidence of progress                                                    |
| Change            | A natural part of software development                                           |
| Simplicity        | Doing what is necessary, with quality, without excess                            |
| Technical quality | The foundation for changing fast                                                 |
| Collaboration     | Reduces misunderstanding and rework                                              |

---

# 7. Do Not Confuse

| Wrong Idea                                         | Correction                                           |
| -------------------------------------------------- | ---------------------------------------------------- |
| Agile means not planning                           | Agile means adaptive planning                        |
| Agile means not documenting                        | Agile means documenting what is necessary            |
| Agile means doing things fast at any cost          | Agile requires technical quality                     |
| Scrum is the same as Agile                         | Scrum is only one Agile framework                    |
| Every change must be accepted                      | Change must be evaluated by value, cost, and risk    |
| Working software removes the need for conversation | Working software still needs business alignment      |
| Autonomy means absence of leadership               | Autonomy requires responsibility and clear direction |

---

# 8. Questions and Answers

## 1. What is the Agile Manifesto?

| Question                     | Answer                                                                                                                     |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| What is the Agile Manifesto? | It is a set of values and principles that guides software development in a collaborative, adaptive, and value-focused way. |

---

## 2. Is the Agile Manifesto a methodology?

| Question                              | Answer                                                                                                                            |
| ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| Is the Agile Manifesto a methodology? | No. It is a foundation of values and principles. Scrum, Kanban, and XP are methods or frameworks that can apply these principles. |

---

## 3. What is the difference between being Agile and using Scrum?

| Question                                                    | Answer                                                                                                                                                                                       |
| ----------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| What is the difference between being Agile and using Scrum? | Being Agile means following a mindset of delivery, adaptation, collaboration, and continuous improvement. Using Scrum means applying a specific framework with roles, events, and artifacts. |

---

## 4. Does the Agile Manifesto reject documentation?

| Question                                       | Answer                                                                                                                                      |
| ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| Does the Agile Manifesto reject documentation? | No. It values working software more than extensive documentation. Documentation should exist when it is useful, objective, and sustainable. |

---

## 5. Why is working software more important than comprehensive documentation?

| Question                                | Answer                                                                                                                                |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| Why is working software more important? | Because working software allows the team to validate real value, test assumptions, receive feedback, and measure progress concretely. |

---

## 6. What does responding to change mean?

| Question                             | Answer                                                                                                                                               |
| ------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| What does responding to change mean? | It means adapting the plan, backlog, architecture, or solution when new information shows that another path creates more value or reduces more risk. |

---

## 7. Should every change be accepted?

| Question                         | Answer                                                                                                          |
| -------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| Should every change be accepted? | No. Changes should be evaluated considering business value, technical cost, deadline, risk, and product impact. |

---

## 8. How does technical quality help agility?

| Question                         | Answer                                                                                                                      |
| -------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| How does technical quality help? | Well-tested, simple, and well-structured code allows faster and safer changes. Without quality, every change becomes risky. |

---

## 9. How do automated tests support the Agile Manifesto?

| Question                     | Answer                                                                                                                                |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| How do automated tests help? | They provide safety for code changes, reduce regressions, speed up feedback, and help the team deliver working software with quality. |

---

## 10. What does simplicity mean in Agile?

| Question            | Answer                                                                                                                                                   |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| What is simplicity? | It means doing what is necessary to deliver value, avoiding unnecessary complexity, excessive architecture, useless documentation, and unclear features. |

---

## 11. What is the role of the customer or business area?

| Question                     | Answer                                                                                                             |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| What is the customer’s role? | To collaborate with the team, clarify rules, validate deliveries, and help prioritize what creates the most value. |

---

## 12. How can a Java Senior apply this?

| Question                          | Answer                                                                                                                                                                                 |
| --------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| How can a Java Senior apply this? | By delivering small and validatable software, writing tests, protecting business rules, reducing coupling, collaborating with the business, and helping the team improve continuously. |

