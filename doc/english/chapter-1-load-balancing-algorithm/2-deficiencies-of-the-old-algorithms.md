# 2 Deficiencies of the Old Algorithms

As previously introduced, apart from the new load balancing algorithm `AvgShedder`, there are three shedding strategies: `ThresholdShedder`, `OverloadShedder`, and `UniformLoadShedder`, and two placement strategies: `LeastLongTermMessageRate` and `LeastResourceUsageWithWeight`. Therefore, users can combine these strategies in `3*2=6` different ways. However, in practice, only two combinations are recommended:

* ThresholdShedder + LeastResourceUsageWithWeight
* UniformLoadShedder + LeastLongTermMessageRate

The default configuration is `ThresholdShedder + LeastLongTermMessageRate`, which is a very poor combination. Users are strongly advised to change the default configuration.

Moreover, each of the recommended combinations has its own advantages and disadvantages. Based on multiple dimensions such as adaptability, stability, correctness, and load balancing speed, I have scored them. I will elaborate on the scoring process for different combinations in the following chapters. Below is the corresponding scoring table:

<table data-header-hidden><thead><tr><th valign="top"></th><th valign="top"></th><th valign="top"></th><th valign="top"></th><th valign="top"></th><th valign="top"></th></tr></thead><tbody><tr><td valign="top">Strategy</td><td valign="top">Adaptability to Heterogeneous Environments (Adaptability)</td><td valign="top">Adaptability to Load Fluctuations (Stability)</td><td valign="top">Over Placement (Correctness)</td><td valign="top">Over Unloading (Correctness)</td><td valign="top">Speed</td></tr><tr><td valign="top">ThresholdShedder + LeastResourceUsageWithWeight</td><td valign="top">Fair</td><td valign="top">Good</td><td valign="top"><strong>Poor</strong></td><td valign="top"><strong>Poor</strong></td><td valign="top">Fair</td></tr><tr><td valign="top">UniformLoadShedder + LeastLongTermMessageRate</td><td valign="top"><strong>Poor</strong></td><td valign="top"><strong>Poor</strong></td><td valign="top">Good</td><td valign="top">Good</td><td valign="top">Fair</td></tr></tbody></table>

&#x20;It can be seen that:

* The combination of `ThresholdShedder + LeastResourceUsageWithWeight` performs poorly in terms of load balancing correctness, often leading to incorrect load balancing decisions. This also results in multiple iterations being required to eventually stabilize, hence the average load balancing speed.
* The combination of `UniformLoadShedder + LeastLongTermMessageRate` performs poorly in terms of adaptability and stability. It cannot adapt to heterogeneous environments and often triggers incorrect load balancing decisions when facing load fluctuations.



## **2.1 Adaptability**

Adaptability refers to the inability of the old algorithms to adapt to heterogeneous environments, where higher-performance machines cannot handle more traffic load compared to lower-performance machines.

There is no doubt that we would expect higher-performance machines to handle more load. However, the old algorithm combin`ation of UniformLoadShedder + LeastLongTermMessageRate` sorts broker based on traffic throughput. As a result, traffic is offloaded from high-performance machines with higher throughput to lower-performance ones with lower throughput, causing the CPU usage of the lower-performance machines to be too high, while the CPU usage of the high-performance machines is too low.



Consider a specific example:

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

When looking solely at the Byte In/Out monitoring, we might think this is a perfect load balancing execution. The traffic on four machines is 28MB/s, 46MB/s, 55MB/s, and 66MB/s respectively. After load balancing, the traffic on all four machines hovers around 40MB/s.

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

However, when we look at the CPU usage, we find that the CPU usage of the blue machine is much lower than that of the other machines. The CPU usage of the blue machine is only 17%, while the CPU usage of the other machines reaches 50%. This is because the blue machine's performance far exceeds that of the other machines, with more than double the number of CPU cores. Therefore, under the same load, the resource usage of the blue machine will be significantly lower than that of the other machines. However, we would prefer the blue machine to handle more load. Thus, this is not a correct load balancing operation.



## **2.2 Stability**

The stability issue refers to the old algorithms frequently switching loads when dealing with short-term load fluctuations. This leads to frequent disconnections of user connections and unstable traffic.

Sometimes users may start a task with a large data throughput but a very short running time. For example, processing hundreds of megabytes of data can be completed in just one or two minutes. This can cause significant load pressure on certain machines, leading to an uneven distribution of load in the cluster. In such cases, load balancing operations should be avoided. Once load balancing is executed, when the user's task ends in a few minutes, the cluster will return to an unbalanced state. This will trigger frequent and unnecessary load balancing adjustments, severely affecting the user experience.

Consider the following example: A simulated traffic is started, with the traffic consumption stopping every 1 minute and resuming after waiting for 1 minute. The old algorithm will keep executing load switching.

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

The bundle offloading count indicator keeps rising, indicating that bundle unload is constantly triggered, and the load balancing algorithm is frequently activated.



## **2.3 Correctness**

Correctness refers to the old algorithms potentially making wrong decisions, leading to machines being overloaded or underloaded. Specifically, this includes two issues: over placement and over unloading.

* **Over placement issue**: For example, there are currently 10 machines with a CPU usage of 50% and 1 machine with a CPU usage of 30%. Given that the cluster pressure is low, a plan to downsize by reducing 3 machines is made. However, the load of these 3 downsized machines is likely to be entirely transferred to the machine with an original CPU usage of 30%, causing its CPU usage to soar from 30% to 80%.
* **Over unloading issue**: For example, Machine 1 has a CPU usage of 80%, while Machine 2 has a CPU usage of only 20%. Ideally, if we evenly distribute the traffic between the two machines, their CPU usage would both become 50%, which is the optimal state. However, there is a **historical average scoring algorithm** in the old algorithm that prevents the actual load of the machine from being promptly reflected in the score ranking. Therefore, the system will continue to judge Machine 1 as being in a high-load state and Machine 2 as being in a low-load state, and continue to transfer the traffic from Machine 1 to Machine 2. The final result is that Machine 1 becomes low-load, while Machine 2 becomes high-load.

&#x20;

Consider the following example using the combination of `ThresholdShedder + LeastResourceUsageWithWeight` algorithm:

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

It can be seen that the yellow machine frequently switches between high-load and low-load states. Its CPU usage drops from 80% to 40%, and then rises back to 80%. In this process, an over unloading issue is first triggered, followed by an over placement issue.











