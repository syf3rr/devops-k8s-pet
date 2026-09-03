# PERN Stack Kubernetes Deployment 🚀

An end-to-end DevOps project demonstrating GitOps principles, container orchestration, and secure secrets management for a full-stack PERN (PostgreSQL, Express, React, Node.js) application.

## 🏗 Architecture & Tech Stack

- **Orchestration**: Kubernetes (Local / On-Prem)
- **GitOps & Continuous Delivery**: ArgoCD (App-of-Apps Pattern)
- **Packaging & Templating**: Helm 3
- **Secrets Management**: Bitnami Sealed Secrets (Asymmetric Encryption in Git)
- **Observability**: Prometheus & Loki (Metrics & Log Aggregation)
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
├── deploy/
│   └── argocd/               # ArgoCD Application manifests (App-of-Apps)
│       └── apps/             # Child apps (demo-app, sealed-secrets, monitoring)
│   └── helm/
│       └── demo-app/         # Helm chart for PERN application stack
│           ├── templates/    # K8s manifests (Deployments, Services, SealedSecrets)
│           └── values.yaml   # Environment configuration
├── backend/                  # Express.js API
├── frontend/                 # React SPA
└── terraform/                # Infrastructure provisioning scripts

```
---

# 🛠 Quick Start / Local Deployment

## Prerequisites

Before starting, ensure you have the following installed and configured:

* **Kubernetes cluster** (Minikube / k3s / Kind / MicroK8s)
* **kubectl** & **helm** CLI
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
