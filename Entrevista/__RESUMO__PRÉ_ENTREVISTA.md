# Exceptions

- Exceptions como parte do design da aplicação.
  - Primeiro separo exceptions de domínio de exceptions técnicas.  
    - `Exceptions de domínio`: representam situações semanticamente relevantes
    - `Exceptions técnicas` : representam falhas de infraestrutura.
      
  - Evito capturar exceptions indiscriminadamente em todas as camadas e deixo a propagação acontecer até um ponto que realmente tenha contexto para recuperar, traduzir ou responder à falha.

- Em aplicações Spring, centralizo a tradução para HTTP utilizando RestControllerAdvice.
  - O domínio não conhece status HTTP. A camada HTTP transforma exceptions em respostas coerentes, usando ProblemDetail e códigos de erro estáveis.

- Também diferencio validação, conflito, recurso inexistente e falha inesperada.
  - Para erros inesperados, considero logging estruturado, trace ID, métricas e observabilidade.
  - E evito expor stack traces ou detalhes internos ao cliente.
