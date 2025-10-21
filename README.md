# 📘 Updating `aws-auth` ConfigMap in Amazon EKS

This document outlines the steps required to update the `aws-auth` ConfigMap in an Amazon EKS cluster. This ConfigMap controls which IAM roles and users have access to the Kubernetes cluster.

---

## 📌 Purpose

- Allow EKS worker nodes to join the cluster.
- Grant administrator access to specified IAM roles.

---

## ✅ Prerequisites

Before proceeding, ensure you have:

- AWS CLI configured with sufficient permissions.
- `kubectl` installed and configured for your EKS cluster.
- IAM Role ARNs for:
  - EKS worker nodes
  - Admin users

---

## 🛠️ Steps to Update `aws-auth` ConfigMap

### 1. Backup the Current ConfigMap

Run the following command to back up the existing `aws-auth` ConfigMap:

```cmd
kubectl get configmap -n kube-system aws-auth -o yaml > aws-auth-backup.yaml
```
2. Edit the ConfigMap
Open the ConfigMap in your default editor:


```cmd
kubectl edit -n kube-system configmap/aws-auth
```

3. Update the mapRoles Section
Modify or add entries in the mapRoles section. Replace <account-id> and role names with your actual IAM role ARNs:

```cmd
mapRoles: |
  - groups:
    - system:bootstrappers
    - system:nodes
    rolearn: arn:aws:iam::<account-id>:role/eks-node-group-role
    username: system:node:{{EC2PrivateDNSName}}
  - groups:
    - system:masters
    rolearn: arn:aws:iam::<account-id>:role/IAM-Role-K8s
    username: IAM-Role-K8s
```
4. Save and Exit
Save the file and exit your editor. This will apply the changes to the cluster.

5. Verify the Update
To confirm the changes were successfully applied:

```cmd
kubectl get configmap -n kube-system aws-auth -o yaml
```
To restore the previous configuration:

```cmd
kubectl apply -f aws-auth-backup.yaml
```

📝 Notes
system:masters: Grants full administrative access.

system:bootstrappers and system:nodes: Required for worker nodes to join and function properly.

Always use the correct IAM Role ARNs from the AWS Console.

Use the {{EC2PrivateDNSName}} template for node role usernames.

🧰 Troubleshooting
If worker nodes do not appear or are in a NotReady state:

Verify IAM role permissions.

Ensure ConfigMap syntax is correct.



