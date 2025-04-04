---
title: Pods
layout: default
nav_order: 1
parent: Core Concepts
permalink: docs/core-concepts/pods
last_modified_date: 2025-04-04
---

# Pods

In Kubernetes, containers are encapsulated within an abstraction called a Pod. Pods are the smallest deployable units in Kubernetes and represent a single instance of an application.

<div markdown="block">
{: .important }
Pods are ephemeral by nature. They are not designed to be persistent and may be terminated and replaced at any time.
</div>

## What is a Pod?

A Pod is a logical host for one or more containers that:
- Share the same network namespace (IP address and port space)
- Can communicate using localhost
- Share the same storage volumes
- Share the same lifecycle (created and destroyed together)

Most commonly, Pods have a one-to-one relationship with application containers, but multi-container Pods are also possible and useful in many scenarios.

## Single-Container Pods

Single-container Pods are the most common use case in Kubernetes. They represent a straightforward approach to running applications:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.19
    ports:
    - containerPort: 80
```

### Key Benefits of Single-Container Pods

1. **Simplicity**: Easier to manage and understand
2. **Isolation**: Each application runs in its own Pod
3. **Scalability**: Can independently scale different application components

### Example Use Cases

- Web servers
- API services
- Batch processing jobs
- Databases

## Multi-Container Pods

Multi-container Pods host multiple cooperating containers that work together as a single unit. These containers share resources and are always scheduled on the same node.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-app
  labels:
    app: web
spec:
  containers:
  - name: web-server
    image: nginx:1.19
    ports:
    - containerPort: 80
  - name: log-agent
    image: log-collector:1.0
    volumeMounts:
    - name: logs-volume
      mountPath: /var/log/nginx
  volumes:
  - name: logs-volume
    emptyDir: {}
```

### Common Patterns for Multi-Container Pods

1. **Sidecar**: Enhances the main container (e.g., log collectors, sync services)
2. **Ambassador**: Proxies network connections to/from the main container
3. **Adapter**: Standardizes and normalizes output from the main container

### Communication Between Containers

Containers within the same Pod can communicate with each other using:
- `localhost`: They share the same network namespace
- Shared volumes: They can read/write to the same storage
- IPC (Inter-Process Communication): They share the same IPC namespace

<div markdown="block" class="warning">
While multi-container Pods are powerful, avoid making Pods too complex. If containers don't need to share resources, consider using separate Pods.
</div>

## Pod Lifecycle

A Pod goes through several phases during its lifetime:
- **Pending**: Pod accepted but containers not yet created
- **Running**: At least one container is running
- **Succeeded**: All containers terminated successfully
- **Failed**: At least one container terminated with failure
- **Unknown**: Pod state cannot be determined

## Pod Resource Management

Pods can define resource requests and limits for the containers they run:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-demo
spec:
  containers:
  - name: app
    image: app:1.0
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

<div markdown="block" class="note">
CPU is specified in cores or millicores (1000m = 1 CPU core). Memory is specified in bytes, with suffixes like Mi (mebibytes) or Gi (gibibytes).
</div>

## Pod Configuration

Pods can be configured with:
- **Environment Variables**: Pass configuration to applications
- **ConfigMaps**: Decouple configuration from Pod definition
- **Secrets**: Store sensitive information
- **Volumes**: Attach storage to Pods

## Best Practices

1. **Keep Pods focused**: Each Pod should serve a single purpose
2. **Use labels and annotations**: Organize and annotate your Pods for better management
3. **Define resource limits**: Always specify resource requests and limits
4. **Use readiness and liveness probes**: Ensure application health is monitored
5. **Implement graceful shutdown handling**: Applications should handle SIGTERM signals


<div markdown="block">
{: .important }
Although we can create Pods directly, it's generally better to use controllers like Deployments or StatefulSets that manage Pods.
</div>