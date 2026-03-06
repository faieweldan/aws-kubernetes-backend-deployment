# Part 3 — Create Kubernetes Manifests

In this part of the project, I created the **Kubernetes manifest files** that define how the backend application should run inside the cluster.

The goal of this step was to move from simply storing the container image to **telling Kubernetes how to deploy and manage that application**.

---

## Overview

Kubernetes uses **manifest files written in YAML** to define the desired state of an application.

These manifests act as instructions that tell Kubernetes:

* what container image to run
* how many instances of the application should exist
* how the application should be exposed to traffic

In this project, two manifest files were created:

* **Deployment manifest** — defines how the application should run and scale
* **Service manifest** — defines how the application can be accessed over the network

---

## Tools Used

* Amazon EKS
* Kubernetes
* kubectl (later used to apply manifests)
* YAML manifest files

---

## Step 1 — Create a Directory for Manifests

To keep the project organized, I created a directory specifically for Kubernetes manifest files.

```bash id="f28skg"
mkdir manifests
cd manifests
```

This directory stores all configuration files related to Kubernetes deployment.

---

## Step 2 — Create Deployment Manifest

The first manifest created was a **Deployment**, which tells Kubernetes how to run the backend application.

File created:

```id="hrb2ht"
flask-deployment.yaml
```

Example structure:

```yaml id="y0m0yd"
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nextwork-flask-backend
  namespace: default

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nextwork-flask-backend

  template:
    metadata:
      labels:
        app: nextwork-flask-backend

    spec:
      containers:
        - name: nextwork-flask-backend
          image: <ecr-image-uri>
          ports:
            - containerPort: 8080
```

<img width="569" height="234" alt="image" src="https://github.com/user-attachments/assets/12d1c824-0d2d-457a-8eba-af5bd85ab21e" />


---

## Understanding the Deployment Manifest

### apiVersion

```yaml id="0xtog0"
apiVersion: apps/v1
```

Defines which Kubernetes API version should manage this resource.

---

### kind

```yaml id="d7c8qg"
kind: Deployment
```

Specifies the resource type being created.

In this case, Kubernetes will create a **Deployment object** that manages Pods running the backend container.

---

### metadata

```yaml id="8s4w8k"
metadata:
  name: nextwork-flask-backend
  namespace: default
```

Contains identifying information about the resource, such as its name and namespace.

---

### replicas

```yaml id="ie9n7f"
replicas: 3
```

Defines how many identical copies of the application should run.

Kubernetes will automatically ensure **three Pods remain running**, even if one crashes.

---

### selector

```yaml id="k0ly75"
selector:
  matchLabels:
    app: nextwork-flask-backend
```

The selector tells Kubernetes which Pods belong to this Deployment.

It looks for Pods with the matching label.

---

### template

```yaml id="rf8c7h"
template:
```

Defines the blueprint for the Pods that will be created.

Every Pod created by the Deployment will follow this template.

---

### containers

```yaml id="v7ftsb"
containers:
  - name: nextwork-flask-backend
    image: <ecr-image-uri>
```

Specifies which container image Kubernetes should run.

In this project, the image is pulled from **Amazon ECR**, which was created in Part 2.

---

### containerPort

```yaml id="15y50l"
containerPort: 8080
```

Defines the port the application listens on inside the container.

---

## Step 3 — Create Service Manifest

Next, I created a **Service manifest**, which allows traffic to reach the application Pods.

File created:

```id="m92udg"
flask-service.yaml
```

Example structure:

```yaml id="sr9bsg"
apiVersion: v1
kind: Service

metadata:
  name: nextwork-flask-backend

spec:
  selector:
    app: nextwork-flask-backend

  type: NodePort

  ports:
    - port: 8080
      targetPort: 8080
      protocol: TCP
```

---

## Understanding the Service Manifest

### Service

A **Service** provides a stable networking endpoint for a group of Pods.

Since Pods can be created and destroyed dynamically, a Service ensures traffic is always routed correctly.

---

### selector

```yaml id="1nau07"
selector:
  app: nextwork-flask-backend
```

The selector matches Pods created by the Deployment.

This connects the Service to the backend Pods.

---

### type: NodePort

```yaml id="h9iw29"
type: NodePort
```

This exposes the application through a port on the cluster nodes, allowing external access.

---

### port mapping

```yaml id="3ks2ep"
port: 8080
targetPort: 8080
```

Defines how network traffic flows:

* `port` — service port
* `targetPort` — container port inside the Pod

---

## What I Learned

This part helped me understand how Kubernetes uses configuration files to manage applications.

Important concepts that became clearer:

* Kubernetes is driven by **declarative configuration**
* Deployments manage application replicas
* Pods are the smallest deployable unit in Kubernetes
* Services route traffic to Pods
* Labels and selectors connect Kubernetes resources together

Instead of manually running containers, Kubernetes maintains the **desired state** defined in the YAML manifests.

---

## Key Takeaways

From this step I learned:

* How Kubernetes uses YAML manifests to define infrastructure
* The role of Deployments in managing application replicas
* How Services expose applications to the network
* How labels and selectors connect Kubernetes resources
* How container images stored in ECR are used by Kubernetes deployments
