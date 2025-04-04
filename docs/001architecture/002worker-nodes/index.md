---
title: Worker Nodes
layout: home
nav_order: 2
has_children: true
parent: Architecture
permalink: docs/architecture/worker-nodes
last_modified_date: 2025-04-04
---

# Worker Nodes

Worker nodes are the machines in a Kubernetes cluster that run your containerized applications. They are responsible for executing the workloads scheduled by the control plane, maintaining network rules, and providing the runtime environment for containers.

## Key Components

Worker nodes consist of several essential components:

- **kubelet**: The primary node agent that communicates with the control plane and ensures containers are running in a Pod.
- **Container Runtime**: Software responsible for running containers (like containerd, CRI-O).
- **kube-proxy**: Maintains network rules on nodes to enable network communication to Pods.

## Worker Node Architecture

Each worker node in a Kubernetes cluster maintains the runtime environment for containers and handles local node-level operations. Worker nodes don't manage the cluster state or scheduling decisions - those responsibilities belong to the control plane.

Worker nodes are where your application workloads actually run, making them a critical part of any Kubernetes deployment.