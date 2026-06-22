



Lucas, este tema entra no bloco **DevOps/Cloud**, ligado a Docker, Kubernetes, logs, métricas e health checks. fileciteturn0file1

# Pipeline CI/CD com GitHub Actions e Jenkins — Mapa Mental

## Versão em Português

| Nó do mapa mental | Resumo enxuto |
|---|---|
| **Pipeline** | Fluxo automatizado que leva o código do commit até validação, build, testes e deploy. |
| **CI — Continuous Integration** | Integração contínua. A cada push ou pull request, o sistema compila, testa e valida o código. |
| **CD — Continuous Delivery/Deployment** | Entrega ou implantação contínua. Automatiza a preparação ou envio da aplicação para ambientes como dev, homologação e produção. |
| **GitHub Actions** | Ferramenta de automação integrada ao GitHub. Usa arquivos `.yml` dentro de `.github/workflows`. |
| **Jenkins** | Servidor de automação independente. Usa pipelines definidos normalmente em `Jenkinsfile`. |
| **Trigger/Gatilho** | Evento que inicia o pipeline: `push`, `pull_request`, merge, tag, agendamento ou execução manual. |
| **Stages/Etapas** | Partes principais do fluxo: checkout, setup, build, test, package, scan, deploy. |
| **Jobs** | Conjunto de passos executados em um ambiente. No GitHub Actions, jobs podem rodar em paralelo. No Jenkins, podem ser organizados por stages. |
| **Steps/Passos** | Comandos individuais: instalar JDK, rodar Maven, executar testes, gerar imagem Docker, publicar artefato. |
| **Runner/Agent** | Máquina onde o pipeline roda. GitHub usa runners. Jenkins usa agents/nodes. |
| **Build Java** | Compilação da aplicação usando Maven ou Gradle. Exemplo: `mvn clean package`. |
| **Testes** | Execução de testes unitários, integração e contrato. Exemplo: `mvn test` ou `mvn verify`. |
| **Qualidade** | Validação com SonarQube, Checkstyle, SpotBugs, JaCoCo, lint e análise estática. |
| **Segurança** | Verificação de dependências vulneráveis, secrets expostos e imagens Docker inseguras. |
| **Artefato** | Resultado do build: `.jar`, `.war`, imagem Docker ou pacote versionado. |
| **Docker** | Empacota a aplicação em uma imagem para rodar de forma padronizada. |
| **Deploy** | Envio da aplicação para servidor, Kubernetes, cloud, ECS, Azure, GCP, Heroku etc. |
| **Secrets** | Variáveis sensíveis como tokens, senhas, chaves SSH e credenciais de cloud. Nunca devem ficar no código. |
| **Ambientes** | Separação entre dev, staging/homologação e produção. Cada ambiente pode ter regras diferentes. |
| **Rollback** | Estratégia para voltar para uma versão anterior em caso de falha. |
| **Observabilidade** | Logs, métricas, health checks e alertas após o deploy. |
| **GitHub Actions vs Jenkins** | GitHub Actions é mais simples para projetos no GitHub. Jenkins é mais flexível, mas exige mais manutenção. |

## Fluxo mental típico

| Ordem | Etapa | Exemplo Java/Spring Boot |
|---:|---|---|
| 1 | **Commit/Pull Request** | Desenvolvedor envia código para o GitHub |
| 2 | **Checkout** | Pipeline baixa o código |
| 3 | **Setup do ambiente** | Instala Java 17 |
| 4 | **Build** | `mvn clean package` |
| 5 | **Testes** | `mvn test` ou `mvn verify` |
| 6 | **Análise de qualidade** | SonarQube, JaCoCo, Checkstyle |
| 7 | **Build Docker** | Gera imagem da aplicação |
| 8 | **Push da imagem** | Publica no Docker Hub, ECR, GCR etc. |
| 9 | **Deploy** | Atualiza ambiente de homologação ou produção |
| 10 | **Monitoramento** | Verifica logs, métricas e health endpoint |

## Comparação rápida

| Critério | GitHub Actions | Jenkins |
|---|---|---|
| **Configuração** | YAML em `.github/workflows` | `Jenkinsfile` ou configuração visual |
| **Hospedagem** | Gerenciado pelo GitHub | Normalmente auto-hospedado |
| **Facilidade inicial** | Mais simples | Mais trabalhoso |
| **Flexibilidade** | Boa | Muito alta |
| **Manutenção** | Baixa | Média/alta |
| **Integração com GitHub** | Nativa | Precisa configurar plugins/webhooks |
| **Plugins** | Actions do marketplace | Grande ecossistema de plugins |
| **Uso comum** | Projetos GitHub, CI/CD simples e médio | Ambientes corporativos, pipelines complexos |
| **Custo operacional** | Menor esforço de infraestrutura | Requer servidor, agentes e manutenção |
| **Controle** | Menor controle da plataforma | Maior controle sobre ambiente e execução |

---

# Pipeline with GitHub Actions and Jenkins — Mind Map

## English Version

| Mind map node | Short summary |
|---|---|
| **Pipeline** | Automated flow that moves code from commit to validation, build, testing and deployment. |
| **CI — Continuous Integration** | Every push or pull request triggers compilation, tests and validation. |
| **CD — Continuous Delivery/Deployment** | Automates release preparation or deployment to environments such as dev, staging and production. |
| **GitHub Actions** | Automation tool integrated with GitHub. Uses `.yml` files inside `.github/workflows`. |
| **Jenkins** | Independent automation server. Pipelines are commonly defined in a `Jenkinsfile`. |
| **Trigger** | Event that starts the pipeline: `push`, `pull_request`, merge, tag, schedule or manual execution. |
| **Stages** | Main parts of the flow: checkout, setup, build, test, package, scan and deploy. |
| **Jobs** | Groups of steps executed in an environment. GitHub Actions jobs can run in parallel. Jenkins organizes them through stages and agents. |
| **Steps** | Individual commands: install JDK, run Maven, execute tests, build Docker image, publish artifact. |
| **Runner/Agent** | Machine where the pipeline runs. GitHub uses runners. Jenkins uses agents/nodes. |
| **Java Build** | Application compilation using Maven or Gradle. Example: `mvn clean package`. |
| **Tests** | Unit, integration and contract tests. Example: `mvn test` or `mvn verify`. |
| **Quality** | Validation with SonarQube, Checkstyle, SpotBugs, JaCoCo, linting and static analysis. |
| **Security** | Checks for vulnerable dependencies, exposed secrets and unsafe Docker images. |
| **Artifact** | Build output: `.jar`, `.war`, Docker image or versioned package. |
| **Docker** | Packages the application into a standardized runnable image. |
| **Deployment** | Sends the application to a server, Kubernetes, cloud platform, ECS, Azure, GCP, Heroku etc. |
| **Secrets** | Sensitive variables such as tokens, passwords, SSH keys and cloud credentials. They must never be stored in source code. |
| **Environments** | Separation between dev, staging and production. Each environment can have different rules. |
| **Rollback** | Strategy to return to a previous version when something fails. |
| **Observability** | Logs, metrics, health checks and alerts after deployment. |
| **GitHub Actions vs Jenkins** | GitHub Actions is simpler for GitHub-based projects. Jenkins is more flexible but requires more maintenance. |

## Typical mental flow

| Order | Stage | Java/Spring Boot example |
|---:|---|---|
| 1 | **Commit/Pull Request** | Developer pushes code to GitHub |
| 2 | **Checkout** | Pipeline downloads the source code |
| 3 | **Environment setup** | Installs Java 17 |
| 4 | **Build** | `mvn clean package` |
| 5 | **Tests** | `mvn test` or `mvn verify` |
| 6 | **Quality analysis** | SonarQube, JaCoCo, Checkstyle |
| 7 | **Docker build** | Creates the application image |
| 8 | **Image push** | Publishes to Docker Hub, ECR, GCR etc. |
| 9 | **Deployment** | Updates staging or production |
| 10 | **Monitoring** | Checks logs, metrics and health endpoint |

## Fast comparison

| Criterion | GitHub Actions | Jenkins |
|---|---|---|
| **Configuration** | YAML in `.github/workflows` | `Jenkinsfile` or UI configuration |
| **Hosting** | Managed by GitHub | Usually self-hosted |
| **Initial setup** | Easier | More complex |
| **Flexibility** | Good | Very high |
| **Maintenance** | Low | Medium/high |
| **GitHub integration** | Native | Requires plugins/webhooks |
| **Plugins** | Marketplace actions | Large plugin ecosystem |
| **Common use** | GitHub projects, simple and medium CI/CD | Enterprise environments, complex pipelines |
| **Operational cost** | Lower infrastructure effort | Requires server, agents and maintenance |
| **Control** | Less platform control | More control over execution environment |

# Resumo ultraenxuto

| Português | English |
|---|---|
| Pipeline é uma esteira automatizada para validar, testar, empacotar e publicar software. | A pipeline is an automated flow to validate, test, package and release software. |
| GitHub Actions é integrado ao GitHub e usa YAML. | GitHub Actions is integrated with GitHub and uses YAML. |
| Jenkins é uma ferramenta mais flexível, mas exige mais manutenção. | Jenkins is more flexible but requires more maintenance. |
| Em Java, o pipeline normalmente roda Maven/Gradle, testes, análise de qualidade, Docker e deploy. | In Java, the pipeline usually runs Maven/Gradle, tests, quality analysis, Docker and deployment. |
| O objetivo é reduzir erro manual e aumentar segurança no deploy. | The goal is to reduce manual errors and make deployments safer. |
