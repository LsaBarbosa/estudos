



# Resumo para entrevistas — Replicação de Banco de Dados e Spring Boot

## 1. Visão geral

**Replicação** é a manutenção de cópias dos mesmos dados em diferentes nós de banco.

Ela busca principalmente:

- alta disponibilidade;
- tolerância a falhas;
- escalabilidade de leitura;
- distribuição geográfica;
- redução de latência;
- recuperação mais rápida.

Os principais desafios são:

- atraso de replicação;
- leituras desatualizadas;
- conflitos de escrita;
- failover;
- split-brain;
- consistência entre réplicas;
- perda de dados em replicação assíncrona.

---

# 2. Modelos de replicação

| Modelo | Quem recebe escrita? | Principal vantagem | Principal problema |
|---|---|---|---|
| **Leader-follower** | Somente o líder | Simplicidade e escala de leitura | Líder pode ser gargalo |
| **Multi-leader** | Vários líderes | Escrita em várias regiões | Conflitos de escrita |
| **Leaderless** | Vários nós | Alta disponibilidade | Reconciliação complexa |

---

## 2.1 Leader-follower

Existe um nó principal, o **leader**, que recebe todas as escritas.

Os **followers** recebem e aplicam as alterações produzidas pelo líder.

```text
Aplicação
   │
   │ escrita
   ▼
Leader
   │
   ├────────► Follower 1
   └────────► Follower 2
```

### Fluxo

1. A aplicação envia `INSERT`, `UPDATE` ou `DELETE` ao líder.
2. O líder persiste a alteração.
3. A alteração é registrada no log.
4. O log é enviado aos followers.
5. Os followers reproduzem a alteração.
6. As réplicas podem atender leituras.

### Vantagens

- arquitetura mais simples;
- evita conflitos entre múltiplos escritores;
- facilita a ordenação das escritas;
- permite escalar leituras;
- uma réplica pode ser promovida em um failover.

### Desvantagens

- o líder pode limitar a escalabilidade de escrita;
- falha do líder exige promoção de uma réplica;
- followers podem retornar dados antigos;
- pode haver perda de commits na replicação assíncrona.

### Resposta de entrevista

> No modelo leader-follower, todas as escritas são direcionadas ao líder, que replica suas alterações para os followers. As réplicas podem atender leituras e servir como candidatas a failover. O principal trade-off está entre consistência, latência e disponibilidade, especialmente quando a replicação é assíncrona.

---

## 2.2 Multi-leader

Mais de um nó aceita escrita.

```text
Aplicação América ──► Leader A
                         │
                         │ replicação bidirecional
                         ▼
Aplicação Europa ───► Leader B
```

### Quando faz sentido

- sistemas multi-região;
- baixa latência de escrita regional;
- aplicações offline-first;
- filiais temporariamente desconectadas;
- continuidade de escrita durante falha regional.

### Principal problema: conflitos

Dois líderes podem alterar simultaneamente o mesmo dado.

```text
Leader A: endereço = Rio de Janeiro
Leader B: endereço = Lisboa
```

### Formas de resolução

| Estratégia | Funcionamento | Risco |
|---|---|---|
| **Last Write Wins** | Vence o timestamp mais recente | Pode descartar uma alteração válida |
| **Regra de negócio** | O domínio define a prioridade | Aumenta a complexidade da aplicação |
| **Merge** | Combina alterações compatíveis | Nem sempre é possível |
| **Versionamento** | Usa versões ou causalidade | Exige infraestrutura adicional |

### Problema com unicidade

Uma constraint global pode ser violada.

```text
Leader A cria usuario@email.com
Leader B cria usuario@email.com
```

Os dois nós podem aceitar a escrita localmente antes da replicação.

### Resposta de entrevista

> Multi-leader permite escrita em diferentes regiões, reduzindo latência e aumentando a disponibilidade regional. O custo é a possibilidade de conflitos concorrentes, que precisam ser resolvidos por last-write-wins, merge, versionamento ou regras específicas do domínio.

---

## 2.3 Leaderless

Não existe um líder central.

A escrita é enviada para vários nós.

```text
                   ┌────► Nó A
Aplicação ─────────┼────► Nó B
                   └────► Nó C
```

É comum trabalhar com:

- `N`: número total de réplicas;
- `W`: confirmações necessárias para escrita;
- `R`: réplicas consultadas na leitura.

Exemplo:

```text
N = 3
W = 2
R = 2
```

A escrita é aceita após dois nós confirmarem.

### Quórum

Uma relação comum é:

```text
W + R > N
```

Isso aumenta a probabilidade de uma leitura encontrar uma réplica que participou da escrita mais recente.

Contudo, essa fórmula não elimina automaticamente:

- escritas concorrentes;
- versões conflitantes;
- falhas de rede;
- sloppy quorum;
- divergências entre réplicas.

### Mecanismos importantes

**Read repair:** durante uma leitura, o sistema detecta uma réplica atrasada e a atualiza.

**Anti-entropy:** processo em segundo plano compara e reconcilia réplicas.

**Hinted handoff:** outro nó armazena temporariamente a escrita destinada a uma réplica indisponível.

### Resposta de entrevista

> Em uma arquitetura leaderless, o cliente ou coordenador envia operações para múltiplos nós. A consistência é ajustada por parâmetros como N, W e R. O modelo aumenta a disponibilidade e a escalabilidade, mas exige versionamento, read repair, anti-entropy e resolução de conflitos.

---

# 3. Replicação síncrona e assíncrona

## 3.1 Replicação síncrona

O líder só confirma o commit depois que uma ou mais réplicas confirmam a alteração.

```text
Cliente → Leader → Réplica → Leader → Cliente
```

### Vantagens

- menor risco de perda de dados;
- failover mais seguro;
- réplica mais atualizada;
- RPO próximo de zero.

### Desvantagens

- maior latência de escrita;
- dependência da rede;
- réplica lenta pode atrasar transações;
- falha de uma réplica pode bloquear escritas;
- custo elevado entre regiões distantes.

### Resposta de entrevista

> Na replicação síncrona, o commit só é confirmado depois que uma ou mais réplicas persistem a alteração. Isso melhora a durabilidade e torna o failover mais seguro, mas aumenta a latência e pode reduzir a disponibilidade de escrita.

---

## 3.2 Replicação assíncrona

O líder confirma a escrita sem esperar que as réplicas apliquem a alteração.

```text
Cliente → Leader → resposta
             │
             └────► réplica posteriormente
```

### Vantagens

- menor latência;
- maior throughput;
- uma réplica indisponível não bloqueia o líder;
- adequada para replicação geográfica.

### Desvantagens

- replication lag;
- stale reads;
- possível perda dos últimos commits;
- failover pode promover uma réplica desatualizada.

### Cenário clássico

```text
1. O líder confirma o pedido.
2. O pedido ainda não chegou à réplica.
3. O líder falha.
4. A réplica é promovida.
5. O pedido desaparece.
```

### Resposta de entrevista

> Na replicação assíncrona, o líder confirma a transação antes que as réplicas apliquem a alteração. Isso reduz a latência e aumenta a disponibilidade, mas introduz replication lag e risco de perda dos commits mais recentes durante um failover.

---

# 4. Replication lag

É o atraso entre o commit no líder e a aplicação da alteração na réplica.

```text
10:00:00.000 → commit no líder
10:00:00.300 → aplicação na réplica

Lag = 300 ms
```

## Principais causas

- lentidão de rede;
- réplica sobrecarregada;
- disco lento;
- transações muito grandes;
- consultas longas;
- contenção;
- excesso de escritas;
- backlog de logs;
- diferença de capacidade entre líder e réplica.

## Consequências

### Stale read

O cliente recebe um dado antigo.

### Dado aparentemente desaparece

```text
POST /pedidos → líder
GET /pedidos/100 → réplica atrasada
Resultado: 404
```

### Sistema voltando no tempo

```text
Requisição 1 → versão 20
Requisição 2 → versão 18
```

## Métricas importantes

- atraso em segundos;
- bytes pendentes;
- posição do WAL ou binlog;
- LSN do líder e da réplica;
- transações pendentes;
- último timestamp aplicado;
- velocidade de replay;
- status do canal de replicação.

## Ponto de entrevista sênior

```java
@Transactional(isolation = Isolation.SERIALIZABLE)
```

Isso não elimina replication lag.

O isolamento controla concorrência no banco onde a transação está sendo executada. Ele não garante que uma réplica assíncrona já tenha aplicado o commit.

> Isolamento transacional e consistência entre réplicas são problemas diferentes.

---

# 5. Failover

É a substituição de um nó indisponível por outro.

```text
Antes:
Leader A
Follower B
Follower C

Depois da falha:
Leader B
Follower C
```

## Etapas

1. Detectar a falha.
2. Eleger uma réplica.
3. Verificar se ela está atualizada.
4. Promovê-la a líder.
5. Impedir o líder antigo de escrever.
6. Redirecionar as aplicações.
7. Reintegrar ou reconstruir o nó antigo.

## RPO e RTO

### RPO — Recovery Point Objective

Quanto dado o sistema aceita perder.

```text
RPO = 30 segundos
```

### RTO — Recovery Time Objective

Quanto tempo o sistema pode ficar indisponível.

```text
RTO = 2 minutos
```

## Problemas comuns

- réplica atrasada;
- perda dos últimos commits;
- conexões apontando para o líder antigo;
- DNS em cache;
- dois líderes ativos;
- jobs duplicados;
- eventos fora de ordem;
- transações com resultado desconhecido.

### Resposta de entrevista

> Failover é a promoção de uma réplica para assumir o papel do líder. Um failover seguro exige detecção de falha, eleição, verificação do estado da réplica, redirecionamento dos clientes e fencing do líder antigo.

---

# 6. Split-brain

Ocorre quando dois nós acreditam ser o líder ao mesmo tempo.

```text
Leader A  X──── falha de rede ────X  Leader B
```

Ambos passam a aceitar escritas, criando estados divergentes.

## Como evitar

### Quórum

Um nó só se torna líder com o apoio da maioria.

Em três nós:

```text
Maioria = 2
```

### Fencing

Impede tecnicamente que o líder antigo continue escrevendo.

Exemplos:

- desligamento forçado;
- revogação de acesso;
- bloqueio de rede;
- fencing token;
- STONITH;
- lease expirável.

### Fencing token

```text
Líder antigo: token 41
Novo líder: token 42
```

O armazenamento rejeita operações com token menor que o atual.

## Ponto sênior

Não receber resposta de um nó não significa necessariamente que ele caiu.

Pode ter ocorrido:

- falha real do nó;
- partição de rede;
- pausa longa da JVM;
- sobrecarga;
- perda temporária de pacotes.

Por isso, detecção de falha deve ser combinada com quorum e fencing.

---

# 7. Read replica

É uma réplica utilizada para atender consultas de leitura.

```text
Escritas ─────────► Leader

Leituras ─────────► Read Replica 1
         └────────► Read Replica 2
```

## Indicada para

- relatórios;
- dashboards;
- catálogos;
- históricos;
- pesquisas;
- consultas analíticas;
- endpoints que toleram atraso.

## Não indicada para

- leitura após escrita;
- controle de estoque;
- validação seguida de escrita;
- autorização recém-alterada;
- confirmação de pagamento;
- `SELECT FOR UPDATE`;
- leitura-modificação-escrita;
- operações que exigem consistência forte.

## Read replica não é cache

| Read replica | Cache |
|---|---|
| Contém dados relacionais replicados | Guarda subconjuntos de dados |
| Executa SQL | Pode não oferecer SQL |
| Pode ter replication lag | Pode ter expiração |
| É mantida pelo banco | É normalmente controlado pela aplicação |

## Read replica não garante failover

A réplica só oferece alta disponibilidade quando existem também:

- promoção;
- eleição;
- fencing;
- redirecionamento;
- detecção de falha;
- política de recuperação.

---

# 8. Garantias de consistência

## 8.1 Read-after-write

Depois de escrever, o cliente deve conseguir ler imediatamente o valor gravado.

### Problema

```text
1. Atualização vai para o líder.
2. Aplicação retorna sucesso.
3. Consulta seguinte vai para uma réplica.
4. A réplica retorna o valor antigo.
```

### Soluções

1. **Ler do líder após uma escrita.**
2. **Manter afinidade temporária com o líder.**
3. **Esperar a réplica alcançar o LSN do commit.**
4. **Usar token de consistência ou versão.**
5. **Retornar o estado atualizado na resposta da escrita.**
6. **Fazer fallback para o líder.**

---

## 8.2 Monotonic reads

Depois que o usuário leu uma versão mais recente, não deve receber uma versão mais antiga.

```text
Primeira leitura → versão 20
Segunda leitura → versão 18
```

Possíveis soluções:

- afinidade com uma réplica;
- version token;
- roteamento por LSN;
- seleção de réplicas suficientemente atualizadas;
- fallback para o líder.

---

# 9. Implementação no Spring Boot

Uma arquitetura comum utiliza dois `DataSource`:

```text
WRITE → Leader
READ  → Read replica
```

## 9.1 `@Transactional(readOnly = true)`

```java
@Transactional(readOnly = true)
public Produto buscar(Long id) {
    return repository.findById(id).orElseThrow();
}
```

O `readOnly = true` pode:

- comunicar a intenção de somente leitura;
- reduzir flushes do Hibernate;
- permitir algumas otimizações;
- influenciar o gerenciador transacional.

Ele **não direciona automaticamente a consulta para uma read replica**.

---

## 9.2 `AbstractRoutingDataSource`

Pode ser utilizado para escolher o datasource com base no tipo da transação.

```java
public class ReadWriteRoutingDataSource
        extends AbstractRoutingDataSource {

    @Override
    protected Object determineCurrentLookupKey() {
        boolean readOnly =
                TransactionSynchronizationManager
                        .isCurrentTransactionReadOnly();

        return readOnly
                ? DataSourceType.READ
                : DataSourceType.WRITE;
    }
}
```

Política:

```text
@Transactional(readOnly = true) → réplica
@Transactional                  → líder
```

---

## 9.3 `LazyConnectionDataSourceProxy`

A conexão precisa ser adquirida depois que o Spring identificar se a transação é somente leitura.

```java
@Bean
@Primary
public DataSource dataSource(
        ReadWriteRoutingDataSource routing) {

    return new LazyConnectionDataSourceProxy(routing);
}
```

Isso evita obter uma conexão antes de o contexto transacional estar completamente definido.

---

## 9.4 Separação entre Command e Query

```text
PedidoCommandService
├── criarPedido
├── cancelarPedido
└── confirmarPagamento

PedidoQueryService
├── listarPedidos
├── consultarHistórico
└── gerarRelatório
```

### Política recomendada

| Operação | Destino |
|---|---|
| Commands | Líder |
| Queries tolerantes a atraso | Réplica |
| Queries críticas | Líder |
| Read-after-write | Líder ou réplica sincronizada |

Essa separação se aproxima de CQRS, mesmo sem implementar CQRS completo.

---

# 10. Armadilhas no Spring

## Self-invocation

```java
public void executar() {
    consultar();
}

@Transactional(readOnly = true)
public Pedido consultar() {
}
```

A chamada interna pode não passar pelo proxy do Spring.

Consequências:

- a transação pode não ser criada;
- `readOnly` pode ser ignorado;
- o roteamento pode cair no datasource padrão;
- a consulta pode ir para o líder.

Uma solução é separar as operações em serviços diferentes.

---

## Transações aninhadas

Uma escrita chamada dentro de uma transação `readOnly` pode reutilizar o contexto externo.

```java
@Transactional(readOnly = true)
public Relatorio gerar() {
    commandService.atualizarContador();
    return consultarDados();
}
```

Possíveis consequências:

- escrita na réplica;
- falha por conexão read-only;
- ausência de nova transação;
- comportamento inesperado de propagação.

---

## Leitura-modificação-escrita

Fluxos desse tipo devem utilizar o líder.

```java
@Transactional
public void reduzirEstoque(Long id, int quantidade) {
    Produto produto = repository.findById(id).orElseThrow();
    produto.reduzirEstoque(quantidade);
}
```

Ler de uma réplica atrasada pode causar decisão baseada em estado antigo.

---

## Optimistic locking com `@Version`

```java
@Version
private Long version;
```

Se o líder está na versão `10`, mas a réplica está na versão `8`, uma entidade carregada da réplica produzirá uma tentativa de atualização com versão antiga.

O update pode falhar sem que tenha ocorrido uma nova concorrência naquele momento.

> Fluxos de leitura-modificação-escrita devem ler no líder.

---

## Lock pessimista

```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
```

Precisa ser executado no líder. Uma read replica somente leitura não deve receber `SELECT FOR UPDATE`.

---

# 11. Operação e observabilidade

## Pools separados

```text
Hikari Leader Pool
Hikari Replica Pool
```

O dimensionamento deve considerar todas as instâncias.

```text
10 pods × 20 conexões = 200 conexões
```

Não basta avaliar o pool de apenas um pod.

## Health check

Executar apenas:

```sql
SELECT 1;
```

não é suficiente.

A réplica pode responder, mas estar dez minutos atrasada.

Também devem ser observados:

- replication lag;
- posição do log;
- replay ativo;
- última atualização;
- conexão com o líder;
- backlog;
- erros de replicação.

## Fallback para o líder

Quando a réplica falha, direcionar todas as leituras ao líder pode gerar sobrecarga repentina.

O fallback deve ser combinado com:

- circuit breaker;
- rate limiting;
- load shedding;
- cache;
- limites de conexão;
- priorização;
- monitoramento.

---

# 12. Failover, retry e idempotência

Durante um failover, uma transação pode ter sido confirmada, mas a aplicação perder a resposta.

```text
1. UPDATE é confirmado.
2. A conexão cai.
3. O cliente recebe timeout.
4. Não se sabe se a operação foi aplicada.
```

Repetir cegamente pode duplicar o efeito.

Soluções:

- idempotency key;
- constraint única;
- identificador da operação;
- verificação de estado;
- retry controlado;
- backoff e jitter.

```sql
CREATE UNIQUE INDEX uk_pagamento_idempotency
ON pagamento(idempotency_key);
```

**Backoff** aumenta progressivamente o tempo entre as tentativas.  
**Jitter** adiciona uma variação aleatória para evitar que várias instâncias repitam a operação simultaneamente. fileciteturn0file1

---

# 13. Replicação não substitui backup

Uma exclusão acidental também será replicada.

```sql
DELETE FROM cliente;
```

```text
Leader → DELETE
Replica → DELETE
```

| Mecanismo | Protege principalmente contra |
|---|---|
| Replicação | Falha de nó |
| Backup | Exclusão, corrupção e recuperação histórica |
| Failover | Indisponibilidade |
| Disaster recovery | Falha regional ou desastre |

---

# 14. Como escolher a arquitetura

## Leader-follower

Use quando:

- existe uma região principal;
- escrita pode ser centralizada;
- leitura é muito maior que escrita;
- simplicidade é importante;
- banco relacional é central;
- conflitos precisam ser evitados.

## Multi-leader

Use quando:

- várias regiões precisam escrever;
- baixa latência regional é necessária;
- falhas regionais não podem interromper escrita;
- o domínio consegue resolver conflitos.

## Leaderless

Use quando:

- alta disponibilidade é prioridade;
- consistência eventual é aceitável;
- o sistema precisa escalar horizontalmente;
- a aplicação consegue reconciliar versões.

## Replicação síncrona

Use quando:

- perda de commits confirmados é inaceitável;
- RPO precisa ser próximo de zero;
- latência adicional é aceitável;
- as réplicas estão próximas.

## Replicação assíncrona

Use quando:

- baixa latência é prioridade;
- existe replicação entre regiões;
- algum lag é aceitável;
- o negócio aceita possível perda recente.

---

# 15. Perguntas comuns de entrevista

## `@Transactional(readOnly = true)` usa read replica?

Não automaticamente. É necessário configurar roteamento, proxy, infraestrutura do provedor ou datasources separados.

## Read replica garante consistência?

Não necessariamente. Com replicação assíncrona, normalmente oferece consistência eventual.

## Como garantir read-after-write?

- leitura no líder;
- afinidade temporária;
- posição do log;
- token de consistência;
- espera pela réplica;
- fallback para o líder.

## Como evitar split-brain?

- quorum;
- fencing;
- eleição consistente;
- leases;
- fencing tokens;
- número ímpar de votantes.

## O que acontece com uma transação durante failover?

Ela pode:

- falhar antes do commit;
- ser confirmada normalmente;
- ser confirmada e perder a resposta;
- ser perdida caso a nova réplica esteja atrasada.

## Por que não mandar todas as leituras para réplicas?

Porque alguns fluxos exigem estado atual:

- estoque;
- pagamento;
- autorização;
- locks;
- unicidade;
- leitura após escrita;
- leitura-modificação-escrita.

## Read replica e failover replica são iguais?

Não necessariamente.

- **Read replica:** criada para escalar leitura.
- **Failover replica:** preparada para assumir como líder.

Uma instância pode cumprir os dois papéis, dependendo da arquitetura.

---

# 16. Resposta pronta de 60 segundos

> Em uma arquitetura leader-follower, todas as escritas são direcionadas ao líder e replicadas para os followers. As réplicas podem escalar leituras e funcionar como candidatas a failover. Quando a replicação é assíncrona, existe replication lag, então uma leitura logo após uma escrita pode retornar dados antigos.
>
> Para operações que exigem read-after-write, direciono a leitura ao líder ou uso posição de log ou token de consistência para garantir que a réplica já recebeu o commit. Em Spring Boot, `@Transactional(readOnly = true)` não faz esse roteamento sozinho; normalmente utilizo datasources separados, `AbstractRoutingDataSource` e `LazyConnectionDataSourceProxy`.
>
> Durante um failover, é essencial usar quorum e fencing para evitar split-brain. Também trato retries com idempotência, porque uma queda de conexão pode deixar o resultado da transação desconhecido.

---

# 17. Pontos que demonstram senioridade

Em uma entrevista, não pare na definição. Explique sempre:

1. **O benefício da decisão.**
2. **O custo ou trade-off.**
3. **O cenário em que usaria.**
4. **O cenário em que evitaria.**
5. **Como monitoraria em produção.**
6. **Como trataria falhas.**

Exemplo:

> Eu usaria read replicas para relatórios e consultas tolerantes a atraso. Não usaria para controle de estoque ou leitura após escrita. Também monitoraria o replication lag e removeria do balanceamento réplicas que ultrapassassem o limite de atraso definido pelo negócio.

---

# 18. Mapa mental de revisão

```text
REPLICAÇÃO
│
├── MODELOS
│   ├── Leader-follower
│   │   ├── Um escritor
│   │   ├── Escala leitura
│   │   └── Failover
│   ├── Multi-leader
│   │   ├── Escrita regional
│   │   └── Conflitos
│   └── Leaderless
│       ├── N, W e R
│       ├── Quórum
│       └── Reconciliação
│
├── MODO
│   ├── Síncrono
│   │   ├── Maior segurança
│   │   └── Maior latência
│   └── Assíncrono
│       ├── Menor latência
│       ├── Lag
│       └── Possível perda
│
├── FALHAS
│   ├── Failover
│   ├── Split-brain
│   ├── Quórum
│   └── Fencing
│
├── CONSISTÊNCIA
│   ├── Stale read
│   ├── Read-after-write
│   ├── Monotonic read
│   └── Consistência eventual
│
└── SPRING BOOT
    ├── Leader DataSource
    ├── Replica DataSource
    ├── AbstractRoutingDataSource
    ├── LazyConnectionDataSourceProxy
    ├── @Transactional
    ├── Command e Query
    ├── Idempotência
    └── Monitoramento de lag
```

Resumo estruturado a partir do material fornecido. fileciteturn0file0