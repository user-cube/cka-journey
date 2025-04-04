---
title: Kube API Server
layout: home
nav_order: 2
has_children: true
parent: Control Plane
permalink: docs/architecture/kube-api-server
last_modified_date: 2025-02-16
---

# Kubernetes API Server

The Kubernetes API Server is the central hub for all control plane operations. It exposes the Kubernetes API, which serves as the frontend for the Kubernetes control plane, handling all internal and external requests for cluster management.

<div markdown="block">
{: .important }
This is the only component that interacts directly with ETCD. Scheduler and Kublets use kube-apiserver to communicate with ETCD.<br>
With Kubeadm, kube-apiserver, is deployed as a POD inside the cluster. It can be checked inside `kube-system` namespace.
</div>

## Core Responsibilities

The API Server serves as:

- The **gateway** to the cluster for users, tools, and components
- The **validator** for all resource configurations
- The **orchestrator** of all cluster state changes
- The **authorizer** for all operations
- The **persistence layer** interface to etcd

## Key Features

### RESTful API

The Kubernetes API follows REST principles with resources addressable via HTTP URLs and manipulated using standard HTTP methods:

- **GET**: Retrieve resource information
- **POST**: Create new resources
- **PUT/PATCH**: Update existing resources
- **DELETE**: Remove resources

### API Versioning

The API is versioned to ensure compatibility and evolution. API versions follow this pattern:

- **Alpha versions** (v1alpha1): Experimental, may be dropped without notice
- **Beta versions** (v1beta1): Code is well-tested but semantics may change
- **Stable versions** (v1): Maintained for many subsequent versions

### Watch Mechanism

The API Server supports "watch" operations, allowing clients to subscribe to resource changes instead of polling, which is crucial for the controller pattern used throughout Kubernetes.

### Authentication & Authorization

The API Server handles multiple authentication methods:
- Client certificates
- Bearer tokens
- Basic authentication
- Service account tokens
- OpenID Connect tokens

Authorization is managed through:
- RBAC (Role-Based Access Control)
- ABAC (Attribute-Based Access Control)
- Node authorization
- Webhook mode

### Admission Control

The API Server uses a chain of admission controllers to validate and modify resource requests. These controllers run:
- **Mutating admission**: Can modify objects before storage
- **Validating admission**: Can reject objects but not modify them

Common admission controllers include:
- `NamespaceLifecycle`
- `LimitRanger`
- `ServiceAccount`
- `PodSecurity`
- `ResourceQuota`

## Architecture

### Request Flow

When a request is made to the Kubernetes API:

1. **Authentication**: Identifies the requestor
2. **Authorization**: Determines if the authenticated user can perform the requested action
3. **Admission Control**: Validates and potentially modifies the request
4. **Validation**: Ensures the request is well-formed and valid
5. **Persistence**: Stores the resulting resource in etcd

### Performance Considerations

The API Server employs several techniques to maintain performance:
- Resource caching
- Request throttling
- Pagination of large result sets
- Compression of request/response data
- Watch bookmarks to reduce network traffic

## High Availability

In production environments, multiple API Server instances can run simultaneously for:
- Load balancing across instances
- Fault tolerance if an instance fails
- Higher request throughput

## Customization

The API Server is highly configurable through command-line flags and configuration files. Common customizations include:

- Authentication methods
- Authorization modes
- Feature gates
- Audit logging configuration
- Encryption configuration

## Accessing the API Server

The API Server can be accessed through:

- The `kubectl` command-line tool
- Client libraries (Go, Python, Java, etc.)
- Direct HTTP requests to the API
- The web-based Kubernetes Dashboard

## Troubleshooting

Common API Server issues include:
- Authentication problems
- Authorization failures
- Resource limit exceeded errors
- etcd connectivity issues
- Performance degradation under load

Diagnosing API Server problems typically involves:
- Examining API Server logs
- Checking etcd health
- Reviewing audit logs
- Monitoring resource usage