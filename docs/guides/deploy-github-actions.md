You can use Github actions to deploy to Subkube directly from Github.

## Prerequisites

- A Namespace in a Subkube Project
- A Github account

## Steps

### Prepare Application

For this guide we will be deploying Nextcloud, which we covered in our [Kompose](kompose.md) guide.

After fetching the resources from our examples repository, you should initialize a new github repository containing our k8s manifests. 

### Setup Kubeconfig in Github

In order to deploy to Subkube using kubectl from Github Actions, we need to store a copy of our kubeconfig inside a Github environment variable.

### Add Kubectl action

After we've setup our kubeconfig in Github, we can define an Action using this config to deploy to subkube. Define the following action inside the `.github/workflows/deploy.yaml` file.

### Commit and Deploy

Now everything is setup, we can push our changes and watch them be applied to your Subkube project.
