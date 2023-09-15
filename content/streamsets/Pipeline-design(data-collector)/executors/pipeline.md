# 管道修整机执行器

当Pipeline Finisher执行程序收到事件时，执行程序将停止管道并将其转换为Finished状态。这允许管道在停止之前完成所有预期的处理。

将Pipeline Finisher执行程序用作事件流的一部分。您可以以任何逻辑方式使用Pipeline Finisher执行程序，例如在从JDBC Query Consumer来源接收到no-more-data事件后停止管道。

例如，您可以在旨在将所有现有数据从Microsoft SQL Server迁移到HDFS的管道中使用执行程序。然后使用单独的管道来处理增量更新。或者，您可以使用执行程序执行传统的“批处理”-处理数据，然后在处理完所有数据后停止，而不是无限期地等待更多数据。

配置Pipeline Finisher执行程序时，可以指定在每次运行管道后执行程序是否应重置原点。在需要时，可以使用前提条件来限制进入阶段以停止管道的记录。您还可以将管道配置为在Pipeline Finisher执行程序停止管道时通知您。

在使用Pipeline Finisher执行程序之前，请查看建议的实施信息。

有关案例研究，请参阅[案例研究：停止管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_kff_ykv_lz)。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

## 推荐实施

Pipeline Finisher执行程序旨在在处理原始系统中的可用数据后停止管道并将其转换为Finished状态。例如，在JDBC查询使用者处理查询中指定的所有可用数据之后，可以使用执行程序停止管道。

当原点仅生成no-more-data事件时，您只需将事件输出连接到Pipeline Finisher执行程序即可。当一个来源生成多种事件类型时，您需要确保Pipeline Finisher仅在收到no-more-data事件之后才停止管道。

您可以通过以下方法确保执行程序仅接收no-more-data事件：

- 为管道修整器配置前提条件

  在执行程序中，添加前提条件以仅允许no-more-data事件进入该阶段以触发执行程序。您可以使用以下表达式：`${record:eventType() == 'no-more-data'}`

  **提示：**由于先决条件而丢弃的记录将根据阶段错误处理配置进行处理。因此，为避免堆积错误记录，您还可以将Pipeline Finisher执行程序配置为丢弃错误记录。

  当管道逻辑允许您丢弃由源生成的其他事件类型时，请使用此方法。

- 在流水线整理器之前添加流选择器

  您可以在源和执行程序之间添加流选择器，以仅将no-more-data事件路由到Pipeline Finisher。当您要将其他事件类型传递到其他分支进行处理时，请使用此选项。

  例如，假设您使用的是JDBC Query Consumer源，该源将生成数据，查询成功和查询失败事件。并说您要存储查询成功和查询失败事件。您可以使用具有以下条件的流选择器将no-more-data事件路由到Pipeline Finisher：`${record:eventType() == 'no-more-data'}`

  然后，您可以将默认流（接收查询成功和查询失败事件）连接到目标存储位置。

## 相关事件产生阶段

最佳实践是仅将Pipeline Finisher执行程序与不会产生任何数据事件的源一起使用。

以下起源产生了没有数据的事件：

- Amazon S3的起源
- Azure Data Lake Storage Gen1来源
- Azure Data Lake Storage Gen2的来源
- 目录来源
- Google Cloud Storage的起源
- Hadoop FS独立版本
- JDBC多表使用者来源
- JDBC查询使用者来源
- MongoDB的起源
- Salesforce来源
- SFTP / FTP / FTPS客户端来源
- SQL Server 2019 BDC多表使用者来源
- SQL Server CDC客户端来源
- SQL Server更改跟踪来源
- Teradata消费者来源

## 原点复位以进行其他管道运行

如果您希望管道在每次管道运行时都处理所有可用数据，请配置Pipeline Finisher执行程序以在停止管道后重置原点。当执行程序重置原点时，管道的重新启动行为对于所有原点都相同：原点处理所有可用数据。

默认情况下，重新启动行为取决于管道中使用的来源。如果原点不保存偏移量，则在重新启动管道时，原点会再次处理所有可用数据。例如，当JDBC查询使用者以完全模式运行时，每次重新启动管道时，原始服务器都会处理完整查询。

当原点存储偏移量时，当您重新启动管道时，默认情况下原点将从最后保存的偏移量开始。例如，当JDBC查询使用者以增量模式运行时，默认情况下，当您重新启动管道时，原点将从中断处继续。

如果希望原点在每次运行管道时都处理所有可用数据，请配置Pipeline Finisher执行程序以重置原点。尽管此属性对不保存偏移量的原点没有影响，但是这些原点已经在每次管道运行时都处理了所有可用数据。

有关在管道级别重置原点的信息，请参见[重置原点](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Maintenance/ResettingTheOrigin.html#task_hdg_j1s_5q)。

## 通知选项

当Pipeline Finisher停止管道时，Data Collector可以通知您。您可以使用以下两种通知方法之一：

- 管道状态通知

  您可以将管道配置为在管道过渡到指定状态时发送电子邮件或Webhook。使用此选项可以发送一个Webhook或简单的电子邮件通知。您无法自定义发送的电子邮件通知。

  若要使管道在管道整理器停止管道时发送通知，请将“管道状态更改时通知”属性设置为“已完成”。有关管道状态通知的更多信息，请参见[通知](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/Notifications.html#concept_mtn_k4j_rz)。

- 电子邮件执行器

  您可以使用电子邮件执行程序发送电子邮件通知。电子邮件执行程序允许您配置条件以用于发送电子邮件，电子邮件收件人，主题和消息。您还可以在任何属性中使用表达式，以将事件记录中的详细信息包括在电子邮件中。使用此选项可在收到事件后发送自定义电子邮件。

  要发送自定义电子邮件，请将触发管道整理程序的同一事件路由到电子邮件执行程序。在电子邮件执行程序和管道中的所有其他阶段完成任务之后，管道完成器将管道转换为“完成”状态。

  有关使用电子邮件执行程序的更多信息，请参阅[电子邮件执行程序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Email.html#concept_sjs_sfp_qz)。

## 配置管道修整器执行器

配置Pipeline Finisher执行程序以在执行程序收到事件记录时停止并将管道转换为Finished状态。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | 前提条件 [![img](imgs/icon_moreInfo-20200310203529892.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。所有其他记录都基于“ **记录错误”** 属性进行处理。单击**添加**以创建其他前提条件。**提示：**若要仅将no-more-data事件传递给执行程序，请使用以下条件：`${record:eventType() == 'no-more-data'}` |
   | 记录错误 [[![img](imgs/icon_moreInfo-20200310203529892.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。**提示：**使用前提条件限制进入执行程序的事件类型时，可以将此属性设置为Discard以避免处理其他事件类型。 |

2. 在**装订**选项卡上，有选择地配置以下属性：

   | 整理器属性                                                   | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [重设原点](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Maintenance/ResettingTheOrigin.html#task_hdg_j1s_5q) | 在Pipeline Finisher执行程序停止管道之后，重置管道原点。启用此选项可在每次管道运行时处理所有可用数据。禁用后，管道重新启动行为取决于原始配置。 |