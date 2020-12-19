Subkube has an integrated interactive web-based console you can use to interact with your namespaces using tools like `kubectl` and `helm`. The interactive console uses websockets and can be used in any modern browser.


## Using the web terminal

An interactive console can be opened from the *Project Overview* page, using the **Interactive Terminal** button.

![Subkube UI Interactive Terminal Button](/img/screenshots/interactive_terminal_button.png){: .shadow}

A new session will be created, and the terminal will be opened in a new tab.

![Subkube UI Interactive Terminal Window](/img/screenshots/interactive_terminal_open.png){: .shadow}

!!! warning
    Please note that each session is only valid for the duration of a single request. Refreshing the page will invalidate the session and discard your working environment.


### Setting a default namespace to use

You can easily update your `kubeconfig` to use a single default namespace using the following command.

```
kubectl config set-context --current --namespace <YOUR-NAMESPACE>
```

By setting a default namespace, you don't have to provide one for each individual `kubectl` / etc call.


## Included Tools

The following tools are included in the interactive terminal image:

- kubectl
- helm
- kubectx
- k9s
