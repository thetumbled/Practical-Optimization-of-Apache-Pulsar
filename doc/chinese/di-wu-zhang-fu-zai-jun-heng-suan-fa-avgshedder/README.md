# 第五章 负载均衡算法--AvgShedder

在前几章中，我们对 shedding 算法与 placement 算法展开了详尽剖析，阐释了它们的运行机制，探讨了二者的搭配方式，并得出了最为适配的两种组合。然而，这两种组合仍存在显著缺陷。这些缺陷促使我着手设计开发一款全新算法。在本章，我们将深入探究如何设计新的负载均衡算法。

设计文档在如下PR里包含了：

{% @github-files/github-code-block %}

实现源码可以在如下PR里看到：

{% @github-files/github-code-block %}
