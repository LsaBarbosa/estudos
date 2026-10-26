

# Como funciona Injeção de Dependência?

> Injeção de Dependência é um princípio no qual um objeto recebe suas dependências de um componente externo, em vez de criá-las diretamente.
>
> No Spring, isso é feito pelo container de IoC, que instancia os objetos, resolve suas dependências e gerencia seu ciclo de vida.
>
> A forma recomendada é a injeção via construtor, porque torna as dependências explícitas, facilita testes e garante que o objeto seja criado em um estado válido.
>
> Esse mecanismo reduz o acoplamento entre as classes e melhora a manutenção e a testabilidade da aplicação.


---

# O que é um Bean?

> Um Bean é um objeto gerenciado pelo container do Spring.
>
> O Spring é responsável por criar a instância, configurar suas dependências, controlar seu ciclo de vida e disponibilizá-la para outras partes da aplicação.
>
> Um Bean pode ser criado automaticamente por anotações como `@Component`, `@Service`, `@Repository` e `@Controller`, ou manualmente por métodos anotados com `@Bean`.
>
> O escopo padrão é Singleton, ou seja, normalmente existe apenas uma instância daquele Bean durante toda a execução da aplicação.


---

# O que faz o Spring Boot?

> O Spring Boot simplifica o desenvolvimento de aplicações Spring por meio de configuração automática, gerenciamento de dependências e servidor embarcado.
>
> Em vez de configurar manualmente dezenas de componentes, o Spring Boot utiliza convenções e detecta automaticamente as bibliotecas presentes no projeto para configurar a aplicação.
>
> Ele também oferece recursos como Actuator, configuração externa, integração com métricas, logging e suporte facilitado para microsserviços.
>
> O objetivo é reduzir configuração repetitiva e permitir que o desenvolvedor foque na lógica de negócio.

---

# Como funciona Auto Configuration?

> A Auto Configuration é um mecanismo do Spring Boot que configura automaticamente diversos componentes da aplicação com base nas dependências presentes no projeto e nas propriedades definidas.
>
> Por exemplo, se o projeto possui Spring Web, o Spring configura automaticamente um servidor web e os componentes necessários para APIs REST. Se detectar Spring Data JPA e um banco configurado, cria automaticamente o DataSource, EntityManager e TransactionManager.
>
> Essas configurações utilizam classes condicionais, como `@ConditionalOnClass` e `@ConditionalOnMissingBean`, permitindo que configurações automáticas sejam substituídas quando necessário.

---

# Qual a diferença entre @Component, @Service e @Repository?

> As três anotações registram uma classe como Bean do Spring. A principal diferença está na responsabilidade semântica de cada uma.
>
> `@Component` é uma anotação genérica para qualquer componente gerenciado pelo Spring.
>
> `@Service` representa a camada de negócio, tornando a intenção da classe mais clara.
>
> `@Repository` representa a camada de acesso a dados e oferece um benefício adicional: traduz automaticamente exceções específicas do banco para exceções da hierarquia `DataAccessException` do Spring.
>
> Embora tecnicamente tenham comportamento semelhante, utilizar cada anotação na camada correta melhora a organização e a legibilidade da arquitetura.

---

# Qual a diferença entre @Controller e @RestController?

> `@Controller` é utilizado principalmente em aplicações MVC que retornam páginas HTML.
>
> Já `@RestController` é uma combinação de `@Controller` com `@ResponseBody`, indicando que os métodos retornam diretamente o corpo da resposta HTTP, normalmente em JSON.
>
> Em aplicações que expõem APIs REST, `@RestController` é a opção mais utilizada.


# Como funciona @Transactional?

> A anotação `@Transactional` define que um método deve ser executado dentro de uma transação.
>
> Quando o método inicia, o Spring abre uma transação. Se ele terminar com sucesso, realiza o commit. Caso ocorra uma exceção que provoque rollback, as alterações são desfeitas para preservar a consistência dos dados.
>
> O Spring implementa esse comportamento utilizando proxies, que interceptam a chamada ao método e controlam automaticamente o ciclo de vida da transação.
>
> Também é possível configurar aspectos como propagação, nível de isolamento, timeout e quais exceções devem provocar rollback.

---

# Como tratar exceções?

> Eu centralizaria o tratamento utilizando `@RestControllerAdvice` com métodos anotados com `@ExceptionHandler`.
>
> Dessa forma, as exceções são convertidas em respostas HTTP padronizadas, mantendo os controllers focados apenas na lógica de negócio.
>
> Também diferenciaria exceções de negócio das exceções inesperadas. Para erros conhecidos retornaria códigos como 400, 404 ou 409, enquanto erros internos seriam tratados como 500, registrando logs com contexto suficiente para investigação, mas sem expor detalhes sensíveis ao cliente.
>
> Sempre buscaria retornar uma estrutura consistente de erro contendo informações como timestamp, código HTTP, mensagem e caminho da requisição.

