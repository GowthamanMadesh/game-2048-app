# 🎮 Game 2048 on Amazon EKS

## Project Overview

This project demonstrates how to deploy the Game 2048 application on Amazon EKS using Kubernetes and AWS Fargate.

The project also configures the AWS Load Balancer Controller to expose the application through an AWS Application Load Balancer (ALB).

## Architecture

Developer
   |
   v
GitHub
   |
   v
Amazon EKS
   |
   +----------------------+
   |                      |
   v                      v
Fargate                 ALB Controller
   |                      |
   v                      v
Game 2048 Pods       AWS Application Load Balancer
                           |
                           v
                        Internet

## Technologies

- AWS EKS
- AWS Fargate
- Kubernetes
- AWS Load Balancer Controller
- Application Load Balancer
- IAM
- OIDC
- Helm
- eksctl
- kubectl
- AWS CLI

## Implementation Steps

1. Install prerequisites
2. Create EKS cluster
3. Configure kubectl
4. Configure IAM OIDC provider
5. Create IAM policy
6. Create IAM service account
7. Install AWS Load Balancer Controller
8. Create Fargate profile
9. Deploy Game 2048
10. Configure Kubernetes Ingress
11. Access application through ALB
12. Verify deployment
13. Clean up AWS resources
