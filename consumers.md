Consumers

```
In messaging we have typically recognized two core models:                              Queuing
```

Publish-subscribe

```
    In a queuing model, a pool of consumers can read from a queue, but a single message only goes to exactly one consumer



    In a publish-subscribe model the message is broadcast to all subscribing consumers



    With the concept of the Consumer Group Kafka offers a single consumer abstraction that generalizes queuing and publish-subscribe.

        In this model, consumers label themselves with a consumer group name, and each message published to the topic is delivered to one consumer instance within each subscribing consumer group.

        Consumer instances can be in separate processes on separate machines, as is shown in the figure below:



        If all the consumer instances have the same consumer group, then this model works just like a traditional queue balancing log over the consumers.

        On the other hand, if all the consumer instances have different consumer groups, then the model works like traditional publish-subscribe and all messages are broadcast to all consumers.

        In most scenarios, a topic will have a small number of consumer groups, one for each logical subscriber. Each group is composed of many consumer instances for scalability and fault tolerance. Effectively, this enforces publish-subscribe semantics where the subscriber is a cluster of consumers instead of a single process.

Two Types of API's

    high-level

        supported consumer groups and handled failover,

    simple consumer 

        provided full control, but required users to manage failover and error handling themselves.

    From 0.9, a new consumer API was introduced replacing the above high level and simple consumer 

        While the old consumer depended on Zookeeper for group management, the new consumer uses a group coordination protocol built into Kafka itself. 
```



