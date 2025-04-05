---
title: Declarative vs Imperative
layout: default
nav_order: 6
parent: Core Concepts
permalink: docs/core-concepts/declarative-vs-imperative
last_modified_date: 2025-04-05
---

# Declarative vs Imperative Approach

In Kubernetes, we have two fundamental approaches to managing resources: imperative and declarative. Understanding the differences between these approaches is crucial for effective cluster management and is particularly important for the CKA exam.

## Imperative Approach

With the imperative approach, we tell Kubernetes exactly what to do through specific commands. We're focused on the steps to achieve a desired outcome rather than describing the outcome itself.

Common imperative commands include:

```bash
kubectl run nginx --image=nginx
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80
kubectl edit deployment nginx
kubectl scale deployment nginx --replicas=5
kubectl set image deployment/nginx nginx=nginx:1.19
kubectl create -f nginx.yaml
kubectl replace -f nginx.yaml
kubectl delete -f nginx.yaml
```

### Advantages of Imperative Commands

- Quick for simple tasks
- More intuitive for beginners
- Useful for one-time tasks or troubleshooting
- Faster for small changes that don't require documentation

### Disadvantages of Imperative Commands

- Difficult to track changes over time
- Not suitable for complex applications
- Harder to review and audit
- Not easily repeatable across environments
- No version control integration

## Declarative Approach

With the declarative approach, we define the desired state of our resources in configuration files (usually YAML) and let Kubernetes figure out how to achieve that state. We use a single command for most operations:

```bash
kubectl apply -f nginx.yaml
kubectl apply -f config/  # Apply all files in a directory
```

The `kubectl apply` command considers:
- The local configuration file(s)
- The live object definition in Kubernetes
- The last applied configuration (stored as an annotation)

### How kubectl apply Works

When we use `kubectl apply`, the following happens:
1. The configuration is converted to JSON format
2. Kubernetes compares this with the current live state
3. Only the necessary changes are applied to reach the desired state
4. The last-applied-configuration annotation is updated

### Last-Applied Configuration

The last-applied configuration is stored as an annotation on the resource:

```yaml
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: '{...json representation of the last applied config...}'
```

This annotation helps Kubernetes understand which fields have been removed from the local configuration and should be removed from the live configuration as well.

<div markdown="block">
{: .warning }
Imperative commands do not update this annotation, which can lead to unexpected behavior if we mix imperative and declarative approaches on the same resources.
</div>

### Advantages of Declarative Approach

- Configuration is documented and versioned
- Supports complex, multi-resource applications
- Enables GitOps workflows
- Easier to review and audit
- Repeatable across environments
- Handles dependencies between resources

### Disadvantages of Declarative Approach

- Steeper learning curve
- More verbose for simple tasks
- May require understanding of YAML syntax
- Can be slower for quick, one-time tasks

## When to Use Each Approach

### Use Imperative Commands When:

- We're learning Kubernetes
- We need to quickly troubleshoot or explore
- We're performing one-time tasks
- We're teaching or demonstrating concepts

### Use Declarative Approach When:

- Managing production workloads
- Working with complex applications
- Implementing continuous deployment
- Collaborating with a team
- Maintaining configuration over time

## Best Practices

1. **Consistency**: Try to stick with one approach for a given environment
2. **Documentation**: Document which approach is used in which context
3. **Source Control**: Always store declarative configurations in version control
4. **Validation**: Validate YAML files before applying them
5. **Namespaces**: Use namespaces to isolate resources for better management

<div markdown="block">
{: .information }
For the CKA exam, we need to be proficient with both approaches, as some tasks may be faster to complete with imperative commands while others are better suited to declarative configurations.
</div>

## Examples of Declarative YAML

### Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
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
        image: nginx:1.19
        ports:
        - containerPort: 80
```

### Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
  type: ClusterIP
```

By understanding both imperative and declarative approaches, we can choose the most appropriate method for each scenario, making our Kubernetes operations more efficient and maintainable.
