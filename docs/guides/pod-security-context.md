As Subkube is a shared platform, containers are only allowed to run in so-called
'restricted' PodSecurityPolicies. This guide walks through the steps of creating
a deployment, seeing it fail, updating it to use a SecurityContext, and seeing it
run successfully.


## Prerequisites

- A Namespace in a Subkube Project
- Kubectl


## Steps

### Setting up a basic Deployment

For this example, we will use the `echoserver` image, which sets up a simple HTTP
server which replies everything sent to it.

=== "deployment-before.yaml"
    ``` yaml
    --8<--
    examples/pod-security-context/deployment-before.yaml
    --8<--
    ```

Let's apply the supplied, unmodified deployment, to a namespace, called `demo`
in our example:

``` sh
kubectl apply -n demo -f deployment-before.yaml
```


### Initial testing

Because we don't want to set up any Services or an Ingress, we'll use a
PortForward to connect to the Deployment:

``` sh
> kubectl port-forward -n demo deployment/hello-subkube 8080:8080
error: unable to forward port because pod is not running. Current status=Pending
```

😕


### Figuring out why our Pod fails

Let's check the logs and see what is going wrong

``` sh
> kubectl logs deployment/hello-subkube -n demo
Error from server (BadRequest): container "echoserver" in pod "hello-subkube-758bc95559-bgg29" is waiting to start: CreateContainerConfigError
```

We are shown a CreateContainerConfigError while trying to fetch the Pod's logs.  Let's look in to that:

``` sh
> kubectl describe pods -n demo
...
Normal   Pulled     47s (x8 over 2m18s)  kubelet, minikube-local-local  Successfully pulled image "ealen/echo-server:latest"
Warning  Failed     47s (x8 over 2m18s)  kubelet, minikube-local-local  Error: container has runAsNonRoot and image will run as root
Normal   Pulling    33s (x9 over 2m27s)  kubelet, minikube-local-local  Pulling image "ealen/echo-server:latest"
...
```

Because we haven't configured our Deployment to run with an appopriate SecurityContext,
the Kubernets control plane isn't allowing Pods in our Deployment to run.


### Setting a SecurityContext

We can easily fix this by setting a `SecurityContext`. For many containers this is
as easy as setting the `runAsUser` property to the value `65534`, which translates
to the `nobody` user account.

By default, the `echo-server` image runs on port `80`, but because our container
won't be running as `root`, the container will be unable to allocate the port,
and the Pod will fail. Instead, we want to run the `echo-server` on a different port,
say 8080.

Let's update our Deployment manifest accordingly:


=== "deployment-after.yaml"
    ``` {.yaml hl_lines="23-27" }
    --8<--
    examples/pod-security-context/deployment-after.yaml
    --8<--
    ```

Now we should apply the modified deployment:

``` sh
kubectl apply -n demo -f deployment-after.yaml
```


### Testing our revised Deployment

Let's again try to start our PortForward:

``` sh
kubectl port-forward -n demo deployment/hello-subkube 8080:8080
```

That should've worked. Now we can use `curl` to test our Deployment:

``` sh
> curl 'localhost:8080/?echo_body=All_Good'
All_Good
```


## More Resources & Further Reading

- [K8s Concepts: Pod Security Policies](https://kubernetes.io/docs/concepts/policy/pod-security-policy/)
- [K8s Concepts: Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/)
- [Better Programming on Medium: Secure Your Kubernetes Cluster With Pod Security Policies](https://medium.com/better-programming/secure-your-kubernetes-cluster-with-pod-security-policies-bd511ec6d034)
