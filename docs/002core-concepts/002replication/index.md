---
title: Replication
layout: default
nav_order: 2
parent: Core Concepts
permalink: docs/core-concepts/replication
last_modified_date: 2025-04-04
---

# Replication

In Kubernetes, replication ensures that a specified number of pod replicas are running at any given time. It provides reliability, load balancing, and scalability to your applications.

## Why Replication Matters

- **High Availability**: Keep applications running even if pods fail
- **Load Balancing**: Distribute traffic across multiple instances
- **Scaling**: Easily adjust the number of instances based on demand
- **Rolling Updates**: Update applications without downtime

## Replication Controllers

Replication Controller was the original Kubernetes replication mechanism. It ensures that a specified number of pod replicas are running at any time. If pods fail, are deleted, or are terminated, the Replication Controller replaces them to maintain the desired count.

<div markdown="block" class="note">
Replication Controller is considered legacy. While still supported, new workloads should use ReplicaSets and Deployments instead.
</div>

## ReplicaSets

ReplicaSet is the next-generation Replication Controller, providing the same functionality but with more flexible selector support. ReplicaSets use set-based selectors instead of equality-based selectors.

Key advantages of ReplicaSets over Replication Controllers:
- More expressive label selectors
- Better integration with Deployments
- Improved reconciliation logic

## Key Differences

| Feature | Replication Controller | ReplicaSet |
|---------|------------------------|------------|
| API version | v1 | apps/v1 |
| Selector support | Equality-based | Set-based (includes equality) |
| Usage | Direct (legacy) | Usually via Deployments |
| Label requirements | Must match exactly | Can use `In`, `NotIn`, `Exists`, etc. |

Although you can work directly with ReplicaSets, it's generally better to use Deployments which manage ReplicaSets and provide additional features like rolling updates and rollbacks.

<div markdown="block" class="important">
For the CKA exam, understand both ReplicationController and ReplicaSet, but focus more on ReplicaSets as they represent the current best practice.
</div>

See the child pages for detailed information about each replication mechanism.
