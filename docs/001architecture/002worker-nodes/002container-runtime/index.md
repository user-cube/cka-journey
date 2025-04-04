---
title: Container Runtime
layout: default
nav_order: 2
parent: Worker Nodes
grand_parent: Architecture
permalink: docs/architecture/worker-nodes/container-runtime
last_modified_date: 2025-04-04
---


# Container Runtime

The container runtime is the software responsible for running containers on a Kubernetes worker node. It handles container image operations, container execution, supervision, and cleanup.

## Container Runtime Interface (CRI)

Kubernetes uses the Container Runtime Interface (CRI) to interact with different container runtimes. This allows Kubernetes to support various runtime implementations without changing the core code.

## Supported Container Runtimes

### containerd

containerd is a lightweight, high-performance container runtime that implements the CRI. It's the default runtime for most Kubernetes deployments.

**Key features:**
- Isolated runtime for container execution
- Image management
- Native CRI support
- Low overhead

### CRI-O

CRI-O is a lightweight container runtime specifically designed for Kubernetes. It implements the CRI and uses OCI (Open Container Initiative) compatible runtimes to run containers.

**Key features:**
- Optimized for Kubernetes
- Minimal dependencies
- Security-focused
- OCI-compliant

### Docker Engine (via cri-dockerd)

While Docker was the original container runtime for Kubernetes, direct Docker Engine support was deprecated in favor of CRI-compatible runtimes. Kubernetes can still use Docker Engine via the cri-dockerd adapter.

## Container Runtime Operations

Container runtimes perform several key operations:
- Pulling images from registries
- Unpacking container images
- Creating and managing container filesystems
- Executing container processes
- Managing container resources
- Cleaning up container resources

## Runtime Selection

When setting up a Kubernetes cluster, you'll need to choose a container runtime. The selection depends on factors like:
- Performance requirements
- Security needs
- Existing infrastructure and expertise
- Resource constraints