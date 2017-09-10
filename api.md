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

