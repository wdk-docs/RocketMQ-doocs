MQ Nodejs HTTP SDK
=========================

阿里云MQ文件: http://www.aliyun.com/product/ons

阿里云MQ控制台: https://ons.console.aliyun.com

安装
------------------

添加依赖 ``@aliyunmq/mq-http-sdk`` ，从npmjs获取最新版本

.. code:: sh

   npm install @aliyunmq/mq-http-sdk --save

.. note:: nodejs >= 7.6.0

文档
------------------

`文档在这里 <https://aliyunmq.github.io/mq-http-nodejs-sdk/>`_

示例
------------------

发布消息
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: js

  const {
    MQClient
  } = require('@aliyunmq/mq-http-sdk');

  // 设置HTTP接入域名（此处以公共云生产环境为例）
  const endpoint = "${HTTP_ENDPOINT}";
  // AccessKey 阿里云身份验证，在阿里云服务器管理控制台创建
  const accessKeyId = "${ACCESS_KEY}";
  // SecretKey 阿里云身份验证，在阿里云服务器管理控制台创建
  const accessKeySecret = "${SECRET_KEY}";

  var client = new MQClient(endpoint, accessKeyId, accessKeySecret);

  // 所属的 Topic
  const topic = "${TOPIC}";
  // Topic所属实例ID，默认实例为空
  const instanceId = "${INSTANCE_ID}";

  const producer = client.getProducer(instanceId, topic);

  (async function(){
    try {
      // 循环发送100条消息
      for(var i = 0; i < 50; i++) {
        let res;
        res = await producer.publishMessage("hello mq.");
        console.log("Publish message: MessageID:%s,BodyMD5:%s", res.body.MessageId, res.body.MessageBodyMD5);
        res = await producer.publishMessage("hello mq.", "tag");
        console.log("Publish message: MessageID:%s,BodyMD5:%s", res.body.MessageId, res.body.MessageBodyMD5);
      }

    } catch(e) {
      // 消息发送失败，需要进行重试处理，可重新发送这条消息或持久化这条数据进行补偿处理
      console.log(e)
    }
  })();

消费消息
~~~~~~~~~~~~~~~~~~

.. code:: js

  const {
    MQClient
  } = require('@aliyunmq/mq-http-sdk');

  // 设置HTTP接入域名（此处以公共云生产环境为例）
  const endpoint = "${HTTP_ENDPOINT}";
  // AccessKey 阿里云身份验证，在阿里云服务器管理控制台创建
  const accessKeyId = "${ACCESS_KEY}";
  // SecretKey 阿里云身份验证，在阿里云服务器管理控制台创建
  const accessKeySecret = "${SECRET_KEY}";

  var client = new MQClient(endpoint, accessKeyId, accessKeySecret);

  // 所属的 Topic
  const topic = "${TOPIC}";
  // 您在控制台创建的 Consumer ID(Group ID)
  const groupId = "${GROUP_ID}";
  // Topic所属实例ID，默认实例为空
  const instanceId = "${INSTANCE_ID}";

  const consumer = client.getConsumer(instanceId, topic, groupId);

  (async function(){
    // 循环消费消息
    while(true) {
      try {
        // 长轮询消费消息
        // 长轮询表示如果topic没有消息则请求会在服务端挂住3s，3s内如果有消息可以消费则立即返回
        res = await consumer.consumeMessage(
            3, // 一次最多消费3条(最多可设置为16条)
            3 // 长轮询时间3秒（最多可设置为30秒）
            );

        if (res.code == 200) {
          // 消费消息，处理业务逻辑
          console.log("Consume Messages, requestId:%s", res.requestId);
          const handles = res.body.map((message) => {
            console.log("\tMessageId:%s,Tag:%s,PublishTime:%d,NextConsumeTime:%d,FirstConsumeTime:%d,ConsumedTimes:%d,Body:%s",
                message.MessageId, message.MessageTag, message.PublishTime, message.NextConsumeTime, message.FirstConsumeTime, message.ConsumedTimes,
                message.MessageBody);
            return message.ReceiptHandle;
          });

          // message.NextConsumeTime前若不确认消息消费成功，则消息会重复消费
          // 消息句柄有时间戳，同一条消息每次消费拿到的都不一样
          res = await consumer.ackMessage(handles);
          if (res.code != 204) {
            // 某些消息的句柄可能超时了会导致确认不成功
            console.log("Ack Message Fail:");
            const failHandles = res.body.map((error)=>{
              console.log("\tErrorHandle:%s, Code:%s, Reason:%s\n", error.ReceiptHandle, error.ErrorCode, error.ErrorMessage);
              return error.ReceiptHandle;
            });
            handles.forEach((handle)=>{
              if (failHandles.indexOf(handle) < 0) {
                console.log("\tSucHandle:%s\n", handle);
              }
            });
          } else {
            // 消息确认消费成功
            console.log("Ack Message suc, RequestId:%s\n\t", res.requestId, handles.join(','));
          }
        }
      } catch(e) {
        if (e.Code.indexOf("MessageNotExist") > -1) {
          // 没有消息，则继续长轮询服务器
          console.log("Consume Message: no new message, RequestId:%s, Code:%s", e.RequestId, e.Code);
        } else {
          console.log(e);
        }
      }
    }
  })();
