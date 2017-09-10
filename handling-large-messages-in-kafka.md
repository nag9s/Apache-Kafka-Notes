HANDLING LARGE MESSAGES IN KAFKA http://ingest.tips/2015/01/21/handling-large-messages-kafka/

	The best way to send large messages is not to send them at all. If shared storage is available \(NAS, HDFS, S3\), placing large files on the shared storage and using Kafka just to send a message about the file’s location is a much better use of Kafka.

	The second best way to send large messages is to slice and dice them. Use the producing client to split the message into small 10K portions, use partition key to make sure all the portions will be sent to the same Kafka partition \(so the order will be preserved\) and have the consuming client sew them back up into a large message.

	Kafka producer can be used to compress messages. If the original message is XML, there’s a good chance that the compressed message will not be very large at all. Use compression.codec and compressed.topics configuration parameters in the producer to enable compression. GZip and Snappy are both supported.

