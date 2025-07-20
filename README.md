# 🚀 Kubernetes Web App Deployment on AWS EKS

**Submitted by:** Nikita Goykar  
**Platform Used:** Amazon EKS (Multi-node Cluster)  
**Region:** ap-south-1 (Mumbai)  
**Kubernetes Version:** v1.32.x

---

## ✅ Assignment Objective

Deploy a simple NGINX web application on a Kubernetes cluster hosted on **AWS EKS**, ensuring proper exposure using **Service** or **Ingress**.

---

## 📁 Project Structure

k8s-deployment/
├── deployment.yaml
├── service.yaml
├── ingress.yaml # optional
├── README.md
└── screenshots/ # folder for images



---

## ⚙️ EKS Cluster Configuration

| Parameter             | Value               |
|----------------------|---------------------|
| Cluster Name         | eks-dev-cluster     |
| Node Group           | 3 x t3.medium EC2   |
| Region               | ap-south-1          |
| Version              | Kubernetes v1.32.x  |

---

## 📄 Kubernetes Manifests

### 1. Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
        - name: webapp
          image: nginx:latest
          ports:
            - containerPort: 80

2. Service

apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: LoadBalancer
  selector:
    app: webapp
  ports:
    - port: 80
      targetPort: 80


3. Ingress

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: webapp-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: webapp-service
                port:
                  number: 80


🧑‍💻 Commands Used

# Configure kubectl to connect to EKS
aws eks --region ap-south-1 update-kubeconfig --name eks-dev-cluster

# Verify nodes
kubectl get nodes

# Apply Kubernetes manifests
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml   # optional

# Verify all resources
kubectl get all
kubectl describe svc webapp-service
kubectl get ingress


📷 Included Screenshots
All screenshots are placed in the screenshots/ folder:

✅ AWS Console: EKS cluster view

✅ kubectl get nodes (3 nodes in Ready state)

✅ kubectl get deployments and get pods (Running)

✅ kubectl get svc showing LoadBalancer with EXTERNAL-IP

✅ Browser view of the deployed NGINX web app

✅ kubectl get ingress output


🧰 EKS Cluster Setup Guide
Step 1: Install eksctl

curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" -o eksctl.tar.gz
tar -xzf eksctl.tar.gz
sudo mv eksctl /usr/local/bin
eksctl version

Step 2: IAM Permissions
Ensure the IAM user/role has the following policies attached:

AmazonEKSClusterPolicy

AmazonEKSWorkerNodePolicy

AmazonEC2FullAccess

AmazonS3FullAccess

IAMFullAccess

Step 3: Create EKS Cluster

eksctl create cluster \
  --name eks-dev-cluster \
  --region ap-south-1 \
  --nodegroup-name dev-nodes \
  --node-type t3.medium \
  --nodes 3 \
  --managed

Step 4: Associate OIDC Provider

eksctl utils associate-iam-oidc-provider \
  --region ap-south-1 \
  --cluster eks-dev-cluster \
  --approve


Step 5: Install kubectl

curl -LO "https://dl.k8s.io/release/v1.30.1/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client


Step 6: Configure kubeconfig

aws eks --region ap-south-1 update-kubeconfig --name eks-dev-cluster
kubectl get nodes

Step 7: Cleanup
eksctl delete cluster --region ap-south-1 --name eks-dev-cluster

