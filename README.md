# Kubernetes
- Orquestração de Containers

## Cloud Providers
- AWS
- Azure
- GCP

## Arquitetura
- pod (encapsula um container)
- rs
- deploy
- vol
- hpa
- pv
- ing
- pvc
- svc
- sc
- ds
- quota

## Kubernetes
- Cluster
- Master (api, c-m, sched, etcd) [control plane]
    - Gerenciar o cluster
    - manter e atualizar o estado desejado
    - receber e executar novos comandos
- Node (kubelet, k-proxy) [nodes]
    - executar as aplicações

1. install k8s
2. install minikube
3. install virtual box

```
minikube start --vm-driver=virtualbox

kubectl get nodes
```

## primeiro pod
```
kubectl run <nome-do-pod> --image=<nome-da-imagem:versão>

## criar
kubectl run nginx-pod --image=nginx:latest

kubectl get pods
kubectl get pods --watch

## descrição
kubectl describe pod nginx-pod

## editar
kubectl edit pod nginx-pod
```


## api versions
- Alpha
- Beta
- Stable


## first-pod-declarativo
```
kubectl apply -f ./first-pod.yaml
kubectl get pods
```

## deletar um pod
```
kubectl delete pod nginx-pod

## declarativo
kubectl delete -f ./first-pod.yaml
```

## execução do pod de maneira iterativa
```
kubectl exec -it portal-noticias -- bash
```

## Service (ClusterIP, NodePort, LoadBalance)
- Um ClusterIP funciona apenas dentro do cluster
- Labels são responsáveis por definir a relação Service x Pod
- Abstração para expor aplicações executando em um ou mais pods
- Proveem IP's fixos para comunicação
- Proveem um DNS para um ou mais pods
- São capazes de fazer balanceamento de carga

```
kubectl get service
kubectl get svc
```

## NodePort: Abre Comunicação para o mundo externo
- Um NodePort expõe Pods para dentro e fora do cluster
```
kubectl get nodes -o wide (get the value of INTERNAL-IP)
```

```
kubectl delete -f ./svc-pod-1-loadbalancer.yaml
kubectl delete -f ./svc-pod-1.yaml
kubectl delete -f ./pod-1.yaml
```
### LoadBalance
- Por serem um Load Balancer, também são um NodePort e ClusterIP ao mesmo tempo.
- Um LoadBalancer também é um NodePort e ClusterIP
- Um LoadBalancer é capaz de automaticamente utilizar um balanceador de carga de um cloud provider


### deletar tudo
```
kubectl delete pods --all
kubectl delete service --all
```

### subindo o portal de noticias com service
```
kubectl apply -f ./portal-noticias.yaml
kubectl apply -f ./svc-portal-noticias.yaml

kubectl get nodes -o wide
```
|NAME|STATUS|ROLES|AGE|VERSION|INTERNAL-IP|EXTERNAL-IP|OS-IMAGE|KERNEL-VERSION|CONTAINER-RUNTIME|
|---|---|---|---|---|---|---|---|---|---|
|minikube|Ready|control-plane,master|3d18h|v1.20.7|192.168.99.105|<none>|Buildroot|2020.02.12|4.19.182|docker://20.10.6|


```
kubectl apply -f ./sistema-noticias.yaml
kubectl apply -f ./svc-sistema-noticias.yaml

kubectl get nodes -o wide


kubectl apply -f ./db-noticias.yaml
kubectl apply -f ./svc-db-noticias.yaml

kubectl exec -it db-noticias -- bash
```

### ConfigMap (desacoplar configurações e definições com um ConfigMap)
```
kubectl apply -f ./db-configmap.yaml
kubectl apply -f ./sistema-configmap.yaml
```
### Pod
```
kubectl apply -f ./db-noticias.yaml
kubectl apply -f ./portal-noticias.yaml
kubectl apply -f ./sistema-noticias.yaml
```

### Service
```
kubectl apply -f ./svc-db-noticias.yaml
kubectl apply -f ./svc-portal-noticias.yaml
kubectl apply -f ./svc-sistema-noticias.yaml
```

kubectl get rs
kubectl get deployments
kubectl rollout history deployment nginx-deployment
kubectl apply -f nginx-deployment.yaml --record
kubectl annotate deployment nginx-deployment kubernetes.io/change-cause="Mensagem mais amigavel..."
kubectl rollout undo deployment nginx-deployment --to-revision=2

## Quando criados, Deployments auxiliam com controle de versionamento e criam um ReplicaSet automaticamente.
ReplicaSet --> Pod
Deployment --> ReplicaSet --> Pod

## limpando o ambiente
```
kubectl get pods
kubectl delete deployment nginx-deployment
kubectl delete -f ./portal-noticias-replicaset.yaml
```

kubectl annotate deployment portal-noticias-deployment kubernetes.io/change-cause="Criando portal de noticias na versão 1"
kubectl rollout history deployment portal-noticias-deployment

kubectl delete pod sistema-noticias
kubectl apply -f ./sistema-noticias-deployment.yaml

kubectl delete pod db-noticias
kubectl apply -f ./db-noticias-deployment.yaml
kubectl delete deployment db-noticias-deployment


## Volumes
- Volumes possuem ciclo de vida dependentes de Pods e independentes de containers.

kubectl exec -it pod-volume --container nginx-container -- bash

no linux precisa via minikube
minikube ssh
cd /home && sudo mkdir primeiro-volume

OU
...
        type: Directory
...        
        type: DirectoryOrCreate
...        
## Persistence Volume
### AWS
aws ec2 create-volume --availability-zone=sa-east-1a --size=8 --volume-type=gp2
awsElasticBlockStore
## Persistence Volume Claim
PV <--> PVC

PersistentVolumes possuem ciclos de vida independentes de Pods.
É necessário um PersistentVolumeClaim para acessar um PersistentVolume.

- Como criar Volumes através de arquivos de definição
- Volumes persistem dados de containers dentro de pods e permitem o compartilhamento de arquivo dentro dos pods
- Que Volumes possuem ciclo de vida independente dos containers, porém, vinculados aos pods
- Como criar PersistentVolumes através de arquivos de definição
- PersistentVolumes persistem dados de pods como um todo
- PersistentVolumes possuem ciclo de vida independente de quaisquer outros recursos, inclusive pods
- Como criar e para que servem os PersistentVolumeClaims
- Que precisamos de um PersistentVolumeClaim para acessar um PersistentVolume


## Storage Class
PVC e PV de forma dinâmica
Storage Classes fornecem dinamismo para criação de PersistentVolumes conforme demanda.

## Stateful Set
Quando um pod falha ou reinicia o volume ainda esta intacto
pod --> pvc --> pv
sistema-noticias-stateful.yaml
 kubectl delete deployments sistema-noticias-deployment


### Visualizando os eventos do cluster:
kubectl get events

### Visualizando a configuração do kubectl:
kubectl config view

minikube service svc-pod-1-loadbalancer
minikube service --url svc-pod-1


### parando tudo
```
kubectl delete service hello-node
kubectl delete deployment hello-node
### (Opcional) Pare a máquina virtual (VM) do Minikube:
minikube stop
### (Opcional) Remova a VM do Minikube:
minikube delete
```


## Probes
- Tornar visivel ao kubernetes que uma aplicação não está se compartando da maneira esperada;

## Liveness (são para saber se a aplicação está saudável e/ou se deve ser reiniciada)
portal-noticias-deployment.yaml

## Readiness (são para saber se a aplicação já está pronta para receber requisições depois de iniciar)
portal-noticias-deployment.yaml

## Escalando pods automaticamente
Horizontal Pod Autoscaler
baseado em metricas

```
minikube addons list
minikube addons enable metrics-server
```