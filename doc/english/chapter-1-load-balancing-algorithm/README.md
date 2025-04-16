# Chapter 1 Load Balancing Algorithm

Thanks to the architecture of Pulsar's separation of storage and computing, Pulsar can easily switch loads to different computing nodes, brokers, achieving load switching in seconds, thus laying the foundation for the implementation of the load balancing module. The load balancing module is a core foundational component of Pulsar. When expanding new machines or reducing machines, it can automatically distribute the load across various broker machines, greatly reducing the operational burden.



Kafka is difficult to achieve load balancing because Kafka's storage and computing are coupled. If you want to transfer the load to other nodes, you must replicate the storage data on the current node. This process is costly and occupies valuable business traffic bandwidth. Therefore, Kafka's scaling steps are troublesome, usually requiring the operation and maintenance team to intervene, and often requires a lot of time for data copying, so that the load can be slowly migrated to the new node. However, operations like expansion are usually carried out in emergencies, which creates an awkward situation: a urgent need for rapid response but unable to achieve it quickly, can only wait anxiously.



In this chapter, we will first provide a preliminary discussion on the basic principles of Pulsar load balancing and compare the performance of different algorithms. Next, we will elaborate on the reasons for developing a new load balancing algorithm—AvgShedder. Finally, we will detail the outstanding performance of AvgShedder in production environments and its configuration methods, guiding users on how to correctly configure and use AvgShedder based on their actual needs. Subsequent chapters will delve deeper into the analysis of load balancing algorithms.

&#x20;
