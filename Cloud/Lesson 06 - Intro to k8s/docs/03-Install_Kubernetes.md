# Setting Up Kubernetes with Minikube

## Objectives

- Start a Kubernetes cluster using minikube
- Explore the Kubernetes dashboard
- Verify your cluster is working

## Before you begin

You should have completed the previous tutorials:
- [Containerize your Application](./01-Containerize_your_Application.md) - Built your container image
- [Host your Container](./02-Host_your_Container.md) - Pushed your image to Docker Hub

You will also need Docker, kubectl, and minikube installed. If you haven't installed these tools yet, see the installation instructions below.

## Install Required Tools

If you haven't already installed Docker, kubectl, and minikube, follow these steps:

### Installing Docker

Docker is required to run minikube and work with containers.

- [Install Docker Desktop](https://docs.docker.com/get-docker/) - Available for Windows, macOS, and Linux

Verify Docker is installed:

```bash
docker --version
```

### Installing kubectl

kubectl is the Kubernetes command-line tool.

- [Install kubectl on Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)
- [Install kubectl on macOS](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/)
- [Install kubectl on Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/)

Verify kubectl is installed:

```bash
kubectl version --client
```

### Installing minikube

minikube runs a local Kubernetes cluster.

- [Install minikube](https://minikube.sigs.k8s.io/docs/start/)

Verify minikube is installed:

```bash
minikube version
```

## Create a minikube cluster

Start your local Kubernetes cluster with the following command:

```bash
minikube start
```

This command will:
- Download the minikube ISO (first time only)
- Create a virtual machine
- Configure Kubernetes on the VM
- Set up kubectl to communicate with the cluster

You should see output similar to:

```
  minikube v1.32.0 on Windows 10
  Automatically selected the docker driver
  Starting control plane node minikube in cluster minikube
  Pulling base image ...
  Creating docker container (CPUs=2, Memory=4000MB) ...
  Preparing Kubernetes v1.28.3 on Docker 24.0.7 ...
  Configuring bridge CNI (Container Networking Interface) ...
  Verifying Kubernetes components...
  Enabled addons: storage-provisioner, default-storageclass
  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

## Verify the Cluster

Check that kubectl is configured to communicate with your cluster:

```bash
kubectl cluster-info
```

You should see:

```
Kubernetes control plane is running at https://192.168.49.2:8443
CoreDNS is running at https://192.168.49.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

View the nodes in your cluster:

```bash
kubectl get nodes
```

You should see one node (since minikube is a single-node cluster):

```
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   1m    v1.28.3
```

## Open the Dashboard

Minikube includes a web-based Kubernetes dashboard for visualizing your cluster.

Open the Kubernetes dashboard:

```bash
minikube dashboard
```

This command will:
- Enable the dashboard addon (if not already enabled)
- Start a proxy to the dashboard
- Open your default web browser to the dashboard URL

> **Note:** Keep the terminal window open while using the dashboard. Closing the terminal will stop the dashboard proxy.

The dashboard provides a graphical interface where you can:
- View all resources in your cluster (Pods, Deployments, Services, etc.)
- Create new resources
- View logs and events
- Monitor resource usage

## Alternative: Get Dashboard URL

If you prefer to open the dashboard manually or want to copy the URL:

```bash
minikube dashboard --url
```

This will print the URL without opening the browser:

```
http://127.0.0.1:12345/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
```

You can then open this URL in any browser.

## Understanding kubectl Configuration

When you started minikube, it automatically configured kubectl to communicate with your cluster. You can view this configuration:

```bash
kubectl config view
```

This shows:
- Cluster information (server address, certificate)
- User credentials
- Contexts (combinations of cluster, user, and namespace)

To see which context is currently active:

```bash
kubectl config current-context
```

You should see:

```
minikube
```

## Useful Minikube Commands

Here are some helpful minikube commands for managing your cluster:

```bash
# Check cluster status
minikube status

# Stop the cluster (preserves state)
minikube stop

# Start the cluster again
minikube start

# Delete the cluster (removes all data)
minikube delete

# SSH into the minikube VM
minikube ssh

# View minikube logs
minikube logs
```

## Troubleshooting

If you encounter issues starting minikube:

1. **Check Docker is running** - Minikube uses Docker as the default driver
   ```bash
   docker ps
   ```

2. **Delete and recreate the cluster**
   ```bash
   minikube delete
   minikube start
   ```

3. **Specify a different driver** (if Docker doesn't work)
   ```bash
   minikube start --driver=virtualbox
   ```

4. **Check minikube logs**
   ```bash
   minikube logs
   ```

## Summary

You've successfully set up a local Kubernetes cluster! You learned how to:

- Start a Kubernetes cluster using minikube
- Verify the cluster is running with kubectl
- Access the Kubernetes dashboard
- Use basic minikube commands

Your Kubernetes cluster is now ready for deploying applications. In the next tutorial, we'll create your first Pod!

---

**Next Lesson** - [Creating Your First Pod](./04-Create_your_First_Pod.md)
