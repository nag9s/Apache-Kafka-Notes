[http://www.jesse-anderson.com/2016/04/kafka-0-9-0-changes-for-developers/](http://www.jesse-anderson.com/2016/04/kafka-0-9-0-changes-for-developers/)

The 0.9.0 consumer assumes all offsets are stored in Kafka Brokers \( they used to be stored in zookeeper prior 0.9 , In earlier Kafka releases \(before 0.8.2\), consumers commit their offsets to ZooKeeper.\) . The way how it is done from 0.9 is ,  Kafka now provides an alternative to store consumer offsets into a special and separate Kafka topic, which is replicated and highly available.



 If you have an existing old consumer, you’ll need to move your offsets to Kafka from ZooKeeper by setting

`offsets.storage=kafka`

and

`dual.commit.enabled=true`

in your 0.8.2 consumer code. Once you’ve moved the offsets to Kafka, you can start using a new consumer at the same place. Another option is to save out the current old consumer’s offset location and start the new consumer at the same offset.

