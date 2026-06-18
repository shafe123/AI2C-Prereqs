# Exploring and Troubleshooting Your Application

> **Note:** This is an optional tutorial that builds upon the core roadmap. It covers advanced troubleshooting techniques.

## Objectives

- Learn about Pods and Nodes in detail
- Use kubectl to troubleshoot applications
- Execute commands inside containers
- View application logs

## Understanding Pods in Detail

A **Pod** is a group of one or more application containers that share:
- **Storage** - Shared volumes
- **Networking** - A unique cluster IP address
- **Configuration** - Information about how to run each container

Pods are the atomic unit in Kubernetes. When we create a Deployment, that Deployment creates Pods with containers inside them.

## Understanding Nodes in Detail

A **Node** is a worker machine in Kubernetes (virtual or physical). Each Node is managed by the Control Plane and contains:
- **kubelet** - Agent that communicates with the Control Plane
- **Container runtime** - Software that runs containers (like Docker)
- **kube-proxy** - Maintains network rules for Pod communication

## Before you begin

You will need the `hello-kubernetes` deployment running from the previous tutorials.

Verify it's running:

```bash
kubectl get deployments
```

## Common kubectl Commands for Troubleshooting

The most useful kubectl commands for troubleshooting:

- **kubectl get** - List resources
- **kubectl describe** - Show detailed information about a resource
- **kubectl logs** - Print logs from a container
- **kubectl exec** - Execute a command inside a container

## Inspecting Your Application

### 1. List Pods

```bash
kubectl get pods
```

Output:
```
NAME                                   READY   STATUS    RESTARTS   AGE
hello-kubernetes-7b5d8c9b8f-abc12   1/1     Running   0          5m
```

Key fields:
- **READY** - Shows running/total containers (1/1 means 1 container running out of 1 total)
- **STATUS** - Current state (Running, Pending, Error, CrashLoopBackOff, etc.)
- **RESTARTS** - How many times the container has restarted (high numbers indicate problems)

### 2. Describe a Pod

```bash
kubectl describe pods
```

This shows extensive information:
- **IP Address** - The Pod's IP within the cluster
- **Node** - Which Node the Pod is running on
- **Containers** - Container details (image, ports, state)
- **Events** - Recent events in the Pod's lifecycle

Common events to look for:
- `Pulling` - Downloading the container image
- `Pulled` - Successfully downloaded the image
- `Created` - Container was created
- `Started` - Container started successfully
- `Failed` - Something went wrong (check error message)

### 3. View Logs

To see what your application is outputting:

```bash
kubectl logs deployment/hello-kubernetes
```

Or for a specific Pod:

```bash
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
kubectl logs $POD_NAME
```

To follow logs in real-time (like `tail -f`):

```bash
kubectl logs -f $POD_NAME
```

Press `Ctrl+C` to stop following logs.

## Accessing the Pod

### Using kubectl proxy

Create a proxy to access your Pod:

```bash
kubectl proxy
```

Keep this running in a separate terminal.

In another terminal, access the Pod:

```bash
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME:8080/proxy/
```

## Executing Commands Inside a Container

One of the most powerful debugging tools is `kubectl exec`, which lets you run commands inside a container.

### View environment variables

```bash
kubectl exec $POD_NAME -- env
```

This shows all environment variables inside the container, including:
- Kubernetes-injected variables
- Service discovery information
- Custom environment variables

### Start an interactive shell

```bash
kubectl exec -it $POD_NAME -- bash
```

The `-it` flags mean:
- `-i` - Interactive (keep STDIN open)
- `-t` - Allocate a pseudo-TTY (terminal)

Now you're inside the container! You can:

```bash
# View the application code
cat server.js

# Test the application locally
curl localhost:8080

# Check running processes
ps aux

# View network configuration
ifconfig

# Exit the container
exit
```

> **Note:** The bash session is inside the container. When you exit, the container continues running.

## Inspecting Nodes

View all nodes in your cluster:

```bash
kubectl get nodes
```

For detailed information:

```bash
kubectl describe nodes
```

This shows:
- **Capacity** - Total CPU and memory on the Node
- **Allocatable** - Resources available for Pods
- **Pods** - Which Pods are running on this Node
- **Events** - Recent Node events

## Common Troubleshooting Scenarios

### Pod stuck in "Pending" state

Check events:
```bash
kubectl describe pod $POD_NAME
```

Common causes:
- Insufficient resources (CPU/memory)
- Node selector doesn't match any nodes
- Image pull issues

### Pod in "CrashLoopBackOff" state

This means the container keeps crashing. Check logs:
```bash
kubectl logs $POD_NAME
kubectl logs $POD_NAME --previous  # Logs from previous crash
```

Common causes:
- Application error at startup
- Missing environment variables
- Port already in use

### Pod in "ImagePullBackOff" state

Kubernetes can't pull the container image. Check events:
```bash
kubectl describe pod $POD_NAME
```

Common causes:
- Image doesn't exist
- Typo in image name
- Authentication required (for private registries)

### Application not responding

1. Check Pod is running:
   ```bash
   kubectl get pods
   ```

2. Check logs for errors:
   ```bash
   kubectl logs $POD_NAME
   ```

3. Test inside the container:
   ```bash
   kubectl exec -it $POD_NAME -- curl localhost:8080
   ```

4. Check Service endpoints:
   ```bash
   kubectl describe service hello-kubernetes
   ```

## Advanced Debugging

### View resource usage

```bash
kubectl top nodes  # Node CPU/memory usage
kubectl top pods   # Pod CPU/memory usage
```

> **Note:** This requires the metrics-server addon. Enable it with:
> ```bash
> minikube addons enable metrics-server
> ```

### View cluster events

```bash
kubectl get events --sort-by='.lastTimestamp'
```

This shows all recent events across the cluster, sorted by time.

## Summary

You've learned advanced troubleshooting techniques! You can now:

- Use kubectl commands to inspect resources
- View detailed information with `kubectl describe`
- Check application logs with `kubectl logs`
- Execute commands inside containers with `kubectl exec`
- Debug common Pod issues
- Understand Node resources and capacity

These skills are essential for running applications in production Kubernetes environments.

---

**Next Lesson (Optional)** - [Scaling Your Application](./07-Scale_your_Application.md)
