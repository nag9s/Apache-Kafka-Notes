Messages and Batches

	The unit of data within Kafka is called a message

	A message is simply an array of bytes, as far as Kafka is concerned, so the data contained within it does not have a specific format or meaning to Kafka

	Messages can have an optional bit of metadata which is referred to as a key.

	The key is also a byte array, and as with the message, has no specific meaning to Kafka

	Keys are used when messages are to be written to partitions in a more controlled manner. The simplest such scheme is to treat partitions as a hash ring, and assure that messages with the same key are always written to the same partition.

	messages are written into Kafka in batches. 

		A batch is just a collection of messages, all of which are being produced to the same topic and partition.

		n individual round trip across the network for each message would result in excessive overhead, and collecting messages together into a batch reduces this	presents a tradeoff between latency and throughput:

			he larger the batches, the more messages that can be handled per unit of time, but the longer it takes an individual message to propagate. Batches are also typically compressed, which provides for more efficient data transfer and storage at the cost of some processing power.

