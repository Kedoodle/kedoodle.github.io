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
