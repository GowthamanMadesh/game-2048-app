# 🎮 Game 2048 Deployment on Amazon EKS

## 📌 Project Overview

This project demonstrates the deployment of the **Game 2048 application** on **Amazon Elastic Kubernetes Service (EKS)** using **AWS Fargate**, Kubernetes, Helm, and the AWS Load Balancer Controller.

The application is deployed in the **AWS Mumbai region (`ap-south-1`)** and exposed to the internet using an **AWS Application Load Balancer (ALB)** through Kubernetes Ingress.

---

## 🏗️ Architecture

```text
                         Internet
                            |
                            v
                +----------------------+
                |   AWS ALB / Ingress  |
                +----------+-----------+
                           |
                           v
                +----------------------+
                |    AWS EKS Cluster   |
                | demo-eks-cluster     |
                +----------+-----------+
                           |
                           v
                +----------------------+
                | Kubernetes Ingress   |
                +----------+-----------+
                           |
                           v
                +----------------------+
                | Kubernetes Service   |
                +----------+-----------+
                           |
                           v
                +----------------------+
                |  Game 2048 Pods      |
                |   AWS Fargate        |
                +----------------------+


🛠️ Technologies Used
Amazon EKS
AWS Fargate
Kubernetes
AWS Application Load Balancer
AWS Load Balancer Controller
IAM
IAM OIDC
Helm
eksctl
kubectl
AWS CLI


📋 Prerequisites

Before starting the project, install the following tools.

1. AWS CLI

AWS CLI is used to interact with AWS services.

Check installation:

aws --version

Configure AWS credentials:

aws configure

Verify AWS account:

aws sts get-caller-identity


2. kubectl

kubectl is used to interact with Kubernetes clusters.

Check installation:

kubectl version --client
3. eksctl

eksctl is used to create and manage Amazon EKS clusters.

Check installation:

eksctl version
4. Helm

Helm is used to install and manage Kubernetes applications and the AWS Load Balancer Controller.

Check installation:

helm version



Verify All Tools
aws --version
kubectl version --client
eksctl version
helm version


Step 1: Create EKS Cluster
Cluster Configuration
Configuration	Value
Cluster Name	demo-eks-cluster
AWS Region	ap-south-1
Region Name	Mumbai
Compute	AWS Fargate

Create the EKS cluster:

eksctl create cluster --name demo-eks-cluster --region ap-south-1 --fargate

This command creates the EKS control plane and configures the required AWS networking and Fargate resources.

Step 2: Verify EKS Cluster

Check the cluster:

eksctl get cluster --region ap-south-1

Check cluster status:

aws eks describe-cluster --name demo-eks-cluster --region ap-south-1 --query "cluster.status" --output text

Expected output:

ACTIVE
