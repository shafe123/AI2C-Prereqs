# Exposing Your Pod to the World

## Objectives

- Learn what a Kubernetes Service is
- Expose your application outside the cluster
- Access your application from your browser

## What is a Service?

Pods in Kubernetes are ephemeral - they can be created and destroyed at any time. When a Pod is destroyed, it gets a new IP address. This creates a problem: how do other applications reliably connect to your application?

A **Service** solves this problem by providing a stable endpoint (IP address and DNS name) that remains constant even as Pods come and go. Services automatically route traffic to healthy Pods.

Types of Services:
- **ClusterIP** (default) - Exposes the Service only within the cluster
- **NodePort** - Exposes the Service on each Node's IP at a static port
- **LoadBalancer** - Creates an external load balancer (in cloud environments)
- **ExternalName** - Maps the Service to an external DNS name

For this tutorial, we'll use **NodePort** since we're running on minikube.

## Before you begin

You will need:
- A running minikube cluster
- The `hello-kubernetes` deployment from the previous tutorial ([Create your First Pod](./04-Create_your_First_Pod.md))

If you deleted your deployment, recreate it using your Docker Hub image:

```bash
kubectl create deployment hello-kubernetes --image=YOUR-DOCKER-ID/hello-kubernetes:v1
```

> **Remember:** Replace `YOUR-DOCKER-ID` with your actual Docker Hub username.

Verify the deployment is running:

```bash
kubectl get deployments
```

You should see `hello-kubernetes` with 1/1 ready.

## Expose Your Deployment

Create a Service to expose your deployment:

```bash
kubectl expose deployment hello-kubernetes --type=NodePort --port=8080
```

This command:
- Creates a Service named `hello-kubernetes` (matching the deployment name)
- Sets the type to `NodePort` (accessible from outside the cluster)
- Exposes port `8080` (the port your application listens on)

You should see:

```
service/hello-kubernetes exposed
```

## View Your Service

List all services:

```bash
kubectl get services
```

You should see:

```
NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
kubernetes         ClusterIP   10.96.0.1       <none>        443/TCP          10m
hello-kubernetes   NodePort    10.100.200.50   <none>        8080:30123/TCP   5s
```

Your `hello-kubernetes` Service is listed with:
- **TYPE** - NodePort (accessible from outside)
- **CLUSTER-IP** - Internal IP address (only accessible within cluster)
- **PORT(S)** - 8080:30123/TCP means:
  - Port 8080 inside the cluster
  - Port 30123 exposed on the Node (this will be different for you)

> **Note:** The `kubernetes` Service is created automatically and provides access to the Kubernetes API.

## Get the Service URL

On minikube, you can easily get the URL to access your Service:

```bash
minikube service hello-kubernetes --url
```

This will output a URL like:

```
http://192.168.49.2:30123
```

## Access Your Application

You can now access your application in several ways:

### Option 1: Using curl

Copy the URL from the previous command and use curl:

```bash
curl $(minikube service hello-kubernetes --url)
```

You should see a response from **your** application:

```
Hello Kubernetes! Running on: hello-kubernetes-7b5d8c9b8f-abc12
```

This is the same application you created in tutorial 01!

### Option 2: Using your browser

Open the URL in your browser:

```bash
minikube service hello-kubernetes
```

This command will automatically open your default browser to the Service URL.

You should see the "Hello Kubernetes!" message with the Pod name in your browser window.

### Option 3: Manual access

If you want to manually construct the URL:

1. Get the minikube IP:
   ```bash
   minikube ip
   ```

2. Get the NodePort:
   ```bash
   kubectl get services hello-kubernetes -o go-template='{{(index .spec.ports 0).nodePort}}'
   ```

3. Combine them:
   ```bash
   curl http://<minikube-ip>:<node-port>
   ```

## Understanding How Services Work

When you access the Service:

1. **Request arrives** at the Node's IP address and NodePort
2. **Service receives** the request and selects a Pod
3. **Traffic is routed** to the selected Pod
4. **Pod responds** and the response is sent back to you

The Service acts as a load balancer, distributing traffic across all Pods in the deployment (currently we only have 1 Pod, but this will matter when we scale in a later tutorial).

## Describe the Service

To see more details about your Service:

```bash
kubectl describe service hello-kubernetes
```

This shows:
- **Endpoints** - The IP addresses of the Pods backing this Service
- **Port mappings** - How ports are mapped
- **Selector** - The label used to find Pods (automatically set based on the deployment)

## How Services Find Pods

Services use **labels** to find Pods. When you created the deployment, Kubernetes automatically added labels to your Pods. The Service uses these same labels to know which Pods to route traffic to.

View the labels on your Pods:

```bash
kubectl get pods --show-labels
```

You'll see labels like `app=hello-kubernetes`. The Service uses this label to identify which Pods belong to the Service.

## Testing Connectivity

You can verify the Service is working by making multiple requests:

```bash
for i in {1..5}; do curl $(minikube service hello-kubernetes --url); done
```

Each request should succeed and show the same Pod name (since we only have 1 Pod currently).

## Clean Up (Optional)

To remove the Service:

```bash
kubectl delete service hello-kubernetes
```

To remove both the Service and Deployment:

```bash
kubectl delete service hello-kubernetes
kubectl delete deployment hello-kubernetes
```

> **Note:** Don't delete these if you want to continue with the optional tutorials on exploring and scaling.

## Summary

You've successfully exposed your Pod to the world! You learned how to:

- Understand what Kubernetes Services are and why they're needed
- Create a Service to expose a Deployment
- Access your application from outside the cluster
- Use minikube service commands to get URLs
- Understand how Services use labels to route traffic to Pods

Congratulations! You've completed the core Kubernetes roadmap:
1. ✅ Containerized an application
2. ✅ Hosted the container in a registry
3. ✅ Installed Kubernetes (minikube)
4. ✅ Created your first Pod (via Deployment)
5. ✅ Exposed your Pod to the world (via Service)

The remaining tutorials are optional but provide valuable skills for troubleshooting and scaling your applications.

---

**Next Lesson (Optional)** - [Exploring and Troubleshooting Your Application](./06-Explore_and_Troubleshoot.md)
