Lucas, para revisão de **AWS ElastiCache em entrevista**, eu resumiria os pontos mais importantes assim:

| Conceito | O que é | Trade-off | Uso real em produção |
|---|---|---|---|
| **ElastiCache** | Serviço gerenciado da AWS para cache distribuído em memória, normalmente usando **Valkey/Redis** ou Memcached. | Reduz latência e carga no banco, mas adiciona mais um componente distribuído para operar e monitorar. | Cache de produtos, configurações, sessões, resultados de consultas e dados acessados com muita frequência. |
| **Cache HIT** | O dado solicitado já existe no cache. | Excelente para performance, mas depende de o dado ainda estar válido. | `GET /products/123` retorna diretamente do ElastiCache sem consultar o RDS. |
| **Cache MISS** | O dado não foi encontrado no cache. | Gera acesso ao sistema de origem e aumenta latência daquela requisição. Muitos misses reduzem o benefício do cache. | Busca o produto no PostgreSQL, retorna ao cliente e armazena no cache para próximas chamadas. |
| **Cache-Aside** | A aplicação consulta primeiro o cache; em caso de miss, consulta o banco e popula o cache. | Simples e flexível, mas a aplicação passa a ser responsável pela estratégia de cache e invalidação. | Padrão comum em APIs Spring Boot com `@Cacheable`. |
| **TTL** | Tempo de vida de uma entrada no cache. Após esse período, a entrada expira. | TTL alto aumenta hit rate, mas aumenta risco de dados desatualizados. TTL baixo melhora frescor, mas aumenta carga no banco. | Catálogo pode ter TTL de minutos; cotação ou dados mais voláteis podem exigir segundos. |
| **Cache Invalidation** | Remoção ou atualização de dados do cache quando o dado original muda. | É uma das partes mais difíceis de caching. Invalidação incorreta gera dados obsoletos. | Ao atualizar um produto no banco, remover `product:123` do cache com `@CacheEvict`. |
| **Stale Cache** | Cache contém uma versão antiga do dado em relação ao banco. | Melhora performance às custas de consistência temporária. Pode ser aceitável ou crítico dependendo do domínio. | Produto custa R$150 no banco, mas o cache ainda retorna R$100. |
| **`@Cacheable`** | Annotation do Spring que retorna o valor do cache quando disponível e evita executar o método. | Simplifica muito o código, mas abstrai detalhes que ainda precisam ser entendidos, como TTL, chave e invalidação. | Cachear `findProductById(id)`. |
| **`@CacheEvict`** | Annotation do Spring que remove uma entrada do cache. | Evita stale data, mas aumenta cache misses após atualizações. | Remover produto do cache após `updateProduct()`. |
| **`@CachePut`** | Executa o método normalmente e atualiza o cache com o resultado. | Mantém o cache atualizado, mas pode aumentar acoplamento entre escrita e caching. | Atualizar produto no banco e gravar imediatamente o novo objeto no cache. |
| **RDS como Source of Truth** | O banco continua sendo a fonte oficial dos dados; o cache é apenas uma cópia temporária. | Consulta ao banco é mais lenta, mas oferece persistência e consistência muito superiores ao cache. | PostgreSQL mantém produtos e pedidos; ElastiCache apenas acelera consultas. |
| **Cache Distribuído** | Cache compartilhado por várias instâncias da aplicação. | Resolve inconsistência entre caches locais, mas exige acesso de rede e infraestrutura adicional. | Vários pods Spring Boot no EKS acessando o mesmo ElastiCache. |
| **Cache local vs distribuído** | Local: Caffeine/heap da JVM. Distribuído: ElastiCache/Redis. | Local é mais rápido, porém não compartilhado. Distribuído é compartilhado, porém possui latência de rede. | L1 com Caffeine e L2 com ElastiCache em sistemas de alta performance. |
| **Shard** | Partição horizontal dos dados do cluster. | Permite escalar memória e throughput, mas aumenta complexidade de distribuição e operação. | Milhões de chaves Redis distribuídas entre vários shards. |
| **Replica** | Cópia de um primary usada principalmente para alta disponibilidade e leitura. | Melhora HA e escalabilidade de leitura, mas existe replication lag porque a replicação normalmente é assíncrona. | Replica em outra Availability Zone para assumir caso o primary falhe. |
| **Multi-AZ** | Distribuição de nós entre múltiplas Availability Zones. | Aumenta custo e complexidade da infraestrutura, mas melhora disponibilidade. | Primary em `us-east-1a` e replica em `us-east-1b`. |
| **Automatic Failover** | Promoção automática de uma replica quando o primary falha. | Reduz downtime, mas pode existir pequena perda de dados recentes devido ao replication lag. | Falha do primary Redis e promoção automática de uma replica. |
| **Replication Lag** | A replica ainda não recebeu as últimas alterações do primary. | Permite replicação assíncrona rápida, mas replica pode temporariamente estar desatualizada. | Primary recebe `price=150`, falha antes da replica receber essa alteração. |
| **Cache Stampede / Thundering Herd** | Muitas requisições recebem cache miss simultaneamente e todas consultam o banco. | TTL melhora caching, mas expirações simultâneas podem gerar picos enormes no backend. | Uma chave muito popular expira e milhares de requisições atingem o RDS ao mesmo tempo. |
| **TTL Jitter** | Pequena variação aleatória adicionada ao TTL. | Expirações ficam menos previsíveis, mas evita que milhares de chaves expirem juntas. | Em vez de todas expirarem em 600s, usar algo entre 570 e 630s. |
| **Distributed Lock** | Apenas uma instância reconstrói determinada entrada do cache enquanto outras aguardam. | Reduz cache stampede, mas lock distribuído adiciona complexidade e risco de contenção. | Uma request consulta o banco para reconstruir `catalog:featured`; as demais aguardam. |
| **Eviction** | Remoção automática de chaves quando a memória disponível acaba. | Mantém o cluster operacional, mas pode eliminar dados úteis e aumentar cache misses. | Redis remove chaves menos relevantes conforme a política configurada. |
| **Hit Rate** | Percentual de consultas atendidas diretamente pelo cache. | Hit rate alto geralmente significa bom aproveitamento, mas não é útil isoladamente; depende do workload. | Monitorar se 90%+ das consultas de catálogo estão sendo atendidas pelo cache. |
| **Fallback para RDS** | Se o cache estiver indisponível, a aplicação consulta diretamente o banco. | Aumenta resiliência funcional, mas pode transferir toda a carga para o banco e gerar **cascading failure**. | Redis cai e todas as instâncias Spring Boot passam a consultar PostgreSQL. |
| **Circuit Breaker** | Interrompe temporariamente chamadas para um componente que está falhando. | Evita chamadas inúteis, mas exige configuração correta de thresholds e recuperação. | Resilience4j evita que toda requisição tente reconectar ao ElastiCache indisponível. |
| **Timeout** | Limite de tempo que a aplicação espera pela resposta do cache. | Timeout muito curto gera falsos erros; muito longo aumenta latência e prende threads. | Redis deveria responder em poucos milissegundos; não faz sentido esperar vários segundos. |
| **Connection Pool** | Conjunto reutilizável de conexões entre aplicação e cache. | Evita criar conexões repetidamente, mas pool mal dimensionado causa contenção ou excesso de conexões. | Lettuce/Jedis mantendo conexões Redis reutilizáveis em uma aplicação Spring Boot. |
| **Key Naming** | Convenção usada para nomear chaves do cache. | Chaves mais descritivas ocupam um pouco mais de memória, mas facilitam operação e versionamento. | `catalog:v1:product:123` em vez de simplesmente `123`. |
| **Serialização** | Conversão de objetos Java para um formato armazenável no Redis, como JSON. | JSON é legível e interoperável, porém maior e mais lento que formatos binários. | `Product` Java serializado como JSON no ElastiCache. |
| **Versionamento de chave** | Inclusão da versão do modelo/schema na chave. | Duplica temporariamente entradas durante migrações, mas evita incompatibilidade entre versões da aplicação. | Migrar de `product:v1:123` para `product:v2:123`. |
| **Observabilidade** | Monitoramento do comportamento e saúde do cache. | Exige métricas e alarmes adicionais, mas é essencial para entender se o cache realmente está ajudando. | CloudWatch monitorando hit rate, evictions, memória, CPU, conexões e replication lag. |

## O que eu decoraria para entrevista

Se precisar reduzir para os **10 conceitos realmente centrais**, memorize esta sequência:

```text
ElastiCache
    ↓
Cache HIT / MISS
    ↓
Cache-Aside
    ↓
TTL
    ↓
Cache Invalidation
    ↓
Stale Cache
    ↓
Shard
    ↓
Replica / Multi-AZ
    ↓
Cache Stampede
    ↓
Fallback + Cascading Failure
```

E, no contexto Java/Spring:

```text
@Cacheable
    → leitura

@CacheEvict
    → invalidação

@CachePut
    → atualização
```

A correlação arquitetural mais importante é:

```text
                Spring Boot
                     |
                     v
                ElastiCache
                /          \
          Cache HIT       Cache MISS
             |                |
           return             v
                             RDS
                              |
                         source of truth
```

O ponto que mais diferencia uma resposta intermediária de uma resposta mais arquitetural é entender que **adicionar cache não elimina problemas de consistência ou disponibilidade; ele troca parte da carga e latência por novos problemas de invalidação, expiração, stampede e falhas distribuídas**.
