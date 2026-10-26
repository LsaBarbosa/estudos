# Fase 5 — Preparação Profissional: Estudos de Caso, Tomada de Decisão, Prova e Entrevista

> Objetivo: transformar conhecimento conceitual em respostas de Gerente de Projetos.

## Índice

- [1. Como responder como gerente](#como-responder)
- [2. Framework de decisão](#framework)
- [3. Caso 1 — Nova demanda durante a Sprint](#caso1)
- [4. Caso 2 — Sprint atrasada](#caso2)
- [5. Caso 3 — Dependência externa](#caso3)
- [6. Caso 4 — Risco técnico crítico](#caso4)
- [7. Caso 5 — Conflito PO x Tech Lead](#caso5)
- [8. Caso 6 — Mudança de prazo executivo](#caso6)
- [9. Caso 7 — Time com baixa previsibilidade](#caso7)
- [10. Caso 8 — Gargalo de fluxo](#caso8)
- [11. Caso 9 — Incidente em produção](#caso9)
- [12. Caso 10 — Vários times](#caso10)
- [13. Perguntas de entrevista](#entrevista)
- [14. Simulado final](#simulado)
- [15. Gabarito](#gabarito)
- [16. Checklist de prova](#checklist)
- [17. Mapa mental final](#mapa-mental)
- [18. Revisão para ouvir](#revisao-ouvir)

<a id="como-responder"></a>
## 1. Como responder como gerente

Uma resposta forte de gestão normalmente apresenta:

1. **objetivo**;
2. **fatos conhecidos**;
3. **riscos**;
4. **responsáveis corretos**;
5. **opções**;
6. **trade-offs**;
7. **decisão ou próximo passo**;
8. **comunicação e acompanhamento**.

Evite respostas como:
> "Eu mandaria o time..."

Prefira:
> "Eu primeiro validaria impacto e urgência, envolveria PO para prioridade e time técnico para esforço e risco, apresentaria cenários e garantiria que a decisão e suas consequências fossem explícitas."

---

<a id="framework"></a>
## 2. Framework de decisão

Use **VIRADA**:

### V — Valor

Qual valor ou problema está em jogo?

### I — Impacto

Prazo, custo, escopo, qualidade, cliente, segurança, compliance.

### R — Risco

O que pode acontecer se fizermos ou não fizermos?

### A — Autoridade

Quem realmente possui a decisão?

### D — Decisão

Quais opções e trade-offs existem?

### A — Acompanhamento

Quem executa, qual prazo, qual métrica e quando revisar?

---

<a id="caso1"></a>
## 3. Caso 1 — Nova demanda durante a Sprint

### Cenário

Sprint Goal:
> Reduzir tempo de carregamento da tela de chamados.

Head pede:
> Filtro de prioridade alta, média e baixa.

### Resposta esperada

1. validar valor e urgência com Head/PO;
2. verificar se item está refinado;
3. pedir avaliação técnica aos Developers/Tech Lead;
4. avaliar impacto sobre Sprint Goal;
5. comparar:
   - manter Sprint atual;
   - substituir parte do escopo;
   - cancelar Sprint em situação extrema e por decisão do PO;
   - planejar para próxima Sprint;
6. registrar decisão e impacto.

### O gerente NÃO deve

- definir detalhes da query;
- decidir índice de banco;
- escrever critérios técnicos;
- estimar sozinho;
- impor prioridade ao PO.

---

<a id="caso2"></a>
## 4. Caso 2 — Sprint atrasada

### Cenário

No oitavo dia de uma Sprint de dez dias:
- 60% dos itens ainda estão em andamento;
- vários itens dependem de teste;
- burndown ficou horizontal.

### Diagnóstico

Não conclua imediatamente "o time está lento".

Pergunte:
- existe WIP excessivo?
- há gargalo em teste?
- itens foram grandes demais?
- houve bloqueio?
- escopo mudou?
- DoD é cumprida apenas no final?

### Ação

- focar em terminar, não começar;
- remover bloqueios;
- renegociar escopo com PO sem comprometer qualidade;
- aprender na retrospectiva;
- ajustar refinamento e WIP.

---

<a id="caso3"></a>
## 5. Caso 3 — Dependência externa

### Cenário

Fornecedor precisa liberar API em 20 dias. Release ocorre em 25.

### Risco

Margem de apenas cinco dias.

### Resposta

- registrar risco;
- obter data e evidência do fornecedor;
- criar mock/sandbox;
- definir ponto de escalonamento;
- negociar entrega parcial;
- acompanhar frequentemente;
- preparar contingência.

### Comunicação executiva

> "A integração externa possui apenas cinco dias de margem. Criamos mock para não bloquear desenvolvimento, mas o go-live depende da API real. Se não houver acesso até D-10, recomendaremos reduzir escopo ou mover o release."

---

<a id="caso4"></a>
## 6. Caso 4 — Risco técnico crítico

### Cenário

Tech Lead identifica que arquitetura atual pode causar perda de dados em pico.

PO quer manter lançamento.

### Papel do gerente

- tornar risco compreensível;
- quantificar impacto;
- pedir alternativas técnicas;
- separar "precisa corrigir tudo" de mitigação mínima;
- envolver autoridade de risco;
- registrar decisão.

### Possíveis opções

- corrigir antes;
- limitar tráfego;
- feature flag;
- piloto;
- monitoramento adicional;
- adiar.

---

<a id="caso5"></a>
## 7. Caso 5 — Conflito PO x Tech Lead

### Princípio

PO possui autoridade de prioridade e valor de produto.  
Tech Lead/Developers possuem responsabilidade sobre implementação e qualidade técnica.

Nenhum deveria simplesmente "mandar" no domínio do outro.

### Gerente

Facilita uma decisão baseada em:
- impacto;
- risco;
- custo;
- prazo;
- alternativas.

---

<a id="caso6"></a>
## 8. Caso 6 — Mudança de prazo executivo

### Cenário

Data mudou de 60 para 30 dias.

### Resposta fraca

> "Vamos acelerar."

### Resposta forte

A restrição de prazo mudou. Reavalie:
- escopo;
- capacidade;
- dependências;
- risco;
- qualidade mínima;
- possibilidade de release incremental.

Monte cenários:

| Cenário | Prazo | Escopo | Risco |
|---|---|---|---|
| A | 30 dias | MVP | Médio |
| B | 45 dias | Core + extra | Médio |
| C | 30 dias | Escopo completo | Muito alto |

A decisão vira um trade-off visível.

---

<a id="caso7"></a>
## 9. Caso 7 — Time com baixa previsibilidade

### Sintomas

- muita variação de throughput;
- itens grandes;
- bloqueios;
- WIP alto;
- demandas urgentes;
- dependências frequentes.

### Intervenção

Não defina "meta de velocidade".

Trabalhe em:
- itens menores;
- políticas de urgência;
- WIP;
- remoção de bloqueios;
- refinamento;
- dados históricos;
- gestão de dependências.

---

<a id="caso8"></a>
## 10. Caso 8 — Gargalo de fluxo

### Cenário

Desenvolvimento: 2 itens.  
Code Review: 11 itens.  
Teste: 1 item.

### Leitura

Gargalo em Code Review.

### Ação

- pausar início de novo desenvolvimento;
- mobilizar capacidade para review;
- revisar política;
- verificar concentração de conhecimento;
- automatizar validações;
- medir efeito.

Otimização local de desenvolvimento pioraria o sistema.

---

<a id="caso9"></a>
## 11. Caso 9 — Incidente em produção

### Primeiros objetivos

1. proteger cliente e dados;
2. conter impacto;
3. estabilizar;
4. comunicar;
5. recuperar;
6. investigar causa raiz depois.

### Papel do gerente

- coordenar comunicação;
- garantir dono do incidente;
- registrar decisões;
- remover dependências;
- atualizar stakeholders;
- não atrapalhar resposta técnica.

### Pós-incidente

Blameless postmortem:
- linha do tempo;
- causa;
- fatores contribuintes;
- detecção;
- resposta;
- ações;
- responsáveis;
- prazos.

---

<a id="caso10"></a>
## 12. Caso 10 — Vários times

### Cenário

Três Scrum Teams dependem de um time de plataforma para todo deploy.

### Problema

Gargalo estrutural.

### Curto prazo

- priorizar fila;
- transparência;
- SLE;
- coordenação.

### Longo prazo

- self-service;
- automação;
- platform engineering;
- guardrails;
- reduzir dependência central.

Gerente deve tratar causa sistêmica, não apenas agendar mais reuniões.

---

<a id="entrevista"></a>
## 13. Perguntas de entrevista

### 1. Como você reage a mudança de escopo?

Resposta forte:
> Primeiro avalio valor, urgência e impacto. Em Scrum, envolvo o Product Owner na decisão de prioridade e o time na análise de esforço e risco. Apresento cenários de prazo, escopo e risco, registro a decisão e atualizo stakeholders.

### 2. Como mede sucesso?

> Uso métricas de resultado e fluxo, não apenas atividade. Dependendo do projeto, observo objetivo de negócio, valor entregue, lead time, previsibilidade, qualidade, risco e satisfação do stakeholder.

### 3. Como trata atraso?

> Primeiro determino a causa. Atraso pode vir de WIP, dependências, escopo, estimativa, qualidade ou capacidade. Depois trabalho em opções e trade-offs, evitando resolver prazo sacrificando qualidade silenciosamente.

### 4. Como lida com conflito?

> Separo posição de interesse, torno fatos e riscos visíveis, identifico autoridade de decisão e facilito alternativas. Escalo apenas quando o grupo não possui autoridade ou o risco excede seu limite.

### 5. Como atua com Scrum sem ser Scrum Master?

> Respeito as accountabilities do Scrum. Posso atuar em governança, stakeholders, orçamento, dependências e riscos organizacionais sem transformar Daily em status nem assumir backlog do PO.

### 6. Como lida com estimativa?

> Estimativa é produzida pelo time que realizará o trabalho. Uso histórico, capacidade e incerteza para previsão, e evito converter Story Points diretamente em horas.

### 7. Como lida com prioridade técnica?

> Débito e risco técnico precisam ser traduzidos em impacto de negócio. PO decide prioridade de produto, mas qualidade e segurança não devem ser tratadas como opcionais. Facilito a decisão com evidências e alternativas.

### 8. Como gerencia Kanban?

> Visualizo o fluxo, limito WIP, acompanho lead time, cycle time, throughput e aging, e trabalho no gargalo do sistema.

### 9. Como trabalha em escala?

> Busco reduzir dependências, criar objetivos comuns, integrar frequentemente e medir fluxo do produto. Evito comparar velocidade entre times.

### 10. Qual principal função do gerente ágil?

> Aumentar capacidade do sistema de entregar valor previsivelmente por meio de alinhamento, gestão de risco, facilitação, governança e remoção de impedimentos organizacionais.

---

<a id="simulado"></a>
## 14. Simulado final

### 1.
Uma Sprint está atrasada. A melhor primeira ação é:
A. aumentar horas extras  
B. identificar causa e impacto antes de escolher ação  
C. reduzir DoD  
D. trocar o Scrum Master

### 2.
Nova demanda urgente entra no meio da Sprint. Prioridade é responsabilidade principal de:
A. gerente  
B. PO  
C. Developer mais sênior  
D. QA

### 3.
WIP elevado normalmente:
A. reduz filas  
B. aumenta multitarefa e lead time  
C. aumenta qualidade automaticamente  
D. elimina gargalos

### 4.
Story Points:
A. são horas codificadas  
B. servem para ranking  
C. são medida relativa  
D. são KPI de RH

### 5.
Um risco já ocorreu. Ele agora é:
A. issue/impedimento  
B. roadmap  
C. épico  
D. persona

### 6.
Sprint Review serve para:
A. avaliar pessoas  
B. inspecionar incremento e adaptar próximos passos  
C. estimar salários  
D. substituir retrospectiva

### 7.
Retrospectiva busca:
A. melhoria do sistema de trabalho  
B. aprovação do cliente  
C. priorização executiva  
D. definição do Product Goal

### 8.
Em Kanban, Pull significa:
A. iniciar tudo  
B. puxar novo trabalho quando houver capacidade  
C. empurrar trabalho para QA  
D. remover WIP

### 9.
CFD ajuda a identificar:
A. salário  
B. gargalos e acúmulo  
C. persona  
D. orçamento

### 10.
Burnup é particularmente útil quando:
A. escopo muda  
B. não há backlog  
C. não há Sprint  
D. time não usa quadro

### 11.
Nexus é voltado a:
A. um único Scrum Team  
B. múltiplos Scrum Teams em um produto  
C. gestão financeira  
D. suporte individual

### 12.
Integrated Increment:
A. deve funcionar como produto integrado  
B. é soma de apresentações  
C. é roadmap  
D. é plano de testes

### 13.
Boa comunicação de risco contém:
A. só problema  
B. problema, impacto, opções e decisão necessária  
C. detalhes irrelevantes  
D. culpado

### 14.
Gerente deve atribuir tarefas no Daily?
A. sempre  
B. não; Daily pertence aos Developers para adaptação do plano  
C. apenas segunda-feira  
D. apenas em Kanban

### 15.
MVP serve principalmente para:
A. entregar baixa qualidade  
B. validar hipótese com investimento mínimo adequado  
C. evitar clientes  
D. congelar escopo

### 16.
DoD é:
A. padrão comum de qualidade  
B. critério específico de uma história apenas  
C. estimativa  
D. risco

### 17.
Um gerente percebe gargalo em Code Review. Melhor atitude:
A. iniciar mais desenvolvimento  
B. ajudar a liberar o gargalo  
C. aumentar backlog  
D. esconder CFD

### 18.
Comparar velocidade de dois times:
A. é uma boa medida de performance  
B. é inadequado porque escalas são locais  
C. é obrigatório  
D. mede valor

### 19.
Contrato ágil:
A. pode incorporar adaptação e checkpoints  
B. elimina obrigações  
C. proíbe orçamento  
D. elimina aceite

### 20.
A prioridade de um gerente diante de conflito entre áreas é:
A. escolher lado rapidamente  
B. tornar interesses, riscos, autoridade e opções claros  
C. evitar toda discussão  
D. aumentar reunião

---

<a id="gabarito"></a>
## 15. Gabarito

1. B  
2. B  
3. B  
4. C  
5. A  
6. B  
7. A  
8. B  
9. B  
10. A  
11. B  
12. A  
13. B  
14. B  
15. B  
16. A  
17. B  
18. B  
19. A  
20. B

### Faixa de desempenho

- **18–20:** domínio muito bom.
- **15–17:** bom; revisar pontos específicos.
- **11–14:** base razoável; reforçar aplicação.
- **0–10:** revisar Fases 1–4 antes de simulações avançadas.

---

<a id="checklist"></a>
## 16. Checklist de prova

Antes da prova/entrevista, confirme que consegue explicar sem consultar material:

- [ ] Manifesto Ágil e empirismo
- [ ] Papéis/accountabilities do Scrum
- [ ] Artefatos e compromissos
- [ ] Eventos do Scrum
- [ ] MVP x MMP
- [ ] Roadmap x Release Plan
- [ ] História de usuário e INVEST
- [ ] DEEP
- [ ] Priorização
- [ ] Story Points
- [ ] Velocidade x capacidade
- [ ] DoR x critérios de aceitação x DoD
- [ ] Burndown x Burnup x CFD
- [ ] WIP e Pull
- [ ] Lead Time x Cycle Time x Throughput
- [ ] Risco x impedimento
- [ ] Liderança e conflito
- [ ] Stakeholders
- [ ] Contratos ágeis
- [ ] Nexus e dependências
- [ ] Papel do gerente em ambiente Scrum

---

<a id="mapa-mental"></a>
## 17. Mapa mental final

```text
GERENTE DE PROJETOS ÁGIL
│
├── VALOR
│   ├── visão
│   ├── Product Goal
│   ├── MVP
│   └── prioridade
│
├── ENTREGA
│   ├── Sprint
│   ├── Incremento
│   ├── DoD
│   └── Release
│
├── FLUXO
│   ├── WIP
│   ├── Pull
│   ├── Lead Time
│   ├── Cycle Time
│   └── Throughput
│
├── RISCO
│   ├── identificar
│   ├── analisar
│   ├── responder
│   └── escalar
│
├── PESSOAS
│   ├── liderança
│   ├── comunicação
│   ├── facilitação
│   └── conflito
│
├── ESCALA
│   ├── Nexus
│   ├── dependências
│   ├── integração
│   └── governança
│
└── DECISÃO
    ├── valor
    ├── impacto
    ├── risco
    ├── autoridade
    ├── opções
    └── acompanhamento
```

---

<a id="revisao-ouvir"></a>
## 18. Revisão para ouvir

O gerente de projetos ágil não é a pessoa que decide tudo. Seu papel é criar clareza para que as decisões sejam tomadas por quem possui autoridade e conhecimento apropriados.

Quando surgir uma mudança, avalie valor, impacto, risco e responsáveis. Crie cenários. Não esconda trade-offs.

No Scrum, Product Owner responde por valor e Product Backlog. Scrum Master responde pela eficácia do Scrum. Developers criam e adaptam o plano para entregar o Incremento. O gerente pode cuidar de riscos, orçamento, stakeholders, governança e dependências sem absorver essas accountabilities.

Em execução, procure terminar antes de começar mais trabalho. WIP alto aumenta filas. Métricas de fluxo ajudam a entender o sistema.

Em conflito, não reduza a conversa a quem "manda". Identifique interesses, riscos e autoridade. Em escala, evite otimização local e trabalhe para reduzir dependências e integrar continuamente.

Uma resposta madura de gerente sempre torna explícitos o objetivo, os fatos, o impacto, o risco, as alternativas, a autoridade da decisão e o acompanhamento.
