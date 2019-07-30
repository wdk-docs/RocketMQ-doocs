HTTP 协议
================

消息队列 RocketMQ 的 HTTP 协议，支持公网访问能力以及 7 种多语言的支持。当前有以下地域（Region）支持 HTTP 协议：

- 公网
- 华东 1（杭州）
- 华东 2（上海）
- 华北 1（青岛）
- 华北 2（北京）
- 华南 1（深圳）
- 德国（法兰克福）

其他地域后续也将陆续支持 HTTP 协议，敬请期待。

语言支持
-----------------

消息队列 RocketMQ 支持 RESTful 风格的 HTTP 协议通信，并提供了以下 7 种语言的 SDK：

- Go
- Python
- Node.js
- PHP
- Java
- C++
- C#

SDK 和示例代码
-----------------

请到 `消息队列 RocketMQ HTTP SDK 库 <https://github.com/aliyunmq?spm=a2c4g.11186623.2.13.45cd4fb3Bu3UrK&tab=repositories%20&amp;amp;amp;quot;%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97%20RocketMQ%20HTTP%20SDK%20%E5%BA%93&amp;amp;amp;quot;>`_ 下载所需语言的 SDK、阅读 SDK 使用说明，以及查看消息收发的示例代码。

版本说明
-----------------

HTTP 协议所支持的功能通过不断迭代，与 TCP 协议所支持的功能逐渐对齐。
下文就 HTTP 协议的迭代版本所支持的功能对比 TCP 协议最新版的功能进行说明。

.. versionadded:: V1.0.1

发布日期：2019 年 6 月 10 日

消息队列 RocketMQ 的 HTTP SDK V1.0.1 的版本说明如下：

.. note:: Node.js 语言的 SDK 版本号为 V1.0.2，其它语言的 SDK 版本号均为 V1.0.1。

- 高级特性消息中已支持定时/延时消息（最长定时不能大于 3 天）和事务消息，暂不支持顺序消息；
- 已支持集群消费模式，不支持广播消费模式；
- 仅支持使用 Message ID（消息 ID）查询消息轨迹；
- TCP 和 HTTP 的 Group ID 不可以共用，需要分别创建。

.. versionadded:: V1.0.0

发布日期：2019 年 1 月 30 日

消息队列 RocketMQ 的 HTTP SDK V1.0.0 的版本说明如下：

.. note:: Node.js 语言的 SDK 版本号为 V1.0.1，其它语言的 SDK 版本号均为 V1.0.0。

- 暂不支持高级特性消息，包括顺序消息、事务消息、定时/延时消息；
- 已支持集群消费模式，不支持广播消费模式；
- 暂不支持查询消息轨迹；
- TCP 和 HTTP 的 Group ID 不可以共用，需要分别创建。
