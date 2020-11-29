# Workloads

In Kubernetes, you run your application as a _workload_. There are various ways
to orchestrate workloads, for instance using the _deployment_ resource, the
_statefulSet_ or the _daemonSet_ resource.

Many apps consist of various different workloads, which need to work together in
order to serve the application as a whole. Kubernetes has an applicable workload
type for any of these.

For more information on workloads, please checkout the dedicated [Kubernetes docs](https://kubernetes.io/docs/concepts/workloads/)

## Pod Security Context

By default, many containers used to run as root user. This isn't an option on a
shared cluster, as this could potentially allow users to "break out" of a running
container, and gain access to the entire node.

In Kubernetes this is enforced using PodSecurityPolicies. The default
PodSecurityPolicy enforced on Subkube is called 'restricted' and is very comparable
to those seen in large scale enterprise Kubernetes deployments.

## Not Available in Subkube

| Resource            | Reason                                                   |
| ------------------- | ---
| Daemonset           | DaemonSets can not be used on SubKube as it is a shared platform, where no application should need a DaemonSet.  |
| NodePort Service    | Too unpredictable for production usage , incompatible with Subkube cluster firewall                            |
| Container Hostport  | Too unpredictable for production usage , incompatible with Subkube cluster firewall                            |
| Custom Resource Definition    | Incompatible with shared cluster usage, as CRDs are cluster-wide resources.                                  |
