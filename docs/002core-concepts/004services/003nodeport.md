---
title: NodePort
layout: default
nav_order: 2
parent: Services
grand_parent: Core Concepts
permalink: docs/core-concepts/services/node-port
last_modified_date: 2025-04-04
---

# NodePort

NodePort is a service type in Kubernetes that exposes the service on each node's IP at a static port. This allows us to access our applications from outside the Kubernetes cluster.

## Understanding NodePort

A NodePort service builds on top of a ClusterIP service and adds an additional capability: it opens a specific port on all the nodes in our cluster. When external traffic comes to a node on the designated port, it's routed to the service and then distributed to the underlying pods.

<div markdown="block">
{: .information }
The valid port range for NodePort services is 30000-32767. This restricted range helps prevent conflicts with system services that might be running on the nodes.
</div>

## Key Components of NodePort

A NodePort service exposes three ports:
- **nodePort**: The port exposed on each node's IP (30000-32767)
- **port**: The port exposed by the service inside the cluster
- **targetPort**: The port the application is running on inside the pods

If we don't specify a `targetPort`, it assumes the same value as `port`. If we don't specify a `nodePort`, Kubernetes will automatically assign an available port from the valid range.

## Creating a NodePort Service

Here's an example of a NodePort service definition:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: NodePort
  selector:
    app: webapp
  ports:
  - port: 80          # Service port (cluster internal)
    targetPort: 8080  # Pod port (application)
    nodePort: 30080   # Node port (external access)
```

This service will:
1. Create a ClusterIP service accessible at `webapp-service` inside the cluster
2. Open port 30080 on all nodes in the cluster
3. Route external traffic coming to `<node-ip>:30080` to port 80 on the service
4. Forward that traffic to port 8080 on pods with the label `app: webapp`

## Load Balancing Behavior

NodePort services have a built-in load balancer that distributes traffic among all pods matching the selector. The algorithm for selecting a pod is random by default.

However, it's important to note that traffic coming to a specific node is not automatically routed to pods on other nodes. The traffic first reaches the node, then it's routed to a pod (which could be on any node). This means that if a node goes down, external clients connecting to that node's IP will lose access.

## Accessing NodePort Services

We can access a NodePort service from outside the cluster using:

```
<any-node-ip>:<nodePort>
```

For example:
```
http://192.168.1.101:30080
```

This flexibility allows us to:
- Set up our own load balancer in front of multiple nodes
- Use DNS round-robin to distribute traffic to different nodes
- Configure external firewalls to allow traffic only to the specific nodePort

## When to Use NodePort

We should consider using NodePort when:
- We need to expose services externally without a cloud provider load balancer
- We're running on-premises or in environments without built-in load balancers
- We're developing or testing and need quick external access
- We want to implement our own load balancing mechanism

## Limitations

NodePort services have some limitations to be aware of:
- Limited port range (30000-32767)
- We need to know the IP of at least one node
- If a node IP changes or a node goes down, we need to update clients
- No automatic external load balancing across nodes
- No built-in security features like TLS termination

## Best Practices

1. **Use stable node IPs**: If possible, assign static IPs to nodes or use DNS names
2. **Consider security**: NodePorts expose services to the outside world, so secure them appropriately
3. **Use external load balancers**: For production workloads, place an external load balancer in front of NodePort services
4. **Reserve specific ports**: For important services, reserve specific nodePort values to ensure consistency

<div markdown="block">
{: .important }
For production environments, LoadBalancer services are generally preferred over NodePort when available. However, understanding NodePort is essential for the CKA exam and for environments without cloud provider integration.
</div>