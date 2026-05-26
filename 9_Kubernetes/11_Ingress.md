# Kubernetes Ingress

## Introduction

When we started learning Kubernetes, we learned about:

* Pods
* Deployments
* Services

We saw that a Kubernetes Service helps us expose applications and communicate with Pods.
But in real-world production environments, especially in large companies, Services alone are not enough.

This is where **Ingress** comes in.

Many beginners get confused and ask:

> “If Services already expose applications, then why do we need Ingress?”

This is the most important question.

To properly understand Ingress, we first need to understand the limitations of Kubernetes Services and the problems companies faced before Ingress existed.

---

# Why Ingress?

# What Problems Exist with Kubernetes Services?

Kubernetes Services are very useful, but they provide only **basic networking and load balancing**.

They work well for small setups, but modern enterprise applications need:

* smarter routing
* better security
* lower cloud costs
* centralized traffic management

Kubernetes Services alone cannot efficiently solve these problems.

There are two major issues.

---

# Problem 1 — Kubernetes Services Provide Only Basic Load Balancing

## Understanding with a Real-Life Example

Imagine a large hospital building.

People arrive for:

* emergency treatment
* surgery
* laboratory testing
* normal checkups

Now suppose the hospital has only a simple security guard at the gate.

The guard does not understand:

* who is an emergency patient
* who needs surgery
* who should go to the lab

The guard simply sends people inside one by one.

This is exactly how a normal Kubernetes Service behaves.

---

# How Kubernetes Service Works

A Kubernetes Service mainly performs:

* Service Discovery
* Basic Load Balancing

It distributes traffic between Pods using methods like:

* Round Robin

Example:

If 4 requests arrive:

* Request 1 → Pod-1
* Request 2 → Pod-2
* Request 3 → Pod-1
* Request 4 → Pod-2

This is simple traffic distribution.

But enterprise applications require much more intelligence.

---

# Features Missing in Kubernetes Services

---

## 1. Path-Based Routing

Suppose your website is:

```text
my-website.com
```

You want:

* `/login` traffic → Login Service
* `/payment` traffic → Payment Service
* `/search` traffic → Search Service

Example:

```text
my-website.com/login
my-website.com/payment
my-website.com/search
```

A normal Kubernetes Service cannot intelligently read URL paths and route traffic accordingly.

It only forwards traffic based on:

* IP
* Port

It cannot understand:

* `/login`
* `/payment`
* `/search`

---

## 2. Host-Based Routing

Suppose your company has multiple domains:

```text
shop.example.com
api.example.com
admin.example.com
```

You want:

* `shop.example.com` → E-commerce application
* `api.example.com` → Backend API
* `admin.example.com` → Admin Panel

Kubernetes Services cannot inspect domain names and route traffic differently based on hostnames.

---

## 3. Sticky Sessions

This is extremely important for applications like:

* shopping carts
* login systems
* banking systems

### The Problem

Suppose:

* User logs in through Pod-1
* User session data exists in Pod-1 memory

Now user refreshes the page.

Because of Round Robin:

* next request goes to Pod-2

Pod-2 does not know:

* who the user is
* what items are in the cart

The session breaks.

---

## What is Sticky Session?

Sticky Session means:

> Once a user connects to a Pod, future requests from the same user continue going to the same Pod.

Normal Kubernetes Services do not provide advanced sticky session capabilities like enterprise load balancers.

---

## 4. SSL/TLS Handling

Modern websites use:

```text
https://
```

instead of:

```text
http://
```

because HTTPS encrypts traffic.

Without Ingress:

* SSL certificates may need to be configured individually
* management becomes difficult
* certificate renewal becomes harder
* scaling becomes messy

Large companies need centralized SSL management.

---

# Conclusion of Problem 1

Kubernetes Services are good for:

* exposing applications
* internal communication
* basic load balancing

But they lack:

* intelligent routing
* domain-based routing
* centralized SSL handling
* enterprise traffic management

Companies needed a smarter solution.

That solution is: **`Ingress`**

---

# Problem 2 — Too Many Public IPs and High Cloud Cost

This is another huge real-world problem.

---

# Understanding with a Real-Life Example

Imagine a building with 100 offices.

Without Ingress:

* every office has its own separate main gate

That means:

* 100 gates
* 100 security systems
* 100 guards
* huge cost

This is exactly what happens with Kubernetes LoadBalancer Services.

---

# What Happens with LoadBalancer Services

Suppose you have these microservices:

* Login Service
* Search Service
* Payment Service
* Notification Service

If every Service uses:

```yaml
type: LoadBalancer
```

then cloud providers create:

* separate cloud load balancers
* separate public IPs

for every Service.

---

# Why This Becomes Expensive

Cloud providers like:

* Amazon Web Services
* Google Cloud
* Microsoft Azure

charge money for:

* Load Balancers
* Public IPs
* Network resources

Small projects may survive this.

But real companies may have:

* 100 services
* 500 services
* even 1000 microservices

Creating:

* 1000 Load Balancers
* 1000 Public IPs

would become extremely expensive.

---

# How Ingress Solves This Problem

Ingress introduces the idea of:

# Single Entry Point

Instead of:

* one LoadBalancer per Service

we use:

* one Ingress Controller
* one public IP

Then traffic is intelligently routed internally.

Example:

```text
/login    → Login Service
/payment  → Payment Service
/search   → Search Service
```

All through:

* one external IP
* one Load Balancer

This dramatically reduces:

* cloud cost
* public IP usage
* infrastructure complexity

---

# What is Kubernetes Ingress?

Ingress is a Kubernetes resource that acts like a:

* smart traffic router
* gatekeeper
* reverse proxy
* centralized entry point

It receives external HTTP/HTTPS traffic and forwards requests to the correct Kubernetes Services based on rules.

---

# Simple Definition

Ingress is a Layer 7 routing mechanism in Kubernetes that routes HTTP and HTTPS traffic to different Services based on:

* URL paths
* domain names
* routing rules

---

# Service vs Ingress

| Feature                 | Service | Ingress  |
| ----------------------- | ------- | -------- |
| Works at                | Layer 4 | Layer 7  |
| Understands URL Path    | No      | Yes      |
| Understands Domain Name | No      | Yes      |
| SSL/TLS Support         | Basic   | Advanced |
| Single Entry Point      | No      | Yes      |
| Advanced Routing        | No      | Yes      |
| Cost Efficient          | Less    | More     |

---

# Layer 4 vs Layer 7

Kubernetes Services work at:

# Layer 4

They understand:

* IP addresses
* Ports

Ingress works at:

# Layer 7

It understands:

* HTTP
* HTTPS
* URLs
* Paths
* Hostnames
* Headers

This makes Ingress much smarter.

---

# How Ingress Works

Ingress works using two components:

1. Ingress Controller
2. Ingress Resource

Both are required.

Without an Ingress Controller, Ingress rules do nothing.

---

# 1. Ingress Controller

The Ingress Controller is the actual software handling traffic.

Examples:

* NGINX Ingress Controller
* HAProxy Ingress Controller
* Traefik
* Envoy

It continuously watches Kubernetes for Ingress rules.

It receives incoming traffic and applies routing logic.

---

# 2. Ingress Resource

Ingress Resource is a YAML configuration file containing routing rules.

Example:

* which domain goes where
* which path goes where
* TLS settings
* backend Services

---

# Ingress Traffic Flow

Here is the full flow:

```text
User Request
     ↓
Single Public IP
     ↓
Ingress Controller
     ↓
Ingress Rules
     ↓
Correct Kubernetes Service
     ↓
Correct Pod
```

---

# Step-by-Step Example

User opens:

```text
my-website.com/login
```

## Step 1 — Request Arrives

Traffic reaches:

* Ingress Controller

through:

* one public IP

---

## Step 2 — Controller Reads Rules

Ingress Controller checks rules:

```text
/login → login-service
```

---

## Step 3 — Traffic Routed

Traffic forwarded to:

```text
login-service
```

---

## Step 4 — Service Sends to Pod

Service forwards request to the correct Pod.

---

# Why Ingress is Powerful

Ingress provides:

* centralized routing
* centralized SSL handling
* advanced traffic control
* reduced cloud cost
* single entry point
* production-grade networking

This is why almost all production Kubernetes environments use Ingress.

---

# Kubernetes Ingress Architecture

```text
Internet
    ↓
Cloud Load Balancer
    ↓
Ingress Controller
    ↓
Ingress Rules
    ↓
Kubernetes Services
    ↓
Pods
```

---

# Example Ingress YAML

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  name: my-ingress

spec:
  ingressClassName: nginx

  rules:
  - host: my-website.com

    http:
      paths:
      - path: /login
        pathType: Prefix

        backend:
          service:
            name: login-service
            port:
              number: 80
```

---

# Understanding This YAML

## ingressClassName

```yaml
ingressClassName: nginx
```

Specifies which Ingress Controller should handle this Ingress.

Example:

* nginx
* haproxy
* traefik

Useful when multiple controllers exist.

---

## Host

```yaml
host: my-website.com
```

Specifies the domain name.

---

## Path

```yaml
path: /login
```

If user accesses:

```text
my-website.com/login
```

traffic goes to backend Service.

---

## Backend Service

```yaml
name: login-service
```

This is the Kubernetes Service receiving traffic.

Ingress never directly talks to Pods.

It always forwards traffic to Services first.

---

# Important Concept

Ingress does NOT replace Services.

Ingress works together with Services.

Flow is always:

```text
Ingress → Service → Pod
```

---

# Installing NGINX Ingress Controller in Minikube

For local Kubernetes environments like Minikube:

```bash
minikube addons enable ingress
```

This installs:

* NGINX Ingress Controller
* ingress-nginx namespace

---

# Useful Commands

## Check Ingress Controller Pods

```bash
kubectl get pods -A | grep nginx
```

---

## Apply Ingress YAML

```bash
kubectl apply -f ingress.yaml
```

---

## Check Ingress

```bash
kubectl get ingress
```

---

## Check Controller Logs

```bash
kubectl logs -n ingress-nginx <pod-name>
```

---

# Local Domain Testing

For local testing, modify:

```bash
/etc/hosts
```

Example:

```text
192.168.49.2 my-website.com
```

Now browser requests for:

```text
my-website.com
```

will resolve to your local cluster IP.

---

# Final Summary

Ingress is one of the most important Kubernetes networking concepts.

It solves major production problems by providing:

* intelligent routing
* path-based routing
* host-based routing
* centralized SSL/TLS
* single entry point
* lower infrastructure cost
* enterprise-grade traffic management

Without Ingress:

* every Service may need its own LoadBalancer
* cloud costs increase
* routing becomes difficult

With Ingress:

* one entry point handles everything intelligently

---

# Key Takeaways

| Concept            | Purpose                         |
| ------------------ | ------------------------------- |
| Service            | Exposes Pods                    |
| Ingress            | Smart HTTP/HTTPS routing        |
| Ingress Controller | Actual engine handling traffic  |
| Ingress Resource   | Routing rules                   |
| Path Routing       | Route using `/login`            |
| Host Routing       | Route using domains             |
| TLS                | HTTPS security                  |
| Single Entry Point | One public IP for many Services |

---
