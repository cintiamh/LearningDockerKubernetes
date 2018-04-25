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

* Load balancing between multiple master nodes
  - maintain multiple master nodes, accessed via load balancer
  - keep multiple master components up and healthy, but only one acting at a time.
  - have a clustered and replicated etcd storage for redundancy
* Federation
  - multiple kuberenetes clusters (kubefed)
  - federation control plane to manage remote clusters
  - provide a host cluster to make up the federation control plane
  - expand across multiple clusters

=> Federation

## Scaling Kubernetes

### Scaling Horizontally

Autoscaling in Kubernetes:
```
$ kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
```

## Federation

* clusters may be spread across availability zones.

* Hight cost

High-level steps:
* install kubefed CLI
* Choose a host cluster to deploy the federation control plane
* Deploy the federation control plane
* Add your clusters to the federation

## Managing secrets and configuration

### Configuration best practices

Configuration:
* API objects => YAML or JSON => using kubectl or API.

```
$ kubectl create -f <directory>
```

* YAML over JSON
* Related API objects => group single YAML file
  - `---`: logically separate object definitions within a YAML file.
* Don't define default values

Best practices:
* `kubectl create -f <directory>`
* `kubectl delete` - `delete` is deprecated
* `kubectl run` - quickly create and expose single container deployments

Node affinity:
* Taints and tolerations work together to ensure that pods are not scheduled onto inappropriate nodes
* Node affinity, is a property of pods that attract (toleration) or repels (taint) them to a set of nodes.
* Taints are applied to nodes, while tolerations are applied to pods.
* `kubectl taint nodes <node-name> <params>` - apply taint to a node
* Tolerations are specified in the PodSpec tolerations

```
tollerations:
  - key: "key"
    operator: "Equal"
    value: "value"
    effect: "NoSchedule"
```

## Creating and Decoding Secrets

```
$ kubectl create secret -f ./secret.yaml
$ kubectl create secret generic db-user-pass --from-file=./username.txt --from-file=./password.txt
$ kubectl delete secret db-user-pass
$ kubectl get secrets
$ kubectl describe secrets/db-user-pass
```

Encode / decode:
```
$ echo -n "admin" | base64
$ echo -n "1f2d1e2e67df" | base64
$ echo "YWRtaW4=" | base64 --decode
```

## Using secrets in applications

yaml configuration
```
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
  password: MWYyZDFlMmU2N2Rm
```

## Deploying an application to Kubernetes

### Overview: Docker Containerization

Container Image:
* lightweight, stand-alone, and executable package
* includes everything needed to run a piece of software
* includes the code, a runtime, libraries, environment variables, and config files needed to run a piece of software
* Docker image => proprietary container image that will run on the Docker container runtime.

Container:
* Runtime instance of an image
* It is what the image becomes in memory when actually executed.
* It runs completely isolated from the host environment by default, only accessing host files and ports if configured to do so.
* Run apps natively on the host machine's kernel
* have better performance characteristics than virtual machines.
* Can get native access, taking no more memory than any other executable.

Hypervisor => VirtualBox

Docker platform:
* Docker Engine
  - A portable, lightweight, application runtime and packaging tool
  - The Docker engine is what you install onto your physical or virtual hosts
  - The Docker engine provides a CLI tool we can use to run Docker commands, to create and run Docker images.
  - A cluster of hosts running Docker can be federated into a Swarm
* Docker Hub
  - Like a repository for Docker images
  - There is the Docker Hub web service
  - Enterprises can opt to have their own private Container repository.

### Installing Docker and Building the Image

Using docker pre-packed with Minikube.

You can install Docker separated.

In your project folder
```
$ touch Dockerfile
```

Dockerfile
```
FROM openjdk:8
COPY hello-world-1.jar /app.jar
EXPOSE 8080/tcp
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

```
$ minikube start
# Point your docker client to the docker daemon in your minikube VM
$ eval $(minikube docker-env)
# Build your docker image within minikube
$ docker build -t hello-java:v1 .
# Check images
$ docker images
```

### Deploying Your Docker Container

```
# Run your docker image
$ kubectl run hello-java --image=hello-java:v1 --labels="app=hello-java" --port=8080
# Start your application pod
$ kubectl get pods
```

### Interacting With Your Container

```
# Create a service
$ kubectl create -f service.yml
```

service.yml
```
apiVersion: v1
kind: Service
metadata:
  name: hello-service
spec:
  ports:
  - port: 8080
    targetPort: 8080
  selector:
    app: hello-java
  type: NodePort
```

```
$ kubectl get services
$ minikube service hello-service
```

This will open a browser with and URL (IP).
