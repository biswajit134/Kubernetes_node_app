# Build a Kubernetes Cluster Locally with Minikube 
* Objective: Deploy and manage apps in Kubernetes.
* Tools: Minikube, kubectl, Docker
* Deliverables: YAML files, screenshots of pods/services

## Hints:
### a. Install Minikube & a. start the cluster.

![image_url](https://github.com/biswajit134/Kubernetes_node_app/blob/main/SS/Screenshot%202026-02-19%20114307.png?raw=true)
![image_url](https://github.com/biswajit134/Kubernetes_node_app/blob/main/SS/Screenshot%202026-02-19%20123653.png?raw=true)
![image_url](https://github.com/biswajit134/Kubernetes_node_app/blob/main/SS/Screenshot%202026-02-19%20130920.png?raw=true)
![image_url](https://github.com/biswajit134/Kubernetes_node_app/blob/main/SS/Screenshot%202026-02-19%20132421.png?raw=true)

## b. Create deployment.yaml for an app.
deployment.yml :
```
# For create namespace
kind: Namespace
apiVersion: v1
metadata:
  name: nodejs-demo-app
---
# For create deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nodejs-demo-app-deployment
  namespace: nodejs-demo-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nodejs-demo-app
  template:
    metadata:
      name: nodejs-demo-app-deployment-pod
      labels:
        app: nodejs-demo-app
    spec:
      containers:
      - name: nodejs-demo-app
        image: biswajit134/nodejs-demo-app:latest

        ports:
        - containerPort: 80
```
```
kubectl apply -f deployment.yml
```
```
kubectl get deployment -n nodejs-demo-app
```
![image_url](https://github.com/biswajit134/Kubernetes_node_app/blob/main/SS/Screenshot%202026-02-19%20160542.png?raw=true)


## c. Expose app using service.yaml.
service.yml :
```
apiVersion: v1
kind: Service
metadata:
  name: nodejs-demo-app-service
  namespace: nodejs-demo-app
spec:
  selector:
    app: nodejs-demo-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```
```
kubectl apply -f service.yml
```
```
kubectl get svc -n nodejs-demo-app
```
```
sudo -E kubectl port-forward service/nodejs-demo-app-service -n nodejs-demo-app 80:80 --address=0.0.0.0
```
![image_url](https://github.com/biswajit134/Kubernetes_node_app/blob/main/SS/Screenshot%202026-02-19%20161705.png?raw=true)

## d. Use kubectl get pods to verify.
```
kubectl get pods -n nodejs-demo-app
```

![image_url](https://github.com/biswajit134/Kubernetes_node_app/blob/main/SS/Screenshot%202026-02-19%20162253.png?raw=true)


## e. Scale deployments using kubectl scale.

```
kubectl scale deployment/nodejs-demo-app-deployment -n nodejs-demo-app --replicas=6 deployment.apps nodejs-demo-app-deployment scaled
```
![image_url](https://github.com/biswajit134/Kubernetes_node_app/blob/main/SS/Screenshot%202026-02-19%20162253.png?raw=true)


## f. Use kubectl describe for logs.
```
kubectl describe deployment/nodejs-demo-app-deployment -n nodejs-demo-app
```
![image_url](https://github.com/biswajit134/Kubernetes_node_app/blob/main/SS/Screenshot%202026-02-19%20162553.png?raw=true)
