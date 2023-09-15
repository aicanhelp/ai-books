# 卡桑德拉

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310183054299.png) 资料收集器

Cassandra目标将数据写入Cassandra集群。

配置Cassandra目标时，可以定义连接信息并将传入字段映射到Cassandra表中的列。您还可以配置目标是将每个批次作为已记录的批次还是未记录的批次写入Cassandra。您可以禁用批写，而可以分别使目标写记录。

您可以配置目标是否不使用身份验证或使用用户名和密码身份验证来访问Cassandra群集。如果安装了DataStax Enterprise（DSE）Java驱动程序，则可以将目标配置为使用DSE用户名和密码认证或Kerberos认证。

您也可以为连接启用SSL / TLS。

## 批次类型

Cassandra目标可以使用以下批处理类型之一将批处理写入Cassandra集群：

- 已记录

  写入Cassandra的已记录批处理使用Cassandra分布式批处理日志，并且是原子的。这意味着目标只能将整批记录写入Cassandra。如果批处理中的一个或多个记录发生错误，则目标将使整个批处理失败。当批处理失败时，所有记录都将发送到阶段以进行错误处理。

- 未记录

  写入Cassandra的未记录批次不使用Cassandra分布式批次日志，并且是非原子的。这意味着目标可以将部分记录记录写入Cassandra。如果批处理中的一个或多个记录发生错误，则目标仅将那些失败的记录发送到阶段以进行错误处理。目标将批处理中的其余记录写入Cassandra。

默认情况下，目标使用记录的批处理类型。

有关Cassandra分布式批处理日志的更多信息，请参见[Cassandra查询语言（CQL）文档](https://docs.datastax.com/en/dse/6.7/cql/cql/cql_reference/cql_commands/cqlBatch.html)。

## 认证方式

将Cassandra目标配置为使用以下身份验证提供程序之一来访问Cassandra集群：

- 无-不执行身份验证。
- 用户名/密码-使用Cassandra用户名和密码验证。
- 用户名/密码（DSE）-使用DataStax Enterprise用户名和密码认证。要求您安装DSE Java驱动程序。
- Kerberos（DSE）-使用Kerberos身份验证。要求您安装DSE Java驱动程序。

选择DSE身份验证提供程序之一之前，请安装DSE Java驱动程序版本1.2.4或更高版本。有关兼容性矩阵，请参见[Cassandra文档](https://docs.datastax.com/en/developer/java-driver/3.6/manual/native_protocol/)。有关安装其他驱动程序的信息，请参阅 Data Collector 文档 中的“ [安装外部库](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/ExternalLibs.html%23concept_pdv_qlw_ft) ”。

### Kerberos（DSE）身份验证

如果安装DSE Java驱动程序，则可以使用Kerberos身份验证连接到Cassandra群集。使用Kerberos身份验证时，Data Collector 使用Kerberos主体和keytab连接到集群。默认情况下，Data Collector 使用启动它的用户帐户进行连接。

Kerberos主体和密钥表在Data Collector 配置文件中定义`$SDC_CONF/sdc.properties`。要使用Kerberos身份验证，请在Data Collector 配置文件中配置所有Kerberos属性，安装DSE Java驱动程序，然后在Cassandra目标中启用Kerberos（DSE）身份验证。

## Cassandra数据类型

由于Cassandra的要求，传入字段的数据类型必须与相应的Cassandra列的数据类型匹配。在适当的时候，在管道中的较早位置使用字段类型转换器处理器来转换数据类型。

有关将Java数据类型转换为Cassandra数据类型的详细信息，请参见Cassandra文档。

Cassandra目标支持以下Cassandra数据类型：

- ASCII码
- 比金特
- 布尔型
- 计数器
- 小数
- 双
- 浮动
- 整数
- 清单
- 地图
- 文本
- 时间戳记
- 时空
- Uuid
- Varchar
- 瓦林特

目前不支持以下数据类型：

- 斑点
- et
- 组

## 配置Cassandra目标

配置Cassandra目标，以将数据写入Cassandra集群。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **Cassandra”**选项卡上，配置以下属性：

   | 卡桑德拉房地产                                               | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 卡桑德拉联络点                                               | Cassandra群集中节点的主机名。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以输入多个主机名以确保连接。 |
   | 卡桑德拉港                                                   | Cassandra节点的端口号。                                      |
   | [认证提供者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Cassandra.html#concept_ajh_vhp_x1b) | 确定用于访问集群的身份验证提供程序：无-不执行身份验证。用户名/密码-使用Cassandra用户名和密码验证。用户名/密码（DSE）-使用DataStax Enterprise用户名和密码认证。要求您安装DSE Java驱动程序。Kerberos（DSE）-使用Kerberos身份验证。要求您安装DSE Java驱动程序。 |
   | 协议版本                                                     | 本机协议版本，定义驱动程序和Cassandra之间交换的二进制消息的格式。选择您正在使用的协议版本。有关确定协议版本的信息，请参阅[Cassandra文档](https://docs.datastax.com/en/developer/java-driver/3.6/manual/native_protocol/)。 |
   | 压缩                                                         | 传输级请求和响应的可选压缩类型。                             |
   | 启用批次                                                     | 启用Cassandra批处理操作。如果未选择，则原点使用单独的语句将记录写入Cassandra。 |
   | 写超时                                                       | 完成写请求所允许的最长时间（以毫秒为单位）。未启用批处理操作时可用。 |
   | [批次类型](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Cassandra.html#concept_fky_vjd_mgb) | 要写入Cassandra的批处理类型：已记录未记录启用批处理操作时可用。 |
   | 最大批量                                                     | 每批写入Cassandra的最大语句数。确保此数字不超过在Cassandra群集中配置的批处理大小。 |
   | 合格表名称                                                   | 要使用的Cassandra表的名称。使用以下格式输入标准名称： `.`。  |
   | 字段到列的映射                                               | 将字段从记录映射到Cassandra列。使用 [简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以创建其他字段映射。**注意：**记录字段数据类型必须与Cassandra列的数据类型匹配。 |

3. 要使用用户名/密码身份验证，请单击“ **凭据”**选项卡，然后输入用户名和密码。

   **提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。

4. 要使用SSL / TLS，请在“ **TLS”**选项卡上配置以下属性：

   | TLS属性                                                      | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 使用TLS                                                      | 启用TLS的使用。                                              |
   | [密钥库文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z) | 密钥库文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。默认情况下，不使用任何密钥库。 |
   | 密钥库类型                                                   | 要使用的密钥库的类型。使用以下类型之一：Java密钥库文件（JKS）PKCS＃12（p12文件）默认值为Java密钥库文件（JKS）。 |
   | 密钥库密码                                                   | 密钥库文件的密码。密码是可选的，但建议使用。**提示：**为了保护敏感信息（如密码），可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 密钥库密钥算法                                               | 用于管理密钥库的算法。默认值为 SunX509。                     |
   | [信任库文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z) | 信任库文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。默认情况下，不使用任何信任库。 |
   | 信任库类型                                                   | 要使用的信任库的类型。使用以下类型之一：Java密钥库文件（JKS）PKCS＃12（p12文件）默认值为Java密钥库文件（JKS）。 |
   | 信任库密码                                                   | 信任库文件的密码。密码是可选的，但建议使用。**提示：**为了保护敏感信息（如密码），可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 信任库信任算法                                               | 用于管理信任库的算法。默认值为SunX509。                      |
   | 使用默认协议                                                 | 确定要使用的传输层安全性（TLS）协议。默认协议是TLSv1.2。要使用其他协议，请清除此选项。 |
   | [传输协议](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_mvs_cxf_5z) | 要使用的TLS协议。要使用默认TLSv1.2以外的协议，请单击“ **添加”**图标并输入协议名称。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加协议。**注意：**较旧的协议不如TLSv1.2安全。 |
   | [使用默认密码套件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_cwx_dyf_5z) | 对SSL / TLS握手使用默认的密码套件。要使用其他密码套件，请清除此选项。 |
   | 密码套房                                                     | 要使用的密码套件。要使用不属于默认密码集的密码套件，请单击“ **添加”**图标并输入密码套件的名称。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加密码套件。输入要使用的其他密码套件的Java安全套接字扩展（JSSE）名称。 |