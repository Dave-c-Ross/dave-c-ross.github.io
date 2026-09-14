---
title: "Ingress Sharding with ARO"
layout: posts
tags:
  - Red Hat
  - ARO
  - OpenShift
classes: wide

---

Based on a request from a customer, here is how you might want to leverage multiple ingress controller inside your ARO cluster, so you can shard and scall routes handling.

## Prerequisits
Create a new subnet in your VNET

## Create the ingress controller

```yaml
apiVersion: operator.openshift.io/v1
kind: IngressController
metadata:
  name: custom-ingress-1
  namespace: openshift-ingress-operator
spec:
  domain: custom.apps.dg6036green.eastus.aroapp.io
  endpointPublishingStrategy:
    type: LoadBalancerService
    loadBalancer:
      scope: Internal
```

Then patch the IngressController LB service with the right subnet

```sh
oc annotate service router-custom-ingress-1 -n openshift-ingress \
  "service.beta.kubernetes.io/azure-load-balancer-internal-subnet=nlb-subnet" --overwrite
```

Optionnaly, you can provid a static IP to be used:

```sh
oc annotate service router-custom-ingress-1 -n openshift-ingress \
  "service.beta.kubernetes.io/azure-load-balancer-ipv4=192.168.60.22" --overwrite
```

Your LB service (used by the new ingresscontroller) will now get an IP from that subnet

```sh
$ oc get svc
NAME                               TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)                      AGE
router-custom-ingress-1            LoadBalancer   172.30.15.164    192.168.60.4    80:31211/TCP,443:32549/TCP   38m
router-default                     LoadBalancer   172.30.210.86    172.171.36.54   80:30548/TCP,443:32040/TCP   3d19h
router-internal-custom-ingress-1   ClusterIP      172.30.194.216   <none>          80/TCP,443/TCP,1936/TCP      38m
router-internal-default            ClusterIP      172.30.211.239   <none>          80/TCP,443/TCP,1936/TCP      3d19h
```

## Use that new IC with specific route, or selected namespace

If you do not configure selectors, both IngressControllers will admit and serve every route in the cluster. OpenShift does not automatically divide or shard routes between controllers unless explicit labels and selectors are defined.

### Use routeSelector

1. Configure routeSelector on your IngressController

```sh
oc patch ingresscontroller custom-ingress-1 -n openshift-ingress-operator --type=merge -p '{"spec":{"routeSelector":{"matchLabels":{"type":"custom-ingress"}}}}'
```

2. Add the Matching Label to Your Route

```sh
oc label route my-app-route type=custom-ingress -n my-app-namespace --overwrite
```

Or inside a YAML manifest

```yaml
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: my-app-route
  namespace: my-app-namespace
  labels:
    type: custom-ingress
spec:
  host: my-app.custom.apps.mycluster.eastus.aroapp.io
  to:
    kind: Service
    name: my-app-service
```

### Use namespaceSelector

1. Label Your Target Namespace(s)

```sh
oc label namespace my-internal-app ingress-type=internal
```

2. Patch the Custom IngressController

```sh
oc patch ingresscontroller custom-ingress-1 -n openshift-ingress-operator \
  --type=merge -p '{"spec":{"namespaceSelector":{"matchLabels":{"ingress-type":"internal"}}}}'
```

### (Optional) Prevent default IngressController from admitting the route

By default, router-default admits all cluster routes. If you want to isolate namespaces entirely so router-default ignores them, set a namespaceSelector on your custom router and label the application namespace accordingly:

```sh
oc label namespace my-app-namespace ingress=custom
oc patch ingresscontroller custom-ingress-1 -n openshift-ingress-operator --type=merge -p '{"spec":{"namespaceSelector":{"matchLabels":{"ingress":"custom"}}}}'
```

Or the other way around, only allow default IC to specific NS

1. Label public/external namespaces
```sh
oc label namespace my-public-app ingress-type=public
```

2. Patch the default ingresscontroller
```sh
oc patch ingresscontroller default -n openshift-ingress-operator \
  --type=merge -p '{"spec":{"namespaceSelector":{"matchLabels":{"ingress-type":"public"}}}}'
```

