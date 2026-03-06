# Part 2 — Prepare Backend for Kubernetes Deployment

In this part of the project, I prepared the backend application so it could later be deployed to Kubernetes.

The goal of this step was to take the backend source code, package it into a **Docker container image**, and store that image in **Amazon ECR (Elastic Container Registry)** so Kubernetes can pull it later when deploying the application.

---

## Overview

Kubernetes does not deploy application source code directly.

Instead, applications are packaged into **container images**, which contain:

* application code
* dependencies
* runtime configuration

These images are stored in a **container registry**, and Kubernetes pulls them when creating containers.

In this part of the project, the workflow looked like this:

```
GitHub backend code
        ↓
Clone to EC2
        ↓
Build Docker Image
        ↓
Push Image to Amazon ECR
```

---

## Tools Used

* Amazon EC2
* Git & GitHub
* Docker
* Amazon ECR
* AWS CLI

---

## Step 1 — Clone Backend Repository

The backend application was stored in a GitHub repository prepared by a team member.

To retrieve the code, I first installed Git on the EC2 instance.

Install Git:

```bash
sudo dnf update
sudo dnf install git -y
```

Verify installation:

```bash
git --version
```

Then I cloned the backend repository:

```bash
git clone <repository-url>
```

After cloning, the backend project files were available inside the EC2 instance.

The repository contained several files including:

* `app.py`
* `requirements.txt`
* `Dockerfile`

---

## Step 2 — Build a Docker Image

Once the backend code was available locally, the next step was to package it into a **Docker image**.

Install Docker:

```bash
sudo yum install -y docker
```

Start Docker:

```bash
sudo service docker start
```

Add the EC2 user to the Docker group so Docker commands can run without `sudo`:

```bash
sudo usermod -a -G docker ec2-user
```

After reconnecting to the EC2 instance, I built the Docker image.

Build command:

```bash
docker build -t nextwork-flask-backend .
```

### What this does

The command reads the **Dockerfile** inside the project directory and builds a container image that contains:

* the backend code
* Python dependencies
* runtime environment

The result is a reusable **container image blueprint**.

---

## Understanding Docker Images vs Containers

One concept that became clearer during this step was the difference between **images** and **containers**.

**Docker Image**

A static blueprint that contains everything required to run the application.

**Container**

A running instance created from that image.

Example:

```
Docker Image → blueprint
Container → running application
```

Multiple containers can be created from the same image, which ensures the application behaves consistently across environments.

---

## Step 3 — Create an Amazon ECR Repository

Once the Docker image was built, it needed to be stored somewhere accessible to Kubernetes.

For this project, I used **Amazon ECR (Elastic Container Registry)**.

Create repository:

```bash
aws ecr create-repository \
  --repository-name nextwork-flask-backend \
  --image-scanning-configuration scanOnPush=true
```

### Why ECR is needed

Kubernetes clusters cannot access images stored only on a local machine.

Instead, container images must be stored in a **registry** where Kubernetes can pull them.

Amazon ECR acts as a central repository for container images in AWS.

---

## Step 4 — Push Docker Image to ECR

After creating the repository, I tagged and pushed the Docker image to ECR.

The push commands were generated from the ECR console.

Typical steps include:

Authenticate Docker to ECR

```bash
aws ecr get-login-password --region <region> \
| docker login \
--username AWS \
--password-stdin <account-id>.dkr.ecr.<region>.amazonaws.com
```

Tag the image:

```bash
docker tag nextwork-flask-backend:latest <ecr-repository-uri>
```

Push the image:

```bash
docker push <ecr-repository-uri>
```

Once pushed, the container image becomes available in the ECR repository.

This image can now be pulled by the Kubernetes cluster during deployment.

---

## What I Learned

This part helped me understand the workflow between application code, Docker, and Kubernetes.

Key ideas:

* Kubernetes does not deploy raw application code.
* Applications must first be packaged into container images.
* Docker images act as reusable blueprints for running containers.
* Container registries like Amazon ECR store these images.
* Kubernetes pulls images from registries when starting containers.

This workflow ensures that every container runs the **exact same application environment**, preventing issues like "it works on my machine".

---

## Key Takeaways

From this step I learned:

* How to retrieve backend code using Git
* How Docker builds container images using a Dockerfile
* The difference between Docker images and containers
* Why container registries are required for Kubernetes deployments
* How Amazon ECR stores and manages container images
