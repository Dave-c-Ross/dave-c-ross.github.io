# OpenShift Articles and Snippets

## Table of contents

[Display notification banner on OpenShift Web Console](/refs/article/openshift-webconsole-display_notification.md)

[Import non-OpenShift cluster in ACM](/refs/article/openshift-acm-imoprt_non_openshift_cluster.md)

## Links
I am using this repo to gather together any information I find useful for my day to day, that said, many of those snippets are actually taken words for words from Red Hat website. Here is the links to get more details:

- [Configuring the web console](https://docs.openshift.com/container-platform/4.11/web_console/configuring-web-console.html)

- [Customizing the web console](https://docs.openshift.com/container-platform/4.11/web_console/customizing-the-web-console.html)


## Setup the logout url for the web console (needed for SLO)

```
apiVersion: config.openshift.io/v1
kind: Console
metadata:
  name: cluster
spec:
  authentication:
    # Specify the URL of the page to load when a user logs out of the web console. If you do not specify a value, the user returns to the login page for the web console. Specifying a logoutRedirect URL allows your users to perform single logout (SLO) through the identity provider to destroy their single sign-on session.
    logoutRedirect: ""
status:
  # The web console URL. To update this to a custom value, see Customizing the web console URL.
  consoleURL: "" 
```

When removing oAuth in openshift, we need to remove all indentity bound to it: `oc get identity`