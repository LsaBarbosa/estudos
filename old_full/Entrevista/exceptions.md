# Exceptions em Java e Spring Boot

Nesta aula, vamos revisar Exceptions com foco no que realmente importa para um desenvolvedor sênior ou Tech Lead.

A ideia principal é simples:

exception não é apenas um mecanismo de erro.

Ela faz parte do design da aplicação.

---

# 1. Hierarquia principal

Tudo começa em `Throwable`.

Abaixo dele temos dois grupos principais:

`Error`.

E `Exception`.

`Error` representa falhas graves do ambiente ou da JVM, como `OutOfMemoryError` e `StackOverflowError`.

Normalmente não capturamos `Error` para tentar continuar a aplicação.

`Exception` representa falhas relacionadas à execução da aplicação.

Dentro dela temos `RuntimeException`, que é a base das principais unchecked exceptions.

---

# 2. Checked e unchecked exceptions

Checked exceptions são verificadas pelo compilador.

O desenvolvedor precisa tratá-las com `try/catch` ou declarar com `throws`.

Um exemplo clássico é `IOException`.

Unchecked exceptions derivam normalmente de `RuntimeException`.

Exemplos:

`NullPointerException`.

`IllegalArgumentException`.

`IllegalStateException`.

O compilador não obriga o tratamento.

Em aplicações Spring, é comum utilizar unchecked exceptions também para regras de negócio.

Por exemplo:

`SaldoInsuficienteException`.

`PedidoInvalidoException`.

`LimiteExcedidoException`.

---

# 3. Throw e throws

`throw` lança efetivamente uma exception.

`throws` declara que um método pode propagá-la.

Uma forma simples de memorizar:

`throw` é ação.

`throws` é contrato.

---

# 4. Propagação de exceptions

Imagine o fluxo:

controller.

service.

repository.

banco.

Se o repository lança uma exception e não a trata, ela sobe para o service.

Se o service também não tratar, ela continua subindo.

Isso é exception propagation.

A regra mais importante é:

trate a exception onde existe contexto suficiente para tomar uma decisão útil.

Não é necessário colocar `try/catch` em todas as camadas.

---

# 5. Try, catch e finally

O `try` contém o código que pode falhar.

O `catch` trata determinada exception.

O `finally` executa código de finalização.

Um cuidado importante:

se `try` e `finally` lançarem exceptions, a exception do `finally` pode mascarar a exception original.

Também devemos evitar `return` dentro de `finally`, porque ele pode sobrescrever retornos e até esconder exceptions.

---

# 6. Try-with-resources

Para recursos como arquivos e conexões, devemos preferir `try-with-resources`.

O recurso precisa implementar `AutoCloseable`.

Exemplos comuns:

conexões JDBC.

statements.

result sets.

arquivos.

streams.

O Java fecha automaticamente os recursos.

Quando existem vários recursos, eles são fechados na ordem inversa da declaração.

---

# 7. Suppressed exceptions

Imagine que a lógica principal falhe.

Depois, o método `close` também falhe.

Com `try-with-resources`, a primeira exception continua sendo a principal.

A exception de fechamento é preservada como suppressed exception.

Isso evita perder a causa real do problema.

---

# 8. Custom exceptions

Uma custom exception deve representar significado.

Em vez de:

`RuntimeException`.

Prefira algo como:

`SaldoInsuficienteException`.

`ClienteNaoEncontradoException`.

`PedidoNaoPodeSerCanceladoException`.

Mas não devemos criar uma exception para cada `if`.

Ela deve representar uma condição relevante do domínio.

---

# 9. Exception de domínio e exception técnica

Exception de domínio representa regra de negócio.

Exemplo:

saldo insuficiente.

pedido já cancelado.

limite excedido.

Exception técnica representa infraestrutura.

Exemplo:

falha de banco.

timeout.

erro de rede.

falha de arquivo.

Essa separação é importante para evitar acoplamento.

O domínio não deveria conhecer `SQLException`.

E o controller não deveria depender de exceptions específicas do PostgreSQL.

---

# 10. Tradução de exceptions

Às vezes uma exception técnica deve ser traduzida.

Por exemplo:

o banco detecta uma violação de chave única.

Tecnicamente temos uma falha de persistência.

Mas semanticamente podemos ter:

`EmailJaCadastradoException`.

Essa tradução faz sentido porque adiciona significado.

O importante é preservar a causa original.

---

# 11. Cuidado com catch genérico

Evite usar:

`catch Exception`

em todos os métodos.

Isso pode esconder bugs e misturar problemas completamente diferentes.

Também evite:

`printStackTrace`.

Em produção, precisamos de logging estruturado.

Com informações como:

trace ID.

correlation ID.

identificador da operação.

exception.

stack trace.

---

# 12. Exceptions não devem controlar fluxo normal

Exceptions não deveriam substituir `if`, `Optional` ou uma modelagem adequada.

Se determinada situação acontece o tempo todo e faz parte do fluxo normal, talvez ela não devesse ser representada por exception.

O problema não é apenas performance.

É principalmente legibilidade.

---

# 13. Exception handling no Spring

No Spring podemos usar `@ExceptionHandler`.

Quando ele fica dentro de um controller, trata exceptions relacionadas àquele controller.

Para tratamento global, normalmente utilizamos:

`@RestControllerAdvice`.

Essa classe centraliza a transformação das exceptions em respostas HTTP.

---

# 14. Domínio não deve conhecer HTTP

Imagine que o domínio lance:

`ClienteNaoEncontradoException`.

O domínio não deveria lançar diretamente um `404`.

Ele apenas informa:

cliente não encontrado.

Na camada HTTP, o `RestControllerAdvice` transforma essa exception em:

`404 Not Found`.

Essa separação mantém o domínio independente do protocolo HTTP.

---

# 15. ProblemDetail

No Spring podemos utilizar `ProblemDetail` para padronizar respostas de erro.

Podemos retornar informações como:

status.

título.

detalhe.

tipo do erro.

identificador da requisição.

E códigos estáveis, como:

`CUSTOMER_NOT_FOUND`.

`INSUFFICIENT_BALANCE`.

Esses códigos são melhores do que fazer o cliente depender do texto da mensagem.

---

# 16. Principais status HTTP

Uma referência prática:

`400`.

Request inválido ou malformado.

`401`.

Falha de autenticação.

`403`.

Usuário autenticado, mas sem autorização.

`404`.

Recurso inexistente.

`409`.

Conflito com o estado atual.

`422`.

Request compreendido, mas semanticamente inválido.

`500`.

Falha inesperada do servidor.

Um erro de negócio não deveria automaticamente virar `500`.

---

# 17. Validação e regra de negócio

Validação pode ser:

campo obrigatório.

email inválido.

valor fora de formato.

Regra de negócio pode ser:

saldo insuficiente.

limite excedido.

pedido não pode ser cancelado.

São problemas diferentes.

E não precisam necessariamente retornar o mesmo status HTTP.

---

# 18. Segurança

Nunca devemos retornar ao cliente:

stack trace.

SQL.

nome de tabelas.

paths internos.

credenciais.

tokens.

detalhes da infraestrutura.

O cliente recebe uma mensagem segura.

Os detalhes técnicos ficam nos logs internos.

---

# 19. Observabilidade

Para falhas inesperadas, precisamos conectar exception handling com observabilidade.

Queremos:

logs estruturados.

trace ID.

métricas.

traces distribuídos.

alertas.

Mas nem toda exception deve gerar `ERROR`.

Um `404` esperado pode ser apenas comportamento normal.

Já uma `NullPointerException` inesperada pode exigir:

`500`.

log em `ERROR`.

stack trace.

métrica.

alerta.

---

# 20. Exceptions e @Transactional

Em Spring existe um detalhe importante.

Por padrão, `RuntimeException` e `Error` normalmente provocam rollback.

Checked exceptions normalmente não provocam rollback automaticamente.

Isso pode ser configurado com `rollbackFor`.

Por isso, exception e transação precisam ser pensadas em conjunto.

---

# Resumo para entrevista

Uma resposta madura seria:

“Eu trato exceptions como parte do design da aplicação. Separo falhas de domínio de falhas técnicas e evito capturar exceptions em todas as camadas. Deixo a propagação acontecer até um ponto que realmente tenha contexto para recuperar, traduzir ou responder à falha.

No Spring, normalmente centralizo a tradução HTTP com `RestControllerAdvice` e utilizo `ProblemDetail` para manter respostas consistentes. O domínio não conhece HTTP. Uma exception como `ClienteNaoEncontradoException` é traduzida para `404` apenas na borda da aplicação.

Também evito transformar tudo em `500`, não exponho detalhes internos ao cliente e conecto falhas inesperadas com logging, tracing, métricas e observabilidade.

O objetivo é preservar significado, baixo acoplamento e capacidade de diagnóstico.”
