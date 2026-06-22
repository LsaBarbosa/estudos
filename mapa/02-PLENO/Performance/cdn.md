Lucas, **CDN** é um tema ligado a **sistemas distribuídos, performance, APIs REST, cache e cloud**, que aparecem diretamente nos seus tópicos de estudo/entrevista.  Também encaixa no seu objetivo de fortalecer arquitetura, performance e sistemas distribuídos em Java.

# 🇧🇷 Versão em Português — Mapa Mental em Tabelas

## 1. Visão geral

| Nó central             | Resumo                                                                                            |
| ---------------------- | ------------------------------------------------------------------------------------------------- |
| **CDN**                | **Content Delivery Network**, ou Rede de Distribuição de Conteúdo.                                |
| Objetivo principal     | Entregar conteúdo mais rápido ao usuário, usando servidores próximos geograficamente.             |
| Ideia central          | Em vez de todo usuário acessar diretamente o servidor principal, ele acessa um servidor de borda. |
| Servidor de borda      | Também chamado de **edge server**.                                                                |
| Servidor original      | Também chamado de **origin server**.                                                              |
| Muito usado para       | Imagens, vídeos, CSS, JavaScript, arquivos estáticos, APIs públicas, páginas cacheáveis.          |
| Exemplos de provedores | Cloudflare, AWS CloudFront, Akamai, Fastly, Azure CDN.                                            |

---

## 2. Conceito principal

| Elemento          | Significado                                                        |
| ----------------- | ------------------------------------------------------------------ |
| **Usuário**       | Cliente que acessa o site, app ou API.                             |
| **CDN**           | Rede intermediária entre o usuário e o servidor original.          |
| **Edge location** | Local físico próximo do usuário onde o conteúdo pode ser cacheado. |
| **Origin server** | Servidor real da aplicação ou armazenamento original.              |
| **Cache**         | Cópia temporária do conteúdo guardada na CDN.                      |
| **TTL**           | Tempo que o conteúdo pode ficar armazenado na CDN.                 |

---

## 3. Fluxo de uma requisição com CDN

| Etapa | Ação                                        | Resultado                        |
| ----- | ------------------------------------------- | -------------------------------- |
| 1     | Usuário acessa `https://site.com/image.png` | A requisição vai para a CDN      |
| 2     | CDN verifica se tem o arquivo em cache      | Busca em um edge server          |
| 3     | Se o conteúdo está na CDN                   | Retorna diretamente ao usuário   |
| 4     | Se o conteúdo não está na CDN               | CDN busca no origin server       |
| 5     | Origin server responde                      | CDN armazena uma cópia           |
| 6     | Próximos usuários recebem da CDN            | Menos carga no servidor original |

---

## 4. Cache hit vs cache miss na CDN

| Conceito           | Significado                                     | Exemplo                                |
| ------------------ | ----------------------------------------------- | -------------------------------------- |
| **CDN cache hit**  | CDN encontrou o conteúdo no edge server         | Imagem já estava cacheada              |
| **CDN cache miss** | CDN não encontrou o conteúdo e buscou no origin | Primeiro acesso ao arquivo             |
| **Hit ratio**      | Percentual de respostas servidas pela CDN       | 95% das imagens vieram da CDN          |
| **Miss ratio**     | Percentual que precisou ir ao origin            | 5% acessaram o servidor original       |
| **Origin fetch**   | Quando a CDN precisa buscar no servidor real    | CDN consulta sua aplicação Java/Spring |

---

## 5. Mapa mental do funcionamento

| Situação                       | Decisão da CDN                                 |
| ------------------------------ | ---------------------------------------------- |
| Conteúdo existe no edge        | Retorna direto ao usuário                      |
| Conteúdo não existe no edge    | Busca no origin server                         |
| Conteúdo pode ser cacheado     | Salva no edge server                           |
| Conteúdo não pode ser cacheado | Apenas repassa a resposta                      |
| TTL expirou                    | CDN valida ou busca novamente                  |
| Arquivo foi atualizado         | Precisa expirar, invalidar ou versionar        |
| Conteúdo é privado             | Normalmente não deve ser cacheado publicamente |

---

## 6. CDN não é a mesma coisa que Redis

| Comparação     | CDN                                            | Redis                                         |
| -------------- | ---------------------------------------------- | --------------------------------------------- |
| Localização    | Na borda da internet, perto do usuário         | Dentro da infraestrutura/backend              |
| Uso comum      | Conteúdo web, arquivos, páginas, APIs públicas | Dados de aplicação, sessões, consultas, locks |
| Quem acessa    | Navegadores, apps, usuários finais             | Aplicação backend                             |
| Exemplo        | Cache de `logo.png`                            | Cache de `product:10`                         |
| Reduz carga de | Servidor web/origin                            | Banco de dados ou serviços internos           |
| Camada         | Infraestrutura de entrega                      | Infraestrutura de aplicação                   |

---

## 7. CDN vs Cache-Aside

| Ponto              | CDN                                               | Cache-Aside                            |
| ------------------ | ------------------------------------------------- | -------------------------------------- |
| Onde fica o cache? | Próximo ao usuário, na rede da CDN                | Na aplicação/backend, geralmente Redis |
| Quem gerencia?     | CDN + headers HTTP + regras do provedor           | Aplicação                              |
| Tipo de conteúdo   | HTTP, arquivos, páginas, APIs cacheáveis          | Objetos/dados de domínio               |
| Exemplo            | Cache de imagem, CSS, JS, resposta pública de API | Cache de produto buscado no banco      |
| Principal ganho    | Menor latência global                             | Menor carga no banco                   |
| Invalidação        | Purge, TTL, versionamento de URL                  | Delete/update de chave no Redis        |

---

## 8. Conteúdos bons para CDN

| Conteúdo                | Faz sentido usar CDN? | Motivo                           |
| ----------------------- | --------------------: | -------------------------------- |
| Imagens públicas        |                   Sim | Alta leitura e baixo risco       |
| CSS                     |                   Sim | Arquivo estático                 |
| JavaScript              |                   Sim | Arquivo estático                 |
| Vídeos                  |                   Sim | Grande volume de tráfego         |
| Documentos públicos     |                   Sim | Pode reduzir carga no servidor   |
| API pública de catálogo |      Sim, com cuidado | Pode ser cacheada por TTL curto  |
| Dados de usuário logado |       Normalmente não | Pode vazar informação            |
| Saldo bancário          |                   Não | Exige consistência e privacidade |

---

## 9. Arquitetura comum

| Camada                    | Responsabilidade                                 |
| ------------------------- | ------------------------------------------------ |
| Browser/App               | Faz a requisição HTTP                            |
| DNS                       | Direciona o domínio para a CDN                   |
| CDN                       | Decide se responde do cache ou consulta o origin |
| Load Balancer/API Gateway | Recebe tráfego que passou pela CDN               |
| Aplicação Java/Spring     | Processa APIs e regras de negócio                |
| Banco/Storage             | Fonte oficial dos dados                          |
| Observabilidade           | Mede latência, hit ratio, erros e tráfego        |

---

## 10. Exemplo de arquitetura

| Sem CDN                                | Com CDN                            |
| -------------------------------------- | ---------------------------------- |
| Usuário → Servidor Java                | Usuário → CDN → Servidor Java      |
| Maior latência para usuários distantes | Menor latência global              |
| Todo tráfego chega ao backend          | Parte do tráfego para na CDN       |
| Maior carga no origin                  | Menor carga no origin              |
| Escalabilidade mais cara               | Escalabilidade melhor para leitura |

---

## 11. Headers importantes para CDN

| Header          | Função                                                      |
| --------------- | ----------------------------------------------------------- |
| `Cache-Control` | Define como e por quanto tempo a resposta pode ser cacheada |
| `max-age`       | Tempo de cache no cliente/CDN                               |
| `s-maxage`      | Tempo específico para caches compartilhados, como CDN       |
| `public`        | Permite cache por intermediários                            |
| `private`       | Resposta apenas para o usuário, não para CDN pública        |
| `no-store`      | Não armazenar em cache                                      |
| `no-cache`      | Pode armazenar, mas precisa revalidar                       |
| `ETag`          | Identificador de versão da resposta                         |
| `Last-Modified` | Data da última modificação do recurso                       |

---

## 12. Exemplo de `Cache-Control`

| Header                                 | Significado                                       |
| -------------------------------------- | ------------------------------------------------- |
| `Cache-Control: public, max-age=86400` | CDN/browser podem cachear por 1 dia               |
| `Cache-Control: public, s-maxage=600`  | CDN pode cachear por 10 minutos                   |
| `Cache-Control: private, max-age=60`   | Apenas o cliente pode cachear por 1 minuto        |
| `Cache-Control: no-store`              | Não deve ser armazenado                           |
| `Cache-Control: no-cache`              | Pode armazenar, mas precisa validar antes de usar |

---

## 13. Exemplo em Java/Spring Boot

### Endpoint público cacheável por CDN

```java
@RestController
@RequestMapping("/public/products")
public class PublicProductController {

    private final ProductService productService;

    public PublicProductController(ProductService productService) {
        this.productService = productService;
    }

    @GetMapping("/{id}")
    public ResponseEntity<ProductResponse> findPublicProduct(@PathVariable Long id) {
        ProductResponse response = productService.findPublicProductById(id);

        return ResponseEntity.ok()
                .cacheControl(
                        CacheControl
                                .maxAge(10, TimeUnit.MINUTES)
                                .cachePublic()
                )
                .body(response);
    }
}
```

---

## 14. Leitura do código

| Parte                          | Explicação                                 |
| ------------------------------ | ------------------------------------------ |
| `/public/products/{id}`        | Endpoint público, candidato a cache na CDN |
| `ResponseEntity.ok()`          | Monta a resposta HTTP                      |
| `cacheControl(...)`            | Define política de cache                   |
| `maxAge(10, TimeUnit.MINUTES)` | Permite cache por 10 minutos               |
| `cachePublic()`                | Permite cache por intermediários, como CDN |
| `body(response)`               | Retorna o conteúdo para o cliente          |

---

## 15. Exemplo para conteúdo privado

```java
@GetMapping("/me/profile")
public ResponseEntity<UserProfileResponse> myProfile() {
    UserProfileResponse response = userService.getCurrentUserProfile();

    return ResponseEntity.ok()
            .cacheControl(CacheControl.noStore())
            .body(response);
}
```

| Parte                    | Explicação                                                       |
| ------------------------ | ---------------------------------------------------------------- |
| `/me/profile`            | Endpoint sensível do usuário logado                              |
| `CacheControl.noStore()` | Instrui browser/CDN a não armazenar                              |
| Motivo                   | Evitar exposição de dados privados                               |
| Regra prática            | Dados personalizados por usuário não devem ir para cache público |

---

## 16. Estratégias de invalidação

| Estratégia               | Como funciona                       | Quando usar                            |
| ------------------------ | ----------------------------------- | -------------------------------------- |
| **TTL**                  | Espera o cache expirar naturalmente | Conteúdo que tolera pequena defasagem  |
| **Purge**                | Remove manualmente da CDN           | Atualizações críticas                  |
| **Versionamento de URL** | Muda o nome do arquivo              | CSS, JS, imagens versionadas           |
| **Revalidação**          | Usa `ETag` ou `Last-Modified`       | Conteúdo que muda ocasionalmente       |
| **TTL curto**            | Cache por poucos segundos/minutos   | APIs públicas com dados semi-dinâmicos |

---

## 17. Versionamento de arquivos

| Sem versionamento | Com versionamento   |
| ----------------- | ------------------- |
| `/app.js`         | `/app.8f3a91.js`    |
| `/style.css`      | `/style.21ab09.css` |
| `/logo.png`       | `/logo.v2.png`      |

| Benefício                  | Explicação                         |
| -------------------------- | ---------------------------------- |
| Evita cache velho          | Nova URL força novo download       |
| Permite TTL longo          | Arquivos antigos continuam válidos |
| Facilita deploy frontend   | Cada build gera nomes únicos       |
| Reduz necessidade de purge | CDN trata como novo recurso        |

---

## 18. Vantagens

| Vantagem                  | Explicação                                                 |
| ------------------------- | ---------------------------------------------------------- |
| Menor latência            | Conteúdo entregue por servidores próximos ao usuário       |
| Menos carga no origin     | Muitas requisições param na CDN                            |
| Maior disponibilidade     | CDN pode absorver tráfego alto                             |
| Melhor performance global | Usuários distantes têm resposta mais rápida                |
| Redução de custo          | Menos tráfego direto no servidor original                  |
| Proteção adicional        | Algumas CDNs oferecem WAF, DDoS protection e rate limiting |

---

## 19. Desvantagens

| Problema             | Explicação                                                 |
| -------------------- | ---------------------------------------------------------- |
| Cache desatualizado  | Usuário pode receber conteúdo antigo                       |
| Invalidação complexa | Purge pode demorar ou ser custoso                          |
| Configuração errada  | Pode cachear dados privados por engano                     |
| Debug mais difícil   | Resposta pode vir da CDN, não da aplicação                 |
| Dependência externa  | CDN vira parte crítica da arquitetura                      |
| Diferença por região | Um edge pode ter versão diferente de outro temporariamente |

---

## 20. Problemas comuns em produção

| Problema                  | Causa                                 | Solução                                          |
| ------------------------- | ------------------------------------- | ------------------------------------------------ |
| Usuário recebe JS antigo  | Arquivo sem versionamento             | Usar hash no nome do arquivo                     |
| Dados privados cacheados  | `Cache-Control: public` indevido      | Usar `private` ou `no-store`                     |
| API retorna dado velho    | TTL muito longo                       | Reduzir TTL ou aplicar purge                     |
| Baixo hit ratio           | URLs muito variáveis                  | Normalizar query strings e headers               |
| Origin sobrecarregado     | Muitos cache misses                   | Aumentar TTL, pré-aquecer cache, melhorar regras |
| Diferença entre ambientes | CDN ativa em produção, ausente em dev | Simular headers e testar comportamento           |

---

## 21. CDN com APIs REST

| Tipo de endpoint         |    Cachear na CDN? | Observação                    |
| ------------------------ | -----------------: | ----------------------------- |
| `GET /public/products`   |                Sim | Catálogo público              |
| `GET /public/categories` |                Sim | Baixa mudança                 |
| `GET /news/home`         |                Sim | TTL curto pode funcionar      |
| `POST /orders`           |                Não | Operação de escrita           |
| `PUT /users/me`          |                Não | Atualização de dado privado   |
| `GET /users/me`          | Não na CDN pública | Resposta personalizada        |
| `GET /payments/{id}`     |    Normalmente não | Sensível e exige consistência |

---

## 22. CDN e métodos HTTP

| Método   | Normalmente cacheável? | Comentário                  |
| -------- | ---------------------: | --------------------------- |
| `GET`    |                    Sim | Principal candidato         |
| `HEAD`   |                    Sim | Similar ao GET sem body     |
| `POST`   |        Normalmente não | Pode ter efeitos colaterais |
| `PUT`    |                    Não | Atualização                 |
| `PATCH`  |                    Não | Atualização parcial         |
| `DELETE` |                    Não | Remoção                     |

---

## 23. CDN e segurança

| Risco                       | Exemplo                                 | Prevenção                              |
| --------------------------- | --------------------------------------- | -------------------------------------- |
| Cache de dados privados     | Perfil do usuário cacheado publicamente | `Cache-Control: private` ou `no-store` |
| Vazamento por header errado | Resposta com `Authorization` cacheada   | Não cachear respostas autenticadas     |
| Query string sensível       | Token na URL                            | Não colocar segredo em URL             |
| Conteúdo manipulado         | Arquivo JS comprometido                 | HTTPS, assinatura, CI/CD seguro        |
| Ataques volumétricos        | DDoS no origin                          | WAF, rate limit, proteção da CDN       |

---

## 24. CDN e observabilidade

| Métrica             | O que indica                            |
| ------------------- | --------------------------------------- |
| **Cache hit ratio** | Eficiência do cache                     |
| **Origin requests** | Quantas requisições chegaram ao backend |
| **Edge latency**    | Tempo de resposta da CDN                |
| **Origin latency**  | Tempo de resposta do servidor original  |
| **4xx rate**        | Erros causados pelo cliente             |
| **5xx rate**        | Erros no origin/CDN                     |
| **Bandwidth**       | Volume de dados trafegados              |
| **Purge events**    | Invalidações realizadas                 |

---

## 25. Boas práticas

| Boa prática                              | Motivo                              |
| ---------------------------------------- | ----------------------------------- |
| Cachear apenas conteúdo público          | Evita vazamento de dados            |
| Usar versionamento em arquivos estáticos | Evita cache antigo                  |
| Configurar TTL conscientemente           | Equilibra performance e atualização |
| Usar `no-store` para dados sensíveis     | Segurança                           |
| Monitorar hit ratio                      | Mede se a CDN está ajudando         |
| Separar domínio de assets                | Exemplo: `static.example.com`       |
| Testar headers HTTP                      | CDN depende fortemente deles        |
| Documentar regras de cache               | Evita erro em deploys futuros       |

---

## 26. Mapa mental resumido

| Centro  | Ramos principais                  |
| ------- | --------------------------------- |
| **CDN** | Edge servers próximos do usuário  |
|         | Cache de conteúdo HTTP            |
|         | Reduz latência                    |
|         | Reduz carga no origin             |
|         | Usa `Cache-Control`               |
|         | Trabalha com TTL                  |
|         | Pode exigir purge                 |
|         | Excelente para arquivos estáticos |
|         | Pode cachear APIs públicas        |
|         | Não deve cachear dados privados   |

---

# 🇺🇸 English Version — Mind Map in Tables

## 1. Overview

| Central node     | Summary                                                                            |
| ---------------- | ---------------------------------------------------------------------------------- |
| **CDN**          | **Content Delivery Network**.                                                      |
| Main goal        | Deliver content faster by using servers close to the user.                         |
| Core idea        | Users do not always hit the origin server directly. They hit an edge server first. |
| Edge server      | CDN server close to the user.                                                      |
| Origin server    | The real application server or storage.                                            |
| Common use cases | Images, videos, CSS, JavaScript, static files, public APIs, cacheable pages.       |

---

## 2. Main components

| Component         | Meaning                                               |
| ----------------- | ----------------------------------------------------- |
| **User**          | Browser, mobile app or client consuming the resource. |
| **CDN**           | Network layer between users and the origin server.    |
| **Edge location** | Physical location where content can be cached.        |
| **Origin server** | Original source of the content.                       |
| **Cache**         | Temporary copy stored by the CDN.                     |
| **TTL**           | Time the content can stay cached.                     |

---

## 3. Request flow

| Step | Action                               | Result                                |
| ---- | ------------------------------------ | ------------------------------------- |
| 1    | User requests a resource             | Example: image, JS file or public API |
| 2    | CDN receives the request             | It checks an edge server              |
| 3    | CDN cache hit                        | CDN returns the response directly     |
| 4    | CDN cache miss                       | CDN calls the origin server           |
| 5    | Origin returns the response          | CDN stores a copy if allowed          |
| 6    | Future users get the cached response | Lower latency and less origin traffic |

---

## 4. CDN cache hit vs cache miss

| Concept            | Meaning                               | Example                        |
| ------------------ | ------------------------------------- | ------------------------------ |
| **CDN cache hit**  | Resource exists in the edge cache     | Image served directly from CDN |
| **CDN cache miss** | Resource is not cached                | CDN fetches it from origin     |
| **Hit ratio**      | Percentage of CDN-served responses    | 95% served from CDN            |
| **Miss ratio**     | Percentage of origin-served responses | 5% reached the backend         |
| **Origin fetch**   | CDN calls the original server         | CDN calls a Spring Boot API    |

---

## 5. CDN vs Redis

| Comparison      | CDN                                      | Redis                                      |
| --------------- | ---------------------------------------- | ------------------------------------------ |
| Location        | Internet edge                            | Backend infrastructure                     |
| Common use      | Static assets, HTTP content, public APIs | Application data, sessions, queries, locks |
| Accessed by     | Browsers, apps, clients                  | Backend application                        |
| Example         | Cache `/logo.png`                        | Cache `product:10`                         |
| Reduces load on | Web server/origin                        | Database/internal services                 |
| Layer           | Delivery infrastructure                  | Application infrastructure                 |

---

## 6. CDN vs Cache-Aside

| Point          | CDN                                       | Cache-Aside                  |
| -------------- | ----------------------------------------- | ---------------------------- |
| Cache location | Edge network                              | Backend cache, usually Redis |
| Managed by     | CDN rules and HTTP headers                | Application code             |
| Content type   | HTTP responses and files                  | Domain/application objects   |
| Example        | Cache image, CSS, JS, public API response | Cache product from database  |
| Main benefit   | Global latency reduction                  | Database load reduction      |
| Invalidation   | Purge, TTL, URL versioning                | Delete/update Redis key      |

---

## 7. Good candidates for CDN

| Content              |  Good for CDN? | Reason                                  |
| -------------------- | -------------: | --------------------------------------- |
| Public images        |            Yes | High read volume                        |
| CSS                  |            Yes | Static file                             |
| JavaScript           |            Yes | Static file                             |
| Videos               |            Yes | Large traffic volume                    |
| Public documents     |            Yes | Reduces origin load                     |
| Public catalog API   | Yes, carefully | Can use short TTL                       |
| Logged-in user data  |     Usually no | Privacy risk                            |
| Bank account balance |             No | Strong consistency and privacy required |

---

## 8. Important HTTP headers

| Header          | Purpose                                    |
| --------------- | ------------------------------------------ |
| `Cache-Control` | Defines how the response can be cached     |
| `max-age`       | Cache duration for browser/CDN             |
| `s-maxage`      | Cache duration for shared caches like CDNs |
| `public`        | Allows shared cache                        |
| `private`       | Only the end user can cache it             |
| `no-store`      | Do not store the response                  |
| `no-cache`      | Store only with revalidation               |
| `ETag`          | Response version identifier                |
| `Last-Modified` | Last resource modification date            |

---

## 9. Java/Spring Boot example

```java
@RestController
@RequestMapping("/public/products")
public class PublicProductController {

    private final ProductService productService;

    public PublicProductController(ProductService productService) {
        this.productService = productService;
    }

    @GetMapping("/{id}")
    public ResponseEntity<ProductResponse> findPublicProduct(@PathVariable Long id) {
        ProductResponse response = productService.findPublicProductById(id);

        return ResponseEntity.ok()
                .cacheControl(
                        CacheControl
                                .maxAge(10, TimeUnit.MINUTES)
                                .cachePublic()
                )
                .body(response);
    }
}
```

---

## 10. Code explanation

| Code part                      | Explanation                             |
| ------------------------------ | --------------------------------------- |
| `/public/products/{id}`        | Public endpoint, possible CDN candidate |
| `ResponseEntity.ok()`          | Builds the HTTP response                |
| `cacheControl(...)`            | Defines cache policy                    |
| `maxAge(10, TimeUnit.MINUTES)` | Allows caching for 10 minutes           |
| `cachePublic()`                | Allows shared caches, including CDNs    |
| `body(response)`               | Returns the response body               |

---

## 11. Private data example

```java
@GetMapping("/me/profile")
public ResponseEntity<UserProfileResponse> myProfile() {
    UserProfileResponse response = userService.getCurrentUserProfile();

    return ResponseEntity.ok()
            .cacheControl(CacheControl.noStore())
            .body(response);
}
```

| Code part                | Explanation                                     |
| ------------------------ | ----------------------------------------------- |
| `/me/profile`            | User-specific endpoint                          |
| `CacheControl.noStore()` | Prevents storing the response                   |
| Reason                   | Avoid leaking private data                      |
| Rule of thumb            | Personalized data should not be publicly cached |

---

## 12. Invalidation strategies

| Strategy           | How it works                     | When to use                   |
| ------------------ | -------------------------------- | ----------------------------- |
| **TTL**            | Wait for cache expiration        | Content that tolerates delay  |
| **Purge**          | Manually remove content from CDN | Critical updates              |
| **URL versioning** | Change the file name/URL         | Static files                  |
| **Revalidation**   | Use `ETag` or `Last-Modified`    | Occasionally changing content |
| **Short TTL**      | Cache for seconds/minutes        | Semi-dynamic public APIs      |

---

## 13. Advantages

| Advantage                 | Explanation                                              |
| ------------------------- | -------------------------------------------------------- |
| Lower latency             | Content is served closer to users                        |
| Less origin load          | Fewer requests hit the backend                           |
| Better global performance | Useful for geographically distributed users              |
| Higher scalability        | CDN absorbs read traffic                                 |
| Cost reduction            | Less direct traffic on origin infrastructure             |
| Extra protection          | Some CDNs provide WAF, DDoS protection and rate limiting |

---

## 14. Disadvantages

| Problem              | Explanation                                               |
| -------------------- | --------------------------------------------------------- |
| Stale content        | Users may receive old data                                |
| Hard invalidation    | Purge can be delayed or costly                            |
| Wrong configuration  | Private data may be cached accidentally                   |
| Harder debugging     | Response may come from CDN, not application               |
| External dependency  | CDN becomes part of the critical architecture             |
| Regional differences | Different edge servers may have different cached versions |

---

## 15. CDN with REST APIs

| Endpoint type            | Cache on CDN? | Note                               |
| ------------------------ | ------------: | ---------------------------------- |
| `GET /public/products`   |           Yes | Public catalog                     |
| `GET /public/categories` |           Yes | Low update frequency               |
| `GET /news/home`         |           Yes | Short TTL may work                 |
| `POST /orders`           |            No | Write operation                    |
| `PUT /users/me`          |            No | Updates private data               |
| `GET /users/me`          |  Not publicly | Personalized response              |
| `GET /payments/{id}`     |    Usually no | Sensitive and consistency-critical |

---

## 16. Best practices

| Best practice                     | Reason                               |
| --------------------------------- | ------------------------------------ |
| Cache only public content         | Avoid data leaks                     |
| Use versioned static assets       | Prevent stale JS/CSS/image issues    |
| Configure TTL carefully           | Balance performance and freshness    |
| Use `no-store` for sensitive data | Security                             |
| Monitor hit ratio                 | Verify CDN effectiveness             |
| Use a separated assets domain     | Example: `static.example.com`        |
| Test HTTP headers                 | CDN behavior depends heavily on them |
| Document cache rules              | Prevent deployment mistakes          |

---

# Revisão rápida

| Pergunta                                  | Resposta curta                                                                      |
| ----------------------------------------- | ----------------------------------------------------------------------------------- |
| O que é CDN?                              | Uma rede de servidores que entrega conteúdo a partir de locais próximos ao usuário. |
| O que é edge server?                      | Servidor da CDN próximo ao usuário.                                                 |
| O que é origin server?                    | Servidor original da aplicação ou storage.                                          |
| O que é CDN cache hit?                    | Quando a CDN responde sem consultar o origin.                                       |
| O que é CDN cache miss?                   | Quando a CDN precisa buscar no origin.                                              |
| CDN substitui Redis?                      | Não. CDN atua na entrega HTTP; Redis atua no backend.                               |
| Qual header é essencial para CDN?         | `Cache-Control`.                                                                    |
| O que não deve ser cacheado publicamente? | Dados privados, sensíveis ou personalizados por usuário.                            |
| Como evitar JS/CSS antigo?                | Usando versionamento de arquivo com hash.                                           |
| Principal benefício?                      | Reduzir latência e carga no origin.                                                 |
| Principal risco?                          | Servir conteúdo desatualizado ou cachear dado privado.                              |

# Exercícios progressivos

| Nível         | Exercício                                                                              |
| ------------- | -------------------------------------------------------------------------------------- |
| Básico        | Explique a diferença entre CDN, edge server e origin server.                           |
| Básico        | Liste 5 tipos de conteúdo bons para CDN.                                               |
| Básico        | Explique cache hit e cache miss em uma CDN.                                            |
| Intermediário | Defina quais headers você usaria para cachear uma API pública por 5 minutos.           |
| Intermediário | Explique por que `GET /users/me` não deve ser cacheado publicamente.                   |
| Intermediário | Escreva um endpoint Spring Boot usando `Cache-Control: public`.                        |
| Avançado      | Desenhe uma arquitetura com Browser → CDN → API Gateway → Spring Boot → Banco.         |
| Avançado      | Explique como resolver o problema de usuários recebendo JavaScript antigo após deploy. |
| Avançado      | Compare CDN, Redis e Cache-Aside em uma arquitetura de e-commerce.                     |
| Avançado      | Defina métricas para saber se a CDN está realmente melhorando a aplicação.             |
