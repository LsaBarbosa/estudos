# Fase 2 — Execução, Monitoramento, Kanban e Gestão de Riscos

> Objetivo: aprender a conduzir a execução, detectar desvios cedo, gerenciar fluxo e tornar riscos visíveis.

## Índice

- [1. Executando a Sprint](#executando)
- [2. Incremento e qualidade](#incremento)
- [3. Radiadores de informação e Task Board](#radiadores)
- [4. Impedimentos, bugs e dívida técnica](#impedimentos)
- [5. Integração Contínua e refatoração](#ci)
- [6. Monitorando progresso](#monitorando)
- [7. Burndown, Burnup e CFD](#graficos)
- [8. Fundamentos de Kanban](#kanban)
- [9. WIP, Pull e fluxo](#wip)
- [10. Métricas de fluxo](#metricas)
- [11. Gestão de riscos em ambiente ágil](#riscos)
- [12. Visão do Gerente de Projetos](#visao-gerente)
- [13. Estudo de caso](#caso)
- [14. Mapa mental](#mapa-mental)
- [15. Revisão para ouvir](#revisao-ouvir)
- [16. Exercícios](#exercicios)
- [17. Gabarito](#gabarito)
- [18. Fontes](#fontes)

<a id="executando"></a>
## 1. Executando a Sprint

Durante a Sprint, o foco é transformar itens selecionados em um Incremento utilizável sem perder de vista o Sprint Goal.

A gestão deve observar:
- progresso em direção ao objetivo;
- impedimentos;
- dependências;
- qualidade;
- carga de trabalho;
- riscos emergentes;
- mudanças de contexto.

Erro comum: acompanhar apenas percentual de tarefas concluídas. O que importa é a capacidade de entregar valor e atingir o objetivo.

---

<a id="incremento"></a>
## 2. Incremento e qualidade

Incremento é trabalho integrado que atende à Definition of Done.

Não basta:
- código compilando;
- tela "funcionando na máquina";
- tarefa marcada como concluída;
- PR aberto.

Um incremento deve estar em condição utilizável.

### Defeito escapado

Defeito escapado é um problema que passa pelas barreiras internas de qualidade e chega a ambiente posterior ou ao cliente.

O gerente deve acompanhar tendência e impacto, não usar a métrica para culpabilizar indivíduos.

### Exemplo

Se o time entrega 20 histórias por Sprint, mas metade retorna por defeitos, a velocidade aparente não representa entrega de valor sustentável.

---

<a id="radiadores"></a>
## 3. Radiadores de informação e Task Board

Radiador de informação é qualquer visualização que torne o estado do trabalho facilmente compreensível.

Exemplos:
- quadro Scrum/Kanban;
- burndown;
- burnup;
- CFD;
- painel de riscos;
- indicadores de incidentes.

Um bom quadro mostra:
- trabalho a fazer;
- trabalho em andamento;
- bloqueios;
- trabalho concluído;
- políticas relevantes.

### Daily Scrum

Use o quadro para apoiar a conversa sobre:
- estamos progredindo rumo ao Sprint Goal?
- o que está bloqueado?
- o plano precisa ser adaptado?

Evite transformar o quadro em sistema de microgestão individual.

---

<a id="impedimentos"></a>
## 4. Impedimentos, bugs e dívida técnica

### Impedimento

Algo que reduz ou impede a capacidade do time de progredir.

Exemplos:
- acesso pendente;
- dependência externa;
- ambiente indisponível;
- decisão de negócio sem resposta;
- ferramenta quebrada.

Um backlog de impedimentos pode registrar:
- descrição;
- responsável pela tratativa;
- data;
- impacto;
- ação;
- status.

### Bugs

Bug deve ser tratado conforme impacto e prioridade. Não existe uma regra universal de "todo bug entra imediatamente na Sprint".

Perguntas:
- afeta produção?
- existe risco financeiro ou regulatório?
- compromete o Sprint Goal?
- pode esperar?
- deve gerar item no Product Backlog?

### Dívida técnica

É o custo futuro criado por decisões técnicas que aumentam velocidade no curto prazo ou surgem de limitações acumuladas.

Exemplos:
- testes ausentes;
- código duplicado;
- dependências obsoletas;
- arquitetura frágil;
- scripts manuais;
- documentação crítica ausente.

O gerente não decide sozinho como corrigir a dívida, mas deve tornar custo e risco visíveis.

---

<a id="ci"></a>
## 5. Integração Contínua e refatoração

### Integração Contínua

Prática de integrar mudanças frequentemente e validar automaticamente o software.

Objetivos:
- reduzir conflito de integração;
- detectar defeitos cedo;
- manter produto em estado integrável.

### Refatoração

Melhoria da estrutura interna do código sem alteração intencional do comportamento externo.

Gestão deve entender que refatoração pode ser necessária para manter capacidade futura de entrega. Tratar toda melhoria técnica como "trabalho sem valor" cria degradação progressiva.

---

<a id="monitorando"></a>
## 6. Monitorando progresso

O objetivo do monitoramento não é produzir relatórios bonitos, mas permitir decisão.

Um indicador útil responde:
- estamos avançando?
- com que previsibilidade?
- onde está o gargalo?
- qual risco está aumentando?
- precisamos adaptar plano ou prioridade?

### Indicadores de atividade x resultado

Atividade:
- quantidade de reuniões;
- quantidade de tarefas;
- horas trabalhadas.

Resultado:
- incremento entregue;
- objetivo atingido;
- lead time reduzido;
- satisfação melhorada;
- risco mitigado.

Prefira indicadores de resultado e fluxo.

---

<a id="graficos"></a>
## 7. Burndown, Burnup e CFD

### 7.1 Sprint Burndown

Mostra trabalho restante ao longo da Sprint.

Interpretações:
- linha quase horizontal → trabalho não está sendo concluído ou atualizado;
- queda brusca no fim → trabalho acumulado ou atualização tardia;
- aumento → escopo adicionado/reestimado;
- queda rápida → itens concluídos antecipadamente.

Não use como prova isolada de sucesso.

### 7.2 Product Burndown

Mostra trabalho restante em relação a um horizonte maior.

Problema: mudanças de escopo podem tornar leitura menos intuitiva.

### 7.3 Burnup

Mostra:
- trabalho concluído;
- escopo total.

É especialmente útil quando o escopo muda, pois torna a alteração visível.

### 7.4 Cumulative Flow Diagram — CFD

Mostra quantidade de itens em cada estado ao longo do tempo.

Faixa crescendo em uma etapa pode indicar gargalo.

Exemplo:
- "Em teste" cresce continuamente.
- "Concluído" cresce pouco.

Hipótese: capacidade de teste está abaixo da taxa de entrada.

---

<a id="kanban"></a>
## 8. Fundamentos de Kanban

Kanban é um método de gestão aplicado sobre uma forma de trabalho existente. Não exige substituir Scrum ou toda a estrutura atual.

### Princípios de mudança

- Comece com o que você faz hoje.
- Busque melhoria por mudança evolutiva.
- Incentive liderança em todos os níveis.

### Princípios de entrega de serviço

- Entenda e foque necessidades e expectativas do cliente.
- Gerencie o trabalho e permita que as pessoas se organizem em torno dele.
- Revise regularmente a rede de serviços e suas políticas.

### Seis práticas gerais

1. Visualizar.
2. Limitar WIP.
3. Gerenciar fluxo.
4. Tornar políticas explícitas.
5. Implementar ciclos de feedback.
6. Melhorar colaborativamente e evoluir experimentalmente.

---

<a id="wip"></a>
## 9. WIP, Pull e fluxo

### WIP

Work in Progress é a quantidade de trabalho iniciado e ainda não concluído.

Muito WIP tende a gerar:
- multitarefa;
- espera;
- filas;
- perda de foco;
- lead time maior;
- previsibilidade menor.

### Limite de WIP

Exemplo:

| Etapa | Limite |
|---|---:|
| Desenvolvimento | 3 |
| Code Review | 2 |
| Teste | 2 |

Se Teste já possui 2 itens, o time não deveria simplesmente empurrar um terceiro para a coluna. Deve colaborar para liberar fluxo.

### Pull

Novo trabalho é puxado quando existe capacidade.

Diferença conceitual:
- **Push:** trabalho é empurrado para a próxima etapa.
- **Pull:** próxima etapa puxa trabalho quando tem capacidade.

### Políticas explícitas

Exemplos:
- o que significa "Ready para teste";
- quando um item pode entrar como urgente;
- WIP por coluna;
- critérios de bloqueio;
- Definition of Done.

Política explícita reduz decisões arbitrárias.

---

<a id="metricas"></a>
## 10. Métricas de fluxo

### Lead Time

Tempo desde um ponto de compromisso/entrada até a entrega.

Pergunta:
> Quanto tempo o cliente espera?

### Cycle Time

Tempo de processamento dentro de um recorte específico do fluxo.

Pergunta:
> Quanto tempo o item leva do início efetivo ao fim?

As definições exatas dependem dos pontos escolhidos pelo sistema. O importante é declarar claramente os limites usados.

### Throughput

Quantidade de itens concluídos por período.

Exemplo:
> 18 itens por semana.

### WIP

Quantidade de itens em andamento em determinado momento.

### Aging WIP

Idade dos itens que ainda estão em andamento.

Ajuda a detectar itens que estão envelhecendo antes de ultrapassarem expectativas.

### Lei de Little

Em um sistema relativamente estável:

`WIP ≈ Throughput × Cycle Time`

Interpretação gerencial: aumentar continuamente WIP sem aumentar capacidade tende a elevar tempo de entrega.

### Service Level Expectation — SLE

Expectativa probabilística sobre tempo de entrega com base em dados históricos.

Exemplo:
> 85% dos itens deste tipo são concluídos em até 8 dias.

Isso é mais informativo que prometer que todo item leva exatamente 8 dias.

---

<a id="riscos"></a>
## 11. Gestão de riscos em ambiente ágil

Agilidade não elimina gestão de riscos. Ela muda a forma.

### 11.1 Identificação

Fontes:
- tecnologia;
- fornecedor;
- segurança;
- compliance;
- prazo;
- orçamento;
- pessoas;
- dependências;
- mercado;
- requisitos.

### 11.2 Registro

Formato simples:

| Risco | Probabilidade | Impacto | Resposta | Dono |
|---|---|---|---|---|
| API externa atrasar | Alta | Alto | Mock + escalonamento | Integração |

### 11.3 Estratégias

Para ameaças:
- evitar;
- mitigar;
- transferir;
- aceitar.

Para oportunidades:
- explorar;
- melhorar;
- compartilhar;
- aceitar.

### 11.4 Risco x impedimento

**Risco:** evento futuro incerto.

**Impedimento/issue:** problema já ocorrido.

### 11.5 Risco no backlog

Itens de alto risco podem ser antecipados.

Exemplo:
Se uma integração crítica é incerta, validar cedo pode ser mais inteligente que deixar para a última Sprint.

### 11.6 Risk Burndown

Acompanha redução da exposição total ao risco ao longo do tempo.

Pode combinar probabilidade e impacto em um score, desde que a equipe entenda que é uma simplificação.

---

<a id="visao-gerente"></a>
## 12. Visão do Gerente de Projetos

Durante execução, o gerente deve buscar **sinais antecipados**, não apenas atraso consolidado.

Perguntas diárias:
- existe item envelhecendo?
- existe bloqueio externo?
- a fila está aumentando?
- qualidade está piorando?
- o Sprint Goal ainda é viável?
- algum risco mudou?
- precisamos renegociar expectativa?

### Anti-padrões

- cobrar 100% de utilização das pessoas;
- iniciar tudo para mostrar progresso;
- medir produtividade por número de tickets;
- punir quem sinaliza bloqueio;
- usar métricas de fluxo para ranking individual;
- esconder mudança de escopo.

---

<a id="caso"></a>
## 13. Estudo de caso

### Cenário

Time de 6 pessoas.  
WIP em Desenvolvimento: 6.  
WIP em Teste: 9.  
Throughput caiu de 18 para 11 itens/semana.  
Itens em Teste possuem aging elevado.

### Diagnóstico

O problema não parece ser "desenvolvimento lento". Existe acúmulo na etapa de teste.

### Resposta inadequada

> "Desenvolvedores precisam codificar mais rápido."

Isso aumenta ainda mais a fila.

### Resposta adequada

1. tornar o gargalo visível;
2. limitar entrada em Teste;
3. colaborar para reduzir fila;
4. verificar automação e capacidade de teste;
5. revisar políticas;
6. medir ciclo após a mudança.

### Decisão gerencial

O gerente coordena remoção de impedimentos organizacionais, ajusta expectativa externa e evita iniciar mais trabalho enquanto o sistema está congestionado.

---

<a id="mapa-mental"></a>
## 14. Mapa mental

```text
EXECUÇÃO E FLUXO
│
├── SPRINT
│   ├── Sprint Goal
│   ├── Incremento
│   ├── Daily
│   └── adaptação
│
├── QUALIDADE
│   ├── DoD
│   ├── bugs
│   ├── defeitos escapados
│   ├── dívida técnica
│   ├── CI
│   └── refatoração
│
├── MONITORAMENTO
│   ├── Burndown
│   ├── Burnup
│   └── CFD
│
├── KANBAN
│   ├── visualizar
│   ├── limitar WIP
│   ├── fluxo
│   ├── políticas
│   ├── feedback
│   └── melhoria evolutiva
│
├── MÉTRICAS
│   ├── Lead Time
│   ├── Cycle Time
│   ├── Throughput
│   ├── WIP
│   └── Aging WIP
│
└── RISCOS
    ├── identificar
    ├── analisar
    ├── responder
    └── acompanhar
```

---

<a id="revisao-ouvir"></a>
## 15. Revisão para ouvir

Durante a Sprint, o objetivo não é manter todas as pessoas ocupadas. O objetivo é transformar trabalho em valor e atingir o Sprint Goal. Trabalho iniciado e não concluído gera filas, contexto e risco.

Radiadores de informação devem tornar o estado visível. Burndown mostra trabalho restante. Burnup mostra trabalho concluído e escopo total. CFD mostra acúmulo em etapas do fluxo.

Kanban é um método de gestão que pode ser aplicado sobre a forma de trabalho atual. As práticas centrais são visualizar, limitar WIP, gerenciar fluxo, tornar políticas explícitas, criar feedback e melhorar de forma evolutiva.

Lead time mede tempo de entrega segundo pontos definidos. Cycle time mede um recorte do processamento. Throughput mede quantos itens terminam por período. Aging WIP mostra há quanto tempo itens ainda abertos permanecem no sistema.

Risco é um evento futuro incerto. Impedimento é um problema já presente. O gerente deve tornar riscos, bloqueios e dependências visíveis cedo, coordenar respostas e proteger o sistema contra excesso de trabalho em andamento.

---

<a id="exercicios"></a>
## 16. Exercícios

1. Um CFD mostra crescimento contínuo da faixa "Teste". O que isso sugere?
   - A) aumento automático de qualidade
   - B) possível gargalo em teste
   - C) aumento de velocidade
   - D) redução do WIP

2. Limitar WIP busca principalmente:
   - A) deixar pessoas ociosas
   - B) reduzir fluxo
   - C) controlar trabalho em andamento e melhorar fluxo
   - D) aumentar multitarefa

3. Lead time responde melhor a:
   - A) quanto tempo o cliente espera
   - B) quantas pessoas trabalham
   - C) quantas reuniões existem
   - D) quantos pontos há

4. Se uma coluna atingiu seu WIP, o comportamento mais alinhado a Kanban é:
   - A) empurrar mais itens
   - B) colaborar para liberar fluxo antes de puxar novo trabalho
   - C) remover o limite
   - D) esconder o bloqueio

5. Um risco virou realidade. Agora ele deve ser tratado principalmente como:
   - A) oportunidade
   - B) impedimento/issue
   - C) estimativa
   - D) épico

6. Uma equipe apresenta muito trabalho "90% pronto". O principal risco é:
   - A) WIP excessivo e baixa entrega real
   - B) excesso de retrospectivas
   - C) backlog pequeno
   - D) poucos stakeholders

7. Burnup é útil em ambientes com mudança de escopo porque:
   - A) oculta mudanças
   - B) mostra progresso e escopo total separadamente
   - C) substitui o Product Backlog
   - D) mede satisfação

8. Dívida técnica deve ser:
   - A) sempre ignorada
   - B) tratada apenas quando produção cair
   - C) tornada visível e equilibrada com valor e risco
   - D) definida pelo gerente sem time técnico

9. Throughput mede:
   - A) itens concluídos por unidade de tempo
   - B) horas por pessoa
   - C) tamanho do backlog
   - D) custo de projeto

10. A melhor reação gerencial a um gargalo é:
   - A) aumentar entrada de trabalho
   - B) analisar fluxo, reduzir fila e remover causas
   - C) cobrar indivíduos
   - D) criar mais status meetings

---

<a id="gabarito"></a>
## 17. Gabarito

1. **B**
2. **C**
3. **A**
4. **B**
5. **B**
6. **A**
7. **B**
8. **C**
9. **A**
10. **B**

---

<a id="fontes"></a>
## 18. Fontes

- Kanban University — Official Guide: https://kanban.university/kanban-guide/
- Scrum Guide: https://scrumguides.org/download.html
- Ementa pública do curso Udemy: https://www.udemy.com/course/gestao-de-projetos-com-agile-scrum-o-guia-definitivo/
