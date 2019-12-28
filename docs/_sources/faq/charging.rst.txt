计费相关
===================

... 每天扣除 2 元，这是什么费用？
    消息队列 RocketMQ 费用 = API 调用费 + Topic 资源占用费。每个 Topic 资源占用费为 2 元/天。

... 我昨天删除了 Topic，为什么今天会收到账单并被扣费？
   Topic 资源占用费以每天 00:00-23:59:59 为周期进行计费，第二天收费。
   所以，昨天删除 Topic，昨天的计费系统已经计入，今天会出账单进行扣费，明天就不会出账单扣费。

... 我没有使用消息队列 RocketMQ 服务，为什么会扣费？
   请查看账单，检查您是否有使用消息队列 RocketMQ 服务：

   .. figure:: http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/55731/cn_zh/1498131240291/billing1%281%29.png
      :alt: billing1

   .. figure:: http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/55731/cn_zh/1498131379327/billing2%281%29.png
      :alt: billing2

   如果您不需要使用消息队列 RocketMQ 服务，请及时删除消息队列 RocketMQ 控制台上所有资源，避免不必要的支出。

... 我有看到消息队列 RocketMQ 的扣费，但是我在控制台上看到没有开通消息队列 RocketMQ？
   消息队列 RocketMQ 处于欠费状态超过 72 小时，阿里云将暂停为您提供服务，
   即您不能再访问消息队列 RocketMQ 控制台与消息队列 RocketMQ API。
   但是您的消息队列 RocketMQ 服务被释放之前的欠费仍然需要结清。

... 如何关闭消息队列 RocketMQ 服务？
   请删除所有地域 Topic 资源，并将所有发送端，消费端停止。

... 一天的消息总量为 631,238 条，但是通过费用查询结果：API 调用次数为 126,315,056 次，那么多 API 调用次数怎么来的？
   API 调用次数 = 发送消息 API 调用次数 + 订阅消息 API 调用次数 + 长轮询 API 调用次数
   （长轮询说明：消息队列 RocketMQ Consumer 为保证消息实时推送而产生的 API 调用，
   每个队列 15 秒一次长轮询，在此期间，若队列内有消息产生，则不计长轮询次数）。

