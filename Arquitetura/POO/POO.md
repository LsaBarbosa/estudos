# 1. Pilares da Programação Orientada a Objetos

## Quadro Geral

| Pilar | Ideia central | Como aparece em Java | Benefício principal | Atenção / Trade-off |minha nota
|---|---|---|---|---|---|----|
| **Encapsulamento** | Protege o estado interno do objeto e evita estados inválidos. | Uso de atributos `private` e métodos que controlam alterações. | Mantém a consistência do objeto, reduz acoplamento e protege regras de negócio. | Apenas usar `private` não garante bom encapsulamento. Getters e setters sem regra podem expor demais o objeto. |é proteger o estado interno do objeto e garantir que ele só possa mudar por meio de comportamentos válidos do domínio. | ```pedido.setStatus(PAGO); // ruim se qualquer classe puder fazer isso
pedido.confirmarPagamento(pagamento); // melhor, porque valida regra de negócio```
| **Herança** | Permite criar uma classe especializada a partir de uma classe base. | `class Pix extends Pagamento`. | Reutiliza comportamentos comuns e permite especializações. | Aumenta o acoplamento entre classe pai e classe filha. Pode criar hierarquias rígidas e difíceis de manter. | Herança deve ser usada qauando a relação é um, pois o acoplamento e forte
| **Polimorfismo** | Permite tratar objetos diferentes por meio da mesma abstração. | Interface `PaymentGateway` com múltiplas implementações. | Facilita extensão, testes, substituição de comportamento e manutenção. | Excesso de abstração pode dificultar a navegação e a compreensão do código. |
| **Abstração** | Expõe apenas o essencial e esconde detalhes internos de implementação. | Interfaces, classes abstratas, services e ports. | Reduz dependência de detalhes técnicos e melhora a separação de responsabilidades. | Abstrações mal desenhadas podem ficar genéricas demais, artificiais ou difíceis de evoluir. |
---

## Uso Prático no Dia a Dia

| Pilar              | Uso comum em sistemas Java                                                  |
| ------------------ | --------------------------------------------------------------------------- |
| **Encapsulamento** | Entidades de domínio, objetos de valor, validações e regras de negócio.     |
| **Herança**        | Exceções customizadas, classes base de framework e alguns modelos estáveis. |
| **Polimorfismo**   | Services, gateways, providers, strategies, repositories e adapters.         |
| **Abstração**      | Interfaces, portas da arquitetura hexagonal e contratos entre camadas.      |


| Princípio | Onde aparece                                                                 | ONde vc aplica SOLID
| --------- | ---------------------------------------------------------------------------- |------- |
| **SRP**   | Controller só HTTP; use case orquestra; repository persiste; gateway integra | Contrller HTTP, repositore para persistencia, serviço |
| **OCP**   | Novos descontos entram como novas `PoliticaDesconto`                         | numa interface, ao inves de encher o service com novas regras de pagamento, crio um interfacePagamento
| **LSP**   | Evita herança falsa entre formas de pagamento                                | Ao criar uma entidade e usar o @Embeddable para evitar herança numa relação usuario e endereço e usar composição
| **ISP**   | Ports pequenos para persistência, pagamento e eventos                        | Em repositories de persistencia evitando interface genérica especificando quais comportamentos quero para o repo em específico
| **DIP**   | Use case depende de interfaces, não de JPA, Kafka ou Feign                   |

---

## Exemplo Mental para Entrevista

| Pergunta                                     | Resposta curta                                                                                                    |
| -------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| Encapsulamento é só usar `private`?          | Não. O objetivo é proteger invariantes e impedir estados inválidos.                                               |
| Herança é sempre boa para reutilizar código? | Não. Pode aumentar acoplamento. Em muitos casos composição é melhor.                                              |
| Polimorfismo reduz `if/else`?                | Sim. Permite escolher comportamento pela implementação concreta.                                                  |
| Abstração é interface?                       | Interface é uma forma de abstração, mas abstração é o conceito de esconder detalhes e expor contratos relevantes. |

---

# 2. Composição vs Herança

## Comparação Principal

| Critério               | Herança                                                  | Composição                                     |
| ---------------------- | -------------------------------------------------------- | ---------------------------------------------- |
| **Ideia principal**    | Uma classe **é um tipo de** outra.                       | Uma classe **possui** ou **usa** outra.        |
| **Relação conceitual** | “É um”.                                                  | “Tem um” ou “usa um”.                          |
| **Exemplo**            | `Pix extends Pagamento`.                                 | `Pedido` tem uma `FormaPagamento`.             |
| **Acoplamento**        | Alto, porque a filha depende da estrutura da classe pai. | Menor, porque objetos colaboram por contratos. |
| **Flexibilidade**      | Menor. A estrutura é fixa em tempo de compilação.        | Maior. Implementações podem ser trocadas.      |
| **Reutilização**       | Por herança de atributos e métodos.                      | Por delegação de responsabilidade.             |
| **Testabilidade**      | Pode dificultar testes isolados.                         | Facilita mocks, stubs e substituições.         |
| **Manutenção**         | Pode piorar com hierarquias profundas.                   | Geralmente mais simples de evoluir.            |
| **Uso em enterprise**  | Usada com cautela.                                       | Normalmente preferida.                         |

---

## Quando Usar Cada Uma

| Situação                                              | Melhor escolha                 |
| ----------------------------------------------------- | ------------------------------ |
| Existe relação real de especialização?                | **Herança**                    |
| O comportamento pode variar?                          | **Composição**                 |
| A implementação precisa ser trocada por configuração? | **Composição**                 |
| Preciso reduzir acoplamento?                          | **Composição**                 |
| Estou criando uma hierarquia profunda?                | Evitar herança                 |
| Quero aplicar Strategy, Adapter, Gateway ou Provider? | **Composição**                 |
| Estou criando exceções customizadas?                  | **Herança** pode fazer sentido |
| Quero modelar objetos ricos de domínio?               | Composição interna pode ajudar |

---

## Relação com SOLID

| Princípio | Relação com composição/herança                             |
| --------- | ---------------------------------------------------------- |
| **SRP**   | Composição ajuda a separar responsabilidades.              |
| **OCP**   | Composição facilita extensão sem alterar código existente. |
| **LSP**   | Herança mal usada costuma quebrar substituição correta.    |
| **DIP**   | Composição com interfaces permite depender de abstrações.  |

---

## Frase de Entrevista

| Tema                  | Resposta forte                                                                                                                                                                                                 |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Composição vs Herança | Herança deve ser usada quando existe uma relação clara de especialização. Em sistemas enterprise modernos, composição costuma ser preferida porque reduz acoplamento, aumenta flexibilidade e facilita testes. |

---

# 3. SOLID na Prática

## Quadro Geral

| Letra | Princípio                       | Ideia central                                                | O que evita                               | Benefício                                 | Trade-off                   |
| ----- | ------------------------------- | ------------------------------------------------------------ | ----------------------------------------- | ----------------------------------------- | --------------------------- |
| **S** | Single Responsibility Principle | Uma classe deve ter um motivo principal para mudar.          | Classes grandes e misturadas.             | Mais coesão e facilidade de teste.        | Pode gerar mais classes.    |
| **O** | Open/Closed Principle           | Aberto para extensão, fechado para modificação.              | Alterar código estável a cada nova regra. | Reduz risco de regressão.                 | Pode criar mais abstrações. |
| **L** | Liskov Substitution Principle   | Subclasse deve substituir a classe pai sem quebrar contrato. | Heranças incorretas.                      | Hierarquias mais seguras.                 | Exige melhor modelagem.     |
| **I** | Interface Segregation Principle | Interfaces pequenas e específicas.                           | Interfaces grandes e genéricas.           | Contratos mais claros.                    | Pode gerar mais interfaces. |
| **D** | Dependency Inversion Principle  | Depender de abstrações, não de implementações.               | Acoplamento com detalhes técnicos.        | Facilita testes e troca de implementação. | Mais configuração inicial.  |

---

## Aplicação Prática

| Princípio | Pergunta prática                                         | Exemplo ruim                                                 | Exemplo melhor                                                                     |
| --------- | -------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------------------------------------------------------- |
| **SRP**   | Essa classe tem mais de um motivo para mudar?            | `PedidoService` valida, calcula frete, salva e envia e-mail. | Separar em `PedidoValidator`, `FreteService`, `PedidoRepository` e `EmailService`. |
| **OCP**   | Para adicionar regra nova preciso alterar código antigo? | `switch` para cada tipo de pagamento.                        | Interface `PaymentStrategy` com implementações específicas.                        |
| **LSP**   | A subclasse realmente pode substituir a classe pai?      | `Pinguim extends Ave` herdando `voar()`.                     | Separar `Ave` de `AveQueVoa`.                                                      |
| **ISP**   | A interface força métodos desnecessários?                | `Funcionario` com salário, ponto e aprovação de férias.      | Interfaces `Pagavel`, `PontoRegistravel`, `AprovadorFerias`.                       |
| **DIP**   | Meu código depende de classe concreta?                   | `new EmailSender()` dentro do service.                       | Injetar `NotificationGateway`.                                                     |

---

## SOLID em Java/Spring

| Princípio | Aplicação prática                                                               |
| --------- | ------------------------------------------------------------------------------- |
| **SRP**   | Separar controller, service, use case, repository, mapper, validator e gateway. |
| **OCP**   | Criar strategies para frete, pagamento, desconto e notificação.                 |
| **LSP**   | Evitar hierarquias falsas em entidades, DTOs e classes base.                    |
| **ISP**   | Criar interfaces pequenas para ports, gateways, repositories e providers.       |
| **DIP**   | Usar injeção de dependência e depender de interfaces do domínio.                |

---

## Trade-offs do SOLID

| Princípio | Ganho                                | Custo                                    |
| --------- | ------------------------------------ | ---------------------------------------- |
| **SRP**   | Classes menores e mais coesas.       | Mais arquivos/classes.                   |
| **OCP**   | Menos alteração em código existente. | Mais interfaces e estratégias.           |
| **LSP**   | Herança mais segura.                 | Menos liberdade para herdar sem análise. |
| **ISP**   | Contratos mais limpos.               | Mais interfaces pequenas.                |
| **DIP**   | Baixo acoplamento e testes melhores. | Mais configuração e indireção.           |

---

## Resumo para Entrevista

| Princípio | Frase-chave                                                |
| --------- | ---------------------------------------------------------- |
| **SRP**   | Uma classe deve ter uma responsabilidade principal.        |
| **OCP**   | Extenda comportamento sem alterar código estável.          |
| **LSP**   | Subclasses devem respeitar o contrato da classe pai.       |
| **ISP**   | Interfaces pequenas são melhores que interfaces genéricas. |
| **DIP**   | Dependa de abstrações, não de implementações concretas.    |

---

# 4. POO e Arquitetura

## Relação Direta

| Conceito de POO    | Relação com arquitetura              | Aplicação prática                                 | Exemplo                                                     |
| ------------------ | ------------------------------------ | ------------------------------------------------- | ----------------------------------------------------------- |
| **Encapsulamento** | Protege regras internas do domínio.  | Entidades impedem estados inválidos.              | `Pedido.adicionarItem()` valida quantidade, preço e status. |
| **Abstração**      | Define contratos entre camadas.      | Interfaces escondem detalhes técnicos.            | `PaymentGateway`, `PedidoRepository`.                       |
| **Polimorfismo**   | Permite trocar comportamento.        | Estratégias para pagamento, frete e desconto.     | `PixPayment`, `CreditCardPayment`.                          |
| **Herança**        | Reutiliza comportamento comum.       | Exceções customizadas e classes base.             | `BusinessException extends RuntimeException`.               |
| **Composição**     | Monta comportamento por colaboração. | Use case usa validators, repositories e gateways. | `CreateOrderUseCase` usa `OrderRepository`.                 |

---

## Arquitetura em Camadas

| Camada                       | Responsabilidade               | Como a POO aparece                                           |
| ---------------------------- | ------------------------------ | ------------------------------------------------------------ |
| **Controller**               | Receber requisições HTTP.      | Usa services/use cases por abstração.                        |
| **Application / Use Case**   | Coordenar fluxo de negócio.    | Usa composição para chamar domínio, repositories e gateways. |
| **Domain**                   | Conter regras centrais.        | Usa encapsulamento, entidades e objetos de valor.            |
| **Infrastructure**           | Implementar detalhes técnicos. | Implementa repositories, gateways, clients e producers.      |
| **Database / APIs externas** | Persistência e integração.     | Ficam atrás de interfaces para não contaminar o domínio.     |

---

## Arquitetura Hexagonal

| Conceito POO          | Conceito na Hexagonal  | Explicação                                            |
| --------------------- | ---------------------- | ----------------------------------------------------- |
| **Interface**         | Port                   | Define o que o core precisa do mundo externo.         |
| **Classe concreta**   | Adapter                | Implementa banco, Kafka, Redis ou API externa.        |
| **Objeto de domínio** | Core                   | Contém regras importantes do negócio.                 |
| **Composição**        | Injeção de dependência | Use case recebe portas/interfaces no construtor.      |
| **Polimorfismo**      | Troca de adapters      | O core usa outra implementação sem conhecer detalhes. |

---

## Clean Architecture

| Conceito POO       | Clean Architecture         | Exemplo                                           |
| ------------------ | -------------------------- | ------------------------------------------------- |
| **Entidade**       | Enterprise Business Rules  | `Pedido`, `Cliente`, `Produto`.                   |
| **Use Case**       | Application Business Rules | `CriarPedido`, `CancelarPedido`.                  |
| **Interface**      | Boundary / Port            | `PedidoRepository`, `EmailGateway`.               |
| **Implementação**  | Frameworks & Drivers       | `JpaPedidoRepository`, `SmtpEmailGateway`.        |
| **Encapsulamento** | Proteção do domínio        | Regras dentro de entidades e services de domínio. |

---

## Exemplo Arquitetural

| Elemento              | Tipo POO                 | Papel arquitetural                   |
| --------------------- | ------------------------ | ------------------------------------ |
| `Pedido`              | Entidade                 | Representa regra central do domínio. |
| `PedidoItem`          | Entidade ou Value Object | Compõe o pedido.                     |
| `PedidoRepository`    | Interface                | Define contrato de persistência.     |
| `JpaPedidoRepository` | Implementação concreta   | Salva dados usando JPA.              |
| `CreatePedidoUseCase` | Classe de aplicação      | Orquestra a criação do pedido.       |
| `PaymentGateway`      | Interface                | Define contrato de pagamento.        |
| `PixPaymentGateway`   | Implementação concreta   | Integra com provedor Pix.            |
| `PedidoController`    | Adapter de entrada       | Expõe endpoint REST.                 |

---

## Spring e POO

| Recurso Java/Spring     | Conceito POO                      | Uso arquitetural                     |
| ----------------------- | --------------------------------- | ------------------------------------ |
| `interface`             | Abstração e polimorfismo          | Contratos entre camadas.             |
| `class`                 | Objeto concreto                   | Implementação de regras ou adapters. |
| `record`                | Dados imutáveis ou semi-imutáveis | DTOs, commands e responses.          |
| `enum`                  | Estados fixos                     | Status de pedido, tipo de pagamento. |
| `@Service`              | Classe de aplicação               | Orquestra regras.                    |
| `@Repository`           | Adapter de persistência           | Acesso a banco.                      |
| `@RestController`       | Adapter de entrada                | Entrada HTTP.                        |
| Injeção via construtor  | Composição / DIP                  | Baixo acoplamento.                   |
| Exceptions customizadas | Herança controlada                | Erros de negócio.                    |

---

## Trade-offs na Arquitetura

| Uso de POO        | Benefício                            | Trade-off                                          |
| ----------------- | ------------------------------------ | -------------------------------------------------- |
| Muitas interfaces | Baixo acoplamento e testabilidade.   | Excesso de arquivos.                               |
| Entidades ricas   | Regras protegidas no domínio.        | Mais cuidado com JPA/ORM.                          |
| Composição        | Flexibilidade e baixo acoplamento.   | Mais objetos colaborando.                          |
| Polimorfismo      | Reduz `if/else` e facilita extensão. | Pode dificultar descobrir qual implementação roda. |
| Herança           | Reuso simples em alguns casos.       | Pode criar hierarquias rígidas.                    |
| Abstração         | Protege o core de detalhes técnicos. | Pode virar abstração prematura.                    |

---

# 5. JVM, Memória e POO

## Conceitos Principais

| Conceito                   | O que representa em Java                | Onde aparece na JVM           | Explicação prática                                                  |
| -------------------------- | --------------------------------------- | ----------------------------- | ------------------------------------------------------------------- |
| **Classe**                 | Modelo de um tipo.                      | **Metaspace**                 | A JVM carrega metadados, métodos, atributos, bytecode e hierarquia. |
| **Objeto**                 | Instância de uma classe.                | **Heap**                      | Objetos criados com `new` normalmente ficam no heap.                |
| **Atributos de instância** | Estado do objeto.                       | **Heap**                      | Cada objeto possui seus próprios valores.                           |
| **Métodos**                | Comportamento da classe.                | Metaspace + Stack             | O código pertence à classe; a chamada cria frame na stack.          |
| **Variáveis locais**       | Dados temporários do método.            | **Stack**                     | Existem apenas durante a execução do método.                        |
| **Referências**            | Variáveis que apontam para objetos.     | Stack apontando para Heap     | A variável guarda referência, não o objeto inteiro.                 |
| **Encapsulamento**         | Controle de acesso ao estado.           | Heap + métodos                | Métodos controlam alteração do estado.                              |
| **Herança**                | Relação entre tipos.                    | Metaspace                     | A JVM conhece a hierarquia entre classes.                           |
| **Polimorfismo**           | Chamada por abstração.                  | Runtime                       | A JVM decide em tempo de execução qual método chamar.               |
| **Abstração**              | Contrato via interface/classe abstrata. | Metaspace + dispatch dinâmico | Usada para validar e despachar chamadas.                            |

---

## Áreas de Memória da JVM

| Área                    | O que armazena                                     | Relação com POO                                               |
| ----------------------- | -------------------------------------------------- | ------------------------------------------------------------- |
| **Heap**                | Objetos criados em tempo de execução.              | Onde vivem as instâncias.                                     |
| **Stack**               | Frames de métodos, variáveis locais e referências. | Onde ocorrem as chamadas de métodos.                          |
| **Metaspace**           | Metadados das classes carregadas.                  | Guarda informações de classes, interfaces, métodos e herança. |
| **PC Register**         | Próxima instrução de cada thread.                  | Controla execução do bytecode.                                |
| **Native Method Stack** | Chamadas de código nativo.                         | Usada com JNI e bibliotecas nativas.                          |

---

## Classe vs Objeto

| Critério      | Classe                                     | Objeto                                 |
| ------------- | ------------------------------------------ | -------------------------------------- |
| O que é       | Modelo.                                    | Instância real.                        |
| Exemplo       | `Conta.class`.                             | `new Conta()`.                         |
| Onde fica     | Metaspace.                                 | Heap.                                  |
| Quantidade    | Uma definição por ClassLoader.             | Pode haver milhares ou milhões.        |
| Contém        | Metadados, métodos e estrutura dos campos. | Valores reais dos atributos.           |
| Ciclo de vida | Enquanto a classe estiver carregada.       | Enquanto houver referência alcançável. |

---

## Stack vs Heap

| Critério         | Stack                                                | Heap                                 |
| ---------------- | ---------------------------------------------------- | ------------------------------------ |
| Armazena         | Chamadas de métodos, variáveis locais e referências. | Objetos.                             |
| Velocidade       | Muito rápida.                                        | Mais custosa.                        |
| Gerenciamento    | Automático por entrada/saída de métodos.             | Gerenciado pelo Garbage Collector.   |
| Compartilhamento | Cada thread tem sua própria stack.                   | Compartilhado entre threads.         |
| Erro comum       | `StackOverflowError`.                                | `OutOfMemoryError: Java heap space`. |
| Exemplo          | `Conta conta`, `BigDecimal valor`.                   | `new Conta()`, `new BigDecimal()`.   |

---

## Spring, Hibernate e JVM

| Tecnologia                 | Relação com POO/JVM            | Explicação                                                               |
| -------------------------- | ------------------------------ | ------------------------------------------------------------------------ |
| **Spring DI**              | Polimorfismo + composição      | Spring injeta uma implementação concreta em uma abstração.               |
| **Hibernate Lazy Loading** | Proxy + polimorfismo           | Hibernate cria proxies que se comportam como entidades reais.            |
| **Beans singleton**        | Objetos compartilhados no heap | Services Spring normalmente são singleton e devem evitar estado mutável. |
| **JPA Entities**           | Objetos mutáveis gerenciados   | Entidades são rastreadas pelo contexto de persistência.                  |

---

# 6. Imutabilidade e Concorrência

## Conceitos Fundamentais

| Conceito           | Explicação                                     | Relação com concorrência                                 |
| ------------------ | ---------------------------------------------- | -------------------------------------------------------- |
| **Imutabilidade**  | Objeto não muda depois de criado.              | Evita alteração simultânea por múltiplas threads.        |
| **Mutabilidade**   | Objeto pode alterar estado durante a vida.     | Pode gerar problemas com estado compartilhado.           |
| **Thread-safe**    | Seguro para uso simultâneo.                    | Objetos imutáveis tendem a ser naturalmente thread-safe. |
| **Race condition** | Resultado incorreto por disputa entre threads. | Ocorre com estado compartilhado mutável sem controle.    |
| **Sincronização**  | Controle de acesso concorrente.                | Resolve concorrência, mas adiciona custo.                |

---

## Objeto Mutável vs Imutável

| Tema                | Objeto mutável                              | Objeto imutável                                   |
| ------------------- | ------------------------------------------- | ------------------------------------------------- |
| **Estado interno**  | Pode mudar depois da criação.               | Não muda depois da criação.                       |
| **Concorrência**    | Requer cuidado.                             | Naturalmente mais seguro.                         |
| **Thread-safety**   | Precisa de proteção.                        | Geralmente seguro sem sincronização.              |
| **Previsibilidade** | Menor.                                      | Maior.                                            |
| **Testabilidade**   | Pode exigir mais cenários.                  | Mais simples de testar.                           |
| **Risco de bug**    | Maior em concorrência.                      | Menor em concorrência.                            |
| **Performance**     | Pode evitar criação de objetos.             | Pode criar mais objetos.                          |
| **Uso comum**       | Entidades JPA, buffers, caches controlados. | DTOs, records, eventos, commands e Value Objects. |

---

## Benefícios da Imutabilidade

| Benefício                          | Explicação                                                      |
| ---------------------------------- | --------------------------------------------------------------- |
| **Thread-safe**                    | Várias threads podem ler o mesmo objeto sem risco de alteração. |
| **Sem sincronização para leitura** | Não precisa de `synchronized` apenas para leitura.              |
| **Previsibilidade**                | O valor não muda inesperadamente.                               |
| **Menos efeitos colaterais**       | Um método não altera o objeto recebido sem o chamador perceber. |
| **Facilita cache**                 | Objetos imutáveis podem ser compartilhados com mais segurança.  |
| **Facilita testes**                | Mesmo input tende a gerar mesmo output.                         |
| **Bom para Value Objects**         | Ideal para dinheiro, período, CPF, e-mail e endereço.           |

---

## Alternativas para Concorrência

| Alternativa                  | Como funciona                                    | Quando usar                              |
| ---------------------------- | ------------------------------------------------ | ---------------------------------------- |
| **Imutabilidade**            | Evita alteração de estado compartilhado.         | Value Objects, commands, eventos e DTOs. |
| **`AtomicInteger`**          | Operações atômicas sem `synchronized` explícito. | Contadores simples.                      |
| **`LongAdder`**              | Contador otimizado para alta concorrência.       | Métricas com muitas threads.             |
| **`synchronized`**           | Apenas uma thread executa o bloco por vez.       | Seções críticas simples.                 |
| **`Lock`**                   | Controle manual mais flexível.                   | Concorrência avançada.                   |
| **Collections concorrentes** | Estruturas preparadas para múltiplas threads.    | `ConcurrentHashMap`, filas concorrentes. |
| **Estado local**             | Cada thread usa seu próprio estado.              | Processamento isolado por requisição.    |

---

## `AtomicInteger`

| Ponto            | Explicação                                                |
| ---------------- | --------------------------------------------------------- |
| **O que é**      | Classe para operações atômicas com inteiros.              |
| **Método comum** | `incrementAndGet()`.                                      |
| **Vantagem**     | Evita `synchronized` explícito para operações simples.    |
| **Bom para**     | Contadores, métricas simples e IDs locais simples.        |
| **Limitação**    | Não resolve regras complexas envolvendo múltiplos campos. |

---

## `synchronized`

| Ponto                   | Explicação                                                            |
| ----------------------- | --------------------------------------------------------------------- |
| **O que faz**           | Garante que apenas uma thread execute o trecho por vez.               |
| **Protege atualização** | Evita perda de atualização em operações como `value++`.               |
| **Protege leitura**     | Pode garantir visibilidade correta quando usado de forma consistente. |
| **Vantagem**            | Simples para casos pequenos.                                          |
| **Custo**               | Pode reduzir performance sob alta concorrência.                       |

---

## Comparação das Soluções

| Solução             | Resolve race condition?                      | Melhor uso                                           | Trade-off                                         |
| ------------------- | -------------------------------------------- | ---------------------------------------------------- | ------------------------------------------------- |
| **Objeto imutável** | Sim, porque não altera estado compartilhado. | Value Objects, eventos, commands e dados de domínio. | Pode criar mais objetos.                          |
| **`AtomicInteger`** | Sim, para operação atômica simples.          | Contadores e métricas simples.                       | Limitado para regras complexas.                   |
| **`LongAdder`**     | Sim, para contagem concorrente.              | Métricas de alta concorrência.                       | Menos direto para leitura exata em todo instante. |
| **`synchronized`**  | Sim, se usado corretamente.                  | Seções críticas simples.                             | Pode reduzir paralelismo.                         |
| **`Lock`**          | Sim, com mais controle.                      | Cenários avançados.                                  | Mais verboso e sujeito a erro.                    |
| **Estado local**    | Sim, porque não há compartilhamento.         | Variáveis dentro de métodos.                         | Nem sempre é possível.                            |

---

## Memória, Atomicidade e Visibilidade

| Conceito         | Explicação                                                                                  |
| ---------------- | ------------------------------------------------------------------------------------------- |
| **Atomicidade**  | A operação acontece de forma indivisível.                                                   |
| **Visibilidade** | Uma thread enxerga alterações feitas por outra.                                             |
| **Ordenação**    | JVM/CPU podem reorganizar instruções se o resultado individual da thread continuar correto. |
| **`volatile`**   | Ajuda em visibilidade, mas não resolve atomicidade de operações compostas.                  |
| **`value++`**    | Não é atômico. Envolve leitura, incremento e escrita.                                       |

---

## Quando Usar Cada Abordagem

| Cenário                            | Melhor escolha                             |
| ---------------------------------- | ------------------------------------------ |
| Value Object de domínio            | Imutabilidade                              |
| Dinheiro, CPF, e-mail, período     | Imutabilidade                              |
| DTO de entrada/saída               | Imutabilidade ou semi-imutabilidade        |
| Evento de domínio                  | Imutabilidade                              |
| Command de caso de uso             | Imutabilidade                              |
| Contador simples concorrente       | `AtomicInteger` ou `LongAdder`             |
| Métricas com alta concorrência     | `LongAdder`                                |
| Regra crítica com múltiplos campos | `synchronized`, `Lock` ou transação        |
| Entidade JPA                       | Geralmente mutável, mas com encapsulamento |
| Service Spring singleton           | Evitar estado mutável compartilhado        |

---

## Relação com Spring

| Situação                        | Risco                                   | Melhor prática                                        |
| ------------------------------- | --------------------------------------- | ----------------------------------------------------- |
| `@Service` com atributo mutável | Estado compartilhado entre requisições. | Manter service stateless.                             |
| Variável local no método        | Cada thread tem sua própria stack.      | Seguro na maioria dos casos.                          |
| DTO imutável                    | Menor risco de alteração acidental.     | Usar `record` quando fizer sentido.                   |
| Cache em memória                | Pode ter concorrência.                  | Usar `ConcurrentHashMap` ou cache apropriado.         |
| Contador em bean singleton      | Pode ter race condition.                | Usar `AtomicInteger`, `LongAdder` ou métrica própria. |

---

## Resumo Final

| Pergunta                                  | Resposta objetiva                                                              |
| ----------------------------------------- | ------------------------------------------------------------------------------ |
| O que é imutabilidade?                    | Objeto cujo estado não muda após a criação.                                    |
| Por que ajuda em concorrência?            | Porque múltiplas threads podem compartilhar o objeto sem alteração simultânea. |
| `value++` é atômico?                      | Não. Ele envolve leitura, incremento e escrita.                                |
| `volatile` resolve `value++`?             | Não. Resolve visibilidade, mas não atomicidade.                                |
| Quando usar `AtomicInteger`?              | Para contadores e operações atômicas simples.                                  |
| Quando usar `LongAdder`?                  | Para contadores com alta concorrência.                                         |
| Quando usar `synchronized`?               | Quando uma seção crítica precisa ser protegida.                                |
| `record` é sempre profundamente imutável? | Não. Se tiver campos mutáveis, precisa de cópia defensiva.                     |
| Service Spring pode ter estado?           | Deve evitar estado mutável, porque geralmente é singleton.                     |

---
