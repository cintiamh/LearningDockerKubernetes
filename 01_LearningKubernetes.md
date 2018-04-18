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

### Key terms

* Kubernetes objects
  - record of intent => system works to ensure the object exists
  - persistent entities
  - represent the state of your cluster
* Kubernetes objects YAML config file
  - spec: desired state for the object
  - status: the actual state of the object (updated by Kubernetes system)
* Kubernetes Pod objects
  - Simplest Kubernetes object
  - Represents a set of running containers on your cluster
    - it's usually one container per pod
  - Pods are commonly managed by a deployment definition/object.
* Kubernetes deployment objects
  - Describe a desired state
  - Deployment controller changes the actual state to the desired state at a controlled rate.
* Kubernetes ReplicaSet
  - ensures that a specified number of pod replicas are running at any given time.
  - allow to define the number of app instances we would like to maintain
  - should be defined within a deployment object
  - provides declarative updates to pods
* Kubernetes service objects
  - it is an API object that describes how to access applications, such as a set of Pods
  - it is an abstraction which defines a logical set of Pods and a policy by which to access them (micro-service)
  - The kube-proxy in each worker node is responsible for managing virtual IPs for the service.

## Achieving high-availability
