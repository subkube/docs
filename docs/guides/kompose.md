Kompose is a simple utility you can use to convert your existing `docker-compose`
files into Kubernetes Workloads. In this guide, we'll walk you through using the
`kompose` to convert a NextCloud Compose file to a full Kubernetes workload.


## Prerequisites

- A Namespace in a Subkube Project
- Kubectl
- _Optional: Kompose (we'll install kompose as part of this guide)_


## Steps

### Installing Kompose

On Linux or macOS, installing compose is easy as:

``` sh
# Linux
curl -L https://github.com/kubernetes/kompose/releases/download/v1.22.0/kompose-linux-amd64 -o kompose

# macOS
curl -L https://github.com/kubernetes/kompose/releases/download/v1.22.0/kompose-darwin-amd64 -o kompose

chmod +x kompose
sudo mv ./kompose /usr/local/bin/kompose
```

More installation instructions, including for windows, can be found on
[the kompose.io/installation page](https://kompose.io/installation/)


### Inspecting our Compose file

For this guide, we will be using the following `docker-compose.yaml` file to
deploy a NextCloud instance, including a MariaDB and Nginx instance:

=== "docker-compose.yaml"
    ``` {.yaml}
    --8<--
    examples/kompose/docker-compose.yaml
    --8<--
    ```

As you can see it is using the `nextcloud:fpm` image to run the NextCloud php
application, and an `nginxinc/nginx-unprivileged` image to run Nginx as non-root
on port `8080`.


### Running the Conversion

The `kompose` utilty has various capabilities, including the option to generate
a _Helm Chart_ from a Compose file, create Openshift-compliant resources,
or create _ReplicationController_ or _DaemonSet_ workloads.

In this example, we will use the `--out` option to tell `kompose` to put the
generated manifests in the `output` directory, which we have to create manually
before running `kompose`:

``` sh
> mkdir output
> kompose convert --out output/
WARN Unsupported root level volumes key - ignoring
WARN Volume mount on the host "./nginx.conf" isn\'t supported - ignoring path on the host
INFO Kubernetes file "output/app-service.yaml" created
INFO Kubernetes file "output/db-service.yaml" created
INFO Kubernetes file "output/web-service.yaml" created
INFO Kubernetes file "output/app-deployment.yaml" created
INFO Kubernetes file "output/nextcloud-persistentvolumeclaim.yaml" created
INFO Kubernetes file "output/db-deployment.yaml" created
INFO Kubernetes file "output/db-persistentvolumeclaim.yaml" created
INFO Kubernetes file "output/web-deployment.yaml" created
INFO Kubernetes file "output/web-claim0-persistentvolumeclaim.yaml" created
```

As you can see, Kompose has created both a _Deployment_ and a _Service_ for each
entry in our Compose file. You also see a warning about the `nginx.conf` file
used for the Nginx `web` container, which we will address in the upcoming section.


### Updating the generated resources

Before we can apply these resources, we want to make some changes.

#### Add our Configuration

We use a custom Nginx Configuration for the `nginx` container serving as HTTP
proxy to Nextloud's `php-fpm` app container. As you cannot simply mount a file
inside a container on Kubernetes, we need to convert the `nginx.conf` file to
a _ConfigMap_. Luckily, `kubectl` can do this largely for us:

``` sh
kubectl create configmap -o yaml --dry-run \
    web \
    --from-file nginx.conf \
    > output/web-configmap.yaml
```

Now we should replace the generated `web-claim0` _PersistentVolumeClaim_ with our
_ConfigMap_:

=== "web-deployment.yaml"
    ``` {.yaml hl_lines="36-45" }
    --8<--
    examples/kompose/output-after/web-deployment.yaml
    --8<--
    ```

Do not forget to remove the unneeded `web-claim0` _PersistentVolumeClaim_ manifest:

``` sh
rm output/web-claim0-persistentvolumeclaim.yaml
```

#### Use a Secret

In our `docker-compose.yaml`, we specified the MySQL connection parameters directly
as environment variables to the container. In Kubernetes, we use _Secrets_ for this
sort of thing. Let's setup a secret we can use for our MariaDB deployment, using `kubectl`:

``` sh
 kubectl create secret generic -oyaml --dry-run \
    db \
    --from-literal=MYSQL_USER=nextcloud \
    --from-literal=MYSQL_ROOT_PASSWORD=nextcloud \
    --from-literal=MYSQL_PASSWORD=nextcloud \
    > output/db-secret.yaml
```

Inside our _Deployments_, we can use the `envFrom` directive to tell
Kubernetes which _Secret_ to use for the `app` and `db` workloads:

=== "app-deployment.yaml"
    ``` {.yaml hl_lines="35-37" }
    --8<--
    examples/kompose/output-after/app-deployment.yaml
    --8<--
    ```
=== "db-deployment.yaml"
    ``` {.yaml hl_lines="34-36" }
    --8<--
    examples/kompose/output-after/db-deployment.yaml
    --8<--
    ```

#### Setting Resources & Security Contexts

As with any workload running on Subkube, we should set appropriate resources for
the deployments, as well as securityContexts.


=== "app-deployment.yaml"
    ``` {.yaml hl_lines="38-44" }
    --8<--
    examples/kompose/output-after/app-deployment.yaml
    --8<--
    ```
=== "db-deployment.yaml"
    ``` {.yaml hl_lines="39-45" }
    --8<--
    examples/kompose/output-after/db-deployment.yaml
    --8<--
    ```
=== "web-deployment.yaml"
    ``` {.yaml hl_lines="35-41" }
    --8<--
    examples/kompose/output-after/web-deployment.yaml
    --8<--
    ```

### Apply and Verify

After we have made these changes, we can deploy the resources:

``` sh
> kubectl apply -n demo -f output/
deployment.apps/app created
service/app created
deployment.apps/db created
persistentvolumeclaim/db created
secret/db created
service/db created
persistentvolumeclaim/nextcloud created
configmap/web created
deployment.apps/web created
service/web created
```

We can monitor our _Deployments'_ to see them progressing:
``` sh
kubectl get deployment -n demo -w
```

Once everything is ready, we can setup a `port-forward` to access NextCloud and
trigger the setup process, by creating an Admin user.

``` sh
kubectl port-forward service/web -n demo 8080:8080
```





## More Resources & Further Reading

- [Kompose Docs: User Guide](https://kompose.io/user-guide/)
- [Kompose Docs: Architecture](https://kompose.io/architectue/)
