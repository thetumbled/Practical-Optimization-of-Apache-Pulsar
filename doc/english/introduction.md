# Introduction

## Author Biography

Feng Wenzhi, an Apache Pulsar Committer, a senior engineer in the big data storage field at BIGO, graduated from Huazhong University of Science and Technology.



## Motivation

Apache Pulsar is a distributed publish-subscribe messaging system, with a positioning similar to Kafka. However, it surpasses Kafka in several aspects, such as implementing the **separation of storage and computation** (supporting load balancing, second-level load switching, elastic scaling), supporting up to **millions of Topic partitions**, and read-write separation. By migrating from Kafka to Pulsar, we leveraged the advantages of high throughput, low latency, and high reliability provided by Apache Pulsar, significantly enhancing our message processing capabilities, while reducing the operational costs of message queues, and saving nearly 50% on hardware expenses.

However, Pulsar is a complex distributed system with nearly 400 configuration items. The underlying BookKeeper distributed storage system it uses also contains over 200 configuration items.

For ordinary users, it is almost impossible to choose the most suitable configuration based on their specific needs. Even as a core developer of Pulsar, I would not dare to modify configuration values based solely on the configuration documentation. Typically, it is necessary to delve into the code and thoroughly understand the purpose of the configuration before making any changes.

In reality, many default settings often leave much to be desired. Taking a key component of Pulsar as an example—the load balancer, which is a significant advantage of Pulsar over Kafka. The default load balancing algorithm it employs, a combination of ThresholdShedder and LeastLongTermMessageRate, is a poor choice. After my analysis and testing, this configuration does not yield ideal results. Nevertheless, for reasons of compatibility and stability, many default values of configurations are not advisable to change. Therefore, the maintainers of Pulsar urgently need to master how to optimize the operation of Pulsar services to provide superior service.

During my involvement in the development and maintenance of Apache Pulsar, I have accumulated extensive experience and research achievements. These achievements have not only contributed to the community in the form of code but have also been integrated into the community's main repository, such as the new load balancing algorithm AvgShedder and the delay queue TimeBucket algorithm. I have also been fortunate enough to become a Committer for Apache Pulsar. However, to maximize the utility of these achievements, user understanding and correct configuration are crucial. Therefore, this book aims to help users master this knowledge.



## Plan

I have written 10 chapters in Chinese, totaling nearly 200 pages, covering topics such as Pulsar load balancing algorithms, delay queues (including both production and consumption ends), and BookKeeper load balancing. Previously, these materials were distributed to some participants at the Pulsar Meetup through an application process.

Now, I plan to reorganize these materials to make them more accessible and easier to understand, in order to help users and developers of Pulsar optimize their experience with Pulsar. In addition, I will provide both Chinese and English versions and update them on [GitBook](https://tumbleds-library.gitbook.io/thetumbleds-library), while also synchronizing the content to [GitHub](https://github.com/thetumbled/Practical-Optimization-of-Apache-Pulsar).



If you are interested in exploring more topics, feel free to leave a message to let me know, and I will consider incorporating new topics into the book. Therefore, this book is not limited to the existing 10 chapters, and new topics may also stem from your suggestions.

I will announce the release of my new article through the X platform, and I welcome everyone to follow my [X account ](https://x.com/thetumbledd)to receive the latest content in a timely manner.



&#x20;

















