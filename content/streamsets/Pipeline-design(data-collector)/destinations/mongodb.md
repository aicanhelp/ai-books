# MongoDB

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310201951404.png) 资料收集器

MongoDB目标将数据写入MongoDB。您还可以使用目标写入Microsoft Azure Cosmos DB。

要写入数据，MongoDB目标要求记录包含CRUD操作记录标头属性。CRUD操作标头属性指示要对每个记录执行的操作。您还可以为更新和替换记录启用upserts。有关Data Collector更改数据处理以及启用CDC的来源的列表的信息，请参见[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

在配置MongoDB目标时，您将定义连接信息，例如连接字符串和MongoDB凭据。您还配置数据库并进行收集，并编写使用注意事项。

要替换和更新记录，必须指定唯一的键字段，并且可以选择启用upsert标志。当您不指定唯一键字段时，替换和更新记录将发送到阶段以进行错误处理。

您可以选择配置高级选项，这些选项确定目标如何连接到MongoDB，包括为目标启用SSL / TLS。

**注意：** StreamSets已使用MongoDB 4.0测试了此阶段。

## 证书



根据MongoDB服务器使用的身份验证，将源配置为不使用身份验证，用户名/密码身份验证或LDAP身份验证。使用用户名/密码身份验证时，还可以使用委托身份验证。

默认情况下，目标不使用身份验证。

要使用用户名/密码或LDAP认证，请通过以下方式之一输入所需的凭据：

- MongoDB选项卡中的连接字符串

  在MongoDB选项卡的连接字符串中输入凭据。

  要输入用于用户名/密码身份验证的凭据，请在主机名之前输入用户名和密码。使用以下格式：`mongodb://**username:password@**host[:port][/[database][?options]]`

  要输入用于LDAP身份验证的凭据，请在主机名之前输入用户名和密码，并将authMechanism选项设置为PLAIN。使用以下格式：`mongodb://**username:password@**host[:port][/[database]**?authMechanism=PLAIN**`

- 凭据选项卡

  在“凭据”选项卡中选择“用户名/密码”或“ LDAP身份验证”类型。然后输入身份验证类型的用户名和密码。

如果同时在连接字符串和“凭据”选项卡中输入凭据，则“凭据”选项卡优先。

## 定义CRUD操作

要写入MongoDB，请确保为管道中的每个记录定义了CRUD操作记录标头属性。没有操作记录头属性的记录将发送到错误。

要更新和替换记录，必须指定唯一的键字段。您还可以启用upsert来更新和替换记录。

请注意，在执行DELETE操作时，目标在MongoDB中最多删除一个匹配的文档。它不会删除所有匹配的文档，这在MongoDB中有时是可能的。

要将记录写入MongoDB，请确保记录包含以下CRUD操作记录头属性：

- sdc.operation.type

  定义后，当写入MongoDB时，MongoDB目标将在sdc.operation.type记录标题属性中使用CRUD操作。MongoDB目标支持sdc.operation.type属性的以下值：INSERT为12个代表删除3更新7换

  如果您的管道包括启用CRUD的原始数据源，该原始数据元处理已更改的数据，则目标位置仅从`sdc.operation.type`原始数据源生成的标头属性中读取操作类型 。如果管道使用非CDC来源，则可以使用表达式评估器或脚本处理器来定义记录头属性。有关Data Collector 更改的数据处理以及启用CDC的来源的列表的详细信息 ，请参阅 [处理更改的数据。](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

### 表演高手

您可以配置目标以执行upsert。启用upserts时，目标位置在找不到要更新或替换的现有记录时会插入“更新”和“替换”记录的记录。

默认情况下，目标不执行upsert。如果目标未找到标记为更新或替换的记录的现有记录，则不会将记录写入MongoDB。

有关MongoDB操作和upsert标志的更多信息，请参阅MongoDB文档。

## 写入Azure Cosmos DB

MongoDB目标可以写入配置为使用MongoDB API的Microsoft Azure Cosmos数据库实例。具体而言，目标可以写入Azure Cosmos DB中具有与数据收集器 管道中的字段名称匹配的分片键的集合。

若要将目标配置为写入Azure Cosmos DB，您需要使用MongoDB API的Azure Cosmos DB帐户和目标可以在其中写入Azure Azure Cosmos DB集合的连接信息。如有必要，您可以创建一个帐户和集合。

1. 在Azure门户中，确定Azure Cosmos DB帐户和容器的连接信息。

   1. 在帐户的“连接字符串”设置中，注意以下几点：

      - 主办
      - 港口
      - 用户名
      - 密码

      若要创建使用Azure Cosmos DB for MongoDB API的新帐户，请参阅[Microsoft文档](https://docs.microsoft.com/en-us/azure/cosmos-db/create-mongodb-dotnet#create-a-database-account)。

   2. 在“数据资源管理器”窗格中，选择要写入目标的集合，并注意以下几点：

      - 数据库ID
      - 馆藏ID

      为了将目标写入集合，集合的分片键必须从Data Collector管道中指定一个字段。

      要创建新集合，请参阅[Microsoft文档](https://docs.microsoft.com/en-us/azure/cosmos-db/how-to-create-container#portal-mongodb)。

2. 在Data Collector中，使用步骤[1中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MongoDB.html#MongoDB-WritingAzureCosmosDB__step-MongoDB-WriteAzureCosmosDB-FromPortal)记录的值将MongoDB目标配置为连接到Azure Cosmos数据库实例。

   1. 在“ **MongoDB”**选项卡上，配置以下属性：

      | 属性       | 组态                                                         |
      | :--------- | :----------------------------------------------------------- |
      | 连接字符串 | 输入以下字符串，替换主机和端口：`mongodb://:/?ssl=true&replicaSet=globaldb` |
      | 数据库     | 输入数据库ID。                                               |
      | 采集       | 输入收集ID。                                                 |

   2. 在“ **凭据”**选项卡上，将“ **身份验证类型”**设置 为“ **用户名/密码”，**然后输入用户名和密码。

3. [为目标启用SSL](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MongoDB.html#task_d5l_qh2_ww)。

## 启用SSL / TLS

您可以启用MongoDB目标以使用SSL / TLS连接到MongoDB。

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

有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。

## 配置MongoDB目标

配置MongoDB目标以写入MongoDB。

**要点：**确保路由到目标的所有记录都包含CDC记录头属性。有关更多信息，请参见[定义CRUD操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MongoDB.html#concept_bkc_m24_4v)。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **MongoDB”**选项卡上，配置以下属性：

   | MongoDB属性                                                  | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 连接字符串                                                   | MongoDB实例的连接字符串。使用以下格式：`mongodb://host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]`连接到集群时，输入其他节点信息以确保连接。要写入Azure Cosmos DB，请参阅[写入Azure Cosmos DB](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MongoDB.html#MongoDB-WritingAzureCosmosDB)。如果MongoDB服务器使用用户名/密码或LDAP身份验证，可以在连接字符串中的凭据，如在[凭证](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MongoDB.html#concept_ppl_3qt_tz)。 |
   | 数据库                                                       | MongoDB数据库名称。                                          |
   | 采集                                                         | MongoDB集合名称。                                            |
   | 唯一关键字段                                                 | 记录中用于更新和替换记录的字段。如果未设置，则将标记为要更新或替换的记录发送到阶段以进行错误处理。 |
   | [增补](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MongoDB.html#concept_syh_s1l_tbb) | 当记录在数据库中不存在时，插入标记为更新或替换的记录。       |
   | 写关注                                                       | 从目标系统请求的确认级别。有关写关注级别的详细信息，请参阅MongoDB文档。 |

3. 要与MongoDB连接字符串分开输入凭据，请单击“ **凭据”**选项卡并配置以下属性：

   | 证书     | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 认证类型 | MongoDB服务器使用的身份验证：用户名/密码或LDAP。             |
   | 用户名   | MongoDB或LDAP用户名。                                        |
   | 密码     | MongoDB或LDAP密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 认证来源 | 可选的备用数据库名称，用于执行委托的身份验证。可用于“用户名/密码”选项。 |

4. （可选）单击“ **高级”**选项卡以配置目标如何连接到MongoDB。

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
   | 启用SSL                | 在Data Collector和MongoDB 之间启用SSL / TLS 。如果MongoDB证书是由私有CA签名的，或者不受默认Java信任库信任的，那么您还必须在SDC_JAVA_OPTS环境变量中定义信任库文件和密码，如[启用SSL / TLS中所述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MongoDB.html#task_d5l_qh2_ww)。 |
   | 允许的SSL无效主机名    | 指定在SSL / TLS证书中是否允许使用无效的主机名。              |