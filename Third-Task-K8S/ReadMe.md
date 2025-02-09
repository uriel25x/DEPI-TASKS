---

# Kubernetes Theory and Practical Task

This repository provides an overview of key concepts and practical tasks related to Kubernetes, including architecture, deployments, services, scaling, and autoscaling.

## Table of Contents
1. [Theory Questions](#theory-questions)
    - [Kubernetes Architecture](#kubernetes-architecture)
    - [Pods in Kubernetes](#what-is-a-pod-in-kubernetes)
    - [Deployments and Services](#deployments-and-services)
    - [Scaling and Autoscaling](#scaling-and-autoscaling)
2. [Practical Task](#practical-task)
    - [Create a Deployment](#create-a-deployment)
    - [Service Exposure](#service-exposure)
    - [Scaling with Autoscaling](#scaling-with-autoscaling)

## Theory Questions

### Kubernetes Architecture
The core components of a Kubernetes cluster include:

#### Master Node:
- **kube-apiserver**: Serves the Kubernetes API and is the central point for communication between all components.
- **etcd**: A distributed key-value store used for storing cluster configuration data and state information.
- **kube-scheduler**: Watches for newly created pods and assigns them to nodes based on available resources and policies.
- **kube-controller-manager**: Runs controllers that ensure the desired state of the cluster (like replication, scaling, etc.) is met.

#### Node (Worker Node):
- **kubelet**: Ensures that containers are running in the pods on the node and communicates with the master node.
- **kube-proxy**: Manages network routing, ensuring that communication between services and pods is correctly routed.
- **Container Runtime**: Software like Docker or containerd used to run containers on the node.

### What is a pod in Kubernetes?
A **pod** is the smallest deployable unit in Kubernetes, representing a single instance of a running process in the cluster. A pod can contain one or more containers that share the same network namespace and storage.

### Deployments and Services

#### Purpose of a Kubernetes Deployment:
A **deployment** in Kubernetes manages a set of replicas of a pod. It ensures that the desired number of pods are running and allows for easy updates and rollbacks. Deployments ensure high availability by automatically creating new pods to replace failed ones and rolling out updates without downtime.

#### Types of Kubernetes Services:
1. **ClusterIP**: Exposes the service only inside the cluster. Used for internal communication.
2. **NodePort**: Exposes the service on a static port on each node’s IP, allowing external access via the node’s external IP address.
3. **LoadBalancer**: Exposes the service externally and provisions a cloud load balancer, routing traffic to the appropriate pod.

#### When to use each service type:
- **ClusterIP**: For internal service communication within the cluster.
- **NodePort**: For external access without a full-fledged load balancer.
- **LoadBalancer**: For exposing services to the outside world with high availability.

### Scaling and Autoscaling

#### How Kubernetes Handles Scaling:
Kubernetes supports manual and automated scaling through the **Horizontal Pod Autoscaler (HPA)**. The HPA automatically adjusts the number of pods based on observed metrics like CPU utilization.

---

## Practical Task

### Create a Deployment
1. Create a file named `web-server-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-server
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-server
  template:
    metadata:
      labels:
        app: web-server
    spec:
      containers:
      - name: web-server
        image: uriel25x/web-server
        ports:
        - containerPort: 80
```

2. Create a **ClusterIP** service to load balance across the replicas. Create a file named `web-server-service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-server-service
spec:
  selector:
    app: web-server
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

3. Apply the deployment and service:

```bash
kubectl apply -f web-server-deployment.yaml
kubectl apply -f web-server-service.yaml
```

4. Test load balancing by sending requests and checking logs:

```bash
curl web-server-service:80
kubectl logs <pod-name>
```

### Service Exposure
Expose the deployment via a **NodePort** service:
1. Create a file named `web-server-nodeport.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-server-nodeport
spec:
  selector:
    app: web-server
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30080
  type: NodePort
```

2. Apply the NodePort service:

```bash
kubectl apply -f web-server-nodeport.yaml
```

3. Verify the service and access it using the node’s external IP:

```bash
kubectl get nodes -o wide
```
Access the service in a browser: `http://<node-ip>:30080`.

### Scaling with Autoscaling
1. Set up the **Horizontal Pod Autoscaler (HPA)**:

```bash
kubectl autoscale deployment web-server --cpu-percent=70 --min=3 --max=10
```

2. Simulate high CPU usage:

```bash
kubectl run -i --tty load-generator --image=busybox /bin/sh
stress --cpu 2 --timeout 60s
```

3. Monitor scaling behavior:

```bash
kubectl get hpa
kubectl get pods
```

---
