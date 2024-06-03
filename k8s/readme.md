# Wanderlust Deployment on Kubernetes
In this project, we will learn about how to deploy wanderlust application on Kubernetes.

## Steps for Kubernetes deployment:
1. Clone code from remote repository (GitHub) :
```
git clone https://github.com/rootmeet/wanderlust.git
```
2. Create kubernetes namespace :
```
kubectl create namespace workshop
```
3. Create docker images and push to docker hub
    a. Environment variables:

    Change .env.sample commection at frontend
    VITE_API_PATH="http://192.168.68.88:31100" # this ip from worker node

    Change .env.sample connection at backend
    MONGODB_URI="mongodb://mongo-service/wanderlust"

    b. Docker build:
    ```
    docker build -t sanjeevrisbud/wanderlust-backend:latest .
    docker build -t sanjeevrisbud/wanderlust-frontend:latest .
    ```
    c. Push docker images to docker hub / ECR
    ```
    docker push sanjeevrisbud/wanderlust-backend:latest
    docker push sanjeevrisbud/wanderlust-frontend:latest
    ```
4. Enable DNS resolution on kubernetes cluster :
    a. Check coredns pod in kube-system namespace
    ```
    kubectl get pods -n kube-system -o wide | grep -i core
    ```
    b. Scale coredns pod on worker node as well for DNS resolution
    ```
    kubectl edit deploy coredns -n kube-system -o yaml
    ```
    Edit replica from 2 to 4

5. Deploy manifest files to kubernets cluster
```
kubectl apply -f persistentVolume.yml -n workshop
kubectl apply -f persistentVolumeClame.yml -n workshop
kubectl apply -f mongodb.yml -n workshop
kubectl apply -f backend-deployment.yml
kubectl apply -f frontend-deployment.yml
```
6. Get all objects inside workshop namespace
```
kubectl get all -n workshop
```

7. See the logs in case of deployment errors
```
kubectl describe pod mongo-deployment-588846c457-x4fr4 -n workshop
kubectl logs backend-deployment-86959b8f65-d82f5 -n workshop
```
