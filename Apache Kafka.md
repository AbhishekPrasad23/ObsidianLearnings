Apache Kafka Explained (At the high level)  
  
From Netflix to Uber to LinkedIn, Apache Kafka is the backbone of their real-time data infrastructure. It is a distributed event streaming platform built to handle massive streams of data with low latency and high reliability.  
  
- Producers: Applications (web, mobile, IoT, logs, etc.) that publish messages to Kafka topics.  
  
- Topics & Partitions: Messages are organized into topics, which are split into partitions for scalability and parallelism.  
  
- Broker Cluster: Kafka brokers store and serve partitioned data. Multiple brokers form a cluster to ensure reliability and fault tolerance.  
  
- KRaft (Controller Quorum): Coordinates cluster metadata and leader elections — ensuring the cluster remains consistent.  
  
- Consumer Groups: Applications that subscribe to topics and consume messages.

![[Pasted image 20250928005331.png]]