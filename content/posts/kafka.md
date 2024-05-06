---
title: 'Apache Kafka Crash Course'
date: 2024-04-27T09:50:09-04:00
draft: false
tags:
  - kafka
categories: notes
keywords:
  - apache
  - kafka
---

## Kafka - Distributed Stream Processing System

Kafka is an open-source distributed streaming platform. In simpler terms, it's a system that excels at handling large amounts of data that is constantly being generated. This data, often called streaming data, comes from various sources and needs to be processed quickly and efficiently.

- Push Poll Model

### Kafka Components

#### Kafka Broker

Default port `9092`

- **Producer**: Producer produces content
- **Consumer**: Consumer consumes the content

The producer/Consumer creates a TCP connection to the broker, which is bi-directional. That means both producer and broker can send and receive data from each other.

- **Topics**: Logical partitions where the consumer writes content to. It is mandatory for the consumer to specify the topic name for the content to be written, where as consumer need to specify from which topic to read from.

![borker](posts/kafka/kaf-broker.png)

- Every message is assigned a position, and it is fast addressable.
- The consumer is pooling for more messages, unlike rabbit-mq.

**What to do if the topic grows large?**

- Sharding in case of the databases if the table grows large.
- Kafka borrows the same concept, called **partitions**.
- The producer now needs to figure out not only which topic to publish data to but also which partition to publish to.

##### Queue vs. PubSub

- Queue: Message published once, consumed once.
- PubSub: Message published once, consumed many times.

**Kafka asked: How can we do both?**
Answer: Consumer Group

#### Consumer Group

Invented to do parallel processing on partitions. Consumer groups remove the awareness of partitions from the consumer.

- To act like a queue, put all your consumers in one group.
- To act like a pub/sub, put each consumer in a unique group.
- We get parallel processing for free.

#### Distributed System

Spin up another broker, Kafka then marks Leader and follower.

It is possible that one broker be leader for one partition and a follower for another partition.

![kafkads](posts/kafka/kafka-ds.png)

**But where is the leader information store?**

Meet **ZooKeeper**

### Example

#### Spin up Kafka cluster

```bash
# run zookeeper
docker run --name zookeeper  -p 2181:2181 -d zookeeper

# run kafka broker - change the hostname
docker run -p 9092:9092 --name kafka  -e KAFKA_ZOOKEEPER_CONNECT=MacBook-Air.local:2181 -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://MacBook-Air.local:9092 -e KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1 -d confluentinc/cp-kafka
```

#### Write node js Producer/

**Creating a Topic**

```javascript
const { Kafka } = require('kafkajs');

async function run() {
	try {
		const kafka = new Kafka({
			clientId: 'myapp',
			brokers: ['MacBook-Air.local:9092'],
		});

		const admin = kafka.admin();
		console.log('connecting...');
		await admin.connect();
		console.log('connected.');
		// A-M, N-Z
		await admin.createTopics({
			topics: [
				{
					topic: 'users',
					numPartitions: 2,
				},
			],
		});
		console.log('topic created successfully');
		await admin.disconnect();
	} catch (ex) {
		console.error(`something went wrong ${ex}`);
	} finally {
		process.exit(0);
	}
}

run();
```

**Creating a Producer**

```javascript
const { Kafka } = require('kafkajs');

const msg = process.argv[2];

async function run() {
	try {
		const kafka = new Kafka({
			clientId: 'myapp',
			brokers: ['MacBook-Air.local:9092'],
		});

		const producer = kafka.producer();
		console.log('connecting...');
		await producer.connect();
		console.log('connected.');
		// A-M 0, N-Z 1
		const partition = msg[0] < 'N' ? 0 : 1;
		const result = await producer.send({
			topic: 'users',
			messages: [
				{
					value: msg,
					partition: partition,
				},
			],
		});
		console.log(`send successfully: ${JSON.stringify(result)}`);
		await producer.disconnect();
	} catch (ex) {
		console.error(`something went wrong ${ex}`);
	} finally {
		process.exit(0);
	}
}

run();
```

**Creating a Consumer**

```javascript
const { Kafka } = require('kafkajs');

async function run() {
	try {
		const kafka = new Kafka({
			clientId: 'myapp',
			brokers: ['MacBook-Air.local:9092'],
		});

		const consumer = kafka.consumer({
			groupId: 'group-1',
		});
		console.log('connecting...');
		await consumer.connect();
		console.log('connected.');
		await consumer.subscribe({
			topic: 'users',
			fromBeginning: true,
		});

		await consumer.run({
			eachMessage: async (result) => {
				console.log(
					`Received message ${result.message.value} on partition ${result.partition}`
				);
			},
		});
	} catch (ex) {
		console.error(`something went wrong ${ex}`);
	} finally {
	}
}

run();
```

### Pros & Cons of Kafka

#### Pros

- Append only Commit log
- Performance: Reading and Writing is fast.
- Distributed
- Long Polling
- Event driver, Pub sub, and Queue
- Scaling
- Parallel Processing

#### Cons

- Zookeeper
- Producer-explicit partition can lead to problems
- It is complex to install, configure, and manage
