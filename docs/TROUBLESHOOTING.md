# 🔧 Kubernetes Troubleshooting Guide

Common issues and solutions when working with the any Kubernetes project (Specifically added detailed command w.r.t this project).

## 🚨 Common Issues & Solutions

#### Problem: Container Crashes (CrashLoopBackOff)
```bash

# Describe resource
kubectl describe pod <pod-name> -n shopnow-priyankp2

# Check pod logs
kubectl logs <pod-name> -n shopnow-priyankp2 --previous

# Debug with interactive shell
kubectl exec -it <pod-name> -n shopnow-priyankp2 -- /bin/sh
```

### Networking Issues

#### Problem: Service Not Accessible
```bash
# Verify service endpoints
kubectl get endpoints -n shopnow-priyankp2

# Test service connectivity
kubectl run debug --image=busybox -it --rm -- nslookup backend.shopnow-priyankp2

# Check service configuration
kubectl describe service backend -n shopnow-priyankp2
```

#### Problem: Ingress Not Working
```bash
# Check ingress controller
kubectl get pods -n ingress-nginx

# Verify ingress resource
kubectl describe ingress shopnow-ingress -n shopnow-priyankp2

# Check ingress controller logs
kubectl logs -n ingress-nginx deployment/ingress-nginx-controller
```

### Storage Issues

#### Problem: Persistent Volume Claims Pending
```bash
# Check available storage classes
kubectl get storageclass

# Verify PVC status
kubectl describe pvc mongo-data-mongo-0 -n shopnow-priyankp2

# Check node storage capacity
kubectl describe nodes
```

### Configuration Issues

#### Problem: ConfigMap/Secret Not Loading
```bash
# Verify ConfigMap exists
kubectl get configmap -n shopnow-priyankp2

# Check secret data
kubectl get secret db-secret -n shopnow-priyankp2 -o yaml

# Validate environment variables in pod
kubectl exec <pod-name> -n shopnow-priyankp2 -- env | grep MONGODB
```

### Scaling Issues

#### Problem: HPA Not Scaling
```bash
# Check metrics server
kubectl top nodes
kubectl top pods -n shopnow-priyankp2

# Verify HPA status
kubectl describe hpa backend-hpa -n shopnow-priyankp2

# Check resource requests/limits
kubectl describe deployment backend -n shopnow-priyankp2
```

## 🔍 Debugging Commands

### Essential Debugging Toolkit
```bash
# Pod inspection
kubectl get pods -n shopnow-priyankp2 -o wide
kubectl describe pod <pod-name> -n shopnow-priyankp2
kubectl logs <pod-name> -n shopnow-priyankp2 --follow

# Service debugging
kubectl get svc -n shopnow-priyankp2
kubectl get endpoints -n shopnow-priyankp2

# Network debugging
kubectl exec -it <pod-name> -n shopnow-priyankp2 -- nslookup kubernetes.default
kubectl exec -it <pod-name> -n shopnow-priyankp2 -- wget -qO- http://backend:5000/health

# Resource monitoring
kubectl top pods -n shopnow-priyankp2
kubectl describe node <node-name>

# Event monitoring
kubectl get events -n shopnow-priyankp2 --sort-by='.lastTimestamp'
```

## 🛠 Helm Troubleshooting

### Common Helm Issues
```bash
# Check Helm release status
helm status shopnow-backend -n shopnow-priyankp2

# Debug template rendering
helm template shopnow-backend kubernetes/helm/charts/backend --debug

# Rollback failed deployment
helm rollback shopnow-backend 1 -n shopnow-priyankp2

# Check Helm history
helm history shopnow-backend -n shopnow-priyankp2
```

## 🔄 ArgoCD Troubleshooting

### ArgoCD Sync Issues
```bash
# Check application status
kubectl get applications -n argocd

# View application details
kubectl describe application shopnow-umbrella -n argocd

# Force sync
argocd app sync shopnow-umbrella

# Check ArgoCD server logs
kubectl logs -n argocd deployment/argocd-server
```

## 📊 Performance Troubleshooting

### Resource Monitoring
```bash
# Node resource usage
kubectl top nodes

# Pod resource usage
kubectl top pods -n shopnow-priyankp2

# Detailed resource metrics
kubectl describe node <node-name> | grep -A 5 "Allocated resources"
```

## 🚨 Emergency Procedures

### Cluster Recovery
```bash
# Check cluster health
kubectl get nodes
kubectl get pods --all-namespaces

# Restart failed deployments
kubectl rollout restart deployment/backend -n shopnow-priyankp2

# Emergency pod deletion
kubectl delete pod <pod-name> -n shopnow-priyankp2 --force --grace-period=0
```
