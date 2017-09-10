Use Cases

	Messaging. Kafka tends to work very well as a replacement for a more traditional message broker. In comparison to most messaging systems Kafka has better throughput, built-in partitioning, replication and inherent fault-tolerance which makes it a good fit for large scale message processing applications.

	IOT Application Analytics. Kafka is a good fit as a message-ingestion point for an IOT Analytics sub-system. Telemetry events are routed to Kafka topics and are consumed downstream by a real-time analytics engine such as Apache Storm. Within this blog series we will perform a deep dive into this topic.

	Log Aggregation. A number of applications use Kafka as a replacement for a log aggregation solution. Log aggregation typically collects physical log files from remote servers, and centralizes them in one location so they can be processed. Kafka provides  a clean abstraction of log data as a stream of messages instead of a traditional file. This enables lower-latency processing and easier support for multiple data sources and distributed data consumption. A Kafka-based system tends to have  lower latency and stronger delivery guarantees.

	Metrics. Kafka is often used for operational monitoring data. This involves aggregating statistics from distributed applications to produce centralized feeds of operational data. Indeed, Kafka is a good fit for collecting “Telemetry about a system”, where log data from different instances of the application is aggregated into a HDFS-based back end.

