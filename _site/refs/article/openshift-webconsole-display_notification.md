# Display a notification bar on OpenShift Web Console

Wether you want to identify your many clusters, or you want to warn your user of an upcoming maintenance, notification bar displayed on top or at the bottom of the Web Console is a good option. Let's see how to generate one of those.

![Notification Example](/refs/resources/openshift-notification-example.png)

### Everything in OpenShift is a Kubernetes custom resource definition

As you may already know, every configurations or features in OpenShift start from a YAML CRD definition. Console notifications are not an exception. The resource type we will use is named `ConsoleNotification` and part of the `console.openshift.io/v1` API.

You may customize many different properties of the `ConsoleNotification`

- Text: What will be displayed as notification text
- Location: Where should the banner appear: BannerTop, BannerBottom, and BannerTopBottom.
- Color: Main text color
- BackgroundColor: Background color of the whole banner
- Link: Add a link so the user can get more details or be redirect to an open ticket maybe ?

```
apiVersion: console.openshift.io/v1
kind: ConsoleNotification
metadata:
  name: cluster-identity
spec:
  text: 'This cluster will be in maintenance until 10:00 PM'
  location: BannerTop
  color: '#fff'
  backgroundColor: '#cd790d'
  link:
    href: 'https://www.example.com'
    text: More Details
```

### Now let's use ConsoleNotification along an ACM Gouvernance Policy

Red Hat Advanced Cluster Management for Kubernetes combine many features which will help create, manage et terminate the life cycle of your Kubernetes cluster fleet. One of those feature is Gouvernance, which will give you the ability to enforce different policies during the creation and all along the life of your differents clusters. [Please take a look at this other article for more details](#none)

#### So, let's use ConsoleNotification in a real world use case.

My team is managing many clusters for different team in the compagny. So I want to identify all of those clusters with their name and a color code to indicate what kind of cluster it is, management, dev, lab, pre-prod etc.

When creating or importing a cluster in ACM, I will give it the color label with the color code I want, to indicate what kind of cluster it is, then I will refer to this label later on in the ACM policy. Additionaly, I will do a lookup in the remote cluster to grab its name, and use it as a value for my notification text.

Here is the result of my policy

```
apiVersion: policy.open-cluster-management.io/v1
kind: Policy
metadata:
  name: openshift-console-identity-policy
  namespace: openshift-acm-policies
  annotations:
    policy.open-cluster-management.io/categories: Console
    policy.open-cluster-management.io/controls: Identity
    policy.open-cluster-management.io/standards: General
spec:
  disabled: false
  remediationAction: enforce
  policy-templates:

    - objectDefinition:
        apiVersion: policy.open-cluster-management.io/v1
        kind: ConfigurationPolicy
        metadata:
          name: configure-console-identity
        spec:
          remediationAction: enforce
          severity: low
          object-templates:
            - complianceType: musthave
              objectDefinition:

                apiVersion: console.openshift.io/v1
                kind: ConsoleNotification
                metadata:
                  name: cluster-identity
                spec:
                  text: 'Cluster {{ upper (lookup "operator.open-cluster-management.io/v1" "Klusterlet" "" "klusterlet").spec.clusterName }}'
                  location: BannerTop
                  color: '#DDD'
                  backgroundColor: '#{{ default "0066cc" (lookup "cluster.open-cluster-management.io/v1alpha1" "ClusterClaim" "" "color").spec.value }}'
```

That way, any of my cluster, where this policy apply, will display their name and type :)


### Additional Resources

[Here is a link to the official documentation regarding ConsoleNotification](https://docs.openshift.com/container-platform/latest/web_console/customizing-the-web-console.html)