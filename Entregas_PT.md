<a id="inicio"></a>

# Storytelling de entregas — Preparação para entrevistas Java

Histórias profissionais e respostas de aprofundamento técnico sobre **microsserviços, Kafka, Kubernetes e refatoração de sistemas legados**.

## Índice

1. [Microsserviços — Dell Technologies](#microsservicos-dell)
   - [Contexto e execução](#micro-contexto)
   - [Pontos de atenção e valor entregue](#micro-valor)
2. [Kafka e comunicação assíncrona — Banco PAN](#kafka-pan)
   - [Experiência no projeto](#kafka-experiencia)
   - [Conceitos rápidos para entrevistas](#kafka-conceitos)
   - [Perguntas frequentes](#kafka-perguntas)
3. [Kubernetes — Deploy e operação de microsserviços](#kubernetes)
   - [Experiência e responsabilidade na Dell](#k8s-experiencia)
   - [Resposta técnica para entrevista](#k8s-resposta)
   - [Conceitos técnicos essenciais](#k8s-conceitos)
   - [Perguntas de aprofundamento](#k8s-perguntas)
4. [Maior desafio profissional — McDonald's](#mcdonalds)
   - [Storytelling para entrevista](#mcd-storytelling)
   - [Perguntas de aprofundamento técnico](#mcd-perguntas)

---

<a id="microsservicos-dell"></a>

## 1. Microsserviços — Dell Technologies

<a id="micro-contexto"></a>

### Contexto e execução

- Uma entrega que considero relevante foi minha participação no desenvolvimento de um **microsserviço de agendamento na Dell**.
- Era um ambiente de sistemas distribuídos, no qual eu precisava evoluir funcionalidades mantendo **baixo acoplamento entre os serviços** e garantindo a confiabilidade das integrações.
- Para isso, mantive as responsabilidades bem definidas, isolando as regras de negócio no domínio e separando-as das implementações de infraestrutura. Utilizamos **arquitetura hexagonal**.
- Com **Java 21 e Spring Boot**, desenvolvi e evoluí endpoints REST, organizando as responsabilidades da aplicação e aplicando princípios **SOLID**.
- Criei testes automatizados com **JUnit e Mockito**, dentro de um ambiente com pipelines de **CI/CD** e infraestrutura **AWS**.

<a id="micro-valor"></a>

### Pontos de atenção e valor entregue

**Principal preocupação:** garantir que as regras de negócio permanecessem isoladas e que falhas de integração fossem tratadas adequadamente, evitando que problemas em componentes externos comprometessem o serviço.

Utilizei estratégias de resiliência, como **circuit breaker** e mecanismos de controle de espera com *jitter*.

**Valor da experiência:** participar da evolução de uma funcionalidade de negócio em uma arquitetura de microsserviços, considerando não apenas a implementação, mas também manutenção, qualidade e comportamento da aplicação em produção.

[↑ Voltar ao início](#inicio)

---

<a id="kafka-pan"></a>

## 2. Kafka e comunicação assíncrona — Banco PAN

<a id="kafka-experiencia"></a>

### Experiência no projeto

- No **Banco PAN**, trabalhei no produto de **empréstimo com antecipação do FGTS**, dentro de uma arquitetura de microsserviços utilizando **Java 21, Spring Boot e Kafka**.
- Um dos desafios era integrar diferentes componentes do backend, garantindo que a evolução das APIs e dos contratos não comprometesse os fluxos de processamento.
- Minha atuação envolveu o desenvolvimento e a evolução de **APIs REST**, o mapeamento dos contratos de integração com o **BFF** e o trabalho com fluxos assíncronos que utilizavam Kafka.
- Como o processamento de eventos acontece de forma desacoplada, isso exigia atenção ao **tratamento de erros, à rastreabilidade e à consistência de dados**.
- Também participava da sustentação em produção, utilizando **Splunk e Log4j** para investigar problemas, além de manter testes automatizados com cobertura mínima de **80%**.
- Essa experiência me permitiu trabalhar com a evolução de um produto financeiro real, entendendo tanto o desenvolvimento das integrações quanto os desafios operacionais de uma arquitetura orientada a eventos.

<a id="kafka-conceitos"></a>

### Conceitos rápidos para entrevistas

| Conceito | Resposta técnica e objetiva |
|---|---|
| **Particionamento** | Um tópico é dividido em partições para permitir processamento paralelo e escalabilidade. O Kafka garante ordenação apenas dentro de cada partição. |
| **Consumer groups** | Consumidores do mesmo grupo dividem as partições de um tópico. Cada partição é atribuída a apenas um consumidor por grupo, permitindo distribuir a carga de processamento. |
| **Offsets** | São posições sequenciais das mensagens dentro de uma partição. O consumidor faz o commit do offset para registrar seu progresso e permitir a retomada do consumo. |
| **Retries** | Estratégia de reprocessamento após falhas transitórias. Pode utilizar *backoff* e limite de tentativas para evitar sobrecarga ou processamento infinito. |
| **DLQ (Dead Letter Queue)** | Tópico destinado a mensagens que falharam após as tentativas de processamento. Permite investigar e reprocessar essas mensagens posteriormente. |
| **Idempotência** | Garante que processar a mesma mensagem várias vezes produza o mesmo efeito de negócio que processá-la uma única vez. Pode ser implementada com identificadores únicos e controle no banco. |
| **At-least-once** | Garante entrega pelo menos uma vez sob as condições de durabilidade e confirmação previstas. Pode ocorrer processamento duplicado, exigindo idempotência. |

<a id="kafka-perguntas"></a>

### Perguntas frequentes

**1. Como garantir que uma mensagem não seja perdida?**

Utilizando replicação, configurações adequadas de durabilidade, producer com `acks=all` e commit do offset somente após o processamento bem-sucedido.

**2. O que acontece quando um consumer falha?**

O Kafka realiza um rebalanceamento do grupo e redistribui suas partições entre os consumidores disponíveis. O processamento é retomado a partir dos offsets registrados.

**3. Como evitar mensagens duplicadas?**

Implementando idempotência no consumidor, por exemplo, armazenando o ID do evento com uma restrição de unicidade no banco.

**4. Qual a diferença entre at-most-once, at-least-once e exactly-once?**

- **At-most-once:** a mensagem pode ser perdida, mas não é reentregue.
- **At-least-once:** evita perda nas condições previstas pela configuração, mas permite duplicidade.
- **Exactly-once:** permite processamento exatamente uma vez dentro do escopo das garantias transacionais do Kafka; efeitos em sistemas externos exigem mecanismos adicionais.

**5. Se um tópico possui 3 partições e 5 consumidores no mesmo grupo, o que acontece?**

Apenas 3 consumidores ficam ativos para esse tópico. Os outros 2 não recebem partições e permanecem ociosos.

**6. Como garantir a ordem das mensagens?**

Publicando mensagens relacionadas com a mesma chave (*message key*), para que sejam direcionadas à mesma partição. A ordem é preservada dentro daquela partição.

> **Ponto importante para entrevistas sênior:** Kafka não garante, sozinho, ausência de duplicidades nem consistência transacional com bancos externos. Essas garantias dependem do desenho da aplicação.

[↑ Voltar ao início](#inicio)

---

<a id="kubernetes"></a>

## 3. Kubernetes — Deploy e operação de microsserviços

<a id="k8s-experiencia"></a>

### Experiência e responsabilidade na Dell

- Na Dell, além do desenvolvimento, tive experiência com o processo de entrega e operação em ambientes **cloud-native**, utilizando **AWS e pipelines no GitLab**, mas não é uma área que domino profundamente.
- Uma parte importante desse trabalho era garantir que as alterações de backend fossem entregues com segurança, mantendo testes automatizados.
- Esse contexto exige uma visão de orquestração de containers. Em Kubernetes, considero fundamentais as **readiness e liveness probes**, o controle de recursos, a escalabilidade horizontal e as estratégias de **rolling update**.
- Uma preocupação é garantir que a aplicação não apenas inicie, mas esteja pronta para receber tráfego e possa encerrar sem prejudicar desnecessariamente as requisições em andamento.

**Minha responsabilidade:** desenvolvimento backend com **Java e Spring Boot**, cobertura de testes e integração das alterações ao processo de **CI/CD do GitLab**. Eu não era responsável pela administração do cluster Kubernetes; meu foco era entregar uma aplicação estável, testável e preparada para ambientes distribuídos.

<a id="k8s-resposta"></a>

### Resposta técnica para entrevista

> "Na Dell, trabalhei com aplicações Java 21 e Spring Boot em ambiente cloud-native, participando do processo de desenvolvimento e entrega por meio de pipelines no GitLab.
>
> Minha atuação estava mais concentrada no backend, garantindo a qualidade das entregas com testes automatizados e acompanhando o processo de CI/CD.
>
> Tenho conhecimento do funcionamento do Kubernetes, principalmente de como ele gerencia aplicações containerizadas, disponibilidade, escalabilidade e atualizações.
>
> Entendo a importância das health checks, como readiness e liveness probes, para controlar quando uma aplicação pode receber tráfego e quando precisa ser reiniciada.
>
> Também conheço os conceitos de rolling update, resource limits e escalabilidade horizontal, embora não tenha sido o principal responsável pela configuração e administração da infraestrutura."

<a id="k8s-conceitos"></a>

### Conceitos técnicos essenciais

| Conceito | Resposta objetiva |
|---|---|
| **Pod** | Menor unidade de execução do Kubernetes. Pode conter um ou mais containers que compartilham rede e armazenamento configurado. |
| **Deployment** | Gerencia o estado desejado da aplicação, incluindo número de réplicas e atualização dos Pods. |
| **Service** | Fornece um endereço estável para acessar um conjunto de Pods, mesmo quando eles são recriados. |
| **Readiness probe** | Verifica se a aplicação está pronta para receber requisições. Se falhar, o Pod deixa de receber tráfego pelos Services correspondentes. |
| **Liveness probe** | Verifica se a aplicação está funcionando. Se falhar repetidamente, o Kubernetes reinicia o container. |
| **Startup probe** | Verifica a inicialização da aplicação, evitando que outras probes interfiram durante um startup demorado. |
| **Requests e limits** | Requests definem os recursos considerados para agendamento; limits estabelecem limites de consumo de CPU e memória. |
| **HPA** | *Horizontal Pod Autoscaler*: ajusta automaticamente o número de réplicas conforme métricas configuradas. |
| **Rolling update** | Atualiza gradualmente os Pods, substituindo versões antigas por novas e buscando manter a disponibilidade. |
| **Rollback** | Permite retornar a uma revisão anterior do Deployment quando uma atualização apresenta problemas. |

<a id="k8s-perguntas"></a>

### Perguntas de aprofundamento

**1. Como funciona um deploy com GitLab CI/CD e Kubernetes?**

> "Normalmente, o pipeline executa os testes, realiza o build da aplicação, cria a imagem Docker e publica essa imagem em um registry. Depois, uma etapa de deploy atualiza a versão da aplicação no Kubernetes, que gerencia a substituição dos Pods."

**2. Qual a diferença entre readiness e liveness probes?**

> "Readiness determina se o Pod pode receber tráfego. Liveness identifica se o container precisa ser reiniciado. Em Spring Boot, podemos utilizar endpoints do Actuator para implementar essas verificações."

Exemplos:

```text
/actuator/health/readiness
/actuator/health/liveness
```

**3. Como o Kubernetes garante alta disponibilidade?**

> "Através do gerenciamento de múltiplas réplicas, substituição de Pods com falha e distribuição de tráfego entre Pods disponíveis. Podemos combinar isso com readiness probes e rolling updates para reduzir indisponibilidade."

**4. O que acontece se um Pod consumir muita memória?**

> "Se ultrapassar o memory limit, o container pode ser encerrado por OOMKilled. Por isso, é importante configurar requests e limits adequadamente e monitorar o consumo."

**5. Como funciona o rolling update?**

> "O Kubernetes substitui gradualmente os Pods da versão anterior pelos da nova versão, seguindo parâmetros como maxSurge e maxUnavailable. As readiness probes ajudam a evitar o envio de tráfego para instâncias que ainda não estão prontas."

**6. Como garantir que requisições não sejam interrompidas durante um deploy?**

> "Uma estratégia é combinar readiness probes, rolling updates e graceful shutdown. No Spring Boot, podemos configurar o encerramento gracioso para permitir a conclusão das requisições em andamento antes que a aplicação finalize."

**7. Como investigar um Pod que não inicia?**

> "Eu começaria verificando o estado e os eventos do Pod com kubectl describe, depois analisaria os logs com kubectl logs. Investigaria falhas de inicialização, variáveis de ambiente, configuração de probes e limites de recursos."

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

[↑ Voltar ao início](#inicio)

---

<a id="mcdonalds"></a>

## 4. Maior desafio profissional — McDonald's

**Foco:** Refatoração de sistemas legados, otimização de queries **Oracle/PLSQL** e comunicação em inglês.

<a id="mcd-storytelling"></a>

### Storytelling para entrevista (60–90 segundos)

> "Um dos maiores desafios da minha carreira foi quando trabalhei em um projeto internacional do McDonald's.
>
> O projeto envolvia um sistema legado desenvolvido em Java 8, com muitas regras de negócio consolidadas ao longo dos anos e uma forte dependência do banco de dados Oracle, incluindo consultas complexas e procedures em PL/SQL.
>
> Meu principal desafio era realizar melhorias e refatorações nesse sistema sem comprometer as funcionalidades existentes, já que qualquer alteração poderia impactar outros processos da aplicação.
>
> Trabalhei na refatoração de código legado, buscando melhorar a organização das responsabilidades, reduzir complexidade e facilitar a manutenção. Também atuei na otimização de consultas SQL e rotinas PL/SQL, investigando gargalos de performance e identificando oportunidades para tornar o acesso aos dados mais eficiente.
>
> Outra dificuldade era a comunicação diária em inglês com uma equipe internacional, principalmente para esclarecer regras de negócio e discutir alterações em funcionalidades que já existiam há bastante tempo.
>
> Para lidar com isso, procurei entender o comportamento original das funcionalidades antes de realizar mudanças, alinhar as decisões técnicas com a equipe e trabalhar com alterações incrementais para reduzir os riscos.
>
> O resultado foi a melhoria da qualidade e da manutenibilidade do código legado, além de otimizações de performance nas operações com o banco de dados.
>
> Essa experiência foi importante porque fortaleceu minha capacidade de investigar sistemas complexos, trabalhar com código legado crítico e colaborar tecnicamente em um ambiente internacional."

<a id="mcd-perguntas"></a>

### Perguntas de aprofundamento técnico

| Pergunta do entrevistador | Como responder |
|---|---|
| **Como você refatorava o Java 8?** | Analisava o código existente para identificar métodos extensos, responsabilidades misturadas e duplicação de lógica. Buscava separar responsabilidades, melhorar a legibilidade e preservar o comportamento original. |
| **Como identificava problemas nas queries?** | Analisava consultas com maior custo de execução, verificando joins, filtros, índices e planos de execução para identificar gargalos. |
| **Como otimizava o PL/SQL?** | Revisava o processamento das procedures, buscando operações redundantes, consultas repetidas e oportunidades de reduzir o número de acessos ao banco. |
| **Como garantia que a refatoração não quebraria funcionalidades?** | Preservando os contratos e comportamentos existentes, validando os principais cenários de negócio e executando testes antes da entrega. |
| **Qual era o maior risco?** | Alterar inadvertidamente uma regra de negócio que estava implícita no código legado ou em uma procedure utilizada por outros processos. |
| **Por que não reescrever tudo?** | Porque uma reescrita completa teria custo e risco elevados. Refatorações incrementais permitiam melhorar a qualidade e a performance sem exigir a substituição de todo o sistema. |

[↑ Voltar ao início](#inicio)
