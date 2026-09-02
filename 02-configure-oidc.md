# Configure IAM OIDC Provider

The **AWS Load Balancer Controller** requires AWS IAM permissions to manage AWS resources such as Application Load Balancers and Target Groups.

To allow Kubernetes service accounts to securely assume AWS IAM roles, we need to configure an **IAM OIDC (OpenID Connect) provider** for the EKS cluster.

---

## Cluster Details

| Configuration | Value              |
| ------------- | ------------------ |
| Cluster Name  | `demo-eks-cluster` |
| AWS Region    | `ap-south-1`       |
| Region Name   | Mumbai             |
| OIDC          | IAM OIDC Provider  |

---

## 1. Associate IAM OIDC Provider

Run the following command to associate an IAM OIDC provider with the EKS cluster:

```powershell
eksctl utils associate-iam-oidc-provider --cluster demo-eks-cluster --region ap-south-1 --approve
```

### What This Does

This command:

* Creates an IAM OIDC identity provider for the EKS cluster.
* Establishes trust between AWS IAM and Kubernetes service accounts.
* Enables Kubernetes service accounts to assume IAM roles.
* Allows the AWS Load Balancer Controller to access required AWS resources securely.

The `--approve` option automatically approves the OIDC provider creation.

---

## 2. Verify OIDC Provider

Verify that the EKS cluster has an OIDC issuer configured:

```powershell
aws eks describe-cluster --name demo-eks-cluster --region ap-south-1 --query "cluster.identity.oidc.issuer" --output text
```

### Expected Output

The command should return an OIDC issuer URL similar to:

```text
https://oidc.eks.ap-south-1.amazonaws.com/id/XXXXXXXXXXXX
```

> **Note:** The OIDC URL will be different for every EKS cluster.

---

## 3. Verify OIDC Provider Using eksctl

You can also check the cluster configuration using:

```powershell
eksctl get cluster --name demo-eks-cluster --region ap-south-1
```

To verify the OIDC provider directly through AWS IAM, list the configured OpenID Connect providers:

```powershell
aws iam list-open-id-connect-providers
```

Example output:

```text
{
    "OpenIDConnectProviderList": [
        {
            "Arn": "arn:aws:iam::<ACCOUNT-ID>:oidc-provider/oidc.eks.ap-south-1.amazonaws.com/id/XXXXXXXXXXXX"
        }
    ]
}
```

---

## 4. OIDC Configuration Flow

The authentication flow will work as follows:

```text
Kubernetes Service Account
          |
          v
    EKS OIDC Provider
          |
          v
       IAM Role
          |
          v
    AWS Permissions
          |
          v
AWS Load Balancer Controller
```

This configuration allows the AWS Load Balancer Controller to use AWS IAM permissions without storing AWS access keys inside Kubernetes.

---

## 5. Verification Checklist

Before moving to the next step, verify the following:

* [ ] EKS cluster `demo-eks-cluster` exists.
* [ ] Cluster is in `ap-south-1`.
* [ ] IAM OIDC provider is associated.
* [ ] OIDC issuer URL is returned successfully.
* [ ] IAM OIDC provider appears in AWS IAM.

---

## Next Step

After the OIDC provider is configured successfully, proceed to the next step:

**Create the IAM policy and IAM role required by the AWS Load Balancer Controller.**
