应用内存不足
=================

现象
-------

在应用部署的机器上通过查看内存已消耗完。

在/{user.home}/logs/ons.log能搜索到 OutOfMemory 关键字。

用户侧反馈消息队列 RocketMQ 控制台：进入消费者管理 > 消费者状态，堆积量栏显示消息堆积较多，
连接状态栏显示了各个已连接客户端的消息堆积。通过 Jstack 排查， ConsumeMessageThread_ 线程无消费卡住现象。

分析
-------

消息队列 RocketMQ 的消费者会主动从 broker 拉取消息，先缓存至客户端本地，然后再将消息投放至业务的消费逻辑。

在 1.7.0.Final 版本之前，默认客户端最多会给每个 Topic 的每个队列缓存 1000 条消息。假设每个 Topic 的队列数是 16 个（集群 2 主 2 备，每台 broker 上 8 个队列），该 Topic 下单条消息平均大小为 64 KB，那么最终该 Topic 在客户端缓存的消息 Size：16 * 1000 * 64 KB = 1 GB。如果用户同时订阅了 8 个 Topic 都在客户端内存缓存消息，最终占用内存将超过用户的 JVM 配置，导致 OOM。

原因 1
~~~~~~~~~~~~~

依赖 1.7.0.Final 之前的 ons-client 版本, Topic 的平均消息大小超过 4 KB, 并且消息消费较慢容易在客户端内存缓存消息。

确认方式
^^^^^^^^^^^^^

在 /{user.home}/logs/ons.log 能搜索到 OutOfMemory 关键字；或者通过jmap -dump:live,format=b,file=heap.bin <pid>命令确认哪些对象占用了大量内存。

恢复方案
^^^^^^^^^^^^^

升级 ons-client 版本至 1.7.0.Final 或以上，同时给对应的 ConsumerBean 配合设置 com.aliyun.openservices.ons.api.PropertyKeyConst#MaxCachedMess ageSizeInMiB参数，然后重启应用。

原因 2
~~~~~~~~~~~~~

依赖 1.7.0.Final 及以上的 ons-client 版本，默认最大消耗内存 512 MB（Group ID 订阅的所有 Topic 缓存总和）；如果应用仍然出现 OOM 现象，可在 ConsumerBean 启动时，配置com.aliyun.openservices.ons.api.PropertyKeyConst#MaxCachedMessageSizeInMiB参数自行定义最大消耗内存（范围在 16 MB ~ 2048 MB）。

确认方式
^^^^^^^^^^^^^

确认应用依赖的 ons-client 版本，并通过 JVM 确认给进程分配的内存大小。

恢复方案
^^^^^^^^^^^^^

根据应用机器的内存使用清况给对应的 ConsumerBean 设置com.aliyun.openservices.ons.api.PropertyKeyConst#MaxCachedMessageSizeInMiB参数，然后重启应用。

验证
-------

- 在/{user.home}/logs/ons.log中 OutOfMemory 关键字不再出现。
- 登录消息队列 RocketMQ 控制台，进入消费者管理 > 消费者状态，看到消费 TPS 栏的值上升，同时堆积量的值下降。
