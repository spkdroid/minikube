# Kubernetes Setup on Windows Using Minikube

## Prerequisites
- Windows 10/11 (Pro, Enterprise, or Education) with Hyper-V enabled.
- **Windows Home users:** Install **Docker Desktop** with WSL 2 backend.
- Minimum **8GB RAM** and sufficient storage.
- Install **kubectl** and **Minikube**.

## Step 1: Install Minikube
1. Download and install Minikube:
   ```powershell
   choco install minikube -y
   ```
2. Verify installation:
   ```powershell
   minikube version
   ```

## Step 2: Install kubectl
1. Download `kubectl` from Kubernetes official site:
   ```powershell
   curl -LO "https://dl.k8s.io/release/v1.29.0/bin/windows/amd64/kubectl.exe"
   ```
2. Move it to a directory in the system PATH (e.g., `C:\kubectl`).
3. Add `C:\kubectl` to your PATH environment variable.
4. Verify installation:
   ```powershell
   kubectl version --client
   ```

## Step 3: Start Minikube
1. Start Minikube with Hyper-V:
   ```powershell
   minikube start --driver=hyperv
   ```
   If using Docker:
   ```powershell
   minikube start --driver=docker
   ```
2. Verify Minikube is running:
   ```powershell
   kubectl get nodes
   ```
   If you see a node with `Ready` status, Kubernetes is running.

## Step 4: Deploy a Simple Container in a Pod
If you want to deploy a simple standalone pod instead of using a Deployment:

1. Create a file `pod.yaml`:
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: nginx-pod
   spec:
     containers:
     - name: nginx
       image: nginx:latest
       ports:
       - containerPort: 80
   ```
2. Apply the pod configuration:
   ```powershell
   kubectl apply -f pod.yaml
   ```
3. Verify the pod is running:
   ```powershell
   kubectl get pods
   ```
4. To access the container inside the pod:
   ```powershell
   kubectl port-forward pod/nginx-pod 8080:80
   ```
   Then open **http://localhost:8080** in your browser.

## Step 5: Create a Deployment (For Scalability)
For better scalability and management, use a Deployment:

1. Create a file `deployment.yaml`:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: nginx
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:latest
           ports:
           - containerPort: 80
   ```
2. Apply the deployment:
   ```powershell
   kubectl apply -f deployment.yaml
   ```
3. Verify the pods are running:
   ```powershell
   kubectl get pods
   ```

## Step 6: Expose the Deployment
To access the deployed container, expose it as a service:
```powershell
kubectl expose deployment nginx-deployment --type=NodePort --port=80
```
Check the service:
```powershell
kubectl get services
```
Find the service URL:
```powershell
minikube service nginx-deployment --url
```
