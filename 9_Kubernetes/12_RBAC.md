# Introduction to Kubernetes RBAC (Role-Based Access Control)

As Kubernetes clusters grow, multiple people and applications start using the same cluster.

Examples:

* Developers
* DevOps Engineers
* QA Teams
* CI/CD tools
* Monitoring systems
* Automation scripts

Now imagine if everyone had full admin access.

It would become extremely dangerous.

A developer could accidentally:

* delete production Pods
* remove namespaces
* destroy deployments
* expose secrets

This is why Kubernetes uses:

# RBAC (Role-Based Access Control)

RBAC is one of the most important Kubernetes security concepts.

---

# What is RBAC?

RBAC is a security mechanism used to control:

* who can access the cluster
* what actions they can perform
* which resources they can access

Simple definition:

> RBAC allows us to define permissions for users and applications inside Kubernetes.

---

# Real-Life Example

Imagine a company office building.

Different people have different access levels:

| Person         | Access               |
| -------------- | -------------------- |
| Security Guard | Main gate only       |
| Employee       | Own department       |
| Manager        | Multiple departments |
| CEO            | Full building access |

Not everyone should access everything.

Kubernetes RBAC works exactly the same way.

---

# Why RBAC is Important

Without RBAC:

* cluster becomes insecure
* accidental deletion risk increases
* sensitive data exposure becomes possible
* production systems become dangerous

RBAC helps achieve:

* security
* controlled access
* least privilege
* safe multi-team environments

---

# Principle of Least Privilege

This is one of the most important security principles.

It means:

> Give users only the permissions they absolutely need.

Example:

* Developer may only need to view Pods
* Monitoring tool may only need read access
* CI/CD pipeline may only deploy applications

Nobody should receive unnecessary admin access.

---

# Core Components of Kubernetes RBAC

RBAC mainly consists of four important components:

1. Users / Service Accounts
2. Roles / ClusterRoles
3. RoleBindings / ClusterRoleBindings

Let us understand each deeply.

---

# 1. Users and Service Accounts

These represent identities.

Identity means:

* who is requesting access

There are two main types:

1. Human Users
2. Service Accounts

---

# Human Users

These are actual people:

* developers
* administrators
* DevOps engineers

---

# Important Concept

Kubernetes itself does NOT manage users directly.

There is no built-in Kubernetes object like:

```yaml id="kppr1n"
kind: User
```

Kubernetes relies on external identity providers.

---

# Identity Providers (IDPs)

Common identity providers include:

* GitHub
* Google
* LDAP
* Active Directory
* SSO systems

These systems authenticate users.

Kubernetes then authorizes them using RBAC.

---

# Example Flow

```text id="jv8j2d"
Developer
   ↓
GitHub Login
   ↓
Kubernetes Authentication
   ↓
RBAC Authorization
   ↓
Access Granted or Denied
```

---

# Identity Brokers

Large enterprise environments often use identity brokers like:

Keycloak

Keycloak can integrate with:

* Kubernetes
* cloud providers
* GitHub
* LDAP

This centralizes authentication management.

---

# Example

A company may use:

* Amazon Elastic Kubernetes Service
* GitHub authentication
* Keycloak as identity broker

Developer logs in using GitHub account, and Kubernetes permissions are applied through RBAC.

---

# 2. Service Accounts

Service Accounts are not human users.

They are identities for:

* Pods
* applications
* automation tools

---

# Why Service Accounts Exist

Sometimes applications inside Kubernetes need to communicate with:

* Kubernetes API Server
* cluster resources

Example:

* Jenkins
* Prometheus
* Argo CD

These applications need identities.

Service Accounts provide that identity.

---

# Important Default Behavior

Whenever a Pod is created, Kubernetes automatically attaches a default Service Account.

This allows:

* basic API communication

unless explicitly disabled.

---

# Custom Service Accounts

We can also create our own Service Accounts.

Example use cases:

* CI/CD pipelines
* monitoring systems
* GitOps tools
* backup systems

---

# Example Service Account YAML

```yaml id="55umdi"
apiVersion: v1
kind: ServiceAccount

metadata:
  name: deployment-bot
```

---

# Creating Service Account

```bash id="e31r7o"
kubectl apply -f sa.yaml
```

---

# Viewing Service Accounts

```bash id="85n15e"
kubectl get sa
```

---

# Role vs ClusterRole

Now we define permissions.

This is where:

* Roles
* ClusterRoles

come in.

---

# 3. Role

A Role defines permissions inside a specific namespace.

Important point:

# Role is namespace-scoped

It works only inside one namespace.

---

# Example

Suppose:

* developer should only view Pods
* only inside development namespace

Role can define:

* get Pods
* list Pods
* watch Pods

but only within that namespace.

---

# Example Role YAML

```yaml id="mu6qtn"
apiVersion: rbac.authorization.k8s.io/v1
kind: Role

metadata:
  namespace: development
  name: pod-reader

rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

---

# Understanding This YAML

---

## Namespace

```yaml id="5upndq"
namespace: development
```

Permissions apply only inside:

```text id="73b46p"
development
```

namespace.

---

## Resources

```yaml id="dbor1v"
resources: ["pods"]
```

Specifies which resource.

---

## Verbs

```yaml id="pf5rgi"
verbs: ["get", "watch", "list"]
```

Allowed actions.

---

# Common Verbs in RBAC

| Verb   | Meaning           |
| ------ | ----------------- |
| get    | Read one resource |
| list   | View list         |
| watch  | Monitor changes   |
| create | Create resource   |
| update | Modify resource   |
| patch  | Partial update    |
| delete | Delete resource   |

---

# 4. ClusterRole

ClusterRole works at:

* cluster level

Unlike Role:

* not limited to one namespace

---

# Used For

Cluster-wide resources like:

* nodes
* namespaces
* persistent volumes
* cluster-wide monitoring

---

# Example ClusterRole YAML

```yaml id="d0a4r5"
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole

metadata:
  name: node-reader

rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["get", "list"]
```

---

# Role vs ClusterRole

| Feature | Role | ClusterRole |
|---|---|
| Scope | Single Namespace | Entire Cluster |
| Used For | Namespace resources | Cluster-wide resources |
| Example | Pods in dev namespace | Nodes |

---

# RoleBinding and ClusterRoleBinding

Creating Roles alone does nothing.

We must connect:

* user/service account
  with:
* role

This connection is called:

* Binding

---

# 5. RoleBinding

RoleBinding connects:

* User
* Group
* Service Account

to a:

* Role

inside a namespace.

---

# Example RoleBinding YAML

```yaml id="2bvyxv"
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding

metadata:
  name: read-pods
  namespace: development

subjects:
- kind: User
  name: developer1

roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

---

# What This Means

User:

```text id="y9bh6u"
developer1
```

receives permissions from:

```text id="v08v4z"
pod-reader
```

Role inside:

```text id="3kx4qv"
development
```

namespace.

---

# 6. ClusterRoleBinding

ClusterRoleBinding connects:

* users
* groups
* service accounts

to:

* ClusterRole

for entire cluster access.

---

# Example ClusterRoleBinding YAML

```yaml id="l5zc0q"
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding

metadata:
  name: cluster-admin-binding

subjects:
- kind: User
  name: admin-user

roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```

---

# Very Important Warning

Be extremely careful with:

```text id="9ldj5x"
cluster-admin
```

This gives:

* full cluster control

Equivalent to:

* root/admin access

Never give this unnecessarily.

---

# Complete RBAC Flow

```text id="2ch4vw"
User / Service Account
        ↓
Role or ClusterRole
        ↓
Binding
        ↓
Permissions Applied
```

---

# Namespace-Level Example

Suppose:

* developer works only in development namespace

You create:

* Role
* RoleBinding

Now developer:

* can manage development resources
* cannot touch production

This is real-world RBAC usage.

---

# RBAC for CI/CD Tools

Tools like:

* Jenkins
* Argo CD
* Prometheus

use Service Accounts with RBAC permissions.

This ensures:

* secure automation
* limited access
* safer operations

---

# Checking RBAC Resources

---

## View Roles

```bash id="jlwmg7"
kubectl get roles
```

---

## View ClusterRoles

```bash id="sdg2bh"
kubectl get clusterroles
```

---

## View RoleBindings

```bash id="cc0yc7"
kubectl get rolebindings
```

---

## View ClusterRoleBindings

```bash id="ylhm1y"
kubectl get clusterrolebindings
```

---

# Testing Permissions

Kubernetes provides a very useful command:

```bash id="c5og0v"
kubectl auth can-i
```

Example:

```bash id="axvx1x"
kubectl auth can-i delete pods
```

---

# Example Output

```text id="br42kw"
yes
```

or

```text id="lkew6z"
no
```

This is extremely useful for debugging RBAC issues.

---

# Best Practices for RBAC

---

## 1. Follow Least Privilege

Never give unnecessary access.

---

## 2. Avoid Cluster Admin Access

Use namespace-scoped Roles whenever possible.

---

## 3. Use Service Accounts Properly

Never run applications with excessive permissions.

---

## 4. Separate Dev/Test/Production

Different namespaces should have separate RBAC policies.

---

## 5. Audit Permissions Regularly

Review:

* users
* roles
* bindings

to remove unnecessary access.

---

# Real Production Example

A company may have:

| Team             | Access                     |
| ---------------- | -------------------------- |
| Developers       | Development namespace only |
| QA Team          | Testing namespace          |
| DevOps Team      | Cluster-wide management    |
| Monitoring Tools | Read-only access           |
| CI/CD Tools      | Deployment permissions     |

RBAC safely separates all these responsibilities.

---

# Architecture Summary

```text id="6l1t5w"
User / Application
        ↓
Authentication
        ↓
RBAC Authorization
        ↓
Allowed or Denied
```

---

# Final Summary

RBAC is Kubernetes' authorization system.

It controls:

* who can access resources
* what actions they can perform
* where permissions apply

Main RBAC components:

| Component          | Purpose                          |
| ------------------ | -------------------------------- |
| User               | Human identity                   |
| ServiceAccount     | Application identity             |
| Role               | Namespace permissions            |
| ClusterRole        | Cluster-wide permissions         |
| RoleBinding        | Connects Role to identity        |
| ClusterRoleBinding | Connects ClusterRole to identity |

RBAC is essential for:

* Kubernetes security
* multi-team environments
* production clusters
* enterprise deployments
* secure automation systems

---
