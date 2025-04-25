# 多次触发

那ThresholdShedder 打分时使用的**历史加权算法**呢？它是用来**解决负载抖动问题**的，但是前面的分析与实验都证明了它会带来严重的负面影响，因此我们不能再采用它来解决负载抖动问题，应该通过其他方式来解决负载抖动问题（稳定性问题）。

我们设计了**多次触发**的机制：需**多次且连续**触及阈值才会最终触发 bundle unload 操作。例如，当一对 broker 之间的差距达到阈值，且这种情况**连续出现 3 次**时，便会触发负载均摊。

&#x20;

如何实现同样是一个值得深入探讨的问题。例如，存在三台 broker，其中 broker1 的负载为 80，broker2 的负载为 80，broker3 的负载为 20 。由于 broker1 与 broker2 的负载相近，在执行过程中，首次可能是 broker1 与 broker3 配对并判定达到阈值；第二次则可能是 broker2 与 broker3 配对并判定达到阈值；到了第三次，依然是 broker2 与 broker3 配对并判定达到阈值，但是**broker2与 broker3 没有连续3次触发阈值**，在此种情况下，我们是否需要触发 bundle unload 操作呢？

答案是**肯定的**。原因在于我们设定了连续触发阈值这一条件。若 `<broker2, broker3>` 与 `<broker1, broker3>` 这两种配对组合交替出现，那么将永远无法触发 bundle unload 操作。当负载相近的 broker 数量较多时，**触发 bundle unload 需经历漫长的等待时间**。

&#x20;

因此我的实现方式为：维护一个`Map<String, Integer>`，key为broker名，value为触发次数。

* 当发现`Pair<brokerX,brokerY>`超过阈值时，则往Map里插入`Entry<brokerX,1>`和`Entry<brokerY,1>`
* 在下一次 shedding 过程中，若再次判定 brokerX 处于超载（或低载）状态，则将其更新为 `Entry<brokerX, 2>`；若 brokerX 未被判定为超载（或低载），则删除 `Entry<brokerX, 1>`。需注意的是，`Entry<brokerY, 1>`不会因上述操作而被删除，即 brokerX 的负载状况不会对 brokerY 的 Entry 产生影响。
* 当某次 shedding 判定中，`Pair<brokerX, brokerZ>` 超过阈值，将 Map 更新为 `Entry<brokerX, 3>`时，由于**连续**触发次数达到阈值 3 ，因此触发 bundle 卸载操作，均摊 brokerX 与 brokerZ 的负载。



使用上面的例子来介绍这个算法：

* 第一次shedding：broker1与broker3配对，记录Entry\<broker1,1>，Entry\<broker3,1>
* 第二次shedding：broker2与broker3配对，记录Entry\<broker2,1>，Entry\<broker3,2>，剔除Entry\<broker1,1>
* 第三次shedding：则无论最高载机器是broker1还是broker2，都会有Entry\<broker3,3>，从而触发shedding。如果最高载机器是broker1，则均摊broker1与broker3的负载；如果最高载机器是broker2，则均摊broker2与broker3的负载。

&#x20;

在诸如集群滚动重启、broker 扩缩容等场景下，不同 broker 间常常会出现显著的负载差异。鉴于我们希望能够快速地完成负载均衡，故而引入了两种阈值：

* loadBalancerAvgShedderLowThreshold，默认值为15
* loadBalancerAvgShedderHighThreshold，默认值为40

两种阈值分别对应两种触发次数要求：

* loadBalancerAvgShedderHitCountLowThreshold，默认值为8
* loadBalancerAvgShedderHitCountHighThreshold，默认值为2

当配对的两个 broker 分数差距超过 LowThreshold 且连续达到 HitCountLowThreshold 次，或者分数差距超过 HighThreshold 且连续达到 HitCountHighThreshold 次时，便会触发 bundle unload。

例如，当分数差距超过 15 时，需连续触发 8 次才会触发 bundle unload；而当分数差距超过 40 时，仅需连续触发 2 次即可。broker 间的负载差距越大，触发 bundle unload 所需的次数就越少，如此设计能够适配 broker 扩缩容等场景。由于负载抖动通常不会致使 broker 的负载出现如此大幅度的变化，因此该机制能够在稳定性和响应速度之间实现良好的平衡。

`org.apache.pulsar.broker.loadbalance.impl.AvgShedder#findBrokerPairs`&#x20;

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

在计算需卸载的流量大小时，AvgShedder 会同时考量消息速率与流量吞吐，且优先关注消息速率。为避免引入过多配置项，AvgShedder 复用了 UniformLoadShedder 的如下三个配置：

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

* minUnloadMessage控制卸载的消息速率的最低阈值
* minUnloadMessageThroughput控制卸载的流量吞吐的最低阈值。
* maxUnloadPercentage 用于控制分摊比例，其默认值为 0.2。鉴于我们旨在平均分摊两个 broker 的压力，故而**建议将其设置为 0.5**。如此一来，当负载均衡完成后，两个 broker 的消息速率（即流量吞吐）将近乎相等。我已在注释中对此予以提醒。

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

实现地方如下：

`org.apache.pulsar.broker.loadbalance.impl.AvgShedder#selectBundleForUnloading`

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

