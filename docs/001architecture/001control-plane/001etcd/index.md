---
title: etcd
layout: home
nav_order: 1
has_children: true
parent: Control Plane
permalink: docs/architecture/control-plane/etcd
last_modified_date: 2025-04-04
---

# etcd

etcd is a distributed, reliable key-value store that serves as Kubernetes' primary datastore. It stores the complete state of the cluster including configurations, specifications, and current status information of all resources.

<div markdown="block">
{: .important }
With Kubeadm, ETCD, is deployed as a POD inside the cluster. It can be checked inside `kube-system` namespace.
</div>

## What is etcd?

etcd is:
- A **distributed key-value store** built on the Raft consensus algorithm
- The **single source of truth** for a Kubernetes cluster
- **Highly available** through a distributed architecture
- **Strongly consistent** in its data storage guarantees
- A **critical component** of the Kubernetes control plane

## Key Features

### Distributed Consensus

etcd uses the Raft consensus algorithm to maintain consistency across multiple nodes. This allows the cluster to continue functioning even if some members fail, as long as a majority (quorum) of nodes remain available.

### Key-Value Storage

Data in etcd is organized as a hierarchical key-value store. Kubernetes uses keys that follow a logical structure to organize different types of resources:

```
/registry/pods/default/nginx-pod
/registry/services/default/my-service
/registry/deployments/kube-system/kube-dns
```

### Watch Mechanism

etcd provides a "watch" capability that notifies clients when keys change. This is used extensively by Kubernetes controllers to respond to changes in the cluster state without constant polling.

### Version Control

Every change to etcd data is assigned a revision number. This allows clients to track changes over time and implement features like optimistic concurrency control.

## etcd in Kubernetes

### Role in the Control Plane

In a Kubernetes cluster, etcd:
- Stores all API objects (Pods, Services, ConfigMaps, etc.)
- Provides the persistence layer for the Kubernetes API server
- Enables the statefulness of a fundamentally stateless system
- Supports the distributed nature of multiple control plane nodes

### Deployment Models

etcd can be deployed in two primary ways in Kubernetes:

1. **Stacked etcd**: etcd runs on the same nodes as other control plane components
   - Simpler to set up and manage
   - More resource-efficient for smaller clusters
   - Higher risk if a node fails (affects multiple components)

2. **External etcd**: etcd runs on dedicated nodes separate from other control plane components
   - Improved fault tolerance
   - Better isolation and resource allocation
   - More complex to set up and maintain
   - Recommended for production clusters

## Performance and Scalability

etcd is designed to handle the needs of Kubernetes clusters, but it has some performance considerations:

- **Memory usage**: etcd keeps its entire dataset in memory
- **Storage performance**: Requires low-latency storage for optimal performance
- **Network latency**: Sensitive to network delays between etcd members
- **Cluster size**: Generally recommended to run 3 or 5 etcd members (odd numbers to maintain quorum)

## Backup and Disaster Recovery

Regular etcd backups are critical for Kubernetes clusters. There are two primary backup methods:

1. **Snapshot-based backup**: Creates a point-in-time snapshot of the etcd datastore
2. **Incremental backup**: Captures changes since the last snapshot

Restoration from backups is a critical operation that can fully recover a cluster after catastrophic failures.

## Monitoring etcd

Key metrics to monitor include:
- Disk I/O latency
- Raft proposal success rate
- Leader elections and changes
- Database size and growth rate
- Memory usage

## Security Considerations

etcd security best practices include:
- TLS encryption for all client and peer communications
- Client certificate authentication
- Role-based access control (RBAC)
- Firewall rules to limit access to etcd ports