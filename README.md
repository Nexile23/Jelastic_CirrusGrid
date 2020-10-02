# [Setting Up Environment with CirrusGrid Multi-Cloud PaaS](https://www.qloud.my/setting-up-environment-with-cirrusgrid-multi-cloud-paas/)


In application hosting, it always starts with creating an environment with the required isolated containers. This article will guide you through all the needed steps, with an extended explanation of available specifics and features.

**1.** Log in to the CirrusGrid dashboard, click the **New Environment** button in the upper-left corner.

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-1.jpg)

**2.** Within the opened **topology wizard** dialog, you can set up all the necessary customizations. We recommend performing adjustments in the following order:

* Choose programming language or specialized deployment solution (_Docker Engine_ or _Kubernetes Cluster_).
* Set up topology by adding required software stacks.
* Configure nodes resources and specifics.
* Review estimations, name the environment, and confirm the creation.

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-3-768x477.png)

Below, we’ll review each of these points in detail, providing explanations on all of the available possibilities.<p>&nbsp;</p>

> ## Choosing Programming Language or Specific Deployment Option
After creating an environment, you need to select the deployment solution required for your project.

**1.** The most common and recommended choice is **certified containers**. These stacks are specifically configured and managed by CirrusGrid (e.g. version updates, security patches).

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-3.jpg)

Click on the tab with the required programming language (_Java, PHP, Node.js, Python, Ruby, .NET_, or _Go_) to proceed with CirrusGrid certified containers.

**2.** The other deployment options are available via the _Docker_.

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-4.jpg)

* **Custom Docker Containers** - any Docker image (based on the supported OS) deployed into the system container. Compared to the **managed containers**, this option provides access to a greater variety of solutions, search the entire Docker Hub registry or use your private repository. However, the software operability and compatibility with the platform cannot be guaranteed as the content is managed by respective image maintainers. It's recommended to build custom Docker images based on CirrusGrid certified images using FROM instruction.
* **Docker Engine** - a Docker Engine CE deployed into the system container. It provides access to all the Docker native functionality, including deployment, scaling, and management of multiple application containers inside.
* **Kubernetes Cluster** - ready-to-go Kubernetes cluster with a preconfigured master and worker nodes created based on the system containers. The deployment, scaling, and orchestration of the microservices inside are handled by Kubernetes control units, while CirrusGrid scales and manages master and worker nodes.

**3.** Also, it is recommended to select the preferred region (if available) before proceeding further.

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-6.png)<p>&nbsp;</p>

> ## Configuring Topology

You can configure environment topology (layers structure) via the left part of the wizard. Here, the following blocks are available:

* **Load Balancers**- stacks that operate as an entry point for the environment to distribute incoming request and create even load on other nodes
* **Application Servers** (compute nodes) - web servers that run your application
* **Databases**(SQL & NoSQL) - database solutions to store and manage data
* **Cache Node** - a Memcached object caching system for speeding up web applications through alleviating database load
* **Shared Storage** - dedicated storage node with NFSv4 support, enlarged disk space, and optimized performance
* **Elastic VPS** - virtual private servers on top of the CentOS, Ubuntu, Debian, and Windows OS
* **Build Node** (SQL & NoSQL) - a build automation tool for Java projects
* **Extra** (custom layers) - any of the stacks mentioned above

**1.** CirrusGrid PaaS offers a number of the most popular options for each of these sections. Enable the blocks needed for your environment and select a suitable stack for the role.

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-7.png)

_**Tip:** If you cannot find a required software solution, you can add it as a custom Docker container from Docker Hub or your private repository._

**2.** An **SSL** protection can be configured for your environment via the same-named section. Here, two options are available:

* **Built-In SSL** - enables an already trusted SSL certificate, avoiding any additional checks and saving your time on the certificate validation. However, it is applied to the default environment domain name only (i.e. with the hoster's domain at the end) and does not work if Public IP is attached to your servers.
* **Custom SSL** - shows the pre-conditions of using your custom SSL certificates for the environment. Click the Enable button to automatically fulfill the requirements (e.g. enable Public IP) and refer to the linked instruction for further guidance.
_**Tip:** Also, you can apply the **Let’s Encrypt SSL** add-on after the environment creation to automatically issue and integrate a free SSL certificate._

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-9.png)<p>&nbsp;</p>

> ## Configuring Nodes Resources and Specifics
Once you are done with the topology structure, you can adjust each particular layer via the wizard’s central part. Let’s review the available options from the top to the bottom of the section.

**1.** You can toggle a layer **on/off**, as well as provide a custom alias for it.

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-10.png)

**2.** Configure the automatic vertical scaling by setting the number of reserved and dynamic cloudlets (1 cloudlet = **128MiB** of RAM and **400MHz** of CPU) for the nodes within the layer. Think of it as a minimum and maximum CPU & RAM capacities per server. Only consumed resources will be charged.

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-11.png)

**3.** The _**Horizontal Scaling**_ part allows defining several nodes within the layer and choosing a preferred scaling mode (stateful or stateless).

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-12.png)

If needed, you can use the drop-down lists to change stack and engine type/versions. In case of adjusting an existing environment, these lists will redirect to the container redeploy dialog to apply changes.

**4.** Next, you need to configure additional settings.

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-13.png)

The list may vary depending on the particular stack and account permissions:

* **Auto-Clustering** - automatic clusterization for some CirrusGrid certified templates. Additional fields can appear after activation, e.g. scheme selection (master-slave, master-master, or galera) for the database cluster.
* **Disk Limit** - an amount of disk space reserved per node. The dedicated Shared Storage containers are usually provided with enlarged storage capacity.
* **Sequential restart delay** - a delay between the restart operation completion on one node and start on the other. It is used to avoid downtime, ensuring that at least one server is active. You can set it to "-1" for the simultaneous restart of all nodes within the layer.
* **High-Availability** - automated session replication for the Tomcat and TomEE application servers.
* **Public IPv4/IPv6** - attach the specified number of external IP addresses to each node within the layer
    
**5.** At the bottom of the section, you can find buttons to the container configuration tools:

* **Variables** - review and manage a list of the environment variables for the current layer.
* **Links** - interconnect layers inside the environment
* **Volumes** - manage a list of the data volumes to ensure files integrity during container lifecycle.
* **Ports** - view information about containers' ports.
* **CMD / Entry Point** - configure containers' Entry Point and Run Command

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-14.png)<p>&nbsp;</p>


> ## Reviewing and Confirming Environment Creation

After all the configurations are done, you can review the amount of allocated resources and the estimated cost of the environment.

**1.** The main resource measuring units in CirrusGrid PaaS are cloudlets. Here, you can see the number of _reserved _ones and the _scaling limit_ (dynamic) for the whole environment.

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-15.png)

For better analysis, the values are divided into the color-marked groups:

* green - load balancers
* blue - application servers
* orange - databases and cache nodes
* gray - all other stacks

**2.** Next, you can view the **Estimated Cost** of your environment. The widget displays price using the same color-marking as above, and you can change the period – _hourly_, _daily_, or _monthly_.

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-16.png)

The **FROM** tab shows the price that will be fully charged as it covers reserved resources. The **TO** tab shows the maximum possible price if all nodes in the environment are going to fully consume all resources up to the scaling limit during the whole period.

_**Tip:** Hover over the pricing widget to view extended details on the estimated cost calculation:_

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-17.png)

If needed, you can click on the links under the widget for additional information on how pricing works and what is charged at CirrusGrid PaaS.

**3.** Lastly, provide a name for your environment and click the Create button to proceed.

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-18.png)

That’s all! In a few minutes, your new environment will appear on the dashboard.

![](https://www.qloud.my/wp-content/uploads/2020/06/setup-cg-guide-19.png)

Now, you are ready for application deployment and further use of your cloud environment.

Get maximum flexibility, ease-of-use, and scalability while hosting your cloud environments with CirrusGrid PaaS.

If you need more information, you may contact support at https://www.qloud.my/contact-us/

Source: https://www.qloud.my/setting-up-environment-with-cirrusgrid-multi-cloud-paas/
