# Summary

This chapter briefly describes the load balancing mechanism of Pulsar, covering two major strategies: **shedding strategy** and **placement strategy**, each with multiple algorithm implementations. For users who have not deeply studied these algorithms, choosing the appropriate algorithm combination may seem somewhat difficult. Specifically, there are three shedding strategies and two placement strategies, allowing users to combine them into six different strategy combinations. However, among these combinations, only two are recommended for use. Although they each have their own advantages, they also have their own weaknesses, and the default algorithm combination is even less recommended.

&#x20;

Therefore, I designed and developed a new load balancing algorithm, AvgShedder, which simultaneously implements both shedding and placement strategies and perfectly solves the problems faced by the old algorithms. Many companies have already adopted it and given me positive feedback. I hope that after reading this article, you can also experience its excellent performance.

&#x20;

&#x20;
