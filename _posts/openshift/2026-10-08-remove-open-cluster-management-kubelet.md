---
title: "Remove OpenClusterManager Addod Kubelet"
layout: posts
tags:
  - Red Hat
  - OpenShift
  - ACM
---

```
oc delete Secret bootstrap-hub-kubeconfig -n open-cluster-management-agent
oc delete Secret open-cluster-management-image-pull-credentials -n open-cluster-management-agent
oc delete ServiceAccount klusterlet -n open-cluster-management-agent
oc delete Deployment klusterlet -n open-cluster-management-agent

oc delete namespace open-cluster-management-agent

oc delete ClusterRoleBinding klusterlet
oc delete ClusterRole klusterlet
oc delete ClusterRole klusterlet-bootstrap-kubeconfig
oc delete ClusterRole open-cluster-management:klusterlet-admin-aggregate-clusterrole

oc delete Klusterlet klusterlet
oc delete CustomResourceDefinition klusterlets.operator.open-cluster-management.io
``