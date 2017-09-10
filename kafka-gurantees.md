Apache Kafka preserves order of messages within a partition.

This means that if messages were sent from the producer in a specific order, the broker will write them to a partition in this order and all consumers will read them in this order. For some use-cases, order is very important. There is a big difference between depositing 100$ in an account and later withdrawing them, and the other way around! However, some use cases are less sensitive.

At a high-level Kafka gives the following guarantees:

* Messages sent by a producer to a particular topic partition will be appended in the order they are sent. That is, if a record M1 is sent by the same producer as a record M2, and M1 is sent first, then M1 will have a lower offset than M2 and appear earlier in the log.
* A consumer instance sees records in the order they are stored in the log.
* For a topic with replication factor N, we will tolerate up to N-1 server failures without losing any records committed to the log.

.





Guarantees http://www.benniehaelen.com/author/bennie-haelenhoneywell-com/ https://kafka.apache.org/documentation

	The Kafka messaging system provides the following guarantees:

	Messages sent by a producer to a particular topic partition will be appended in the order that they were sent. If a message Msg1 is sent by the same producer as message Msg2, and Msg1 is sent first, then Msg1 will have a lower offset that Msg2 and appear earlier in the log.

		Kafka provides order guarantee of messages in a partition. If message B was written after message A, using the same producer into the same partition, then Kafka guarantees that the offset of message B will be higher than message A, and that consumers will read message B after message A.

	A consumer instance sees messages in the order they are stored in the log.

	For a topic with replication factor N, Kafka will tolerate up to N-1 server failures without losing any messages committed to the log.

	Produced messages are considered “committed” when they were written to the partition on all its in-sync replicas \(but not necessarily flushed to disk\). Producers can choose to receive acknowledgement on messages sent when the message was fully committed, when it was written to the leader or when it was sent over the network.

	Messages that are committed, will not be lost as long as at least one replica remains alive.

	Consumers can only read messages that are committed.

