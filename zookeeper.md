Zookeeper

	While most readers will know Zookeeper from the larger Hadoop ecosystem, it is also used in stand-alone Kafka deployments.

	Zookeeper servers as the coordination interface between the Kafka brokers and consumers. At the core, Zookeeper is an open source, High Performance coordination service for distributed applications. Within the Apache Hadoop ecosystem it acts as a centralized service for tasks such as:

		1. Configuration Management. Zookeeper enables the cluster member nodes to “bootstrap” their configuration from a central source.

		2. Group Membership. It will manage the cluster’s node status in real time and coordinate nodes joining or leaving the cluster.

		3. Naming Services and Registry. Zookeeper maintains a centralized and highly available registry where it maintain the central naming services.

		4. Locking and Synchronization. Zookeeper will maintain any shared locks, barriers etc…

		5. Leader Switch-Over.  Zookeeper maintains the shared state between partition leaders and followers, ensuring that a leader transition is seamless.

	An example of how Kafka leverages Zookeeper is shown in the figure below:

		

			In this case the producer will first call onto Zookeeper to get the Kafka Broker address. . r.

			As the Producers start streaming in messages, the broker maintains it state in Zookeeper.

			 The consumers also track their individual message offsets through Zookeeper,This is actually done by consumer group instead of individual consumers, more about that late

