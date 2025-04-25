# 第四章 负载均衡算法--实验验证

在本章中，我们将呈现一系列实验数据，用以验证上一篇文章经分析得出的结论。为降低验证工作的复杂度，我们仅针对最优的两种组合开展实验，以对前文分析加以验证：

* ThresholdShedder + LeastResourceUsageWithWeight
* UniformLoadShedder + LeastLongTermMessageRate

实验过程旨在增强前文分析所得结论的可靠性。如果你对实验细节不感兴趣，亦可直接跳过本章，转而阅读[第五章 AvgShedder](../di-wu-zhang-fu-zai-jun-heng-suan-fa-avgshedder/) 的相关内容。



