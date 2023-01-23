# Openshift usful snippets

## Links
I am using this repo to gather together any information I find useful for my day to day, that said, many of those snippets are actually taken words for words from Red Hat website. Here is the links to get more details:

###### Configuring the web console
[Configuring the web console](https://docs.openshift.com/container-platform/4.11/web_console/configuring-web-console.html)

###### Customizing the web console
[Customizing the web console](https://docs.openshift.com/container-platform/4.11/web_console/customizing-the-web-console.html)


## Create a Console Notification

![Notification Example](/refs/resources/openshift-notification-exemple.png)

To do so, simply create a `ConsoleNotification` resource definition
```
apiVersion: console.openshift.io/v1
kind: ConsoleNotification
metadata:
  name: example
spec:
  text: This is an example notification message with an optional link.
  location: BannerTop 
  link:
    href: 'https://www.example.com'
    text: Optional link text
  color: '#fff'
  backgroundColor: '#0088ce'
```

## Setup the logout url for the web console (needed for SSO)

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
  consoleURL: "" 
```