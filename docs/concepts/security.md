As a shared tenant platform, Subkube takes security very seriously. Luckily, the container & kubernetes ecosystem have been designed from the ground up with security on various levels in mind.


# Access Control

Security starts at the front door. The Subkube platform uses state-of-the-art technology to ensure a user's identity before allowing access to any of our systems and applications.


## 2 Factor Authentication

In general it's good habit to enable 2 factor authentication for your accounts whenever possible. Subkube offers a basic TOTP implementation, allowing you to use most Authenticator apps to generate tokens allowing you to login using the application interface or our `subctl` CLI.

During 2FA setup, a backup token will be generated and shown, allowing you to regain access to your account if for some reason your TOTP applications is no longer able to produce valid tokens for your Subkube account (a lost phone for instance). Be sure to store the backup token in a safe place, to ensure access to your Subkube account at all times.


## API Keys

In modern day IT, we want to automate everything, but not at the expense of security. Fine-grained access control for automated processes is key in achieving this. 

Subkube implements a flexible way to create API tokens, to be consumed by various application, such as 3rd party services like Github and Gitlab, scripts running locally or on premise, but also in-cluster, to be used by auto-scaling tools for example. Tokens can be revoked at any time easily from the Subkube interface.


# Workload Isolation

As we briefly outlined in [Workloads](workloads.md), Subkube kubernetes clusters enforce certain policies and restrictions on the workloads customers run, the most important of which are the following:

## Restricted Pod Security Policy

All namespaces created on Subkube have a 'restricted' PodSecurityPolicy applied by default. Amongst other things, this prohibits containers from running as the `root` user, and all security implications this has. For most applications, and well designed container images, this shouldn't pose a problem.

As containers can not run as root, most processes will be unable to, for instance, allocate a port lower than `1000`. Also, package management isn't available while not running as user `root`. Though this might seem problematic at first, it really shouldn't be. 

If a container has been though-out and built properly, applications will never need these kinds of permissions in  order to run, and as such, neither does your container. For instance, if you feel you need `systemd` or `init` inside your container, you're probably trying to put various processes in a single container, which is bad practise, as Kubernetes offers you the Pod concept, specifically to deal with these kinds of "groups" of processes.


## Namespace Network Isolation

As in any decent microservices architecture services need to be able to talk to oneanother, providing network connectivity between pods is vital to any Kubernetes deployment. However, as Subkube uses a shared tenancy principle, we do need to seperate traffic from various customers.

We accomplish this by isolating traffic to a namespace, using Network Policies. Our clusters run Canal, a mixed implementation of Flannel to provide inter-node and inter-pod connectivity, combined with Calico to provide network policy management & enforcement. 

By default, when a Namespace is created inside a project, NetworkPolicies are defined inside the namespace to allow traffic between the namespaces inside your project, and namespaces hosting Subkube shared services, such as the Ingress Controller.


# Cluster Operations

## Hardening

All our Kubernetes nodes run on selinux-enforced CentOS 8 Core, hardened according to enterprise standard. Our Kubernetes deployment takes all Kubernetes hardening considerations into account, and we run daily automated audits to ensure our clusters are compliant with the CIS Hardened benchmark.

## End-to-end Encryption

We use Wireguard, both to connect our control-plane and worker nodes together, and to connect our frontend and APIs to our backing Kubernetes clusters, hosted across the internet. Wireguard is state-of-the-art and allows for full encryption with minimal overhead, to ensure fast response times across the platform.

## Let's Encrypt Integration

All projects utilising Ingress for connectivity benefit from out Let's Encrypt integration, which offers a wildcard certificate to be used by ingress resources in project namespaces, so we can properly enforce SSL for connecting to Ingress-exposed services.
