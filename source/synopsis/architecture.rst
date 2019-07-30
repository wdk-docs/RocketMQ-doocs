产品架构
======================

消息队列 RocketMQ 在任何一个环境都是可扩展的，发布者必须是一个集群，消息服务器必须是一个集群，订阅者也同样。
集群级别的高可用，是消息队列 RocketMQ 跟其他的消息服务器的主要区别，消息发布者（Producer）发送一条消息到消息服务器，
消息服务器会随机的选择一个消费者（Consumer），只要这个消费者消费成功我们就认为是成功了。

.. note:: 文中所提及的消息队列 RocketMQ 的服务端或者服务器包含 Name Server、Broker 等。服务端不等同于 Broker。

系统部署架构
--------------------

系统部署架构如下图所示。

.. figure:: http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/112008/cn_zh/1557056104868/%E4%BA%A7%E5%93%81%E6%9E%B6%E6%9E%84.png
   :alt: 部署架构

图中所涉及到的概念如下所述：

- Name Server: 是一个几乎无状态节点，可集群部署，在消息队列 RocketMQ 中提供命名服务，更新和发现 Broker 服务。
- Broker：分为 Master Broker 和 Slave Broker，一个 Master Broker 可以对应多个 Slave Broker，
  但是一个 Slave Broker 只能对应一个 Master Broker。
  Broker 启动后需要完成一次将自己注册至 Name Server 的操作；
  随后每隔 30s 定期向 Name Server 上报 Topic 路由信息。
- Producer：与 Name Server 集群中的其中一个节点（随机）建立长链接（Keep-alive），
  定期从 Name Server 读取 Topic 路由信息，并向提供 Topic 服务的 Master Broker 建立长链接，
  且定时向 Master Broker 发送心跳。
- Consumer： 与 Name Server 集群中的其中一个节点（随机）建立长连接，
  定期从 Name Server 拉取 Topic 路由信息，
  并向提供 Topic 服务的 Master Broker、Slave Broker 建立长连接，
  且定时向 Master Broker、Slave Broker 发送心跳。
  Consumer 既可以从 Master Broker 订阅消息，也可以从 Slave Broker 订阅消息，订阅规则由 Broker 配置决定。

订阅模式
--------------------

消息队列 RocketMQ 的订阅模式采用的是发布/订阅模式（Pub/Sub 模式），如下图所示。

.. figure:: http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/112008/cn_zh/1554883226316/%E8%AE%A2%E9%98%85%E6%A8%A1%E5%BC%8F.png
   :alt: 订阅模式

- Producer 集群: 用来表示一个发送消息应用，一个 Producer 集群下包含多个 Producer 实例，
  可以是多台机器，也可以是一台机器的多个进程，或者一个进程的多个 Producer 对象。
  一个 Producer 集群可以发送多个 Topic 消息。可以想象一下，发送分布式事物消息时，
  如果 Producer 中途意外宕机，Broker 会主动回调 Producer 集群的任意一台机器来确认事务状态。
- Consumer 集群：用来表示一个消费消息应用，一个 Consumer 集群下包含多个 Consumer 实例，
  可以是多台机器，也可以是多个进程，或者是一个进程的多个 Consumer 对象。
  一个 Consumer 集群下的多个 Consumer 以均摊方式消费消息。如果设置的是广播方式，
  那么这个 Consumer 集群下的每个实例都消费全量数据。

一个 Consumer 对应一个 Group ID，一个 Group ID 可以订阅多个 Topic，如图中的 Group 1 所示。
Group 和 Topic 的订阅关系可以通过直接在程序中设置即可，具体设置方法可参见产品更新日志中的资源申请流程优化部分。
