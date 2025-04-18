# Chapter 2: Load Balancing Algorithm Principles and Analysis -- Load Shedding Strategy

In Chapter 1, we introduced the basic principles of load balancing and compared the performance of different algorithms to obtain an important scoring table. In the next two chapters, we will delve into the implementation principles of different algorithms and elaborate on the process of obtaining this scoring table. This analysis process is also the cornerstone for the subsequent design of the new algorithm `AvgShedder`. Only by accurately identifying the root causes of defects in the old algorithms and avoiding these issues in the design of the new algorithm can we scientifically and reasonably design a new algorithm.&#x20;

We previously introduced that the execution of load balancing is divided into `LoadSheddingStrategy` and `ModularLoadManagerStrategy`. Therefore, in this chapter, we will first introduce the `LoadSheddingStrategy` and then introduce the `ModularLoadManagerStrategy` in the next chapter.





<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

The load balancer calls `LoadSheddingStrategy#findBundlesForUnloading` to find the bundles that need to be unloaded. It passes the load data (`LoadData`) it collects and the broker configuration (configured by the `broker.conf`file) and obtains a `MultiMap` that maps brokers to bundles.

The load data `LoadData` includes the throughput, message rate, and resource usage (including CPU usage, network card in/out usage, direct memory usage, and memory usage) of each broker, as well as the throughput and message rate information of each bundle, for the use of the load balancing algorithm.



