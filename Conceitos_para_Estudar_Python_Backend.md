# Conceitos para Estudar — Python, FastAPI, SQLAlchemy, Arquitetura e Backend

Material de revisão para **engenharia de software backend com Python**, com **respostas curtas para estudo e entrevistas**, organizado por pré-requisitos. Inspirado na estrutura do roteiro fornecido para Java, preserva fundamentos transversais (SQL, arquitetura, microsserviços, Kafka, AWS e CI/CD) e inclui conceitos próprios de Python.

> **Como estudar:** leia pela ordem, explique cada conceito sem consultar a resposta e implemente um exemplo pequeno. Para cada capítulo, considere trade-offs, testes e diagnósticos de produção. Detalhes de biblioteca e de runtime devem ser conferidos na versão instalada.

> **Público-alvo:** pessoa que já desenvolve backend e quer dominar Python de forma sólida, sem tratar FastAPI como substituto direto de todo o Spring.

<a id="sumario-geral"></a>

## Sumário Geral

### Etapa 1 — Linguagem, modelagem e design

- [01. Fundamentos de Python](#python-01-sumario)
- [02. Programação Orientada a Objetos em Python](#python-02-sumario)
- [03. Collections e Estruturas de Dados](#python-03-sumario)
- [04. Python Funcional, Iteradores e Generators](#python-04-sumario)
- [05. Type Hints, Protocols e Modelagem de Dados](#python-05-sumario)
- [06. SOLID e Princípios de Design](#python-06-sumario)
- [07. Design Patterns em Python](#python-07-sumario)

### Etapa 2 — Runtime, concorrência e dependências

- [08. CPython: Memória e Garbage Collection](#python-08-sumario)
- [09. Concorrência e Paralelismo em Python](#python-09-sumario)
- [10. Asyncio e Programação Assíncrona](#python-10-sumario)
- [11. Build, Ambientes e Dependências Python](#python-11-sumario)

### Etapa 3 — Dados, HTTP e frameworks

- [12. SQL e Bancos Relacionais](#python-12-sumario)
- [13. HTTP e APIs REST](#python-13-sumario)
- [14. FastAPI: Framework HTTP e Dependency Injection](#python-14-sumario)
- [15. Pydantic: Validação e Serialização](#python-15-sumario)
- [16. SQLAlchemy, ORM e Alembic](#python-16-sumario)
- [17. Segurança em Backends Python](#python-17-sumario)
- [18. Testes Automatizados com Pytest](#python-18-sumario)

### Etapa 4 — Arquitetura e sistemas distribuídos

- [19. Arquitetura de Software](#python-19-sumario)
- [20. Microsserviços](#python-20-sumario)
- [21. Apache Kafka e Clientes Python](#python-21-sumario)
- [22. Mensageria, Filas e Jobs com Python](#python-22-sumario)
- [23. Observabilidade, Resiliência e Performance](#python-23-sumario)

### Etapa 5 — Infraestrutura, entrega e complementos

- [24. Docker e Kubernetes para Python](#python-24-sumario)
- [25. AWS para Backends Python](#python-25-sumario)
- [26. CI/CD e Entrega de Aplicações Python](#python-26-sumario)
- [27. Django e Django REST Framework](#python-27-sumario)
- [28. Java vs Python: Mapeamento para Backend](#python-28-sumario)

> Cada capítulo tem sumário interno e, após cada resposta, os atalhos **↑ Sumário do tópico** e **↑ Sumário geral**.

---

<a id="python-01-sumario"></a>

# 01. Fundamentos de Python

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-01-visao-geral)
- [Interpretador, CPython e Bytecode](#python-01-interpretador-cpython-e-bytecode)
- [Tipagem Dinâmica e Tipagem Forte](#python-01-tipagem-dinamica-e-tipagem-forte)
- [Variáveis, Identidade e Referências](#python-01-variaveis-identidade-e-referencias)
- [Mutável vs Imutável](#python-01-mutavel-vs-imutavel)
- [`is` vs `==`](#python-01-is-vs)
- [`None`, Truthiness e Falsy](#python-01-none-truthiness-e-falsy)
- [Tipos Numéricos e Precisão](#python-01-tipos-numericos-e-precisao)
- [Strings, Unicode e Encoding](#python-01-strings-unicode-e-encoding)
- [F-strings e Formatação](#python-01-f-strings-e-formatacao)
- [Operadores e Comparações Encadeadas](#python-01-operadores-e-comparacoes-encadeadas)
- [Controle de Fluxo e `match`](#python-01-controle-de-fluxo-e-match)
- [Funções e `return`](#python-01-funcoes-e-return)
- [Parâmetros Posicionais, Nomeados, `*args` e `**kwargs`](#python-01-parametros-posicionais-nomeados-args-e-kwargs)
- [Argumentos Padrão Mutáveis](#python-01-argumentos-padrao-mutaveis)
- [LEGB, `global` e `nonlocal`](#python-01-legb-global-e-nonlocal)
- [`try`, `except`, `else` e `finally`](#python-01-try-except-else-e-finally)
- [`raise` e Encadeamento de Exceções](#python-01-raise-e-encadeamento-de-excecoes)
- [`with` e Gerenciadores de Contexto](#python-01-with-e-gerenciadores-de-contexto)
- [Módulos, Pacotes e Importação](#python-01-modulos-pacotes-e-importacao)
- [`__name__ == "__main__"`](#python-01-name-main)
- [Iteráveis, Iteradores e Protocolo de Iteração](#python-01-iteraveis-iteradores-e-protocolo-de-iteracao)
- [Slicing e Unpacking](#python-01-slicing-e-unpacking)
- [Boas Práticas de Estilo e PEP 8](#python-01-boas-praticas-de-estilo-e-pep-8)
- [Resumo de Entrevista](#python-01-resumo-de-entrevista)

---

<a id="python-01-visao-geral"></a>

## Visão Geral

> Python é uma linguagem de alto nível, tipagem dinâmica e forte, com múltiplos paradigmas. Para backend, priorizo semântica de objetos, funções, tratamento de falhas, módulos, tipagem gradual e comportamento do interpretador.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-interpretador-cpython-e-bytecode"></a>

## Interpretador, CPython e Bytecode

> CPython compila módulos em bytecode e os executa numa máquina virtual. Arquivos `.pyc` são caches de compilação, não artefatos portáveis entre quaisquer implementações e versões.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-tipagem-dinamica-e-tipagem-forte"></a>

## Tipagem Dinâmica e Tipagem Forte

> O tipo pertence ao objeto e é verificado durante a execução; atribuir outro tipo à variável é permitido. Operações incompatíveis não são convertidas indiscriminadamente e podem lançar `TypeError`.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-variaveis-identidade-e-referencias"></a>

## Variáveis, Identidade e Referências

> Nomes são vinculados a objetos. `a = b` não copia o objeto; dois nomes podem referenciar a mesma estrutura mutável. `id()` revela identidade durante a vida do objeto, não um endereço universal.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-mutavel-vs-imutavel"></a>

## Mutável vs Imutável

> `list`, `dict` e `set` são mutáveis; `int`, `str`, `tuple` e `frozenset` não permitem alteração do próprio valor. Uma tupla que contém uma lista não é profundamente imutável.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-is-vs"></a>

## `is` vs `==`

> `is` compara identidade; `==` usa o protocolo de igualdade. Emprego `is None` e `is not None` para testar sentinelas; não uso `is` para comparar strings ou números por valor.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-none-truthiness-e-falsy"></a>

## `None`, Truthiness e Falsy

> `None` representa ausência. Valores como `False`, zero e coleções vazias são falsy; quando zero e ausência têm significados distintos, comparo explicitamente com `None`.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-tipos-numericos-e-precisao"></a>

## Tipos Numéricos e Precisão

> `int` possui precisão arbitrária sujeita a memória; `float` usa representação binária e introduz arredondamentos. Para finanças, considero `decimal.Decimal` e regras explícitas de quantização.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-strings-unicode-e-encoding"></a>

## Strings, Unicode e Encoding

> `str` representa texto Unicode e `bytes` representa sequências de bytes. Codifico e decodifico nas fronteiras, preferindo UTF-8 explícito para arquivos e protocolos.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-f-strings-e-formatacao"></a>

## F-strings e Formatação

> F-strings interpolam expressões com legibilidade. Não substituem parametrização SQL nem escapam automaticamente conteúdo para HTML, comandos ou logs.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-operadores-e-comparacoes-encadeadas"></a>

## Operadores e Comparações Encadeadas

> `and`/`or` podem devolver operandos, não necessariamente booleanos. Expressões como `0 < x < 10` são encadeadas; avalio falsy com cuidado para não confundir ausência e valor válido.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-controle-de-fluxo-e-match"></a>

## Controle de Fluxo e `match`

> `if`, `for`, `while` e `match` organizam decisões. Pattern matching compara estruturas e padrões, não substitui automaticamente polimorfismo ou uma modelagem de estados.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-funcoes-e-return"></a>

## Funções e `return`

> Funções são objetos de primeira classe, aceitam parâmetros e podem retornar múltiplos valores por empacotamento em tupla. `return` sem expressão devolve `None`.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-parametros-posicionais-nomeados-args-e-kwargs"></a>

## Parâmetros Posicionais, Nomeados, `*args` e `**kwargs`

> `*args` reúne posicionais extras e `**kwargs` reúne nomeados extras. Os marcadores `/` e `*` definem parâmetros somente posicionais e somente nomeados, melhorando contratos públicos.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-argumentos-padrao-mutaveis"></a>

## Argumentos Padrão Mutáveis

> Valores padrão são avaliados na definição da função, não a cada chamada. Evito `def f(itens=[])`; uso `None` como sentinela e crio uma lista por invocação.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-legb-global-e-nonlocal"></a>

## LEGB, `global` e `nonlocal`

> A resolução de nomes procura escopos Local, Enclosing, Global e Builtins. `nonlocal` permite religar nome em escopo envolvente; `global` atinge nome do módulo e cria acoplamento quando usado indiscriminadamente.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-try-except-else-e-finally"></a>

## `try`, `except`, `else` e `finally`

> `except` trata falhas específicas; `else` executa quando o `try` não lança exceção; `finally` viabiliza limpeza. Evito `except Exception: pass` e preservo causas ao encapsular falhas.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-raise-e-encadeamento-de-excecoes"></a>

## `raise` e Encadeamento de Exceções

> `raise MinhaFalha(...) from erro` registra causalidade. Converto erros de infraestrutura para erros de aplicação na fronteira adequada, sem esconder informações de diagnóstico.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-with-e-gerenciadores-de-contexto"></a>

## `with` e Gerenciadores de Contexto

> `with` chama o protocolo de entrada e saída e garante liberação em caminhos normais ou excepcionais. Uso para arquivos, locks, sessões e outros recursos; `contextlib` simplifica gerenciadores customizados.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-modulos-pacotes-e-importacao"></a>

## Módulos, Pacotes e Importação

> Arquivos `.py` formam módulos; pacotes estruturam namespaces. Imports executam inicialização de módulo e são armazenados em cache no processo; importações circulares normalmente indicam organização inadequada.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-name-main"></a>

## `__name__ == "__main__"`

> Esse guard permite executar um arquivo como programa sem iniciar o fluxo principal quando ele é importado como módulo.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-iteraveis-iteradores-e-protocolo-de-iteracao"></a>

## Iteráveis, Iteradores e Protocolo de Iteração

> Um iterável fornece um iterador via `iter()`. O iterador produz elementos com `next()` até `StopIteration`; loops `for` fazem essa coordenação automaticamente.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-slicing-e-unpacking"></a>

## Slicing e Unpacking

> Fatias `inicio:fim:passo` selecionam subsequências; desempacotamento torna atribuições concisas. Em listas, slices produzem outra lista rasa, não cópias profundas de elementos.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-boas-praticas-de-estilo-e-pep-8"></a>

## Boas Práticas de Estilo e PEP 8

> Uso nomes claros, pequenos módulos coesos, APIs previsíveis, formatação automatizada e documentação de decisões. Legibilidade não depende de obedecer mecanicamente uma regra estética.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-01-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Python liga nomes a objetos e combina tipagem dinâmica com contratos verificáveis em runtime. Domino mutabilidade, identidade, exceções, imports e gerenciadores de contexto para escrever serviços previsíveis e testáveis.

[↑ Sumário do tópico](#python-01-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-sumario"></a>

# 02. Programação Orientada a Objetos em Python

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-02-visao-geral)
- [Classe, Instância e `self`](#python-02-classe-instancia-e-self)
- [`__init__` vs `__new__`](#python-02-init-vs-new)
- [Atributos de Instância vs de Classe](#python-02-atributos-de-instancia-vs-de-classe)
- [Encapsulamento, `_nome` e Name Mangling](#python-02-encapsulamento-nome-e-name-mangling)
- [`@property` e Invariantes](#python-02-property-e-invariantes)
- [`@classmethod`, `@staticmethod` e Método de Instância](#python-02-classmethod-staticmethod-e-metodo-de-instancia)
- [Herança e `super()`](#python-02-heranca-e-super)
- [MRO e Herança Múltipla](#python-02-mro-e-heranca-multipla)
- [Polimorfismo e Duck Typing](#python-02-polimorfismo-e-duck-typing)
- [`abc.ABC` e Métodos Abstratos](#python-02-abc-abc-e-metodos-abstratos)
- [Composição e Delegação](#python-02-composicao-e-delegacao)
- [`__repr__` vs `__str__`](#python-02-repr-vs-str)
- [`__eq__` e `__hash__`](#python-02-eq-e-hash)
- [Data Model e Dunder Methods](#python-02-data-model-e-dunder-methods)
- [`__slots__`](#python-02-slots)
- [`dataclasses`](#python-02-dataclasses)
- [`default_factory` em Dataclasses](#python-02-default-factory-em-dataclasses)
- [Entidade vs Value Object](#python-02-entidade-vs-value-object)
- [Validação de Invariantes](#python-02-validacao-de-invariantes)
- [Resumo de Entrevista](#python-02-resumo-de-entrevista)

---

<a id="python-02-visao-geral"></a>

## Visão Geral

> POO em Python organiza estado e comportamento por classes, mas a linguagem também favorece funções e composição. Não crio classes por obrigação: escolho a abstração que explicita regras de negócio e invariantes.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-classe-instancia-e-self"></a>

## Classe, Instância e `self`

> Uma classe é um objeto que define atributos e comportamentos; uma instância guarda estado próprio. `self` é a convenção para o primeiro parâmetro dos métodos de instância.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-init-vs-new"></a>

## `__init__` vs `__new__`

> `__new__` cria a instância e `__init__` inicializa seu estado. Usualmente concentro validação em `__init__` e só personalizo `__new__` quando a criação do objeto realmente exige.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-atributos-de-instancia-vs-de-classe"></a>

## Atributos de Instância vs de Classe

> Atributos da classe podem ser compartilhados entre instâncias. Uma lista declarada na classe como estado mutável pode gerar compartilhamento acidental; para dados por instância, inicializo no objeto.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-encapsulamento-nome-e-name-mangling"></a>

## Encapsulamento, `_nome` e Name Mangling

> `_atributo` sinaliza uso interno por convenção; `__atributo` usa name mangling para evitar colisões em subclasses. Não são barreiras de segurança como `private` do Java.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-property-e-invariantes"></a>

## `@property` e Invariantes

> Properties oferecem uma interface de atributo com validação e computação encapsuladas. Evito criar setters genéricos que permitam estados inválidos; exponho operações de domínio com intenção.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-classmethod-staticmethod-e-metodo-de-instancia"></a>

## `@classmethod`, `@staticmethod` e Método de Instância

> Método de instância recebe `self`; `classmethod` recebe `cls` e pode servir como construtor alternativo; `staticmethod` não recebe nenhum deles. Escolho pela responsabilidade, não por micro-otimização.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-heranca-e-super"></a>

## Herança e `super()`

> Herança especializa comportamento; `super()` delega conforme a MRO, não necessariamente a um pai fixo. Prefiro composição quando não existe relação de substituição consistente.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-mro-e-heranca-multipla"></a>

## MRO e Herança Múltipla

> A Method Resolution Order define a busca por métodos em hierarquias múltiplas. Mixins pequenos e cooperativos podem ser úteis, mas hierarquias profundas dificultam evolução e entendimento.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-polimorfismo-e-duck-typing"></a>

## Polimorfismo e Duck Typing

> Objetos podem colaborar quando disponibilizam o comportamento necessário, independentemente de uma interface herdada. Para contratos estáticos, `Protocol` oferece tipagem estrutural.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-abc-abc-e-metodos-abstratos"></a>

## `abc.ABC` e Métodos Abstratos

> `ABC` e `@abstractmethod` definem contratos nominais em runtime. Uso quando desejo restringir instanciação ou declarar explicitamente responsabilidades compartilhadas.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-composicao-e-delegacao"></a>

## Composição e Delegação

> Composição permite trocar colaboradores sem acoplar classes a hierarquias. Um caso de uso pode receber um repositório por construtor e delegar persistência sem conhecer sua implementação.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-repr-vs-str"></a>

## `__repr__` vs `__str__`

> `__repr__` procura representar o objeto de maneira útil para depuração; `__str__` fornece formato legível ao usuário. Evito incluir dados sensíveis em representações e logs.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-eq-e-hash"></a>

## `__eq__` e `__hash__`

> Objetos iguais devem apresentar hashes compatíveis quando são hashable. Definir igualdade para objetos mutáveis exige cuidado: chaves usadas em sets e dicts precisam manter hash estável.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-data-model-e-dunder-methods"></a>

## Data Model e Dunder Methods

> Métodos especiais como `__len__`, `__iter__`, `__enter__` e `__call__` permitem integração com protocolos da linguagem; só os implemento quando a semântica natural do objeto se aplica.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-slots"></a>

## `__slots__`

> `__slots__` pode restringir atributos e reduzir overhead em certos objetos. Não implica imutabilidade e adiciona trade-offs em herança, introspecção e APIs dinâmicas.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-dataclasses"></a>

## `dataclasses`

> `@dataclass` sintetiza inicialização, representação e comparação conforme opções. `frozen=True` impede atribuições usuais, mas não torna recursivamente imutáveis objetos contidos.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-default-factory-em-dataclasses"></a>

## `default_factory` em Dataclasses

> Para listas e dicionários por instância, uso `field(default_factory=list)` em vez de compartilhar um objeto mutável definido na classe.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-entidade-vs-value-object"></a>

## Entidade vs Value Object

> Entidade mantém identidade de negócio e ciclo de vida; Value Object é comparado por valores e favorece imutabilidade. A escolha deve seguir o domínio, não o framework ORM.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-validacao-de-invariantes"></a>

## Validação de Invariantes

> Um objeto de domínio deve nascer válido e só permitir transições consistentes. Regras como quantidade positiva e estado de pedido não devem depender apenas da validação do endpoint.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-02-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Modelo entidades e comportamentos com invariantes, composição e contratos explícitos. Uso duck typing ou Protocol quando apropriado e entendo que convenções de encapsulamento Python diferem do modelo Java.

[↑ Sumário do tópico](#python-02-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-sumario"></a>

# 03. Collections e Estruturas de Dados

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-03-visao-geral)
- [Big O: Custos Médio e Pior Caso](#python-03-big-o-custos-medio-e-pior-caso)
- [`list` e Arrays Dinâmicos](#python-03-list-e-arrays-dinamicos)
- [`tuple`](#python-03-tuple)
- [`dict`: Hashing, Ordem e Lookup](#python-03-dict-hashing-ordem-e-lookup)
- [`set` e `frozenset`](#python-03-set-e-frozenset)
- [`collections.deque`](#python-03-collections-deque)
- [`Counter`](#python-03-counter)
- [`defaultdict`](#python-03-defaultdict)
- [`heapq` e Filas de Prioridade](#python-03-heapq-e-filas-de-prioridade)
- [`bisect` e Arrays Ordenados](#python-03-bisect-e-arrays-ordenados)
- [Iteração com `enumerate` e `zip`](#python-03-iteracao-com-enumerate-e-zip)
- [Comprehensions](#python-03-comprehensions)
- [Cópia Rasa vs Profunda](#python-03-copia-rasa-vs-profunda)
- [Hashable e Chaves Mutáveis](#python-03-hashable-e-chaves-mutaveis)
- [Ordered Dicts e LRU](#python-03-ordered-dicts-e-lru)
- [`array`, `memoryview` e Buffers](#python-03-array-memoryview-e-buffers)
- [Coleções e Concorrência](#python-03-colecoes-e-concorrencia)
- [Escolha de Estrutura em Produção](#python-03-escolha-de-estrutura-em-producao)
- [Resumo de Entrevista](#python-03-resumo-de-entrevista)

---

<a id="python-03-visao-geral"></a>

## Visão Geral

> Python oferece coleções nativas poderosas. Escolho estrutura por unicidade, ordenação, lookup, consumo de memória, padrão de acesso e concorrência — não só pela sintaxe curta.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-big-o-custos-medio-e-pior-caso"></a>

## Big O: Custos Médio e Pior Caso

> Complexidade assintótica explica crescimento do custo, mas medição real também envolve localidade, alocações e tamanho de objetos. Diferencio custo médio, pior caso e amortizado.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-list-e-arrays-dinamicos"></a>

## `list` e Arrays Dinâmicos

> `list` é uma sequência dinâmica de referências: acesso por índice é O(1), `append` é O(1) amortizado e inserção no início/meio costuma exigir O(n).

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-tuple"></a>

## `tuple`

> Tuplas são sequências imutáveis e apropriadas a registros pequenos ou retornos agrupados. Não garantem imutabilidade profunda dos elementos.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-dict-hashing-ordem-e-lookup"></a>

## `dict`: Hashing, Ordem e Lookup

> `dict` preserva ordem de inserção em Python moderno, com busca e inserção O(1) em média. Exige chaves hashable com contratos estáveis de igualdade e hash.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-set-e-frozenset"></a>

## `set` e `frozenset`

> `set` representa valores únicos e oferece pertencimento eficiente em média; `frozenset` é sua versão imutável e potencialmente hashable, desde que os elementos permitam.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-collections-deque"></a>

## `collections.deque`

> `deque` permite inserção e remoção O(1) nas extremidades; é preferível a `list.pop(0)` em filas grandes. Acesso aleatório intermediário não é sua especialidade.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-counter"></a>

## `Counter`

> `collections.Counter` conta frequências de elementos hashable, útil para métricas simples e análise de dados. Para fluxos enormes, considero agregação incremental e memória.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-defaultdict"></a>

## `defaultdict`

> `defaultdict` inicializa entradas ausentes por uma fábrica definida, simplificando agrupamentos. Uma leitura de chave ausente pode criar estado; isso importa em lógica de cache e concorrência.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-heapq-e-filas-de-prioridade"></a>

## `heapq` e Filas de Prioridade

> Heap mantém acesso ao menor elemento em O(1) e inserção/remoção em O(log n). Empates podem exigir contador para evitar comparações de objetos incompatíveis.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-bisect-e-arrays-ordenados"></a>

## `bisect` e Arrays Ordenados

> `bisect` localiza posição em O(log n) dentro de sequência ordenada, mas inserir em `list` segue O(n) por deslocamento. Não confundo busca rápida com mutação barata.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-iteracao-com-enumerate-e-zip"></a>

## Iteração com `enumerate` e `zip`

> `enumerate` produz índice e valor; `zip` combina iteráveis e normalmente para no menor. `zip(strict=True)` detecta comprimentos diferentes quando essa diferença é um erro.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-comprehensions"></a>

## Comprehensions

> List, set e dict comprehensions expressam mapeamento e filtros de forma clara. Evito expressões aninhadas excessivamente complexas que sacrificam manutenção.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-copia-rasa-vs-profunda"></a>

## Cópia Rasa vs Profunda

> `copy.copy` replica o contêiner mantendo referências aos filhos; `copy.deepcopy` tenta duplicar recursivamente. Cópias profundas podem ser caras e inadequadas a objetos com recursos externos.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-hashable-e-chaves-mutaveis"></a>

## Hashable e Chaves Mutáveis

> Uma lista não pode ser chave de dicionário; uma tupla pode ser, se todos os elementos forem hashable. Alterar atributos usados em `__hash__` compromete consultas futuras.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-ordered-dicts-e-lru"></a>

## Ordered Dicts e LRU

> A ordem do `dict` é de inserção, não um cache LRU automático. `functools.lru_cache` oferece memoização e suas políticas exigem atenção à invalidação e memória.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-array-memoryview-e-buffers"></a>

## `array`, `memoryview` e Buffers

> Arrays tipados e views de buffer podem reduzir cópias em dados binários; seu uso depende de formato, alinhamento e custo de conversão na aplicação.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-colecoes-e-concorrencia"></a>

## Coleções e Concorrência

> A integridade de uma operação composta não é garantida só porque uma operação individual parece atômica. Uso locks, filas ou isolamento de estado conforme o interpretador e o contrato.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-escolha-de-estrutura-em-producao"></a>

## Escolha de Estrutura em Produção

> Para lookup por ID começo com dict; para unicidade, set; para sequência, list; para fila, deque; para prioridade, heap. Ajusto após medir workload e volume.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-03-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Explico escolhas por semântica e custo: dict/set para hashing, list para acesso indexado, deque para extremos e heap para prioridade, mantendo chaves hashable e invariantes estáveis.

[↑ Sumário do tópico](#python-03-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-sumario"></a>

# 04. Python Funcional, Iteradores e Generators

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-04-visao-geral)
- [Funções de Primeira Classe](#python-04-funcoes-de-primeira-classe)
- [`lambda`](#python-04-lambda)
- [Closures](#python-04-closures)
- [Decorators](#python-04-decorators)
- [`map`, `filter` e Comprehension](#python-04-map-filter-e-comprehension)
- [`sorted`, `sort` e `key`](#python-04-sorted-sort-e-key)
- [`any`, `all`, `sum`, `min` e `max`](#python-04-any-all-sum-min-e-max)
- [`itertools`](#python-04-itertools)
- [`functools.reduce`](#python-04-functools-reduce)
- [`functools.partial`](#python-04-functools-partial)
- [`lru_cache` e `cache`](#python-04-lru-cache-e-cache)
- [Generators e `yield`](#python-04-generators-e-yield)
- [Generator Expressions](#python-04-generator-expressions)
- [`yield from`](#python-04-yield-from)
- [Pipeline e Efeitos Colaterais](#python-04-pipeline-e-efeitos-colaterais)
- [Resumo de Entrevista](#python-04-resumo-de-entrevista)

---

<a id="python-04-visao-geral"></a>

## Visão Geral

> Python suporta estilo funcional sem abandonar POO. Componho transformações legíveis, evito efeitos colaterais desnecessários e reconheço o custo de materialização e avaliação preguiçosa.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-funcoes-de-primeira-classe"></a>

## Funções de Primeira Classe

> Funções podem ser atribuídas, recebidas como parâmetros e retornadas. Isso viabiliza estratégias e callbacks sem classes artificiais.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-lambda"></a>

## `lambda`

> Lambdas definem expressões pequenas, úteis como chave de ordenação. Para lógica com nomes e decisões importantes, prefiro `def` pela clareza.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-closures"></a>

## Closures

> Funções internas podem capturar variáveis do escopo envolvente. Capturas tardias em loops podem surpreender; avalio binding explícito quando necessário.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-decorators"></a>

## Decorators

> Decorators recebem e devolvem funções ou outros callables, permitindo logging, autorização ou caching. Uso `functools.wraps` para preservar metadados e evito esconder regras de negócio no wrapper.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-map-filter-e-comprehension"></a>

## `map`, `filter` e Comprehension

> `map` transforma e `filter` seleciona; comprehensions costumam ser mais idiomáticas quando a expressão é simples. Ambos exigem atenção a avaliação preguiçosa e consumo.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-sorted-sort-e-key"></a>

## `sorted`, `sort` e `key`

> `sorted()` produz nova lista; `list.sort()` modifica a original. A chave é calculada por elemento para comparação; escolho ordenação estável quando empates importam.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-any-all-sum-min-e-max"></a>

## `any`, `all`, `sum`, `min` e `max`

> Operações built-in agregam iteráveis de maneira concisa. `any` e `all` fazem curto-circuito; fluxo vazio pode demandar regra de negócio explícita.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-itertools"></a>

## `itertools`

> Ferramentas como `chain`, `islice`, `groupby` e `batched` apoiam pipelines. `groupby` agrupa elementos consecutivos de mesma chave; normalmente preciso ordenar antes para agrupar valores dispersos.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-functools-reduce"></a>

## `functools.reduce`

> Reduce combina itens cumulativamente, mas nem sempre é mais legível que `sum`, `any` ou um loop. Exijo operação associativa quando a ordem não deve alterar o resultado.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-functools-partial"></a>

## `functools.partial`

> `partial` fixa argumentos para criar um callable especializado, útil para callbacks e composição de políticas.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-lru-cache-e-cache"></a>

## `lru_cache` e `cache`

> Cacheia resultados por argumentos hashable. Avalio invalidação, cardinalidade das chaves e possibilidade de reter instâncias ou dados além do esperado.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-generators-e-yield"></a>

## Generators e `yield`

> Funções com `yield` produzem valores sob demanda e preservam estado entre avanços. Não são listas persistentes; podem ser consumidas apenas uma vez.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-generator-expressions"></a>

## Generator Expressions

> Expressões geradoras evitam materialização inicial, úteis para reduzir pico de memória. Encadear geradores não garante processamento econômico se uma etapa materializar tudo.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-yield-from"></a>

## `yield from`

> Delega iteração a outro iterável ou subgerador, simplificando composição e propagação de valores na semântica de geradores.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-pipeline-e-efeitos-colaterais"></a>

## Pipeline e Efeitos Colaterais

> Separei transformação pura de escrita, rede e persistência para facilitar testes. Pipelines com efeitos colaterais dentro de filtros são difíceis de depurar e repetir.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-04-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Uso funções de primeira classe, generators e itertools para fluxos claros; evito materializações desnecessárias e explico quando decorator, closure ou cache realmente melhoram o design.

[↑ Sumário do tópico](#python-04-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-sumario"></a>

# 05. Type Hints, Protocols e Modelagem de Dados

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-05-visao-geral)
- [Annotations e Limitações](#python-05-annotations-e-limitacoes)
- [`typing.Any` vs `object`](#python-05-typing-any-vs-object)
- [`Optional[T]` e União](#python-05-optional-t-e-uniao)
- [Coleções Genéricas](#python-05-colecoes-genericas)
- [`TypeVar` e Generics](#python-05-typevar-e-generics)
- [`Protocol` e Tipagem Estrutural](#python-05-protocol-e-tipagem-estrutural)
- [`TypedDict`](#python-05-typeddict)
- [`Literal` e `Enum`](#python-05-literal-e-enum)
- [`NewType`](#python-05-newtype)
- [`Annotated`](#python-05-annotated)
- [`dataclass` vs Pydantic](#python-05-dataclass-vs-pydantic)
- [Mypy e Pyright](#python-05-mypy-e-pyright)
- [`cast`, `assert` e Type Narrowing](#python-05-cast-assert-e-type-narrowing)
- [`@overload`](#python-05-overload)
- [Interfaces de Biblioteca e `.pyi`](#python-05-interfaces-de-biblioteca-e-pyi)
- [Contrato Externo vs Modelo Interno](#python-05-contrato-externo-vs-modelo-interno)
- [Resumo de Entrevista](#python-05-resumo-de-entrevista)

---

<a id="python-05-visao-geral"></a>

## Visão Geral

> Type hints acrescentam contratos analisáveis por ferramentas, sem converter Python em linguagem estaticamente tipada em runtime. Combino tipagem gradual, validação de fronteira e testes.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-annotations-e-limitacoes"></a>

## Annotations e Limitações

> `nome: str` e `-> int` descrevem intenção para ferramentas e frameworks; Python não rejeita automaticamente valores incompatíveis durante execução.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-typing-any-vs-object"></a>

## `typing.Any` vs `object`

> `Any` desativa parte da checagem estática e propaga incerteza; `object` aceita qualquer objeto, mas exige estreitamento de tipo para operações específicas.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-optional-t-e-uniao"></a>

## `Optional[T]` e União

> `T | None` representa ausência possível; `T | U` permite múltiplas formas. Evito declarar opcionais sem definir a semântica de ausência no domínio.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-colecoes-genericas"></a>

## Coleções Genéricas

> `list[Pedido]`, `dict[str, int]` e `Sequence[T]` comunicam estrutura. Prefiro protocolos abstratos de leitura, como `Mapping` ou `Sequence`, quando mutabilidade não é necessária.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-typevar-e-generics"></a>

## `TypeVar` e Generics

> Variáveis de tipo permitem relacionar entrada e saída sem retornar `Any`. Generics em Python apoiam análise estática e não implicam especialização física de objetos em runtime.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-protocol-e-tipagem-estrutural"></a>

## `Protocol` e Tipagem Estrutural

> Protocol descreve as operações necessárias de um colaborador; classes compatíveis podem satisfazê-lo sem herança nominal. É útil para portas de uma arquitetura hexagonal.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-typeddict"></a>

## `TypedDict`

> Declara o shape esperado de dicionários heterogêneos para a análise estática. Não valida dados externos sozinho nem substitui um modelo de validação.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-literal-e-enum"></a>

## `Literal` e `Enum`

> `Literal` restringe valores conhecidos para análise estática; `Enum` representa constantes com semântica própria em runtime. Uso conforme o contrato de domínio e a necessidade de serialização.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-newtype"></a>

## `NewType`

> Distingue identificadores como `PedidoId` e `ClienteId` para o checker com baixo custo de runtime. Não adiciona validação real nem cria classe de domínio completa.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-annotated"></a>

## `Annotated`

> Associa metadados a um tipo; frameworks como FastAPI aproveitam isso para dependências e validação. O significado dos metadados depende de quem os interpreta.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-dataclass-vs-pydantic"></a>

## `dataclass` vs Pydantic

> Dataclass é apropriada para modelo interno e conveniências de construção; Pydantic atende validação, parsing e serialização de dados externos. Nenhuma decide sozinha o boundary do domínio.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-mypy-e-pyright"></a>

## Mypy e Pyright

> Checadores estáticos encontram erros de tipos antes da execução quando a base está anotada. Endereço primeiro boundaries e contratos críticos, avançando para modo estrito de forma incremental.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-cast-assert-e-type-narrowing"></a>

## `cast`, `assert` e Type Narrowing

> `cast()` informa ao checker um tipo sem validar runtime. `isinstance`, guardas e validação explícita podem estreitar tipo com evidência executável.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-overload"></a>

## `@overload`

> Oferece múltiplas assinaturas estáticas para uma implementação concreta, útil quando o retorno depende do tipo ou da forma dos argumentos. Não cria sobrecarga de método em runtime.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-interfaces-de-biblioteca-e-pyi"></a>

## Interfaces de Biblioteca e `.pyi`

> Stub files descrevem APIs para ferramentas quando o código não tem anotações adequadas; a versão dos stubs deve acompanhar a API efetiva.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-contrato-externo-vs-modelo-interno"></a>

## Contrato Externo vs Modelo Interno

> Validação do request, DTO e entidade do domínio têm responsabilidades distintas. Evito aceitar payload arbitrário e depender apenas de type hints para segurança.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-05-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Tipagem gradual melhora manutenção e refatoração, mas não valida input em runtime. Protocol e abstrações ajudam desacoplamento; Pydantic valida nas fronteiras e mypy/pyright verificam contratos antes do deploy.

[↑ Sumário do tópico](#python-05-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-06-sumario"></a>

# 06. SOLID e Princípios de Design

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-06-visao-geral)
- [S — Single Responsibility Principle (SRP)](#python-06-s-single-responsibility-principle-srp)
- [O — Open/Closed Principle (OCP)](#python-06-o-open-closed-principle-ocp)
- [L — Liskov Substitution Principle (LSP)](#python-06-l-liskov-substitution-principle-lsp)
- [I — Interface Segregation Principle (ISP)](#python-06-i-interface-segregation-principle-isp)
- [D — Dependency Inversion Principle (DIP)](#python-06-d-dependency-inversion-principle-dip)
- [DIP vs Dependency Injection vs IoC](#python-06-dip-vs-dependency-injection-vs-ioc)
- [SOLID na Prática](#python-06-solid-na-pratica)
- [DRY, KISS e YAGNI](#python-06-dry-kiss-e-yagni)
- [Code Smells e Refatoração](#python-06-code-smells-e-refatoracao)
- [Resumo de Entrevista](#python-06-resumo-de-entrevista)
- [SOLID em Python e Duck Typing](#python-06-solid-em-python-e-duck-typing)
- [SRP em Módulos e Funções](#python-06-srp-em-modulos-e-funcoes)

---

<a id="python-06-visao-geral"></a>

## Visão Geral

> SOLID reúne cinco princípios para organizar responsabilidades e dependências. Uso esses princípios para facilitar mudanças e testes, sem criar interfaces e camadas que não resolvam uma necessidade concreta.

[↑ Sumário do tópico](#python-06-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-06-s-single-responsibility-principle-srp"></a>

## S — Single Responsibility Principle (SRP)

> Uma classe deve concentrar responsabilidades que mudam pelo mesmo motivo, associadas ao mesmo grupo de interessados. Por exemplo, separo cálculo de cobrança da geração de relatórios. SRP não significa que toda classe deve ter apenas um método. [Referência](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)

[↑ Sumário do tópico](#python-06-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-06-o-open-closed-principle-ocp"></a>

## O — Open/Closed Principle (OCP)

> Um componente deve permitir extensões sem exigir mudanças recorrentes na lógica estável. Por exemplo, adiciono uma estratégia de desconto por uma nova implementação. Isso não proíbe corrigir código existente nem exige prever toda variação futura. [Referência](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)

[↑ Sumário do tópico](#python-06-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-06-l-liskov-substitution-principle-lsp"></a>

## L — Liskov Substitution Principle (LSP)

> Uma implementação deve poder substituir seu tipo base preservando o contrato esperado. Não deve exigir precondições mais fortes nem prometer resultados mais fracos; se uma conta não permite saque, não deve implementar um contrato que garante essa operação. [Referência](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)

[↑ Sumário do tópico](#python-06-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-06-i-interface-segregation-principle-isp"></a>

## I — Interface Segregation Principle (ISP)

> Um cliente deve depender de contratos focados no que utiliza. Separar consulta e escrita pode evitar implementações obrigadas a oferecer operações sem sentido; o objetivo é coesão do contrato, não multiplicar interfaces artificialmente. [Referência](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)

[↑ Sumário do tópico](#python-06-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-06-d-dependency-inversion-principle-dip"></a>

## D — Dependency Inversion Principle (DIP)

> Regras de alto nível e detalhes devem depender de abstrações estáveis, cujos contratos não dependem da infraestrutura. Um caso de uso pode depender de `PagamentoGateway`, enquanto o adaptador HTTP implementa esse contrato. [Referência](https://blog.cleancoder.com/uncle-bob/2020/10/18/Solid-Relevance.html)

[↑ Sumário do tópico](#python-06-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-06-dip-vs-dependency-injection-vs-ioc"></a>

## DIP vs Dependency Injection vs IoC

> DIP orienta a direção das dependências no código. Dependency Injection fornece dependências de fora do objeto. IoC é a inversão mais ampla do controle; usar injeção com uma classe concreta não garante, sozinho, o cumprimento de DIP.

[↑ Sumário do tópico](#python-06-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-06-solid-na-pratica"></a>

## SOLID na Prática

> Em um checkout, separo cálculo, persistência e pagamento por responsabilidade, uso estratégias para variações e contratos coerentes para integrações. Avalio o resultado pela facilidade de testar e alterar regras, não pela quantidade de classes criadas.

[↑ Sumário do tópico](#python-06-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-06-dry-kiss-e-yagni"></a>

## DRY, KISS e YAGNI

> DRY evita duplicar conhecimento de negócio; KISS favorece soluções compreensíveis; YAGNI evita construir funcionalidades apenas imaginadas. Código parecido nem sempre representa a mesma regra: abstrair cedo demais também pode aumentar acoplamento.

[↑ Sumário do tópico](#python-06-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-06-code-smells-e-refatoracao"></a>

## Code Smells e Refatoração

> Code smells são sinais de possível problema, como classes com muitas responsabilidades, métodos longos e regras duplicadas. Refatoração melhora a estrutura preservando comportamento observável; uso testes e mudanças pequenas para reduzir o risco.

[↑ Sumário do tópico](#python-06-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-06-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Aplico SOLID para tornar responsabilidades e contratos claros e reduzir o impacto das mudanças. Combino esses princípios com simplicidade e testes, criando abstrações onde existem variações reais ou fronteiras relevantes.

[↑ Sumário do tópico](#python-06-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-06-solid-em-python-e-duck-typing"></a>

## SOLID em Python e Duck Typing

> Uso protocolos, objetos pequenos e composição quando há variabilidade real. A ausência de interfaces nominais obrigatórias não elimina o valor de contratos coesos.

[↑ Sumário do tópico](#python-06-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-06-srp-em-modulos-e-funcoes"></a>

## SRP em Módulos e Funções

> Em Python, o limite de responsabilidade pode estar no módulo, na função ou na classe. Não traduzo automaticamente classes Java em uma classe Python para cada arquivo.

[↑ Sumário do tópico](#python-06-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-sumario"></a>

# 07. Design Patterns em Python

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-07-visao-geral)
- [Categorias GoF](#python-07-categorias-gof)
- [Factory Method — Criacional](#python-07-factory-method-criacional)
- [Abstract Factory — Criacional](#python-07-abstract-factory-criacional)
- [Builder — Criacional](#python-07-builder-criacional)
- [Prototype — Criacional](#python-07-prototype-criacional)
- [Singleton — Criacional](#python-07-singleton-criacional)
- [Adapter — Estrutural](#python-07-adapter-estrutural)
- [Bridge — Estrutural](#python-07-bridge-estrutural)
- [Composite — Estrutural](#python-07-composite-estrutural)
- [Decorator — Estrutural](#python-07-decorator-estrutural)
- [Facade — Estrutural](#python-07-facade-estrutural)
- [Flyweight — Estrutural](#python-07-flyweight-estrutural)
- [Proxy — Estrutural](#python-07-proxy-estrutural)
- [Chain of Responsibility — Comportamental](#python-07-chain-of-responsibility-comportamental)
- [Command — Comportamental](#python-07-command-comportamental)
- [Interpreter — Comportamental](#python-07-interpreter-comportamental)
- [Iterator — Comportamental](#python-07-iterator-comportamental)
- [Mediator — Comportamental](#python-07-mediator-comportamental)
- [Memento — Comportamental](#python-07-memento-comportamental)
- [Observer — Comportamental](#python-07-observer-comportamental)
- [State — Comportamental](#python-07-state-comportamental)
- [Strategy — Comportamental](#python-07-strategy-comportamental)
- [Template Method — Comportamental](#python-07-template-method-comportamental)
- [Visitor — Comportamental](#python-07-visitor-comportamental)
- [Strategy vs State vs Template Method](#python-07-strategy-vs-state-vs-template-method)
- [Adapter vs Decorator vs Proxy vs Facade](#python-07-adapter-vs-decorator-vs-proxy-vs-facade)
- [Repository, DTO e Mapper](#python-07-repository-dto-e-mapper)
- [Padrão de Projeto vs Padrão Arquitetural](#python-07-padrao-de-projeto-vs-padrao-arquitetural)
- [Quando Não Aplicar um Padrão](#python-07-quando-nao-aplicar-um-padrao)
- [Resumo de Entrevista](#python-07-resumo-de-entrevista)
- [Strategy com Callables e Protocol](#python-07-strategy-com-callables-e-protocol)
- [Decorator Python vs Padrão Decorator](#python-07-decorator-python-vs-padrao-decorator)
- [Dependency Injection sem Container](#python-07-dependency-injection-sem-container)

---

<a id="python-07-visao-geral"></a>

## Visão Geral

> Design Patterns descrevem soluções recorrentes para problemas de design, com contexto e consequências. Não são código pronto nem obrigações: primeiro identifico o problema e depois avalio se o padrão simplifica a solução.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-categorias-gof"></a>

## Categorias GoF

> Os 23 padrões GoF dividem-se em criacionais, estruturais e comportamentais. Os primeiros organizam criação de objetos; os estruturais, sua composição; os comportamentais, a distribuição de responsabilidades e a colaboração entre objetos.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-factory-method-criacional"></a>

## Factory Method — Criacional

> Define um método de criação que subclasses podem especializar para escolher o produto concreto. Separo a lógica que usa o objeto da escolha de sua implementação; uma função estática que instancia objetos não é, por isso só, o padrão GoF.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-abstract-factory-criacional"></a>

## Abstract Factory — Criacional

> Cria famílias de objetos relacionados por meio de contratos comuns. É útil quando componentes precisam variar juntos e permanecer compatíveis, mas adicionar um novo tipo de produto exige alterar as fábricas da família.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-builder-criacional"></a>

## Builder — Criacional

> Separa a construção de um objeto complexo de sua representação. Ajuda com muitos parâmetros e etapas, mantendo a validação no resultado final; para objetos simples, um construtor claro pode bastar.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-prototype-criacional"></a>

## Prototype — Criacional

> Cria objetos a partir de cópias de um modelo existente. É útil quando a configuração inicial é cara ou complexa; preciso distinguir cópia rasa e profunda para não compartilhar estado mutável por acidente.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-singleton-criacional"></a>

## Singleton — Criacional

> Controla a criação de uma única instância dentro de determinado escopo. Em Python, singletons baseados em módulo, fábricas e caches têm trade-offs de estado global e testes; em processos separados não garantem unicidade distribuída.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-adapter-estrutural"></a>

## Adapter — Estrutural

> Traduz uma interface existente para o contrato esperado pelo cliente. Uso, por exemplo, para integrar um provedor externo a uma porta da aplicação, mantendo detalhes de tradução fora das regras de negócio.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-bridge-estrutural"></a>

## Bridge — Estrutural

> Separa uma abstração de sua implementação para que ambas possam variar independentemente. É útil quando duas dimensões gerariam muitas subclasses combinadas; o custo é introduzir uma camada adicional de delegação.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-composite-estrutural"></a>

## Composite — Estrutural

> Representa estruturas em árvore e permite tratar folhas e conjuntos pelo mesmo contrato. Serve para hierarquias como pastas e arquivos, desde que as operações comuns façam sentido para os dois tipos.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-decorator-estrutural"></a>

## Decorator — Estrutural

> Acrescenta comportamento envolvendo um objeto com outro do mesmo contrato. Permite combinar recursos como compressão e criptografia sem muitas subclasses, mas cadeias extensas podem dificultar depuração e entendimento da ordem.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-facade-estrutural"></a>

## Facade — Estrutural

> Oferece uma interface simples para um subsistema mais complexo. Reduz o conhecimento exigido dos clientes, mas deve permanecer focada para não concentrar regras e virar uma classe responsável por tudo.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-flyweight-estrutural"></a>

## Flyweight — Estrutural

> Compartilha estado intrínseco entre muitos objetos para economizar memória, mantendo o estado variável fora do objeto compartilhado. Só compensa quando a quantidade de instâncias e a economia justificam a complexidade.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-proxy-estrutural"></a>

## Proxy — Estrutural

> Representa outro objeto e controla acesso, podendo acrescentar segurança, lazy loading ou interceptação. Em Python, wrappers, objetos delegadores e descriptors podem desempenhar esse papel, respeitando os contratos de origem.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-chain-of-responsibility-comportamental"></a>

## Chain of Responsibility — Comportamental

> Encadeia handlers que processam ou encaminham uma solicitação. É útil para filtros e validações; a ordem e a regra de interrupção devem ficar explícitas para evitar caminhos inesperados.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-command-comportamental"></a>

## Command — Comportamental

> Encapsula uma solicitação como objeto, separando quem pede de quem executa. Facilita filas, histórico e agendamento; suporte a desfazer exige lógica própria e não aparece automaticamente ao adotar o padrão.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-interpreter-comportamental"></a>

## Interpreter — Comportamental

> Representa regras de uma linguagem e interpreta expressões dessa gramática. Pode servir para linguagens simples de regras, mas gramáticas grandes normalmente exigem ferramentas de parsing mais apropriadas.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-iterator-comportamental"></a>

## Iterator — Comportamental

> Permite percorrer uma estrutura sem expor sua representação interna. Em Python, os protocolos `__iter__` e `__next__` fornecem iteração, inclusive para generators com `yield`.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-mediator-comportamental"></a>

## Mediator — Comportamental

> Centraliza a coordenação entre objetos que interagiriam diretamente de forma complexa. Reduz dependências entre participantes, mas o mediador precisa de limites claros para não acumular toda a lógica.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-memento-comportamental"></a>

## Memento — Comportamental

> Captura um estado para permitir restauração sem expor detalhes internos ao cliente. É útil em histórico ou desfazer, mas snapshots grandes têm custo de memória e não revertem automaticamente efeitos externos.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-observer-comportamental"></a>

## Observer — Comportamental

> Permite notificar interessados quando um objeto muda ou emite um evento. Reduz dependência direta dos observadores, mas exige tratar falhas e ciclo de vida; não garante assincronia, persistência nem entrega distribuída.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-state-comportamental"></a>

## State — Comportamental

> Encapsula comportamentos que variam conforme o estado do objeto. Ajuda em fluxos como pedido pendente, pago e cancelado, tornando transições explícitas e reduzindo condicionais espalhados.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-strategy-comportamental"></a>

## Strategy — Comportamental

> Encapsula algoritmos intercambiáveis sob um mesmo contrato, como cálculo de frete ou desconto. Favorece composição e OCP; o cliente ou uma política escolhe qual estratégia usar.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-template-method-comportamental"></a>

## Template Method — Comportamental

> Define o esqueleto de um algoritmo em uma classe base e permite especializar etapas. Reutiliza o fluxo por herança, mas cria acoplamento com a superclasse; Strategy oferece variação por composição.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-visitor-comportamental"></a>

## Visitor — Comportamental

> Separa operações de uma estrutura estável de tipos, como uma árvore sintática. Facilita adicionar operações, mas novos tipos de elemento normalmente exigem atualizar os visitantes; uso quando essa troca favorece o domínio.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-strategy-vs-state-vs-template-method"></a>

## Strategy vs State vs Template Method

> Strategy varia o algoritmo escolhido; State varia o comportamento conforme o estado e suas transições; Template Method preserva um fluxo e permite especializar etapas por herança. Escolho pela natureza da variação, não pela semelhança das classes.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-adapter-vs-decorator-vs-proxy-vs-facade"></a>

## Adapter vs Decorator vs Proxy vs Facade

> Adapter traduz contratos; Decorator acrescenta comportamento; Proxy controla acesso; Facade simplifica um subsistema. Todos podem usar delegação, mas sua intenção é diferente.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-repository-dto-e-mapper"></a>

## Repository, DTO e Mapper

> Repository organiza consultas conforme o domínio; DTO transporta dados entre fronteiras; Mapper converte representações. São padrões comuns em backends Python com FastAPI e SQLAlchemy, sem pertencer ao catálogo GoF.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-padrao-de-projeto-vs-padrao-arquitetural"></a>

## Padrão de Projeto vs Padrão Arquitetural

> Padrões de projeto tratam colaborações entre componentes e objetos. Padrões arquiteturais tratam organização e propriedades mais amplas; Saga e Outbox resolvem coordenação e consistência distribuída e são estudados nos capítulos de arquitetura e microsserviços.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-quando-nao-aplicar-um-padrao"></a>

## Quando Não Aplicar um Padrão

> Evito um padrão quando ele cria mais indireção do que resolve ou antecipa variações sem evidência. A solução deve continuar fácil de explicar, testar e modificar; conhecer o nome não substitui avaliar o custo.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Escolho padrões pelo problema: Strategy para algoritmos variáveis, Builder para construção complexa, Adapter para integração e Proxy para interceptação. Explico sempre o ganho, o custo e por que uma solução mais simples seria ou não suficiente.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-strategy-com-callables-e-protocol"></a>

## Strategy com Callables e Protocol

> Em Python posso representar estratégia com funções, objetos callables ou Protocol. Escolho classe quando estado, múltiplas operações ou identidade justificam a estrutura.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-decorator-python-vs-padrao-decorator"></a>

## Decorator Python vs Padrão Decorator

> O `@decorator` da linguagem envolve callables, mas não é automaticamente o padrão GoF para objetos; comparo a intenção, contrato e efeitos da composição.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-07-dependency-injection-sem-container"></a>

## Dependency Injection sem Container

> Passar colaboradores pelo construtor ou parâmetro de função é DI. Não preciso de framework para aplicar DIP e testar adaptadores.

[↑ Sumário do tópico](#python-07-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-sumario"></a>

# 08. CPython: Memória e Garbage Collection

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-08-visao-geral)
- [Stack de Execução e Frames](#python-08-stack-de-execucao-e-frames)
- [Reference Counting](#python-08-reference-counting)
- [Garbage Collector Cíclico](#python-08-garbage-collector-ciclico)
- [Gerações e Mudanças de Versão](#python-08-geracoes-e-mudancas-de-versao)
- [`__del__` e Finalização](#python-08-del-e-finalizacao)
- [`weakref`](#python-08-weakref)
- [Memory Leak em Python](#python-08-memory-leak-em-python)
- [`tracemalloc`](#python-08-tracemalloc)
- [`gc` e `sys.getsizeof`](#python-08-gc-e-sys-getsizeof)
- [RSS vs Memória de Objetos](#python-08-rss-vs-memoria-de-objetos)
- [Alocador de CPython e Fragmentação](#python-08-alocador-de-cpython-e-fragmentacao)
- [GIL: Escopo e Limites](#python-08-gil-escopo-e-limites)
- [Free-threaded CPython](#python-08-free-threaded-cpython)
- [Profiling de Memória em Produção](#python-08-profiling-de-memoria-em-producao)
- [Processos Workers e Cópia de Memória](#python-08-processos-workers-e-copia-de-memoria)
- [Resumo de Entrevista](#python-08-resumo-de-entrevista)

---

<a id="python-08-visao-geral"></a>

## Visão Geral

> O modelo de memória depende da implementação. Em CPython, reference counting e um coletor de ciclos convivem com o alocador de memória; a aplicação ainda pode reter objetos indefinidamente por referências válidas.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-stack-de-execucao-e-frames"></a>

## Stack de Execução e Frames

> Frames representam chamadas e ambientes locais; objetos Python geralmente são gerenciados pelo heap do interpretador. Não equiparo diretamente a organização de frames de CPython à Stack e Heap da JVM.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-reference-counting"></a>

## Reference Counting

> CPython acompanha referências para liberar muitos objetos quando deixam de ser usados. Não deduzo ciclos de vida portáveis a partir disso, pois outras implementações podem adotar estratégias diferentes.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-garbage-collector-ciclico"></a>

## Garbage Collector Cíclico

> O coletor adicional encontra grupos de objetos que se referenciam sem acesso externo. A coleta de ciclos é distinta da liberação imediata por redução de contagem de referências.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-geracoes-e-mudancas-de-versao"></a>

## Gerações e Mudanças de Versão

> Estratégias geracionais e parâmetros do módulo `gc` podem mudar entre versões de Python; confirmo a documentação do runtime em uso antes de fazer tuning.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-del-e-finalizacao"></a>

## `__del__` e Finalização

> Destrutores dependem da finalização do objeto e não oferecem liberação determinística de recursos externos. Prefiro `with` e métodos explícitos de fechamento.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-weakref"></a>

## `weakref`

> Referências fracas permitem observar objetos sem estender sua vida útil; são úteis em caches ou mapas auxiliares quando o ciclo de vida pertence a outro componente.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-memory-leak-em-python"></a>

## Memory Leak em Python

> Um vazamento lógico ocorre quando estruturas continuam referenciando objetos sem necessidade, como caches sem limite, callbacks retidos, listas globais e sessões não encerradas.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-tracemalloc"></a>

## `tracemalloc`

> Rastreia alocações Python e compara snapshots para localizar crescimento. Nem toda memória nativa de extensões está visível; complemento com métricas de RSS.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-gc-e-sys-getsizeof"></a>

## `gc` e `sys.getsizeof`

> `gc` permite observação e ações de coleta. `getsizeof` mede tamanho raso do objeto no interpretador, não toda a árvore de objetos alcançáveis.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-rss-vs-memoria-de-objetos"></a>

## RSS vs Memória de Objetos

> RSS considera páginas residentes do processo, incluindo bibliotecas, extensões nativas e alocadores. Queda de objetos vivos não garante diminuição imediata do RSS.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-alocador-de-cpython-e-fragmentacao"></a>

## Alocador de CPython e Fragmentação

> CPython reutiliza arenas e pools para objetos pequenos. Parte da memória liberada pode permanecer no processo para reutilização, exigindo distinguir cache do alocador de retenção indevida.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-gil-escopo-e-limites"></a>

## GIL: Escopo e Limites

> No build padrão de CPython com GIL, uma única thread executa bytecode Python por vez. O GIL não impede race conditions nem necessariamente bloqueia paralelismo de extensões nativas que o liberam.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-free-threaded-cpython"></a>

## Free-threaded CPython

> Existem builds opcionais de CPython sem GIL a partir da linha 3.13. Compatibilidade de extensões, características de desempenho e versão instalada importam; não assumo que toda instalação é free-threaded.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-profiling-de-memoria-em-producao"></a>

## Profiling de Memória em Produção

> Investigo tendência de RSS, cardinalidade de coleções, caches, tamanho de filas e perfil de alocação. Reproduzo carga e comparo snapshots antes de aumentar recursos.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-processos-workers-e-copia-de-memoria"></a>

## Processos Workers e Cópia de Memória

> Cada worker de processo possui seu próprio espaço de endereçamento; multiplicar workers normalmente multiplica custos de memória, embora mecanismos de copy-on-write possam compartilhar algumas páginas inicialmente.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-08-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Em CPython, referências e GC cíclico gerenciam objetos, mas vazamentos por retenção ainda existem. Diagnostico com métricas, tracemalloc e recursos nativos, diferenciando GIL, RSS e memória alcançável.

[↑ Sumário do tópico](#python-08-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-sumario"></a>

# 09. Concorrência e Paralelismo em Python

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-09-visao-geral)
- [Concorrência vs Paralelismo](#python-09-concorrencia-vs-paralelismo)
- [I/O-bound vs CPU-bound](#python-09-i-o-bound-vs-cpu-bound)
- [`threading.Thread`](#python-09-threading-thread)
- [`ThreadPoolExecutor`](#python-09-threadpoolexecutor)
- [`ProcessPoolExecutor`](#python-09-processpoolexecutor)
- [`multiprocessing` e Start Methods](#python-09-multiprocessing-e-start-methods)
- [`concurrent.futures.Future`](#python-09-concurrent-futures-future)
- [Race Condition](#python-09-race-condition)
- [`Lock` e `RLock`](#python-09-lock-e-rlock)
- [`Semaphore` e `Condition`](#python-09-semaphore-e-condition)
- [`queue.Queue`](#python-09-queue-queue)
- [Deadlock, Livelock e Starvation](#python-09-deadlock-livelock-e-starvation)
- [Contexto por Thread e `contextvars`](#python-09-contexto-por-thread-e-contextvars)
- [Processo vs Thread vs Corrotina](#python-09-processo-vs-thread-vs-corrotina)
- [Benchmark e Throughput](#python-09-benchmark-e-throughput)
- [Resumo de Entrevista](#python-09-resumo-de-entrevista)

---

<a id="python-09-visao-geral"></a>

## Visão Geral

> Concorrência em Python pode usar threads, processos ou corrotinas. Seleciono o mecanismo pelo tipo de trabalho, necessidade de paralelismo, custo de coordenação e características do runtime.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-concorrencia-vs-paralelismo"></a>

## Concorrência vs Paralelismo

> Concorrência intercala múltiplas tarefas; paralelismo executa ao mesmo tempo em recursos distintos. I/O-bound e CPU-bound demandam estratégias diferentes.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-i-o-bound-vs-cpu-bound"></a>

## I/O-bound vs CPU-bound

> Em I/O-bound, tempo é gasto aguardando rede, disco ou banco; threads e asyncio podem elevar throughput. Em CPU-bound puro no CPython convencional, múltiplos processos costumam contornar a restrição do GIL.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-threading-thread"></a>

## `threading.Thread`

> Threads compartilham memória e são adequadas a I/O bloqueante ou bibliotecas que liberam o GIL. Compartilhar estado exige sincronização mesmo quando o runtime usa GIL.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-threadpoolexecutor"></a>

## `ThreadPoolExecutor`

> Gerencia um número limitado de threads e devolve Futures. Dimensiono executores para evitar filas e chamadas bloqueantes ilimitadas.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-processpoolexecutor"></a>

## `ProcessPoolExecutor`

> Distribui trabalho CPU-bound entre processos; argumentos e resultados precisam ser transferidos, muitas vezes por serialização. Avalio custo de startup, cópia e uso de memória.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-multiprocessing-e-start-methods"></a>

## `multiprocessing` e Start Methods

> Processos oferecem paralelismo e isolamento de memória. Comportamentos de `spawn`, `fork` e `forkserver` variam por plataforma e versão; não uso fork cegamente após inicializar threads.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-concurrent-futures-future"></a>

## `concurrent.futures.Future`

> Representa execução pendente e disponibiliza resultado, erro e solicitação de cancelamento. Cancelar não garante interromper uma tarefa que já está executando.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-race-condition"></a>

## Race Condition

> Uma sequência ler-calcular-gravar pode perder atualizações quando tarefas compartilham estado. A presença do GIL não torna uma operação de negócio composta atômica.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-lock-e-rlock"></a>

## `Lock` e `RLock`

> Lock protege regiões críticas; RLock permite aquisição reentrante pela mesma thread. Mantenho escopo curto e ordem consistente para evitar deadlocks.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-semaphore-e-condition"></a>

## `Semaphore` e `Condition`

> Semaphore limita concorrência por permissões; Condition permite esperar por um estado compartilhado com coordenação. São úteis para pools e produção-consumo.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-queue-queue"></a>

## `queue.Queue`

> Fornece uma fila thread-safe com limites e operações bloqueantes, útil para desacoplar produtores e consumidores dentro do mesmo processo.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-deadlock-livelock-e-starvation"></a>

## Deadlock, Livelock e Starvation

> Deadlock é espera circular; livelock é atividade sem progresso; starvation é privação prolongada. Investigo lock order, fairness, fila e contenção.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-contexto-por-thread-e-contextvars"></a>

## Contexto por Thread e `contextvars`

> `threading.local()` armazena estado por thread; `contextvars` permite contexto lógico apropriado a tarefas assíncronas. Não confundo contexto de request com variáveis globais.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-processo-vs-thread-vs-corrotina"></a>

## Processo vs Thread vs Corrotina

> Processo isola memória e usa mais recursos; thread compartilha espaço; corrotina é agendada cooperativamente em um event loop. Escolho conforme carga e isolamento, não apenas pela quantidade de tarefas.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-benchmark-e-throughput"></a>

## Benchmark e Throughput

> Meço concorrência com carga real, CPU, RSS, p95/p99, saturação de pools e limites do banco. Mais workers ou threads pode piorar a latência sob contenção.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-09-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Escolho threads para I/O bloqueante, processos para CPU em CPython convencional e asyncio para I/O cooperativo. Protejo estado compartilhado, imponho limites e meço antes de escalar.

[↑ Sumário do tópico](#python-09-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-sumario"></a>

# 10. Asyncio e Programação Assíncrona

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-10-visao-geral)
- [Corrotina e `async def`](#python-10-corrotina-e-async-def)
- [`await`](#python-10-await)
- [Event Loop](#python-10-event-loop)
- [`asyncio.run()`](#python-10-asyncio-run)
- [`create_task()`](#python-10-create-task)
- [`gather()`](#python-10-gather)
- [`TaskGroup`](#python-10-taskgroup)
- [Cancelamento e `CancelledError`](#python-10-cancelamento-e-cancellederror)
- [`asyncio.timeout()` e Deadlines](#python-10-asyncio-timeout-e-deadlines)
- [`asyncio.Semaphore`](#python-10-asyncio-semaphore)
- [`asyncio.Queue`](#python-10-asyncio-queue)
- [`asyncio.Lock`](#python-10-asyncio-lock)
- [`to_thread()` e I/O Bloqueante](#python-10-to-thread-e-i-o-bloqueante)
- [`asyncio` vs Threads](#python-10-asyncio-vs-threads)
- [Async HTTP e Connection Pool](#python-10-async-http-e-connection-pool)
- [Async Database Drivers](#python-10-async-database-drivers)
- [Structured Concurrency e Falhas](#python-10-structured-concurrency-e-falhas)
- [Resumo de Entrevista](#python-10-resumo-de-entrevista)

---

<a id="python-10-visao-geral"></a>

## Visão Geral

> `asyncio` implementa concorrência cooperativa por event loop, corrotinas e tarefas. Escala bem conexões I/O-bound quando o caminho de execução realmente é não bloqueante.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-corrotina-e-async-def"></a>

## Corrotina e `async def`

> Chamar função `async def` cria um objeto corrotina; o corpo executa quando aguardado ou agendado. Criar a corrotina sem `await` não dispara automaticamente o trabalho.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-await"></a>

## `await`

> `await` suspende a corrotina até uma operação awaitable progredir, permitindo ao loop avançar outras tarefas. Só ajuda quando o trabalho aguarda de modo cooperativo.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-event-loop"></a>

## Event Loop

> O loop coordena readiness, callbacks e tarefas. Código CPU-intensivo e bibliotecas bloqueantes dentro dele impedem avanço das demais corrotinas.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-asyncio-run"></a>

## `asyncio.run()`

> Estabelece e fecha o loop de uma execução principal. Não devo chamá-lo de dentro de um loop que já está ativo, como em rotas assíncronas de framework.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-create-task"></a>

## `create_task()`

> Agenda corrotinas para execução concorrente no loop. Gerencio a referência e o ciclo de vida; tarefas soltas sem observação de falhas dificultam diagnóstico.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-gather"></a>

## `gather()`

> Aguarda múltiplas awaitables e reúne resultados. A política de propagação de erros e cancelamento difere de uma estrutura com tarefas irmãs sob gerenciamento conjunto.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-taskgroup"></a>

## `TaskGroup`

> Gerencia tarefas dentro de um escopo estruturado: aguarda conclusão e coordena cancelamentos em falhas. Favoreço quando subtarefas pertencem à mesma operação de negócio.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-cancelamento-e-cancellederror"></a>

## Cancelamento e `CancelledError`

> Cancelamento é cooperativo, entregue em pontos de suspensão. Faço limpeza com `try/finally` e não suprimo `CancelledError` sem intenção clara.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-asyncio-timeout-e-deadlines"></a>

## `asyncio.timeout()` e Deadlines

> Limito o tempo total da operação e considero o budget entre chamadas. Timeout local não garante cancelamento de efeitos externos, que podem ter sido confirmados remotamente.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-asyncio-semaphore"></a>

## `asyncio.Semaphore`

> Limita quantidade de operações assíncronas simultâneas, especialmente sobre APIs externas e bancos. Criar milhares de tarefas não aumenta capacidade do serviço dependente.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-asyncio-queue"></a>

## `asyncio.Queue`

> Coordena produtores e consumidores async, inclusive com `maxsize` para backpressure local. Dimensiono consumidores conforme throughput e latência observados.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-asyncio-lock"></a>

## `asyncio.Lock`

> Protege seções críticas entre tarefas do mesmo loop. Não substitui lock distribuído e não deve ser usado como mecanismo geral de sincronização entre threads.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-to-thread-e-i-o-bloqueante"></a>

## `to_thread()` e I/O Bloqueante

> `asyncio.to_thread` desloca funções bloqueantes para thread, evitando travar o event loop; não transforma automaticamente CPU-bound Python puro em paralelismo efetivo no build com GIL.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-asyncio-vs-threads"></a>

## `asyncio` vs Threads

> Corrotinas são cooperativas e eficientes em I/O async; threads integram APIs bloqueantes. Misturar as duas exige atenção a comunicação thread-safe e ao encerramento dos recursos.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-async-http-e-connection-pool"></a>

## Async HTTP e Connection Pool

> Clientes assíncronos compartilham conexões e respeitam timeout, pool e limitação de concorrência. Criar cliente novo a cada request geralmente desperdiça conexões.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-async-database-drivers"></a>

## Async Database Drivers

> Driver e ORM precisam oferecer I/O async real. Usar `async def` ao redor de uma chamada síncrona de banco continua bloqueando o loop.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-structured-concurrency-e-falhas"></a>

## Structured Concurrency e Falhas

> Organizo tarefas filhas com escopos claros, propago falhas e libero recursos. Evito fire-and-forget para operações críticas que exigem confirmação, repetição ou recuperação.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-10-resumo-de-entrevista"></a>

## Resumo de Entrevista

> `asyncio` é I/O cooperativo: `await` cede execução, TaskGroup organiza subtarefas e semáforos impõem capacidade. Bloqueios no loop, cancelamentos e timeouts exigem tratamento explícito.

[↑ Sumário do tópico](#python-10-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-sumario"></a>

# 11. Build, Ambientes e Dependências Python

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-11-visao-geral)
- [Instalação do Python e Seleção de Versão](#python-11-instalacao-do-python-e-selecao-de-versao)
- [`venv`](#python-11-venv)
- [`pip` e Índices](#python-11-pip-e-indices)
- [`requirements.txt` vs `pyproject.toml`](#python-11-requirements-txt-vs-pyproject-toml)
- [`[project]`, `[build-system]` e `[tool]`](#python-11-project-build-system-e-tool)
- [Extras vs Dependency Groups](#python-11-extras-vs-dependency-groups)
- [`uv` e Gerenciamento de Projeto](#python-11-uv-e-gerenciamento-de-projeto)
- [Poetry e Alternativas](#python-11-poetry-e-alternativas)
- [Lockfile e Reprodutibilidade](#python-11-lockfile-e-reprodutibilidade)
- [Wheel vs sdist](#python-11-wheel-vs-sdist)
- [`__init__.py`, Pacotes e Estrutura `src/`](#python-11-init-py-pacotes-e-estrutura-src)
- [Imports Absolutos, Relativos e Circularidade](#python-11-imports-absolutos-relativos-e-circularidade)
- [Entrypoints e CLI](#python-11-entrypoints-e-cli)
- [Variáveis de Ambiente e Segredos](#python-11-variaveis-de-ambiente-e-segredos)
- [Ferramentas de Qualidade](#python-11-ferramentas-de-qualidade)
- [Dependências Vulneráveis e SBOM](#python-11-dependencias-vulneraveis-e-sbom)
- [Resumo de Entrevista](#python-11-resumo-de-entrevista)

---

<a id="python-11-visao-geral"></a>

## Visão Geral

> Gestão de ambiente e build Python envolve interpretador, isolamento de dependências, metadados, distribuição e reprodutibilidade. Produção requer versões controladas e segurança de supply chain.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-instalacao-do-python-e-selecao-de-versao"></a>

## Instalação do Python e Seleção de Versão

> Fixar a família e a versão do interpretador reduz divergências entre dev, CI e produção. Bibliotecas nativas podem impor restrições específicas de plataforma e ABI.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-venv"></a>

## `venv`

> Ambientes virtuais isolam packages instalados por projeto. Eles não substituem a necessidade de declarar dependências nem de reconstruir ambientes de forma reproduzível.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-pip-e-indices"></a>

## `pip` e Índices

> `pip` instala pacotes de índices ou artefatos locais. Estabeleço controle de origem e versões para não depender implicitamente de releases futuros ou índices não confiáveis.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-requirements-txt-vs-pyproject-toml"></a>

## `requirements.txt` vs `pyproject.toml`

> Requirements descrevem conjuntos de instalação frequentemente usados com pip. `pyproject.toml` centraliza metadados, sistema de build, dependências e configurações de ferramentas.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-project-build-system-e-tool"></a>

## `[project]`, `[build-system]` e `[tool]`

> `project` define metadados e dependências; `build-system` determina ferramentas de construção; `tool` guarda configurações específicas. Nem todo projeto precisa publicar pacote, mas deve saber como é executado.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-extras-vs-dependency-groups"></a>

## Extras vs Dependency Groups

> Extras representam recursos opcionais distribuídos ao consumidor; dependency groups podem organizar dev/test/lint sem virar metadados de dependência do pacote publicado.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-uv-e-gerenciamento-de-projeto"></a>

## `uv` e Gerenciamento de Projeto

> `uv` pode gerenciar Python, ambientes, resolução e execução conforme a configuração do projeto. Adoto uma ferramenta principal, registro lock e confiro comportamento exato da versão usada.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-poetry-e-alternativas"></a>

## Poetry e Alternativas

> Poetry oferece gestão de dependências e empacotamento; Hatch, PDM e pip também atendem cenários distintos. Escolho por compatibilidade com o pipeline e políticas da equipe.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-lockfile-e-reprodutibilidade"></a>

## Lockfile e Reprodutibilidade

> Um lock registra resolução concreta de dependências. Separo constraints de versões flutuantes e reproduzo instalação em CI, inclusive dependências transitivas.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-wheel-vs-sdist"></a>

## Wheel vs sdist

> Wheel é artefato de distribuição previamente construído; sdist inclui fontes para build. Dependências nativas e diferenças entre arquiteturas afetam instalação e compatibilidade.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-init-py-pacotes-e-estrutura-src"></a>

## `__init__.py`, Pacotes e Estrutura `src/`

> Organização de imports e pacotes torna o código importável como instalação real. Layout `src/` ajuda a detectar dependências acidentais do diretório atual.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-imports-absolutos-relativos-e-circularidade"></a>

## Imports Absolutos, Relativos e Circularidade

> Imports absolutos melhoram rastreabilidade em projetos grandes; relativos podem ser adequados dentro do pacote. Circularidades frequentes indicam boundaries mal definidos.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-entrypoints-e-cli"></a>

## Entrypoints e CLI

> Declaro comandos executáveis via `project.scripts` quando construo CLI. Para serviços, separo bootstrap, configuração, inicialização e implementação do domínio.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-variaveis-de-ambiente-e-segredos"></a>

## Variáveis de Ambiente e Segredos

> Configurações externas não devem ser hardcoded. `.env` ajuda localmente, mas não substitui gerenciamento de segredos em produção.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-ferramentas-de-qualidade"></a>

## Ferramentas de Qualidade

> Ruff formata e faz lint conforme configuração; mypy/pyright analisam tipos; pytest executa testes. Uso as mesmas configurações localmente e na pipeline.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-dependencias-vulneraveis-e-sbom"></a>

## Dependências Vulneráveis e SBOM

> Monitoro CVEs, atualizações, hashes e origem de artefatos. Um lock ajuda repetibilidade, mas não garante automaticamente que a cadeia de suprimentos esteja segura.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-11-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Entrego aplicações com ambiente isolado, `pyproject.toml`, lock, build e pipeline reprodutíveis. Entendo diferenças entre package, import, wheel, índice e dependência de desenvolvimento.

[↑ Sumário do tópico](#python-11-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-sumario"></a>

# 12. SQL e Bancos Relacionais

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral — SQL](#python-12-visao-geral-sql)
- [SELECT, INSERT, UPDATE e DELETE](#python-12-select-insert-update-e-delete)
- [Primary Key, Unique e Foreign Key](#python-12-primary-key-unique-e-foreign-key)
- [NOT NULL, CHECK e Integridade](#python-12-not-null-check-e-integridade)
- [Normalização vs Desnormalização](#python-12-normalizacao-vs-desnormalizacao)
- [NULL](#python-12-null)
- [Ordem Lógica de uma Consulta](#python-12-ordem-logica-de-uma-consulta)
- [INNER JOIN, LEFT JOIN, RIGHT JOIN e FULL JOIN](#python-12-inner-join-left-join-right-join-e-full-join)
- [WHERE vs HAVING](#python-12-where-vs-having)
- [GROUP BY](#python-12-group-by)
- [CTE vs Subquery](#python-12-cte-vs-subquery)
- [Window Functions](#python-12-window-functions)
- [EXISTS vs IN vs JOIN](#python-12-exists-vs-in-vs-join)
- [NOT IN e NULL](#python-12-not-in-e-null)
- [UNION vs UNION ALL](#python-12-union-vs-union-all)
- [DELETE vs TRUNCATE vs DROP](#python-12-delete-vs-truncate-vs-drop)
- [Prepared Statements e SQL Injection](#python-12-prepared-statements-e-sql-injection)
- [Índices](#python-12-indices)
- [Índice Composto e Ordem das Colunas](#python-12-indice-composto-e-ordem-das-colunas)
- [Índice de Cobertura, Parcial e Funcional](#python-12-indice-de-cobertura-parcial-e-funcional)
- [Seletividade e Cardinalidade](#python-12-seletividade-e-cardinalidade)
- [Sargabilidade](#python-12-sargabilidade)
- [Quando um índice pode não ser usado](#python-12-quando-um-indice-pode-nao-ser-usado)
- [Execution Plan](#python-12-execution-plan)
- [Otimização de Query em Produção](#python-12-otimizacao-de-query-em-producao)
- [Paginação OFFSET vs Keyset](#python-12-paginacao-offset-vs-keyset)
- [N+1 Queries](#python-12-n-1-queries)
- [ACID](#python-12-acid)
- [Isolation Levels](#python-12-isolation-levels)
- [Dirty Read, Non-repeatable Read e Phantom Read](#python-12-dirty-read-non-repeatable-read-e-phantom-read)
- [Lost Update e Write Skew](#python-12-lost-update-e-write-skew)
- [MVCC](#python-12-mvcc)
- [PostgreSQL vs Oracle: Isolamento](#python-12-postgresql-vs-oracle-isolamento)
- [Locks](#python-12-locks)
- [Optimistic vs Pessimistic Concurrency](#python-12-optimistic-vs-pessimistic-concurrency)
- [Atualização Condicional Atômica](#python-12-atualizacao-condicional-atomica)
- [Deadlock no Banco](#python-12-deadlock-no-banco)
- [Transação Longa](#python-12-transacao-longa)
- [Connection Pool e Saturação](#python-12-connection-pool-e-saturacao)
- [Resumo de Entrevista](#python-12-resumo-de-entrevista)

---

<a id="python-12-visao-geral-sql"></a>

## Visão Geral — SQL

> SQL é a linguagem usada para consultar e manipular dados em bancos relacionais. Em uma entrevista, eu separo o tema em modelagem, consultas, índices, transações e concorrência. Para performance, não olho apenas a query: considero cardinalidade, seletividade, índices, volume de dados e o plano de execução.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-select-insert-update-e-delete"></a>

## SELECT, INSERT, UPDATE e DELETE

> `SELECT` consulta dados; `INSERT` adiciona linhas; `UPDATE` altera; `DELETE` remove. Defino filtros e limites transacionais antes de executar mudanças, considerando constraints, índices e quantas linhas realmente serão afetadas.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-primary-key-unique-e-foreign-key"></a>

## Primary Key, Unique e Foreign Key

> Primary Key identifica unicamente cada linha e não permite `NULL`. Uma constraint `UNIQUE` também garante unicidade, mas sua semântica de `NULL` pode variar entre bancos. Foreign Key garante integridade referencial entre tabelas. Eu uso constraints no banco porque consistência não deve depender apenas da aplicação.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-not-null-check-e-integridade"></a>

## NOT NULL, CHECK e Integridade

> `NOT NULL` exige preenchimento; `CHECK` restringe valores conforme uma expressão; chaves e unicidade completam a integridade. Regras que o banco consegue garantir devem permanecer protegidas contra acessos concorrentes e outros clientes além da aplicação.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-normalizacao-vs-desnormalizacao"></a>

## Normalização vs Desnormalização

> Normalização reduz redundância e anomalias de atualização, normalmente separando entidades e relacionamentos em tabelas coerentes. Desnormalização duplica ou pré-calcula dados para reduzir joins ou acelerar leitura. Eu começo normalizado e desnormalizo apenas quando existe um requisito de performance comprovado, aceitando o custo adicional de consistência.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-null"></a>

## NULL

> `NULL` representa ausência ou valor desconhecido e não deve ser comparado com `=`. Uso `IS NULL` e `IS NOT NULL`. Também lembro que lógica com `NULL` é ternária — true, false e unknown — e isso afeta filtros, joins e agregações.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-ordem-logica-de-uma-consulta"></a>

## Ordem Lógica de uma Consulta

> Como modelo de raciocínio, considero FROM/JOIN, WHERE, GROUP BY, HAVING, SELECT e ORDER BY, seguidos da limitação do resultado. O otimizador pode escolher outra ordem física, preservando a semântica da consulta.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-inner-join-left-join-right-join-e-full-join"></a>

## INNER JOIN, LEFT JOIN, RIGHT JOIN e FULL JOIN

> `INNER JOIN` retorna somente linhas com correspondência entre os lados. `LEFT JOIN` preserva todas as linhas da tabela à esquerda e retorna `NULL` quando não existe correspondência à direita. `RIGHT JOIN` faz o inverso e `FULL JOIN` preserva linhas dos dois lados. Na prática, escolho o JOIN de acordo com a semântica do relacionamento, não por performance presumida.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-where-vs-having"></a>

## WHERE vs HAVING

> `WHERE` filtra linhas antes da agregação. `HAVING` filtra grupos depois do `GROUP BY`. Sempre que o filtro não depende de uma função agregadora, prefiro colocá-lo no `WHERE` para reduzir o conjunto de dados antes da agregação.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-group-by"></a>

## GROUP BY

> `GROUP BY` agrupa linhas para aplicar funções agregadoras como `COUNT`, `SUM`, `AVG`, `MIN` e `MAX`. Um cuidado importante é que colunas selecionadas fora de agregações precisam respeitar as regras de agrupamento do banco. Em produção também observo o custo de ordenar ou agregar grandes volumes.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-cte-vs-subquery"></a>

## CTE vs Subquery

> CTE melhora legibilidade e permite estruturar consultas complexas, além de suportar recursão em bancos que oferecem CTE recursiva. Subquery pode ser suficiente para casos pequenos. Não assumo que CTE é automaticamente mais rápida ou mais lenta: o otimizador pode tratá-las de maneiras diferentes, então performance deve ser validada no plano.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-window-functions"></a>

## Window Functions

> Window Functions calculam valores sobre um conjunto relacionado de linhas sem colapsar o resultado como um `GROUP BY`. Exemplos são `ROW_NUMBER`, `RANK`, `LAG`, `LEAD` e agregações com `OVER`. São muito úteis para ranking, comparação com linha anterior e acumulados sem recorrer a várias subqueries.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-exists-vs-in-vs-join"></a>

## EXISTS vs IN vs JOIN

> `EXISTS`, `IN` e `JOIN` expressam necessidades diferentes e otimizadores modernos podem gerar planos equivalentes em alguns casos. Uso `EXISTS` quando quero testar existência, `JOIN` quando preciso combinar dados e `IN` quando a semântica de pertencimento é clara. Para performance, comparo os planos em vez de aplicar uma regra universal.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-not-in-e-null"></a>

## NOT IN e NULL

> Se o conjunto de `NOT IN` contiver `NULL`, comparações podem resultar em unknown e excluir linhas inesperadamente. Para testar ausência, `NOT EXISTS` costuma expressar melhor a intenção, desde que a correlação e a regra para valores nulos estejam corretas.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-union-vs-union-all"></a>

## UNION vs UNION ALL

> `UNION` combina resultados removendo duplicados; `UNION ALL` apenas concatena os conjuntos. Como remover duplicados exige trabalho adicional, prefiro `UNION ALL` quando duplicidade é aceitável ou impossível pela lógica do domínio.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-delete-vs-truncate-vs-drop"></a>

## DELETE vs TRUNCATE vs DROP

> `DELETE` remove linhas e permite filtro com `WHERE`. `TRUNCATE` remove os dados da tabela de forma mais ampla e costuma ter comportamento e logging diferentes conforme o banco. `DROP` remove o próprio objeto da estrutura. Detalhes de transação, identidade e rollback variam entre SGBDs, então evito generalizar além disso.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-prepared-statements-e-sql-injection"></a>

## Prepared Statements e SQL Injection

> Para evitar SQL Injection, não concateno entrada do usuário na query. Uso parâmetros através de prepared statements ou APIs que façam binding corretamente. Isso separa dado de código SQL; validação de entrada continua importante, mas não substitui parametrização.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-indices"></a>

## Índices

> Índice é uma estrutura auxiliar que reduz o custo de localizar dados, normalmente evitando varrer toda a tabela. O trade-off é que índices ocupam espaço e aumentam o custo de `INSERT`, `UPDATE` e `DELETE`, porque também precisam ser mantidos. Portanto, não considero 'mais índices' automaticamente melhor.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-indice-composto-e-ordem-das-colunas"></a>

## Índice Composto e Ordem das Colunas

> Em um índice composto, a ordem das colunas é importante. Eu defino a ordem com base nos padrões reais de filtro, join e ordenação. Em índices B-tree, consultas tendem a aproveitar melhor os prefixos iniciais do índice; por isso um índice em `(cliente_id, status, data)` não equivale a um índice em `(status, cliente_id, data)`.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-indice-de-cobertura-parcial-e-funcional"></a>

## Índice de Cobertura, Parcial e Funcional

> Um índice pode incluir dados suficientes para reduzir acesso à tabela, cobrir apenas parte das linhas ou indexar expressões, quando o banco permite. A utilidade depende da consulta e do SGBD; validação por plano real é indispensável.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-seletividade-e-cardinalidade"></a>

## Seletividade e Cardinalidade

> Cardinalidade descreve quantos valores distintos existem em uma coluna e seletividade indica o quanto um predicado reduz o conjunto de linhas. Índices tendem a ser mais úteis quando a consulta elimina uma parcela relevante dos dados. Para colunas com poucos valores distintos, o otimizador pode preferir um scan dependendo da distribuição e do volume.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-sargabilidade"></a>

## Sargabilidade

> Uma condição sargable permite que o banco use um índice de forma eficiente para localizar o intervalo de dados. Por exemplo, aplicar uma função diretamente sobre a coluna filtrada pode impedir ou reduzir o uso do índice. Eu tento escrever o predicado de forma que a coluna indexada permaneça pesquisável e valido o resultado no execution plan.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-quando-um-indice-pode-nao-ser-usado"></a>

## Quando um índice pode não ser usado

> Um índice pode não ser escolhido quando o filtro retorna grande parte da tabela, quando existe conversão ou função sobre a coluna indexada, quando a estatística está ruim ou quando outro plano é estimado como mais barato. Por isso eu confirmo com o plano de execução em vez de assumir que criar um índice garante seu uso.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-execution-plan"></a>

## Execution Plan

> O execution plan mostra como o otimizador pretende executar uma consulta: scans, seeks, joins, ordenações, estimativas de linhas e custos relativos. Para investigar query lenta, eu comparo estimativa com quantidade real de linhas, verifico scans inesperados, joins caros, sorts, índices ausentes e estatísticas. O objetivo é encontrar a causa, não apenas 'forçar índice'.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-otimizacao-de-query-em-producao"></a>

## Otimização de Query em Produção

> Eu começo pela evidência: latência, frequência, volume retornado e execution plan. Depois verifico filtros, joins, índices, cardinalidade, estatísticas, quantidade de linhas lidas versus retornadas e possíveis operações O(n) desnecessárias. Só então considero reescrever a query, criar ou alterar índice, paginar ou remodelar o acesso.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-paginacao-offset-vs-keyset"></a>

## Paginação OFFSET vs Keyset

> Paginação com `OFFSET/LIMIT` é simples, mas offsets altos podem exigir que o banco percorra e descarte muitas linhas e podem gerar inconsistência visual quando os dados mudam entre páginas. Keyset pagination usa a última chave ordenada como cursor e costuma escalar melhor para grandes volumes, desde que exista ordenação estável e índice adequado.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-n-1-queries"></a>

## N+1 Queries

> N+1 acontece quando uma consulta inicial carrega uma coleção de registros e depois são executadas consultas adicionais para cada item. O efeito é aumento de round-trips e latência. Eu detecto por logs ou APM e resolvo alterando a estratégia de carregamento, usando joins, batch ou queries específicas conforme o caso.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-acid"></a>

## ACID

> ACID significa Atomicidade, Consistência, Isolamento e Durabilidade. Atomicidade garante que a transação seja aplicada por inteiro ou revertida; consistência preserva regras e constraints; isolamento controla interferência entre transações concorrentes; durabilidade garante que um commit confirmado sobreviva a falhas conforme as garantias do banco.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-isolation-levels"></a>

## Isolation Levels

> Os níveis clássicos são `READ UNCOMMITTED`, `READ COMMITTED`, `REPEATABLE READ` e `SERIALIZABLE`. Quanto maior o isolamento, menor a possibilidade de fenômenos como dirty read, non-repeatable read e phantom read, mas potencialmente maior o custo de concorrência. O comportamento exato depende do banco e do uso de locks ou MVCC.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-dirty-read-non-repeatable-read-e-phantom-read"></a>

## Dirty Read, Non-repeatable Read e Phantom Read

> Dirty read lê alterações ainda não confirmadas. Non-repeatable read observa mudança na mesma linha entre leituras; phantom read observa mudança no conjunto de linhas que satisfaz um filtro. A prevenção depende do isolamento e da implementação do banco.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-lost-update-e-write-skew"></a>

## Lost Update e Write Skew

> Lost update ocorre quando uma escrita sobrescreve indevidamente outra. Write skew pode ocorrer quando transações alteram linhas diferentes após ler uma condição comum, violando uma regra conjunta; versão em uma única linha não resolve toda invariante entre várias linhas.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-mvcc"></a>

## MVCC

> MVCC, Multi-Version Concurrency Control, mantém versões de linhas para permitir que leituras e escritas concorram com menos bloqueio em determinados níveis de isolamento. Isso melhora concorrência, mas não elimina conflitos, locks ou necessidade de entender o isolamento específico do banco.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-postgresql-vs-oracle-isolamento"></a>

## PostgreSQL vs Oracle: Isolamento

> Ambos usam READ COMMITTED como padrão em configurações usuais, mas as garantias não são intercambiáveis. PostgreSQL trata READ UNCOMMITTED como READ COMMITTED e oferece REPEATABLE READ; Oracle não oferece todos os mesmos níveis. Verifico snapshots, conflitos e necessidade de retry na documentação do banco. [PostgreSQL](https://www.postgresql.org/docs/current/transaction-iso.html) · [Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/26/cncpt/data-concurrency-and-consistency.html)

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-locks"></a>

## Locks

> Locks coordenam acesso concorrente aos dados para preservar consistência. Podem existir locks de leitura e escrita em diferentes granularidades conforme o banco. O problema aparece quando transações seguram locks por tempo demais, aumentando espera, latência e risco de deadlock. Por isso mantenho transações curtas e acesso aos recursos em ordem previsível.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-optimistic-vs-pessimistic-concurrency"></a>

## Optimistic vs Pessimistic Concurrency

> Concorrência otimista assume que conflitos são raros e detecta alteração concorrente, por exemplo usando uma coluna de versão. Concorrência pessimista bloqueia o recurso antes da alteração. Eu prefiro otimista quando conflitos são raros e pessimista quando o custo de conflito é alto e a contenção esperada justifica o bloqueio.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-atualizacao-condicional-atomica"></a>

## Atualização Condicional Atômica

> Posso proteger uma regra com uma única atualização, como decrementar estoque apenas quando ele é suficiente. Verifico a quantidade de linhas afetadas e uso transação e constraints conforme necessário, evitando o intervalo vulnerável entre consultar e atualizar separadamente.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-deadlock-no-banco"></a>

## Deadlock no Banco

> Deadlock ocorre quando transações criam uma espera circular por locks. O banco normalmente detecta o ciclo e aborta uma das transações. Para reduzir a ocorrência, mantenho transações curtas, adquiro recursos em ordem consistente, garanto bons índices para evitar bloquear linhas desnecessárias e preparo a aplicação para retry da transação vítima quando apropriado.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-transacao-longa"></a>

## Transação Longa

> Transações longas mantêm recursos, versões ou locks por mais tempo, aumentando contenção e consumo do banco. Em serviços backend, procuro manter o boundary transacional apenas ao redor do trabalho que realmente precisa ser atômico e evito chamadas remotas lentas dentro da transação sempre que possível.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-connection-pool-e-saturacao"></a>

## Connection Pool e Saturação

> O pool reutiliza conexões e limita quantas operações acessam o banco simultaneamente. Observo espera por conexão, transações longas e capacidade do banco; aumentar o pool pode apenas transferir a sobrecarga para o servidor.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-12-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Em bancos relacionais, combino modelagem e constraints com consultas, índices e transações. Escolho isolamento e controle de concorrência conforme as invariantes. Para performance, parto de queries reais e planos de execução, considerando volume, estatísticas, locks e custo de leitura e escrita.

[↑ Sumário do tópico](#python-12-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-sumario"></a>

# 13. HTTP e APIs REST

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-13-visao-geral)
- [Recursos, URIs e Contratos](#python-13-recursos-uris-e-contratos)
- [GET, POST, PUT, PATCH e DELETE](#python-13-get-post-put-patch-e-delete)
- [Métodos Seguros e Idempotentes](#python-13-metodos-seguros-e-idempotentes)
- [Códigos de Status HTTP](#python-13-codigos-de-status-http)
- [Headers, Content-Type e Accept](#python-13-headers-content-type-e-accept)
- [REST Stateless](#python-13-rest-stateless)
- [Paginação, Filtros e Ordenação](#python-13-paginacao-filtros-e-ordenacao)
- [Erros e Problem Details](#python-13-erros-e-problem-details)
- [OpenAPI e Documentação de Contrato](#python-13-openapi-e-documentacao-de-contrato)
- [Idempotency Key em APIs](#python-13-idempotency-key-em-apis)
- [Cache HTTP, ETag e If-Match](#python-13-cache-http-etag-e-if-match)
- [TLS e Segurança de Transporte](#python-13-tls-e-seguranca-de-transporte)
- [DNS, Conexões e Timeouts](#python-13-dns-conexoes-e-timeouts)
- [REST, RPC e gRPC](#python-13-rest-rpc-e-grpc)
- [Resumo de Entrevista](#python-13-resumo-de-entrevista)

---

<a id="python-13-visao-geral"></a>

## Visão Geral

> HTTP define a troca de mensagens entre cliente e servidor; REST é um estilo arquitetural baseado em recursos e interface uniforme. Uma API JSON sobre HTTP não se torna REST apenas por usar URLs e controllers.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-recursos-uris-e-contratos"></a>

## Recursos, URIs e Contratos

> Modelo recursos com identificadores estáveis e contratos claros de entrada, saída e erro. Evito expor diretamente entidades de persistência, pois isso acopla a API ao schema e pode revelar dados indevidos.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-get-post-put-patch-e-delete"></a>

## GET, POST, PUT, PATCH e DELETE

> `GET` consulta; `POST` submete processamento ou criação; `PUT` cria ou substitui a representação do recurso alvo; `PATCH` aplica alterações parciais; `DELETE` solicita remoção. O significado deve seguir o contrato HTTP e a regra de negócio.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-metodos-seguros-e-idempotentes"></a>

## Métodos Seguros e Idempotentes

> Métodos seguros, como `GET`, não solicitam mudança de estado de negócio. `PUT` e `DELETE` são idempotentes por semântica, embora respostas possam variar; `POST` e `PATCH` podem receber mecanismos de idempotência, mas ela não é garantida pelo método.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-codigos-de-status-http"></a>

## Códigos de Status HTTP

> `200` indica sucesso, `201` criação e `204` sucesso sem corpo; `400` entrada inválida, `401` ausência de autenticação válida, `403` proibição e `404` recurso não encontrado. `409` sinaliza conflito, `429` excesso de requisições e `5xx` falhas do servidor.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-headers-content-type-e-accept"></a>

## Headers, Content-Type e Accept

> `Content-Type` informa o formato enviado; `Accept` indica formatos aceitos na resposta. Headers também transportam autenticação, cache e rastreamento; trato esses valores como parte do contrato e não como substitutos da autorização.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-rest-stateless"></a>

## REST Stateless

> Uma requisição deve trazer o contexto necessário para ser entendida sem depender de uma sessão conversacional no servidor. Stateless não significa ausência de banco nem de estado de negócio; significa reduzir dependência de contexto de interação armazenado entre chamadas.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-paginacao-filtros-e-ordenacao"></a>

## Paginação, Filtros e Ordenação

> Defino limites de página, filtros permitidos e ordenação estável. Para grandes volumes, cursores podem ser mais eficientes que offsets; um desempate único, como `id`, evita ambiguidades quando vários registros compartilham a mesma data.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-erros-e-problem-details"></a>

## Erros e Problem Details

> Padronizo respostas de erro com código, descrição útil e identificador de rastreamento. Problem Details oferece um formato comum para APIs HTTP; evito retornar stack traces, detalhes internos ou informações sensíveis.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-openapi-e-documentacao-de-contrato"></a>

## OpenAPI e Documentação de Contrato

> OpenAPI descreve rotas, parâmetros, schemas, respostas e mecanismos de segurança. A documentação deve evoluir com a API e apoiar validações; um contrato publicado sem testes de compatibilidade pode ficar desatualizado.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-idempotency-key-em-apis"></a>

## Idempotency Key em APIs

> Uma chave idempotente permite reconhecer tentativas da mesma operação. Registro chave, resultado e vínculo com a requisição de forma atômica, definindo expiração e tratamento de chamadas concorrentes; reutilizar a chave com outro conteúdo deve ser detectado.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-cache-http-etag-e-if-match"></a>

## Cache HTTP, ETag e If-Match

> `Cache-Control` orienta reutilização de respostas e `ETag` identifica uma versão da representação. `If-None-Match` ajuda na revalidação; `If-Match` condiciona uma alteração à versão esperada e pode impedir atualizações perdidas.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-tls-e-seguranca-de-transporte"></a>

## TLS e Segurança de Transporte

> TLS protege confidencialidade e integridade em trânsito e autentica o servidor por certificados. Isso não substitui autorização, validação de entrada nem proteção dos dados depois que chegam à aplicação.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-dns-conexoes-e-timeouts"></a>

## DNS, Conexões e Timeouts

> Uma chamada remota pode gastar tempo em resolução DNS, conexão, TLS e leitura. Configuro limites adequados a cada cliente e um prazo total da operação, porque limitar só a leitura pode deixar outras etapas sem proteção.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-rest-rpc-e-grpc"></a>

## REST, RPC e gRPC

> REST enfatiza recursos e semântica HTTP; RPC expressa chamadas de operações. gRPC oferece contratos tipados e streaming com Protobuf em seu uso comum, mas exige avaliar suporte dos clientes, infraestrutura e estratégia de evolução do contrato.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-13-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Projeto APIs com recursos, contratos, status e erros consistentes. Considero autenticação, autorização, idempotência, paginação, compatibilidade e timeouts desde o início, porque uma chamada HTTP também é uma fronteira sujeita a falhas.

[↑ Sumário do tópico](#python-13-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-sumario"></a>

# 14. FastAPI: Framework HTTP e Dependency Injection

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-14-visao-geral)
- [ASGI vs WSGI](#python-14-asgi-vs-wsgi)
- [`FastAPI()` e `APIRouter`](#python-14-fastapi-e-apirouter)
- [Path Operations](#python-14-path-operations)
- [Parâmetros de Path e Query](#python-14-parametros-de-path-e-query)
- [Request Body](#python-14-request-body)
- [`response_model`](#python-14-response-model)
- [Dependency Injection com `Depends`](#python-14-dependency-injection-com-depends)
- [Dependências com `yield`](#python-14-dependencias-com-yield)
- [`Annotated`](#python-14-annotated)
- [`def` vs `async def`](#python-14-def-vs-async-def)
- [Middlewares](#python-14-middlewares)
- [Exception Handlers e `HTTPException`](#python-14-exception-handlers-e-httpexception)
- [Lifespan e Ciclo de Inicialização](#python-14-lifespan-e-ciclo-de-inicializacao)
- [OpenAPI e Swagger UI](#python-14-openapi-e-swagger-ui)
- [BackgroundTasks](#python-14-backgroundtasks)
- [WebSockets](#python-14-websockets)
- [Deployment Uvicorn, Workers e Proxies](#python-14-deployment-uvicorn-workers-e-proxies)
- [Resumo de Entrevista](#python-14-resumo-de-entrevista)

---

<a id="python-14-visao-geral"></a>

## Visão Geral

> FastAPI é framework ASGI orientado a type hints, validação por modelos e OpenAPI. Entendo o ciclo HTTP, a execução sync/async e a separação entre controller, aplicação e domínio.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-asgi-vs-wsgi"></a>

## ASGI vs WSGI

> ASGI admite conexões assíncronas e protocolos como HTTP e WebSocket; WSGI tem interface síncrona tradicional. O protocolo do servidor não torna dependências internas automaticamente não bloqueantes.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-fastapi-e-apirouter"></a>

## `FastAPI()` e `APIRouter`

> O app recebe rotas e configurações; `APIRouter` organiza endpoints por domínio ou versão. Evito concentrar toda a regra de negócio nos handlers.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-path-operations"></a>

## Path Operations

> Decorators como `@router.get` associam método HTTP e path a uma função. Declaro status, respostas e contratos explicitamente e evito revelar entidades internas.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-parametros-de-path-e-query"></a>

## Parâmetros de Path e Query

> Path identifica recursos; query transporta filtros, paginação e opções. Type hints e validações documentam e verificam parâmetros que chegam à aplicação.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-request-body"></a>

## Request Body

> Modelos de entrada descrevem payload esperado e produzem mensagens de validação. Não aceito um `dict` irrestrito quando um schema de negócio pode ser explicitado.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-response-model"></a>

## `response_model`

> O modelo de saída filtra e valida dados retornados conforme o contrato, além de compor documentação. Útil para não expor por acidente campos internos ou sensíveis.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-dependency-injection-com-depends"></a>

## Dependency Injection com `Depends`

> Dependências declaram fornecedores de recursos ou serviços por parâmetro. Facilita substituição em testes e separação de infraestrutura, sem exigir que toda regra conheça FastAPI.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-dependencias-com-yield"></a>

## Dependências com `yield`

> Permitem fornecer sessão ou cliente e executar finalização ao terminar o escopo definido pelo framework. Uso context managers e tratamento de exceptions de modo correto.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-annotated"></a>

## `Annotated`

> Combina o tipo Python com metadados de dependência e validação, favorecendo assinaturas verificáveis e documentação consistente.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-def-vs-async-def"></a>

## `def` vs `async def`

> Funções `def` executam normalmente via threadpool no processamento do FastAPI; `async def` executa no event loop. Dentro de async, não faço chamadas bloqueantes diretamente.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-middlewares"></a>

## Middlewares

> Interceptam requisição e resposta para questões transversais, como correlação e métricas. Cuidado com ordem, propagação de exceptions e leitura do corpo da requisição.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-exception-handlers-e-httpexception"></a>

## Exception Handlers e `HTTPException`

> Mapeio falhas de domínio para erros HTTP com payload consistente. Não espalho `HTTPException` pela lógica de negócio, para manter desacoplamento.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-lifespan-e-ciclo-de-inicializacao"></a>

## Lifespan e Ciclo de Inicialização

> Crio e encerro clientes, pools e recursos durante o lifespan da aplicação. Evito inicialização pesada no momento de import dos módulos.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-openapi-e-swagger-ui"></a>

## OpenAPI e Swagger UI

> FastAPI gera OpenAPI a partir de anotações e modelos. Documentação gerada é útil, mas contratos críticos também merecem testes e política de evolução.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-backgroundtasks"></a>

## BackgroundTasks

> Executa tarefas após o fluxo de resposta no contexto da aplicação, mas não oferece durabilidade de fila distribuída. Para jobs críticos, prefiro broker e workers dedicados.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-websockets"></a>

## WebSockets

> Suportam comunicação bidirecional persistente. Exigem controle de autenticação, desconexões, backpressure e escala entre instâncias.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-deployment-uvicorn-workers-e-proxies"></a>

## Deployment Uvicorn, Workers e Proxies

> Uvicorn atende ASGI; múltiplos processos aumentam paralelismo/isolamento, com custo de memória. Ajusto proxy headers, timeouts, health e graceful shutdown com cuidado.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-14-resumo-de-entrevista"></a>

## Resumo de Entrevista

> FastAPI recebe HTTP via ASGI, usa type hints e validação de schemas e injeta recursos com Depends. Handler fica fino; domínio não depende do framework e I/O async precisa ser realmente não bloqueante.

[↑ Sumário do tópico](#python-14-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-sumario"></a>

# 15. Pydantic: Validação e Serialização

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-15-visao-geral)
- [`BaseModel`](#python-15-basemodel)
- [`model_validate()` e `model_dump()`](#python-15-model-validate-e-model-dump)
- [`model_dump_json()`](#python-15-model-dump-json)
- [Coerção vs Strict Mode](#python-15-coercao-vs-strict-mode)
- [`Field` e Constraints](#python-15-field-e-constraints)
- [`field_validator`](#python-15-field-validator)
- [`model_validator`](#python-15-model-validator)
- [`ConfigDict`](#python-15-configdict)
- [`from_attributes`](#python-15-from-attributes)
- [Alias de Entrada e Saída](#python-15-alias-de-entrada-e-saida)
- [Campos Opcionais e Defaults](#python-15-campos-opcionais-e-defaults)
- [Modelos de Entrada vs Saída](#python-15-modelos-de-entrada-vs-saida)
- [Modelos Genéricos e Submodelos](#python-15-modelos-genericos-e-submodelos)
- [`TypeAdapter`](#python-15-typeadapter)
- [Erros de Validação](#python-15-erros-de-validacao)
- [Resumo de Entrevista](#python-15-resumo-de-entrevista)

---

<a id="python-15-visao-geral"></a>

## Visão Geral

> Pydantic valida e serializa dados com base em modelos tipados. Aplico-o principalmente em fronteiras externas e mantenho invariantes de domínio nos objetos responsáveis.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-basemodel"></a>

## `BaseModel`

> Declara campos e tipos esperados; a construção interpreta dados conforme políticas de coerção e validação. Anotação sozinha não define todas as regras da API.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-model-validate-e-model-dump"></a>

## `model_validate()` e `model_dump()`

> `model_validate` constrói/valida a partir de dados compatíveis; `model_dump` produz representações Python com opções de inclusão e exclusão. Serialização JSON pode exigir modo apropriado.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-model-dump-json"></a>

## `model_dump_json()`

> Converte modelo para texto JSON conforme configuração e serializadores. Evito usar `.json()` de APIs antigas sem conferir versão e migração.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-coercao-vs-strict-mode"></a>

## Coerção vs Strict Mode

> Pydantic pode converter tipos de entrada; strict mode restringe coerções inesperadas. Defino estratégia por contrato, especialmente para identificadores, números e campos sensíveis.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-field-e-constraints"></a>

## `Field` e Constraints

> `Field` declara limites, aliases e descrição. Validação de request não dispensa constraints no banco nem regras de negócio resistentes a concorrência.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-field-validator"></a>

## `field_validator`

> Valida ou normaliza campos específicos em estágios suportados. Evito validações com chamadas remotas e efeitos colaterais que tornam o parsing imprevisível.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-model-validator"></a>

## `model_validator`

> Verifica regras envolvendo múltiplos campos. Para invariantes que dependem de persistência ou concorrência, a validação do schema é insuficiente.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-configdict"></a>

## `ConfigDict`

> Centraliza configurações como tratamento de campos extras e modelos baseados em atributos. Escolho regras explícitas para rejeitar ou ignorar atributos desconhecidos.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-from-attributes"></a>

## `from_attributes`

> Permite derivar modelo de objetos com atributos, útil com ORM. Defino claramente quais campos serão acessados para evitar I/O implícito ou N+1.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-alias-de-entrada-e-saida"></a>

## Alias de Entrada e Saída

> Aliases ajudam compatibilidade com contratos externos. Não permito nomes conflitantes ou renomeações silenciosas que escondem quebra de contrato.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-campos-opcionais-e-defaults"></a>

## Campos Opcionais e Defaults

> Campo com `T | None` pode ser nullable; a obrigatoriedade depende de haver ou não valor padrão. Distingo ausente, nulo e valor preenchido em PATCH.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-modelos-de-entrada-vs-saida"></a>

## Modelos de Entrada vs Saída

> `PedidoCreate`, `PedidoUpdate` e `PedidoResponse` expõem contratos distintos. Evito reutilizar um único schema que aceite campos administrativos ou devolva dados secretos.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-modelos-genericos-e-submodelos"></a>

## Modelos Genéricos e Submodelos

> Reutilizo wrappers de paginação quando a semântica é comum, sem montar hierarquias genéricas que dificultam OpenAPI e manutenção.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-typeadapter"></a>

## `TypeAdapter`

> Valida e serializa estruturas tipadas que não exigem `BaseModel` próprio, como `list[MeuDTO]`. Útil em boundaries específicos.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-erros-de-validacao"></a>

## Erros de Validação

> Transformo falhas de parsing em feedback legível e consistente, sem revelar detalhes de implementação, stack traces ou valores secretos.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-15-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Uso Pydantic para validar contratos em runtime, com coerção ou modo estrito conforme o caso. Separo schemas de entidades, protejo outputs e não confundo validação da API com consistência do domínio.

[↑ Sumário do tópico](#python-15-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-sumario"></a>

# 16. SQLAlchemy, ORM e Alembic

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-16-visao-geral)
- [SQLAlchemy Core vs ORM](#python-16-sqlalchemy-core-vs-orm)
- [Engine e Connection Pool](#python-16-engine-e-connection-pool)
- [`Session` e Unit of Work](#python-16-session-e-unit-of-work)
- [`AsyncSession`](#python-16-asyncsession)
- [Declarative Mapping](#python-16-declarative-mapping)
- [`select()` e SQLAlchemy 2.x](#python-16-select-e-sqlalchemy-2-x)
- [Flush vs Commit vs Refresh](#python-16-flush-vs-commit-vs-refresh)
- [Autoflush e Transações Implícitas](#python-16-autoflush-e-transacoes-implicitas)
- [`add`, `delete` e Lifecycle](#python-16-add-delete-e-lifecycle)
- [Relacionamentos e Lazy Loading](#python-16-relacionamentos-e-lazy-loading)
- [`selectinload` vs `joinedload`](#python-16-selectinload-vs-joinedload)
- [N+1 e Diagnóstico de Queries](#python-16-n-1-e-diagnostico-de-queries)
- [Transações e Context Managers](#python-16-transacoes-e-context-managers)
- [Optimistic Locking](#python-16-optimistic-locking)
- [Pessimistic Locking](#python-16-pessimistic-locking)
- [Alembic e Migrations](#python-16-alembic-e-migrations)
- [Expand and Contract](#python-16-expand-and-contract)
- [SQL Injection e Queries Parametrizadas](#python-16-sql-injection-e-queries-parametrizadas)
- [Repository e Limites do ORM](#python-16-repository-e-limites-do-orm)
- [Resumo de Entrevista](#python-16-resumo-de-entrevista)

---

<a id="python-16-visao-geral"></a>

## Visão Geral

> SQLAlchemy oferece SQL Core e ORM. Entendo Engine, Session, Unit of Work, transações, estratégia de carregamento e migrações antes de usar abstrações de repository.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-sqlalchemy-core-vs-orm"></a>

## SQLAlchemy Core vs ORM

> Core constrói e executa expressões SQL; ORM mapeia objetos e organiza estados e persistência. Escolho pelo tipo de consulta, clareza, ciclo de vida e necessidade de controle.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-engine-e-connection-pool"></a>

## Engine e Connection Pool

> Engine encapsula dialeto e pool de conexões. Configuro tamanho, overflow, timeout e reciclagem com base na capacidade do banco e na quantidade de workers.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-session-e-unit-of-work"></a>

## `Session` e Unit of Work

> Session acompanha objetos e operações numa unidade de trabalho; flush envia SQL pendente, commit confirma a transação. Sessão não é cache global nem recurso para ser compartilhado indiscriminadamente.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-asyncsession"></a>

## `AsyncSession`

> Fornece API assíncrona e não deve ser compartilhada entre tarefas concorrentes. Uso uma sessão por unidade lógica de trabalho e controlo quando I/O é permitido.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-declarative-mapping"></a>

## Declarative Mapping

> Models definem tabelas, colunas e relacionamentos. Entidades ORM não precisam ser os próprios DTOs HTTP nem os objetos de domínio em arquitetura desacoplada.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-select-e-sqlalchemy-2-x"></a>

## `select()` e SQLAlchemy 2.x

> Prefiro a API orientada a `select` e execução explícita da Session. Não presumo semântica legada de Query APIs sem conferir versão.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-flush-vs-commit-vs-refresh"></a>

## Flush vs Commit vs Refresh

> Flush sincroniza pendências sem confirmar a transação; commit persiste conforme as garantias do banco; refresh recarrega estado. Conflitos podem ocorrer antes ou durante commit.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-autoflush-e-transacoes-implicitas"></a>

## Autoflush e Transações Implícitas

> A Session pode executar flush antes de certas consultas e iniciar transações conforme o uso. Defino boundaries deliberados e compreendo o SQL disparado implicitamente.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-add-delete-e-lifecycle"></a>

## `add`, `delete` e Lifecycle

> Objetos transitam entre estados transient, pending, persistent, detached e deleted. Alterar um objeto detached não equivale automaticamente a persistir a mudança.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-relacionamentos-e-lazy-loading"></a>

## Relacionamentos e Lazy Loading

> Acesso tardio pode causar N+1 e, em async, I/O implícito problemático. Uso eager loading seletivo ou projeções alinhadas ao caso de uso.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-selectinload-vs-joinedload"></a>

## `selectinload` vs `joinedload`

> `selectinload` faz carregamento relacionado por consulta adicional em lote; `joinedload` usa joins. Considero cardinalidade, duplicação de linhas, paginação e custo real.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-n-1-e-diagnostico-de-queries"></a>

## N+1 e Diagnóstico de Queries

> Uma consulta inicial seguida de N consultas de relacionamento pode degradar latência. Inspeciono SQL gerado, tracing e plano, ajustando fetch ou query dedicada.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-transacoes-e-context-managers"></a>

## Transações e Context Managers

> Uso `with session.begin()` ou equivalentes async para delimitar commit e rollback. Transações longas com chamadas de rede ampliam contenção e retenção de conexões.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-optimistic-locking"></a>

## Optimistic Locking

> Versionamento de linha detecta atualizações concorrentes. A regra para conflito e eventual retry deve refletir semântica de negócio e idempotência.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-pessimistic-locking"></a>

## Pessimistic Locking

> Bloqueios `SELECT ... FOR UPDATE` podem preservar invariantes sob contenção, com custo de espera e deadlock. O suporte e o isolamento exatos dependem do SGBD.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-alembic-e-migrations"></a>

## Alembic e Migrations

> Versiono evolução do schema; autogenerate auxilia, mas reviso migrações, tipos, índices e operações destrutivas antes de aplicar.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-expand-and-contract"></a>

## Expand and Contract

> Para deploy sem downtime, adiciono estrutura compatível, migro dados e só removo a anterior após consumidores serem atualizados. Rollback de schema exige planejamento próprio.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-sql-injection-e-queries-parametrizadas"></a>

## SQL Injection e Queries Parametrizadas

> SQLAlchemy fornece binding quando usado corretamente. Não monto SQL com concatenação de dados externos nem confundo parâmetros com identificadores dinâmicos.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-repository-e-limites-do-orm"></a>

## Repository e Limites do ORM

> Repository encapsula consultas necessárias ao domínio sem esconder todo SQL atrás de abstração genérica. Decisões sobre transação permanecem explícitas na camada adequada.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-16-resumo-de-entrevista"></a>

## Resumo de Entrevista

> SQLAlchemy combina ORM, Unit of Work e transações. Administro sessões e pools por operação, previno N+1, uso locks conforme invariantes e versiono schema com Alembic.

[↑ Sumário do tópico](#python-16-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-sumario"></a>

# 17. Segurança em Backends Python

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-17-visao-geral)
- [Autenticação vs Autorização](#python-17-autenticacao-vs-autorizacao)
- [OAuth 2.0 e OIDC](#python-17-oauth-2-0-e-oidc)
- [JWT: Estrutura e Validação](#python-17-jwt-estrutura-e-validacao)
- [Bearer Tokens e Revogação](#python-17-bearer-tokens-e-revogacao)
- [Senhas e Hashing](#python-17-senhas-e-hashing)
- [Sessões e Cookies](#python-17-sessoes-e-cookies)
- [CSRF vs CORS](#python-17-csrf-vs-cors)
- [RBAC vs ABAC](#python-17-rbac-vs-abac)
- [Multi-tenancy e Isolamento de Dados](#python-17-multi-tenancy-e-isolamento-de-dados)
- [SQL Injection e Command Injection](#python-17-sql-injection-e-command-injection)
- [SSRF](#python-17-ssrf)
- [Secrets e Configuração](#python-17-secrets-e-configuracao)
- [Rate Limit e Proteção de Abuso](#python-17-rate-limit-e-protecao-de-abuso)
- [Security Headers e TLS](#python-17-security-headers-e-tls)
- [Logging Seguro e Privacidade](#python-17-logging-seguro-e-privacidade)
- [Dependências e Supply Chain](#python-17-dependencias-e-supply-chain)
- [Resumo de Entrevista](#python-17-resumo-de-entrevista)

---

<a id="python-17-visao-geral"></a>

## Visão Geral

> Segurança backend envolve autenticar identidades, autorizar recursos, proteger credenciais e reduzir superfícies de ataque. Frameworks não substituem modelagem de ameaças e controles de infraestrutura.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-autenticacao-vs-autorizacao"></a>

## Autenticação vs Autorização

> Autenticação estabelece identidade; autorização decide operações permitidas. Conferir login não garante acesso ao pedido ou documento pertencente a outro usuário.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-oauth-2-0-e-oidc"></a>

## OAuth 2.0 e OIDC

> OAuth 2.0 trata autorização delegada; OIDC acrescenta identidade. Distingo Access Token de ID Token e valido audience, issuer e escopos por recurso.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-jwt-estrutura-e-validacao"></a>

## JWT: Estrutura e Validação

> Um JWT assinado não é criptografado. Valido assinatura, algoritmos permitidos, expiração, emissor e audiência; decodificar payload sozinho não autentica a mensagem.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-bearer-tokens-e-revogacao"></a>

## Bearer Tokens e Revogação

> Quem possui bearer token pode usá-lo dentro de sua validade. Expiração curta, rotação e estratégias de revogação limitam impacto de vazamento.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-senhas-e-hashing"></a>

## Senhas e Hashing

> Armazeno senhas com algoritmo específico para hashing de senha, como Argon2id, com parâmetros adequados. Nunca uso SHA-256 simples como substituto de password hashing.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-sessoes-e-cookies"></a>

## Sessões e Cookies

> Cookies com Secure, HttpOnly e SameSite reduzem riscos, mas exigem desenho de sessão, CSRF e renovação coerentes. Stateless não é sinônimo de seguro.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-csrf-vs-cors"></a>

## CSRF vs CORS

> CSRF explora credenciais automáticas do browser; CORS controla leitura cross-origin no navegador. CORS não impede chamadas servidor-servidor nem substitui autenticação.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-rbac-vs-abac"></a>

## RBAC vs ABAC

> RBAC atribui permissões por papel; ABAC considera atributos do usuário, recurso e contexto. Para autorização por objeto, verifico ownership e limites do tenant.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-multi-tenancy-e-isolamento-de-dados"></a>

## Multi-tenancy e Isolamento de Dados

> Toda consulta deve respeitar o tenant de forma verificável, inclusive jobs, caches e mensagens. Para dados críticos, considero Row-Level Security e testes de isolamento.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-sql-injection-e-command-injection"></a>

## SQL Injection e Command Injection

> Queries parametrizadas previnem injeção de valores em SQL; subprocessos devem usar argumentos separados e evitar `shell=True` com entrada não confiável.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-ssrf"></a>

## SSRF

> URLs fornecidas por usuários podem atingir endereços internos ou metadata services. Uso allowlist, regras de rede, resolução controlada e bloqueio de destinos privados conforme risco.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-secrets-e-configuracao"></a>

## Secrets e Configuração

> Senhas e chaves não devem entrar no Git ou logs. Uso IAM, secret managers, rotação e menor privilégio, com acesso restrito por ambiente.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-rate-limit-e-protecao-de-abuso"></a>

## Rate Limit e Proteção de Abuso

> Rate limiting, quotas e limites de tamanho ajudam a conter abuso. Se a aplicação escala horizontalmente, limites críticos precisam de coordenação externa.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-security-headers-e-tls"></a>

## Security Headers e TLS

> TLS protege tráfego; políticas HTTP como HSTS podem reduzir risco em browsers. A configuração depende do tipo de cliente e do reverse proxy.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-logging-seguro-e-privacidade"></a>

## Logging Seguro e Privacidade

> Redijo segredos e PII sensível nos logs; mantenho traceId, motivo operacional e informações suficientes para auditoria sem revelar tokens completos.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-dependencias-e-supply-chain"></a>

## Dependências e Supply Chain

> Pacotes PyPI, wheels e imagens base precisam de versionamento, análise e proveniência. Monitoro vulnerabilidades e acesso das credenciais de publicação.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-17-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Segurança exige autenticação e autorização por recurso, validação criptográfica correta, segredos externos e prevenção de injeção. Em APIs multi-tenant, proteger cada consulta é tão importante quanto validar o token.

[↑ Sumário do tópico](#python-17-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-sumario"></a>

# 18. Testes Automatizados com Pytest

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-18-visao-geral)
- [Teste Unitário](#python-18-teste-unitario)
- [Arrange, Act, Assert](#python-18-arrange-act-assert)
- [`pytest` e `assert`](#python-18-pytest-e-assert)
- [`@pytest.mark.parametrize`](#python-18-pytest-mark-parametrize)
- [Fixtures e Escopos](#python-18-fixtures-e-escopos)
- [`yield` nas Fixtures](#python-18-yield-nas-fixtures)
- [Mocks com `unittest.mock`](#python-18-mocks-com-unittest-mock)
- [Stub, Fake e Spy](#python-18-stub-fake-e-spy)
- [Monkeypatch](#python-18-monkeypatch)
- [Testes de Integração com PostgreSQL](#python-18-testes-de-integracao-com-postgresql)
- [Testcontainers para Python](#python-18-testcontainers-para-python)
- [Testes HTTP em FastAPI](#python-18-testes-http-em-fastapi)
- [Dependency Overrides](#python-18-dependency-overrides)
- [Testes de Contrato](#python-18-testes-de-contrato)
- [Testes E2E](#python-18-testes-e2e)
- [Testes Assíncronos](#python-18-testes-assincronos)
- [Hypothesis e Property-based Testing](#python-18-hypothesis-e-property-based-testing)
- [Cobertura vs Qualidade](#python-18-cobertura-vs-qualidade)
- [Ruff, Mypy e Quality Gates](#python-18-ruff-mypy-e-quality-gates)
- [Resumo de Entrevista](#python-18-resumo-de-entrevista)

---

<a id="python-18-visao-geral"></a>

## Visão Geral

> `pytest` permite testes expressivos de domínio, API e persistência. Construo uma suíte rápida e determinística, adicionando integração e contrato para riscos que mocks não detectam.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-teste-unitario"></a>

## Teste Unitário

> Exercita uma unidade sem banco ou rede real quando não são necessários. Priorizo casos de regra de negócio e limites, não apenas execução de linhas.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-arrange-act-assert"></a>

## Arrange, Act, Assert

> Arrange monta o cenário, Act executa a operação e Assert verifica efeito observável. Nomes claros descrevem expectativa e condição.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-pytest-e-assert"></a>

## `pytest` e `assert`

> `pytest` coleta funções de teste e apresenta introspecção de assertions. Comparo resultados e efeitos corretos, evitando testar apenas que um método foi chamado.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-pytest-mark-parametrize"></a>

## `@pytest.mark.parametrize`

> Executa mesma regra com múltiplas entradas, incluindo casos de borda e erros. Melhora cobertura sem duplicar código.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-fixtures-e-escopos"></a>

## Fixtures e Escopos

> Fixtures montam e liberam dependências; escopos function, class, module e session controlam reutilização. Escopo longo com estado mutável pode contaminar testes.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-yield-nas-fixtures"></a>

## `yield` nas Fixtures

> Uma fixture com `yield` permite teardown após o teste; uso para fechar conexão, limpar recursos e restaurar estado.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-mocks-com-unittest-mock"></a>

## Mocks com `unittest.mock`

> Mock, patch e AsyncMock substituem dependências e verificam interações. Faço patch no local onde o símbolo é resolvido, não necessariamente onde foi originalmente definido.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-stub-fake-e-spy"></a>

## Stub, Fake e Spy

> Stubs devolvem valores; fakes implementam uma alternativa funcional; spies observam comportamento. Escolho o dublê mínimo que demonstra o contrato sem prender o teste à implementação.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-monkeypatch"></a>

## Monkeypatch

> A fixture `monkeypatch` altera ambiente, funções ou atributos temporariamente. Evito modificar globais sem restauração e uso interfaces injetáveis quando o design permitir.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-testes-de-integracao-com-postgresql"></a>

## Testes de Integração com PostgreSQL

> Executo queries e transações reais para verificar mapping, locks e constraints que mocks não reproduzem. Prefiro banco compatível com produção.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-testcontainers-para-python"></a>

## Testcontainers para Python

> Containers efêmeros fornecem banco ou broker real durante testes. Cuido de inicialização, cleanup, isolamento e suporte no ambiente CI.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-testes-http-em-fastapi"></a>

## Testes HTTP em FastAPI

> `TestClient` atende cenários síncronos; `httpx.AsyncClient` com ASGITransport é útil para testes assíncronos. Uso overrides de dependência quando isolo adaptadores.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-dependency-overrides"></a>

## Dependency Overrides

> FastAPI permite substituir provedores durante testes, como banco e autenticação. O override deve ser restaurado para evitar vazamento entre cenários.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-testes-de-contrato"></a>

## Testes de Contrato

> Verifico compatibilidade entre produtores e consumidores, schemas e respostas, além dos status HTTP. OpenAPI pode apoiar geração e validação, mas não substitui testes de regra.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-testes-e2e"></a>

## Testes E2E

> Exercitam jornadas com infraestrutura integrada. São mais caros e frágeis; mantenho poucos, focados em fluxos críticos.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-testes-assincronos"></a>

## Testes Assíncronos

> Verifico tasks, cancelamentos, timeouts e concorrência sem sleeps arbitrários. Isolo relógio e dependências para evitar flakiness.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-hypothesis-e-property-based-testing"></a>

## Hypothesis e Property-based Testing

> Gera exemplos a partir de estratégias para explorar invariantes e casos não previstos manualmente. Defino propriedades verificáveis, não apenas outputs copiados da implementação.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-cobertura-vs-qualidade"></a>

## Cobertura vs Qualidade

> Cobertura mede execução, não capacidade de detectar defeitos. Analiso condições, mutações e cenários de falha para avaliar o valor da suíte.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-ruff-mypy-e-quality-gates"></a>

## Ruff, Mypy e Quality Gates

> Lint, formato e verificação estática antecipam problemas antes de testes de integração. Mantenho configuração consistente em IDE, pre-commit e pipeline.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-18-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Uso pytest e fixtures para testes determinísticos, mocks para isolamento e serviços reais nos testes de integração. Cobertura é sinal, não objetivo único, e valido também contratos e concorrência.

[↑ Sumário do tópico](#python-18-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-sumario"></a>

# 19. Arquitetura de Software

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral — Arquitetura de Software](#python-19-visao-geral-arquitetura-de-software)
- [Requisitos Funcionais e Não Funcionais](#python-19-requisitos-funcionais-e-nao-funcionais)
- [Trade-off Arquitetural](#python-19-trade-off-arquitetural)
- [ADR e C4 Model](#python-19-adr-e-c4-model)
- [DDD e Arquitetura](#python-19-ddd-e-arquitetura)
- [Linguagem Ubíqua e Context Map](#python-19-linguagem-ubiqua-e-context-map)
- [Bounded Context](#python-19-bounded-context)
- [Entidade vs Value Object](#python-19-entidade-vs-value-object)
- [Agregado e Aggregate Root](#python-19-agregado-e-aggregate-root)
- [Application Service vs Domain Service](#python-19-application-service-vs-domain-service)
- [Arquitetura em Camadas](#python-19-arquitetura-em-camadas)
- [Arquitetura Hexagonal](#python-19-arquitetura-hexagonal)
- [Clean Architecture](#python-19-clean-architecture)
- [Arquitetura Hexagonal vs Clean Architecture](#python-19-arquitetura-hexagonal-vs-clean-architecture)
- [Monólito](#python-19-monolito)
- [Monólito Modular](#python-19-monolito-modular)
- [Microsserviços](#python-19-microsservicos)
- [Monólito vs Microsserviços](#python-19-monolito-vs-microsservicos)
- [Quando extrair um Microsserviço](#python-19-quando-extrair-um-microsservico)
- [Síncrono vs Assíncrono](#python-19-sincrono-vs-assincrono)
- [API REST vs Mensageria](#python-19-api-rest-vs-mensageria)
- [Arquitetura Orientada a Mensagens](#python-19-arquitetura-orientada-a-mensagens)
- [Arquitetura Orientada a Eventos](#python-19-arquitetura-orientada-a-eventos)
- [Evento vs Comando](#python-19-evento-vs-comando)
- [At-most-once, At-least-once e Exactly-once](#python-19-at-most-once-at-least-once-e-exactly-once)
- [Consistência Forte vs Eventual](#python-19-consistencia-forte-vs-eventual)
- [CAP Theorem](#python-19-cap-theorem)
- [PACELC](#python-19-pacelc)
- [Banco por Serviço](#python-19-banco-por-servico)
- [Shared Database — Trade-off](#python-19-shared-database-trade-off)
- [Replicação e Read Replica](#python-19-replicacao-e-read-replica)
- [Particionamento vs Sharding](#python-19-particionamento-vs-sharding)
- [Sharding](#python-19-sharding)
- [CQRS](#python-19-cqrs)
- [CQRS vs Read Replica](#python-19-cqrs-vs-read-replica)
- [Event Sourcing](#python-19-event-sourcing)
- [Saga](#python-19-saga)
- [Saga — Orquestração vs Coreografia](#python-19-saga-orquestracao-vs-coreografia)
- [Outbox Pattern](#python-19-outbox-pattern)
- [Idempotência](#python-19-idempotencia)
- [API Gateway](#python-19-api-gateway)
- [BFF — Backend for Frontend](#python-19-bff-backend-for-frontend)
- [Service Discovery](#python-19-service-discovery)
- [Load Balancing](#python-19-load-balancing)
- [Stateless vs Stateful](#python-19-stateless-vs-stateful)
- [Escalabilidade Vertical vs Horizontal](#python-19-escalabilidade-vertical-vs-horizontal)
- [Alta Disponibilidade](#python-19-alta-disponibilidade)
- [Tolerância a Falhas](#python-19-tolerancia-a-falhas)
- [Retry, Timeout e Circuit Breaker na Arquitetura](#python-19-retry-timeout-e-circuit-breaker-na-arquitetura)
- [Bulkhead](#python-19-bulkhead)
- [Degradação Graciosa](#python-19-degradacao-graciosa)
- [Rate Limiting](#python-19-rate-limiting)
- [Backpressure](#python-19-backpressure)
- [Cache](#python-19-cache)
- [Cache-Aside](#python-19-cache-aside)
- [Cache Stampede](#python-19-cache-stampede)
- [Observabilidade](#python-19-observabilidade)
- [Logs, Métricas e Traces](#python-19-logs-metricas-e-traces)
- [SLO, SLI e SLA](#python-19-slo-sli-e-sla)
- [P95, P99 e Média](#python-19-p95-p99-e-media)
- [Arquitetura e Segurança](#python-19-arquitetura-e-seguranca)
- [Versionamento de API](#python-19-versionamento-de-api)
- [Backward Compatibility](#python-19-backward-compatibility)
- [Strangler Fig Pattern](#python-19-strangler-fig-pattern)
- [Anti-Corruption Layer](#python-19-anti-corruption-layer)
- [12-Factor App](#python-19-12-factor-app)
- [Arquitetura Cloud-Native](#python-19-arquitetura-cloud-native)
- [Kubernetes na Arquitetura](#python-19-kubernetes-na-arquitetura)
- [Arquitetura Evolutiva](#python-19-arquitetura-evolutiva)
- [Resumo de Entrevista](#python-19-resumo-de-entrevista)

---

<a id="python-19-visao-geral-arquitetura-de-software"></a>

## Visão Geral — Arquitetura de Software

> Arquitetura de software define como responsabilidades, componentes, dados e integrações são organizados para atender requisitos funcionais e não funcionais. Em entrevista, eu procuro deixar claro que arquitetura não é escolher tecnologia, mas tomar decisões estruturais considerando trade-offs como escalabilidade, disponibilidade, consistência, complexidade, custo e capacidade de evolução.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-requisitos-funcionais-e-nao-funcionais"></a>

## Requisitos Funcionais e Não Funcionais

> Requisitos funcionais descrevem comportamentos; não funcionais definem propriedades como latência, disponibilidade, segurança e custo. Transformo expressões vagas como “rápido” em metas mensuráveis e condições de carga para comparar alternativas.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-trade-off-arquitetural"></a>

## Trade-off Arquitetural

> Toda decisão arquitetural troca uma propriedade por outra. Mais consistência pode reduzir disponibilidade; mais abstração pode reduzir acoplamento, mas aumentar complexidade; mais serviços aumentam autonomia, mas também operação. Em entrevista, eu tento explicitar qual requisito estou priorizando e qual custo estou aceitando.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-adr-e-c4-model"></a>

## ADR e C4 Model

> ADR registra uma decisão, contexto, alternativas e consequências. C4 organiza visões de contexto, containers e componentes, com detalhe de código quando útil; uso documentação suficiente para orientar decisões e evolução, sem diagramar tudo.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-ddd-e-arquitetura"></a>

## DDD e Arquitetura

> Domain-Driven Design ajuda quando o problema de negócio é complexo. Eu destaco linguagem ubíqua, bounded contexts, entidades, value objects, agregados e serviços de domínio. DDD não significa obrigatoriamente microsserviços. Pode ser aplicado em monólito modular e, na prática, ajuda mais a organizar o domínio do que a escolher infraestrutura.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-linguagem-ubiqua-e-context-map"></a>

## Linguagem Ubíqua e Context Map

> Linguagem ubíqua é o vocabulário compartilhado entre negócio e desenvolvimento dentro de um contexto. Context map mostra relações entre contextos e seus contratos; um bounded context não precisa corresponder exatamente a um microsserviço.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-bounded-context"></a>

## Bounded Context

> Bounded Context, em DDD, define uma fronteira dentro da qual um modelo de domínio possui significado consistente. Em arquitetura de microsserviços, ele ajuda a encontrar limites mais naturais para serviços. Um mesmo termo pode ter modelos diferentes em contextos distintos. Eu evito compartilhar o mesmo modelo de domínio entre serviços porque isso aumenta acoplamento.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-entidade-vs-value-object"></a>

## Entidade vs Value Object

> Entidade possui identidade própria ao longo do tempo. Value Object é definido por seus valores e normalmente é imutável. Por exemplo, um `Pedido` tende a ser entidade; `Dinheiro` pode ser um value object. Value Objects ajudam a encapsular regras e reduzir uso de tipos primitivos sem significado de negócio.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-agregado-e-aggregate-root"></a>

## Agregado e Aggregate Root

> Agregado é um conjunto de objetos de domínio tratado como uma unidade de consistência. A Aggregate Root é o ponto de entrada para alterações internas. Regras do agregado devem ser preservadas dentro dessa fronteira. Agregados muito grandes aumentam contenção e custo transacional, então tento mantê-los focados nas invariantes que realmente precisam de consistência forte.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-application-service-vs-domain-service"></a>

## Application Service vs Domain Service

> Application service coordena um caso de uso, permissões e transação. Domain service expressa uma regra de negócio que não cabe naturalmente em uma entidade ou value object; evito colocar toda a modelagem em serviços sem comportamento no domínio.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-arquitetura-em-camadas"></a>

## Arquitetura em Camadas

> Arquitetura em camadas separa responsabilidades em níveis como apresentação, aplicação, domínio e infraestrutura. O principal benefício é organização e separação de responsabilidades. O trade-off é que, quando aplicada rigidamente, pode gerar acoplamento entre camadas e fluxos artificiais. Eu uso quando o domínio e a complexidade justificam essa separação, sem transformar a estrutura em burocracia.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-arquitetura-hexagonal"></a>

## Arquitetura Hexagonal

> Arquitetura Hexagonal organiza o sistema em torno do domínio e usa portas e adaptadores para isolar infraestrutura. As portas representam contratos de entrada e saída; os adaptadores conectam HTTP, banco, mensageria ou APIs externas. O principal ganho é desacoplamento e testabilidade. O custo é mais abstração e mais classes, então aplico principalmente onde a independência de infraestrutura agrega valor.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-clean-architecture"></a>

## Clean Architecture

> Clean Architecture organiza dependências apontando para regras de negócio mais internas. Frameworks, banco e interface ficam nas bordas. O objetivo é manter o domínio independente de detalhes externos. O trade-off é aumento de abstrações e mapeamentos. Eu considero útil em sistemas com regra de negócio relevante e longa vida útil, mas evito aplicar de forma dogmática em CRUDs simples.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-arquitetura-hexagonal-vs-clean-architecture"></a>

## Arquitetura Hexagonal vs Clean Architecture

> As duas buscam reduzir dependência de infraestrutura e proteger o domínio. Hexagonal enfatiza portas e adaptadores; Clean Architecture enfatiza círculos ou camadas com dependências apontando para dentro. Na prática, os princípios se sobrepõem bastante. Eu escolho a modelagem que deixa as dependências explícitas sem criar abstrações desnecessárias.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-monolito"></a>

## Monólito

> Monólito é uma aplicação implantada como uma unidade única. Ele não é sinônimo de código ruim. Pode ser simples de desenvolver, testar, implantar e operar, especialmente no início. O problema aparece quando cresce sem modularização, gerando acoplamento e dificuldade de evolução. Eu começo simples e só distribuo quando existe uma razão concreta.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-monolito-modular"></a>

## Monólito Modular

> Monólito modular mantém um único deploy, mas separa o sistema em módulos com fronteiras claras e dependências controladas. Ele preserva simplicidade operacional enquanto reduz acoplamento interno. Para muitos sistemas, é um excelente passo antes de microsserviços, porque permite descobrir boundaries sem assumir de imediato o custo de distribuição.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-microsservicos"></a>

## Microsserviços

> Microsserviços dividem o sistema em serviços implantáveis de forma independente, normalmente alinhados a capacidades de negócio. Os benefícios incluem autonomia de deploy, isolamento de falhas e escalabilidade independente. O custo é alto: rede, observabilidade distribuída, consistência eventual, versionamento, segurança, tracing, DevOps e operação. Eu uso microsserviços quando esses benefícios compensam a complexidade distribuída.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-monolito-vs-microsservicos"></a>

## Monólito vs Microsserviços

> Eu não trato microsserviços como evolução obrigatória do monólito. Monólito reduz complexidade operacional e favorece consistência local. Microsserviços ganham autonomia, escalabilidade e isolamento, mas introduzem falhas de rede e consistência distribuída. A decisão depende de escala organizacional, domínios independentes, frequência de deploy, requisitos de disponibilidade e capacidade operacional do time.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-quando-extrair-um-microsservico"></a>

## Quando extrair um Microsserviço

> Eu consideraria extrair um serviço quando existe uma fronteira de domínio clara e uma necessidade concreta, como escala independente, ritmo de deploy diferente, isolamento de falha, requisitos de segurança específicos ou ownership por outro time. Não extraio apenas porque uma classe ficou grande. Primeiro procuro um boundary estável e mensuro o custo operacional da separação.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-sincrono-vs-assincrono"></a>

## Síncrono vs Assíncrono

> Comunicação síncrona é mais simples quando o chamador precisa de resposta imediata, mas cria acoplamento temporal e propaga latência e falhas. Comunicação assíncrona reduz esse acoplamento e absorve picos, porém exige lidar com consistência eventual, idempotência e observabilidade. Eu escolho com base na necessidade de resposta e no comportamento esperado em falhas.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-api-rest-vs-mensageria"></a>

## API REST vs Mensageria

> REST é adequado para interação síncrona em que o chamador precisa de resposta imediata. Mensageria é melhor quando quero desacoplar tempo, absorver picos ou propagar eventos. Muitas arquiteturas usam ambos: comando síncrono para uma operação e evento assíncrono para comunicar consequências.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-arquitetura-orientada-a-mensagens"></a>

## Arquitetura Orientada a Mensagens

> Mensageria desacopla produtores e consumidores no tempo e permite bufferizar carga. O trade-off é lidar com entrega duplicada, ordenação, retries, DLQ e observabilidade. Eu considero idempotência do consumidor e semântica de ack parte do contrato de produção.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-arquitetura-orientada-a-eventos"></a>

## Arquitetura Orientada a Eventos

> Arquitetura orientada a eventos comunica mudanças de estado através de eventos, reduzindo acoplamento temporal entre produtores e consumidores. Ela melhora extensibilidade e integração assíncrona, mas traz consistência eventual, duplicidade, ordenação, observabilidade e tratamento de falhas. Eu considero idempotência e semântica de entrega parte obrigatória do design.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-evento-vs-comando"></a>

## Evento vs Comando

> Comando expressa intenção: `CriarPedido`. Evento expressa um fato que já aconteceu: `PedidoCriado`. Um comando normalmente tem um destinatário esperado e pode falhar; um evento pode ter zero ou vários consumidores. Misturar esses conceitos costuma gerar contratos confusos em arquiteturas orientadas a mensagens.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-at-most-once-at-least-once-e-exactly-once"></a>

## At-most-once, At-least-once e Exactly-once

> At-most-once prioriza não duplicar, mas pode perder mensagem. At-least-once reduz risco de perda, mas permite duplicidade e exige idempotência. Exactly-once geralmente depende de escopo e garantias específicas da tecnologia; eu evito tratá-lo como garantia mágica end-to-end sem analisar banco, broker e efeitos externos.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-consistencia-forte-vs-eventual"></a>

## Consistência Forte vs Eventual

> Consistência forte precisa de uma garantia definida, como linearizabilidade: uma leitura iniciada após uma escrita concluída observa essa escrita ou uma posterior. Consistência eventual permite divergência temporária, mas prevê convergência sem novas atualizações. Isso não é sinônimo dos níveis de isolamento de transações.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-cap-theorem"></a>

## CAP Theorem

> CAP afirma que, durante uma partição de rede, não é possível garantir simultaneamente consistência linearizável e disponibilidade para toda requisição a nós não falhos. A decisão é como cada operação reage à partição; não é simplesmente escolher dois de três atributos em qualquer situação.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-pacelc"></a>

## PACELC

> PACELC acrescenta que, mesmo sem partição, sistemas replicados podem trocar latência por consistência. Durante partição, aparece a escolha entre disponibilidade e consistência; fora dela, avalio quanto a coordenação custa para a garantia exigida.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-banco-por-servico"></a>

## Banco por Serviço

> Em microsserviços, cada serviço deve idealmente controlar seu próprio modelo e persistência. Compartilhar diretamente o mesmo schema cria acoplamento forte e dificulta deploy independente. Integração entre contextos deve acontecer por APIs ou eventos, embora migração gradual possa exigir compromissos temporários.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-shared-database-trade-off"></a>

## Shared Database — Trade-off

> Banco compartilhado simplifica joins e transações no início, mas aumenta acoplamento entre serviços e permite que um serviço dependa do schema interno de outro. Se a arquitetura exige autonomia real, eu procuro separar ownership dos dados e evitar acesso cruzado direto.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-replicacao-e-read-replica"></a>

## Replicação e Read Replica

> Read replicas podem aumentar capacidade de leitura e isolar carga analítica, mas introduzem possibilidade de replication lag. Uma leitura imediatamente após escrita pode retornar estado antigo. Portanto, eu só uso replica quando o caso de uso tolera essa consistência e faço roteamento de leitura conscientemente.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-particionamento-vs-sharding"></a>

## Particionamento vs Sharding

> Particionamento divide logicamente ou fisicamente os dados, inclusive dentro do mesmo banco. Sharding distribui partições entre instâncias ou nós; pode ampliar capacidade, mas adiciona roteamento, rebalanceamento e dificuldade em consultas e transações entre shards.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-sharding"></a>

## Sharding

> Sharding divide dados entre múltiplas partições ou bancos usando uma shard key. Ele permite escalar volume e throughput, mas complica joins globais, transações, rebalanceamento e hotspots. A escolha da shard key é crítica. Eu só considero sharding quando uma única instância ou cluster já não atende com alternativas mais simples.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-cqrs"></a>

## CQRS

> CQRS separa modelos ou caminhos de escrita e leitura. Pode variar desde handlers separados até armazenamentos distintos. O benefício é otimizar cada lado para necessidades diferentes e reduzir acoplamento entre modelos. O trade-off é maior complexidade, duplicação de modelos e possível consistência eventual. Eu não aplico CQRS apenas porque existem comandos e queries.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-cqrs-vs-read-replica"></a>

## CQRS vs Read Replica

> Read replica é uma estratégia de infraestrutura para escalar leitura sobre um modelo semelhante. CQRS é uma decisão arquitetural que separa modelos ou caminhos de leitura e escrita. Eles podem coexistir, mas resolvem problemas diferentes.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-event-sourcing"></a>

## Event Sourcing

> Event Sourcing persiste a sequência de eventos como fonte de verdade, em vez de armazenar apenas o estado atual. Isso oferece histórico completo e reconstrução de estado, mas aumenta bastante a complexidade de versionamento de eventos, replay, snapshots, debugging e evolução do modelo. É útil em domínios que realmente precisam desse histórico, não como padrão geral.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-saga"></a>

## Saga

> Saga coordena uma transação de negócio distribuída através de várias transações locais, usando ações compensatórias quando uma etapa falha. Pode ser por coreografia, via eventos, ou orquestração, com um coordenador explícito. Ela não fornece rollback ACID global; fornece uma estratégia de consistência de negócio entre serviços.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-saga-orquestracao-vs-coreografia"></a>

## Saga — Orquestração vs Coreografia

> Na orquestração, um componente central decide os próximos passos, o que facilita visualizar o fluxo, mas aumenta dependência no orquestrador. Na coreografia, serviços reagem a eventos, reduzindo controle central, porém o fluxo pode ficar mais difícil de rastrear. Eu escolho conforme complexidade do processo e necessidade de governança do fluxo.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-outbox-pattern"></a>

## Outbox Pattern

> Outbox Pattern resolve o problema de atualizar o banco e publicar um evento sem depender de uma transação distribuída. A aplicação grava a mudança de negócio e um registro de outbox na mesma transação local. Depois, outro processo publica o evento. O trade-off é lidar com publicação assíncrona, deduplicação e limpeza da outbox.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-idempotencia"></a>

## Idempotência

> Uma operação idempotente pode ser repetida sem produzir efeitos adicionais indevidos. Em sistemas distribuídos isso é essencial porque retries e redelivery acontecem. Implemento com chave idempotente, unique constraint, registro de mensagens processadas ou lógica de negócio naturalmente idempotente, dependendo do caso.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-api-gateway"></a>

## API Gateway

> API Gateway centraliza preocupações de borda, como roteamento, autenticação, rate limiting e observabilidade. Ele pode simplificar clientes, mas não deve concentrar regras de negócio nem se tornar um novo monólito. Segurança e autorização internas ainda podem ser necessárias dependendo do trust boundary.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-bff-backend-for-frontend"></a>

## BFF — Backend for Frontend

> BFF cria uma camada backend específica para as necessidades de um tipo de cliente, como web ou mobile. Ele reduz lógica de composição no frontend e permite contratos mais adequados, mas adiciona mais um serviço para operar. Eu uso quando os clientes possuem necessidades suficientemente diferentes para justificar essa camada.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-service-discovery"></a>

## Service Discovery

> Service Discovery permite localizar instâncias dinamicamente. Em plataformas como Kubernetes, esse recurso normalmente já existe através de Services e DNS. Eu evito adicionar um registry dedicado quando a plataforma já resolve o problema adequadamente.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-load-balancing"></a>

## Load Balancing

> Load balancing distribui requisições entre múltiplas instâncias para melhorar capacidade e disponibilidade. Pode ocorrer no cliente, gateway, proxy ou infraestrutura. O algoritmo precisa considerar health e, em alguns casos, afinidade ou pesos. Load balancing não substitui autoscaling nem resiliência.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-stateless-vs-stateful"></a>

## Stateless vs Stateful

> Serviço stateless não depende de estado de sessão local entre requisições, facilitando balanceamento e escala horizontal. Stateful pode ser necessário em alguns domínios, mas exige estratégia de replicação, afinidade ou armazenamento externo. Eu evito estado de sessão em memória local quando a aplicação precisa escalar horizontalmente.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-escalabilidade-vertical-vs-horizontal"></a>

## Escalabilidade Vertical vs Horizontal

> Escala vertical aumenta recursos de uma instância; é simples, mas tem limites físicos e pode manter um ponto único de falha. Escala horizontal adiciona instâncias, aumentando capacidade e resiliência, porém exige que estado, sessão, cache e dados sejam projetados para distribuição. Eu prefiro aplicações stateless quando quero escalar horizontalmente com simplicidade.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-alta-disponibilidade"></a>

## Alta Disponibilidade

> Alta disponibilidade busca manter o serviço operacional apesar de falhas. Normalmente envolve redundância, múltiplas instâncias, health checks, failover, replicação e eliminação de single points of failure. HA não é apenas subir duas instâncias; dependências como banco, fila e DNS também precisam ser consideradas.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-tolerancia-a-falhas"></a>

## Tolerância a Falhas

> Tolerância a falhas significa continuar oferecendo comportamento aceitável mesmo quando componentes falham. Isso pode envolver redundância, retries controlados, circuit breaker, fallback, filas e degradação graciosa. O objetivo é evitar que uma falha local vire falha sistêmica.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-retry-timeout-e-circuit-breaker-na-arquitetura"></a>

## Retry, Timeout e Circuit Breaker na Arquitetura

> Timeout limita quanto tempo um serviço espera; retry tenta recuperar falhas transitórias; circuit breaker evita insistir em uma dependência degradada. Eles são complementares. Retry deve respeitar idempotência, usar backoff e jitter e caber dentro do budget de latência. Configuração errada pode amplificar uma falha em cascata.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-bulkhead"></a>

## Bulkhead

> Bulkhead isola recursos para impedir que uma dependência degradada consuma toda a capacidade da aplicação. Posso aplicar com pools separados, semáforos ou limites de concorrência. O objetivo é conter falha e preservar capacidade para outras funções do sistema.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-degradacao-graciosa"></a>

## Degradação Graciosa

> Degradação graciosa mantém funcionalidades essenciais quando partes não críticas estão indisponíveis. Por exemplo, permitir checkout mesmo se recomendações estiverem fora. O fallback precisa ser explícito e observável; esconder falhas silenciosamente pode gerar dados incorretos ou mascarar incidentes.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-rate-limiting"></a>

## Rate Limiting

> Rate limiting controla quantas operações são aceitas em determinado período. Ele protege capacidade, reduz abuso e evita sobrecarga de dependências. Pode ser por usuário, API key, IP ou tenant. É importante definir também resposta apropriada, normalmente `429`, e política de retry do cliente.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-backpressure"></a>

## Backpressure

> Backpressure evita que produtores gerem trabalho mais rápido do que consumidores ou dependências conseguem processar. Pode ser implementado com filas limitadas, controle de concorrência, rate limiting ou mecanismos reativos. Sem backpressure, o sistema tende a acumular memória, threads ou conexões até falhar.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-cache"></a>

## Cache

> Cache troca consistência e memória por menor latência e menor carga na origem. O principal desafio não é colocar dados no cache, mas invalidá-los corretamente. Eu defino estratégia de TTL, chave, tamanho, eviction e comportamento em miss ou indisponibilidade. Também observo risco de cache stampede.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-cache-aside"></a>

## Cache-Aside

> No padrão cache-aside, a aplicação consulta o cache; em miss busca a origem e popula o cache. É simples e comum, mas pode gerar stampede quando muitas requisições perdem a mesma chave. TTL, locking por chave ou refresh antecipado podem ajudar conforme o cenário.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-cache-stampede"></a>

## Cache Stampede

> Cache stampede ocorre quando uma chave expira e muitas requisições consultam a origem simultaneamente. Isso pode derrubar a dependência. Estratégias incluem TTL com jitter, single-flight/locking por chave, refresh antecipado e stale-while-revalidate.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-observabilidade"></a>

## Observabilidade

> Observabilidade permite entender o comportamento interno do sistema a partir de sinais externos. Eu considero três pilares principais: logs, métricas e traces. Em sistemas distribuídos, correlation ID, trace ID, p95/p99, taxa de erro e saturação são fundamentais para localizar gargalos e falhas entre serviços.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-logs-metricas-e-traces"></a>

## Logs, Métricas e Traces

> Logs ajudam a entender eventos específicos; métricas mostram comportamento agregado ao longo do tempo; traces mostram o caminho de uma requisição entre componentes. Eu uso os três de forma complementar. Um trace pode apontar o serviço lento, uma métrica mostrar frequência e um log explicar o erro daquela execução.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-slo-sli-e-sla"></a>

## SLO, SLI e SLA

> SLI é a métrica observada, como disponibilidade ou latência. SLO é o objetivo interno para essa métrica, por exemplo 99,9% de disponibilidade. SLA é o compromisso formal com consequência contratual. Arquitetura deve ser orientada pelos SLOs porque eles ajudam a justificar redundância, capacidade e investimento operacional.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-p95-p99-e-media"></a>

## P95, P99 e Média

> P95 indica que aproximadamente 95% das observações ficam naquele valor ou abaixo; P99 cobre 99%. Percentis mostram a cauda que a média pode esconder. Não somo percentis de serviços nem faço média de P99s para obter o percentil global.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-arquitetura-e-seguranca"></a>

## Arquitetura e Segurança

> Segurança deve ser tratada como requisito arquitetural. Eu considero autenticação, autorização, least privilege, secrets, criptografia em trânsito e em repouso, trust boundaries, auditoria e proteção de endpoints administrativos. Segurança apenas no gateway ou frontend não é suficiente para todos os cenários.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-versionamento-de-api"></a>

## Versionamento de API

> Versionamento é necessário quando preciso evoluir contrato sem quebrar consumidores. Estratégias incluem URI, header ou negociação de conteúdo. Prefiro mudanças backward compatible quando possível. Em microsserviços, também trato eventos como contratos versionados e evito mudanças destrutivas.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-backward-compatibility"></a>

## Backward Compatibility

> Backward compatibility significa que consumidores antigos continuam funcionando após evolução do produtor. Em APIs e eventos, prefiro adicionar campos opcionais, evitar remover ou renomear campos abruptamente e coordenar mudanças em etapas. Isso reduz necessidade de deploy sincronizado.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-strangler-fig-pattern"></a>

## Strangler Fig Pattern

> Strangler Fig permite substituir um sistema legado gradualmente, desviando funcionalidades para novos componentes enquanto o legado continua operando. É útil para migração incremental porque reduz risco de big bang. O desafio é manter integração e consistência durante o período de coexistência.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-anti-corruption-layer"></a>

## Anti-Corruption Layer

> Anti-Corruption Layer protege um domínio contra modelos externos ou legados. Em vez de espalhar tipos e regras do sistema externo, crio uma camada que traduz contratos para o modelo interno. Isso reduz acoplamento e facilita substituição futura da integração.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-12-factor-app"></a>

## 12-Factor App

> Os princípios 12-Factor são úteis para aplicações cloud-native: configuração externa, processos stateless, logs como fluxo de eventos, dependências explícitas e paridade entre ambientes. Eu não trato como regra absoluta, mas como um conjunto útil de práticas para facilitar deploy e operação.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-arquitetura-cloud-native"></a>

## Arquitetura Cloud-Native

> Cloud-native não significa apenas hospedar na nuvem. Envolve projetar para automação, elasticidade, observabilidade, resiliência, configuração externa e infraestrutura dinâmica. Containers e Kubernetes podem fazer parte, mas a arquitetura precisa lidar corretamente com falhas, estado e operação.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-kubernetes-na-arquitetura"></a>

## Kubernetes na Arquitetura

> Kubernetes resolve problemas de orquestração, como scheduling, service discovery, rollout, health checks e scaling. Ele não resolve regra de negócio, consistência de dados, idempotência ou arquitetura de domínio. Eu o trato como plataforma de execução, não como substituto de decisões arquiteturais.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-arquitetura-evolutiva"></a>

## Arquitetura Evolutiva

> Arquitetura evolutiva aceita que decisões mudam conforme o sistema cresce. Eu procuro decisões reversíveis, módulos bem definidos, métricas e fitness functions que ajudem a detectar degradação arquitetural. O objetivo não é prever tudo, mas manter capacidade de adaptação.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-19-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Arquitetura organiza responsabilidades e dependências para atender requisitos de negócio e qualidade. Começo por domínio, volume, latência, disponibilidade e custo, depois comparo alternativas. Em distribuição, considero consistência, idempotência, resiliência e observabilidade, registrando as consequências das decisões.

[↑ Sumário do tópico](#python-19-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-sumario"></a>

# 20. Microsserviços

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral — Microsserviços](#python-20-visao-geral-microsservicos)
- [Quando usar Microsserviços](#python-20-quando-usar-microsservicos)
- [Quando não usar Microsserviços](#python-20-quando-nao-usar-microsservicos)
- [Monólito Modular vs Microsserviços](#python-20-monolito-modular-vs-microsservicos)
- [Boundaries de Serviço](#python-20-boundaries-de-servico)
- [Banco por Serviço](#python-20-banco-por-servico)
- [Shared Database — Trade-off](#python-20-shared-database-trade-off)
- [Comunicação Síncrona](#python-20-comunicacao-sincrona)
- [Comunicação Assíncrona](#python-20-comunicacao-assincrona)
- [REST vs Mensageria](#python-20-rest-vs-mensageria)
- [Consistência Distribuída](#python-20-consistencia-distribuida)
- [Saga](#python-20-saga)
- [Saga — Orquestração vs Coreografia](#python-20-saga-orquestracao-vs-coreografia)
- [Outbox Pattern](#python-20-outbox-pattern)
- [Idempotência em Microsserviços](#python-20-idempotencia-em-microsservicos)
- [Inbox e Consumidor Idempotente](#python-20-inbox-e-consumidor-idempotente)
- [CDC vs Polling da Outbox](#python-20-cdc-vs-polling-da-outbox)
- [Timeout](#python-20-timeout)
- [Budget de Latência e Deadline](#python-20-budget-de-latencia-e-deadline)
- [Resultado Desconhecido Após Timeout](#python-20-resultado-desconhecido-apos-timeout)
- [Retry](#python-20-retry)
- [Circuit Breaker](#python-20-circuit-breaker)
- [Estados do Circuit Breaker](#python-20-estados-do-circuit-breaker)
- [Bulkhead](#python-20-bulkhead)
- [Fallback](#python-20-fallback)
- [API Gateway](#python-20-api-gateway)
- [Service Discovery](#python-20-service-discovery)
- [Load Balancing](#python-20-load-balancing)
- [Stateless](#python-20-stateless)
- [Escalabilidade Horizontal](#python-20-escalabilidade-horizontal)
- [Alta Disponibilidade](#python-20-alta-disponibilidade)
- [Observabilidade em Microsserviços](#python-20-observabilidade-em-microsservicos)
- [Tracing Distribuído](#python-20-tracing-distribuido)
- [Trace Context e Propagação](#python-20-trace-context-e-propagacao)
- [P95 e P99](#python-20-p95-e-p99)
- [SLO e Error Budget](#python-20-slo-e-error-budget)
- [Rate Limiting](#python-20-rate-limiting)
- [Backpressure](#python-20-backpressure)
- [Cache em Microsserviços](#python-20-cache-em-microsservicos)
- [Cache Stampede](#python-20-cache-stampede)
- [Versionamento de API](#python-20-versionamento-de-api)
- [Backward Compatibility](#python-20-backward-compatibility)
- [Deploy Independente](#python-20-deploy-independente)
- [Falha em Cascata](#python-20-falha-em-cascata)
- [Degradação Graciosa](#python-20-degradacao-graciosa)
- [Configuração Distribuída](#python-20-configuracao-distribuida)
- [Segurança entre Serviços](#python-20-seguranca-entre-servicos)
- [Resumo de Entrevista](#python-20-resumo-de-entrevista)

---

<a id="python-20-visao-geral-microsservicos"></a>

## Visão Geral — Microsserviços

> Microsserviços dividem uma aplicação em serviços menores, implantáveis de forma independente e normalmente alinhados a capacidades de negócio. O principal ganho é autonomia de deploy, escala e ownership; o principal custo é a complexidade distribuída, como rede, observabilidade, consistência eventual, segurança, versionamento e operação.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-quando-usar-microsservicos"></a>

## Quando usar Microsserviços

> Eu usaria microsserviços quando existem fronteiras de domínio claras e uma necessidade concreta de autonomia, como escalabilidade independente, ciclos de deploy diferentes, isolamento de falhas, requisitos de segurança distintos ou times com ownership separado. Não considero microsserviços uma evolução obrigatória de todo monólito.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-quando-nao-usar-microsservicos"></a>

## Quando não usar Microsserviços

> Eu evitaria microsserviços quando o domínio ainda não está claro, o time é pequeno, o volume não exige distribuição ou a capacidade operacional é baixa. Nesses casos, um monólito modular normalmente entrega mais velocidade com menos complexidade.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-monolito-modular-vs-microsservicos"></a>

## Monólito Modular vs Microsserviços

> Monólito modular mantém um único deploy, mas separa o código por módulos e boundaries claros. Microsserviços levam essa separação para processos e deploys independentes. Eu costumo preferir monólito modular enquanto autonomia operacional e de escala ainda não justificarem a distribuição.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-boundaries-de-servico"></a>

## Boundaries de Serviço

> Eu defino boundaries com base em capacidade de negócio e ownership de dados, não por tabelas ou camadas técnicas. Um bom serviço tende a encapsular seu próprio modelo, regras e persistência, reduzindo necessidade de chamadas excessivas entre serviços.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-banco-por-servico"></a>

## Banco por Serviço

> Em microsserviços, cada serviço deve idealmente possuir ownership dos próprios dados. Compartilhar diretamente o mesmo schema cria acoplamento forte e permite que um serviço dependa da estrutura interna de outro. A integração deve ocorrer por APIs ou eventos.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-shared-database-trade-off"></a>

## Shared Database — Trade-off

> Banco compartilhado simplifica joins e transações no início, mas reduz autonomia e aumenta acoplamento entre serviços. Pode ser aceitável em migração gradual, mas eu trataria como compromisso temporário se o objetivo real é independência entre serviços.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-comunicacao-sincrona"></a>

## Comunicação Síncrona

> Comunicação síncrona é adequada quando o chamador precisa de resposta imediata. O custo é acoplamento temporal: se a dependência está lenta ou indisponível, o chamador sente diretamente. Por isso uso timeout, circuit breaker e observabilidade em chamadas remotas.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-comunicacao-assincrona"></a>

## Comunicação Assíncrona

> Comunicação assíncrona desacopla produtor e consumidor no tempo e ajuda a absorver picos. Em troca, introduz consistência eventual, duplicidade, ordenação, retry, DLQ e necessidade de idempotência. Eu uso quando o fluxo tolera resposta posterior ou quando desacoplamento temporal é importante.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-rest-vs-mensageria"></a>

## REST vs Mensageria

> REST é melhor quando preciso de resposta imediata e um contrato request-response claro. Mensageria é melhor para propagação de eventos, processamento assíncrono e absorção de picos. Em muitos sistemas uso os dois: comando síncrono para iniciar uma operação e evento assíncrono para comunicar consequências.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-consistencia-distribuida"></a>

## Consistência Distribuída

> Em microsserviços eu evito tentar reproduzir uma transação ACID global entre serviços. Normalmente cada serviço mantém sua transação local e a consistência entre serviços é coordenada de forma eventual, usando eventos, Saga, Outbox e operações idempotentes.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-saga"></a>

## Saga

> Saga coordena uma operação de negócio distribuída por meio de várias transações locais. Se uma etapa falha, ações compensatórias podem desfazer efeitos de negócio anteriores. Pode ser implementada por orquestração ou coreografia.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-saga-orquestracao-vs-coreografia"></a>

## Saga — Orquestração vs Coreografia

> Na orquestração existe um coordenador explícito do fluxo, o que facilita entendimento e controle. Na coreografia os serviços reagem a eventos, reduzindo centralização, mas o fluxo pode ficar mais difícil de rastrear. Eu escolho conforme complexidade e necessidade de governança do processo.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-outbox-pattern"></a>

## Outbox Pattern

> Outbox resolve o problema de atualizar o banco e publicar um evento de forma consistente sem transação distribuída. A aplicação grava a mudança de negócio e o registro da outbox na mesma transação local. Depois, outro processo publica a mensagem.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-idempotencia-em-microsservicos"></a>

## Idempotência em Microsserviços

> Idempotência garante que uma operação possa ser repetida sem gerar efeitos adicionais indevidos. Ela é importante em retries e mensageria. Eu implemento com chave idempotente, unique constraint, tabela de eventos processados ou lógica naturalmente idempotente.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-inbox-e-consumidor-idempotente"></a>

## Inbox e Consumidor Idempotente

> Registro o identificador do evento recebido e a mudança de negócio na mesma transação local, com unicidade para impedir processamento duplicado. Só confirmo o consumo após o commit; efeitos externos exigem sua própria estratégia, como outra outbox ou chave idempotente.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-cdc-vs-polling-da-outbox"></a>

## CDC vs Polling da Outbox

> Polling consulta registros pendentes da outbox; CDC acompanha mudanças confirmadas no log do banco. CDC pode reduzir consultas de varredura, mas exige infraestrutura e operação próprias. Em ambos, considero reentrega, ordenação e limpeza dos registros.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-timeout"></a>

## Timeout

> Timeout limita quanto tempo um serviço espera por uma dependência. Sem timeout, threads, conexões e recursos podem ficar presos indefinidamente. O timeout deve respeitar o budget total da requisição e ser coerente com retries.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-budget-de-latencia-e-deadline"></a>

## Budget de Latência e Deadline

> Em chamadas sequenciais, somo os orçamentos de cada etapa; em paralelas, considero o caminho mais lento e o custo de coordenação. Reservo margem para processamento local e retries e propago o prazo restante. Não somo P99s como se fossem um P99 total exato.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-resultado-desconhecido-apos-timeout"></a>

## Resultado Desconhecido Após Timeout

> Timeout informa que a resposta não chegou no prazo, não que o servidor deixou de executar a operação. Para efeitos como cobrança, consulto o estado ou repito com a mesma chave idempotente antes de iniciar uma nova operação.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-retry"></a>

## Retry

> Retry deve ser usado apenas para falhas transitórias e quando a operação suporta reexecução. Eu limito tentativas, uso backoff e jitter e verifico idempotência. Retry mal configurado pode amplificar uma indisponibilidade.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-circuit-breaker"></a>

## Circuit Breaker

> Circuit Breaker interrompe temporariamente chamadas para uma dependência com alta taxa de falha, evitando desperdício de recursos e falha em cascata. Ele normalmente passa por estados fechado, aberto e semiaberto.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-estados-do-circuit-breaker"></a>

## Estados do Circuit Breaker

> Fechado permite chamadas e mede falhas; aberto rejeita rapidamente as chamadas protegidas; semiaberto libera tentativas de avaliação. Se a dependência se recupera, volta a fechar; se a falha persiste, abre novamente. Ele não substitui timeout nem limite de concorrência.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-bulkhead"></a>

## Bulkhead

> Bulkhead isola recursos para impedir que a falha de uma dependência consuma toda a capacidade da aplicação. Pode ser implementado com pools separados, semáforos ou limites de concorrência.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-fallback"></a>

## Fallback

> Fallback fornece uma resposta alternativa quando uma dependência falha. Eu só uso quando existe uma resposta de negócio válida. Fallback silencioso pode mascarar incidentes e retornar informação incorreta.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-api-gateway"></a>

## API Gateway

> API Gateway centraliza preocupações de borda, como roteamento, autenticação, rate limiting, headers e observabilidade. Evito colocar regra de negócio no gateway para não criar um novo monólito central.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-service-discovery"></a>

## Service Discovery

> Service discovery permite localizar instâncias dinamicamente. Em Kubernetes, normalmente uso os mecanismos nativos de Services e DNS antes de adicionar um registry dedicado.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-load-balancing"></a>

## Load Balancing

> Load balancing distribui tráfego entre instâncias disponíveis. Pode existir no cliente, gateway, proxy ou infraestrutura. É complementar a health checks, autoscaling e resiliência.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-stateless"></a>

## Stateless

> Serviços stateless não dependem de estado de sessão local entre requisições, o que facilita scale-out e failover. Estado durável deve ficar em sistemas apropriados, como banco, cache ou storage externo.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-escalabilidade-horizontal"></a>

## Escalabilidade Horizontal

> Escalabilidade horizontal adiciona instâncias para aumentar capacidade. Para funcionar bem, preciso evitar estado local, compartilhar apenas o necessário e garantir que banco, cache e mensageria também suportem o crescimento.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-alta-disponibilidade"></a>

## Alta Disponibilidade

> Alta disponibilidade exige eliminar single points of failure em toda a cadeia: aplicação, banco, broker, rede e infraestrutura. Subir duas instâncias da aplicação não é suficiente se uma dependência crítica continua única.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-observabilidade-em-microsservicos"></a>

## Observabilidade em Microsserviços

> Em microsserviços eu considero logs, métricas e traces obrigatórios para investigação. Correlation ID e trace ID ajudam a seguir uma requisição entre serviços, enquanto p95, p99, taxa de erro e saturação mostram comportamento agregado.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-tracing-distribuido"></a>

## Tracing Distribuído

> Tracing distribuído mostra o caminho de uma requisição por múltiplos serviços e o tempo gasto em cada etapa. É especialmente útil para descobrir qual dependência está elevando latência ou causando falha.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-trace-context-e-propagacao"></a>

## Trace Context e Propagação

> Um trace relaciona spans de uma operação entre serviços. Propago o contexto por headers ou metadados de mensagens e configuro sua continuidade em tarefas assíncronas; correlation ID sozinho não mede durações nem reconstrói automaticamente toda a árvore de chamadas.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-p95-e-p99"></a>

## P95 e P99

> P95 e P99 indicam os limites abaixo dos quais ficam aproximadamente 95% e 99% das latências observadas. Acompanho-os por operação e dependência, junto com volume e erros; a média de percentis ou a soma dos P99s não produz o P99 de ponta a ponta.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-slo-e-error-budget"></a>

## SLO e Error Budget

> SLO define um objetivo de confiabilidade ou latência. Error budget representa quanto de indisponibilidade ou erro ainda é aceitável dentro desse objetivo. Eu uso isso para equilibrar velocidade de entrega e investimento em confiabilidade.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-rate-limiting"></a>

## Rate Limiting

> Rate limiting protege o serviço e suas dependências contra excesso de requisições. Pode ser aplicado por usuário, token, tenant ou IP. Também preciso definir como clientes devem reagir, normalmente usando `429` e política de retry apropriada.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-backpressure"></a>

## Backpressure

> Backpressure controla o fluxo quando produtores geram trabalho mais rápido que consumidores conseguem processar. Pode ser implementado com filas limitadas, semáforos, rate limiting ou mecanismos reativos. Sem backpressure, o sistema tende a acumular trabalho até esgotar recursos.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-cache-em-microsservicos"></a>

## Cache em Microsserviços

> Cache reduz latência e carga na origem, mas introduz risco de dados desatualizados e problemas de invalidação. Eu defino TTL, chave, política de eviction e comportamento em miss. Cache deve ser tratado como otimização, não como fonte de verdade.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-cache-stampede"></a>

## Cache Stampede

> Cache stampede ocorre quando uma chave muito acessada expira e várias requisições vão simultaneamente à origem. Estratégias comuns incluem TTL com jitter, locking por chave, single-flight e refresh antecipado.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-versionamento-de-api"></a>

## Versionamento de API

> Versionamento é necessário quando preciso evoluir contratos sem quebrar consumidores. Prefiro mudanças backward compatible sempre que possível, como adicionar campos opcionais em vez de remover ou renomear campos de forma abrupta.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-backward-compatibility"></a>

## Backward Compatibility

> Backward compatibility permite que consumidores antigos continuem funcionando enquanto o produtor evolui. Isso reduz deploy coordenado entre serviços e é especialmente importante em APIs e eventos assíncronos.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-deploy-independente"></a>

## Deploy Independente

> Deploy independente é um dos principais objetivos de microsserviços, mas só existe de verdade quando contratos, banco, configuração e dependências também estão desacoplados. Se todo deploy exige coordenação entre serviços, a arquitetura ainda está fortemente acoplada.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-falha-em-cascata"></a>

## Falha em Cascata

> Falha em cascata acontece quando a degradação de um serviço consome recursos de serviços dependentes e propaga o problema. Timeout, circuit breaker, bulkhead, rate limiting e degradação graciosa ajudam a limitar esse efeito.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-degradacao-graciosa"></a>

## Degradação Graciosa

> Degradação graciosa mantém funcionalidades essenciais mesmo quando componentes secundários falham. Um exemplo é continuar permitindo checkout se recomendações estiverem indisponíveis. O fallback precisa ser explícito e observável.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-configuracao-distribuida"></a>

## Configuração Distribuída

> Configuração distribuída centraliza propriedades comuns a vários serviços, mas cria uma dependência operacional adicional. Eu avalio disponibilidade, versionamento, segurança de secrets e comportamento de startup quando a fonte de configuração está indisponível.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-seguranca-entre-servicos"></a>

## Segurança entre Serviços

> Segurança entre serviços envolve autenticação, autorização, criptografia em trânsito, least privilege e proteção de secrets. Mesmo em rede interna, eu não assumo confiança irrestrita sem analisar o trust boundary.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-20-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Microsserviços oferecem autonomia de deploy e escala, com custos de rede, dados e operação. Defino fronteiras de domínio e contratos claros, combino transações locais com Saga e Outbox e protejo dependências com limites. Só distribuo quando os benefícios justificam essa complexidade.

[↑ Sumário do tópico](#python-20-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-sumario"></a>

# 21. Apache Kafka e Clientes Python

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral — Kafka](#python-21-visao-geral-kafka)
- [Broker](#python-21-broker)
- [KRaft e Controller](#python-21-kraft-e-controller)
- [Topic](#python-21-topic)
- [Partition](#python-21-partition)
- [Offset](#python-21-offset)
- [Partition Key](#python-21-partition-key)
- [Producer](#python-21-producer)
- [Consumer](#python-21-consumer)
- [Consumer Group](#python-21-consumer-group)
- [Mais Partições que Consumidores](#python-21-mais-particoes-que-consumidores)
- [Mais Consumidores que Partições](#python-21-mais-consumidores-que-particoes)
- [Ordenação](#python-21-ordenacao)
- [Leader e Replicas](#python-21-leader-e-replicas)
- [Replication Factor](#python-21-replication-factor)
- [ISR](#python-21-isr)
- [acks=0, 1 e all](#python-21-acks-0-1-e-all)
- [min.insync.replicas](#python-21-min-insync-replicas)
- [At-most-once](#python-21-at-most-once)
- [At-least-once](#python-21-at-least-once)
- [Exactly-once](#python-21-exactly-once)
- [Transações Kafka e read_committed](#python-21-transacoes-kafka-e-read-committed)
- [Producer Idempotente](#python-21-producer-idempotente)
- [Consumer Idempotente](#python-21-consumer-idempotente)
- [Commit de Offset](#python-21-commit-de-offset)
- [Auto Commit vs Manual Commit](#python-21-auto-commit-vs-manual-commit)
- [Rebalance](#python-21-rebalance)
- [max.poll.interval.ms e Processamento Lento](#python-21-max-poll-interval-ms-e-processamento-lento)
- [Cooperative Rebalancing](#python-21-cooperative-rebalancing)
- [Consumer Lag](#python-21-consumer-lag)
- [Lag em Produção](#python-21-lag-em-producao)
- [Retention](#python-21-retention)
- [Log Compaction](#python-21-log-compaction)
- [Replay](#python-21-replay)
- [DLQ](#python-21-dlq)
- [Retry com Kafka](#python-21-retry-com-kafka)
- [Retry Topics e Ordenação](#python-21-retry-topics-e-ordenacao)
- [Poison Pill](#python-21-poison-pill)
- [Schema Evolution](#python-21-schema-evolution)
- [Schema Registry](#python-21-schema-registry)
- [Compatibilidade Backward, Forward e Full](#python-21-compatibilidade-backward-forward-e-full)
- [Kafka vs RabbitMQ](#python-21-kafka-vs-rabbitmq)
- [Kafka vs SQS](#python-21-kafka-vs-sqs)
- [Kafka vs Banco de Dados](#python-21-kafka-vs-banco-de-dados)
- [Kafka e Outbox](#python-21-kafka-e-outbox)
- [Kafka Connect e Kafka Streams](#python-21-kafka-connect-e-kafka-streams)
- [Partições e Escalabilidade](#python-21-particoes-e-escalabilidade)
- [Hot Partition](#python-21-hot-partition)
- [Batching](#python-21-batching)
- [Backpressure no Consumidor](#python-21-backpressure-no-consumidor)
- [Observabilidade Kafka](#python-21-observabilidade-kafka)
- [Falha de Broker](#python-21-falha-de-broker)
- [Kafka e Ordenação por Entidade](#python-21-kafka-e-ordenacao-por-entidade)
- [Kafka e Transação com Banco](#python-21-kafka-e-transacao-com-banco)
- [Resumo de Entrevista](#python-21-resumo-de-entrevista)
- [`confluent-kafka` no Python](#python-21-confluent-kafka-no-python)
- [`aiokafka` e Event Loop](#python-21-aiokafka-e-event-loop)
- [Consumer Python e Tratamento de Falhas](#python-21-consumer-python-e-tratamento-de-falhas)
- [Resumo Python no Kafka](#python-21-resumo-python-no-kafka)

---

<a id="python-21-visao-geral-kafka"></a>

## Visão Geral — Kafka

> Apache Kafka é uma plataforma distribuída de streaming de eventos baseada em log append-only. Produtores publicam registros em tópicos, tópicos são divididos em partições e consumidores leem os eventos mantendo posição por offset. Kafka é muito forte em throughput, retenção e processamento distribuído de eventos.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-broker"></a>

## Broker

> Broker é um servidor Kafka que armazena partições e atende produtores e consumidores. Um cluster é composto por múltiplos brokers para distribuir carga e fornecer redundância.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-kraft-e-controller"></a>

## KRaft e Controller

> KRaft mantém metadados do cluster usando um quorum de controllers baseado em Raft. Brokers armazenam e servem os registros; controllers coordenam metadados e liderança. Essa distinção ajuda a separar problemas do plano de controle e do fluxo de dados.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-topic"></a>

## Topic

> Topic é uma categoria lógica de eventos. Um tópico é dividido em partições, e cada registro pertence a uma dessas partições. A escolha do número de partições influencia paralelismo, throughput e ordenação.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-partition"></a>

## Partition

> Partition é um log ordenado e append-only dentro de um tópico. A ordem é garantida dentro de uma mesma partição, não globalmente entre todas as partições do tópico.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-offset"></a>

## Offset

> Offset identifica a posição de um registro dentro de uma partição. O consumidor usa offsets para saber até onde processou. O offset não é um identificador global do evento; ele só faz sentido dentro daquela partição.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-partition-key"></a>

## Partition Key

> A partition key influencia em qual partição um evento será gravado. Eventos com a mesma chave tendem a ir para a mesma partição, o que ajuda a preservar ordenação por entidade, como `orderId`. O trade-off é risco de hotspot se poucas chaves concentram muito volume.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-producer"></a>

## Producer

> Producer envia eventos para tópicos e escolhe partição com base na key, estratégia de particionamento ou distribuição padrão. Em produção, considero `acks`, retries, idempotência do producer, batching e compressão.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-consumer"></a>

## Consumer

> Consumer lê registros das partições e controla o progresso através de offsets. O processamento deve considerar falhas, commit de offset, idempotência e tempo máximo de processamento.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-consumer-group"></a>

## Consumer Group

> Consumer group permite paralelizar consumo de um tópico. Dentro do mesmo grupo, uma partição é atribuída a no máximo um consumidor ativo por vez. Grupos diferentes recebem o mesmo fluxo de eventos de forma independente.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-mais-particoes-que-consumidores"></a>

## Mais Partições que Consumidores

> Se existem mais partições do que consumidores no grupo, alguns consumidores processam várias partições. Isso aumenta o paralelismo potencial até o número de consumidores atingir o número de partições.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-mais-consumidores-que-particoes"></a>

## Mais Consumidores que Partições

> Se há mais consumidores que partições no mesmo grupo, consumidores excedentes ficam ociosos porque uma partição não é processada simultaneamente por dois consumidores do mesmo grupo.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-ordenacao"></a>

## Ordenação

> Kafka garante ordenação apenas dentro de uma partição. Se preciso manter ordem por cliente ou pedido, uso uma key estável para que eventos relacionados caiam na mesma partição. Isso reduz paralelismo para aquela chave, mas preserva sequência.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-leader-e-replicas"></a>

## Leader e Replicas

> Cada partição possui um leader e pode possuir replicas. Produtores e consumidores trabalham principalmente com o leader. As replicas mantêm cópias para permitir failover quando o leader fica indisponível.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-replication-factor"></a>

## Replication Factor

> Replication factor define quantas cópias de cada partição existem no cluster. Um fator maior aumenta tolerância a falhas, mas também aumenta uso de disco e tráfego de replicação.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-isr"></a>

## ISR

> ISR, In-Sync Replicas, representa replicas suficientemente sincronizadas com o leader. Esse conjunto é importante para decisões de durabilidade e failover.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-acks-0-1-e-all"></a>

## acks=0, 1 e all

> `acks=0` não espera confirmação; `acks=1` espera o leader; `acks=all` espera as réplicas atualmente no ISR. `min.insync.replicas` estabelece o mínimo de réplicas sincronizadas para aceitar a escrita com `acks=all`; não é o número fixo de confirmações quando o ISR é maior. [Referência](https://kafka.apache.org/41/design/design/)

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-min-insync-replicas"></a>

## min.insync.replicas

> `min.insync.replicas` define o número mínimo de replicas em sincronia necessário para aceitar gravações quando o producer usa `acks=all`. Essa combinação ajuda a evitar confirmar escrita com redundância insuficiente.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-at-most-once"></a>

## At-most-once

> At-most-once prioriza evitar duplicidade, mas pode perder mensagens. Isso ocorre, por exemplo, se o offset é confirmado antes de o processamento ser concluído e a aplicação falha depois.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-at-least-once"></a>

## At-least-once

> At-least-once prioriza não perder mensagens, mas permite duplicidade. É comum confirmar o offset depois do processamento. Se a aplicação processa e falha antes do commit, o evento pode ser reentregue. Por isso idempotência é importante.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-exactly-once"></a>

## Exactly-once

> Exactly-once em Kafka depende do escopo. Kafka oferece mecanismos como producer idempotente e transações para fluxos Kafka-to-Kafka. Isso não significa automaticamente exactly-once end-to-end quando existem efeitos externos, como banco de dados ou chamadas HTTP.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-transacoes-kafka-e-read-committed"></a>

## Transações Kafka e read_committed

> Uma transação Kafka pode agrupar publicações e offsets no próprio Kafka. `read_committed` evita entregar registros de transações abortadas ou ainda abertas como resultados confirmados; isso não inclui automaticamente o commit de um banco externo. [Referência](https://kafka.apache.org/41/design/design/)

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-producer-idempotente"></a>

## Producer Idempotente

> Producer idempotente evita duplicatas causadas por retries do próprio producer dentro das garantias do Kafka. Ele não resolve sozinho duplicidade de negócio causada por reprocessamento no consumidor.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-consumer-idempotente"></a>

## Consumer Idempotente

> Consumer idempotente garante que reentrega do mesmo evento não produza efeito duplicado. Estratégias comuns são chave idempotente, unique constraint, tabela de eventos processados ou operação naturalmente idempotente.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-commit-de-offset"></a>

## Commit de Offset

> O offset confirmado indica de onde o grupo deve retomar; normalmente registro o próximo offset a consumir. Confirmar antes de concluir pode perder processamento; confirmar depois pode gerar reentrega. Com execução paralela, não avanço além de registros anteriores ainda incompletos.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-auto-commit-vs-manual-commit"></a>

## Auto Commit vs Manual Commit

> Auto commit simplifica configuração, mas reduz controle sobre o ponto exato em que o progresso é confirmado. Manual commit permite alinhar offset ao processamento, porém exige mais cuidado com erros e rebalances.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-rebalance"></a>

## Rebalance

> Rebalance redistribui partições entre consumidores de um grupo quando consumidores entram, saem ou quando metadados mudam. Durante esse processo pode haver pausa no consumo. Rebalances frequentes prejudicam estabilidade e throughput.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-max-poll-interval-ms-e-processamento-lento"></a>

## max.poll.interval.ms e Processamento Lento

> O intervalo máximo entre chamadas de poll limita quanto tempo um consumidor pode ficar sem avançar o ciclo esperado. Processamento demorado pode provocar perda da atribuição; ajusto tamanho dos lotes e execução sem confirmar offsets antes de concluir o trabalho.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-cooperative-rebalancing"></a>

## Cooperative Rebalancing

> Cooperative rebalancing reduz impacto ao mover partições de forma mais incremental, evitando parar todo o grupo quando possível. É útil para reduzir pausas em grupos maiores.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-consumer-lag"></a>

## Consumer Lag

> Consumer lag expressa a distância entre a posição de referência do log e a posição do consumidor, conforme a métrica. Lag por offset confirmado pode incluir trabalho já processado sem commit; comparo tendência, idade dos eventos, throughput e erros para interpretar o atraso.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-lag-em-producao"></a>

## Lag em Produção

> Eu monitoro lag por partição e grupo, junto com throughput, tempo de processamento, erros e rebalances. Lag isolado não basta: preciso entender se está crescendo continuamente ou apenas absorvendo um pico temporário.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-retention"></a>

## Retention

> Kafka retém eventos por tempo ou tamanho, independentemente de terem sido consumidos. Isso diferencia Kafka de filas tradicionais e permite replay. O trade-off é uso de armazenamento.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-log-compaction"></a>

## Log Compaction

> Log compaction remove versões antigas de uma chave de forma assíncrona, preservando seu estado mais recente conforme a política. Duplicatas por chave podem coexistir antes da limpeza; registros com valor nulo, chamados tombstones, representam exclusão e também têm retenção configurada.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-replay"></a>

## Replay

> Replay significa reposicionar o consumidor em offsets anteriores e reprocessar eventos. Isso é útil para reconstrução de estado e correção de consumidores, mas exige processamento idempotente e cuidado com efeitos externos.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-dlq"></a>

## DLQ

> DLQ armazena mensagens que não puderam ser processadas após a política de retry. Ela evita bloquear indefinidamente o consumo, mas exige estratégia de inspeção, correção e reprocessamento. DLQ não deve virar depósito permanente de erros ignorados.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-retry-com-kafka"></a>

## Retry com Kafka

> Retry pode ser imediato ou com atraso através de tópicos de retry, dependendo da arquitetura. Eu evito loop agressivo no mesmo evento porque isso bloqueia a partição e reduz throughput. Também considero idempotência e limite de tentativas.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-retry-topics-e-ordenacao"></a>

## Retry Topics e Ordenação

> Mover uma mensagem com falha para outro tópico pode permitir que mensagens seguintes avancem, quebrando a ordem de negócio por chave. Defino se o fluxo deve parar, tolerar reordenação ou usar controle de versão antes de escolher a estratégia de retry.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-poison-pill"></a>

## Poison Pill

> Poison pill é uma mensagem que falha repetidamente, por exemplo por payload inválido. Sem tratamento, ela pode bloquear o progresso da partição. Estratégias incluem DLQ, tratamento específico de desserialização e limites de retry.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-schema-evolution"></a>

## Schema Evolution

> Eventos são contratos. Para evoluir schema, prefiro mudanças backward compatible, como adicionar campos opcionais. Formatos como Avro, Protobuf ou JSON Schema podem ser usados com registry para governar compatibilidade.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-schema-registry"></a>

## Schema Registry

> Schema Registry centraliza schemas e regras de compatibilidade. Ele ajuda produtores e consumidores a evoluírem sem quebrar contratos, mas adiciona mais um componente operacional.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-compatibilidade-backward-forward-e-full"></a>

## Compatibilidade Backward, Forward e Full

> Backward permite ao leitor novo ler dados antigos; forward permite ao leitor antigo ler dados novos; full exige as duas direções. As regras dependem do formato e da configuração do registry, e compatibilidade estrutural não garante semântica de negócio preservada.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-kafka-vs-rabbitmq"></a>

## Kafka vs RabbitMQ

> Kafka é orientado a log distribuído, retenção e alto throughput de eventos, com replay natural. RabbitMQ é tradicionalmente orientado a filas e roteamento de mensagens. A escolha depende do padrão: streaming e histórico favorecem Kafka; filas de trabalho e roteamento complexo podem favorecer RabbitMQ.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-kafka-vs-sqs"></a>

## Kafka vs SQS

> Kafka oferece log particionado, replay e controle detalhado de consumo. SQS é uma fila gerenciada com modelo operacional mais simples. Eu escolho Kafka quando preciso de streaming, múltiplos consumidores independentes, replay e alto throughput; SQS quando quero desacoplamento por fila com menor custo operacional.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-kafka-vs-banco-de-dados"></a>

## Kafka vs Banco de Dados

> Kafka não substitui banco transacional. Ele é excelente como log de eventos e backbone de integração, enquanto banco relacional continua melhor para consultas transacionais e consistência de dados de negócio. Os dois frequentemente coexistem.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-kafka-e-outbox"></a>

## Kafka e Outbox

> Outbox é uma estratégia comum para publicar eventos no Kafka de forma consistente com uma transação de banco. A aplicação grava negócio e outbox na mesma transação e depois um publicador envia o evento ao Kafka.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-kafka-connect-e-kafka-streams"></a>

## Kafka Connect e Kafka Streams

> Kafka Connect integra fontes e destinos de dados; Kafka Streams é biblioteca de processamento no ecossistema JVM, não o cliente Python nativo. Em Python, escolho consumidores, produtores e frameworks de stream processing compatíveis.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-particoes-e-escalabilidade"></a>

## Partições e Escalabilidade

> Número de partições define o teto de paralelismo de consumo dentro de um consumer group. Mais partições aumentam paralelismo potencial, mas também aumentam metadados, arquivos, custo de rebalance e complexidade operacional.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-hot-partition"></a>

## Hot Partition

> Hot partition ocorre quando uma ou poucas partições recebem volume muito maior, normalmente por distribuição desigual de keys. Isso limita throughput mesmo com muitos consumidores. Eu reviso estratégia de chave e distribuição de carga.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-batching"></a>

## Batching

> Producer batching agrupa registros para melhorar throughput e reduzir overhead de rede. O trade-off é introduzir pequena latência adicional enquanto o batch é formado. Compressão também pode reduzir rede ao custo de CPU.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-backpressure-no-consumidor"></a>

## Backpressure no Consumidor

> Se o processamento downstream é mais lento que o consumo, o lag cresce. Eu controlo concorrência, pausas de partição, capacidade de dependências e throughput de processamento em vez de simplesmente aumentar consumers sem analisar o gargalo.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-observabilidade-kafka"></a>

## Observabilidade Kafka

> Em produção, monitoro lag, throughput, taxa de erro, rebalances, tempo de processamento, utilização de disco, estado de replicas e latência de produce/fetch. Para consumidores, lag por partição é uma das métricas mais importantes.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-falha-de-broker"></a>

## Falha de Broker

> Quando um broker falha, partições com replicas adequadas podem eleger novos leaders e continuar operando. A disponibilidade depende de replication factor, ISR e configurações de durabilidade.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-kafka-e-ordenacao-por-entidade"></a>

## Kafka e Ordenação por Entidade

> Uma chave estável ajuda a manter eventos da entidade na mesma partição, preservando a ordem do log. A aplicação deve manter essa ordem no processamento; execução paralela, tópicos de retry e mudanças no particionamento podem alterar a sequência observada.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-kafka-e-transacao-com-banco"></a>

## Kafka e Transação com Banco

> Não existe uma transação ACID simples envolvendo Kafka e banco relacional de forma genérica. Para consistência entre os dois, eu normalmente uso Outbox ou desenho idempotente, em vez de depender de transação distribuída.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Kafka organiza eventos em tópicos particionados com retenção e offsets. Projeto chaves, replicação e consumo considerando ordenação, paralelismo e durabilidade. Controlo commits, duplicidade, retries e lag; garantias transacionais do Kafka não tornam efeitos em bancos e APIs automaticamente atômicos.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-confluent-kafka-no-python"></a>

## `confluent-kafka` no Python

> Biblioteca Python baseada em librdkafka para produzir e consumir eventos. Configuro idempotência, callbacks de entrega, polling, commits e shutdown sem assumir garantias adicionais por linguagem.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-aiokafka-e-event-loop"></a>

## `aiokafka` e Event Loop

> Clientes assíncronos integram consumo/publicação ao asyncio quando a biblioteca e versão utilizadas suportam o requisito. Evito bloqueio de loop durante processamento ou serialização pesada.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-consumer-python-e-tratamento-de-falhas"></a>

## Consumer Python e Tratamento de Falhas

> Separo desserialização, validação, domínio e confirmação de offset. Reprocessamento requer semântica de idempotência no efeito externo, especialmente banco.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-21-resumo-python-no-kafka"></a>

## Resumo Python no Kafka

> Kafka fornece particionamento, retenção e entrega conforme configurações. No consumidor Python, faço commit deliberado, idempotência no banco e métricas de lag, erros e throughput.

[↑ Sumário do tópico](#python-21-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-sumario"></a>

# 22. Mensageria, Filas e Jobs com Python

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-22-visao-geral)
- [Celery](#python-22-celery)
- [Broker vs Result Backend](#python-22-broker-vs-result-backend)
- [RabbitMQ: Exchanges e Queues](#python-22-rabbitmq-exchanges-e-queues)
- [Redis como Broker](#python-22-redis-como-broker)
- [SQS e Workers Python](#python-22-sqs-e-workers-python)
- [Ack e At-least-once](#python-22-ack-e-at-least-once)
- [Retries e Backoff com Jitter](#python-22-retries-e-backoff-com-jitter)
- [DLQ e Poison Messages](#python-22-dlq-e-poison-messages)
- [Idempotência no Worker](#python-22-idempotencia-no-worker)
- [Outbox e Inbox](#python-22-outbox-e-inbox)
- [Agendamento Celery Beat ou Scheduler](#python-22-agendamento-celery-beat-ou-scheduler)
- [Limites de Concorrência e Prefetch](#python-22-limites-de-concorrencia-e-prefetch)
- [Tarefas Longas e Progresso](#python-22-tarefas-longas-e-progresso)
- [Monitoramento de Filas](#python-22-monitoramento-de-filas)
- [Resumo de Entrevista](#python-22-resumo-de-entrevista)

---

<a id="python-22-visao-geral"></a>

## Visão Geral

> Para tarefas demoradas ou garantias de entrega, separo request HTTP de execução de jobs por filas e workers. A arquitetura deve explicitar confirmação, retry, idempotência e limites de capacidade.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-celery"></a>

## Celery

> Celery distribui tarefas entre workers usando brokers suportados. Uma chamada de tarefa assíncrona não significa execução exatamente uma vez; desenho consumidores idempotentes.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-broker-vs-result-backend"></a>

## Broker vs Result Backend

> Broker transporta tarefas; result backend armazena estados/resultados conforme configuração. Nem todo job precisa persistir resposta e guardar resultados indefinidamente custa recursos.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-rabbitmq-exchanges-e-queues"></a>

## RabbitMQ: Exchanges e Queues

> Produtores publicam em exchanges, que roteiam mensagens para filas por bindings. Entendo routing keys, acknowledgments, prefetch e durabilidade.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-redis-como-broker"></a>

## Redis como Broker

> Redis pode apoiar fluxos de fila conforme biblioteca e configuração, mas suas características de durabilidade não são idênticas às de RabbitMQ, Kafka ou SQS.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-sqs-e-workers-python"></a>

## SQS e Workers Python

> SQS desacopla serviços com visibility timeout e DLQ. É possível receber mensagens duplicadas; processamento deve tolerar repetições e falhas de confirmação.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-ack-e-at-least-once"></a>

## Ack e At-least-once

> Confirmar mensagem antes de concluir o efeito pode perder trabalho; confirmar após efeito pode causar repetição. Protejo persistência com transações e idempotency keys.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-retries-e-backoff-com-jitter"></a>

## Retries e Backoff com Jitter

> Repito falhas transitórias com limite e intervalos crescentes; não repito indiscriminadamente erros permanentes. Jitter reduz picos sincronizados de tentativas.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-dlq-e-poison-messages"></a>

## DLQ e Poison Messages

> Mensagens que repetidamente falham vão para investigação ou reprocessamento controlado. DLQ não corrige mensagem inválida nem garante recuperação automática.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-idempotencia-no-worker"></a>

## Idempotência no Worker

> Registro identificador da operação, aplico constraint única e trato concorrência de tentativas. A decisão e o efeito precisam ser transacionalmente consistentes quando possível.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-outbox-e-inbox"></a>

## Outbox e Inbox

> Outbox registra evento no mesmo commit do dado de negócio; um publicador entrega depois. Inbox registra mensagens processadas para evitar efeitos duplicados no consumidor.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-agendamento-celery-beat-ou-scheduler"></a>

## Agendamento Celery Beat ou Scheduler

> Jobs periódicos precisam de política sobre overlaps, concorrência, fuso e execução perdida. Agendar duas vezes o mesmo job não deve multiplicar o efeito.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-limites-de-concorrencia-e-prefetch"></a>

## Limites de Concorrência e Prefetch

> Workers e brokers precisam respeitar pool de banco e capacidade externa. Prefetch alto pode causar distribuição desigual, retenção e pior latência.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-tarefas-longas-e-progresso"></a>

## Tarefas Longas e Progresso

> Tarefas extensas devem emitir checkpoints ou progresso quando adequado e tolerar reinício. Não trato tempo máximo de execução como mecanismo único de consistência.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-monitoramento-de-filas"></a>

## Monitoramento de Filas

> Meço mensagens pendentes, idade da mensagem mais antiga, taxa de erros, tentativas e tempo de processamento. Alerta deve refletir impacto real no SLA.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-22-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Workers desacoplam HTTP de tarefas, mas garantias de entrega dependem de broker, ack e idempotência. Dimensiono filas, retries e DLQ conforme capacidade e semântica de negócio.

[↑ Sumário do tópico](#python-22-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-sumario"></a>

# 23. Observabilidade, Resiliência e Performance

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-23-visao-geral)
- [Structured Logging](#python-23-structured-logging)
- [`logging` vs `print`](#python-23-logging-vs-print)
- [Correlation ID e Trace Context](#python-23-correlation-id-e-trace-context)
- [OpenTelemetry](#python-23-opentelemetry)
- [Métricas RED e USE](#python-23-metricas-red-e-use)
- [P95/P99 vs Média](#python-23-p95-p99-vs-media)
- [SLI, SLO e Error Budget](#python-23-sli-slo-e-error-budget)
- [Timeout e Deadline End-to-end](#python-23-timeout-e-deadline-end-to-end)
- [Retry com Backoff e Jitter](#python-23-retry-com-backoff-e-jitter)
- [Circuit Breaker](#python-23-circuit-breaker)
- [Bulkhead](#python-23-bulkhead)
- [Rate Limiting e Backpressure](#python-23-rate-limiting-e-backpressure)
- [Cache e Cache Stampede](#python-23-cache-e-cache-stampede)
- [Profiling CPU com `cProfile`](#python-23-profiling-cpu-com-cprofile)
- [`py-spy` e Sampling Profilers](#python-23-py-spy-e-sampling-profilers)
- [Event Loop Lag](#python-23-event-loop-lag)
- [Memória, RSS e Leaks](#python-23-memoria-rss-e-leaks)
- [Gargalos de Banco e Pool](#python-23-gargalos-de-banco-e-pool)
- [Graceful Shutdown](#python-23-graceful-shutdown)
- [Resumo de Entrevista](#python-23-resumo-de-entrevista)

---

<a id="python-23-visao-geral"></a>

## Visão Geral

> Em serviços Python, observabilidade combina logs, métricas e traces; resiliência limita o impacto das falhas, e profiling identifica gargalos. Otimizo com evidências, não por intuição.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-structured-logging"></a>

## Structured Logging

> Registros estruturados com nível, operação, traceId e atributos de negócio facilitam busca. Evito logs duplicados, dados sensíveis e formatação excessiva em caminhos críticos.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-logging-vs-print"></a>

## `logging` vs `print`

> `logging` oferece níveis, handlers, filtros e integração com observabilidade. `print` não fornece um contrato operacional consistente para produção.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-correlation-id-e-trace-context"></a>

## Correlation ID e Trace Context

> Propago identificadores entre HTTP, tarefas e mensagens, respeitando padrões de tracing. Context propagation atravessando threads e processos exige configuração.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-opentelemetry"></a>

## OpenTelemetry

> Instrumentação OTEL permite traces, métricas e logs conforme componentes e pipeline. Padronizo semantic conventions e evito cardinalidade explosiva em labels.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-metricas-red-e-use"></a>

## Métricas RED e USE

> RED mede rate, errors e duration de requests; USE mede utilização, saturação e erros de recursos. Junto métricas de negócio para interpretar impacto.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-p95-p99-vs-media"></a>

## P95/P99 vs Média

> Percentis revelam cauda de latência que a média esconde. Meço nos limites corretos e preservo volume de amostras e janelas para decisões.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-sli-slo-e-error-budget"></a>

## SLI, SLO e Error Budget

> SLI quantifica experiência medida; SLO define objetivo; error budget traduz tolerância a falhas. Uso alertas baseados em impacto, não cada exceção isolada.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-timeout-e-deadline-end-to-end"></a>

## Timeout e Deadline End-to-end

> Configuro timeout de conexão, leitura e prazo total. Em chamadas encadeadas, redistribuo budget sem permitir waits cumulativos ilimitados.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-retry-com-backoff-e-jitter"></a>

## Retry com Backoff e Jitter

> Repito operações transitórias e idempotentes dentro do deadline. Tentativas multiplicam carga e podem agravar indisponibilidade se usadas sem limites.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-circuit-breaker"></a>

## Circuit Breaker

> Impede novas chamadas a dependência degradada e reabre por tentativas de recuperação. Métricas, janelas e estados devem corresponder ao perfil de falhas.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-bulkhead"></a>

## Bulkhead

> Isola capacidade por dependência ou tipo de tráfego para impedir que uma falha esgote todos os recursos do serviço.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-rate-limiting-e-backpressure"></a>

## Rate Limiting e Backpressure

> Rate limiting controla taxa aceita; backpressure adapta produção à capacidade do consumidor. Defino comportamento de overflow e resposta 429 quando aplicável.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-cache-e-cache-stampede"></a>

## Cache e Cache Stampede

> Cache reduz latência, mas exige política de expiração, invalidação e limites. Em expirações simultâneas, coalescing ou jitter pode evitar tempestade contra origem.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-profiling-cpu-com-cprofile"></a>

## Profiling CPU com `cProfile`

> Identifica funções custosas e quantidade de chamadas. Uso profiling com carga representativa e comparo antes/depois sem confundir tempo CPU com espera de rede.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-py-spy-e-sampling-profilers"></a>

## `py-spy` e Sampling Profilers

> Profiler por amostragem observa processos com menor invasividade em certos cenários. Entendo restrições de permissões e impacto operacional antes de executar em produção.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-event-loop-lag"></a>

## Event Loop Lag

> Em serviços ASGI, atraso do loop indica bloqueio ou saturação que prejudica múltiplas requisições. Procuro código síncrono, tarefas CPU e pools saturados.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-memoria-rss-e-leaks"></a>

## Memória, RSS e Leaks

> Monitoro RSS por worker, caches, filas e retenção de objetos. Tracemalloc auxilia alocação Python; bibliotecas nativas podem exigir ferramentas adicionais.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-gargalos-de-banco-e-pool"></a>

## Gargalos de Banco e Pool

> Latência pode decorrer de query, bloqueio ou espera por conexão. Investigo execution plan e concorrência antes de aumentar número de workers.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-graceful-shutdown"></a>

## Graceful Shutdown

> Ao receber encerramento, paro de aceitar trabalho conforme arquitetura, finalizo requests permitidos e libero conexões. Jobs com confirmação precisam reentrega ou checkpoint confiável.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-23-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Investigo produção com logs correlacionados, métricas RED/USE, traces e profiling. Uso timeout, retry, breaker e bulkhead com limites; melhoria só conta quando reduz latência ou falha medida.

[↑ Sumário do tópico](#python-23-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-sumario"></a>

# 24. Docker e Kubernetes para Python

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-24-visao-geral)
- [Imagem vs Container vs Máquina Virtual](#python-24-imagem-vs-container-vs-maquina-virtual)
- [Dockerfile, Layers e Build Context](#python-24-dockerfile-layers-e-build-context)
- [Tags e Digests](#python-24-tags-e-digests)
- [Volumes e Persistência](#python-24-volumes-e-persistencia)
- [Docker Compose](#python-24-docker-compose)
- [Cluster, Node e Control Plane](#python-24-cluster-node-e-control-plane)
- [Pod](#python-24-pod)
- [Deployment e ReplicaSet](#python-24-deployment-e-replicaset)
- [Service, DNS e Entrada de Tráfego](#python-24-service-dns-e-entrada-de-trafego)
- [ConfigMap e Secret](#python-24-configmap-e-secret)
- [Requests, Limits e OOMKilled](#python-24-requests-limits-e-oomkilled)
- [Startup, Readiness e Liveness Probes](#python-24-startup-readiness-e-liveness-probes)
- [Graceful Shutdown](#python-24-graceful-shutdown)
- [HPA e Autoscaling](#python-24-hpa-e-autoscaling)
- [StatefulSet, Job e CronJob](#python-24-statefulset-job-e-cronjob)
- [RBAC e NetworkPolicy](#python-24-rbac-e-networkpolicy)
- [Diagnóstico de Pods](#python-24-diagnostico-de-pods)
- [Resumo de Entrevista](#python-24-resumo-de-entrevista)
- [Dockerfile Python Multi-stage](#python-24-dockerfile-python-multi-stage)
- [Workers Python, CPU e Memória](#python-24-workers-python-cpu-e-memoria)

---

<a id="python-24-visao-geral"></a>

## Visão Geral

> Containers empacotam aplicação e dependências em um ambiente de execução isolado. Kubernetes coordena esses workloads declarativamente; nenhum dos dois elimina a necessidade de tratar persistência, encerramento, capacidade e falhas na aplicação.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-imagem-vs-container-vs-maquina-virtual"></a>

## Imagem vs Container vs Máquina Virtual

> Imagem é o pacote de camadas e configuração; container é sua execução isolada, compartilhando o kernel do host. Máquina virtual normalmente possui seu próprio sistema operacional convidado, com outra relação entre isolamento e custo.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-dockerfile-layers-e-build-context"></a>

## Dockerfile, Layers e Build Context

> Dockerfile descreve como construir uma imagem. A ordem das etapas influencia reaproveitamento de cache; mantenho o contexto pequeno e não copio segredos para camadas, pois removê-los depois pode deixá-los no histórico da imagem.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-tags-e-digests"></a>

## Tags e Digests

> Tags podem apontar para imagens diferentes ao longo do tempo. Digest identifica o conteúdo da imagem e permite fixar exatamente o artefato executado; associá-lo ao commit facilita auditoria e rollback.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-volumes-e-persistencia"></a>

## Volumes e Persistência

> Dados gravados apenas na camada gravável do container podem desaparecer com sua substituição. Uso volumes ou serviços externos para estado durável, com estratégia explícita de backup e recuperação.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-docker-compose"></a>

## Docker Compose

> Compose descreve vários serviços, redes e volumes para executar ambientes com múltiplos containers. É útil no desenvolvimento e em cenários compatíveis, mas não fornece sozinho as mesmas capacidades de um orquestrador distribuído.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-cluster-node-e-control-plane"></a>

## Cluster, Node e Control Plane

> O cluster reúne o plano de controle e os nós que executam workloads. Kubernetes compara estado desejado e observado para reconciliar recursos; disponibilidade exige também planejar falhas de nós, zonas e dependências.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-pod"></a>

## Pod

> Pod é a menor unidade de implantação do Kubernetes e pode conter containers que compartilham rede e volumes. Pods são substituíveis; seu endereço e armazenamento temporário não devem ser tratados como identidade durável da aplicação.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-deployment-e-replicaset"></a>

## Deployment e ReplicaSet

> Deployment declara versões e quantidade desejada de réplicas e gerencia rollout por ReplicaSets. Ter réplicas não garante alta disponibilidade se todas dependem do mesmo ponto de falha ou não estão distribuídas adequadamente.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-service-dns-e-entrada-de-trafego"></a>

## Service, DNS e Entrada de Tráfego

> Service oferece um destino estável para alcançar Pods selecionados e pode ser descoberto por DNS. A entrada externa depende de recursos e controladores apropriados, como LoadBalancer, Ingress ou Gateway API.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-configmap-e-secret"></a>

## ConfigMap e Secret

> ConfigMap armazena configuração não sensível; Secret é destinado a dados sensíveis. Codificação base64 não é criptografia: controlo RBAC, proteção em repouso e acesso dos workloads, sem colocar credenciais em imagens ou repositórios.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-requests-limits-e-oomkilled"></a>

## Requests, Limits e OOMKilled

> Requests orientam o agendamento; limits restringem uso de recursos. CPU pode sofrer throttling, e ultrapassar memória pode levar a OOMKilled; investigo RSS por worker e não confundo encerramento do container com MemoryError do Python.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-startup-readiness-e-liveness-probes"></a>

## Startup, Readiness e Liveness Probes

> Startup tolera inicialização; readiness sinaliza aptidão para receber tráfego; liveness detecta condições sem progresso que exigem reinicialização. Em Python, considero inicialização de pools, modelos e workers sem fazer da probe uma chamada cara.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-graceful-shutdown"></a>

## Graceful Shutdown

> Ao encerrar um Pod, a aplicação ASGI deve parar de receber novas requisições, permitir o término controlado do trabalho em andamento e liberar pools. Jobs confirmados apenas após processamento podem exigir reentrega.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-hpa-e-autoscaling"></a>

## HPA e Autoscaling

> HPA ajusta réplicas com base em métricas configuradas. Escalar Pods não aumenta automaticamente conexões de banco ou capacidade do broker; escolho sinais úteis, requests adequados e limites coerentes com as dependências.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-statefulset-job-e-cronjob"></a>

## StatefulSet, Job e CronJob

> StatefulSet atende workloads que precisam de identidade estável e associação com volumes. Job executa trabalho até sua conclusão; CronJob agenda execuções. Mesmo com agendamento, desenho tarefas idempotentes para tolerar falhas e reexecuções.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-rbac-e-networkpolicy"></a>

## RBAC e NetworkPolicy

> RBAC limita ações sobre a API do Kubernetes. NetworkPolicy restringe tráfego entre workloads quando o plugin de rede oferece suporte; uso ambos com permissões mínimas e separação de responsabilidades.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-diagnostico-de-pods"></a>

## Diagnóstico de Pods

> Começo por eventos, estado e motivo de término, depois logs, probes, recursos e conectividade. `CrashLoopBackOff` indica reinícios repetidos; não explica sozinho a causa, que pode estar na aplicação, configuração, permissões ou dependências.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Empacoto serviços Python em imagens reproduzíveis, com recursos, probes e encerramento coerentes. Em Kubernetes, separo execução, rede, configuração e persistência e investigo falhas por evidência antes de aumentar réplicas.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-dockerfile-python-multi-stage"></a>

## Dockerfile Python Multi-stage

> Uso imagem base enxuta, dependências fixadas, usuário não-root e separação de build e runtime. Cuidado com wheels nativos e compatibilidade de libc e arquitetura.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-24-workers-python-cpu-e-memoria"></a>

## Workers Python, CPU e Memória

> Dimensiono processos do servidor ASGI conforme CPU, RSS por worker, número de conexões e GIL do build escolhido. Aumentar workers sem medir pode saturar o banco.

[↑ Sumário do tópico](#python-24-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-sumario"></a>

# 25. AWS para Backends Python

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral — AWS](#python-25-visao-geral-aws)
- [Região e Availability Zone](#python-25-regiao-e-availability-zone)
- [Alta Disponibilidade Multi-AZ](#python-25-alta-disponibilidade-multi-az)
- [Multi-Region](#python-25-multi-region)
- [Shared Responsibility Model](#python-25-shared-responsibility-model)
- [IAM](#python-25-iam)
- [IAM User vs Role](#python-25-iam-user-vs-role)
- [IAM Policy](#python-25-iam-policy)
- [Identity-based vs Resource-based Policy](#python-25-identity-based-vs-resource-based-policy)
- [STS e AssumeRole](#python-25-sts-e-assumerole)
- [VPC](#python-25-vpc)
- [Subnet Pública vs Privada](#python-25-subnet-publica-vs-privada)
- [Internet Gateway](#python-25-internet-gateway)
- [NAT Gateway](#python-25-nat-gateway)
- [Security Group vs NACL](#python-25-security-group-vs-nacl)
- [Route Table](#python-25-route-table)
- [VPC Endpoint](#python-25-vpc-endpoint)
- [EC2](#python-25-ec2)
- [Auto Scaling Group](#python-25-auto-scaling-group)
- [ALB vs NLB](#python-25-alb-vs-nlb)
- [EBS](#python-25-ebs)
- [EFS](#python-25-efs)
- [S3](#python-25-s3)
- [Consistência do S3](#python-25-consistencia-do-s3)
- [S3 Versioning](#python-25-s3-versioning)
- [S3 Lifecycle](#python-25-s3-lifecycle)
- [S3 Presigned URL](#python-25-s3-presigned-url)
- [CloudFront](#python-25-cloudfront)
- [Route 53](#python-25-route-53)
- [RDS](#python-25-rds)
- [RDS Multi-AZ: Instância vs Cluster](#python-25-rds-multi-az-instancia-vs-cluster)
- [RDS Read Replica](#python-25-rds-read-replica)
- [Aurora](#python-25-aurora)
- [DynamoDB](#python-25-dynamodb)
- [DynamoDB Partition Key](#python-25-dynamodb-partition-key)
- [DynamoDB Sort Key](#python-25-dynamodb-sort-key)
- [DynamoDB GSI e LSI](#python-25-dynamodb-gsi-e-lsi)
- [DynamoDB Consistency](#python-25-dynamodb-consistency)
- [DynamoDB On-Demand vs Provisioned](#python-25-dynamodb-on-demand-vs-provisioned)
- [ElastiCache](#python-25-elasticache)
- [Lambda](#python-25-lambda)
- [Lambda Cold Start](#python-25-lambda-cold-start)
- [Lambda SnapStart](#python-25-lambda-snapstart)
- [Lambda Concurrency](#python-25-lambda-concurrency)
- [Lambda Idempotência](#python-25-lambda-idempotencia)
- [ECS](#python-25-ecs)
- [ECS Service vs Task](#python-25-ecs-service-vs-task)
- [Fargate](#python-25-fargate)
- [EKS](#python-25-eks)
- [ECS vs EKS](#python-25-ecs-vs-eks)
- [ECS vs Lambda](#python-25-ecs-vs-lambda)
- [API Gateway](#python-25-api-gateway)
- [API Gateway vs ALB](#python-25-api-gateway-vs-alb)
- [SQS](#python-25-sqs)
- [SQS Standard vs FIFO](#python-25-sqs-standard-vs-fifo)
- [SQS Visibility Timeout](#python-25-sqs-visibility-timeout)
- [SQS DLQ](#python-25-sqs-dlq)
- [SQS Long Polling](#python-25-sqs-long-polling)
- [SNS](#python-25-sns)
- [SNS + SQS](#python-25-sns-sqs)
- [EventBridge](#python-25-eventbridge)
- [SNS vs EventBridge](#python-25-sns-vs-eventbridge)
- [SQS vs Kafka](#python-25-sqs-vs-kafka)
- [Amazon MSK](#python-25-amazon-msk)
- [CloudWatch](#python-25-cloudwatch)
- [CloudTrail](#python-25-cloudtrail)
- [KMS](#python-25-kms)
- [Secrets Manager vs Parameter Store](#python-25-secrets-manager-vs-parameter-store)
- [WAF](#python-25-waf)
- [CloudFormation](#python-25-cloudformation)
- [AWS CDK](#python-25-aws-cdk)
- [Infrastructure as Code](#python-25-infrastructure-as-code)
- [RTO e RPO](#python-25-rto-e-rpo)
- [Disaster Recovery](#python-25-disaster-recovery)
- [Well-Architected Framework](#python-25-well-architected-framework)
- [Cost Optimization](#python-25-cost-optimization)
- [NAT Gateway Cost](#python-25-nat-gateway-cost)
- [Resumo de Entrevista](#python-25-resumo-de-entrevista)
- [Lambda com Python](#python-25-lambda-com-python)
- [RDS e SQLAlchemy em AWS](#python-25-rds-e-sqlalchemy-em-aws)

---

<a id="python-25-visao-geral-aws"></a>

## Visão Geral — AWS

> Em entrevistas eu organizo AWS em cinco blocos: compute, networking, storage/database, integração assíncrona e segurança/observabilidade. O ponto principal não é decorar serviços, mas saber escolher entre alternativas considerando disponibilidade, escalabilidade, segurança, custo e operação.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-regiao-e-availability-zone"></a>

## Região e Availability Zone

> Uma Region é uma área geográfica da AWS composta por múltiplas Availability Zones. AZs são locais isolados dentro da mesma região, com conectividade de baixa latência entre si. Para alta disponibilidade regional, normalmente distribuo recursos por mais de uma AZ.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-alta-disponibilidade-multi-az"></a>

## Alta Disponibilidade Multi-AZ

> Multi-AZ reduz impacto da falha de uma zona. Em aplicações web, isso normalmente significa múltiplas instâncias distribuídas entre AZs, balanceador e dependências também redundantes. Não adianta colocar a aplicação em duas AZs se banco ou outro componente crítico continua como ponto único de falha.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-multi-region"></a>

## Multi-Region

> Multi-Region é usado quando requisitos de disponibilidade, disaster recovery, latência global ou residência de dados justificam operar em mais de uma região. O custo e a complexidade aumentam porque preciso tratar replicação, roteamento, consistência e failover entre regiões.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-shared-responsibility-model"></a>

## Shared Responsibility Model

> No modelo de responsabilidade compartilhada, a AWS é responsável pela segurança da nuvem, como datacenters, hardware e infraestrutura física; o cliente é responsável pela segurança na nuvem, como IAM, configuração de rede, dados, patches conforme o serviço e controles da aplicação.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-iam"></a>

## IAM

> IAM controla autenticação e autorização dentro da AWS. Eu trabalho com users, roles e policies, sempre seguindo least privilege: conceder apenas as permissões necessárias, pelo menor tempo e escopo possíveis.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-iam-user-vs-role"></a>

## IAM User vs Role

> IAM User representa uma identidade persistente. IAM Role é uma identidade assumível, normalmente temporária, usada por aplicações, serviços AWS ou usuários federados. Para workloads em EC2, ECS ou Lambda, prefiro roles em vez de access keys estáticas.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-iam-policy"></a>

## IAM Policy

> IAM Policy define permissões com elementos como `Effect`, `Action`, `Resource` e condições. A autorização efetiva resulta da combinação de policies aplicáveis, lembrando que um `Deny` explícito prevalece sobre `Allow`.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-identity-based-vs-resource-based-policy"></a>

## Identity-based vs Resource-based Policy

> Identity-based policy é anexada a user, group ou role. Resource-based policy é anexada ao próprio recurso, como bucket S3 ou fila SQS. Resource policies são especialmente úteis em acesso cross-account.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-sts-e-assumerole"></a>

## STS e AssumeRole

> AWS STS emite credenciais temporárias. `AssumeRole` permite que uma identidade assuma uma role e receba permissões temporárias. Isso é importante para acesso cross-account e para evitar distribuição de credenciais permanentes.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-vpc"></a>

## VPC

> VPC é a rede virtual isolada da AWS. Dentro dela defino CIDR, subnets, route tables, gateways e security groups. Eu normalmente separo recursos públicos e privados conforme a necessidade de exposição.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-subnet-publica-vs-privada"></a>

## Subnet Pública vs Privada

> Uma subnet é pública quando sua tabela de rotas oferece caminho direto para um Internet Gateway. Para uma instância IPv4 acessar a internet por esse caminho, também precisa de endereço público e regras compatíveis. Subnets privadas não têm essa rota direta.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-internet-gateway"></a>

## Internet Gateway

> Internet Gateway conecta uma VPC à internet. Para uma instância ter acesso público, além do IGW é necessário roteamento adequado, endereço público ou equivalente e regras de segurança compatíveis.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-nat-gateway"></a>

## NAT Gateway

> NAT Gateway permite que recursos em subnets privadas iniciem conexões para a internet sem aceitar conexões de entrada iniciadas externamente. É comum para updates ou chamadas externas, mas possui custo relevante, principalmente por volume processado.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-security-group-vs-nacl"></a>

## Security Group vs NACL

> Security Group é stateful e atua no nível da interface de rede ou recurso. NACL é stateless e atua no nível da subnet. Em geral, Security Groups são o principal mecanismo de controle entre workloads; NACLs adicionam uma camada de controle mais ampla.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-route-table"></a>

## Route Table

> Route Table define para onde o tráfego de uma subnet deve ser encaminhado. Exemplos são rota local da VPC, Internet Gateway, NAT Gateway, Transit Gateway ou Peering. Problemas de conectividade frequentemente começam pela análise das rotas.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-vpc-endpoint"></a>

## VPC Endpoint

> VPC Endpoint permite acessar determinados serviços AWS sem passar pela internet pública. Isso melhora a postura de segurança e pode reduzir dependência de NAT para alguns acessos. Existem endpoints do tipo Gateway e Interface, dependendo do serviço.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-ec2"></a>

## EC2

> EC2 fornece máquinas virtuais sob demanda. Em entrevista, eu considero tipo de instância, AMI, storage, networking, autoscaling e estratégia de substituição. Para aplicações stateless, prefiro tratar instâncias como descartáveis e automatizar bootstrap e deploy.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-auto-scaling-group"></a>

## Auto Scaling Group

> Auto Scaling Group mantém uma quantidade desejada de instâncias e pode ajustar capacidade conforme métricas. Ele também substitui instâncias não saudáveis. Normalmente combino ASG com Load Balancer e health checks.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-alb-vs-nlb"></a>

## ALB vs NLB

> Application Load Balancer opera em camada 7 e entende HTTP/HTTPS, permitindo routing por host e path. Network Load Balancer opera em camada 4 e é indicado para TCP/UDP/TLS, alta performance e cenários que exigem características de rede mais baixas.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-ebs"></a>

## EBS

> EBS é block storage normalmente associado a EC2. É adequado para discos de sistema, banco autogerenciado e workloads que precisam de storage em bloco. Persistência e performance dependem do tipo de volume e da configuração.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-efs"></a>

## EFS

> EFS é um filesystem gerenciado acessível por múltiplas instâncias, útil quando várias máquinas precisam compartilhar arquivos. O modelo e a performance são diferentes de EBS, que é block storage.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-s3"></a>

## S3

> S3 é object storage altamente durável e escalável. Eu uso para arquivos, backups, artefatos, data lakes e conteúdo estático. Não é filesystem tradicional nem banco relacional.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-consistencia-do-s3"></a>

## Consistência do S3

> S3 oferece consistência forte para operações de leitura e listagem após escritas bem-sucedidas nos escopos documentados. Isso não torna várias alterações de objetos uma transação única nem elimina caches externos, como uma CDN, que podem servir conteúdo anterior. [Referência](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-s3-versioning"></a>

## S3 Versioning

> Versioning mantém múltiplas versões de um objeto e ajuda na recuperação de exclusões ou sobrescritas acidentais. O trade-off é maior consumo de storage, então normalmente combino com lifecycle policies.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-s3-lifecycle"></a>

## S3 Lifecycle

> Lifecycle permite mover objetos para classes de storage mais econômicas ou expirá-los automaticamente conforme idade. É importante para reduzir custo em dados que envelhecem.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-s3-presigned-url"></a>

## S3 Presigned URL

> Presigned URL fornece acesso temporário a um objeto S3 sem tornar o bucket público. É útil para upload ou download direto pelo cliente, reduzindo tráfego pela aplicação.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-cloudfront"></a>

## CloudFront

> CloudFront é a CDN da AWS. Ele distribui conteúdo a partir de edge locations, reduzindo latência e carga na origem. Pode usar S3, ALB ou outras origens e integrar com controles como WAF.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-route-53"></a>

## Route 53

> Route 53 é o serviço DNS da AWS. Além de resolução DNS, oferece políticas de roteamento como weighted, latency-based e failover. É útil em arquiteturas multi-region e estratégias de recuperação.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-rds"></a>

## RDS

> RDS é o serviço gerenciado para bancos relacionais. A AWS gerencia provisioning, backups e parte da operação conforme configuração. Eu ainda preciso cuidar de schema, índices, queries, capacidade e arquitetura de acesso.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-rds-multi-az-instancia-vs-cluster"></a>

## RDS Multi-AZ: Instância vs Cluster

> Em uma implantação Multi-AZ de instância, o standby serve ao failover e não às leituras. Em um Multi-AZ DB cluster, há instâncias que também atendem leitura. Confirmo a modalidade antes de afirmar se Multi-AZ aumenta a capacidade de consulta. [Referência](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.MultiAZ.html)

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-rds-read-replica"></a>

## RDS Read Replica

> Read Replica é usada para escalar leitura e pode ter replicação assíncrona. Como pode existir lag, eu não assumo leitura imediatamente consistente após uma escrita. Multi-AZ e Read Replica resolvem problemas diferentes.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-aurora"></a>

## Aurora

> Aurora é um banco relacional compatível com MySQL e PostgreSQL, com storage distribuído gerenciado pela AWS. Pode oferecer alta disponibilidade e escala de leitura, mas continua exigindo bom design de schema, índices e queries.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-dynamodb"></a>

## DynamoDB

> DynamoDB é um banco NoSQL key-value/document totalmente gerenciado, projetado para baixa latência e escala horizontal. O design começa pelo padrão de acesso, porque partition key, sort key e índices determinam como os dados serão distribuídos e consultados.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-dynamodb-partition-key"></a>

## DynamoDB Partition Key

> Partition key influencia como os itens são distribuídos internamente. Uma chave com distribuição ruim pode criar hot partitions e limitar throughput. Por isso escolho uma chave com boa cardinalidade e distribuição.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-dynamodb-sort-key"></a>

## DynamoDB Sort Key

> Sort key permite armazenar múltiplos itens sob a mesma partition key e fazer consultas ordenadas ou por intervalo dentro dessa partição. É útil para séries temporais, eventos ou entidades relacionadas.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-dynamodb-gsi-e-lsi"></a>

## DynamoDB GSI e LSI

> GSI cria uma nova chave de acesso com partition/sort key alternativas e é mais flexível. LSI compartilha a mesma partition key da tabela e define outra sort key, com restrições maiores. Eu escolho de acordo com os padrões de acesso necessários.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-dynamodb-consistency"></a>

## DynamoDB Consistency

> DynamoDB suporta leituras eventualmente consistentes por padrão e, em contextos suportados, leituras fortemente consistentes. Strong consistency custa mais capacidade, então escolho conforme a necessidade do caso de uso.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-dynamodb-on-demand-vs-provisioned"></a>

## DynamoDB On-Demand vs Provisioned

> On-Demand simplifica workloads imprevisíveis porque a capacidade é ajustada pelo serviço. Provisioned permite definir capacidade e pode ser mais econômico em carga previsível. A decisão depende de perfil de tráfego e custo.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-elasticache"></a>

## ElastiCache

> ElastiCache oferece mecanismos gerenciados compatíveis com Valkey, Redis OSS e Memcached. Uso para cache e outros acessos de baixa latência conforme o mecanismo, definindo TTL, invalidação, capacidade e comportamento em falhas. [Referência](https://docs.aws.amazon.com/AmazonElastiCache/latest/dg/SelectEngine.html)

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-lambda"></a>

## Lambda

> Lambda executa funções sob demanda sem gerenciar servidores. É adequada para processamento orientado a eventos, automações e APIs de carga compatível. O trade-off envolve cold start, limites de execução, concorrência e observabilidade.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-lambda-cold-start"></a>

## Lambda Cold Start

> Cold start adiciona latência quando um novo ambiente de execução precisa ser inicializado. Em Python, avalio imports, inicialização de clientes, tamanho do pacote e provisioned concurrency quando o requisito justificar.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-lambda-snapstart"></a>

## Lambda SnapStart

> SnapStart reutiliza um snapshot de inicialização em runtimes e configurações compatíveis, reduzindo parte do custo de cold start. Valido conexões, credenciais e dados que precisam ser únicos após a restauração, além das restrições do serviço para o runtime escolhido. [Referência](https://docs.aws.amazon.com/lambda/latest/dg/snapstart.html)

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-lambda-concurrency"></a>

## Lambda Concurrency

> Concurrency representa quantas execuções podem ocorrer simultaneamente. É importante para capacidade e também para proteger dependências downstream. Aumentar concorrência do Lambda não aumenta automaticamente a capacidade do banco ou API chamada.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-lambda-idempotencia"></a>

## Lambda Idempotência

> Como eventos podem ser reentregues ou retries acontecerem, funções Lambda devem ser idempotentes quando produzem efeitos externos. Uso chave idempotente, conditional writes ou registro de processamento conforme o cenário.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-ecs"></a>

## ECS

> ECS é o serviço de orquestração de containers da AWS. Permite executar tasks e services e integrar com Load Balancer, autoscaling e IAM. É mais simples operacionalmente que Kubernetes para muitos workloads AWS-native.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-ecs-service-vs-task"></a>

## ECS Service vs Task

> Task é uma execução de uma task definition. Service mantém uma quantidade desejada de tasks em execução e integra health check, deployment e autoscaling.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-fargate"></a>

## Fargate

> Fargate permite executar containers em ECS ou EKS sem gerenciar diretamente instâncias EC2. Simplifica operação e isolamento de capacidade, com trade-off de custo e menor controle sobre a infraestrutura subjacente.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-eks"></a>

## EKS

> EKS fornece Kubernetes com plano de controle gerenciado pela AWS. A responsabilidade pelos nós varia conforme o modo de operação; continuo responsável por workloads, permissões, configuração e dados, mesmo quando parte maior da infraestrutura é automatizada.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-ecs-vs-eks"></a>

## ECS vs EKS

> ECS é mais simples e integrado ao ecossistema AWS. EKS oferece o ecossistema e a portabilidade do Kubernetes, mas aumenta complexidade operacional. Eu escolheria EKS quando existe necessidade real de Kubernetes; caso contrário, ECS pode ser suficiente.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-ecs-vs-lambda"></a>

## ECS vs Lambda

> ECS é mais apropriado para serviços long-running e workloads containerizados com maior controle do runtime. Lambda é adequada para execução orientada a eventos e curta duração. A escolha depende do modelo de execução, duração, dependências e operação.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-api-gateway"></a>

## API Gateway

> API Gateway fornece gerenciamento de APIs com roteamento, autenticação, throttling e integração com Lambda ou backends HTTP. É especialmente comum em arquiteturas serverless. Para serviços containerizados simples, um ALB pode ser mais direto.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-api-gateway-vs-alb"></a>

## API Gateway vs ALB

> API Gateway oferece API management, throttling, authorization e integração serverless. ALB é um load balancer HTTP de camada 7 e costuma ser mais simples para rotear tráfego a ECS ou EC2. A escolha depende das funcionalidades necessárias.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-sqs"></a>

## SQS

> SQS é uma fila gerenciada para desacoplamento assíncrono. Produtores enviam mensagens e consumidores processam independentemente. É útil para absorver picos, retry e reduzir acoplamento temporal entre serviços.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-sqs-standard-vs-fifo"></a>

## SQS Standard vs FIFO

> SQS Standard oferece entrega pelo menos uma vez e ordenação best-effort. FIFO ordena por grupo de mensagens e oferece deduplicação dentro de suas condições, mas o consumidor ainda deve tolerar reprocessamento, por exemplo após falha antes da exclusão da mensagem.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-sqs-visibility-timeout"></a>

## SQS Visibility Timeout

> Visibility Timeout esconde temporariamente uma mensagem após ela ser recebida. Se o consumidor não a remover antes do timeout, ela pode ficar disponível novamente. O valor precisa considerar o tempo esperado de processamento.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-sqs-dlq"></a>

## SQS DLQ

> Dead Letter Queue recebe mensagens que falharam repetidamente após o limite configurado. Ela evita retry infinito e facilita inspeção, mas precisa de processo de análise e redrive.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-sqs-long-polling"></a>

## SQS Long Polling

> Long polling espera por mensagens durante um intervalo em vez de retornar imediatamente vazio. Isso reduz requests desnecessários e custo quando a fila tem baixo volume.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-sns"></a>

## SNS

> SNS é um serviço pub/sub. Um publisher envia uma mensagem para um tópico e múltiplos subscribers podem recebê-la. É adequado para fan-out.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-sns-sqs"></a>

## SNS + SQS

> Combinar SNS com SQS permite fan-out com isolamento por consumidor. Cada serviço recebe sua própria fila, podendo processar em ritmo diferente e aplicar retry e DLQ independentemente.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-eventbridge"></a>

## EventBridge

> EventBridge é um event bus gerenciado com regras de roteamento baseadas no conteúdo do evento e integração ampla com serviços AWS e SaaS. É útil quando o roteamento é mais rico que um simples fan-out.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-sns-vs-eventbridge"></a>

## SNS vs EventBridge

> SNS é excelente para pub/sub e fan-out simples. EventBridge oferece regras de roteamento, event buses e integração ampla. Eu escolho conforme necessidade de filtragem, roteamento e governança.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-sqs-vs-kafka"></a>

## SQS vs Kafka

> SQS é uma fila gerenciada simples, ótima para desacoplamento com baixa operação. Kafka oferece log particionado, replay, consumer groups e streaming de alto throughput. Escolho Kafka quando preciso de histórico, replay e múltiplos consumidores independentes; SQS quando uma fila de trabalho é suficiente.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-amazon-msk"></a>

## Amazon MSK

> Amazon MSK oferece Apache Kafka gerenciado, reduzindo parte da operação do cluster. A aplicação ainda precisa definir partições, retenção, segurança, chaves, commits e idempotência; serviço gerenciado não elimina o design do fluxo de eventos.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-cloudwatch"></a>

## CloudWatch

> CloudWatch centraliza métricas, logs, alarms e dashboards de serviços AWS. Em produção, monitoro latência, taxa de erro, saturação, throttling, filas, CPU, memória quando disponível e métricas de negócio.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-cloudtrail"></a>

## CloudTrail

> CloudTrail registra eventos de API e atividades na conta AWS. É fundamental para auditoria, investigação de mudanças e segurança. Não substitui logs de aplicação.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-kms"></a>

## KMS

> KMS gerencia chaves criptográficas e integra com diversos serviços AWS. Eu uso para criptografia de dados e secrets, controlando quem pode usar cada chave através de IAM e key policies.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-secrets-manager-vs-parameter-store"></a>

## Secrets Manager vs Parameter Store

> Secrets Manager é focado em secrets e oferece recursos como rotação integrada em vários cenários. Parameter Store é útil para configuração e também pode armazenar valores seguros. Eu escolho considerando rotação, integração e custo.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-waf"></a>

## WAF

> AWS WAF aplica regras de camada 7 para proteger aplicações web contra padrões maliciosos, bots e ataques comuns. Pode ser associado a serviços como CloudFront e ALB. Ele complementa, mas não substitui segurança da aplicação.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-cloudformation"></a>

## CloudFormation

> CloudFormation permite declarar infraestrutura como código usando templates. Isso melhora repetibilidade, versionamento e automação. O trade-off é lidar com ciclo de vida e dependências dos stacks.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-aws-cdk"></a>

## AWS CDK

> AWS CDK permite definir infraestrutura usando linguagens de programação e sintetiza templates de CloudFormation. Ele melhora abstração e reutilização, mas continua baseado no modelo de recursos e deployment do CloudFormation.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-infrastructure-as-code"></a>

## Infrastructure as Code

> IaC torna infraestrutura versionável, reproduzível e auditável. Eu evito alterações manuais em produção porque geram drift. Seja com Terraform, CloudFormation ou CDK, o objetivo é manter infraestrutura dentro do processo de entrega.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-rto-e-rpo"></a>

## RTO e RPO

> RTO é quanto tempo o sistema pode ficar indisponível até recuperação. RPO é quanto de dados pode ser perdido em termos de tempo. Esses requisitos determinam estratégia de backup, replicação, failover e custo.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-disaster-recovery"></a>

## Disaster Recovery

> Estratégias de DR variam de backup/restore até warm standby e active-active. Quanto menores RTO e RPO, maior o custo e a complexidade. Eu começo pelos requisitos de negócio antes de escolher a estratégia.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-well-architected-framework"></a>

## Well-Architected Framework

> O AWS Well-Architected Framework organiza boas práticas em pilares como excelência operacional, segurança, confiabilidade, eficiência de performance, otimização de custos e sustentabilidade. Eu o uso como estrutura para justificar decisões, não como checklist decorado.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-cost-optimization"></a>

## Cost Optimization

> Otimização de custo envolve right sizing, autoscaling, desligar recursos ociosos, storage adequado, compromissos de uso quando a carga é previsível e atenção a data transfer. A arquitetura mais barata é a que atende requisitos sem excesso.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-nat-gateway-cost"></a>

## NAT Gateway Cost

> NAT Gateway possui custo por hora e por volume processado. Em arquiteturas com muito acesso a serviços AWS, VPC Endpoints podem reduzir tráfego pelo NAT e melhorar segurança, dependendo do padrão de uso.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Escolho serviços AWS pelos requisitos de computação, rede, dados e integração. Uso IAM e credenciais temporárias, considero redundância e recuperação e automatizo infraestrutura. Serviços gerenciados reduzem trabalho operacional, mas continuam exigindo atenção a segurança, capacidade, consistência e custo.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-lambda-com-python"></a>

## Lambda com Python

> Avalio cold start, inicialização de clientes fora do handler, timeout, concorrência, idempotência e pacote de dependências. Não presumo persistência de estado entre invocações.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-25-rds-e-sqlalchemy-em-aws"></a>

## RDS e SQLAlchemy em AWS

> Controlo conexões considerando workers, autoscaling e limites do RDS; timeouts e pool inadequados podem derrubar banco mesmo com CPU da aplicação disponível.

[↑ Sumário do tópico](#python-25-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-sumario"></a>

# 26. CI/CD e Entrega de Aplicações Python

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral — CI/CD](#python-26-visao-geral-ci-cd)
- [Continuous Integration](#python-26-continuous-integration)
- [Continuous Delivery vs Continuous Deployment](#python-26-continuous-delivery-vs-continuous-deployment)
- [Pipeline CI/CD](#python-26-pipeline-ci-cd)
- [Fail Fast](#python-26-fail-fast)
- [Build Reprodutível](#python-26-build-reprodutivel)
- [Artefato Imutável](#python-26-artefato-imutavel)
- [Versionamento de Artefato](#python-26-versionamento-de-artefato)
- [Git Strategy](#python-26-git-strategy)
- [Trunk-Based Development](#python-26-trunk-based-development)
- [Pull Request e Code Review](#python-26-pull-request-e-code-review)
- [Branch Protection](#python-26-branch-protection)
- [Testes Unitários na Pipeline](#python-26-testes-unitarios-na-pipeline)
- [Testes de Integração](#python-26-testes-de-integracao)
- [Testcontainers](#python-26-testcontainers)
- [Contract Testing](#python-26-contract-testing)
- [End-to-End Tests](#python-26-end-to-end-tests)
- [Test Pyramid](#python-26-test-pyramid)
- [Flaky Tests](#python-26-flaky-tests)
- [Quality Gate](#python-26-quality-gate)
- [SonarQube](#python-26-sonarqube)
- [Cobertura de Testes](#python-26-cobertura-de-testes)
- [SAST](#python-26-sast)
- [SCA](#python-26-sca)
- [DAST](#python-26-dast)
- [SBOM](#python-26-sbom)
- [Supply Chain Security](#python-26-supply-chain-security)
- [Secrets no Pipeline](#python-26-secrets-no-pipeline)
- [Docker Build em CI](#python-26-docker-build-em-ci)
- [Multi-stage Docker Build](#python-26-multi-stage-docker-build)
- [Imagem Docker Imutável](#python-26-imagem-docker-imutavel)
- [Image Scanning](#python-26-image-scanning)
- [Registry](#python-26-registry)
- [Environment Promotion](#python-26-environment-promotion)
- [Configuração por Ambiente](#python-26-configuracao-por-ambiente)
- [Feature Flag](#python-26-feature-flag)
- [Rolling Deployment](#python-26-rolling-deployment)
- [Blue-Green Deployment](#python-26-blue-green-deployment)
- [Canary Deployment](#python-26-canary-deployment)
- [Rolling vs Blue-Green vs Canary](#python-26-rolling-vs-blue-green-vs-canary)
- [Rollback](#python-26-rollback)
- [Roll Forward](#python-26-roll-forward)
- [Database Migration](#python-26-database-migration)
- [Zero-Downtime Database Migration](#python-26-zero-downtime-database-migration)
- [Expand and Contract](#python-26-expand-and-contract)
- [Backward Compatibility no Deploy](#python-26-backward-compatibility-no-deploy)
- [Health Check Pós-Deploy](#python-26-health-check-pos-deploy)
- [Smoke Test](#python-26-smoke-test)
- [Observabilidade Pós-Deploy](#python-26-observabilidade-pos-deploy)
- [Automated Rollback](#python-26-automated-rollback)
- [Approval Gate](#python-26-approval-gate)
- [Pipeline as Code](#python-26-pipeline-as-code)
- [Jenkins](#python-26-jenkins)
- [GitHub Actions](#python-26-github-actions)
- [GitLab CI](#python-26-gitlab-ci)
- [Runner Self-hosted vs Hosted](#python-26-runner-self-hosted-vs-hosted)
- [Cache na Pipeline](#python-26-cache-na-pipeline)
- [Paralelismo na Pipeline](#python-26-paralelismo-na-pipeline)
- [Artifacts entre Jobs](#python-26-artifacts-entre-jobs)
- [Idempotência do Deploy](#python-26-idempotencia-do-deploy)
- [Infraestrutura como Código no CI/CD](#python-26-infraestrutura-como-codigo-no-ci-cd)
- [GitOps](#python-26-gitops)
- [CI/CD em Kubernetes](#python-26-ci-cd-em-kubernetes)
- [CI/CD em ECS](#python-26-ci-cd-em-ecs)
- [Graceful Shutdown](#python-26-graceful-shutdown)
- [Readiness e Liveness](#python-26-readiness-e-liveness)
- [Dependency Management](#python-26-dependency-management)
- [Pipeline para Microsserviços](#python-26-pipeline-para-microsservicos)
- [Pipeline Monorepo](#python-26-pipeline-monorepo)
- [Pipeline Failure — Como Investigar](#python-26-pipeline-failure-como-investigar)
- [Lead Time](#python-26-lead-time)
- [Deployment Frequency](#python-26-deployment-frequency)
- [Change Failure Rate](#python-26-change-failure-rate)
- [MTTR e Failed Deployment Recovery Time](#python-26-mttr-e-failed-deployment-recovery-time)
- [Deployment Rework Rate](#python-26-deployment-rework-rate)
- [DORA Metrics](#python-26-dora-metrics)
- [CI/CD e Segurança](#python-26-ci-cd-e-seguranca)
- [CI/CD e Compliance](#python-26-ci-cd-e-compliance)
- [Resumo de Entrevista](#python-26-resumo-de-entrevista)
- [Pipeline Python com Pytest e Ruff](#python-26-pipeline-python-com-pytest-e-ruff)
- [`pyproject.toml` e Lock no CI](#python-26-pyproject-toml-e-lock-no-ci)
- [Alembic e Migrations em Deploy](#python-26-alembic-e-migrations-em-deploy)
- [Release ASGI no Kubernetes](#python-26-release-asgi-no-kubernetes)

---

<a id="python-26-visao-geral-ci-cd"></a>

## Visão Geral — CI/CD

> CI/CD automatiza integração, validação, empacotamento e entrega. Em Python, o fluxo inclui ambiente reproduzível, lint, checagem de tipos, pytest, verificação de dependências, wheel ou imagem, registry e deploy. O objetivo é feedback rápido e releases auditáveis.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-continuous-integration"></a>

## Continuous Integration

> Continuous Integration significa integrar mudanças pequenas e frequentes e executar validações automaticamente. Em Python, incluo lint, type checking, pytest e testes de integração conforme o risco.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-continuous-delivery-vs-continuous-deployment"></a>

## Continuous Delivery vs Continuous Deployment

> Continuous Delivery mantém o software sempre em estado implantável, mas o deploy em produção pode depender de aprovação. Continuous Deployment leva isso adiante e promove automaticamente para produção quando todos os gates passam. A diferença principal é o último passo de promoção.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-pipeline-ci-cd"></a>

## Pipeline CI/CD

> Uma pipeline Python pode executar checkout, instalação reproduzível, Ruff, mypy/pyright, pytest, testes de integração, empacotamento, imagem e deploy, seguido de smoke test e verificação de métricas.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-fail-fast"></a>

## Fail Fast

> Fail fast significa executar primeiro as validações mais rápidas e baratas. Por exemplo, compilar e rodar testes unitários antes de testes de integração ou deploy. Isso reduz feedback time e custo de pipeline.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-build-reprodutivel"></a>

## Build Reprodutível

> Um build reproduzível deve produzir o mesmo resultado a partir da mesma revisão e configuração. Para isso, fixo versões, uso wrappers, evito dependências dinâmicas, controlo plugins e não dependo de estado local do runner.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-artefato-imutavel"></a>

## Artefato Imutável

> Eu prefiro construir o artefato uma única vez e promover o mesmo binário ou imagem entre ambientes. Rebuildar em cada ambiente pode gerar diferenças de dependência ou configuração e quebra a garantia de que o que foi testado é exatamente o que chegou à produção.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-versionamento-de-artefato"></a>

## Versionamento de Artefato

> Eu evito sobrescrever versões publicadas. Normalmente uso versão única por build, associada a commit ou release. Isso facilita rollback, rastreabilidade e reprodução de incidentes.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-git-strategy"></a>

## Git Strategy

> A estratégia de Git deve reduzir branches longas e integração tardia. Trunk-based development favorece branches curtas e integração frequente. Git Flow pode funcionar em alguns contextos, mas adiciona mais branches e coordenação. Eu escolho de acordo com frequência de release e maturidade do time.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-trunk-based-development"></a>

## Trunk-Based Development

> Trunk-based development incentiva branches pequenas e curtas, integradas rapidamente ao trunk. Isso reduz divergência, conflitos e tempo até integração. Feature flags ajudam quando funcionalidades ainda não estão prontas para exposição.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-pull-request-e-code-review"></a>

## Pull Request e Code Review

> Pull Request serve como gate técnico e de colaboração. Eu combino review com validações automáticas para evitar depender apenas de revisão humana. Branch protection deve impedir merge quando build, testes ou checks obrigatórios falham.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-branch-protection"></a>

## Branch Protection

> Branch protection normalmente exige review, checks verdes e bloqueia push direto em branches críticas. Isso reduz mudanças não auditadas e garante que a política de qualidade seja aplicada de forma consistente.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-testes-unitarios-na-pipeline"></a>

## Testes Unitários na Pipeline

> Testes unitários devem rodar cedo porque são rápidos e dão feedback imediato. Eles validam comportamento isolado e ajudam a impedir regressões antes de etapas mais caras.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-testes-de-integracao"></a>

## Testes de Integração

> Testes de integração validam interação entre componentes reais, como banco, fila ou serviços. São mais caros e lentos, então normalmente ficam em estágio posterior ao unit test, mas antes de promover o artefato.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-testcontainers"></a>

## Testcontainers

> Testcontainers permite subir dependências reais, como PostgreSQL, Kafka ou Redis, durante testes de integração. Isso reduz diferenças entre mock e ambiente real, embora aumente tempo e consumo de recursos da pipeline.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-contract-testing"></a>

## Contract Testing

> Contract tests verificam compatibilidade entre produtor e consumidor sem exigir um ambiente end-to-end completo. São úteis em microsserviços para detectar breaking changes cedo e reduzir dependência de testes integrados frágeis.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-end-to-end-tests"></a>

## End-to-End Tests

> Testes end-to-end validam o fluxo completo, mas são lentos e mais suscetíveis a flakiness. Eu mantenho poucos E2E focados em jornadas críticas e deixo a maior parte da cobertura em testes unitários, integração e contrato.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-test-pyramid"></a>

## Test Pyramid

> A test pyramid prioriza muitos testes unitários, uma quantidade menor de integração e poucos end-to-end. O objetivo é equilibrar velocidade, confiança e custo de manutenção.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-flaky-tests"></a>

## Flaky Tests

> Teste flaky falha de forma não determinística e reduz confiança na pipeline. Eu trato flakiness como defeito: investigo concorrência, timing, dependência externa ou estado compartilhado em vez de simplesmente adicionar retry indefinido.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-quality-gate"></a>

## Quality Gate

> Quality gate define condições mínimas para promoção, como testes aprovados, análise estática aceitável e ausência de vulnerabilidades críticas. O gate deve bloquear problemas reais, não virar uma coleção de métricas arbitrárias.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-sonarqube"></a>

## SonarQube

> SonarQube analisa qualidade e segurança do código, como bugs potenciais, code smells e vulnerabilidades. Eu uso como sinal automatizado dentro da pipeline, mas não substituo revisão técnica nem testes.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-cobertura-de-testes"></a>

## Cobertura de Testes

> Cobertura mede código executado pelos testes, não qualidade dos testes. Eu uso coverage para identificar áreas sem validação, mas evito metas cegas que incentivem testes sem valor.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-sast"></a>

## SAST

> SAST analisa código-fonte ou bytecode procurando vulnerabilidades sem executar a aplicação. É útil cedo na pipeline para detectar padrões inseguros.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-sca"></a>

## SCA

> Software Composition Analysis identifica vulnerabilidades conhecidas nas dependências. Em Python, verifico bibliotecas transitivas, pacotes instalados, artefatos PyPI e imagens container.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-dast"></a>

## DAST

> DAST testa a aplicação em execução, simulando ataques ou comportamentos maliciosos. Normalmente roda em ambiente implantado e complementa SAST e SCA.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-sbom"></a>

## SBOM

> SBOM é uma lista estruturada dos componentes e dependências que formam o software. Ajuda a responder rapidamente se uma aplicação usa uma biblioteca vulnerável e melhora rastreabilidade da supply chain.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-supply-chain-security"></a>

## Supply Chain Security

> Segurança da supply chain inclui dependências, plugins, imagens, runners, registry e processo de build. Eu evito binários não verificados, mantenho dependências controladas, aplico scanning e protejo credenciais do pipeline.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-secrets-no-pipeline"></a>

## Secrets no Pipeline

> Secrets nunca devem ficar hardcoded no repositório ou nos logs. Uso secret stores da plataforma de CI/CD ou serviços como Secrets Manager e Vault e limito acesso por ambiente e job.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-docker-build-em-ci"></a>

## Docker Build em CI

> Na pipeline, construo a imagem Docker depois que código e testes passam. A imagem deve ser versionada de forma imutável, por exemplo com tag de commit, e publicada em registry.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-multi-stage-docker-build"></a>

## Multi-stage Docker Build

> Multi-stage build separa instalação/compilação de wheels do runtime. Em Python, levo para a imagem final somente dependências instaladas e código necessário, sem build toolchain desnecessária.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-imagem-docker-imutavel"></a>

## Imagem Docker Imutável

> Eu não reutilizo a mesma tag mutável para representar builds diferentes em produção. Prefiro tags imutáveis por commit ou versão e, quando possível, referência por digest.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-image-scanning"></a>

## Image Scanning

> Image scanning identifica vulnerabilidades conhecidas no sistema operacional e nas dependências da imagem. Eu incluo esse check antes da promoção e também reavalio imagens já publicadas porque novas CVEs surgem depois do build.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-registry"></a>

## Registry

> Registry armazena imagens versionadas, como ECR ou outro registry. Além do armazenamento, considero políticas de retenção, scanning, permissões e imutabilidade de tags.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-environment-promotion"></a>

## Environment Promotion

> Eu promovo o mesmo artefato entre dev, staging e produção, mudando apenas configuração externa. Isso mantém rastreabilidade e reduz diferenças entre ambientes.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-configuracao-por-ambiente"></a>

## Configuração por Ambiente

> Configuração específica de ambiente deve ser externa ao artefato, por exemplo variáveis, ConfigMap, Secrets ou serviço de configuração. O código e a imagem devem permanecer iguais.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-feature-flag"></a>

## Feature Flag

> Feature flag desacopla deploy de release. Posso colocar código em produção desabilitado e ativá-lo gradualmente. O cuidado é evitar flags permanentes e combinações demais, que aumentam complexidade.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-rolling-deployment"></a>

## Rolling Deployment

> Rolling deployment substitui instâncias gradualmente. Tem baixo custo de infraestrutura e é simples, mas versões antiga e nova coexistem durante o rollout, então compatibilidade entre versões é importante.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-blue-green-deployment"></a>

## Blue-Green Deployment

> Blue-Green mantém dois ambientes: o atual e o novo. Após validação, o tráfego é trocado para o novo ambiente. O rollback é rápido, mas exige capacidade duplicada durante a transição.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-canary-deployment"></a>

## Canary Deployment

> Canary envia uma pequena parcela do tráfego para a nova versão e aumenta gradualmente se as métricas estiverem saudáveis. Reduz blast radius, mas exige bom roteamento e observabilidade.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-rolling-vs-blue-green-vs-canary"></a>

## Rolling vs Blue-Green vs Canary

> Rolling é simples e econômico, mas mistura versões. Blue-Green facilita rollback rápido com custo de capacidade duplicada. Canary reduz risco expondo progressivamente, porém exige automação e métricas maduras. Eu escolho conforme criticidade, custo e capacidade operacional.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-rollback"></a>

## Rollback

> Rollback retorna rapidamente para uma versão conhecida. Para funcionar, o artefato anterior deve estar disponível e mudanças de banco precisam ser compatíveis. Nem todo incidente permite rollback simples, principalmente quando dados já foram transformados.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-roll-forward"></a>

## Roll Forward

> Roll forward corrige o problema com uma nova versão em vez de voltar. É útil quando rollback de schema ou dados é arriscado. Em sistemas modernos, muitas equipes preferem roll forward para mudanças irreversíveis.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-database-migration"></a>

## Database Migration

> Migração de banco entra no pipeline por ferramentas como Alembic ou migrations Django. Versiono scripts e uso expand-and-contract para compatibilidade com instâncias antigas durante o rollout.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-zero-downtime-database-migration"></a>

## Zero-Downtime Database Migration

> Para zero downtime, faço mudanças backward compatible em etapas. Primeiro adiciono coluna ou estrutura nova sem remover a antiga, depois atualizo aplicação para usar o novo formato, migro dados e só por último removo o legado quando nenhuma versão depende dele.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-expand-and-contract"></a>

## Expand and Contract

> Expand and Contract é uma estratégia de evolução de schema em duas fases. Expand adiciona estruturas compatíveis; Contract remove estruturas antigas somente depois que todos os consumidores migraram. É essencial em rolling deployments.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-backward-compatibility-no-deploy"></a>

## Backward Compatibility no Deploy

> Durante rollout, versões antiga e nova podem rodar juntas. Por isso APIs, eventos e schema precisam permanecer compatíveis durante a transição. Breaking changes exigem estratégia em múltiplas etapas.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-health-check-pos-deploy"></a>

## Health Check Pós-Deploy

> Depois do deployment, verifico readiness, liveness e health checks relevantes. O objetivo é confirmar que a nova versão está apta a receber tráfego e que dependências críticas estão funcionando.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-smoke-test"></a>

## Smoke Test

> Smoke test é um conjunto pequeno de testes após o deploy para validar fluxos essenciais. Ele detecta rapidamente problemas de configuração, rota, banco ou dependência antes de liberar tráfego completo.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-observabilidade-pos-deploy"></a>

## Observabilidade Pós-Deploy

> Deployment não termina quando o container sobe. Eu observo erro, latência, p95/p99, CPU, memória, GC, pool de conexões e métricas de negócio para confirmar que a nova versão está saudável.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-automated-rollback"></a>

## Automated Rollback

> Rollback automático pode ser disparado quando métricas ou health checks ultrapassam limites durante canary ou rollout. Eu uso com critérios bem definidos para evitar rollback por ruído.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-approval-gate"></a>

## Approval Gate

> Approval gate adiciona aprovação manual antes de promover para ambiente crítico. Faz sentido em organizações reguladas ou quando o risco exige controle adicional. Em fluxos maduros e bem automatizados, o objetivo pode ser reduzir gates manuais desnecessários.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-pipeline-as-code"></a>

## Pipeline as Code

> Pipeline as Code mantém a definição da pipeline versionada junto do código ou em repositório controlado. Isso facilita review, histórico, reprodução e evolução do processo de entrega.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-jenkins"></a>

## Jenkins

> Jenkins é um servidor de automação flexível e extensível. Permite pipelines complexas via Jenkinsfile, mas exige operação, atualização de plugins, segurança e manutenção de agentes.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-github-actions"></a>

## GitHub Actions

> GitHub Actions integra workflows diretamente ao repositório. É conveniente para CI/CD e automação, mas eu considero segurança de actions de terceiros, permissões do token, secrets e custo dos runners.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-gitlab-ci"></a>

## GitLab CI

> GitLab CI define pipelines em arquivo versionado e integra build, test e deploy à plataforma. O conceito é semelhante a outras ferramentas: stages, jobs, runners, artifacts e environments.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-runner-self-hosted-vs-hosted"></a>

## Runner Self-hosted vs Hosted

> Runner hospedado simplifica operação. Self-hosted oferece mais controle de rede, performance e ferramentas, mas aumenta responsabilidade de patching, isolamento e segurança.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-cache-na-pipeline"></a>

## Cache na Pipeline

> Cache da pipeline acelera instalação de dependências e ferramentas. Em Python, cache de pacotes, wheels e uv/pip deve ser invalidado por lockfile e configuração adequados.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-paralelismo-na-pipeline"></a>

## Paralelismo na Pipeline

> Stages independentes podem rodar em paralelo, como testes unitários, análise estática e scanning. Isso reduz lead time, desde que não aumente excessivamente custo ou competição por recursos.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-artifacts-entre-jobs"></a>

## Artifacts entre Jobs

> Artifacts transportam resultados de uma etapa à outra, como wheels, imagens, relatórios e SBOM. Prefiro promover o mesmo pacote aprovado em vez de reconstruí-lo em cada estágio.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-idempotencia-do-deploy"></a>

## Idempotência do Deploy

> Deploy idempotente significa que executar o mesmo processo novamente leva ao mesmo estado desejado sem efeitos colaterais indevidos. Isso é importante para retries automáticos e recuperação de falhas.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-infraestrutura-como-codigo-no-ci-cd"></a>

## Infraestrutura como Código no CI/CD

> IaC integra mudanças de infraestrutura ao mesmo processo de review e automação. Eu executo validação e plan antes de aplicar mudanças, mantendo auditabilidade e reduzindo drift manual.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-gitops"></a>

## GitOps

> GitOps usa Git como fonte declarativa do estado desejado e um controlador aplica mudanças no ambiente. É comum em Kubernetes. O benefício é auditabilidade e reconciliação contínua; o trade-off é adicionar outro modelo operacional.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-ci-cd-em-kubernetes"></a>

## CI/CD em Kubernetes

> Em Kubernetes, a pipeline normalmente constrói e publica a imagem e depois atualiza manifests, Helm chart ou configuração GitOps. O rollout deve considerar readiness, strategy, recursos, autoscaling e rollback.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-ci-cd-em-ecs"></a>

## CI/CD em ECS

> Em ECS, a pipeline publica a imagem no registry, cria uma nova task definition e atualiza o service. Eu acompanho health checks e deployment status e mantenho revisão anterior disponível para rollback.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-graceful-shutdown"></a>

## Graceful Shutdown

> Graceful shutdown permite que a instância pare de aceitar novas requisições e termine trabalho em andamento antes de encerrar. É importante em rolling deploys para evitar requests interrompidas.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-readiness-e-liveness"></a>

## Readiness e Liveness

> Readiness indica se a instância está pronta para receber tráfego. Liveness indica se o processo está saudável o suficiente para continuar vivo. Confundir os dois pode causar reinícios desnecessários ou tráfego para instâncias não prontas.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-dependency-management"></a>

## Dependency Management

> Fixo versões do Python e das dependências com pyproject.toml e lockfiles, aplico políticas de atualização e reconstruo o ambiente do zero na pipeline para detectar dependências implícitas.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-pipeline-para-microsservicos"></a>

## Pipeline para Microsserviços

> Cada microsserviço deve ter pipeline independente sempre que possível. Um deploy de um serviço não deveria exigir rebuild de todos os outros. Contratos, testes e versionamento devem permitir autonomia.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-pipeline-monorepo"></a>

## Pipeline Monorepo

> Em monorepo, evito rebuildar tudo quando apenas um módulo mudou. Posso detectar paths impactados e executar pipelines seletivas, mantendo também uma validação global quando necessário.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-pipeline-failure-como-investigar"></a>

## Pipeline Failure — Como Investigar

> Eu identifico primeiro o estágio que falhou: build, teste, análise, publicação ou deploy. Depois separo erro de código de erro de infraestrutura, verifico logs, artefatos, versão das ferramentas, credenciais e diferenças de ambiente. Evito simplesmente reexecutar até passar sem entender a causa.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-lead-time"></a>

## Lead Time

> Lead time mede quanto tempo uma mudança leva desde o commit até produção. Pipelines lentas aumentam batch size e atrasam feedback. Eu reduzo lead time com fail fast, paralelismo, cache e testes bem distribuídos.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-deployment-frequency"></a>

## Deployment Frequency

> Deployment frequency mostra com que frequência o time consegue colocar mudanças em produção. Frequência alta normalmente depende de automação, testes confiáveis e mudanças pequenas.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-change-failure-rate"></a>

## Change Failure Rate

> Change failure rate mede a proporção de deploys que causam incidente, rollback ou correção urgente. É um indicador importante da qualidade do processo de entrega.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-mttr-e-failed-deployment-recovery-time"></a>

## MTTR e Failed Deployment Recovery Time

> MTTR é uma medida genérica de tempo médio de recuperação, cujo escopo precisa ser definido. DORA usa Failed Deployment Recovery Time para a recuperação de deploys que falham e exigem intervenção imediata; distingo esse recorte dos demais incidentes operacionais.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-deployment-rework-rate"></a>

## Deployment Rework Rate

> Mede a proporção de deploys não planejados realizados em consequência de incidentes em produção. Complementa a taxa de falha ao mostrar quanto da atividade de entrega está sendo consumida por recuperação e correções urgentes.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-dora-metrics"></a>

## DORA Metrics

> O modelo DORA considera cinco métricas: change lead time, deployment frequency, failed deployment recovery time, change failure rate e deployment rework rate. Avalio entrega e instabilidade no contexto de uma aplicação ou serviço, evitando usá-las como ranking individual. [Referência](https://dora.dev/guides/dora-metrics/)

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-ci-cd-e-seguranca"></a>

## CI/CD e Segurança

> Pipeline possui acesso a código, artefatos, ambientes e credenciais, então é parte da superfície de ataque. Eu aplico least privilege, runners isolados, actions ou plugins confiáveis, secrets protegidos, branch protection e auditoria.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-ci-cd-e-compliance"></a>

## CI/CD e Compliance

> Em ambientes regulados, a pipeline pode registrar aprovações, artefatos, evidências de testes, versão implantada e histórico de mudanças. Automação melhora auditabilidade quando cada passo deixa evidência rastreável.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-resumo-de-entrevista"></a>

## Resumo de Entrevista

> CI/CD automatiza build, testes, segurança, empacotamento e entrega. Promovo o mesmo artefato, evoluo schemas de forma compatível e escolho a estratégia de deploy conforme risco e observabilidade. Após implantar, verifico comportamento e mantenho uma estratégia viável de recuperação.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-pipeline-python-com-pytest-e-ruff"></a>

## Pipeline Python com Pytest e Ruff

> Executo instalação reproduzível, lint/format, checagem estática, testes unitários e integração. Promovo o mesmo artefato aprovado em vez de reconstruir independentemente por ambiente.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-pyproject-toml-e-lock-no-ci"></a>

## `pyproject.toml` e Lock no CI

> A configuração do projeto e as versões resolvidas participam da revisão de código. Verifico drift entre lock e dependências declaradas e falho a pipeline quando inconsistente.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-alembic-e-migrations-em-deploy"></a>

## Alembic e Migrations em Deploy

> Aplico migrações revisadas com estratégia expand-and-contract, verificando compatibilidade de versões em rolling, blue-green e canary.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-26-release-asgi-no-kubernetes"></a>

## Release ASGI no Kubernetes

> Verifico startup, readiness, liveness, drains e encerramento controlado de workers, além de métricas e smoke tests após a implantação.

[↑ Sumário do tópico](#python-26-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-sumario"></a>

# 27. Django e Django REST Framework

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-27-visao-geral)
- [Projeto vs App](#python-27-projeto-vs-app)
- [Settings e Ambientes](#python-27-settings-e-ambientes)
- [ORM Django](#python-27-orm-django)
- [QuerySets e Lazy Evaluation](#python-27-querysets-e-lazy-evaluation)
- [`select_related` vs `prefetch_related`](#python-27-select-related-vs-prefetch-related)
- [Migrations Django](#python-27-migrations-django)
- [Middleware e Request Lifecycle](#python-27-middleware-e-request-lifecycle)
- [Autenticação e Permissões](#python-27-autenticacao-e-permissoes)
- [Django REST Framework](#python-27-django-rest-framework)
- [Serializers e Validação](#python-27-serializers-e-validacao)
- [ViewSets vs APIView](#python-27-viewsets-vs-apiview)
- [Transações Atômicas](#python-27-transacoes-atomicas)
- [Async no Django](#python-27-async-no-django)
- [Django vs FastAPI](#python-27-django-vs-fastapi)
- [Resumo de Entrevista](#python-27-resumo-de-entrevista)

---

<a id="python-27-visao-geral"></a>

## Visão Geral

> Django é framework web com ORM, autenticação, administração e convenções integradas. Estudo como alternativa ao FastAPI quando o produto se beneficia de um ecossistema full-stack e produtividade em CRUD.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-projeto-vs-app"></a>

## Projeto vs App

> Um projeto reúne configuração e aplicações; apps encapsulam funcionalidades reutilizáveis. Divisão por responsabilidade de negócio evita apps gigantes sem boundaries claros.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-settings-e-ambientes"></a>

## Settings e Ambientes

> Configuração controla banco, middleware, segurança e apps instalados. Segredos, hosts permitidos e debug precisam ser ajustados por ambiente.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-orm-django"></a>

## ORM Django

> Models representam tabelas e relacionamentos; QuerySets descrevem consultas com avaliação preguiçosa. Observo SQL gerado e não presumo que ORM elimina custo de join ou roundtrip.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-querysets-e-lazy-evaluation"></a>

## QuerySets e Lazy Evaluation

> Construir QuerySet não executa necessariamente SQL. Iteração, conversão em lista e outras operações podem avaliar a query; repetição ou caching precisa ser compreendida.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-select-related-vs-prefetch-related"></a>

## `select_related` vs `prefetch_related`

> `select_related` normalmente usa joins para relações únicas; `prefetch_related` usa consultas separadas e combina resultados. Escolho pelo tipo de relação e custo para evitar N+1.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-migrations-django"></a>

## Migrations Django

> Mudanças de model geram migrações versionadas; reviso operações antes de aplicar. Data migrations e deploy sem downtime exigem plano próprio.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-middleware-e-request-lifecycle"></a>

## Middleware e Request Lifecycle

> Middlewares processam requisição/resposta para interesses transversais. Ordem afeta autenticação, segurança e exceções.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-autenticacao-e-permissoes"></a>

## Autenticação e Permissões

> Django disponibiliza usuários, sessões e permissões; regras por objeto e por tenant ainda precisam ser implementadas e testadas.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-django-rest-framework"></a>

## Django REST Framework

> DRF acrescenta serializers, views, authentication, permissions, routers e paginação para APIs. Não confundo serializer com entidade de domínio.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-serializers-e-validacao"></a>

## Serializers e Validação

> Serializers traduzem e validam entrada/saída; validação de integridade concorrente permanece no domínio e no banco.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-viewsets-vs-apiview"></a>

## ViewSets vs APIView

> ViewSets agrupam ações convencionais; APIView oferece controle explícito. Escolho conforme clareza do caso, evitando endpoints excessivamente genéricos.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-transacoes-atomicas"></a>

## Transações Atômicas

> `transaction.atomic()` define boundary; nested blocks podem usar savepoints conforme cenário. Evito executar chamadas remotas lentas dentro de transações abertas.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-async-no-django"></a>

## Async no Django

> Django possui capacidades assíncronas, mas algumas APIs ou middlewares podem permanecer síncronos. Confirmo suporte de todo o caminho antes de assumir I/O não bloqueante.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-django-vs-fastapi"></a>

## Django vs FastAPI

> Django oferece convenções e funcionalidades integradas; FastAPI enfatiza APIs tipadas e estilo ASGI. Escolho com base no escopo, curva da equipe, integrações e operação.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-27-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Django favorece desenvolvimento integrado com ORM e autenticação; DRF constrói APIs. Conheço lazy QuerySets, prevenção de N+1, migrations, transações e limites de componentes assíncronos.

[↑ Sumário do tópico](#python-27-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-sumario"></a>

# 28. Java vs Python: Mapeamento para Backend

## Sumário do tópico

[↑ Sumário geral](#sumario-geral)


- [Visão Geral](#python-28-visao-geral)
- [Java `class` vs Python `class`](#python-28-java-class-vs-python-class)
- [Java Interface vs Python `Protocol`/ABC](#python-28-java-interface-vs-python-protocol-abc)
- [Java Records vs Python Dataclasses](#python-28-java-records-vs-python-dataclasses)
- [Java `static` vs `@staticmethod`/`@classmethod`](#python-28-java-static-vs-staticmethod-classmethod)
- [Java `==`/`equals` vs Python `is`/`==`](#python-28-java-equals-vs-python-is)
- [Java `null` vs Python `None`](#python-28-java-null-vs-python-none)
- [Java BigDecimal vs Python Decimal](#python-28-java-bigdecimal-vs-python-decimal)
- [Java Collections vs Coleções Python](#python-28-java-collections-vs-colecoes-python)
- [Java Stream vs Python Generators](#python-28-java-stream-vs-python-generators)
- [Java Exceptions vs Python Exceptions](#python-28-java-exceptions-vs-python-exceptions)
- [Java Thread/JVM GC vs CPython GIL/Refcount](#python-28-java-thread-jvm-gc-vs-cpython-gil-refcount)
- [Spring DI vs FastAPI Depends](#python-28-spring-di-vs-fastapi-depends)
- [Spring MVC vs FastAPI](#python-28-spring-mvc-vs-fastapi)
- [Spring Data JPA vs SQLAlchemy](#python-28-spring-data-jpa-vs-sqlalchemy)
- [JUnit/Mockito vs Pytest/Mock](#python-28-junit-mockito-vs-pytest-mock)
- [Maven/Gradle vs Pyproject/uv](#python-28-maven-gradle-vs-pyproject-uv)
- [Roadmap de Projeto Prático](#python-28-roadmap-de-projeto-pratico)
- [Critério de Domínio Técnico](#python-28-criterio-de-dominio-tecnico)
- [Resumo de Entrevista](#python-28-resumo-de-entrevista)

---

<a id="python-28-visao-geral"></a>

## Visão Geral

> Para um engenheiro Java migrando ou ampliando backend em Python, reaproveito princípios de domínio, SQL e sistemas distribuídos, mas reviso diferenças de tipagem, runtime, concorrência e gerenciamento de dependências.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-java-class-vs-python-class"></a>

## Java `class` vs Python `class`

> Ambas expressam objetos e comportamento; Python usa tipagem dinâmica e convenções de encapsulamento. A arquitetura do domínio não precisa ser idêntica em quantidade de classes.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-java-interface-vs-python-protocol-abc"></a>

## Java Interface vs Python `Protocol`/ABC

> Interfaces Java definem contrato nominal; Protocol permite contrato estrutural verificado estaticamente, enquanto ABC fornece contrato nominal em runtime quando aplicado.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-java-records-vs-python-dataclasses"></a>

## Java Records vs Python Dataclasses

> Records geram classes transparentes de dados com componentes definidos; dataclasses geram métodos por opção e podem permanecer mutáveis. Nenhum é substituto automático de Value Object validado.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-java-static-vs-staticmethod-classmethod"></a>

## Java `static` vs `@staticmethod`/`@classmethod`

> `staticmethod` não recebe instância ou classe; `classmethod` recebe `cls` e suporta construtores alternativos. Atributos de classe podem compartilhar estado inadvertidamente.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-java-equals-vs-python-is"></a>

## Java `==`/`equals` vs Python `is`/`==`

> Em Java, `==` de referência testa identidade e `equals` igualdade lógica; Python usa `is` para identidade e `==` chama protocolo de igualdade.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-java-null-vs-python-none"></a>

## Java `null` vs Python `None`

> Ambos expressam ausência, mas `None` participa das regras de truthiness. Verifico explicitamente ausência quando zero ou string vazia são valores de domínio.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-java-bigdecimal-vs-python-decimal"></a>

## Java BigDecimal vs Python Decimal

> Ambos suportam aritmética decimal; Python `decimal.Decimal` também exige cuidado com entrada de float, contexto, escala e arredondamento.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-java-collections-vs-colecoes-python"></a>

## Java Collections vs Coleções Python

> List/Map/Set têm equivalentes conceituais, mas estruturas nativas Python possuem APIs e custos próprios; `dict` preserva ordem de inserção e `list` usa array dinâmico.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-java-stream-vs-python-generators"></a>

## Java Stream vs Python Generators

> Streams Java representam pipelines, enquanto generators Python produzem itens por demanda. Comprehensions materializam conforme tipo; não assumo que todo pipeline é lazy.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-java-exceptions-vs-python-exceptions"></a>

## Java Exceptions vs Python Exceptions

> Python não possui checked exceptions exigidas pelo compilador. Defino contratos por tipagem, documentação e testes e preservo causas com `raise ... from`.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-java-thread-jvm-gc-vs-cpython-gil-refcount"></a>

## Java Thread/JVM GC vs CPython GIL/Refcount

> GC e threads dependem do runtime; no CPython padrão há GIL e reference counting, enquanto Java usa JVM e coletores específicos. Não transfiro conclusões de performance diretamente.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-spring-di-vs-fastapi-depends"></a>

## Spring DI vs FastAPI Depends

> Ambos organizam dependências, mas Spring usa container de beans e proxies; FastAPI resolve dependências declaradas no fluxo de requests. Decorators de rota não equivalem a proxies transacionais.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-spring-mvc-vs-fastapi"></a>

## Spring MVC vs FastAPI

> Ambos criam APIs HTTP, mas FastAPI usa ASGI, funções e esquemas tipados. A escolha entre async e sync depende das bibliotecas integradas.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-spring-data-jpa-vs-sqlalchemy"></a>

## Spring Data JPA vs SQLAlchemy

> Ambos mapeiam persistência, mas Session, flush e lazy loading possuem APIs e comportamentos específicos. Conhecer `@Transactional` não substitui entender boundaries SQLAlchemy.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-junit-mockito-vs-pytest-mock"></a>

## JUnit/Mockito vs Pytest/Mock

> Testes mantêm princípio de isolamento e comportamento, mas pytest usa fixtures e asserts nativos; patch depende do namespace de lookup em Python.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-maven-gradle-vs-pyproject-uv"></a>

## Maven/Gradle vs Pyproject/uv

> Ecossistema Python usa ambientes, metadados de build, resolvers e wheels. Lockfile e artefato testado precisam ser promovidos consistentemente.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-roadmap-de-projeto-pratico"></a>

## Roadmap de Projeto Prático

> Implemente uma API de agendamento: domínio, FastAPI, Pydantic, SQLAlchemy/PostgreSQL, Alembic, pytest, autenticação, jobs, Docker, observabilidade e CI/CD.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-criterio-de-dominio-tecnico"></a>

## Critério de Domínio Técnico

> Explique cada escolha, escreva exemplos sem copiar, provoque falhas e monitore sintomas. Entendimento sênior aparece em trade-offs e diagnósticos, não na memorização de decorators.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="python-28-resumo-de-entrevista"></a>

## Resumo de Entrevista

> Python não é Java com sintaxe menor: muda o modelo de runtime, tipagem e dependências. Mantenho arquitetura, testes, idempotência e observabilidade, adaptando as ferramentas às garantias reais do stack.

[↑ Sumário do tópico](#python-28-sumario) · [↑ Sumário geral](#sumario-geral)

---

<a id="referencias"></a>

# Referências oficiais para aprofundamento

O texto é uma síntese para revisão; consulte documentação oficial quando a informação depender de implementação ou versão.

- [Python — documentação oficial](https://docs.python.org/3/)
- [CPython e free-threading](https://docs.python.org/3/howto/free-threading-python.html)
- [asyncio — corrotinas e TaskGroup](https://docs.python.org/3/library/asyncio-task.html)
- [Python Packaging User Guide](https://packaging.python.org/)
- [FastAPI](https://fastapi.tiangolo.com/)
- [Pydantic](https://docs.pydantic.dev/latest/)
- [SQLAlchemy ORM](https://docs.sqlalchemy.org/en/20/orm/)
- [Alembic](https://alembic.sqlalchemy.org/)
- [Pytest](https://docs.pytest.org/)
- [Django](https://docs.djangoproject.com/)
- [Django REST Framework](https://www.django-rest-framework.org/)
- [Apache Kafka](https://kafka.apache.org/documentation/)
- [AWS Documentation](https://docs.aws.amazon.com/)

[↑ Sumário geral](#sumario-geral)
