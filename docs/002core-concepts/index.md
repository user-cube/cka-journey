---
title: Core Concepts
layout: home
nav_order: 2
has_children: true
permalink: docs/core-concepts
last_modified_date: 2025-04-04
---

# Core Concepts

This section covers the fundamental building blocks of Kubernetes that you need to understand for the CKA exam. We'll explore the key resources that make up a Kubernetes application and how they work together.

## What You'll Learn

- **Pods**: The smallest deployable units in Kubernetes that host your application containers
- **ReplicaSets**: Ensure a specified number of pod replicas are running at any given time
- **Replication Controllers**: The predecessor to ReplicaSets, providing basic pod replication
- **Deployments**: Higher-level resources that manage ReplicaSets and provide declarative updates
- **Services**: Networking abstractions that expose applications running on pods
- **Namespaces**: Virtual clusters that provide isolation and organization within a Kubernetes cluster

## Declarative vs. Imperative Approaches

Kubernetes supports two primary approaches to resource management:

### Imperative Commands

With the imperative approach, you directly tell Kubernetes what to do using specific commands:

```bash
kubectl run nginx --image=nginx
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80
```

Advantages:
- Quick for simple tasks
- Good for learning
- Useful for troubleshooting

Disadvantages:
- Difficult to track changes
- Not suitable for complex applications
- Harder to automate and version control

### Declarative Configuration

With the declarative approach, you define the desired state of resources in YAML or JSON files:

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

Advantages:
- Configuration is documented and version-controlled
- Supports complex deployments
- Easier to audit and review
- Ideal for GitOps workflows

Disadvantages:
- Steeper learning curve
- Requires understanding YAML/JSON syntax

In real-world production environments, the declarative approach is strongly recommended, but knowing both approaches is valuable for the CKA exam.