# Programação Orientada a Objetos em Java

<a id="indice"></a>

## Índice

- [1. Visão Geral](#visao-geral)
- [2. Classe, Objeto e Instância](#classe-objeto-instancia)
  - [2.1 Classe](#classe)
  - [2.2 Objeto](#objeto)
  - [2.3 Estado e Comportamento](#estado-comportamento)
  - [2.4 Construtores](#construtores)
- [3. Encapsulamento](#encapsulamento)
  - [3.1 Modificadores de Acesso](#modificadores-acesso)
  - [3.2 Getters e Setters](#getters-setters)
  - [3.3 Invariantes](#invariantes)
  - [3.4 Imutabilidade](#imutabilidade)
- [4. Abstração](#abstracao)
  - [4.1 Classe Abstrata](#classe-abstrata)
  - [4.2 Interface](#interface)
  - [4.3 Classe Abstrata vs Interface](#classe-abstrata-vs-interface)
- [5. Herança](#heranca)
  - [5.1 Relação IS-A](#is-a)
  - [5.2 Sobrescrita de Métodos](#override)
  - [5.3 super](#super)
  - [5.4 final em Herança](#final-heranca)
  - [5.5 Trade-offs da Herança](#tradeoffs-heranca)
- [6. Polimorfismo](#polimorfismo)
  - [6.1 Polimorfismo de Subtipo](#polimorfismo-subtipo)
  - [6.2 Dynamic Dispatch](#dynamic-dispatch)
  - [6.3 Upcasting e Downcasting](#casting)
  - [6.4 Sobrecarga vs Sobrescrita](#overload-vs-override)
- [7. Composição](#composicao)
  - [7.1 Relação HAS-A](#has-a)
  - [7.2 Composição vs Herança](#composicao-vs-heranca)
  - [7.3 Delegação](#delegacao)
- [8. Associação, Agregação e Composição](#relacionamentos)
  - [8.1 Associação](#associacao)
  - [8.2 Agregação](#agregacao)
  - [8.3 Composição](#composicao-forte)
- [9. Acoplamento e Coesão](#acoplamento-coesao)
  - [9.1 Acoplamento](#acoplamento)
  - [9.2 Coesão](#coesao)
- [10. Contratos entre Objetos](#contratos)
  - [10.1 equals](#equals)
  - [10.2 hashCode](#hashcode)
  - [10.3 toString](#tostring)
- [11. static, final e this](#static-final-this)
  - [11.1 static](#static)
  - [11.2 final](#final)
  - [11.3 this](#this)
- [12. Java Moderno e Modelagem OO](#java-moderno)
  - [12.1 Records](#records)
  - [12.2 Sealed Classes](#sealed-classes)
  - [12.3 Enums](#enums)
- [13. Princípios de Design relacionados a POO](#principios-design)
  - [13.1 Programar para abstrações](#programar-abstracoes)
  - [13.2 Favor composition over inheritance](#favor-composition)
  - [13.3 Tell, Don't Ask](#tell-dont-ask)
  - [13.4 Encapsular invariantes](#encapsular-invariantes)
- [14. Armadilhas comuns de entrevista](#armadilhas)
- [15. Cenários de Produção](#producao)
- [16. Resposta Completa de Entrevista](#resposta-completa)
- [17. Mapa Mental](#mapa-mental)
- [18. Resumo Rápido](#resumo-rapido)

---

<a id="visao-geral"></a>
# 1. Visão Geral

Programação Orientada a Objetos — **POO** — organiza o software em torno de **objetos que possuem estado e comportamento**.

Os quatro pilares clássicos são:

```text
POO
│
├── Encapsulamento
├── Abstração
├── Herança
└── Polimorfismo
```

Em Java, esses conceitos aparecem através de:

```text
classes
interfaces
objetos
métodos
modificadores de acesso
herança
composição
polimorfismo
```

O objetivo não é apenas "usar classes", mas criar modelos em que:

- responsabilidades estejam bem definidas;
- invariantes sejam protegidas;
- dependências sejam controladas;
- implementação possa evoluir sem quebrar consumidores;
- comportamento esteja próximo dos dados que governa.

### Resposta de entrevista

> Programação Orientada a Objetos é um paradigma que organiza o sistema em objetos com estado e comportamento. Em Java, os principais conceitos são encapsulamento, abstração, herança e polimorfismo. Na prática, eu uso POO para modelar responsabilidades, proteger invariantes e reduzir acoplamento, normalmente favorecendo abstrações e composição quando isso melhora a flexibilidade.

[↑ Voltar ao índice](#indice)

---

<a id="classe-objeto-instancia"></a>
# 2. Classe, Objeto e Instância

<a id="classe"></a>
## 2.1 Classe

Uma **classe** define estrutura e comportamento para objetos.

```java
public class Conta {

    private BigDecimal saldo;

    public void depositar(BigDecimal valor) {
        saldo = saldo.add(valor);
    }
}
```

A classe define:

- atributos;
- métodos;
- construtores;
- regras de acesso;
- invariantes.

### Regra mental

```text
Classe → modelo
Objeto → instância concreta desse modelo
```

---

<a id="objeto"></a>
## 2.2 Objeto

Um objeto possui:

```text
identidade
estado
comportamento
```

Exemplo:

```java
Conta conta = new Conta();
```

`conta` referencia uma instância da classe `Conta`.

---

<a id="estado-comportamento"></a>
## 2.3 Estado e Comportamento

Estado:

```java
private BigDecimal saldo;
```

Comportamento:

```java
public void sacar(BigDecimal valor) {
    ...
}
```

Um bom modelo OO evita expor estado de forma indiscriminada.

Em vez de:

```java
conta.setSaldo(conta.getSaldo().subtract(valor));
```

prefira comportamento que represente a regra do domínio:

```java
conta.sacar(valor);
```

Isso mantém a regra dentro do objeto responsável por ela.

---

<a id="construtores"></a>
## 2.4 Construtores

Construtores inicializam objetos.

```java
public Conta(String numero, BigDecimal saldoInicial) {
    this.numero = numero;
    this.saldo = saldoInicial;
}
```

Podem garantir que o objeto já seja criado em um estado válido.

### Exemplo

Ruim:

```java
Conta conta = new Conta();
conta.setNumero(null);
conta.setSaldo(new BigDecimal("-100"));
```

Melhor:

```java
Conta conta = new Conta(numero, saldoInicial);
```

com validação no construtor.

### Resposta de entrevista

> Uma classe define estrutura e comportamento; o objeto é uma instância concreta dessa classe. Eu procuro criar objetos já em estado válido e colocar comportamento próximo do estado que ele controla, evitando expor atributos sem necessidade.

[↑ Voltar ao índice](#indice)

---

<a id="encapsulamento"></a>
# 3. Encapsulamento

Encapsulamento consiste em **controlar acesso ao estado interno e expor uma API coerente para manipulação desse estado**.

Não significa apenas:

```java
private + getter + setter
```

O objetivo real é:

```text
proteger invariantes
esconder detalhes internos
controlar mudanças de estado
reduzir acoplamento
```

Exemplo:

```java
public class Conta {

    private BigDecimal saldo = BigDecimal.ZERO;

    public void sacar(BigDecimal valor) {
        if (valor.signum() <= 0) {
            throw new IllegalArgumentException("Valor inválido");
        }

        if (saldo.compareTo(valor) < 0) {
            throw new IllegalStateException("Saldo insuficiente");
        }

        saldo = saldo.subtract(valor);
    }

    public BigDecimal getSaldo() {
        return saldo;
    }
}
```

O consumidor não altera diretamente:

```java
saldo
```

Ele solicita uma operação:

```java
conta.sacar(valor);
```

---

<a id="modificadores-acesso"></a>
## 3.1 Modificadores de Acesso

| Modificador | Mesma classe | Mesmo package | Subclasse | Qualquer classe |
|---|---:|---:|---:|---:|
| `private` | Sim | Não | Não | Não |
| package-private | Sim | Sim | Depende do package | Não |
| `protected` | Sim | Sim | Sim | Não |
| `public` | Sim | Sim | Sim | Sim |

### Regra prática

> Use a menor visibilidade necessária.

Isso reduz a superfície pública da classe.

---

<a id="getters-setters"></a>
## 3.2 Getters e Setters

Ter campos `private` com getters e setters para tudo **não garante bom encapsulamento**.

Exemplo fraco:

```java
public void setStatus(Status status) {
    this.status = status;
}
```

Isso permite transições inválidas.

Melhor:

```java
public void aprovar() {
    if (status != Status.PENDENTE) {
        throw new IllegalStateException();
    }

    status = Status.APROVADO;
}
```

Agora o objeto controla a própria regra.

---

<a id="invariantes"></a>
## 3.3 Invariantes

Invariante é uma condição que deve permanecer verdadeira para o objeto ser válido.

Exemplo:

```text
saldo nunca pode ficar negativo
```

ou:

```text
pedido CANCELADO não pode ser APROVADO
```

Boa POO mantém essa regra dentro da abstração responsável.

---

<a id="imutabilidade"></a>
## 3.4 Imutabilidade

Objeto imutável não altera seu estado após criação.

Exemplo:

```java
public final class Dinheiro {

    private final BigDecimal valor;

    public Dinheiro(BigDecimal valor) {
        if (valor == null) {
            throw new IllegalArgumentException();
        }

        this.valor = valor;
    }

    public BigDecimal valor() {
        return valor;
    }
}
```

### Vantagens

- mais simples de raciocinar;
- facilita concorrência;
- evita estado compartilhado mutável;
- reduz efeitos colaterais.

### Trade-off

Pode gerar novos objetos em vez de atualizar o existente.

### Resposta de entrevista

> Encapsulamento não é apenas colocar atributos como private. É controlar como o estado pode mudar e proteger invariantes. Eu prefiro expor operações de negócio em vez de setters genéricos, porque isso reduz estados inválidos e acoplamento com a implementação interna.

[↑ Voltar ao índice](#indice)

---

<a id="abstracao"></a>
# 4. Abstração

Abstração expõe **o que um componente faz**, escondendo detalhes de **como ele faz**.

Exemplo:

```java
public interface PagamentoGateway {

    ResultadoPagamento pagar(Pagamento pagamento);
}
```

O consumidor depende do contrato:

```java
PagamentoGateway
```

e não precisa conhecer:

```text
HTTP
SDK externo
autenticação
timeout
serialização
```

---

<a id="classe-abstrata"></a>
## 4.1 Classe Abstrata

Uma classe abstrata pode possuir:

- estado;
- construtores;
- métodos concretos;
- métodos abstratos;
- visibilidade `protected`;
- lógica compartilhada.

Exemplo:

```java
public abstract class Funcionario {

    private final String nome;

    protected Funcionario(String nome) {
        this.nome = nome;
    }

    public String getNome() {
        return nome;
    }

    public abstract BigDecimal calcularBonus();
}
```

Não pode ser instanciada diretamente.

---

<a id="interface"></a>
## 4.2 Interface

Interface define um contrato.

```java
public interface Notificador {

    void enviar(Mensagem mensagem);
}
```

Implementações:

```java
public class NotificadorEmail implements Notificador {
    ...
}

public class NotificadorSms implements Notificador {
    ...
}
```

Interfaces modernas também podem possuir:

```java
default
static
private
```

methods.

### Uso típico

- definir capacidades;
- criar contratos;
- desacoplar consumidores de implementações;
- permitir múltiplas implementações.

---

<a id="classe-abstrata-vs-interface"></a>
## 4.3 Classe Abstrata vs Interface

| Classe Abstrata | Interface |
|---|---|
| Pode manter estado de instância | Normalmente representa contrato/capacidade |
| Pode ter construtor | Não possui construtor de instância |
| Herança única | Uma classe pode implementar várias interfaces |
| Pode compartilhar implementação e estado | Boa para desacoplamento |
| Relação de especialização mais forte | Contrato mais flexível |

### Regra prática

Use **interface** quando o principal objetivo é definir um contrato.

Use **classe abstrata** quando existe uma relação forte de especialização e comportamento/estado comum relevante.

### Resposta de entrevista

> Abstração significa expor a capacidade essencial e esconder detalhes de implementação. Em Java, interfaces são ótimas para contratos e desacoplamento. Classes abstratas fazem sentido quando subclasses compartilham estado ou comportamento base e existe uma relação de especialização clara.

[↑ Voltar ao índice](#indice)

---

<a id="heranca"></a>
# 5. Herança

Herança permite que uma classe derive comportamento e estrutura de outra.

```java
public class Gerente extends Funcionario {
    ...
}
```

---

<a id="is-a"></a>
## 5.1 Relação IS-A

Herança deve representar uma relação válida:

```text
Gerente IS-A Funcionario
```

Não use herança apenas para reaproveitar código.

Exemplo ruim:

```text
Pedido extends ArrayList<Item>
```

apenas porque Pedido contém itens.

O correto normalmente é:

```text
Pedido HAS-A List<Item>
```

---

<a id="override"></a>
## 5.2 Sobrescrita de Métodos

Override ocorre quando uma subclasse redefine um método herdado.

```java
@Override
public BigDecimal calcularBonus() {
    return salario.multiply(new BigDecimal("0.20"));
}
```

A assinatura deve ser compatível com o contrato do método pai.

### Use sempre

```java
@Override
```

porque o compilador valida a intenção.

---

<a id="super"></a>
## 5.3 super

`super` acessa membros da superclasse.

Exemplo:

```java
public Gerente(String nome) {
    super(nome);
}
```

Também pode chamar implementação do método pai:

```java
super.processar();
```

---

<a id="final-heranca"></a>
## 5.4 final em Herança

Classe `final`:

```java
public final class Dinheiro {
}
```

não pode ser herdada.

Método `final`:

```java
public final void validar() {
}
```

não pode ser sobrescrito.

---

<a id="tradeoffs-heranca"></a>
## 5.5 Trade-offs da Herança

### Vantagens

- reutilização de comportamento;
- polimorfismo;
- modelagem de especializações reais.

### Custos

- forte acoplamento com superclasse;
- hierarquias difíceis de evoluir;
- subclasses podem depender de detalhes do pai;
- alterações no pai podem impactar toda a árvore.

### Regra prática

> Herança deve representar substituição válida, não apenas compartilhamento de código.

### Resposta de entrevista

> Herança modela uma relação IS-A. Eu uso quando existe uma especialização real e a subclasse pode substituir a superclasse preservando seu contrato. Evito usar herança apenas para reaproveitar código, porque ela aumenta o acoplamento entre pai e filhos.

[↑ Voltar ao índice](#indice)

---

<a id="polimorfismo"></a>
# 6. Polimorfismo

Polimorfismo permite usar diferentes implementações através de uma mesma abstração.

```java
Notificador notificador = new NotificadorEmail();
```

Depois:

```java
notificador.enviar(mensagem);
```

O consumidor trabalha com:

```java
Notificador
```

e não com a implementação específica.

---

<a id="polimorfismo-subtipo"></a>
## 6.1 Polimorfismo de Subtipo

Exemplo:

```java
List<Notificador> notificadores = List.of(
    new NotificadorEmail(),
    new NotificadorSms()
);

for (Notificador notificador : notificadores) {
    notificador.enviar(mensagem);
}
```

Cada implementação possui comportamento próprio.

---

<a id="dynamic-dispatch"></a>
## 6.2 Dynamic Dispatch

Quando:

```java
Notificador n = new NotificadorEmail();
n.enviar(mensagem);
```

o método executado é determinado pelo **tipo real do objeto em runtime**.

```text
tipo da referência → Notificador
tipo do objeto     → NotificadorEmail
método executado   → NotificadorEmail.enviar()
```

---

<a id="casting"></a>
## 6.3 Upcasting e Downcasting

### Upcasting

```java
Notificador n = new NotificadorEmail();
```

É natural e seguro.

### Downcasting

```java
NotificadorEmail email = (NotificadorEmail) n;
```

É mais perigoso.

Pode gerar:

```text
ClassCastException
```

se o objeto não for daquele tipo.

Java moderno permite pattern matching:

```java
if (n instanceof NotificadorEmail email) {
    email.enviarComTemplate();
}
```

### Regra prática

> Muitos downcasts podem indicar uma abstração mal desenhada.

---

<a id="overload-vs-override"></a>
## 6.4 Sobrecarga vs Sobrescrita

### Overloading — Sobrecarga

Mesmo nome, parâmetros diferentes.

```java
enviar(String mensagem)

enviar(String mensagem, Prioridade prioridade)
```

Resolvido principalmente em **compile time**.

### Overriding — Sobrescrita

Subclasse redefine comportamento herdado.

```java
@Override
public void enviar(Mensagem mensagem) {
    ...
}
```

A implementação concreta é escolhida em **runtime**.

| Overload | Override |
|---|---|
| Mesmo nome | Mesmo contrato herdado |
| Parâmetros diferentes | Mesma assinatura compatível |
| Compile time | Runtime |
| Não exige herança | Exige relação de herança/implementação |

### Resposta de entrevista

> Polimorfismo permite tratar diferentes implementações através de um mesmo contrato. Em Java, quando uma referência da interface aponta para uma implementação concreta, o método sobrescrito é resolvido em runtime através de dynamic dispatch.

[↑ Voltar ao índice](#indice)

---

<a id="composicao"></a>
# 7. Composição

Composição constrói objetos a partir de outros objetos.

```java
public class Pedido {

    private final CalculadoraFrete calculadoraFrete;
    private final PagamentoGateway pagamentoGateway;
}
```

---

<a id="has-a"></a>
## 7.1 Relação HAS-A

```text
Pedido HAS-A PagamentoGateway
```

Diferente da herança:

```text
Gerente IS-A Funcionario
```

---

<a id="composicao-vs-heranca"></a>
## 7.2 Composição vs Herança

Com composição:

```java
public class CheckoutService {

    private final PagamentoGateway gateway;

    public CheckoutService(PagamentoGateway gateway) {
        this.gateway = gateway;
    }
}
```

A implementação pode ser trocada:

```java
new CheckoutService(new StripeGateway());
new CheckoutService(new PixGateway());
```

Sem mudar a classe principal.

### Vantagens

- menor acoplamento;
- maior flexibilidade;
- facilita testes;
- troca de implementação;
- evita hierarquias profundas.

---

<a id="delegacao"></a>
## 7.3 Delegação

Composição normalmente usa delegação.

```java
public Resultado pagar(Pagamento pagamento) {
    return gateway.pagar(pagamento);
}
```

O objeto delega uma responsabilidade a outro objeto especializado.

### Resposta de entrevista

> Composição representa relação HAS-A e permite montar comportamentos através de dependências. Eu normalmente prefiro composição quando quero reutilizar comportamento ou trocar implementações, porque ela reduz o acoplamento estrutural criado pela herança.

[↑ Voltar ao índice](#indice)

---

<a id="relacionamentos"></a>
# 8. Associação, Agregação e Composição

<a id="associacao"></a>
## 8.1 Associação

Um objeto conhece ou utiliza outro.

```text
Pedido ───── Cliente
```

Não implica necessariamente propriedade de ciclo de vida.

---

<a id="agregacao"></a>
## 8.2 Agregação

Representa relação todo-parte em que a parte pode existir independentemente.

Exemplo conceitual:

```text
Time ◇──── Jogador
```

O jogador pode continuar existindo sem o time.

---

<a id="composicao-forte"></a>
## 8.3 Composição

Relação todo-parte mais forte.

```text
Pedido ◆──── ItemPedido
```

`ItemPedido` normalmente só faz sentido dentro do `Pedido`.

### Observação prática

Em código Java, a diferença entre agregação e composição nem sempre aparece sintaticamente.

Ela é principalmente uma decisão de **modelagem e ciclo de vida**.

### Resposta de entrevista

> Associação representa relacionamento geral entre objetos. Agregação é uma relação todo-parte mais fraca, em que a parte pode existir sozinha. Composição é mais forte e normalmente implica que a parte pertence ao ciclo de vida do todo.

[↑ Voltar ao índice](#indice)

---

<a id="acoplamento-coesao"></a>
# 9. Acoplamento e Coesão

<a id="acoplamento"></a>
## 9.1 Acoplamento

Acoplamento mede quanto uma unidade depende de detalhes de outra.

Exemplo fortemente acoplado:

```java
public class PedidoService {

    private final StripeClient client = new StripeClient();
}
```

Agora `PedidoService` depende diretamente da implementação.

Melhor:

```java
public class PedidoService {

    private final PagamentoGateway gateway;

    public PedidoService(PagamentoGateway gateway) {
        this.gateway = gateway;
    }
}
```

---

<a id="coesao"></a>
## 9.2 Coesão

Coesão mede quão relacionadas são as responsabilidades dentro de uma classe.

Classe pouco coesa:

```text
ClienteService
├── cadastrar cliente
├── gerar PDF
├── enviar e-mail
├── calcular imposto
└── acessar FTP
```

Classe mais coesa:

```text
ClienteService
└── regras de cliente
```

### Objetivo

```text
baixo acoplamento
+
alta coesão
```

### Resposta de entrevista

> Eu procuro baixa dependência entre componentes e alta coesão dentro de cada classe. Baixo acoplamento facilita substituição e evolução; alta coesão mantém responsabilidades relacionadas no mesmo lugar.

[↑ Voltar ao índice](#indice)

---

<a id="contratos"></a>
# 10. Contratos entre Objetos

Todo objeto Java herda de:

```java
java.lang.Object
```

Métodos importantes:

```java
equals()
hashCode()
toString()
```

---

<a id="equals"></a>
## 10.1 equals

Define igualdade lógica.

Exemplo:

```java
cliente1.equals(cliente2)
```

pode significar que representam a mesma identidade de negócio.

Contrato:

- reflexivo;
- simétrico;
- transitivo;
- consistente;
- comparação com `null` retorna `false`.

---

<a id="hashcode"></a>
## 10.2 hashCode

Regra fundamental:

```text
se a.equals(b) == true
então
a.hashCode() == b.hashCode()
```

Importante para:

```java
HashMap
HashSet
```

### Atenção

Objetos usados como chave de `HashMap` devem evitar alterar campos usados por:

```java
equals()
hashCode()
```

enquanto estiverem armazenados no mapa.

---

<a id="tostring"></a>
## 10.3 toString

Fornece representação textual do objeto.

```java
@Override
public String toString() {
    return "Cliente{id=" + id + ", nome='" + nome + "'}";
}
```

Útil para:

- debug;
- logs;
- diagnóstico.

### Cuidado

Evite incluir:

- senhas;
- tokens;
- dados pessoais sensíveis;
- segredos.

### Resposta de entrevista

> equals define igualdade lógica, hashCode precisa respeitar essa igualdade para estruturas hash e toString fornece uma representação textual útil para diagnóstico. Em entidades mutáveis, também tomo cuidado para não alterar campos usados em hashCode enquanto o objeto está dentro de HashMap ou HashSet.

[↑ Voltar ao índice](#indice)

---

<a id="static-final-this"></a>
# 11. static, final e this

<a id="static"></a>
## 11.1 static

Membro `static` pertence à classe, não a uma instância específica.

```java
public static final int MAX_RETRIES = 3;
```

Método estático:

```java
public static Cliente criar(...) {
    ...
}
```

### Atenção

Estado `static` mutável é compartilhado por todas as instâncias e pode gerar:

- acoplamento global;
- problemas de concorrência;
- dificuldade de testes.

---

<a id="final"></a>
## 11.2 final

Pode ser aplicado a:

### Variável

```java
final Cliente cliente;
```

A referência não pode ser reatribuída.

Isso **não torna o objeto imutável**.

### Método

```java
public final void executar() {
}
```

não pode ser sobrescrito.

### Classe

```java
public final class Dinheiro {
}
```

não pode ser herdada.

---

<a id="this"></a>
## 11.3 this

`this` referencia a instância atual.

```java
public Cliente(String nome) {
    this.nome = nome;
}
```

Também permite delegar entre construtores:

```java
public Cliente(String nome) {
    this(nome, Status.ATIVO);
}
```

[↑ Voltar ao índice](#indice)

---

<a id="java-moderno"></a>
# 12. Java Moderno e Modelagem OO

<a id="records"></a>
## 12.1 Records

Records são adequados para representar **dados imutáveis por referência de componentes**, com pouca cerimônia.

```java
public record ClienteResponse(
    Long id,
    String nome
) {}
```

O compilador gera automaticamente:

- construtor canônico;
- acessores;
- `equals`;
- `hashCode`;
- `toString`.

### Bom uso

- DTOs;
- value carriers;
- mensagens;
- respostas de API;
- objetos de transporte.

### Não significa imutabilidade profunda

```java
record Pedido(List<Item> itens) {}
```

A referência `itens` é final, mas a lista pode continuar mutável.

---

<a id="sealed-classes"></a>
## 12.2 Sealed Classes

Controlam quais tipos podem herdar ou implementar uma abstração.

```java
public sealed interface Pagamento
        permits Pix, Cartao, Boleto {
}
```

Implementações:

```java
public final class Pix implements Pagamento {
}
```

Útil para hierarquias fechadas e bem conhecidas.

Combina bem com pattern matching.

---

<a id="enums"></a>
## 12.3 Enums

Enum representa um conjunto fechado de constantes.

```java
public enum StatusPedido {
    CRIADO,
    PAGO,
    CANCELADO
}
```

Enums também podem possuir:

- atributos;
- métodos;
- construtores;
- comportamento específico por constante.

Exemplo:

```java
public enum Operacao {

    SOMA {
        @Override
        public int executar(int a, int b) {
            return a + b;
        }
    };

    public abstract int executar(int a, int b);
}
```

### Resposta de entrevista

> Em Java moderno, records são úteis para modelos orientados a dados com pouca cerimônia, sealed classes permitem controlar hierarquias e enums representam conjuntos fechados de valores com possibilidade de comportamento.

[↑ Voltar ao índice](#indice)

---

<a id="principios-design"></a>
# 13. Princípios de Design relacionados a POO

<a id="programar-abstracoes"></a>
## 13.1 Programar para abstrações

Evite depender diretamente de implementação.

Menos flexível:

```java
private StripeGateway gateway;
```

Mais flexível:

```java
private PagamentoGateway gateway;
```

Isso permite:

- substituição;
- testes;
- múltiplas implementações.

---

<a id="favor-composition"></a>
## 13.2 Favor composition over inheritance

Herança cria acoplamento estrutural.

Composição cria dependência substituível.

```text
Herança
Classe A
   ↑
Classe B
   ↑
Classe C
```

versus:

```text
CheckoutService
   │
   ├── PagamentoGateway
   ├── FraudChecker
   └── CalculadoraFrete
```

Prefira composição quando a relação não é claramente IS-A.

---

<a id="tell-dont-ask"></a>
## 13.3 Tell, Don't Ask

Em vez de buscar dados e decidir tudo fora do objeto:

```java
if (pedido.getStatus() == PENDENTE) {
    pedido.setStatus(APROVADO);
}
```

prefira:

```java
pedido.aprovar();
```

A regra fica encapsulada.

---

<a id="encapsular-invariantes"></a>
## 13.4 Encapsular invariantes

Ruim:

```java
conta.setSaldo(new BigDecimal("-500"));
```

Melhor:

```java
conta.sacar(valor);
```

e a própria classe valida se a operação é permitida.

### Resposta de entrevista

> Em modelagem OO eu procuro programar para abstrações, favorecer composição quando não existe uma relação IS-A legítima e manter invariantes dentro dos próprios objetos. Isso reduz acoplamento e evita que regras de negócio fiquem espalhadas.

[↑ Voltar ao índice](#indice)

---

<a id="armadilhas"></a>
# 14. Armadilhas comuns de entrevista

## Encapsulamento é apenas usar `private`?

```text
Não.
```

`private` ajuda a restringir acesso, mas encapsulamento envolve controlar mudanças de estado e proteger invariantes.

---

## Herança é sempre reutilização de código?

```text
Não.
```

Herança deve representar uma relação de substituição válida.

Para reutilização de comportamento, composição muitas vezes é melhor.

---

## Java suporta herança múltipla de classes?

```text
Não.
```

Uma classe só pode:

```java
extends UmaClasse
```

Mas pode implementar múltiplas interfaces:

```java
implements A, B, C
```

---

## Interface pode ter implementação?

```text
Sim.
```

Pode ter métodos:

```java
default
static
private
```

---

## Classe abstrata pode ter construtor?

```text
Sim.
```

O construtor é utilizado pelas subclasses durante inicialização.

---

## Método `static` é polimórfico por override?

```text
Não.
```

Métodos estáticos pertencem à classe.

Eles podem sofrer **method hiding**, não overriding polimórfico tradicional.

---

## `final` torna objeto imutável?

```text
Não.
```

Isto:

```java
final List<String> nomes = new ArrayList<>();
```

impede:

```java
nomes = outraLista;
```

mas permite:

```java
nomes.add("Lucas");
```

---

## Overload e Override são iguais?

```text
Não.
```

```text
Overload  → parâmetros diferentes → compile time
Override  → comportamento redefinido → runtime
```

---

## Composição e agregação são diferenciadas pelo Java?

Não diretamente.

A diferença é principalmente semântica e relacionada ao ciclo de vida dos objetos.

---

## Polimorfismo exige classe abstrata?

```text
Não.
```

Pode ocorrer via:

- interfaces;
- superclasses concretas;
- classes abstratas.

[↑ Voltar ao índice](#indice)

---

<a id="producao"></a>
# 15. Cenários de Produção

## Cenário 1 — Muitos `if/else` por tipo

Exemplo:

```java
if (tipo.equals("PIX")) {
    ...
} else if (tipo.equals("CARTAO")) {
    ...
} else if (tipo.equals("BOLETO")) {
    ...
}
```

Pode indicar oportunidade para:

```java
PagamentoProcessor
```

com implementações:

```text
PixProcessor
CartaoProcessor
BoletoProcessor
```

Uso:

```java
processor.processar(pagamento);
```

### Conceito relacionado

```text
abstração + polimorfismo
```

---

## Cenário 2 — Classe com dezenas de setters

```text
Pedido
├── setStatus()
├── setTotal()
├── setDesconto()
├── setPagamento()
└── ...
```

Risco:

```text
objeto entrar em estado inválido
```

Melhor:

```java
pedido.aplicarDesconto(...)
pedido.confirmarPagamento(...)
pedido.cancelar(...)
```

### Conceito relacionado

```text
encapsulamento
```

---

## Cenário 3 — Hierarquia muito profunda

```text
BaseService
   ↑
AbstractCrudService
   ↑
AbstractAuditableService
   ↑
ClienteService
```

Problemas possíveis:

- comportamento implícito;
- dificuldade de rastrear origem dos métodos;
- forte acoplamento;
- efeito cascata em mudanças.

Considere:

```text
composição
+
delegação
```

---

## Cenário 4 — Dependência direta de infraestrutura

Ruim:

```java
public class PedidoService {

    private final StripeSdk stripe = new StripeSdk();
}
```

Melhor:

```java
public class PedidoService {

    private final PagamentoGateway gateway;

    public PedidoService(PagamentoGateway gateway) {
        this.gateway = gateway;
    }
}
```

Implementação de infraestrutura:

```java
public class StripePagamentoGateway
        implements PagamentoGateway {
}
```

### Ganhos

- teste unitário;
- substituição;
- menor acoplamento.

---

## Cenário 5 — Objeto anêmico

Modelo:

```java
class Conta {
    private BigDecimal saldo;

    public BigDecimal getSaldo() { ... }
    public void setSaldo(...) { ... }
}
```

Toda regra fica em:

```java
ContaService
```

Isso pode resultar em:

```text
dados separados do comportamento
```

Em domínios ricos, considere mover invariantes e comportamento relevantes para a entidade.

### Atenção

Nem todo DTO precisa ter comportamento.

Um DTO pode ser apenas transporte de dados.

---

## Cenário 6 — Testabilidade

Com dependência concreta:

```java
class PedidoService {
    private StripeClient client;
}
```

Teste fica acoplado ao cliente real.

Com abstração:

```java
class PedidoService {
    private PagamentoGateway gateway;
}
```

No teste:

```java
PagamentoGateway fake = ...
```

### Conceito relacionado

```text
abstração
+
composição
+
injeção de dependência
```

---

## Cenário 7 — Estado compartilhado mutável

Objetos mutáveis compartilhados entre threads podem exigir:

- sincronização;
- locks;
- atomics;
- controle de visibilidade.

Objetos imutáveis reduzem esse problema.

### Correlação com concorrência

```text
imutabilidade
      ↓
menos estado compartilhado mutável
      ↓
menos necessidade de sincronização
```

### Resposta de entrevista

> Em produção eu uso POO para concentrar regras e responsabilidades, não apenas para criar hierarquias. Se encontro muitos condicionais por tipo, avalio polimorfismo. Se há setters permitindo estados inválidos, reforço encapsulamento. Se a hierarquia ficou profunda, normalmente considero composição e delegação.

[↑ Voltar ao índice](#indice)

---

<a id="resposta-completa"></a>
# 16. Resposta Completa de Entrevista

Se perguntarem:

**"O que você entende sobre Programação Orientada a Objetos em Java?"**

Uma resposta objetiva pode ser:

> Programação Orientada a Objetos organiza o sistema em objetos que possuem estado e comportamento. Os pilares clássicos são encapsulamento, abstração, herança e polimorfismo.
>
> Encapsulamento é controlar como o estado interno pode ser acessado e modificado, protegendo invariantes. Abstração permite expor contratos e esconder detalhes de implementação, normalmente através de interfaces ou classes abstratas.
>
> Herança representa uma relação IS-A e permite especialização, mas cria acoplamento entre superclasse e subclasses, então eu evito usá-la apenas para reaproveitar código. Quando quero combinar comportamentos ou trocar implementações, normalmente prefiro composição.
>
> Polimorfismo permite trabalhar com diferentes implementações através de um mesmo contrato. Por exemplo, um serviço pode depender de PagamentoGateway e receber implementações diferentes sem alterar sua lógica principal.
>
> Na prática, procuro manter alta coesão, baixo acoplamento, programar para abstrações e colocar regras próximas dos objetos responsáveis por elas. Em Java moderno, também utilizo records para objetos orientados a dados e sealed classes quando quero controlar uma hierarquia fechada.

[↑ Voltar ao índice](#indice)

---

<a id="mapa-mental"></a>
# 17. Mapa Mental

```text
POO EM JAVA
│
├── Classe / Objeto
│   ├── estado
│   ├── comportamento
│   └── construtor
│
├── Encapsulamento
│   ├── private
│   ├── invariantes
│   ├── comportamento
│   └── imutabilidade
│
├── Abstração
│   ├── interface
│   └── classe abstrata
│
├── Herança
│   ├── IS-A
│   ├── extends
│   ├── override
│   ├── super
│   └── acoplamento
│
├── Polimorfismo
│   ├── interface → implementação
│   ├── dynamic dispatch
│   ├── upcasting
│   └── override
│
├── Composição
│   ├── HAS-A
│   ├── delegação
│   └── flexibilidade
│
├── Relacionamentos
│   ├── associação
│   ├── agregação
│   └── composição
│
├── Design
│   ├── baixo acoplamento
│   ├── alta coesão
│   ├── programar para abstrações
│   └── composição > herança quando aplicável
│
├── Object
│   ├── equals
│   ├── hashCode
│   └── toString
│
└── Java Moderno
    ├── records
    ├── sealed classes
    └── enums
```

[↑ Voltar ao índice](#indice)

---

<a id="resumo-rapido"></a>
# 18. Resumo Rápido

| Conceito | O que lembrar | Trade-off / cuidado |
|---|---|---|
| Classe | Modelo de estado e comportamento | Evitar classes com responsabilidades demais |
| Objeto | Instância concreta | Pode ser mutável ou imutável |
| Encapsulamento | Controla acesso e protege invariantes | Não é apenas getter/setter |
| Abstração | Expõe contrato e esconde detalhes | Abstração excessiva aumenta complexidade |
| Interface | Contrato flexível | Evitar interfaces sem necessidade |
| Classe abstrata | Estado/comportamento base compartilhado | Herança aumenta acoplamento |
| Herança | Relação IS-A | Não usar só para reaproveitar código |
| Polimorfismo | Mesma abstração, comportamentos diferentes | Evitar downcasts frequentes |
| Composição | Relação HAS-A | Geralmente mais flexível que herança |
| Override | Redefine comportamento herdado | Deve preservar contrato |
| Overload | Mesmo nome, parâmetros diferentes | Resolvido em compile time |
| Imutabilidade | Estado não muda após criação | Pode criar mais objetos |
| Acoplamento | Dependência entre componentes | Preferir baixo acoplamento |
| Coesão | Responsabilidades relacionadas | Preferir alta coesão |
| equals/hashCode | Igualdade lógica e hashing | Contrato deve ser consistente |
| `static` | Pertence à classe | Estado static mutável pode ser problemático |
| `final` | Restringe alteração/herança | Não garante imutabilidade profunda |
| Record | Modelo conciso orientado a dados | Não é profundamente imutável |
| Sealed Class | Restringe hierarquia | Adequado para conjuntos fechados de tipos |

---

## Resposta de 30 segundos

> POO em Java organiza o software em objetos com estado e comportamento. Os pilares principais são encapsulamento, abstração, herança e polimorfismo. Eu uso encapsulamento para proteger invariantes, abstrações para desacoplar implementações e polimorfismo para permitir comportamentos diferentes através do mesmo contrato. Herança eu reservo para relações IS-A reais; quando quero flexibilidade e reutilização de comportamento, geralmente prefiro composição.

---

## Perguntas que normalmente vêm depois

Depois de responder sobre POO, é comum aprofundarem em:

```text
1. Interface vs classe abstrata
2. Overload vs Override
3. Composição vs Herança
4. equals e hashCode
5. Encapsulamento e invariantes
6. Polimorfismo e dynamic dispatch
7. final / static / this
8. Imutabilidade
9. Acoplamento e coesão
10. Records e sealed classes
```
