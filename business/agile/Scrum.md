Lucas, a partir daqui vou puxar o estudo mais para **gestão de projetos, gestão de produto, liderança, facilitação e entrega de valor**, não apenas para back-end Java.

# Section 4 — Scrum

A seção 4 do curso **“Métodos ágeis de A a Z: o curso completo”** é dedicada a **Scrum**. Pela ementa pública da Udemy, ela possui **9 aulas**, cerca de **53 minutos**, e cobre: visão geral do framework, três pilares, papéis, user story, artefatos, eventos, entrevista com especialista, exercícios e referências. ([Udemy][1])

---

# 1. Resumo geral da seção

| Tópico da seção | O que você deve entender                                                                                                       |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| Scrum Overview  | O Scrum é um framework para lidar com problemas complexos usando ciclos curtos de planejamento, execução, inspeção e adaptação |
| Três pilares    | Transparência, inspeção e adaptação                                                                                            |
| Papéis          | Product Owner, Scrum Master e Developers                                                                                       |
| User Story      | Técnica para representar necessidade do usuário de forma simples e orientada a valor                                           |
| Artefatos       | Product Backlog, Sprint Backlog e Increment                                                                                    |
| Eventos         | Sprint, Sprint Planning, Daily Scrum, Sprint Review e Sprint Retrospective                                                     |
| Exercícios      | Fixação dos conceitos centrais                                                                                                 |
| Entrevista      | Visão prática de especialista sobre aplicação do Scrum                                                                         |

---

# 2. O que é Scrum

| Aspecto       | Resumo                                                                                               |
| ------------- | ---------------------------------------------------------------------------------------------------- |
| Definição     | Scrum é um framework leve para gerar valor por meio de soluções adaptativas para problemas complexos |
| Natureza      | Não é uma metodologia detalhada passo a passo                                                        |
| Funcionamento | O time trabalha em ciclos chamados Sprints                                                           |
| Objetivo      | Entregar incrementos de valor de forma frequente                                                     |
| Base          | Empirismo, aprendizado contínuo e melhoria progressiva                                               |
| Uso em gestão | Ajuda a organizar trabalho, priorizar valor, reduzir risco e melhorar previsibilidade                |

O Scrum Guide define Scrum como um framework leve que ajuda pessoas, times e organizações a gerar valor por meio de soluções adaptativas para problemas complexos. Ele também reforça que Scrum é propositalmente incompleto: define apenas as partes necessárias para aplicar sua teoria, não um manual detalhado de execução. ([Guias Scrum][2])

---

# 3. Scrum em visão de gestão de projetos

| Gestão tradicional            | Scrum                                            |
| ----------------------------- | ------------------------------------------------ |
| Planejamento grande no início | Planejamento contínuo por Sprint                 |
| Escopo tende a ser fixo       | Escopo pode ser adaptado conforme aprendizado    |
| Entrega concentrada no final  | Entregas incrementais                            |
| Mudança vista como problema   | Mudança tratada como informação                  |
| Controle por cronograma       | Controle por valor entregue e inspeção frequente |
| Gerente coordena tarefas      | Time se auto-organiza dentro de objetivos claros |
| Ênfase em seguir plano        | Ênfase em entregar valor e adaptar               |

**Ponto importante para sua migração para gestão:**
No Scrum, o foco do gestor não deve ser “mandar tarefas”. O foco passa a ser **criar clareza, remover impedimentos, facilitar decisões, alinhar stakeholders, proteger foco e medir entrega de valor**.

---

# 4. Os 3 pilares do Scrum

Scrum é baseado em **empirismo** e **lean thinking**. O empirismo considera que o conhecimento vem da experiência e de decisões baseadas no que é observado. O Scrum usa uma abordagem iterativa e incremental para melhorar previsibilidade e controlar risco. ([Guias Scrum][2])

| Pilar         | Significado                                                               | Aplicação na gestão                                             |
| ------------- | ------------------------------------------------------------------------- | --------------------------------------------------------------- |
| Transparência | O trabalho, os problemas e o progresso precisam estar visíveis            | Backlog claro, metas claras, riscos expostos, status real       |
| Inspeção      | O time verifica frequentemente produto, processo e progresso              | Review, Daily, métricas, acompanhamento de impedimentos         |
| Adaptação     | O time ajusta plano, escopo, processo ou produto quando aprende algo novo | Repriorizar backlog, corrigir rota, mudar estratégia de entrega |

---

# 5. Papéis no Scrum

O Scrum Guide define que o Scrum Team é composto por **Product Owner**, **Scrum Master** e **Developers**. Não há sub-times ou hierarquias dentro do Scrum Team; ele é uma unidade focada em um objetivo por vez. ([Guias Scrum][2])

## 5.1 Product Owner

| Responsabilidade          | Explicação prática                                                        |
| ------------------------- | ------------------------------------------------------------------------- |
| Maximizar valor           | Decide o que gera mais valor para o produto                               |
| Gerenciar Product Backlog | Mantém o backlog claro, ordenado e visível                                |
| Comunicar Product Goal    | Dá direção de médio/longo prazo ao time                                   |
| Priorizar                 | Ordena itens considerando valor, risco, dependência e estratégia          |
| Representar stakeholders  | Consolida necessidades de clientes, negócio, operação, suporte e usuários |

O Product Owner é responsável por maximizar o valor do produto e por gerenciar efetivamente o Product Backlog, incluindo comunicar o Product Goal, criar e comunicar itens de backlog, ordenar esses itens e garantir transparência. ([Guias Scrum][2])

---

## 5.2 Scrum Master

| Responsabilidade              | Explicação prática                                                               |
| ----------------------------- | -------------------------------------------------------------------------------- |
| Garantir uso correto do Scrum | Ajuda o time e a organização a entenderem Scrum                                  |
| Remover impedimentos          | Atua para destravar problemas que impedem progresso                              |
| Facilitar eventos             | Garante que Planning, Daily, Review e Retrospective sejam produtivas             |
| Desenvolver o time            | Ajuda o time a ganhar autonomia e maturidade                                     |
| Apoiar o PO                   | Ajuda em técnicas de backlog, objetivo de produto e colaboração com stakeholders |
| Apoiar a organização          | Ajuda na adoção de Scrum, treinamento e remoção de barreiras organizacionais     |

O Scrum Master é responsável por estabelecer o Scrum como definido no Scrum Guide e por melhorar a efetividade do Scrum Team. Ele serve ao time, ao Product Owner e à organização. ([Guias Scrum][2])

---

## 5.3 Developers

| Responsabilidade              | Explicação prática                                  |
| ----------------------------- | --------------------------------------------------- |
| Criar o incremento            | Transformar itens do backlog em produto utilizável  |
| Planejar a Sprint             | Criar o Sprint Backlog                              |
| Garantir qualidade            | Seguir a Definition of Done                         |
| Adaptar plano diariamente     | Ajustar trabalho conforme progresso e aprendizado   |
| Responsabilidade profissional | O time se responsabiliza coletivamente pela entrega |

No Scrum, “Developers” não significa apenas programadores. O termo representa as pessoas que criam qualquer parte de um incremento utilizável. Dependendo do contexto, pode incluir engenharia, design, dados, QA, UX, operação ou outros especialistas. O Scrum Guide usa “Developers” de forma ampla, já que Scrum se expandiu para além do desenvolvimento de software. ([Guias Scrum][2])

---

# 6. Onde entra o gerente de projetos?

| Pergunta                                             | Resposta                                                                                                       |
| ---------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| Existe papel formal de Gerente de Projetos no Scrum? | Não como accountabilities oficiais do Scrum Team                                                               |
| Scrum elimina gestão?                                | Não. Ele muda a forma de gestão                                                                                |
| Quem gerencia prioridade?                            | Product Owner                                                                                                  |
| Quem facilita processo e remove impedimentos?        | Scrum Master                                                                                                   |
| Quem gerencia como o trabalho será feito?            | Developers, como time auto-organizado                                                                          |
| O que um gerente pode fazer nesse contexto?          | Atuar em governança, dependências, stakeholders, orçamento, riscos, comunicação executiva e melhoria sistêmica |

## Visão prática para sua migração

| Área de atuação   | Como um gestor contribui em Scrum                               |
| ----------------- | --------------------------------------------------------------- |
| Stakeholders      | Alinha expectativa, comunica riscos e evita ruído político      |
| Governança        | Garante visibilidade de progresso, custo, escopo e riscos       |
| Dependências      | Coordena integrações com outros times, fornecedores e áreas     |
| Roadmap           | Ajuda a conectar estratégia, objetivos e entregas incrementais  |
| Métricas          | Acompanha fluxo, previsibilidade, lead time, throughput e valor |
| Riscos            | Torna riscos visíveis antes que virem problemas críticos        |
| Cultura           | Protege autonomia do time e combate microgerenciamento          |
| Melhoria contínua | Ajuda o time a melhorar processo, comunicação e qualidade       |

---

# 7. User Story

| Aspecto       | Explicação                                                     |
| ------------- | -------------------------------------------------------------- |
| O que é       | Uma forma simples de descrever uma necessidade do usuário      |
| Objetivo      | Conectar funcionalidade com valor                              |
| Formato comum | “Como [tipo de usuário], quero [objetivo], para [benefício]”   |
| Uso no Scrum  | Pode ser usada como item de Product Backlog                    |
| Cuidado       | User Story não deve virar apenas uma tarefa técnica disfarçada |

## Exemplo

| Campo       | Exemplo                                                                                   |
| ----------- | ----------------------------------------------------------------------------------------- |
| Persona     | Cliente de e-commerce                                                                     |
| Necessidade | Acompanhar o status do pedido                                                             |
| Benefício   | Saber quando o pedido será entregue                                                       |
| User Story  | Como cliente, quero acompanhar o status do meu pedido para saber quando ele será entregue |

## Critérios de aceite

| Critério       | Exemplo                                                                     |
| -------------- | --------------------------------------------------------------------------- |
| Status visível | O cliente deve ver se o pedido está “em separação”, “enviado” ou “entregue” |
| Atualização    | O status deve ser atualizado quando houver mudança logística                |
| Notificação    | O cliente deve receber aviso quando o pedido for enviado                    |
| Erro           | Se o pedido não existir, o sistema deve informar que não encontrou o pedido |

**Ponto de gestão:**
User Story boa não é apenas escrita bonita. Ela precisa ajudar o time a entender **quem precisa, o que precisa, por que precisa e como validar se foi entregue corretamente**.

---

# 8. Artefatos do Scrum

Os artefatos do Scrum representam trabalho ou valor e existem para maximizar transparência. Os três artefatos são **Product Backlog**, **Sprint Backlog** e **Increment**. Cada um possui um compromisso associado: Product Goal, Sprint Goal e Definition of Done. ([Guias Scrum][2])

| Artefato        | O que é                                                    | Compromisso associado | Uso na gestão                           |
| --------------- | ---------------------------------------------------------- | --------------------- | --------------------------------------- |
| Product Backlog | Lista ordenada do que é necessário para melhorar o produto | Product Goal          | Dá visão de prioridade, valor e direção |
| Sprint Backlog  | Itens selecionados para a Sprint mais o plano de entrega   | Sprint Goal           | Dá foco ao ciclo atual                  |
| Increment       | Resultado utilizável produzido pelo time                   | Definition of Done    | Mostra valor entregue de verdade        |

---

## 8.1 Product Backlog

| Ponto          | Resumo                                                         |
| -------------- | -------------------------------------------------------------- |
| Dono           | Product Owner                                                  |
| Conteúdo       | Features, melhorias, bugs, débitos, experimentos, necessidades |
| Característica | É vivo, muda conforme aprendizado                              |
| Boa prática    | Itens mais próximos de execução devem estar mais claros        |
| Erro comum     | Ter backlog enorme, desatualizado e sem prioridade real        |

---

## 8.2 Sprint Backlog

| Ponto          | Resumo                                                     |
| -------------- | ---------------------------------------------------------- |
| Dono prático   | Developers                                                 |
| Conteúdo       | Objetivo da Sprint, itens selecionados e plano de execução |
| Característica | Pode ser ajustado durante a Sprint                         |
| Boa prática    | Deve permitir inspeção diária de progresso                 |
| Erro comum     | Tratar Sprint Backlog como contrato fechado e imutável     |

---

## 8.3 Increment

| Ponto            | Resumo                                          |
| ---------------- | ----------------------------------------------- |
| O que representa | Produto utilizável criado durante a Sprint      |
| Condição         | Precisa atender à Definition of Done            |
| Valor            | Permite inspeção real por stakeholders          |
| Boa prática      | Incremento deve ser potencialmente liberável    |
| Erro comum       | Apresentar algo inacabado como se fosse entrega |

O Scrum Guide reforça que trabalho não pode ser considerado parte de um Increment se não atender à Definition of Done. ([Guias Scrum][2])

---

# 9. Eventos do Scrum

Os eventos do Scrum criam regularidade e reduzem a necessidade de reuniões não definidas no framework. Cada evento é uma oportunidade formal de inspeção e adaptação. ([Guias Scrum][2])

| Evento               | Objetivo                               | Participantes principais  | Resultado esperado                              |
| -------------------- | -------------------------------------- | ------------------------- | ----------------------------------------------- |
| Sprint               | Ciclo de trabalho de até um mês        | Scrum Team                | Incremento de valor                             |
| Sprint Planning      | Planejar o trabalho da Sprint          | Scrum Team                | Sprint Goal e Sprint Backlog                    |
| Daily Scrum          | Inspecionar progresso diário           | Developers                | Plano ajustado para o próximo dia               |
| Sprint Review        | Inspecionar resultado com stakeholders | Scrum Team e stakeholders | Feedback e possíveis ajustes no Product Backlog |
| Sprint Retrospective | Melhorar qualidade e efetividade       | Scrum Team                | Ações de melhoria para próximas Sprints         |

---

## 9.1 Sprint

| Ponto   | Resumo                                                        |
| ------- | ------------------------------------------------------------- |
| O que é | Contêiner dos demais eventos                                  |
| Duração | Um mês ou menos                                               |
| Função  | Transformar ideias em valor                                   |
| Gestão  | Ajuda a limitar risco e criar ciclos de aprendizado           |
| Cuidado | Sprints longas demais aumentam risco, incerteza e desperdício |

---

## 9.2 Sprint Planning

| Pergunta                           | Resposta esperada                     |
| ---------------------------------- | ------------------------------------- |
| Por que esta Sprint é valiosa?     | Sprint Goal                           |
| O que pode ser feito nesta Sprint? | Itens selecionados do Product Backlog |
| Como o trabalho será feito?        | Plano dos Developers                  |

---

## 9.3 Daily Scrum

| Ponto       | Resumo                                                                |
| ----------- | --------------------------------------------------------------------- |
| Duração     | 15 minutos                                                            |
| Objetivo    | Inspecionar progresso rumo ao Sprint Goal                             |
| Não é       | Reunião de status para gerente                                        |
| Boa prática | Focar em impedimentos, plano do dia e risco de não cumprir o objetivo |
| Erro comum  | Cada pessoa reportar tarefas mecanicamente sem discutir adaptação     |

---

## 9.4 Sprint Review

| Ponto         | Resumo                                       |
| ------------- | -------------------------------------------- |
| Objetivo      | Inspecionar o resultado da Sprint            |
| Participantes | Scrum Team e stakeholders                    |
| Foco          | Produto, valor, feedback e próximos passos   |
| Não é         | Apenas uma apresentação ou demo teatral      |
| Resultado     | Aprendizado que pode mudar o Product Backlog |

---

## 9.5 Sprint Retrospective

| Ponto      | Resumo                                                          |
| ---------- | --------------------------------------------------------------- |
| Objetivo   | Melhorar qualidade e efetividade                                |
| Foco       | Pessoas, interações, processo, ferramentas e Definition of Done |
| Resultado  | Ações práticas de melhoria                                      |
| Erro comum | Virar sessão de reclamações sem ação concreta                   |

---

# 10. Scrum visto como sistema de gestão

| Elemento           | Para que serve na gestão              |
| ------------------ | ------------------------------------- |
| Product Goal       | Dá direção estratégica                |
| Product Backlog    | Organiza demanda e prioridade         |
| Sprint Goal        | Dá foco de curto prazo                |
| Sprint Backlog     | Dá visibilidade do plano de execução  |
| Increment          | Mostra entrega real                   |
| Review             | Coleta feedback e ajusta direção      |
| Retrospective      | Melhora o sistema de trabalho         |
| Daily              | Mantém coordenação e adaptação diária |
| Definition of Done | Controla qualidade mínima             |

---

# 11. Métricas úteis para gestão em Scrum

| Métrica                    | O que indica                                       | Cuidado                                  |
| -------------------------- | -------------------------------------------------- | ---------------------------------------- |
| Velocity                   | Volume médio entregue por Sprint                   | Não comparar times diferentes            |
| Throughput                 | Quantidade de itens concluídos em período          | Útil para previsibilidade                |
| Lead Time                  | Tempo da demanda desde solicitação até entrega     | Mostra velocidade fim a fim              |
| Cycle Time                 | Tempo de execução após início do trabalho          | Ajuda a identificar gargalos             |
| Burndown                   | Trabalho restante na Sprint                        | Pode mascarar qualidade                  |
| Burnup                     | Trabalho concluído versus escopo total             | Melhor para visualizar mudança de escopo |
| Defeitos pós-entrega       | Qualidade real da entrega                          | Não deve ser analisado isoladamente      |
| Cumprimento do Sprint Goal | Foco em objetivo, não apenas quantidade de tarefas | Mais relevante que “terminar tickets”    |

---

# 12. Erros comuns ao aplicar Scrum

| Erro                         | Por que é problema                        | Correção                                      |
| ---------------------------- | ----------------------------------------- | --------------------------------------------- |
| Daily como reunião de status | Reduz autonomia e vira microgerenciamento | Usar Daily para inspeção e adaptação          |
| PO sem poder de decisão      | Backlog fica político e lento             | Dar autoridade real ao Product Owner          |
| Scrum Master como secretário | Papel perde força organizacional          | Atuar em impedimentos, facilitação e melhoria |
| Sprint sem objetivo          | Time vira fábrica de tarefas              | Definir Sprint Goal claro                     |
| Review sem stakeholder       | Perde feedback real                       | Envolver usuários, negócio e áreas impactadas |
| Retrospectiva sem ação       | Melhoria contínua não acontece            | Gerar ações pequenas e acompanháveis          |
| Backlog desorganizado        | Time perde clareza e previsibilidade      | Refinar, ordenar e remover itens obsoletos    |
| Definition of Done fraca     | Entrega parece pronta, mas não está       | Definir critérios mínimos de qualidade        |
| Medir só velocidade          | Incentiva quantidade, não valor           | Combinar métricas de fluxo, qualidade e valor |

---

# 13. O que memorizar

| Conceito           | Resumo curto                                              |
| ------------------ | --------------------------------------------------------- |
| Scrum              | Framework leve para entregar valor em ambientes complexos |
| Sprint             | Ciclo curto de entrega e aprendizado                      |
| Product Owner      | Maximiza valor e gerencia backlog                         |
| Scrum Master       | Facilita Scrum, remove impedimentos e melhora efetividade |
| Developers         | Criam incrementos utilizáveis                             |
| Product Backlog    | Lista ordenada do que pode melhorar o produto             |
| Sprint Backlog     | Plano da Sprint                                           |
| Increment          | Entrega utilizável e validável                            |
| Definition of Done | Critério mínimo para considerar algo pronto               |
| Review             | Inspeção do produto                                       |
| Retrospective      | Inspeção do processo                                      |
| Daily              | Inspeção diária do progresso rumo ao objetivo             |

---

# 14. Perguntas e respostas

| Pergunta                                                | Resposta                                                                                                                                  |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Scrum é uma metodologia completa?                       | Não. Scrum é um framework leve. Ele define papéis, eventos, artefatos e regras essenciais, mas não detalha todas as práticas de execução. |
| Qual é o objetivo principal do Scrum?                   | Gerar valor de forma incremental, com inspeção e adaptação frequentes.                                                                    |
| Quais são os três pilares do Scrum?                     | Transparência, inspeção e adaptação.                                                                                                      |
| Quais são os papéis do Scrum?                           | Product Owner, Scrum Master e Developers.                                                                                                 |
| Existe gerente de projetos como papel oficial no Scrum? | Não. A gestão existe, mas é distribuída entre PO, Scrum Master e Developers.                                                              |
| O que faz o Product Owner?                              | Maximiza valor, comunica objetivo de produto, ordena backlog e representa necessidades dos stakeholders.                                  |
| O que faz o Scrum Master?                               | Garante o entendimento do Scrum, facilita eventos, remove impedimentos e ajuda o time e a organização a melhorarem.                       |
| O que fazem os Developers?                              | Planejam e executam o trabalho da Sprint, criando incrementos utilizáveis com qualidade.                                                  |
| O que é Product Backlog?                                | Lista ordenada e evolutiva do que é necessário para melhorar o produto.                                                                   |
| O que é Sprint Backlog?                                 | Plano da Sprint, composto por Sprint Goal, itens selecionados e plano de entrega.                                                         |
| O que é Increment?                                      | Resultado utilizável produzido pelo time e validado pela Definition of Done.                                                              |
| O que é Sprint Planning?                                | Evento em que o time define por que a Sprint é valiosa, o que será feito e como será feito.                                               |
| O que é Daily Scrum?                                    | Evento diário de 15 minutos para inspecionar progresso e adaptar o plano rumo ao Sprint Goal.                                             |
| O que é Sprint Review?                                  | Evento para inspecionar o incremento com stakeholders e adaptar próximos passos.                                                          |
| O que é Sprint Retrospective?                           | Evento para melhorar processo, qualidade, colaboração e efetividade do time.                                                              |
| O que é User Story?                                     | Técnica para descrever uma necessidade do usuário conectada a valor.                                                                      |
| Qual é o erro mais comum na gestão com Scrum?           | Usar Scrum como controle de tarefas, e não como sistema de entrega de valor, aprendizado e adaptação.                                     |
| Como um gestor deve atuar em Scrum?                     | Facilitando alinhamento, removendo impedimentos organizacionais, gerenciando riscos, coordenando dependências e protegendo foco do time.  |

[1]: https://www.udemy.com/course/metodos-ageis/?srsltid=AfmBOopLY6w04AQthRz8X4aB9GV4T_x5cMA-uDkfoOQAarHlj2P24WW7 "Métodos ágeis de A a Z: o curso completo"
[2]: https://scrumguides.org/scrum-guide.html "Scrum Guide | Scrum Guides"

 **English version**  

# Section 4 — Scrum

## 1. General Summary

| Topic                    | What You Should Understand                                                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------ |
| Scrum Overview           | Scrum is a lightweight framework for dealing with complex problems through short cycles of planning, execution, inspection, and adaptation |
| Three Pillars            | Transparency, inspection, and adaptation                                                                                                   |
| Roles / Accountabilities | Product Owner, Scrum Master, and Developers                                                                                                |
| User Story               | A technique to describe user needs in a simple and value-oriented way                                                                      |
| Artifacts                | Product Backlog, Sprint Backlog, and Increment                                                                                             |
| Events                   | Sprint, Sprint Planning, Daily Scrum, Sprint Review, and Sprint Retrospective                                                              |
| Exercises                | Concept reinforcement                                                                                                                      |
| Specialist Interview     | Practical view on how Scrum works in real environments                                                                                     |

---

# 2. What Scrum Is

| Aspect                 | Summary                                                                                                 |
| ---------------------- | ------------------------------------------------------------------------------------------------------- |
| Definition             | Scrum is a lightweight framework used to generate value through adaptive solutions for complex problems |
| Nature                 | It is not a detailed step-by-step methodology                                                           |
| How it works           | The team works in short cycles called Sprints                                                           |
| Main goal              | Deliver increments of value frequently                                                                  |
| Foundation             | Empiricism, continuous learning, and progressive improvement                                            |
| Project management use | Helps organize work, prioritize value, reduce risk, and improve predictability                          |

**Important idea:**
Scrum does not try to predict everything at the beginning. It creates a structure where the team can **plan, deliver, inspect, learn, and adapt** continuously.

---

# 3. Scrum from a Project Management Perspective

| Traditional Project Management      | Scrum                                              |
| ----------------------------------- | -------------------------------------------------- |
| Large upfront planning              | Continuous planning by Sprint                      |
| Scope tends to be fixed             | Scope can be adapted based on learning             |
| Delivery usually happens at the end | Delivery happens incrementally                     |
| Change is often seen as a problem   | Change is treated as useful information            |
| Control by schedule                 | Control by value delivered and frequent inspection |
| Manager assigns tasks               | Team self-manages within clear goals               |
| Focus on following the plan         | Focus on delivering value and adapting             |

## Key point for your migration to project management

In Scrum, the manager’s role is not mainly to command tasks.

The focus shifts to:

| Management Focus     | Practical Meaning                                            |
| -------------------- | ------------------------------------------------------------ |
| Create clarity       | Make goals, priorities, risks, and expectations visible      |
| Remove impediments   | Help solve organizational, technical, or dependency blockers |
| Facilitate decisions | Help the team and stakeholders make decisions faster         |
| Align stakeholders   | Keep business, product, technology, and leadership aligned   |
| Protect focus        | Reduce interruptions and priority conflicts                  |
| Measure value        | Track outcomes, not only task completion                     |

---

# 4. The Three Pillars of Scrum

| Pillar       | Meaning                                                       | Project Management Application                                          |
| ------------ | ------------------------------------------------------------- | ----------------------------------------------------------------------- |
| Transparency | Work, progress, risks, and problems must be visible           | Clear backlog, clear Sprint Goal, visible impediments, realistic status |
| Inspection   | The team frequently checks product, process, and progress     | Daily Scrum, Sprint Review, metrics, risk tracking                      |
| Adaptation   | The team adjusts plan, scope, product, or process when needed | Reprioritize backlog, change delivery strategy, improve workflow        |

## Simple interpretation

| Pillar       | Simple Question                                          |
| ------------ | -------------------------------------------------------- |
| Transparency | Can everyone see what is really happening?               |
| Inspection   | Are we checking progress and results often enough?       |
| Adaptation   | Are we changing direction when evidence shows we should? |

---

# 5. Scrum Roles / Accountabilities

Scrum has three main accountabilities:

| Role          | Main Responsibility                             |
| ------------- | ----------------------------------------------- |
| Product Owner | Maximize product value                          |
| Scrum Master  | Ensure Scrum is understood and used effectively |
| Developers    | Create usable increments of product             |

---

## 5.1 Product Owner

| Responsibility           | Practical Explanation                                                       |
| ------------------------ | --------------------------------------------------------------------------- |
| Maximize value           | Decides what brings more value to the product                               |
| Manage Product Backlog   | Keeps the backlog clear, ordered, and visible                               |
| Communicate Product Goal | Gives medium/long-term direction to the team                                |
| Prioritize               | Orders work based on value, risk, dependencies, and strategy                |
| Represent stakeholders   | Consolidates needs from customers, business, support, operations, and users |

## Product Owner in management terms

| Area           | Product Owner Focus                     |
| -------------- | --------------------------------------- |
| Strategy       | What outcome are we trying to achieve?  |
| Prioritization | What should be done first?              |
| Value          | Why does this matter?                   |
| Stakeholders   | Who needs to be aligned?                |
| Scope          | What should enter or leave the backlog? |
| Validation     | How do we know this delivery worked?    |

## Common Product Owner mistakes

| Mistake                              | Problem                                    |
| ------------------------------------ | ------------------------------------------ |
| Acting only as a requirements writer | Loses strategic ownership                  |
| Saying yes to every stakeholder      | Creates an overloaded backlog              |
| Not prioritizing clearly             | The team loses focus                       |
| Not being available                  | Developers wait too long for clarification |
| Prioritizing only by urgency         | Long-term value and risk are ignored       |

---

## 5.2 Scrum Master

| Responsibility            | Practical Explanation                                                      |
| ------------------------- | -------------------------------------------------------------------------- |
| Ensure proper Scrum usage | Helps the team and organization understand Scrum                           |
| Remove impediments        | Works to unblock problems that slow down the team                          |
| Facilitate events         | Makes Planning, Daily, Review, and Retrospective useful                    |
| Develop team maturity     | Helps the team become more autonomous                                      |
| Support the Product Owner | Helps with backlog management, Product Goal, and stakeholder collaboration |
| Support the organization  | Helps the company adopt Scrum and remove systemic barriers                 |

## Scrum Master in management terms

| Area                   | Scrum Master Focus                                   |
| ---------------------- | ---------------------------------------------------- |
| Facilitation           | Improve meetings, collaboration, and decision-making |
| Process                | Help the team inspect and improve how it works       |
| Impediments            | Remove blockers that the team cannot solve alone     |
| Team health            | Support trust, clarity, and accountability           |
| Agile adoption         | Help the organization understand Agile principles    |
| Continuous improvement | Turn problems into improvement actions               |

## Common Scrum Master mistakes

| Mistake                                         | Problem                                 |
| ----------------------------------------------- | --------------------------------------- |
| Acting as a team secretary                      | Reduces the role to administrative work |
| Becoming a command-and-control manager          | Damages team autonomy                   |
| Only scheduling meetings                        | Does not improve team effectiveness     |
| Ignoring organizational blockers                | Leaves the team stuck                   |
| Protecting Scrum rules without explaining value | Creates mechanical Scrum                |

---

## 5.3 Developers

In Scrum, “Developers” does not mean only software engineers. It means the people responsible for creating the product Increment.

Depending on the context, this can include:

| Possible Team Member       | Contribution                                       |
| -------------------------- | -------------------------------------------------- |
| Software engineer          | Builds technical solution                          |
| QA / Tester                | Supports quality and validation                    |
| UX/UI Designer             | Designs user experience                            |
| Data specialist            | Supports data analysis or data products            |
| Business analyst           | Helps clarify rules and process                    |
| DevOps / Platform engineer | Supports deployment, environments, and reliability |

## Developers’ responsibilities

| Responsibility       | Practical Explanation                                 |
| -------------------- | ----------------------------------------------------- |
| Create the Increment | Transform backlog items into usable product           |
| Plan the Sprint work | Build the Sprint Backlog                              |
| Ensure quality       | Follow the Definition of Done                         |
| Adapt daily          | Adjust the plan based on progress and new information |
| Own delivery         | Take collective responsibility for the result         |

---

# 6. Where Does the Project Manager Fit?

| Question                                            | Answer                                                                                                 |
| --------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| Is there an official Project Manager role in Scrum? | No, not as part of the official Scrum accountabilities                                                 |
| Does Scrum eliminate management?                    | No. It changes how management happens                                                                  |
| Who manages priority?                               | Product Owner                                                                                          |
| Who facilitates process and removes impediments?    | Scrum Master                                                                                           |
| Who manages how the work is done?                   | Developers, as a self-managing team                                                                    |
| Can a project manager still add value?              | Yes, especially in governance, dependencies, risks, budget, stakeholders, and organizational alignment |

## Practical project manager contribution in a Scrum environment

| Area                   | How a Project Manager Can Help                           |
| ---------------------- | -------------------------------------------------------- |
| Stakeholders           | Align expectations and reduce communication noise        |
| Governance             | Provide visibility on scope, cost, progress, and risks   |
| Dependencies           | Coordinate with other teams, vendors, and business areas |
| Roadmap                | Connect strategy, objectives, and incremental delivery   |
| Metrics                | Track flow, predictability, quality, and outcomes        |
| Risks                  | Make risks visible before they become critical problems  |
| Culture                | Protect team autonomy and reduce micromanagement         |
| Continuous improvement | Help improve process, communication, and delivery system |

---

# 7. User Story

| Aspect        | Explanation                                             |
| ------------- | ------------------------------------------------------- |
| What it is    | A simple way to describe a user need                    |
| Objective     | Connect functionality to value                          |
| Common format | “As a [type of user], I want [goal], so that [benefit]” |
| Scrum usage   | Can be used as a Product Backlog Item                   |
| Main risk     | Becoming only a disguised technical task                |

## Example

| Field      | Example                                                                                 |
| ---------- | --------------------------------------------------------------------------------------- |
| Persona    | E-commerce customer                                                                     |
| Need       | Track order status                                                                      |
| Benefit    | Know when the order will be delivered                                                   |
| User Story | As a customer, I want to track my order status so that I know when it will be delivered |

## Acceptance Criteria

| Criterion         | Example                                                                               |
| ----------------- | ------------------------------------------------------------------------------------- |
| Status visibility | The customer can see whether the order is “being prepared”, “shipped”, or “delivered” |
| Update            | The status changes when logistics updates the order                                   |
| Notification      | The customer receives a notification when the order is shipped                        |
| Error case        | If the order does not exist, the system informs that it was not found                 |

## Management view

A good User Story is not only well-written. It must help the team understand:

| Question                 | Purpose                                |
| ------------------------ | -------------------------------------- |
| Who needs this?          | Identify the user or stakeholder       |
| What do they need?       | Clarify the expected capability        |
| Why do they need it?     | Connect work to value                  |
| How will we validate it? | Define acceptance and success criteria |

---

# 8. Scrum Artifacts

| Artifact        | What It Is                                            | Commitment         | Management Use                       |
| --------------- | ----------------------------------------------------- | ------------------ | ------------------------------------ |
| Product Backlog | Ordered list of what is needed to improve the product | Product Goal       | Shows direction, priority, and value |
| Sprint Backlog  | Selected work for the Sprint plus the delivery plan   | Sprint Goal        | Gives focus to the current cycle     |
| Increment       | Usable result produced by the team                    | Definition of Done | Shows real delivered value           |

---

## 8.1 Product Backlog

| Point          | Summary                                                          |
| -------------- | ---------------------------------------------------------------- |
| Owner          | Product Owner                                                    |
| Content        | Features, improvements, bugs, technical debt, experiments, needs |
| Nature         | Dynamic and constantly evolving                                  |
| Good practice  | Items closer to execution should be clearer                      |
| Common mistake | Huge, outdated backlog with no real prioritization               |

## Management interpretation

The Product Backlog is not just a task list. It is a **value management tool**.

It should answer:

| Question                | Purpose                  |
| ----------------------- | ------------------------ |
| What may we build?      | Scope visibility         |
| What matters most?      | Prioritization           |
| Why does it matter?     | Value justification      |
| What comes next?        | Planning and forecasting |
| What should be removed? | Waste reduction          |

---

## 8.2 Sprint Backlog

| Point           | Summary                                                         |
| --------------- | --------------------------------------------------------------- |
| Practical owner | Developers                                                      |
| Content         | Sprint Goal, selected Product Backlog Items, and execution plan |
| Nature          | Can be adjusted during the Sprint                               |
| Good practice   | Should allow daily inspection of progress                       |
| Common mistake  | Treating it as a fixed and immutable contract                   |

## Management interpretation

The Sprint Backlog is the team’s short-term execution plan.

It helps answer:

| Question                                   | Purpose              |
| ------------------------------------------ | -------------------- |
| What are we trying to achieve this Sprint? | Sprint focus         |
| What work did we select?                   | Scope for the Sprint |
| How will we deliver it?                    | Execution plan       |
| Are we still on track?                     | Daily inspection     |
| What needs to change?                      | Adaptation           |

---

## 8.3 Increment

| Point              | Summary                                           |
| ------------------ | ------------------------------------------------- |
| What it represents | A usable product result created during the Sprint |
| Condition          | Must meet the Definition of Done                  |
| Value              | Allows real inspection by stakeholders            |
| Good practice      | Should be potentially releasable                  |
| Common mistake     | Presenting unfinished work as if it were delivery |

## Management interpretation

The Increment is the strongest evidence of progress.

| Weak Evidence               | Strong Evidence                        |
| --------------------------- | -------------------------------------- |
| “The team is working on it” | A usable Increment exists              |
| “The task is almost done”   | It meets the Definition of Done        |
| “Development is complete”   | It was tested, reviewed, and validated |
| “We followed the plan”      | Stakeholders can inspect real value    |

---

# 9. Scrum Events

| Event                | Objective                                | Main Participants           | Expected Result                               |
| -------------------- | ---------------------------------------- | --------------------------- | --------------------------------------------- |
| Sprint               | Container for all Scrum events           | Scrum Team                  | Increment of value                            |
| Sprint Planning      | Plan the Sprint                          | Scrum Team                  | Sprint Goal and Sprint Backlog                |
| Daily Scrum          | Inspect daily progress                   | Developers                  | Adjusted plan for the next day                |
| Sprint Review        | Inspect product result with stakeholders | Scrum Team and stakeholders | Feedback and possible Product Backlog changes |
| Sprint Retrospective | Improve quality and effectiveness        | Scrum Team                  | Improvement actions for future Sprints        |

---

## 9.1 Sprint

| Point          | Summary                                                         |
| -------------- | --------------------------------------------------------------- |
| What it is     | The container for the other Scrum events                        |
| Duration       | One month or less                                               |
| Function       | Turn ideas into value                                           |
| Management use | Limits risk and creates learning cycles                         |
| Caution        | Sprints that are too long increase risk, uncertainty, and waste |

---

## 9.2 Sprint Planning

| Key Question                      | Expected Answer                |
| --------------------------------- | ------------------------------ |
| Why is this Sprint valuable?      | Sprint Goal                    |
| What can be done this Sprint?     | Selected Product Backlog Items |
| How will the chosen work be done? | Developers’ execution plan     |

## Management focus

| Focus     | Explanation                                      |
| --------- | ------------------------------------------------ |
| Value     | The Sprint should have a clear reason to exist   |
| Capacity  | The team should not overcommit                   |
| Risk      | Dependencies and uncertainties should be visible |
| Alignment | Everyone should understand the Sprint Goal       |
| Clarity   | Selected items should be sufficiently understood |

---

## 9.3 Daily Scrum

| Point          | Summary                                                              |
| -------------- | -------------------------------------------------------------------- |
| Duration       | 15 minutes                                                           |
| Objective      | Inspect progress toward the Sprint Goal                              |
| What it is not | A status meeting for a manager                                       |
| Good practice  | Focus on impediments, adaptation, and risk to the Sprint Goal        |
| Common mistake | Each person mechanically reports tasks without discussing adaptation |

## Better Daily Scrum questions

| Weak Question              | Better Question                          |
| -------------------------- | ---------------------------------------- |
| What did you do yesterday? | Are we closer to the Sprint Goal?        |
| What will you do today?    | What needs to change in our plan today?  |
| Do you have blockers?      | What is putting the Sprint Goal at risk? |

---

## 9.4 Sprint Review

| Point          | Summary                                      |
| -------------- | -------------------------------------------- |
| Objective      | Inspect the result of the Sprint             |
| Participants   | Scrum Team and stakeholders                  |
| Focus          | Product, value, feedback, and next steps     |
| What it is not | Only a presentation or theatrical demo       |
| Result         | Learning that can change the Product Backlog |

## Management focus

| Focus                   | Explanation                                            |
| ----------------------- | ------------------------------------------------------ |
| Stakeholder feedback    | Validate whether the delivery solves the right problem |
| Product direction       | Adjust priorities based on evidence                    |
| Value delivered         | Discuss outcomes, not only outputs                     |
| Risks and opportunities | Identify what changed in the business context          |
| Next steps              | Update the Product Backlog if needed                   |

---

## 9.5 Sprint Retrospective

| Point          | Summary                                                      |
| -------------- | ------------------------------------------------------------ |
| Objective      | Improve quality and effectiveness                            |
| Focus          | People, interactions, process, tools, and Definition of Done |
| Result         | Practical improvement actions                                |
| Common mistake | Becoming a complaint session without concrete action         |

## Good retrospective outputs

| Problem Found                  | Possible Improvement Action                |
| ------------------------------ | ------------------------------------------ |
| Too many unclear backlog items | Improve refinement before Sprint Planning  |
| Frequent production bugs       | Strengthen Definition of Done and testing  |
| Many external blockers         | Improve dependency management              |
| Team overloaded                | Reduce Sprint commitment                   |
| Meetings not productive        | Improve facilitation and agenda            |
| Delayed feedback               | Bring stakeholders closer to Sprint Review |

---

# 10. Scrum as a Management System

| Scrum Element        | Management Purpose                          |
| -------------------- | ------------------------------------------- |
| Product Goal         | Gives strategic direction                   |
| Product Backlog      | Organizes demand and priority               |
| Sprint Goal          | Gives short-term focus                      |
| Sprint Backlog       | Provides visibility of execution plan       |
| Increment            | Shows real delivery                         |
| Sprint Review        | Collects feedback and adjusts direction     |
| Sprint Retrospective | Improves the work system                    |
| Daily Scrum          | Maintains coordination and daily adaptation |
| Definition of Done   | Controls minimum quality                    |

---

# 11. Useful Metrics for Scrum Management

| Metric               | What It Indicates                           | Caution                                 |
| -------------------- | ------------------------------------------- | --------------------------------------- |
| Velocity             | Average amount of work delivered per Sprint | Do not compare different teams          |
| Throughput           | Number of items completed in a period       | Useful for predictability               |
| Lead Time            | Time from request to delivery               | Shows end-to-end speed                  |
| Cycle Time           | Time from work start to completion          | Helps identify bottlenecks              |
| Burndown             | Remaining work in the Sprint                | Can hide quality problems               |
| Burnup               | Completed work versus total scope           | Better for visualizing scope changes    |
| Post-release defects | Real delivery quality                       | Should not be analyzed alone            |
| Sprint Goal success  | Whether the Sprint objective was achieved   | More relevant than only finishing tasks |

---

# 12. Common Scrum Mistakes

| Mistake                              | Why It Is a Problem                          | Correction                                          |
| ------------------------------------ | -------------------------------------------- | --------------------------------------------------- |
| Daily as a status meeting            | Reduces autonomy and creates micromanagement | Use Daily for inspection and adaptation             |
| Product Owner without decision power | Backlog becomes political and slow           | Give real authority to the Product Owner            |
| Scrum Master as a secretary          | The role loses organizational impact         | Focus on impediments, facilitation, and improvement |
| Sprint without a goal                | Team becomes a task factory                  | Define a clear Sprint Goal                          |
| Review without stakeholders          | Real feedback is lost                        | Involve users, business, and impacted areas         |
| Retrospective without action         | Continuous improvement does not happen       | Generate small and trackable actions                |
| Disorganized backlog                 | Team loses clarity and predictability        | Refine, order, and remove obsolete items            |
| Weak Definition of Done              | Work looks complete but is not truly done    | Define minimum quality criteria                     |
| Measuring only velocity              | Encourages quantity instead of value         | Combine flow, quality, and value metrics            |

---

# 13. What to Memorize

| Concept              | Short Summary                                                      |
| -------------------- | ------------------------------------------------------------------ |
| Scrum                | Lightweight framework for delivering value in complex environments |
| Sprint               | Short cycle of delivery and learning                               |
| Product Owner        | Maximizes value and manages the backlog                            |
| Scrum Master         | Facilitates Scrum, removes impediments, and improves effectiveness |
| Developers           | Create usable increments                                           |
| Product Backlog      | Ordered list of what may improve the product                       |
| Sprint Backlog       | Sprint plan                                                        |
| Increment            | Usable and validatable delivery                                    |
| Definition of Done   | Minimum criteria to consider work complete                         |
| Sprint Review        | Product inspection                                                 |
| Sprint Retrospective | Process inspection                                                 |
| Daily Scrum          | Daily inspection of progress toward the Sprint Goal                |
| Sprint Goal          | Clear objective for the Sprint                                     |
| Product Goal         | Product direction and strategic target                             |

---

# 14. Questions and Answers

| Question                                                 | Answer                                                                                                                                               |
| -------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| Is Scrum a complete methodology?                         | No. Scrum is a lightweight framework. It defines essential roles, events, artifacts, and rules, but it does not describe every execution practice.   |
| What is the main goal of Scrum?                          | To generate value incrementally through frequent inspection and adaptation.                                                                          |
| What are the three pillars of Scrum?                     | Transparency, inspection, and adaptation.                                                                                                            |
| What are the Scrum accountabilities?                     | Product Owner, Scrum Master, and Developers.                                                                                                         |
| Is Project Manager an official Scrum role?               | No. However, management still exists and is distributed across Product Owner, Scrum Master, and Developers.                                          |
| What does the Product Owner do?                          | Maximizes value, communicates the Product Goal, orders the Product Backlog, and represents stakeholder needs.                                        |
| What does the Scrum Master do?                           | Helps the team and organization understand Scrum, facilitates events, removes impediments, and improves effectiveness.                               |
| What do Developers do?                                   | Plan and execute Sprint work, creating usable increments with quality.                                                                               |
| What is the Product Backlog?                             | An ordered and evolving list of what is needed to improve the product.                                                                               |
| What is the Sprint Backlog?                              | The Sprint plan, composed of the Sprint Goal, selected items, and the execution plan.                                                                |
| What is an Increment?                                    | A usable product result created by the team and validated by the Definition of Done.                                                                 |
| What is Sprint Planning?                                 | The event where the team defines why the Sprint is valuable, what will be done, and how the work will be performed.                                  |
| What is the Daily Scrum?                                 | A 15-minute daily event to inspect progress and adapt the plan toward the Sprint Goal.                                                               |
| What is the Sprint Review?                               | An event to inspect the Increment with stakeholders and adapt future product direction.                                                              |
| What is the Sprint Retrospective?                        | An event to improve process, quality, collaboration, and team effectiveness.                                                                         |
| What is a User Story?                                    | A technique to describe a user need connected to value.                                                                                              |
| What is the Definition of Done?                          | A shared quality standard that defines when work is truly complete.                                                                                  |
| Why is the Sprint Goal important?                        | It gives focus and helps the team make decisions during the Sprint.                                                                                  |
| What is the most common management mistake in Scrum?     | Using Scrum as a task-control system instead of a value delivery, learning, and adaptation system.                                                   |
| How should a project manager act in a Scrum environment? | By facilitating alignment, managing risks and dependencies, supporting stakeholders, removing organizational impediments, and protecting team focus. |

