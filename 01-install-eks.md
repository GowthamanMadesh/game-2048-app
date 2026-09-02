# Create Amazon EKS Cluster

This document explains how to create an Amazon EKS cluster using **eksctl** with **AWS Fargate**.

---

## Cluster Details

| Configuration | Value              |
| ------------- | ------------------ |
| Cluster Name  | `demo-eks-cluster` |
| AWS Region    | `ap-south-1`       |
| Region Name   | Mumbai             |
| Compute       | AWS Fargate        |

---

## 1. Create EKS Cluster

Create the EKS cluster using `eksctl`:

```powershell
eksctl create cluster --name demo-eks-cluster --region ap-south-1 --fargate
```

The command creates and configures:

* EKS control plane
* VPC
* Subnets
* Security groups
* Fargate profile
* IAM resources
* Kubernetes configuration

> **Note:** Cluster creation can take several minutes.

---

## 2. Verify EKS Cluster

List the EKS clusters in the `ap-south-1` region:

```powershell
eksctl get cluster --region ap-south-1
```

Example output:

```text
NAME               REGION      EKSCTL CREATED
demo-eks-cluster   ap-south-1  True
```

---

## 3. Check Cluster Status

Check the status of the EKS cluster:

```powershell
aws eks describe-cluster --name demo-eks-cluster --region ap-south-1 --query "cluster.status" --output text
```

Expected output:

```text
ACTIVE
```

If the output is `ACTIVE`, the EKS control plane is ready.

---

## 4. Configure kubectl

Configure `kubectl` to communicate with the newly created EKS cluster:

```powershell
aws eks update-kubeconfig --name demo-eks-cluster --region ap-south-1
```

Expected output:

```text
Added new context arn:aws:eks:ap-south-1:<ACCOUNT-ID>:cluster/demo-eks-cluster to C:\Users\<USERNAME>\.kube\config
```

---

## 5. Verify kubectl Context

Check the current Kubernetes context:

```powershell
kubectl config current-context
```

Expected output will contain:

```text
arn:aws:eks:ap-south-1:<ACCOUNT-ID>:cluster/demo-eks-cluster
```

---

## 6. Verify Kubernetes Cluster

Check the Kubernetes cluster information:

```powershell
kubectl cluster-info
```

Example output:

```text
Kubernetes control plane is running at https://xxxxxxxx.gr7.ap-south-1.eks.amazonaws.com
CoreDNS is running at https://xxxxxxxx.gr7.ap-south-1.eks.amazonaws.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

---

## 7. Verify Nodes

Check the nodes available in the cluster:

```powershell
kubectl get nodes
```

Because this cluster uses **AWS Fargate**, you may not see traditional EC2 worker nodes immediately. Fargate creates compute capacity for eligible pods when they are scheduled.

---

## 8. Verify Fargate Profiles

List the Fargate profiles:

```powershell
eksctl get fargateprofile --cluster demo-eks-cluster --region ap-south-1
```

Example:

```text
NAME                      SELECTOR_NAMESPACE
fp-default                default
```

---

## 9. Verify Kubernetes Namespaces

List all namespaces:

```powershell
kubectl get namespaces
```

Example:

```text
NAME              STATUS   AGE
default           Active   ...
kube-node-lease   Active   ...
kube-public       Active   ...
kube-system       Active   ...
```

---

## 10. Verify EKS Cluster

Run the following commands to perform a final verification:

```powershell
eksctl get cluster --region ap-south-1
```

```powershell
aws eks describe-cluster --name demo-eks-cluster --region ap-south-1 --query "cluster.status" --output text
```

```powershell
kubectl config current-context
```

```powershell
kubectl cluster-info
```

```powershell
kubectl get namespaces
```

---

## Expected Result

The EKS cluster should have the following status:

```text
ACTIVE
```

`kubectl` should successfully connect to:

```text
demo-eks-cluster
```

The Kubernetes API server should be accessible through:

```powershell
kubectl cluster-info
```

---

## Troubleshooting

### Check AWS Identity

If you receive AWS authentication errors, verify your AWS credentials:

```powershell
aws sts get-caller-identity
```

### Check AWS Region

Verify that you are using the correct region:

```powershell
aws configure get region
```

Expected:

```text
ap-south-1
```

### Check EKS Cluster

```powershell
eksctl get cluster --region ap-south-1
```

### Check kubectl Context

```powershell
kubectl config get-contexts
```

Switch to the EKS cluster context if required:

```powershell
kubectl config use-context <EKS-CONTEXT>
```

---

## Next Step

After the EKS cluster is successfully created and `kubectl` is configured, proceed to the next step:

**Install and configure the AWS Load Balancer Controller.**
