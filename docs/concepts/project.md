# Project

In SubKube, your Kubernetes Namespaces are organised inside a Project. Project can be personal,
or belong to an Organization.


## Namespaces

Namespaces are used in Kubernetes to group Workloads together. Namespaces can
operate in various levels of isolation. In SubKube, this is configurable per
project.

More about namespaces can be found in the [Kubernetes docs](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)

### Resource Quota
Resource Quota are set on namespaces by Rancher based on the set Project limits.

More about Resource Quotas in the Rancher (on which Subkube is built) [docs](https://rancher.com/docs/rancher/v2.x/en/project-admin/resource-quotas/quotas-for-projects/)


## Persistent Volumes

Are available and can be created using normal PVC's. Storage is implemented
using Longhorn CSI.

More about Persistent Volumes and associated Claims can be found in the [Kubernetes docs](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)

## Ingress

In Kubernetes, Ingress objects are used to allow your application to be accessed
by the outside world, by using a reverse proxy. You can read more about the Ingress
concept in the [Kubernetes docs](https://kubernetes.io/docs/concepts/services-networking/ingress/)

The Nginx Ingress Controller is available for all users of Subkube, including
certmanager, to allow SSL certificates to be set up automatically using
Let's Encrypt.

The Nginx Ingress Controller allows for very flexible configuration, for a full
list of all options please see the [nginx-ingress docs](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/)
