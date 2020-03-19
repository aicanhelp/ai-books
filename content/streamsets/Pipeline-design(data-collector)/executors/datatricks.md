# Databricks执行器

每次收到事件时，Databricks执行程序都会启动Databricks作业。您可以基于笔记本或JAR运行作业。

使用Databricks执行程序作为事件流的一部分启动Databricks作业。您可以以任何逻辑方式使用执行程序，例如在Hadoop FS，MapR FS或Amazon S3目标关闭文件后运行Databricks作业。

请注意，Databricks执行程序在外部系统中启动作业。它不会监视作业或等待作业完成。成功执行作业后，执行者即可进行其他处理。

在使用Databricks执行程序之前，请执行必要的先决条件。

配置执行程序时，您可以指定群集基本URL，作业类型，作业ID和用户凭据。您可以选择配置作业参数和安全性，例如HTTP代理和SSL / TLS详细信息。

您可以将执行程序配置为为另一个事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

## 先决条件

运行在Databricks上启动作业的Databricks执行程序管道之前，请在Databricks中执行以下任务：

1. 创建作业。

   Databricks执行程序可以基于笔记本或JAR来启动作业。

2. （可选）将作业配置为允许并发运行。

   默认情况下，Databricks不允许同时运行一个作业的多个实例。默认情况下，如果Databricks执行程序快速连续接收到多个事件，它将启动该作业的多个实例，但是Databricks将这些实例排队并逐个运行它们。

   要启用并行处理，请在Databricks中将作业配置为允许并发运行。您可以使用max_concurrent_runs参数通过Databricks API或使用“作业”>“高级”菜单和“最大并行运行数”属性通过UI配置最大并行运行数。

3. 保存作业并记下作业ID。

   提交作业时，Databricks会生成一个作业ID。配置Databricks执行程序时，请使用作业ID。

## 事件产生

Databricks执行程序可以生成可在事件流中使用的事件。启用事件生成后，执行程序每次启动Databricks作业时都会生成事件。

Databricks执行程序事件可以任何逻辑方式使用。例如：

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

由于Databricks执行程序事件包括每个已启动作业的运行ID，因此您可能会生成事件以保留运行ID的日志。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

由Databricks执行程序生成的事件记录具有以下与事件相关的记录头属性。记录标题属性存储为字符串值：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型：AppSubmittedEvent-在执行程序启动Databricks作业时生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

由Databricks执行程序生成的事件记录具有以下字段：

| 活动栏位名称 | 描述                     |
| :----------- | :----------------------- |
| app_id       | Databricks作业的运行ID。 |

## 监控方式

Data Collector 不监视Databricks作业。使用常规群集监视器应用程序查看作业的状态。

由Databricks执行程序启动的作业将使用阶段中指定的作业ID进行显示。所有作业实例的作业ID均相同。您可以在Data Collector 日志中找到特定实例的运行ID 。

Databricks执行程序还将作业的运行ID写入事件记录。要保留所有运行ID的记录，请启用该阶段的事件生成。

## 配置Databricks执行器

配置一个Databricks执行程序，使其在每次执行程序收到事件记录时启动一个Databricks作业。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Databricks.html#concept_ekj_mdz_cfb) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击**添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |

2. 在“ **作业”**选项卡上，配置以下属性：

   | 工作性质       | 描述                                                         |
   | :------------- | :----------------------------------------------------------- |
   | 群集基本URL    | 您公司的Databricks URL。URL使用以下格式：`https://.cloud.databricks.com` |
   | 使用代理服务器 | 启用使用HTTP代理连接到系统。                                 |
   | 工作类型       | 要运行的作业类型：笔记本或JAR。                              |
   | 工作编号       | 提交作业后，Databricks生成的作业ID，如[先决条件中所述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Databricks.html#concept_esz_x3d_kz)。 |
   | 参量           | 传递给作业的参数。完全按照预期的顺序输入参数。执行程序不验证参数。您可以在作业参数中使用表达语言。例如，在对Amazon S3对象执行后处理时，您可以使用以下表达式从事件记录中检索对象键名称：`${record:field('/objectKey')}` |

3. 在“ **凭据”**选项卡上，配置以下属性：

   | 凭证属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 凭证类型 | 用于连接到Databricks的凭据类型：用户名/密码或令牌。          |
   | 用户名   | Databricks用户名。                                           |
   | 密码     | 帐户密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 代币     | 帐户的个人访问令牌。                                         |

4. 要使用HTTP代理，请在“ **代理”**选项卡上配置以下属性：

   | HTTP代理属性 | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 代理URI      | 代理URI。                                                    |
   | 用户名       | 代理用户名。                                                 |
   | 密码         | 代理密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |

5. 要使用SSL / TLS，请在“ **TLS”**选项卡上配置以下属性：

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