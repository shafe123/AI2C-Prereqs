# Containerizing Your Application

## Objectives

- Learn what containerization is and why it's important
- Create a simple Node.js application
- Write a Dockerfile to containerize the application
- Build a Docker container image

## What is Containerization?

Containerization is the process of packaging an application and its dependencies into a standardized unit called a container. Containers are lightweight, portable, and ensure that your application runs consistently across different computing environments.

Before deploying applications to Kubernetes, they must first be containerized. This involves creating a **Docker image** that includes your application code, runtime, system tools, libraries, and settings.

## Before you begin

You will need to have Docker installed on your system. Docker is the most popular containerization platform and will allow you to build and run containers locally.

- [Install Docker Desktop](https://docs.docker.com/get-docker/) - Available for Windows, macOS, and Linux

After installation, verify Docker is working by running:

```bash
docker --version
```

## Create a Simple Application

For this tutorial, we'll create a simple Node.js web application. First, create a new directory for your project:

```bash
mkdir hello-kubernetes-app
cd hello-kubernetes-app
```

Create a file named `server.js` with the following content:

```javascript
var http = require('http');
var os = require('os');

var handleRequest = function(request, response) {
  console.log('Received request for URL: ' + request.url);
  response.writeHead(200);
  response.end('Hello Kubernetes! Running on: ' + os.hostname() + '\n');
};

var www = http.createServer(handleRequest);
www.listen(8080, function() {
  console.log('Server listening on port 8080');
});
```

This simple application creates a web server that listens on port 8080 and responds with a greeting message that includes the hostname.

## Create a Dockerfile

A **Dockerfile** is a text file that contains instructions for building a Docker image. Create a file named `Dockerfile` (no extension) in your project directory with the following content:

```dockerfile
# Use an official Node.js runtime as the base image
FROM node:14-alpine

# Set the working directory in the container
WORKDIR /app

# Copy the application file into the container
COPY server.js .

# Expose port 8080 to the outside world
EXPOSE 8080

# Command to run the application
CMD ["node", "server.js"]
```

Let's break down what each instruction does:

- **FROM** - Specifies the base image (Node.js version 14 on Alpine Linux)
- **WORKDIR** - Sets the working directory inside the container
- **COPY** - Copies files from your local system into the container
- **EXPOSE** - Documents which port the container will listen on
- **CMD** - Specifies the command to run when the container starts

## Build the Docker Image

Now that we have our application and Dockerfile, we can build a Docker image. Run the following command in your project directory:

```bash
docker build -t hello-kubernetes:v1 .
```

The `-t` flag tags the image with a name and version. The `.` at the end specifies the build context (current directory).

You should see output showing each step of the build process:

```
[+] Building 2.3s (8/8) FINISHED
 => [1/3] FROM docker.io/library/node:14-alpine
 => [2/3] WORKDIR /app
 => [3/3] COPY server.js .
 => exporting to image
 => => naming to docker.io/library/hello-kubernetes:v1
```

Verify that your image was created successfully:

```bash
docker images
```

You should see your `hello-kubernetes` image in the list:

```
REPOSITORY          TAG       IMAGE ID       CREATED          SIZE
hello-kubernetes    v1        abc123def456   10 seconds ago   116MB
```

## Test Your Container Locally

Before deploying to Kubernetes, it's good practice to test your container locally. Run the container:

```bash
docker run -p 8080:8080 hello-kubernetes:v1
```

The `-p 8080:8080` flag maps port 8080 on your local machine to port 8080 in the container.

You should see:

```
Server listening on port 8080
```

Open a new terminal and test the application:

```bash
curl http://localhost:8080
```

You should receive a response:

```
Hello Kubernetes! Running on: 8a7b9c0d1e2f
```

To stop the container, press `Ctrl+C` in the terminal where it's running.

## Understanding Container Images

A Docker image is a lightweight, standalone package that includes everything needed to run your application:

- **Code** - Your application source code
- **Runtime** - Node.js, Python, Java, etc.
- **System Libraries** - Dependencies required by your application
- **Settings** - Configuration and environment variables

Images are built in layers, making them efficient to store and transfer. Each instruction in your Dockerfile creates a new layer.

## Best Practices

When containerizing applications, follow these best practices:

1. **Use official base images** - Start with trusted images from Docker Hub
2. **Keep images small** - Use Alpine Linux variants when possible
3. **One process per container** - Each container should have a single responsibility
4. **Don't store data in containers** - Use volumes for persistent data
5. **Use specific tags** - Avoid using `latest` tag in production

## Clean Up

To remove the running container (if still running):

```bash
docker ps  # Find the container ID
docker stop <container-id>
```

> **Note:** We'll keep the Docker image as we'll need it for the next tutorial where we push it to a container registry.

## Summary

You've successfully containerized your first application! You learned how to:

- Create a simple Node.js application
- Write a Dockerfile to define how to build a container image
- Build a Docker image from the Dockerfile
- Run and test the container locally

In the next tutorial, we'll learn how to host this container image in a registry so it can be accessed by Kubernetes.

---

**Next Lesson** - [Hosting Your Container in a Registry](./02-Host_your_Container.md)
