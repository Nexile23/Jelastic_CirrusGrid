In this article, we would like to share specifics of Java memory management and elasticity inside containers that are not evident at the first sight. Below you’ll find a list of the issues to be aware of and important updates in the upcoming JDK releases, as well as existing workarounds for the core pain points. We collected the top 5 most interesting and useful tips to improve resource usage efficiency for Java applications.

> ## Java Heap Memory Limit in Docker
Currently, the community is discussing questions about incorrect memory limits determination while running Java applications in Docker Containers.

![](https://jelastic.com/blog/wp-content/uploads/2017/04/screen.png)

The issue is that if Xmx option is not defined explicitly, then JVM uses 1/4th of all memory available for the host OS due to a default internal garbage collection (GC) ergonomic algorithm. This can lead to killing the Java process by the kernel if JVM memory usage grows over the cgroups limit defined for a Docker container.

To solve this problem, one improvement was recently implemented in OpenJDK 9:  

| _A first experimental change has been added to OpenJDK 9 so the JVM can understand that it is running within a container and adjust memory limits accordingly.” from [Java 9 Will Adjust Memory Limits if Running with Docker](https://www.infoq.com/news/2017/02/java-memory-limit-container/) article._ |
| ----------- |

A new JVM option (**-XX:+UseCGroupMemoryLimitForHeap**) automatically sets Xmx for a Java process according to memory limit defined in cgroup.

As a good workaround to solve the issue before public Java 9 release, Xmx limit can be specified explicitly in startup options for JVM. There is an open pull request for “a script to set better default Xmx values according to the docker memory limits” in the [official OpenJDK repo](https://github.com/docker-library/openjdk/pull/71).

CirrusGrid managed to omit incorrect memory limit determination by using an enhanced system containers virtualization layer in combination with Docker images. Earlier we explained how it works in the article [Java and Memory Limits in Containers: LXC, Docker and OpenVZ](https://jelastic.com/blog/java-and-memory-limits-in-containers-lxc-docker-and-openvz/).<p>&nbsp;</p>

> ## Tracking Native Non-Heap Memory Usage

While running Java applications in the cloud it’s also important to pay attention to the native memory usage by Java process, so-called off-heap memory. It can be consumed for different purposes:

- Garbage Collectors and JIT optimizations are tracking and storing data of the object graphs in the native memory. Moreover, since JDK8, names and fields of classes, bytecode of the methods, constant pool, etc are now located in Metaspace which is also stored outside of JVM heap.

- Also, to gain high performance a number of Java applications allocates memory in the native area. Using [java.nio.ByteBuffer](https://docs.oracle.com/javase/7/docs/api/java/nio/ByteBuffer.html) or 3rd party JNI libs those applications store large, long-lived buffers that are managed with underlying system’s native I/O operations.

By default, Metaspace allocation is only limited by the amount of available native OS memory. And in combination with incorrect memory limits determination in Docker containers, this increases the risk of application instability. Limiting the size of the metadata is important, specially if you are having OOM issues. Do this with the special option **-XX:MaxMetaspaceSize**.

With all the objects stored outside of the normal garbage-collected heap memory, it is not obvious what impact they can have upon the memory footprint of a Java application. There is a good article that explains the issue in details and provides some guidelines how to analyze the native memory usage:

| _“A few weeks ago I faced an interesting problem trying to analyze a memory consumption in my Java application (Spring Boot + Infinispan) running under Docker. The Xmx parameter was set to 256m, but the Docker monitoring tool displayed almost two times more used memory.” - [Analyzing java memory usage in a Docker container](http://trustmeiamadeveloper.com/2016/03/18/where-is-my-memory-java/)._ |
| ----------- |

And interesting conclusions of the author:

| _“What I can say as a conclusion? Well… never put words “java” and “micro” in the same sentence 🙂 I'm kidding - just remember that dealing with memory in case of java, linux and docker is a bit more tricky thing than it seems at first.”_ |
| ----------- |

In order to track native memory allocation, a specific JVM option can be used (**-XX:NativeMemoryTracking=summary**). Please note that you’ll get a 5-10% performance hit if you enable this option.<p>&nbsp;</p>

> ## Resizing JVM Memory Usage in Runtime
Another useful solution for reducing memory consumption by a Java application is to adjust JVM manageable options on the fly while java process is running. Since JDK7u60 and JDK8u20, the options **MinHeapFreeRatio** and **MaxHeapFreeRatio** became manageable, that means we can change their values in runtime without the need to restart the Java process.

In the [Runtime Committed Heap Resizing](https://dzone.com/articles/runtime-commited-heap-resizing) article the author describes how to reduce memory usage tweaking these manageable options:

| _“...Resizing worked one more time and heap capacity has increased from 159MB to 444MB. We described that minimum 85% of our heap capacity should be free, and that pointed JVM to resize the heap to gain at most 15% usage.”_ |
| ----------- |

Such approach can bring significant resource usage optimization for variable workloads. And the next step to improve JVM memory resizing can be allowing to change Xmx in runtime mode without Java process restart.<p>&nbsp;</p>

> ## Improving Memory Compaction

In many cases, customers want to minimize the amount of memory used within Java applications inducing more frequent GCs. For example, it can help to save money by utilizing resources more efficiently in dev, test and build environments, as well as on productions after load spikes. However, according to [the official enhancement ticket](https://bugs.openjdk.java.net/browse/JDK-8146436), current GC algorithms require multiple full garbage collection cycles to release all free unused memory.

As a result, a new JVM option (**-XX:+ShrinkHeapInSteps**) was introduced to regulate GC algorithm behavior in JDK9. This setting should be changed to **-XX:-ShrinkHeapInSteps** for disabling the 4 full GC cycles. That will release unused RAM resources faster and minimize the Java heap size usage in applications with variable load.<p>&nbsp;</p>

> ## Reducing Memory Usage to Speed Up Live Migration

Live migration of Java applications with heavy memory consumption takes a significant amount of time. To decrease total migration time and resource overhead, the migration engine should minimize transmitted data between the hosts. It can be done by compacting RAM with a help of the full GC cycle before live migration process. Such approach can be more cost-effective for a variety of applications to overcome performance degradation during the GC cycle than to migrate with unpacked RAM.

We found a great research work related to this topic: GC-assisted JVM Live Migration for Java Server Applications. The authors integrate JVM with CRIU (Checkpoint/Restore In Userspace) and introduce a new GC logic for reducing the time of Java application live migration from one host to another. The offered method allows to enable a migration-aware garbage collection before taking the snapshot of the Java process state, then freeze a running container by checkpointing it on disk and later restore container from the point it was frozen.

Also, Docker community is integrating CRIU into the mainstream. At the moment this feature is still at an experimental stage.

A combination of both (Java and CRIU) can unleash still undiscovered opportunities of performance and deployment optimization for improving Java application hosting in the cloud.  You can find more specifics how containers live migration works in the cloud within the article “[Containers Live Migration: Behind the Scenes](https://www.infoq.com/articles/container-live-migration/)”.

Java is great and already works nicely in the cloud and specifically in containers, but we believe it can be even better. So in this article we covered a set of current issues that can be already improved to run Java applications smoothly and efficiently.

At CirrusGrid we are running thousands of Java containers in hundreds of datacenters worldwide. Good memory management is critical to us. That's why we constantly incorporate new findings on Java memory into our platform, so developers don't have to explicitly deal with those issues. [Experiment running your Java containers on the CirrusGrid enhanced platform](https://www.qloud.my/cirrusgrid/).
