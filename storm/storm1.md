##Storm基础学习笔记

####Storm特点

1. 用例非常广泛
2. 可伸缩性
3. 保证没有数据丢失
4. 强壮的鲁棒性
5. 容错性好
6. 编程语言无关

####Storm框架核心

> 以下为个人的理解，如有错误，请帮忙指摘。大部分对比Spring的JMS处理机制来理解。
> 暂不对比Netty实现。

- Spout（喷口）

  Spout 是流的来源。一般从外部读取，提交到Topology。

  > 类似于Spring JMS中从API,数据库等读取数据的一个线程，然后提交到队列异步处理的源。

- Bolt（螺栓）

  Bolt可以完成过滤、业务处理、连接运算、连接、访问数据库等业务处理。

  > 相当于Spring JMS 当中的Listener，从监听的队列从获取消息，执行业务处理。

- Topology（拓扑）

  就是一个图的计算。拓扑的每个节点包含处理逻辑，节点之间信息该如何传递。

  >相当于Spring JMS 中的Message Dispatcher处理的事情，将队列中的消息，分发给
  >监听的Listener。 观察者模式,Swing的事件分发和Spring JMS是典型的实现。


- Stream（流）

  流是一个无界Tuple序列,就是序列化后的一个消息流。

  >相当于Spring JMS中的Message。被序列化处理过，当然，可包含的数据类型多。

- Stream grouping（流分组）

  流分组在Bolt 的任务中定义流应该如何分区。拓扑定义中的一部分，为每个Bolt指定应该接收哪个流作为输入。

  >有点类似于Spring JMS中为每个Listener设定的监听。

- Task（任务）

  每个Spout或者Bolt在集群执行许多任务。

- Worker（工作进程）

  Topology跨一个或多个Worker节点的进程执行。每个Worker节点的进程是一个物理的JVM和Topology执行所有任务的一个子集。

  容错机制：当一个Worker（工作进程）死亡，Supervisor 会尝试重启它。如果它在启动时连续失败了一定的次数，
  无法发送心跳信息到Nimbus，Nimbus 将在另一台主机上重新分配Worker。

~~~Java
//定义一个拓扑
TopologyBuilder builder = new TopologyBuilder();

//配置Spout
builder.setSpout("sentences", new KestrelSpout("kestrel.backtype.com",
22133, "sentence_queue",new StringScheme()));

//配置不同的Bolt
builder.setBolt("split", new SplitSentence(), 10).shuffleGrouping("sentences");
builder.setBolt("count", new WordCount(), 20).fieldsGrouping("split", new Fields("word"));
~~~

####Storm节点

- 主节点

  主结点运行一个叫做Nimbus的守护进程，负责在集群内分发代码，为每个工作结点指派任务和监控失败的任务。

- 工作节点

  工作结点运行一个叫做Supervisor的守护进程，它执行topology的一部分。

>Nimbus 和Supervisor 守护进程被设计成快速失败的（每当遇到任何意外的情况，进程自
动毁灭）和无状态的（所有状态都保存在ZooKeeper 或者磁盘上）

####Storm操作模式

- 本地模式

  最简单的查看Topology组件一起运作的模式，用于开发，测试和调试。

- 远程模式

  提交Topology到集群，不显示调试信息，用于生产环境。

####常用的类

- BaseRichSpout/IRichSpout(消息生产者)
- BaseRichBolt(消息处理者)
- TopologyBuilder(拓扑构建器)
- Values(数据存放传输给下个组件)
- Tuple(封装的元消息)
- Config(基本配置)
- StormSubmitter/LocalCluster(拓扑提交器)

####Storm的可靠性机制

- Storm可靠性功能的用户，有两件事情必须完成：一是当在树的元组上创建一
个新链接时需要通知Storm，二是处理完成一个单一元组时需要通知Storm。(Acker机制)

  如何保证消息的可靠性:某个组件从队列里面取出一个消息时，它将“打开”消息。这意味着消息实际上还没有从队列取出，
  而是放在“待定”状态等待确认消息已经完成。当在“待定”状态时，一个消息将不会被发送到其他消费者队列。
  如果客户端断开连接，所有“待定”状态的消息会放回到队列。

- Acker机制

  原理就是:一个数字跟自己异或得到的值是0,每一个操作数出现且仅出现两次。

  一般使用:首先,在你生成一个新的tuple的时候要通知storm; 其次,完成处理一个tuple之后要通知storm。

  >Spout发射完某个MessageId对应的源Tuple之后，它会告诉Acker自己发射的RootId以及生成的那些源Tuple的Id。而当Bolt处理完一个输入Tuple并产生出新的Tuple时，也会告知Acker自己处理的输入Tuple的Id以及新生成的那些Tuple的Id(如果失败了自然就不会发送了，不发送的话代表tuple树就失败了)。Acker只需要对这些Id进行异或运算，就能判断出该RootId对应的消息单元是否成功处理完成了。
