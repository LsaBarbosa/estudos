# Monólito  » Microservice

1. Identificar e mapear as dependências relacionadas ao módulo:
   * Banco de dados, jobs, contratos, outros módulos.


2. Definir um contrato claro para o novo microserviço com REST ou mensageria.


3. Criar banco de dados própios para o Microserviço.
   * Usar integração assincrona Transactional Outbox e mensageria com consumidores idempontente para popular dados durante a transição e evitar efeitos duplicados.


4. Para migração com baixo risco, padrão Strangler Fig. usando Roteamento dividindo as requisições entre o micro serviço e monolito.


5. Testes automatizados, métricas e logs centralizados e tracing distribuidos e estratégia clara de rollback


6.  Deploy com execução paralela ou Canary 

## Execução Passo a passo
 
### Contratos
| Situação                           | Tipo de comunicação recomendado |
| ---------------------------------- | ------------------------------- |
| Precisa de resposta imediata       | REST ou gRPC                    |
| Pode ser processado posteriormente | Mensageria                      |
| Integração entre domínios          | Eventos                         |
| Consulta simples                   | API síncrona                    |
| Processo resiliente e desacoplado  | Comunicação assíncrona          |

## Banco de dados
### Possíveis estratégias
| Estratégia                                                     | Conceito                                                                                                                                                          |
| -------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Migração gradual das tabelas                                   | Transferência dos dados e responsabilidades do banco legado para o novo banco em etapas, reduzindo o risco de uma migração completa de uma só vez.                |
| Replicação temporária                                          | Manutenção de cópias dos mesmos dados nos bancos antigo e novo durante o período de transição.                                                                    |
| Sincronização por eventos                                      | Publicação de eventos sempre que um dado é alterado, permitindo que outros sistemas atualizem suas próprias bases de dados.                                       |
| Leitura temporária do banco legado por uma camada de adaptação | Uso de uma camada intermediária para permitir que o novo serviço consulte dados ainda armazenados no banco legado sem criar acoplamento direto com sua estrutura. |
| Execução paralela para validação                               | Operação simultânea da solução antiga e da nova, comparando resultados para identificar divergências antes da migração definitiva.                                |

## Conssitência de Dados
| Estratégia                | Conceito                                                                                                                                                                  |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Consistência eventual     | Modelo em que os dados podem ficar temporariamente diferentes entre serviços ou réplicas, mas convergem para um estado consistente após o processamento das atualizações. |
| Eventos                   | Registros de fatos que já ocorreram no sistema, publicados para que outros serviços possam reagir sem acoplamento direto com o produtor.                                  |
| Saga                      | Padrão para coordenar transações distribuídas por meio de uma sequência de transações locais, utilizando ações compensatórias quando alguma etapa falha.                  |
| Transactional Outbox      | Padrão que salva a alteração de negócio e o evento na mesma transação do banco de dados, garantindo que o evento não seja perdido antes de ser publicado na mensageria.   |
| Compensações              | Operações utilizadas para desfazer ou neutralizar os efeitos de etapas anteriores quando uma transação distribuída não pode ser concluída.                                |
| Consumidores idempotentes | Consumidores preparados para processar a mesma mensagem mais de uma vez sem gerar efeitos duplicados ou inconsistentes.                                                   |

## Deploy
| Estratégia              | Conceito                                                                                                                                                        |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Feature flags           | Mecanismo que permite ativar ou desativar funcionalidades por configuração, sem necessidade de realizar um novo deploy.                                         |
| Canary release          | Estratégia que libera uma nova versão inicialmente para uma pequena parcela dos usuários, permitindo validar seu comportamento antes da liberação completa.     |
| Blue-green deployment   | Estratégia que mantém dois ambientes idênticos: um com a versão atual e outro com a nova versão. O tráfego é direcionado para o novo ambiente após a validação. |
| Shadow traffic          | Técnica que copia requisições reais para a nova versão do sistema sem utilizar sua resposta para atender o usuário, permitindo testes com tráfego de produção.  |
| Execução paralela       | Operação simultânea da versão antiga e da nova para validar comportamento, estabilidade e resultados antes da substituição definitiva.                          |
| Comparação de respostas | Processo de comparar as respostas produzidas pelas versões antiga e nova para identificar diferenças funcionais, erros ou alterações inesperadas.               |
