---
layout: post
title: "Learning Kubernetes"
date: 
permalink: /notes/linkedin-learning/learning-kubernetes
---

# Containerisation

> A container is a collection of software processes unified by one namespace, with access to an operating system kernel that it shares wth other containers and little to no access between containers.

Docker is the most popular form of containerisation. To run an instance of a Docker image, you require a Docker image, an execution environment, and a standard set of instructions.

The Docker ecosystem relies upon the Docker Engine and Docker Store. The engine, installed on a host machine that runs Docker, provides the runtime and build tools. A store is a cloud service where Docker images can be stored and shared - examples of stores include Docker Hub, Cloudsmith, and AWS ECR.

Containers are different to Virtual Machines. A VM can have many applications and their dependencies, as well as the guest operating system. Containers include just the application and the dependencies. It shares the kernel with other containers and so it is not reliant on any particular infrastructure - it only requires the runtime, e.g. Docker Engine, to be installed on the host. Containers can therefore run on almost any infrastructure - local computers, on-premises infrastructure, or cloud.

Containers are portable and packaged in a standard way. Containerisation supports microservice architectures while also reducing platform compatibility problems. Environments are consistent between staging, development, and production. Containers can be scaled up and down easily.

They make deployment of applications easy and repeatable. Deployment pipelines can perform automated testing, packaging, and integrations. This creates fast, reliable deployments that can be triggered often.

There is a common ground, created by containers, for development and deployment teams alike.


# Kubernetes Overview

Kubernetes is an open-source container orchestration engine. It schedules and maintains containers at scale.

A single Docker host runs a median of around 10 containers at a time. Kubernetes comes in to help manage all these running containers on not just a single host, but across the entire infrastructure. Container orchestration solves the problem of deploying one or many containers, as part of an application, across multiple hosts.

A basic orchestrator needs to be able to provision hosts, instantiate containers on those hosts, restart failing containers, expose containers externally where required, and scale the cluster. 

Kubernetes provides a platform to schedule and run containers on any infrastructure. Docker can be used to build applications, while Kubernetes can be used to run them. Kubernetes provides multi-host container scheduling - in other words, it assigns pods (container) to a host (node) at runtime based on the user specifications and the available resources within the cluster (collection of nodes).

Multi-region deployments allow for high availability Kubernetes clusters. The cluster can scale up to 5000 nodes with 150000 total pods. Kubernetes allows plugging add-ons to extend the architecture as required.

Nodes self-register with the master, while service discovery automatically detects services and endpoints over DNS or environment variables.

Data is persisted with persistent volumes - data is retained even through pod crashes and restarts. Application upgrades and rollbacks are also provided out-of-the-box. There is monitoring for the health of applications via TCP, HTTP, or container execution, as well as for the health of nodes.

Secrets are a first-class citizen in Kubernetes. They are mounted as data volumes or environment variables and are specific to a particular namespace, ensuring that the scope isn't across all applications.

There are other orchestrators available outside of Kubernetes. With orchestration being such a difficult problem to solve, and now with so many options around, it makes little sense to try create your own orchestrator. Alternatives include Docker Swarm, Rancher, and Mesos. There are also some scenarios where it makes sense to use a Serverless architecture instead of orchestrating containers.


# Kubernetes Architecture

The master node is responsible for the overall management of the cluster. It has an API server which exposes the control plane of the cluster. The scheduler assigns pods, as they are started, to a specific node. The controller manager helps manage controllers which run background tasks within the cluster - worker states, replication controllers for pod replicas, endpoint joining of services and pods, service account and token controllers for access management.

The master node uses a simple, distributed key value store, `etcd`, to store cluster data e.g. job scheduling, pod details, state information. Interaction with the master node is done through the `kubectl` command line interface. The `kubeconfig` file has server and authentication information to allow `kubectl` to access the API server.

<img src="/assets/kubernetes-architecture.png" alt="kubernetes-architecture.png" title="High level overview of the Kubernetes components"/>

Worker nodes are where the containers are actually run. Each worker node has a `kubelet` process and a `kube-proxy` process. The `kubelet` communicates with the master node API server to determine if any pods have been assigned to the node. It then executes the pod containers on the container engine, mounts volumes and secrets, and relays pod and node states to the master node. The `kube-proxy` acts as the network proxy and load balancer, handling network routing and connection forwarding. Worker nodes can also be exposed to the internet, in which case the `kube-proxy` also handles that traffic, allowing end users to talk to an application deployed on the cluster.


# Nodes and Pods

A node is a worker machine within the cluster. It can be a physical or virtual machine. Each node must have a `kubelet` process, `kube-proxy` process, container runtime, and a process like Supervisord which can restart components. When using Kubernetes in production, the recommendation is to have at least three nodes within the cluster.

Minikube is a simple way to get a lightweight cluster running on a local machine - it creates a virtual machine and deploys a single-node cluster.

A pod is the simplest unit in the Kubernetes world, representing a process running on the cluster. The pod includes your Docker application container or containers, storage, **a unique IP**, and a configuration of how the container should run. You can create, deploy, and delete pods. Pods are designed to be ephemeral and disposable. It is advised to use higher-level controllers, e.g deployments, instead of pods directly. Pods have many states throughout their lifecycle - `Pending`, `Running`, `Succeeded`, `Failed`, or `CrashLoopBackOff`.
# **Nodes and Pods**

A node is a worker machine within the cluster. It can be a physical or virtual machine. Each node must have a `kubelet` process, `kube-proxy` process, container runtime, and a process like Supervisord which can restart components. When using Kubernetes in production, the recommendation is to have at least three nodes within the cluster.

Minikube is a simple way to get a lightweight cluster running on a local machine - it creates a virtual machine and deploys a single-node cluster.

A pod is the simplest unit in the Kubernetes world, representing a process running on the cluster. The pod includes your Docker application container or containers, storage, ****a unique IP****, and a configuration of how the container should run. You can create, deploy, and delete pods. Pods are designed to be ephemeral and disposable. It is advised to use higher-level controllers, e.g deployments, instead of pods directly. Pods have many states throughout their lifecycle - `Pending`, `Running`, `Succeeded`, `Failed`, or `CrashLoopBackOff`.


# Controllers

Pods are the basic building blocks in Kubernetes, but we use higher level controllers instead of pods directly. Controllers help with application reliability, scaling, and load balancing.

## Deployments and ReplicaSets

A `ReplicaSet` defines how many replicas of a pod should be running at all times and monitors these pods as a single unit. When the number of pods reduces below the specified count e.g. due to a pod crashing, the `ReplicaSet` controller will start up a new instance.

The `Deployment` controller uses YAML files to declare the creation, and updates of, `ReplicaSets` and therefore pods. Each time a new deployment configuration is applied, a new `ReplicaSet` is created. The previous `ReplicaSet` is preserved and made available for rollback in case the new deployment is configured incorrectly.

## DaemonSets

A `DaemonSet` will ensure that every node in the cluster has a copy of a specific pod. As the cluster adds or removes a node, a `DaemonSet` will add or remove that pod. Removing a `DaemonSet` will remove all associated pods. Common uses are for log aggregration and monitoring.

## Jobs

A `Job` monitors completion of pods carrying out batched processes. Cron jobs are used to run a process at a given time and repeat at another time. 

## Services

A `Service` creates networking between pods across different deployments. Each `Service` is assigned a unique IP address which persists through the lifetime of the `Service`.

Pods can be configured to communicate with the `Service` rather than an individual, ephemeral pod. For example, a frontend pod would depend on a backend `Service` instead of an individual backend pod which may not later exist.

A `Service` can be internal (`ClusterIP`), where the IP is only reachable within the cluster, or it can be external (`NodePort`), where an endpoint is exposed on the IP of each `Node` at a static port. A `Service` can also be a `LoadBalancer` type when using an external cloud provider's load balancing capability.


# Organisation

## Labels

A label is a key value pair attached to a Kubernetes object. The key is unique per object. Labels are used to organise the objects within the cluster in some meaningful way. They can be added at deployment or afterwards, and can be modified at any time.

## Selectors

Labels are powerful when used with selectors. Selectors pluck out objects based on their labels. There are equality-based selectors (equal or not equal) and set-based (in, not in, or exists). Typically, selectors are used with `kubectl` to filter objects.

## Namespaces

A namespace is a virtual segment of the physical cluster. Namespaces are useful for larger organisations where there are many users utilising the cluster resources - a namespace can be scoped for a particular function, team, or subset of users. Names for objects are scoped within the namespace, allowing names to be reused within the cluster. There is a `default` namespace when a cluster is created.

## kubelet

The `kubelet` is an agent running on each node in the cluster. It watches for pod assignments to the node, executes pod containers using the container engine, manages pod volumes and secrets, and runs status health checks against pods and the node.

`kubelet` checks that the containers defined in `PodSpecs`, provided by the `kube-apiserver`, are healthy and running. Containers created outside of Kubernetes are not managed by `kubelet`.

## kube-proxy

The `kube-proxy` also runs on each worker node in the cluster. It reflects services, defined against the `kube-apiserver`, onto each node and forwards network traffic across backends. There are three operating modes for `kube-proxy`: `userspace` (older and fall-back mode), `iptables` (faster and current default), and `ipvs` or `kernelspace` (Windows).

`kube-proxy` monitors the API server for the addition and removal of services. When a new service is detected, a random port is opened on the local node and connections to the port are proxied to an appropriate backend pod.


# Cluster basics

We use `kubectl`, the Kubernetes command-line tool, to interact with a cluster. To run a local Kubernetes cluster, we use `minikube`.

## Installation

Using Homebrew, `brew install kubectl` or `brew install kubernetes-cli` to install `kubectl`. Official installation documentation [here](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

To install `minikube` using Homebrew, `brew install minikube`. Docker is required for `minikube`. Official installation documentation [here](https://minikube.sigs.k8s.io/docs/start/).

# Starting a local cluster

To start a local Kubernetes cluster, `minikube start`. A hypervisor is required, but `minikube` will automatically select one. This will start a `minikube` cluster and configure `kubectl` to use the `minikube` cluster. Confirm that this worked by running `kubectl get nodes` and seeing a `minikube` node running.

To create a resource from a template, we run `kubectl create -f <template-file-path>`. This is imperative, whereby we tell Kubernetes explicitly to create a resource. We can also take a declarative approach using `kubectl apply -f <template-file-path>`, where we declare what end state we would like e.g. if a resource did not already exist, it would be created, but if it already exists, it would be updated.

A deployment will create a `ReplicaSet` which will in turn create the necessary pods. To access the deployment, we must expose it as a service. This can be done in template or with `kubectl expose deployment <deployment-name> --type=NodePort`. We can then access the service with `kubectl service <service-name>`.


# Production techniques

## Labels

Labels are useful for tagging the various objects within your cluster to allow for better sorting, searching, and general management of objects.

Labels can be applied as part templates under by adding `<key>: <value>` pairs under `metadata` > `labels`. They can also be added, updated, or removed using `kubectl label <object> <key>=<value>` where `<value>` is a `-` if the label is to be deleted. You can see labels on objects using the `--show-labels` flag when using `kubectl get` commands.

Selectors can be used to select objects based on their labels. This is done using the `--selector` or `-l` flag e.g. `kubectl get pods -l env=production` would get all pods with the label `env` with value `production`. Running the same command with `delete` instead of `get` would delete those selected pods. There are other operators like `!=`, `in`, and `notin` which can help with selecting your desired objects.

## Probes

Probes are checks which help determine the health of a container. The `readinessProbe` checks if a container is ready, while the `livenessProbe` is used to ensure that the pod remains healthy. When the `livenessProbe` fails `failureThreshold` times, Kubernetes will try restarting the pod. After a few failures, the pod will go into a `CrashLoopBackOff` status.

## Rollouts

When creating or modifying resources imperatively (`create`, `set`, etc.), using the `--record` flag will record the executed command in the resource change cause annotation. 

We can use the `kubectl rollout history` command on a resource to see what revisions exist and the change cause associated with each revision. We can roll back to a specific revision as so `kubectl rollout undo <object> --to-revision=<revision>`.

## Basic troubleshooting

The `kubectl describe <object>` command displays useful information relating to the object. The events section of this can reveal reasons and messages for what might be happening e.g. no pull access for an image repository.

Finding the pod that you want to troubleshoot and then running the `kubectl logs <pod>` command will dump the pod's logs (`stdout`).  We can also run `kubectl exec -it <pod> /bin/sh` to go into the pod's shell to run commands within the pod.
