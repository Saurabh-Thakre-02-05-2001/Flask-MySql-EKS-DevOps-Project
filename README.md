🚀 Flask + MySQL Two-Tier Application on Amazon EKS

This project demonstrates deploying a Flask application with a MySQL backend on Amazon EKS using eksctl and kubectl.

📌 Prerequisites

AWS Account

EC2 Instance (Amazon Linux 2 – t2.micro)

IAM Role with EKS permissions (Recommended)

🔹 Step 1: Launch EC2 (Amazon Linux 2)

Update system:

sudo yum update -y
🔹 Step 2: Install AWS CLI (If Not Installed)

Check version:

aws --version

If not installed:

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

Configure AWS:

aws configure

(OR attach IAM Role to EC2)

🔹 Step 3: Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -Ls https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x kubectl

mkdir -p $HOME/bin
cp kubectl $HOME/bin/

echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

kubectl version --client
🔹 Step 4: Install eksctl
curl --silent --location \
"https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" \
| tar xz -C /tmp

sudo mv /tmp/eksctl /usr/local/bin

eksctl version
🔹 Step 5: Create EKS Cluster
eksctl create cluster \
  --name reyaz-cluster \
  --region ap-south-1 \
  --node-type t2.small \
  --nodes-min 2 \
  --nodes-max 3

Verify cluster:

eksctl get cluster

Update kubeconfig:

aws eks update-kubeconfig --name reyaz-cluster --region ap-south-1

Check nodes:

kubectl get nodes
🔹 Step 6: Install Git and Clone Project
sudo yum install git -y

git clone https://github.com/ReyazShaik/eks-flask-mysql-project.git

cd eks-flask-mysql-project
🔹 Step 7: Deploy MySQL
kubectl apply -f mysql-secrets.yml
kubectl apply -f mysql-configmap.yml
kubectl apply -f mysql-deployment.yml
kubectl apply -f mysql-svc.yml
🔹 Step 8: Deploy Flask Application
kubectl apply -f two-tier-app-deployment.yml
kubectl apply -f two-tier-app-svc.yml

Check resources:

kubectl get all
kubectl get deploy
🔹 Step 9: Restart Application (If Needed)
kubectl rollout restart deployment two-tier-app
🔹 Step 10: Delete Resources

Delete deployments and services:

kubectl delete deployment two-tier-app mysql
kubectl delete service two-tier-app-service mysql

Delete EKS cluster:

eksctl delete cluster --name reyaz-cluster --region ap-south-1
✅ Project Flow
EC2 Setup
   ↓
Install AWS CLI, kubectl, eksctl
   ↓
Create EKS Cluster
   ↓
Deploy MySQL
   ↓
Deploy Flask App
   ↓
Access Application
