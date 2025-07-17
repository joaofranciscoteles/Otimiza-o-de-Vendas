# Documentação do Projeto – Redis + Kubernetes

## 1. Integrantes

  * Felipe Werneck
  * João Francisco

## 2. Introdução

O **Kubernetes** é uma plataforma de orquestração de containers open-source que automatiza a implantação, escalonamento e operações de aplicativos. Ele foi desenvolvido pelo Google e hoje é um dos principais padrões para orquestrar ambientes containerizados em produção.

### O que é o Redis?

O **Redis** é um banco de dados **NoSQL** de chave-valor que armazena os dados em memória, tornando-o extremamente rápido para operações de leitura e escrita. Ele é amplamente utilizado como um cache para melhorar o desempenho de aplicações, além de oferecer funcionalidades para trabalhar com listas, conjuntos, mapas e até mesmo operações geoespaciais.

O objetivo deste projeto é implantar uma aplicação Redis em um ambiente **Kubernetes**, utilizando seus principais recursos como **Pods**, **Deployments** e **Services** para garantir escalabilidade, resiliência e alta disponibilidade.

## 3. Arquitetura Kubernetes

O Kubernetes organiza os containers em **Pods**, gerencia a replicação de containers por meio de **Deployments** e expõe esses containers para comunicação interna e externa com **Services**. 

### **Pods**

O Pod é a menor unidade de implantação no Kubernetes. Ele pode conter um ou mais containers, sendo que esses containers compartilham a mesma rede e armazenamento. No contexto do Redis, um Pod pode ser suficiente para rodar uma instância Redis.

### **Deployments**

O Deployment é responsável por garantir que um número desejado de Pods esteja rodando a todo momento. Ele permite que os Pods sejam atualizados de maneira segura e eficiente, escalando a aplicação para lidar com mais tráfego, caso necessário.

### **Services**

O Service é utilizado para expor um conjunto de Pods a outros Pods dentro do cluster Kubernetes ou até mesmo externamente. Existem diferentes tipos de Service, como **ClusterIP** (acesso interno ao cluster) e **NodePort** (acesso externo).

Neste projeto, utilizamos o tipo **ClusterIP** para o Redis, já que o serviço só precisa ser acessado internamente pelos outros Pods do cluster.

## 4. Instruções de Instalação do Ambiente Kubernetes

### Passos para Instalar o **Minikube** e o **kubectl** no WSL

1. **Instalar o kubectl** (ferramenta de linha de comando para Kubernetes):

   ```bash
   # Baixar o kubectl
   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
   # Tornar o kubectl executável
   chmod +x kubectl
   # Mover para o diretório correto
   sudo mv kubectl /usr/local/bin/
2. **Instalar o Minikube** (utilizado para criar um cluster Kubernetes local):
    ```bash
    # Baixar o Minikube
    curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
    # Instalar o Minikube
    sudo install minikube-linux-amd64 /usr/local/bin/minikube
3. **Iniciar o Cluster com o driver Docker** 
    ```bash
    #Iniciar o Minikube com o driver Docker e a flag --force (caso necessário):
    minikube start --driver=docker --force
    #Verificar o status do cluster:
    kubectl get nodes
4. **Acessar o Minikube Dashboard**
    ```bash
    minikube dashboard

## 5. Arquivos de Configuração YAML

### Manifesto para o Deployment do Redis

Abaixo está o arquivo YAML usado para criar o **Deployment** do Redis, que definiu a imagem a ser usada, o número de réplicas (Pods) e a configuração do container.

```inhame
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-deployment
spec:
  replicas: 1  # Número de réplicas (pods)
  selector:
    matchLabels:
      app: redis  # Seleção de pods baseados no label
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis:latest  # Imagem do Redis
        ports:
        - containerPort: 6379  # Porta do Redis
```

## Manifesto para o Service do Redis

Este manifesto cria um **Service** que expõe o Redis internamente no cluster com o tipo **ClusterIP**, o que significa que o Redis será acessível apenas dentro do cluster.Abaixo está o arquivo utilizado para tal funcionalidade.

```inhame
apiVersion: v1
kind: Service
metadata:
  name: redis-service
spec:
  selector:
    app: redis  # Seleciona os Pods com o label "app=redis"
  ports:
    - protocol: TCP
      port: 6379  # Porta que o Service expõe
  clusterIP: None  # Torna o service acessível apenas dentro do cluster
```

## Comandos utilizados para Execução e Teste
```festança
# Aplica o manifesto do Deployment
kubectl apply -f redis-deployment.yaml

# Aplica o manifesto do Service
kubectl apply -f redis-service.yaml

# Verifica os Pods no cluster
kubectl get pods

# Verifica os Serviços no cluster
kubectl get services

# Teste a comunicação com o Redis dentro do cluster
kubectl run -it --rm --restart=Never redis-cli-test --image=redis:latest -- bash
redis-cli -h redis-service SET usuario:100 "Joao"
redis-cli -h redis-service GET usuario:100
```
## 7. Prints das Execuções
![Print da criação do Deployment](redis_hash.jpeg)
![Print do Service](redis_AOF.png)
![Print do funcionamento do Redis](logs.jpeg)
![Print do funcionamento do DashBoard](logs.jpeg)










