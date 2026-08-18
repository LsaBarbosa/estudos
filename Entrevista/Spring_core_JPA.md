# Spring Core e JPA

Nesta aula, vamos resumir **Spring Core e JPA** com foco no que realmente importa para uma entrevista técnica.

---

## Spring Core

Spring Core gira principalmente em torno de **Inversion of Control** e **Dependency Injection**.

A ideia é simples.

Em vez de cada classe criar suas próprias dependências com `new`, ela apenas declara aquilo de que precisa.

O Spring fica responsável por **criar, configurar e conectar esses objetos**.

Esse gerenciamento acontece dentro do **IoC Container**, normalmente representado pelo `ApplicationContext`.

Os objetos gerenciados pelo Spring são chamados de **Beans**.

Um Bean é apenas um objeto Java cujo ciclo de vida é controlado pelo Spring.

Isso permite que o framework aplique recursos como:

- injeção de dependência;
- lifecycle;
- scopes;
- AOP;
- cache;
- transações.

---

## Component Scanning e Beans

O Spring encontra muitos desses Beans através do **component scanning**.

Classes anotadas com:

- `Component`
- `Service`
- `Repository`
- `Controller`
- `RestController`

podem ser registradas automaticamente.

`Component` é genérico.

`Service` representa regra de negócio.

`Repository` representa persistência.

`Controller` trabalha com MVC.

E `RestController` normalmente expõe APIs REST.

Também podemos registrar Beans explicitamente com `Bean` dentro de uma classe de configuração.

Isso é útil principalmente para **bibliotecas externas** ou quando precisamos controlar exatamente como o objeto será criado.

---

## Injeção de Dependências

Para injeção de dependências, **constructor injection** normalmente é a abordagem preferida.

Ela deixa as dependências explícitas, permite atributos finais e facilita testes.

Se existirem múltiplos Beans da mesma interface, podemos usar:

- `Primary` para definir uma opção padrão;
- `Qualifier` para escolher uma implementação específica.

---

## Scope dos Beans

Outro ponto importante é o **scope**.

O padrão é **singleton**.

Isso significa que a mesma instância do Bean pode ser utilizada por várias requisições.

Por isso:

> **singleton não significa thread-safe.**

Services normalmente devem ser **stateless** e evitar estado mutável compartilhado.

---

## Spring Boot e Auto Configuration

O Spring Boot adiciona **Auto Configuration**.

Ele observa:

- bibliotecas presentes no classpath;
- properties;
- Beans já existentes;

para configurar automaticamente parte da aplicação.

Os **starters** trazem conjuntos de dependências, e essas dependências ajudam a determinar quais configurações automáticas serão ativadas.

---

# JPA

Agora entrando em JPA.

**JPA é uma especificação de persistência.**

**Hibernate** é uma implementação muito utilizada dessa especificação.

**Spring Data JPA** adiciona abstrações por cima de JPA, como repositories.

---

## Persistence Context

O conceito mais importante em JPA é o **Persistence Context**.

Ele representa o conjunto de entidades que estão sendo gerenciadas.

O `EntityManager` é a principal interface utilizada para interagir com esse contexto.

Uma entidade pode estar em quatro estados principais.

### Transient

Objeto criado, mas ainda não gerenciado.

### Managed

Objeto controlado pelo Persistence Context.

### Detached

Já foi managed, mas não está mais sendo acompanhado.

### Removed

Está marcado para exclusão.

---

## Dirty Checking

Quando uma entidade está **managed**, o Hibernate acompanha suas alterações através de **dirty checking**.

Por isso, dentro de uma transação, podemos buscar uma entidade, alterar um atributo e terminar o método.

Normalmente não é necessário executar `save` apenas para gerar o update.

No **flush**, o Hibernate detecta a alteração e envia o SQL ao banco.

---

## Flush e Commit

**Flush e commit não são a mesma coisa.**

**Flush** sincroniza o Persistence Context com o banco dentro da transação.

**Commit** confirma definitivamente a transação.

---

## Carregamento de Relacionamentos

Outro tema essencial em JPA é carregamento de relacionamentos.

Temos `LAZY` e `EAGER`.

### `LAZY`

Carrega a associação quando necessário.

### `EAGER`

Exige que ela esteja disponível durante o carregamento.

Mas colocar tudo como `EAGER` não é solução para problemas de performance.

---

## Problema N mais um

O problema clássico é **N mais um**.

Buscamos cem pedidos com uma query e, ao acessar o cliente de cada pedido, executamos mais cem queries.

Resultado:

> **cento e uma queries.**

As principais ferramentas para resolver isso são:

- `JOIN FETCH`;
- `EntityGraph`;
- Batch fetching;
- Projections.

A estratégia correta depende do caso de uso.

Outro cuidado importante é **paginação com fetch de coleções**, porque os joins podem multiplicar linhas e prejudicar a paginação.

---

## Relacionamentos

Também precisamos entender relacionamentos.

Em relacionamentos bidirecionais existe um **owning side**, que controla a associação no banco.

`mappedBy` indica o lado inverso.

**Cascade** define quais operações JPA são propagadas entre entidades.

**Orphan removal** pode excluir uma entidade filha quando ela deixa de pertencer ao pai, quando existe realmente uma relação de ciclo de vida entre os dois.

---

## Diagnóstico de Performance

Por fim, em uma API JPA lenta, eu não mudaria tudo para `EAGER`.

Primeiro mediria.

- Quantas queries estão sendo executadas?
- Existe N mais um?
- A consulta está lenta ou existem queries demais?
- Estamos carregando entidades completas sem necessidade?
- A paginação está correta?
- O SQL possui bons índices?

Depois escolheria a estratégia adequada entre:

- `JOIN FETCH`;
- `EntityGraph`;
- batch fetching;
- projections;
- ajustes na própria consulta.

---

# Modelo mental final

O modelo mental final é este.

**Spring Core gerencia os componentes da aplicação.**

**JPA gerencia as entidades persistentes.**

O Spring cria e conecta **Beans**.

O JPA controla entidades dentro do **Persistence Context**.

**Dependency Injection** conecta componentes.

**Dirty checking** detecta alterações em entidades.

E, em nível de **Tech Lead**, o mais importante não é apenas saber usar as anotações.

É entender:

- lifecycle;
- proxies;
- scopes;
- Persistence Context;
- dirty checking;
- fetch strategies;
- o SQL que realmente está sendo executado.
