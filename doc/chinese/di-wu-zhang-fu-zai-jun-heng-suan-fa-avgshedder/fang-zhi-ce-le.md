# 放置策略

放置策略会在多种场景下使用到：

1. 执行shedding策略将bundle unload后，需要接着使用放置策略将该bundle分配给某个broker。
2. 机器重启、broker缩容等场景下，某个broker服务的bundle全部unload下来，需要分配给集群里的其他broker。
3. 集群初始化。

前面已经讲过，AvgShedder同时实现了卸载策略和放置策略，当一个 bundle 根据shedding策略执行unload操作时，其下一个 owner broker 便已依据shedding策略确定完毕，即第一种场景的逻辑我们设计好了。那么第二、第三种场景下，该如何分配这些bundle呢？

我们采用哈希分配的方式，即**通过哈希映射将（bundle 名 + 随机数）映射到 broker**。由于哈希映射大致符合均匀分布特性，所以 bundle 会大致均匀地分布于所有 broker 上。不过，**鉴于不同 bundle 之间的流量存在差异，集群会出现一定程度的不均衡状况**。但此问题并不严重，后续借助shedding策略便能实现均衡。况且像集群初始化、滚动重启、broker 缩容这类场景出现的频率较低，所以对整体影响不大。





