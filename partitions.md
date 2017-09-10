parttions

	A partition is nothing more than an immutable, ordered sequence of messages. As messages are published the commit log will be updated. Each message in a partition is assigned a sequential ID number which uniquely identifies each message within the partition. This sequence number is often referred to as the message offset.

	 The performance of Kafka is relatively constant with respect to partition sizes, so retaining a lot of data will not negatively affect overall performance.

	The only metadata retained by the system on a per-consumer basis is the position of the consumer in the log, called the “offset”. This offset is controlled by the consumers; normally the consumer will advance its offset linearly as it reads messages, but since the consumer is in control, it can consume the messages in any preferred order. For example, a consumer can reset to an older offset it it wishes to re-process a batch of messages.

	This also implies that Kafka consumers are very lightweight, and have very little impact on the cluster’s overall performance. Since each consumer maintains its own offset, it has no ability to impact other consumers.

	two distinct purposes:

		The allow a topic to scale beyond a size that will fit on a single server. Each individual partition must fit on the servers that host it, but a topic may have many partitions, so it can handle an arbitrary amount of data.

		Partitions act as the primary unit of parallelism in Kafka. They allow  a single consumer to concurrently read messages in multiple concurrent threads. I will elaborate on this subject in detail in a later post.

		

	**Partition Distribution/ Replication / Leaders**

		The partitions of a topic are distributed over the servers in the Kafka clusters with each server handling data and request for a share of the partitions. Each partition is replicated across a configurable number of servers for fault tolerance.

		Each partition has one server which acts as the “leader” and zero on more servers which act as “followers”. The leader handles all read and write requests for the partition while the followers passively replicate the leader. If the leader fails, one of the followers will automatically become the new leader.  A follower acts as a normal consumer, it pulls messages and updates it own log. When a follower rejoins a partition it first re-syncs all messages, after which it can become fully online.

		The leader uses the Zookeeper heartbeat mechanism to indicate that it is alive and well. Each server acts as a leader some of its partitions and a follower for others so load is well balanced within the cluster.

		 Each server acts as a leader for some of its partitions and a follower for others so load is well balanced within the cluster . See the diagram. 

			You can also inspect the local filesystem to see how the --describe output above matches actual files. By default Kafka persists topics as “log files” \(Kafka terminology\) in the log.dir directory.

				

				

			

				In this example output the first broker \(with broker.id = 1\) happens to be the designated leader for partition 0 at the moment. Similarly, the second and third brokers are the leaders for partitions 1 and 2, respectively.

		

			When a topic is created in Kafka 0.8, Kafka determines how each replica of a partition is mapped to a broker. In general Kafka tries to spread the replicas across all brokers \(source\). Messages are first sent to the first replica of a partition \(i.e. to the current “leader” broker of that partition\) before they are replicated to the remaining brokers. Message producers may choose from different strategies for sending messages \(e.g. synchronous mode, asynchronous mode\). Producers discover the available brokers in a cluster and the number of partitions on each, by registering watchers in ZooKeeper.

			Having more partitions increases I/O parallelism for writes and also increases the degree of parallelism for consumers \(since partition is the unit for distributing data to consumers\).                    On the other hand, more partitions adds some overhead: \(a\) there will be more files and thus more open file handlers; \(b\) there are more offsets to be checkpointed by consumers which can increase the load of ZooKeeper. So, you want to balance these tradeoffs.

		![](/assets/import1.png)

