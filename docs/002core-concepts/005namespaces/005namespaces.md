---
title: Namespaces
layout: default
nav_order: 5
parent: Core Concepts
permalink: docs/core-concepts/namespaces
last_modified_date: 2025-04-05
---

# Namespaces

Namespaces provide a mechanism for isolating groups of resources within a single Kubernetes cluster. They are a way to divide cluster resources between multiple users, teams, or applications.

## Default Namespaces

When a Kubernetes cluster is first created, several default namespaces are automatically established:

- **default**: The namespace where resources are placed when no namespace is specified
- **kube-system**: Contains system components such as networking solutions, DNS services, and control plane components
- **kube-public**: Contains resources that should be publicly available to all users, including unauthenticated users

<div markdown="block">
{: .information }
The `kube-system` namespace is particularly important for troubleshooting, as it contains critical system pods that keep the cluster functioning.
</div>

## Why Use Namespaces?

Namespaces offer several important benefits:

1. **Resource Isolation**: We can separate resources between different teams, projects, or environments
2. **Name Collision Prevention**: Resources can have the same name if they're in different namespaces
3. **Access Control**: We can define different RBAC policies for different namespaces
4. **Resource Quotas**: We can limit resource consumption on a per-namespace basis
5. **Network Policies**: We can define different network policies for each namespace

## Working with Namespaces

### Creating Namespaces

We can create namespaces using a YAML file:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: development
```

Or directly with kubectl:

```bash
kubectl create namespace development
```

### Viewing Namespaces

To list all namespaces:

```bash
kubectl get namespaces
# or shorter
kubectl get ns
```

### Specifying a Namespace

When creating resources, we can specify the namespace in several ways:

1. In the YAML definition:
```yaml
metadata:
  name: my-app
  namespace: development
```

2. Using the `-n` flag with kubectl:
```bash
kubectl create deployment nginx --image=nginx -n development
```

3. Setting the default namespace for the current context:
```bash
kubectl config set-context --current --namespace=development
```

## Resource Policies and Quotas

Namespaces can have associated policies that define what is and isn't allowed within them:

### Resource Quotas

Resource quotas limit the total resource consumption in a namespace:

```yaml
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
    limits.cpu: "8"
    limits.memory: 16Gi
```

### LimitRanges

LimitRanges define default resource limits for containers in a namespace:

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: dev-limits
  namespace: development
spec:
  limits:
  - default:
      memory: 512Mi
      cpu: 500m
    defaultRequest:
      memory: 256Mi
      cpu: 200m
    type: Container
```

## Cross-Namespace Communication

### Service DNS Resolution

Services within Kubernetes are assigned DNS entries that follow a predictable pattern. When we're accessing services:

1. Within the same namespace, we can use just the service name:
```python
mysql.connect("db-service")
```

2. Across different namespaces, we use the fully qualified domain name:
```python
mysql.connect("db-service.dev.svc.cluster.local")
```

The format for fully qualified service names is:
```
<service-name>.<namespace>.svc.cluster.local
```

This DNS entry is automatically created when a service is established within a namespace.

## Namespace Lifecycle

Namespaces that contain resources cannot be deleted until all resources within them are removed. When we delete a namespace, all resources within it are also deleted.

```bash
kubectl delete namespace development
```

<div markdown="block">
{: .important }
Deleting a namespace is irreversible and removes ALL resources within it. We should be extremely careful with this operation, especially in production environments.
</div>

## Namespaces and kubectl Commands

We can specify the namespace for any kubectl command:

```bash
# List pods in a specific namespace
kubectl get pods -n kube-system

# Describe a service in a specific namespace
kubectl describe service kubernetes -n default

# Delete a deployment in a specific namespace
kubectl delete deployment nginx -n development
```

To set our default namespace for the current context:

```bash
kubectl config set-context --current --namespace=development
```

This allows us to work within a specific namespace without specifying the `-n` flag repeatedly.

## Namespace Best Practices

1. **Use clear naming conventions**: Choose namespace names that clearly indicate their purpose (e.g., `production`, `development`, `team-a`)
2. **Create separate namespaces for different environments**: Isolate production, staging, and development workloads
3. **Implement resource quotas**: Prevent any single namespace from consuming too many cluster resources
4. **Apply network policies**: Control traffic flow between namespaces for better security
5. **Use RBAC for access control**: Restrict who can access and modify resources in each namespace