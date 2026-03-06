# Kubernetes on AWS Hands-on Project

This repository documents my hands-on learning journey with **Kubernetes on AWS** through a 4-part project series.

The overall goal of this project was to understand how a backend application moves from source code into a running deployment on a Kubernetes cluster. Instead of only learning the theory, I went through the setup process step by step using AWS services and Kubernetes tools.

The project starts from creating the Kubernetes cluster, then preparing the backend application, creating Kubernetes manifest files, and finally deploying the backend to the cluster.

## Project Structure

This project is divided into 4 parts:

### Part 1 – Launch a Kubernetes Cluster
In this part, I launched an EC2 instance, installed `eksctl`, created an Amazon EKS cluster, and learned how AWS helps provision the infrastructure needed for Kubernetes.

### Part 2 – Set Up Kubernetes Deployment
In this part, I cloned the backend application from GitHub, built a Docker image, created an Amazon ECR repository, and pushed the backend image so it could later be used by Kubernetes.

### Part 3 – Create Kubernetes Manifests
In this part, I created the Kubernetes manifest files needed to define how the backend should run inside the cluster. This included a Deployment manifest and a Service manifest.

### Part 4 – Deploy Backend with Kubernetes
In this part, I installed `kubectl`, connected to the EKS cluster, applied the manifest files, and deployed the backend application to Kubernetes.

## What I Practiced

Through this project, I got hands-on practice with:

- Amazon EKS
- Amazon EC2
- Amazon ECR
- IAM roles
- CloudFormation
- Docker
- Git and GitHub
- kubectl
- eksctl
- Kubernetes Deployments and Services
- Writing and applying YAML manifests

## Key Things I Learned

Some of the main things I learned from this project:

- How Amazon EKS simplifies Kubernetes cluster setup on AWS
- How backend code can be packaged into a Docker image
- Why container registries like Amazon ECR are needed in a Kubernetes workflow
- How Kubernetes uses manifest files to define the desired state of an application
- The difference between Deployments and Services
- How Kubernetes manages multiple identical application instances using replicas
- How to deploy and inspect workloads using `kubectl`

## Notes

This repository is organized as project documentation.  
Each part has its own markdown file with the main steps, tools used, and what I learned from that stage.
