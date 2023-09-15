# MongoDB Oplog

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310172941812.png) 资料收集器

MongoDB Oplog来源从MongoDB Oplog读取条目。

MongoDB将有关数据库更改的信息存储在称为Oplog的本地限定集合中。Oplog包含有关数据更改以及数据库更改的信息。MongoDB Oplog源可以读取任何写入Oplog的操作。

使用MongoDB Oplog来源捕获数据或数据库中的更改。要处理写入标准有上限或无上限集合的MongoDB数据，请使用[MongoDB origin](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDB.html#concept_bk4_2rs_ns)。

MongoDB Oplog源在记录头属性中包括CRUD操作类型，因此生成的记录可以由启用CRUD的目标轻松处理。有关Data Collector更改的数据处理的概述以及启用CRUD的目标的列表，请参阅[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

配置MongoDB Oplog来源时，将配置连接信息，例如连接字符串和MongoDB凭据。您还定义了可选的时间戳记和顺序号，以指定从何处开始读取，要处理的操作以及读取首选项。

您可以选择配置高级选项，这些选项可确定源如何连接到MongoDB，包括启用SSL / TLS。

**注意：** StreamSets已使用MongoDB 4.0测试了此阶段。

## 证书

根据MongoDB服务器使用的身份验证，将源配置为不使用身份验证，用户名/密码身份验证或LDAP身份验证。使用用户名/密码身份验证时，还可以使用委托身份验证。

默认情况下，源不使用身份验证。

要使用用户名/密码或LDAP认证，请通过以下方式之一输入所需的凭据：

- MongoDB选项卡中的连接字符串

  在MongoDB选项卡的连接字符串中输入凭据。

  要输入用于用户名/密码身份验证的凭据，请在主机名之前输入用户名和密码。使用以下格式：`mongodb://**username:password@**host[:port][/[database][?options]]`

  要输入用于LDAP身份验证的凭据，请在主机名之前输入用户名和密码，并将authMechanism选项设置为PLAIN。使用以下格式：`mongodb://**username:password@**host[:port][/[database]**?authMechanism=PLAIN**`

- 凭据选项卡

  在“凭据”选项卡中选择“用户名/密码”或“ LDAP身份验证”类型。然后输入身份验证类型的用户名和密码。

如果同时在连接字符串和“凭据”选项卡中输入凭据，则“凭据”选项卡优先。

## Oplog时间戳和顺序

当您启动管道时，默认情况下，MongoDB Oplog起点从Oplog的开头开始读取。您可以配置时间戳记和顺序来指定要在哪里开始处理。

MongoDB Oplog条目包含一个名为“ ts”的时间戳字段，该字段由时间戳和顺序组成，如下所示：

```
"ts": Timestamp(<timestamp>, <ordinal>)
```

时间戳格式是自Unix时代以来的秒数，例如1412180887。

序数是一个整数计数器，用于区分同一秒内出现的条目。

您可以使用时间戳记和序数来指定从Oplog开始读取的位置。使用时间戳记时，还必须定义一个序数。

有关Oplog时间戳字段的更多信息，请参阅MongoDB文档。

## 阅读偏好

您可以配置MongoDB Oplog来源使用的读取首选项。

读取首选项确定来源如何从MongoDB副本集的不同成员读取数据。

您可以使用以下MongoDB读取首选项：

- 主要-要求主要成员阅读。
- 首选首选-首选从首选读取，但允许从二级读取。
- 二级-需要二级成员阅读。
- 首选二级-首选从二级读取，但在必要时允许从一级读取。
- 最近-从成员那里读取的网络延迟最少。

默认情况下，源使用“首选优先级”以避免对主要成员进行不必要的请求。

## 生成的记录

MongoDB Oplog起源基于MongoDB Oplog的数据生成记录，并添加与CRUD和CDC相关的记录头属性。

Oplog记录的结构是唯一的，因此在必要时，您可以在管道中使用某些处理器来转换记录结构。

例如，对于插入记录，记录数据驻留在名为“ o”的映射字段中。但是对于更新记录，_id字段是o2 map字段的一部分。要合并记录数据，可以使用Field Flattener展平地图字段，并使用Field Remover删除任何不必要的字段。

有关Oplog记录结构的更多信息，请参阅MongoDB文档。以下站点也是一个不错的资源：[https](https://www.compose.com/articles/the-mongodb-oplog-and-node-js/) : [//www.compose.com/articles/the-mongodb-oplog-and-node-js/](https://www.compose.com/articles/the-mongodb-oplog-and-node-js/)。

### CRUD操作和CDC标头属性

MongoDB Oplog的起源在sdc.operation.type记录标题属性中包括CRUD操作类型。

如果您在诸如JDBC Producer或Elasticsearch之类的管道中使用启用CRUD的目标，则该目标可以在写入目标系统时使用操作类型。必要时，可以使用表达式评估器或脚本处理器来处理`sdc.operation.type`header属性中的值 。有关Data Collector更改的数据处理的概述以及启用CRUD的目标的列表，请参阅[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

MongoDB Oplog起源在sdc.operation.type记录标头属性中使用以下值表示操作类型：

- INSERT为1
- 2个代表删除
- 3更新
- 5，用于不支持的操作，例如CMD，NOOP或DB，它们是可用的MongoDB操作类型，但不适用于记录数据。

当信息相关时，MongoDB Oplog来源还可以包括以下标头属性：

- op-使用以下值进行CRUD操作：
  - 我为插入
  - 你更新
  - d代表删除
- NS -命名空间，使用以下格式： `:`。

## 启用SSL / TLS

您可以启用MongoDB Oplog源以使用SSL / TLS连接到MongoDB。

1. 在该阶段的“ **高级”**选项卡中，选择“ **启用SSL”**属性。

2. 如果MongoDB证书由私有CA签名或不受默认Java信任库信任，请创建一个自定义信任库文件或修改默认Java信任库文件的副本以将CA添加到该文件中。然后配置数据收集器以使用修改后的信任库文件。

   默认情况下，Data Collector 使用$ JAVA_HOME / jre / lib / security / cacerts中的Java信任库文件 。如果您的证书是由默认Java信任库文件中包含的CA签名的，则无需创建信任库文件，可以跳过此步骤。

   在这些步骤中，我们展示了如何修改默认的信任库文件，以将其他CA添加到受信任的CA列表中。如果您希望创建自定义信任库文件，请参阅[keytool文档](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html)。

   **注意：**如果已经将Data Collector配置为使用自定义信任库文件来[启用HTTPS](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/HTTP_protocols.html#concept_xyp_lt4_cw)或[到LDAP服务器的安全连接](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/LDAP-Step2Secure.html#task_wyf_kkw_ty)，则只需将此附加CA添加到相同的修改后的信任库文件中即可。

   1. 使用以下命令来设置JAVA_HOME环境变量：

      ```
      export JAVA_HOME=<Java home directory>
      ```

   2. 使用以下命令来设置SDC_CONF环境变量：

      ```
      export SDC_CONF=<Data Collector configuration directory>
      ```

      例如，对于RPM安装，请使用：

      ```
      export SDC_CONF=/etc/sdc
      ```

   3. 使用以下命令将默认的Java truststore文件复制到Data Collector 配置目录：

      ```
      cp "${JAVA_HOME}/jre/lib/security/cacerts" "${SDC_CONF}/truststore.jks"
      ```

   4. 使用以下keytool命令将CA证书导入到信任库文件中：

      ```
      keytool -import -file <MongoDB certificate> -trustcacerts -noprompt -alias <MongoDB alias> -storepass <password> -keystore "${SDC_CONF}/truststore.jks"
      ```

      例如：

      ```
      keytool -import -file  myMongoDB.pem -trustcacerts -noprompt -alias MyMongoDB -storepass changeit -keystore "${SDC_CONF}/truststore.jks"
      ```

   5. 在SDC_JAVA_OPTS环境变量中定义以下选项：

      - javax.net.ssl.trustStore- 数据收集器 计算机上信任库文件的路径。
      - javax.net.ssl.trustStorePassword -信任库密码。

      使用安装类型所需的方法。

      例如，如下定义选项：

      ```
      export SDC_JAVA_OPTS="${SDC_JAVA_OPTS} -Djavax.net.ssl.trustStore=/etc/sdc/truststore.jks -Djavax.net.ssl.trustStorePassword=mypassword -Xmx1024m -Xms1024m -server -XX:-OmitStackTraceInFastThrow"
      ```

      或者，为避免在导出命令中保存密码，请将密码保存在文本文件中，然后按如下所示定义truststore password选项： -Djavax.net.ssl.trustStorePassword = $（cat passwordfile.txt）

      然后，确保密码文件仅可由执行导出命令的用户读取。

   6. 重新启动Data Collector 以启用更改。

## 配置MongoDB Oplog来源

配置MongoDB Oplog源以从MongoDB Oplog读取数据。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **MongoDB”**选项卡上，配置以下属性：

   | MongoDB属性                                                  | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 连接字符串                                                   | MongoDB实例的连接字符串。使用以下格式：`mongodb://host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]`连接到集群时，输入其他节点信息以确保连接。如果MongoDB服务器使用用户名/密码或LDAP身份验证，可以在连接字符串中的凭据，如在[凭证](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDBOplog.html#concept_ovt_vpt_tz)。 |
   | 启用单模                                                     | 选择以连接到单个MongoDB服务器或节点。如果在连接字符串中定义了多个节点，则该阶段仅连接到第一个节点。请谨慎使用此选项。如果阶段无法连接或连接失败，则管道将停止。 |
   | 采集                                                         | 要使用的MongoDB Oplog集合的名称，通常是 oplog.rs。集合名称必须以“ oplog”开头。 |
   | 初始时间戳 [![img](imgs/icon_moreInfo-20200310172942334.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDBOplog.html#concept_iry_ykm_sy) | Oplog `ts`时间戳字段中的时间戳开始读取数据。使用MongoDB时间戳格式，即Unix时代以来的秒数。有关Oplog时间戳的更多信息，请参阅MongoDB文档。使用-1退出此属性，并从Oplog的开头读取。 |
   | 序数 [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDBOplog.html#concept_iry_ykm_sy) | 整数序号，用于指定要在一组相同的时间戳记中使用的条目。指定初始时间戳时必需。使用-1退出此属性。 |
   | 操作类型                                                     | 要处理的操作。原点读取所选的操作。从以下数据操作中选择：插入更新删除您还可以从以下非数据操作中选择：NOOPCMDD B有关Oplog操作类型的更多信息，请参阅MongoDB文档。 |
   | 批次大小（记录）                                             | 批处理中允许的最大记录数。                                   |
   | 最大批次等待时间 [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ypd_vgr_5q) | 在发送空批次之前，源将等待填充批次的时间。                   |
   | 阅读偏好 [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDBOplog.html#concept_cwt_gd3_sy) | 确定来源如何从MongoDB副本集的不同成员读取数据。              |

3. 要与MongoDB连接字符串分开输入凭据，请单击“ **凭据”**选项卡并配置以下属性：

   | 证书     | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 认证类型 | MongoDB服务器使用的身份验证：用户名/密码或LDAP。             |
   | 用户名   | MongoDB或LDAP用户名。                                        |
   | 密码     | MongoDB或LDAP密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 认证来源 | 可选的备用数据库名称，用于执行委托的身份验证。可用于“用户名/密码”选项。 |

4. （可选）单击“ **高级”**选项卡以配置源如何连接到MongoDB。

   这些属性的默认值在大多数情况下都应该起作用：

   | 先进物业               | 描述                                                         |
   | :--------------------- | :----------------------------------------------------------- |
   | 每个主机的连接         | 每个主机的最大连接数。默认值为100。                          |
   | 每个主机的最小连接数   | 每个主机的最小连接数。默认值为0。                            |
   | 连接超时               | 等待连接的最长时间（以毫秒为单位）。默认值为10,000。         |
   | 最大连接空闲时间       | 池化连接可以保持空闲状态的最长时间（以毫秒为单位）。当池化连接超过空闲时间时，连接将关闭。使用0退出此属性。默认值为0。 |
   | 最大连接寿命           | 池化连接可以活动的最长时间（以毫秒为单位）。当池化连接超过生存期时，该连接将关闭。使用0退出此属性。默认值为0。 |
   | 最长等待时间           | 线程可以等待连接可用的最长时间（以毫秒为单位）。使用0退出此属性。使用负值无限期等待。默认值为120,000。 |
   | 服务器选择超时         | 在抛出异常之前，Data Collector等待服务器选择的最长时间（以毫秒为单位）。如果使用0，则在没有服务器可用时立即引发异常。使用负值无限期等待。默认值为30,000。 |
   | 允许阻塞的连接乘数线程 | 乘数，确定可以等待池中的连接可用的最大线程数。此数字乘以“每个主机的连接数”值确定最大线程数。默认值为5。 |
   | 心跳频率               | Data Collector尝试确定集群中每个服务器的当前状态的频率（以毫秒为单位）。默认值为10,000。 |
   | 最小心跳频率           | 最小心跳频率（以毫秒为单位）。在检查每个服务器的状态之前，Data Collector至少要等待这么长时间。默认值为500。 |
   | 心跳连接超时           | 等待用于群集心跳的连接的最长时间（以毫秒为单位）。默认值为20,000。 |
   | 心跳套接字超时         | 用于集群心跳的连接的套接字超时的最长时间（以毫秒为单位）。默认值为20,000。 |
   | 本地阈值               | 本地阈值（以毫秒为单位）。将请求发送到其ping时间小于或等于具有最快ping时间加本地阈值的服务器的服务器。默认值为15。 |
   | 必需副本集名称         | 用于集群的必需副本集名称。                                   |
   | 启用了游标终结器       | 指定是否启用游标终结器。                                     |
   | 套接字保持活动         | 指定是否启用套接字保持活动状态。                             |
   | 套接字超时             | 套接字超时的最长时间（以毫秒为单位）。使用0退出此属性。默认值为0。 |
   | 启用SSL                | 在Data Collector和MongoDB 之间启用SSL / TLS 。如果MongoDB证书是由私有CA签名的，或者不受默认Java信任库信任的，那么您还必须在SDC_JAVA_OPTS环境变量中定义信任库文件和密码，如[启用SSL / TLS中所述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDBOplog.html#task_tcy_gg3_sy)。 |
   | 允许的SSL无效主机名    | 指定在SSL / TLS证书中是否允许使用无效的主机名。              |