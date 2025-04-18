# 第二章 负载均衡算法原理与分析 -- 卸载策略LoadSheddingStrategy

第一章我们简单介绍了负载均衡的基本原理，并对不同算法的性能进行比较，得到了一个重要的评分表格，接下来两章我们会深入分析不同算法的实现原理，详细阐述获取该评分表格的过程。这一分析过程，同时也是后续设计新算法 AvgShedder 的基石。只有精准找出旧算法存在缺陷的根源，并在设计新算法 AvgShedder 时规避这些问题，才能够科学合理地设计出全新算法。

我们前面介绍了，负载均衡的执行分为卸载策略LoadSheddingStrategy和放置策略ModularLoadManagerStrategy，因此这一章我们先介绍卸载策略LoadSheddingStrategy，下一章介绍放置策略ModularLoadManagerStrategy。

&#x20;

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

负载均衡器调用LoadSheddingStrategy#findBundlesForUnloading来找到将要卸载的bundle，传入它搜集到的负载数据LoadData和broker配置（由broker.conf文件配置），得到一个MultiMap，映射broker到bundle。

负载数据LoadData包含了每个broker的流量吞吐、消息速率、资源使用率（包括CPU使用率、网卡进/出使用率、直接内存使用率、内存使用率），和每个bundle的流量吞吐、消息速率信息等，供负载均衡算法使用。

&#x20;







