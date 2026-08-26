# FASE 12 — Kubernetes

Lucas, para um Senior/Tech Lead, o objetivo não é administrar o cluster inteiro. É entender **como a aplicação é executada, exposta, escalada e protegida em produção**, além de conseguir diagnosticar por que um workload não sobe ou não recebe tráfego.

## 1. Conceitos, trade-offs e casos de uso

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **Pod** | Menor unidade executável do Kubernetes. Agrupa um ou mais containers que compartilham rede e lifecycle. | Pods são efêmeros; não devem ser tratados como servidores permanentes. | Executar uma instância de uma aplicação Spring Boot. |
| **Deployment** | Controller declarativo para aplicações normalmente stateless. Gerencia ReplicaSets, replicas, updates e rollback. | Excelente para stateless; não resolve sozinho requisitos de identidade/storage de workloads stateful. | APIs e microsserviços. |
| **ReplicaSet** | Garante que determinada quantidade de Pods esteja executando. | Normalmente não deve ser gerenciado diretamente; Deployment fica acima dele. | Manter 3 replicas da API disponíveis. |
| **Service** | Endpoint estável que representa um conjunto dinâmico de Pods e distribui tráfego para eles. | Adiciona abstração de rede; selectors incorretos podem deixar o Service sem endpoints. | `order-service` acessível pelos demais microsserviços. |
| **Ingress** | Define roteamento HTTP/HTTPS externo para Services por host/path. | Precisa de um Ingress Controller. A API Ingress está estável, mas congelada; o projeto Kubernetes atualmente recomenda Gateway API para novas capacidades. | `api.exemplo.com/orders` → Order Service. |
| **ConfigMap** | Armazena configuração **não sensível** fora da imagem do container. | Mudanças nem sempre reiniciam automaticamente a aplicação; não serve para segredo. | URL de serviço, feature configuration, propriedades. |
| **Secret** | Armazena pequena quantidade de dados sensíveis, como senha, token e chave. | Secret não significa segurança automática; acesso precisa ser protegido com RBAC e outras medidas. | Credenciais de banco, tokens, certificados. |
| **Namespace** | Cria escopo lógico para recursos dentro de um cluster. | Não é isolamento de segurança completo sozinho; normalmente combina com RBAC, quotas e NetworkPolicies. | Separar times, projetos ou ambientes. |
| **Requests** | Quantidade de CPU/memória solicitada pelo container; usada principalmente pelo scheduler. | Request alto desperdiça capacidade; baixo demais favorece overcommit e instabilidade. | Garantir capacidade mínima para uma API Java. |
| **Limits** | Limite máximo de recurso que o container pode consumir. | CPU pode sofrer throttling; memória pode resultar em OOM kill. | Impedir um workload de consumir todo o node. |
| **Liveness Probe** | Responde: **a aplicação ainda consegue progredir?** Falhas repetidas podem causar restart do container. | Probe agressiva pode gerar restart loops e cascading failures. | Detectar deadlock ou processo travado. |
| **Readiness Probe** | Responde: **a aplicação está pronta para receber tráfego?** | Configuração errada pode retirar Pods saudáveis do tráfego. | Não receber requests enquanto banco/configuração ainda não está pronta. |
| **Startup Probe** | Indica que a aplicação ainda está inicializando; enquanto não passa, protege contra liveness/readiness prematuras. | Valores altos demais escondem startup problemático. | Aplicações Java/Spring com startup ou warm-up demorado. |
| **HPA** | Horizontal Pod Autoscaler altera dinamicamente a quantidade de replicas com base em CPU, memória ou métricas customizadas/externas. | Scaling não é instantâneo e a métrica precisa representar capacidade real. | Escalar checkout conforme CPU ou tamanho de fila. |
| **PDB** | PodDisruptionBudget limita quantos Pods podem ficar indisponíveis durante **disrupções voluntárias**. | Configuração muito rígida pode bloquear drain/manutenção de nodes. | Manter pelo menos 2 de 3 replicas durante manutenção. |
| **Rolling Update** | Substitui gradualmente Pods antigos por novos durante um deploy. | Durante um período coexistem duas versões; exige compatibilidade de contratos. | Deploy sem indisponibilidade planejada. |
| **Rollback** | Retorna um Deployment para uma revisão anterior do Pod template. | Banco/schema e efeitos externos nem sempre são revertidos junto com a aplicação. | Nova versão apresenta crash ou regressão. |
| **CrashLoopBackOff** | Container inicia, falha repetidamente e Kubernetes aumenta progressivamente o intervalo entre restarts. | O restart automático não corrige bug/configuração incorreta. | Exceção no startup, config ausente, conexão obrigatória falhando. |
| **OOMKilled** | Processo/container foi encerrado por falta de memória, frequentemente ao ultrapassar o memory limit. | Aumentar limit sem investigar pode apenas esconder leak ou sizing incorreto. | Heap JVM maior que o orçamento do container. |
| **Pending** | Pod existe, mas ainda não conseguiu ser colocado em execução. | Pode indicar falta de CPU/memória, constraints de scheduling, volumes etc. | Request de CPU não cabe em nenhum node. |
| **ImagePullBackOff** | Kubernetes não consegue obter a imagem e passa a tentar novamente com backoff. | O Pod nunca inicia enquanto a causa persistir. | Tag inexistente, registry privado ou credencial inválida. |
| **Readiness failing** | Container está rodando, mas não está sendo considerado pronto para receber tráfego. | Replica existe, mas capacidade útil pode cair. | Endpoint de health falhando ou dependência obrigatória indisponível. |
| **CPU throttling** | Kernel restringe tempo de CPU quando o container atinge seu CPU limit. | A aplicação continua rodando, mas pode apresentar aumento significativo de latência. | Java utilizando mais CPU que o limite configurado. |

Deployments fazem rolling updates gerenciando ReplicaSets antigos e novos, com parâmetros como `maxUnavailable` e `maxSurge`; também mantêm histórico que permite rollback. 

---

# 2. Relação entre Pod, ReplicaSet e Deployment

Esse fluxo precisa ficar claro:

```text
Deployment
    ↓
ReplicaSet
    ↓
┌─────┬─────┬─────┐
Pod   Pod   Pod
```

Você normalmente declara:

```text
Deployment
replicas = 3
```

O Deployment gerencia o ReplicaSet.

O ReplicaSet garante:

```text
3 Pods desejados
```

Se um Pod morrer:

```text
3 desejados
2 existentes
      ↓
ReplicaSet cria outro
```

Por isso você normalmente **não cria Pods manualmente** para aplicações de produção.

---

# 3. Service

Pods possuem lifecycle dinâmico.

Hoje:

```text
Pod A = 10.0.1.10
Pod B = 10.0.1.11
```

Depois de um deploy:

```text
Pod C = 10.0.2.20
Pod D = 10.0.2.21
```

Clientes não deveriam acompanhar esses IPs.

O Service fornece:

```text
order-service
     ↓
endpoint estável
     ↓
Pods atuais
```

Essa abstração desacopla os clientes do conjunto mutável de Pods. 

---

# 4. Ingress

Um fluxo externo típico:

```text
Internet
   ↓
Ingress Controller
   ↓
Ingress Rules
   ↓
Service
   ↓
Pods
```

Exemplo:

```text
api.company.com/orders
          ↓
Order Service
```

e:

```text
api.company.com/payments
          ↓
Payment Service
```

O Ingress trabalha principalmente com HTTP/HTTPS e pode realizar roteamento por hostname/path e TLS termination. Um detalhe atual importante: a API Ingress continua estável, porém está congelada, e a documentação oficial recomenda considerar **Gateway API** para evolução de networking. 

---

# 5. ConfigMap x Secret

A diferença precisa estar automática.

### ConfigMap

```text
não sensível
```

Exemplos:

```text
PAYMENT_URL
FEATURE_X_ENABLED
LOG_LEVEL
```

### Secret

```text
sensível
```

Exemplos:

```text
DATABASE_PASSWORD
API_TOKEN
TLS_CERTIFICATE
```

ConfigMap existe justamente para separar configuração da imagem do container. 

Um cuidado importante:

> `Secret` não significa que o valor está automaticamente criptografado de ponta a ponta.

A documentação recomenda restringir acesso com RBAC e proteger o armazenamento dos Secrets adequadamente. 

---

# 6. Requests x Limits

Esse é um dos pontos mais importantes para aplicações Java.

Considere:

```yaml
resources:
  requests:
    cpu: 500m
    memory: 512Mi
  limits:
    cpu: "1"
    memory: 1Gi
```

### Request

Diz aproximadamente:

> Para colocar este Pod em um node, considere que ele precisa desta quantidade de recurso.

O scheduler utiliza requests para decidir onde o Pod cabe. 

### Limit

Diz:

> O container não deve ultrapassar este orçamento.

Mas CPU e memória se comportam diferente.

---

# 7. CPU Limit

Se:

```text
CPU limit = 1 CPU
```

e a aplicação tenta consumir mais:

```text
Java
 ↓
quer mais CPU
 ↓
cgroup limit
 ↓
CPU throttling
```

O container normalmente **não morre** por exceder CPU.

Ele recebe menos tempo de CPU.

Resultado possível:

```text
CPU aparentemente controlada
       +
p99 aumentando
```

CPU limits são aplicados através de cgroups e podem gerar throttling. 

---

# 8. Memory Limit

Memória é diferente.

Imagine:

```text
limit = 1Gi
```

Mas JVM + memória nativa chegam ao limite.

O kernel pode encerrar o processo.

Então aparece:

```text
OOMKilled
```

Esse assunto conecta diretamente Kubernetes com JVM:

```text
Heap
+
Metaspace
+
Thread Stacks
+
Direct Buffers
+
Code Cache
+
Native Memory
```

precisam caber dentro do orçamento do container.

O limite de memória é aplicado através de cgroups e pode resultar em OOM kill. 

---

# 9. Liveness x Readiness x Startup

Memorize assim:

```text
Liveness
   ↓
devo reiniciar?


Readiness
   ↓
devo receber tráfego?


Startup
   ↓
já terminei de inicializar?
```

Essa distinção aparece bastante em entrevista.

---

# 10. Liveness

Liveness responde:

> O processo está funcionando de forma que um restart possa recuperá-lo?

Exemplo:

```text
Java process está vivo
       ↓
mas entrou em deadlock
       ↓
liveness falha
       ↓
container restart
```

Não coloque qualquer dependência externa na liveness sem pensar.

Imagine:

```text
Database caiu
     ↓
100 Pods falham liveness
     ↓
100 Pods reiniciam
```

Agora você transformou:

```text
falha de banco
```

em:

```text
falha de banco
+
tempestade de restarts
```

A própria documentação alerta que liveness incorreta pode provocar cascading failures. 

---

# 11. Readiness

Readiness responde:

> Posso enviar novas requisições para este Pod?

Imagine:

```text
Pod Running
```

mas:

```text
cache ainda carregando
```

ou:

```text
aplicação temporariamente incapaz
de atender requests
```

Então:

```text
readiness = false
```

O container continua vivo.

Mas o Pod deixa de receber tráfego do Service enquanto não estiver Ready. 

Isso é diferente de liveness:

```text
Readiness falhou
      ↓
não recebe tráfego


Liveness falhou
      ↓
container pode reiniciar
```

---

# 12. Startup Probe

Aplicações Java podem levar algum tempo para:

```text
subir JVM
carregar classes
inicializar Spring
criar pools
realizar warm-up
```

Se a liveness começar cedo demais:

```text
Spring iniciando
      ↓
liveness falha
      ↓
restart
      ↓
Spring iniciando
      ↓
restart
```

Você criou um loop.

Startup Probe resolve isso.

Enquanto ela não passa, Kubernetes não executa liveness/readiness normalmente; quando passa, as outras probes assumem. 

---

# 13. HPA

Horizontal Pod Autoscaler responde:

> Quantos Pods preciso agora?

Exemplo:

```text
3 Pods

CPU aumenta
    ↓
HPA
    ↓
6 Pods
```

Depois:

```text
tráfego cai
    ↓
HPA
    ↓
3 Pods
```

O HPA pode utilizar:

```text
CPU
memory
custom metrics
external metrics
```

e ajusta o scale target, como um Deployment. 

---

# 14. HPA e CPU Request

Esse detalhe é muito importante.

Quando HPA utiliza:

```text
CPU utilization = 70%
```

esse percentual normalmente é calculado em relação ao:

```text
CPU request
```

Imagine:

```text
request = 500m

uso atual = 350m
```

Então aproximadamente:

```text
350 / 500
=
70%
```

Se requests estão totalmente errados, a decisão do HPA também pode ficar ruim. A documentação destaca que HPA baseado em utilização depende dos resource requests configurados. 

---

# 15. Nem sempre escalar por CPU é suficiente

Imagine um consumer Kafka.

CPU:

```text
30%
```

Mas:

```text
consumer lag
=
2 milhões de mensagens
```

Nesse caso, uma métrica melhor pode ser:

```text
Kafka consumer lag
```

Outro exemplo:

```text
queue depth
```

Por isso HPA suporta custom e external metrics. 

---

# 16. PDB

PodDisruptionBudget protege disponibilidade durante **disrupções voluntárias**.

Imagine:

```text
Deployment
3 replicas
```

Você define:

```text
minAvailable = 2
```

Durante:

```text
node drain
```

Kubernetes tenta respeitar:

```text
pelo menos 2 Pods disponíveis
```

Ou:

```text
maxUnavailable = 1
```

O ponto importante:

> PDB não protege contra toda falha.

Se o node simplesmente morrer:

```text
PDB
```

não impede essa indisponibilidade.

Ele controla principalmente **evictions voluntárias**, como manutenção/drain. 

---

# 17. Rolling Update

Um Deployment normalmente consegue atualizar Pods gradualmente:

```text
Version 1
Pod V1
Pod V1
Pod V1

       ↓ rollout

Pod V1
Pod V1
Pod V2

       ↓

Pod V1
Pod V2
Pod V2

       ↓

Pod V2
Pod V2
Pod V2
```

Duas propriedades importantes:

```text
maxUnavailable
```

e:

```text
maxSurge
```

controlam quantos Pods podem ficar indisponíveis e quantos adicionais podem existir durante o rollout. 

---

# 18. Rolling Update exige compatibilidade

Esse ponto é muito importante para arquitetura.

Durante o deploy podem existir:

```text
API v1
+
API v2
```

simultaneamente.

Por isso alterações como:

```text
schema do banco
event schema
API contracts
```

precisam ser compatíveis durante a janela de rollout.

Um deploy tecnicamente rolling pode falhar se:

```text
v2 altera tabela
       ↓
v1 não consegue mais funcionar
```

---

# 19. Rollback

Se uma versão nova apresenta problema:

```text
Deployment revision 10
       ↓
bug
```

podemos retornar:

```text
revision 9
```

O Kubernetes mantém histórico de revisões do Pod template para permitir rollback. 

Mas existe uma limitação importante:

```text
Kubernetes rollback
≠
rollback completo do sistema
```

Se a nova versão:

```text
alterou banco
publicou eventos
enviou pagamentos
```

esses efeitos não desaparecem automaticamente.

---

# 20. Diagnóstico: CrashLoopBackOff

Sintoma:

```text
Pod
 ↓
container inicia
 ↓
crash
 ↓
restart
 ↓
crash
 ↓
backoff
```

Primeiras verificações:

```bash
kubectl describe pod <pod>
kubectl logs <pod>
kubectl logs <pod> --previous
```

Procure:

```text
Exception no startup
configuração ausente
Secret ausente
porta errada
liveness incorreta
OOM
dependência obrigatória indisponível
```

O ponto é:

> `CrashLoopBackOff` não é a causa raiz; é o comportamento resultante de repetidos crashes.

---

# 21. Diagnóstico: OOMKilled

Se:

```text
Last State:
Terminated

Reason:
OOMKilled
```

investigue:

```text
memory limit
heap
Metaspace
Direct Memory
thread count
native memory
memory leak
```

Para Java, não raciocine:

```text
-Xmx = 1Gi
```

então:

```text
container pode ter limit = 1Gi
```

porque JVM utiliza memória fora do Heap.

---

# 22. Diagnóstico: Pending

Pod em:

```text
Pending
```

normalmente significa:

> Kubernetes ainda não conseguiu colocá-lo para executar.

Use:

```bash
kubectl describe pod <pod>
```

e veja `Events`.

Uma causa clássica:

```text
Pod request
CPU = 4

Nodes disponíveis
máximo livre = 2
```

Resultado:

```text
Unschedulable
```

O scheduler considera os requests na decisão de placement, mesmo que o uso real naquele momento esteja baixo. 

Outras causas podem envolver volumes, affinity, taints/tolerations ou outras restrições de scheduling.

---

# 23. Diagnóstico: ImagePullBackOff

Fluxo:

```text
Pod
 ↓
pull image
 ↓
falha
 ↓
retry
 ↓
backoff
```

Verifique:

```text
nome da imagem
tag
registry
network
imagePullSecrets
credenciais
```

Comece novamente por:

```bash
kubectl describe pod <pod>
```

Os `Events` normalmente mostram o erro retornado pelo registry.

---

# 24. Diagnóstico: Readiness failing

Situação:

```text
Pod Running
```

mas:

```text
Ready = false
```

O primeiro ponto é não confundir:

```text
Running
```

com:

```text
Ready
```

Um processo pode estar executando e ainda não estar apto a atender tráfego.

Investigue:

```text
endpoint configurado
porta
path
timeout
failureThreshold
dependências verificadas pela probe
startup da aplicação
```

Readiness falhando faz o Pod sair do conjunto de backends utilizados pelo Service. 

---

# 25. Diagnóstico: CPU throttling

Situação clássica:

```text
CPU usage perto do limit
       ↓
throttling
       ↓
latência aumenta
       ↓
p95 / p99 sobem
```

Mesmo que:

```text
Pod não crash
```

o serviço pode ficar lento.

Você precisa analisar:

```text
CPU usage
CPU request
CPU limit
throttled time
latência
HPA
```

CPU limit é um limite efetivamente aplicado pelo kernel; ao excedê-lo, o workload pode ser restringido em vez de encerrado. 

---

# 26. Namespace

Namespaces ajudam a organizar recursos:

```text
cluster
│
├── payments
│
├── orders
│
└── platform
```

ou, dependendo da estratégia:

```text
dev
staging
prod
```

Eles fornecem escopo e podem trabalhar junto com:

```text
RBAC
ResourceQuota
NetworkPolicy
```

Mas Namespace sozinho não deve ser interpretado como boundary de segurança completa. A documentação recomenda inclusive evitar o namespace `default` para workloads de produção quando fizer sentido organizar o cluster explicitamente. 

---

# 27. Mapa mental principal

Memorize o fluxo:

```text
Internet
   ↓
Ingress / Gateway
   ↓
Service
   ↓
Deployment
   ↓
ReplicaSet
   ↓
Pods
```

Configuração:

```text
Pod
├── ConfigMap
└── Secret
```

Disponibilidade:

```text
Readiness
Liveness
Startup Probe
PDB
Rolling Update
```

Capacidade:

```text
Requests
Limits
HPA
```

---

# Resposta objetiva para entrevista

> Em Kubernetes eu entendo primeiro a relação entre os recursos. Normalmente utilizo um Deployment para declarar a aplicação e quantidade de replicas; ele gerencia ReplicaSets, que por sua vez mantêm os Pods. Um Service fornece um endpoint estável para esses Pods, enquanto Ingress ou Gateway pode controlar o acesso HTTP externo. 
>
> Para configuração, utilizo ConfigMap para dados não sensíveis e Secret para credenciais e informações sensíveis, sempre considerando também RBAC e proteção adequada dos Secrets. 
>
> Em produção, presto bastante atenção em requests e limits. Requests influenciam scheduling e também métricas de utilização do HPA. CPU limit pode provocar throttling, enquanto ultrapassar o limite de memória pode resultar em OOM kill. 
>
> Também separo bem as probes: startup indica que a aplicação terminou de subir, readiness determina se o Pod deve receber tráfego e liveness determina se o container precisa ser reiniciado. Configurar liveness incorretamente pode inclusive provocar falhas em cascata. 
>
> Para escala utilizo HPA com uma métrica coerente com o workload, não necessariamente apenas CPU. Para disponibilidade durante manutenção posso usar PDB, lembrando que ele protege contra disrupções voluntárias, não contra qualquer falha. Rolling Update permite substituir versões gradualmente e o Deployment também oferece rollback de revisões. 
>
> Em diagnóstico, `CrashLoopBackOff` me leva a verificar logs e eventos do container; `OOMKilled`, memória e sizing da JVM; `Pending`, scheduling e requests; `ImagePullBackOff`, imagem e credenciais; readiness failing, probe e dependências; e CPU throttling, a relação entre uso, request e limit.
>
> Para mim, dominar Kubernetes como desenvolvedor Senior significa conseguir **entender o lifecycle da aplicação no cluster, configurar disponibilidade e recursos corretamente e diagnosticar por que um workload não inicia, não recebe tráfego, não escala ou apresenta degradação**.
