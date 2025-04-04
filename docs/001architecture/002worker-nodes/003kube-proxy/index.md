---
title: Kube-proxy
layout: default
nav_order: 3
parent: Worker Nodes
grand_parent: Architecture
permalink: docs/architecture/worker-nodes/kube-proxy
last_modified_date: 2025-04-04
---

# Kube-proxy

Kube-proxy is a network proxy that runs on each node in a Kubernetes cluster. It maintains network rules that allow network communication to pods from network sessions inside or outside the cluster.

<div markdown="block">
{: .important }
When installing a cluster with Kubeadm, kube-proxy is installed as a pod using a daemonset. This pod can be found inside `kube-system`.
</div>


## Responsibilities

- Implementing part of the Kubernetes Service concept
- Maintaining network rules on nodes
- Performing connection forwarding
- Load balancing across pod replicas

## Proxy Modes

Kube-proxy can operate in different modes, each with its own characteristics:

### 1. iptables Mode (Default)

In this mode, kube-proxy watches the Kubernetes API server for Service and Endpoint objects and sets up iptables rules to capture traffic to the Service's clusterIP and port, and redirect that traffic to one of the Service's backend Pods.

**Characteristics:**
- Uses native Linux iptables
- Lower system overhead compared to userspace mode
- Random backend selection for load balancing
- Cannot properly detect dead connections when a Pod fails

### 2. IPVS Mode

IPVS (IP Virtual Server) mode provides better scalability and performance for large clusters. It implements transport-layer load balancing as part of the Linux kernel.

**Characteristics:**
- Better performance for large clusters
- More load balancing algorithms (round-robin, least connection, etc.)
- Requires the IPVS Linux kernel modules to be installed

### 3. Userspace Mode (Legacy)

The original implementation where kube-proxy runs in userspace. This mode is less efficient and generally not recommended for production.

**Characteristics:**
- Higher latency as traffic passes through userspace
- More resource-intensive than other modes
- Round-robin load balancing
- Primarily exists for compatibility

## Service Implementation

Kube-proxy is responsible for implementing the networking aspects of Kubernetes Services:

1. **ClusterIP Services**: Traffic to the service IP is redirected to backend pods
2. **NodePort Services**: Exposes the service on each node's IP at a static port
3. **LoadBalancer Services**: Works with cloud provider's load balancer to direct external traffic to nodes

## Configuration

Kube-proxy is typically deployed as a DaemonSet to ensure it runs on every node. Important configuration parameters include:

- `--proxy-mode`: Selects which proxy mode to use (iptables, ipvs, userspace)
- `--cluster-cidr`: CIDR range for pods in the cluster
- `--hostname-override`: Override the node name
- `--iptables-masquerade-bit`: Bit of the iptables fwmark used for masquerading