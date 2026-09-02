# AWS Load Balancer Controller

The **AWS Load Balancer Controller** allows Kubernetes applications running on Amazon EKS to create and manage AWS Application Load Balancers (ALB).

This guide covers:

* Downloading the IAM policy
* Creating the IAM policy
* Creating the Kubernetes IAM service account
* Configuring the AWS EKS Helm repository
* Getting the EKS VPC ID
* Installing the AWS Load Balancer Controller
* Verifying the controller

---

## Cluster Details

| Configuration       | Value                        |
| ------------------- | ---------------------------- |
| Cluster Name        | `demo-eks-cluster`           |
| AWS Region          | `ap-south-1`                 |
| Namespace           | `kube-system`                |
| Controller          | AWS Load Balancer Controller |
| Installation Method | Helm                         |

---

# Step 1: Download IAM Policy

Download the IAM policy required by the AWS Load Balancer Controller:

```powershell
curl.exe -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
```

### Verify the IAM Policy File

Check that the file was downloaded successfully:

```powershell
Get-Item .\iam_policy.json
```

You should see the `iam_policy.json` file in your current directory.

---

# Step 2: Create IAM Policy

Create an AWS IAM policy using the downloaded JSON file:

```powershell
aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json
```

The command returns an IAM policy ARN similar to:

```text
arn:aws:iam::<AWS_ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy
```

> **Note:** Replace `<AWS_ACCOUNT_ID>` with your actual AWS account ID when required.

---

# Step 3: Get AWS Account ID

Get the AWS account ID associated with your configured AWS credentials:

```powershell
aws sts get-caller-identity --query Account --output text
```

Example output:

```text
123456789012
```

Save this account ID because it will be required when creating the IAM service account.

---

# Step 4: Create IAM Service Account

Create the Kubernetes service account and associate it with an IAM role.

Replace `<AWS_ACCOUNT_ID>` with your actual AWS account ID:

```powershell
eksctl create iamserviceaccount --cluster=demo-eks-cluster --region=ap-south-1 --namespace=kube-system --name=aws-load-balancer-controller --role-name=AmazonEKSLoadBalancerControllerRole --attach-policy-arn=arn:aws:iam::<AWS_ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy --approve
```

This command creates:

* Kubernetes service account
* IAM role
* IAM trust relationship
* IAM policy attachment
* IAM-to-Kubernetes service account association

### Verify IAM Service Account

```powershell
eksctl get iamserviceaccount --cluster=demo-eks-cluster --region=ap-south-1
```

You should see the following service account:

```text
aws-load-balancer-controller
```

---

# Step 5: Add AWS EKS Helm Repository

Add the AWS EKS Helm repository:

```powershell
helm repo add eks https://aws.github.io/eks-charts
```

Expected output:

```text
"eks" has been added to your repositories
```

---

# Step 6: Update Helm Repository

Update the Helm repository:

```powershell
helm repo update
```

This downloads the latest available chart information from the configured repositories.

---

# Step 7: Verify Helm Repository

Check the configured Helm repositories:

```powershell
helm repo list
```

Example output:

```text
NAME    URL
eks     https://aws.github.io/eks-charts
```

---

# Step 8: Get VPC ID

The AWS Load Balancer Controller requires the VPC ID associated with the EKS cluster.

Run:

```powershell
aws eks describe-cluster --name demo-eks-cluster --region ap-south-1 --query "cluster.resourcesVpcConfig.vpcId" --output text
```

Example output:

```text
vpc-0123456789abcdef
```

Copy the VPC ID for the next step.

---

# Step 9: Install AWS Load Balancer Controller

Replace `<VPC_ID>` with the VPC ID obtained in the previous step.

```powershell
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=demo-eks-cluster --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller --set region=ap-south-1 --set vpcId=<VPC_ID>
```

### Important Configuration

| Parameter              | Value                          |
| ---------------------- | ------------------------------ |
| Helm Release           | `aws-load-balancer-controller` |
| Cluster                | `demo-eks-cluster`             |
| Namespace              | `kube-system`                  |
| Region                 | `ap-south-1`                   |
| Service Account        | `aws-load-balancer-controller` |
| Create Service Account | `false`                        |
| VPC ID                 | Your EKS VPC ID                |

The `serviceAccount.create=false` option tells Helm to use the IAM service account created using `eksctl`.

---

# Step 10: Verify Controller Deployment

Check the AWS Load Balancer Controller deployment:

```powershell
kubectl get deployment -n kube-system aws-load-balancer-controller
```

Example output:

```text
NAME                           READY   UP-TO-DATE   AVAILABLE
aws-load-balancer-controller  2/2     2            2
```

---

# Step 11: Verify Controller Pods

Check the controller pods:

```powershell
kubectl get pods -n kube-system | Select-String aws-load-balancer-controller
```

Expected output will look similar to:

```text
aws-load-balancer-controller-xxxxx   1/1   Running
aws-load-balancer-controller-yyyyy   1/1   Running
```

> The exact pod names will be different in your cluster.

---

# Step 12: Check Controller Logs

If the controller is not running correctly, check its logs:

```powershell
kubectl logs -n kube-system deployment/aws-load-balancer-controller
```

Look for errors related to:

* IAM permissions
* OIDC provider
* AWS credentials
* VPC configuration
* Kubernetes service account
* AWS API access

---

# Step 13: Verify Helm Installation

Check the Helm release:

```powershell
helm list -n kube-system
```

You should see:

```text
aws-load-balancer-controller
```

You can also check the Helm release status:

```powershell
helm status aws-load-balancer-controller -n kube-system
```

---

# Verification Checklist

Before proceeding, verify the following:

* [ ] `iam_policy.json` downloaded successfully.
* [ ] IAM policy created successfully.
* [ ] AWS account ID verified.
* [ ] IAM service account created.
* [ ] IAM service account verified using `eksctl`.
* [ ] AWS EKS Helm repository added.
* [ ] Helm repository updated.
* [ ] EKS VPC ID retrieved.
* [ ] AWS Load Balancer Controller installed.
* [ ] Controller deployment is available.
* [ ] Controller pods are `Running`.
* [ ] Helm release status is `deployed`.

---

# Troubleshooting

## Check Service Account

```powershell
kubectl get serviceaccount aws-load-balancer-controller -n kube-system
```

## Check Service Account Annotations

```powershell
kubectl describe serviceaccount aws-load-balancer-controller -n kube-system
```

The service account should have an IAM role annotation.

## Check Controller Deployment

```powershell
kubectl describe deployment aws-load-balancer-controller -n kube-system
```

## Check Controller Pods

```powershell
kubectl get pods -n kube-system -l app.kubernetes.io/name=aws-load-balancer-controller
```

## Check Controller Logs

```powershell
kubectl logs -n kube-system deployment/aws-load-balancer-controller
```

---

# Final Verification

Run the following commands:

```powershell
aws sts get-caller-identity
```

```powershell
eksctl get iamserviceaccount --cluster=demo-eks-cluster --region=ap-south-1
```

```powershell
helm list -n kube-system
```

```powershell
kubectl get deployment -n kube-system aws-load-balancer-controller
```

```powershell
kubectl get pods -n kube-system | Select-String aws-load-balancer-controller
```

If the controller pods show **`Running`** and the deployment is **Available**, the AWS Load Balancer Controller has been successfully installed.

---

# Next Step

After successfully installing the AWS Load Balancer Controller, proceed to:

**Deploy the Kubernetes application and expose it using an Ingress resource.**
