****🚀 Flask + MySQL Two-Tier Application on Amazon EKS****

A production-style Two-Tier Application Deployment on Amazon EKS, where:

🐍 Flask acts as the application layer

🗄️ MySQL acts as the database layer

☁️ Amazon EKS orchestrates containerized workloads

⚙️ eksctl & kubectl manage Kubernetes infrastructure

**📌 Project Architecture**

<p align="center">
  <img src="images/architecture.png" alt="Flask MySQL EKS Architecture" width="800"/>
</p>

```
User → Kubernetes Service (LoadBalancer)
        → Flask Application Pod
              → MySQL Service (ClusterIP)
                    → MySQL Pod
```

**🔹 Tech Stack**

Cloud Provider: Amazon Web Services

Container Orchestration: Amazon Elastic Kubernetes Service (EKS)

Cluster Provisioning: eksctl

Container Management: Kubernetes

Backend: Flask

Database: MySQL

Compute: Amazon EC2 (Amazon Linux 2)

**📋 Prerequisites**

AWS Account

EC2 Instance (Amazon Linux 2 – t2.micro recommended)

IAM Role with EKS permissions (Recommended over access keys)

🛠 Step-by-Step Implementation

🔹 Step 1: Launch EC2 Instance

Launch an Amazon Linux 2 EC2 instance.

Update system:
```
sudo yum update -y
```
🔹 Step 2: Install AWS CLI

Check if installed:
```
aws --version
```
If not installed:
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```
Configure AWS:

aws configure

✅ Recommended: Attach IAM Role instead of using access keys.

**🔹 Step 3: Install kubectl**
```
curl -LO "https://dl.k8s.io/release/$(curl -Ls https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x kubectl
mkdir -p $HOME/bin
cp kubectl $HOME/bin/

echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

kubectl version --client
```

**🔹 Step 4: Install eksctl**
```
curl --silent --location \
"https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" \
| tar xz -C /tmp

sudo mv /tmp/eksctl /usr/local/bin

eksctl version
```
**🔹 Step 5: Create EKS Cluster**
```
eksctl create cluster \
  --name flask-mysql-cluster \
  --region ap-south-1 \
  --node-type t2.small \
  --nodes-min 2 \
  --nodes-max 3
```

Verify:
```
eksctl get cluster
```

Update kubeconfig:
```
aws eks update-kubeconfig --name flask-mysql-cluster --region ap-south-1
```
Check nodes:
```
kubectl get nodes
```

**🔹 Step 6: Clone Project Repository**
```
sudo yum install git -y
git clone https://github.com/ReyazShaik/eks-flask-mysql-project.git
cd eks-flask-mysql-project
```

**🗄 Deploy MySQL (Database Layer)**

Apply Kubernetes manifests:
```
kubectl apply -f mysql-secrets.yml
kubectl apply -f mysql-configmap.yml
kubectl apply -f mysql-deployment.yml
kubectl apply -f mysql-svc.yml
```

**🔐 Security Best Practice Used**

Kubernetes Secrets for DB credentials

ConfigMap for environment configuration

ClusterIP service for internal communication

🐍 Deploy Flask Application (App Layer)
```
kubectl apply -f two-tier-app-deployment.yml
kubectl apply -f two-tier-app-svc.yml
```

Verify:
```
kubectl get all
kubectl get deploy
```
If restart required:
```
kubectl rollout restart deployment two-tier-app
```

**🌐 Access the Application**

Get LoadBalancer URL:
```
kubectl get svc
```
Copy the EXTERNAL-IP of two-tier-app-service and open in browser.

**🧠 Key Kubernetes Concepts Demonstrated**

Deployments

Services (ClusterIP & LoadBalancer)

Secrets & ConfigMaps

Rolling Updates

Cluster Autoscaling

Two-Tier Architecture

**🏗 Project Workflow**
```
EC2 Setup
    ↓
Install AWS CLI, kubectl, eksctl
    ↓
Create EKS Cluster
    ↓
Deploy MySQL (Database Tier)
    ↓
Deploy Flask App (Application Tier)
    ↓
Expose via LoadBalancer
```

**🗑 Cleanup**

Delete application resources:
```
kubectl delete deployment two-tier-app mysql
kubectl delete service two-tier-app-service mysql
```
Delete cluster:
```
eksctl delete cluster --name flask-mysql-cluster --region ap-south-1
```
