---
title: "Openshift Virtualization - Live Migration between multicluster using ACM"
layout: posts
tags:
  - Red Hat
  - ARO
  - OpenShift
  - Virtualization
---

When moving Virtual Machines between two separate OpenShift clusters managed by Red Hat Advanced Cluster Management (ACM), **true zero-downtime memory live migration across a wide-area network is not technically possible**. Instead, cross-cluster migration is achieved using Orchestrated Failover/Relocation (Disaster Recovery) or Warm Migration.

[Red Hat Reference](https://www.redhat.com/en/resources/virtualization-migration-toolkit-overview#:~:text=For%20users%20looking%20for%20minimal%2C%20planned%20cutovers%2C,storage%20array%20rather%20than%20the%20IP%20network.)

The underlying architecture relies on ACM, KubeVirt, and OpenShift Data Foundation (ODF) / Storage Replication to coordinate cross-cluster VM movement.


| Feature | Intra-Cluster Live Migration | Inter-Cluster ACM Migration |
| ------- | ---------------------------- | --------------------------- |
| Downtime | Zero downtime (RAM pre-copied over network) | Minimal downtime (Seconds to minutes, Warm cutover) |
| RAM State | Retained in-memory during migration | Cleared; VM reboots state on target cluster |
| Storage Requirement | Shared Storage (e.g., ReadWriteMany PVC) | Replicated Storage (ODF / Storage-level sync) |
| Control Component | OpenShift KubeVirt Controller | ACM Hub + RamenDR + MTV |
| Primary Use Case | Host maintenance, node evacuation | Data Center evacuation, Multi-Region DR |

> [!IMPORTANT]
> **Important distinction on RAM state**
>
> If zero-downtime memory transfer is strictly required, both physical host nodes must reside within the same OpenShift cluster utilizing shared storage. Across distinct cluster boundaries, ACM orchestrates rapid Warm Migrations or Automated DR Failovers rather than live RAM streaming.