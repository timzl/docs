---
slug: how-to-migrate-from-aws-to-linode
description: 'This guide will provide best practices when migrating AWS instances to Linode.'
keywords: ['migrate','Amazon Web Services','AWS','Linode']
license: '[CC BY-ND 4.0](https://creativecommons.org/licenses/by-nd/4.0)'
published: 2020-01-21
modified_by:
  name: Linode
image: [TBD]
title: 'Migrating from AWS to Linode'
title_meta: 'Migrating from AWS to Linode'
tags: ["linode platform"]
aliases: ['/platform/migrate-to-linode/how-to-migrate-from-aws-to-linode/']
authors: ["Linode"]
---

This guide describes the recommended strategy for migrating your services from an Amazon Web Services (AWS) instance to the Linode platform. The specific steps you'll need to take will vary depending on the software you use, however, this outline will generally apply regardless of the nature of your services. The [Migrate to Linode](https://www.linode.com/docs/guides/platform/migrate-to-linode/) section offers other guides which describe migrating particular services in more detail.

## Assess Your Current Workloads

Before migrating your services to Linode, it will be helpful to run discovery on your current cloud workloads to understand what sort of resources you will need to deploy on the Linode platform. The products offered by AWS are wide ranging and while Linode has many comparables, they are not one-to-one. The following table should assist in your assessment process:
