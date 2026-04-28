# 🚀 EKS Setup & Deployment using Terraform

This guide covers:

* EKS cluster creation using Terraform
* Connecting using AWS CLI
* Deploying app on Kubernetes
* Exposing via LoadBalancer
* Debugging using kubectl
* Cleanup (end-to-end)

---

# 📦 Prerequisites

* AWS CLI
* Terraform (`>=1.6`)
* kubectl
* IAM credentials

---

# 🔐 Configure AWS

```bash
aws configure
```

---

# 🪣 Backend Setup (S3 + DynamoDB)

```bash
aws s3api create-bucket --bucket my-terraform-state-eks-demo --region ap-south-1 --create-bucket-configuration LocationConstraint=ap-south-1

aws s3api put-bucket-versioning --bucket my-terraform-state-eks-demo --versioning-configuration Status=Enabled

aws dynamodb create-table --table-name terraform-lock --attribute-definitions AttributeName=LockID,AttributeType=S --key-schema AttributeName=LockID,KeyType=HASH --billing-mode PAY_PER_REQUEST --region ap-south-1
```

---

# 🏗️ Deploy EKS

```bash
cd terraform/k8

terraform init
terraform plan
terraform apply -auto-approve
```

---

# 🔗 Connect to Cluster

```bash
aws eks update-kubeconfig --region ap-south-1 --name demo-eks-webapp-v25
```

---

# ✅ Verify Cluster

```bash
kubectl get nodes
kubectl get pods -A
```

---

# 🚀 Deploy Application (NGINX)

```bash
kubectl create deployment nginx --image=nginx
```

---

# 🔍 Check resources

```bash
kubectl get deployments
kubectl get pods
kubectl get rs   # ReplicaSets
```

---

# 📊 Detailed info

```bash
kubectl describe pod <pod-name>
kubectl describe deployment nginx
```

---

# 📜 View logs

```bash
kubectl logs <pod-name>
```

👉 For multi-container pod:

```bash
kubectl logs <pod-name> -c <container-name>
```

---

# 🔄 Scale application

```bash
kubectl scale deployment nginx --replicas=3
```

Check:

```bash
kubectl get pods
```

---

# 🌐 Expose Application

```bash
kubectl expose deployment nginx --type=LoadBalancer --port=80
```

---

# 🔍 Get service details

```bash
kubectl get svc
kubectl describe svc nginx
```

---

# 🌍 Access from browser

```text
http://<EXTERNAL-IP>
```

---

# ⚡ Port Forward (local testing)

```bash
kubectl port-forward deployment/nginx 8080:80
```

Access:

```text
http://localhost:8080
```

---

# 🧠 Debugging Commands

## Check cluster events

```bash
kubectl get events
```

---

## Check pod status issues

```bash
kubectl describe pod <pod-name>
```

---

## Check node details

```bash
kubectl describe node <node-name>
```

---

## Exec into pod

```bash
kubectl exec -it <pod-name> -- /bin/bash
```

---

## Check namespaces

```bash
kubectl get ns
```

---

# 📦 Advanced (Optional)

## Apply YAML file

```bash
kubectl apply -f deployment.yaml
```

---

## Delete using YAML

```bash
kubectl delete -f deployment.yaml
```

---

# 🧹 Cleanup Kubernetes Resources

```bash
kubectl delete svc nginx
kubectl delete deployment nginx
```

---

# 💣 Destroy Infrastructure

```bash
terraform destroy -auto-approve
```

---

# 🔍 Verify Cleanup

```bash
aws eks list-clusters --region ap-south-1
```

---

# ⚠️ Common Issues

## ❌ kubectl not found

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/windows/amd64/kubectl.exe"

mkdir -p $HOME/bin
mv kubectl.exe $HOME/bin/

export PATH=$PATH:$HOME/bin
```

---

## ❌ External IP pending

```bash
kubectl get svc
```

(wait 2–3 mins)

---

## ❌ Pod not running

```bash
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

---

# 🎯 Summary

| Task            | Command                     |
| --------------- | --------------------------- |
| Create infra    | `terraform apply`           |
| Connect cluster | `aws eks update-kubeconfig` |
| Deploy app      | `kubectl create deployment` |
| Scale app       | `kubectl scale`             |
| Logs            | `kubectl logs`              |
| Debug           | `kubectl describe`          |
| Expose app      | `kubectl expose`            |
| Access          | Browser via EXTERNAL-IP     |
| Cleanup         | `kubectl delete`            |
| Destroy infra   | `terraform destroy`         |

---

# 🚀 Next Steps

* Ingress (ALB) setup
* Domain + HTTPS
* CI/CD deployment
* Auto scaling

---
