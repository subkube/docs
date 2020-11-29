In this guide, we will be creating an _Ingress_ resource to expose a workload
to the world, after which we will setup Certmanager to automatically generate
and configure a TLS certificate for our _Ingress_.

!!! tip
    Due to issues with Let's Encrypt rate limiting, we've disabled Cert Manager for now.


## Prerequisites

- Kubectl
- A custom domain name for which you can configure DNS


## Steps


### Deploy a workload

Before we can dive in to Ingresses, we need a workload to expose through an Ingress.
We'll be using a very basic `echo-server` _Deployment_ and _Service_:

=== "deployment.yaml"
    ``` {.yaml}
    --8<--
    examples/ingress-certmanager/deployment.yaml
    --8<--
    ```
=== "service.yaml"
    ``` {.yaml}
    --8<--
    examples/ingress-certmanager/service.yaml
    --8<--
    ```

Let's deploy these resources:

``` sh
> kubectl apply -n demo -f deployment.yaml -f service.yaml
deployment.apps/echoserver created
service/echoserver created
```


### Setup DNS

Point a domain name towards the cluster ingress by creating a cname to ...


### Defining an Ingress

An Ingress is basically a mapping between a _Domain Name_ and a _Service_,
provided on Subkube using an Nginx reverse proxy, but other _Ingress_
implementations are also available.

=== "ingress.yaml"
    ``` {.yaml}
    --8<--
    examples/ingress-certmanager/ingress-before.yaml
    --8<--
    ```

Using `kubectl` we can apply this _Ingress_, http-only for now:

``` sh
> kubectl apply -n demo -f ingress.yaml
ingress.extensions/echoserver created
```

We can then use `curl` to check the result:

``` sh
> curl
```



### Create an Issuer

CertManager needs an _Issuer_ to use while requesting certificates. It is
recommended to use a separate `staging` and `production` issuer, while using
Let's Encrypt, to ensure you don't hit any rate limits during experimentation.

=== "issuer.yaml"
    ``` {.yaml}
    --8<--
    examples/ingress-certmanager/issuer.yaml
    --8<--
    ```

``` sh
> kubectl apply -n demo -f issuer.yaml
issuer.cert-manager.io/letsencrypt-staging created
```


### Update Ingress to use Issuer

Now we can update our previously created _Ingress_ resource to make use of
the new _Issuer_ to automatically request, verify, and setup TLS certificates.

=== "ingress.yaml"
    ``` {.yaml}
    --8<--
    examples/ingress-certmanager/ingress-after.yaml
    --8<--
    ```

``` sh
> kubectl apply -n demo -f ingress.yaml
ingress.extensions/echoserver configured
```

After we've applied the updated _Ingress_, we need to give CertManager some time
to setup the _CertificateRequest_ and update the appropriate _Certificate_ resource:

``` sh
> kubectl get certificate -n demo -w
```


### Verify Certificate










## Further Reading
