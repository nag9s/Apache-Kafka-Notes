Version Incompatibles

	The major limitation is that Kafka Version 0.7.x can't just be replaced by Version 0.8, as it is not backward compatible. If the existing Kafka cluster is based on 0.7.x, a migration tool is provided for migrating the data from the Kafka 0.7.x-based

cluster to the 0.8-based cluster.



	Kafka starting 0.8.2.x exposed a new set of API's to work with kafka, older being Producer which works with KeyedMessage\[K,V\] where the new API is KafkaProducer with ProducerRecord\[K,V\]:

