# Fase 1 — Consolidação: Fundamentos Ágeis, Scrum, Iniciação, Backlog e Estimativas

> Objetivo: consolidar os fundamentos antes de avançar para execução, Kanban, liderança e escala.  
> Perspectiva: entender o conceito, saber aplicá-lo e saber qual é a responsabilidade do Gerente de Projetos.

## Índice

- [1. Como estudar esta fase](#como-estudar)
- [2. Mindset Ágil](#mindset-agil)
- [3. Scrum: teoria e empirismo](#scrum-teoria)
- [4. Responsabilidades no Scrum](#responsabilidades-scrum)
- [5. Artefatos e compromissos](#artefatos)
- [6. Eventos do Scrum](#eventos)
- [7. Iniciando o projeto](#iniciando)
- [8. Criando o Product Backlog](#product-backlog)
- [9. Priorização e refinamento](#priorizacao)
- [10. Estimativas e Sprint Planning](#estimativas)
- [11. Definition of Ready, Critério de Aceitação e Definition of Done](#ready-aceitacao-dod)
- [12. Visão do Gerente de Projetos](#visao-gerente)
- [13. Mapa mental](#mapa-mental)
- [14. Revisão para ouvir](#revisao-ouvir)
- [15. Exercícios](#exercicios)
- [16. Gabarito comentado](#gabarito)
- [17. Fontes](#fontes)

<a id="como-estudar"></a>
## 1. Como estudar esta fase

Para cada conceito, use quatro perguntas:

1. **O que é?**
2. **Para que serve?**
3. **Quem é responsável por isso?**
4. **Como eu tomaria uma decisão usando esse conceito?**

Não memorize Scrum apenas como uma sequência de cerimônias. O ponto central é entender como transparência, inspeção e adaptação reduzem risco em trabalho complexo.

---

<a id="mindset-agil"></a>
## 2. Mindset Ágil

### 2.1 Projeto e incerteza

Projeto é um esforço temporário para produzir um resultado único. Em ambientes previsíveis, um plano detalhado pode funcionar bem. Em ambientes complexos, requisitos, tecnologia, mercado e comportamento do cliente mudam durante a execução.

O pensamento ágil aceita que uma parte relevante do conhecimento surgirá **durante** o trabalho. Por isso, troca-se parte do planejamento preditivo por ciclos curtos de entrega, feedback e adaptação.

### 2.2 Cascata x abordagem adaptativa

**Cascata** tende a funcionar melhor quando:
- requisitos são relativamente estáveis;
- o domínio é conhecido;
- mudanças são caras ou raras;
- o resultado pode ser especificado com boa precisão antecipadamente.

**Abordagem ágil** tende a ser mais adequada quando:
- existe incerteza de produto ou tecnologia;
- feedback frequente é valioso;
- prioridades podem mudar;
- entregar valor em partes reduz risco.

Não é correto afirmar que ágil significa "sem planejamento". O planejamento existe, mas é feito em diferentes horizontes e revisado conforme novas evidências surgem.

### 2.3 Cone da incerteza

No início de um projeto, estimativas possuem maior variação porque pouco é conhecido. À medida que o time aprende sobre produto, tecnologia e contexto, a faixa de incerteza tende a diminuir.

**Aplicação de gestão:** evite comunicar estimativas iniciais como compromissos rígidos. Trabalhe com faixas, hipóteses, riscos e revisões frequentes.

### 2.4 Manifesto Ágil

Os quatro valores enfatizam:
- pessoas e interações;
- produto funcionando;
- colaboração com o cliente;
- resposta a mudanças.

Os itens da direita continuam tendo valor. A diferença é a preferência pelo item da esquerda quando há tensão entre os dois.

### 2.5 Tripla restrição

A tripla restrição tradicional envolve:
- escopo;
- prazo;
- custo.

Qualidade normalmente é tratada como uma dimensão que não deveria ser sacrificada para compensar problemas nas demais.

Em gestão ágil, é comum manter equipe e cadência relativamente estáveis e tornar o escopo mais adaptável, priorizando o que gera mais valor.

### 2.6 Planning Onion

O planejamento pode ser dividido em níveis:
- visão;
- roadmap;
- release;
- iteração/Sprint;
- dia.

Quanto mais distante o horizonte, menor deve ser o nível de detalhe. Quanto mais próximo, maior a precisão possível.

### Exemplo curto

Uma diretoria pergunta em janeiro tudo o que será entregue em novembro. A resposta adequada não é inventar precisão. O gerente pode apresentar objetivos, temas e marcos no roadmap, reservando detalhamento fino para releases e Sprints mais próximas.

---

<a id="scrum-teoria"></a>
## 3. Scrum: teoria e empirismo

Scrum é um framework leve para gerar valor por meio de soluções adaptativas para problemas complexos.

### 3.1 Empirismo

O empirismo se baseia em aprender com a experiência e tomar decisões com base no que é observado.

Seus pilares são:

- **Transparência:** estado do produto, trabalho e problemas precisam ser visíveis.
- **Inspeção:** artefatos e progresso são examinados com frequência.
- **Adaptação:** quando algo se desvia do esperado, o plano ou abordagem é ajustado.

Sem transparência, inspeção produz conclusões ruins. Sem inspeção, problemas permanecem escondidos. Sem adaptação, aprender não gera melhoria.

### 3.2 Valores do Scrum

- Compromisso
- Foco
- Abertura
- Respeito
- Coragem

Esses valores não são decorativos. Eles sustentam o comportamento necessário para empirismo e autogerenciamento.

### 3.3 Autogerenciamento

O Scrum Team decide internamente quem faz o quê, quando e como. Isso não significa ausência de objetivos, responsabilidade ou liderança.

O ambiente organizacional define restrições e objetivos. Dentro delas, o time deve possuir autonomia suficiente para organizar o trabalho.

### 3.4 ScrumButs

ScrumBut é o padrão: "usamos Scrum, mas...".

Exemplos:
- "Fazemos Daily, mas o gerente distribui todas as tarefas."
- "Temos Sprint, mas inserimos qualquer demanda urgente sem renegociar o objetivo."
- "Temos Product Owner, mas o diretor muda o backlog diretamente."

A adaptação do framework pode ser necessária em uma organização, mas alterar elementos essenciais pode eliminar benefícios esperados.

---

<a id="responsabilidades-scrum"></a>
## 4. Responsabilidades no Scrum

O Scrum Guide de 2020 define três **accountabilities** dentro do Scrum Team:

### 4.1 Product Owner

Responsável por maximizar o valor do produto e pelo gerenciamento eficaz do Product Backlog.

Na prática:
- define e comunica o Product Goal;
- ordena os itens do Product Backlog;
- garante que o backlog esteja transparente;
- toma decisões de prioridade de produto.

O PO pode delegar trabalho operacional, mas permanece accountable.

### 4.2 Scrum Master

Responsável pela eficácia do Scrum Team.

Atua:
- ajudando o time a usar Scrum corretamente;
- removendo ou facilitando a remoção de impedimentos;
- promovendo autogerenciamento;
- apoiando Product Owner e organização;
- facilitando eventos quando necessário.

### 4.3 Developers

São as pessoas comprometidas com criar qualquer aspecto de um Incremento utilizável a cada Sprint.

Responsabilidades:
- criar o plano da Sprint;
- garantir qualidade pela Definition of Done;
- adaptar o plano diariamente;
- responsabilizar-se mutuamente como profissionais.

### 4.4 E o Gerente de Projetos?

O Scrum Guide **não define um papel de Project Manager dentro do Scrum Team**.

Isso não significa que a organização não possa ter gerente de projetos. Significa que ele não deve absorver artificialmente as responsabilidades de PO, Scrum Master ou Developers.

O gerente pode atuar em:
- governança;
- orçamento;
- riscos externos;
- integração entre áreas;
- dependências organizacionais;
- contratos;
- stakeholders;
- comunicação executiva;
- planejamento de portfólio;
- remoção de impedimentos fora da autoridade do time.

### Exemplo curto

O gerente identifica que uma nova demanda pode comprometer a Sprint. Ele **não estima tecnicamente** a demanda nem ordena sozinho o backlog. Ele reúne PO e Tech Lead/Developers, explicita impacto, risco, prazo e opções, e facilita uma decisão.

---

<a id="artefatos"></a>
## 5. Artefatos e compromissos

### 5.1 Product Backlog → Product Goal

O Product Backlog é uma lista emergente e ordenada do que é necessário para melhorar o produto.

Seu compromisso é o **Product Goal**, que descreve um estado futuro do produto que orienta o planejamento.

### 5.2 Sprint Backlog → Sprint Goal

O Sprint Backlog contém:
- Sprint Goal;
- itens selecionados do Product Backlog;
- plano para entregar o Incremento.

É criado e atualizado pelos Developers.

### 5.3 Increment → Definition of Done

Incremento é um passo concreto em direção ao Product Goal.

A **Definition of Done** define o estado de qualidade necessário para considerar trabalho parte do Incremento.

Se um item não atende à DoD, ele não deve ser apresentado como concluído.

### Ponto de prova

**Critério de aceitação** e **Definition of Done** não são a mesma coisa.

- Critério de aceitação: condições específicas de um item.
- DoD: padrão de qualidade comum aplicado ao Incremento.

---

<a id="eventos"></a>
## 6. Eventos do Scrum

### 6.1 Sprint

Container dos demais eventos. Tem duração fixa de um mês ou menos.

Durante a Sprint:
- não se fazem mudanças que coloquem o Sprint Goal em risco;
- qualidade não diminui;
- backlog pode ser refinado;
- escopo pode ser renegociado com o PO conforme aprendizado.

### 6.2 Sprint Planning

Responde a três tópicos:
1. Por que esta Sprint é valiosa?
2. O que pode ser feito?
3. Como o trabalho será realizado?

Resultado:
- Sprint Goal;
- itens selecionados;
- plano de execução.

### 6.3 Daily Scrum

Evento de 15 minutos para Developers inspecionarem o progresso rumo ao Sprint Goal e adaptarem o plano.

Não é reunião de status para o gerente.

### 6.4 Sprint Review

Inspeciona o resultado da Sprint com stakeholders e adapta o Product Backlog conforme novas informações.

Não deve ser apenas "demonstração para aprovação".

### 6.5 Sprint Retrospective

Inspeciona a forma de trabalho e define melhorias para aumentar qualidade e eficácia.

### 6.6 Refinamento

Refinamento é uma atividade contínua de decompor e detalhar itens do Product Backlog. **Não é um evento formal do Scrum Guide.**

---

<a id="iniciando"></a>
## 7. Iniciando o projeto

### 7.1 Business Case

Justifica por que vale a pena investir no projeto ou produto.

Perguntas:
- Qual problema resolvemos?
- Para quem?
- Qual benefício esperado?
- Qual custo?
- Quais riscos?
- Quais alternativas existem?

### 7.2 MVP

Minimum Viable Product é a menor solução capaz de gerar aprendizado validado sobre uma hipótese relevante.

MVP não significa "produto ruim" ou "produto incompleto de qualquer forma".

### 7.3 MMP

Minimum Marketable Product representa o menor conjunto de funcionalidades que pode ser colocado no mercado de forma suficientemente útil para um público-alvo.

Um MVP pode ser usado para aprender antes de um MMP comercial.

### 7.4 Visão do produto

Uma boa visão responde:
- público;
- problema;
- solução;
- diferencial;
- resultado esperado.

### 7.5 5W2H

- What
- Why
- Where
- When
- Who
- How
- How much

Útil para transformar uma intenção em plano operacional inicial.

### 7.6 Elevator Statement

Resumo curto da proposta de valor. Ajuda a alinhar entendimento entre stakeholders.

### 7.7 SMART

Uma meta tende a ser melhor quando é:
- Specific;
- Measurable;
- Achievable;
- Relevant;
- Time-bound.

No Scrum, o Sprint Goal não precisa ser artificialmente forçado a um template, mas SMART pode ajudar na clareza de metas organizacionais.

### 7.8 Stakeholders

Identifique:
- poder;
- interesse;
- impacto;
- influência;
- expectativas;
- estratégia de engajamento.

O objetivo não é apenas listar pessoas, mas planejar como cada grupo participará da tomada de decisão.

### 7.9 Formação do time e Tuckman

Estágios clássicos:
- Forming;
- Storming;
- Norming;
- Performing;
- Adjourning.

O gerente não deve interpretar conflito inicial como fracasso automático. Parte da evolução do time envolve aprender papéis, expectativas e formas de colaboração.

### 7.10 Roadmap e Release Planning

**Roadmap:** visão temporal de objetivos, resultados, temas ou grandes capacidades.

**Release Plan:** visão mais concreta de quando um conjunto de valor poderá ser disponibilizado.

Em ambientes ágeis, ambos devem ser tratados como planos adaptáveis, não como contratos imutáveis.

---

<a id="product-backlog"></a>
## 8. Criando o Product Backlog

### 8.1 Requisitos funcionais e não funcionais

**Funcionais:** descrevem comportamentos ou capacidades.

Exemplo:
> O cliente deve conseguir cancelar um agendamento.

**Não funcionais:** descrevem atributos de qualidade ou restrições.

Exemplo:
> 95% das requisições devem responder em menos de 500 ms.

### 8.2 Wireframes

Protótipos simplificados que ajudam a validar fluxo, estrutura e experiência antes de investir em implementação.

### 8.3 Personas

Representações de grupos de usuários baseadas em necessidades e comportamentos relevantes.

Evite personas puramente decorativas sem influência sobre decisões.

### 8.4 História de usuário

Formato comum:

> Como [persona], quero [capacidade], para [benefício].

O formato é ferramenta de conversa, não especificação completa.

### 8.5 INVEST

Uma história tende a ser melhor quando é:
- Independent;
- Negotiable;
- Valuable;
- Estimable;
- Small;
- Testable.

### 8.6 Épicos e temas

**Épico:** item grande demais para ser implementado como uma história comum e que precisa ser decomposto.

**Tema:** agrupamento conceitual de itens relacionados a um objetivo ou área.

### 8.7 DEEP

Um Product Backlog saudável tende a ser:
- Detailed appropriately;
- Emergent;
- Estimated;
- Prioritized/Ordered.

A ideia central é que itens próximos da execução possuam mais detalhe do que itens distantes.

---

<a id="priorizacao"></a>
## 9. Priorização e refinamento

### 9.1 Priorização simples

Ordenar por valor percebido, risco, urgência ou dependência.

### 9.2 MoSCoW

- Must have
- Should have
- Could have
- Won't have now

Risco: tudo virar "Must". É necessário impor disciplina.

### 9.3 100 pontos

Stakeholders distribuem um orçamento fictício de pontos entre funcionalidades. Ajuda a revelar valor relativo.

### 9.4 Buy a Feature

Participantes recebem orçamento limitado e "compram" funcionalidades. Força trade-offs.

### 9.5 User Story Mapping

Organiza histórias em uma jornada do usuário e permite visualizar fatias de release.

### 9.6 Valor x custo

Priorização considerando retorno e esforço/custo.

### 9.7 Valor x risco

Itens de alto valor e alto risco podem merecer validação antecipada para reduzir incerteza.

### 9.8 Pareto

Princípio 80/20 como heurística: pequena parte das causas pode produzir grande parte do efeito.

### 9.9 Kano

Categorias comuns:
- básicas;
- desempenho;
- encantamento.

Ajuda a discutir impacto na satisfação do cliente.

### 9.10 Refinamento

Objetivos:
- esclarecer;
- dividir;
- estimar;
- identificar dependências;
- verificar critérios;
- reduzir incerteza.

### 9.11 Spikes

Investigações timeboxed para reduzir incerteza técnica ou de produto.

Resultado de um spike é aprendizado, não necessariamente funcionalidade pronta.

---

<a id="estimativas"></a>
## 10. Estimativas e Sprint Planning

### 10.1 Capacidade

Representa disponibilidade real do time na Sprint.

É afetada por:
- férias;
- feriados;
- treinamentos;
- suporte;
- incidentes;
- outras responsabilidades.

### 10.2 Velocidade

Quantidade de trabalho, normalmente em pontos, concluída historicamente em Sprints.

Velocidade:
- é específica do time;
- não deve ser usada para comparar times;
- não é meta de produtividade;
- é evidência histórica para previsão.

### 10.3 Story Points

Medida relativa de tamanho/esforço percebido.

Pode considerar:
- complexidade;
- quantidade de trabalho;
- incerteza;
- risco.

Não existe conversão universal ponto → hora.

### 10.4 T-Shirt Sizing

Classificação relativa:
- XS;
- S;
- M;
- L;
- XL.

Útil em estimativas iniciais de alto nível.

### 10.5 Planning Poker

Cada participante estima individualmente. Divergências relevantes são discutidas. O objetivo é expor diferenças de entendimento, não produzir uma média matemática.

### 10.6 Affinity Estimation

Itens são agrupados por tamanho relativo, permitindo estimar muitos itens rapidamente.

### 10.7 Triangulação

Compara itens novos com itens de referência já conhecidos.

### 10.8 Magic Estimation

Técnica rápida e colaborativa para posicionar itens em valores relativos, discutindo exceções ou divergências.

### 10.9 Horas x pontos

**Horas:** medida temporal.

**Story Points:** medida relativa.

É possível usar horas para tarefas operacionais em determinados contextos, mas converter rigidamente pontos em horas destrói parte da utilidade da estimativa relativa.

### Exemplo curto

Velocidade média: 30 pontos.  
Sprint atual: duas pessoas de férias.

O gerente não deveria dizer "então façam 30 mesmo". A velocidade é evidência histórica; a capacidade específica da Sprint mudou.

---

<a id="ready-aceitacao-dod"></a>
## 11. Definition of Ready, Critério de Aceitação e Definition of Done

### Definition of Ready

Prática complementar, não elemento obrigatório do Scrum Guide.

Pode definir condições para um item estar suficientemente preparado para discussão ou seleção.

Exemplo:
- objetivo entendido;
- dependências principais conhecidas;
- critérios de aceitação discutidos;
- tamanho compatível com a Sprint.

Evite transformar Ready em burocracia que impede aprendizado.

### Critérios de aceitação

Condições específicas para considerar que uma história atende ao comportamento esperado.

Exemplo:

História:
> Como cliente, quero cancelar meu agendamento.

Critérios:
- permitir cancelamento até 2h antes;
- registrar data do cancelamento;
- liberar horário;
- notificar prestador.

### Definition of Done

Padrão comum de qualidade.

Exemplo:
- código revisado;
- testes automatizados passando;
- segurança validada;
- integração concluída;
- documentação necessária atualizada;
- deployável.

---

<a id="visao-gerente"></a>
## 12. Visão do Gerente de Projetos

### O gerente deve

- garantir visibilidade de riscos e dependências;
- apoiar decisões com dados;
- manter stakeholders alinhados;
- facilitar negociação de prazo, escopo e capacidade;
- proteger foco quando mudanças surgem;
- remover obstáculos organizacionais;
- acompanhar resultados e não apenas atividades.

### O gerente não deve

- substituir o Product Owner na prioridade;
- definir sozinho estimativas técnicas;
- distribuir tarefas para cada Developer;
- usar Daily como reunião de cobrança;
- aumentar velocidade artificialmente;
- declarar item "Done" sem atender à DoD;
- transformar roadmap em contrato imutável.

### Framework de decisão

Quando surgir uma demanda urgente:

1. Qual problema de negócio ela resolve?
2. Qual é a urgência real?
3. Qual o impacto no Sprint Goal?
4. O item está refinado?
5. Qual risco técnico existe?
6. Qual trabalho atual teria de ser interrompido?
7. Quem possui autoridade de prioridade?
8. Quais cenários podem ser apresentados?

---

<a id="mapa-mental"></a>
## 13. Mapa mental

```text
GESTÃO ÁGIL — FUNDAMENTOS
│
├── MINDSET
│   ├── incerteza
│   ├── feedback
│   ├── adaptação
│   ├── planejamento em camadas
│   └── valor
│
├── SCRUM
│   ├── empirismo
│   │   ├── transparência
│   │   ├── inspeção
│   │   └── adaptação
│   ├── PO
│   ├── Scrum Master
│   └── Developers
│
├── ARTEFATOS
│   ├── Product Backlog → Product Goal
│   ├── Sprint Backlog → Sprint Goal
│   └── Increment → Definition of Done
│
├── INICIAÇÃO
│   ├── Business Case
│   ├── MVP / MMP
│   ├── Visão
│   ├── Stakeholders
│   ├── Roadmap
│   └── Releases
│
├── BACKLOG
│   ├── Requisitos
│   ├── Histórias
│   ├── INVEST
│   ├── DEEP
│   ├── Priorização
│   ├── Refinamento
│   └── Spikes
│
└── ESTIMATIVAS
    ├── Capacidade
    ├── Velocidade
    ├── Story Points
    ├── Planning Poker
    ├── Afinidade
    ├── Triangulação
    └── Sprint Planning
```

---

<a id="revisao-ouvir"></a>
## 14. Revisão para ouvir

Agilidade é uma forma de trabalhar adequada a ambientes em que não conhecemos tudo antecipadamente. Em vez de depender apenas de um grande plano inicial, trabalhamos em ciclos, entregamos pequenas partes de valor, coletamos feedback e adaptamos o plano.

Scrum é um framework baseado em empirismo. Seus pilares são transparência, inspeção e adaptação. O Scrum Team possui Product Owner, Scrum Master e Developers. O Product Owner maximiza valor e gerencia o Product Backlog. O Scrum Master é responsável pela eficácia do Scrum. Os Developers criam o Incremento e organizam o trabalho necessário para atingir o Sprint Goal.

Os três artefatos possuem compromissos. Product Backlog se relaciona ao Product Goal. Sprint Backlog se relaciona ao Sprint Goal. Incremento se relaciona à Definition of Done.

Na iniciação, o gerente deve entender o problema, valor, stakeholders, riscos e objetivos. MVP existe para validar hipóteses com o mínimo investimento necessário. Roadmap comunica direção e não deve ser tratado como um contrato inflexível.

O Product Backlog é emergente. Itens próximos da execução precisam de mais detalhe. Histórias de usuário promovem conversa. INVEST ajuda a avaliar qualidade das histórias. DEEP ajuda a avaliar saúde do backlog.

Estimativas não são promessas. Story Points representam tamanho relativo e não equivalem diretamente a horas. Velocidade mostra comportamento histórico. Capacidade mostra disponibilidade específica. A Sprint Planning deve considerar objetivo, valor, capacidade, riscos e contexto.

O gerente de projetos usa essas informações para coordenar, comunicar e reduzir risco. Ele não deve substituir Product Owner, Scrum Master ou Developers.

---

<a id="exercicios"></a>
## 15. Exercícios

### 1.
Qual dos itens abaixo melhor descreve empirismo?

A. Planejar completamente antes de iniciar.  
B. Aprender com experiência e adaptar usando evidências.  
C. Eliminar qualquer documentação.  
D. Fazer entregas apenas quando o produto estiver completo.

### 2.
Quem é accountable pela ordenação do Product Backlog?

A. Gerente de Projetos.  
B. Scrum Master.  
C. Product Owner.  
D. Developers.

### 3.
A Daily Scrum é principalmente:

A. Reunião de status para o gerente.  
B. Reunião de aprovação do PO.  
C. Evento dos Developers para inspecionar progresso e adaptar o plano.  
D. Reunião de priorização do backlog.

### 4.
Qual afirmação sobre Story Points é mais adequada?

A. Um ponto sempre equivale a oito horas.  
B. Pontos devem permitir comparar produtividade entre times.  
C. Pontos representam tamanho relativo e dependem do contexto do time.  
D. Pontos eliminam incerteza.

### 5.
Um Head pede nova funcionalidade urgente no meio da Sprint. O primeiro passo de gestão deveria ser:

A. Mandar o time começar imediatamente.  
B. Excluir o item mais lento.  
C. Entender valor, urgência e impacto no Sprint Goal e envolver responsáveis.  
D. Aumentar a velocidade esperada.

### 6.
Qual diferença principal entre critério de aceitação e DoD?

A. Não existe diferença.  
B. Critério é específico do item; DoD é padrão comum de qualidade.  
C. DoD pertence ao cliente; critério pertence ao gerente.  
D. Critério vale apenas para bugs.

### 7.
Uma Sprint possui velocidade histórica de 40 pontos, mas 30% da capacidade ficará indisponível. A melhor atitude é:

A. Exigir 40 pontos porque velocidade é compromisso.  
B. Ignorar a capacidade.  
C. Usar velocidade como referência e ajustar previsão ao contexto.  
D. Converter pontos em horas.

### 8.
Qual item NÃO é evento formal do Scrum Guide?

A. Sprint Review.  
B. Sprint Retrospective.  
C. Refinamento do Product Backlog.  
D. Sprint Planning.

### 9.
Um spike é usado principalmente para:

A. Aumentar artificialmente a velocidade.  
B. Reduzir incerteza por investigação.  
C. Substituir retrospectiva.  
D. Criar documentação obrigatória.

### 10.
No Scrum, o Gerente de Projetos:

A. deve assumir as responsabilidades do Product Owner.  
B. deve atribuir tarefas a cada Developer.  
C. pode atuar em governança, riscos e stakeholders sem retirar accountabilities do Scrum Team.  
D. é o responsável oficial pela Daily Scrum.

---

<a id="gabarito"></a>
## 16. Gabarito comentado

1. **B** — empirismo usa observação, inspeção e adaptação.
2. **C** — Product Owner é accountable pelo gerenciamento eficaz do Product Backlog.
3. **C** — Daily Scrum existe para os Developers ajustarem o plano rumo ao Sprint Goal.
4. **C** — Story Points são relativos e não possuem conversão universal para tempo.
5. **C** — antes de agir, explicite valor, urgência, impacto, risco e autoridade de decisão.
6. **B** — aceitação é específica; DoD é padrão comum do Incremento.
7. **C** — velocidade é evidência histórica, não obrigação.
8. **C** — refinamento é atividade contínua.
9. **B** — spike reduz incerteza.
10. **C** — a organização pode ter gerente, mas ele não substitui as accountabilities do Scrum.

---

<a id="fontes"></a>
## 17. Fontes

- Scrum Guide — versão oficial vigente: https://scrumguides.org/download.html
- Ementa pública do curso Udemy: https://www.udemy.com/course/gestao-de-projetos-com-agile-scrum-o-guia-definitivo/
