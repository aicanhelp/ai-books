# MongoDB

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310172912421.png) 资料收集器

MongoDB源从MongoDB读取数据。您还可以使用原点从Microsoft Azure Cosmos DB中读取。MongoDB的来源会为每个MongoDB文档生成一条记录。要从MongoDB Oplog中读取更改数据捕获信息，请使用[MongoDB Oplog](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDB.html#concept_bk4_2rs_ns)来源。

MongoDB的来源从有上限和无上限的集合中读取。配置MongoDB时，您将定义连接信息，例如连接字符串和MongoDB凭据。您还可以配置偏移量字段，收集类型和初始偏移量。这些属性确定来源如何查询数据库。

当管道停止时，MongoDB源会记录它停止读取的位置。当管道再次启动时，默认情况下原点将从上次保存的偏移开始继续处理。您可以重置原点以处理所有请求的文件。

您可以选择配置高级选项，这些选项用于确定来源如何连接到MongoDB，包括为来源启用SSL / TLS。

源可以为事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

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

## 偏移字段和初始偏移

MongoDB使用offset字段跟踪要读取的数据。默认情况下，MongoDB原点使用_id字段作为偏移量字段。

您可以使用嵌套的偏移量字段，例如o._id。或者，您可以使用任何对象ID，日期或字符串字段作为偏移量字段。不保证使用默认_id字段以外的任何字段的结果。

使用日期或对象ID字段时，请指定要用作初始偏移量的时间戳。对象ID字段包含一个嵌入的时间戳记，起点将使用该时间戳记来确定集合中开始读取的位置。在为日期或对象ID字段定义初始偏移量时，请使用以下格式：

```
YYYY-MM-DD HH:mm:ss
```

使用字符串字段时，请指定要用作初始偏移量的初始字符串。

**注意：**如果在管道运行然后停止后更改原点的偏移字段类型，则必须重置原点，然后才能再次运行管道。

## 阅读偏好

您可以配置MongoDB源使用的读取首选项。

读取首选项确定来源如何从MongoDB副本集的不同成员读取数据。

您可以使用以下MongoDB读取首选项：

- 主要-要求主要成员阅读。
- 首选首选-首选从首选读取，但允许从二级读取。
- 二级-需要二级成员阅读。
- 首选二级-首选从二级读取，但在必要时允许从一级读取。
- 最近-从成员那里读取的网络延迟最少。

默认情况下，源使用“首选优先级”以避免对主要成员进行不必要的请求。

## 事件产生

当MongoDB源完成处理所有可用数据并且已配置的批处理等待时间过去时，它可以生成事件。

MongoDB起源事件可以任何逻辑方式使用。例如：

- 当原始完成处理可用数据时，使用Pipeline Finisher执行程序停止管道并将管道转换为Finished状态。

  重新启动由Pipeline Finisher执行程序停止的管道时，原点将从上次保存的偏移开始继续处理，除非您重置原点。

  有关示例，请参见[案例研究：停止管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_kff_ykv_lz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

MongoDB源生成的事件记录具有以下与事件相关的记录头属性。记录标题属性存储为字符串值：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下事件类型：no-more-data-原点完成对所有可用对象的处理并且经过了“最大批处理等待时间”配置的秒数后生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

MongoDB源可以生成以下事件记录：

- 没有更多数据

  当MongoDB原点完成对所有可用记录的处理并且经过为“最大批处理等待时间”配置的秒数而未显示任何新对象时，MongoDB原点将生成无数据事件记录。

  由源生成的无数据事件记录将sdc.event.type设置为无数据，并包含以下字段：事件记录字段描述记录数自管道启动或自上一次创建no-more-data事件以来成功生成的记录数。错误计数自管道启动或自上一次创建no-more-data事件以来生成的错误记录数。

## BSON时间戳

处理来自MongoDB 2.6版和更高版本的数据时，MongoDB原始支持MongoDB BSON Timestamp数据类型。

MongoDB BSON时间戳是一种MongoDB数据类型，其中包括时间戳和以下序数：

```
<BSON Timestamp field name>:Timestamp(<timestamp>, <ordinal>)
```

MongoDB源将BSON时间戳转换为地图，如下所示：

```
<BSON Timestamp field name>{MAP}:
    Timestamp{DATETIME}:<UTC timestamp>
    Ordinal{INTEGER}:<integer ordinal>
```

例如，交易BSON时间戳记为 `(1485449409, 1)`，将转换为以下“交易映射”字段：

```
"Transaction":{
    "Timestamp":Jan 26, 2016 14:50:09PM
    "Ordinal":1
}
```

## 从Azure Cosmos DB读取

MongoDB源可以从配置为使用MongoDB API的Microsoft Azure Cosmos数据库实例读取。

若要配置源以从Azure Cosmos DB读取，您需要使用MongoDB API的Azure Cosmos DB帐户和源读取的Azure Cosmos DB集合的连接信息。如有必要，您可以创建一个帐户和集合。

1. 在Azure门户中，确定Azure Cosmos DB帐户和容器的连接信息。

   1. 在帐户的“连接字符串”设置中，注意以下几点：

      - 主办
      - 港口
      - 用户名
      - 密码

      若要创建使用Azure Cosmos DB for MongoDB API的新帐户，请参阅[Microsoft文档](https://docs.microsoft.com/en-us/azure/cosmos-db/create-mongodb-dotnet#create-a-database-account)。

   2. 在“数据资源管理器”窗格中，选择要原始读取的集合，并注意以下几点：

      - 数据库ID
      - 馆藏ID

      要创建新集合，请参阅[Microsoft文档](https://docs.microsoft.com/en-us/azure/cosmos-db/how-to-create-container#portal-mongodb)。

2. 在Data Collector中，使用步骤[1中记](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDB.html#MongoDB-ReadingAzureCosmosDB__step-MongoDB-ReadAzureCosmosDB-FromPortal)下的值配置MongoDB源以连接到Azure Cosmos数据库实例。

   1. 在“ **MongoDB”**选项卡上，配置以下属性：

      | 属性       | 组态                                                         |
      | :--------- | :----------------------------------------------------------- |
      | 连接字符串 | 输入以下字符串，替换主机和端口：`mongodb://:/?ssl=true&replicaSet=globaldb` |
      | 数据库     | 输入数据库ID。                                               |
      | 采集       | 输入收集ID。                                                 |

   2. 在“ **凭据”**选项卡上，将“ **身份验证类型”**设置 为“ **用户名/密码”，**然后输入用户名和密码。

3. [为源启用SSL](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDB.html#task_zry_dg2_ww)。

## 启用SSL / TLS

您可以启用MongoDB源以使用SSL / TLS连接到MongoDB。

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

## 配置MongoDB起源

配置MongoDB源以从MongoDB读取数据。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDB.html#concept_vx3_1gh_scb) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **MongoDB”**选项卡上，配置以下属性：

   | MongoDB属性                                                  | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 连接字符串                                                   | MongoDB实例的连接字符串。使用以下格式：`mongodb://host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]`连接到集群时，输入其他节点信息以确保连接。要从Azure Cosmos DB读取，请参阅[从Azure Cosmos DB读取](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDB.html#MongoDB-ReadingAzureCosmosDB)。如果MongoDB服务器使用用户名/密码或LDAP身份验证，可以在连接字符串中的凭据，如在[凭证](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDB.html#concept_gzz_kdr_tz)。 |
   | 启用单模                                                     | 选择以连接到单个MongoDB服务器或节点。如果在连接字符串中定义了多个节点，则该阶段仅连接到第一个节点。请谨慎使用此选项。如果阶段无法连接或连接失败，则管道将停止。 |
   | 数据库                                                       | MongoDB数据库的名称。                                        |
   | 采集                                                         | 要使用的MongoDB集合的名称。                                  |
   | 上限集合                                                     | 该集合已设置上限。清除此选项以读取未加盖的集合。             |
   | 初始偏移                                                     | 用于开始读取的初始偏移量。当使用日期或对象ID字段作为偏移字段时，请输入具有以下格式的时间戳：`YYYY-MM-DD hh:mm:ss`。使用字符串字段时，输入要使用的字符串。默认值为：`2015-01-01 00:00:00`。 |
   | 偏移字段类型                                                 | 偏移字段的数据类型：ObjectId-用于对象ID字段。日期-用于日期字段。字符串-用于字符串字段。默认值为ObjectId。 |
   | [偏移场](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDB.html#concept_kx3_zrs_ns) | 用于跟踪读取的字段。默认值为_id字段。您可以使用嵌套的偏移量字段，例如o._id。您也可以使用任何对象ID，日期或字符串字段。除_id字段外，不保证任何结果。 |
   | 批次大小（记录）                                             | 批处理中允许的最大记录数。                                   |
   | [最大批次等待时间](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ypd_vgr_5q) | 在发送空批次之前，源将等待填充批次的时间。                   |
   | [阅读偏好](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDB.html#concept_oy2_1dt_ns) | 确定来源如何从MongoDB副本集的不同成员读取数据。              |

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
   | 启用SSL                | 在Data Collector和MongoDB 之间启用SSL / TLS 。如果MongoDB证书是由私有CA签名的，或者不受默认Java信任库信任的，那么您还必须在SDC_JAVA_OPTS环境变量中定义信任库文件和密码，如[启用SSL / TLS中所述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDB.html#task_zry_dg2_ww)。 |
   | 允许的SSL无效主机名    | 指定在SSL / TLS证书中是否允许使用无效的主机名。              |