---
title: LoadBalancer
layout: default
nav_order: 3
parent: Services
grand_parent: Core Concepts
permalink: docs/core-concepts/services/load-balancer
last_modified_date: 2025-04-05
---

# LoadBalancer

LoadBalancer is a service type in Kubernetes that exposes applications to the internet by provisioning an external load balancer in cloud environments. It builds on the foundation of NodePort services and adds automated external load balancing.

## Understanding LoadBalancer

A LoadBalancer service extends the NodePort service type by integrating with cloud providers' load balancing services. When we create a LoadBalancer service, Kubernetes automatically:

1. Creates a ClusterIP service (for internal communication)
2. Opens a NodePort on each node (like a NodePort service)
3. Provisions an external load balancer from the cloud provider
4. Configures the load balancer to route traffic to the NodePorts

This provides a complete path for external traffic to reach our application pods.

<div markdown="block">
{: .information }
LoadBalancer services typically require a cloud provider integration. In bare-metal or on-premises environments, we might need additional tools like MetalLB to enable this functionality.
</div>

## Creating a LoadBalancer Service

Here's an example of a LoadBalancer service definition:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: "nlb"  # Cloud provider-specific annotation
spec:
  type: LoadBalancer
  selector:
    app: web-app
  ports:
  - port: 80          # Port exposed by the load balancer
    targetPort: 8080  # Port the pods are listening on
```

This service will:
1. Provision an external load balancer in our cloud environment
2. Configure the load balancer to forward traffic to our application pods
3. Provide a stable external IP address/hostname for accessing our application

## Cloud Provider Integration

Different cloud providers implement LoadBalancer services in different ways:

### AWS

In AWS, a LoadBalancer service provisions an Elastic Load Balancer (ELB) or Network Load Balancer (NLB). We can specify the type using annotations:

```yaml
metadata:
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: "nlb"  # or "elb"
```

We can also configure other AWS-specific settings:
- Internal vs. Internet-facing
- SSL certificates
- Security groups
- Subnet selection

### Google Cloud (GCP)

In Google Cloud, a LoadBalancer service provisions a Google Cloud Load Balancer. Additional configurations include:

```yaml
metadata:
  annotations:
    cloud.google.com/load-balancer-type: "Internal"  # Internal or external
    service.beta.kubernetes.io/gcp-backend-protocol: "HTTP"  # HTTP, HTTPS, HTTP2, etc.
```

### Azure

In Azure, a LoadBalancer service provisions an Azure Load Balancer. Azure-specific annotations include:

```yaml
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"  # Internal or external
    service.beta.kubernetes.io/azure-load-balancer-resource-group: "my-resource-group"
```

## Working with LoadBalancer Services

We can interact with LoadBalancer services using standard kubectl commands:

```bash
# Create a LoadBalancer service
kubectl apply -f loadbalancer-service.yaml

# Get information about the service
kubectl get service web-app

# Find the external IP/hostname
kubectl get service web-app -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
# or
kubectl get service web-app -o jsonpath='{.status.loadBalancer.ingress[0].hostname}'
```

It might take a few minutes for the external IP to be provisioned by the cloud provider.

## Multiple Ports

Like other service types, LoadBalancer services can expose multiple ports:

```yaml
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 80
    targetPort: 8080
  - name: https
    port: 443
    targetPort: 8443
```

## Traffic Routing

LoadBalancer services distribute traffic to all healthy pods matching the service selector. The exact load balancing algorithm depends on the cloud provider's implementation, but typically includes options like:

- Round-robin
- Least connections
- IP hash (session affinity)

## Advantages of LoadBalancer Services

- **Automated provisioning**: Reduces manual configuration of external load balancers
- **High availability**: Cloud provider load balancers typically offer high availability
- **Health checking**: Integrated health checks ensure traffic only goes to healthy pods
- **Auto-scaling**: Many cloud load balancers can auto-scale based on traffic patterns
- **DNS integration**: Often provides stable DNS hostnames for the service

## Limitations and Considerations

- **Cost**: Each LoadBalancer service typically provisions a new cloud load balancer, which can incur costs
- **Cloud dependency**: Requires a cloud provider with Kubernetes integration
- **Limited customization**: Some advanced load balancing features might not be exposed
- **Provisioning time**: It can take several minutes for a new load balancer to be provisioned

## Best Practices

1. **Use Ingress for HTTP/HTTPS**: For multiple HTTP services, consider using a single LoadBalancer with Ingress resources
2. **Monitor costs**: Keep track of the number of LoadBalancer services to control cloud costs
3. **Configure health checks**: Ensure proper health checking with readiness and liveness probes
4. **Set appropriate timeouts**: Configure session timeouts based on application requirements
5. **Consider internal load balancers**: For services that don't need external access, use internal load balancers

<div markdown="block">
{: .important }
For the CKA exam, we should understand how LoadBalancer services work, but we might not need to implement them in an exam environment that doesn't have cloud provider integration.
</div>