# Workloads

In Kubernetes, you run your application as a _workload_. There are various ways
to orchestrate workloads, for instance using the _deployment_ resource, the
_statefulSet_ or the _daemonSet_ resource.

Many apps consist of various different workloads, which need to work together in
order to serve the application as a whole. Kubernetes has an applicable workload
type for any of these.

For more information on workloads, please checkout the dedicated [Kubernetes docs](https://kubernetes.io/docs/concepts/workloads/)

## DaemonSets

DaemonSets can not be used on SubKube as it is a shared platform, where no
application should need a DaemonSet.
