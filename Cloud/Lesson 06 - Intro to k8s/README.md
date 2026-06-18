# Lesson 06 - Introduction to Kubernetes

This repository provides an overview of **Kubernetes (K8s)**, the industry-standard orchestration platform for automating the deployment, scaling, and management of containerized applications.


## 🎯 Lesson Objectives

* Define **Kubernetes** and its role in modern infrastructure.
* Explain the **problems** Kubernetes was designed to solve.
* Identify and describe **key components** of a Kubernetes cluster.


## 🏗️ What is Kubernetes?

Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services. It provides a framework to run distributed systems resiliently.


### The Evolution of Deployment

* **Traditional Deployment:** Applications ran on physical servers with no way to define resource boundaries, leading to resource contention.
* **Virtualized Deployment:** Introduced Hypervisors to run multiple VMs on a single CPU, providing isolation and better resource utilization.
* **Container Deployment:** Similar to VMs but with relaxed isolation properties to share the Operating System (OS) among applications, making them lightweight and agile.


## 🛠️ Why Use Kubernetes?

Kubernetes acts as a "cluster brain," ensuring your applications run exactly as defined.


### Key Solutions Provided:

* **Service Discovery & Load Balancing:** Kubernetes can expose a container using a DNS name or IP address and distribute network traffic to maintain stability.
* **Storage Orchestration:** Automatically mount a storage system of your choice (local, public cloud, etc.).
* **Automated Rollouts/Rollbacks:** Describe the desired state for deployed containers, and K8s changes the actual state to the desired state at a controlled rate.
* **Self-Healing:** Restarts failed containers, replaces/kills containers that don't respond to health checks, and hides them from clients until ready.
* **Secret & Configuration Management:** Store and manage sensitive information (passwords, tokens) without rebuilding images or exposing secrets in stack configuration.


## 🧩 Kubernetes Architecture & Components

A Kubernetes deployment is called a **cluster**, consisting of a set of worker machines (nodes) that run containerized applications.


### 1. The Control Plane (The "Brain")

The Control Plane makes global decisions about the cluster and detects/responds to cluster events.

* **kube-apiserver:** The front end for the Kubernetes control plane.
* **etcd:** Consistent and highly-available key-value store for all cluster data.
* **kube-scheduler:** Watches for newly created Pods with no assigned node and selects a node for them to run on.
* **kube-controller-manager:** Runs controller processes, such as the Node Controller and Job Controller.


### 2. Node Components

Node components run on every node, maintaining running pods and providing the Kubernetes runtime environment.

* **kubelet:** An agent that ensures containers are running in a Pod.
* **kube-proxy:** A network proxy that maintains network rules on nodes, allowing communication to Pods from inside or outside the cluster.
* **Container Runtime:** The software responsible for running containers (e.g., Docker, containerd).


### 3. Key Objects

* **Pods:** The smallest deployable units of computing that you can create and manage in Kubernetes. A Pod represents a single instance of a running process in your cluster.


## 🗺️ Kubernetes Roadmap

1. **Containerize your application** - Create a Dockerfile and build a Docker image
2. **Host your container** - Push your image to a container registry (Docker Hub)
3. **Install Kubernetes** - Set up a local cluster using minikube
4. **Create your first Pod** - Deploy your application using Deployments
5. **Expose your Pod to the world** - Create a Service to make your app accessible

# Optional Intermediate Tutorials

6. **Explore and troubleshoot** - Learn debugging techniques with kubectl *(optional)*
7. **Scale your application** - Run multiple replicas for high availability *(optional)*
