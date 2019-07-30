事务生产者 MQTransProducer
===========================

MQ的消息生产者，支持事务

.. js:class:: MQTransProducer

   构造函数

   new MQTransProducer(client, instanceId, topic, groupId)

   :param MQClient client: MQ的客户端
   :param string instanceId:  实例ID
   :param string topic:  主题名字
   :param string groupId:  客户端GroupID

方法
------------

.. js:function:: <async> MQTransProducer.commit(receiptHandle)

   提交事务消息

   :param string receiptHandle:  consumeHalfMessage返回的单条消息句柄或者是发送事务消息返回的句柄
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
         // MQ服务端返回的错误Code，如ReceiptHandleError，表示消息句柄非法，MessageNotExist如果超过了TransCheckImmunityTime（针对发送事务消息的句柄）或者超过NextCnosumeTime
         Code: ""
         // 请求ID
         RequestId: ""
       }

.. js:function:: <async> MQTransProducer.consumeHalfMessage(numOfMessages, waitSeconds)

   消费检查事务半消息,默认如果该条消息没有被 {commit} 或者 {rollback} 在NextConsumeTime时会再次消费到该条消息

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
           // 下次重试消费的时间，前提是这次不调用{commit} 或者 {rollback}，毫秒
           NextConsumeTime: {long},
           // 第一次消费的时间，毫秒
           FirstConsumeTime: {long},
           // 消费的次数
           ConsumedTimes: {long},
           // 消息句柄，调用 {commit} 或者 {rollback} 需要将消息句柄传入，用于提交或者回滚该条事务消息
           ReceiptHandle: "",
           // 消息内容
           MessageBody: "",
           // 消息标签
           MessageTag: ""
         }
       ]}

   :throws exception: err MQ服务端返回的错误或者其它网络异常

     .. code:: js

       {
         // MQ服务端返回的错误Code，其中MessageNotExist是正常现象，表示没有可消费的消息
         Code: "",
         // 请求ID
         RequestId: ""
       }

.. js:function:: <async> MQTransProducer.publishMessage(body, tag, msgProps)

   向主题发送一条消息

   :param string body:  发送的内容
   :param string tag:  发送消息的标签
   :param MessageProperties msgProps: 发送消息的属性
   :rtype: object
   :returns:

     .. code:: js

       {
         // http请求状态码，发送成功就是201，如果发送失败则抛异常
         code: 201,
         // 请求ID
         requestId: "xxxxxxxxxxxxxx",
         // 发送消息的响应内容
         body: {
           // 消息ID
           MessageId: "",
           // 消息体内容的MD5值
           MessageBodyMD5: ""
           // 消息句柄，仅事务消息存在
           ReceiptHandle: ""
         }
       }

   :throws exception: err MQ服务端返回的错误或者其它网络异常

     .. code:: js

       {
         // MQ服务端返回的错误Code，like: TopicNotExist
         Code:"",
         // 请求ID
         RequestId:""
       }

.. js:function:: <async> MQTransProducer.rollback(receiptHandle)

   回滚事务消息

   :param string receiptHandle: MQTransProducer.consumeHalfMessage返回的单条消息句柄或者是发送事务消息返回的句柄
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
         // MQ服务端返回的错误Code，如ReceiptHandleError，表示消息句柄非法，MessageNotExist如果超过了TransCheckImmunityTime（针对发送事务消息的句柄）或者超过NextCnosumeTime
         Code: ""
         // 请求ID
         RequestId: ""
       }
