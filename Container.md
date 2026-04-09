# Container Introdução
- Uma unidade leve que empacota aplicação
- Garante portabilidade e isolamento
- Compartilha kernel do Host

## Container Vs VM
- Principal diferença é Vm virtualiza um sistema inteiro
- O Container virtualiza o espaço d eexecução da aplicação
  - Sendo mais leve e subindo mais rápido.

## Imagem
- contém sistema base | Dependencia | aplicação | instrução de inicialização
  - Exemplo:
    - Imagem java pode conter: Linux base, JDK e o JAR da aplicação
   
- Container é a instância de execução da imagem.

# Como funcionam
