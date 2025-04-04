---
title: Kubelet
layout: default
nav_order: 1
parent: Worker Nodes
grand_parent: Architecture
permalink: docs/architecture/worker-nodes/kubelet
last_modified_date: 2025-04-04
---

# Kubelet

The kubelet is the primary node agent that runs on each worker node in a Kubernetes cluster. It takes a set of PodSpecs provided by the Kubernetes API server and ensures that the containers described in those PodSpecs are running and healthy.

## Responsibilities

- **Pod Lifecycle Management**: Creates, starts, and monitors containers that make up pods
- **Volume Mounting**: Mounts volumes to pods as specified in their configuration
- **Secret and ConfigMap Management**: Provides pods with their required secrets and configuration data
- **Container Health Checks**: Executes liveness, readiness, and startup probes
- **Node Status Reporting**: Reports the node's status back to the API server

## How Kubelet Works

1. **Pod Assignment**: The kubelet watches for pod assignments from the API server
2. **Container Creation**: For each assigned pod, kubelet uses the container runtime to create containers
3. **Monitoring**: Continuously monitors containers and reports their status
4. **Garbage Collection**: Cleans up unused containers and images

## Configuration

The kubelet can be configured with various flags and a kubelet configuration file. Some important configuration parameters include:

- `--kubeconfig`: Path to a kubeconfig file that specifies how to connect to the API server
- `--pod-manifest-path`: Directory containing static pod manifests
- `--config`: Path to the kubelet's configuration file

## Static Pods

Kubelet can manage pods that are not scheduled through the API server, known as static pods. These are controlled directly by the kubelet and are typically used for running control plane components in self-hosted Kubernetes deployments.

## CRI (Container Runtime Interface)

Kubelet communicates with container runtimes through the Container Runtime Interface (CRI). This allows Kubernetes to support multiple container runtimes without modifying kubelet code.