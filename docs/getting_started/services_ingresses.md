Subkube comes with an easy to use User Interface to list services for your workloads and create Ingresses for them, including setting up a subdomain with SSL certificate on us.


## About Services & Ingresses

In Kubernetes, **Services** are used to tell kubernetes what *ports* your *containers / pods* expose to allow network communication. Services are often created part of a (Helm)[] chart, or created for you when using (Kompose)[] for instance. If you are crafting your own workloads however, you will need to create these yourself as well.

**Ingresses** allow you to expose these *services* using a proxy. An ingress consists of a host name and an optional path to route external traffic to. It also offloads SSL using certificates configured & stored in Kubernetes.

In Subkube, you *have* to use an ingress resource to allow access to your application from the outside world, that way we can protect your application from malicious traffic like DDOS attacks, and keep your visistors surfing' smoothly.


## Listing Services

On the Project Show page, you can find a list of all services deployed to one of the projects' namespaces, along with any ingresses currently defined for that service.

![Subkube UI Services List](/img/screenshots/services_list.png){: .shadow}


## Creating an Ingress
