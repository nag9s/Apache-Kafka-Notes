Kafka

	Kafka is an



Open source

Distributed

Partitioned

Replicated

Commit-log-based

publish-subscribe messaging system.



	A filesystem or database commit log is designed to provide a durable record of all transactions so that they can be replayed to consistently build the state of a system



	“distributed commit log”

	data within Kafka is stored durably, in order, and can be read deterministically. In addition, the data can be distributed within the system to provide additional protections against failures, as well as significant opportunities for scaling performance.

	The key abstraction in Kafka is the Topic. At the implementation level, a Kafka topic is just sharded write-ahead log. Topics are partitioned and each partition is represented by an ordered, immutable sequence of messages.

Processes that publish messages to a Kafka topic are referred to as Producers. Producers publish data to a topic by choosing the appropriate partition within the topic.

Processes that subscribe to topics and process the published messages are called Consumers.

Kafka is run as a cluster comprised of one or more servers each of which is called a Broker. Topics are created within the context of broker processes.



	Terminology

		Messages and Batches

			The unit of data within Kafka is called a message

			A message is simply an array of bytes, as far as Kafka is concerned, so the data contained within it does not have a specific format or meaning to Kafka

			Messages can have an optional bit of metadata which is referred to as a key.

			The key is also a byte array, and as with the message, has no specific meaning to Kafka

			Keys are used when messages are to be written to partitions in a more controlled manner. The simplest such scheme is to treat partitions as a hash ring, and assure that messages with the same key are always written to the same partition.

			messages are written into Kafka in batches. 

				A batch is just a collection of messages, all of which are being produced to the same topic and partition.

				n individual round trip across the network for each message would result in excessive overhead, and collecting messages together into a batch reduces this

					presents a tradeoff between latency and throughput:

					he larger the batches, the more messages that can be handled per unit of time, but the longer it takes an individual message to propagate. Batches are also typically compressed, which provides for more efficient data transfer and storage at the cost of some processing power.

		Schemas

		Topics

			topic is divided into parttions

			A topic can be seen as a category, stream or feed to which a producer can publish messages. Consumers can read the published messages from the specified topics

			

		parttions

			A partition is nothing more than an immutable, ordered sequence of messages. As messages are published the commit log will be updated. Each message in a partition is assigned a sequential ID number which uniquely identifies each message within the partition. This sequence number is often referred to as the message offset.

			 The performance of Kafka is relatively constant with respect to partition sizes, so retaining a lot of data will not negatively affect overall performance.

			The only metadata retained by the system on a per-consumer basis is the position of the consumer in the log, called the “offset”. This offset is controlled by the consumers; normally the consumer will advance its offset linearly as it reads messages, but since the consumer is in control, it can consume the messages in any preferred order. For example, a consumer can reset to an older offset it it wishes to re-process a batch of messages.

			This also implies that Kafka consumers are very lightweight, and have very little impact on the cluster’s overall performance. Since each consumer maintains its own offset, it has no ability to impact other consumers.

			two distinct purposes:

				The allow a topic to scale beyond a size that will fit on a single server. Each individual partition must fit on the servers that host it, but a topic may have many partitions, so it can handle an arbitrary amount of data.

				Partitions act as the primary unit of parallelism in Kafka. They allow  a single consumer to concurrently read messages in multiple concurrent threads. I will elaborate on this subject in detail in a later post.

				

			Partition Distribution/ Replication / Leaders

				The partitions of a topic are distributed over the servers in the Kafka clusters with each server handling data and request for a share of the partitions. Each partition is replicated across a configurable number of servers for fault tolerance.

				Each partition has one server which acts as the “leader” and zero on more servers which act as “followers”. The leader handles all read and write requests for the partition while the followers passively replicate the leader. If the leader fails, one of the followers will automatically become the new leader.  A follower acts as a normal consumer, it pulls messages and updates it own log. When a follower rejoins a partition it first re-syncs all messages, after which it can become fully online.

				The leader uses the Zookeeper heartbeat mechanism to indicate that it is alive and well. Each server acts as a leader some of its partitions and a follower for others so load is well balanced within the cluster.

				 Each server acts as a leader for some of its partitions and a follower for others so load is well balanced within the cluster . See the diagram. 

					You can also inspect the local filesystem to see how the --describe output above matches actual files. By default Kafka persists topics as “log files” \(Kafka terminology\) in the log.dir directory.

						

						

					

						In this example output the first broker \(with broker.id = 1\) happens to be the designated leader for partition 0 at the moment. Similarly, the second and third brokers are the leaders for partitions 1 and 2, respectively.

				

					When a topic is created in Kafka 0.8, Kafka determines how each replica of a partition is mapped to a broker. In general Kafka tries to spread the replicas across all brokers \(source\). Messages are first sent to the first replica of a partition \(i.e. to the current “leader” broker of that partition\) before they are replicated to the remaining brokers. Message producers may choose from different strategies for sending messages \(e.g. synchronous mode, asynchronous mode\). Producers discover the available brokers in a cluster and the number of partitions on each, by registering watchers in ZooKeeper.

					Having more partitions increases I/O parallelism for writes and also increases the degree of parallelism for consumers \(since partition is the unit for distributing data to consumers\).                    On the other hand, more partitions adds some overhead: \(a\) there will be more files and thus more open file handlers; \(b\) there are more offsets to be checkpointed by consumers which can increase the load of ZooKeeper. So, you want to balance these tradeoffs.

				

		Producers

			Producers public their data to the topics of their choice.

			The producer is responsible for choosing which message to assign to which partition within the topic. This can be done in two different ways:

				Publishers can publish to different topics in a round-robin fashion, thereby automatically balancing the load.

				Public can dynamically select the topics by means of a “semantic partition function”, which is typically based upon a key contained in the message.

			The producer connects to any of the alive nodes \( using BOOTSTRAP\_SERVERS\_CONFIG property, which specifies list of brokeers \) and requests metadata about the leaders for the partitions of a topic. This allows the producer to put the message directly to the lead broker for the partition. Remember, the leader is the node responsible for all reads and writes for a given topic partition.

		Consumers



			In messaging we have typically recognized two core models:                              Queuing

Publish-subscribe



				In a queuing model, a pool of consumers can read from a queue, but a single message only goes to exactly one consumer

					

				In a publish-subscribe model the message is broadcast to all subscribing consumers

					

				With the concept of the Consumer Group Kafka offers a single consumer abstraction that generalizes queuing and publish-subscribe.

					In this model, consumers label themselves with a consumer group name, and each message published to the topic is delivered to one consumer instance within each subscribing consumer group.

					Consumer instances can be in separate processes on separate machines, as is shown in the figure below:

						

					If all the consumer instances have the same consumer group, then this model works just like a traditional queue balancing log over the consumers.

					On the other hand, if all the consumer instances have different consumer groups, then the model works like traditional publish-subscribe and all messages are broadcast to all consumers.

					In most scenarios, a topic will have a small number of consumer groups, one for each logical subscriber. Each group is composed of many consumer instances for scalability and fault tolerance. Effectively, this enforces publish-subscribe semantics where the subscriber is a cluster of consumers instead of a single process.

			Two Types of API's

				high-level

					supported consumer groups and handled failover,

				simple consumer 

					provided full control, but required users to manage failover and error handling themselves.

				From 0.9, a new consumer API was introduced replacing the above high level and simple consumer 

					While the old consumer depended on Zookeeper for group management, the new consumer uses a group coordination protocol built into Kafka itself. 

	Zookeeper

		While most readers will know Zookeeper from the larger Hadoop ecosystem, it is also used in stand-alone Kafka deployments.

		Zookeeper servers as the coordination interface between the Kafka brokers and consumers. At the core, Zookeeper is an open source, High Performance coordination service for distributed applications. Within the Apache Hadoop ecosystem it acts as a centralized service for tasks such as:

			1. Configuration Management. Zookeeper enables the cluster member nodes to “bootstrap” their configuration from a central source.

			2. Group Membership. It will manage the cluster’s node status in real time and coordinate nodes joining or leaving the cluster.

			3. Naming Services and Registry. Zookeeper maintains a centralized and highly available registry where it maintain the central naming services.

			4. Locking and Synchronization. Zookeeper will maintain any shared locks, barriers etc…

			5. Leader Switch-Over.  Zookeeper maintains the shared state between partition leaders and followers, ensuring that a leader transition is seamless.

		An example of how Kafka leverages Zookeeper is shown in the figure below:

			

				In this case the producer will first call onto Zookeeper to get the Kafka Broker address. . r.

				As the Producers start streaming in messages, the broker maintains it state in Zookeeper.

				 The consumers also track their individual message offsets through Zookeeper,This is actually done by consumer group instead of individual consumers, more about that late

	HANDLING LARGE MESSAGES IN KAFKA http://ingest.tips/2015/01/21/handling-large-messages-kafka/

		The best way to send large messages is not to send them at all. If shared storage is available \(NAS, HDFS, S3\), placing large files on the shared storage and using Kafka just to send a message about the file’s location is a much better use of Kafka.

		The second best way to send large messages is to slice and dice them. Use the producing client to split the message into small 10K portions, use partition key to make sure all the portions will be sent to the same Kafka partition \(so the order will be preserved\) and have the consuming client sew them back up into a large message.

		Kafka producer can be used to compress messages. If the original message is XML, there’s a good chance that the compressed message will not be very large at all. Use compression.codec and compressed.topics configuration parameters in the producer to enable compression. GZip and Snappy are both supported.

	Infrastrucure

		With 3 brokers - 10 K msg per sec . With 1 broker  - 1 million per sec in some cases.

	Guarantees http://www.benniehaelen.com/author/bennie-haelenhoneywell-com/ https://kafka.apache.org/documentation

		The Kafka messaging system provides the following guarantees:

		Messages sent by a producer to a particular topic partition will be appended in the order that they were sent. If a message Msg1 is sent by the same producer as message Msg2, and Msg1 is sent first, then Msg1 will have a lower offset that Msg2 and appear earlier in the log.

			Kafka provides order guarantee of messages in a partition. If message B was written after message A, using the same producer into the same partition, then Kafka guarantees that the offset of message B will be higher than message A, and that consumers will read message B after message A.

		A consumer instance sees messages in the order they are stored in the log.

		For a topic with replication factor N, Kafka will tolerate up to N-1 server failures without losing any messages committed to the log.

		Produced messages are considered “committed” when they were written to the partition on all its in-sync replicas \(but not necessarily flushed to disk\). Producers can choose to receive acknowledgement on messages sent when the message was fully committed, when it was written to the leader or when it was sent over the network.

		Messages that are committed, will not be lost as long as at least one replica remains alive.

		Consumers can only read messages that are committed.

	Use Cases

		Messaging. Kafka tends to work very well as a replacement for a more traditional message broker. In comparison to most messaging systems Kafka has better throughput, built-in partitioning, replication and inherent fault-tolerance which makes it a good fit for large scale message processing applications.

		IOT Application Analytics. Kafka is a good fit as a message-ingestion point for an IOT Analytics sub-system. Telemetry events are routed to Kafka topics and are consumed downstream by a real-time analytics engine such as Apache Storm. Within this blog series we will perform a deep dive into this topic.

		Log Aggregation. A number of applications use Kafka as a replacement for a log aggregation solution. Log aggregation typically collects physical log files from remote servers, and centralizes them in one location so they can be processed. Kafka provides  a clean abstraction of log data as a stream of messages instead of a traditional file. This enables lower-latency processing and easier support for multiple data sources and distributed data consumption. A Kafka-based system tends to have  lower latency and stronger delivery guarantees.

		Metrics. Kafka is often used for operational monitoring data. This involves aggregating statistics from distributed applications to produce centralized feeds of operational data. Indeed, Kafka is a good fit for collecting “Telemetry about a system”, where log data from different instances of the application is aggregated into a HDFS-based back end.

	Version Incompatibles

		The major limitation is that Kafka Version 0.7.x can't just be replaced by Version

0.8, as it is not backward compatible. If the existing Kafka cluster is based on 0.7.x,

a migration tool is provided for migrating the data from the Kafka 0.7.x-based

cluster to the 0.8-based cluster.



		Kafka starting 0.8.2.x exposed a new set of API's to work with kafka, older being Producer which works with KeyedMessage\[K,V\] where the new API is KafkaProducer with ProducerRecord\[K,V\]:

	API

		Kafka includes four core apis:

			The Producer API allows applications to send streams of data to topics in the Kafka cluster.

			The Consumer API allows applications to read streams of data from topics in the Kafka cluster.

			The Streams API allows transforming streams of data from input topics to output topics.

			The Connect API allows implementing connectors that continually pull from some source system or application into Kafka or push from Kafka into some sink system or application.

			The Legacy API is a more limited legacy producer and consumer api is also included in Kafka. These old Scala APIs are deprecated and only still available for compatibility purposes. Information on them can be found here here.

		Old Producer APIs -- Kafka Definitive Guide

			In this chapter we discussed the Java producer client that is part of org.apache.kafka.clients package. At the time of writing this chapter, Apache Kafka still has two older clients written in Scala that are part of kafka.producer package and part of the core Kafka module. These producers are called SyncProducer \(which, depending on the value of acks parameter it may wait for the server to ack each message or batch of messages before sending additional messages\) and AsyncProducer \(Which batches messages in the background, sends them in a separate thread and does not provide feedback regarding success to the client\).

			Because the current producer supports both behaviors and give much more reliability and control to the developer, we will not discuss the older APIs. If you are interested in using them, please think twice and then refer to Apache Kafka documentation to learn more.

	 Message Delivery Semantics

		At most once—Messages may be lost but are never redelivered.

		At least once—Messages are never lost but may be redelivered.

		Exactly once—this is what people actually want, each message is delivered once and only once.

			what about exactly once semantics \(i.e. the thing you actually want\)? The limitation here is not actually a feature of the messaging system but rather the need to co-ordinate the consumer's position with what is actually stored as output. The classic way of achieving this would be to introduce a two-phase commit between the storage for the consumer position and the storage of the consumers output. But this can be handled more simply and generally by simply letting the consumer store its offset in the same place as its output. This is better because many of the output systems a consumer might want to write to will not support a two-phase commit. As an example of this, our Hadoop ETL that populates data in HDFS stores its offsets in HDFS with the data it reads so that it is guaranteed that either data and offsets are both updated or neither is. We follow similar patterns for many other data systems which require these stronger semantics and for which the messages do not have a primary key to allow for deduplication.

		It's worth noting that this breaks down into two problems: the durability guarantees for publishing a message and the guarantees when consuming a message.

		So effectively Kafka guarantees at-least-once delivery by default and allows the user to implement at most once delivery by disabling retries on the producer and committing its offset prior to processing a batch of messages.

		Exactly-once delivery requires co-operation with the destination storage system but Kafka provides the offset which makes implementing this straight-forward.

	General Requirments

		Is every message critical, or can we tolerate loss of messages? Are we ok with accidentally duplicating messages? Are there any strict latency or throughput requirements we need to support?

