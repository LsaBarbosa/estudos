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
## Namespaces
  - Isolam a visão que o processo tem do sistema
  - Tipos de namespace
    - PID namespace: isola processos
    - NET namespace: isola rede
    - MNT namespace: isola pontos de montagem
    - UTS namespace: hostname próprio
    - IPC namespace: isola comunicação entre processos
    - USER namespace: isola usuários e permissões
 ## Cgroups
  - Controlam recursos
    - Limitam: CPU | memória | I/O | númeor de processos  
  - Sem os cgroups, um container poderia consumir recursos demais do host.
 ## Camadas | Union FileSystem
  - As imagem em container são compostas em camadas
  - Isso melhora o reuso e adistribuição
    
# Ciclo de vida da imagem e do container
 ## Fluxo
  - escreve _dockerfile_ -> gera imagem com _docker buuild_ -> armazena a imagem no _registry_
    -> container sobe como um processo isolado -> parar| remover container (pode perder dados se não houver volume

# Docker
 ## Dockerfile
- FROM eclipse-temurin:21-jre  -> define a imagem base
- WORKDIR /app                 -> define diretorio de trabalho
- COPY target/app.jar app.jar  -> copia arquivos para dentro da imagem
- EXPOSE 8080                  -> documenta a porta usada pela aplicação
- ENTRYPOINT ["java", "-jar", "app.jar"] -> define o processo principal do container
 ## CMD VS ENTRYPOINT
  - entrypoint define o executavel
  - cmd define argumentos para esse executável
     - ENTRYPOINT ["java", "-jar", "app.jar"]
     - CMD ["--spring.profiles.active=prod"]

# Persistencia 
 - Container devem ser stateless sempre que possível.
 - Quando há necessidade de perssitencia usa-se volumes ou serviço externo.
