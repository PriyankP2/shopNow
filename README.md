# 🛍️ ShopNow - MERN Stack CI/CD with Kubernetes & Helm

A production-grade deployment of a MERN stack e-commerce application demonstrating DevOps best practices with containerization, orchestration, and automated CI/CD pipelines.

![Architecture](https://img.shields.io/badge/Architecture-Microservices-blue)
![CI/CD](https://img.shields.io/badge/CI%2FCD-Jenkins-red)
![Container](https://img.shields.io/badge/Container-Docker-2496ED)
![Orchestration](https://img.shields.io/badge/Orchestration-Kubernetes-326CE5)
![Package Manager](https://img.shields.io/badge/Package%20Manager-Helm-0F1689)

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Architecture](#-architecture)
- [Technology Stack](#-technology-stack)
- [Project Structure](#-project-structure)
- [Implementation Details](#-implementation-details)
- [CI/CD Pipeline](#-cicd-pipeline)
- [Deployment Guide](#-deployment-guide)
- [Challenges & Solutions](#-challenges--solutions)
- [Key Learnings](#-key-learnings)
- [Future Enhancements](#-future-enhancements)

---

## 🎯 Overview

ShopNow is a full-stack e-commerce application deployed using modern DevOps practices. This project showcases:

- **Containerization** of all application components using Docker
- **Orchestration** with Kubernetes (Minikube) for scalability and resilience
- **Configuration management** using Helm charts for environment-agnostic deployments
- **Automated CI/CD** pipelines with Jenkins for seamless delivery
- **Secure image registry** integration with Amazon ECR

### Key Objectives

✅ Create production-ready Kubernetes manifests for all services  
✅ Build reusable Helm charts with parameterized configurations  
✅ Implement separate CI/CD pipelines for each microservice  
✅ Automate deployment with GitOps principles  
✅ Establish secure image management workflow  

---

## 🏗️ Architecture

### Infrastructure Design

The project uses a distributed architecture with separated concerns:

```
                ┌────────────────────────────────────────────┐
                │            Jenkins CI/CD Server            │
                │                 (EC2 #1)                   │
                │                                            │
                │  ┌──────────────────────────────────────┐  │
                │  │ CI Pipeline                          │  │
                │  │ - Checkout Code                      │  │
                │  │ - Build Docker Image                 │  │
                │  │ - Tag with Git SHA                   │  │
                │  │ - Push to Amazon ECR                 │  │
                │  └──────────────────────────────────────┘  │
                │                                            │
                │  ┌──────────────────────────────────────┐  │
                │  │ CD Pipeline                          │  │
                │  │ - SSH to K8s Server                  │  │
                │  │ - Helm Upgrade/Install               │  │
                │  └──────────────────────────────────────┘  │
                └──────────────┬─────────────────────────────┘
                               │
                               │ Docker Push
                               ▼
                  ┌────────────────────────────────┐
                  │        Amazon ECR              │
                  │   (Private Container Registry) │
                  │                                │
                  │  shopnow-backend:<git-sha>     │
                  │  shopnow-frontend:<git-sha>    │
                  │  shopnow-admin:<git-sha>       │
                  └──────────────┬─────────────────┘
                                 │
                                 │ Image Pull
                                 ▼
            ┌────────────────────────────────────────────┐
            │        Kubernetes Cluster (EC2 #2)         │
            │             Minikube + Helm                │
            │                                            │
            │  ┌──────────────────────────────────────┐  │
            │  │ Ingress Controller (NGINX)           │  │
            │  │ Routes:                              │  │
            │  │   /       → Frontend Service         │  │
            │  │   /admin  → Admin Service            │  │
            │  │   /api    → Backend Service          │  │
            │  └──────────────────────────────────────┘  │
            │                                            │
            │  ┌───────────────┐   ┌───────────────┐     │
            │  │ Frontend Pod  │   │ Admin Pod     │     │
            │  │ (React+Nginx) │   │ (React+Nginx) │     │
            │  └───────────────┘   └───────────────┘     │
            │                                            │
            │  ┌───────────────┐   ┌───────────────┐     │
            │  │ Backend Pods  │   │ MongoDB Pod   │     │
            │  │ (Node/Express)│   │ (Database)    │     │
            │  └───────────────┘   └───────────────┘     │
            └────────────────────────────────────────────┘

```

### Component Distribution

| Component          | Platform        | Purpose                          |
|--------------------|-----------------|----------------------------------|
| Jenkins Server     | EC2 Instance #1 | CI/CD orchestration             |
| Kubernetes Cluster | EC2 Instance #2 | Application runtime environment |
| Container Registry | Amazon ECR      | Secure image storage            |
| Source Code        | GitHub          | Version control                 |

### Deployment Flow

```
Developer Push (GitHub)
         ↓
Jenkins CI Pipeline Triggered
         ↓
Docker Image Build & Tag (Git SHA)
         ↓
Push to Amazon ECR
         ↓
Trigger CD Pipeline
         ↓
SSH to Kubernetes Server
         ↓
Helm Upgrade with New Image Tag
         ↓
Rolling Update in Kubernetes
         ↓
Application Live ✅
```

---

## 🔧 Technology Stack

### Application Layer

| Component | Technology       | Replicas | Exposure    |
|-----------|------------------|----------|-------------|
| Frontend  | React + Nginx    | 1        | Ingress (/) |
| Admin     | React + Nginx    | 2        | Ingress (/admin) |
| Backend   | Node.js + Express| 2        | Ingress (/api) |
| Database  | MongoDB          | 1        | Internal    |

### DevOps Toolchain

- **Containerization**: Docker
- **Orchestration**: Kubernetes (Minikube)
- **Package Management**: Helm 3
- **CI/CD**: Jenkins with Pipeline as Code
- **Container Registry**: Amazon ECR
- **Ingress Controller**: NGINX
- **Version Control**: Git (GitHub)

---

## 📁 Project Structure

```
shopNow/
├── admin/                      # Admin panel React application
├── backend/                    # Node.js API server
├── frontend/                   # Customer-facing React app
├── kubernetes/                 # Raw Kubernetes manifests
│   ├── namespace.yaml
│   ├── frontend-deployment.yaml
│   ├── backend-deployment.yaml
│   ├── admin-deployment.yaml
│   ├── mongo-deployment.yaml
│   ├── services.yaml
│   └── ingress.yaml
├── helm/                       # Helm chart for ShopNow
│   ├── Chart.yaml
│   ├── values.yaml
│   └── templates/
│       ├── backend-deployment.yaml
│       ├── frontend-deployment.yaml
│       ├── admin-deployment.yaml
│       ├── mongo-deployment.yaml
│       ├── services.yaml
│       └── ingress.yaml
├── jenkins/                    # CI/CD pipeline definitions
│   ├── Jenkinsfile.ci.backend
│   ├── Jenkinsfile.cd.backend
│   ├── Jenkinsfile.ci.frontend
│   ├── Jenkinsfile.cd.frontend
│   ├── Jenkinsfile.ci.admin
│   └── Jenkinsfile.cd.admin
├── docker-compose.yml          # Local development setup
└── README.md
```

---

## ⚙️ Implementation Details

### Kubernetes Configuration

#### Namespace Isolation

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: shopnow
```

All resources are deployed in the `shopnow` namespace for logical separation.

#### Service Architecture

All services use `ClusterIP` type for internal communication, with external access managed through the Ingress controller:

| Service  | Type       | Port | Target Port |
|----------|------------|------|-------------|
| frontend | ClusterIP  | 80   | 3000        |
| backend  | ClusterIP  | 5000 | 5000        |
| admin    | ClusterIP  | 80   | 3001        |
| mongo    | ClusterIP  | 27017| 27017       |

#### Ingress Configuration

NGINX Ingress Controller was installed using:

```bash
minikube addons enable ingress
```

**Ingress Routes:**

| Path      | Service  | Description           |
|-----------|----------|-----------------------|
| `/`       | frontend | Customer-facing app   |
| `/admin`  | admin    | Admin dashboard       |
| `/api`    | backend  | API endpoints         |

This production-style setup eliminates the need for NodePort services and provides clean URL routing.

### Helm Implementation

A Helm chart was created for parameterized deployments, enabling environment-agnostic configurations.

#### Helm Chart Structure

```
helm/
├── Chart.yaml
├── values.yaml
└── templates/
    ├── backend-deployment.yaml
    ├── frontend-deployment.yaml
    ├── admin-deployment.yaml
    ├── mongo-deployment.yaml
    ├── services.yaml
    └── ingress.yaml
```

#### Helm Upgrade Command Used in CD

```bash
helm upgrade --install shopnow . \
  -n shopnow \
  --set backend.image.tag=<git_sha>
```

**Why This Approach?**

This enables dynamic versioning using Git commit SHA, which provides:
- **Immutable deployments**: Each commit creates a unique image
- **Easy rollbacks**: Previous versions remain in ECR
- **Traceability**: Image tags map directly to Git commits
- **Automated versioning**: No manual version management needed

**Example Deployment:**

```bash
# During CD pipeline execution
IMAGE_TAG=$(git rev-parse --short HEAD)  # e.g., "a3c5d7f"

helm upgrade --install shopnow ./helm \
  --namespace shopnow \
  --create-namespace \
  --set backend.image.tag=${IMAGE_TAG} \
  --set frontend.image.tag=${IMAGE_TAG} \
  --set admin.image.tag=${IMAGE_TAG}
```

---

## 🔄 CI/CD Pipeline

### Pipeline Architecture

The project implements **separated CI/CD pipelines** for each microservice, following the single responsibility principle:

- **3 CI Pipelines**: Build and push images
- **3 CD Pipelines**: Deploy to Kubernetes

### CI Pipeline Flow

**Files**: `Jenkinsfile.ci.backend`, `Jenkinsfile.ci.frontend`, `Jenkinsfile.ci.admin`

```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            // Clone repository
        }
        
        stage('Generate Image Tag') {
            // IMAGE_TAG = Git commit SHA (7 chars)
        }
        
        stage('Build Docker Image') {
            // docker build -t <ECR_URI>:<IMAGE_TAG>
        }
        
        stage('Login to ECR') {
            // aws ecr get-login-password
        }
        
        stage('Push to ECR') {
            // docker push <ECR_URI>:<IMAGE_TAG>
        }
        
        stage('Trigger CD Pipeline') {
            // Automatically trigger deployment
        }
    }
}
```

**Image Naming Convention:**
```
975050024946.dkr.ecr.us-west-1.amazonaws.com/shopnow-backend:a3c5d7f
                                                              └─ Git SHA
```

### CD Pipeline Flow

**Files**: `Jenkinsfile.cd.backend`, `Jenkinsfile.cd.frontend`, `Jenkinsfile.cd.admin`

```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout Helm Chart') {
            // Clone repository
        }
        
        stage('Extract Image Tag') {
            // Get Git SHA from previous build
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([sshUserPrivateKey(...)]) {
                    sh '''
                        ssh -i $SSH_KEY user@k8s-server \
                        "cd /path/to/helm && \
                         helm upgrade --install shopnow . \
                         --namespace shopnow \
                         --set backend.image.tag=${IMAGE_TAG}"
                    '''
                }
            }
        }
        
        stage('Verify Deployment') {
            // kubectl rollout status
        }
    }
}
```

### Security Measures

✅ **ECR Authentication**: Automated using AWS CLI  
✅ **Private Repositories**: All images stored in private ECR repos  
✅ **Image Pull Secrets**: Kubernetes secrets for ECR access  
✅ **SSH Key Management**: Jenkins credentials plugin  
✅ **MongoDB Authentication**: Username/password via Kubernetes secrets  
✅ **Secrets Encryption**: Kubernetes native secret management  

---

## 🚀 Deployment Guide

### Prerequisites

- AWS account with ECR access
- Two EC2 instances (t2.medium recommended)
- Jenkins installed on Instance #1
- Minikube + kubectl installed on Instance #2
- Helm 3.x installed
- GitHub repository access

### Step-by-Step Deployment

#### 1. Configure Jenkins

```bash
# Install required plugins
- Docker Pipeline
- AWS Credentials
- SSH Agent
- Git

# Configure credentials
- AWS credentials for ECR
- SSH key for K8s server
- GitHub credentials
```

#### 2. Setup Kubernetes Cluster

```bash
# On EC2 Instance #2
minikube start --driver=docker --memory=4096

# Enable Ingress
minikube addons enable ingress

# Create namespace
kubectl create namespace shopnow

# Create ECR pull secret
kubectl create secret docker-registry ecr-secret \
  --docker-server=<ECR_URI> \
  --docker-username=AWS \
  --docker-password=$(aws ecr get-login-password --region us-west-1) \
  --namespace=shopnow
```

#### 3. Configure MongoDB

```bash
# Create MongoDB secret
kubectl create secret generic mongo-secret \
  --from-literal=username=shopuser \
  --from-literal=password=<PASSWORD> \
  --namespace=shopnow
```

#### 4. Deploy Application

```bash
# Clone repository
git clone <REPO_URL>
cd shopNow/helm

# Deploy using Helm
helm install shopnow . --namespace shopnow
```

#### 5. Verify Deployment

```bash
# Check pods
kubectl get pods -n shopnow

# Check services
kubectl get svc -n shopnow

# Check ingress
kubectl get ingress -n shopnow

# Get Minikube IP
minikube ip
```

#### 6. Access Application

Get the Minikube IP address:

```bash
minikube ip
# Example output: 192.168.49.2
```

**🌐 Access the Apps:**

- **Customer App** → `http://<EC2 #2 IP>`
  <img width="1913" height="1027" alt="image" src="https://github.com/user-attachments/assets/62220b1c-ea5f-4d33-b157-47e560b258f1" />
  
- **Admin Dashboard** → `http://<EC2 #2 IP>/admin`  
  <img width="1913" height="981" alt="image" src="https://github.com/user-attachments/assets/7e84865f-db88-4e7e-b894-39f8489b21a3" />

- **API Health Check** → `http://<EC2 #2 IP>/api/health`
  <img width="1913" height="1025" alt="image" src="https://github.com/user-attachments/assets/9d401151-bb4d-426c-b08c-a6e6d19dcc4c" />

> **Note**: Replace `<EC2 #2 IP>` with your actual EC2 instance public IP.

**Example URLs:**

```
http://54.241.235.66/           # Frontend
http://54.241.235.66/admin      # Admin Panel
http://54.241.235.66/api/health # Backend Health Check
```

**Testing the Deployment:**

```bash
# Test frontend
curl http://<MINIKUBE_IP>/

# Test admin
curl http://<MINIKUBE_IP>/admin

# Test backend API
curl http://<MINIKUBE_IP>/api/health

# Expected response from health endpoint:
# {"status": "OK", "timestamp": "2024-02-16T10:30:00.000Z"}
```

---

## 🛠️ Challenges & Solutions

### Challenge 1: Minikube Networking on EC2

**Problem**: NodePort services were not accessible from outside the EC2 instance.

**Root Cause**: Security group restrictions and Minikube's isolated network.

**Solution**: 
- Implemented NGINX Ingress Controller for production-style routing
- Configured ingress rules for path-based routing
- Updated security groups to allow traffic on port 80/443

```bash
minikube addons enable ingress
```

### Challenge 2: MongoDB Authentication Errors

**Problem**: Backend pod logs showed `MongoError: Authentication failed`.

**Root Cause**: Incorrect connection string format and missing authentication database.

**Solution**:
- Created MongoDB user with proper roles:
  ```javascript
  db.createUser({
    user: "shopuser",
    pwd: "password",
    roles: [{role: "readWrite", db: "shopnow"}]
  })
  ```
- Updated connection string:
  ```
  mongodb://shopuser:password@mongo:27017/shopnow?authSource=admin
  ```

### Challenge 3: Jenkins SSH Agent Errors

**Problem**: `sshagent` plugin caused cryptographic library errors on Jenkins.

**Error Message**: `libcrypto.so.1.1: cannot open shared object file`

**Solution**:
- Replaced `sshagent` with `withCredentials` approach:
  ```groovy
  withCredentials([sshUserPrivateKey(credentialsId: 'k8s-ssh', keyFileVariable: 'SSH_KEY')]) {
      sh 'ssh -i $SSH_KEY user@k8s-server "helm upgrade ..."'
  }
  ```

### Challenge 4: Image Pull Errors

**Problem**: Pods stuck in `ImagePullBackOff` state.

**Root Cause**: ECR credentials expired after 12 hours.

**Solution**:
- Created automated credential refresh job in Jenkins
- Used Kubernetes CronJob to refresh ECR secret:
  ```yaml
  apiVersion: batch/v1
  kind: CronJob
  metadata:
    name: ecr-cred-refresh
  spec:
    schedule: "0 */6 * * *"
    jobTemplate:
      spec:
        template:
          spec:
            containers:
            - name: refresh
              image: amazon/aws-cli
              command: ["/bin/sh", "-c"]
              args:
                - kubectl create secret docker-registry ecr-secret --dry-run=client -o yaml | kubectl apply -f -
  ```

---

## 🎓 Key Learnings

### DevOps Principles Applied

1. **Infrastructure as Code**: All configurations versioned in Git
2. **Immutable Deployments**: Docker images tagged with Git SHA
3. **Separation of Concerns**: Distinct CI and CD pipelines
4. **GitOps Workflow**: Kubernetes state derived from Git repository
5. **Security Best Practices**: Secrets management and private registries
6. **Scalability**: Horizontal pod autoscaling ready architecture
7. **Observability**: Health checks and readiness probes implemented

### Technical Skills Developed

- Container lifecycle management from build to runtime
- Kubernetes resource orchestration and networking
- Helm chart templating and value overrides
- Jenkins pipeline scripting (declarative syntax)
- AWS ECR integration and authentication
- SSH-based remote deployment strategies
- Ingress controller configuration for routing
- MongoDB authentication in containerized environments

### Production Readiness Aspects

✅ Rolling updates with zero downtime  
✅ Resource limits and requests defined  
✅ Liveness and readiness probes configured  
✅ Multi-replica deployments for high availability  
✅ Centralized logging capability (future enhancement)  
✅ Monitoring hooks ready (Prometheus compatible)  

---

## 🔮 Future Enhancements

### Planned Improvements

- [ ] **Monitoring & Observability**
  - Integrate Prometheus + Grafana for metrics
  - Add ELK stack for centralized logging
  - Implement distributed tracing with Jaeger

- [ ] **High Availability**
  - Multi-node Kubernetes cluster
  - MongoDB replica set
  - Redis for session management

- [ ] **Security Hardening**
  - Network policies for pod-to-pod communication
  - OPA (Open Policy Agent) for governance
  - Vault integration for secrets management

- [ ] **Advanced Deployment Strategies**
  - Blue-Green deployments
  - Canary releases with traffic splitting
  - Automated rollback on health check failure

- [ ] **Performance Optimization**
  - Horizontal Pod Autoscaler (HPA) configuration
  - CDN integration for static assets
  - Database query optimization and indexing

- [ ] **Testing Integration**
  - Unit test execution in CI pipeline
  - Integration tests before deployment
  - Automated smoke tests post-deployment

---

## 📊 Project Metrics

| Metric                    | Value          |
|---------------------------|----------------|
| Total Deployments         | 6 (3 apps × 2) |
| Average Build Time        | ~3-5 minutes   |
| Deployment Time           | ~1-2 minutes   |
| Total Containers          | 6 pods         |
| Kubernetes Resources      | 15+ objects    |
| Lines of Pipeline Code    | ~400           |
| Helm Template Files       | 6              |

---

## 🤝 Contributing

This is a learning project demonstrating DevOps practices. Suggestions for improvements are welcome!

### Areas for Contribution

- Additional monitoring dashboards
- Alternative deployment strategies
- Security enhancements
- Documentation improvements

---

## 📝 License

This project is created for educational purposes as part of a DevOps training program.

---

## 🙏 Acknowledgments

- Jenkins community for comprehensive pipeline documentation
- Kubernetes documentation and tutorials
- Helm chart best practices guides
- DevOps mentors and course instructors

---

<div align="center">

**Built with ❤️ using DevOps best practices**

[⬆ Back to Top](#-shopnow---mern-stack-cicd-with-kubernetes--helm)

</div>
