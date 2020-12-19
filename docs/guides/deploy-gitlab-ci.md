You can use Gitlab CI/CD to deploy to Subkube directly from Gitlab.

## Prerequisites

- A Namespace in a Subkube Project
- A Gitlab(.com) account

## Steps

### Prepare Application

For this guide we will be deploying Nextcloud, which we covered in our
[Kompose](kompose.md) guide. The resulting manifests can easily be fetched from
our examples repository, as shown below.

```
curl -LO https://github.com/subkube/examples/archive/master.zip
unzip master.zip
mkdir -p nextcloud-from-gitlab/manifests
cp -R examples-master/kompose/output-after/* nextcloud-from-gitlab/manifests/
cd nextcloud-from-gitlab
```

### Prepare Repository

After fetching the resources from our examples repository, you should create a
new Gitlab repository.

> ![Create Github repository](/img/screenshots/github_actions/create_repo.png){: .shadow}

Now we can initialize the local git repository, and set up Gitlab as it's `origin`:

```
git init
git add manifests/
git commit -m "first commit"
git remote add origin git@gitlab.com:stud-io/subkube/nextcloud-from-gitlab.git
git push -u origin master
```

### Setup (Partial) Gitlab Kubernetes Integration

In order to deploy to Subkube using kubectl from Gitlab CI, we'll need to add
Subkube as a Kubernetes environment to Gitlab. You don't need to supply a PEM
certificate, as you use the Subkube API proxy to interface with the Kubernetes API.

![Create Kubernetes Gitlab Environment](/img/screenshots/gitlab_cicd/k8s_setup.png){: .shadow}

### Add Kubectl action

After we've setup our kubeconfig in Github, we can define an Action using this
config to deploy to subkube. Define the following action inside the `.github/workflows/deploy.yaml`
file.

=== ".gitlab-ci.yml"
    ``` {.yaml}
    --8<--
    examples/deploy-gitlab-ci/nextcloud-from-gitlab/.gitlab-ci.yml
    --8<--
    ```


### Commit and Deploy

Now everything is setup, we can push our changes and watch them be applied to
your Subkube project.

```
git add .gitlab-ci.yml
git commit -m "Add Gitlab CI deploy to Subkube job"
git push origin main
```

> Our workflow should now be visible under the **actions** tab:

> ![Github Action Output](/img/screenshots/github_actions/deploy_action.png){: .shadow}
