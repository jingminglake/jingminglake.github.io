---
layout:     post
title:      Kafka
subtitle:   核心概念与可靠性
date:       2026-09-30
author:     Jingming
header-img: img/post-bg-map.jpg
catalog: true
tags:
    - 后端
    - 分布式
    - 消息队列
---

Kafka是可靠的、可扩展的实时数据处理系统，实时系统的意思是数据一旦产生便需要处理，
其中最常见的实时处理系统就是消息中间件，也叫消息队列Message queue。

### 一、topic

生产者和消费者之间的一条通道叫做topic，说白了，好比我关注了某人的某个频道，那么这个频道其实就是一个topic，
该up生产的消息都会发送到该频道，然后被我这个消费者所接收。

### 二、offset

传统的queue结构，就是先进先出，也就是消息存入队列后，有个出队列的概念。

但是在kafka中消费者可能有很多，例如很多人都关注了同一个topic，那么接收消息的时候，不能存在消费者自己把已读消息删除的情况，因为不知道其他消费者
有没有接收到，因此kafka首先会对数据进行持久化存储；其次，每个消费者端都会维持自己当前消费到的数据队列中所在位置，也就是offset。

注意：消费者提交的offset表示的是"**下一条要读的位置**"，不是"最后读完的那条"。例如处理完了offset 150，提交的是151。
offset存在Kafka内部一个叫 `__consumer_offsets` 的topic里（老版本存在zookeeper）。

消息不会因为被消费而删除，但会按**保留策略（retention）**删除：按时间（默认7天）或按大小。
另外还有**日志压缩（compaction）**：同一个key只保留最新的一条，适合存"某个实体的最新状态"。

### 三、Partition

当一个topic的消息过多的时候，那么使用一个topic一条队列，甚至多个topic在一条队列，显然效率很低。

Partition就是一个topic使用多个队列的概念，每个队列称为一个partition，

队列内的消息是有序的，队列间的消息是无序的（可以无序，因为有时候消息的有序并不重要）。

#### 按key分区

生产者发消息时可以带一个key，Kafka按 `hash(key) % partition数` 选partition，因此**同一个key的消息一定在同一个partition，也就一定有序**。

key的选择原则：**选"需要有序的最小粒度"**。例如需求是同一订单的"创建、支付、发货"有序，key就选 `order_id`，而不是 `merchant_id`：
后者也能保证正确，但大商家的订单全挤进一个partition形成热点，而且同商家不同订单之间被迫排队。

保证有序的三个坑：

1. 生产端重试可能乱序（消息1失败重试后排到了消息2后面）。打开幂等生产者 `enable.idempotence=true`（3.0起默认开），broker按序列号去重、保序。
2. 消费端把同一partition的消息丢进线程池并行处理，顺序就没了。要么一个partition单线程，要么再按key分发到固定线程。
3. 增加partition数后 `% N` 变了，同一个key的新消息会落到别的partition。Kafka用的是普通取模不是一致性哈希，所以partition数要一开始留足。

#### 只用1个partition行不行

可以保证全局有序，但代价是：

- 同一消费者组里只有1个消费者能干活，消费能力被限制在一台机器
- 1个partition的leader只在一台broker上，写入也只能靠这一台
- 一条慢消息会堵住后面所有消息（队头阻塞，head-of-line blocking）

只适合流量小、又真的需要全局有序的场景（配置变更、表结构变更事件）。实际业务通常只要求**同一实体**有序，用key分区即可。

### 四、Broker

Partition是如何分配在具体机器上的呢？如果把相同topic的partition都放在一个机器上，可见如果这个机器挂了或者效率不行，那么整体这个topic就
要遭受重大影响。

Broker是kafka集群中的机器的单位，每个broker里面会放入很多不同topic的Partition，而且这些Partition并不是唯一的，可以在另一个broker里面
放入replica。这些replica之间的区别是，有一个是leader，负责消息写入（也负责读），follower从leader**异步拉取**数据来同步。

补充：Kafka 2.4起消费者可以从同机架的follower读取（节省跨机房带宽），但写入仍只能经过leader。
leader决定写入顺序、follower按顺序复制，这就是状态机复制（state machine replication），和MySQL主从、Raft是同一个原理。

### 五、消费者组（consumer group）

规则：**同一个组内，一个partition只能分给一个消费者**；不同的组各自独立地读全量数据，各有各的offset。

- 消费并行度的上限 = partition数。10个partition的topic，组里开15个消费者，有5个空闲（相当于热备，有人挂了可以顶上）。
- 同一个组 = 队列模型（多个worker瓜分任务）；不同的组 = 发布订阅模型（每个服务都收到全量）。
  例如订单服务和风控服务都要读全部消息，就各建一个组；放在同一组的话两边会瓜分消息，各自只读到一部分。

#### 再平衡（rebalance）

消费者挂了怎么办？和GFS的chunk server挂了是一个套路：心跳发现故障 → 重新分配任务。

1. 发现：每个组有一个**组协调者（group coordinator）**，由某台broker担任。消费者定期发心跳；心跳超时，或太久没来拉消息（超过 `max.poll.interval.ms`），就判定它挂了。
2. 重新分配：协调者触发再平衡，把partition重新分给活着的消费者。
3. 接着消费：接手者从该partition**最后提交的offset**开始读。挂掉的消费者处理了但没来得及提交的消息，会被**再处理一次**。

为什么老协议（eager）再平衡时要停掉全组？因为分配算法是从头重算，不只是挂掉那个人的partition会动，活着的消费者手上的partition也可能被挪走：

```
挂之前：C1: p0, p1    C2: p2, p3    C3: p4, p5
C3 挂了，按范围重新分：
挂之后：C1: p0, p1, p2    C2: p3, p4, p5      ← p2 从 C2 移到 C1
```

交接必须"原主人先停、提交offset、交出 → 新主人再接手"，否则两个消费者会同时读p2。老协议不知道哪些会动，干脆全员交出再重分，代价是全组暂停。
**增量再平衡（cooperative rebalancing）**拆成两轮：第一轮只让要换手的partition交出，其他照常消费；第二轮再分给新主人。Kafka 4.0的新消费者组协议又把分配逻辑移到了broker端。

一句话：停下来不是因为他们会抢同一个partition，而是为了保证交接过程中不会抢。

### 六、acks与ISR：生产端怎么不丢消息

`acks` 决定生产者要等多少个副本确认才算发送成功：

| 设置 | broker什么时候回复成功 | 可靠性 |
|---|---|---|
| `acks=0` | 不回复，发出去就不管 | 最差 |
| `acks=1` | leader写入自己的日志后 | 中等 |
| `acks=all`（`-1`） | **ISR里的所有副本**都写入后 | 最好 |

**ISR（in-sync replicas）**：和leader保持同步的副本集合（含leader自己）。follower落后太多（默认30秒没跟上）就被踢出ISR，追上了再加回来。

`acks=1` 为什么会丢：follower是异步拉取的，leader回复成功时follower可能还没拉到。此时leader挂了，从follower里选出的新leader没有这条消息；
旧leader修好重启后还会**截断自己的日志对齐新leader**，连它磁盘上那份也删掉。注意是"可能"丢，不是一定丢。

`acks=all` 的"all"是**ISR里的所有副本，不是所有副本**。不等所有副本，是因为一个慢的或挂掉的follower会拖住每一次写入。
陷阱：ISR缩到只剩leader时，`acks=all` 就退化成了 `acks=1`。所以要配合 `min.insync.replicas`：

```
replication.factor = 3                    # 每个partition 3个副本
min.insync.replicas = 2                   # ISR少于2个时拒绝写入（NotEnoughReplicas）
acks = all                                # 等ISR全部确认
unclean.leader.election.enable = false    # 只有ISR里的副本能当新leader（默认值）
```

宁可不可用，也不丢数据。这套组合可以容忍1台机器宕机且继续写入。四项缺一不可。

### 七、投递语义：消费端怎么不丢、不重

消费者要做两件事：处理消息、提交offset。顺序决定语义：

- **先提交，再处理**：处理到一半挂了，这条消息不会再被读到 → 可能丢，**at-most-once（最多一次）**
- **先处理，再提交**：处理完、没提交就挂了，重启/再平衡后从旧offset重读 → 可能重复，**at-least-once（至少一次）**

选择：**默认选at-least-once + 消费端幂等**。重复可以靠幂等消除，丢了就找不回来。
只有丢几条也无所谓的场景（监控指标、埋点、日志采集）才选at-most-once。
幂等手段见系统设计知识（四）：唯一ID、唯一约束、条件更新。例如发优惠券，给 `(user_id, campaign_id)` 加唯一约束。

坑：消费者默认 `enable.auto.commit=true`，每5秒在拉取时自动提交offset，它不知道你是否处理完了。
如果把消息丢给后台线程异步处理，可能已经自动提交了、处理还没完就挂了 → 本以为是至少一次，实际成了最多一次。
**对可靠性有要求的业务，关掉自动提交，处理完手动提交。**

#### exactly-once（恰好一次）

Kafka自带的exactly-once（事务API）只覆盖"从Kafka读 → 处理 → 写回Kafka"这条链路，发券、写数据库这类外部副作用管不了。办法：

1. at-least-once + 幂等：效果上就是恰好一次，最常用
2. 把业务结果和offset存在**同一个数据库事务**里，重启时从数据库读offset：

```sql
BEGIN;
INSERT INTO coupons(user_id, campaign_id) VALUES (...);
UPDATE consumer_offsets SET next_offset = 151 WHERE topic = 'coupon' AND partition_id = 3;
COMMIT;
```

### 八、元数据管理：从zookeeper到KRaft

早期Kafka离不开zookeeper（zookeeper是保证分布式系统信息一致性的组件）。作用是：

1. controller选举（保证有且只有一个，挂了还保证选举一个新的），controller是一个broker，作用是维护leader和follower关系：具体来说，就是
   如果发现哪个leader挂了，那么controller会告诉其中的某个follower来进行接管（成为新leader）。
2. cluster membership： 维护哪些broker仍存活并属于kafka集群？
3. topic管理：每个topic有多少个partition，replica在哪
4. ACLs：管理topic的读写权限

现状：Kafka 3.3起 **KRaft模式**可用于生产——由几台controller节点自己用Raft协议选主、保存元数据，不再需要zookeeper；
**Kafka 4.0（2025年）已彻底移除zookeeper**。上面4件事现在都由KRaft controller来做。
好处是少运维一套系统，元数据变更和故障切换也更快。
