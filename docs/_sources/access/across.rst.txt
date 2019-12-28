跨云账号授权
====================

使用企业 A 的阿里云账号（主账号）创建 RAM 角色并为该角色授权，并将该角色赋予企业 B，即可实现使用企业 B 的主账号或其 RAM 用户（子账号）访问企业 A 的阿里云资源的目的。

应用场景
-------------

企业 A 购买了消息队列 RocketMQ 服务来开展业务，并希望将部分业务授权给企业 B。

需求说明：

A 希望能专注于业务系统，仅作为资源 Owner；而消息发布和订阅等任务委托或授权给企业 B。

企业 A 希望当企业 B 的员工加入或离职时，无需做任何权限变更。企业 B 可以进一步将 A 的资源访问权限分配给 B 的 RAM 用户（员工或应用），并可以精细控制其员工或应用对资源的访问和操作权限。

企业 A 希望如果双方合同终止，企业 A 随时可以撤销对企业 B 的授权。

操作步骤
-------------

首先需要使用企业 A 的阿里云账号（主账号）登录 RAM 控制台并为企业 B 的云账号创建 RAM 角色。

具体步骤参见创建可信实体为阿里云账号的 RAM 角色。

（可选）企业 A 为刚创建的 RAM 角色创建自定义策略。

具体步骤参见创建自定义策略。

目前，消息队列 RocketMQ 支持实例、Topic 和 Group 粒度的权限设置。详情参见权限策略。

新创建的角色没有任何权限，因此企业 A 必须为该角色添加权限。可添加系统权限策略或自定义权限策略。

具体步骤参见为 RAM 角色授权。

使用企业 B 的阿里云账号（主账号）登录 RAM 控制台并创建 RAM 用户。

具体步骤参见企业 B 创建 RAM 用户

企业 B 为 RAM 用户添加 AliyunSTSAssumeRoleAccess 权限。

具体步骤参见为 RAM 用户授权。

企业 B 必须为其主账号下的 RAM 用户添加 AliyunSTSAssumeRoleAccess 权限，RAM 用户才能扮演企业 A 创建的 RAM 角色。

企业 B 的 RAM 用户通过控制台或 API 访问企业 A 的资源。

具体步骤参见后续步骤。

后续步骤
-------------

完成上述操作后，企业 B 的 RAM 用户即可按照以下步骤登录控制台访问企业 A 的云资源或调用 API。

登录控制台访问企业 A 的云资源

在浏览器中打开 RAM 用户登录入口 https://signin.aliyun.com/login.htm。

在 RAM 用户登录页面上，输入 RAM 用户登录名称，单击下一步，并输入 RAM 用户密码，然后单击登录。

说明：RAM 用户登录名称的格式为 <$username>@<$AccountAlias> 或 <$username>@<$AccountAlias>.onaliyun.com。 <$AccountAlias> 为账号别名，如果没有设置账号别名，则默认值为阿里云账号（主账号）的 ID。

在子用户用户中心页面上，将鼠标指针移到右上角头像，并在浮层中单击切换身份。

在阿里云－角色切换页面，输入企业 A 的企业别名或默认域名，以及角色名，然后单击切换。

对企业 A 的阿里云资源执行操作。

使用企业 B 的 RAM 用户通过 API 访问企业 A 的云资源

要使用企业 B 的 RAM 用户通过 API 访问企业 A 的云资源，必须在代码中提供 RAM 用户的 AccessKeyId、AccessKeySecret 和 SecurityToken（临时安全令牌）。使用 STS 获取临时安全令牌的方法参见 STS 使用入门。

STS 在消息队列 RocketMQ 中的使用
注意：STS 功能只在消息队列 RocketMQ 的 Java SDK 1.7.8.Final 版本及以上支持。

使用消息队列 RocketMQ 的 SDK 收发消息时，您只需将获取到的 AccessKey 和 Token 填入到以下属性中即可：

.. code::

  Properties properties = new Properties();
  ......
  // STS 的 AccessKeyId
  properties.put(PropertyKeyConst.AccessKey,"XXX");
  // STS 的 AccessKeySecret
  properties.put(PropertyKeyConst.SecretKey, "XXX");
  // STS 的 SecurityToken
  properties.put(PropertyKeyConst.SecurityToken, "XXX");
  ......

更多信息
-------------

RAM 主子账号授权

权限策略概述

什么是 RAM

基本概念

创建自定义策略

创建 RAM 用户

为 RAM 用户授权
