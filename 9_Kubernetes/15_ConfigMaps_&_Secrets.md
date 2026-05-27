# Kubernetes ConfigMaps and Secrets

Applications running inside Kubernetes often need:

* configuration settings
* environment variables
* database passwords
* API keys
* certificates
* application settings

But hardcoding these values directly inside containers is a very bad practice.

Why?

Because:

* configuration changes frequently
* passwords are sensitive
* applications should remain portable
* environments differ (dev, test, prod)

To solve this problem, Kubernetes provides:

1. ConfigMaps
2. Secrets

These are two of the most important Kubernetes configuration management resources.

---

# Why Configuration Management is Important

Imagine an application that needs:

* database host
* database port
* API URL
* log level
* authentication credentials

Hardcoding these values inside application code creates many problems.

---

# Problems with Hardcoding

If configuration is hardcoded:

* changing environment becomes difficult
* rebuilding containers becomes necessary
* passwords become exposed
* security risks increase
* deployments become inflexible

---

# Real-Life Example

Suppose you have a banking application.

Development environment may use:

```text id="cfg1"
dev-database.local
```

Production environment may use:

```text id="cfg2"
prod-database.company.com
```

Application code should NOT change for every environment.

Only configuration should change.

---

# Kubernetes Solution

Kubernetes separates:

* application code
  from:
* application configuration

using:

* ConfigMaps
* Secrets

---

# What is a ConfigMap?

A ConfigMap is a Kubernetes object used to store:

# Non-sensitive configuration data

Examples:

* application settings
* environment variables
* URLs
* feature flags
* configuration files

---

# Simple Definition

> ConfigMap stores non-confidential application configuration data in key-value format.

---

# What Kind of Data Goes Into ConfigMaps?

Examples:

| Configuration | Example         |
| ------------- | --------------- |
| App Mode      | production      |
| API URL       | api.company.com |
| Log Level     | debug           |
| Feature Flags | enabled         |
| Port Numbers  | 8080            |

---

# Important Rule

ConfigMaps should NOT store:

* passwords
* API tokens
* certificates
* sensitive credentials

Sensitive data belongs in:

# Secrets

---

# Real-Life Analogy

Imagine a restaurant kitchen.

Recipe instructions:

* cooking temperature
* ingredient quantities
* serving style

can be publicly visible to kitchen staff.

These are like:

# ConfigMaps

But safe passwords or locker keys must remain secret.

Those are like:

# Secrets

---

# Creating a ConfigMap

There are multiple ways to create ConfigMaps.

---

# Method 1 — Using YAML

---

# Example ConfigMap YAML

```yaml id="cm1"
apiVersion: v1
kind: ConfigMap

metadata:
  name: app-config

data:
  APP_MODE: "production"
  LOG_LEVEL: "debug"
  API_URL: "https://api.company.com"
```

---

# Understanding This YAML

---

## kind

```yaml id="cm2"
kind: ConfigMap
```

Defines Kubernetes ConfigMap object.

---

## data

```yaml id="cm3"
data:
```

Stores key-value pairs.

---

# Apply ConfigMap

```bash id="cm4"
kubectl apply -f configmap.yaml
```

---

# Verify ConfigMap

```bash id="cm5"
kubectl get configmaps
```

or:

```bash id="cm6"
kubectl get cm
```

---

# View ConfigMap Details

```bash id="cm7"
kubectl describe configmap app-config
```

---

# Method 2 — Create from Command Line

```bash id="cm8"
kubectl create configmap app-config \
  --from-literal=APP_MODE=production \
  --from-literal=LOG_LEVEL=debug
```

---

# Method 3 — Create from File

Suppose file:

```text id="cm9"
app.properties
```

contains:

```text id="cm10"
APP_MODE=production
LOG_LEVEL=debug
```

Create ConfigMap:

```bash id="cm11"
kubectl create configmap app-config \
  --from-file=app.properties
```

---

# Using ConfigMaps in Pods

ConfigMaps are commonly used in two ways:

1. Environment Variables
2. Mounted Files

---

# Method 1 — ConfigMap as Environment Variables

---

# Example Pod YAML

```yaml id="cm12"
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
  - name: nginx
    image: nginx

    env:
    - name: APP_MODE
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: APP_MODE
```

---

# What Happens?

Container receives environment variable:

```text id="cm13"
APP_MODE=production
```

---

# Access Inside Container

```bash id="cm14"
echo $APP_MODE
```

---

# Import Entire ConfigMap

Instead of importing one key manually:

```yaml id="cm15"
envFrom:
- configMapRef:
    name: app-config
```

Now all ConfigMap keys become:

* environment variables

---

# Method 2 — ConfigMap as Mounted Volume

ConfigMaps can also appear as files inside containers.

---

# Example

```yaml id="cm16"
volumes:
- name: config-volume
  configMap:
    name: app-config
```

---

# Mount Inside Container

```yaml id="cm17"
volumeMounts:
- name: config-volume
  mountPath: /etc/config
```

---

# Result

Files created inside container:

```text id="cm18"
/etc/config/APP_MODE
/etc/config/LOG_LEVEL
```

---

# Why Mounted Files Are Useful

Some applications expect:

* configuration files
  instead of:
* environment variables

Examples:

* NGINX
* Apache
* Java applications

---

# What is a Secret?

A Secret is similar to ConfigMap but designed for:

# Sensitive Data

Examples:

* passwords
* API keys
* SSH keys
* certificates
* authentication tokens

---

# Simple Definition

> Secret stores confidential data securely inside Kubernetes.

---

# Important Difference Between ConfigMap and Secret

| Feature | ConfigMap | Secret |
|---|---|
| Sensitive Data | No | Yes |
| Intended for Passwords | No | Yes |
| Base64 Encoded | No | Yes |

---

# Important Reality

Secrets are:

* Base64 encoded
  NOT fully encrypted by default.

This is extremely important.

---

# Base64 Is NOT Encryption

Many beginners misunderstand this.

Base64 only:

* encodes data
* makes it unreadable at glance

But it can easily be decoded.

---

# Production Recommendation

Real production environments should use:

* encryption at rest
* external secret managers

Examples:

* HashiCorp Vault
* Amazon Web Services Secrets Manager
* Google Cloud Secret Manager

---

# Creating a Secret

---

# Method 1 — Using YAML

---

# Example Secret YAML

```yaml id="sec1"
apiVersion: v1
kind: Secret

metadata:
  name: db-secret

type: Opaque

data:
  username: YWRtaW4=
  password: cGFzc3dvcmQxMjM=
```

---

# Important Part

```yaml id="sec2"
data:
```

Values are Base64 encoded.

---

# Example Encoding

```text id="sec3"
admin
```

becomes:

```text id="sec4"
YWRtaW4=
```

---

# Generate Base64

Linux example:

```bash id="sec5"
echo -n 'admin' | base64
```

---

# Apply Secret

```bash id="sec6"
kubectl apply -f secret.yaml
```

---

# View Secrets

```bash id="sec7"
kubectl get secrets
```

---

# Describe Secret

```bash id="sec8"
kubectl describe secret db-secret
```

---

# View Actual Secret Data

```bash id="sec9"
kubectl get secret db-secret -o yaml
```

---

# Decode Secret

```bash id="sec10"
echo "YWRtaW4=" | base64 --decode
```

---

# Using Secrets in Pods

Secrets can also be used as:

* environment variables
* mounted files

---

# Secret as Environment Variable

```yaml id="sec11"
env:
- name: DB_USERNAME
  valueFrom:
    secretKeyRef:
      name: db-secret
      key: username
```

---

# Secret as Volume

```yaml id="sec12"
volumes:
- name: secret-volume
  secret:
    secretName: db-secret
```

---

# Mount Secret

```yaml id="sec13"
volumeMounts:
- name: secret-volume
  mountPath: /etc/secrets
```

---

# Result

Files created:

```text id="sec14"
/etc/secrets/username
/etc/secrets/password
```

---

# Secret Types

Kubernetes supports different Secret types.

---

# Common Secret Types

| Type                                | Purpose                |
| ----------------------------------- | ---------------------- |
| Opaque                              | Generic secrets        |
| kubernetes.io/dockerconfigjson      | Docker registry auth   |
| kubernetes.io/tls                   | TLS certificates       |
| kubernetes.io/service-account-token | Service account tokens |

---

# Example TLS Secret

Used for:

* HTTPS certificates
* Ingress TLS

Example:

```bash id="sec15"
kubectl create secret tls my-tls-secret \
  --cert=tls.crt \
  --key=tls.key
```

---

# ConfigMap vs Secret

| Feature | ConfigMap | Secret |
|---|---|
| Stores Sensitive Data | No | Yes |
| Base64 Encoded | No | Yes |
| Used for App Config | Yes | Yes |
| Used for Passwords | No | Yes |

---

# Important Production Practices

---

# 1. Never Store Secrets in Git

This is one of the biggest security mistakes.

Avoid committing:

* passwords
* API keys
* certificates

into repositories.

---

# 2. Use External Secret Managers

Large companies often use:

* Vault
* AWS Secrets Manager
* Azure Key Vault

instead of plain Kubernetes Secrets.

---

# 3. Enable Encryption at Rest

Encrypt Secrets inside:

* etcd database

---

# 4. Apply RBAC

Restrict Secret access using:

# RBAC

---

# 5. Rotate Secrets Regularly

Passwords and keys should change periodically.

---

# Updating ConfigMaps and Secrets

When ConfigMaps/Secrets change:

* some applications reload automatically
* others require Pod restart

This depends on:

* application behavior

---

# Restart Deployment After Update

Common production practice:

```bash id="cfgrestart1"
kubectl rollout restart deployment my-app
```

---

# Real Production Examples

---

# ConfigMap Use Cases

| Use Case          | Example           |
| ----------------- | ----------------- |
| App Configuration | URLs, ports       |
| Feature Flags     | enable-login=true |
| Logging           | debug mode        |
| NGINX Config      | nginx.conf        |

---

# Secret Use Cases

| Use Case           | Example        |
| ------------------ | -------------- |
| Database Passwords | MySQL password |
| API Tokens         | GitHub token   |
| TLS Certificates   | HTTPS certs    |
| SSH Keys           | Git access     |

---

# Architecture Summary

```text id="cfgarch1"
Application
    ↓
ConfigMap → Non-sensitive Config
    ↓
Secret → Sensitive Data
```

---

# Final Summary

Kubernetes ConfigMaps and Secrets provide:

* externalized configuration
* secure credential management
* environment flexibility
* cloud-native configuration handling

Main differences:

| Resource  | Purpose                     |
| --------- | --------------------------- |
| ConfigMap | Non-sensitive configuration |
| Secret    | Sensitive confidential data |

Both can be used as:

* environment variables
* mounted volumes/files

These resources are essential for:

* production Kubernetes
* secure deployments
* cloud-native applications
* environment management
* enterprise DevOps practices

---
