# AWS Lambda

O **AWS Lambda** é um serviço de computação **serverless** da AWS que permite executar código sem precisar criar ou administrar servidores.

Você escreve apenas a função, faz o deploy para a AWS e define quais eventos irão executá-la.

A AWS é responsável por:

* provisionar servidores
* iniciar a aplicação
* escalar automaticamente
* aplicar patches de segurança
* monitorar a infraestrutura

O desenvolvedor fica responsável apenas pela lógica de negócio.

---

# Uma função Lambda

Uma AWS Lambda executa apenas uma pequena unidade de código.

Por exemplo:

```
Receber um pedido → Validar os dados → Gravar no banco → Retornar resposta
```
- Cada função normalmente possui uma responsabilidade específica.
- Uma Lambda não executa sozinha. Ela é disparada por um evento.
  - requisição HTTP via API Gateway, Mensagem SNS ou SQS ...

- A AWS cria automaticamente novas instâncias da função.
- Você não precisa configurar novos servidores.
- A Lambda só consome recursos quando é executada.
- Stateless não deve depender de memória entre execuções.
- Ela funciona como um componente central em arquiteturas orientadas a eventos.

---

# Observabilidade

É possível monitorar uma Lambda com:

* **CloudWatch Logs** para logs
* **CloudWatch Metrics** para métricas como duração, erros e número de invocações
* **AWS X-Ray** para tracing distribuído
* **CloudWatch Alarms** para alertas

---

# Casos de uso comuns

* APIs REST com API Gateway
* Processamento de arquivos enviados ao S3
* Processamento assíncrono de mensagens do SQS
* Consumidores de eventos do EventBridge
* ETL de pequenos volumes
* Automação de tarefas
* Integrações entre serviços AWS
* Webhooks
* Backends para aplicações web e mobile

---

# Resumo para entrevista

> AWS Lambda é um serviço serverless da AWS que executa funções em resposta a eventos, sem que o desenvolvedor precise gerenciar servidores. A plataforma realiza provisionamento, escalabilidade e manutenção da infraestrutura automaticamente. O modelo de cobrança é baseado em invocações e tempo de execução, e a função pode ser acionada por serviços como API Gateway, S3, SQS, EventBridge e DynamoDB. Conceitos importantes incluem Cold Start, escalabilidade automática, execução stateless, integração via IAM e observabilidade com CloudWatch e X-Ray. Em Java, é comum utilizá-la para APIs, processamento de eventos e integrações em arquiteturas distribuídas.
