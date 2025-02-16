---
title: Home
layout: home
nav_order: 1
last_modified_date: 2025-02-16
---

This project collects study notes and reference materials for the Certified Kubernetes Administrator (CKA) exam. It uses the [Just the Docs] theme and can be published on [GitHub Pages] for easy access. Local builds with [Jekyll] allow quick previews before deployment. Explore the content to support your exam preparation and modify as needed to highlight key topics and best practices.

## Domains & Competencies

### 1. **Storage (10%)**
- **Persistent Volumes & Storage Classes**
  - Understand different Kubernetes storage classes and persistent volumes (PVs).
  - Know how to use volume modes, access modes, and reclaim policies.
  - Understand persistent volume claims (PVCs) and their role in persistent storage.
- **Persistent Storage for Applications**
  - Learn how to configure applications with persistent storage using PVCs.

### 2. **Troubleshooting (30%)**
- **Cluster and Node Logging**
  - Learn how to evaluate cluster and node logs.
  - Understand the role of container logs in troubleshooting.
- **Monitoring Applications**
  - Know how to monitor application health and troubleshoot failures.
- **Logs Management**
  - Manage container stdout and stderr logs.
- **Troubleshooting Application Failures**
  - Identify and resolve application failures.
- **Cluster Component Failure Troubleshooting**
  - Learn how to troubleshoot failures of critical cluster components (e.g., kube-apiserver, kube-scheduler).
- **Networking Issues**
  - Troubleshoot networking problems within the Kubernetes cluster.

### 3. **Workloads & Scheduling (15%)**
- **Deployments & Updates**
  - Understand how to create and manage Kubernetes deployments.
  - Know how to perform rolling updates and rollbacks.
- **ConfigMaps & Secrets**
  - Learn how to use ConfigMaps and Secrets for application configuration.
- **Scaling Applications**
  - Understand how to scale applications horizontally.
- **Self-Healing Applications**
  - Explore Kubernetes primitives for creating robust, self-healing applications.
- **Resource Limits & Pod Scheduling**
  - Understand how resource limits affect pod scheduling.
- **Manifest Management**
  - Be aware of manifest management and common templating tools used for Kubernetes resources.

### 4. **Cluster Architecture, Installation & Configuration (25%)**
- **Role-Based Access Control (RBAC)**
  - Manage RBAC and understand its role in controlling access to resources.
- **Kubeadm & Cluster Installation**
  - Use Kubeadm to install a basic Kubernetes cluster.
  - Understand how to set up a highly-available Kubernetes cluster.
- **Cluster Infrastructure**
  - Learn how to provision the underlying infrastructure needed to deploy a Kubernetes cluster.
- **Cluster Upgrades**
  - Perform a version upgrade on a Kubernetes cluster using Kubeadm.
- **Etcd Backup and Restore**
  - Implement etcd backup and restore to ensure data safety.

### 5. **Services & Networking (20%)**
- **Host Networking Configuration**
  - Understand how to configure and manage host networking on Kubernetes nodes.
- **Pod Connectivity**
  - Learn how to manage and troubleshoot connectivity between Pods in a cluster.
- **Service Types & Endpoints**
  - Understand the different service types (ClusterIP, NodePort, LoadBalancer) and how to configure endpoints.
- **Ingress Controllers & Resources**
  - Know how to use Ingress controllers and Ingress resources to expose applications.
- **CoreDNS Configuration**
  - Understand how to configure and use CoreDNS for service discovery and DNS resolution.
- **Container Network Interface (CNI) Plugins**
  - Choose and configure an appropriate CNI plugin for your Kubernetes cluster networking.

---

This content is organized to cover all the necessary domains for your CKA exam preparation. Feel free to explore and modify the materials as needed to tailor the study guide to your needs.