# Initial Setup

!!! tip
    For hardcore command line users, we've created `subctl`, a simple CLI tool
    allowing you to log in to SubKube and interact with your Projects and
    Namespace directly from your shell, just as you would with `kubectl`.
    More about how to install [`subctl` here](cli.md)


## Signing up

To start using SubKube, [sign up for an account](http://app.subku.be/accounts/signup/)

Before you can start creating projects, you'll need to setup a billing method

!!! tip
    If you wish to use `subctl`, don't forget to log in
    ``` sh
    subctl login
    ```


## Create a Project

Once you've signed up and are logged in to SubKube, you need to create a project
to deploy Namespaces and Workloads to.

=== "Subkube UI"

    Open the [_Projects_](http://app.subku.be/projects/) page and click the ___Create Project___ button.

    ![Subkube UI Project List](/img/screenshots/app.poc.subku.be_projects_.png){: .shadow}

=== "subctl"

    ``` sh
    subctl create project <PROJECT-NAME>
    ```


## Create a Namespace

After creating a project, we need to create a Kubernetes Namespace to deploy
our Workloads to.

=== "Subkube UI"

    This can easily be done from a _Project_ page, by clicking the ___Create Namespace___ button.

    ![Subkube UI Project Overview](/img/screenshots/app.poc.subku.be_projects_project.png){: .shadow}

=== "subctl"

    ``` sh
    subctl create namespace <NAMESPACE-NAME> -p <PROJECT-UUID>
    ```


## Setup Kubectl

!!! tip
    If you don't have `kubectl` locally installed, checkout our [Interactive Console](interactive_console.md), which sets up your `kubeconfig` automatically for you, and offers `helm` and others besides `kubectl`.

In order to use `kubectl`, we need to setup our `kubeconfig`.

=== "Subkube UI"

    On your projects' page you will find the Kubeconfig card, which allows you to
    download the `kubeconfig` as a file, which will be called `subkubeconfig`, or
    you can show the `kubeconfig` for inspection or manual copy-pasting /
    distribution.

    ![Subkube UI Project Overview](/img/screenshots/app.poc.subku.be_projects_project.png){: .shadow}

=== "subctl"

    ``` sh
    subctl use project <project-uuid>
    ```


## Deploy workloads

Once we have our project, namespace and `kubectl` configured, we can check to
see if everything works by running a test pod interactively.

As you can notice

- we've set a securityContext using overrides, please see
  [Concept: Workloads](/concepts/workloads/) for more on SecurityContext and
  PodSecurityPolicies in Subkube

- we've set limits for the pod using `--limits`, please see
  [Concept: Projects](/concepts/projects) for more on Resource Limits in Subkube


```sh
kubectl run -n <YOUR-NAMESPACE> -i --tty busybox --image=busybox \
  --overrides='{"spec":{"securityContext":{"runAsUser":65534}}}' \
  --limits='cpu=100m,memory=100Mi' --rm sh
$ echo 'hello world!'
hello world!
```
