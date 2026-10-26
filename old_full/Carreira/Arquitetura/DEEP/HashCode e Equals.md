Lucas, uma boa síntese para entrevista seria:

> `HashMap` funciona combinando `hashCode()`
>> O `hashCode()` ajuda a determinar **onde procurar** </br>
>> O `equals()` determina **se aquela chave é realmente a mesma chave lógica**. Por isso, dois objetos diferentes podem ter o mesmo hash e ainda coexistirem corretamente, desde que `equals()` consiga diferenciá-los.
>
>> Quando existem muitas colisões, a performance pode degradar e, em implementações modernas do Java, buckets muito congestionados podem ser transformados em árvores para reduzir o custo de busca.
>
> O contrato entre `equals()` e `hashCode()` 
>> Se dois objetos são iguais por `equals()`, obrigatoriamente precisam ter o mesmo `hashCode()`. </br>
>> **O inverso não é necessário**. Uma implementação como `hashCode()` sempre retornando `1` é válida do ponto de vista do contrato, mas destrói a distribuição do mapa e prejudica severamente a performance.
>
> Mutabilidade. 
>> Se uma chave já inserida em um `HashMap` ou `HashSet` tiver alterado um atributo usado em `equals()` ou `hashCode()`, o hash pode mudar. </br> O objeto continua fisicamente armazenado no bucket calculado anteriormente, mas operações como `get`, `contains` e `remove` podem deixar de encontrá-lo.
>
> JPA
>> Uma entidade pode começar como `transient`, depois se tornar `managed` e posteriormente `detached`. </br> Se `equals()` e `hashCode()` dependem exclusivamente de um `@Id @GeneratedValue`, o objeto começa com `id == null` e recebe o ID depois do `persist`. Se o ID participa do `hashCode()`, o hash muda durante o ciclo de vida.</br> Em um `HashSet`, isso pode deixar a entidade presa no bucket antigo.
>>
>> Para entidades JPA, quando existir uma chave natural realmente única, imutável e disponível desde a criação do objeto, ela pode ser uma boa opção.</br> Outra alternativa é utilizar um UUID criado pela própria aplicação antes da persistência, pois a identidade já nasce definida e não muda depois do `persist`.
>
> Igualdade lógica e Identidade persistente. 
>> `==` compara referências,</br> `equals()` representa igualdade lógica </br> `ID da entidade` representa identidade persistente.</br> Duas instâncias Java diferentes podem representar a mesma linha do banco.
>
> Hibernate e Proxies. 
>>Um `equals()` baseado `getClass()` pode falhar pois um proxy pode ter uma class runtime diferente da entidade original. </br> Mas, usar `instanceof` indiscriminadamente pode gerar problemas em hierarquias de herança.</br> A estratégia precisa considerar a semântica do domínio e o comportamento dos proxies.
>
> Bastante cautela com Lombok `@Data` em entidades JPA. 
>> Ele pode gerar automaticamente `equals()`, `hashCode()` e `toString()` usando IDs, coleções e relacionamentos. </br> Isso pode causar alteração de hash, lazy loading inesperado, consultas adicionais entre outros problemas. </br> Em entidades JPA, normalmente prefiro controlar explicitamente quais atributos participam de `equals()` e `hashCode()`.

### Resumo mental

```text
HashMap
   │
   ├── hashCode() → onde procurar
   │
   └── equals()   → qual é a chave
```

```text
equals() == true
        ↓
hashCode() obrigatoriamente igual

hashCode() igual
        ↓
equals() pode ser true ou false
```

```text
Objeto entra no HashSet
        ↓
hash = X
        ↓
atributo usado no hash muda
        ↓
hash = Y
        ↓
objeto continua no bucket de X
        ↓
contains/remove podem falhar
```

E, para JPA, a principal regra é:

> **A identidade usada por `equals()` e `hashCode()` deve ser previsível e, principalmente, estável durante todo o período em que a entidade participa de estruturas baseadas em hash.**
