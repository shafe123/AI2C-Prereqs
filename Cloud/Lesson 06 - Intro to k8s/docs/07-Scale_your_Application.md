# Scaling Your Application

> **Note:** This is an optional tutorial that builds upon the core roadmap. It covers scaling applications to handle increased load.

## Objectives

- Understand horizontal scaling in Kubernetes
- Scale a deployment to multiple replicas
- Observe load balancing across multiple Pods

## What is Scaling?

**Scaling** means adjusting the number of running instances (Pods) of your application. Kubernetes makes this easy by allowing you to change the number of replicas in a Deployment.

Types of scaling:
- **Horizontal scaling** - Adding more Pods (covered in this tutorial)
- **Vertical scaling** - Giving more resources (CPU/memory) to existing Pods
- **Autoscaling** - Automatically adjusting based on load (advanced topic)

## Why Scale?

Reasons to scale your application:
- **Handle more traffic** - Distribute load across multiple Pods
- **High availability** - If one Pod fails, others keep serving traffic
- **Rolling updates** - Update Pods one at a time without downtime
- **Performance** - Parallel processing across multiple instances

## Before you begin

You will need:
- The `hello-kubernetes` deployment and service from previous tutorials

Verify they're running:

```bash
kubectl get deployments
kubectl get services
```

If not, create them using your Docker Hub image:

```bash
kubectl create deployment hello-kubernetes --image=YOUR-DOCKER-ID/hello-kubernetes:v1
kubectl expose deployment hello-kubernetes --type=NodePort --port=8080
```

> **Remember:** Replace `YOUR-DOCKER-ID` with your actual Docker Hub username.

## Check Current Scale

View your current deployment:

```bash
kubectl get deployments
```

Output:
```
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
hello-kubernetes   1/1     1            1           5m
```

Key fields:
- **READY** - Shows current/desired replicas (1/1)
- **UP-TO-DATE** - Number of replicas with the latest configuration
- **AVAILABLE** - Number of replicas available to users

Currently, we have 1 replica (1 Pod).

View the ReplicaSet created by the Deployment:

```bash
kubectl get rs
```

Output:
```
NAME                             DESIRED   CURRENT   READY   AGE
hello-kubernetes-7b5d8c9b8f   1         1         1       5m
```

A **ReplicaSet** ensures the specified number of Pod replicas are running at all times.

## Scale Up to 4 Replicas

Scale the deployment to 4 replicas:

```bash
kubectl scale deployment hello-kubernetes --replicas=4
```

Output:
```
deployment.apps/hello-kubernetes scaled
```

Check the deployment again:

```bash
kubectl get deployments
```

Output:
```
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
hello-kubernetes   4/4     4            4           6m
```

Now we have 4/4 replicas!

## View All Pods

List all Pods:

```bash
kubectl get pods -o wide
```

Output:
```
NAME                                   READY   STATUS    RESTARTS   AGE   IP           NODE
hello-kubernetes-7b5d8c9b8f-abc12   1/1     Running   0          6m    10.244.0.5   minikube
hello-kubernetes-7b5d8c9b8f-def34   1/1     Running   0          20s   10.244.0.6   minikube
hello-kubernetes-7b5d8c9b8f-ghi56   1/1     Running   0          20s   10.244.0.7   minikube
hello-kubernetes-7b5d8c9b8f-jkl78   1/1     Running   0          20s   10.244.0.8   minikube
```

You now have 4 Pods, each with:
- Different names (with random suffixes)
- Different IP addresses
- All running on the same Node (minikube only has 1 Node)

## View Scaling Events

See what happened when you scaled:

```bash
kubectl describe deployment hello-kubernetes
```

Look for events like:
```
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  30s   deployment-controller  Scaled up replica set hello-kubernetes-7b5d8c9b8f to 4
```

## Test Load Balancing

The Service automatically load balances traffic across all 4 Pods.

Make multiple requests to see different Pods responding:

```bash
for i in {1..10}; do curl $(minikube service hello-kubernetes --url); done
```

Output:
```
Hello Kubernetes bootcamp! | Running on: hello-kubernetes-7b5d8c9b8f-abc12 | v=1
Hello Kubernetes bootcamp! | Running on: hello-kubernetes-7b5d8c9b8f-def34 | v=1
Hello Kubernetes bootcamp! | Running on: hello-kubernetes-7b5d8c9b8f-ghi56 | v=1
Hello Kubernetes bootcamp! | Running on: hello-kubernetes-7b5d8c9b8f-jkl78 | v=1
Hello Kubernetes bootcamp! | Running on: hello-kubernetes-7b5d8c9b8f-abc12 | v=1
...
```

Notice the Pod names change! The Service is distributing requests across all Pods.

## View Service Endpoints

See which Pods the Service is routing to:

```bash
kubectl describe service hello-kubernetes
```

Look for the Endpoints field:
```
Endpoints: 10.244.0.5:8080,10.244.0.6:8080,10.244.0.7:8080,10.244.0.8:8080
```

This shows all 4 Pod IP addresses that the Service routes to.

## Scale Down to 2 Replicas

You can also scale down:

```bash
kubectl scale deployment hello-kubernetes --replicas=2
```

Check the Pods:

```bash
kubectl get pods
```

You should see only 2 Pods in "Running" state. The other 2 will be terminating:

```
NAME                                   READY   STATUS        RESTARTS   AGE
hello-kubernetes-7b5d8c9b8f-abc12   1/1     Running       0          8m
hello-kubernetes-7b5d8c9b8f-def34   1/1     Running       0          2m
hello-kubernetes-7b5d8c9b8f-ghi56   1/1     Terminating   0          2m
hello-kubernetes-7b5d8c9b8f-jkl78   1/1     Terminating   0          2m
```

After a few seconds, only 2 Pods remain:

```bash
kubectl get pods
```

```
NAME                                   READY   STATUS    RESTARTS   AGE
hello-kubernetes-7b5d8c9b8f-abc12   1/1     Running   0          8m
hello-kubernetes-7b5d8c9b8f-def34   1/1     Running   0          2m
```

## Understanding Self-Healing

What happens if a Pod crashes? Kubernetes automatically recreates it!

Try deleting a Pod:

```bash
# Get the first Pod name
export POD_NAME=$(kubectl get pods -o go-template --template '{{(index .items 0).metadata.name}}')
echo "Deleting pod: $POD_NAME"

# Delete it
kubectl delete pod $POD_NAME
```

Immediately check the Pods:

```bash
kubectl get pods
```

You'll see the old Pod terminating and a new Pod being created:

```
NAME                                   READY   STATUS              RESTARTS   AGE
hello-kubernetes-7b5d8c9b8f-abc12   1/1     Terminating         0          9m
hello-kubernetes-7b5d8c9b8f-def34   1/1     Running             0          3m
hello-kubernetes-7b5d8c9b8f-xyz99   0/1     ContainerCreating   0          2s
```

After a few seconds, you'll have 2 healthy Pods again, but with a new one replacing the deleted one.

This is **self-healing** in action!

## Alternative: Using kubectl edit

You can also change the replica count by editing the deployment directly:

```bash
kubectl edit deployment hello-kubernetes
```

This opens the deployment configuration in an editor. Find the line:
```yaml
spec:
  replicas: 2
```

Change it to your desired number, save, and exit. Kubernetes will automatically apply the change.

## Autoscaling (Preview)

Kubernetes can automatically scale based on CPU usage:

```bash
kubectl autoscale deployment hello-kubernetes --min=2 --max=10 --cpu-percent=80
```

This creates a **HorizontalPodAutoscaler** that:
- Keeps at least 2 replicas running
- Scales up to 10 replicas if needed
- Scales when CPU usage exceeds 80%

> **Note:** Autoscaling requires the metrics-server addon and CPU resource requests to be set. This is an advanced topic beyond this tutorial.

View autoscalers:
```bash
kubectl get hpa
```

Delete the autoscaler:
```bash
kubectl delete hpa hello-kubernetes
```

## Best Practices

When scaling applications:

1. **Set resource requests and limits** - Help Kubernetes schedule Pods efficiently
2. **Use multiple replicas in production** - For high availability
3. **Test your application can scale** - Not all apps are designed for horizontal scaling
4. **Monitor resource usage** - Know when you need to scale
5. **Use autoscaling for variable load** - Automatically handle traffic spikes

## Clean Up

Scale back to 1 replica:

```bash
kubectl scale deployment hello-kubernetes --replicas=1
```

Or delete everything:

```bash
kubectl delete deployment hello-kubernetes
kubectl delete service hello-kubernetes
```

## Summary

You've learned how to scale applications in Kubernetes! You can now:

- Scale deployments up and down with `kubectl scale`
- Understand ReplicaSets and how they manage Pods
- Observe load balancing across multiple Pods
- Understand self-healing when Pods fail
- Know the basics of horizontal pod autoscaling

Scaling is a fundamental capability that makes Kubernetes powerful for production applications. With these skills, you can ensure your applications handle varying loads and remain highly available.
