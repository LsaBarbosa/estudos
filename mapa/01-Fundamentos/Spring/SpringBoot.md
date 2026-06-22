Lucas, segue em formato de **quadro/tabela**, dividido por blocos para ficar mais fácil de revisar antes de entrevista.

# Checklist para entrevistas — Spring Boot

## 1. Fundamentos principais

| Nº | Tema                         | O que é                                                                                     | Como responder em entrevista                                                                                                                                              | Ponto Senior                                                                                                                                                                                                |
| -: | ---------------------------- | ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|  1 | **Spring Boot**              | Camada sobre o ecossistema Spring que facilita criar aplicações Java prontas para produção. | Spring Boot não substitui o Spring Framework. Ele usa o Spring por baixo e adiciona convenções, auto configuration, starters, servidor embarcado e recursos operacionais. | O valor principal não é apenas “subir API rápido”, mas padronizar configuração, facilitar deploy, reduzir boilerplate e oferecer recursos de produção como Actuator, métricas, logs e configuração externa. |
|  2 | **`@SpringBootApplication`** | Anotação principal de bootstrap da aplicação.                                               | Ela combina, de forma simplificada, `@SpringBootConfiguration`, `@EnableAutoConfiguration` e `@ComponentScan`.                                                            | Um Senior precisa entender que essa anotação ativa o escaneamento de componentes, registra configurações e habilita auto configuration.                                                                     |
|  3 | **ApplicationContext**       | Container IoC do Spring.                                                                    | É o contexto que cria, configura, injeta e gerencia os beans da aplicação.                                                                                                | Problemas de ciclo de vida, proxy, escopo e injeção dependem diretamente de entender o ApplicationContext.                                                                                                  |
|  4 | **Standalone app**           | Aplicação que roda sozinha, geralmente com servidor embarcado.                              | Uma API Spring Boot normalmente pode ser executada com `java -jar app.jar`.                                                                                               | Facilita deploy em Docker, Kubernetes, VPS, cloud e pipelines CI/CD.                                                                                                                                        |

---

## 2. Auto Configuration e Starters

| Nº | Tema                                | O que é                                                                                               | Exemplo                                                                                                             | Ponto Senior                                                                                                             |
| -: | ----------------------------------- | ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
|  5 | **Auto Configuration**              | Mecanismo que configura automaticamente beans com base no classpath, propriedades e beans existentes. | Se adicionar `spring-boot-starter-web`, o Boot configura Spring MVC, Jackson, Bean Validation e servidor embarcado. | Não é mágica. Funciona com condições como `@ConditionalOnClass`, `@ConditionalOnMissingBean` e `@ConditionalOnProperty`. |
|  6 | **Condições da auto configuration** | Regras que decidem se uma configuração será aplicada.                                                 | `@ConditionalOnMissingBean(DataSource.class)`                                                                       | Se o usuário cria seu próprio `DataSource`, o Boot normalmente deixa de criar o padrão.                                  |
|  7 | **Starters**                        | Dependências agregadoras opinativas.                                                                  | `spring-boot-starter-web`, `spring-boot-starter-data-jpa`, `spring-boot-starter-validation`                         | Starters simplificam compatibilidade de versões, mas adicionar starters demais pode ativar configurações desnecessárias. |
|  8 | **Erro comum com starters**         | Misturar dependências sem necessidade clara.                                                          | Usar `spring-boot-starter-web` e `spring-boot-starter-webflux` sem estratégia definida.                             | Pode causar confusão entre stack servlet e stack reativa.                                                                |

---

## 3. IoC, DI e Beans

| Nº | Tema                           | O que é                                                                        | Boa prática                                                            | Ponto Senior                                                                                              |
| -: | ------------------------------ | ------------------------------------------------------------------------------ | ---------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
|  9 | **IoC — Inversion of Control** | O controle de criação e gerenciamento dos objetos fica com o container Spring. | A aplicação declara beans e dependências; o Spring instancia e injeta. | Reduz acoplamento e centraliza configuração de objetos.                                                   |
| 10 | **Dependency Injection**       | Dependências são fornecidas externamente à classe.                             | Preferir injeção por construtor.                                       | Facilita testes, substituição de implementação e aplicação do princípio Dependency Inversion.             |
| 11 | **Constructor Injection**      | Dependências obrigatórias são recebidas no construtor.                         | Campos `final` + construtor explícito.                                 | Deixa dependências visíveis, favorece imutabilidade e facilita teste unitário puro.                       |
| 12 | **Field Injection**            | Dependência injetada diretamente no atributo com `@Autowired`.                 | Evitar em código de produção moderno.                                  | Esconde dependências, dificulta testes, impede `final` e pode mascarar design ruim.                       |
| 13 | **Bean**                       | Objeto gerenciado pelo Spring.                                                 | `@Service`, `@Component`, `@Repository`, `@Controller`, `@Bean`.       | Saber o ciclo de vida dos beans ajuda a entender inicialização, proxies, escopos e problemas de produção. |

---

## 4. Ciclo de vida e escopos de Beans

| Tema                      | Explicação                                                                                                           | Exemplo                          | Cuidado Senior                                                                                     |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------- | -------------------------------- | -------------------------------------------------------------------------------------------------- |
| **Ciclo de vida do bean** | O Spring identifica, registra, instancia, injeta dependências, executa callbacks e disponibiliza o bean no contexto. | `@PostConstruct` e `@PreDestroy` | Evitar inicialização pesada em `@PostConstruct`, dependências circulares e uso incorreto de proxy. |
| **Singleton**             | Uma instância por `ApplicationContext`.                                                                              | Escopo padrão de `@Service`.     | Como é compartilhado entre threads, deve ser stateless ou proteger estado mutável.                 |
| **Prototype**             | Nova instância a cada solicitação ao container.                                                                      | `@Scope("prototype")`            | Nem sempre resolve problema de estado em aplicação web. Precisa entender quem solicita o bean.     |
| **Request**               | Uma instância por request HTTP.                                                                                      | `@RequestScope`                  | Útil para dados específicos da requisição.                                                         |
| **Session**               | Uma instância por sessão HTTP.                                                                                       | `@SessionScope`                  | Cuidado com memória e escalabilidade em ambiente distribuído.                                      |

---

## 5. Controllers REST

| Tema                  | O que deve fazer                                                                                              | O que não deve fazer                                                                                | Resposta Senior                                                                                                              |
| --------------------- | ------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| **Controller REST**   | Receber requisição HTTP, validar entrada, definir status code, headers, body e delegar para service/use case. | Não deve conter regra de negócio, acesso direto ao banco, transação complexa ou integração externa. | Controller fino reduz acoplamento entre HTTP e domínio. Isso facilita testes, mensageria e reaproveitamento de casos de uso. |
| **`@RestController`** | Combina `@Controller` e `@ResponseBody`.                                                                      | Não deve virar camada de negócio.                                                                   | É apenas a porta de entrada HTTP.                                                                                            |
| **`@RequestMapping`** | Define rota base.                                                                                             | Evitar rotas inconsistentes.                                                                        | Contrato HTTP deve ser estável e previsível.                                                                                 |
| **`ResponseEntity`**  | Permite controlar status, headers e body.                                                                     | Não retornar sempre `200 OK`.                                                                       | APIs maduras usam corretamente `201`, `204`, `400`, `401`, `403`, `404`, `409`, `422`, `500`.                                |

---

## 6. Validação e tratamento de erros

| Tema                        | Uso correto                                       | Exemplo                                                 | Ponto Senior                                                                              |
| --------------------------- | ------------------------------------------------- | ------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| **Bean Validation**         | Validar formato e constraints simples de entrada. | `@NotBlank`, `@Email`, `@NotNull`, `@Size`, `@Positive` | Regras de negócio complexas devem ficar no domínio ou service, não apenas em annotations. |
| **`@Valid`**                | Aciona validação do DTO no controller.            | `@Valid @RequestBody CreateCustomerRequest request`     | Ajuda a impedir dados inválidos antes de chegar no caso de uso.                           |
| **`@RestControllerAdvice`** | Tratamento global de exceções.                    | Centralizar handlers de erro.                           | Evita `try/catch` espalhado nos controllers.                                              |
| **`@ExceptionHandler`**     | Trata exceções específicas.                       | `CustomerNotFoundException → 404`                       | Padroniza contrato de erro da API.                                                        |
| **Error Response**          | Corpo padronizado de erro.                        | `code`, `message`, `traceId`                            | Não expor stack trace ou detalhes internos para o cliente.                                |

### Exemplo de contrato de erro

| Campo     | Função                                      |
| --------- | ------------------------------------------- |
| `code`    | Código interno ou funcional do erro.        |
| `message` | Mensagem segura para o cliente.             |
| `traceId` | Identificador para rastrear logs e tracing. |
| `details` | Lista opcional com erros de validação.      |

---

## 7. Transações, AOP e Proxies

| Tema                    | Explicação                                              | Exemplo                                                           | Pegadinha Senior                                                             |
| ----------------------- | ------------------------------------------------------- | ----------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| **`@Transactional`**    | Define fronteira transacional.                          | Método de service que altera múltiplas entidades.                 | É aplicado via proxy/AOP. Chamada interna pode não ativar transação.         |
| **Unidade de trabalho** | Tudo que precisa confirmar ou reverter junto.           | Transferência bancária: debitar origem e creditar destino.        | Evitar transações longas e chamadas HTTP externas dentro da transação.       |
| **Self-invocation**     | Um método da própria classe chama outro método anotado. | `methodA()` chama `methodB()` com `@Transactional`.               | Como não passa pelo proxy, a anotação pode não ser aplicada.                 |
| **Spring AOP**          | Mecanismo para aplicar comportamento transversal.       | Transação, cache, async, segurança, logs.                         | Métodos `private`, `final` ou chamadas internas podem impedir interceptação. |
| **Proxy**               | Objeto intermediário criado pelo Spring.                | Controller chama proxy do service, não diretamente o objeto real. | Muitos bugs vêm de não entender quando o proxy está sendo usado.             |

---

## 8. Spring Data JPA

| Tema                | O que é                                                  | Exemplo                                                      | Ponto Senior                                                                              |
| ------------------- | -------------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------------------------------------------- |
| **Spring Data JPA** | Abstração para reduzir boilerplate de persistência.      | `JpaRepository<Customer, UUID>`                              | Não elimina a necessidade de entender JPA, Hibernate e SQL.                               |
| **Query method**    | Consulta derivada do nome do método.                     | `findByEmail(String email)`                                  | Útil para casos simples. Evitar nomes enormes e consultas complexas demais por convenção. |
| **`@Query`**        | Consulta JPQL ou SQL customizada.                        | `select c from Customer c where c.email = :email`            | Melhor para consultas específicas e controle de fetch.                                    |
| **`@EntityGraph`**  | Define carregamento de relacionamentos.                  | Buscar pedidos com itens.                                    | Ajuda a resolver N+1 em alguns cenários.                                                  |
| **N+1 queries**     | Uma consulta principal gera várias consultas adicionais. | Buscar lista de pedidos e depois acessar `order.getItems()`. | Em produção, sempre verificar queries geradas, plano de execução, índices e volume.       |
| **Dirty Checking**  | Hibernate detecta alterações em entidades gerenciadas.   | Alterar atributo dentro de transação pode gerar update.      | Importante entender estado managed/detached.                                              |
| **Flush**           | Sincroniza contexto de persistência com o banco.         | Pode ocorrer antes do commit.                                | Bugs podem surgir por flush automático antes de queries.                                  |

---

## 9. Entidade, DTO e Domínio

| Elemento            | Função                                    | Exemplo                        | Cuidado                                                              |
| ------------------- | ----------------------------------------- | ------------------------------ | -------------------------------------------------------------------- |
| **Entidade JPA**    | Representa estrutura persistida no banco. | `@Entity Customer`             | Carrega detalhes de persistência, relacionamentos e lazy loading.    |
| **DTO de request**  | Representa entrada da API.                | `CreateCustomerRequest`        | Deve validar formato da entrada, não carregar regra pesada.          |
| **DTO de response** | Representa saída da API.                  | `CustomerResponse`             | Evita expor estrutura interna do banco.                              |
| **Domínio**         | Representa regra de negócio.              | `Customer`, `Order`, `Payment` | Pode ou não ser separado da entidade JPA, dependendo da arquitetura. |
| **Mapper**          | Converte entre entidade, domínio e DTO.   | Manual, MapStruct, ModelMapper | Evitar lógica de negócio escondida no mapper.                        |

### Resposta Senior

| Boa prática                               | Motivo                                                        |
| ----------------------------------------- | ------------------------------------------------------------- |
| Não expor entidade JPA diretamente na API | Evita vazamento de persistência e problemas com lazy loading. |
| Usar DTOs para contrato externo           | Mantém estabilidade da API mesmo que o banco mude.            |
| Separar domínio em projetos complexos     | Reduz acoplamento com infraestrutura.                         |
| Mapear explicitamente campos importantes  | Evita exposição acidental de dados sensíveis.                 |

---

## 10. Configuração externa e Profiles

| Tema                      | Uso                                          | Exemplo                                         | Cuidado Senior                                              |
| ------------------------- | -------------------------------------------- | ----------------------------------------------- | ----------------------------------------------------------- |
| **`application.yml`**     | Configuração declarativa da aplicação.       | Porta, datasource, JPA, logs, actuator.         | Não colocar segredos fixos no arquivo versionado.           |
| **Variáveis de ambiente** | Configuração por ambiente.                   | `${DB_URL}`, `${DB_USERNAME}`                   | Essencial para Docker, cloud e CI/CD.                       |
| **Profiles**              | Separar configurações por ambiente.          | `dev`, `test`, `staging`, `prod`                | Profiles demais podem esconder bugs entre ambientes.        |
| **Secrets externos**      | Armazenar senhas e tokens fora do código.    | AWS Secrets Manager, Vault, Kubernetes Secrets. | Nunca versionar senha, token, certificado ou chave privada. |
| **Configuração imutável** | Mesmo artefato roda em ambientes diferentes. | Mesmo Docker image para dev/staging/prod.       | Muda configuração, não muda código.                         |

---

## 11. Actuator, métricas e observabilidade

| Tema                     | O que entrega                            | Exemplo                                 | Ponto Senior                                                   |
| ------------------------ | ---------------------------------------- | --------------------------------------- | -------------------------------------------------------------- |
| **Spring Boot Actuator** | Endpoints operacionais para produção.    | `/actuator/health`, `/metrics`, `/info` | Aplicação precisa ser observável, não apenas “funcionar”.      |
| **Health Check**         | Indica saúde da aplicação.               | `/actuator/health`                      | Pode ser usado por load balancer e Kubernetes.                 |
| **Readiness**            | Diz se a aplicação pode receber tráfego. | Kubernetes readiness probe.             | Diferente de liveness.                                         |
| **Liveness**             | Diz se a aplicação deve ser reiniciada.  | Kubernetes liveness probe.              | Não deve falhar por dependência externa instável sem critério. |
| **Micrometer**           | Fachada de métricas.                     | Prometheus, Datadog, New Relic, OTLP.   | Métricas devem cobrir técnica e negócio.                       |
| **TraceId**              | Identificador de rastreamento.           | Logs e tracing distribuído.             | Essencial para investigar produção.                            |
| **Logs estruturados**    | Logs em formato pesquisável.             | JSON logs.                              | Evitar logs soltos e sem contexto.                             |

### Métricas relevantes

| Tipo        | Exemplos                                                       |
| ----------- | -------------------------------------------------------------- |
| Técnicas    | CPU, memória, GC, threads, pool de conexão, latência HTTP.     |
| Banco       | Queries lentas, conexões ativas, locks, deadlocks.             |
| Mensageria  | Lag, retries, DLQ, throughput, tempo de processamento.         |
| Negócio     | Pagamentos aprovados, pedidos criados, recusas, cancelamentos. |
| Integrações | Latência externa, timeout, erro por provedor.                  |

---

## 12. Testes em Spring Boot

| Tipo de teste                 | Anotação/abordagem                            | Quando usar                                                    | Cuidado Senior                                                  |
| ----------------------------- | --------------------------------------------- | -------------------------------------------------------------- | --------------------------------------------------------------- |
| **Unitário puro**             | JUnit + Mockito, sem Spring Context.          | Testar regra de negócio isolada.                               | Deve ser rápido e independente de infraestrutura.               |
| **Controller test**           | `@WebMvcTest`                                 | Testar camada HTTP, status, validação e serialização.          | Mockar service/use case.                                        |
| **Repository test**           | `@DataJpaTest`                                | Testar queries, mappings e persistência.                       | Preferir banco real via Testcontainers em cenários importantes. |
| **Integração completa**       | `@SpringBootTest`                             | Testar wiring completo, contexto, transação e integração real. | Não usar para tudo, porque sobe contexto completo e fica lento. |
| **Testcontainers**            | Containers reais em teste.                    | PostgreSQL, Redis, Kafka, RabbitMQ.                            | Mais fiel que H2 quando o banco real é PostgreSQL/MySQL/Oracle. |
| **MockMvc**                   | Simula chamadas HTTP sem subir servidor real. | Testes de controller MVC.                                      | Bom para validar contrato REST.                                 |
| **RestAssured/WebTestClient** | Testes HTTP mais próximos do real.            | APIs com servidor subindo.                                     | Útil em testes de integração.                                   |

### Regra prática

| Camada                    | Melhor tipo de teste                     |
| ------------------------- | ---------------------------------------- |
| Domínio/service puro      | Unitário                                 |
| Controller                | `@WebMvcTest`                            |
| Repository                | `@DataJpaTest`                           |
| Integração com banco real | Testcontainers                           |
| Fluxo completo            | `@SpringBootTest`                        |
| Mensageria                | Teste de integração com broker/container |

---

## 13. Segurança com Spring Security

| Tema                       | Explicação                                           | Boa prática Senior                                                          |
| -------------------------- | ---------------------------------------------------- | --------------------------------------------------------------------------- |
| **Autenticação**           | Verifica quem é o usuário.                           | JWT, OAuth2, session, identity provider.                                    |
| **Autorização**            | Verifica o que o usuário pode acessar.               | Regras por rota, role, permission ou método.                                |
| **JWT**                    | Token assinado usado em APIs stateless.              | Validar assinatura, expiração, issuer e audience quando aplicável.          |
| **OAuth2 Resource Server** | API que valida tokens emitidos por provedor externo. | Usar `oauth2ResourceServer().jwt()`.                                        |
| **CSRF**                   | Proteção contra requisição forjada.                  | Em APIs stateless com JWT geralmente é desabilitado, mas depende do modelo. |
| **CORS**                   | Controle de origem no browser.                       | Configurar no backend; não tratar como segurança principal.                 |
| **Actuator protegido**     | Endpoints operacionais podem expor dados sensíveis.  | Liberar apenas `/health` quando necessário.                                 |
| **Logs seguros**           | Não registrar tokens, senhas ou dados sensíveis.     | Sanitizar logs e respostas de erro.                                         |
| **HTTPS**                  | Comunicação criptografada.                           | Obrigatório em produção.                                                    |

---

## 14. Cache com Spring

| Tema                  | Explicação                           | Exemplo                       | Cuidado Senior                                           |
| --------------------- | ------------------------------------ | ----------------------------- | -------------------------------------------------------- |
| **`@Cacheable`**      | Armazena retorno de método em cache. | Buscar cliente por ID.        | Definir chave corretamente.                              |
| **`@CacheEvict`**     | Remove item do cache.                | Deletar ou atualizar cliente. | Invalidação errada gera dado velho.                      |
| **`@CachePut`**       | Atualiza cache com novo retorno.     | Atualização de dados.         | Usar com critério para não gerar inconsistência.         |
| **Cache local**       | Cache dentro da aplicação.           | Caffeine.                     | Em múltiplas instâncias, cada uma tem seu próprio cache. |
| **Cache distribuído** | Cache compartilhado.                 | Redis.                        | Exige estratégia de TTL, serialização e invalidação.     |
| **TTL**               | Tempo de vida do item em cache.      | 5 min, 1 h etc.               | Depende da volatilidade do dado.                         |

### Não cachear cegamente

| Tipo de dado                   | Motivo                                          |
| ------------------------------ | ----------------------------------------------- |
| Dados altamente mutáveis       | Alto risco de inconsistência.                   |
| Dados sensíveis                | Risco de vazamento por chave mal definida.      |
| Resposta dependente do usuário | Precisa incluir usuário/permissão na chave.     |
| Entidade JPA gerenciada        | Pode causar problemas de estado e lazy loading. |
| Objetos muito grandes          | Pode pressionar memória.                        |

---

## 15. Mensageria com Spring Boot

| Tema               | Explicação                               | Exemplo                                       | Ponto Senior                                                |
| ------------------ | ---------------------------------------- | --------------------------------------------- | ----------------------------------------------------------- |
| **Kafka/RabbitMQ** | Comunicação assíncrona entre sistemas.   | `@KafkaListener`, listeners RabbitMQ.         | Desacopla serviços, mas adiciona consistência eventual.     |
| **Consumer**       | Componente que processa mensagens.       | `PaymentConsumer`                             | Deve ser idempotente.                                       |
| **Producer**       | Componente que publica eventos.          | `PaymentCreatedEvent`                         | Publicar evento depois de estado consistente.               |
| **Idempotência**   | Processar repetição sem duplicar efeito. | Verificar `eventId` processado.               | Mensagens podem ser entregues mais de uma vez.              |
| **Retry**          | Nova tentativa após falha.               | Retry com backoff.                            | Cuidado para não sobrecarregar dependência com falha.       |
| **DLQ**            | Dead Letter Queue.                       | Fila/tópico de mensagens com erro definitivo. | Essencial para análise e reprocessamento.                   |
| **Ordering**       | Ordem das mensagens.                     | Kafka por partition key.                      | Garantia de ordem não é global, normalmente é por partição. |

---

## 16. Deploy e produção

| Tema                      | O que observar                                  | Ponto Senior                                                         |
| ------------------------- | ----------------------------------------------- | -------------------------------------------------------------------- |
| **Jar standalone**        | `java -jar app.jar`                             | Simples para deploy e containerização.                               |
| **Docker**                | Imagem com JRE/JDK e aplicação.                 | A mesma imagem deve rodar em ambientes diferentes mudando variáveis. |
| **Variáveis de ambiente** | Configuração externa.                           | Não rebuildar imagem para mudar configuração.                        |
| **Health checks**         | `/actuator/health`                              | Load balancer precisa saber se a aplicação está saudável.            |
| **Graceful shutdown**     | Encerrar sem derrubar requisições em andamento. | Importante em deploy rolling.                                        |
| **JVM tuning**            | Memória, GC, heap, metaspace.                   | Containers precisam de limites bem definidos.                        |
| **Logs**                  | Saída estruturada para stdout/stderr.           | Facilita coleta por Docker/Kubernetes/Cloud.                         |
| **Observabilidade**       | Métricas, logs e tracing.                       | Sem isso, produção vira tentativa e erro.                            |
| **Timeouts**              | Chamadas externas com limite.                   | Evita threads presas e efeito cascata.                               |
| **Pool de conexões**      | HikariCP, pool HTTP, threads.                   | Má configuração causa gargalo ou queda.                              |

---

## 17. Performance em Spring Boot

| Possível gargalo    | Causa comum                                             | Como investigar                    | Correção comum                                           |
| ------------------- | ------------------------------------------------------- | ---------------------------------- | -------------------------------------------------------- |
| Startup lento       | Muitos beans, classpath grande, scans amplos.           | Logs de startup, actuator startup. | Reduzir dependências, modularizar, revisar auto configs. |
| Alto uso de memória | Objetos grandes, cache excessivo, heap mal configurado. | Métricas JVM, heap dump.           | Ajustar cache, heap, GC e estrutura de dados.            |
| Banco lento         | Queries ruins, ausência de índice, N+1.                 | SQL logs, APM, plano de execução.  | Índices, fetch join, paginação, query otimizada.         |
| Latência alta       | Chamada externa lenta.                                  | Métricas por integração.           | Timeout, retry com backoff, circuit breaker.             |
| Pool saturado       | Poucas conexões ou vazamento.                           | Métricas HikariCP.                 | Ajustar pool e corrigir transações longas.               |
| JSON pesado         | Payload grande ou serialização excessiva.               | Profiling, tamanho de resposta.    | DTOs menores, paginação, compressão.                     |
| Logs excessivos     | Log em loop ou nível inadequado.                        | Volume de logs.                    | Ajustar nível e remover logs ruidosos.                   |
| Transação longa     | Operações externas dentro da transação.                 | Tempo de transação, locks.         | Reduzir fronteira transacional.                          |

---

## 18. Problemas comuns em Spring Boot

| Problema                            | Exemplo                                                          | Por que é ruim                                 | Melhor abordagem                                             |
| ----------------------------------- | ---------------------------------------------------------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| **Dependência circular**            | `A` depende de `B` e `B` depende de `A`.                         | Indica acoplamento forte ou modelagem ruim.    | Separar responsabilidades ou criar componente intermediário. |
| **Service gigante**                 | Um service faz pedido, pagamento, nota fiscal, e-mail e estoque. | Baixa coesão, difícil testar e manter.         | Dividir por caso de uso ou contexto.                         |
| **Controller com regra de negócio** | Valida regras complexas e acessa banco diretamente.              | Acopla HTTP ao domínio.                        | Delegar para service/use case.                               |
| **Expor entidade JPA**              | Retornar `Customer` diretamente na API.                          | Vaza persistência e pode expor dados internos. | Retornar DTO.                                                |
| **`@Transactional` mal usado**      | Método privado, self-invocation, transação longa.                | Transação pode não funcionar ou gerar locks.   | Definir fronteira transacional clara.                        |
| **Ignorar N+1**                     | Loop acessando relacionamento lazy.                              | Explode número de queries.                     | `join fetch`, `@EntityGraph`, DTO projection.                |
| **Sem timeout externo**             | Chamada HTTP sem limite.                                         | Pode prender threads indefinidamente.          | Configurar connect/read timeout.                             |
| **Sem observabilidade**             | Sem logs, métricas ou traceId.                                   | Dificulta diagnóstico em produção.             | Actuator, Micrometer, tracing e logs estruturados.           |
| **Cache sem invalidação**           | Dados alteram, cache permanece velho.                            | Inconsistência funcional.                      | TTL, evict, eventos de invalidação.                          |
| **Starters desnecessários**         | Dependências adicionadas “por garantia”.                         | Ativa auto configs e aumenta complexidade.     | Manter classpath enxuto.                                     |

---

## 19. Padrões usados no Spring Boot

| Padrão                         | Onde aparece                                  | Exemplo                                     | Ponto Senior                                              |
| ------------------------------ | --------------------------------------------- | ------------------------------------------- | --------------------------------------------------------- |
| **Singleton**                  | Beans Spring por padrão.                      | `@Service CustomerService`                  | Deve ser stateless ou thread-safe.                        |
| **Proxy**                      | AOP, transação, cache, async, segurança.      | `@Transactional`, `@Cacheable`, `@Async`    | Entender proxy evita bugs de self-invocation.             |
| **Factory**                    | Container cria objetos.                       | `ApplicationContext#getBean()`              | O Spring atua como fábrica e gerenciador de dependências. |
| **Template Method / Template** | Classes utilitárias do Spring.                | `JdbcTemplate`, `RestTemplate`              | Encapsula fluxo repetitivo e expõe pontos customizáveis.  |
| **Strategy**                   | Seleção de comportamento por regra.           | `PaymentStrategy`                           | Muito útil para regras de negócio variáveis.              |
| **Adapter**                    | Integração externa.                           | `StripePaymentAdapter` implementando porta. | Isola detalhes de APIs externas.                          |
| **Facade**                     | Orquestração de fluxo complexo.               | `CheckoutFacade`                            | Simplifica uso de múltiplos serviços.                     |
| **Repository**                 | Abstração de persistência.                    | `CustomerRepository`                        | Não deve esconder completamente preocupações de banco.    |
| **Decorator/Interceptor**      | Comportamento adicional ao redor de execução. | Filtros, interceptors, aspects.             | Bom para logs, auditoria, segurança e métricas.           |

---

## 20. Quadro rápido de respostas Senior

| Pergunta de entrevista                          | Resposta objetiva de nível Senior                                                                                                                                                                 |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **O que é Spring Boot?**                        | É uma camada opinativa sobre o Spring que acelera a criação de aplicações Java production-grade por meio de auto configuration, starters, convenções, servidor embarcado e recursos operacionais. |
| **Spring Boot substitui Spring Framework?**     | Não. Ele usa o Spring Framework por baixo e reduz configuração manual.                                                                                                                            |
| **Auto configuration é mágica?**                | Não. É baseada em condições, classpath, propriedades e beans existentes.                                                                                                                          |
| **Por que usar constructor injection?**         | Porque deixa dependências explícitas, permite `final`, facilita testes e reduz acoplamento com o container.                                                                                       |
| **Por que evitar field injection?**             | Porque esconde dependências, dificulta testes puros e impede imutabilidade.                                                                                                                       |
| **Controller pode ter regra de negócio?**       | Deve evitar. Controller deve tratar HTTP e delegar para service/use case.                                                                                                                         |
| **Quando usar `@Transactional`?**               | Em métodos que representam uma unidade consistente de trabalho no banco.                                                                                                                          |
| **Qual a pegadinha do `@Transactional`?**       | Ele funciona via proxy. Chamada interna, método privado ou método final pode não ser interceptado.                                                                                                |
| **Spring Data JPA elimina necessidade de SQL?** | Não. Em produção é necessário entender queries geradas, índices, N+1, locks e plano de execução.                                                                                                  |
| **Por que não expor entidade JPA na API?**      | Porque vaza modelo de persistência, relacionamentos, lazy loading e dados internos.                                                                                                               |
| **Para que serve Actuator?**                    | Para expor recursos operacionais como health, metrics, info e integração com monitoramento.                                                                                                       |
| **Como escolher testes?**                       | Usar o menor escopo possível: unitário para regra, slice para camada, integração para wiring/banco/transação.                                                                                     |
| **Cache sempre melhora performance?**           | Não. Cache melhora latência, mas introduz risco de inconsistência e complexidade de invalidação.                                                                                                  |
| **Mensageria resolve tudo?**                    | Não. Ela desacopla serviços, mas traz duplicidade, ordering, retry, DLQ, idempotência e consistência eventual.                                                                                    |
| **O que observar em produção?**                 | Health checks, métricas, logs, tracing, graceful shutdown, timeouts, pool de conexão, memória e comportamento da JVM.                                                                             |

---

## 21. Resumo final para entrevista

| Nível            | Resposta                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Júnior/Pleno** | Spring Boot facilita criar aplicações Spring com menos configuração manual, usando starters, auto configuration e servidor embarcado.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| **Pleno forte**  | Spring Boot acelera o desenvolvimento, mas é importante entender IoC, DI, beans, controllers, validação, JPA, transações, profiles, testes e segurança.                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| **Senior**       | Spring Boot é uma plataforma opinativa para construir aplicações Spring production-grade. Ele reduz boilerplate com starters, auto configuration e convenções, mas exige entendimento profundo de IoC, ApplicationContext, ciclo de vida dos beans, escopos, proxies, AOP, transações, JPA, validação, segurança, testes, cache, mensageria, observabilidade e deploy. O ponto Senior é não tratar o framework como mágica: é preciso saber quando seguir as convenções, quando sobrescrever configurações, como diagnosticar problemas de produção e como manter baixo acoplamento entre infraestrutura, domínio e contrato externo. |

---

## 22. Checklist mental antes da entrevista

| Área         | Você precisa conseguir explicar                                     |
| ------------ | ------------------------------------------------------------------- |
| Fundamentos  | Spring Boot, Spring Framework, IoC, DI, ApplicationContext.         |
| Configuração | Auto configuration, starters, profiles, propriedades externas.      |
| Web          | REST controllers, DTOs, validação, status HTTP, exception handler.  |
| Persistência | Spring Data JPA, Hibernate, transações, lazy loading, N+1, queries. |
| AOP          | Proxies, `@Transactional`, `@Cacheable`, `@Async`, self-invocation. |
| Segurança    | Spring Security, JWT, OAuth2, CORS, CSRF, autorização.              |
| Testes       | Unitário, slice test, integração, Testcontainers.                   |
| Produção     | Actuator, health, metrics, logs, tracing, graceful shutdown.        |
| Performance  | Banco, pool, timeout, serialização, cache, transações longas.       |
| Arquitetura  | Controller fino, service/use case, DTOs, domínio, ports/adapters.   |
| Mensageria   | Kafka/RabbitMQ, idempotência, retry, DLQ, ordering.                 |
| Design       | Singleton, Proxy, Factory, Strategy, Adapter, Facade, Repository.   |
