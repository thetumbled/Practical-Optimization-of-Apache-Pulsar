# Chapter 1 Load Balancing Algorithm - Introduction

Thanks to the architecture of Pulsar's separation of storage and computation, Pulsar can easily switch loads to different computing nodes, brokers, achieving load switching in seconds, thus laying the foundation for the implementation of the load balancing module. The load balancing module is a core component of Pulsar. When expanding new machines or reducing machines, it can automatically distribute the load across various broker machines, greatly reducing the operational burden.



Kafka is difficult to accomplish load balancing because Kafka's storage and computation are coupled. If you want to transfer the load to other nodes, you must replicate the data on the current node. This process incurs high costs and consumes valuable business traffic bandwidth. Consequently, Kafka's scaling process are troublesome, usually requiring DevOps to intervene, and often requires a significant amout of time for data replication, so that the load can be slowly transferred to the new node. However, operations like expansion are usually carried out in emergencies. This gives rise to an awkward scenario: there is an urgent need for a rapid response, yet it cannot be achieved promptly, and one can only wait anxiously.



In this chapter, we will first provide a preliminary discussion on the basic principles of Pulsar load balancing and compare the performance of different algorithms. Next, we will elaborate on the reasons for developing a new load-balancing algorithm—`AvgShedder`. Finally, we will detail the outstanding performance of `AvgShedder` in production environments and its configuration methods, guiding users on how to correctly configure and use `AvgShedder` based on their actual needs. Subsequent chapters will delve deeper into the analysis of load-balancing algorithms.

&#x20;
