# Kubernetes Namespace Practice

## Overview

In this practice, I learned how to create and use **Kubernetes Namespaces** and how to deploy a Pod inside a specific namespace.

A Namespace is used to logically separate and organize Kubernetes resources within the same cluster.

---

## What is a Namespace?

A Kubernetes Namespace provides a way to divide a Kubernetes cluster into multiple logical environments.

For example:

* `dev` → Development environment
* `test` → Testing environment
* `prod` → Production environment

Resources such as Pods, Deployments, Services, and ConfigMaps can be placed inside a specific namespace.

Namespaces are especially useful when multiple teams or environments are using the same Kubernetes cluster.

---

# Method 1: Create Namespace Using kubectl

First, create the namespace using the command:

```bash
kubectl create namespace dev
```

You can verify that the namespace was created:

```bash
kubectl get namespaces
```

Then deploy the Pod into the `dev` namespace:

```bash
kubectl apply -f pod.yml -n dev
```

The `-n dev` option tells Kubernetes to create the resource in the `dev` namespace.

Check the Pod:

```bash
kubectl get pods -n dev
```

Get more detailed information:

```bash
kubectl describe pod <pod-name> -n dev
```

### Important Point

When using this method, the namespace is created separately using `kubectl`.

The Pod YAML does not need to contain:

```yaml
namespace: dev
```

because the namespace is provided through the command:

```bash
kubectl apply -f pod.yml -n dev
```

---

# Method 2: Specify Namespace in Pod YAML

Another way is to create the namespace separately and specify the namespace inside the Pod YAML.

First create the namespace:

```bash
kubectl create namespace dev
```

Then use the following `pod.yml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dev-nginx-pod
  namespace: dev
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
```

Apply the YAML:

```bash
kubectl apply -f pod.yml
```

Because `namespace: dev` is already specified in the YAML, Kubernetes creates the Pod inside the `dev` namespace.

Check the Pod:

```bash
kubectl get pods -n dev
```

---

# Method 3: Create Namespace and Pod in the Same YAML File

Kubernetes also allows multiple resources to be defined in a single YAML file using `---`.

Example:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: prod
---
apiVersion: v1
kind: Pod
metadata:
  name: prod-nginx-pod
  namespace: prod
spec:
  containers:
  - name: nginx-container
    image: nginx:latest
```

Save this as:

```text
prod.yml
```

Apply it:

```bash
kubectl apply -f prod.yml
```

This creates:

1. The `prod` Namespace
2. The `prod-nginx-pod` inside the `prod` Namespace

Check the namespace:

```bash
kubectl get namespace prod
```

Check the Pod:

```bash
kubectl get pods -n prod
```

You can also check all resources in the namespace:

```bash
kubectl get all -n prod
```

---

# Commands Practiced

### Create Namespace

```bash
kubectl create namespace dev
```

### List Namespaces

```bash
kubectl get namespaces
```

or:

```bash
kubectl get ns
```

### Apply YAML to a Specific Namespace

```bash
kubectl apply -f pod.yml -n dev
```

### Get Pods in a Namespace

```bash
kubectl get pods -n dev
```

Example:

```bash
kubectl get pods -n prod
```

### Describe a Pod

```bash
kubectl describe pod <pod-name> -n dev
```

### Get All Resources in a Namespace

```bash
kubectl get all -n dev
```

### Check Pod in All Namespaces

```bash
kubectl get pods -A
```

or:

```bash
kubectl get pods --all-namespaces
```

---

# What I Practiced

In this exercise, I practiced three different approaches for working with Kubernetes Namespaces:

### 1. Namespace through kubectl

```bash
kubectl create namespace dev
kubectl apply -f pod.yml -n dev
```

### 2. Namespace inside resource YAML

```yaml
metadata:
  name: dev-nginx-pod
  namespace: dev
```

### 3. Namespace and resource in the same YAML

```yaml
kind: Namespace
metadata:
  name: prod
```

followed by:

```yaml
---
kind: Pod
metadata:
  name: prod-nginx-pod
  namespace: prod
```

I also verified the deployed Pods using:

```bash
kubectl get pods -n dev
kubectl get pods -n prod
```

---

# Why Do We Use Namespaces?

Namespaces are mainly used for **logical separation and organization of resources**.

For example, suppose a company has one Kubernetes cluster:

```text
Kubernetes Cluster
│
├── dev
│   └── Development applications
│
├── test
│   └── Testing applications
│
└── prod
    └── Production applications
```

Without namespaces, all resources would be in the same logical space, which can become difficult to manage as the cluster grows.

Namespaces help us:

* Separate environments
* Organize resources
* Avoid naming conflicts
* Apply ResourceQuotas
* Apply LimitRanges
* Apply Role-Based Access Control (RBAC)
* Manage resources for different teams

---

# Interview Explanation

### Question: What is a Namespace in Kubernetes?

**Answer:**

> A Namespace in Kubernetes is a logical isolation mechanism used to organize and separate resources within a Kubernetes cluster. It allows us to divide resources based on environments, teams, or applications, such as `dev`, `test`, and `prod`.

### Question: Why do we use Namespaces?

**Answer:**

> We use Namespaces to logically separate resources, avoid naming conflicts, control access using RBAC, and apply resource limits and quotas to different teams or environments.

### Question: How can you create a Namespace?

There are multiple ways.

Using kubectl:

```bash
kubectl create namespace dev
```

Using YAML:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

Then:

```bash
kubectl apply -f namespace.yml
```

### Question: How do you deploy a Pod into a Namespace?

Using the command:

```bash
kubectl apply -f pod.yml -n dev
```

Or specify it in the YAML:

```yaml
metadata:
  name: nginx-pod
  namespace: dev
```

### Question: What happens if we don't specify a Namespace?

If no namespace is specified, Kubernetes generally creates the namespaced resource in the **`default` namespace**, unless another namespace is supplied through the kubectl context/command.

You can check resources in the default namespace using:

```bash
kubectl get pods
```

---

# Important Interview Point

A Namespace is **not a separate Kubernetes cluster**.

For example:

```text
                 Kubernetes Cluster
                        |
          +-------------+-------------+
          |             |             |
         dev           test          prod
          |             |             |
        Pods          Pods          Pods
```

All three namespaces can exist in the same cluster, while providing logical separation between their resources.

Namespaces are useful for organization and access/resource management, but they are **not a complete security boundary by themselves**.

---

# Useful Verification Commands

```bash
kubectl get ns
```

```bash
kubectl get pods -n dev
```

```bash
kubectl get pods -n prod
```

```bash
kubectl get all -n prod
```

```bash
kubectl get pods -A
```

```bash
kubectl describe pod prod-nginx-pod -n prod
```

---

## Summary

I learned:

* What a Kubernetes Namespace is
* How to create a Namespace using `kubectl`
* How to specify a Namespace in a YAML file
* How to create a Namespace and Pod from the same YAML file
* How to deploy resources into a specific Namespace
* How to verify resources using `kubectl get`
* Why Namespaces are useful in real-world Kubernetes environments
* How to explain Namespaces in a Kubernetes interview
