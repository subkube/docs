Helm describes itself as the package manager for Kubernetes. It allows us to
quickly install and upgrade an Application including all it's dependencies.

In this guide we'll deploy an instance of `node-red,` an awesome automation tool you can easily host yourself.

There are literally thousands of Helm charts you can easily deploy to any
Subkube project, we've added a list at the bottom of the page for some awesome
examples.

## Prerequisites

- A Namespace in a Subkube Project
- Helm (we'll install Helm as part of this guide)

## Steps

### Get Helm

Installing Helm is easy as:

```
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

Or using Homebrew:

```
brew install helm
```

Or on windows using Chocolatey:

```
choco install kubernetes-helm
```

### Add Repo

Helm uses **Repositories** to store so-called **Charts**. Many Chart developers
maintain their own Repository, which you need to add to your Helm configuration
before you will be able to use their charts.

For node-red, a very comprehensive chart is maintained by [k8s@home](https://k8s-at-home.com/),
which we will be using in our guide. You can easily add their repo using the
following commands:

```
helm repo add k8s-at-home https://k8s-at-home.com/charts/
> "k8s-at-home" has been added to your repositories
```

### First Install

We can install a chart using the `helm install` command, which takes the chart
to install as an argument. Helm deploys applications in so-called **Releases**,
for which you will need to provide a name as an argument. You'll also need to
supply a namespace using the `-n` toggle.

```
helm install node-red k8s-at-home/node-red -n poc-node-red
> NAME: node-red
> LAST DEPLOYED: Mon Nov 30 19:19:56 2020
> NAMESPACE: poc-n8n
> STATUS: deployed
> REVISION: 1
> TEST SUITE: None
> NOTES:
> 1. Get the application URL by running these commands:
>   export POD_NAME=$(kubectl get pods --namespace poc-n8n -l "app.kubernetes.io/name=node-red,app.kubernetes.io/instance=node-red" -o jsonpath="{.items[0].metadata.name}")
>   echo "Visit http://127.0.0.1:8080 to use your application"
>   kubectl port-forward $POD_NAME 8080:1880
```

### Using a Values file



### Upgrading


## Further Reading

- Artifact Hub
  - HASS, Node-red, homebridge
  - Sonarr, Radarr, etc, if that's your kinda thing
