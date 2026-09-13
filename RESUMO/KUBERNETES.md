# Kubernetes
Lucas, abaixo estão os itens mais importantes de Kubernetes para entrevistas de backend Java/Spring.

| Conceito | O que é | Trade-off | Uso real em produção |
|---|---|---|---|
| **Pod** | Menor unidade executável gerenciada pelo Kubernetes. Normalmente contém o container da aplicação Java. | Pod é efêmero, pode ser recriado e trocar de IP. Não deve armazenar estado importante localmente. | Executar instâncias de microsserviços Spring Boot. |
| **Deployment** | Recurso que declara como uma aplicação deve ser executada: imagem, número de réplicas, estratégia de atualização etc. | Simplifica gestão e self-healing, mas exige configuração correta de probes, recursos e estratégia de rollout. | Manter, por exemplo, 3 réplicas do `pedido-service` sempre ativas. |
| **Replica** | Instância de um Pod pertencente normalmente a um Deployment. | Mais réplicas aumentam disponibilidade e throughput, mas também aumentam consumo de CPU, RAM, conexões com banco e consumidores. | Escalar horizontalmente uma API Spring Boot. |
| **Service** | Endpoint estável para acessar um conjunto de Pods. Também oferece descoberta via DNS. | Adiciona uma camada de abstração de rede. Não resolve sozinho resiliência, timeout ou retry. | `pagamento-service` acessando `http://pedido-service`. |
| **Service Discovery / DNS** | Permite localizar serviços pelo nome em vez de usar IP de Pods. | Depende da infraestrutura de DNS do cluster e não elimina necessidade de políticas de resiliência. | Microsserviços Java chamando outros serviços pelo nome do Service. |
| **Desired State** | Kubernetes trabalha declarativamente: você informa o estado desejado e o cluster tenta convergir para ele. | Muito poderoso para automação, mas mudanças incorretas no manifesto podem ser propagadas rapidamente. | Declarar `replicas: 3` e deixar Kubernetes recriar Pods perdidos. |
| **Self-healing** | Kubernetes detecta falhas e tenta restaurar o estado esperado. | Reiniciar Pods não resolve problemas de aplicação, dependências externas ou bugs lógicos. | Recriar automaticamente uma instância Spring Boot que morreu. |
| **Readiness Probe** | Indica se o Pod está pronto para receber tráfego. | Configuração muito agressiva pode remover Pods saudáveis do tráfego; muito permissiva pode enviar requisições para uma aplicação ainda não pronta. | Evitar HTTP 503 enquanto Spring Boot ainda está inicializando. |
| **Liveness Probe** | Verifica se a aplicação está viva e consegue continuar executando. | Se depender de banco ou API externa, pode gerar reinicializações em massa e restart loops. | Reiniciar um processo Java travado. |
| **Startup Probe** | Verifica se a aplicação concluiu sua inicialização. | Acrescenta configuração e precisa de thresholds adequados. | Aplicações Spring Boot que levam vários segundos para subir. |
| **Spring Boot Actuator** | Expõe endpoints de health, métricas e informações operacionais. | Exposição inadequada pode revelar informações sensíveis. | `/actuator/health/readiness` e `/actuator/health/liveness` usados pelo Kubernetes. |
| **ConfigMap** | Armazena configurações não sensíveis fora da imagem da aplicação. | Mudanças podem exigir reload ou restart da aplicação, dependendo de como são consumidas. | URL de APIs, feature flags, parâmetros de timeout. |
| **Secret** | Armazena valores sensíveis usados pelos Pods. | Secret do Kubernetes não substitui sozinho um secret manager completo; segurança depende também de RBAC, criptografia e acesso ao cluster. | Senhas de banco, tokens e credenciais. |
| **Resource Request** | Quantidade de CPU/memória usada pelo scheduler para reservar capacidade para o Pod. | Request muito alto desperdiça capacidade; muito baixo pode causar contenção e scheduling inadequado. | Reservar `512Mi` de memória e `500m` de CPU para um Spring Boot. |
| **Resource Limit** | Limite máximo de recursos que o container pode consumir. | Limite baixo causa throttling ou `OOMKilled`; alto demais reduz previsibilidade do cluster. | Limitar um serviço Java a `1Gi` de RAM e `1 CPU`. |
| **OOMKilled** | Container encerrado porque ultrapassou o limite de memória imposto pelo ambiente. | Aumentar memória pode mascarar memory leak; reduzir heap demais pode aumentar GC. | Diagnóstico comum em aplicações Java rodando em Kubernetes. |
| **JVM Container Awareness** | JVM moderna reconhece limites de CPU e memória do container. | Configuração inadequada de heap ainda pode deixar pouca memória para metaspace, threads e memória nativa. | Utilizar `-XX:MaxRAMPercentage` em vez de ocupar 100% do limite com `-Xmx`. |
| **Stateless** | Aplicação não mantém estado de negócio relevante dentro de uma instância específica. | Estado precisa ser externalizado, aumentando dependência de banco, cache ou storage externo. | Sessões em Redis e dados persistidos em PostgreSQL, permitindo qualquer Pod atender a requisição. |
| **Horizontal Scaling** | Aumentar ou diminuir a quantidade de Pods. | Mais Pods não significam necessariamente mais throughput; gargalo pode estar no banco, Kafka ou API externa. | Escalar `pedido-service` de 3 para 10 Pods em horário de pico. |
| **HPA** | Horizontal Pod Autoscaler. Ajusta réplicas baseado em métricas. | Escalar apenas por CPU pode não refletir corretamente a carga real da aplicação. Scaling também aumenta pressão sobre dependências. | Aumentar Pods conforme CPU, memória ou métricas customizadas. |
| **HPA + Banco** | Cada novo Pod normalmente cria seu próprio pool de conexões. | Scaling exagerado pode esgotar `max_connections` do banco. | 10 Pods × HikariCP 20 = até 200 conexões potenciais. |
| **Rolling Update** | Substitui gradualmente Pods antigos por novos durante um deploy. | Durante algum tempo versões antigas e novas coexistem. Exige compatibilidade entre versões. | Atualizar `pedido-service:v1` para `v2` sem indisponibilidade total. |
| **Rollback** | Voltar para uma versão anterior após um deployment problemático. | Banco e eventos podem tornar rollback incompatível se houver mudanças não retrocompatíveis. | Reverter rapidamente uma release que passou a retornar erros. |
| **Graceful Shutdown** | Permite concluir requisições ou trabalhos em andamento antes de encerrar a aplicação. | Shutdown excessivamente longo atrasa rollouts; curto demais pode interromper transações. | Spring Boot finalizar requisições antes da JVM encerrar. |
| **SIGTERM** | Sinal normalmente utilizado para iniciar o encerramento do processo no container. | Aplicação que ignora shutdown adequado pode interromper requisições, consumidores ou transações. | Spring Boot recebendo SIGTERM durante um Rolling Update. |
| **CPU Throttling** | Ocorre quando o container tenta consumir mais CPU que o limite permitido. | Pode causar aumento significativo de latência mesmo sem CPU aparente em 100%. | Aplicação Java com `cpu limit: 500m` sofrendo latência em picos. |
| **Connection Pool por Pod** | Cada instância Spring Boot normalmente possui seu próprio HikariCP. | Mais réplicas multiplicam conexões abertas ao banco. | `12 Pods × pool 20 = até 240 conexões`. |
| **Kubernetes + Kafka** | Pods podem executar consumidores pertencentes ao mesmo Consumer Group. | Mais Pods que partitions não aumenta paralelismo de consumo. | Tópico com 6 partitions permite no máximo aproximadamente 6 consumidores ativos no mesmo grupo. |
| **Persistent Volume (PV/PVC)** | Abstração para armazenamento persistente fora do ciclo de vida do Pod. | Storage distribuído é mais complexo e pode introduzir latência e restrições de acesso. | Bancos, arquivos e workloads stateful que precisam sobreviver à recriação do Pod. |
| **Namespace** | Separação lógica de recursos dentro do cluster. | Não é isolamento absoluto de segurança por si só. | Separar `dev`, `staging`, `prod` ou times diferentes. |
| **Ingress / Gateway** | Expõe serviços HTTP/HTTPS para clientes externos e define regras de roteamento. | Adiciona mais um componente crítico de infraestrutura e configuração. | `api.minhaempresa.com/orders` roteando para `order-service`. |
| **Observabilidade** | Coleta de logs, métricas e traces de múltiplos Pods. | Tem custo de infraestrutura e armazenamento; alta cardinalidade pode ser cara. | Prometheus, Grafana e OpenTelemetry monitorando microsserviços Spring Boot. |
| **Affinity / Anti-Affinity** | Controla preferência ou restrição sobre onde os Pods devem ser executados. | Regras muito rígidas podem impedir scheduling. | Evitar que todas as réplicas críticas fiquem no mesmo Node. |
| **PodDisruptionBudget** | Define quantas réplicas podem ficar indisponíveis durante interrupções voluntárias. | Pode dificultar manutenção do cluster se configurado de forma muito restritiva. | Garantir que pelo menos 2 de 3 Pods de uma API continuem disponíveis. |

## Correlações mais importantes com Java

Para entrevista, eu daria prioridade especial a esta cadeia:

```text
Kubernetes
   │
   ├── Pod
   │     └── JVM / Spring Boot
   │
   ├── Requests / Limits
   │     └── Heap + Metaspace + Native Memory
   │
   ├── Probes
   │     └── Spring Boot Actuator
   │
   ├── HPA
   │     ├── mais JVMs
   │     ├── mais HikariCP pools
   │     └── mais consumidores Kafka
   │
   ├── Service
   │     └── Service Discovery
   │
   └── Rolling Update
         ├── Readiness
         └── Graceful Shutdown
```

### As relações que mais valem decorar

| Relação | Ponto importante |
|---|---|
| **Pod ↔ JVM** | Cada réplica normalmente representa uma JVM independente. |
| **Memory Limit ↔ JVM** | `Xmx` não pode ocupar toda a memória do container porque JVM usa memória fora do heap. |
| **HPA ↔ HikariCP** | Mais Pods multiplicam os pools de conexão. |
| **HPA ↔ Kafka** | Mais Pods que partitions não aumentam o paralelismo do Consumer Group. |
| **Readiness ↔ Rolling Update** | Um Pod novo só deve receber tráfego quando realmente estiver pronto. |
| **Liveness ↔ Spring Boot** | Deve detectar travamento da própria aplicação, não indisponibilidade temporária de dependências. |
| **Graceful Shutdown ↔ SIGTERM** | Evita interromper requisições e trabalhos em andamento durante deploys. |
| **Stateless ↔ Scaling** | Quanto menos estado local, mais simples escalar horizontalmente. |
| **Service ↔ DNS** | Aplicações devem chamar serviços por nomes estáveis, não IPs de Pods. |
| **HPA ↔ Arquitetura** | Escalar aplicação pode apenas transferir o gargalo para PostgreSQL, Redis, Kafka ou outra API. |

Para o nível de **Java Backend / preparação para Arquiteto**, os 10 conceitos que eu consideraria indispensáveis são: **Pod, Deployment, Service, Readiness/Liveness, Requests/Limits, JVM Memory, Stateless, HPA, Rolling Update e Graceful Shutdown**.

# Docker
Lucas, estes são os conceitos de Docker que mais valem a pena dominar pensando em **Java, Spring Boot, microsserviços e produção**.

| Conceito | O que é | Trade-off | Uso real em produção |
|---|---|---|---|
| **Docker Image** | Artefato imutável contendo aplicação, runtime e dependências necessárias para execução. | Imagens grandes aumentam tempo de build, pull, armazenamento e superfície de ataque. | Empacotar um `order-service` Java com JRE + JAR e publicar em um registry. |
| **Container** | Instância em execução de uma Docker Image. | É descartável; dados gravados somente no filesystem interno podem ser perdidos ao remover o container. | Executar várias instâncias do mesmo microsserviço a partir da mesma imagem. |
| **Dockerfile** | Arquivo declarativo que descreve como uma imagem será construída. | Dockerfiles ruins geram imagens grandes, builds lentos e problemas de segurança. | Automatizar a criação da imagem da aplicação Spring Boot no pipeline. |
| **Base Image (`FROM`)** | Imagem usada como fundação para construir outra imagem. | Imagens maiores têm mais ferramentas, mas também mais dependências e possíveis vulnerabilidades. Imagens mínimas podem dificultar troubleshooting. | Usar `eclipse-temurin:21-jre` para executar uma aplicação Java 21. |
| **JDK vs JRE** | JDK possui ferramentas de desenvolvimento/compilação; JRE/runtime contém o necessário para executar Java. | JDK facilita diagnóstico e build, mas aumenta a imagem. Runtime menor reduz tamanho e superfície de ataque. | Compilar com JDK no estágio de build e executar com JRE no estágio final. |
| **Multi-stage Build** | Dockerfile com múltiplos estágios, normalmente separando build e runtime. | Dockerfile fica um pouco mais complexo. Em troca, reduz significativamente a imagem final. | Primeiro estágio executa Maven; segundo recebe apenas o JAR e o JRE. |
| **Layer** | Camada imutável que compõe uma Docker Image. | Muitas layers ou organização ruim podem aumentar tamanho e prejudicar cache. | Separar dependências Maven do código da aplicação para reutilizar camadas. |
| **Build Cache** | Reutilização de layers previamente construídas quando suas entradas não mudaram. | Ordem incorreta das instruções do Dockerfile invalida o cache com frequência. | Copiar `pom.xml` antes do `src` para evitar baixar dependências Maven a cada mudança de código. |
| **Registry** | Repositório de imagens Docker. | Introduz dependência de infraestrutura, autenticação, armazenamento e governança de versões. | Armazenar imagens no AWS ECR, Docker Hub, GitHub Container Registry etc. |
| **Tag** | Identificador legível associado a uma versão da imagem. | Tags podem ser sobrescritas; `latest` não garante imutabilidade. | `payment-service:1.7.3` ou `payment-service:a84f9c1`. |
| **Image Digest** | Identificador criptográfico imutável do conteúdo da imagem. | É menos legível para humanos que uma tag. | Kubernetes ou pipelines usam `image@sha256:...` para garantir exatamente qual artefato está sendo executado. |
| **Port Mapping** | Mapeamento de uma porta do host para uma porta do container. | Expor serviços desnecessariamente aumenta superfície de acesso. | `8080:8080` para acessar uma API Spring Boot a partir do host. |
| **`EXPOSE`** | Metadado que documenta qual porta o container espera utilizar. | Não publica a porta automaticamente, o que causa confusão para iniciantes. | Dockerfile com `EXPOSE 8080`; publicação real é feita pelo runtime/orquestrador. |
| **Docker Network** | Rede virtual que permite comunicação entre containers. | Acrescenta uma camada de networking e exige entender DNS, portas internas e externas. | `order-service` acessa `postgres:5432` usando o nome do serviço como hostname. |
| **`localhost` em container** | Dentro de um container, `localhost` referencia o próprio container. | Configuração incorreta causa falhas de comunicação entre serviços. | Em vez de `localhost:5432`, a aplicação usa `postgres:5432` quando o banco está em outro container. |
| **Service Discovery no Compose** | Serviços conseguem se localizar pelo nome configurado no `compose.yaml`. | Funciona dentro da rede Docker; não deve ser confundido com DNS externo ou service discovery de Kubernetes. | `jdbc:postgresql://postgres:5432/orders`. |
| **Volume** | Armazenamento com ciclo de vida separado do container. | Persistência exige gerenciamento, backup e cuidado com permissões. | Persistir `/var/lib/postgresql/data` de um container PostgreSQL. |
| **Bind Mount** | Montagem de um diretório/arquivo do host dentro do container. | Cria maior acoplamento com o filesystem do host. | Montar código-fonte local durante desenvolvimento ou arquivos de configuração específicos. |
| **Environment Variables** | Forma de fornecer configuração externa ao container. | Variáveis podem aparecer em inspeções, logs ou ferramentas; não são solução ideal para segredos sensíveis isoladamente. | Configurar `DB_URL`, `SPRING_PROFILES_ACTIVE`, URLs externas etc. |
| **Externalized Configuration** | Separação entre artefato da aplicação e configuração do ambiente. | Exige disciplina e infraestrutura de configuração. | A mesma imagem roda em dev, homologação e produção com configurações diferentes. |
| **Secrets** | Credenciais, tokens, certificados e outros dados sensíveis fornecidos externamente. | Requer mecanismo específico de gerenciamento e rotação. | Senha do banco fornecida por Kubernetes Secret, AWS Secrets Manager, Vault etc. |
| **Docker Compose** | Ferramenta declarativa para executar vários containers relacionados. | Excelente para desenvolvimento e testes, mas não substitui um orquestrador distribuído como Kubernetes. | Subir Spring Boot + PostgreSQL + Redis + Kafka localmente. |
| **Healthcheck** | Verificação de saúde de um container ou serviço. | Healthchecks muito agressivos podem gerar falsos positivos ou sobrecarga. | Verificar PostgreSQL com `pg_isready` ou API por endpoint de health. |
| **Running vs Ready** | Um processo pode estar iniciado sem estar pronto para atender tráfego. | Exige health/readiness checks corretos. | PostgreSQL pode estar `running`, mas ainda inicializando schema/recovery. |
| **`depends_on`** | Expressa dependência de inicialização entre serviços no Compose. | Sozinho não significa que o serviço dependente está pronto para uso. | API espera um banco com `condition: service_healthy`. |
| **Resource Limits** | Limites de CPU e memória aplicados ao container. | Limites baixos causam throttling ou OOM; limites altos desperdiçam capacidade. | Definir `1 CPU` e `1 GiB` para um microsserviço conforme testes de carga. |
| **JVM Container Awareness** | JVM moderna reconhece limites de CPU e memória do ambiente containerizado. | Ainda é necessário validar comportamento real da aplicação e GC. | JVM dimensiona heap e paralelismo considerando recursos do container. |
| **Heap vs memória total da JVM** | Heap é apenas uma parte do consumo do processo Java. Existem Metaspace, stacks, direct buffers, native memory etc. | Configurar `-Xmx` igual ao limite do container pode provocar OOM Kill. | Container com `1 GiB` não deveria normalmente usar `-Xmx1g`. |
| **`MaxRAMPercentage`** | Configuração da JVM para calcular heap máximo como percentual da memória disponível. | Percentual inadequado pode deixar pouca memória nativa ou heap insuficiente. | `-XX:MaxRAMPercentage=70` ou `75`, calibrado por métricas e testes. |
| **CPU Limit + JVM** | Limites de CPU influenciam número efetivo de processors, GC, thread pools e throughput. | CPU muito restrita aumenta latência e pode piorar GC. | Definir recursos no container e ajustar pools Java conforme capacidade real. |
| **Container Imutável** | Container não deve ser alterado manualmente para representar uma nova versão da aplicação. | Pequenas correções exigem novo build/deploy. Em troca, há rastreabilidade e consistência. | Corrigiu código → gera `service:1.2.1` → substitui containers antigos. |
| **Ephemeral Container** | Princípio de que containers de aplicação devem poder ser destruídos e recriados sem perda do estado importante. | Estado persistente precisa estar fora do container. | Kubernetes recria um container Spring Boot após falha sem depender do filesystem anterior. |
| **Stateless Application** | Serviço não mantém estado crítico exclusivamente na instância local. | Estado precisa ir para banco, cache distribuído ou storage externo. | Várias réplicas de uma API podem atender qualquer requisição. |
| **Spring Boot Actuator** | Módulo Spring que expõe endpoints operacionais, métricas e health information. | Expor endpoints sem proteção pode revelar informações sensíveis. | `/actuator/health` usado por health checks e probes do Kubernetes. |
| **Testcontainers** | Biblioteca Java que cria containers efêmeros durante testes de integração. | Testes ficam mais pesados do que testes unitários e exigem runtime de containers. | Testar Repository contra PostgreSQL real em vez de H2. |
| **CI/CD + Docker** | Pipeline compila, testa, constrói e publica a imagem da aplicação. | Adiciona etapas de build, scanning e gerenciamento de registry. | `mvn test → docker build → scan → push ECR → deploy`. |
| **Build Once, Deploy Many** | A mesma imagem construída pelo CI é promovida entre ambientes. | Configuração precisa ser totalmente externalizada. | A imagem validada em homologação é exatamente a mesma implantada em produção. |
| **Docker + Kubernetes** | Docker/OCI produz o artefato containerizado; Kubernetes orquestra sua execução distribuída. | Kubernetes adiciona elevada complexidade operacional. | Image no ECR → Deployment Kubernetes → Pods executando containers. |

## Os que eu priorizaria para entrevista

Se precisar reduzir tudo a um núcleo de estudo, memorize esta sequência:

| Prioridade | Conceito | Por que importa |
|---|---|---|
| **1** | Image vs Container | Base de toda a containerização |
| **2** | Dockerfile | Define como seu artefato é construído |
| **3** | Layers + Cache | Impacta diretamente tempo de build |
| **4** | Multi-stage Build | Fundamental para imagens Java menores |
| **5** | JDK vs JRE | Muito relacionado a Java em produção |
| **6** | Networking + `localhost` | Erro extremamente comum |
| **7** | Port Mapping | Essencial para entender acesso aos serviços |
| **8** | Volumes | Necessário para serviços stateful |
| **9** | Healthcheck | Diferencia processo iniciado de serviço saudável |
| **10** | Resource Limits | Fundamental em produção |
| **11** | JVM Heap vs Container Memory | Muito relevante para Java |
| **12** | Docker Compose | Principalmente ambiente local/integrado |
| **13** | Testcontainers | Forte correlação com ecossistema Java |
| **14** | CI/CD + Registry | Mostra visão de entrega de software |
| **15** | Kubernetes | Evolução natural após dominar containers |

### Relação mais importante para Java

```text
Docker limit: 1 GiB
        │
        ▼
JVM container-aware
        │
        ├── Heap
        ├── Metaspace
        ├── Thread Stacks
        ├── Direct Memory
        ├── Code Cache
        └── Native Memory

-Xmx != memória total do processo
```

Essa relação entre **Docker Resource Limit → JVM → Heap → Native Memory** é uma das mais relevantes para demonstrar entendimento de Docker aplicado a Java em produção.
