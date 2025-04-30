# 第三章 负载均衡算法--放置策略PlacementStrategy

第二章介绍了LoadSheddingStrategy策略的所有实现，这一章我们来深入分析放置策略的实现原理，并讨论不同放置策略与不同卸载策略LoadSheddingStrategy的配合效果，最终得到一个评分表格，供读者清晰地看到不同算法组合的优缺点。

放置策略由配置loadBalancerLoadPlacementStrategy控制，默认使用LeastLongTermMessageRate策略。

```
# load balance placement strategy, support LeastLongTermMessageRate and LeastResourceUsageWithWeight
loadBalancerLoadPlacementStrategy=org.apache.pulsar.broker.loadbalance.impl.LeastLongTermMessageRate
```

实现的接口名为org.apache.pulsar.broker.loadbalance.ModularLoadManagerStrategy，有四种实现。其中，RoundRobinBrokerSelector 通常不在考虑范围内，因其采用 RoundRobin 方式放置 bundle，并未考量诸如负载数据等关键信息。在后续内容中，我们将着重介绍 LeastLongTermMessageRate 与 LeastResourceUsageWithWeight 这两种实现方式，而 AvgShedder 的相关内容将留待下一章节展开讲解。

<figure><img src="../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>





















