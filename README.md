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
