---
title: Docker vs ContainerD
layout: default
nav_order: 1
parent: Container Runtime
grand_parent: Worker Nodes
permalink: docs/architecture/worker-nodes/container-runtime/dockervscontainerd
last_modified_date: 2025-04-04
---

# Docker

Docker is not just a container runtime but a comprehensive toolset consisting of:
- **CLI**: User-friendly command-line interface for managing containers
- **API**: Programmatic interface for container management
- **Build**: Image building capabilities (Dockerfile)
- **Volumes**: Persistent storage management
- **Auth**: Authentication to image registries
- **Security**: Security scanning and access controls
- **ContainerD**: The actual container runtime component

Docker provides a complete ecosystem for container development, deployment, and management. This made it popular among developers, but its complexity was unnecessary for Kubernetes, which only needed the core container runtime functionality.

## Dockershim

Dockershim was a compatibility layer in Kubernetes that allowed it to use Docker as a container runtime. It was completely removed in Kubernetes 1.24. Container images built with Docker continue to be compatible with current Kubernetes versions, but Docker itself can no longer be used as the container runtime.

The removal of Dockershim was necessary because:
- It required maintaining a separate translation layer between Kubernetes and Docker
- It added complexity to the Kubernetes codebase
- It prevented fully leveraging the Container Runtime Interface (CRI)
- It created an additional potential point of failure

## The Container Runtime Interface (CRI)

To standardize how Kubernetes interacts with different container runtimes, the Container Runtime Interface (CRI) was introduced. CRI defines the API between Kubernetes and the container runtime, allowing for pluggable runtime implementations.

# ContainerD

ContainerD is a lightweight, high-performance container runtime that was extracted from Docker to serve as a standalone runtime. It's now a graduated project within the Cloud Native Computing Foundation (CNCF).

## Key Features

- **Simplicity**: Focused specifically on container runtime operations
- **Performance**: Optimized for production container workloads
- **Stability**: Designed for mission-critical deployments
- **OCI Compliance**: Follows Open Container Initiative standards
- **Image Management**: Pulls, stores, and manages container images
- **Native CRI Support**: Direct implementation of Kubernetes' CRI

## Tools for ContainerD

When installed, ContainerD comes with `ctr`, which is a basic CLI tool not designed for end users. It's limited in functionality and not particularly user-friendly.

The recommended tool for interacting with ContainerD is `nerdctl`, which provides a Docker-compatible CLI. This makes the transition from Docker to ContainerD smoother for users already familiar with Docker commands.

### nerdctl vs Docker CLI

Examples of equivalent commands:

| Task | Docker | nerdctl |
|------|--------|---------|
| Run a container | `docker run nginx` | `nerdctl run nginx` |
| List containers | `docker ps` | `nerdctl ps` |
| Build an image | `docker build -t myapp .` | `nerdctl build -t myapp .` |
| Pull an image | `docker pull alpine` | `nerdctl pull alpine` |

## Namespace Support

ContainerD supports namespaces, allowing different clients to operate in isolation. When using `nerdctl` with Kubernetes, you typically use the `k8s.io` namespace:

```bash
nerdctl --namespace k8s.io ps
```

## Migration from Docker to ContainerD

When migrating from Docker to ContainerD in Kubernetes:

1. **Container Images**: No changes needed, OCI-compliant images work with both
2. **Deployment Scripts**: Update any scripts that interact directly with Docker
3. **Development Workflow**: Docker can still be used for local development
4. **Logging & Monitoring**: Update to capture ContainerD logs instead of Docker logs

## Performance Comparison

ContainerD generally provides better performance in Kubernetes environments due to:
- Reduced complexity and fewer components
- Direct CRI implementation without translation layers
- Optimized for Kubernetes workloads
- Lower resource consumption

This performance difference becomes more significant in large-scale deployments with hundreds or thousands of containers.