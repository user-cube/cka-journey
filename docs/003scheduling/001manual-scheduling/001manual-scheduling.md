---
title: Manual Scheduling
layout: default
nav_order: 1
parent: Scheduling
permalink: docs/scheduling/manual
last_modified_date: 2025-04-05
---

# Manual Scheduling

In Kubernetes, pod scheduling is typically handled automatically by the scheduler component. However, there are situations where we might need to manually specify which node a pod should run on. This is achieved through a field called `nodeName`.

## Understanding the nodeName Field

All pods have a field called `nodeName` which is empty by default. When this field is empty, the Kubernetes scheduler works as follows:

1. The scheduler identifies pods without a specified `nodeName`
2. It runs its scheduling algorithm to determine the best node for each pod
3. It assigns the pod to the selected node by creating a binding object

<div markdown="block" class="note">
The `nodeName` field is part of the Pod specification and can be found in the `spec` section.
</div>

## What Happens Without a Scheduler?

If a Kubernetes cluster doesn't have a functioning scheduler:

- Pods will remain indefinitely in the `Pending` state
- They will not be automatically assigned to any node
- We must manually intervene to schedule these pods

## Manually Scheduling a Pod

We have two methods to manually schedule pods:

### Method 1: Directly Specifying nodeName

We can specify the `nodeName` field when creating a pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx
    image: nginx
  nodeName: worker-node-1  # Directly specify the node
```

When we use this method:

- The pod bypasses the scheduler entirely
- The kubelet on the specified node will directly create the pod
- No scheduling evaluation occurs (resource checks, taints, etc.)

<div markdown="block">
{: .important }
The `nodeName` field can only be set during pod creation. We cannot modify it for an existing pod.
</div>

### Method 2: Creating a Binding Object

If we need to manually schedule an existing pod that's stuck in `Pending` state, we need to create a binding object and make an API call:

1. First, we create a binding object in JSON format:

```json
{
  "apiVersion": "v1",
  "kind": "Binding",
  "metadata": {
    "name": "nginx-pod"
  },
  "target": {
    "apiVersion": "v1",
    "kind": "Node",
    "name": "worker-node-1"
  }
}
```

2. Then, we make a POST request to the binding API endpoint:

```bash
curl --header "Content-Type: application/json" \
  --request POST \
  --data @binding.json \
  http://$SERVER/api/v1/namespaces/default/pods/nginx-pod/binding/
```

This mimics what the scheduler does when it assigns a pod to a node.

## When to Use Manual Scheduling

We might consider manual scheduling in the following scenarios:

1. **Scheduler Failure**: When the default scheduler is not functioning
2. **Testing and Debugging**: To test specific pod-node interactions
3. **Special Hardware Requirements**: To ensure pods run on nodes with specific hardware
4. **Troubleshooting**: To replicate and diagnose node-specific issues
5. **Migration Scenarios**: When migrating workloads with specific node requirements

## Limitations of Manual Scheduling

Manual scheduling has several important limitations to consider:

1. **No Automatic Rescheduling**: If the node fails, the pod won't be automatically rescheduled
2. **No Constraint Checking**: The system won't verify if the node has sufficient resources
3. **No Consideration for Taints**: Taints on the node are ignored
4. **No Optimization**: Load balancing and optimal resource utilization aren't considered
5. **Maintenance Burden**: Manual tracking and maintenance of pod-node assignments becomes necessary

## Best Practices

If we need to influence pod placement, consider these alternatives to manual scheduling:

1. **Node Selectors**: Use `nodeSelector` to select nodes based on labels
2. **Node Affinity**: Use node affinity rules for more complex node selection
3. **Taints and Tolerations**: Configure which nodes repel certain pods
4. **Pod Affinity/Anti-Affinity**: Control pod placement relative to other pods
5. **Custom Schedulers**: Implement specialized scheduling logic if needed