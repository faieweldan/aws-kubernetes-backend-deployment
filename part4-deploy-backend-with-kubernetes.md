# Part 4 — Deploy Backend with Kubernetes

In this final part of the project, I deployed the backend application to the Kubernetes cluster created earlier.

By this stage, the following components were already prepared:

- An **Amazon EKS cluster**
- A **Docker container image stored in Amazon ECR**
- Kubernetes **Deployment and Service manifest files**

The goal of this step was to connect to the Kubernetes cluster and deploy the backend using `kubectl`.

This completes the full workflow of moving an application from **source code → container image → Kubernetes deployment**.

---

# Overview

The deployment workflow looks like this:

Backend Code (GitHub)  
↓  
Docker Image Built  
↓  
Image Stored in Amazon ECR  
↓  
Kubernetes Manifest Files  
↓  
Application Deployed to Amazon EKS  

Each step prepares the application for the next stage until it finally runs inside a Kubernetes cluster.

---

# Tools Used

- Amazon EKS  
- Amazon EC2  
- kubectl  
- Kubernetes YAML manifests  
- Amazon ECR  

---

# Step 1 — Install kubectl

To interact with Kubernetes resources, I installed **kubectl**, which is the command-line tool used to communicate with the Kubernetes API server.

Install kubectl:

```bash
sudo curl -o /usr/local/bin/kubectl \
https://s3.us-west-2.amazonaws.com/amazon-eks/1.31.0/2024-09-12/bin/linux/amd64/kubectl
````

Give executable permissions:

```bash
sudo chmod +x /usr/local/bin/kubectl
```

Verify installation:

```bash
kubectl version
```

kubectl allows us to:

* deploy applications
* inspect cluster resources
* monitor workloads
* troubleshoot deployments

---

# Step 2 — Connect kubectl to the EKS Cluster

Before deploying the application, kubectl needs to know which cluster to connect to.

This is done by updating the kubeconfig file:

```bash
aws eks update-kubeconfig --name nextwork-eks-cluster --region <region>
```

This command updates the Kubernetes configuration file located at:

```
~/.kube/config
```

To verify that kubectl is connected to the cluster:

```bash
kubectl get nodes
```

If the nodes appear in the output, the connection to the EKS cluster is successful.

---

# Step 3 — Deploy the Backend Application

Once kubectl is connected to the cluster, the backend application can be deployed using the manifest files created earlier.

Apply the Deployment manifest:

```bash
kubectl apply -f flask-deployment.yaml
```

Apply the Service manifest:

```bash
kubectl apply -f flask-service.yaml
```

Running `kubectl apply` tells Kubernetes to create resources based on the configuration defined in the YAML files.

These resources include:

* a **Deployment** that manages backend Pods
* a **Service** that exposes the backend for network access

---

# Step 4 — Verify the Deployment

After applying the manifests, I verified that the resources were successfully created.

Check running Pods:

```bash
kubectl get pods
```

Check Deployments:

```bash
kubectl get deployments
```

Check Services:

```bash
kubectl get services
```

If the deployment is successful, Kubernetes should show:

* multiple running Pods
* an active Deployment
* a Service exposing the application

---

# Inspecting the Deployment in Amazon EKS

To better understand how the application was deployed, I also inspected the cluster directly from the **Amazon EKS console**.

Steps taken:

1. Open the **Amazon EKS Console**
2. Select the cluster `nextwork-eks-cluster`
3. Navigate to the **Compute** tab
4. Review the **Node Groups**

The node group contains multiple **EC2 instances** that act as Kubernetes worker nodes.

These nodes are responsible for running containers managed by Kubernetes.

---

# Viewing Pods in the Cluster

Pods represent the smallest deployable unit in Kubernetes.

Each Pod contains one or more containers running the application.

List all Pods:

```bash
kubectl get pods
```

Inspect a specific Pod:

```bash
kubectl describe pod <pod-name>
```

This command displays detailed information about the Pod, including:

* container status
* resource usage
* networking information
* deployment events

---

# Understanding Pod Events

The **Events** section shows the lifecycle of the container deployment.

Typical events include:

* Pod scheduled to a node
* Container image pulled from Amazon ECR
* Container created
* Container started

These events confirm that Kubernetes successfully retrieved the container image and started the backend application inside the cluster.

---

# Understanding Replicas

The Deployment manifest specified the number of replicas:

```yaml
replicas: 3
```

This means Kubernetes maintains **three identical Pods** running the backend application.

If one Pod crashes or fails, Kubernetes automatically creates a replacement Pod to maintain the desired state.

---

# Cleaning Up Resources

Since this project was created for learning purposes, it is important to remove resources afterward to avoid unnecessary AWS charges.

Delete the EKS cluster:

```bash
eksctl delete cluster --name nextwork-eks-cluster --region <region>
```

Terminate the EC2 instance from the AWS console.

Delete the ECR repository from the ECR dashboard.

This prevents the **EKS control plane cost (~$0.10 per hour)** from continuing to run.

---

# Key Takeaways

From this step I learned:

* how to connect kubectl to an EKS cluster
* how to deploy applications using Kubernetes manifests
* how Kubernetes manages Pods and replicas automatically
* how Services route traffic to backend containers
* how to inspect Kubernetes workloads and deployment events
