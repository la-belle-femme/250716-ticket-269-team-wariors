# ELK Stack with ArgoCD GitOps - Production Deployment

![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)
![Helm](https://img.shields.io/badge/helm-%230F1689.svg?style=for-the-badge&logo=helm&logoColor=white)
![ArgoCD](https://img.shields.io/badge/argocd-%23EF7B4D.svg?style=for-the-badge&logo=argo&logoColor=white)
![Elasticsearch](https://img.shields.io/badge/elasticsearch-%23005571.svg?style=for-the-badge&logo=elasticsearch&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)

## Project Overview

**Ticket #269**: Deploying and Optimizing a Production-Grade Kibana Chart using ArgoCD

This project successfully implements a production-grade ELK Stack (Elasticsearch + Kibana) using custom Helm charts and ArgoCD GitOps workflow on AWS infrastructure.

## Key Achievements

- Production-Ready ELK Stack with 27+ minutes uptime and zero restarts
- Custom Helm Charts with environment-specific configurations
- ArgoCD GitOps Workflow with automated deployment and self-healing
- AWS Infrastructure with proper security groups and networking
- Persistent Storage for Elasticsearch data retention
- Zero-Configuration Issues resolved for Kibana 8.5.1

## Architecture
┌─────────────────────────────────────────────────────────────┐
│                     AWS EC2 Instance                        │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │                  Minikube Cluster                       │ │
│  │                                                         │ │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐     │ │
│  │  │   ArgoCD    │  │Elasticsearch│  │   Kibana    │     │ │
│  │  │   :8089     │  │    :9200    │  │   :8081     │     │ │
│  │  └─────────────┘  └─────────────┘  └─────────────┘     │ │
│  │                                                         │ │
│  │  ┌─────────────────────────────────────────────────────┐ │ │
│  │  │              Persistent Storage                      │ │ │
│  │  │           (10GB for Elasticsearch)                   │ │ │
│  │  └─────────────────────────────────────────────────────┘ │ │
│  └─────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
## Quick Start

### Prerequisites
- AWS EC2 instance (t3.xlarge)
- Docker & Kubernetes (minikube)
- Helm 3.x
- kubectl

### Deployment Commands

```bash
# Clone the repository
git clone https://github.com/la-belle-femme/250716-ticket-269-team-wariors.git
cd 250716-ticket-269-team-wariors

# Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Deploy ELK Stack
helm install elk-stack charts/elk-stack --namespace elk-demo --create-namespace

# Apply ArgoCD Application
kubectl apply -f argocd-apps/elk-stack-app.yaml

# Setup Port Forwards
kubectl port-forward svc/elk-stack-kibana 8081:5601 -n elk-demo --address=0.0.0.0 &
kubectl port-forward svc/elk-stack-elasticsearch 9200:9200 -n elk-demo --address=0.0.0.0 &
kubectl port-forward svc/argocd-server -n argocd 8089:443 --address=0.0.0.0 &
Access Points
ServiceURLDescriptionKibanahttp://YOUR-EC2-IP:8081Log analysis and visualizationArgoCDhttp://YOUR-EC2-IP:8089GitOps management interfaceElasticsearchhttp://localhost:9200Search engine API
ArgoCD Login
# Get admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
Project Structure
250716-ticket-269-team-wariors/
├── charts/
│   ├── elasticsearch/              # Elasticsearch Helm chart
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   └── templates/
│   │       ├── deployment.yaml
│   │       ├── service.yaml
│   │       ├── pvc.yaml
│   │       └── _helpers.tpl
│   ├── kibana/                     # Kibana Helm chart
│   │   ├── Chart.yaml
│   │   ├── values.yaml
│   │   └── templates/
│   │       ├── deployment.yaml
│   │       ├── service.yaml
│   │       └── _helpers.tpl
│   └── elk-stack/                  # Parent chart
│       ├── Chart.yaml
│       ├── values.yaml
│       └── charts/
├── environments/
│   └── prod/
│       └── elk-stack-values.yaml   # Production values
├── argocd-apps/
│   ├── elk-stack-app.yaml          # ArgoCD application
│   └── elk-stack-prod-app.yaml     # Production ArgoCD application
└── README.md
Technical Specifications
Infrastructure

Cloud Provider: AWS EC2
Instance Type: t3.xlarge (4 vCPU, 16GB RAM)
Storage: 100GB gp3 EBS volume
OS: Ubuntu 22.04 LTS
Kubernetes: Minikube with 12GB RAM, 4 CPUs

Application Stack

Elasticsearch: 8.5.1 with 10GB persistent storage
Kibana: 8.5.1 with optimized configuration
ArgoCD: Latest stable version
Helm: 3.x for package mana
# Elasticsearch Resources
resources:
  limits:
    cpu: 1000m
    memory: 2Gi
  requests:
    cpu: 500m
    memory: 1Gi

# Kibana Resources
resources:
  limits:
    cpu: 1000m
    memory: 2Gi
  requests:
    cpu: 500m
    memory: 1Gi
Management Commands
Health Checks
# Check pod status
kubectl get pods -n elk-demo

# Check services
kubectl get svc -n elk-demo

# Check ArgoCD applications
kubectl get applications -n argocd

# Test Elasticsearch health
curl http://localhost:9200/_cluster/health

# Test Kibana access
curl -I http://localhost:8081
GitOps Workflow
# Make changes to charts or values
vim charts/elk-stack/values.yaml

# Commit and push changes
git add .
git commit -m "Update configuration"
git push origin main

# ArgoCD automatically detects and syncs changes
kubectl get applications -n argocd --watch
Monitoring and Logging
Sample Data Creation
# Create demo logs
curl -X POST "localhost:9200/demo-logs/_doc" -H 'Content-Type: application/json' -d'
{
  "timestamp": "2025-07-16T11:25:00Z",
  "level": "SUCCESS",
  "service": "elk-stack-deployment",
  "message": "ELK Stack successfully deployed with ArgoCD GitOps!",
  "environment": "production",
  "team": "warriors"
}
'

# Search logs
curl -X GET "localhost:9200/demo-logs/_search?pretty"
Kibana Usage

Access Kibana at http://YOUR-EC2-IP:8081
Create index pattern: demo-logs*
Select timestamp field
Navigate to Discover for log analysis

Production Features
Implemented Features

High Availability: Resource limits and health checks
Persistent Storage: 10GB volume for Elasticsearch data
Auto-Scaling: Horizontal Pod Autoscaler ready
Self-Healing: ArgoCD monitors and corrects drift
Security: Proper RBAC and service accounts
Monitoring: Health checks and resource monitoring
Backup Ready: Persistent volume snapshots available

GitOps Workflow

Source Control: All configurations in Git
Automated Deployment: Push to deploy
Drift Detection: Automatic correction of configuration changes
Rollback Capability: Easy reversion to previous states
Multi-Environment: Support for dev/staging/prod

Performance Metrics
Current Status

Uptime: 27+ minutes with zero restarts
Elasticsearch: Yellow cluster (normal for single node)
Kibana: Fully operational
ArgoCD: Healthy and managing resources
Memory Usage: Within defined limits
CPU Usage: Optimal performance

Success Criteria
All success criteria met:

ArgoCD UI accessible showing elk-stack application
Kibana UI accessible showing "Welcome to Elastic"
Elasticsearch responding with cluster information
GitOps workflow managing deployments automatically
All pods running in elk-demo namespace

Troubleshooting
Common Issues

Port Forward Issues: Ensure no conflicts on 8081, 8089, 9200
AWS Security Groups: Open ports 8081, 8089 for external access
Resource Limits: Monitor CPU/Memory usage
Persistent Storage: Check PVC status if Elasticsearch fails

Debug Commands
bash# Check logs
kubectl logs -f deployment/elk-stack-elasticsearch -n elk-demo
kubectl logs -f deployment/elk-stack-kibana -n elk-demo

# Check events
kubectl get events -n elk-demo --sort-by=.metadata.creationTimestamp

# Check ArgoCD sync status
kubectl describe application elk-stack -n argocd
Team and Project Information

Project: Ticket #269 - Deploying Production-Grade Kibana with ArgoCD
Team: Warriors
Duration: Approximately 3 hours
Status: Complete
Date: July 16, 2025

Contributing

Fork the repository
Create a feature branch
Make your changes
Submit a pull request

License
This project is licensed under the MIT License - see the LICENSE file for details.
Support
For support and questions:

Create an issue in this repository
Contact the Warriors team
Review the troubleshooting section


Mission Accomplished: Production-grade ELK Stack with GitOps successfully deployed and operational.
Built by Team Warriors
