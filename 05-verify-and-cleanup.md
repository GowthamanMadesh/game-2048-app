# Verification and Cleanup

This guide explains how to verify the EKS cluster, Fargate profile, Game 2048 application, AWS Load Balancer Controller, and finally clean up the AWS resources created during this project.

---

# Verification

## 1. Verify EKS Cluster

Check the current status of the EKS cluster:

```powershell
aws eks describe-cluster --name demo-eks-cluster --region ap-south-1 --query "cluster.status" --output text
```

### Expected Output

```text
ACTIVE
```

The `ACTIVE` status indicates that the EKS control plane is running successfully.

---

## 2. Verify Fargate Profile

Check the Fargate profiles configured for the EKS cluster:

```powershell
eksctl get fargateprofile --cluster demo-eks-cluster --region ap-south-1
```

The `alb-sample-app` Fargate profile should be listed.

---

## 3. Verify Application Pods

Check the Game 2048 pods:

```powershell
kubectl get pods -n game-2048
```

Expected status:

```text
Running
```

Example:

```text
NAME                         READY   STATUS    RESTARTS   AGE
deployment-2048-xxxxx       1/1     Running   0          5m
```

---

## 4. Verify Services

Check the Kubernetes services:

```powershell
kubectl get svc -n game-2048
```

The Game 2048 service should be available.

---

## 5. Verify Ingress

Check the Kubernetes Ingress:

```powershell
kubectl get ingress -n game-2048
```

The `ADDRESS` column should contain the AWS Application Load Balancer DNS name.

Example:

```text
NAME           CLASS   HOSTS   ADDRESS
ingress-2048   alb     *       k8s-game2048-xxxxx.ap-south-1.elb.amazonaws.com
```

Open the ALB DNS address in a web browser to verify that the Game 2048 application is accessible.

---

## 6. Verify AWS Load Balancer Controller

Check the AWS Load Balancer Controller deployment:

```powershell
kubectl get deployment -n kube-system aws-load-balancer-controller
```

Example:

```text
NAME                           READY   UP-TO-DATE   AVAILABLE
aws-load-balancer-controller   2/2     2            2
```

The controller should show the required replicas as `READY` and `AVAILABLE`.

---

## 7. Verify All Kubernetes Resources

Check all resources in the application namespace:

```powershell
kubectl get all -n game-2048
```

Check the Ingress separately:

```powershell
kubectl get ingress -n game-2048
```

---

# Complete Verification

Run the following commands before cleanup:

```powershell
aws eks describe-cluster --name demo-eks-cluster --region ap-south-1 --query "cluster.status" --output text
```

```powershell
eksctl get fargateprofile --cluster demo-eks-cluster --region ap-south-1
```

```powershell
kubectl get pods -n game-2048
```

```powershell
kubectl get svc -n game-2048
```

```powershell
kubectl get ingress -n game-2048
```

```powershell
kubectl get deployment -n kube-system aws-load-balancer-controller
```

---

# Cleanup

> ⚠️ **Warning:** The following commands delete AWS and Kubernetes resources. Make sure you no longer need the environment before proceeding.

---

## 1. Delete Game 2048 Application

Delete the `game-2048` namespace:

```powershell
kubectl delete namespace game-2048
```

This removes the Kubernetes resources deployed inside the namespace, including:

* Pods
* Deployments
* Services
* Ingress

The AWS Application Load Balancer associated with the Ingress should also be removed by the AWS Load Balancer Controller.

Verify the namespace deletion:

```powershell
kubectl get namespace game-2048
```

Expected:

```text
Error from server (NotFound): namespaces "game-2048" not found
```

---

## 2. Delete Fargate Profile

Delete the Fargate profile created for the Game 2048 application:

```powershell
eksctl delete fargateprofile --cluster demo-eks-cluster --region ap-south-1 --name alb-sample-app
```

Verify the remaining Fargate profiles:

```powershell
eksctl get fargateprofile --cluster demo-eks-cluster --region ap-south-1
```

---

## 3. Uninstall AWS Load Balancer Controller

Uninstall the Helm release:

```powershell
helm uninstall aws-load-balancer-controller -n kube-system
```

Verify the deployment has been removed:

```powershell
kubectl get deployment -n kube-system aws-load-balancer-controller
```

Expected:

```text
Error from server (NotFound): deployments.apps "aws-load-balancer-controller" not found
```

---

## 4. Delete EKS Cluster

After verifying that the application and controller resources are no longer required, delete the EKS cluster:

```powershell
eksctl delete cluster --name demo-eks-cluster --region ap-south-1
```

This may take several minutes to complete.

---

# 5. Verify Cluster Deletion

After the deletion process finishes, verify that the EKS cluster no longer exists:

```powershell
eksctl get cluster --region ap-south-1
```

You should no longer see:

```text
demo-eks-cluster
```

You can also verify through AWS CLI:

```powershell
aws eks describe-cluster --name demo-eks-cluster --region ap-south-1
```

The command should return a `ResourceNotFoundException`.

---

# 6. Check AWS Resources

After deleting the cluster, review the AWS Console and verify that unwanted resources have been removed.

Check the following services:

* Amazon EKS
* EC2 / VPC
* Elastic Load Balancing
* IAM
* CloudFormation
* CloudWatch
* AWS Fargate

> **Important:** Review your AWS account after cleanup to make sure no resources are still running and generating charges.

---

# Cleanup Checklist

* [ ] Game 2048 namespace deleted.
* [ ] Application resources deleted.
* [ ] Application ALB removed.
* [ ] Fargate profile deleted.
* [ ] AWS Load Balancer Controller uninstalled.
* [ ] EKS cluster deleted.
* [ ] AWS resources reviewed.
* [ ] No unnecessary resources remain.

---

# Important Notes

### IAM Resources

The IAM policy and IAM role created for the AWS Load Balancer Controller may not automatically be removed when uninstalling the Helm chart.

Review the IAM resources created during this project.

### OIDC Provider

The IAM OIDC provider associated with the EKS cluster may also remain in your AWS account after cluster deletion.

Review and remove it if it is no longer required.

### IAM Policy

The following policy was created during this project:

```text
AWSLoadBalancerControllerIAMPolicy
```

If it is no longer required, delete it after confirming that no other workloads use it.

### IAM Role

The following IAM role was created for the controller:

```text
AmazonEKSLoadBalancerControllerRole
```

Remove it only after confirming that it is not being used by another application.

---

# Final Status

After completing the cleanup, the following resources should no longer exist:

```text
demo-eks-cluster
        |
        +-- Fargate Profile
        |
        +-- Game 2048 Application
        |
        +-- Application Load Balancer
        |
        +-- AWS Load Balancer Controller
```

The AWS account should be reviewed to ensure that no unnecessary resources remain.

---

# Project Completed

The project has demonstrated:

* Amazon EKS cluster creation
* AWS Fargate configuration
* IAM OIDC provider configuration
* IAM service account configuration
* AWS Load Balancer Controller installation
* Kubernetes application deployment
* AWS Application Load Balancer integration
* Application verification
* EKS resource cleanup
