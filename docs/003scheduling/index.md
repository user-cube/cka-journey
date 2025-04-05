---
title: Scheduling
layout: home
nav_order: 3
has_children: true
permalink: docs/scheduling
last_modified_date: 2025-04-05
---

# Kubernetes Scheduling

Scheduling in Kubernetes refers to the process of assigning pods to nodes in the cluster. The default scheduler does this automatically based on resource requirements, constraints, and other policies. However, we have many ways to influence and control this process.

## Topics Covered

### Manual Scheduling

While Kubernetes typically handles scheduling automatically, there are scenarios where we might need to manually assign pods to specific nodes. We'll explore how to bypass the scheduler and directly assign pods to nodes using the `nodeName` field.

### Labels and Selectors

Labels and selectors form the foundation of Kubernetes' grouping mechanism. We'll learn how to use these to organize resources and influence scheduling decisions, enabling powerful filtering capabilities for our deployments and services.

### Taints and Tolerations

Taints allow nodes to repel certain pods, while tolerations allow pods to schedule onto nodes with matching taints. This mechanism gives us fine-grained control over which pods can run on which nodes, enabling scenarios like dedicated nodes and preventing pods from scheduling to problematic nodes.

### Node Affinity

Node affinity provides a more expressive way to define pod-to-node scheduling rules based on node labels. We'll explore how to use node affinity for scenarios like hardware-specific workloads, topology-aware scheduling, and workload distribution.

### Resource Requirements and Limits

Properly defining resource requests and limits is crucial for efficient cluster utilization. We'll learn how to specify CPU, memory, and other resource requirements for containers, and how these specifications influence scheduling decisions.

### DaemonSets

DaemonSets ensure that all (or some) nodes run a copy of a specific pod. We'll explore how DaemonSets are useful for cluster-wide activities like monitoring, logging, and networking, and how they interact with node taints and tolerations.

### Static Pods

Static pods are managed directly by the kubelet on a node, without API server involvement. We'll learn how these special pods are used in bootstrapping control plane components and how they can be useful for certain operational scenarios.

### Multiple Schedulers

Kubernetes allows running multiple schedulers simultaneously. We'll explore how to configure and use custom schedulers alongside the default scheduler, enabling specialized scheduling logic for different workloads.

### Scheduler Profiles

Scheduler profiles allow customizing the default scheduler's behavior without writing a custom scheduler. We'll learn how to configure different scheduling profiles to optimize for different use cases within a single cluster.

### Admission Controllers

Admission controllers intercept requests to the Kubernetes API server before object persistence. While not strictly part of scheduling, they play a crucial role in enforcing policies that can affect scheduling. We'll cover important admission controllers like LimitRanger and PodSecurityPolicy.