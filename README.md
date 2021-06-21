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