# Chapter 3: Load Balancing Algorithm Principles and Analysis -- Placement Strategy

In Chapter 2, we introduced all the implementations of the `LoadSheddingStrategy`. In this chapter, we will delve into the implementation principles of the placement strategy and discuss the effects of combining different placement strategies with different `LoadSheddingStrategy` implementations. Ultimately, we will present a scoring table that allows readers to clearly see the strengths and weaknesses of different algorithm combinations.

&#x20;

The placement strategy is controlled by the configuration `loadBalancerLoadPlacementStrategy`, with the default being the `LeastLongTermMessageRate` strategy.

<figure><img src=".gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

The implemented interface is named `org.apache.pulsar.broker.loadbalance.ModularLoadManagerStrategy`, and there are four implementations. Among them, `RoundRobinBrokerSelector` is usually not considered because it places bundles using a round robin method without considering key information such as load data. In the following sections, we will focus on the `LeastLongTermMessageRate` and `LeastResourceUsageWithWeight` implementations, while the content related to `AvgShedder` will be covered in the next chapter.

<figure><img src=".gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>













