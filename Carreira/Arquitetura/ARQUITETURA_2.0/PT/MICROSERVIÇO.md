## 1. Começar pelo domínio e pela responsabilidade de negócio

> “Eu começaria pelo domínio e pela responsabilidade de negócio, não pela infraestrutura.”
>
> Primeiro é necessário entender o que o módulo resolve, quais integrações ele faz e quais operações ele precisa possuir.
>
> Isso está diretamente ligado ao DDD

---

## 3. Definir os limites do serviço

Cada microsserviço deve ter limites claros e quais:
>dados e regras pertencem ao serviço;
>
>operações ele oferece e eventos ele publica;
>
>informações ele recebe de outros serviços.

No DDD, esse limite pode ser associado ao conceito de **Bounded Context**, ou contexto delimitado.

---

# 7. Definir os requisitos não funcionais

> “Depois definiria requisitos não funcionais, como disponibilidade, volume, latência, segurança e consistência.”

## 7.1 Disponibilidade

Disponibilidade representa quanto tempo o serviço permanece operacional.

Para melhorar disponibilidade, podem ser usados:

* múltiplas instâncias;
* balanceador de carga;
* health checks;

---

## 7.2 Volume e throughput

##### É necessário estimar:

* mensagens e requisições por segundo;

##### O volume influencia:

* Quantidade de instâncias índices do banco;
* Particionamento e cache, filas;
* Limites de conexão e tamanho de pools; 

---

## 7.3 Latência

Latência é o tempo entre a requisição e a resposta.

É necessário definir percentis, e não apenas média.

```text
P95 menor que 300 milissegundos.
O P95 indica que 95% das requisições devem responder dentro daquele tempo.
```



A média pode esconder requisições muito lentas.

### Fontes comuns de latência

* consultas sem índice, chamadas em cadeia entre serviços;
* conexões mal configuradas,  gargalos no pool de conexões.
* processamento bloqueante;
---
## 7.5 Consistência

> uma transação ACID pode garantir consistência local.
>
>`@Transactional` normalmente não cria uma transação única entre vários microsserviços.

##### é necessário tratar a consistência distribuída.

* **Consistência eventual:** Modelo de consistência em que, após um período de propagação, todas as réplicas convergem para o mesmo estado.

* **Saga:** Padrão para coordenar transações distribuídas por meio de uma sequência de transações locais e ações de compensação.

* **Eventos:** Registros imutáveis que representam algo relevante que aconteceu no domínio de negócio e podem ser consumidos por outros sistemas.

* **Compensações:** Operações executadas para desfazer ou neutralizar os efeitos de uma etapa anterior quando uma Saga falha.

* **Outbox Pattern:** Padrão que garante a publicação confiável de eventos, gravando a alteração de dados e o evento na mesma transação local.

* **Reconciliação:** Processo de verificar e corrigir divergências entre sistemas para garantir que o estado final seja consistente.

* **Idempotência:** Propriedade de uma operação que produz o mesmo resultado quando executada uma ou mais vezes com a mesma entrada.

---

# 8. Projetar o tratamento de falhas

> “Também projetaria tratamento de falhas desde o início.”

Em sistemas distribuídos, falhas não são exceções raras.
 

## 8.3 Circuit Breaker

O Circuit Breaker evita continuar chamando uma dependência que está falhando repetidamente.

Estados principais:

```text
Fechado:
as chamadas passam normalmente.

Aberto:
as chamadas são bloqueadas rapidamente.

Meio aberto:
algumas chamadas de teste são permitidas.
```
Ele ajuda a evitar:

* cascata de falhas;
* saturação de threads;
* esgotamento de conexões;
* aumento progressivo de latência.

---

## 8.4 Bulkhead

Bulkhead isola recursos para impedir que uma dependência consuma toda a capacidade do serviço.

Por exemplo, chamadas ao serviço de relatórios não deveriam consumir todas as threads usadas pelo serviço de pagamentos.

A ideia é semelhante aos compartimentos de um navio: uma falha em um compartimento não deve afundar toda a embarcação.

---

# 10. Projetar observabilidade

> “Também projetaria observabilidade desde o início.”

Observabilidade permite compreender o estado interno do sistema por meio dos sinais que ele produz.

Os pilares mais conhecidos são:

* logs;
* métricas;
* traces distribuídos.

---

## 10.1 Logs

Logs devem ser estruturados e conter contexto.

Também é necessário evitar dados sensíveis em logs.

---

## 10.2 Métricas

Métricas permitem acompanhar o comportamento agregado do serviço.

Com Spring Boot Actuator e Micrometer, métricas podem ser exportadas para Prometheus.

---

## 10.3 Tracing distribuído

Tracing distribuído acompanha uma requisição através de vários serviços.

Todos os spans compartilham um `traceId`.

Isso permite descobrir:

* onde ocorreu a falha;
* qual serviço ficou lento;
* quanto tempo cada etapa consumiu;
* qual dependência causou o problema.

Tecnologias comuns incluem:

* OpenTelemetry;
* Jaeger;
* Zipkin;
* Grafana Tempo.

---


# 13. Planejar testes desde o início

> “Também projetaria testes desde o início.”

Microsserviços precisam de diferentes níveis de teste.

---

## 13.1 Testes unitários

Validam regras isoladas.

São rápidos e não dependem de infraestrutura.

---

## 13.2 Testes de integração

Validam a integração com componentes reais ou próximos da realidade.

Testcontainers é bastante útil no ecossistema Java.

Isso reduz diferenças entre o ambiente de testes e o ambiente real.

---

## 13.3 Testes de contrato

Validam se produtor e consumidor concordam sobre o contrato.

Esses testes ajudam a detectar alterações incompatíveis antes do deploy.

---

## 13.4 Testes end-to-end

Validam o fluxo completo.

Devem existir, mas em menor quantidade, porque são:

* mais lentos;
* mais frágeis;
* mais caros;
* mais difíceis de diagnosticar.

---

## 13.5 Testes de falha

Também é necessário testar situações negativas:

Um sistema distribuído não está adequadamente testado se apenas o caminho de sucesso foi validado.

---

# 14. Planejar a estratégia de deploy

> “Também projetaria estratégia de deploy desde o início.”
---

## 14.1 Rolling deployment

As instâncias são substituídas gradualmente.

```text
Versão 1
Versão 1
Versão 1

Depois:

Versão 1
Versão 1
Versão 2

Depois:

Versão 1
Versão 2
Versão 2
```

Durante algum tempo, duas versões coexistem.

Por isso, contratos e banco precisam ser compatíveis entre versões.

---

## 14.2 Blue-green deployment

Existem dois ambientes:

```text
Blue: versão atual.
Green: nova versão.
```

Após validar o Green, o tráfego é direcionado para ele.

Se houver falha, o tráfego pode voltar ao Blue.

---

## 14.3 Canary deployment

A nova versão recebe uma pequena porcentagem do tráfego.

Exemplo:

```text
95% do tráfego → versão atual.
5% do tráfego → nova versão.
```

Se métricas e erros permanecerem saudáveis, a porcentagem é aumentada.

---


---

# Exemplo resumido: microsserviço de pedidos

## Responsabilidade

Gerenciar o ciclo de vida de pedidos.

## Dados próprios

* pedido;
* itens;
* status;
* histórico;
* valor total.

## API

```text
POST /pedidos
GET /pedidos/{id}
POST /pedidos/{id}/cancelamento
```

## Eventos publicados

```text
PedidoCriado
PedidoConfirmado
PedidoCancelado
```

## Eventos consumidos

```text
PagamentoAprovado
PagamentoRecusado
EstoqueReservado
ReservaDeEstoqueFalhou
```

## Requisitos não funcionais

```text
Disponibilidade: 99,9%.
P95: até 300 milissegundos.
Pico: 1.000 requisições por segundo.
Consistência: eventual entre pedido, estoque e pagamento.
```

## Resiliência

```text
Timeout nas chamadas remotas.
Retry apenas para falhas transitórias.
Circuit breaker para dependências.
Consumidores idempotentes.
Dead-letter queue para mensagens não processáveis.
```

## Observabilidade

```text
Logs com pedidoId, eventId e traceId.
Métricas de pedidos criados e cancelados.
Métrica de taxa de erro.
Tracing das integrações.
Alertas de latência e falhas.
```

---

# Resposta adequada para entrevista

> Eu começaria identificando uma capacidade de negócio coesa e definindo claramente o limite do domínio. O microsserviço deveria controlar suas próprias regras e seus próprios dados, expondo contratos bem definidos por APIs ou eventos.
>
> Depois levantaria os requisitos não funcionais, como disponibilidade, volume, latência, segurança e nível de consistência. Essas informações determinariam decisões como persistência, comunicação síncrona ou assíncrona, escalabilidade e mecanismos de resiliência.
>
> Desde o início, projetaria timeouts, retries controlados, circuit breaker, idempotência, tratamento de mensagens duplicadas e estratégia de consistência distribuída. Também incluiria logs estruturados, métricas, tracing, testes de contrato, testes de integração e uma estratégia segura de deploy e evolução do banco.
>
> Por fim, validaria se existe uma necessidade real de separação. Microsserviços oferecem autonomia e escalabilidade independente, mas aumentam a complexidade operacional e distribuída. Quando esses benefícios não justificam o custo, eu preferiria começar com um monólito modular bem estruturado.
