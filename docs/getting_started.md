# Getting Started

## Signing up

To start using SubKube, [sign up for an account](http://app.subku.be/accounts/signup/)


## Create a Project

Once you've signed up and are logged in to SubKube, you need to create a project
to deploy Namespaces and Workloads to.

Open the [_Projects_](http://app.subku.be/projects/) page and click the ___Create Project___ button

!!! tip
    For hardcore command line users, we've created `subctl`, a simple CLI tool
    allowing you to log in to SubKube and interact with your Projects and
    Namespace directly from your shell, just as you would with `kubectl`.
    More about [`subctl` here](cli.md)


## Create a Namespace

After creating a project, we need to create a Kubernetes Namespace to deploy
our Workloads to.

This can easily be done from a _Project_ page, by clicking the
___Create Namespace___ button.


## Setup Kubectl

In order to use `kubectl`, we need to setup our `kubeconfig`.

On your projects' page you will find the Kubeconfig card, which allows you to
download the `kubeconfig` as a file, which will be called `subkubeconfig`, or
you can show the `kubeconfig` for inspection or manual copy-pasting /
distribution.

!!! tip
    If you have `subctl` installed, you can simply run the following to
    automatically setup your existing `kubeconfig` to allow access to subkube:
    ```
    subctl use project <project-uuid>
    ```


## Deploy workloads

Once we have our project, namespace and `kubectl` configured, we can check to
see if everything works by running a test pod interactively:

```
kubectl run -n <YOUR-NAMESPACE> -i --tty busybox --image=busybox -- sh
```
