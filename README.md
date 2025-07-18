# k8s-app-deploy

A complete CI/CD pipeline for deploying Node.js applications to Kubernetes using Argo CD and GitHub Actions.

## 🏗️ Architecture Overview

This project implements a GitOps-based CI/CD pipeline with the following components:

- **GitHub Actions**: Automated CI/CD pipeline triggered on branch pushes
- **Argo CD**: GitOps continuous deployment to Kubernetes
- **Multi-Environment**: Dev, Staging, and Production environments
- **Container Registry**: GitHub Container Registry (GHCR)
- **Kubernetes**: Container orchestration platform

## 🌿 Branch Strategy

- `develop` → Dev environment deployment
- `staging` → Staging environment deployment  
- `main` → Production environment deployment

## 🚀 CI/CD Pipeline

### Workflow Triggers

The pipeline is triggered on pushes to:
- `main` (Production)
- `staging` (Staging)
- `develop` (Development)

### Pipeline Steps

1. **Environment Detection**: Determines target environment based on branch
2. **Build & Push**: Builds Docker image and pushes to GHCR
3. **Deploy**: Updates Kubernetes manifests and syncs Argo CD applications
4. **Notify**: Reports deployment status

## 📁 Project Structure

```
├── .github/workflows/
│   └── deploy.yml              # GitHub Actions CI/CD pipeline
├── argocd/applications/
│   ├── dev-app.yaml           # Argo CD app for dev environment
│   ├── stage-app.yaml         # Argo CD app for staging environment
│   └── prod-app.yaml          # Argo CD app for production environment
├── k8s/
│   ├── dev/
│   │   ├── deployment.yaml    # Dev deployment manifest
│   │   └── service.yaml       # Dev service manifest
│   ├── stage/
│   │   ├── deployment.yaml    # Staging deployment manifest
│   │   └── service.yaml       # Staging service manifest
│   └── prod/
│       ├── deployment.yaml    # Production deployment manifest
│       └── service.yaml       # Production service manifest
├── Dockerfile                 # Container image definition
├── package.json              # Node.js application dependencies
├── index.js                  # Express.js application
└── README.md                 # This file
```

## 🔧 Setup Instructions

### Prerequisites

- Kubernetes cluster
- Argo CD installed in the cluster
- GitHub repository with proper permissions
- Container registry access (GHCR)

### Required GitHub Secrets

Set up the following secrets in your GitHub repository:

```bash
KUBE_CONFIG          # Base64 encoded kubeconfig file
ARGOCD_SERVER        # Argo CD server URL (e.g., argocd.example.com)
ARGOCD_AUTH_TOKEN    # Argo CD authentication token
```

### Environment Configuration

Each environment has different resource allocations:

| Environment | Replicas | CPU Request | Memory Request | CPU Limit | Memory Limit |
|-------------|----------|-------------|----------------|-----------|-------------|
| Dev         | 2        | 50m         | 64Mi           | 100m      | 128Mi       |
| Staging     | 3        | 100m        | 128Mi          | 200m      | 256Mi       |
| Production  | 5        | 200m        | 256Mi          | 500m      | 512Mi       |

## 🚢 Deployment Process

### 1. Code Push
```bash
git push origin develop    # Deploys to dev
git push origin staging    # Deploys to staging
git push origin main       # Deploys to production
```

### 2. Automatic Pipeline
- GitHub Actions builds and pushes Docker image
- Updates Kubernetes manifests with new image tag
- Applies Argo CD applications to cluster
- Syncs applications to deploy changes

### 3. Manual Argo CD Operations
```bash
# Create applications manually if needed
kubectl apply -f argocd/applications/dev-app.yaml
kubectl apply -f argocd/applications/stage-app.yaml
kubectl apply -f argocd/applications/prod-app.yaml

# Sync applications
argocd app sync k8s-app-dev
argocd app sync k8s-app-stage
argocd app sync k8s-app-prod
```

## 🔍 Application Endpoints

- **Health Check**: `GET /health`
- **Root**: `GET /`
- **Status**: `GET /api/status`

## 🛠️ Local Development

### Run Locally
```bash
npm install
npm start
```

### Build Docker Image
```bash
docker build -t k8s-app-deploy .
docker run -p 3000:3000 k8s-app-deploy
```

### Test Health Check
```bash
curl http://localhost:3000/health
```

## 🔐 Security Features

- Non-root container user
- Resource limits and requests
- Health checks (liveness and readiness probes)
- Secret management via Kubernetes secrets
- Environment-specific configurations

## 📊 Monitoring

- Health check endpoint: `/health`
- Application metrics: `/api/status`
- Kubernetes probes for container health
- Argo CD application status dashboard

## 🤝 Contributing

1. Create a feature branch from `develop`
2. Make your changes
3. Test in development environment
4. Create a pull request to `develop`
5. After review, merge to `develop` for dev deployment
6. Promote to `staging` and then `main` for production

## 📝 License

MIT License - see LICENSE file for details.
# Test CI/CD Pipeline
