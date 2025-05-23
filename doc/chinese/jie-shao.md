# 介绍



## 作者

冯文智，Apache Pulsar Committer，BIGO大数据存储高级工程师，毕业于华中科技大学。



## 动机

Apache Pulsar 是一个分布式发布-订阅消息系统，其定位与 Kafka 相似，然而，它在多个方面超越了 Kafka，例如实现了存储与计算的分离（支持**负载均衡、秒级负载切换、弹性扩缩容**）、支持高达**百万级别的 Topic 分区**、以及**读写分离**等先进特性。通过从 Kafka 迁移到 Pulsar，我们利用了 Apache Pulsar 提供的高吞吐量、低延迟和高可靠性等优势，显著增强了我们的消息处理能力，同时降低了消息队列的运维成本，并且节约了近 50% 的硬件开支。

然而，Pulsar是一个复杂的分布式系统，拥有近400个配置项。其底层使用的BookKeeper分布式存储系统同样包含超过200个配置项。

对于普通用户而言，几乎不可能根据自己的具体需求选择最合适的配置。即便是作为Pulsar核心开发者的我，也不敢仅凭配置说明文档就擅自修改配置值。通常需要深入分析代码，彻底理解配置的作用后，才敢进行修改。

实际上，许多默认设置往往不尽如人意。以Pulsar的一个关键组件——负载均衡为例，这是Pulsar相较于Kafka的一个显著优势所在。然而，其默认配置采用的负载均衡算法，即ThresholdShedder与LeastLongTermMessageRate的组合，却是一个糟糕的选择。经过我的分析和测试，这种配置的效果并不理想。但是，出于兼容性和稳定性等方面的考虑，许多配置的默认值是不宜更改的。因此，Pulsar的维护者们迫切需要掌握如何优化Pulsar服务的运行，以提供更卓越的服务。

在参与Apache Pulsar的开发与维护过程中，我积累了丰富的经验和研究成果。这些成果不仅以代码的形式贡献给了社区，还被整合进了社区的主仓库，例如新的**负载均衡算法AvgShedder**和**延迟队列TimeBucket算法**等。我也有幸成为了Apache Pulsar的Committer。然而，为了让这些成果发挥出最大的效用，用户的理解与正确配置至关重要。因此，本书旨在帮助用户掌握这些知识。

&#x20;

## 计划

我已用中文撰写了10章内容，总计近200页，内容包括Pulsar负载均衡算法、延迟队列（包括生产端和消费端）、以及BookKeeper负载均衡等主题。此前，这些材料以申请方式在Pulsar Meetup上分发给了部分参与者。

现在，我计划重新编写这些文章，使其更加通俗易懂，以便帮助Pulsar的用户和开发者们优化Pulsar的使用体验。此外，我将提供中英文两个版本，并在[gitbook](https://tumbleds-library.gitbook.io/thetumbleds-library)上进行更新，同时将内容同步至[github](https://github.com/thetumbled/Practical-Optimization-of-Apache-Pulsar)。

如果您有兴趣探索更多主题，欢迎留言告知，或者通过邮件联系我，我会考虑将新主题纳入书中。因此，本书不仅限于现有的10章内容，新的主题也可能源自您的建议。



&#x20;

## &#x20;章节难度

尽管我尽量尝试以通俗易懂的方式介绍相关内容，但对于 Pulsar 的初学者而言，部分文章内容仍可能显得晦涩难懂。故而，我在此对各章节的难度进行了简易的评估标记，划分为难与易两个等级，以便读者能够便捷地跳转到自己感兴趣的内容。

* 易

[第一章 负载均衡算法--入门](di-yi-zhang-fu-zai-jun-heng-suan-fa-ru-men/README)

[第四章 负载均衡算法--实验验证](di-si-zhang-fu-zai-jun-heng-suan-fa-shi-yan-yan-zheng/)

[第六章 负载均衡算法--使用指南](di-liu-zhang-fu-zai-jun-heng-shi-yong-zhi-nan/)



* 难

[第二章 负载均衡算法--卸载策略LoadSheddingStrategy](di-er-zhang-fu-zai-jun-heng-suan-fa-xie-zai-ce-le-loadsheddingstrategy/)

[第三章 负载均衡算法--放置策略PlacementStrategy](di-san-zhang-fu-zai-jun-heng-suan-fa-fang-zhi-ce-le-placementstrategy/)

[第五章 负载均衡算法--AvgShedder ](di-wu-zhang-fu-zai-jun-heng-suan-fa-avgshedder/)

&#x20;

&#x20;

&#x20;

&#x20;

&#x20;

&#x20;

&#x20;

&#x20;

&#x20;

&#x20;
