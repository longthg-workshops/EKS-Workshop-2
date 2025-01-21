---
title: "Exposing your applications on Amazon EKS"
weight: 1
chapter: false
---

# Load balancing and Ingress on Amazon EKS

### Kubernetes

Kubernetes is a flexible, scalable, and **open-sourced** platform for containerized applications and associated services orchestration, making it easier to configure and automate the application development process. Known as a large and rapidly growing ecosystem, Kubernetes provides extensive support for a variety of services and tools.

The name **Kubernetes** comes from the Greek word for helmsman/pilot. Kubernetes was released to the public in 2014 by Google, based on Google's recent decade of real-world workload management experience, combined with ideas and **best practices** from the community.

### Why do you need Kubernetes and what can it do?

Containers are a convenient way for you to contribute and run applications. In a production environment, there needs to be an efficient mechanism for managing containers, ensuring no downtime. Kubernetes helps manage powerful distributed systems, automates scaling, provides declarative development patterns, and much more.

Kubernetes provides:

- **Service discovery and load balancing** 
- **Storage orchestration** 
- **Automatic rollouts and rollbacks** 
- **Automatic packaging** 
- **Self-healing** 
- **Configuration management and security** 

### Amazon Elastic Kubernetes Service (EKS)
Amazon Elastic Kubernetes Service (Amazon EKS) is a managed service that eliminates the need to install, operate, and maintain your own Kubernetes control plane on Amazon Web Services (AWS).

### Features of Amazon EKS
The following are key features of Amazon EKS:

#### Secure networking and authentication
Amazon EKS integrates your Kubernetes workloads with AWS networking and security services. It also integrates with AWS Identity and Access Management (IAM) to provide authentication for your Kubernetes clusters.

#### Easy cluster scaling
Amazon EKS enables you to scale your Kubernetes clusters up and down easily based on the demand of your workloads. Amazon EKS supports horizontal Pod autoscaling based on CPU or custom metrics, and cluster autoscaling based on the demand of the entire workload.

#### Managed Kubernetes experience
You can make changes to your Kubernetes clusters using eksctl, AWS Management Console, AWS Command Line Interface (AWS CLI), the API, kubectl, and Terraform.

#### High availability
Amazon EKS provides high availability for your control plane across multiple Availability Zones.

#### Integration with AWS services
Amazon EKS integrates with other AWS services, providing a comprehensive platform for deploying and managing your containerized applications. You can also more easily troubleshoot your Kubernetes workloads with various observability tools.