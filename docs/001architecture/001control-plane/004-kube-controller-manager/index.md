---
title: Kube Controller Manager
layout: home
nav_order: 4
has_children: true
parent: Control Plane
permalink: docs/architecture/control-plane/kube-controller-manager
last_modified_date: 2025-02-16
---

# Kubernetes Controller Manager

The Kubernetes Controller Manager is a control plane component that runs controller processes to regulate the state of the Kubernetes cluster. These controllers watch the current state of the cluster through the Kubernetes API and work to make changes to bring the cluster to the desired state.

<div markdown="block">
{: .important }
With Kubeadm, kube controller manager, is deployed as a POD inside the cluster. It can be checked inside `kube-system` namespace.
</div>


## Core Concept

The Controller Manager implements the Kubernetes control loop pattern:
1. **Observe** the current state of the cluster
2. **Compare** with the desired state
3. **Take action** to reconcile differences
4. **Repeat** continuously

This pattern is fundamental to Kubernetes' declarative approach to cluster management.

## Key Controllers

The Controller Manager runs multiple controllers as a single process, including:

### Node Controller

- Monitors node health and availability
- Responds when nodes become unreachable
- Manages node lifecycle (creation, deletion, updates)
- Maintains accurate node status in the API

### Replication Controller

- Ensures the specified number of pod replicas are running
- Creates or deletes pods as needed
- Predecessor to the more advanced ReplicaSet controller

### Deployment Controller

- Manages Deployment resources
- Handles rolling updates and rollbacks
- Maintains deployment history
- Scales applications up or down

### StatefulSet Controller

- Manages applications with persistent state
- Ensures ordered pod creation, updates, and deletion
- Maintains stable network identities and persistent storage

### Job and CronJob Controllers

- **Job Controller**: Manages one-time tasks to completion
- **CronJob Controller**: Schedules Jobs on a time-based schedule

### Endpoint Controller

- Creates Endpoints objects to connect Services with Pods
- Updates Endpoints when Pod IP addresses change
- Handles readiness status changes

### Service Account & Token Controllers

- Creates default service accounts for namespaces
- Manages API access tokens for service accounts

### Namespace Controller

- Handles namespace lifecycle
- Cleans up resources when namespaces are deleted

### Persistent Volume Controller

- Manages the lifecycle of persistent volumes and claims
- Handles volume provisioning, binding, and recycling

## Controller Design Principles

Kubernetes controllers follow several key design principles:

### Level Triggering

Controllers don't just respond to events but continuously reconcile state. This makes them resilient to missed events and helps recover from failures.

### Eventual Consistency

Controllers don't require immediate consistency. They keep working until the desired state is achieved, handling transient failures along the way.

### Single Responsibility

Each controller focuses on a specific resource type, making the system modular and easier to maintain.

### Optimistic Concurrency

Controllers use resource versions to detect and handle conflicting updates to resources.

## High Availability

In production environments, multiple Controller Manager instances can run simultaneously:
- Only one instance is active (leader election)
- Others remain on standby
- Automatic failover if the leader fails

## Configuration

The Controller Manager is highly configurable through command-line flags:

- Controller-specific parameters
- Reconciliation periods
- Resource thresholds
- Concurrent workers
- Leader election settings

## Custom Resource Controllers

The Kubernetes controller pattern can be extended to custom resources:

- **Custom Resource Definitions (CRDs)**: Define new resource types
- **Custom Controllers**: Implement logic for these resources
- **Operator Pattern**: Bundle CRDs and controllers to automate complex applications

## Troubleshooting

Common Controller Manager issues include:

- High CPU/memory usage
- Controller reconciliation failures
- Leader election problems
- Configuration errors

Diagnostic approaches:

- Examining Controller Manager logs
- Monitoring controller metrics
- Checking resource events
- Inspecting controller configurations

## Best Practices

- Run multiple Controller Manager instances for high availability
- Monitor controller metrics for performance issues
- Set appropriate resource requests and limits
- Configure controllers for your cluster size and workload