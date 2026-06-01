# Checklist para entrevistas — Docker

## 1. Visão geral dos principais conceitos

| Tema                     | Conceito principal              | Explicação objetiva                                                                          | Resposta de entrevista                                                                                                                                                                                        |
| ------------------------ | ------------------------------- | -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Docker**               | Plataforma de containerização   | Permite empacotar aplicação + dependências em uma imagem executável em diferentes ambientes. | Docker empacota aplicações e dependências em imagens imutáveis, executadas como containers isolados. Ele reduz problemas de ambiente, facilita CI/CD e melhora previsibilidade entre dev, staging e produção. |
| **Problema que resolve** | “Na minha máquina funciona”     | Evita diferença entre ambiente local, homologação e produção.                                | Com Docker, a aplicação roda com runtime, bibliotecas, configurações e comandos definidos na imagem.                                                                                                          |
| **Docker não é VM**      | Container ≠ máquina virtual     | Container isola processos usando recursos do sistema operacional, como namespaces e cgroups. | Docker é mais leve que VM porque compartilha o kernel do host, enquanto VM virtualiza um sistema operacional completo.                                                                                        |
| **Uso em Java**          | Spring Boot, workers, consumers | Muito usado para empacotar APIs, consumers Kafka, jobs e serviços auxiliares.                | Em Java, uso Docker para padronizar APIs Spring Boot, workers, consumers Kafka e dependências como PostgreSQL, Redis e RabbitMQ.                                                                              |

---

## 2. Imagem, container, Dockerfile e registry

| Conceito       | O que é                                        | Analogia               | Exemplo                       |
| -------------- | ---------------------------------------------- | ---------------------- | ----------------------------- |
| **Dockerfile** | Arquivo que descreve como construir a imagem   | Código-fonte da imagem | `FROM eclipse-temurin:21-jre` |
| **Imagem**     | Artefato imutável com aplicação e dependências | Classe                 | `minha-api:1.0.0`             |
| **Container**  | Instância em execução de uma imagem            | Objeto                 | `docker run minha-api`        |
| **Registry**   | Local onde imagens ficam armazenadas           | Repositório de imagens | Docker Hub, AWS ECR, Harbor   |
| **Tag**        | Identificador de versão da imagem              | Versão do artefato     | `payments-api:1.0.0`          |

### Resposta de entrevista

| Pergunta                     | Resposta                                                                                                                                                                                                          |
| ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Imagem vs container**      | Imagem é um artefato imutável usado como base de execução. Container é uma instância executável dessa imagem, com processo, rede, variáveis e filesystem isolados. Uma mesma imagem pode gerar vários containers. |
| **Por que evitar `latest`?** | Porque `latest` é mutável, dificulta rastreabilidade e prejudica rollback. Em produção, prefiro tags imutáveis como versão semântica, número de build ou commit SHA.                                              |

---

## 3. Dockerfile

| Instrução     | Função                                   | Exemplo                                                         |
| ------------- | ---------------------------------------- | --------------------------------------------------------------- |
| `FROM`        | Define a imagem base                     | `FROM eclipse-temurin:21-jre`                                   |
| `WORKDIR`     | Define diretório de trabalho             | `WORKDIR /app`                                                  |
| `COPY`        | Copia arquivos para imagem               | `COPY target/app.jar app.jar`                                   |
| `RUN`         | Executa comandos durante o build         | `RUN mvn clean package`                                         |
| `EXPOSE`      | Documenta porta usada pelo container     | `EXPOSE 8080`                                                   |
| `ENV`         | Define variável de ambiente              | `ENV APP_ENV=prod`                                              |
| `CMD`         | Define comando ou argumento padrão       | `CMD ["--spring.profiles.active=prod"]`                         |
| `ENTRYPOINT`  | Define processo principal do container   | `ENTRYPOINT ["java", "-jar", "app.jar"]`                        |
| `USER`        | Define usuário que executará o processo  | `USER app`                                                      |
| `HEALTHCHECK` | Define verificação de saúde do container | `HEALTHCHECK CMD curl -f http://localhost:8080/actuator/health` |

### Dockerfile básico para Spring Boot

| Parte         | Exemplo                                  |
| ------------- | ---------------------------------------- |
| Imagem base   | `FROM eclipse-temurin:21-jre`            |
| Diretório     | `WORKDIR /app`                           |
| Cópia do JAR  | `COPY target/app.jar app.jar`            |
| Porta         | `EXPOSE 8080`                            |
| Inicialização | `ENTRYPOINT ["java", "-jar", "app.jar"]` |

### Resposta de entrevista

| Tema                 | Resposta                                                                                                                                                                                                     |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Dockerfile**       | Dockerfile define o processo de build da imagem. Em produção, evito imagens grandes, removo dependências desnecessárias, uso multi-stage build, separo build-time de runtime e não coloco secrets na imagem. |
| **Java/Spring Boot** | Para Spring Boot, geralmente construo o JAR em uma etapa e executo em uma imagem menor contendo apenas JRE.                                                                                                  |

---

## 4. Multi-stage build

| Ponto        | Exemplo ruim                                       | Exemplo correto                    |
| ------------ | -------------------------------------------------- | ---------------------------------- |
| Imagem final | Contém Maven, código-fonte e dependências de build | Contém apenas JRE e JAR final      |
| Tamanho      | Maior                                              | Menor                              |
| Segurança    | Maior superfície de ataque                         | Menor superfície de ataque         |
| Build        | `FROM maven...` e roda a aplicação ali             | Usa `maven` só no estágio de build |
| Runtime      | Maven permanece na imagem final                    | Apenas JRE permanece               |

### Exemplo ruim

```dockerfile
FROM maven:3.9-eclipse-temurin-21

WORKDIR /app

COPY . .

RUN mvn clean package

CMD ["java", "-jar", "target/app.jar"]
```

### Exemplo correto

```dockerfile
FROM maven:3.9-eclipse-temurin-21 AS build

WORKDIR /app

COPY pom.xml .
COPY src ./src

RUN mvn clean package -DskipTests

FROM eclipse-temurin:21-jre

WORKDIR /app

COPY --from=build /app/target/app.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Resposta de entrevista

| Pergunta                       | Resposta                                                                                                                                                                  |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **O que é multi-stage build?** | É uma técnica que separa a etapa de compilação da etapa de execução. Em Java, uso Maven ou Gradle no estágio de build e copio apenas o JAR final para uma imagem com JRE. |
| **Benefícios**                 | Reduz tamanho da imagem, melhora segurança, remove ferramentas desnecessárias e deixa o deploy mais limpo.                                                                |

---

## 5. Cache de build

| Estratégia                 | Ruim                                   | Melhor                                   |
| -------------------------- | -------------------------------------- | ---------------------------------------- |
| Copiar tudo antes do build | `COPY . .`                             | Copiar primeiro arquivos que mudam pouco |
| Dependências Maven         | Baixadas novamente em quase todo build | Reaproveitadas se `pom.xml` não mudar    |
| Código-fonte               | Invalida cache inteiro                 | Invalida apenas camadas posteriores      |
| CI/CD                      | Build mais lento                       | Build mais rápido                        |

### Exemplo ruim

```dockerfile
COPY . .

RUN mvn clean package
```

### Exemplo melhor

```dockerfile
COPY pom.xml .

RUN mvn dependency:go-offline

COPY src ./src

RUN mvn clean package
```

### Resposta de entrevista

| Tema               | Resposta                                                                                                                                                                                                                                        |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Cache de build** | Docker constrói imagens em camadas. Se uma camada não muda, ela pode ser reutilizada. Por isso, organizo o Dockerfile colocando primeiro arquivos que mudam pouco, como `pom.xml`, e depois o código-fonte. Isso reduz tempo de build em CI/CD. |

---

## 6. Volume vs bind mount

| Critério            | Volume                     | Bind mount                                |
| ------------------- | -------------------------- | ----------------------------------------- |
| Gerenciamento       | Docker                     | Usuário/Sistema operacional               |
| Portabilidade       | Maior                      | Menor                                     |
| Uso comum           | Persistência de dados      | Desenvolvimento local                     |
| Segurança           | Melhor controle            | Mais risco                                |
| Dependência do host | Baixa                      | Alta                                      |
| Exemplo comum       | PostgreSQL, MySQL, MongoDB | Logs locais, código local, configs locais |
| Caminho             | Gerenciado pelo Docker     | Diretório real do host                    |

### Exemplo de volume

```bash
docker volume create postgres-data
```

```bash
docker run \
  -v postgres-data:/var/lib/postgresql/data \
  postgres:16
```

### Exemplo de bind mount

```bash
docker run \
  -v ./logs:/app/logs \
  minha-api
```

### Resposta de entrevista

| Pergunta                     | Resposta                                                                                                                                                                                                                                             |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Para que servem volumes?** | Volumes persistem dados fora do ciclo de vida do container. São importantes para bancos de dados, porque containers são descartáveis.                                                                                                                |
| **Volume vs bind mount**     | Uso volumes para persistência gerenciada pelo Docker e bind mounts principalmente em desenvolvimento, quando preciso mapear arquivos locais para dentro do container. Em produção, bind mount exige mais cuidado porque pode expor arquivos do host. |

---

## 7. Redes no Docker

| Tema                  | Explicação                                                        | Exemplo                                     |
| --------------------- | ----------------------------------------------------------------- | ------------------------------------------- |
| Rede entre containers | Containers na mesma rede Docker se comunicam pelo nome do serviço | `db:5432`                                   |
| Docker Compose        | Cria uma rede padrão para os serviços                             | `api` acessa `db`                           |
| DNS interno           | Nome do serviço vira hostname                                     | `jdbc:postgresql://db:5432/app`             |
| Erro comum            | Usar `localhost` dentro do container                              | `localhost` aponta para o próprio container |

### Exemplo correto no Spring

```properties
spring.datasource.url=jdbc:postgresql://db:5432/app
```

### Exemplo errado

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/app
```

### Resposta de entrevista

| Pergunta                          | Resposta                                                                                                                                                                                             |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Como containers se comunicam?** | Em Docker Compose, serviços se comunicam pelo nome do serviço usando DNS interno. Uma API dentro de container não deve acessar PostgreSQL por `localhost`, mas pelo nome do serviço, como `db:5432`. |

---

## 8. Docker Compose

| Conceito           | Explicação                                                                                                                |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------- |
| **Docker Compose** | Ferramenta para declarar e subir múltiplos containers                                                                     |
| **Arquivo**        | Normalmente `docker-compose.yml` ou `compose.yml`                                                                         |
| **Uso comum**      | Ambiente local com API, banco, Redis, RabbitMQ, Kafka                                                                     |
| **Define**         | Serviços, redes, volumes, variáveis, portas e dependências                                                                |
| **Produção**       | Pode ser usado em cenários simples, mas em sistemas maiores costuma ser substituído por Kubernetes, ECS, Nomad ou similar |

### Exemplo

```yaml
services:
  api:
    build: .
    ports:
      - "8080:8080"
    environment:
      SPRING_PROFILES_ACTIVE: docker
      DB_HOST: db
    depends_on:
      - db
      - redis

  db:
    image: postgres:16
    environment:
      POSTGRES_DB: payments
      POSTGRES_USER: payments
      POSTGRES_PASSWORD: payments
    volumes:
      - postgres-data:/var/lib/postgresql/data

  redis:
    image: redis:7

volumes:
  postgres-data:
```

### Resposta de entrevista

| Pergunta                           | Resposta                                                                                                                                                                                                                                                                                      |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Para que serve Docker Compose?** | Docker Compose é útil para subir uma stack local com API, banco, Redis, Kafka ou outros serviços. Ele descreve serviços, redes, volumes e variáveis em YAML. Em produção, geralmente uso Kubernetes, ECS ou outro orquestrador, mas Compose é excelente para desenvolvimento e testes locais. |

---

## 9. Variáveis de ambiente e configuração

| Boa prática                    | Explicação                                                 |
| ------------------------------ | ---------------------------------------------------------- |
| Configuração externa           | A mesma imagem deve rodar em vários ambientes              |
| Não colocar secrets na imagem  | Senhas não devem estar no Dockerfile                       |
| Usar env vars                  | Banco, usuário, senha, profile e URLs devem vir de fora    |
| Separar imagem de configuração | Imagem é artefato imutável; configuração muda por ambiente |
| Usar secrets quando possível   | Em produção, usar mecanismo do orquestrador                |

### Ruim

```dockerfile
ENV DB_PASSWORD=senha_producao
```

### Melhor

```yaml
services:
  api:
    environment:
      DB_HOST: db
      DB_USER: app
      DB_PASSWORD: ${DB_PASSWORD}
```

### Spring Boot

```properties
spring.datasource.url=jdbc:postgresql://${DB_HOST}:5432/app
spring.datasource.username=${DB_USER}
spring.datasource.password=${DB_PASSWORD}
```

### Resposta de entrevista

| Tema             | Resposta                                                                                                                                                                                                                   |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Configuração** | Trato a imagem Docker como artefato imutável e configuração como algo externo. A mesma imagem deve poder rodar em dev, staging e produção, mudando apenas variáveis de ambiente, secrets ou configurações do orquestrador. |

---

## 10. `CMD` vs `ENTRYPOINT`

| Item        | `CMD`                                   | `ENTRYPOINT`                             |
| ----------- | --------------------------------------- | ---------------------------------------- |
| Função      | Comando ou argumento padrão             | Processo principal do container          |
| Sobrescrita | Fácil sobrescrever no `docker run`      | Mais fixo                                |
| Uso comum   | Argumentos padrão                       | Executável principal                     |
| Exemplo     | `CMD ["--spring.profiles.active=prod"]` | `ENTRYPOINT ["java", "-jar", "app.jar"]` |

### Uso combinado

```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]
CMD ["--spring.profiles.active=prod"]
```

### Resposta de entrevista

| Pergunta                             | Resposta                                                                                                                                                                                                                                        |
| ------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Diferença entre CMD e ENTRYPOINT** | Uso `ENTRYPOINT` para definir o processo principal do container e `CMD` para argumentos padrão que podem ser sobrescritos. Em Spring Boot, geralmente uso `ENTRYPOINT ["java", "-jar", "app.jar"]` e deixo configurações externas via ambiente. |

---

## 11. `EXPOSE` vs `ports`

| Item                   | `EXPOSE`                | `ports`                        |
| ---------------------- | ----------------------- | ------------------------------ |
| Onde fica              | Dockerfile              | Docker Compose ou `docker run` |
| Publica porta no host? | Não                     | Sim                            |
| Função                 | Documenta a porta usada | Mapeia porta container → host  |
| Exemplo                | `EXPOSE 8080`           | `"8080:8080"`                  |
| Acesso externo         | Não garante acesso      | Permite acesso pelo host       |

### Resposta de entrevista

| Pergunta                    | Resposta                                                                                                                                                      |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **EXPOSE publica a porta?** | Não. `EXPOSE` apenas documenta que o container usa determinada porta. Para acessar pelo host, preciso mapear com `-p 8080:8080` ou `ports` no Docker Compose. |

---

## 12. Logs em containers

| Boa prática                             | Explicação                                                |
| --------------------------------------- | --------------------------------------------------------- |
| Escrever em `stdout`                    | Logs devem sair na saída padrão                           |
| Escrever em `stderr`                    | Erros devem sair na saída de erro                         |
| Evitar arquivo interno como única fonte | Container é efêmero                                       |
| Centralizar logs                        | ELK, Loki, CloudWatch, Datadog                            |
| Orquestrador coleta logs                | Docker/Kubernetes/ECS conseguem coletar saída do processo |

### Evitar

```text
/app/logs/application.log
```

como única fonte de logs.

### Resposta de entrevista

| Tema     | Resposta                                                                                                                                                                                                   |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Logs** | Em containers, escrevo logs em stdout e stderr. O runtime ou orquestrador coleta esses logs e envia para uma solução centralizada. Evito depender de arquivos internos porque containers são descartáveis. |

---

## 13. Healthcheck, readiness e liveness

| Conceito                 | Explicação                                               |
| ------------------------ | -------------------------------------------------------- |
| **Healthcheck**          | Verifica se o container está saudável                    |
| **Liveness**             | Verifica se a aplicação está viva                        |
| **Readiness**            | Verifica se a aplicação está pronta para receber tráfego |
| **Spring Boot Actuator** | Expõe endpoints como `/actuator/health`                  |
| **Cuidado**              | Uma aplicação pode estar viva, mas ainda não pronta      |

### Exemplo Dockerfile

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:8080/actuator/health || exit 1
```

### Resposta de entrevista

| Pergunta                                  | Resposta                                                                                                                                                                                                                                                                |
| ----------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Como usar healthcheck em Spring Boot?** | Exponho health checks via Spring Boot Actuator. Isso permite que Docker, Kubernetes ou outro orquestrador verifique se a aplicação está saudável. Em produção, separo readiness de liveness, porque uma aplicação pode estar viva, mas não pronta para receber tráfego. |

---

## 14. Segurança em Docker

| Boa prática                        | Motivo                                      |
| ---------------------------------- | ------------------------------------------- |
| Usar imagem oficial ou confiável   | Reduz risco de imagem comprometida          |
| Evitar rodar como root             | Reduz impacto em caso de invasão            |
| Usar imagens menores               | Menor superfície de ataque                  |
| Não colocar secrets na imagem      | Evita vazamento de credenciais              |
| Usar multi-stage build             | Remove ferramentas de build da imagem final |
| Usar `.dockerignore`               | Evita copiar arquivos sensíveis             |
| Fixar versões                      | Melhora previsibilidade                     |
| Escanear vulnerabilidades          | Detecta CVEs no pipeline                    |
| Remover ferramentas desnecessárias | Reduz vetores de ataque                     |

### Exemplo com usuário não-root

```dockerfile
FROM eclipse-temurin:21-jre

WORKDIR /app

RUN addgroup --system app && adduser --system --ingroup app app

COPY target/app.jar app.jar

USER app

ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Resposta de entrevista

| Tema          | Resposta                                                                                                                                                                                                                                                                                    |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Segurança** | Em produção, evito rodar containers como root, reduzo o tamanho da imagem, removo dependências de build, não coloco secrets no Dockerfile e uso scanners de vulnerabilidade no pipeline. Também prefiro multi-stage build para que Maven, Gradle e código-fonte não fiquem na imagem final. |

---

## 15. `.dockerignore`

| O que ignora         | Por que ignorar                           |
| -------------------- | ----------------------------------------- |
| `.git`               | Reduz contexto e evita expor histórico    |
| `target`             | Evita copiar builds locais desnecessários |
| `.idea`              | Remove arquivos da IDE                    |
| `*.log`              | Evita logs no contexto de build           |
| `.env`               | Evita vazamento de secrets                |
| `docker-compose.yml` | Normalmente não precisa estar na imagem   |
| arquivos temporários | Reduz tamanho do contexto                 |

### Exemplo

```text
.git
target
.idea
*.log
.env
docker-compose.yml
```

### Resposta de entrevista

| Tema              | Resposta                                                                                                                                                                                           |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **.dockerignore** | Uso `.dockerignore` para reduzir o contexto de build, acelerar builds e evitar vazamento de arquivos sensíveis. É parecido com `.gitignore`, mas aplicado ao contexto enviado para o Docker build. |

---

## 16. Java em Docker

| Ponto de atenção   | Explicação                                   |
| ------------------ | -------------------------------------------- |
| Heap               | Não deve consumir todo o limite do container |
| Metaspace          | Também consome memória                       |
| Threads            | Cada thread consome stack                    |
| Buffers nativos    | Podem consumir memória fora do heap          |
| GC                 | Precisa considerar limite real do container  |
| `MaxRAMPercentage` | Ajuda a controlar uso de heap                |
| Métricas           | Devem ser observadas em ambiente real        |
| CPU                | Limites de CPU impactam GC e throughput      |

### Exemplo

```dockerfile
ENTRYPOINT ["java", "-XX:MaxRAMPercentage=75", "-jar", "app.jar"]
```

### Resposta de entrevista

| Pergunta                                 | Resposta                                                                                                                                                                                                                                                                  |
| ---------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Quais cuidados com JVM em container?** | Em Java containerizado, não penso só em `-Xmx`. A JVM usa heap, metaspace, stacks de threads, buffers nativos e memória do processo. Por isso, uso parâmetros como `MaxRAMPercentage`, observo métricas reais e evito configurar heap igual ao limite total do container. |

---

## 17. Dockerfile recomendado para Spring Boot

| Cenário     | Dockerfile                                                                                |
| ----------- | ----------------------------------------------------------------------------------------- |
| Simples     | Usa JAR já gerado localmente                                                              |
| Multi-stage | Compila com Maven/Gradle e gera imagem final com JRE                                      |
| Produção    | Preferir multi-stage, usuário não-root, `.dockerignore`, variáveis externas e healthcheck |

### Dockerfile simples

```dockerfile
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY target/payment-api.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-XX:MaxRAMPercentage=75", "-jar", "app.jar"]
```

### Multi-stage com Maven

```dockerfile
FROM maven:3.9-eclipse-temurin-21 AS build

WORKDIR /app

COPY pom.xml .
RUN mvn dependency:go-offline

COPY src ./src
RUN mvn clean package -DskipTests

FROM eclipse-temurin:21-jre

WORKDIR /app

COPY --from=build /app/target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-XX:MaxRAMPercentage=75", "-jar", "app.jar"]
```

### Resposta de entrevista

| Tema                       | Resposta                                                                                                                                                                                                                                                         |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Spring Boot com Docker** | Para Spring Boot, prefiro imagem final somente com JRE, não com Maven ou Gradle. Também separo dependências do código-fonte para melhorar cache, uso variáveis externas para configuração, exponho health check com Actuator e trato o container como stateless. |

---

## 18. Stateless vs stateful

| Tipo          | Explicação                         | Exemplos                                                   | Cuidados                                                 |
| ------------- | ---------------------------------- | ---------------------------------------------------------- | -------------------------------------------------------- |
| **Stateless** | Não guarda estado local importante | API REST, worker, consumer Kafka, scheduler                | Escala horizontalmente melhor                            |
| **Stateful**  | Guarda estado persistente          | PostgreSQL, MongoDB, Redis com persistência, Elasticsearch | Exige volume, backup, restore, replicação e consistência |

### Resposta de entrevista

| Pergunta                              | Resposta                                                                                                                                                                                                                                                                                                        |
| ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Como tratar estado em containers?** | APIs Spring Boot devem ser preferencialmente stateless. Isso facilita escalabilidade horizontal, deploy, rollback e resiliência. Estado deve ficar em banco, cache distribuído, storage externo ou mensageria. Containers stateful exigem mais cuidado com volumes, backup, restore, replicação e consistência. |

---

## 19. Docker em CI/CD

| Etapa           | Objetivo                         |
| --------------- | -------------------------------- |
| `commit`        | Código entra no repositório      |
| `build`         | Aplicação é compilada            |
| `test`          | Testes são executados            |
| `docker build`  | Imagem é construída              |
| `security scan` | Vulnerabilidades são verificadas |
| `docker push`   | Imagem é publicada no registry   |
| `deploy`        | Imagem é implantada no ambiente  |

### Fluxo

```text
commit
  ↓
build
  ↓
test
  ↓
docker build
  ↓
security scan
  ↓
docker push
  ↓
deploy
```

### Resposta de entrevista

| Tema                | Resposta                                                                                                                                                                                                                                                                      |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Docker no CI/CD** | Docker permite gerar uma imagem versionada e promover o mesmo artefato entre ambientes. O ideal é buildar uma vez, versionar com tag imutável, publicar em um registry e usar a mesma imagem em staging e produção. Isso reduz diferença entre ambientes e facilita rollback. |

---

## 20. Docker vs Kubernetes

| Critério       | Docker                           | Kubernetes                              |
| -------------- | -------------------------------- | --------------------------------------- |
| Foco           | Build e execução de containers   | Orquestração de containers em escala    |
| Ambiente comum | Desenvolvimento e execução local | Produção, alta disponibilidade e escala |
| Rede           | Redes Docker locais              | Services, ingress, DNS interno          |
| Escalabilidade | Manual ou limitada               | Autoscaling                             |
| Deploy         | `docker run`, Compose            | Deployments, StatefulSets, Jobs         |
| Rollback       | Manual ou por plataforma externa | Recursos nativos de rollout/rollback    |
| Self-healing   | Limitado                         | Recria pods automaticamente             |
| Configuração   | Env vars, Compose                | ConfigMaps e Secrets                    |
| Healthcheck    | Docker healthcheck               | Liveness/readiness probes               |

### Resposta de entrevista

| Pergunta                         | Resposta                                                                                                                                                                                                                            |
| -------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Docker substitui Kubernetes?** | Não. Docker resolve empacotamento e execução de containers. Kubernetes resolve orquestração em escala, incluindo scheduling, autoscaling, service discovery, rolling update, self-healing, secrets, config maps, probes e rollback. |

---

## 21. Troubleshooting em Docker

| Problema                 | Comandos úteis                        | O que verificar                                     |
| ------------------------ | ------------------------------------- | --------------------------------------------------- |
| Container não sobe       | `docker ps -a`, `docker logs`         | Erro de startup, variável ausente, comando inválido |
| Container reiniciando    | `docker logs container`               | Stacktrace, profile, conexão com banco              |
| Porta não responde       | `docker ps`, `docker port`            | Porta mapeada, aplicação escutando na porta correta |
| API não conecta no banco | `docker inspect`, `docker network ls` | Host errado, rede, porta, readiness do banco        |
| Lentidão/memória         | `docker stats`                        | CPU, memória, OOM, configuração da JVM              |
| Volume não persiste      | `docker volume ls`, `docker inspect`  | Volume correto, path correto, permissões            |
| Debug interno            | `docker exec -it container sh`        | Arquivos, env vars, conectividade interna           |

### Comandos essenciais

```bash
docker ps
docker ps -a
docker logs container
docker exec -it container sh
docker inspect container
docker stats
docker network ls
docker volume ls
docker port container
```

### Resposta de entrevista

| Tema                | Resposta                                                                                                                                                                                                                                                           |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Troubleshooting** | Quando um container falha, começo por `docker ps`, `docker logs` e `docker inspect`. Depois valido variáveis, portas, rede, volumes e comando de inicialização. Em aplicações Java, também verifico heap, erro de conexão com banco, profile ativo e health check. |

---

## 22. Problemas comuns em Docker

| Problema                        | Causa                                                            | Solução                                            |
| ------------------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
| Usar `localhost` errado         | Dentro do container, `localhost` aponta para o próprio container | Usar nome do serviço, como `db:5432`               |
| Imagem gigante                  | JDK completo, Maven na imagem final, `.git` copiado              | Multi-stage build, `.dockerignore`, imagem menor   |
| Secrets na imagem               | Senhas no Dockerfile ou dentro da imagem                         | Usar env vars, secrets ou config do orquestrador   |
| Estado local no container       | Uploads/sessões salvos no filesystem interno                     | Usar S3, MinIO, volume, banco ou storage externo   |
| Aplicação inicia antes do banco | `depends_on` não garante readiness completa                      | Retry/backoff, healthcheck, readiness              |
| Falha de permissão em volume    | Usuário do container sem acesso ao path                          | Ajustar usuário, grupo, ownership e permissões     |
| JVM estoura memória             | Heap configurado igual ao limite total                           | Usar `MaxRAMPercentage`, monitorar heap e non-heap |
| Porta não acessível             | Usou `EXPOSE`, mas não publicou porta                            | Usar `-p` ou `ports` no Compose                    |

### Resposta de entrevista

| Tema                 | Resposta                                                                                                                                                                                                                                                                                                                            |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Problemas comuns** | Os erros mais comuns são usar `localhost` incorretamente, criar imagens grandes, colocar secrets no Dockerfile, não usar `.dockerignore`, não configurar memória da JVM, depender de estado local e achar que `depends_on` resolve readiness. Em produção, a aplicação precisa ser resiliente à inicialização parcial dos serviços. |

---

## 23. Trade-offs do Docker

| Vantagens                           | Desvantagens                                  |
| ----------------------------------- | --------------------------------------------- |
| Portabilidade                       | Adiciona complexidade operacional             |
| Ambientes reproduzíveis             | Networking pode confundir                     |
| Deploy consistente                  | Problemas de volume e permissão               |
| Isolamento                          | Imagem mal construída gera risco de segurança |
| Integração com CI/CD                | Observabilidade precisa ser pensada           |
| Facilidade para dependências locais | Java exige atenção com memória                |
| Melhor rollback                     | Debugging pode ser mais trabalhoso            |
| Base para Kubernetes/ECS            | Exige maturidade em operação                  |

### Resposta de entrevista

| Tema           | Resposta                                                                                                                                                                                                                                                                                                                                                |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Trade-offs** | Docker melhora previsibilidade e entrega, mas não elimina complexidade. Ele muda o tipo de problema: saímos de problemas de instalação manual e entramos em problemas de imagem, rede, volume, segurança, observabilidade e orquestração. Um uso maduro exige boas práticas de build, configuração externa, logs, health checks e controle de recursos. |

---

# Quadro principal para revisão rápida

| Nº | Tema                   | Preciso saber explicar? | Resumo para entrevista                                                                                   |
| -: | ---------------------- | ----------------------- | -------------------------------------------------------------------------------------------------------- |
|  1 | Docker                 | Sim                     | Plataforma de containerização para empacotar, distribuir e executar aplicações isoladas e reproduzíveis. |
|  2 | Imagem vs container    | Sim                     | Imagem é o template imutável; container é a instância em execução.                                       |
|  3 | Dockerfile             | Sim                     | Arquivo declarativo que define como construir a imagem.                                                  |
|  4 | Multi-stage build      | Sim                     | Separa build e runtime, reduz tamanho e melhora segurança.                                               |
|  5 | Cache de build         | Sim                     | Docker reutiliza camadas quando instruções e arquivos não mudam.                                         |
|  6 | Volumes                | Sim                     | Persistem dados fora do ciclo de vida do container.                                                      |
|  7 | Bind mount vs volume   | Sim                     | Volume é gerenciado pelo Docker; bind mount usa path real do host.                                       |
|  8 | Redes Docker           | Sim                     | Containers se comunicam pelo nome do serviço na mesma rede.                                              |
|  9 | Docker Compose         | Sim                     | Define múltiplos serviços, redes, volumes e variáveis em YAML.                                           |
| 10 | Env vars               | Sim                     | Configuração deve ser externa à imagem.                                                                  |
| 11 | CMD vs ENTRYPOINT      | Sim                     | `ENTRYPOINT` define processo principal; `CMD` define argumento/comando padrão.                           |
| 12 | EXPOSE vs ports        | Sim                     | `EXPOSE` documenta; `ports` publica no host.                                                             |
| 13 | Logs                   | Sim                     | Containers devem logar em stdout/stderr.                                                                 |
| 14 | Healthcheck            | Sim                     | Verifica saúde do container/aplicação.                                                                   |
| 15 | Segurança              | Sim                     | Não-root, imagem menor, sem secrets, scan, `.dockerignore`.                                              |
| 16 | `.dockerignore`        | Sim                     | Evita enviar arquivos desnecessários ou sensíveis para o build.                                          |
| 17 | Java em Docker         | Sim                     | Controlar heap, metaspace, threads, buffers e limites do container.                                      |
| 18 | Spring Boot Dockerfile | Sim                     | Preferir JRE final, multi-stage, env vars e healthcheck.                                                 |
| 19 | Stateless vs stateful  | Sim                     | APIs devem ser stateless; estado fica fora do container.                                                 |
| 20 | CI/CD                  | Sim                     | Buildar imagem versionada, escanear, publicar e promover entre ambientes.                                |
| 21 | Registry e tags        | Sim                     | Registry armazena imagens; tags versionam; evitar `latest` em produção.                                  |
| 22 | Docker vs Kubernetes   | Sim                     | Docker executa containers; Kubernetes orquestra containers em escala.                                    |
| 23 | Troubleshooting        | Sim                     | Usar `logs`, `ps`, `inspect`, `exec`, `stats`, redes e volumes.                                          |
| 24 | Problemas comuns       | Sim                     | `localhost`, readiness, secrets, volumes, permissões, memória JVM.                                       |
| 25 | Trade-offs             | Sim                     | Padroniza deploy, mas adiciona complexidade operacional.                                                 |
| 26 | Resposta Senior        | Sim                     | Conectar Docker com build, segurança, Java, observabilidade e operação.                                  |

---

# Resposta final de nível Senior

| Estrutura            | Resposta                                                                                                                                                                                                             |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Definição**        | Docker é uma ferramenta de containerização que permite empacotar uma aplicação e suas dependências em imagens imutáveis, executadas como containers isolados.                                                        |
| **Uso em Java**      | Em sistemas Java modernos, uso Docker para padronizar ambientes, acelerar CI/CD e facilitar deploy de APIs Spring Boot, workers e consumers.                                                                         |
| **Produção**         | Em produção, me preocupo com tamanho da imagem, multi-stage build, segurança, usuário não-root, configuração externa, logs em stdout, health checks, volumes, rede entre containers e limites de CPU/memória da JVM. |
| **Boas práticas**    | Evito `latest`, não coloco secrets na imagem, uso `.dockerignore`, faço scan de vulnerabilidades e trato containers de aplicação como stateless.                                                                     |
| **Limite do Docker** | Docker não substitui arquitetura nem orquestração. Ele resolve empacotamento e execução; escala, alta disponibilidade e self-healing normalmente ficam com Kubernetes, ECS ou outra plataforma.                      |
| **Trade-off**        | Docker melhora consistência e portabilidade, mas exige maturidade em build, segurança, observabilidade, configuração e operação.                                                                                     |

---

# Checklist final para entrevista

| Pergunta de revisão                                                 | Status |
| ------------------------------------------------------------------- | ------ |
| Sei explicar Docker sem confundir com máquina virtual?              | ☐      |
| Sei diferenciar imagem, container, Dockerfile e registry?           | ☐      |
| Sei criar um Dockerfile para aplicação Spring Boot?                 | ☐      |
| Sei usar multi-stage build?                                         | ☐      |
| Sei otimizar cache de build?                                        | ☐      |
| Sei explicar volume vs bind mount?                                  | ☐      |
| Sei explicar rede entre containers?                                 | ☐      |
| Sei usar Docker Compose para API + banco + Redis?                   | ☐      |
| Sei configurar variáveis de ambiente corretamente?                  | ☐      |
| Sei diferenciar `CMD` e `ENTRYPOINT`?                               | ☐      |
| Sei explicar `EXPOSE` vs `ports`?                                   | ☐      |
| Sei lidar com logs em containers?                                   | ☐      |
| Sei configurar healthcheck?                                         | ☐      |
| Sei boas práticas de segurança?                                     | ☐      |
| Sei usar `.dockerignore`?                                           | ☐      |
| Sei explicar cuidados da JVM em containers?                         | ☐      |
| Sei diferenciar stateless e stateful?                               | ☐      |
| Sei explicar Docker em CI/CD?                                       | ☐      |
| Sei usar tags imutáveis?                                            | ☐      |
| Sei diferenciar Docker e Kubernetes?                                | ☐      |
| Sei diagnosticar problemas com `logs`, `exec`, `inspect` e `stats`? | ☐      |
| Sei explicar trade-offs de Docker em produção?                      | ☐      |
