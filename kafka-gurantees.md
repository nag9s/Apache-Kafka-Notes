Apache Kafka preserves order of messages within a partition.

This means that if messages were sent from the producer in a specific order, the broker will write them to a partition in this order and all consumers will read them in this order. For some use-cases, order is very important. There is a big difference between depositing 100$ in an account and later withdrawing them, and the other way around! However, some use cases are less sensitive

.

