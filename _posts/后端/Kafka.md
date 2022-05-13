Kafka是可靠的、可扩展的实时数据处理系统，实时系统的意思是数据一旦产生便需要处理，
其中最常见的实时处理系统就是消息中间件，也叫消息队列Message queue。

### topic

生产者和消费者之间的一条通道叫做topic，说白了，好比我关注了某人的某个频道，那么这个频道其实就是一个topic，
该up生产的消息都会发送到该频道，然后被我这个消费者所接收。

### offset

传统的queue结构，就是先进先出，也就是消息存入队列后，有个出队列的概念。

但是在kafka中消费者可能有很多，例如很多人都关注了同一个topic，那么接收消息的时候，不能存在消费者自己把已读消息删除的情况，因为不知道其他消费者
有没有接收到，因此kafka首先会对数据进行持久化存储；其次，每个消费者端都会维持自己当前消费到的数据队列中所在位置，也就是offset，
下次读取从offset+1。


### Partition

当一个topic的消息过多的时候，那么使用一个topic一条队列，甚至多个topic在一条队列，显然效率很低。

Partition就是一个topic使用多个队列的概念，每个队列称为一个partition，

队列内的消息是有序的，队列间的消息是无序的（可以无序，因为有时候消息的有序并不重要）。

### Broker

Partition是如何分配在具体机器上的呢？如果把相同topic的partition都放在一个机器山，可见如果这个机器挂了或者效率不行，那么整体这个topic就
要遭受重大影响。

Broker是kafka集群中的机器的单位，每个broker里面会放入很多不同topic的Partition，而且这些Partition并不是唯一的，可以在另一个broker里面
放入replica。这些replica之间的区别是，有一个是leader，负责消息读写，与其余follower replica同步。

### 和zookeeper的关系

zookeeper是保证分布式系统的信息一致性。kafka（目前）离不开zookeeper。作用是：

1. controller选举（保证有且只有一个，挂了还保证选举一个新的），controller是一个broker，作用是维护leader和follower关系：具体来说，就是
   如果发现哪个leader挂了，那么controller会告诉其中的某个follower来进行接管（成为新leader）。
2. cluster membership： 维护哪些broker仍存活并属于kafka集群？
3. topic管理：每个topic有多少个partition，replica在哪
4. ACLs：管理topic的读写权限