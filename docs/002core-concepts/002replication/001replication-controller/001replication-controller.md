---
title: Replication Controller vs ReplicaSet
layout: default
nav_order: 1
parent: Replication
grand_parent: Core Concepts
permalink: docs/core-concepts/replication/replication-controller-vs-replicaset
last_modified_date: 2025-04-04
---

# Replication Controller vs ReplicaSet

Both Replication Controller and ReplicaSet help run multiple instances of a pod, providing high availability for our applications. Even with just a single pod, these controllers take action to ensure the desired state is maintained. They also enable load balancing across pod instances as our application scales.

<div markdown="block">
{: .information }
While Replication Controller and ReplicaSet serve similar functions, they are not the same. ReplicaSet is the evolution of Replication Controller and is the preferred option for new deployments.
</div>

## Key Differences

The main difference between Replication Controller and ReplicaSet is that ReplicaSet introduces a required `selector` field with more powerful selection capabilities. ReplicaSet can manage pods that existed before its creation, whereas Replication Controller is more limited in its selection capabilities.

In Replication Controller, the selector is optional - if omitted, it will assume the labels defined in the pod template. In ReplicaSet, the selector is mandatory and more expressive.

## Labels and Selectors

The role of both controllers is to monitor pods and create new ones if any fail. They use labels to identify which pods to monitor.

### How Labels Work

Labels are key-value pairs attached to Kubernetes objects. A selector is a way to filter objects based on these labels.

## ReplicationController Example

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: nginx-rc
spec:
  replicas: 3
  selector:
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

This ReplicationController will ensure three nginx pods are always running.

### Creating and Managing ReplicationController

```bash
# Create a ReplicationController
kubectl create -f nginx-rc.yaml

# View ReplicationControllers
kubectl get replicationcontrollers
# or shorter
kubectl get rc

# Scale a ReplicationController
kubectl scale rc nginx-rc --replicas=5

# Delete a ReplicationController (and its pods)
kubectl delete rc nginx-rc
```

## ReplicaSet Example

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
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

The key difference in this example is the `matchLabels` section under the selector, which is a requirement for ReplicaSet.

### More Advanced Selector Example

ReplicaSet allows for more complex selectors using set-based requirements:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: advanced-rs
spec:
  replicas: 3
  selector:
    matchExpressions:
      - key: app
        operator: In
        values:
          - nginx
          - webserver
      - key: environment
        operator: NotIn
        values:
          - test
  template:
    metadata:
      labels:
        app: nginx
        environment: production
    spec:
      containers:
      - name: nginx
        image: nginx:1.19
```

This ReplicaSet selects pods with the app label set to either "nginx" or "webserver", but not those with environment=test.

### Creating and Managing ReplicaSet

```bash
# Create a ReplicaSet
kubectl create -f nginx-rs.yaml

# View ReplicaSets
kubectl get replicasets
# or shorter
kubectl get rs

# Scale a ReplicaSet
kubectl scale rs nginx-rs --replicas=5

# Delete a ReplicaSet (and its pods)
kubectl delete rs nginx-rs
```

## Comparing Selector Capabilities

| Selector Type | ReplicationController | ReplicaSet |
|---------------|----------------------|------------|
| Equality-based | ✅ | ✅ |
| Set-based | ❌ | ✅ |
| `matchExpressions` | ❌ | ✅ |
| Multiple selectors | ❌ | ✅ |

## When to Use ReplicaSet vs Deployment

While ReplicaSets provide replication capabilities, they lack features like rolling updates. This is why Deployments are generally preferred:

- **Use ReplicaSet directly**: When we need fine-grained control over replication without needing rolling updates
- **Use Deployment**: For most application deployments (Deployments create and manage ReplicaSets behind the scenes)

<div markdown="block">
{: .important }
In modern Kubernetes environments, we'll rarely create ReplicaSets directly. Instead, we should use Deployments which manage ReplicaSets and provide additional capabilities like rolling updates and rollbacks.
</div>

## Migration from ReplicationController to ReplicaSet

If we're using ReplicationControllers, here's how to migrate to ReplicaSets:

1. Create a new ReplicaSet with the same pod template
2. Scale down the ReplicationController to 0
3. Verify the ReplicaSet is working correctly
4. Delete the ReplicationController

Alternatively, we can migrate to Deployments which will create appropriate ReplicaSets.