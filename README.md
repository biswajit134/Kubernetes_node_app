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


## Interview Questions:

**1. What is Kubernetes?**
Kubernetes (often abbreviated as K8s) is an open-source container orchestration platform. It automates the deployment, scaling, and management of containerized applications across a cluster of nodes. Essentially, it handles the operational complexity of running thousands of containers in production.

**2. What is the role of kubelet?**
The kubelet is the primary "node agent" that runs on every worker node in a Kubernetes cluster. Its main job is to ensure that containers are running in a Pod as expected. It registers the node with the control plane, receives Pod specifications (usually from the API server), and interacts with the container runtime (like containerd or Docker) to start, stop, and monitor the containers.

**3. Explain Pods, Deployments, and Services.**
These are the foundational building blocks of applications in Kubernetes:

* Pod: The smallest and simplest deployable unit in Kubernetes. A Pod encapsulates one or more containers that share the same network namespace (they share an IP address and port space) and storage volumes.

* Deployment: A higher-level controller that manages the declarative updates of Pods and ReplicaSets. You tell a Deployment what state you want (e.g., "I need 3 replicas of my web app running version 2.0"), and the Deployment automatically creates, updates, or replaces Pods to match that desired state.

* Service: Because Pods are ephemeral (they are created and destroyed frequently, changing their IP addresses), a Service provides a stable, permanent IP address and DNS name. It acts as an internal load balancer, routing traffic to the correct set of underlying Pods based on label selectors.

**4. How do you scale in Kubernetes?**
Scaling in Kubernetes can be handled both manually and automatically at different levels:

* Manual Scaling: You can manually increase or decrease the number of Pod replicas by running kubectl scale deployment <name> --replicas=<number>.

* Horizontal Pod Autoscaler (HPA): Automatically scales the number of Pod replicas up or down based on observed metrics, such as CPU utilization, memory usage, or custom application metrics.

* Vertical Pod Autoscaler (VPA): Automatically adjusts the CPU and memory resource limits and requests for containers within existing Pods based on historical usage.

* Cluster Autoscaler: Automatically adds or removes physical/virtual worker nodes in the cluster based on whether there are Pods in a "pending" state due to insufficient cluster resources.

**5. What is a namespace?**
A namespace provides a mechanism for isolating groups of resources within a single Kubernetes cluster. Namespaces are typically used in environments with many users spread across multiple teams, or to separate different environments (such as dev, staging, and production). It prevents naming collisions, as resource names only need to be unique within a namespace.

**6. Difference between ClusterIP, NodePort, LoadBalancer.**
These are the three primary types of Kubernetes Services, each dictating how traffic is routed to your Pods:

* ClusterIP (Default): Exposes the Service on a cluster-internal IP address. The Service is only accessible from within the Kubernetes cluster.

* NodePort: Exposes the Service on each worker Node's IP address at a static, high-numbered port (usually between 30000-32767). It allows external traffic to access the service by requesting <NodeIP>:<NodePort>.

* LoadBalancer: Exposes the Service externally using a cloud provider's physical load balancer (like AWS ALB or Azure Load Balancer). It automatically provisions the external load balancer, which then routes traffic to the automatically created NodePort and ClusterIPs.

**7. What are ConfigMaps?**
ConfigMaps are API objects used to store non-confidential configuration data in key-value pairs. They allow you to decouple environment-specific configuration artifacts from your container image content. By using ConfigMaps, you can keep your container images highly portable, injecting different configuration files, command-line arguments, or environment variables depending on the environment (e.g., dev vs. prod).

**8. How do you perform rolling updates?**
A rolling update allows you to update a Deployment with zero application downtime by incrementally replacing old Pod instances with new ones.

You trigger a rolling update simply by updating the container image version in your Deployment manifest (e.g., running kubectl set image deployment/<name> <container>=<new-image>). Kubernetes will automatically start creating new Pods while simultaneously taking down the old ones, governed by the maxSurge (maximum number of extra pods created during the update) and maxUnavailable (maximum number of pods that can be offline) settings to ensure service continuity.
