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

  流分组在Bolt 的任务中定义流应该如何分区。

- Task（任务）

  每个Spout或者Bolt在集群执行许多任务。

- Worker（工作进程）

  Topology跨一个或多个Worker节点的进程执行。每个Worker节点的进程是一个物理的JVM和Topology执行所有任务的一个子集。

####Storm节点

- 主节点

  主结点运行一个叫做Nimbus的守护进程，负责在集群内分发代码，为每个工作结点指派任务和监控失败的任务。

- 工作节点

  工作结点运行一个叫做Supervisor的守护进程，它执行topology的一部分。

####Storm操作模式

- 本地模式

  最简单的查看Topology组件一起运作的模式，用于开发，测试和调试。

- 远程模式

  提交Topology到集群，不显示调试信息，用于生产环境。

####常用的类

- BaseRichSpout(消息生产者)
- BaseRichBolt(消息处理者)
- TopologyBuilder(拓扑构建器)
- Values(数据存放传输给下个组件)
- Tuple(封装的元消息)
- Config(基本配置)
- StormSubmitter/LocalCluster(拓扑提交器)
