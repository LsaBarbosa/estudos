# Index
> Índices são estruturas auxiliares de acesso aos dados que permitem otimizar consultas, reduzindo a necessidade de percorrer integralmente uma tabela.
> 
> Estruturas como B-Tree permitem localizar registros de maneira eficiente por meio de chaves de busca.
> 
> Entretanto, há o trade-off de armazenamento e manutenção, principalmente em operações de escrita.
> 
> A existência de um índice não garante sua utilização, o Query Optimizer avalia as estratégias de acesso disponíveis e seus respectivos custos.
> 
> Por isso, a criação de índices deve considerar os padrões de consulta da aplicação, a distribuição dos dados, a seletividade dos filtros e os planos de execução, evitando índices desnecessários.

## Tipos de Indices
| Tipo          | O que você precisa saber                  |
| ------------- | ----------------------------------------- |
| B-Tree        | Versátil, suporta igualdade e intervalos. |
| Hash          | Especializado em igualdade.               |
| Bitmap        | Interessante em ambientes analíticos.     |
| Clustered     | Organiza o armazenamento dos registros.   |
| Non-Clustered | Estrutura auxiliar de acesso.             |
| Unique        | Garante unicidade.                        |
| Composite     | Indexa múltiplas colunas.                 |
| Partial       | Indexa um subconjunto dos registros.      |
| Functional    | Indexa expressões.                        |
| Covering      | Pode evitar acesso adicional à tabela.    |
