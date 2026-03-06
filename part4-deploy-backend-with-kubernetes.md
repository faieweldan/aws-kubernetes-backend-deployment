# Part 4 — Deploy Backend with Kubernetes

In this final part of the project, I deployed the backend application to the Kubernetes cluster created earlier.

The goal of this step was to take the **container image stored in Amazon ECR** and deploy it into the **Amazon EKS cluster** using the Kubernetes manifest files created in the previous step.

This part completes the workflow of moving an application from source code into a running Kubernetes deployment.

---

## Overview

At this stage of the project, the required components were already prepared:

* Kubernetes cluster running on **Amazon EKS**
* Backend **Docker image stored in Amazon ECR**
* Kubernetes **Deployment and Service manifests**

The remaining step was to connect to the cluster and instruct Kubernetes to create the application resources.

The deployment workflow looks like this:

```id="workflow-diagram"
GitHub Backend Code
        ↓
Docker Image Built
        ↓
Image Stored in Amazon ECR
        ↓
Kubernetes Manifest Files
        ↓
kubectl Deploys Application to EKS
```

---

## Tools Used

* Amazon EKS
* Amazon EC2
* kubectl
* Kubernetes YAML manifests
* Amazon ECR

---

## Step 1 — Install kubectl

To interact with the Kubernetes cluster, I installed **kubectl**, which is the command line interface used to communicate with the Kubernetes API server.

Install kubectl:

```bash id="install-kubectl"
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

Make the binary executable:

```bash id="chmod-kubectl"
chmod +x kubectl
```

Move it into the system path:

```bash id="move-kubectl"
sudo mv kubectl /usr/local/bin/
```

Verify installation:

```bash id="verify-kubectl"
kubectl version --client
```

---

## Step 2 — Connect kubectl to the EKS Cluster

Next, I configured kubectl so it could communicate with the EKS cluster created earlier.

This updates the Kubernetes configuration file and allows kubectl to authenticate with the cluster.

```bash id="update-kubeconfig"
aws eks update-kubeconfig --region <region> --name nextwork-eks-cluster
```

Once connected, I verified that the cluster nodes were visible:

```bash id="get-nodes"
kubectl get nodes
```

If the nodes appear, it confirms that kubectl is successfully communicating with the EKS cluster.

---

## Step 3 — Deploy the Backend Application

With kubectl connected to the cluster, I deployed the backend using the manifest files created in Part 3.

Apply the Deployment manifest:

```bash id="apply-deployment"
kubectl apply -f flask-deployment.yaml
```

Apply the Service manifest:

```bash id="apply-service"
kubectl apply -f flask-service.yaml
```

These commands instruct Kubernetes to create the required resources inside the cluster.

---

## Step 4 — Verify the Deployment

After applying the manifests, I checked that the Kubernetes resources were successfully created.

Check running Pods:

```bash id="get-pods"
kubectl get pods
```

Check Deployments:

```bash id="get-deployments"
kubectl get deployments
```

Check Services:

```bash id="get-services"
kubectl get services
```

If everything is working correctly, Kubernetes should show:

* multiple running Pods
* an active Deployment
* a Service exposing the application

---

## Understanding What Kubernetes Does

Once the manifests are applied, Kubernetes begins working to match the **desired state** defined in the YAML files.

For example:

* The Deployment ensures **three replicas** of the backend are running
* If a Pod fails, Kubernetes automatically creates a replacement
* The Service routes traffic to the correct Pods

This automated management is one of the main advantages of Kubernetes.

---

## Observing the Deployment

Kubernetes provides commands that allow developers to inspect and monitor running applications.

Examples include:

Check detailed Pod information:

```bash id="describe-pod"
kubectl describe pod <pod-name>
```

View application logs:

```bash id="pod-logs"
kubectl logs <pod-name>
```

These commands help troubleshoot issues and observe application behavior inside the cluster.

---

## Cleaning Up Resources

Since this project was created for learning purposes, it is important to remove the resources afterward to avoid unnecessary AWS charges.

Delete the EKS cluster:

```bash id="delete-cluster"
eksctl delete cluster --name nextwork-eks-cluster
```

Terminate the EC2 instance from the AWS console.

Delete the ECR repository from the ECR dashboard.

Cleaning up resources ensures that the **EKS hourly control plane cost (~$0.10 per hour)** does not continue accumulating.

---

## What I Learned

This final part tied together the entire Kubernetes workflow.

Important ideas that became clearer during this step:

* kubectl communicates with the Kubernetes API server
* manifest files define the desired application state
* Kubernetes automatically manages container replicas
* Services provide stable networking access to Pods
* container images stored in ECR allow Kubernetes to launch containers consistently

---

## Key Takeaways

From this step I learned:

* How to connect kubectl to an EKS cluster
* How to deploy applications using Kubernetes manifests
* How Kubernetes manages Pods and replicas automatically
* How Services route traffic to backend containers
* How to inspect and troubleshoot Kubernetes workloads

This step completed the process of deploying a containerized backend application onto a Kubernetes cluster running on AWS.
