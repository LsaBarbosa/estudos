# Guia de Entrevista Técnica — Java 17+, Spring Boot e Arquitetura

> **Perfil:** Desenvolvedor Java Pleno/Sênior.  
> **Objetivo:** revisar fundamentos, explicar decisões técnicas e responder perguntas situacionais.  
> **Como estudar:** responda em voz alta antes de consultar a resposta; depois acrescente um exemplo de projeto e um trade-off.

## Sumário

1. [Java 17+ e Spring Boot](#1-java-17-e-spring-boot)
2. [JDBC e bancos relacionais](#2-jdbc-e-bancos-relacionais)
3. [Redis e estratégias de cache](#3-redis-e-estratégias-de-cache)
4. [Git e Gitflow](#4-git-e-gitflow)
5. [Testes, mocks e Testcontainers](#5-testes-mocks-e-testcontainers)
6. [Estruturas de dados](#6-estruturas-de-dados)
7. [Clean Architecture, Clean Code e SOLID](#7-clean-architecture-clean-code-e-solid)
8. [Metodologias ágeis](#8-metodologias-ágeis)
9. [Arquitetura orientada a eventos](#9-arquitetura-orientada-a-eventos)
10. [MVC, MVVM e DDD](#10-mvc-mvvm-e-ddd)
11. [Azure Cloud](#11-azure-cloud)
12. [Docker e Kubernetes](#12-docker-e-kubernetes)
13. [CI/CD](#13-cicd)
14. [Observabilidade e Datadog](#14-observabilidade-e-datadog)
15. [Qualidade de código e revisão](#15-qualidade-de-código-e-revisão)
16. [Cenários integrados de arquitetura](#16-cenários-integrados-de-arquitetura)
17. [Checklist final](#17-checklist-final)

---

## 1. Java 17+ e Spring Boot

### 1.1 Quais recursos relevantes surgiram no Java 17 e no Java 21?
**Resposta:** No Java 17, destacam-se records, sealed classes, switch expressions e pattern matching para `instanceof`. No Java 21, virtual threads, sequenced collections, record patterns e pattern matching para `switch`. Records reduzem boilerplate em portadores de dados, mas não garantem imutabilidade profunda. Virtual threads aumentam a capacidade de lidar com tarefas bloqueantes de I/O; não tornam processamento de CPU mais rápido.

### 1.2 Como funciona a injeção de dependência no Spring?
**Resposta:** O contêiner IoC cria e gerencia beans, resolve suas dependências e controla seu ciclo de vida. Prefiro injeção por construtor porque explicita dependências, favorece testes e permite campos `final`. `@Component`, `@Service` e `@Repository` registram componentes com papéis semânticos diferentes; `@Bean` registra objetos produzidos por métodos de configuração.

### 1.3 Qual a diferença entre `@Controller` e `@RestController`?
**Resposta:** `@Controller` é apropriado para controladores MVC, inclusive renderização de views. `@RestController` combina `@Controller` e `@ResponseBody`, serializando por padrão o retorno dos handlers HTTP, normalmente como JSON.

### 1.4 Como funciona `@Transactional`? Quais armadilhas existem?
**Resposta:** A anotação define limites transacionais, geralmente por proxy AOP. Por padrão, ocorre rollback em `RuntimeException` e `Error`, não necessariamente em exceções checked. Self-invocation normalmente não passa pelo proxy; chamadas assíncronas não herdam automaticamente o contexto transacional. É preciso entender propagação, isolamento, duração e efeitos de chamadas externas dentro da transação.

### 1.5 Quando usar virtual threads?
**Resposta:** Para serviços com muitas operações bloqueantes de I/O, como JDBC ou HTTP síncrono, desde que bibliotecas e pools não imponham gargalos. Não substituem limites de concorrência, timeout ou controle de conexões. É importante evitar pinning prolongado e medir o ganho real.

### 1.6 Como tratar erros de uma API REST no Spring Boot?
**Resposta:** Centralizo o mapeamento de exceções com `@RestControllerAdvice` e `@ExceptionHandler`, devolvendo códigos HTTP corretos e respostas consistentes, como `ProblemDetail`. Validação de entrada pode usar Bean Validation (`@Valid`), mas invariantes de negócio devem permanecer no domínio. Não exponho stack traces nem dados sensíveis ao cliente.

### 1.7 O que diferencia `HashMap` de `ConcurrentHashMap`?
**Resposta:** `HashMap` não oferece segurança para acessos concorrentes sem sincronização externa. `ConcurrentHashMap` suporta operações concorrentes e métodos atômicos como `computeIfAbsent`, com restrições como não aceitar chaves ou valores `null`. Escolho pela necessidade real de compartilhamento entre threads.

---

## 2. JDBC e bancos relacionais

### 2.1 O que é JDBC e qual a diferença para JPA?
**Resposta:** JDBC é a API de baixo nível para conexão, execução de SQL e leitura de resultados. JPA é uma especificação de persistência ORM que abstrai mapeamento e gerenciamento de entidades, geralmente implementada pelo Hibernate. JDBC oferece controle explícito; JPA aumenta produtividade, mas exige atenção ao SQL gerado e ao ciclo de vida das entidades.

### 2.4 Explique ACID e os níveis de isolamento.
**Resposta:** Atomicidade, Consistência, Isolamento e Durabilidade são propriedades de transações. Níveis comuns: Read Uncommitted, Read Committed, Repeatable Read e Serializable. As garantias exatas variam conforme o SGBD; por exemplo, MVCC e bloqueios afetam quais anomalias são possíveis.

### 2.5 O que são dirty read, non-repeatable read e phantom read?
**Resposta:** Dirty read é ler dado não confirmado; non-repeatable read é obter valores diferentes ao reler a mesma linha; phantom read é uma nova execução de consulta por predicado retornar conjunto diferente de linhas. Lost update e write skew são anomalias adicionais relevantes para concorrência.

### 2.6 Quando usar locking otimista ou pessimista?
**Resposta:** Otimista funciona bem com baixa contenção, usando versão (`@Version`) ou atualização condicional e tratando conflitos. Pessimista usa locks para serializar acesso a dados disputados, com risco de espera e deadlock. A escolha depende de contenção, custo de retry e invariantes de negócio.

### 2.7 Como investigar consultas lentas e N+1?
**Resposta:** Verifico métricas, logs SQL, plano `EXPLAIN`/`EXPLAIN ANALYZE`, índices, cardinalidade e quantidade de round-trips. N+1 ocorre quando uma consulta inicial desencadeia uma consulta adicional por entidade relacionada. Posso usar fetch join, `@EntityGraph`, projeções ou consultas específicas; `EAGER` global não é solução universal.

### 2.8 Quando criar um índice?
**Resposta:** Quando predicados, joins ou ordenações frequentes justificam o custo de armazenamento e manutenção nas escritas. Considero seletividade, ordem de colunas em índices compostos, plano real e frequência das consultas. Índice não é automaticamente útil para toda coluna.

---

## 3. Redis e estratégias de cache

### 3.1 O que é cache-aside?
**Resposta:** A aplicação consulta o cache; em caso de miss, busca a fonte de verdade e grava no cache com TTL. É simples e amplamente usado, mas exige estratégia de invalidação e proteção contra múltiplas recomputações simultâneas.

### 3.2 Como evitar dados desatualizados no Redis?
**Resposta:** Defino TTL de acordo com tolerância à defasagem e invalido ou atualizo chaves após alterações. Em fluxos distribuídos, considero eventos de invalidação e a possibilidade de atraso ou falha. Não trato cache como fonte de verdade sem projeto específico para isso.

### 3.4 Quando Redis não é uma boa escolha?
**Resposta:** Quando a informação exige consistência forte imediata, quando a taxa de acerto é baixa, ou quando custo operacional e complexidade superam o benefício. Também avalio política de eviction, memória, persistência e indisponibilidade do cache.

### 3.5 Como usar Redis com Spring Boot?
**Resposta:** Posso usar Spring Cache com `@Cacheable`, `@CachePut` e `@CacheEvict`, configurando `RedisCacheManager`, TTL, serialização e nomes de chave. Cuido de chamadas internas que não passam pelo proxy, dados sensíveis, versionamento de payload e métricas de hit/miss.

---

## 4. Git e Gitflow

### 4.1 Qual a diferença entre merge, rebase e cherry-pick?
**Resposta:** Merge integra históricos e pode criar commit de merge; rebase reaplica commits sobre outra base e reescreve histórico; cherry-pick aplica mudanças de commits específicos. Evito rebase em branches compartilhadas sem coordenação.

### 4.2 Como funciona Gitflow?
**Resposta:** É um modelo de branches com `main`, `develop`, `feature/*`, `release/*` e `hotfix/*`. Facilita ciclos de release planejados, mas adiciona complexidade e pode atrasar integração. Em entrega contínua, trunk-based development costuma ser alternativa mais simples.

### 4.3 Como resolver conflitos e reverter um problema em produção?
**Resposta:** Entendo as alterações de ambos os lados, resolvo conflitos com testes e revisão. Para desfazer um commit já publicado, `git revert` preserva histórico; `git reset` altera referências locais e exige cuidado, especialmente com force push. Um rollback de aplicação pode exigir também estratégia compatível de banco.

---

## 5. Testes, mocks e Testcontainers

### 5.1 Qual a diferença entre testes unitários, integração e ponta a ponta?
**Resposta:** Unitários validam comportamento isolado com feedback rápido; integração verifica colaboração real entre componentes e infraestrutura; ponta a ponta exercita fluxos completos. A distribuição depende de custo, risco e arquitetura, não de uma proporção rígida.

### 5.2 O que é mock e quando utilizar?
**Resposta:** Mock substitui uma dependência para controlar respostas e verificar interações, frequentemente com Mockito. Uso principalmente em fronteiras externas. Evito mockar detalhes internos em excesso, pois isso gera testes frágeis acoplados à implementação.

### 5.3 Qual a diferença entre `@Mock`, `@InjectMocks` e `@MockBean`?
**Resposta:** `@Mock` cria mock Mockito; `@InjectMocks` tenta injetar mocks na classe sob teste, sem subir o contexto Spring. `@MockBean` foi usado para substituir beans em testes Spring, mas está deprecated nas versões recentes do Spring Boot em favor de `@MockitoBean` (conforme versão). Sempre confirmo a versão do projeto.

### 5.4 Por que usar Testcontainers?
**Resposta:** Para executar testes de integração contra dependências reais em containers descartáveis, como PostgreSQL ou Redis, reduzindo divergências de comportamento em relação a bancos em memória. Exige runtime de containers e planejamento de tempo de inicialização e paralelismo no CI.

### 5.5 Como testar uma transação e uma regra de negócio?
**Resposta:** Testo invariantes e resultados com testes unitários do domínio. Para transações, constraints, queries e locks, faço testes de integração com banco real. Atenção: testes Spring anotados com `@Transactional` podem fazer rollback ao final e ocultar certos problemas de commit.

### 5.6 Como evitar testes instáveis?
**Resposta:** Elimino dependência de relógio real (injeto `Clock`), ordem de execução, estado compartilhado, rede externa e sleeps arbitrários. Para sistemas assíncronos, uso espera condicionada com timeout e assertions determinísticas.

---

## 6. Estruturas de dados

### 6.1 ArrayList ou LinkedList?
**Resposta:** `ArrayList` oferece acesso indexado O(1) e boa localidade de memória; inserções no meio podem exigir deslocamento O(n). `LinkedList` permite inserção/remoção O(1) quando já se tem o nó/posição adequada, mas localizar elementos custa O(n), além de maior overhead. Na maioria dos casos práticos, `ArrayList` é a escolha inicial.

### 6.2 Quando utilizar pilha, fila e deque?
**Resposta:** Pilha segue LIFO, útil em parsing e backtracking; fila segue FIFO, útil para processamento ordenado; deque permite operações nas duas extremidades. Em Java, prefiro `ArrayDeque` para pilhas e filas locais, em vez da classe legada `Stack`.

### 6.3 Como funciona uma tabela hash?
**Resposta:** Calcula hash da chave para localizar buckets; colisões são tratadas pela implementação. Operações típicas de `HashMap` são O(1) em média, mas não há garantia absoluta de tempo constante. `equals` e `hashCode` precisam respeitar o contrato, e chaves mutáveis podem quebrar buscas.

### 6.4 Qual a diferença entre árvore binária de busca e heap?
**Resposta:** Uma BST ordena nós para busca por chave; balanceada, pode oferecer operações O(log n). Um heap mantém prioridade entre pai e filhos, sendo apropriado para extrair mínimo/máximo, mas não para buscar qualquer elemento eficientemente. Em Java, `PriorityQueue` usa heap.

### 6.5 O que significa complexidade O(n log n)?
**Resposta:** É uma descrição assintótica do crescimento do custo com o tamanho da entrada. Algoritmos de ordenação por comparação eficientes frequentemente têm O(n log n). Na prática também importam constantes, memória, alocação e padrão de acesso.

---

## 7. Clean Architecture, Clean Code e SOLID

### 7.1 Qual é a regra central da Clean Architecture?
**Resposta:** Dependências de código devem apontar para dentro, em direção às políticas de negócio. Domínio e casos de uso não devem depender de detalhes de banco, HTTP ou framework. Interfaces/ports podem ser definidas no núcleo e implementadas na infraestrutura.

### 7.2 Explique os cinco princípios SOLID.
**Resposta:** **S**RP: uma responsabilidade/motivo de mudança; **O**CP: estender comportamento sem modificar continuamente o núcleo; **L**SP: subtipos preservam contratos; **I**SP: interfaces específicas para consumidores; **D**IP: módulos de alto nível dependem de abstrações, não de implementações concretas. São heurísticas, não regras para criar interfaces em toda classe.

### 7.3 O que é inversão de dependência na prática?
**Resposta:** Um caso de uso depende de `PedidoRepository` definido em uma camada interna; um adaptador JPA implementa a interface. Assim, a regra de negócio não conhece `JpaRepository` nem entidades de persistência. O framework pode compor as dependências na inicialização.

### 7.4 Como reconhecer uma abstração desnecessária?
**Resposta:** Quando a abstração não representa uma variação real, aumenta indireção sem melhorar testes ou mudança, ou existe apenas para seguir um padrão. Equilibro simplicidade atual com pontos de mudança prováveis, evitando tanto acoplamento rígido quanto overengineering.

### 7.5 O que é Clean Code em um code review?
**Resposta:** Nomes que expressem intenção, métodos coesos, baixa duplicação relevante, tratamento explícito de erros, testes legíveis e ausência de efeitos colaterais surpreendentes. A qualidade também envolve desempenho, segurança, observabilidade e facilidade de evolução.

---

## 9. Arquitetura orientada a eventos

### 9.1 Qual a diferença entre evento e comando?
**Resposta:** Evento registra um fato ocorrido (`PedidoCriado`) e pode interessar a vários consumidores. Comando solicita uma ação (`CriarPedido`) a um responsável. Eventos normalmente são descritos no passado e não devem pressupor um único destinatário.

### 9.2 Service Bus e Event Hubs resolvem o mesmo problema?
**Resposta:** Não. Azure Service Bus é mensageria corporativa com filas e tópicos, recursos como dead-letter, sessões e processamento confiável. Azure Event Hubs é plataforma de ingestão e streaming de eventos em grande volume, com partições, retenção e offsets/checkpoints por consumidor. Escolho conforme semântica de trabalho versus fluxo de eventos.

### 9.4 O que é padrão Outbox?
**Resposta:** Na mesma transação do banco, salvo a alteração de negócio e um registro outbox. Um publicador assíncrono envia os eventos posteriormente, reduzindo o risco de gravar no banco sem publicar. Ainda é necessário tratar duplicatas, ordenação, retry e monitoramento de backlog.

### 9.5 Como tratar falhas e mensagens venenosas?
**Resposta:** Distingo erros transitórios de permanentes, uso retry limitado com backoff/jitter, dead-letter queue, alertas e mecanismo de reprocessamento. Evito retries infinitos que bloqueiam processamento e monitoro idade das mensagens e taxa de falhas.

### 9.6 O que é consistência eventual?
**Resposta:** Serviços ou projeções podem convergir para o mesmo estado após atraso de propagação. É necessário definir janelas aceitáveis, estados intermediários, reconciliação e experiência do usuário. Não é desculpa para inconsistência sem controle.

---

## 10. MVC, MVVM e DDD

### 10.1 O que é MVC?
**Resposta:** Model-View-Controller separa estado/regras de apresentação, interface e tratamento de entradas. Em APIs Spring MVC, controllers são adaptadores HTTP e não deveriam concentrar lógica de negócio.

### 10.2 O que é MVVM?
**Resposta:** Model-View-ViewModel separa a view de um modelo de apresentação com estado e comandos, frequentemente associado a data binding em aplicações de interface. É mais comum em frontend/mobile; não substitui diretamente uma arquitetura de domínio de backend.

### 10.3 Quais conceitos centrais de DDD?
**Resposta:** Linguagem ubíqua, bounded contexts, entidades com identidade, value objects, agregados e invariantes, domain services quando apropriado e domain events. DDD busca alinhar software ao modelo do negócio, não apenas criar pastas `domain`.

### 10.4 O que é aggregate root?
**Resposta:** É a entidade que controla acesso e alterações dentro de um agregado, preservando invariantes transacionais. Referências externas devem preferencialmente ocorrer por identidade; agregados grandes aumentam contenção e custo de persistência.

### 10.5 DDD e Clean Architecture são concorrentes?
**Resposta:** Não. DDD ajuda a modelar o negócio e delimitar contextos; Clean Architecture organiza dependências e separa políticas de detalhes técnicos. Podem ser combinados, mas ambos têm custo e não são obrigatórios em CRUDs simples.

---

## 11. Azure Cloud

### 11.1 Quando escolher Azure Service Bus?
**Resposta:** Para comandos e mensagens de negócio que demandam filas/tópicos, dead-letter, agendamento, sessões ou controle de processamento. Configuro políticas de retry, lock renewal, concorrência, idempotência e observabilidade.

### 11.2 Quando escolher Azure Event Hubs?
**Resposta:** Para ingestão e processamento de streams de alto volume, telemetria e eventos que consumidores leem por partição e offset. Dimensiono throughput, partições, retenção e grupos de consumidores, além de checkpoints e tratamento de reprocessamento.

### 11.3 O que é Blob Storage?
**Resposta:** Armazenamento de objetos para arquivos não estruturados, como imagens, documentos e backups. Considero tiers de acesso, políticas de ciclo de vida, versionamento, criptografia, permissões e acesso temporário com SAS quando adequado.

### 11.4 Qual o papel do Azure Key Vault?
**Resposta:** Gerenciar segredos, chaves e certificados de forma centralizada. Prefiro Managed Identity e RBAC em vez de credenciais fixas no código. Planejo rotação, permissões mínimas, auditoria e cache de segredos quando aplicável.

### 11.5 O que é Managed Identity?
**Resposta:** Identidade gerenciada pelo Azure para um recurso autenticar em serviços compatíveis sem manter senha ou client secret na aplicação. Atribuo apenas permissões necessárias e verifico configuração por ambiente.

### 11.6 Como projetar uma aplicação resiliente em cloud?
**Resposta:** Uso timeouts, retries seletivos, circuit breaker, limites de concorrência, health checks, escalabilidade e observabilidade. Avalio disponibilidade por zona/região, RPO/RTO, custo e comportamento quando dependências estão degradadas.

---

## 12. Docker e Kubernetes

### 12.1 Qual a diferença entre imagem e container?
**Resposta:** Imagem é um artefato imutável em camadas com aplicação e dependências; container é uma instância executada da imagem com isolamento de processos e recursos. Dados persistentes exigem volumes ou serviços externos.

### 12.2 Como otimizar Dockerfile de aplicação Java?
**Resposta:** Uso multi-stage build, imagem base enxuta e suportada, usuário não-root, `.dockerignore`, cache de dependências e versionamento fixo de imagens. Não embuto segredos nem dependo de `latest` em produção.

### 12.3 O que são Pod, Deployment e Service?
**Resposta:** Pod é a menor unidade implantável, contendo um ou mais containers. Deployment gerencia réplicas e rollout de pods stateless. Service oferece endpoint estável e descoberta para acessar pods selecionados.

### 12.4 Qual a diferença entre readiness, liveness e startup probes?
**Resposta:** Readiness determina se o pod deve receber tráfego; liveness detecta condição que justifica reinício; startup protege inicializações lentas. Probes mal configuradas podem causar loops de reinício e indisponibilidade.

### 12.5 Como escalar uma aplicação no Kubernetes?
**Resposta:** Ajusto requests/limits, réplicas e Horizontal Pod Autoscaler baseado em métricas apropriadas. Verifico gargalos em banco, filas e dependências: aumentar pods não resolve um recurso compartilhado saturado.

### 12.6 O que investigar em `CrashLoopBackOff`?
**Resposta:** Eventos (`kubectl describe`), logs atuais e anteriores (`kubectl logs --previous`), exit code, OOMKilled, probes, variáveis, secrets, permissões e conexão com dependências. Corrijo causa raiz antes de aumentar recursos indiscriminadamente.

---

## 13. CI/CD

### 13.1 Qual a diferença entre integração, entrega e implantação contínuas?
**Resposta:** CI integra mudanças frequentemente com builds e testes automatizados. Continuous Delivery mantém a aplicação pronta para release, que pode exigir aprovação. Continuous Deployment promove automaticamente alterações aprovadas pelo pipeline até produção.

### 13.2 Quais etapas incluir em um pipeline Java?
**Resposta:** Checkout, resolução de dependências, compilação, testes unitários e de integração, análise estática, SCA/scan de dependências, build de imagem, scan de imagem, publicação de artefato imutável, deploy em ambiente de teste, smoke tests e promoção controlada. Ajusto gates ao risco e ao tempo de feedback.

### 13.3 Como implementar rollback seguro?
**Resposta:** Uso artefatos versionados e deploy gradual, com possibilidade de voltar à versão anterior. Para banco, prefiro migrações backward-compatible (expand-and-contract), porque reverter apenas a imagem pode não ser suficiente.

### 13.4 O que são blue-green e canary?
**Resposta:** Blue-green alterna tráfego entre dois ambientes completos; canary libera progressivamente para uma fração do tráfego e monitora métricas. Ambos exigem atenção a sessões, banco, compatibilidade e custo.

---

## 14. Observabilidade e Datadog

### 14.1 Qual a diferença entre logs, métricas e traces?
**Resposta:** Logs registram eventos discretos; métricas mostram séries temporais agregáveis; traces distribuídos mostram o caminho de uma requisição entre serviços e spans. A correlação entre sinais acelera investigação.

### 14.2 O que monitorar em uma API Spring Boot?
**Resposta:** Latência p50/p95/p99, throughput, taxa de erros, saturação, heap/GC, CPU, pool de conexões, timeouts, dependências e métricas de negócio. Uso Micrometer e instrumentação OpenTelemetry/Datadog conforme stack.

### 14.3 Como investigar aumento de latência no Datadog?
**Resposta:** Identifico quando começou, endpoints e versões afetadas; correlaciono deploys, APM traces, queries SQL, chamadas externas, GC, CPU e saturação de pools. Formulo hipótese, comparo com baseline e valido mitigação usando métricas.

### 14.4 O que são SLI, SLO e SLA?
**Resposta:** SLI é uma medida de confiabilidade (por exemplo, taxa de sucesso); SLO é a meta interna definida para o indicador; SLA é um compromisso contratual que pode prever consequências. Error budget ajuda a equilibrar velocidade de mudança e confiabilidade.

---

## 15. Qualidade de código e revisão

### 15.1 O que você avalia em um pull request?
**Resposta:** Correção funcional, aderência a regras de negócio, clareza, testes, compatibilidade, segurança, desempenho, transações, observabilidade e impacto operacional. Comentários devem ser específicos, respeitosos e proporcionais ao risco.

### 15.2 Como reduzir dívida técnica?
**Resposta:** Registro impacto e risco, priorizo itens que afetam velocidade ou confiabilidade, faço melhorias incrementais junto às entregas e reservo refatorações maiores para casos com justificativa mensurável. Evito refatorar sem cobertura de testes suficiente.

### 15.3 Como lidar com divergência técnica em code review?
**Resposta:** Separo preferência pessoal de requisito, explicito trade-offs, busco padrões do time e dados (testes, benchmark, documentação). Para decisões relevantes, registro ADR e acordo de evolução.

### 15.4 O que fazer diante de um bug crítico em produção?
**Resposta:** Priorizo contenção e comunicação, avalio rollback/feature flag, preservo evidências e monitoro recuperação. Depois faço análise de causa raiz sem culpabilização, com ações preventivas verificáveis.

---

## 16. Cenários integrados de arquitetura

### 16.1 Um pedido foi gravado no PostgreSQL, mas o evento não chegou ao Service Bus. Como resolver?
**Resposta:** Evito dual write sem coordenação. Implemento Transactional Outbox: pedido e evento outbox na mesma transação; um worker publica com retry, confirma envio e monitora pendências. O consumidor deve aceitar duplicatas. Se for exigida ordenação por agregado, defino estratégia explícita de sequenciamento.

### 16.2 Uma API Spring Boot apresenta picos de latência após introduzir Redis. O que investigar?
**Resposta:** Comparo hit rate, TTL, tamanho de payload, serialização, latência Redis, conexões e timeouts. Verifico stampede e chaves de alta cardinalidade. Adiciono fallback com cautela e não deixo uma falha de cache derrubar indiscriminadamente o serviço principal.

### 16.3 Dois clientes tentam comprar a última unidade de estoque. Como evitar overselling?
**Resposta:** Uma opção é atualização condicional atômica: `UPDATE estoque SET quantidade = quantidade - 1 WHERE produto_id = ? AND quantidade >= 1`, verificando linhas afetadas. Alternativas incluem lock pessimista ou controle otimista com versão. A decisão deve ocorrer na fonte de verdade, não somente no cache.

### 16.4 Um consumidor recebe o mesmo evento três vezes. Como garantir segurança?
**Resposta:** Defino chave idempotente estável e registro processamento com constraint única, idealmente na mesma transação da alteração de negócio. Se a operação chamar sistema externo, considero idempotency key suportada pelo provedor ou reconciliação, pois não há atomicidade automática entre sistemas.

### 16.5 Uma aplicação em Kubernetes reinicia durante picos. Como diagnosticar?
**Resposta:** Verifico OOMKilled, limites de memória, CPU throttling, heap e memória nativa, probes, logs e métricas. Em Java, configuro limites compatíveis com heap, metaspace, stacks e overhead nativo. Avalio escalonamento e gargalos antes de apenas aumentar recursos.

### 16.6 Uma alteração no banco precisa ser implantada sem downtime. Como proceder?
**Resposta:** Uso expand-and-contract: adiciono coluna/estrutura compatível, implanto código capaz de operar com ambos os formatos, migro dados com controle, troco leitura/escrita e removo estrutura antiga apenas após validação. Evito mudanças destrutivas na mesma release que introduz o novo formato.

### 16.7 Como desenhar uma API de upload de arquivos usando Azure?
**Resposta:** Autentico e autorizo usuário, valido metadados e limites, armazeno conteúdo no Blob Storage (eventualmente por SAS de curta duração com permissões mínimas), persisto referência no banco e processamentos assíncronos por fila quando necessário. Protejo contra uploads maliciosos, vazamento de URLs e inconsistência entre blob e registro.

### 16.8 Quando preferir arquitetura monolítica modular a microserviços?
**Resposta:** Quando domínio, equipe ou requisitos operacionais ainda não justificam distribuição. Um monólito modular preserva limites internos com menor custo de rede, deploy e observabilidade. Microserviços são úteis quando autonomia, escalabilidade e ciclos independentes compensam consistência distribuída e complexidade operacional.

---

## 17. Checklist final

- [ ] Consigo explicar Java 17/21, Spring IoC, transações e proxies.
- [ ] Sei comparar JDBC, JPA, SQL, índices e isolamentos.
- [ ] Consigo justificar estratégia de cache e invalidação.
- [ ] Sei explicar merge, rebase, Gitflow e rollback.
- [ ] Sei demonstrar quando usar Mockito e Testcontainers.
- [ ] Conheço complexidades e aplicações de listas, filas, pilhas, mapas e árvores.
- [ ] Sei explicar SOLID, dependências e limites de DDD.
- [ ] Distingo Service Bus de Event Hubs e entendo idempotência/outbox.
- [ ] Sei descrever Key Vault, Managed Identity e Blob Storage.
- [ ] Sei diagnosticar containers, pods, probes e escalabilidade.
- [ ] Sei montar pipeline com gates de qualidade e deploy seguro.
- [ ] Sei investigar incidentes com logs, métricas e traces.
- [ ] Tenho pelo menos três histórias reais: incidente, decisão arquitetural e melhoria de qualidade.

### Modelo de resposta oral para a entrevista

1. **Definição (15–20 segundos):** explique o conceito com precisão.
2. **Aplicação (20–30 segundos):** diga onde usaria em um sistema Java/Spring.
3. **Trade-off (15–20 segundos):** explique custo, limitação ou alternativa.
4. **Experiência (opcional):** cite resultado real, sem atribuir a si experiência que não possui.

> **Prioridade de revisão:** Spring/transações; SQL/concorrência; testes; arquitetura/SOLID; mensageria/idempotência; Redis; Docker/Kubernetes; Azure; CI/CD e observabilidade.
