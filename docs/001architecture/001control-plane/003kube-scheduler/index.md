---
title: Kube Scheduler
layout: home
nav_order: 3
has_children: true
parent: Control Plane
permalink: docs/architecture/control-plane/kube-scheduler
last_modified_date: 2025-02-16
---

# Kubernetes Scheduler

The Kubernetes Scheduler is a control plane component responsible for assigning newly created pods to nodes in the cluster. It determines the best node for running a pod based on resource requirements, constraints, and other policies.

<div markdown="block">
{: .important }
With Kubeadm, kube-scheduler, is deployed as a POD inside the cluster. It can be checked inside `kube-system` namespace.
</div>


## Core Responsibilities

The Scheduler's primary function is to:

- **Watch** for newly created pods with no assigned node
- **Select** the most suitable node for each pod
- **Inform** the API server of the scheduling decisions
- **Balance** workload distribution across the cluster

## How Scheduling Works

The scheduling process follows a two-step operation:

### 1. Filtering (Predicates)

First, the scheduler filters out nodes that cannot run the pod:
- Nodes without sufficient resources (CPU, memory, GPU)
- Nodes that don't match node selectors or affinity rules
- Nodes with taints that aren't tolerated by the pod
- Nodes that don't have required volumes available

### 2. Scoring (Priorities)

Next, the scheduler ranks the remaining nodes:
- **Resource allocation**: Balancing resource utilization
- **Spreading**: Distributing pods across nodes, zones, or domains
- **Affinity/anti-affinity**: Preferring or avoiding co-location with other pods
- **Taints/tolerations**: Soft preferences for node selection
- **Custom policies**: User-defined scoring functions

Finally, the pod is scheduled to the node with the highest score.

## Scheduling Policies

### Resource Requests and Limits

The scheduler uses pod resource requests (not limits) for making scheduling decisions:
- **CPU requests**: Minimum guaranteed CPU
- **Memory requests**: Minimum guaranteed memory
- **Extended resources**: GPUs, FPGAs, or other specialized hardware

### Node Selection Constraints

Pods can specify node selection requirements:

- **nodeSelector**: Simple key-value pair matching for node labels
- **Node affinity**: More expressive requirements for node selection
  - **requiredDuringSchedulingIgnoredDuringExecution**: Hard requirements
  - **preferredDuringSchedulingIgnoredDuringExecution**: Soft preferences

### Pod Affinity and Anti-Affinity

Pods can specify relationships with other pods:

- **Pod affinity**: Attract pods to each other (co-location)
- **Pod anti-affinity**: Repel pods from each other (separation)
  - Useful for high availability by spreading replicas
  - Important for performance isolation

### Taints and Tolerations

- **Taints**: Properties on nodes that repel pods
- **Tolerations**: Properties on pods that allow (but don't require) scheduling on tainted nodes

Common use cases:
- Dedicated nodes for specific workloads
- Nodes reserved for specific users
- Preventing scheduling on problematic nodes

## Advanced Scheduling

### Multiple Schedulers

Kubernetes supports running multiple schedulers simultaneously:
- Pods can specify which scheduler should handle them
- Custom schedulers can implement domain-specific logic
- Default scheduler continues to handle regular pods

### Scheduler Extenders

External processes that the scheduler calls out to for:
- Additional filtering
- Custom prioritization
- Binding decisions

### Scheduler Profiles

Configure multiple scheduling profiles with different plugin sets:
- Pods can select a profile via the `.spec.schedulerName` field
- Allows different workloads to have different scheduling behaviors
- Introduced in Kubernetes 1.18

## Performance and Capacity

- The scheduler is designed to handle large clusters
- Scheduling decisions are typically made in milliseconds
- Scheduler performance can impact pod startup latency
- In very large clusters, scheduler performance becomes critical

## Scheduler Plugins

The scheduler uses a plugin architecture with extension points:
- **PreFilter**: Run before filtering
- **Filter**: Node filtration
- **PreScore**: Run before scoring
- **Score**: Node scoring
- **Reserve**: Reserve node resources
- **Permit**: Allow or reject scheduling
- **PreBind**: Run before binding
- **Bind**: Bind pod to node
- **PostBind**: Run after binding

## Common Scheduling Scenarios

### High Availability

- Spreading pods across failure domains (nodes, racks, zones)
- Using pod anti-affinity to prevent co-location
- Ensuring critical services don't share fate

### Resource Efficiency

- Bin-packing pods onto nodes to maximize utilization
- Co-locating complementary workloads
- Balancing the cluster to avoid hotspots

### Specialized Hardware

- Directing pods to nodes with GPUs, FPGAs, or other hardware
- Managing access to limited specialized resources
- Isolating high-performance workloads

## Troubleshooting Scheduling

Common issues include:
- **Insufficient resources**: Cluster doesn't have enough CPU, memory, or other resources
- **Affinity/anti-affinity constraints**: Too restrictive conditions
- **Node selectors**: No nodes match the required labels
- **Taints without tolerations**: Pods can't tolerate node taints
- **PersistentVolume availability**: Required volumes not available on nodes

Diagnostic commands:
- `kubectl describe pod <pod-name>`: Check Events section
- `kubectl get events`: View cluster-wide scheduling events
- Scheduler logs: Examine detailed scheduling decisions