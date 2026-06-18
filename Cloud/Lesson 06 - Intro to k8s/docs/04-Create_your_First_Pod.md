# Creating Your First Pod

## Objectives

- Learn about Pods and Deployments
- Deploy your containerized application to Kubernetes
- View and verify your deployment

## What are Pods?

A **Pod** is the smallest deployable unit in Kubernetes. A Pod represents a single instance of a running process in your cluster and can contain one or more containers.

Key characteristics of Pods:
- Pods are ephemeral (temporary) - they can be created, destroyed, and recreated
- Each Pod gets its own IP address
- Containers within a Pod share the same network namespace and can communicate via localhost
- Pods are typically not created directly; instead, we use Deployments

## What are Deployments?

A **Deployment** is a higher-level concept that manages Pods. When you create a Deployment, Kubernetes automatically creates and manages Pods for you.

Benefits of using Deployments:
- **Self-healing** - If a Pod fails, the Deployment automatically creates a new one
- **Scaling** - Easily run multiple replicas of your application
- **Updates** - Roll out new versions with zero downtime
- **Rollback** - Revert to previous versions if needed

This is why we create Deployments instead of Pods directly.

## Before you begin

You will need:
- A running minikube cluster (see [Install Kubernetes](./03-Install_Kubernetes.md))
- kubectl installed and configured
- Your `hello-kubernetes` image pushed to Docker Hub (from [Host your Container](./02-Host_your_Container.md))

Verify your cluster is running:

```bash
kubectl get nodes
```

You should see your minikube node in "Ready" status.

## Deploy Your Application

Now we'll deploy the `hello-kubernetes` application you containerized and pushed to Docker Hub in the previous tutorials!

Create the deployment using your Docker Hub image:

```bash
kubectl create deployment hello-kubernetes --image=YOUR-DOCKER-ID/hello-kubernetes:v1
```

> **Important:** Replace `YOUR-DOCKER-ID` with your actual Docker Hub username. For example:
> ```bash
> kubectl create deployment hello-kubernetes --image=johndoe/hello-kubernetes:v1
> ```

This command tells Kubernetes to:
- Create a Deployment named `hello-kubernetes`
- Use your container image from Docker Hub
- Create and manage Pods running this image

You should see:

```
deployment.apps/hello-kubernetes created
```

### If You Don't Have a Docker Hub Image

If you didn't complete tutorials 01-02 or don't have your image on Docker Hub, you can use a pre-built sample image:

```bash
kubectl create deployment hello-kubernetes --image=gcr.io/google-samples/kubernetes-bootcamp:v1
```

However, we recommend completing the earlier tutorials to get the full learning experience!

## View Your Deployment

List all deployments in your cluster:

```bash
kubectl get deployments
```

You should see:

```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
hello-kubernetes   1/1     1            1           10s
```

This output shows:
- **NAME** - The name of the deployment (`hello-kubernetes`)
- **READY** - How many Pods are ready (1/1 means 1 Pod is ready out of 1 desired)
- **UP-TO-DATE** - Number of Pods updated to match the desired state
- **AVAILABLE** - Number of Pods available to users
- **AGE** - How long the deployment has been running

## View Your Pods

The Deployment automatically created a Pod. Let's view it:

```bash
kubectl get pods
```

You should see:

```
NAME                                READY   STATUS    RESTARTS   AGE
hello-kubernetes-7b5d8c9b8f-abc12   1/1     Running   0          30s
```

> **Note:** The Pod name includes a random suffix (like `-abc12`). Your Pod will have a different suffix.

The **STATUS** field shows the Pod's current state:
- **ContainerCreating** - Kubernetes is downloading the image and starting the container
- **Running** - The Pod is running successfully
- **ImagePullBackOff** - Kubernetes can't pull the image (check your Docker Hub username and that the image exists)
- **Error** or **CrashLoopBackOff** - Something went wrong with the container

If your Pod shows **ContainerCreating**, wait a few seconds and run `kubectl get pods` again.

### Troubleshooting ImagePullBackOff

If you see `ImagePullBackOff`, it means Kubernetes can't pull your image from Docker Hub. Common causes:

1. **Typo in image name** - Double-check your Docker Hub username
2. **Image doesn't exist** - Verify the image exists on Docker Hub
3. **Private repository** - If your Docker Hub repo is private, you need to configure credentials

Check the error details:

```bash
kubectl describe pod <pod-name>
```

Look for the "Events" section at the bottom for specific error messages.

## View More Details

To see detailed information about your deployment:

```bash
kubectl describe deployment hello-kubernetes
```

This shows extensive information including:
- Number of replicas
- Container image being used (`YOUR-DOCKER-ID/hello-kubernetes:v1`)
- Events (what Kubernetes has done with this deployment)

To see detailed information about your Pod:

```bash
kubectl describe pods
```

This shows:
- Pod's IP address
- Which node it's running on (should be `minikube`)
- Container information
- Events related to the Pod's lifecycle

## Understanding What Happened

When you created the Deployment, Kubernetes:

1. **Received the request** - kubectl sent your command to the Kubernetes API server
2. **Scheduled the Pod** - The scheduler found a suitable node (in our case, the only minikube node)
3. **Pulled the image** - The node downloaded your container image from Docker Hub
4. **Started the container** - The container runtime started your application
5. **Monitored the Pod** - The Deployment controller ensures the Pod keeps running

All of this happened automatically! Your application that you built and containerized is now running on Kubernetes!

## Accessing Your Application

By default, Pods are only accessible within the Kubernetes cluster network. To access your application from your browser, we need to create a **Service**, which we'll cover in the next tutorial.

However, we can test that the application is running using `kubectl proxy`:

Open a new terminal and run:

```bash
kubectl proxy
```

This creates a proxy between your local machine and the Kubernetes cluster. Keep this terminal open.

In your original terminal, get the Pod name and store it in a variable:

```bash
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Pod Name: $POD_NAME
```

Now you can access the Pod through the proxy:

```bash
curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME:8080/proxy/
```

You should see a response from **your** application:

```
Hello Kubernetes! Running on: hello-kubernetes-7b5d8c9b8f-abc12
```

This is the same application you created in tutorial 01! It's now running on Kubernetes.

Press `Ctrl+C` in the proxy terminal to stop the proxy.

## View Application Logs

To see what your application is outputting:

```bash
kubectl logs $POD_NAME
```

Or if you didn't set the POD_NAME variable:

```bash
kubectl logs deployment/hello-kubernetes
```

You should see:

```
Server listening on port 8080
```

This is the log output from your Node.js application!

## Verify It's Your Image

You can verify that Kubernetes is using your Docker Hub image:

```bash
kubectl describe deployment hello-kubernetes | grep Image
```

You should see:

```
    Image:        YOUR-DOCKER-ID/hello-kubernetes:v1
```

This confirms Kubernetes pulled and deployed your custom image!

## Clean Up (Optional)

If you want to remove the deployment:

```bash
kubectl delete deployment hello-kubernetes
```

> **Note:** Don't delete the deployment yet if you want to continue with the next tutorials, as they build upon this deployment.

## Summary

You've successfully deployed your first application to Kubernetes! You learned how to:

- Understand the difference between Pods and Deployments
- Create a Deployment using your custom container image
- View deployments and Pods
- Access your application through kubectl proxy
- View application logs
- Verify Kubernetes is using your Docker Hub image

**Congratulations!** You've completed the full journey from:
1. ✅ Creating an application
2. ✅ Containerizing it with Docker
3. ✅ Pushing it to a registry
4. ✅ Deploying it to Kubernetes

In the next tutorial, we'll learn how to expose your application to the outside world using Services!

---

**Next Lesson** - [Exposing Your Pod to the World](./05-Expose_your_Pod_to_the_World.md)
