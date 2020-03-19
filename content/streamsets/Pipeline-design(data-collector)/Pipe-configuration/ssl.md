# SSL / TLS配置

某些阶段允许您使用SSL / TLS安全地连接到外部系统。

启用TLS时，通常可以在阶段的“ TLS”选项卡上配置属性。可用的属性可能取决于您正在配置的阶段。TLS选项卡可以包含以下属性：

- 密钥库属性
- 信任库属性
- TLS协议
- 密码套件

**注意：**您还可以为Data Collector启用HTTPS，以确保与Data Collector UI和REST API 的通信安全。而且，您可以为群集管道启用HTTPS，以保护群集中网关和工作节点之间的通信。有关更多信息，请参阅[启用HTTPS](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/HTTP_protocols.html#concept_xyp_lt4_cw)。

您可以在以下阶段和位置启用SSL / TLS类型属性：

- 卡桑德拉目的地
- Couchbase查找处理器和Couchbase目标
- Databricks执行器
- gRPC客户端来源
- HTTP客户端的来源，处理器和目标
- HTTP服务器来源
- HTTP到Kafka的起源
- Kafka使用者来源，Kafka Multitopic使用者来源和Kafka生产者目的地-这些阶段需要配置其他Kafka属性。有关更多信息，请参阅阶段文档中的“启用安全性”。
- MongoDB原始和目标，MongoDB Oplog原始和MongoDB查找处理器-这些阶段需要配置SDC_JAVA_OPTS环境变量。有关更多信息，请参阅阶段文档中的“启用SSL / TLS”。
- MQTT订户来源和MQTT发布者目的地
- OPC UA客户端来源
- Pulsar消费者来源和Pulsar生产者目的地-这些阶段需要证书文件，而不是密钥库和信任库文件。有关更多信息，请参阅阶段文档中的“启用安全性”。
- RabbitMQ消费者来源和RabbitMQ生产者目的地
- REST服务来源
- Salesforce的原点，查找和目标以及Einstein Analytics目标
- SDC RPC的来源和目的地
- SDC RPC到Kafka的起源
- SFTP / FTP / FTPS客户端来源和目的地
- Splunk目的地
- Syslog目标-此目标需要配置SDC_JAVA_OPTS环境变量。有关更多信息，请参阅目标文档中的“ [启用SSL / TLS](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Syslog.html#task_fcg_q1k_ffb) ”。
- TCP服务器来源
- UDP到Kafka的起源
- WebSocket客户端的来源和目的地
- WebSocket服务器的起源
- 将错误记录写入另一个管道时，管道错误处理

## 密钥库和信任库配置

在阶段中启用SSL / TLS时，您还可以启用密钥库和信任库的使用。

尽管在很多方面都相似，但是密钥库包含一个私钥和公共证书，用于根据SSL / TLS服务器的请求来验证客户端的身份。相反，信任库通常包含来自受信任的证书颁发机构的证书，SSL / TLS客户端使用这些证书来验证SSL / TLS服务器的身份。

**重要：**在阶段中启用SSL / TLS之前，请将密钥库和信任库文件存储在Data Collector 或Data Collector Edge计算机上。

配置密钥库或信任库时，可以配置以下属性：

- 密钥库/信任库类型

  您可以使用以下类型的密钥库和信任库：Java密钥库文件（JKS）PKCS＃12（p12文件）

  ![img](imgs/icon-Edge-20200310110915209.png)在Data Collector Edge管道中，密钥库和信任库文件必须使用PEM格式。

- 文件和位置

  指定密钥库或信任库文件的文件和位置时，可以使用文件的绝对路径，也可以使用相对于Data Collector资源目录的路径 。

  ![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中，使用文件的绝对路径。

- 密码

  密钥库和信任库文件的密码是可选的，但强烈建议使用。

  ![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中无效。在Data Collector Edge管道中，阶段将忽略密钥库或信任库的password属性。

- 算法

  默认情况下，Data Collector使用SunX509密钥交换算法。您可以使用与JVM支持的与密钥库/信任库文件兼容的任何算法。

  ![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中无效。在Data Collector Edge管道中，阶段将忽略密钥库或信任库的算法属性。

## 传输协议

在阶段中启用SSL / TLS时，可以配置要使用的传输协议。

默认情况下，Data Collector 使用TLSv1.2。您可以指定一个或多个其他协议，但是TLSv1.2之前的版本不那么安全。

![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中，阶段仅支持TLSv1.2协议。

## 密码套房

在阶段中启用SSL / TLS时，您可以配置密码套件以用于执行SSL / TLS握手。

默认情况下，阶段可以使用以下任何密码套件：

| 支持的密码套件                | Java安全套接字扩展（JSSE）名称          |
| :---------------------------- | :-------------------------------------- |
| ECDHE-ECDSA-AES256-GCM-SHA384 | TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
| ECDHE-RSA-AES256-GCM-SHA384   | TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384   |
| ECDHE-ECDSA-AES128-GCM-SHA256 | TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
| ECDHE-RSA-AES128-GCM-SHA256   | TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256   |
| ECDHE-ECDSA-AES256-SHA384     | TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
| ECDHE-RSA-AES256-SHA384       | TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384   |
| ECDHE-ECDSA-AES128-SHA256     | TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
| ECDHE-RSA-AES128-SHA256       | TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256   |