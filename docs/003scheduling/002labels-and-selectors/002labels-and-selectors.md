---
title: Labels, Selectors, and Annotations
layout: default
nav_order: 2
parent: Scheduling
permalink: docs/scheduling/labels-and-selectors
last_modified_date: 2025-04-05
---

# Labels, Selectors, and Annotations

Labels, selectors, and annotations are powerful metadata mechanisms in Kubernetes that help us organize, select, and add non-identifying information to our resources. Understanding these concepts is crucial for effective resource management and scheduling in Kubernetes.

## Labels

Labels are key-value pairs attached to Kubernetes objects that serve as identifying attributes. They allow us to organize and select subsets of objects across our cluster.

### Label Syntax

Labels follow these rules:
- Keys may have an optional prefix and a name, separated by a slash
- The name segment is required
- The key cannot be longer than 63 characters
- Valid characters include alphanumeric, `-`, `_`, and `.`
- The value can be empty or follow similar character constraints as the key

Examples of valid labels:
```yaml
environment: production
app: nginx
tier: frontend
release: stable
version: 1.3.5
app.kubernetes.io/name: mysql
```

### Adding Labels to Resources

We can add labels to resources at creation time:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    environment: production
    app: nginx
    tier: frontend
spec:
  containers:
  - name: nginx
    image: nginx
```

Or modify them later using kubectl:

```bash
# Add a label to a pod
kubectl label pods nginx version=1.0.0

# Overwrite an existing label
kubectl label --overwrite pods nginx environment=staging

# Remove a label
kubectl label pods nginx environment-
```

## Selectors

Selectors allow us to identify and select a set of objects based on their labels. They form the backbone of how Kubernetes creates relationships between resources.

### Types of Selectors

There are two types of selectors:

1. **Equality-Based Selectors**
   - Use `=`, `==` (equals) or `!=` (not equals)
   - Example: `environment=production`, `tier!=frontend`

2. **Set-Based Selectors**
   - Use `in`, `notin` and `exists` operators
   - Example: `environment in (production, staging)`, `!version`, `tier notin (frontend, backend)`

### Using Selectors with kubectl

```bash
# List all pods with environment=production
kubectl get pods -l environment=production

# List all pods with tier is not equal to frontend
kubectl get pods -l tier!=frontend

# List all pods with environment label in production or staging
kubectl get pods -l 'environment in (production,staging)'

# List all pods with a version label
kubectl get pods -l 'version'

# List all pods without a version label
kubectl get pods -l '!version'
```

### Selectors in Kubernetes Resources

Many resources use selectors to establish relationships:

```yaml
# ReplicaSet using selectors
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
    matchExpressions:
      - key: tier
        operator: In
        values:
        - frontend
        - middleware
  template:
    metadata:
      labels:
        app: nginx
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx
```

## Node Labels and NodeSelectors

Node labels can identify hardware capabilities, topology information, or custom categories:

```bash
# Label nodes
kubectl label nodes worker-1 gpu=true
kubectl label nodes worker-1 disk=ssd
```

We can use nodeSelectors to influence pod scheduling:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
  - name: cuda-container
    image: nvidia/cuda
  nodeSelector:
    gpu: "true"
```

## Annotations

Unlike labels, annotations are used to attach non-identifying metadata to objects. Annotations are not used for selection purposes but are ideal for storing additional information about resources.

### Annotation Use Cases

1. **Build/Release Information**: Timestamps, release IDs, git branch, PR numbers
2. **Logging/Monitoring/Analytics**: Endpoints for monitoring tools
3. **Tool Information**: Tool-specific configuration
4. **Deployment Information**: Contact information for teams responsible for the resource
5. **Directives for third-party tools**: Special handling instructions

### Annotation Syntax

Annotations follow similar syntax rules as labels but can store larger pieces of data:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: annotations-demo
  annotations:
    build-version: "2021-01-15-12:54"
    git-commit: "a8f2c100cfc678cd10496faea9fa3c30ce1d3122"
    maintainer: "team-backend@example.com"
    description: "This is a complex pod that demonstrates annotations"
    documentation: "https://example.com/docs/pods/complex-pod"
spec:
  containers:
  - name: nginx
    image: nginx
```

### Working with Annotations

We can manage annotations using kubectl:

```bash
# Add an annotation
kubectl annotate pods nginx build-version="2021-01-15-12:54"

# Overwrite an existing annotation
kubectl annotate --overwrite pods nginx build-version="2021-01-16-13:20"

# Remove an annotation
kubectl annotate pods nginx build-version-
```

### Viewing Annotations

```bash
# View detailed information including annotations
kubectl describe pod nginx

# Output in YAML format with annotations
kubectl get pod nginx -o yaml
```

## Key Differences Between Labels and Annotations

| Feature | Labels | Annotations |
|---------|--------|-------------|
| Purpose | Identifying and selecting | Storing non-identifying metadata |
| Selection | Used with selectors | Not used for selection |
| Size | Limited to 63 characters | Can store larger data |
| Usage | Resource organization | Additional information |
| Impact | Affect scheduling | No impact on scheduling |

## Best Practices

1. **Labels**:
   - Use for resource organization and selection
   - Keep concise and relevant to filtering
   - Establish a consistent labeling scheme
   - Use recommended Kubernetes label standards

2. **Annotations**:
   - Use for metadata that doesn't require filtering
   - Store relevant build and deployment information
   - Document third-party tool configurations
   - Maintain organization contact information

3. **General**:
   - Document your label and annotation strategy
   - Use prefixes to avoid collisions
   - Consider automation for consistent application

<div markdown="block" class="important">
For the CKA exam, we should understand how labels influence scheduling through nodeSelectors, and how selectors establish relationships between resources like pods, services, and deployments.
</div>