# Hosting Your Container in a Registry

## Objectives

- Learn what a container registry is and why it's needed
- Create a Docker Hub account
- Tag your container image appropriately
- Push your container image to Docker Hub
- Verify your image is accessible

## What is a Container Registry?

A **container registry** is a repository for storing and distributing container images. It's similar to how GitHub stores code, but specifically designed for container images.

Kubernetes needs to pull container images from a registry when deploying applications. While you can use a local registry, public registries like Docker Hub make it easy to share images and deploy them to Kubernetes clusters anywhere.

Popular container registries include:

- **Docker Hub** - The default public registry (we'll use this)
- **Google Container Registry (GCR)** - Google's registry service
- **Amazon Elastic Container Registry (ECR)** - AWS's registry service
- **GitHub Container Registry (GHCR)** - GitHub's registry service
- **Azure Container Registry (ACR)** - Microsoft's registry service

## Before you begin

You will need:

- A Docker Hub account (free)
- Docker installed and running on your system
- The `hello-kubernetes:v1` image from the previous tutorial

If you haven't completed the previous tutorial, go back and complete [Containerizing Your Application](./Containerizing_an_application.md) first.

## Create a Docker Hub Account

If you don't already have a Docker Hub account, create one:

1. Visit [https://hub.docker.com/signup](https://hub.docker.com/signup)
2. Fill in your details (Docker ID, email, password)
3. Verify your email address
4. Remember your Docker ID - you'll need it for pushing images

## Log in to Docker Hub

Before you can push images, you need to authenticate with Docker Hub. Run the following command:

```bash
docker login
```

Enter your Docker ID and password when prompted:

```
Username: your-docker-id
Password:
Login Succeeded
```

> **Note:** For security, consider using an access token instead of your password. You can create one in Docker Hub under Account Settings > Security > Access Tokens.

## Tag Your Image

Container images need to be tagged with the registry location before they can be pushed. The format is:

```
registry-url/username/image-name:tag
```

For Docker Hub, the registry URL is implicit, so you only need:

```
username/image-name:tag
```

Tag your existing `hello-kubernetes:v1` image with your Docker Hub username:

```bash
docker tag hello-kubernetes:v1 YOUR-DOCKER-ID/hello-kubernetes:v1
```

Replace `YOUR-DOCKER-ID` with your actual Docker Hub username. For example:

```bash
docker tag hello-kubernetes:v1 johndoe/hello-kubernetes:v1
```

Verify the tag was created:

```bash
docker images
```

You should see both the original and tagged images:

```
REPOSITORY                      TAG       IMAGE ID       CREATED         SIZE
hello-kubernetes                v1        abc123def456   5 minutes ago   116MB
johndoe/hello-kubernetes        v1        abc123def456   5 minutes ago   116MB
```

Notice they have the same Image ID because they're the same image with different names.

## Push Your Image to Docker Hub

Now push the tagged image to Docker Hub:

```bash
docker push YOUR-DOCKER-ID/hello-kubernetes:v1
```

You'll see the upload progress:

```
The push refers to repository [docker.io/johndoe/hello-kubernetes]
5f70bf18a086: Pushed
54f7e8ac64a0: Pushed
4f3256bdf66b: Pushed
v1: digest: sha256:abc123... size: 1234
```

This may take a minute or two depending on your internet connection speed.

## Verify Your Image on Docker Hub

Once the push completes, verify your image is available:

1. Visit [https://hub.docker.com](https://hub.docker.com)
2. Log in with your credentials
3. Navigate to your repositories
4. You should see `hello-kubernetes` listed

Click on the repository to see details like:
- Available tags (you should see `v1`)
- Image size
- Last updated timestamp
- Pull command

## Pull and Test Your Image

To verify everything works, you can pull your image from Docker Hub on any machine with Docker installed:

```bash
docker pull YOUR-DOCKER-ID/hello-kubernetes:v1
```

Run the image to test it:

```bash
docker run -p 8080:8080 YOUR-DOCKER-ID/hello-kubernetes:v1
```

In a new terminal, test it:

```bash
curl http://localhost:8080
```

You should receive the familiar response:

```
Hello Kubernetes! Running on: 8a7b9c0d1e2f
```

Stop the container with `Ctrl+C`.

## Understanding Image Tags and Versions

Tags are crucial for managing different versions of your container images:

- **v1, v2, v3** - Semantic versioning for releases
- **latest** - Points to the most recent build (use cautiously in production)
- **stable** - A known good version
- **dev** - Development version

It's best practice to:
1. Always use specific version tags in production
2. Avoid relying on the `latest` tag
3. Update tags when you make changes to your application

To push multiple tags, repeat the tag and push process:

```bash
docker tag hello-kubernetes:v1 YOUR-DOCKER-ID/hello-kubernetes:latest
docker push YOUR-DOCKER-ID/hello-kubernetes:latest
```

## Using Private Registries

While this tutorial uses Docker Hub's public repository, you can also create private repositories:

- **Docker Hub Free** - 1 private repository
- **Docker Hub Pro** - Unlimited private repositories
- **Self-hosted** - Run your own registry using Docker Registry

For private images, you'll need to configure Kubernetes with credentials to pull the images (covered in later tutorials).

## Clean Up (Optional)

To free up disk space, you can remove the local images:

```bash
docker rmi hello-kubernetes:v1
docker rmi YOUR-DOCKER-ID/hello-kubernetes:v1
```

> **Note:** You can always pull the image back from Docker Hub when needed.

## Alternative: Using Pre-built Images

For this tutorial series, if you prefer not to push your own image, you can use pre-existing images from Docker Hub such as:

- `gcr.io/google-samples/kubernetes-bootcamp:v1`
- `registry.k8s.io/e2e-test-images/agnhost:2.53`

These are maintained by the Kubernetes community and are perfect for learning purposes.

## Summary

You've successfully hosted your container image in a registry! You learned how to:

- Create a Docker Hub account and authenticate
- Tag container images with the proper naming convention
- Push images to Docker Hub
- Verify images are accessible from the registry
- Pull and run images from the registry

Your containerized application is now ready to be deployed to Kubernetes! In the next tutorial, we'll install Kubernetes and prepare our environment for deployment.

---

**Next Lesson** - [Setting Up Kubernetes with Minikube](./03-Install_Kubernetes.md)
