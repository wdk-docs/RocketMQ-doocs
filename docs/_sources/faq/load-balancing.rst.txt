消息负载均衡策略
=======================

本页目录
发布方消息负载均衡策略
订阅方消息负载均衡策略

消息队列 RocketMQ 的消息负载策略针对发布方和订阅方有所差异。对订阅方而言，消息负载策略在一定程度上影响消息堆积。

发布方消息负载均衡策略
---------------------------

消息队列 RocketMQ 针对发布方采取的是轮询制，即 Producer 的消息以轮询的方式发送至 Queue，如下图所示。

.. figure:: http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/117459/cn_zh/1557060781329/%E8%B4%9F%E8%BD%BD%E7%AD%96%E7%95%A50-7.PNG
   :alt: 发布方消息负载均衡策略

图中箭头线条上的标号代表顺序，发布方会把第一条消息发送至 Queue 0，
然后第二条消息发送至 Queue 1，以此类推，第八条消息发送至 Queue 7，
第九条消息又发送至 Queue 0，循环往复。

订阅方消息负载均衡策略
---------------------------

消息队列 RokcetMQ 包含 Broker 和 Name Server 等节点，
其中 Broker 节点负责将 Topic 的路由信息上报至 Name Server 节点。

假设目前消息队列 RocketMQ 只有一个 Broker 节点，消息从 Producer 发送至消息队列 RocketMQ 的 Topic，
默认会将这些 Topic 下的消息均衡负载至 8 个 Queue（逻辑概念）。
RocketMQ Broker 会将这些 Queue 再平均分配至属于同一个 Group ID 的订阅方集群。

因此，每台订阅方机器处理的 Queue 的数量有以下几种可能：

- 若订阅方机器数量大于 Queue 的数量，则超出 Queue 数量的机器会处理 0 个 Queue 上的消息，如下图所示。

  .. figure:: http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/117459/cn_zh/1557060862975/%E8%AE%A2%E9%98%85%E6%96%B9%E7%AD%96%E7%95%A50-7.PNG
    :alt: 订阅方消息负载均衡策略

- 若订阅方机器数量等于 Queue 的数量，则每台机器会处理 1 个 Queue 上的消息，如下图所示。

  .. figure:: http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/117459/cn_zh/1557060880565/%E8%B4%9F%E8%BD%BD%E7%AD%96%E7%95%A5%3D0-7.PNG
    :alt: 订阅方消息负载均衡策略

- 若订阅方机器数量小于 Queue 的数量，则每台机器会处理多个 Queue 上的消息，如下图所示。

  .. figure:: http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/117459/cn_zh/1557060899125/%E8%AE%A2%E9%98%85%E6%96%B9%E7%AD%96%E7%95%A5%E6%98%AF0-7.PNG
    :alt: 订阅方消息负载均衡策略

如果其中一台机器处理变慢，可能是机器硬件、系统、远程 RPC 调用或 Java GC 等原因导致分配至此机器上的 Queue 的消息不能及时处理；此外，消息队列 RocketMQ 的消息负载是按 Queue 为粒度维护，所以，整个 Queue上的消息都会堆积。
