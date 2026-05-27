# Kubernetes Namespaces

As Kubernetes clusters grow, many applications, teams, and environments start sharing the same cluster.

For example, a company may have:

* development applications
* testing applications
* production applications
* monitoring tools
* CI/CD tools

Now imagine if everything existed together without separation.

Problems would quickly appear:

* resource conflicts
* naming conflicts
* security issues
* management difficulties

To solve this problem, Kubernetes provides:

# Namespaces

Namespaces are one of the most important Kubernetes organizational and isolation concepts.

---

# What is a Namespace?

A Namespace is a logical partition inside a Kubernetes cluster.

Simple definition:

> Namespaces divide a Kubernetes cluster into multiple virtual environments.

They help organize and isolate:

* applications
* teams
* environments
* resources

inside the same cluster.

---

# Real-Life Example

Imagine a large office building.

Inside the building:

* HR department
* Finance department
* Engineering department
* Management department

All departments share:

* same building
* same electricity
* same infrastructure

But each department has:

* separate rooms
* separate employees
* separate resources

Kubernetes Namespaces work similarly.

The Kubernetes cluster is:

* the building

Namespaces are:

* separate departments

---

# Why Namespaces Are Needed

Without Namespaces:

* all resources exist together
* resource names may conflict
* teams interfere with each other
* management becomes difficult

Namespaces provide:

* organization
* isolation
* access control
* resource separation

---

# Common Use Cases of Namespaces

Namespaces are commonly used for:

| Use Case               | Example                     |
| ---------------------- | --------------------------- |
| Environment Separation | dev, test, prod             |
| Team Separation        | frontend-team, backend-team |
| Application Isolation  | monitoring, logging         |
| Multi-Tenancy          | different customers/clients |

---

# Example Environment Separation

A company may create:

| Namespace   | Purpose                         |
| ----------- | ------------------------------- |
| development | Developers testing applications |
| staging     | Pre-production testing          |
| production  | Live applications               |

This prevents developers from accidentally affecting production systems.

---

# Important Concept

Namespaces provide:

# Logical Isolation

Not complete physical isolation.

All namespaces still share:

* same cluster
* same nodes
* same Kubernetes control plane

---

# Namespace Scope

Some Kubernetes resources are:

* namespace-scoped

Others are:

* cluster-scoped

This is extremely important.

---

# Namespace-Scoped Resources

These resources exist inside namespaces.

Examples:

| Resource   | Namespace Scoped? |
| ---------- | ----------------- |
| Pod        | Yes               |
| Deployment | Yes               |
| Service    | Yes               |
| ConfigMap  | Yes               |
| Secret     | Yes               |

---

# Cluster-Scoped Resources

These resources belong to entire cluster.

Examples:

| Resource         | Cluster Scoped? |
| ---------------- | --------------- |
| Node             | Yes             |
| PersistentVolume | Yes             |
| Namespace        | Yes             |
| ClusterRole      | Yes             |

---

# Default Namespaces in Kubernetes

Kubernetes automatically creates some namespaces.

---

# 1. default Namespace

This is the default working namespace.

If no namespace specified:

* resources created here

---

# Example

```bash id="ns1"
kubectl get pods
```

actually means:

```bash id="ns2"
kubectl get pods -n default
```

---

# 2. kube-system Namespace

Contains Kubernetes system components.

Examples:

* CoreDNS
* kube-proxy
* API server components
* controller manager

---

# Important Warning

Never accidentally delete or modify:

# kube-system

unless you fully understand what you are doing.

---

# 3. kube-public Namespace

Publicly accessible resources.

Rarely used directly.

---

# 4. kube-node-lease Namespace

Used internally by Kubernetes for:

* node heartbeats
* node availability tracking

---

# View All Namespaces

```bash id="ns3"
kubectl get namespaces
```

or:

```bash id="ns4"
kubectl get ns
```

---

# Example Output

```text id="ns5"
NAME              STATUS   AGE
default           Active   10d
kube-system       Active   10d
kube-public       Active   10d
kube-node-lease   Active   10d
```

---

# Creating a Namespace

---

# YAML Example

```yaml id="ns6"
apiVersion: v1
kind: Namespace

metadata:
  name: development
```

---

# Apply Namespace

```bash id="ns7"
kubectl apply -f namespace.yaml
```

---

# Create Using Command

```bash id="ns8"
kubectl create namespace development
```

---

# Verify Namespace

```bash id="ns9"
kubectl get ns
```

---

# Creating Resources Inside Namespace

Now let us create resources inside:

```text id="ns10"
development
```

namespace.

---

# Example Deployment YAML

```yaml id="ns11"
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment
  namespace: development

spec:
  replicas: 2

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
      - name: nginx
        image: nginx
```

---

# Important Line

```yaml id="ns12"
namespace: development
```

This places resource inside:

* development namespace

---

# Viewing Namespace Resources

---

## Get Pods in Namespace

```bash id="ns13"
kubectl get pods -n development
```

---

## Get Services in Namespace

```bash id="ns14"
kubectl get svc -n development
```

---

# Namespace Isolation Example

Suppose:

Namespace:

```text id="ns15"
development
```

contains Pod:

```text id="ns16"
nginx
```

Another namespace:

```text id="ns17"
production
```

can also contain Pod named:

```text id="ns18"
nginx
```

No conflict occurs.

This is because:

* namespaces isolate resource names

---

# Important DNS Concept

Pods and Services inside namespaces use namespace-aware DNS.

---

# Service DNS Format

```text id="ns19"
service-name.namespace.svc.cluster.local
```

Example:

```text id="ns20"
mysql.production.svc.cluster.local
```

---

# Why Important?

Allows applications to communicate across namespaces safely.

---

# Switching Default Namespace

Typing:

```bash id="ns21"
-n development
```

again and again becomes annoying.

We can change current context namespace.

---

# Set Default Namespace

```bash id="ns22"
kubectl config set-context --current --namespace=development
```

Now commands automatically use:

* development namespace

---

# Check Current Namespace

```bash id="ns23"
kubectl config view --minify | grep namespace:
```

---

# Resource Quotas in Namespaces

Namespaces are heavily used with:

# ResourceQuota

to limit resource usage.

---

# Why Needed?

Suppose:

* one team consumes entire cluster CPU/RAM

Other teams suffer.

Resource Quotas solve this.

---

# Example ResourceQuota

```yaml id="ns24"
apiVersion: v1
kind: ResourceQuota

metadata:
  name: dev-quota
  namespace: development

spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 8Gi
```

---

# What This Does

Namespace:

```text id="ns25"
development
```

can use maximum:

* 10 Pods
* 4 CPU
* 8Gi RAM

---

# LimitRange in Namespaces

Namespaces can also enforce:

* minimum resources
* maximum resources
* default resources

using:

# LimitRange

---

# Example

Prevent developers from creating:

* extremely large Pods
* resource-hungry containers

---

# RBAC with Namespaces

Namespaces are extremely important for:

# RBAC

Example:

* developers allowed only in dev namespace
* production access restricted

---

# Example Production Scenario

| Team            | Namespace    |
| --------------- | ------------ |
| Frontend Team   | frontend-dev |
| Backend Team    | backend-dev  |
| QA Team         | testing      |
| Production Apps | production   |

RBAC policies then control:

* who accesses which namespace

---

# Network Policies + Namespaces

Namespaces also work with:

# Network Policies

Example:

* frontend namespace allowed to access backend namespace
* monitoring namespace allowed cluster-wide access

This creates strong isolation.

---

# Important Limitation of Namespaces

Namespaces do NOT provide:

* full security isolation
* separate kernels
* separate clusters

For stronger isolation:

* separate Kubernetes clusters may be needed.

---

# Multi-Tenancy in Kubernetes

Large cloud platforms use namespaces for:

# Multi-Tenant Clusters

Meaning:

* multiple teams/customers share same cluster safely.

Examples:

* SaaS platforms
* cloud providers
* enterprise platforms

---

# Delete Namespace

```bash id="ns26"
kubectl delete namespace development
```

---

# Very Important Warning

Deleting a namespace deletes:

* Pods
* Deployments
* Services
* ConfigMaps
* Secrets

inside that namespace.

Be extremely careful in production.

---

# Namespace Lifecycle

```text id="ns27"
Create Namespace
        ↓
Deploy Resources
        ↓
Apply RBAC
        ↓
Apply Quotas
        ↓
Manage Applications
```

---

# Best Practices

---

## 1. Separate Environments

Always separate:

* dev
* staging
* production

---

## 2. Use Resource Quotas

Prevent resource abuse.

---

## 3. Combine with RBAC

Namespaces alone are not security.

Use RBAC together.

---

## 4. Use Naming Standards

Example:

```text id="ns28"
team-environment
```

like:

```text id="ns29"
frontend-prod
backend-dev
```

---

## 5. Avoid Using Default Namespace in Production

Create dedicated namespaces instead.

---

# Architecture Summary

```text id="ns30"
Kubernetes Cluster
        ↓
Namespaces
   ↓     ↓     ↓
Dev   Test   Prod
```

Namespaces create:

* logical separation
* organizational structure

inside one cluster.

---

# Final Summary

Namespaces are logical partitions inside Kubernetes clusters.

They help:

* organize resources
* isolate environments
* separate teams
* control access
* manage resources

Key concepts:

| Concept          | Meaning                            |
| ---------------- | ---------------------------------- |
| Namespace        | Logical cluster partition          |
| Namespace Scoped | Resource exists inside namespace   |
| Cluster Scoped   | Resource belongs to entire cluster |
| ResourceQuota    | Resource limits                    |
| LimitRange       | Default/min/max limits             |

Namespaces are essential for:

* enterprise Kubernetes
* multi-team environments
* RBAC
* resource management
* production cluster organization

---
