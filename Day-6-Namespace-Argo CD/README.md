# Argo CD Complete Setup Guide 

# What is Argo CD?

Argo CD is a GitOps Continuous Delivery tool for Kubernetes.

It automatically deploys Kubernetes applications from a Git repository.

Argo CD continuously monitors:

* Kubernetes manifests
* Helm charts
* Kustomize files

and syncs them into a Kubernetes cluster.

---

# What is GitOps?

GitOps means:

* Git repository is the single source of truth
* All Kubernetes configurations are stored in Git
* Any changes pushed to Git are automatically deployed to Kubernetes

Benefits:

* Version control
* Easy rollback
* Automated deployments
* Infrastructure consistency
* Audit tracking

---

# Argo CD Architecture

## Main Components

### 1. API Server

Handles:

* UI
* CLI
* Authentication
* API requests

---

### 2. Repository Server

Responsible for:

* Cloning Git repositories
* Reading manifests
* Helm rendering
* Kustomize rendering

---

### 3. Application Controller

Responsible for:

* Monitoring applications
* Comparing desired state vs live state
* Synchronization
* Self-healing

---

# Argo CD Workflow

Developer Pushes Code → Git Repository → Argo CD Detects Changes → Syncs to Kubernetes Cluster

---

# Prerequisites

Before installing Argo CD:

You need:

* Kubernetes Cluster
* kubectl installed
* Argo CD CLI
* GitHub repository
* Internet access

Supported Kubernetes:

* EKS
* AKS
* GKE
* Minikube
* K3s
* Kind

---

# Step 1: Create Namespace

```bash
kubectl create namespace argocd
```

---

# Step 2: Install Argo CD

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

---

# Step 3: Verify Installation

```bash
kubectl get pods -n argocd
```

All pods should be in Running state.

---

# Step 4: Expose Argo CD Server


## LoadBalancer

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

Get External IP:

```bash
kubectl get svc -n argocd
```

---

# Step 5: Get Initial Admin Password

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Username:

```text
admin
```
---

# Step 7: Login to Argo CD

```bash
argocd login with loadbalncer url
```

Provide:

* Username
* Password

---



# Creating Argo CD Application

# What is an Application?

An Argo CD Application is:

* A deployment definition
* Connects Git repo to Kubernetes cluster
* Defines:

  * Source repository
  * Path
  * Destination cluster
  * Namespace

---


# Application Parameters Explanation

## --repo

Git repository URL.

---

## --path

Folder inside repository containing manifests.

Example:

```text
kubernetes/
manifests/
helm/
```

---

## --dest-server

Target Kubernetes cluster.

Default cluster:

```text
https://kubernetes.default.svc
```

---

## --dest-namespace

Namespace where application will deploy.

---

# Sync Application

```bash
argocd app sync ecommerce-app
```

This deploys manifests into Kubernetes.

---

# Auto Sync

# What is Auto Sync?

Automatically deploys changes whenever Git repository updates.

---

# Enable Auto Sync

---

# Self Healing

# What is Self Healing?

If someone manually changes Kubernetes resources:

Argo CD restores them back to Git state.

Enable:

---

# Pruning

# What is Pruning?

Deletes Kubernetes resources removed from Git.

Enable:

---

# Application Lifecycle

Git Push → Argo CD Detects Changes → Compare Desired vs Live State → Sync → Deployment

---

# Application States

## Synced

Git state matches cluster state.

---

## OutOfSync

Cluster differs from Git.

---

## Healthy

Application running successfully.

---

## Degraded

Application has issues.

Examples:

* CrashLoopBackOff
* Failed deployment
* Pod failures

---

# Deploy Using YAML

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp-argo-application
  namespace: argocd
spec:
  project: default

  source:
    repoURL: https://github.com/CloudTechDevOps/Kubernetes-0730.git
    targetRevision: HEAD
    path: Day-5-Argocd
  destination: 
    server: https://kubernetes.default.svc
    namespace: myapp

  syncPolicy:
    syncOptions:
    - CreateNamespace=true

    automated:
      selfHeal: true
      prune: true
```

Apply:

```bash
kubectl apply -f app.yaml
```
---

# Kustomize Integration

Argo CD supports Kustomize.

Repository structure:

```text
base/
overlays/
```

---

# Multi Cluster Deployment

Argo CD can deploy to multiple clusters.

Add cluster:

```bash
argocd cluster add CONTEXT_NAME
```

List clusters:

```bash
argocd cluster list
```
# Private Repository Integration

# Why Private Repo Authentication is Needed

Private repositories require authentication.

Without authentication:

* Argo CD cannot clone repository
* Manifests cannot be fetched
* Application deployment fails

---

# Authentication Methods

Argo CD supports:

1. HTTPS + Username/Token
2. SSH Authentication

---

# Method 1: HTTPS with Personal Access Token


# Method : SSH Authentication

# Why SSH?

SSH authentication is more secure.

Used mostly in:

* Production
* Enterprises
* Secure environments

---

# Step 1: Generate SSH Key

```bash
ssh-keygen -t rsa -b 4096 -C "argocd"
```

Files created:

```text
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
```

---

# Step 2: Add Public Key to GitHub

Open:

GitHub → Settings → SSH and GPG Keys

Add:

```text
id_rsa.pub
```
---
3. Add the Public Key to GitHub

Open your private GitHub repository.

Go to:

Repository
  → Settings
  → Deploy keys
  → Add deploy key

Paste your public key.

Give the key read-only access.

Do not enable write access unless Argo CD needs to push changes to the repository.

4. Use the SSH Repository URL

Use:

git@github.com:USERNAME/REPOSITORY.git

Example:

git@github.com:sameershaik6/kubernetes.git

Do not use:

https://github.com/sameershaik6/kubernetes.git

when configuring the repository for SSH authentication.

5. Add Repository in Argo CD UI

Open Argo CD.

Go to:

Settings
  → Repositories
  → Connect Repo

Select:

Connection Method: SSH

Repository URL:

git@github.com:sameershaik6/kubernetes.git

SSH Private Key:

cat ~/.ssh/id_ed25519

Copy the complete private key and paste it into Argo CD.

The private key looks like:

-----BEGIN OPENSSH PRIVATE KEY-----
...
-----END OPENSSH PRIVATE KEY-----

Click:

Connect

The repository should show as:

Successful
6. Create an Argo CD Application

Create an Application in Argo CD.

Use the private repository:

git@github.com:sameershaik6/kubernetes.git

Specify the path containing your Kubernetes manifests.

For example:

kubernetes/
├── deployment.yaml
├── service.yaml
└── namespace.yaml

Example:

Repository:
git@github.com:sameershaik6/kubernetes.git

Path:
.

Cluster:
https://kubernetes.default.svc

Namespace:
default

Then create the application.

7. Verify the Application

Check the Argo CD application.

Expected status:

Sync Status: Synced
Health Status: Healthy

You can also verify from Kubernetes:

kubectl get pods
kubectl get svc
kubectl get deployment
8. Test GitOps

Change a Kubernetes manifest in Git.

For example:

spec:
  replicas: 3

Commit and push:

git add .
git commit -m "Update replicas"
git push

Argo CD will detect the change.

If automatic sync is enabled, Argo CD will deploy the change automatically.

If automatic sync is disabled, the application will show:

OutOfSync

Click Sync to deploy the change.

9. Common Error
Error
authentication required
Repository not found
Cause

Argo CD is using an HTTPS URL without valid authentication.

Incorrect:

https://github.com/sameershaik6/kubernetes

Correct SSH URL:

git@github.com:sameershaik6/kubernetes.git

Also verify that the SSH public key has been added to the GitHub repository's Deploy keys.

Security

Never commit your private SSH key to GitHub.

Never put this file in your repository:

id_ed25519

The public key can be added to GitHub:

id_ed25519.pub

The private key should only be stored securely in Argo CD's repository credentials.

Final GitOps Flow
Developer
    |
    | git push
    v
Private GitHub Repository
    |
    | SSH
    v
Argo CD
    |
    | Sync
    v
Kubernetes / EKS
    |
    v
Application

Key interview statement:

Argo CD can connect to a private GitHub repository using SSH authentication. We add the SSH public key as a GitHub Deploy Key and configure the corresponding private key in Argo CD. Argo CD then securely reads the Kubernetes manifests from the private repository and synchronizes them with the Kubernetes cluster.
---

# Argo CD UI Overview

Main sections:

* Applications
* Repositories
* Clusters
* Settings
* Projects

---

# Summary

Argo CD is a Kubernetes GitOps deployment tool.

Workflow:

1. Store manifests in Git
2. Connect repository to Argo CD
3. Create application
4. Sync manifests
5. Argo CD deploys automatically
6. Git becomes source of truth

Private repositories can be integrated using:

* HTTPS tokens
* SSH keys
* Repository secrets

Argo CD continuously monitors Git and Kubernetes to maintain desired application state.
