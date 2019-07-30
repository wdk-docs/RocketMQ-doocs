消息过滤
============================================

本文描述消息队列 RocketMQ 的消费者如何根据 Tag 在消息队列 RocketMQ 服务端完成消息过滤，
关于 Topic 和 Tag 的介绍，请参考Topic 与 Tag 最佳实践。

Tag，即消息标签、消息类型，用于对某个 Topic 下的消息进行分类。
消息队列 RocketMQ 允许消费者按照 Tag 对消息进行过滤，确保消费者最终只消费到他关心的消息类型。

以下图电商交易场景为例，从客户下单到收到商品这一过程会生产一系列消息，比如订单创建消息（order）、支付消息（pay）、物流消息（logistics）。
这些消息会发送到 Topic 为 Trade_Topic 的队列中，被各个不同的系统所接收，
比如支付系统、物流系统、交易成功率分析系统、实时计算系统等。 其中，物流系统只需接收物流类型的消息（logistics），
而实时计算系统需要接收所有和交易相关（order、pay、logistics）的消息。

.. figure:: http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/29543/cn_zh/1535686269135/%E4%BA%A4%E6%98%93%E5%9C%BA%E6%99%AF.png
   :alt: 消息过滤

.. note::
   针对消息归类，您可以选择创建多个 Topic， 或者在同一个 Topic 下创建多个 Tag。
   但通常情况下，不同的 Topic 之间的消息没有必然的联系，
   而 Tag 则用来区分同一个 Topic 下相互关联的消息，比如全集和子集的关系，流程先后的关系。

参考示例
-------------

发送消息
~~~~~~~~~~~~~~~~~~~~~

发送消息时，每条消息必须指明 Tag：

.. code:: java

   Message msg = new Message("MQ_TOPIC","TagA","Hello MQ".getBytes());

消费方式-1
~~~~~~~~~~~~~~~~~~~~~

消费者如需订阅某 Topic 下所有类型的消息，Tag 用符号 * 表示：

.. code:: java

    consumer.subscribe("MQ_TOPIC", "*", new MessageListener() {
        public Action consume(Message message, ConsumeContext context) {
            System.out.println(message.getMsgID());
            return Action.CommitMessage;
        }
    });

消费方式-2
~~~~~~~~~~~~~~~~~~~~~

消费者如需订阅某 Topic 下某一种类型的消息，请明确标明 Tag：

.. code:: java

    consumer.subscribe("MQ_TOPIC", "TagA", new MessageListener() {
        public Action consume(Message message, ConsumeContext context) {
            System.out.println(message.getMsgID());
            return Action.CommitMessage;
        }
    });

消费方式-3
~~~~~~~~~~~~~~~~~~~~~

消费者如需订阅某 Topic 下多种类型的消息，请在多个 Tag 之间用 || 分隔：

.. code:: java

    consumer.subscribe("MQ_TOPIC", "TagA||TagB", new MessageListener() {
        public Action consume(Message message, ConsumeContext context) {
            System.out.println(message.getMsgID());
            return Action.CommitMessage;
        }
    });

消费方式-4（错误示例）
~~~~~~~~~~~~~~~~~~~~~

同一个消费者多次订阅某个 Topic 下的 Tag，以最后一次订阅的 Tag 为准：

.. code:: java

    //如下错误代码中，consumer 只能接收到 MQ_TOPIC 下 TagB 的消息，而不能接收 TagA 的消息。
    consumer.subscribe("MQ_TOPIC", "TagA", new MessageListener() {
        public Action consume(Message message, ConsumeContext context) {
            System.out.println(message.getMsgID());
            return Action.CommitMessage;
        }
    });
    consumer.subscribe("MQ_TOPIC", "TagB", new MessageListener() {
        public Action consume(Message message, ConsumeContext context) {
            System.out.println(message.getMsgID());
            return Action.CommitMessage;
        }
    });
