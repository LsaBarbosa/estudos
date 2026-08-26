# FASE 14 — CI/CD e DevOps

Lucas, em CI/CD o ponto principal não é decorar YAML de Jenkins, GitHub Actions ou GitLab CI. É entender **como transformar uma alteração de código em um artefato testado, seguro, reproduzível e implantável com risco controlado**.

## 1. Conceitos, trade-offs e casos de uso

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **CI — Continuous Integration** | Integra alterações frequentemente e executa validações automáticas como build, testes e análise de código. | Pipelines lentos reduzem feedback; pipelines fracos deixam defeitos chegarem às próximas etapas. | Validar cada Pull Request. |
| **CD — Continuous Delivery** | Mantém o software sempre em condição de ser implantado, normalmente com promoção controlada entre ambientes. | Exige automação, testes confiáveis e disciplina de release. | Homologação automática e produção após aprovação. |
| **Continuous Deployment** | Toda mudança que passa pelas validações pode chegar automaticamente à produção. | Exige alta confiança em testes, observabilidade e rollback. | Produtos com releases frequentes e baixo lead time. |
| **Pipeline as Code** | Pipeline é versionado junto com código ou infraestrutura. | Adiciona código operacional que também precisa de manutenção e revisão. | `Jenkinsfile`, `.github/workflows/*.yml`, `.gitlab-ci.yml`. |
| **Git Push / Pull Request** | Evento que normalmente inicia o pipeline de CI. | Executar tudo a cada alteração pode aumentar custo e tempo. | Build e testes automáticos de PR. |
| **Compile** | Compila código e valida dependências e estrutura básica do projeto. | Detecta apenas problemas de compilação, não comportamento incorreto. | `mvn compile` ou `gradle build`. |
| **Unit Test** | Testa unidades isoladas de código rapidamente. | Mocks excessivos podem produzir testes distantes da realidade. | Testar `OrderService`, regras e Value Objects. |
| **Integration Test** | Testa integração entre componentes reais ou próximos do ambiente real. | Mais lento e caro que unit tests. | Spring + PostgreSQL via Testcontainers. |
| **Static Analysis** | Analisa código sem executá-lo para encontrar bugs, vulnerabilidades e problemas de qualidade. | Pode produzir falsos positivos e aumentar duração do pipeline. | SonarQube, SpotBugs, Checkstyle. |
| **Security Scan** | Analisa código, dependências, secrets e/ou imagens em busca de vulnerabilidades. | Pode bloquear releases por vulnerabilidades sem risco real se não houver política adequada. | SAST, SCA, container scanning. |
| **Build Image** | Gera uma imagem de container imutável contendo a aplicação e runtime necessários. | Imagens grandes aumentam tempo de build, pull e superfície de ataque. | Imagem Docker da API Java. |
| **Container Registry** | Repositório versionado para armazenar imagens. | Exige governança de tags, retenção e segurança. | ECR, GHCR, GitLab Container Registry. |
| **Push Registry** | Publica o artefato construído para posterior implantação. | Tags mutáveis como `latest` prejudicam rastreabilidade. | Publicar `order-service:1.7.3`. |
| **Deploy** | Promove uma versão para determinado ambiente. | Deploy sem estratégia de rollout pode causar indisponibilidade. | Kubernetes, ECS, VMs. |
| **Smoke Test** | Validação curta após o deploy para verificar funcionalidades críticas. | Não substitui testes completos. | Verificar health, login e endpoint principal. |
| **Quality Gate** | Critério que precisa ser satisfeito para o pipeline continuar. | Gates excessivamente rígidos podem reduzir velocidade de entrega. | Testes obrigatórios, cobertura mínima, vulnerabilidade crítica = zero. |
| **Artifact** | Resultado versionado do build que será promovido entre ambientes. | Rebuild diferente por ambiente prejudica reprodutibilidade. | JAR ou imagem Docker. |
| **GitHub Actions** | Plataforma de automação integrada ao GitHub, baseada em workflows, jobs, steps e runners. | Excelente integração com GitHub, mas workflows grandes podem ficar difíceis de reutilizar/governar. | Projetos hospedados no GitHub. |
| **Jenkins** | Servidor de automação extensível; Pipeline permite CI/CD como código via `Jenkinsfile`. | Altamente flexível, mas exige manutenção de controller, agents, plugins e segurança. | Ambientes corporativos com pipelines altamente customizados. |
| **GitLab CI/CD** | CI/CD integrado ao GitLab, baseado em pipelines, stages, jobs e runners. | Forte integração, mas pode gerar dependência maior do ecossistema GitLab. | Repositórios hospedados no GitLab. |
| **Rolling Deployment** | Substitui instâncias antigas gradualmente pelas novas. | Durante o rollout duas versões coexistem; exige compatibilidade. | Deploy padrão em Kubernetes. |
| **Blue/Green** | Mantém dois ambientes completos; um recebe tráfego e o outro recebe a nova versão antes do cutover. | Duplica temporariamente infraestrutura e exige cuidado com banco/estado. | Releases com necessidade de rollback rápido. |
| **Canary** | Envia inicialmente uma pequena porcentagem do tráfego para a nova versão e aumenta gradualmente. | Exige roteamento, métricas e observabilidade mais sofisticados. | Mudanças de maior risco em produção. |
| **Feature Flag** | Controla ativação de uma funcionalidade independentemente do deploy do código. | Flags acumuladas geram dívida técnica e caminhos adicionais de teste. | Liberar nova funcionalidade para 5% dos usuários. |
| **Rollback** | Retorna rapidamente à versão anterior quando uma implantação apresenta problema. | Código pode voltar, mas mudanças de banco e efeitos externos podem não ser reversíveis. | Regressão detectada após deploy. |

GitHub Actions modela automações como workflows contendo jobs e steps executados por runners; Jenkins utiliza Pipeline as Code através do `Jenkinsfile`; e GitLab CI/CD utiliza `.gitlab-ci.yml` com stages, jobs e runners. 

---

# 2. CI x CD

A diferença precisa estar clara.

```text
CI
↓
Integro e valido o código continuamente


CD
↓
Entrego ou implanto o software
de forma automatizada e confiável
```

Um fluxo típico:

```text
Developer
   ↓
Git Push / Pull Request
   ↓
CI
├── Compile
├── Unit Tests
├── Integration Tests
├── Static Analysis
└── Security Scan
```

Depois:

```text
Build Artifact
     ↓
Registry
     ↓
CD
├── Dev
├── Staging
└── Production
```

GitHub define CI como a prática de construir e testar mudanças frequentemente e CD como automação da publicação/deploy após essas validações. 

---

# 3. Pipeline ideal

Um pipeline Java pode ser visualizado assim:

```text
Git Push
   ↓
Compile
   ↓
Unit Test
   ↓
Integration Test
   ↓
Static Analysis
   ↓
Security Scan
   ↓
Build JAR
   ↓
Build Image
   ↓
Push Registry
   ↓
Deploy
   ↓
Smoke Test
   ↓
Monitor
```

Cada etapa responde uma pergunta.

```text
Compile
→ O código compila?


Unit Test
→ As regras isoladas continuam corretas?


Integration Test
→ Os componentes funcionam juntos?


Static Analysis
→ Existe problema de qualidade?


Security Scan
→ Existe vulnerabilidade conhecida?


Smoke Test
→ A aplicação realmente funciona depois do deploy?
```

---

# 4. Fail Fast

Uma boa prática é colocar verificações:

```text
rápidas
+
baratas
```

antes das:

```text
lentas
+
caras
```

Por exemplo:

```text
Compile
   ↓
Unit Test
   ↓
Static Analysis
   ↓
Integration Test
   ↓
Build Image
   ↓
Deploy
```

Não faz sentido gastar vários minutos construindo e publicando uma imagem se:

```text
Unit Test
```

já poderia detectar o problema em segundos.

---

# 5. Unit Test x Integration Test

Esses testes possuem objetivos diferentes.

### Unit Test

```text
OrderService
     ↓
isolado
```

Normalmente é:

```text
rápido
numeroso
determinístico
```

### Integration Test

Pode validar:

```text
Spring Boot
    ↓
Hibernate
    ↓
PostgreSQL
```

ou:

```text
Producer
   ↓
Kafka
   ↓
Consumer
```

Integration Test aumenta confiança de que:

> **as peças realmente funcionam juntas.**

No ecossistema Java, uma boa combinação pode ser:

```text
JUnit
Mockito
Testcontainers
```

---

# 6. Static Analysis

Static Analysis não executa a aplicação.

Ela analisa:

```text
código
bytecode
dependências
padrões
```

para encontrar problemas.

Exemplos:

```text
bug potencial
complexidade alta
duplicação
code smell
vulnerabilidade
```

Ferramentas possíveis:

```text
SonarQube
SpotBugs
Checkstyle
PMD
```

Mas o objetivo não é:

> atingir uma métrica bonita.

O objetivo é evitar que problemas conhecidos avancem no pipeline.

---

# 7. Security Scan

Segurança deveria entrar no pipeline antes de produção.

Podemos separar:

```text
SAST
↓
analisa código


SCA
↓
analisa dependências


Secret Scanning
↓
procura credenciais expostas


Container Scan
↓
analisa imagem e SO
```

Exemplo:

```text
Spring Boot
   ↓
dependency vulnerável
   ↓
Security Gate
   ↓
pipeline bloqueado
```

Mas uma política madura precisa considerar:

```text
severidade
exploitabilidade
contexto
exceções aprovadas
```

e não simplesmente bloquear qualquer CVE indiscriminadamente.

---

# 8. Build once, deploy many

Esse é um conceito importante.

Evite:

```text
Build DEV
Build QA
Build PROD
```

porque podemos terminar com:

```text
3 artefatos diferentes
```

Idealmente:

```text
Commit abc123
     ↓
Build
     ↓
Image
order-service:1.5.0
     ↓
DEV
     ↓
QA
     ↓
PROD
```

Ou seja:

> **o mesmo artefato validado é promovido entre ambientes.**

As diferenças de ambiente entram por:

```text
configuração externa
Secrets
ConfigMaps
environment variables
```

e não recompilando a aplicação.

---

# 9. Imutabilidade e rastreabilidade

Evite depender apenas de:

```text
latest
```

Prefira algo rastreável:

```text
order-service:1.5.0
```

ou:

```text
order-service:git-a83f27c
```

Assim:

```text
Produção
   ↓
versão 1.5.0
   ↓
commit a83f27c
   ↓
pipeline 8821
```

Você consegue responder:

> **Qual código exatamente está em produção?**

Isso é fundamental para troubleshooting e rollback.

---

# 10. GitHub Actions

Um workflow do GitHub Actions é definido em YAML dentro de:

```text
.github/workflows/
```

Mentalmente:

```text
Event
  ↓
Workflow
  ↓
Jobs
  ↓
Steps
  ↓
Runner
```

Exemplo:

```text
Pull Request
     ↓
CI Workflow
     ↓
Build
Test
Scan
```

Os jobs podem executar sequencialmente ou em paralelo, e runners podem ser hospedados pelo GitHub ou pelo próprio time. 

---

# 11. Jenkins

Jenkins continua muito presente em ambientes corporativos.

Com Pipeline as Code:

```text
Jenkinsfile
```

define:

```text
Pipeline
   ↓
Stages
   ↓
Steps
```

Exemplo:

```text
Build
 ↓
Test
 ↓
Sonar
 ↓
Docker
 ↓
Deploy
```

A vantagem do Jenkins é:

```text
flexibilidade
+
ecossistema
+
customização
```

O trade-off é operacional:

```text
plugins
agents
upgrades
credentials
controller
segurança
```

Jenkins Pipeline foi projetado justamente para modelar pipelines de entrega como código versionado junto ao projeto. 

---

# 12. GitLab CI

GitLab CI/CD utiliza normalmente:

```text
.gitlab-ci.yml
```

A estrutura básica é:

```text
Pipeline
  ↓
Stages
  ↓
Jobs
  ↓
Runners
```

Por exemplo:

```text
build
 ↓
test
 ↓
security
 ↓
deploy
```

Os runners são os agentes responsáveis por executar efetivamente os jobs. 

Para entrevista, mais importante do que saber qual é “melhor” é entender:

> **os três resolvem essencialmente o mesmo problema de automação de CI/CD com modelos operacionais diferentes.**

---

# 13. Rolling Deployment

Rolling substitui instâncias gradualmente.

```text
Antes

V1 V1 V1 V1
```

Durante:

```text
V1 V1 V1 V2
```

Depois:

```text
V1 V1 V2 V2
```

Até:

```text
V2 V2 V2 V2
```

No Kubernetes, `maxUnavailable` controla quantos Pods podem ficar indisponíveis e `maxSurge` quantos Pods adicionais podem existir durante o rollout. 

### Vantagens

```text
baixo custo adicional
deploy gradual
boa integração com Kubernetes
```

### Risco

Durante algum tempo:

```text
V1 + V2
```

existem simultaneamente.

Portanto API, eventos e banco precisam ser compatíveis entre versões.

---

# 14. Blue/Green

Blue/Green mantém:

```text
BLUE
versão atual
recebendo tráfego
```

e:

```text
GREEN
nova versão
sem tráfego
```

Depois de validar Green:

```text
Router
  ↓
muda tráfego

BLUE  →  GREEN
```

Se houver problema:

```text
GREEN
  ↓
rollback

BLUE
```

A principal vantagem é:

> **cutover e rollback muito rápidos.**

O custo é manter dois ambientes simultaneamente, além do cuidado necessário com banco e estado compartilhado. 

---

# 15. Canary

Canary reduz risco liberando a nova versão para poucos usuários primeiro.

```text
V1 = 95%

V2 = 5%
```

Observamos:

```text
error rate
latency
CPU
business metrics
```

Se estiver saudável:

```text
V2 = 20%

V2 = 50%

V2 = 100%
```

Se piorar:

```text
V2 = 0%
```

Canary é particularmente poderoso quando combinado com observabilidade.

A técnica consiste justamente em liberar gradualmente uma nova versão para um subconjunto de usuários antes da expansão geral. 

---

# 16. Rolling x Blue/Green x Canary

| Estratégia | Como funciona | Principal vantagem | Principal custo |
|---|---|---|---|
| **Rolling** | Troca instâncias gradualmente | Simples e econômico | V1 e V2 coexistem |
| **Blue/Green** | Dois ambientes completos e troca de tráfego | Rollback rápido | Infraestrutura duplicada |
| **Canary** | Nova versão recebe pequena parte do tráfego | Menor blast radius | Roteamento e observabilidade mais complexos |

Uma heurística:

```text
Deploy padrão
→ Rolling


Rollback imediato importante
→ Blue/Green


Mudança de alto risco
→ Canary
```

Não é uma regra absoluta.

---

# 17. Feature Flags

Feature Flag separa dois conceitos:

```text
Deploy
```

de:

```text
Release
```

Você pode colocar:

```text
Version 2
```

em produção com:

```text
new_checkout = false
```

Depois liberar:

```text
employees = true
```

Depois:

```text
5% users
```

Depois:

```text
50%
```

Finalmente:

```text
100%
```

Isso permite controlar funcionalidade sem realizar novo deploy.

---

# 18. Feature Flag não substitui estratégia de deploy

São ferramentas diferentes.

```text
Canary
↓
controla exposição da versão
```

Enquanto:

```text
Feature Flag
↓
controla exposição da funcionalidade
```

Podemos inclusive combinar:

```text
Canary Deployment
+
Feature Flag
```

e reduzir ainda mais o blast radius.

---

# 19. Dívida de Feature Flags

Flags não devem viver para sempre.

Imagine:

```text
if (flagA) {

    if (flagB) {

        if (flagC) {
```

Agora existem vários caminhos possíveis.

Isso aumenta:

```text
complexidade
testes
debugging
cognitive load
```

Portanto flags temporárias precisam de:

```text
owner
data de remoção
monitoramento
limpeza
```

---

# 20. Smoke Test

Depois do deploy:

```text
Pod Running
```

não significa necessariamente:

```text
Aplicação funcionando
```

Por isso executamos Smoke Tests.

Exemplos:

```text
GET /health
```

```text
GET /products
```

ou um fluxo crítico pequeno.

A ideia é responder rapidamente:

> **A nova versão consegue realizar suas operações fundamentais?**

---

# 21. Rollback

Rollback precisa existir antes do incidente.

Não deveria ser:

> “Se quebrar, pensamos depois.”

Pipeline maduro precisa conhecer:

```text
versão atual
versão anterior
procedimento de rollback
```

Mas existe um detalhe importante:

```text
Application rollback
≠
Database rollback
```

Imagine que V2 executou:

```text
ALTER TABLE
```

e V1 não entende mais aquela estrutura.

Voltar o container para V1 não resolve.

Por isso migrations precisam ser:

```text
backward compatible
```

principalmente em Rolling, Blue/Green e Canary.

---

# 22. Expand and Contract

Uma estratégia útil para migrations é:

```text
EXPAND
↓
adiciona estrutura nova
sem quebrar a antiga

↓
deploy da aplicação nova

↓
migração dos consumidores

↓
CONTRACT
remove estrutura antiga
```

Exemplo:

```text
V1 usa coluna:

customer_name
```

Primeiro:

```text
adiciona estrutura nova
mantendo customer_name
```

Depois V2 passa a utilizar a nova estrutura.

Só posteriormente removemos:

```text
customer_name
```

Esse tipo de mudança paralela é especialmente importante quando versões antigas e novas convivem durante deploys graduais. 

---

# 23. O pipeline não termina no Deploy

Um pipeline maduro deveria pensar:

```text
Deploy
  ↓
Smoke Test
  ↓
Observability
  ↓
Decision
```

Após o deploy observe:

```text
error rate
latency
p95 / p99
CPU
memory
restart count
business metrics
```

Imagine um Canary:

```text
5% traffic
```

Se:

```text
V1 error rate = 0.2%

V2 error rate = 8%
```

o rollout deve parar.

Isso conecta:

```text
CI/CD
+
Observabilidade
+
Resiliência
```

---

# 24. Pipeline como mecanismo de proteção

Um pipeline não deveria ser visto somente como:

> automação de deploy.

Ele é uma sequência de **quality gates**.

```text
Code
 ↓
Compile Gate
 ↓
Test Gate
 ↓
Quality Gate
 ↓
Security Gate
 ↓
Artifact
 ↓
Deployment Gate
 ↓
Runtime Validation
```

Cada etapa reduz a probabilidade de um defeito chegar ao usuário.

---

# 25. Mapa mental para entrevista

Memorize:

```text
CI

Git Push
   ↓
Compile
   ↓
Unit Test
   ↓
Integration Test
   ↓
Static Analysis
   ↓
Security Scan
   ↓
Build
```

Depois:

```text
CD

Artifact
   ↓
Registry
   ↓
Deploy
   ↓
Smoke Test
   ↓
Monitor
```

E estratégias:

```text
Rolling
↓
substituição gradual


Blue/Green
↓
troca de ambiente


Canary
↓
tráfego gradual


Feature Flag
↓
liberação de funcionalidade
```

---

# Resposta objetiva para entrevista

> Eu vejo CI/CD como uma forma de transformar uma alteração de código em um artefato reproduzível, validado e implantável com risco controlado.
>
> Na parte de CI, normalmente começo com compile, unit tests, integration tests, static analysis e security scanning. Se todas as quality gates passam, construo um artefato versionado, normalmente uma imagem de container, e publico no registry. GitHub Actions, Jenkins e GitLab CI são ferramentas diferentes para automatizar esse fluxo através de pipelines definidos como código. 
>
> Eu prefiro o conceito de **build once, deploy many**: o mesmo artefato validado deve ser promovido entre ambientes, mudando configuração externa, e não sendo recompilado para cada ambiente.
>
> No deployment, posso utilizar Rolling, Blue/Green ou Canary dependendo do risco e do requisito operacional. Rolling substitui instâncias gradualmente; Blue/Green mantém dois ambientes e permite troca rápida de tráfego; Canary libera a nova versão inicialmente para uma pequena parcela dos usuários. 
>
> Feature Flags complementam essas estratégias porque desacoplam deploy de release: posso colocar o código em produção sem habilitar imediatamente a funcionalidade.
>
> Depois do deploy, executo smoke tests e observo métricas técnicas e de negócio. Também preparo rollback antecipadamente e mantenho migrations de banco compatíveis com versões antigas e novas durante rollouts graduais.
>
> Então, para mim, CI/CD não é simplesmente automatizar `build` e `deploy`; é construir **uma cadeia de qualidade, segurança, rastreabilidade e redução de risco desde o commit até a produção**.
