# Learning Kubernetes

## Installing Kubernetes

Kubernetes helps to automate DevOps tasks like deployments, scaling, and management of containerized applications.

### Kubernetes Component Architecture

* 1 Kubernetes Master => REST API
* Multiple Kubernetes Nodes (KNode) => Master communicates with Node's Kubelet (maintain state of cluster)
* Kubernetes cluster => Master + Nodes
* User requests => Kube proxy
* DevOps / Developer => `kubectl` => Creates Kubernetes API objects and manage the cluster.

### Minikube

* small-footprint, single node Kubernetes (local => development / demonstration)
* easy to run locally
* single-node Kubenetes cluster inside a VM (VirtualBox)

AWS ECS

## Installing Dependencies

* VirtualBox (https://www.virtualbox.org/)
* `brew install kubectl`
* `brew cask install minikube`

## Installing the latest Kubernetes

Start a new minikube instance.
```
$ minikube start
```

=> this might take a while. :-D

## Exploring your Kubernetes Installation

```
$ kubectl run hello-minikube --image=gcr.io/google_containers/echoserver:1.4 --port=8080
$ kubectl expose deployment hello-minikube --type=NodePort
$ kubectl delete deployment hello-minikube
$ kubectl get pod
```

Test if the pod is up and running
```
$ curl $(minikube service hello-minikube --url)
```

Stop => turn down cluster
```
$ minikube stop
```

## Kubernetes architecture and design
