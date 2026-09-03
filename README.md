# PERN Stack Kubernetes Deployment 🚀

An end-to-end DevOps project demonstrating GitOps principles, container orchestration, and secure secrets management for a full-stack PERN (PostgreSQL, Express, React, Node.js) application.

## 🏗 Architecture & Tech Stack

- **Orchestration**: Kubernetes (Local / On-Prem)
- **GitOps & Continuous Delivery**: ArgoCD (App-of-Apps Pattern)
- **Packaging & Templating**: Helm 3
- **Secrets Management**: Bitnami Sealed Secrets (Asymmetric Encryption in Git)
- **Observability**: Prometheus & Loki (Metrics & Log Aggregation)
- **Ingress / Reverse Proxy:** Nginx Ingress Controller
- **Infrastructure as Code**: Terraform
- **Database**: PostgreSQL with persistent storage

---

## 🔒 Security & Secrets Management

To adhere to DevSecOps best practices, no unencrypted sensitive data is stored in the Git repository:
- All sensitive environment variables (PostgreSQL credentials, JWT secrets) are encrypted using `Bitnami Sealed Secrets`.
- The `SealedSecret` resources are safely committed to Git and decrypted at runtime inside the cluster by the `sealed-secrets-controller` using private key pairs.

---

## 📁 Repository Structure & GitOps Layout

```text
.
├── .github/
│   └── workflows/          # GitHub Actions CI/CD pipelines
├── backend/                # Express.js API
├── deploy/                 # GitOps and Kubernetes deployment configs
│   ├── argocd/             # ArgoCD manifests (App-of-Apps pattern)
│   │   ├── apps/           # Child application manifests
│   │   └── root-app.yaml   # Root App-of-Apps manifest
│   └── helm/               # Helm charts for services
│       ├── demo-app/       # Main application Helm chart (PERN stack)
│       └── monitoring/     # Monitoring stack Helm chart
├── frontend/               # React SPA
├── terraform/              # Infrastructure as Code (IaC) scripts
├── docker-compose.yaml     # Local development environment
└── README.md

```
---

# 🛠 Quick Start / Local Deployment

## Prerequisites

Before starting, ensure you have the following installed and configured:

* **Kubernetes cluster** (Minikube / k3s / Kind / MicroK8s)
* **kubectl**, **helm** & **kubeseal** CLI
* **ArgoCD** deployed in the cluster

---

## Deployment Steps

### 1. Deploy ArgoCD Root Application

Apply the root application manifest to start the GitOps synchronization:

```bash

kubectl apply -f deploy/argocd/root-app.yaml

```

### 2. Verify Sealed Secrets Controller

Ensure that the Sealed Secrets controller is running properly in the cluster:

```bash

kubectl get pods -n kube-system -l app.kubernetes.io/name=sealed-secrets

```
### 3. Encrypting New Secrets (Developer Workflow)

To safely encrypt sensitive data before committing it to the repository, run:

```bash
kubeseal --controller-name=sealed-secrets-controller \
         --controller-namespace=kube-system \
         --format yaml < raw-secrets.yaml > deploy/helm/demo-app/templates/sealed-secret.yaml
```
