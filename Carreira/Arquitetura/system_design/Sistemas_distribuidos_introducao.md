# Fundamentos de sistemas distribuídos

## 8. Sistemas stateless e stateful

### Stateless

Uma aplicação **stateless** não depende de dados armazenados localmente entre uma requisição e outra.

Cada requisição contém ou permite recuperar todas as informações necessárias para seu processamento.

```text
Requisição
   ↓
Qualquer instância
   ↓
Banco ou cache compartilhado
```

Exemplo:

```http
GET /pedidos/123
Authorization: Bearer token
```

Qualquer instância pode atender a requisição, pois o token e o identificador estão presentes e o estado permanente está no banco.

#### Vantagens

* fácil escalabilidade horizontal;
* melhor balanceamento de carga;


### Stateful

Uma aplicação **stateful** mantém informações necessárias entre interações.

Exemplos:

* sessão armazenada na memória da aplicação;
* banco de dados;

```text
Cliente A ──> Instância 1
              possui a sessão do cliente A
```

Se o cliente for direcionado para outra instância, a sessão pode não existir.

Soluções possíveis:

* sticky session;
* armazenar sessões no Redis;
* replicar o estado;
* utilizar armazenamento persistente.

### Comparação

| Característica                        | Stateless          | Stateful                    |
| ------------------------------------- | ------------------ | --------------------------- |
| Mantém estado local entre requisições | Não                | Sim                         |
| Escalabilidade horizontal             | Mais simples       | Mais complexa               |
| Substituição de instâncias            | Fácil              | Exige recuperação de estado |
| Exemplo                               | API REST           | Banco de dados              |
| Balanceamento de carga                | Qualquer instância | Pode exigir afinidade       |

---

## 11. Escalabilidade vertical

**Escalabilidade vertical**, ou *scale up*, significa aumentar os recursos de uma máquina.

```text
Antes:
4 CPUs e 8 GB de RAM

Depois:
16 CPUs e 64 GB de RAM
```

### Vantagens

* implementação simples;
* não exige necessariamente mudar a arquitetura;
* útil para bancos de dados e sistemas legados.

### Limitações

* existe um limite físico;
* máquinas maiores custam mais;
* pode exigir indisponibilidade;
* mantém a dependência de uma única máquina;
* não elimina ponto único de falha.

---

## 12. Escalabilidade horizontal

**Escalabilidade horizontal**, ou *scale out*, significa adicionar mais instâncias ou máquinas.

```text
Antes:
1 instância

Depois:
4 instâncias
```

```text
Cliente
   ↓
Load Balancer
   ├── Instância 1
   ├── Instância 2
   ├── Instância 3
   └── Instância 4
```

### Vantagens

* permite distribuir carga;
* melhora a disponibilidade;
* permite crescimento gradual;
* facilita substituição de instâncias;
* combina bem com cloud e containers.

### Desafios

* gerenciamento do estado;
* comunicação pela rede;
* concorrência;
* consistência;
* balanceamento;
* observabilidade;
* coordenação entre instâncias.

### Comparação

| Característica      | Vertical                | Horizontal                |
| ------------------- | ----------------------- | ------------------------- |
| Estratégia          | Aumentar a máquina      | Adicionar máquinas        |
| Complexidade        | Menor                   | Maior                     |
| Limite              | Capacidade física       | Geralmente mais flexível  |
| Disponibilidade     | Pode manter ponto único | Pode aumentar redundância |
| Aplicação stateless | Não obrigatória         | Altamente recomendada     |

---

## 13. Alta disponibilidade

**Alta disponibilidade** é a capacidade de o sistema permanecer acessível durante falhas, manutenções ou aumento de carga.

```text
Load Balancer
   ├── Instância 1
   ├── Instância 2
   └── Instância 3
```

Se a instância 1 falhar, as outras continuam atendendo.

Alta disponibilidade normalmente exige:

* múltiplas instâncias;
* balanceador de carga;
* health checks;
* redundância;
* replicação;
* failover;
* distribuição entre zonas;
* monitoramento.

### Importante

Alta disponibilidade não significa ausência total de falhas. Significa que o sistema consegue continuar operando apesar delas.

---

## 14. Tolerância a falhas

**Tolerância a falhas** é a capacidade de continuar funcionando corretamente, mesmo quando algum componente falha.

Exemplos:

* uma instância cai e outra assume;
* uma mensagem falha e é reprocessada;
* uma réplica do banco assume após a queda do primary;
* um serviço usa fallback quando uma dependência está indisponível.

```text
Serviço A
   ├── tenta Serviço B
   ├── Serviço B falha
   └── utiliza fallback ou resposta degradada
```

Mecanismos comuns:

* retry com backoff e jitter;
* circuit breaker;
* redundância;
* replicação;
* failover;
* filas;
* idempotência;
* timeouts;
* recuperação automática;
* graceful degradation.

### Diferença para alta disponibilidade

* **Alta disponibilidade:** foco em manter o sistema acessível.
* **Tolerância a falhas:** foco em continuar operando mesmo durante falhas.

Os conceitos são relacionados, mas não idênticos.

---

## 15. Redundância

**Redundância** significa possuir componentes duplicados para evitar dependência de um único recurso.

```text
Aplicação
 ├── Instância A
 └── Instância B
```

Outros exemplos:

* dois servidores;
* múltiplas réplicas de banco;
* mais de uma zona de disponibilidade;
* múltiplos links de rede;
* brokers replicados;
* cópias de dados.

### Objetivo

Evitar um **Single Point of Failure**, ou ponto único de falha.

```text
Sem redundância:

Aplicação ──> Banco único
                ↓
             Falhou
                ↓
          Sistema indisponível
```

```text
Com redundância:

Aplicação ──> Banco primário
                  ↓
              Réplica
```

### Atenção

Ter componentes duplicados não garante automaticamente tolerância a falhas. Também é necessário:

* detectar a falha;
* redirecionar tráfego;
* manter dados sincronizados;
* testar o failover;
* evitar que todos os componentes falhem pela mesma causa.

---

# Relação entre os conceitos

```text
Sistema distribuído
│
├── É executado em nós
│   └── Cada nó executa processos
│       └── Os processos executam instâncias de serviços
│
├── Os nós podem formar um cluster
│
├── As instâncias podem manter
│   ├── Estado local
│   └── Estado compartilhado
│
├── Os serviços podem ser
│   ├── Stateless
│   └── Stateful
│
├── Os serviços se comunicam de forma
│   ├── Síncrona
│   └── Assíncrona
│
├── O sistema pode escalar
│   ├── Verticalmente
│   └── Horizontalmente
│
└── Para resistir a falhas utiliza
    ├── Alta disponibilidade
    ├── Tolerância a falhas
    └── Redundância
```

# Exemplo em Spring Boot e Kubernetes

Considere um serviço de pedidos desenvolvido em Spring Boot:

```text
Usuário
   ↓
Load Balancer
   ├── Pod 1: pedido-service
   ├── Pod 2: pedido-service
   └── Pod 3: pedido-service
            ↓
        PostgreSQL
            ↓
          Kafka
```

Nesse cenário:

* cada servidor do cluster é um **nó**;
* cada JVM é um **processo**;
* `pedido-service` é o **serviço**;
* cada pod é uma **instância**;
* o conjunto dos nós forma o **cluster**;
* variáveis em memória são **estado local**;
* PostgreSQL e Kafka mantêm **estado compartilhado**;
* a API Spring Boot deve ser preferencialmente **stateless**;
* REST representa comunicação **síncrona**;
* Kafka representa comunicação **assíncrona**;
* aumentar CPU e memória do pod é escalabilidade **vertical**;
* aumentar a quantidade de pods é escalabilidade **horizontal**;
* múltiplos pods oferecem **redundância**;
* manter o serviço funcionando após a queda de um pod aumenta a **alta disponibilidade**;
* recuperar mensagens, redirecionar tráfego e sobreviver a falhas representa **tolerância a falhas**.
