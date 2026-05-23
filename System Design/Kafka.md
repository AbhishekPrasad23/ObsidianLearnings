

| 1   | [[#Kafka wrong payload problem]] |
| --- | -------------------------------- |
|     |                                  |

Apache Kafka Explained (At the high level)  
  
From Netflix to Uber to LinkedIn, Apache Kafka is the backbone of their real-time data infrastructure. It is a distributed event streaming platform built to handle massive streams of data with low latency and high reliability.  
  
- Producers: Applications (web, mobile, IoT, logs, etc.) that publish messages to Kafka topics.  
  
- Topics & Partitions: Messages are organized into topics, which are split into partitions for scalability and parallelism.  
  
- Broker Cluster: Kafka brokers store and serve partitioned data. Multiple brokers form a cluster to ensure reliability and fault tolerance.  
  
- KRaft (Controller Quorum): Coordinates cluster metadata and leader elections — ensuring the cluster remains consistent.  
  
- Consumer Groups: Applications that subscribe to topics and consume messages.

![[Pasted image 20250928005331.png]]


Kafka 101  
  
1 - What is Kafka?  
Kafka is a distributed event store and a streaming platform. It began as an internal project at LinkedIn and now powers some of the largest data pipelines in the world in orgs like Netflix, Uber, etc.  
  
2 - Kafka Messages  
Message is the basic unit of data in Kafka. It consists of headers, key, and value.  
  
3 - Kafka Topics and Partitions  
Every message goes to a particular Topic. Topics have multiple partitions.  
  
4 - Advantages of Kafka  
Kafka can handle multiple producers and consumers, while providing disk-based data retention and high scalability.  
  
5 - Kafka Producer  
Producers in Kafka create new messages, batch them, and send them to a Kafka topic.  
  
6 - Kafka Consumer  
Kafka consumers work together as a consumer group to read messages from the broker.  
  
7 - Kafka Cluster  
A Kafka cluster consists of several brokers where each partition is replicated across multiple brokers to provide high availability and redundancy.  
  
8 - Use Cases of Kafka  
Kafka can be used for log analysis, data streaming, change data capture, and system monitoring.  
  
---
# Kafka wrong payload problem
## The Problem With Perfection

Let us talk about the difference between a standard REST API and Apache Kafka.

In a standard REST API, if another service sends a bad JSON payload to your server, what do you do? You return a “400 Bad Request” status code, and you move on with your life. The bad data is rejected at the front door.

What are you thinking right now? You are probably thinking, “Does Kafka not do the same thing?”

The answer is no. Kafka is built differently, and it is built for perfection. Kafka guarantees at-least-once delivery. When a consumer reads a message, it must process it successfully and then commit the offset. Committing the offset is the consumer telling Kafka, “I am done with this message, give me the next one.”

But what happens if the payload is completely malformed and the consumer cannot deserialize it?

The consumer throws an exception and crashes. Because the offset was never committed, the Spring Boot framework automatically restarts the consumer to try again. The consumer wakes up, looks at the queue, reads the exact same bad message, fails to deserialize it, and crashes again.

This loop happens hundreds of times a second. We call this the Poison Pill.

## A Real Production Nightmare

I am going to share a real example from an e-commerce project.

Imagine you have an Order Service that produces messages to a Kafka topic whenever a customer buys something. You also have an Inventory Service that consumes these messages to deduct stock from the warehouse.

Everything is running perfectly for months. Then, a junior developer introduces a bug in the Order Service. Instead of sending the product quantity as a number, it sends it as a word.

The payload looks like this: **“quantity”: “two”** instead of **"quantity": 2**.

The message hits Kafka. The Inventory Service picks it up. The internal JSON deserializer tries to map the word “two” to an Integer field in your Java class. It instantly throws a DeserializationException.

The Inventory consumer dies. It restarts. It reads the same message. It dies again.

Within minutes, your application logs are filled with millions of identical error traces. Your servers run out of disk space. Because the Inventory Service is stuck in an infinite crash loop, no inventory is updated. Soon, the Shipping Service stops working because it is waiting for the inventory confirmation.

One single unparseable message just paralyzed your entire microservice ecosystem.

## The Trap of Try-Catch

Right now, you might be thinking, “I will just put a try-catch block around my code!”

I completely understand why you would think that. It is the logical first step for any developer. But it will not work.

Why? Because the error happens before your code even executes. The deserialization happens in the background by the Kafka framework before the message is ever passed to your listener method. Your try-catch block will never see the error.

### The Architecture Solution: ErrorHandlingDeserializer

To fix this, we need to catch the error at the deserialization level. Spring Kafka provides a brilliant built-in tool for this exact scenario called the ErrorHandlingDeserializer.

Instead of crashing the application, this special deserializer catches the bad payload, wraps the error safely, and passes a null value to your listener along with the error details. This allows your application to stay alive.

Here is the production-level configuration you need in your configuration file. It is very simple to understand:

spring:  
  kafka:  
    consumer:  
      value-deserializer: org.springframework.kafka.support.serializer.ErrorHandlingDeserializer  
      properties:  
        spring.deserializer.value.delegate.class: org.springframework.kafka.support.serializer.JsonDeserializer

### The Next Step: The Dead Letter Queue

So, the application did not crash. Excellent. But we still have a bad message that we could not process. We cannot just delete it. It might be a real customer order that needs attention!

This is where the Dead Letter Queue comes in. A Dead Letter Queue is like a quarantine box. It is a completely separate Kafka topic where we send all the messages that we cannot process. This way, the main queue is cleared, the consumer can move on to the next good message, and your engineering team can inspect the bad messages in the Dead Letter Queue later.

Here is how you write a production-ready Dead Letter Queue configuration in Spring Boot using Java:

import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
import org.springframework.kafka.core.KafkaTemplate;  
import org.springframework.kafka.listener.DeadLetterPublishingRecoverer;  
import org.springframework.kafka.listener.DefaultErrorHandler;  
import org.springframework.util.backoff.FixedBackOff;  
  
@Configuration  
public class KafkaConfig {  
  
    @Bean  
    public DefaultErrorHandler errorHandler(KafkaTemplate<Object, Object> template) {  
        // This object takes the bad message and publishes it to a new quarantine topic  
        DeadLetterPublishingRecoverer recoverer = new DeadLetterPublishingRecoverer(template);  
  
        // We tell Kafka to retry processing the message 2 times, waiting 1000 milliseconds between retries  
        FixedBackOff backOff = new FixedBackOff(1000L, 2);  
  
        // Return the complete error handler  
        return new DefaultErrorHandler(recoverer, backOff);  
    }  
}

With this code, if a message fails, Kafka will wait one second and try again. It will do this two times. If it still fails, it safely sends the message to the Dead Letter Queue and moves forward. Your system never goes down.

### Never Trust the Producer

The biggest lesson here is about trust. In a distributed system, you should never blindly trust the data coming from other services. Even if the same team wrote both services, mistakes happen.

Always design your consumers to be defensive. Handle errors gracefully. Use Dead Letter Queues. And if possible, use strict schema validation tools which prevent bad data from ever entering Kafka in the first place.
