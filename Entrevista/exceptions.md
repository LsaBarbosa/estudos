# Exceptions em Java e Spring Boot

Nesta aula, vamos construir uma visão completa sobre exceptions em Java e Spring Boot.

O objetivo não é apenas entender `try`, `catch`, `throw` e `throws`.

A ideia é entender exceptions como parte do design da aplicação.

Isso inclui:

como Java representa erros;

como exceptions propagam pela aplicação;

quando devemos tratar uma exception;

quando devemos deixá-la propagar;

como criar exceptions de domínio;

como separar erros de negócio de erros técnicos;

como o Spring transforma exceptions em respostas HTTP;

e como estruturar um tratamento de erros adequado para aplicações de produção.

---

# Parte 1 — A hierarquia de exceptions em Java

Tudo começa com a classe `Throwable`.

`Throwable` é a classe-base de tudo que pode ser lançado através da palavra-chave `throw`.

A partir de `Throwable`, temos duas grandes ramificações.

`Error`.

E `Exception`.

Podemos imaginar a hierarquia da seguinte forma.

`Throwable`.

Abaixo de `Throwable`, temos `Error`.

E também temos `Exception`.

Dentro de `Exception`, temos `RuntimeException`.

Essa distinção é muito importante.

`Error` normalmente representa problemas graves relacionados à máquina virtual Java ou ao ambiente de execução.

Exemplos são:

`OutOfMemoryError`.

`StackOverflowError`.

E alguns erros de carregamento de classes.

Já `Exception` representa condições de falha relacionadas à execução da aplicação.

Dentro de `Exception`, temos dois grandes comportamentos.

As checked exceptions.

E as unchecked exceptions.

As unchecked exceptions normalmente são subclasses de `RuntimeException`.

---

# Parte 2 — Checked exceptions e unchecked exceptions

A principal diferença entre checked e unchecked exceptions está no compilador.

Quando um método pode lançar uma checked exception, o compilador obriga o desenvolvedor a tomar uma decisão.

Ou a exception é tratada.

Ou ela é declarada na assinatura do método.

Por exemplo, algumas operações com arquivos podem lançar `IOException`.

Se um método executa uma operação que pode gerar `IOException`, normalmente precisamos usar `try` e `catch`.

Ou declarar `throws IOException`.

Esse é um contrato imposto pelo compilador.

Já uma unchecked exception não precisa ser obrigatoriamente tratada nem declarada.

Ela normalmente deriva de `RuntimeException`.

Exemplos comuns são:

`NullPointerException`.

`IllegalArgumentException`.

`IllegalStateException`.

E `ArithmeticException`.

O compilador permite que essas exceptions propaguem livremente.

Mas existe um cuidado importante.

Não devemos pensar que checked exception significa erro recuperável e unchecked exception significa bug.

Essa classificação é simplista.

Podemos perfeitamente criar uma unchecked exception para representar uma regra de negócio.

Por exemplo:

`SaldoInsuficienteException`.

`PedidoInvalidoException`.

Ou `LimiteExcedidoException`.

Em aplicações Spring, isso é bastante comum.

---

# Parte 3 — Quando usar checked ou unchecked exceptions

Uma boa forma de decidir é perguntar:

o chamador realmente precisa ser obrigado a reconhecer essa condição?

Se a resposta for sim, uma checked exception pode fazer sentido.

Imagine uma API de biblioteca que realiza uma operação externa e espera que o chamador trate uma determinada falha.

Nesse caso, tornar essa possibilidade explícita no contrato pode ser interessante.

Por outro lado, em aplicações corporativas com várias camadas, checked exceptions podem poluir significativamente as assinaturas dos métodos.

Imagine um serviço com dezenas de métodos.

E imagine que cada método precise declarar:

`SaldoInsuficienteException`.

`ContaBloqueadaException`.

`LimiteExcedidoException`.

`ClienteInativoException`.

E assim por diante.

Esse tipo de design pode criar muito acoplamento entre as camadas.

Por isso, em aplicações Spring, exceptions de negócio são frequentemente implementadas como subclasses de `RuntimeException`.

---

# Parte 4 — A diferença entre Exception e Error

`Exception` representa situações que a aplicação pode potencialmente tratar.

`Error`, por outro lado, geralmente representa uma condição muito mais séria.

Imagine um `OutOfMemoryError`.

Isso significa que a máquina virtual Java não conseguiu alocar memória conforme necessário.

Mesmo que o código capture esse erro, não existe garantia de que a aplicação esteja em condições adequadas para continuar funcionando normalmente.

Por isso, normalmente não capturamos `Error` para tentar continuar a aplicação.

É possível que algum componente de infraestrutura capture um erro apenas para registrar logs ou encerrar o processo de forma mais controlada.

Mas capturar um `Error` e simplesmente continuar como se nada tivesse acontecido é uma estratégia extremamente arriscada.

---

# Parte 5 — Exception propagation

Agora vamos entender um dos conceitos mais importantes.

A propagação de exceptions.

Imagine uma chamada que começa no controller.

O controller chama um service.

O service chama um repository.

E o repository acessa o banco de dados.

Temos algo parecido com:

controller.

service.

repository.

banco de dados.

Agora imagine que o repository lance uma exception.

Se o repository não possuir um `catch` compatível, a exception sobe para o service.

Se o service também não tratar, ela sobe para o controller.

Se o controller não tratar, ela pode chegar ao próprio framework.

Esse processo é chamado de exception propagation.

A exception percorre a pilha de chamadas procurando algum ponto que saiba tratá-la.

Esse conceito é essencial porque nos mostra que não precisamos capturar exceptions em todos os métodos.

Na verdade, muitas vezes não devemos.

Uma exception deve normalmente ser tratada no nível que possui informações suficientes para tomar uma decisão útil.

---

# Parte 6 — Throw e throws

As palavras `throw` e `throws` parecem semelhantes, mas possuem responsabilidades diferentes.

`throw` é uma ação.

Ele efetivamente lança uma exception.

Por exemplo:

se o saldo for menor que o valor da operação, podemos fazer:

`throw new SaldoInsuficienteException`.

Já `throws` aparece na assinatura do método.

Ele declara que determinado método pode propagar uma exception.

Portanto, uma forma simples de memorizar é:

`throw` lança.

`throws` declara.

---

# Parte 7 — Try, catch e finally

O bloco `try` contém o código que pode gerar uma exception.

O bloco `catch` define como uma determinada exception será tratada.

O bloco `finally` contém código que normalmente deve ser executado ao final da operação, independentemente de sucesso ou falha.

Historicamente, `finally` era muito utilizado para fechar recursos.

Por exemplo:

fechar arquivos.

fechar conexões.

ou liberar outros recursos externos.

O problema é que esse tipo de gerenciamento manual de recursos pode ser perigoso.

Principalmente porque o próprio processo de fechamento pode lançar uma nova exception.

E isso nos leva a um caso importante.

---

# Parte 8 — Exception no try e exception no finally

Imagine que o bloco `try` lance uma exception.

Depois disso, o bloco `finally` também lança outra exception.

Nesse cenário, a exception lançada no `finally` pode substituir a exception original.

Isso é muito ruim.

Porque a falha real pode ter ocorrido no processamento principal.

Mas o desenvolvedor acaba enxergando apenas uma falha secundária que aconteceu durante a limpeza.

Esse comportamento dificulta bastante o debugging.

Essa é uma das razões pelas quais o Java introduziu `try-with-resources`.

---

# Parte 9 — Por que retornar dentro de finally é perigoso

Outro problema clássico é utilizar `return` dentro de `finally`.

Imagine que o bloco `try` queira retornar o valor um.

Depois, o bloco `finally` retorna o valor dois.

O valor efetivamente retornado será o do `finally`.

Pior ainda.

Uma exception lançada no `try` pode ser completamente escondida por um `return` dentro de `finally`.

Por isso, `return` dentro de `finally` é considerado uma prática perigosa.

O papel de `finally` deveria estar muito mais relacionado à limpeza de recursos do que à lógica principal do método.

---

# Parte 10 — Try-with-resources

`try-with-resources` é a estrutura moderna para gerenciar recursos automaticamente.

Ela pode ser utilizada quando o recurso implementa `AutoCloseable`.

A ideia é simples.

O recurso é declarado dentro do `try`.

Quando o bloco termina, o Java chama automaticamente o método `close`.

Isso reduz a quantidade de código.

Evita esquecimentos.

E melhora o tratamento de exceptions durante o fechamento dos recursos.

Essa estrutura é muito utilizada com:

arquivos;

streams;

conexões JDBC;

statements;

result sets;

e outros recursos que precisam ser fechados.

---

# Parte 11 — Suppressed exceptions

Agora imagine o seguinte cenário.

A lógica principal lança uma exception.

Depois, durante o fechamento do recurso, o método `close` também lança outra exception.

Qual das duas é mais importante?

Normalmente, a exception da lógica principal.

Por isso, com `try-with-resources`, o Java preserva a exception original como principal.

E a exception que ocorreu durante o fechamento é armazenada como uma suppressed exception.

Em português, podemos pensar como uma exception secundária preservada.

Ela não substitui a causa principal.

Mas também não é perdida.

Essa abordagem melhora muito a capacidade de diagnóstico.

---

# Parte 12 — Ordem de fechamento dos recursos

Quando existem vários recursos em um `try-with-resources`, eles são fechados na ordem inversa da declaração.

Imagine:

primeiro declaramos a conexão.

Depois declaramos o statement.

Depois declaramos o result set.

No fechamento, o Java faz o contrário.

Primeiro fecha o result set.

Depois o statement.

E por último a conexão.

Isso faz sentido porque os recursos geralmente possuem uma relação de dependência.

O recurso mais interno depende do recurso anterior.

Então ele deve ser encerrado primeiro.

---

# Parte 13 — Multi-catch

Java permite capturar diferentes tipos de exceptions utilizando o mesmo bloco de tratamento.

Por exemplo:

`IOException` ou `SQLException`.

Se as duas exceptions precisam exatamente do mesmo tratamento, podemos colocá-las no mesmo `catch`.

Isso é chamado de multi-catch.

Mas existem limitações.

Não podemos colocar no mesmo multi-catch uma classe e uma subclasse dela.

Por exemplo, não faria sentido capturar ao mesmo tempo `Exception` e `IOException` no mesmo bloco.

Porque `IOException` já é uma `Exception`.

---

# Parte 14 — A ordem dos catches

A ordem dos blocos `catch` importa.

As exceptions mais específicas precisam aparecer antes das exceptions mais genéricas.

Imagine que capturamos primeiro `Exception`.

Depois tentamos capturar `IOException`.

Isso não funciona.

Porque `Exception` já capturaria também uma `IOException`.

Então o bloco específico nunca seria executado.

Por isso, o padrão correto é:

primeiro as exceptions específicas.

Depois as mais genéricas.

---

# Parte 15 — Custom exceptions de negócio

Uma custom exception é útil quando queremos expressar significado.

Compare duas abordagens.

Primeira abordagem:

lançar `RuntimeException` com a mensagem `erro`.

Segunda abordagem:

lançar `SaldoInsuficienteException`.

A segunda opção comunica muito mais.

Ela faz parte da linguagem do domínio.

Outros exemplos podem ser:

`PedidoNaoPodeSerCanceladoException`.

`EstoqueInsuficienteException`.

`ContaBloqueadaException`.

`LimiteDiarioExcedidoException`.

Exceptions de domínio são úteis porque tornam o código mais expressivo.

Mas isso não significa que devemos criar uma exception para cada `if`.

A exception deve representar uma condição importante do domínio.

---

# Parte 16 — Exception de domínio e exception técnica

Essa separação é extremamente importante em arquitetura.

Uma exception de domínio representa um problema relacionado ao negócio.

Por exemplo:

saldo insuficiente.

pedido já cancelado.

limite diário excedido.

Uma exception técnica representa um problema de infraestrutura ou implementação.

Por exemplo:

falha de banco.

falha de rede.

timeout.

erro de leitura de arquivo.

ou indisponibilidade de algum serviço externo.

Essa distinção ajuda a manter cada camada com sua responsabilidade correta.

Um controller não deveria precisar conhecer uma `PSQLException`.

Da mesma forma, uma entidade de domínio não deveria conhecer detalhes de JDBC.

---

# Parte 17 — Tradução de exceptions

Às vezes precisamos transformar uma exception técnica em uma exception mais significativa.

Imagine que o banco gere uma violação de constraint de unicidade.

Tecnicamente, isso pode aparecer como uma exception de persistência.

Mas para o domínio, o significado real pode ser:

email já cadastrado.

Nesse caso, pode fazer sentido traduzir a exception técnica para algo como:

`EmailJaCadastradoException`.

Essa tradução reduz o acoplamento.

A camada superior deixa de depender do detalhe técnico do banco.

Mas existe uma regra importante.

Só devemos traduzir quando conseguimos adicionar semântica.

Capturar uma `SQLException` e lançar simplesmente uma `RuntimeException` sem nenhum significado adicional normalmente não melhora o design.

Também é importante preservar a causa original.

Ou seja, a nova exception deve manter referência para a exception anterior.

Isso ajuda muito no diagnóstico.

---

# Parte 18 — O problema do catch genérico

Um dos maiores anti-padrões é capturar `Exception` em todos os métodos.

Por exemplo:

tentar executar alguma lógica;

capturar qualquer `Exception`;

escrever um log genérico;

e continuar normalmente.

Isso pode esconder bugs.

Pode esconder falhas de infraestrutura.

Pode eliminar informações importantes.

E pode deixar a aplicação funcionando em um estado incorreto.

`catch Exception` não é proibido.

Ele pode ser útil em pontos específicos.

Um exemplo clássico é o handler global de uma API.

Ali podemos ter um último fallback.

Se nenhuma exception conhecida foi tratada, capturamos `Exception` e retornamos `500 Internal Server Error`.

Mas esse tipo de fallback não deve ser confundido com uma estratégia de tratamento genérico em todas as camadas.

---

# Parte 19 — Por que printStackTrace não é estratégia de produção

`printStackTrace` simplesmente escreve a stack trace.

Isso pode ajudar durante um teste rápido.

Mas não é suficiente em produção.

Em produção queremos logs estruturados.

Queremos contexto.

Queremos saber:

qual operação falhou;

qual identificador estava sendo processado;

qual serviço estava envolvido;

qual era o trace ID;

qual era o correlation ID;

e qual foi a exception completa.

Além disso, também precisamos decidir o comportamento.

A exception deve ser propagada?

Deve haver retry?

Deve haver fallback?

Deve gerar métrica?

Deve gerar alerta?

Ou deve apenas resultar em uma resposta de negócio?

Logging e tratamento são responsabilidades relacionadas, mas diferentes.

---

# Parte 20 — Onde uma exception deve ser tratada

Uma boa regra é:

trate a exception no nível que possui contexto suficiente para tomar uma decisão útil.

Se uma chamada externa sofre timeout e existe um cache adequado, talvez o service possa tratar localmente e utilizar um fallback.

Se ocorre uma falha transitória e a operação é idempotente, talvez possamos executar retry.

Mas se a camada não consegue fazer nada útil, normalmente é melhor deixar a exception propagar.

Capturar apenas para logar e relançar pode gerar outro problema.

Logs duplicados.

O repository loga.

O service loga.

O controller loga.

E o handler global também loga.

No final, uma única falha gera quatro stack traces.

Isso aumenta ruído e dificulta observabilidade.

---

# Parte 21 — Exceptions como controle de fluxo

Exceptions não deveriam substituir o fluxo normal da aplicação.

Imagine que uma condição acontece milhares de vezes e faz parte do comportamento esperado.

Nesse caso, talvez seja melhor utilizar:

um `if`;

um `Optional`;

um objeto de resultado;

ou uma máquina de estados.

Criar exceptions possui custo.

Principalmente devido à geração da stack trace.

Mas o problema principal nem sempre é performance.

O problema é legibilidade.

Exceptions deveriam representar falhas ou condições excepcionais da operação.

Não simplesmente funcionar como um `goto` sofisticado.

---

# Parte 22 — Exceptions no Spring

Agora entramos em Spring.

No Spring MVC, podemos criar métodos anotados com `@ExceptionHandler`.

Um `@ExceptionHandler` declarado diretamente dentro de um controller é utilizado para tratar exceptions relacionadas àquele controller.

Isso pode ser útil quando o tratamento é realmente específico.

Mas em uma aplicação com dezenas de controllers, repetir handlers gera muito código duplicado.

Por isso, normalmente utilizamos um tratamento global.

---

# Parte 23 — ControllerAdvice e RestControllerAdvice

`@ControllerAdvice` permite aplicar comportamentos globalmente aos controllers.

Já `@RestControllerAdvice` é especialmente conveniente para APIs REST.

Ele combina a ideia de advice global com respostas escritas diretamente no corpo HTTP.

Na prática, para APIs REST, é muito comum criar uma classe chamada algo como:

`GlobalExceptionHandler`.

E anotá-la com `@RestControllerAdvice`.

Dentro dela, definimos diferentes métodos com `@ExceptionHandler`.

Cada método traduz uma determinada exception para uma resposta HTTP.

---

# Parte 24 — Separação entre domínio e HTTP

Essa separação é muito importante.

Imagine que o domínio lance:

`ClienteNaoEncontradoException`.

Essa exception pertence ao domínio ou à aplicação.

Ela não deveria saber que HTTP possui o status `404`.

Por quê?

Porque o domínio não deveria depender do protocolo HTTP.

A tradução acontece na borda.

O `RestControllerAdvice` recebe a `ClienteNaoEncontradoException`.

E decide transformá-la em:

`404 Not Found`.

Essa arquitetura mantém responsabilidades bem separadas.

O domínio fala a linguagem do negócio.

O adapter HTTP fala a linguagem da web.

---

# Parte 25 — ProblemDetail

Spring possui uma classe chamada `ProblemDetail`.

Ela serve para representar respostas de erro de forma padronizada.

Uma resposta de erro pode possuir informações como:

tipo do erro;

título;

status HTTP;

descrição;

e identificação da requisição.

Além disso, podemos adicionar propriedades customizadas.

Uma propriedade especialmente útil é um código estável de erro.

Por exemplo:

`CUSTOMER_NOT_FOUND`.

Ou:

`INSUFFICIENT_BALANCE`.

Isso é muito melhor do que fazer clientes dependerem do texto da mensagem.

Textos podem mudar.

Podem ser traduzidos.

Podem sofrer pequenos ajustes.

Um código de erro deve ser estável.

---

# Parte 26 — Status HTTP

Agora vamos revisar os principais códigos de erro.

`400 Bad Request`.

Normalmente utilizado quando a requisição é inválida ou malformada.

`401 Unauthorized`.

Apesar do nome, normalmente representa ausência ou falha de autenticação.

Por exemplo, token inexistente ou inválido.

`403 Forbidden`.

O usuário está autenticado, mas não possui permissão para executar a operação.

`404 Not Found`.

O recurso solicitado não existe.

`409 Conflict`.

Existe conflito com o estado atual do sistema.

Por exemplo, tentar cadastrar um email que já está cadastrado.

`422 Unprocessable Content`.

A requisição foi compreendida, mas existe uma falha semântica ou de regra que impede o processamento.

E `500 Internal Server Error`.

Esse status deve representar uma falha inesperada do servidor.

Um ponto importante é que nem toda regra de negócio possui um único status universalmente correto.

O importante é possuir uma convenção coerente e consistente para a API.

---

# Parte 27 — Validação versus regra de negócio

Validação de entrada e regra de negócio não são exatamente a mesma coisa.

Validação pode significar:

campo obrigatório ausente;

email com formato inválido;

tamanho máximo excedido;

valor numérico fora de um limite básico.

Regra de negócio pode significar:

saldo insuficiente;

pedido não pode mais ser cancelado;

cliente atingiu limite diário;

produto não está disponível naquele estado.

A primeira categoria normalmente está muito ligada à estrutura e semântica da entrada.

A segunda depende do estado do domínio.

Dependendo da convenção, validações podem resultar em `400` ou `422`.

Já regras de negócio podem resultar em `409`, `422` ou outros códigos coerentes com a situação.

O importante é evitar o antipadrão:

todo erro do cliente vira `400`.

Ou pior:

todo erro da aplicação vira `500`.

---

# Parte 28 — Segurança nas respostas de erro

Nunca devemos expor detalhes internos desnecessários ao cliente.

Isso inclui:

stack traces;

nomes de classes internas;

nomes de tabelas;

SQL;

paths do servidor;

credenciais;

tokens;

senhas;

e dados pessoais.

Imagine retornar ao cliente uma mensagem contendo uma query SQL completa.

Além de ser tecnicamente desnecessário, isso pode revelar informações importantes para um atacante.

A regra é simples.

Logs internos podem possuir detalhes técnicos necessários para investigação.

A resposta externa deve possuir apenas informações seguras e úteis para o consumidor da API.

---

# Parte 29 — Logging e observabilidade

Um bom tratamento de exceptions não termina em retornar o status HTTP correto.

Também precisamos garantir observabilidade.

Uma falha inesperada deveria possuir contexto suficiente para investigação.

Podemos querer registrar:

nome do serviço;

endpoint;

trace ID;

correlation ID;

identificador da entidade;

tipo da exception;

stack trace;

tempo da operação;

e dependência envolvida.

Isso permite correlacionar uma resposta `500` com um trace distribuído.

Depois conseguimos seguir o fluxo.

API.

Service.

Banco.

Kafka.

Outro microsserviço.

Ou serviço externo.

Também podemos gerar métricas.

Por exemplo:

quantidade de erros `500`.

taxa de erro por endpoint.

erros por dependência.

timeouts.

falhas de banco.

Tudo isso pode alimentar dashboards e alertas.

---

# Parte 30 — Nem toda exception deve ser ERROR

Outro ponto importante para um Tech Lead.

Nem toda exception deveria gerar um log no nível `ERROR`.

Imagine uma API que consulta um cliente inexistente.

O domínio lança `CustomerNotFoundException`.

O sistema retorna `404`.

Isso pode ser uma condição perfeitamente esperada.

Gerar um stack trace em `ERROR` para cada `404` pode produzir muito ruído.

Já uma `NullPointerException` inesperada em produção é diferente.

Essa provavelmente merece:

log em `ERROR`;

stack trace;

trace ID;

métrica;

e talvez alerta.

Portanto, observabilidade também precisa refletir a semântica da falha.

---

# Parte 31 — Como eu estruturaria uma API Spring

Em uma aplicação Spring, eu pensaria em quatro níveis.

Primeiro, o domínio.

O domínio possui exceptions semanticamente relevantes.

Por exemplo:

`SaldoInsuficienteException`.

Segundo, a aplicação.

Ela coordena casos de uso e pode deixar exceptions propagarem ou traduzi-las quando necessário.

Terceiro, a infraestrutura.

Ela pode gerar exceptions técnicas.

Por exemplo:

falhas de banco;

timeouts;

erros de mensageria.

Quarto, o adapter HTTP.

Ele traduz o resultado para o protocolo HTTP.

O fluxo pode ser:

banco gera uma falha técnica.

repository entende o significado.

a aplicação traduz quando necessário.

o domínio possui uma exception significativa.

o `RestControllerAdvice` transforma essa exception em HTTP.

Isso mantém cada camada com seu próprio vocabulário.

---

# Parte 32 — Exemplo completo de fluxo

Imagine uma transferência bancária.

O cliente chama a API.

A requisição chega ao controller.

O controller chama o service.

O service consulta a conta.

O saldo é cem reais.

O cliente tenta transferir quinhentos reais.

O domínio detecta a violação.

Então lança:

`SaldoInsuficienteException`.

Essa exception propaga até o `RestControllerAdvice`.

O advice identifica a exception.

E transforma a resposta em algo como:

status `422`.

Título:

saldo insuficiente.

Código:

`INSUFFICIENT_BALANCE`.

O cliente recebe apenas os detalhes necessários.

Internamente, essa situação talvez nem precise ser logada como `ERROR`, porque é uma regra de negócio esperada.

Agora imagine outro cenário.

Durante a mesma transferência, ocorre uma `NullPointerException` inesperada.

Essa exception não possui handler específico.

Então ela chega ao handler genérico.

O sistema retorna:

`500 Internal Server Error`.

Para o cliente, a mensagem é genérica e segura.

Internamente, o sistema registra:

stack trace;

trace ID;

endpoint;

identificador da operação;

e demais informações de observabilidade.

Essa diferença é fundamental.

---

# Parte 33 — Os principais anti-padrões

Vamos consolidar os erros mais comuns.

Primeiro.

Capturar `Exception` em todos os métodos.

Segundo.

Capturar uma exception e ignorá-la.

Terceiro.

Utilizar apenas `printStackTrace`.

Quarto.

Logar e relançar a mesma exception em todas as camadas.

Quinto.

Retornar `500` para qualquer problema.

Sexto.

Expor stack trace ao cliente.

Sétimo.

Utilizar exceptions técnicas diretamente nas camadas de domínio.

Oitavo.

Criar custom exceptions sem significado real.

Nono.

Utilizar exception para fluxo normal.

Décimo.

Utilizar `return` dentro de `finally`.

Esses pontos aparecem com frequência em revisões de código e entrevistas técnicas.

---

# Parte 34 — Como pensar como Tech Lead

Como Tech Lead, a pergunta não é apenas:

qual exception devo capturar?

A pergunta é maior.

O que essa exception significa?

Qual camada deveria conhecê-la?

Essa falha é de domínio ou técnica?

Existe estratégia local de recuperação?

Existe fallback?

Existe retry?

A operação é idempotente?

Essa exception deveria gerar rollback?

Qual status HTTP representa melhor a situação?

O cliente precisa conhecer essa informação?

Essa falha deve aparecer como `ERROR`?

Precisamos de métrica?

Precisamos de alerta?

Precisamos preservar a causa original?

Existe risco de vazamento de informações?

Esse conjunto de perguntas transforma tratamento de exceptions em uma decisão arquitetural.

---

# Parte 35 — Exceptions e transações no Spring

Existe um detalhe importante quando trabalhamos com `@Transactional`.

Por padrão, o Spring normalmente executa rollback quando ocorre uma `RuntimeException` ou um `Error`.

Checked exceptions não geram rollback automaticamente por padrão.

Isso pode surpreender desenvolvedores.

Imagine um método transacional.

Ele altera o banco.

Depois lança uma checked exception.

Se nenhuma configuração adicional existir, a transação pode ser commitada.

Por isso, quando utilizamos exceptions customizadas com transações, precisamos entender o comportamento de rollback.

Podemos configurar explicitamente regras como `rollbackFor`.

Mas essa decisão deve ser consciente.

Não devemos escolher checked ou unchecked exception apenas por causa do rollback.

Primeiro modelamos corretamente a semântica.

Depois configuramos a transação conforme a necessidade.

---

# Parte 36 — Exceptions e retries

Também existe uma relação importante entre exceptions e resiliência.

Nem toda exception deve gerar retry.

Se recebemos:

erro de validação;

saldo insuficiente;

pedido inválido;

ou autenticação inválida;

repetir a operação provavelmente não resolverá nada.

Agora imagine:

timeout temporário;

falha transitória de rede;

indisponibilidade curta;

ou lock otimista.

Dependendo do contexto, retry pode fazer sentido.

Mas mesmo nesses casos precisamos considerar:

idempotência;

backoff;

limite de tentativas;

e risco de sobrecarregar uma dependência que já está falhando.

Ou seja, a classe da exception pode ajudar a orientar políticas de resiliência.

---

# Parte 37 — Exceptions e arquitetura limpa

Em uma arquitetura limpa, hexagonal ou baseada em ports and adapters, evitamos deixar exceptions de infraestrutura contaminarem o domínio.

Imagine que hoje utilizamos PostgreSQL.

A camada de banco lança uma exception específica do PostgreSQL.

Se essa exception chegar até o controller, criamos acoplamento.

Agora imagine que amanhã migramos para outro banco.

Camadas superiores podem precisar mudar.

Uma alternativa melhor é converter detalhes técnicos em abstrações mais estáveis.

A infraestrutura conhece PostgreSQL.

A aplicação conhece uma abstração.

O domínio conhece regras de negócio.

O adapter HTTP conhece status HTTP.

Cada camada traduz apenas o necessário.

---

# Parte 38 — O princípio mais importante

Se eu precisasse resumir toda a aula em uma frase, seria esta:

uma exception deve carregar significado e ser tratada no ponto da aplicação que possui contexto suficiente para tomar uma decisão correta.

Não capturamos cedo demais.

Não propagamos detalhes técnicos demais.

Não escondemos a falha.

Não transformamos tudo em `500`.

E não misturamos domínio, infraestrutura e protocolo HTTP.

---

# Resumo final

Vamos revisar.

`Throwable` é a raiz da hierarquia.

Abaixo dele temos `Error` e `Exception`.

`RuntimeException` representa o principal grupo de unchecked exceptions.

Checked exceptions são verificadas pelo compilador.

Unchecked exceptions não exigem tratamento explícito.

`throw` lança uma exception.

`throws` declara que um método pode propagá-la.

Exceptions propagam pela call stack até encontrarem um handler compatível.

`try-with-resources` deve ser preferido para recursos `AutoCloseable`.

Suppressed exceptions preservam falhas secundárias durante o fechamento.

Exceptions de domínio representam regras de negócio.

Exceptions técnicas representam problemas de infraestrutura.

Devemos tratar exceptions no nível que consiga tomar uma decisão útil.

No Spring, `@RestControllerAdvice` centraliza o tratamento das APIs REST.

`ProblemDetail` ajuda a padronizar respostas de erro.

Os principais status são:

`400` para requisição inválida.

`401` para falha de autenticação.

`403` para falta de autorização.

`404` para recurso inexistente.

`409` para conflito.

`422` para conteúdo semanticamente inválido ou determinadas regras de negócio.

E `500` para falhas inesperadas.

Nunca devemos expor stack traces ou detalhes internos ao cliente.

E, em produção, tratamento de exceptions precisa estar conectado a logging, tracing, métricas e observabilidade.

---

# Resposta de entrevista

Se um entrevistador perguntasse:

como você trata exceptions em uma aplicação Java com Spring?

Uma resposta forte seria:

“Eu trato exceptions como parte do design da aplicação. Primeiro separo exceptions de domínio de exceptions técnicas. Exceptions de domínio representam situações semanticamente relevantes, enquanto exceptions técnicas representam falhas de infraestrutura. Evito capturar exceptions indiscriminadamente em todas as camadas e deixo a propagação acontecer até um ponto que realmente tenha contexto para recuperar, traduzir ou responder à falha.

Em aplicações Spring, normalmente centralizo a tradução para HTTP utilizando `RestControllerAdvice`. O domínio não conhece status HTTP. A camada HTTP transforma exceptions em respostas coerentes, usando `ProblemDetail` e códigos de erro estáveis.

Também diferencio validação, conflito, recurso inexistente e falha inesperada. Nem tudo vira `500`. Além disso, para erros inesperados, considero logging estruturado, trace ID, métricas e observabilidade. E evito expor stack traces ou detalhes internos ao cliente.

Por fim, quando existe tradução entre exceptions técnicas e exceptions da aplicação, preservo a causa original e só realizo essa tradução quando ela realmente adiciona significado.”

Essa resposta demonstra conhecimento de Java, Spring, HTTP, arquitetura, segurança e operação em produção.
