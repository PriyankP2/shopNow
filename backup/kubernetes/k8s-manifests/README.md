# 📝 Raw Kubernetes Manifests

This directory contains raw YAML manifests for deploying the ShopNow application. Perfect for beginners to understand Kubernetes fundamentals.

## 🎯 Learning Objectives
- Understand Kubernetes resource definitions
- Learn YAML structure and syntax
- Practice kubectl commands
- Grasp resource relationships and dependencies

## 📁 Directory Structure

```
k8s-manifests/
├── namespace/          # Application namespace
├── database/          # MongoDB StatefulSet and storage
├── backend/           # API server deployment and services
├── frontend/          # React app deployment and services
├── admin/             # Admin dashboard deployment and services
├── ingress/           # External traffic routing
└── daemonsets-example/ # DaemonSet example for learning
```

## 🚀 Deployment Order (Important!)

Deploy in this order to avoid dependency issues:

### 1. Create Namespace
```bash
kubectl apply -f namespace/
```

### 2. Deploy Database (StatefulSet)
```bash
kubectl apply -f database/
# Wait for MongoDB to be ready
kubectl wait --for=condition=ready pod/mongo-0 -n shopnow-priyankp2 --timeout=300s
```

### 3. Deploy Backend API
```bash
kubectl apply -f backend/
# Wait for backend to be ready
kubectl wait --for=condition=available deployment/backend -n shopnow-priyankp2 --timeout=300s
```

### 4. Deploy Frontend Applications
```bash
kubectl apply -f frontend/
kubectl apply -f admin/
```

### 5. Configure External Access (Optional)
```bash
kubectl apply -f ingress/
```

## 🔍 Understanding Each Component

### Namespace (`namespace/namespace.yaml`)
Creates an isolated environment for the ShopNow application.
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: shopnow-priyankp2
  labels:
    app: shopnow
```

**Key Concepts:**
- Namespaces provide isolation
- Resources in different namespaces can't directly communicate
- Default namespace is used if none specified

### Database (`database/`)
- **StatefulSet**: For stateful applications that need persistent identity
- **Headless Service**: For direct pod-to-pod communication
- **PersistentVolumeClaim**: For data persistence

**Why StatefulSet vs Deployment?**
- StatefulSet: Ordered deployment, stable network identity, persistent storage
- Deployment: Stateless applications, any pod can handle any request

### Backend (`backend/`)
- **Deployment**: Manages multiple replicas of the API server
- **ConfigMap**: Environment variables and configuration
- **Secret**: Database credentials
- **Service**: ClusterIP, NodePort, and LoadBalancer options
- **HPA**: Horizontal Pod Autoscaler for automatic scaling

### Frontend & Admin (`frontend/`, `admin/`)
- **Deployment**: Serves the React applications
- **ConfigMap**: Nginx configuration for serving static files
- **Service**: Different service types for various access patterns

### Ingress (`ingress/`)
- Routes external HTTP/HTTPS traffic to services
- Provides load balancing and SSL termination
- Requires an Ingress Controller (like NGINX)

## 🛠 Common Operations

### Check Deployment Status
```bash
# Overview of all resources
kubectl get all -n shopnow-priyankp2

# Detailed pod information
kubectl get pods -n shopnow-priyankp2 -o wide

# Check service endpoints
kubectl get endpoints -n shopnow-priyankp2
```

### Debugging
```bash
# Pod logs
kubectl logs deployment/backend -n shopnow-priyankp2

# Pod details and events
kubectl describe pod <pod-name> -n shopnow-priyankp2

# Interactive shell
kubectl exec -it <pod-name> -n shopnow-priyankp2 -- /bin/bash
```

### Scaling
```bash
# Scale backend
kubectl scale deployment backend --replicas=3 -n shopnow-priyankp2

# Check HPA status
kubectl get hpa -n shopnow-priyankp2
```

### Access Applications
```bash
# Port forward to access locally
kubectl port-forward svc/frontend 3000:3000 -n shopnow-priyankp2
kubectl port-forward svc/backend 5000:5000 -n shopnow-priyankp2
kubectl port-forward svc/admin 3001:3001 -n shopnow-priyankp2

# If using NodePort services
kubectl get svc -n shopnow-priyankp2
# Access via http://<node-ip>:<node-port>
```
