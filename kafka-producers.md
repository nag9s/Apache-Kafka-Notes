Producers

	Producers public their data to the topics of their choice.

	The producer is responsible for choosing which message to assign to which partition within the topic. This can be done in two different ways:

		Publishers can publish to different topics in a round-robin fashion, thereby automatically balancing the load.

		Public can dynamically select the topics by means of a “semantic partition function”, which is typically based upon a key contained in the message.

	The producer connects to any of the alive nodes \( using BOOTSTRAP\_SERVERS\_CONFIG property, which specifies list of brokeers \) and requests metadata about the leaders for the partitions of a topic. This allows the producer to put the message directly to the lead broker for the partition. Remember, the leader is the node responsible for all reads and writes for a given topic partition.

