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