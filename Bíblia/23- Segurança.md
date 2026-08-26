# FASE 17 — Segurança

Lucas, para um Senior/Tech Lead, segurança não significa apenas configurar Spring Security. É conseguir raciocinar sobre **identidade, autorização, proteção de dados, comunicação segura, gestão de credenciais e superfície de ataque**.

A versão atual do **OWASP Top 10 é a de 2025**, que mantém Broken Access Control como A01 e inclui categorias como Security Misconfiguration, Software Supply Chain Failures, Cryptographic Failures e Injection. 

## 1. Conceitos, trade-offs e casos de uso

| Item | Conceito objetivo | Trade-off / impacto | Caso de uso |
|---|---|---|---|
| **OWASP Top 10** | Lista de conscientização com os principais riscos de segurança em aplicações web. | Não é checklist completo de segurança; cobre categorias de risco, não todas as vulnerabilidades possíveis. | Base para threat modeling, code review e práticas de desenvolvimento seguro. |
| **Broken Access Control** | Usuário consegue executar operação ou acessar recurso para o qual não possui autorização. | Controles muito granulares aumentam complexidade, mas ausência deles pode expor dados críticos. | Usuário A acessando `/orders/123` pertencente ao usuário B. |
| **Security Misconfiguration** | Configuração insegura de aplicação, cloud, framework, servidor ou infraestrutura. | Hardening aumenta esforço operacional. | Actuator exposto, debug habilitado em produção, permissões excessivas. |
| **Software Supply Chain Failures** | Riscos vindos de dependências, builds, plugins, registries ou pipeline comprometidos. | Atualizações e controles de supply chain adicionam processos e ferramentas. | Dependência Java vulnerável ou imagem Docker comprometida. |
| **Cryptographic Failures** | Uso incorreto ou ausência de criptografia para dados sensíveis. | Criptografia exige gestão de chaves, rotação e performance adicional. | Senhas, dados financeiros e dados pessoais. |
| **Injection** | Dados não confiáveis são interpretados como comandos ou código. | Validação e APIs parametrizadas exigem disciplina, mas evitam execução arbitrária. | SQL Injection e Command Injection. |
| **Insecure Design** | Problema de segurança originado na própria arquitetura ou regra de negócio, não apenas no código. | Mitigar depois pode exigir redesign. | Fluxo financeiro sem limites ou proteção contra abuso. |
| **Authentication Failures** | Falhas na identificação/autenticação de usuários ou sistemas. | Controles fortes podem adicionar fricção. | MFA ausente, sessão insegura, credential stuffing. |
| **Software/Data Integrity Failures** | Código ou dados são aceitos sem verificar sua integridade/origem. | Verificação e assinatura aumentam complexidade operacional. | Artefatos, updates, plugins ou eventos não confiáveis. |
| **Security Logging and Alerting Failures** | Sistema não registra ou alerta adequadamente eventos de segurança. | Mais logs aumentam volume e custo. | Não detectar ataques repetidos ou escalada de privilégios. |
| **Exceptional Condition Handling** | Erros, falhas ou situações inesperadas são tratados de forma insegura. | Tratamento defensivo aumenta código e testes. | Fail-open após erro de autorização. |
| **OAuth2** | Framework de autorização que permite conceder acesso limitado a recursos por meio de access tokens. | Possui vários atores, tokens e fluxos; configuração incorreta cria vulnerabilidades. | SPA/BFF/API, integrações e service-to-service. |
| **OIDC** | Camada de identidade sobre OAuth2 que adiciona autenticação padronizada e ID Token. | Mais componentes e validações. | Login federado com Keycloak, Okta, Entra ID ou Google. |
| **JWT** | Formato compacto de token baseado em claims, frequentemente assinado. | Revogação é mais difícil e payload não deve ser tratado como secreto apenas por estar em JWT. | Access Token stateless entre cliente e Resource Server. |
| **Access Token** | Credencial utilizada para acessar um recurso protegido. | Vazamento permite uso indevido durante sua validade. | `Authorization: Bearer ...`. |
| **ID Token** | Token OIDC que representa a autenticação do usuário para o Client. | Não deve ser confundido com Access Token. | Login via OIDC. |
| **RBAC** | Role-Based Access Control. Permissões são associadas a papéis atribuídos a usuários. | Muitos papéis podem causar role explosion. | `ADMIN`, `MANAGER`, `USER`. |
| **Secrets** | Credenciais e materiais sensíveis como senha, API key, token e chave privada. | Exigem armazenamento, controle de acesso, auditoria e rotação. | Senha de banco, private key, client secret. |
| **Encryption** | Transforma dados usando chave criptográfica para protegê-los contra leitura não autorizada. | Gestão das chaves costuma ser mais difícil que o algoritmo em si. | Dados em repouso e informações sensíveis. |
| **TLS** | Protege comunicação em trânsito fornecendo criptografia e autenticação do servidor. | Certificados, renovação e configuração precisam ser gerenciados. | HTTPS entre cliente e API. |
| **mTLS** | TLS em que cliente e servidor apresentam certificados e autenticam mutuamente suas identidades. | Gestão de certificados é significativamente mais complexa. | Comunicação service-to-service e ambientes zero-trust. |
| **SQL Injection** | Entrada do usuário altera semanticamente uma instrução SQL. | Mitigação exige queries parametrizadas e controle de queries dinâmicas. | Login, filtros, relatórios e consultas dinâmicas. |
| **XSS** | Conteúdo controlado por atacante é executado como JavaScript no navegador da vítima. | Encoding e políticas restritivas podem limitar conteúdo dinâmico legítimo. | Comentários ou campos exibidos sem escaping. |
| **CSRF** | Navegador autenticado é induzido a realizar uma operação não desejada. | Proteção adiciona tokens/políticas adicionais. | Aplicações autenticadas por cookie/sessão. |
| **SSRF** | Atacante faz o servidor acessar destinos que ele não deveria alcançar. | Restrições de saída podem limitar integrações legítimas. | Endpoint que aceita URL e acessa metadata service ou rede interna. |
| **Command Injection** | Entrada do usuário é interpretada como comando pelo sistema operacional. | APIs seguras podem ser menos flexíveis que shell commands. | `Runtime.exec`, `ProcessBuilder`, scripts. |
| **Least Privilege** | Usuário ou serviço recebe somente as permissões necessárias para sua função. | Controle granular aumenta administração. | Aplicação somente com `SELECT/INSERT` necessários no banco. |
| **Defense in Depth** | Utiliza várias camadas independentes de proteção. | Mais componentes e operação. | TLS + autenticação + autorização + network policy + auditoria. |

O OWASP Top 10 de 2025 possui oficialmente estas dez categorias: Broken Access Control, Security Misconfiguration, Software Supply Chain Failures, Cryptographic Failures, Injection, Insecure Design, Authentication Failures, Software or Data Integrity Failures, Security Logging and Alerting Failures e Mishandling of Exceptional Conditions. 

---

# 2. Authentication x Authorization

Essa diferença precisa estar automática.

```text
Authentication
      ↓
Quem é você?


Authorization
      ↓
O que você pode fazer?
```

Por exemplo:

```text
JWT válido
    ↓
Authentication ✓

ROLE_USER
    ↓
DELETE /admin/users/10
    ↓
Authorization ✗
```

O usuário está autenticado, mas não autorizado.

Grande parte de **Broken Access Control** aparece quando o sistema autentica corretamente, mas falha ao verificar autorização sobre a operação ou sobre o recurso específico.

---

# 3. OAuth2

OAuth2 é principalmente um framework de **autorização**.

Os quatro papéis fundamentais são:

```text
Resource Owner
      ↓
normalmente o usuário


Client
      ↓
aplicação que deseja acesso


Authorization Server
      ↓
emite tokens


Resource Server
      ↓
API protegida
```

O cliente obtém um Access Token e o utiliza para acessar o Resource Server. 

Para desenvolvimento moderno, é importante conhecer também as práticas atuais do OAuth2: o RFC 9700, publicado em 2025, recomenda **Authorization Code com PKCE** e determina que o Resource Owner Password Credentials Grant não deve mais ser utilizado. 

---

# 4. OIDC

OIDC significa OpenID Connect.

Ele adiciona uma camada de identidade sobre OAuth2.

Mentalmente:

```text
OAuth2
   ↓
Authorization


OIDC
   ↓
OAuth2
+
Authentication
+
Identity
```

Uma das principais adições é:

```text
ID Token
```

Então, em login federado:

```text
Usuário
   ↓
Authorization Server / IdP
   ↓
OIDC
   ↓
ID Token
   ↓
Client conhece a identidade
```

---

# 5. JWT

JWT é um **formato de token**, não um protocolo de autenticação.

Estrutura simplificada:

```text
HEADER
.
PAYLOAD
.
SIGNATURE
```

O payload contém claims como:

```text
sub
iss
aud
exp
iat
scope
roles
```

Um erro clássico de entrevista é dizer:

> "JWT criptografa as informações."

Não necessariamente.

Um JWT assinado normalmente garante:

```text
integridade
+
autenticidade
```

mas seu payload pode ser facilmente decodificado.

Portanto:

```text
JWT assinado
≠
dados secretos
```

---

# 6. JWT — o que validar

Receber um JWT não é suficiente.

Um Resource Server deve verificar elementos como:

```text
assinatura

issuer

audience

expiration

not-before

scopes / permissions
```

Conceitualmente:

```text
Bearer JWT
    ↓
assinatura válida?
    ↓
issuer correto?
    ↓
audience correta?
    ↓
não expirou?
    ↓
possui permissão?
    ↓
Controller
```

Segurança não deve ser:

```text
decodifiquei JWT
     ↓
confio
```

---

# 7. Access Token x ID Token

Para entrevista:

```text
Access Token
     ↓
acessa API


ID Token
     ↓
informa identidade
ao Client
```

O Access Token é enviado ao Resource Server.

O ID Token pertence ao fluxo de autenticação OIDC e não deve ser usado indiscriminadamente como Access Token.

---

# 8. RBAC

RBAC significa:

**Role-Based Access Control.**

Exemplo:

```text
USER
   ↓
orders.read


MANAGER
   ↓
orders.read
orders.update


ADMIN
   ↓
administração
```

Mas uma aplicação segura não deve verificar apenas:

```text
possui ROLE_USER?
```

Ela também pode precisar verificar:

```text
esse pedido pertence ao usuário?
```

Isso evita um problema clássico de Broken Access Control.

---

# 9. Broken Access Control / IDOR

Imagine:

```text
GET /users/100/orders/500
```

O usuário muda manualmente:

```text
500
↓
501
```

e consegue visualizar um pedido de outro usuário.

Mesmo que:

```text
Authentication ✓
```

temos:

```text
Authorization ✗
```

A API não deveria apenas verificar:

```java
hasRole("USER")
```

Deveria verificar também:

```text
O usuário autenticado
pode acessar ESTE recurso?
```

Esse tipo de falha explica por que Broken Access Control continua em **A01 no OWASP Top 10:2025**. 

---

# 10. Secrets

Secrets incluem:

```text
passwords
API keys
tokens
private keys
certificates
client secrets
```

Evite:

```java
String password = "prod123";
```

ou:

```text
application.properties
↓
senha commitada no Git
```

O modelo desejável é:

```text
Application
    ↓
Secret Manager / Vault
    ↓
Secret
```

Em cloud/Kubernetes podemos utilizar soluções como secret managers e mecanismos de identidade do workload para reduzir credenciais estáticas.

A regra mental:

> **segredo não pertence ao código-fonte.**

---

# 11. Encryption

Criptografia deve ser pensada em duas dimensões.

### Data in transit

```text
Client
  ↓
TLS
  ↓
Server
```

### Data at rest

```text
Database
Disk
Backup
Object Storage
```

Mas criptografia não resolve tudo.

Se:

```text
aplicação comprometida
```

e ela possui acesso legítimo à chave e ao dado:

```text
encryption
```

sozinha não salva o sistema.

Por isso entram:

```text
least privilege
IAM
RBAC
auditoria
segmentação
```

---

# 12. Hashing x Encryption

Também não confunda:

```text
Encryption
```

com:

```text
Hashing
```

Encryption é reversível com uma chave.

Hash criptográfico é projetado para ser unidirecional.

Para senhas, normalmente queremos:

```text
password
    ↓
password hashing algorithm
    ↓
hash
```

e não simplesmente:

```text
AES(password)
```

Senhas não deveriam precisar ser recuperadas em texto puro.

---

# 13. TLS

TLS protege dados **em trânsito**.

Ele oferece principalmente:

```text
confidencialidade
integridade
autenticação do servidor
```

Fluxo:

```text
Client
   ↓
HTTPS / TLS
   ↓
API
```

Isso evita que alguém na rede simplesmente capture:

```text
senha
token
dados pessoais
```

em texto puro.

Mas TLS não decide:

```text
esse usuário pode deletar pedido?
```

Isso continua sendo responsabilidade da autorização.

---

# 14. mTLS

TLS tradicional:

```text
Client
   ↓
verifica certificado
   ↓
Server
```

mTLS:

```text
Client certificate
       ↓
     Server

       ↑
Server certificate
       ↑
     Client
```

Os dois lados se autenticam.

É muito interessante para:

```text
service-to-service
B2B
zero-trust
infraestrutura crítica
```

Mas o custo operacional é maior:

```text
emissão
rotação
expiração
revogação
distribuição de certificados
```

Um detalhe importante:

> **mTLS autentica a identidade do workload, mas não substitui autorização.**

O serviço ainda pode precisar verificar o que aquela identidade pode fazer.

---

# 15. SQL Injection

Exemplo perigoso:

```java
String sql =
    "SELECT * FROM users WHERE email = '"
    + email
    + "'";
```

Entrada:

```text
' OR '1'='1
```

pode alterar a semântica da consulta.

A principal defesa é:

```text
prepared statements
+
parameter binding
```

Por exemplo:

```java
SELECT *
FROM users
WHERE email = ?
```

Com JPA/Hibernate:

```java
where u.email = :email
```

Não monte SQL/HQL com entrada não confiável por concatenação.

---

# 16. XSS

XSS significa Cross-Site Scripting.

Imagine um campo:

```text
comentário
```

recebendo conteúdo malicioso.

Se o frontend inserir aquilo diretamente como HTML, o navegador pode executar código do atacante.

Consequências:

```text
roubo de sessão
ações em nome do usuário
alteração da página
exfiltração de dados
```

Proteções importantes incluem:

```text
output encoding
sanitização quando HTML é permitido
CSP
frameworks que escapam conteúdo corretamente
```

A regra é:

> **dados não confiáveis não devem virar código executável no browser.**

---

# 17. CSRF

CSRF explora o fato de o browser poder enviar credenciais automaticamente.

Exemplo:

```text
User autenticado em bank.com
        ↓
cookie de sessão
```

Depois visita:

```text
evil.com
```

que força:

```text
POST bank.com/transfer
```

O browser pode enviar o cookie automaticamente.

Então a requisição parece autenticada.

Proteções incluem:

```text
CSRF Token
SameSite cookies
Origin checking
```

Um ponto importante:

```text
CSRF
```

não é simplesmente sinônimo de:

```text
JWT
```

O risco depende principalmente de **como a credencial é transportada**.

---

# 18. SSRF

SSRF significa Server-Side Request Forgery.

Imagine:

```text
POST /preview

{
  "url":
  "https://algum-site.com"
}
```

A aplicação faz:

```text
server
  ↓
HTTP GET URL recebida
```

O atacante fornece algo como:

```text
http://servico-interno
```

ou tenta acessar serviços de metadata/cloud.

Agora o servidor está fazendo requisições em nome do atacante.

Proteções incluem:

```text
allowlist de destinos
bloqueio de redes privadas
validação de protocolo
controle de redirects
egress filtering
```

---

# 19. Command Injection

Exemplo extremamente perigoso:

```java
Runtime.getRuntime()
       .exec("ping " + host);
```

Entrada:

```text
host controlado pelo usuário
```

pode virar um comando diferente dependendo do ambiente e da forma de execução.

A regra principal é:

> **não concatene entrada não confiável em comandos de sistema.**

Prefira APIs específicas.

Quando execução de processo for realmente necessária:

```text
argumentos estruturados
allowlist
least privilege
isolamento
```

---

# 20. SQL Injection x Command Injection

Os dois pertencem à família de Injection.

Diferença:

```text
SQL Injection
     ↓
entrada vira instrução
para banco


Command Injection
     ↓
entrada vira instrução
para sistema operacional
```

A raiz é parecida:

> **misturar dados não confiáveis com uma linguagem executável.**

Por isso a solução estrutural é separar:

```text
comando
```

de:

```text
dados
```

---

# 21. CORS não é segurança de backend

Um ponto importante vindo também do Spring Security:

```text
CORS
```

é principalmente uma política do navegador.

Não use CORS como mecanismo de autorização.

Mesmo que sua API aceite apenas:

```text
https://frontend.company.com
```

um atacante ainda pode fazer uma requisição diretamente por outro backend ou cliente HTTP.

Segurança real exige:

```text
Authentication
+
Authorization
```

---

# 22. Least Privilege

Princípio fundamental:

> **Dê apenas o acesso necessário.**

Por exemplo, se um serviço precisa somente de:

```text
SELECT
INSERT
UPDATE
```

ele provavelmente não deveria possuir:

```text
DROP DATABASE
CREATE USER
SUPERUSER
```

O mesmo vale para:

```text
AWS IAM
Kubernetes RBAC
database users
OAuth scopes
filesystem
```

Se uma credencial for comprometida, least privilege limita o raio do dano.

---

# 23. Defense in Depth

Não dependa de uma única defesa.

Exemplo:

```text
Internet
   ↓
TLS
   ↓
API Gateway / WAF
   ↓
Authentication
   ↓
Authorization
   ↓
Application validation
   ↓
Database least privilege
   ↓
Encryption at rest
   ↓
Audit logs
```

Se uma camada falhar, outra ainda pode reduzir o impacto.

---

# 24. Mapa mental para entrevistas

Memorize em quatro blocos:

```text
IDENTIDADE

OAuth2
OIDC
JWT
RBAC
```

```text
DADOS

Secrets
Encryption
Key management
```

```text
COMUNICAÇÃO

TLS
mTLS
```

```text
ATAQUES

Broken Access Control
Injection
XSS
CSRF
SSRF
Command Injection
```

E por cima de tudo:

```text
OWASP Top 10
      +
Least Privilege
      +
Defense in Depth
```

---

# Resposta objetiva para entrevista

> Eu vejo segurança como uma responsabilidade transversal, não apenas como configuração de autenticação. Começo separando authentication de authorization: primeiro identifico corretamente usuário ou serviço, depois verifico explicitamente quais recursos e operações aquela identidade pode acessar.
>
> Para identidade federada, utilizo OAuth2 para autorização e OIDC quando preciso de autenticação e identidade. Em APIs, JWT pode ser utilizado como formato de Access Token, mas valido assinatura, issuer, audience, expiração e scopes, e não trato o payload de um JWT assinado como informação secreta. As recomendações atuais do OAuth2 priorizam Authorization Code com PKCE e desaconselham fluxos legados como Resource Owner Password Credentials. 
>
> Para autorização, posso utilizar RBAC, mas também verifico ownership do recurso para evitar Broken Access Control, que continua sendo o risco A01 do OWASP Top 10 de 2025. 
>
> Na proteção de dados, mantenho secrets fora do código-fonte, aplico criptografia conforme a classificação dos dados e utilizo TLS para comunicação. Em comunicação service-to-service mais sensível, mTLS pode adicionar autenticação mútua, lembrando que autenticação do workload não substitui autorização.
>
> Em desenvolvimento seguro, considero ameaças como SQL Injection, XSS, CSRF, SSRF e Command Injection. Evito injection utilizando queries parametrizadas e APIs estruturadas, trato output corretamente no frontend, avalio CSRF conforme o mecanismo de credencial e restrinjo fortemente chamadas feitas pelo servidor para evitar SSRF.
>
> Então, para mim, uma arquitetura segura combina **least privilege, defense in depth, autenticação forte, autorização por recurso, gestão segura de secrets, criptografia, validação de entrada, observabilidade de segurança e revisão contínua das dependências e configurações**.
