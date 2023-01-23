# Openshift usful snippets

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