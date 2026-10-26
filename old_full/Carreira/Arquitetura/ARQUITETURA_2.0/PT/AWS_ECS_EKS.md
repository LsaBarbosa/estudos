
# O que é um Orquestrador?

Um orquestrador é responsável por administrar containers automaticamente.

---

# Amazon ECS

ECS significa **Elastic Container Service**.


---

## Como funciona

Você informa:

* qual imagem Docker executar
* quantas instâncias deseja
* memória
* CPU
* portas
* rede

O ECS faz o restante.

```
Docker Image → Task Definition → ECS Service → Containers executando
```

---

# Task Definition

No ECS, uma aplicação é descrita por uma **Task Definition**.

Ela define:

* imagem Docker
* CPU
* memória
* variáveis de ambiente
* portas
* volumes
* logs

É como um "modelo" do container.

---

# Service

O Service garante que sempre exista a quantidade desejada de containers.

Por exemplo:

```
Desejo: 5 containers

Se um falhar:`ECS cria outro`

```
---

# ECS + Fargate

Uma das maiores vantagens do ECS.

Com o **AWS Fargate**, você não precisa administrar servidores.

```
Aplicação → ECS → Fargate → AWS administra infraestrutura
```

- Você apenas envia o container.

---

# ECS em EC2

Outra possibilidade.

```
Aplicação → ECS → EC2
```

##### Nesse caso você administra:

* sistema operacional | patches | capacidade | atualização das máquinas

A AWS apenas executa o ECS.

---

# Amazon EKS

EKS significa **Elastic Kubernetes Service**.

- Ela fornece um Kubernetes pronto para uso.

---

#  Kubernetes


## Por que utilizar Kubernetes?

Ele oferece muitos recursos.

| Conceito                    | Definição                                                                                                                                   | Relacionado a                            |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| **Auto Scaling**            | Ajusta automaticamente a quantidade de réplicas (Pods) ou nós do cluster conforme carga, CPU, memória ou métricas personalizadas.           | Escalabilidade e disponibilidade         |
| **Service Discovery**       | Permite que aplicações encontrem e se comuniquem entre si utilizando nomes de serviços, sem conhecer IPs dos Pods.                          | Comunicação entre serviços               |
| **Rolling Update**          | Atualiza gradualmente os Pods para uma nova versão da aplicação, reduzindo ou eliminando indisponibilidade.                                 | Deploy contínuo                          |
| **Rollback**                | Reverte uma aplicação para uma versão anterior caso o deploy apresente problemas.                                                           | Recuperação de falhas                    |
| **Secrets**                 | Armazena informações sensíveis, como senhas, tokens e certificados, de forma separada da aplicação.                                         | Segurança e gerenciamento de credenciais |
| **ConfigMaps**              | Armazena configurações não sensíveis, permitindo alterar parâmetros sem modificar a imagem da aplicação.                                    | Gerenciamento de configuração            |
| **Ingress**                 | Expõe serviços HTTP/HTTPS externamente, permitindo roteamento por domínio, caminho e suporte a TLS.                                         | Entrada de tráfego                       |
| **Persistent Volumes (PV)** | Fornecem armazenamento persistente, mantendo os dados mesmo após a recriação dos Pods.                                                      | Persistência de dados                    |
| **Operators**               | Automatizam operações complexas de aplicações, como instalação, atualização, backup e recuperação, utilizando controladores personalizados. | Automação operacional                    |
| **Políticas de Segurança**  | Definem regras para restringir privilégios, acesso à rede, execução de containers e uso de recursos do cluster.                             | Segurança do cluster                     |
| **Namespaces**              | Dividem logicamente o cluster em ambientes isolados, facilitando organização, controle de acesso e gerenciamento de recursos.               | Organização e isolamento                 |


---

# Como funciona o EKS?

- A AWS administra o **Control Plane**.
  * onde executar um Pod
  * quando recriar Pods
  * estado desejado
  * comunicação entre componentes

`AWS` → API Server | Scheduler | Controller Manager | etcd

- Você administra os **Worker Nodes** (ou utiliza Fargate).

  - Work nodes são as máquinas que executam os containers.
 
    `Worker Node` → `Pod` → `Container Java`
 

  - Esses nós podem ser:

     EC2 ou Fargate


`Sua equipe` → Aplicações | Pods | Deployments | Services


---
 

# ECS x Kubernetes

A maior diferença está na complexidade.

### ECS

Mais simples.

```
Imagem Docker → Task → Service → Executando
```



---

### Kubernetes

Possui diversos recursos.

```
Deployment → ReplicaSet → Pod → Container
```

Também envolve:

| Conceito         | Definição                                                                                                               | Relacionado a                 |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------- | ----------------------------- |
| **Ingress**      | Expõe serviços HTTP/HTTPS externamente, realizando roteamento por domínio ou caminho e podendo fornecer terminação TLS. | Entrada de tráfego            |
| **Services**     | Fornecem um ponto de acesso estável para um conjunto de Pods, realizando balanceamento de carga e service discovery.    | Comunicação entre serviços    |
| **ConfigMaps**   | Armazenam configurações não sensíveis da aplicação, desacoplando parâmetros da imagem do container.                     | Gerenciamento de configuração |
| **Secrets**      | Armazenam informações sensíveis, como senhas, tokens e certificados, com controle de acesso apropriado.                 | Segurança e credenciais       |
| **Volumes**      | Disponibilizam armazenamento para os Pods, podendo ser temporário ou persistente, conforme o tipo de volume utilizado.  | Armazenamento de dados        |
| **Namespaces**   | Criam partições lógicas dentro do cluster para organizar recursos, controlar acesso e aplicar quotas.                   | Organização e isolamento      |
| **DaemonSets**   | Garantem que um Pod seja executado em todos os nós (ou em um subconjunto deles) do cluster.                             | Serviços de infraestrutura    |
| **StatefulSets** | Gerenciam aplicações com estado, fornecendo identidade estável, armazenamento persistente e inicialização ordenada.     | Aplicações stateful           |
| **Jobs**         | Executam uma tarefa até sua conclusão com sucesso e encerram os Pods após o término.                                    | Processamento em lote         |
| **CronJobs**     | Executam Jobs de forma agendada, seguindo uma expressão cron.                                                           | Tarefas agendadas             |


É muito mais flexível.

---

# Integração com AWS

O ECS possui integração extremamente forte.

Exemplo:

| Conceito                             | Definição                                                                                                                         | Relacionado a                            |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| **IAM**                              | Gerencia identidades, usuários, grupos, funções e permissões para controlar o acesso aos recursos da AWS.                         | Controle de acesso e segurança           |
| **CloudWatch**                       | Coleta métricas, logs e eventos, permitindo monitoramento, dashboards e alertas para recursos e aplicações.                       | Observabilidade e monitoramento          |
| **Secrets Manager**                  | Armazena, gerencia e rotaciona automaticamente credenciais, senhas, chaves de API e outros segredos.                              | Segurança e gerenciamento de credenciais |
| **ECR (Elastic Container Registry)** | Serviço gerenciado para armazenar, versionar e distribuir imagens Docker e OCI.                                                   | Registro de imagens de containers        |
| **ALB (Application Load Balancer)**  | Balanceador de carga de camada 7 (HTTP/HTTPS) que distribui requisições entre aplicações e suporta roteamento por host e caminho. | Balanceamento de carga e roteamento      |
| **Auto Scaling**                     | Ajusta automaticamente a quantidade de instâncias ou tarefas em execução conforme métricas ou demanda.                            | Escalabilidade e alta disponibilidade    |
| **VPC (Virtual Private Cloud)**      | Rede virtual isolada na AWS onde são definidos sub-redes, rotas, gateways e regras de segurança para os recursos.                 | Rede e isolamento                        |


Tudo funciona praticamente de forma nativa.

---


# Quando escolher ECS?

O ECS costuma ser uma boa escolha quando:

* a empresa utiliza apenas AWS
* a equipe não possui experiência com Kubernetes
* deseja menor esforço operacional
* precisa entregar rapidamente
* quer integração nativa com os serviços AWS
* busca simplicidade

---

# Quando escolher EKS?

O EKS tende a ser mais adequado quando:

* a empresa já utiliza Kubernetes
* existem aplicações distribuídas em múltiplas nuvens
* há necessidade de portabilidade
* a equipe domina Kubernetes
* são necessários recursos avançados da plataforma
* já existe um ecossistema baseado em Helm, Operators e ferramentas do Kubernetes

---

# Comparação resumida

| Característica       | ECS                   | EKS                                |
| -------------------- | --------------------- | ---------------------------------- |
| Orquestrador         | Proprietário da AWS   | Kubernetes gerenciado              |
| Complexidade         | Baixa                 | Alta                               |
| Curva de aprendizado | Pequena               | Grande                             |
| Portabilidade        | Baixa                 | Alta                               |
| Integração com AWS   | Excelente             | Excelente                          |
| Padrão de mercado    | Não                   | Sim (Kubernetes)                   |
| Recursos avançados   | Menos                 | Muito mais                         |
| Ideal para           | Ambientes AWS simples | Ambientes complexos ou multi-cloud |

---

# Trade-offs

Nenhuma das soluções é objetivamente melhor.

* **ECS** reduz a complexidade operacional e acelera a adoção quando a infraestrutura está concentrada na AWS.
* **EKS** oferece maior flexibilidade e padronização, mas exige mais conhecimento operacional e manutenção do ecossistema Kubernetes.

A escolha deve considerar fatores como experiência da equipe, necessidade de portabilidade, maturidade operacional e estratégia de infraestrutura da organização.

---

# Resumo para entrevista

> Amazon ECS e Amazon EKS são serviços de orquestração de containers da AWS. O ECS é uma solução proprietária, mais simples de operar e profundamente integrada ao ecossistema AWS, sendo ideal para aplicações executadas exclusivamente na plataforma. Já o EKS fornece um Kubernetes gerenciado, no qual a AWS administra o plano de controle enquanto a equipe gerencia as aplicações e, normalmente, os nós do cluster. O ECS prioriza simplicidade e menor esforço operacional; o EKS prioriza flexibilidade, padronização com Kubernetes e portabilidade entre diferentes ambientes. A decisão deve ser baseada nos requisitos da arquitetura, na experiência da equipe e na estratégia de infraestrutura da empresa.
