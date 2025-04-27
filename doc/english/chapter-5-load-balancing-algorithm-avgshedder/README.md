# Chapter 5 Load Balancing Algorithm - AvgShedder

In the previous chapters, we have conducted an in-depth analysis of [shedding algorithms](../chapter-2-load-balancing-algorithm-principles-and-analysis-load-shedding-strategy/) and [placement algorithms](../chapter-3-load-balancing-algorithm-principles-and-analysis-placement-strategy/README), explaining their mechanisms, exploring their combinations, and identifying the [two most suitable combinations](../chapter-3-load-balancing-algorithm-principles-and-analysis-placement-strategy/3.-strategy-selection.md).

However, these two combinations still have significant defects. These flaws spurred me to design and develop a novel algorithm. In this chapter, we shall explore in depth the design of the new load-balancing algorithm `AvgShedder`.

The design document is included in the following PR:

[\[improve\] \[pip\] PIP-364: Introduce a new load balance algorithm AvgShedder](https://github.com/apache/pulsar/pull/22946)

\
The implementation source code can be seen in the following PR:\
[\[improve\]\[broker\] PIP-364: Introduce a new load balance algorithm AvgShedder](https://github.com/apache/pulsar/pull/22949)

