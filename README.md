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
![image_url](https://github.com/biswajit134/Kubernetes_node_app/blob/main/SS/Screenshot%202026-02-19%20160542.png?raw=true)


## c. Expose app using service.yaml.
![image_url]()
![image_url]()
![image_url]()
![image_url]()

## d. Use kubectl get pods to verify.
![image_url]()
![image_url]()
![image_url]()
![image_url]()
![image_url]()

## e. Scale deployments using kubectl scale.
![image_url]()
![image_url]()
![image_url]()
![image_url]()
![image_url]()
![image_url]()

## f. Use kubectl describe for logs.
![image_url]()
![image_url]()
![image_url]()
![image_url]()
![image_url]()
![image_url]()
![image_url]()
