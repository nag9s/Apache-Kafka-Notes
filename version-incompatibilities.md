Version Incompatibles

	The major limitation is that Kafka Version 0.7.x can't just be replaced by Version

cluster to the 0.8-based cluster.



	Kafka starting 0.8.2.x exposed a new set of API's to work with kafka, older being Producer which works with KeyedMessage\[K,V\] where the new API is KafkaProducer with ProducerRecord\[K,V\]:
