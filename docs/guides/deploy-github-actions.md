You can use Github actions to deploy to Subkube directly from Github.

## Prerequisites

- A Namespace in a Subkube Project
- A Github account

## Steps

### Prepare Application

For this guide we will be deploying Nextcloud, which we covered in our
[Kompose](kompose.md) guide. The resulting manifests can easily be fetched from
our examples repository, as shown below.

```
curl -LO https://github.com/subkube/examples/archive/master.zip
unzip master.zip
mkdir -p nextcloud-from-github/manifests
cp -R examples-master/kompose/output-after/* nextcloud-from-github/manifests/
cd nextcloud-from-github
```

### Prepare Repository

After fetching the resources from our examples repository, you should create a
new Github repository.

![Create Github repository](/img/screenshots/github_actions/create_repo.png){: .shadow}

Now we can initialize the local git repository, and set up Github as it's `origin`:

```
git init
git add manifests/
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:subkube/nextcloud-from-github.git
git push -u origin main
```

### Setup Kubeconfig in Github

In order to deploy to Subkube using kubectl from Github Actions, we need to store
a copy of our kubeconfig inside a Github secret.

In your Github repository, under the **settings** tab, navigate to the
**Secrets** page, and click the **Create Repository Secret** button. The `kubectl`
*Action* we'll be using a base64 encoded version of our `kubeconfig`, which we can
get using the following command:

```
cat $KUBECONFIG | base64
```

Now create the secret using the encoded config:

![Create Github Repository Secret](/img/screenshots/github_actions/create_secret.png){: .shadow}

### Add Kubectl action

After we've setup our kubeconfig in Github, we can define an Action using this
config to deploy to subkube. Define the following action inside the `.github/workflows/deploy.yaml`
file.

=== ".github/workflows/deploy.yaml"
    ``` {.yaml}
    --8<--
    examples/deploy-github-actions/nextcloud-from-github/.github/workflows/deploy.yaml
    --8<--
    ```


### Commit and Deploy

Now everything is setup, we can push our changes and watch them be applied to your Subkube project.

```
git add .github/
git commit -m "Add Github deploy to Subkube Action"
git push origin main
```

Our workflow should now be visible under the **actions** tab:

![Github Action Output](/img/screenshots/github_actions/deploy_action.png){: .shadow}
