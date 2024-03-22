---
slug: how-to-migrate-from-aws-to-linode
title: "How to Migrate From Aws to Linode"
description: "This guide will provide best practices when migrating AWS instances to Linode."
keywords: ['migrate','Amazon Web Services','AWS','Linode']
license: '[CC BY-ND 4.0](https://creativecommons.org/licenses/by-nd/4.0)'
authors: ["Linode"]
published: 2024-02-13
modified_by:
  name: Linode
external_resources:
- '[Link Title 1](http://www.example.com)'
- '[Link Title 2](http://www.example.net)'
---

This guide describes the recommended strategy for migrating your services from an Amazon Web Services (AWS) instance to the Linode platform. The specific steps you'll need to take will vary depending on the software you use, however, this outline will generally apply regardless of the nature of your services. The [Migrate to Linode](https://www.linode.com/docs/guides/platform/migrate-to-linode/) section offers other guides which describe migrating particular services in more detail.

## Assess Your Current Workloads

Before migrating your services to Linode, it will be helpful to run discovery on your current cloud workloads to understand what sort of resources you will need to deploy on the Linode platform. The products offered by AWS are wide ranging and while Linode has many comparables, they are not one-to-one. The following table should assist in your assessment process:

### AWS / Linode Compute Product Comparison

The following chart is not meant to be used for 1:1 comparison. Instead you can use it to determine the kinds of services offered by Linode compared to AWS.

| AWS  Product | Description | Linode Comparable Product |
| -- | -- | -- |
| **General Purpose EC2 Instance** | Scalable virtual private server deployed around the world | [Shared CPU](https://www.linode.com/products/shared/) |
| **Compute Optimized EC2 Instance** | Best suited for compute-intensive applications | [Dedicated CPU](https://www.linode.com/products/dedicated-cpu/) / [Premium CPU](https://www.linode.com/products/premium-cpu/) |
| **Memory Optimized EC2 Instance** | Virtual machines for memory-intensive applications | [High Memory](https://www.linode.com/products/high-memory/) |
| **Accelerated Computing EC2 Instance** | GPU-Based instances for complex compute jobs and machine learning | [GPU](https://www.linode.com/products/high-memory/) |
| **Elastic Container Service (ECS)** | Fully-managed container orchestration service | [Linode Kubernetes Engine (LKE)](https://www.linode.com/products/kubernetes/) |
| **Elastic Block Store (EBS)** | Scalable block-storage service | [Block Storage](https://www.linode.com/products/block-storage/) |
| **Amazon S3** | Object storage for unstructured data | [Object Storage](https://www.linode.com/products/object-storage/) |
| **AWS Backup** | Fully-managed backup service | [Linode Backups](https://www.linode.com/products/backups/)
| **AWS Firewall Manager** | Network security with firewall protection | [Cloud Firewall](https://www.linode.com/products/cloud-firewall/) |
| **Route 53** | Highly available and scalable cloud domain name service | [DNS Manager](https://www.linode.com/products/dns-manager/) |
| **Elastic Load Balancer** | 


