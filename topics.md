The key abstraction in Kafka is the Topic. At the implementation level, a Kafka topic is just sharded write-ahead log. Topics are partitioned and each partition is represented by an ordered, immutable sequence of messages.

Processes that publish messages to a Kafka topic are referred to as Producers. Producers publish data to a topic by choosing the appropriate partition within the topic.

Processes that subscribe to topics and process the published messages are called Consumers.

Kafka is run as a cluster comprised of one or more servers each of which is called a Broker. Topics are created within the context of broker processes.	topic is divided into parttions

	A topic can be seen as a category, stream or feed to which a producer can publish messages. Consumers can read the published messages from the specified topics

	![](/assets/import.png)

