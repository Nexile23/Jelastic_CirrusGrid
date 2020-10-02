Jelastic PaaS combines two types of containers in a single platform. These containerization technologies are oriented to solve different problems, but Jelastic orchestration inherits benefits of both implementations.

- **[System Containers](https://docs.jelastic.com/what-are-system-containers/)** - one of the oldest container types, which is quite similar to virtual machines. It is a stateful, operating system centric solution that can run multiple processes. System containers are usually used for traditional or monolithic applications, as they allow to host architectures, tools, and configurations implemented for VMs. There are different implementations of system containers: LXC/LXD, OpenVZ/Virtuozzo, BSD jails, Linux vServer, and some others. Jelastic PaaS uses Virtuozzo solution.
- **[Application Containers](https://docs.jelastic.com/what-are-application-containers/)** - a relatively new container type, which commonly runs a single process inside. It is a stateless microservice-centric solution that is easily scalable horizontally. Application containers are the most suitable for immutable and ephemeral infrastructures. Several application container implementations are available at the market: Docker, containerd, CRI-O, and some others. Jelastic PaaS utilizes Docker as the most widely adopted technology for application containers.

![](https://jelastic.com/blog/wp-content/uploads/2020/07/application-container-system-container-1-300x191.png)

Talking about containers nowadays, people often think of Docker technology, as it was highly promoted and adopted during the last years. Most cloud vendors offer Docker application containers inside Virtual Machines. Each VM includes Guest OS with its own memory, CPU and disk footprint that increases the amount of required resources to run the application and thus make its hosting more expensive. In case with Jelastic, Docker technology is running inside system containers within the same kernel. Thus they share OS resources from the host operating system and reduce the consumption. And while being more lightweight than VMs, these nested containers are still highly isolated and secure.

![](https://jelastic.com/blog/wp-content/uploads/2020/07/system-container-vs-virtual-machine--768x321.png)

Within the Jelastic platform, different container types can be used for various use cases:

- Certified Managed Containers
- Elastic Virtual Private Servers (Elastic VPS)
- Custom Docker Containers
- Docker Engine CE (Docker Native)
- Kubernetes Cluster

Below we will review each case in detail, as well as provide some hints on what options can be more appropriate for your project.<p>&nbsp;</p>

> ## Certified Managed Containers

The most common and recommended choice for Jelastic customers is **certified containers**. The platform offers multiple pre-configured and managed [software stacks](https://docs.jelastic.com/software-stacks-versions/), that allow creation of flexible topologies with the required **application server** (_Java, PHP, Node.js, Ruby, Python,_ or _Go_), **load balancer**, **databases**, etc. 

![](https://jelastic.com/blog/wp-content/uploads/2020/07/Certified-Managed-Containers-2-768x690.png)

All of these certified containers are thoroughly tested and optimized specifically for the most common scenario within the platform. Jelastic team regularly updates these software stacks to the newest available stable versions or apply security patches to already released container images.

![](https://jelastic.com/blog/wp-content/uploads/2020/07/Certified-Managed-Containers-scheme.png)

Usually, these containers also benefit from additional integrations, such as automated configuration based on the resource ([cloudlet](https://www.qloud.my/cirrusgrid/)) scaling limits, automated SSL certificates installation, application deployment automation, built-in [auto-clustering](https://docs.jelastic.com/auto-clustering/), managed delivery of security updates, and others.<p>&nbsp;</p>

> ## Virtual Private Servers (Elastic VPS)

The most straightforward example of a system container implementation is a _**virtual private server**_. Jelastic offers [Elastic VPS](https://docs.jelastic.com/vps/) containers with the following pre-installed operating systems: _CentOS_, _Ubuntu_, and _Debian_. It is a pure OS-based container without any additional customization or software installed. It can be considered as the most suitable option for containerizing legacy applications as it requires minimal to no changes while migration from VMs. 

![](https://jelastic.com/blog/wp-content/uploads/2020/07/Elastic-VPS-768x687.png)

So, since it is practically “empty” after the installation, all the required configurations should be done by the end user. In order to help you with this task, root access is provided to Elastic VPS containers. It’s almost like a virtual machine but more lightweight and with the advantages of automatic vertical and horizontal scaling.<p>&nbsp;</p>

> ## Custom Docker Containers

The _**Custom Docker Container**_ is a _Docker image_ (based on the [supported OS](https://docs.jelastic.com/docker-supported-distributions/)) deployed inside the Jelastic system container, which makes it compatible with the most (but not all) platform-distinguishing features, such as built-in vertical and horizontal scaling. In other words, the filesystem of your custom Docker image is unpacked inside the system container runtime.

![](https://jelastic.com/blog/wp-content/uploads/2020/07/Custom-Docker-Containers-scheme.png)

Compared to the certified managed containers, this option provides access to a wider choice of software stacks. You can select from a vast range of 3rd party Docker images available at the Docker Hub or any other compatible public or private container registry. However, software operability and compatibility within the platform cannot be guaranteed as it is managed by respective 3rd party image maintainers.

![](https://jelastic.com/blog/wp-content/uploads/2020/07/Custom-Docker-Containers-1-768x473.png)
<p>&nbsp;</p>

> ## Docker Engine CE (Docker Native)

Jelastic PaaS provides support for the _**Docker Engine Community Edition**_ that is running inside system containers but at the same time has full compatibility to the native Docker ecosystem. 

![](https://jelastic.com/blog/wp-content/uploads/2020/07/Docker-Engine-CE.png)

Such integration makes it possible to work with the core tools of Docker container technology, namely:

- **Docker Engine** - processes Dockerfile manifests or runs pre-built container images 
- **Docker Registry** - stores and provides access to numerous public and private images, intended for deployment within Docker Engine
- **Docker Compose** - helps to assemble applications, that consist of multiple components where all the required configurations are declared within a single compose file
- **Docker Swarm** - represents several independent Docker nodes, interconnected into a cluster

Jelastic provides a pre-packaged version of the Docker Engine CE solution and Docker Swarm Cluster with integrated [auto-clustering](https://jelastic.com/blog/docker-swarm-auto-clustering-and-scaling-with-paas/). 

![](https://jelastic.com/blog/wp-content/uploads/2020/07/Docker-Engine-CE-swarm-cluster-1.png)

Read our dedicated blog post series for more details:

- [Auto-Install Docker Engine and Connect It to Swarm Cluster](https://jelastic.com/blog/docker-engine-automatic-install-swarm-connect/)
- [Docker Swarm Auto-Clustering and Scaling](https://jelastic.com/blog/docker-swarm-auto-clustering-and-scaling-with-paas/)
- [Connecting to Docker Engine and Its Management](https://jelastic.com/blog/docker-engine-auto-install-connect-ssh-portainer/)
- [Deploying Services to Docker Swarm Cluster](https://jelastic.com/blog/deploy-services-docker-swarm-cluster/)
<p>&nbsp;</p>

> ## Kubernetes Cluster
Application containers can be run and managed with the help of Kubernetes orchestration tool. It is an open-source platform designed for deployment and management of fault-tolerant containerized applications. It can handle complex tasks of container orchestration, such as deployment, service discovery, rolling upgrades, self-healing, and security management.

![](https://jelastic.com/blog/wp-content/uploads/2020/07/Kubernetes-Cluster-scheme.png)

[Jelastic PaaS Kubernetes](https://jelastic.com/blog/kubernetes-cluster-scaling-pay-per-use-hosting/) implementation automates cluster installation, configuration, updates, and supplies multiple pre-integrated services (e.g. Weave CNI, CoreDNS, Traefik, etc.).

![](https://jelastic.com/blog/wp-content/uploads/2020/07/Kubernetes-Cluster.png)

In other words, we run Kubernetes with the help of Jelastic orchestration providing maximum interoperability for projects that were designed for Kubernetes from the beginning. The major benefit of Jelastic Kubernetes implementation is the advanced pay-per-use model that solves right-sizing problem and makes hosting of multiple containers more cost efficient.      

Additional information on the Kubernetes Cluster can be viewed via the appropriate documentation sections:

- [Kubernetes Overview](https://docs.jelastic.com/kubernetes-cluster/)
- [Kubernetes Cluster Access](https://docs.jelastic.com/kubernetes-cluster-access/)
- [Scaling Kubernetes on Application and Infrastructure Levels](https://jelastic.com/blog/scaling-kubernetes/)
- [Kubernetes Helm Integration](https://docs.jelastic.com/kubernetes-helm-integration/)
- [Kubernetes Volume Provisioner](https://docs.jelastic.com/kubernetes-volume-provisioner/)

Now, you know about various container types available at Jelastic PaaS, as well as their specifics that can help to choose the most suitable option for your project needs. Get started with a preferable [Jelastic service provider](https://www.qloud.my/cirrusgrid/).
