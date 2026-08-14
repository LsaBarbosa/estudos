# Fase 4 — Ágil Escalado, Nexus, Dependências e Governança Multitime

> Objetivo: entender quando e como escalar entrega ágil sem criar uma camada desnecessária de coordenação.

## Índice

- [1. Por que escalar](#por-que-escalar)
- [2. Quando não escalar](#quando-nao)
- [3. Principais desafios](#desafios)
- [4. Scrum of Scrums](#scrum-of-scrums)
- [5. Nexus](#nexus)
- [6. Elementos do Nexus](#elementos)
- [7. Eventos do Nexus](#eventos)
- [8. Dependências entre times](#dependencias)
- [9. Integração contínua em escala](#integracao)
- [10. Planejamento e roadmap em escala](#planejamento)
- [11. Governança em escala](#governanca)
- [12. Métricas em escala](#metricas)
- [13. Anti-padrões](#antipadroes)
- [14. Estudo de caso](#caso)
- [15. Mapa mental](#mapa-mental)
- [16. Revisão para ouvir](#revisao-ouvir)
- [17. Exercícios](#exercicios)
- [18. Gabarito](#gabarito)
- [19. Fontes](#fontes)

<a id="por-que-escalar"></a>
## 1. Por que escalar

Escala é necessária quando um único time não consegue entregar um produto complexo dentro das restrições desejadas.

Motivos possíveis:
- amplitude do produto;
- múltiplas especialidades;
- volume de trabalho;
- necessidades regulatórias;
- presença geográfica;
- dependências organizacionais.

Mais pessoas não significam automaticamente mais velocidade. Coordenação possui custo.

---

<a id="quando-nao"></a>
## 2. Quando não escalar

Antes de criar vários times, pergunte:

- o produto realmente exige mais capacidade?
- o problema é falta de foco?
- o backlog está mal priorizado?
- arquitetura cria dependências desnecessárias?
- existem filas organizacionais?
- um time menor e estável poderia entregar melhor?

Escalar um sistema ruim normalmente escala também seus problemas.

---

<a id="desafios"></a>
## 3. Principais desafios

### Dependências

Time A não conclui sem trabalho do Time B.

### Integração

Cada time conclui sua parte, mas o produto integrado não funciona.

### Priorização local

Cada time otimiza sua própria meta, prejudicando resultado global.

### Comunicação

Mais times criam mais interfaces de comunicação.

### Fórmula conceitual

Com `n` grupos, o número potencial de conexões cresce aproximadamente como:

`n(n-1)/2`

Não significa que toda conexão ocorrerá, mas ilustra por que coordenação fica mais difícil.

---

<a id="scrum-of-scrums"></a>
## 4. Scrum of Scrums

Scrum of Scrums é uma prática de coordenação entre times.

Pode ser usada para discutir:
- dependências;
- impedimentos intertimes;
- integração;
- riscos compartilhados.

Risco: virar reunião de status em cascata.

Uma boa coordenação deve gerar decisões e remoção de dependências, não apenas relatos.

---

<a id="nexus"></a>
## 5. Nexus

Nexus é um framework da Scrum.org para escalar Scrum na entrega de um único produto.

Objetivo central:
- minimizar dependências entre times;
- reduzir problemas de integração;
- produzir um **Integrated Increment** utilizável a cada Sprint.

Nexus estende Scrum apenas onde necessário.

### Estrutura conceitual

Vários Scrum Teams trabalham sobre:
- um único produto;
- um único Product Backlog;
- um Product Goal compartilhado;
- um Incremento integrado.

---

<a id="elementos"></a>
## 6. Elementos do Nexus

### Nexus Integration Team

Existe para garantir que o Nexus entregue um Incremento integrado valioso e utilizável ao menos uma vez por Sprint.

O foco não é "integrar no final", mas promover condições para integração contínua.

### Nexus Sprint Backlog

Visualiza dependências e trabalho necessário entre os times para atingir o objetivo da Sprint do Nexus.

### Integrated Increment

Resultado integrado de todo o trabalho concluído pelos times.

Se cinco times entregam componentes isolados que não funcionam juntos, o Nexus não possui incremento efetivo.

### Nexus Goal

Objetivo da Sprint em nível Nexus, que conecta trabalho dos times a um resultado comum.

---

<a id="eventos"></a>
## 7. Eventos do Nexus

O Nexus inclui eventos que complementam Scrum para lidar com dependências e integração.

### Cross-Team Refinement

Refinamento entre times para:
- identificar dependências;
- decompor itens;
- decidir alocação;
- reduzir acoplamento.

### Nexus Sprint Planning

Coordena o que os times trabalharão e como dependências serão tratadas.

### Nexus Daily Scrum

Foco:
- problemas de integração;
- dependências;
- progresso do Nexus.

Não substitui Daily Scrum de cada time.

### Nexus Sprint Review

Inspeciona o Integrated Increment com stakeholders.

Idealmente existe uma visão integrada do produto, não cinco demos independentes sem conexão.

### Nexus Sprint Retrospective

Busca melhorar o sistema multitime, incluindo problemas compartilhados.

---

<a id="dependencias"></a>
## 8. Dependências entre times

Tipos:

### Dependência de sequência

B só começa depois que A termina.

### Dependência de conhecimento

A depende de especialista que está em B.

### Dependência técnica

Dois times modificam o mesmo componente ou contrato.

### Dependência de ambiente

Times disputam infraestrutura limitada.

### Dependência externa

Fornecedor, jurídico, segurança ou outra área.

### Estratégias de redução

- dividir por capacidade de negócio;
- definir interfaces claras;
- automatizar testes;
- compartilhar conhecimento;
- decompor backlog verticalmente;
- reduzir componentes compartilhados;
- antecipar refinamento;
- usar contratos de API;
- evitar "time de integração" como gargalo.

---

<a id="integracao"></a>
## 9. Integração contínua em escala

Em escala, integração tardia é um dos maiores riscos.

Boas práticas:
- trunk-based ou integração frequente;
- testes automatizados;
- contratos versionados;
- pipelines comuns;
- ambientes reproduzíveis;
- feature flags;
- observabilidade;
- definição de qualidade compartilhada.

### Anti-padrão

Time A termina em semana 1.  
Time B termina em semana 2.  
Time C termina em semana 3.  
Integração ocorre na semana 4.

Isso transforma Sprint em mini-cascata.

---

<a id="planejamento"></a>
## 10. Planejamento e roadmap em escala

### Um produto, uma direção

Times não devem possuir roadmaps conflitantes para o mesmo produto.

Estruture:
- Product Goal;
- resultados estratégicos;
- roadmap;
- releases;
- Product Backlog;
- objetivos de Sprint.

### Dependências no roadmap

Uma dependência relevante deve ser:
- visível;
- possuir responsável;
- ter data necessária;
- ter plano de mitigação.

### Planejamento de capacidade

Não some velocidades de times como se fossem unidades universais.

Story Points são relativos ao contexto de cada time.

Use:
- throughput;
- histórico por time;
- decomposição;
- Monte Carlo quando aplicável;
- faixas de previsão.

---

<a id="governanca"></a>
## 11. Governança em escala

A governança precisa definir:

- visão e prioridade;
- orçamento;
- arquitetura e guardrails;
- segurança;
- compliance;
- qualidade;
- dependências;
- decisões intertimes;
- escalonamento.

### Guardrails

São limites que permitem autonomia sem exigir aprovação para cada decisão.

Exemplos:
- tecnologias suportadas;
- padrões de segurança;
- limite de custo;
- SLO;
- política de dados.

---

<a id="metricas"></a>
## 12. Métricas em escala

Evite ranking de times por velocidade.

Melhores indicadores:
- tempo de entrega ponta a ponta;
- throughput do produto;
- frequência de integração;
- defeitos de integração;
- dependências abertas;
- aging de dependências;
- estabilidade;
- valor entregue;
- predictability.

### Métrica local x sistema

Um time pode aumentar throughput local e piorar o sistema se criar fila em outro ponto.

A gestão deve otimizar fluxo global.

---

<a id="antipadroes"></a>
## 13. Anti-padrões

### Component Teams excessivos

Times organizados apenas por camada:
- frontend;
- backend;
- banco;
- QA.

Isso pode criar muitas dependências.

Quando possível, prefira times capazes de entregar fatias verticais de valor.

### Arquitetura como fila central

Toda decisão precisa do arquiteto-chefe.

Resultado:
- gargalo;
- espera;
- baixa autonomia.

Use princípios, guardrails e fóruns adequados.

### Program Management por planilha

Centenas de dependências são controladas manualmente sem atacar sua causa estrutural.

### Integração no final

Um dos maiores riscos em escala.

---

<a id="caso"></a>
## 14. Estudo de caso

### Cenário

Quatro times trabalham em checkout:

- Time A: carrinho
- Time B: pagamento
- Time C: antifraude
- Time D: notificações

O release depende dos quatro.

Problemas:
- APIs mudam sem comunicação;
- testes de integração ocorrem apenas no final;
- cada time possui prioridade própria;
- nenhum objetivo comum.

### Intervenção

1. estabelecer Product Goal comum;
2. Product Backlog único;
3. Cross-Team Refinement;
4. contratos de API;
5. integração frequente;
6. Nexus Sprint Goal;
7. visualizar dependências;
8. revisar incremento integrado.

### Resultado esperado

Menos surpresas de integração e decisão orientada ao produto, não ao sucesso local de cada time.

---

<a id="mapa-mental"></a>
## 15. Mapa mental

```text
ÁGIL ESCALADO
│
├── PROBLEMA
│   ├── dependências
│   ├── integração
│   ├── coordenação
│   └── prioridades locais
│
├── NEXUS
│   ├── múltiplos Scrum Teams
│   ├── um Product Backlog
│   ├── Nexus Integration Team
│   ├── Nexus Sprint Backlog
│   ├── Nexus Goal
│   └── Integrated Increment
│
├── EVENTOS
│   ├── Cross-Team Refinement
│   ├── Nexus Sprint Planning
│   ├── Nexus Daily Scrum
│   ├── Nexus Sprint Review
│   └── Nexus Sprint Retrospective
│
├── REDUÇÃO DE DEPENDÊNCIAS
│   ├── times por capacidade
│   ├── APIs claras
│   ├── automação
│   └── integração contínua
│
└── GOVERNANÇA
    ├── Product Goal
    ├── guardrails
    ├── métricas sistêmicas
    └── decisões intertimes
```

---

<a id="revisao-ouvir"></a>
## 16. Revisão para ouvir

Escalar agilidade significa aumentar a capacidade de entregar um produto sem perder integração e foco. Mais times criam mais dependências e custo de coordenação.

Nexus estende Scrum para múltiplos times trabalhando no mesmo produto. Existe um Product Backlog comum e o objetivo é produzir um Integrated Increment utilizável a cada Sprint.

A principal preocupação em escala é reduzir dependências e integrar continuamente. Não basta cada time concluir sua parte. O produto precisa funcionar como um todo.

O gerente atua criando transparência de dependências, coordenando decisões organizacionais, protegendo objetivos comuns e evitando otimização local. Métricas devem observar o fluxo do produto e não comparar Story Points entre times.

---

<a id="exercicios"></a>
## 17. Exercícios

1. Escalar um sistema ruim tende a:
   - A) corrigir automaticamente o sistema
   - B) ampliar também problemas existentes
   - C) eliminar dependências
   - D) reduzir comunicação

2. Principal foco do Nexus:
   - A) criar hierarquia
   - B) integrar trabalho de múltiplos Scrum Teams em um produto
   - C) substituir Scrum
   - D) comparar velocidade

3. Integrated Increment significa:
   - A) soma de documentos dos times
   - B) resultado integrado e utilizável
   - C) plano de release
   - D) status report

4. Cross-Team Refinement ajuda principalmente:
   - A) identificar dependências e decompor trabalho
   - B) calcular salário
   - C) aprovar férias
   - D) substituir Sprint Review

5. Comparar velocidade entre times:
   - A) é prática recomendada
   - B) é inadequado porque pontos são relativos a cada time
   - C) é obrigatório no Nexus
   - D) mede valor

6. Integração tardia:
   - A) reduz risco
   - B) aumenta risco de incompatibilidade
   - C) substitui CI
   - D) elimina testes

7. Guardrails servem para:
   - A) microgerenciar
   - B) permitir autonomia dentro de limites
   - C) eliminar arquitetura
   - D) impedir decisões

8. Um time aumenta throughput, mas cria fila em outro. O sistema:
   - A) necessariamente melhorou
   - B) pode ter piorado apesar da melhora local
   - C) dobrou velocidade
   - D) não possui gargalo

9. Time de frontend, backend, banco e QA pode:
   - A) reduzir sempre dependências
   - B) criar muitas dependências de handoff
   - C) eliminar integração
   - D) substituir PO

10. O gerente em escala deve otimizar:
    - A) utilização individual
    - B) fluxo e resultado do produto
    - C) volume de reuniões
    - D) quantidade de planos

---

<a id="gabarito"></a>
## 18. Gabarito

1. **B**
2. **B**
3. **B**
4. **A**
5. **B**
6. **B**
7. **B**
8. **B**
9. **B**
10. **B**

---

<a id="fontes"></a>
## 19. Fontes

- Nexus Guide — Scrum.org: https://www.scrum.org/resources/online-nexus-guide
- Scrum Guide: https://scrumguides.org/download.html
- Ementa pública do curso Udemy: https://www.udemy.com/course/gestao-de-projetos-com-agile-scrum-o-guia-definitivo/
