---
title: Control Plane
layout: home
nav_order: 1
has_children: true
parent: Architecture
permalink: docs/architecture/control-plane
last_modified_date: 2025-02-16
---

# Control Plane

The Control Plane is the core component of a Kubernetes cluster, responsible for managing the cluster’s state, scheduling workloads, and handling API requests. It ensures the desired state of the cluster is maintained and orchestrates the lifecycle of containers.

## Components of the Control Plane

The Kubernetes Control Plane consists of several key components:

### Kube-API Server (kube-apiserver)

- Acts as the front-end for the Kubernetes API.
- Serves as the primary entry point for all administrative operations.
- Validates and processes REST requests before updating the cluster state.
- Communicates with etcd to store and retrieve cluster data.

### Etcd
- A distributed key-value store that serves as Kubernetes’ backing store.
- Maintains the entire cluster state, including node and pod information.
- Ensures consistency across the control plane components.

### Kube-Scheduler (kube-scheduler)
- Assigns new pods to appropriate nodes based on resource availability and constraints.
- Considers factors such as CPU, memory, affinity rules, and taints/tolerations.

### Kube-Controller-Manager (kube-controller-manager)
- Runs multiple controller processes that maintain cluster state automatically.
Important controllers include:
  - Node Controller: Monitors node health and handles node failures.
  - Replication Controller: Ensures the desired number of pod replicas are running.
  - Endpoint Controller: Manages service endpoints.
  - Service Account & Token Controllers: Manages authentication tokens for pods.

### Cloud Controller Manager (cloud-controller-manager)

- Integrates Kubernetes with cloud provider services.
- Manages cloud-based resources like load balancers, networking, and storage.
- Abstracts cloud-specific logic from the core Kubernetes components.

## How the Control Plane Works

1. User or external system sends a request (e.g., kubectl apply -f deployment.yaml).
2. API Server processes the request and updates etcd.
3. Scheduler assigns pods to suitable nodes.
4. Controllers ensure the desired state (e.g., maintaining replicas, reacting to node failures).
5. Kubelet on worker nodes applies changes and starts containers using the container runtime.
6. Continuous reconciliation ensures the cluster stays in the desired state.