STOMP 协议
==================

STOMP 协议是一个精简的消息订阅发布协议，类似于 HTTP 的纯文本的协议机制，常用于脚本语言（如 Ruby、Python、Perl）和消息队列 RocketMQ Broker 进行轻量级交互。

名词解释
-----------------

- **Topic**：即消息队列 RocketMQ 的消息主题，一级消息类型，通过 Topic 对消息进行分类以及权限管理。
- **Destination**：STOMP 协议里面的消息目的地，形式如“/t/t1”，其中一级字符串“t”即为 Topic。
- **Instance ID**：消息队列 RocketMQ 的实例 ID。
- **Client ID**：发送端或订阅端的唯一标识，便于日志查询。

更多名词解释信息，请参见消息队列 RocketMQ 的名词解释。

快速入门
-----------------

Destination 配置
~~~~~~~~~~~~~~~~~~~~~~~~

STOMP 协议中 Destination 的一级字符串即为消息队列 RocketMQ 中的 Topic，是消息的一级归类。

权限认证
~~~~~~~~~~~~~~~~~~~~~~~~

身份认证
^^^^^^^^^^^^^^^

客户端和消息队列 RocketMQ Broker 建立一个 STOMP 连接后，Broker 需要对客户端的身份进行验证，
主要验证客户的 AccessKey 和签名的正确性。其中，AccessKey 和签名分别对应 STOMP 协议头（Header）里面的 login、passcode。

passcode 生成规则：
^^^^^^^^^^^^^^^^^^^

用 AccessKeySecret 作为秘钥，使用 HmacSHA1 方法对 Client ID 做签名计算得到一个二进制数组，
最后对该二进制数组做 Base64 编码得到最终的签名字符串 passcode。

Topic 权限
~~~~~~~~~~~~~~~~~~~~~~~~

STOMP 协议里面的发布和订阅涉及到的 Destination，其包括的一级字符串即 Topic 需要客户端有相应的权限。
当客户端发送消息的时候需要有相应 Topic 的 PUB 权限，当客户端订阅或取消订阅的时候需要有相应 Topic 的 SUB 权限。
关于 Topic 权限的申请和授权，请参见消息队列 RocketMQ 的 RAM 子账号授权。

接入点
-----------------

接入点，即 STOMP 协议客户端欲连接的远端 Broker 的地址和端口。
目前仅开放新加坡地域（Region）的内网接入服务，具体接入点如下：

+----------+-------------------------------------------+------+
|  网络类  |                   域名                    | 端口 |
+==========+===========================================+======+
| 经典网络 | classic.stomp.ap-southeast-1.aliyuncs.com | 8080 |
+----------+-------------------------------------------+------+
| VPC网络  | vpc.stomp.ap-southeast-1.aliyuncs.com     | 8080 |
+----------+-------------------------------------------+------+

使用限制
-----------------

- 当前只在新加坡地域开放，其它地域将会陆续开放，敬请期待；
- 每个地域接入点只能在本地域内访问，不能跨地域使用；
- 暂时不支持 STOMP 消息的持久化，即客户端断线重连后不会收到之前的消息；
- 关于 STOMP 协议里面的事务（Transaction）特性，一个客户端连接里同时执行的事务不能超过一条，且一个事务里面不能超过三条消息，连接断开后如果事务没有提交将被自动回滚；
- 暂不支持 SSL 的连接；
- 关于 STOMP 协议版本，目前兼容 1.0、1.1、1.2 的客户端连接；
- 不支持 Selector 功能；
- Destination 的字符串限制为：数字、字母、中划线、下划线。

开发指南
-----------------

STOMP 客户端有很多开源的实现，详情参考，本文仅基于开源的 Python 客户端 stompest 进行消息收发的代码示例。

订阅端示例代码：

.. code:: py

    import base64
    import hmac
    from hashlib import sha1
    from stompest.config import StompConfig
    from stompest.protocol import StompSpec
    from stompest.sync import Stomp
    QUEUE = '/xx/xxx'
    accessKey = 'xxxx'
    secretKey = 'xxx'
    clientId = 'recv_01'
    brokerUrl = 'tcp://xxxxxx:8080'
    passcode = base64.b64encode(hmac.new(secretKey.encode(), clientId.encode(), sha1).digest()).decode()
    CONFIG = StompConfig(brokerUrl, accessKey, passcode, StompSpec.VERSION_1_2)
    if __name__ == '__main__':
        client = Stomp(CONFIG)
        client.connect(
            {
                'client-id': clientId,
                'instance-id': 'xxxxx',
            })
        client.subscribe(QUEUE,
                        {
                            StompSpec.ACK_HEADER: StompSpec.ACK_CLIENT_INDIVIDUAL,
                            StompSpec.ID_HEADER: '001',
                            StompSpec.RECEIPT_HEADER: "R001"
                        })
        frame = client.receiveFrame()
        print('recv: %s' % frame.info())
        while True:
            frame = client.receiveFrame()
            print('recv: %s' % frame.info())
            client.ack(frame)
        client.disconnect()

发送端示例代码：

.. code:: py

    import base64
    import hmac
    import time
    from hashlib import sha1
    from stompest.config import StompConfig
    from stompest.protocol import StompSpec
    from stompest.sync import Stomp
    QUEUE = '/xx/xxx'
    accessKey = 'xxxxx'
    secretKey = 'xxxxx'
    clientId = 'send_01'
    brokerUrl = 'tcp://xxxx:8080'
    passcode = base64.b64encode(hmac.new(secretKey.encode(), clientId.encode(), sha1).digest()).decode()
    CONFIG = StompConfig(brokerUrl, accessKey, passcode, StompSpec.VERSION_1_2)
    if __name__ == '__main__':
        client = Stomp(CONFIG)
        client.connect(
            {
                'client-id': clientId,
                'instance-id': 'xxxxx'
            })
        for i in range(1, 100):
            msg = 'm_' + str(i)
            print('send :' + msg)
            client.send(QUEUE, msg.encode(), {StompSpec.RECEIPT_HEADER: str(i)})
            frame = client.receiveFrame()
            print('resp: %s' % frame.info())
            time.sleep(1)
