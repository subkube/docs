Once you know the basics of Subkube, let's deploy something cool. We've outlined some resources we think are great to get started using Kubernetes on Subkube.


## Helm Charts

Helm is a great tool to get to know k8s using your favourite apps, as it allows you to install and upgrade entire applications to namespaces. From there on you can use our [Ingress UI](services_ingresses.md) to expose your apps to the world!

!!! tip
    It is highly recommended you read the [fairly extensive guide](../guides/helm.md) we've written on how to use helm before attempting to run any of the following examples. **Consider yourself warned**

!!! tip
    The following code samples assume a default namespace is set, as outlined [here](interactive_console.md#setting-a-default-namespace-to-use).


#### Wordpress

The worlds most popular CMS, now on Subkube!

```
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install wordpress bitnami/wordpress --set resources.limits.memory=512Mi --set resources.limits.cpu=300m 
```

#### Home Assistant

Control your home from the cloud! Well, almost 😉

```
helm repo add k8s-at-home https://k8s-at-home.com/charts/
helm install home-assistant k8s-at-home/home-assistant
```

## Building an App from scratch

Kubernetes is the ideal platform to develop your new idea on. Use the following guides to get started using the programming language of your preference.

*Coming Soon*
