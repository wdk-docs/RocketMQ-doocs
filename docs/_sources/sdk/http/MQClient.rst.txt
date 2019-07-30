客户端 MQClient
==================

  MQ的client,用于保存aliyun账号消息,以及发送http请求

.. js:class:: MQClient(endpoint, accessKeyId, accessKeySecret, securityToken)

   MQClient构造函数

   new MQClient(endpoint, accessKeyId, accessKeySecret, securityToken)

   :param string endpoint: MQ的HTTP接入地址
   :param string accessKeyId: 阿里云账号的AK
   :param string accessKeySecret: 阿里云账号的SK
   :param string securityToken: 阿里云RAM授权的STS TOKEN，可空

方法
------------

.. js:function:: MQClient.buildHeaders(method, body, resource)

   组装请求MQ需要的请求头

   :param string method: 请求方法
   :param string body: 请求内容
   :param string resource: HTTP请求URL的path
   :rtype: object
   :returns: headers

.. js:function:: MQClient.delete(resource, type, requestBody)

   发送HTTP DELETE请求

   :param string resource: HTTP请求URL的path
   :param string type: 解析XML响应内容的元素名字
   :param string requestBody: 请求的body
   :rtype: object
   :returns:

     .. code:: js

      {
      code: 200,
      requestId: "xxxxxxxxxxxxxx",
      body: {A:1,B:2,C:3}
      }

.. js:function:: MQClient.get(resource, type, opts)

   发送HTTP GET请求

   :param string resource: HTTP请求URL的path
   :param string type: 解析XML响应内容的元素名字
   :param object opts: 额外请求的参数
   :rtype: object
   :returns:

     .. code:: js

      {
      code: 200,
      requestId: "xxxxxxxxxxxxxx",
      body: {A:1,B:2,C:3}
      }


.. js:function:: MQClient.getConsumer(instanceId, topic, consumer, messageTag)

   构造一个MQ的消费者

   :param string instanceId: 实例ID
   :param string topic: 主题名字
   :param string consumer: 消费者名字
   :param string messageTag: 消费的过滤标签，可空
   :rtype: MQConsumer
   :returns:

.. js:function:: MQClient.getProducer(instanceId, topic)

   构造一个MQ的生产者

   :param string instanceId: 实例ID
   :param string topic: 主题名字
   :returns:
   :rtype: MQProducer

.. js:function:: MQClient.getTransProducer(instanceId, topic, groupId)

   构造一个MQ的事务消息生产者

   :param string instanceId: 实例ID
   :param string topic: 主题名字
   :param string groupId: 客户端GroupID
   :returns:
   :rtype: MQTransProducer

.. js:function:: MQClient.post(resource, type, requestBody)

   发送HTTP POST请求

   :param string resource: HTTP请求URL的path
   :param string type: 解析XML响应内容的元素名字
   :param string requestBody: 请求的body
   :returns:

     .. code:: js

       {
       code: 200,
       requestId: "xxxxxxxxxxxxxx",
       body: {A:1,B:2,C:3}
       }

   :rtype: object

.. js:function:: <async> MQClient.request(method, resource, type, requestBody, opts)

   发送http请求

   :param string method: HTTP的请求方法GET/PUT/POST/DELETE...
   :param string resource: HTTP请求URL的path
   :param string type: 解析XML响应内容的元素名字
   :param string requestBody: 请求的body
   :param object opts: 额外请求的参数
   :rtype: object
   :returns:

     .. code:: js

      {
        code: 200,
        requestId: "xxxxxxxxxxxxxx",
        body: {A:1,B:2,C:3}
      }


.. js:function:: MQClient.sign(method, headers, resource)

   对请求的内容按照MQ的HTTP协议签名,sha1+base64

   :param string method: 请求方法
   :param object headers: 请求头
   :param string resource: HTTP请求URL的path
   :rtype: string
   :returns: 签名
