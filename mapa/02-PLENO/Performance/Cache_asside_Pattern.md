



Lucas, correção do termo: **Cache-Aside Pattern**, não `Cahce asside patern`.

O tema se encaixa diretamente em **persistência, Redis, cache, banco de dados, microsserviços e performance**, pontos relevantes para entrevistas Java/Spring. fileciteturn2file0

# 🇧🇷 Versão em Português — Mapa Mental em Tabelas

## 1. Visão geral

| Nó central | Resumo |
|---|---|
| **Cache-Aside Pattern** | Padrão onde a aplicação gerencia manualmente o cache. Ela consulta primeiro o cache; se não encontrar, busca no banco e popula o cache. |
| Também chamado | **Lazy Loading Cache** |
| Principal objetivo | Reduzir leituras no banco de dados e melhorar tempo de resposta. |
| Muito usado com | **Redis**, Memcached, Spring Boot, APIs REST, microsserviços. |
| Responsável pelo cache | A própria aplicação. |
| Melhor cenário | Dados muito lidos e pouco alterados. |

---

## 2. Fluxo de leitura

| Etapa | Ação | Resultado |
|---|---|---|
| 1 | Cliente chama API | Exemplo: `GET /products/10` |
| 2 | Aplicação consulta o cache | Busca chave `product:10` no Redis |
| 3 | Se encontrou no cache | Retorna rápido, sem acessar banco |
| 4 | Se não encontrou no cache | Consulta banco de dados |
| 5 | Banco retorna o dado | Aplicação salva no cache |
| 6 | API responde ao cliente | Próximas leituras serão mais rápidas |

---

## 3. Cache hit vs cache miss

| Conceito | Significado | Exemplo |
|---|---|---|
| **Cache hit** | O dado foi encontrado no cache | Produto `10` já estava no Redis |
| **Cache miss** | O dado não estava no cache | Produto `10` precisa ser buscado no banco |
| **Hit ratio** | Percentual de acertos no cache | 90% das leituras vieram do Redis |
| **Miss ratio** | Percentual de falhas no cache | 10% precisaram ir ao banco |

---

## 4. Fluxo mental do padrão

| Situação | Decisão |
|---|---|
| Preciso ler um dado | Consulto o cache primeiro |
| Dado existe no cache | Retorno o valor |
| Dado não existe no cache | Busco no banco |
| Banco encontrou o dado | Salvo no cache e retorno |
| Banco não encontrou o dado | Retorno vazio/erro e posso aplicar negative caching |
| Dado foi alterado | Atualizo ou removo o cache |
| Dado expirou | Próxima leitura buscará novamente no banco |

---

## 5. Exemplo conceitual com produto

| Camada | Responsabilidade |
|---|---|
| Controller | Recebe `GET /products/{id}` |
| Service | Orquestra cache + banco |
| Cache Redis | Guarda dados temporários |
| Repository | Busca no banco quando o cache falha |
| Database | Fonte oficial da verdade |

---

## 6. Exemplo em Java/Spring Boot

### Implementação manual simplificada

```java
@Service
public class ProductService {

    private final ProductRepository productRepository;
    private final RedisTemplate<String, ProductResponse> redisTemplate;

    public ProductService(ProductRepository productRepository,
                          RedisTemplate<String, ProductResponse> redisTemplate) {
        this.productRepository = productRepository;
        this.redisTemplate = redisTemplate;
    }

    public ProductResponse findById(Long id) {
        String key = "product:" + id;

        ProductResponse cachedProduct = redisTemplate.opsForValue().get(key);

        if (cachedProduct != null) {
            return cachedProduct;
        }

        Product product = productRepository.findById(id)
                .orElseThrow(() -> new ProductNotFoundException(id));

        ProductResponse response = ProductResponse.from(product);

        redisTemplate.opsForValue().set(key, response, Duration.ofMinutes(10));

        return response;
    }
}
```

---

## 7. Leitura do código

| Parte do código | Função |
|---|---|
| `String key = "product:" + id` | Cria a chave do cache |
| `redisTemplate.opsForValue().get(key)` | Tenta buscar no Redis |
| `if (cachedProduct != null)` | Caso seja cache hit |
| `productRepository.findById(id)` | Busca no banco quando ocorre cache miss |
| `redisTemplate.opsForValue().set(...)` | Popula o cache |
| `Duration.ofMinutes(10)` | Define TTL para expiração |

---

## 8. Escrita: atualizar ou invalidar?

| Estratégia | Como funciona | Quando usar |
|---|---|---|
| **Invalidar cache** | Remove a chave após atualizar o banco | Mais simples e comum |
| **Atualizar cache** | Atualiza o banco e também o cache | Quando precisa de resposta mais consistente |
| **TTL apenas** | Espera o cache expirar naturalmente | Dados pouco críticos |
| **Write-through** | Escreve sempre no cache e no banco | Outro padrão, não é cache-aside puro |

---

## 9. Exemplo de atualização com invalidação

```java
@Transactional
public ProductResponse update(Long id, UpdateProductRequest request) {
    Product product = productRepository.findById(id)
            .orElseThrow(() -> new ProductNotFoundException(id));

    product.changeName(request.name());
    product.changePrice(request.price());

    Product updatedProduct = productRepository.save(product);

    String key = "product:" + id;
    redisTemplate.delete(key);

    return ProductResponse.from(updatedProduct);
}
```

---

## 10. Por que invalidar após atualizar?

| Motivo | Explicação |
|---|---|
| Evita dado velho | O cache antigo pode não refletir o banco |
| Reduz inconsistência | Próxima leitura força nova consulta ao banco |
| Simples de manter | Menos risco do que atualizar múltiplos formatos no cache |
| Bom para APIs REST | Mantém a fonte oficial no banco |

---

## 11. Vantagens

| Vantagem | Explicação |
|---|---|
| Melhora performance | Menos consultas ao banco |
| Reduz latência | Redis costuma ser mais rápido que banco relacional |
| Escalável | Ajuda APIs com alto volume de leitura |
| Simples de entender | Fluxo direto: cache → banco → cache |
| Flexível | A aplicação decide o que cachear e por quanto tempo |

---

## 12. Desvantagens

| Problema | Explicação |
|---|---|
| Código mais complexo | A aplicação gerencia cache manualmente |
| Risco de dado stale | Cache pode ficar desatualizado |
| Cache stampede | Muitos requests simultâneos podem bater no banco após expiração |
| Invalidação difícil | Saber quando remover cache pode ser complexo |
| Duplicação de lógica | Várias services podem repetir regras de cache |

---

## 13. Problemas comuns em produção

| Problema | Causa | Solução |
|---|---|---|
| **Cache stampede** | Muitos acessos ao mesmo dado expirado | Lock, TTL com jitter, refresh assíncrono |
| **Dado desatualizado** | Cache não foi invalidado | Invalidar no update/delete |
| **Chaves mal definidas** | Nome genérico ou inconsistente | Padronizar: `entity:id` |
| **Cache infinito** | Sem TTL | Sempre definir expiração |
| **Cache de nulo mal usado** | Banco não acha dado e toda chamada consulta banco | Negative caching com TTL curto |
| **Serialização ruim** | Objeto incompatível no Redis | Usar JSON estável ou DTOs específicos |

---

## 14. TTL

| Conceito | Resumo |
|---|---|
| **TTL** | Tempo de vida do dado no cache |
| TTL curto | Menos risco de dado velho, mais consultas no banco |
| TTL longo | Melhor performance, maior risco de inconsistência |
| TTL com jitter | Adiciona variação para evitar expiração em massa |
| Exemplo | 10 minutos + variação aleatória |

---

## 15. Quando usar Cache-Aside

| Usar quando | Evitar quando |
|---|---|
| Dados são lidos muitas vezes | Dados mudam o tempo todo |
| Banco está sobrecarregado por leitura | Consistência forte é obrigatória |
| Latência é crítica | Cache pode causar risco de negócio |
| Dados podem tolerar pequena defasagem | Dados financeiros/auditoria exigem precisão imediata |
| Existe Redis/Memcached disponível | Sistema é simples e não precisa cache |

---

## 16. Exemplo de bons candidatos a cache

| Dado | Faz sentido cachear? | Motivo |
|---|---:|---|
| Catálogo de produtos | Sim | Alta leitura, baixa alteração |
| Dados de perfil público | Sim | Leitura frequente |
| Configurações do sistema | Sim | Pouca alteração |
| Saldo bancário | Cuidado | Consistência forte |
| Token de autenticação | Depende | Requer segurança e expiração |
| Resultado de relatório pesado | Sim | Pode reduzir custo computacional |

---

## 17. Diferença entre Cache-Aside e outros padrões

| Padrão | Quem gerencia? | Como funciona |
|---|---|---|
| **Cache-Aside** | Aplicação | App lê cache; se falhar, lê banco e popula cache |
| **Read-Through** | Cache/provider | App consulta cache; cache busca no banco automaticamente |
| **Write-Through** | Cache/provider | Escrita vai para cache e banco juntos |
| **Write-Behind** | Cache/provider | Escreve no cache primeiro e depois no banco assincronamente |
| **Refresh-Ahead** | Cache/provider/app | Cache é renovado antes de expirar |

---

## 18. Mapa mental resumido

| Centro | Ramos principais |
|---|---|
| **Cache-Aside Pattern** | Leitura: cache primeiro |
|  | Miss: busca no banco |
|  | Depois do banco: popula cache |
|  | Escrita: atualiza banco |
|  | Após escrita: invalida ou atualiza cache |
|  | Usa TTL |
|  | Pode gerar stale data |
|  | Ajuda performance |
|  | Exige estratégia de invalidação |

---

# 🇺🇸 English Version — Mind Map in Tables

## 1. Overview

| Central node | Summary |
|---|---|
| **Cache-Aside Pattern** | A caching pattern where the application manages the cache manually. |
| Also called | **Lazy Loading Cache** |
| Main goal | Reduce database reads and improve response time. |
| Commonly used with | Redis, Memcached, Spring Boot, REST APIs, microservices. |
| Cache owner | The application. |
| Best scenario | Frequently read data with low update frequency. |

---

## 2. Read flow

| Step | Action | Result |
|---|---|---|
| 1 | Client calls the API | Example: `GET /products/10` |
| 2 | Application checks the cache | Looks for `product:10` in Redis |
| 3 | Cache hit | Return data without querying the database |
| 4 | Cache miss | Query the database |
| 5 | Database returns data | Application stores it in cache |
| 6 | API responds | Future reads become faster |

---

## 3. Cache hit vs cache miss

| Concept | Meaning | Example |
|---|---|---|
| **Cache hit** | Data exists in cache | Product `10` was found in Redis |
| **Cache miss** | Data does not exist in cache | Product `10` must be loaded from DB |
| **Hit ratio** | Percentage of cache hits | 90% of reads came from Redis |
| **Miss ratio** | Percentage of cache misses | 10% needed database access |

---

## 4. Mental flow

| Situation | Decision |
|---|---|
| Need to read data | Check cache first |
| Data exists in cache | Return cached value |
| Data does not exist in cache | Query database |
| Database returns data | Store in cache and return |
| Database does not return data | Return empty/error, optionally use negative caching |
| Data was updated | Update or invalidate cache |
| Data expired | Next read reloads from database |

---

## 5. Java/Spring responsibility map

| Layer | Responsibility |
|---|---|
| Controller | Receives the HTTP request |
| Service | Coordinates cache and database access |
| Redis Cache | Stores temporary data |
| Repository | Queries the database on cache miss |
| Database | Official source of truth |

---

## 6. Advantages

| Advantage | Explanation |
|---|---|
| Better performance | Reduces database queries |
| Lower latency | Redis is usually faster than relational databases |
| Scalable | Helps high-read APIs |
| Flexible | Application controls what to cache |
| Simple model | Cache → database → cache |

---

## 7. Disadvantages

| Problem | Explanation |
|---|---|
| More application logic | Cache is handled manually |
| Stale data risk | Cache may become outdated |
| Cache stampede | Many requests may hit the database after expiration |
| Hard invalidation | Knowing when to remove cache can be difficult |
| Duplicated logic | Multiple services may repeat cache rules |

---

## 8. Write strategy

| Strategy | How it works | When to use |
|---|---|---|
| **Invalidate cache** | Delete the cached key after DB update | Most common and simple |
| **Update cache** | Update DB and cache together | When fresher responses are needed |
| **TTL only** | Wait for cache expiration | Low-criticality data |
| **Write-through** | Write to cache and DB together | Different pattern, not pure cache-aside |

---

## 9. Production issues

| Issue | Cause | Solution |
|---|---|---|
| **Cache stampede** | Many requests reload the same expired key | Lock, TTL jitter, async refresh |
| **Stale data** | Cache was not invalidated | Delete cache on update/delete |
| **Bad key design** | Generic or inconsistent keys | Use a standard: `entity:id` |
| **No expiration** | Cache lives forever | Always define TTL |
| **Repeated null lookups** | Missing DB value causes repeated queries | Use short-lived negative caching |
| **Serialization issues** | Object format changes | Use stable DTOs or JSON schema |

---

## 10. When to use it

| Use when | Avoid when |
|---|---|
| Data is read frequently | Data changes constantly |
| Database is under read pressure | Strong consistency is mandatory |
| Low latency matters | Stale data creates business risk |
| Small delay is acceptable | Financial/audit data must be exact immediately |
| Redis/Memcached is available | The system is simple and does not need caching |

---

# Revisão rápida

| Pergunta | Resposta curta |
|---|---|
| Quem controla o cache no Cache-Aside? | A aplicação. |
| O que acontece em um cache miss? | A aplicação busca no banco e popula o cache. |
| O banco ou o cache é a fonte oficial? | O banco. |
| O que fazer após atualizar um dado? | Invalidar ou atualizar o cache. |
| O que é dado stale? | Dado antigo/desatualizado no cache. |
| Para que serve o TTL? | Expirar dados automaticamente. |
| Principal benefício? | Reduzir latência e carga no banco. |
| Principal risco? | Inconsistência temporária. |

# Exercícios progressivos

| Nível | Exercício |
|---|---|
| Básico | Explique com suas palavras o fluxo de leitura do Cache-Aside. |
| Básico | Dê 3 exemplos de dados bons para cache. |
| Intermediário | Implemente um `findById` com Redis e fallback para banco. |
| Intermediário | Adicione TTL de 5 minutos ao cache. |
| Avançado | Implemente invalidação de cache no método `update`. |
| Avançado | Explique como evitar cache stampede em uma API com alto tráfego. |