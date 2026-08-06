# Master
  - Gerencia o cluster como um todo
  - Mantem o estado desejado ( qnt de pods em execução ex)
  - Agendar containers
# Nodes
  - São maquinas onde as aplicações sao executadas
  - Executam os pods e reporta status para control plane
  - Cada node possui
    - Kublete - agente que conversa com control plane
    - Container runtime - docker por exemplo
    - kube-proxy - gerencia a rede e a comunicaação entre pods
# Control Plane
  - Conjunto de componentes que gerencia o cluster
    - Kube-apiserver
      - porta de entrada do kluster ( tudo passa por ele)
     
    - etcd
      - BD do kubernetes ( chave valor)
     
    - Kube-Schedule
      - Decide em qual node um pod será executado
     
    - Kube-controller-manager
      - Garnate que o estado desejado seja mantido
# Api (kubernet api)
  - tudo fcniona via Api
    - Criar pods
    - atualizar deployments
    - escalar app
   
  - Fluxo:
    - Você envia uma requisição (via kubectl ou sistema)
    - Vai para o kube-apiserver
    - Estado é salvo no etcd
    - Controladores executam ações
