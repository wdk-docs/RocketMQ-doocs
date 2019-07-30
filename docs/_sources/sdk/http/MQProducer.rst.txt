生产者 MQProducer
====================

MQ的消息生产者

.. js:class:: MQProducer

   构造函数 new MQProducer(client, instanceId, topic)

   :param MQClient client: MQ的客户端
   :param String instanceId: 实例ID
   :param String topic: 主题名字

方法
------------

.. js:function:: <async> publishMessage(body, tag, msgProps)

   向主题发送一条消息

   :param String body: 发送的内容
   :param String tag: 发送消息的标签
   :param MessageProperties msgProps: 发送消息的属性
   :rtype: object
   :returns:

     .. code:: json

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
         }
       }

   :throws exception: err MQ服务端返回的错误或者其它网络异常

     .. code:: json

       {
         // MQ服务端返回的错误Code，like: TopicNotExist
         Code:"",
         // 请求ID
         RequestId:""
       }

