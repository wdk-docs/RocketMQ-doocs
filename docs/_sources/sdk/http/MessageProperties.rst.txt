消息属性 MessageProperties
=================================

.. js:class:: MessageProperties

   消息属性 new MessageProperties()

方法
------------

.. js:function:: MessageProperties.getProperties()

   获取消息属性内部的Object

   :rtype: Object

.. js:function:: MessageProperties.messageKey(key)

   设置消息KEY

   :param stirng key: 消息KEY

.. js:function:: MessageProperties.putProperty(key, value)

   设置消息自定义属性

   :param string key: 属性键,非空
   :param string value: 属性值,非空

.. js:function:: MessageProperties.startDeliverTime(timeMillis)

   定时消息，单位毫秒（ms），在指定时间戳（当前时间之后）进行投递。
   如果被设置成当前时间戳之前的某个时刻，消息将立刻投递给消费者

   :param Number timeMillis: 定时的绝对时间戳

.. js:function:: MessageProperties.transCheckImmunityTime(timeSeconds)

   在消息属性中添加第一次消息回查的最快时间，单位秒，并且表征这是一条事务消息

   :param Number timeSeconds: 第一次消息回查时间，单位秒
