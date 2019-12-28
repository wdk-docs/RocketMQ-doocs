收到订阅关系不一致告警后怎么办？
===================================

问题描述
------------

- Group ID 订阅的部分消息未收到。登录消息队列 RocketMQ 控制台，
  单击消息查询 > 按 Message ID 查询消息，消息显示已至少被消费—次，但从消费逻辑判断未消费。
- 登录消息队列 RocketMQ 控制台，单击消费者管理 > 消费者状态，订阅关系是否一致栏显示否。

问题分析
---------------

消息队列 RocketMQ 里的一个 Group ID 代表一个 Consumer 实例群组。
对大多数分布式应用来说，一个 Group ID 下通常会挂载多个 Consumer 实例。
订阅关系一致指，同一个 Group ID 下所有的 Consumer 实例的 Topic 和 Tag 都必须完全一致。

若同一个 Group ID 下的 Consumer 实例配置了不同的 Topic，
或是 Topic 相同但 Tag 不同 ，
订阅关系就会不一致。一旦订阅关系不一致，消息消费的逻辑就会混乱，导致消息丢失。

【原因1】配置了相同 Group ID 的不同客户端订阅的 Topic 不一致。
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**错误示例**

同一个 Group ID（GID-MQ-FAQ）订阅的 Topic 不同（分别是 MQ-FAQ-TOPIC-1、MQ-FAQ-TOPIC-2）。

**JVM-1 上的代码**

.. code:: java

  Properties properties = new Properties();
  properties.put(PropertyKeyConst.GROUP_ID, "GID-MQ-FAQ");
  Consumer consumer = ONSFactory.createConsumer(properties);
  consumer.subscribe("MQ-FAQ-TOPIC-1", "NM-MQ-FAQ", new MessageListener() {
      public Action consume(Message message, ConsumeContext context) {
          System.out.println("Receive: " + message);
          return Action.CommitMessage;
      }
  });
  consumer.start();

**JVM-2 上的代码**

.. code:: java

  Properties properties = new Properties();
  properties.put(PropertyKeyConst.GROUP_ID, "GID-MQ-FAQ");
  Consumer consumer = ONSFactory.createConsumer(properties);
  consumer.subscribe("MQ-FAQ-TOPIC-2", "NM-MQ-FAQ", new MessageListener() {
      public Action consume(Message message, ConsumeContext context) {
          System.out.println("Receive: " + message);
          return Action.CommitMessage;
      }
  });
  consumer.start();

【原因2】同一个 Group ID 下的不同客户端订阅的 Topic 相同，但 Tag 不同。
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


**错误示例**

同一个 Group ID（GID-MQ-FAQ）订阅的 Topic 不同（分别是 MQ-FAQ-TOPIC-1、MQ-FAQ-TOPIC-2）。

**JVM-1 上的代码**

.. code:: java

  Properties properties = new Properties();
  properties.put(PropertyKeyConst.GROUP_ID, "GID-MQ-FAQ");
  Consumer consumer = ONSFactory.createConsumer(properties);
  consumer.subscribe("MQ-FAQ-TOPIC-1", "NM-MQ-FAQ", new MessageListener() {
      public Action consume(Message message, ConsumeContext context) {
          System.out.println("Receive: " + message);
          return Action.CommitMessage;
      }
  });
  consumer.start();

**JVM-2 上的代码**

.. code:: java

  Properties properties = new Properties();
  properties.put(PropertyKeyConst.GROUP_ID, "GID-MQ-FAQ");
  Consumer consumer = ONSFactory.createConsumer(properties);
  consumer.subscribe("MQ-FAQ-TOPIC-2", "NM-MQ-FAQ", new MessageListener() {
      public Action consume(Message message, ConsumeContext context) {
          System.out.println("Receive: " + message);
          return Action.CommitMessage;
      }
  });
  consumer.start();

解决方案
---------------

请按照如下步骤操作：

1. 检查不同客户端的订阅代码。保持配置相同 Group ID 的所有客户端的订阅关系—致，包括订阅的 Topic 与 Tag。
2. 重启所有客户端应用。

结果验证
------------

- 消费者能接收到所有期望被接收的消息。
- 登录消息队列 RocketMQ 控制台，单击消费者管理 > 消费者状态，订阅关系是否一致栏显示是。
