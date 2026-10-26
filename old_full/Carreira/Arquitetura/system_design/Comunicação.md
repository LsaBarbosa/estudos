# Resumo para entrevistas — Comunicação entre Serviços

Lucas, o fluxo mental principal é:

```text
Cliente
  ↓
Service Discovery
  ↓
Load Balancer
  ↓
Connection Pool
  ↓
HTTP/REST ou gRPC
  ↓
JSON ou Protobuf
  ↓
Propagação de contexto
  ↓
Serviço de destino
```

---

# 1. HTTP, REST, JSON, gRPC e Protobuf

Esses conceitos não são equivalentes.

| Conceito     | Responsabilidade                           |
| ------------ | ------------------------------------------ |
| **HTTP**     | Protocolo de comunicação                   |
| **REST**     | Estilo arquitetural                        |
| **JSON**     | Formato textual de serialização            |
| **gRPC**     | Framework de chamadas remotas              |
| **Protobuf** | Formato binário baseado em contrato        |
| **HTTP/2**   | Transporte normalmente utilizado pelo gRPC |

## Resposta rápida

> REST é um estilo arquitetural normalmente implementado sobre HTTP e frequentemente utiliza JSON. gRPC é um framework de RPC que normalmente utiliza HTTP/2 e Protocol Buffers.

---

# 2. REST

REST modela o sistema como **recursos** acessados por meio de operações HTTP.

```http
POST /api/orders
GET /api/orders/875
PUT /api/orders/875
DELETE /api/orders/875
```

## Métodos principais

| Método   | Uso comum                  | Idempotente              |
| -------- | -------------------------- | ------------------------ |
| `GET`    | Consultar recurso          | Sim                      |
| `POST`   | Criar ou executar operação | Normalmente não          |
| `PUT`    | Substituir recurso         | Sim                      |
| `PATCH`  | Atualizar parcialmente     | Depende da implementação |
| `DELETE` | Remover recurso            | Deve ser                 |

## Vantagens

* fácil integração;
* excelente suporte em navegadores;
* payload legível com JSON;
* debugging simples;
* compatível com gateways, proxies e ferramentas;
* muito utilizado em APIs públicas.

## Desvantagens

* JSON normalmente possui payload maior;
* contratos podem ser menos rígidos;
* erros de integração podem aparecer apenas em runtime;
* múltiplas chamadas aumentam latência;
* streaming não é o foco do REST tradicional.

## Quando utilizar

* APIs públicas;
* front-end web;
* integração com terceiros;
* clientes heterogêneos;
* quando simplicidade e compatibilidade são prioridades.

## Resposta de entrevista

> Eu utilizaria REST principalmente para APIs públicas, aplicações web e integrações com diferentes clientes. Ele oferece ampla compatibilidade, facilidade de debugging e boa integração com gateways e ferramentas de observabilidade.

---

# 3. gRPC

gRPC representa as operações como chamadas de métodos remotos.

```text
CreateOrder
FindOrder
CancelOrder
WatchOrder
```

O contrato é declarado em um arquivo `.proto`.

```protobuf
service OrderService {
  rpc CreateOrder(CreateOrderRequest)
      returns (CreateOrderResponse);

  rpc FindOrder(FindOrderRequest)
      returns (FindOrderResponse);
}
```

A partir do contrato são gerados:

* classes das mensagens;
* cliente tipado;
* stub do cliente;
* classe-base do servidor;
* serialização e desserialização.

## Tipos de chamadas

| Tipo                    | Fluxo                                |
| ----------------------- | ------------------------------------ |
| Unary                   | Uma requisição e uma resposta        |
| Server streaming        | Uma requisição e várias respostas    |
| Client streaming        | Várias requisições e uma resposta    |
| Bidirectional streaming | Fluxos simultâneos nos dois sentidos |

## Vantagens

* contrato fortemente tipado;
* código gerado;
* payload geralmente menor;
* boa performance;
* suporte nativo a streaming;
* multiplexação com HTTP/2;
* adequado para comunicação interna.

## Desvantagens

* payload não é legível diretamente;
* debugging é menos simples;
* suporte de navegador é limitado;
* requer geração de código;
* contrato precisa evoluir cuidadosamente;
* pode aumentar o acoplamento entre serviços.

## Quando utilizar

* comunicação interna entre microsserviços;
* alto volume de chamadas;
* baixa latência;
* streaming;
* contratos rígidos;
* cliente e servidor controlados pela mesma organização.

## Resposta de entrevista

> Eu utilizaria gRPC em comunicação interna de alto volume, especialmente quando contratos tipados, baixa latência ou streaming forem importantes. Para clientes externos, REST normalmente oferece maior compatibilidade e simplicidade operacional.

---

# 4. REST versus gRPC

| Característica      | REST + JSON               | gRPC + Protobuf            |
| ------------------- | ------------------------- | -------------------------- |
| Modelo              | Recursos                  | Métodos remotos            |
| Transporte          | HTTP/1.1 ou HTTP/2        | Normalmente HTTP/2         |
| Contrato            | OpenAPI recomendado       | `.proto`                   |
| Serialização        | Texto                     | Binário                    |
| Tipagem             | Menos rígida              | Forte                      |
| Payload             | Geralmente maior          | Geralmente menor           |
| Browser             | Suporte direto            | Requer gRPC-Web ou gateway |
| Streaming           | Limitado no REST clássico | Nativo                     |
| API pública         | Muito comum               | Menos comum                |
| Comunicação interna | Possível                  | Muito adequada             |
| Debugging           | Mais simples              | Requer ferramentas         |

## Ponto sênior

Não escolha gRPC apenas porque ele pode ser mais rápido.

A decisão deve considerar:

* compatibilidade dos clientes;
* experiência da equipe;
* operação;
* observabilidade;
* evolução do contrato;
* suporte de gateways;
* necessidade real de streaming;
* volume e latência.

---

# 5. HTTP/2 no gRPC

O HTTP/2 permite:

* multiplexação;
* várias chamadas na mesma conexão;
* compressão de headers;
* streaming;
* comunicação bidirecional.

Entretanto, uma conexão persistente pode concentrar tráfego em uma única instância.

```text
Cliente
   │ única conexão HTTP/2
   ▼
Instância A
```

Mesmo existindo instâncias B e C, as chamadas podem continuar sendo multiplexadas na conexão já estabelecida com A.

## Soluções

* load balancing client-side;
* múltiplos canais;
* proxy compatível com gRPC;
* service mesh;
* balanceamento por requisição.

---

# 6. Evolução de contratos

Uma arquitetura distribuída precisa manter compatibilidade entre versões antigas e novas.

## Mudanças geralmente seguras

* adicionar campo opcional;
* adicionar endpoint;
* adicionar novo método gRPC;
* adicionar campo Protobuf com número novo;
* adicionar resposta que clientes antigos possam ignorar.

## Mudanças perigosas

* remover campo utilizado;
* alterar o tipo;
* alterar significado semântico;
* tornar campo opcional obrigatório;
* alterar unidade de medida;
* alterar formato de data;
* reutilizar número Protobuf;
* remover enum ainda utilizado.

---

## Compatibilidade no Protobuf

Versão inicial:

```protobuf
message Customer {
  int64 id = 1;
  string name = 2;
}
```

Evolução segura:

```protobuf
message Customer {
  int64 id = 1;
  string name = 2;
  string email = 3;
}
```

Ao remover um campo, reserve seu nome e número:

```protobuf
message Customer {
  reserved 2;
  reserved "name";

  int64 id = 1;
  string email = 3;
}
```

Nunca reutilize o mesmo número com outro significado.

```protobuf
// Antigo
string customer_name = 2;

// Novo e incorreto
int64 account_balance = 2;
```

## Resposta de entrevista

> Em Protobuf, adicionar campos com novos números normalmente é compatível. Campos removidos devem ser marcados como `reserved`, pois reutilizar um número antigo pode fazer consumidores interpretarem dados com significado incorreto.

---

# 7. Versionamento de API

Quando uma mudança incompatível for inevitável, uma nova versão deve ser criada.

## Pela URL

```http
/api/v1/orders
/api/v2/orders
```

## Por header

```http
Accept: application/vnd.company.orders.v2+json
```

## Pelo pacote Protobuf

```protobuf
package order.v1;
```

```protobuf
package order.v2;
```

## Ponto sênior

Versionamento não deve substituir evolução compatível.

Crie uma nova versão apenas quando a mudança não puder ser introduzida sem quebrar consumidores existentes.

---

# 8. Service Discovery

Em sistemas distribuídos, os IPs das instâncias mudam.

Não é adequado configurar diretamente:

```text
payment-service = 10.0.0.42:8080
```

A instância pode:

* reiniciar;
* mudar de IP;
* escalar;
* ser substituída;
* falhar;
* mudar de host.

O Service Discovery permite consultar instâncias por nome lógico.

```text
payment-service

10.0.0.42:8080
10.0.0.57:8080
10.0.0.83:8080
```

## Client-side discovery

O próprio cliente consulta o registro e escolhe uma instância.

```text
Order Service
      ↓
Service Registry
      ↓
Payment Service
```

### Vantagem

* cliente possui controle direto da escolha.

### Desvantagem

O cliente precisa lidar com:

* descoberta;
* cache;
* health check;
* load balancing;
* retry.

## Server-side discovery

O cliente chama um endpoint conhecido.

```text
Order Service
      ↓
Load Balancer
      ↓
Payment Service
```

O balanceador descobre e seleciona a instância.

## Kubernetes

No Kubernetes, o serviço pode ser acessado por DNS:

```text
payment-service.default.svc.cluster.local
```

A aplicação conhece o serviço, não os IPs dos pods.

## Resposta de entrevista

> Service Discovery resolve a localização dinâmica das instâncias. Em Kubernetes, normalmente isso é realizado por Services e DNS, evitando que a aplicação dependa diretamente dos IPs dos pods.

---

# 9. Service Discovery versus Load Balancing

São conceitos relacionados, mas diferentes.

```text
Service Discovery:
Quais instâncias existem?

Load Balancing:
Qual instância receberá esta requisição?
```

O discovery fornece as instâncias disponíveis.

O load balancer define como distribuir o tráfego entre elas.

---

# 10. Health checks

## Liveness

Verifica se o processo está vivo.

```text
Falhou → reiniciar a aplicação
```

## Readiness

Verifica se a aplicação está pronta para receber tráfego.

```text
Falhou → remover do balanceamento
```

Uma aplicação pode estar viva, mas não pronta.

Exemplos:

* banco ainda indisponível;
* cache carregando;
* migração em execução;
* dependência crítica indisponível.

## Resposta de entrevista

> Liveness verifica se o processo deve ser reiniciado. Readiness verifica se a instância pode receber tráfego. Uma aplicação pode estar viva, mas temporariamente não estar pronta.

---

# 11. Load Balancing

Load balancing distribui requisições entre várias instâncias.

```text
Cliente
   ↓
Load Balancer
   ├── Instância A
   ├── Instância B
   └── Instância C
```

## Objetivos

* distribuir carga;
* aumentar disponibilidade;
* permitir escala horizontal;
* evitar sobrecarga;
* retirar instâncias não saudáveis.

## Estratégias

### Round Robin

```text
A → B → C → A
```

Simples, mas não considera a carga real.

### Weighted Round Robin

```text
A: peso 5
B: peso 2
C: peso 1
```

Útil quando as instâncias têm capacidades diferentes.

### Least Connections

Seleciona a instância com menos conexões ativas.

Adequado quando as requisições possuem durações diferentes.

### Least Response Time

Considera latência e número de conexões.

### Random

Escolha aleatória. Pode distribuir bem em grande escala.

### Consistent Hashing

Seleciona uma instância com base em uma chave.

```text
hash(customerId) → Instância B
```

Utilizado para:

* afinidade de cache;
* sessões;
* particionamento;
* roteamento por tenant.

---

# 12. Sticky session

Mantém o cliente associado à mesma instância.

```text
Cliente 10 → Instância B
```

## Problemas

* distribuição desigual;
* dependência de estado local;
* recuperação mais difícil;
* menor flexibilidade;
* falha da instância pode perder a sessão.

Em arquiteturas stateless, prefira armazenar estado em:

* Redis;
* banco;
* token assinado;
* armazenamento distribuído.

---

# 13. Connection Pooling

Criar uma nova conexão para cada operação pode exigir:

* resolução DNS;
* handshake TCP;
* negociação TLS;
* autenticação;
* alocação de recursos.

O pool mantém conexões abertas para reutilização.

```text
Aplicação
   ↓
Pool
   ├── Conexão 1
   ├── Conexão 2
   └── Conexão 3
```

## Benefícios

* menor latência;
* menor consumo de CPU;
* menos handshakes;
* menos sockets em `TIME_WAIT`;
* menor consumo de portas efêmeras.

---

# 14. Pool de banco com HikariCP

```yaml
spring:
  datasource:
    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
      connection-timeout: 3000
      idle-timeout: 600000
      max-lifetime: 1800000
```

| Configuração         | Finalidade                 |
| -------------------- | -------------------------- |
| `maximum-pool-size`  | Máximo de conexões         |
| `minimum-idle`       | Mínimo de conexões ociosas |
| `connection-timeout` | Espera para obter conexão  |
| `idle-timeout`       | Tempo máximo ocioso        |
| `max-lifetime`       | Vida máxima da conexão     |

## Pool maior não é necessariamente melhor

```text
20 pods × 50 conexões = 1.000 conexões
```

Se o banco suporta efetivamente 300, o resultado pode ser:

* rejeição de conexões;
* contenção;
* alto consumo de memória;
* aumento de latência;
* instabilidade.

O cálculo precisa considerar o cluster inteiro, não apenas um pod.

---

# 15. Lei de Little no dimensionamento

Relação conceitual:

```text
Concorrência ≈ throughput × latência
```

Exemplo:

```text
100 operações por segundo
latência média no banco = 0,05 segundo
```

```text
100 × 0,05 = 5 operações concorrentes
```

Isso fornece uma referência, mas o pool também deve considerar:

* picos;
* percentis de latência;
* transações longas;
* margem operacional;
* quantidade de pods;
* capacidade do banco.

---

# 16. Vazamento de conexão

Ocorre quando a aplicação obtém uma conexão e não a devolve ao pool.

Prefira `try-with-resources`:

```java
try (
    Connection connection = dataSource.getConnection();
    PreparedStatement statement =
        connection.prepareStatement(sql)
) {
    // execução
}
```

Em um pool, `close()` normalmente devolve a conexão ao pool, em vez de fechar fisicamente o socket.

---

# 17. Cliente HTTP no Spring

Não crie um cliente novo para cada requisição.

## Evite

```java
public PaymentResponse pay(PaymentRequest request) {
    RestClient client = RestClient.create();

    return client.post()
            .uri("http://payment-service/payments")
            .body(request)
            .retrieve()
            .body(PaymentResponse.class);
}
```

## Prefira reutilização

```java
@Configuration
public class HttpClientConfiguration {

    @Bean
    RestClient paymentRestClient(
            RestClient.Builder builder) {

        return builder
                .baseUrl("http://payment-service")
                .build();
    }
}
```

O cliente compartilhado permite reutilizar conexões e centralizar:

* timeout;
* autenticação;
* interceptors;
* métricas;
* tracing;
* retries.

---

# 18. Propagação de contexto

Permite correlacionar uma operação que atravessa vários serviços.

```text
Cliente
  ↓
API Gateway
  ↓
Order Service
  ↓
Payment Service
  ↓
Fraud Service
```

## Contextos importantes

* Trace ID;
* Span ID;
* Correlation ID;
* autenticação;
* tenant;
* locale;
* deadline;
* event ID;
* causation ID.

---

# 19. Trace ID versus Correlation ID

## Correlation ID

Identifica uma operação nos logs.

```http
X-Correlation-ID: 550e8400-e29b-41d4-a716-446655440000
```

```text
[correlationId=550e8400] Pedido criado
[correlationId=550e8400] Pagamento iniciado
```

## Distributed tracing

Representa:

* hierarquia de chamadas;
* duração;
* dependências;
* spans;
* erros;
* caminho completo da requisição.

## Diferença

> Correlation ID ajuda a localizar logs relacionados. Distributed tracing mostra como a requisição percorreu os serviços, quanto tempo cada etapa levou e onde ocorreram erros.

---

# 20. Trace Context

O padrão W3C utiliza headers como:

```http
traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01
tracestate: vendor=value
```

Estrutura simplificada:

```text
versão
trace-id
parent-span-id
flags
```

Ferramentas como OpenTelemetry podem propagar esse contexto automaticamente por HTTP, gRPC e mensageria, desde que estejam corretamente instrumentadas.

---

# 21. Autenticação e tenant

Um token pode transportar:

* usuário;
* permissões;
* escopos;
* tenant;
* identidade do cliente.

```http
Authorization: Bearer token
```

Em ambientes multi-tenant:

```http
X-Tenant-ID: company-875
```

O serviço não deve confiar cegamente no header.

É necessário validar se o usuário autenticado realmente possui acesso ao tenant informado.

---

# 22. Deadline e timeout

Uma deadline representa o limite total da operação.

```text
Toda a chamada deve terminar até 14:30:05.500
```

Se o serviço A consumiu 700 ms de um orçamento de 1 segundo, o serviço B deve receber apenas o tempo restante.

## Problema sem propagação

```text
Cliente: timeout de 1 segundo
Serviço A: espera 1 segundo
Serviço B: espera 1 segundo
Serviço C: espera 1 segundo
```

A operação pode durar muito mais que o limite original.

## Resposta de entrevista

> Eu propago o deadline ou o orçamento restante para evitar que cada serviço reinicie o timeout. Isso mantém a latência total limitada pelo prazo original da operação.

---

# 23. Contexto assíncrono

Um `ThreadLocal` não é automaticamente transferido entre threads.

```java
CompletableFuture.runAsync(() -> {
    log.info("Processando pedido");
});
```

O código pode perder:

* Trace ID;
* Correlation ID;
* usuário;
* tenant;
* MDC.

## Soluções

* executor instrumentado;
* OpenTelemetry;
* context propagation;
* task decorators;
* interceptors;
* propagação manual controlada.

---

# 24. MDC

O MDC adiciona dados aos logs da thread.

```java
MDC.put("correlationId", correlationId);
```

O contexto precisa ser removido.

```java
try {
    MDC.put("correlationId", correlationId);
    process();
} finally {
    MDC.remove("correlationId");
}
```

Sem a limpeza, uma thread reutilizada pode registrar dados de outra requisição.

---

# 25. Contexto em mensageria

Em Kafka ou RabbitMQ, o contexto precisa estar nos headers.

```text
traceparent
correlation-id
tenant-id
event-id
causation-id
```

## Event ID

Identifica exclusivamente o evento.

```text
eventId = evt-100
```

Também pode ser utilizado para idempotência.

## Correlation ID

Agrupa mensagens relacionadas à mesma operação.

```text
correlationId = order-875
```

## Causation ID

Indica qual mensagem originou a mensagem atual.

```text
payment-approved
foi causado por
payment-requested
```

---

# 26. Erros conceituais comuns

## “REST é protocolo”

Incorreto.

> HTTP é protocolo. REST é estilo arquitetural.

## “gRPC não usa HTTP”

Incorreto.

> gRPC normalmente utiliza HTTP/2.

## “Protobuf criptografa os dados”

Incorreto.

> Protobuf serializa dados. TLS é responsável por proteger o transporte.

## “JSON não pode ter contrato”

Incorreto.

JSON pode utilizar:

* OpenAPI;
* JSON Schema;
* AsyncAPI.

## “Service Discovery e load balancing são iguais”

Incorreto.

> Discovery localiza instâncias; balanceamento escolhe uma delas.

## “Correlation ID substitui tracing”

Incorreto.

> Correlation ID correlaciona logs; tracing representa a árvore completa de execução.

## “Quanto maior o pool, melhor”

Incorreto.

> Um pool excessivo pode esgotar a dependência e aumentar a contenção.

---

# 27. Resposta pronta de entrevista sênior

> Em sistemas distribuídos, eu separo protocolo, estilo de API e serialização. REST é um estilo arquitetural normalmente utilizado sobre HTTP e frequentemente com JSON. gRPC é um framework de chamadas remotas que normalmente utiliza HTTP/2 e Protocol Buffers.
>
> Para APIs públicas e clientes heterogêneos, REST costuma oferecer maior compatibilidade e simplicidade. Para comunicação interna, alto volume, contratos tipados e streaming, gRPC pode ser mais adequado.
>
> Service Discovery localiza as instâncias disponíveis, enquanto load balancing determina qual instância receberá a chamada. Também reutilizo conexões por meio de pools, mas dimensiono os limites considerando todas as instâncias da aplicação e a capacidade da dependência.
>
> Por fim, propago trace context, correlation ID, autenticação validada, tenant e deadlines. Em fluxos assíncronos, coloco esse contexto nos headers das mensagens ou uso executores e bibliotecas instrumentadas, pois `ThreadLocal` e MDC não atravessam automaticamente threads e processos.

---

# 28. Perguntas rápidas de entrevista

## Quando usar REST?

> APIs públicas, navegadores, integração com terceiros e cenários em que compatibilidade e simplicidade são prioridades.

## Quando usar gRPC?

> Comunicação interna, alto volume, baixa latência, streaming e contratos fortemente tipados.

## Service Discovery e load balancing são a mesma coisa?

> Não. Discovery identifica as instâncias disponíveis; load balancing escolhe qual delas receberá a requisição.

## Por que utilizar connection pooling?

> Para evitar repetir DNS, TCP, TLS e autenticação em todas as chamadas. O pool reduz latência, mas precisa ser limitado.

## Como dimensionar um pool?

> Considero throughput, latência, número de pods, duração das operações, picos e capacidade máxima da dependência.

## O que deve ser propagado entre serviços?

> Trace context, correlation ID, autenticação, tenant validado e deadline. Em mensageria, também event ID e causation ID.

## O que acontece com o contexto em `CompletableFuture`?

> Ele não é propagado automaticamente. É necessário usar executor instrumentado, task decorator ou uma solução como OpenTelemetry.

## Como evoluir Protobuf sem quebrar clientes?

> Adicionando campos com números novos, mantendo os existentes e reservando números e nomes de campos removidos.

---

# 29. Quadro final de revisão

| Conceito          | Definição                   | Principal cuidado                 |
| ----------------- | --------------------------- | --------------------------------- |
| HTTP              | Protocolo de comunicação    | Timeouts e conexões               |
| REST              | Estilo orientado a recursos | Contrato e granularidade          |
| gRPC              | Framework de RPC            | Acoplamento e compatibilidade     |
| JSON              | Serialização textual        | Payload maior                     |
| Protobuf          | Serialização binária tipada | Não reutilizar números            |
| Service Discovery | Localização de instâncias   | Health e cache                    |
| Load Balancing    | Distribuição de tráfego     | Algoritmo e conexões persistentes |
| Connection Pool   | Reutilização de conexões    | Dimensionamento global            |
| Correlation ID    | Correlação de logs          | Não substitui tracing             |
| Trace ID          | Identificação do trace      | Propagação entre serviços         |
| Deadline          | Limite total da operação    | Propagar tempo restante           |
| MDC               | Contexto nos logs           | Limpar após execução              |
| Event ID          | Identificação do evento     | Útil para idempotência            |
| Causation ID      | Evento que causou outro     | Rastreabilidade                   |

---

# 30. Mapa mental

```text
COMUNICAÇÃO ENTRE SERVIÇOS
│
├── REST
│   ├── HTTP
│   ├── Recursos
│   ├── JSON
│   └── APIs públicas
│
├── gRPC
│   ├── RPC
│   ├── HTTP/2
│   ├── Protobuf
│   ├── Código gerado
│   └── Streaming
│
├── CONTRATOS
│   ├── OpenAPI
│   ├── .proto
│   ├── Compatibilidade
│   ├── Campos opcionais
│   └── Versionamento
│
├── SERVICE DISCOVERY
│   ├── Registro
│   ├── DNS
│   ├── Client-side
│   └── Server-side
│
├── LOAD BALANCING
│   ├── Round Robin
│   ├── Weighted
│   ├── Least Connections
│   └── Consistent Hashing
│
├── CONNECTION POOLING
│   ├── HTTP
│   ├── Banco
│   ├── HikariCP
│   ├── Limites globais
│   └── Lei de Little
│
└── CONTEXT PROPAGATION
    ├── Trace ID
    ├── Correlation ID
    ├── Autenticação
    ├── Tenant
    ├── Deadline
    ├── MDC
    ├── Event ID
    └── Causation ID
```

Resumo estruturado com base no material enviado. 
