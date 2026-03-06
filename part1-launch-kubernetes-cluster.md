# Part 1 — Launch a Kubernetes Cluster with Amazon EKS

In this part of the project, I created a Kubernetes cluster using **Amazon EKS (Elastic Kubernetes Service)**.

The goal of this step was to understand how Kubernetes clusters are created on AWS and what infrastructure is required behind the scenes before we can deploy any application.

---

## Overview

Before deploying applications to Kubernetes, we first need a **running cluster**.
To do this, I used an **EC2 instance** as the environment where commands would be executed and used **eksctl** to provision the EKS cluster.

Under the hood, AWS automatically creates several resources needed for Kubernetes such as networking, security groups, and worker nodes.

---

## Tools Used

* Amazon EC2
* Amazon EKS
* AWS IAM
* AWS CloudFormation
* eksctl CLI

---

## Step 1 — Launch an EC2 Instance

I first launched an **EC2 instance** that acts as a control environment where I can run commands to interact with AWS and Kubernetes.

Configuration used:

* Amazon Linux 2023 AMI
* Instance type: `t3.micro`
* Default security group
* Connected using **EC2 Instance Connect**

This instance serves as a workspace where tools like `eksctl`, `kubectl`, and `docker` can be installed.

---

## Step 2 — Install `eksctl`

To create the Kubernetes cluster, I installed **eksctl**, a command line tool that simplifies creating and managing Amazon EKS clusters.

Install command:

```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv -v /tmp/eksctl /usr/local/bin
```

Verify installation:

```bash
eksctl version
```

### What I Learned

`eksctl` simplifies the EKS cluster creation process by automatically configuring:

* networking
* node groups
* security settings
* Kubernetes control plane resources

Without tools like `eksctl`, creating a Kubernetes cluster manually would require many more AWS configuration steps.

---

## Step 3 — Create IAM Role for the EC2 Instance

By default, a newly created EC2 instance **has no permission to interact with AWS services**.

To allow the instance to create and manage an EKS cluster, I created an IAM role called:

```
nextwork-eks-instance-role
```

Permissions used:

```
AdministratorAccess
```

This role was then attached to the EC2 instance.

### Why this is necessary

The EC2 instance needs permission to:

* create AWS resources
* manage EKS clusters
* interact with CloudFormation
* access networking resources

Without an IAM role, the cluster creation command would fail.

In a real production environment, the recommended practice would be **least privilege access**, but for learning purposes `AdministratorAccess` simplifies the setup.

---

## Step 4 — Create the EKS Cluster

Once the environment was ready, I created the Kubernetes cluster using `eksctl`.

Example command used:

```bash
eksctl create cluster \
  --name nextwork-eks-cluster \
  --nodegroup-name nextwork-nodegroup \
  --node-type t3.micro \
  --nodes 3 \
  --nodes-min 1 \
  --nodes-max 3 \
  --version 1.33 \
  --region <your-region>
```

This command provisions:

* the Kubernetes control plane
* worker nodes
* networking resources
* security groups
* IAM roles for the cluster

Cluster creation typically takes **15–20 minutes**.

---

## What Happens Behind the Scenes

When `eksctl` runs the cluster creation command, it actually triggers **AWS CloudFormation** templates.

CloudFormation then provisions the infrastructure needed for the Kubernetes cluster.

Resources created include:

* VPC networking
* Subnets
* Security groups
* Node groups
* IAM roles
* Load balancing components

In simple terms:

```
eksctl → CloudFormation → AWS Infrastructure → EKS Cluster
```

---

## Cost Consideration

Amazon EKS has a **cluster management cost of about $0.10 per hour**.

Even if no applications are running, the control plane itself is billed.

Because of this, I made sure to **delete the cluster after finishing the project** to avoid unnecessary charges.

For personal learning projects, this is important to keep in mind.

---

## Key Takeaways

From this step I learned:

* How Amazon EKS simplifies running Kubernetes on AWS
* How `eksctl` automates cluster setup
* Why IAM roles are required for EC2 instances
* How CloudFormation provisions infrastructure behind the scenes
* That EKS clusters have a running cost and should be deleted when not in use
