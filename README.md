### DevSecOps With Docker Scout Hotstar Clone
![DisneyHotstar (1)](https://github.com/user-attachments/assets/4389de0c-5ee4-4dd4-91c9-dad4a1673bc1)

# ✅ Steps to Fix EKS Access Issues for Jenkins CI/CD Pipeline

---

## 🔧 1. Update the EC2 Instance Kubeconfig  
*Because it's not aware of the newly created EKS cluster*

Run these commands **in EC2 instance**:

```bash
chown -R $(whoami):$(whoami) ~/.kube
echo $KUBECONFIG
unset KUBECONFIG
aws eks update-kubeconfig --name EKS_CLOUD --region ap-south-1
```

---

## 📋 2. Update the `aws-auth` ConfigMap

1. Go to config and copy it:
   ```bash
   cat /root/.kube/config
   ```

2. Then in **Jenkins > Manage Jenkins > Kubernetes Plugin Configuration**, paste this config where appropriate.

3. Now edit the `aws-auth` configmap:
   ```bash
   kubectl edit configmap aws-auth -n kube-system
   ```

4. Paste the following content under `mapRoles:` **(don't modify anything)**:

```yaml
mapRoles: |
  - groups:
    - system:bootstrappers
    - system:nodes
    rolearn: arn:aws:iam::353805436497:role/eks-node-group-cloud
    username: system:node:{{EC2PrivateDNSName}}

  - groups:
    - system:masters
    rolearn: arn:aws:iam::353805436497:role/IAM-Role-K8s
    username: IAM-Role-K8s
```

---

## ⚠️ 3. If Jenkins is Still Failing to Deploy to K8s

If you're still facing issues with deployment from Jenkins to EKS:

```bash
sudo cat ~/.kube/config
```

👉 Copy and paste the output into **Jenkins Credentials** where the Kubernetes plugin requests the kubeconfig.


