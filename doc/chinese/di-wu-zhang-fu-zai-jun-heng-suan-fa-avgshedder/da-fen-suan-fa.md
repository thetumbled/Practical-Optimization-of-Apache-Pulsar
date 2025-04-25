# 打分算法

要确定高载broker，肯定要对broker进行打分并排序，当前有两种打分依据：

* broker的资源使用率
* broker的消息速率、流量吞吐

根据前面分析可知，如果根据消息速率、流量吞吐来打分就会像UniformLoadShedder一样面临**异构环境的问题**；而如果根据资源使用率来打分，在放置bundle的时候会像LeastResourceUsageWithWeight一样面临**过度加载的问题**。难道真的是熊掌与鱼不可兼得吗？

深入思考LeastResourceUsageWithWeight过度加载问题的根源，会发现问题不是因为打分依据导致的，而是因为**卸载策略和放置策略是两个独立的模块，两者之间没有信息互通！**

&#x20;

我们举个例子就明白了，当采用 ThresholdShedder 与 LeastResourceUsageWithWeight 的组合，并使用默认配置时，假设当前有一组 broker，其打分分别为 20、51、52、80、80、80 。经计算平均分是 60.5。ThresholdShedder 会将三个分值为 80 的 broker 判定为高负载 broker，进而卸载相关 bundle。而依据 LeastResourceUsageWithWeight 算法，仅有打分 20 的 broker 会被选为候选 broker。如此一来，卸载下来的 bundle 将全部被分配到这个低负载 broker 上，极有可能使其转变为负载最高的 broker 。

&#x20;

然而，若**让人去手动进行负载均衡**，一种极为简单直观的思路是：**使得分最高的 broker 与得分最低的 broker 均摊负载（即流量吞吐大小相等），而这就是AvgShedder算法的雏形**。

以前面所举的例子来分析，假定负载均摊能够让两者的得分相同，让一个得分为 80 分的 broker 与得分为 20 分的 broker 进行负载均摊，这两个broker的得分都变成(80+20)/2 = 50，那么原本的打分序列 20,51,52,80,80,80 将转变为 50,50,51,52,80,80。倘若认为 50 分与 80 分之间的差距依旧较大，便可进一步实施均摊操作。因此我们引入一个阈值，当最低分数与最高分数之间的差距超过该阈值时，便会触发 bundle unload。

该算法的本质在于，在**执行bundle unload操作时便已指定接收方**，而非先完成对一批 bundle 的卸载，之后才开始确定分配对象。换言之，LoadSheddingStrategy 在进行卸载操作的同时，就已明确了后续 ModularLoadManagerStrategy 的决策结果。基于此，我们**让 AvgShedder 同时实现了 LoadSheddingStrategy 与 ModularLoadManagerStrategy 接口，将卸载策略与放置策略整合为一体**。

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

综上所述，**AvgShedder 依据 broker 的资源使用率进行打分，以此规避异构环境（适应性）所带来的问题，同时借助捆绑卸载策略与放置策略，有效防止过度加载问题的出现。**

&#x20;

读者可以发现，AvgShedder跟UniformLoadShedder有点像，它也是比较最高和最低载的broker，然后从最高载的broker上卸载bundle。我们前面也提到过，这种设计在每次shedding时只能处理一个高负载 broker，对于大规模集群而言，处理速度较慢。因此我们进一步做优化，一次shedding匹配多对高低载broker，即对broker打分排序后，将排名第一的与倒数第一的进行配对，排名第二的与倒数第二的进行配对，依此类推。当配对的两个broker之间的分数差值大于设定阈值时，则会在两者之间均摊负载，这样就能**解决速度慢的问题**了。

&#x20;

我们最后细化一下打分的算法，因为内存使用率和直接内存使用率都跟具体负载关系不大，因此我们还是会引入资源权值的机制来进行打分，即复用配置loadBalancerBandwithInResourceWeight、loadBalancerBandwithOutResourceWeight、loadBalancerCPUResourceWeight、loadBalancerDirectMemoryResourceWeight、loadBalancerMemoryResourceWeight。这样我们就可以屏蔽掉内存使用率和直接内存使用率的影响。

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

`org.apache.pulsar.broker.loadbalance.impl.AvgShedder#calculateScores`

