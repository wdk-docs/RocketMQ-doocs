消费者 MQConsumer
====================

MQ的消息消费者

.. js:class:: MQConsumer

   消费者构造函数

   new MQConsumer(client, instanceId, topic, consumer, messageTag)

   :param MQClient client: MQ客户端
   :param string instanceId: 实例ID
   :param string topic: 主题名字
   :param string consumer: 消费者名字(CID)a
   :param string messageTag: 消费消息的过滤标签，可空

方法
----------

.. js:function:: <async> MQConsumer.ackMessage(receiptHandles)

   确认消息消费成功，消费成功后需要调用该接口否则会重复消费消息

   :param array receiptHandles: 消息句柄数组
   :rtype: object
   :returns:

     .. code:: js

       {
         // 请求成功
         code:204,
         // 请求ID
         requestId:""
       }

   :throws exception: err 请求失败或者其它网络异常

     .. code:: js

       {
         // MQ服务端返回的错误Code，如ReceiptHandleError，表示消息句柄非法，MessageNotExist表示超过了ack的时间，即NextConsumeTime
         Code: ""
         // 请求ID
         RequestId: ""
       }




.. js:function:: <async> MQConsumer.consumeMessage(numOfMessages, waitSeconds)

   消费消息,默认如果该条消息没有被 {ackMessage} 确认消费成功，即在NextConsumeTime时会再次消费到该条消息

   :param int numOfMessages: 每次从服务端消费条消息
   :param int waitSeconds: 长轮询的等待时间（可空），如果服务端没有消息请求会在该时间之后返回等于请求阻塞在服务端，如果期间有消息立刻返回

   :rtype: object

   :returns:

     .. code:: js

       {
       code: 200,
       requestId: "",
       body: [
           {
             // 消息ID
             MessageId: "",
             // 消息体MD5
             MessageBodyMD5: "",
             // 发送消息的时间戳，毫秒
             PublishTime: {long},
             // 下次重试消费的时间，前提是这次不调用{ackMessage} 确认消费消费成功，毫秒
             NextConsumeTime: {long},
             // 第一次消费的时间，毫秒
             FirstConsumeTime: {long},
             // 消费的次数
             ConsumedTimes: {long},
             // 消息句柄，调用 {ackMessage} 需要将消息句柄传入，用于确认该条消息消费成功
             ReceiptHandle: "",
             // 消息内容
             MessageBody: "",
             // 消息标签
             MessageTag: ""
           }
         ]
       }

   :throws exception: err MQ服务端返回的错误或者其它网络异常

     .. code:: js

       {
         // MQ服务端返回的错误Code，其中MessageNotExist是正常现象，表示没有可消费的消息
         Code: "",
         // 请求ID
         RequestId: ""
       }

