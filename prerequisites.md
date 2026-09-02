# Prerequisites

The following tools are required to complete this project.

---

## 1. AWS CLI

**AWS CLI** is used to interact with AWS services from the command line.

### Check AWS CLI Installation

```powershell
aws --version
```

### Configure AWS Credentials

```powershell
aws configure
```

You will be prompted to enter:

* AWS Access Key ID
* AWS Secret Access Key
* Default region
* Default output format

### Verify AWS Account

```powershell
aws sts get-caller-identity
```

This command verifies that your AWS CLI credentials are configured correctly.

---

## 2. kubectl

**kubectl** is the Kubernetes command-line tool used to interact with Kubernetes clusters.

### Check kubectl Installation

```powershell
kubectl version --client
```

---

## 3. eksctl

**eksctl** is a command-line utility used to create and manage Amazon EKS clusters.

### Check eksctl Installation

```powershell
eksctl version
```

---

## 4. Helm

**Helm** is a package manager for Kubernetes. It is used to install and manage Kubernetes applications, including the **AWS Load Balancer Controller**.

### Check Helm Installation

```powershell
helm version
```

---

## 5. Verify All Tools

Run the following commands to verify that all required tools are installed correctly:

```powershell
aws --version
kubectl version --client
eksctl version
helm version
```

### Expected Result

All four commands should return their respective installed versions.

Example:

```text
aws-cli/2.x.x
Client Version: v1.x.x
0.x.x
version.BuildInfo{Version:"v3.x.x"}
```

---

## Prerequisites Checklist

Before proceeding with the project, make sure the following are installed and configured:

* [ ] AWS CLI
* [ ] AWS credentials configured
* [ ] AWS account verified using `aws sts get-caller-identity`
* [ ] kubectl
* [ ] eksctl
* [ ] Helm

Once all prerequisites are completed, you can proceed with creating and configuring the Amazon EKS cluster.
