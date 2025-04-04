---
title: Services
layout: default
nav_order: 4
parent: Core Concepts
permalink: docs/core-concepts/services
last_modified_date: 2025-04-04
---

# Services

Services in Kubernetes provide a stable networking endpoint for a set of dynamically changing pods. They act as an abstraction layer that decouples frontend applications from the backend pods that provide functionality.

## Why We Need Services

Pods in Kubernetes are ephemeral - they can be created, destroyed, and recreated at any time. Each time a pod is recreated, it receives a new IP address. This creates challenges:

- How do we consistently access our applications when pod IPs change?
- How do we load balance between multiple pod replicas?
- How do we expose our applications outside the cluster?

Services solve these problems by providing a stable IP address and DNS name that remains consistent despite changes to the underlying pods.

## Key Service Types

Kubernetes offers several service types to address different networking requirements:

### ClusterIP (Default)

- Exposes the service on an internal IP within the cluster
- Only accessible within the cluster
- Perfect for internal communication between applications

### NodePort

- Exposes the service on each node's IP at a static port
- Accessible from outside the cluster using `<NodeIP>:<NodePort>`
- Port range is typically limited to 30000-32767
- Useful for development and testing

### LoadBalancer

- Exposes the service externally using a cloud provider's load balancer
- Automatically creates the external load balancer and assigns a fixed, external IP
- The standard way to expose services to the internet in cloud environments

### ExternalName

- Maps the service to a DNS name rather than selecting pods
- Primarily used to create a service alias for an external service
- No proxying is set up, just DNS-level redirection

## Service Discovery

Services provide two mechanisms for discovery:

1. **Environment Variables**: When a pod starts, Kubernetes injects environment variables for each active service
2. **DNS**: Kubernetes provides a DNS server that watches for new services and creates DNS records

## Selecting Pod Targets

Services use label selectors to determine which pods should receive traffic:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: MyApp    # Service will send traffic to all pods with label app=MyApp
  ports:
  - port: 80      # Port exposed by the service
    targetPort: 9376  # Port the pods are listening on
```

## When to Use Different Service Types

- Use **ClusterIP** for internal communication between applications
- Use **NodePort** for development, testing, or when a load balancer isn't available
- Use **LoadBalancer** for production workloads exposed to external users
- Use **ExternalName** when we need to access external services via Kubernetes service discovery

In the following sections, we'll explore each service type in detail and learn how to effectively use them in our Kubernetes applications.
