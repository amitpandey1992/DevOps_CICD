# **Deploying a Dockerized Application to Amazon EKS (Using CLI)**

## **Prerequisites**
Before deploying your Dockerized application to Amazon EKS, ensure you have:
- An **AWS account** with the necessary permissions.
- **AWS CLI** installed and configured (`aws configure`).
- **kubectl** installed to interact with the EKS cluster.
- **eksctl** installed for easy cluster management.
- **Docker** installed for building and pushing images.
- **Helm** installed (optional, for managing deployments).

---

## **Step 1: Create an EKS Cluster**
Use `eksctl` to create a new EKS cluster:
```sh
eksctl create cluster --name boardgame-cluster --region us-east-1 --nodegroup-name standard-workers --node-type t3.medium --nodes 2 --nodes-min 1 --nodes-max 3 --managed
```
Verify the cluster is running:
```sh
aws eks update-kubeconfig --name boardgame-cluster --region us-east-1
kubectl get nodes
```

---

## **Step 2: Build and Push Docker Image to Amazon ECR**
### **1. Create an ECR Repository**
```sh
aws ecr create-repository --repository-name boardshack --region us-east-1
```
### **2. Authenticate Docker to AWS ECR**
```sh
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com
```
### **3. Build and Push Image**
```sh
docker build -t boardshack:latest .
docker tag boardshack:latest <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/boardshack:latest
docker push <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/boardshack:latest
```

---

## **Step 3: Deploy Application to EKS**
### **1. Create a Kubernetes Deployment YAML file (`deployment.yaml`)**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: boardshack-app
  labels:
    app: boardshack
spec:
  replicas: 2
  selector:
    matchLabels:
      app: boardshack
  template:
    metadata:
      labels:
        app: boardshack
    spec:
      containers:
        - name: boardshack
          image: <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/boardshack:latest
          ports:
            - containerPort: 80
```
Apply the deployment:
```sh
kubectl apply -f deployment.yaml
```

### **2. Create a Kubernetes Service YAML file (`service.yaml`)**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: boardshack-service
spec:
  type: LoadBalancer
  selector:
    app: boardshack
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```
Apply the service:
```sh
kubectl apply -f service.yaml
```
Check if the service has an external IP:
```sh
kubectl get svc
```
Once an external IP is assigned, access your application via:
```
http://<EXTERNAL-IP>
```

---

## **Step 4: Monitor and Scale Application**
### **1. Check Pod and Service Status**
```sh
kubectl get pods
kubectl get svc
kubectl describe svc boardshack-service
```
### **2. Scale the Deployment**
Increase replicas:
```sh
kubectl scale deployment boardshack-app --replicas=3
```
Verify scaling:
```sh
kubectl get pods
```

---

## **Step 5: Clean Up Resources**
To delete the deployment and service:
```sh
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
```
To delete the EKS cluster:
```sh
eksctl delete cluster --name boardgame-cluster
```

---

## **Conclusion**
This guide provides a step-by-step process to deploy a Dockerized application to Amazon EKS, including setting up the cluster, pushing the Docker image to ECR, deploying it on EKS, and exposing it using a LoadBalancer service.

---

## **Author**
Maintained by **[Your Name]**

## **License**
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

