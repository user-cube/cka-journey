---
title: Architecture
layout: home
nav_order: 2
has_children: true
permalink: docs/architecture
last_modified_date: 2025-02-16
---
# Architecture

![architecture](/assets/images/001architecture/kubernetes-cluster-architecture.svg)


## Control Plane
The Control Plane is responsible for managing the cluster’s state and includes the following components:

* kube-apiserver: The central API server that facilitates communication within the cluster.
* etcd: A distributed key-value store that maintains cluster configuration and state.
* scheduler (kube-scheduler): Assigns Pods to worker nodes based on resource availability.
* controller manager (kube-controller-manager): Runs controllers that ensure the desired cluster state.

## Worker Nodes
Worker nodes execute application workloads and contain:

* kubelet: The agent responsible for managing Pods and ensuring container runtime interaction.
* kube-proxy: Handles network communication and load balancing between services.
* container runtime: It is responsible for managing the execution and lifecycle of containers within the Kubernetes environment.
