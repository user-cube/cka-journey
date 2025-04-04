---
title: Deployments
layout: default
nav_order: 3
parent: Core Concepts
permalink: docs/core-concepts/deployments
last_modified_date: 2025-04-04
---

# Deployments

Deployments provide the capability to update underlying application instances seamlessly using rolling updates, revert changes, pause or resume deployments, and scale our applications. They represent one of the most commonly used resources in Kubernetes.

## What is a Deployment?

A Deployment is a higher-level resource that manages ReplicaSets and provides declarative updates to applications. When we create a Deployment, we define the desired state, and the Deployment Controller changes the actual state to match our desired state at a controlled rate.

<div markdown="block" class="note">
Deployments manage ReplicaSets, which in turn manage Pods. This layered approach gives us powerful management capabilities while maintaining separation of concerns.
</div>

## Key Features of Deployments

- **Replication**: Ensures a specified number of pod replicas are running
- **Rolling Updates**: Updates pods in a rolling fashion without downtime
- **Rollbacks**: Reverts to a previous revision if issues are detected
- **Scaling**: Easily scales applications up or down
- **Pausing/Resuming**: Controls the roll out process
- **Deployment History**: Maintains revision history for rollbacks

## Creating a Deployment

Here's a basic Deployment example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
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

We can create this Deployment with:

```bash
kubectl apply -f deployment.yaml
```

## Deployment Strategies

When updating our Deployments, we can choose different strategies:

### Rolling Update (Default)

Rolling updates gradually replace old pods with new ones, maintaining application availability during the update:

```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%        # Maximum number of pods over desired count
      maxUnavailable: 25%  # Maximum number of unavailable pods
```

### Recreate

Recreate strategy terminates all existing pods before creating new ones. This causes downtime but ensures no two versions run simultaneously:

```yaml
spec:
  strategy:
    type: Recreate
```

## Managing Deployments

We can manage our Deployments with various commands:

```bash
# Get information about deployments
kubectl get deployments

# See detailed information about a deployment
kubectl describe deployment nginx-deployment

# Scale a deployment
kubectl scale deployment nginx-deployment --replicas=5

# Update a deployment's image
kubectl set image deployment/nginx-deployment nginx=nginx:1.20
```

## Rolling Back Deployments

One of the most powerful features of Deployments is the ability to roll back to a previous version:

```bash
# Check deployment history
kubectl rollout history deployment/nginx-deployment

# View a specific revision
kubectl rollout history deployment/nginx-deployment --revision=2

# Roll back to the previous revision
kubectl rollout undo deployment/nginx-deployment

# Roll back to a specific revision
kubectl rollout undo deployment/nginx-deployment --to-revision=2
```

## Pausing and Resuming Rollouts

We can pause a Deployment to make multiple changes and then resume it:

```bash
# Pause a rollout
kubectl rollout pause deployment/nginx-deployment

# Make changes
kubectl set image deployment/nginx-deployment nginx=nginx:1.20
kubectl set resources deployment/nginx-deployment -c nginx --limits=cpu=200m,memory=512Mi

# Resume the rollout
kubectl rollout resume deployment/nginx-deployment
```

## Deployment Status

We can check the status of a Deployment rollout:

```bash
kubectl rollout status deployment/nginx-deployment
```

## Blue-Green Deployments

While not directly supported by Kubernetes, we can implement blue-green deployments using labels and services:

1. Create a new Deployment with a different label version
2. Wait for the new Deployment to be ready
3. Switch the Service selector from the old version to the new version
4. Delete the old Deployment when no longer needed

## Canary Deployments

We can implement canary deployments by creating a second Deployment with a subset of replicas:

1. Run the main Deployment with most of the traffic
2. Deploy a small canary Deployment with the new version
3. Gradually increase the canary size as confidence builds
4. Finally, update the main Deployment

## Best Practices

- **Define Resource Limits**: Always specify resource requests and limits
- **Use Liveness and Readiness Probes**: Ensure proper health checking
- **Set Appropriate Update Parameters**: Configure maxSurge and maxUnavailable based on our application needs
- **Maintain Revision History**: Set an appropriate revisionHistoryLimit
- **Use Namespaces**: Organize Deployments in logical namespaces
- **Apply Labels Properly**: Use clear, consistent labeling for easier management

<div markdown="block">
{: .important}
For the CKA exam, we should be comfortable creating, updating, and rolling back Deployments, as well as troubleshooting common Deployment issues.
</div>