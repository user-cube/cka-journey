---
title: ClusterIP
layout: default
nav_order: 1
parent: Services
grand_parent: Core Concepts
permalink: docs/core-concepts/services/cluster-ip
last_modified_date: 2025-04-05
---

# ClusterIP

ClusterIP is the default service type in Kubernetes that provides internal network connectivity to a set of pods within the cluster.

```
Internal Client (Pod)
      |
      | HTTP Request to Service Name or ClusterIP
      v
[Kubernetes ClusterIP Service]
      |
      | Routes to any matching pod based on selector
      v
┌─────┴──────┬──────────┐
|            |          |
[Pod A]    [Pod B]    [Pod C]
app=db     app=db     app=db
```

This diagram shows:
1. An internal client (another pod in the cluster) sends a request to the service name or ClusterIP
2. The ClusterIP service receives the request
3. The service routes the traffic to any pod matching the selector
4. One of the matching pods receives the traffic

## Understanding ClusterIP

All pods in Kubernetes have an associated IP address, but these addresses are not static. Pods are ephemeral - they can be created, destroyed, or rescheduled based on various factors, and each time a pod is recreated, it receives a new IP address.

Services allow us to group pods together and randomly distribute traffic to these pods. Each service has a name and an IP address associated with it, and this name should be used by other pods to access the correct backend.

<div markdown="block">
{: .information }
When the service type is not specified, it defaults to ClusterIP.
</div>

## Key Features of ClusterIP

- **Stable Internal IP**: Provides a single, stable IP address for a set of pods
- **Internal Access Only**: Only accessible from within the cluster
- **Load Balancing**: Automatically distributes traffic among all pods matching the service selector
- **Service Discovery**: Enables other applications to discover the service via DNS or environment variables

## When to Use ClusterIP

We should use ClusterIP services when:
- We need internal communication between applications within the cluster
- We want to expose backend services to frontend applications
- We require stable internal endpoints for microservices
- We don't need to expose the service outside the cluster

## ClusterIP Example

Here's an example of a ClusterIP service definition:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  type: ClusterIP  # This is optional as ClusterIP is the default
  selector:
    app: backend
  ports:
  - port: 80       # Port exposed by the service
    targetPort: 8080  # Port the pods are listening on
```

This service will:
1. Create a stable IP address accessible as `backend-service` within the cluster
2. Direct traffic to all pods with the label `app: backend`
3. Forward traffic from port 80 on the service to port 8080 on the pods

## Accessing ClusterIP Services

We can access ClusterIP services in several ways:

### 1. Using the Service DNS Name

Kubernetes automatically creates internal DNS entries for services:

```
<service-name>.<namespace>.svc.cluster.local
```

For services in the same namespace, we can simply use the service name:

```bash
curl backend-service
```

### 2. Using the Service IP

We can use the service's cluster IP directly:

```bash
# First get the IP
kubectl get service backend-service

# Then connect using the IP
curl 10.96.23.45
```

### 3. Environment Variables

Kubernetes automatically injects environment variables for each active service when a pod is created:

```bash
# Inside a pod
echo $BACKEND_SERVICE_SERVICE_HOST
echo $BACKEND_SERVICE_SERVICE_PORT
```

## Multiple Ports

A single ClusterIP service can expose multiple ports:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: multi-port-service
spec:
  selector:
    app: my-app
  ports:
  - name: http
    port: 80
    targetPort: 8080
  - name: https
    port: 443
    targetPort: 8443
```

When specifying multiple ports, we must provide names for each port.

## Service Without Selectors

We can also create ClusterIP services without selectors, which requires us to manually create the Endpoints object:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-database
spec:
  ports:
  - port: 3306
---
apiVersion: v1
kind: Endpoints
metadata:
  name: external-database  # Must match service name
subsets:
  - addresses:
    - ip: 192.168.1.100
    ports:
    - port: 3306
```

This approach is useful for connecting to external services or services in different namespaces.

## Best Practices

1. **Use Meaningful Names**: Choose descriptive service names that indicate their purpose
2. **Consistent Labeling**: Use consistent labels across pods and services
3. **Document Port Mappings**: Clearly document which ports are exposed by each service
4. **Consider Health Checks**: Use readiness probes to ensure traffic only goes to healthy pods
5. **Use Namespaces**: Organize services into logical namespaces

<div markdown="block">
{: .important }
For the CKA exam, we should be comfortable creating and troubleshooting ClusterIP services as they form the foundation for internal communication in Kubernetes.
</div>