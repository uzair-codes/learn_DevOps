# Kubernetes Custom Resource Definition (CRD)

Until now, we have worked with built-in Kubernetes resources like:

* Pods
* Deployments
* Services
* ConfigMaps
* Secrets
* StatefulSets

These are default Kubernetes objects already provided by Kubernetes.

But in real production environments, companies often need:

* custom application resources
* custom automation logic
* custom APIs
* platform-specific objects

This is where Kubernetes provides one of its most powerful features:

# Custom Resource Definitions (CRDs)

CRDs are one of the most advanced and important Kubernetes extensibility concepts.

---

# Why Kubernetes Needed CRDs

Kubernetes was designed to be:

* extensible
* flexible
* customizable

Different companies have different needs.

Example:

* database companies
* cloud providers
* monitoring systems
* GitOps tools
* AI platforms

all require custom objects.

Kubernetes developers realized:

> It is impossible to build every possible resource type inside Kubernetes core.

So Kubernetes introduced:

# CRDs

to allow users and companies to create their own Kubernetes resource types.

---

# Simple Real-Life Example

Imagine a smartphone.

By default, it comes with:

* camera
* browser
* gallery
* calculator

But users can install:

* WhatsApp
* Zoom
* Banking apps
* Games

CRDs work similarly.

Kubernetes provides core functionality, while CRDs allow adding completely new capabilities.

---

# What is a CRD?

CRD stands for:

# Custom Resource Definition

It allows you to create:

* your own Kubernetes object types
* your own APIs
* your own resources

Simple definition:

> A CRD extends the Kubernetes API by adding custom resource types.

---

# Important Concept

CRD itself is NOT the actual resource.

CRD is:

# Blueprint / Schema

It defines:

* what custom object will look like
* what fields it contains
* how Kubernetes should validate it

---

# Example Analogy

Think of CRD like:

* class definition in programming

And actual custom resources like:

* objects created from that class

---

# Built-In Kubernetes Resources

Examples:

| Resource   | Kind       |
| ---------- | ---------- |
| Pod        | Pod        |
| Deployment | Deployment |
| Service    | Service    |

These are built-in Kubernetes APIs.

---

# CRD Allows Custom Resources Like

| Custom Resource  | Purpose             |
| ---------------- | ------------------- |
| MySQLCluster     | Database management |
| KafkaCluster     | Kafka management    |
| BackupJob        | Backup automation   |
| MonitoringConfig | Monitoring rules    |

These are NOT built into Kubernetes by default.

CRDs make them possible.

---

# Important Architecture Concept

Kubernetes API Server is highly extensible.

CRDs dynamically add new API endpoints into Kubernetes.

This is extremely powerful.

---

# How CRDs Work Internally

Flow:

```text id="crdflow1"
CRD Installed
      ↓
New API Added
      ↓
Kubernetes Understands New Resource Type
      ↓
Users Create Custom Objects
```

---

# Example

Suppose we create a CRD named:

```text id="crdflow2"
Database
```

After installation, Kubernetes may support:

```bash id="crdflow3"
kubectl get databases
```

even though:

* Database resource never existed before.

This is the magic of CRDs.

---

# API Extension Example

Default Kubernetes API:

```text id="crdflow4"
/api/v1/pods
```

After CRD installation:

```text id="crdflow5"
/apis/mycompany.com/v1/databases
```

New API endpoints automatically appear.

---

# Basic CRD Structure

A CRD contains:

* API group
* version
* resource name
* schema
* validation rules

---

# Simple CRD YAML Example

```yaml id="crdexample1"
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition

metadata:
  name: databases.mycompany.com

spec:
  group: mycompany.com

  versions:
    - name: v1
      served: true
      storage: true

      schema:
        openAPIV3Schema:
          type: object

          properties:
            spec:
              type: object

              properties:
                engine:
                  type: string

                version:
                  type: string

  scope: Namespaced

  names:
    plural: databases
    singular: database
    kind: Database
    shortNames:
      - db
```

---

# Understanding This YAML

Now let us deeply understand each section.

---

# apiVersion

```yaml id="crdapi1"
apiVersion: apiextensions.k8s.io/v1
```

CRDs belong to:

* Kubernetes API Extensions system

---

# kind

```yaml id="crdkind1"
kind: CustomResourceDefinition
```

Tells Kubernetes:

* we are defining a new custom resource type

---

# metadata.name

```yaml id="crdmeta1"
name: databases.mycompany.com
```

Format:

```text id="crdmeta2"
plural.group
```

---

# group

```yaml id="crdgroup1"
group: mycompany.com
```

This creates API group:

```text id="crdgroup2"
mycompany.com
```

---

# versions

```yaml id="crdver1"
versions:
```

Defines API versions.

Example:

* v1
* v2
* beta

---

# served

```yaml id="crdserved1"
served: true
```

Means:

* API version accessible

---

# storage

```yaml id="crdstorage1"
storage: true
```

Specifies:

* primary storage version in etcd

---

# Schema Validation

This is extremely important.

CRDs support:

# OpenAPI Schema Validation

Example:

```yaml id="crdschema1"
engine:
  type: string
```

Now Kubernetes validates:

* correct field types
* required structure

---

# scope

```yaml id="crdscope1"
scope: Namespaced
```

Two possible scopes:

| Scope      | Meaning                 |
| ---------- | ----------------------- |
| Namespaced | Exists inside namespace |
| Cluster    | Cluster-wide            |

---

# names Section

Defines:

* singular name
* plural name
* short names
* kind

---

# After Installing CRD

Apply CRD:

```bash id="crdapply1"
kubectl apply -f crd.yaml
```

Now Kubernetes understands:

* Database resource

---

# Verify CRD

```bash id="crdverify1"
kubectl get crd
```

---

# Creating Custom Resource (CR)

After CRD installation, we can create actual resources.

This is called:

# Custom Resource (CR)

---

# Example Custom Resource

```yaml id="crexample1"
apiVersion: mycompany.com/v1
kind: Database

metadata:
  name: my-database

spec:
  engine: mysql
  version: "8.0"
```

---

# Apply Custom Resource

```bash id="crdapply2"
kubectl apply -f database.yaml
```

---

# View Custom Resources

```bash id="crdget1"
kubectl get databases
```

Output example:

```text id="crdget2"
NAME           AGE
my-database    2m
```

---

# Important Concept

CRD alone only stores objects.

By itself:

* CRD does NOT automate anything.

This is extremely important.

---

# CRD vs Controller

CRD defines:

* new resource type

Controller adds:

* operational logic
* automation
* intelligence

---

# Real Production Example

Suppose:

* MySQL CRD exists

You create:

```yaml id="mysqlcr1"
kind: MySQLCluster
```

But who actually:

* creates Pods?
* manages backups?
* handles scaling?

This is handled by:

# Custom Controllers / Operators

---

# CRD + Controller = Operator

This is one of the most important Kubernetes concepts.

---

# Formula

```text id="crdformula1"
CRD + Controller = Kubernetes Operator
```

---

# Real-World Example — Prometheus Operator

Prometheus Operator uses CRDs.

Example custom resources:

```text id="promcrd1"
ServiceMonitor
Prometheus
Alertmanager
```

These resources do not exist in normal Kubernetes.

Prometheus Operator adds them using:

* CRDs
* custom controllers

---

# Example — Argo CD

Argo CD adds resources like:

```text id="argocrd1"
Application
AppProject
```

through CRDs.

---

# Example — Cert Manager

cert-manager adds:

```text id="certcrd1"
Certificate
Issuer
ClusterIssuer
```

using CRDs.

---

# Why CRDs Are Powerful

CRDs transform Kubernetes into:

* programmable platform
* extensible operating system
* cloud-native automation engine

Companies can build:

* custom platforms
* internal APIs
* automation systems

directly inside Kubernetes.

---

# CRDs in Enterprise Environments

Large companies heavily use CRDs for:

* database automation
* machine learning platforms
* backup systems
* monitoring systems
* security systems
* GitOps platforms

---

# CRD Lifecycle

```text id="crdlifecycle1"
Install CRD
      ↓
New API Created
      ↓
Create Custom Resources
      ↓
Controller Watches Resources
      ↓
Automation Happens
```

---

# Important CRD Limitations

CRDs are powerful but come with challenges.

---

# 1. Complexity

Large CRD systems become:

* difficult to maintain
* harder to debug

---

# 2. API Version Management

Supporting:

* v1
* v2
* backward compatibility

becomes complex.

---

# 3. Security Risks

Badly designed CRDs may:

* expose cluster risks
* create unstable systems

---

# 4. Performance

Too many CRDs/controllers may:

* overload API server
* increase memory usage

---

# CRD vs ConfigMap

Many beginners confuse them.

---

# Difference

| CRD                        | ConfigMap                   |
| -------------------------- | --------------------------- |
| Extends Kubernetes API     | Stores configuration        |
| Creates new resource types | Stores key-value data       |
| Used for automation        | Used for application config |

---

# CRD vs Built-In Resources

| Feature | Built-In Resource | CRD |
|---|---|
| Created by Kubernetes | Yes | No |
| Extensible | Limited | Fully Custom |
| Examples | Pod, Service | Database, Certificate |

---

# Checking Installed CRDs

```bash id="crdcheck1"
kubectl get crd
```

---

# Describe CRD

```bash id="crddesc1"
kubectl describe crd databases.mycompany.com
```

---

# Delete CRD

```bash id="crddel1"
kubectl delete crd databases.mycompany.com
```

---

# Important Warning

Deleting CRD may also delete:

* all custom resources created from it

Be very careful in production.

---

# Best Practices

---

## 1. Use Validation Schemas

Always validate custom resources.

---

## 2. Version APIs Properly

Avoid breaking older applications.

---

## 3. Use Controllers Carefully

Controllers must handle failures safely.

---

## 4. Keep CRDs Simple

Overcomplicated CRDs become hard to maintain.

---

## 5. Monitor API Performance

Too many controllers can overload cluster.

---

# Architecture Summary

```text id="crdarch1"
Custom Resource Definition
           ↓
New Kubernetes API
           ↓
Custom Resource
           ↓
Controller Watches It
           ↓
Automation Happens
```

---

# Final Summary

CRDs allow Kubernetes to become:

* extensible
* customizable
* platform-driven

They add:

* new API resources
* custom object types
* cloud-native automation capabilities

Important concepts:

| Concept    | Meaning                   |
| ---------- | ------------------------- |
| CRD        | Defines new resource type |
| CR         | Actual custom object      |
| Controller | Adds operational logic    |
| Operator   | CRD + Controller          |

CRDs are foundational for:

* Kubernetes Operators
* GitOps platforms
* monitoring systems
* database automation
* enterprise Kubernetes ecosystems

They are one of the reasons Kubernetes became a powerful cloud-native platform instead of just a container orchestrator.

---
