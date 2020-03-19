# 配置单元查询执行器

Hive Query执行程序连接到Hive或Impala，并在每次收到事件记录时执行一个或多个用户定义的Hive或Impala查询。

将Hive Query执行程序用作事件流的一部分，以在Hive或Impala中执行事件驱动的查询。您可以以任何逻辑方式使用执行程序，例如在Hive元数据目标更新Hive元存储之后，或者在Hadoop FS或MapR FS目标关闭文件之后运行Hive或Impala查询。

例如，作为Hive [漂移同步解决方案的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Hive_Drift_Solution/HiveDriftSolution_title.html#concept_phk_bdf_2w)一部分，您可以使用Hive Query执行程序对Impala执行Invalidate Metadata查询，或者为新创建的表配置表属性。

将Hive Query执行程序与Impala一起使用时，可以使用Data Collector附带的默认驱动程序，也可以安装Impala JDBC驱动程序。

**注意：** Hive Query执行程序等待每个查询完成，然后再继续对同一事件记录进行下一个查询。它还会等待所有查询完成，然后再开始查询下一个事件记录。根据管道的速度和查询的复杂性，等待查询完成会降低管道的性能。

配置Hive Query执行程序时，您将JDBC连接信息配置为Hive，并可以选择添加其他要使用的HDFS配置属性。您指定要运行的查询，并指示在查询失败后是否运行其余的查询。

您还可以配置执行程序以为另一个事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

有关使用Hive Query执行程序的[案例研究](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_szz_xwm_lx)，请参阅[案例研究：针对Hive的DDS的Impala元数据更新](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_szz_xwm_lx)。

## 相关事件产生阶段

在事件流中使用Hive Query执行程序。Hive Query执行程序旨在在收到事件记录后运行一组Hive或Impala查询。您可以在逻辑适合您需求的任何事件生成阶段中使用Hive Query执行程序。

在使用Impala实施针对Hive的Drift同步解决方案时，在Hive Metastore目标更改表结构之后以及Hadoop FS目标将文件写入Hive之后，请使用执行程序来运行Invalidate Metadata查询。

## 安装Impala驱动程序

您可以使用Data Collector随附的Apache Hive JDBC驱动程序来执行Impala查询。但是，某些发行版建议使用本机Impala JDBC驱动程序。

要使用Data Collector随附的随附的Apache Hive JDBC驱动程序，您无需执行任何其他步骤。

要使用Impala JDBC驱动程序，请执行以下步骤：

1. 为您使用的Hive发行版下载本机Impala JDBC驱动程序。

2. 将驱动程序安装为Hive Query执行程序使用的阶段库的外部库。

   例如，说执行程序配置为使用CDH 5.12.0阶段库。如果使用软件包管理器来安装驱动程序，则在“安装外部库”对话框中，选择CDH 5.12.0阶段库，然后浏览以选择要安装的Impala驱动程序库。

**注意：** StreamSets已使用Apache Hadoop的受支持发行版中随附的JDBC驱动程序对Hive Query执行程序进行了测试。

有关安装其他驱动程序的信息，请参阅 Data Collector 文档 中的“ [安装外部库](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/ExternalLibs.html%23concept_pdv_qlw_ft) ”。

如果您在确定配置执行程序时使用的URL格式时遇到麻烦，请查看我们的[Ask StreamSets帖子](https://ask.streamsets.com/question/7/how-do-you-configure-a-hive-impala-jdbc-driver-for-data-collector/?answer=8#post-id-8)。

## 蜂巢和黑斑羚查询

您可以在每次执行程序收到事件记录时使用Hive Query执行程序执行一组Hive或Impala查询。

Hive Query执行程序等待每个查询完成，然后再继续下一个对同一事件记录的查询。它还会等待所有查询完成，然后再开始查询下一个事件记录。根据管道的速度和查询的复杂性，等待查询完成会降低管道的性能。

尽可能避免使用Hive Query执行程序来运行长时间运行的查询。此外，在对事件记录运行多个查询时，可以将执行程序配置为在查询失败时跳过其余查询。默认情况下，执行程序将继续运行其余查询。

您可以在查询的事件记录中使用字段和属性。例如，对于Hive Metastore创建或更新表时生成的事件记录，可以在事件记录中使用表名来执行其他任务。

有关事件记录中的字段名称和描述的列表，请参阅事件生成阶段的“事件记录”文档。

## 用于Hive的漂移同步解决方案的Impala查询

用于Hive的Drift同步解决方案使管道可以自动创建和更新Hive表并将文件写入表。

在使用Impala实现Hive的Drift同步解决方案时，每次需要更新Impala元数据缓存时，都可以使用Hive Query执行程序来提交无效的元数据查询。有关详细的示例，请参阅[案例研究：Dive for Hive的Impala元数据更新](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_szz_xwm_lx)。

将Hive查询执行程序从Hive Metastore目标和Hadoop FS目标连接到事件流。您可以为两者使用同一执行程序，也可以为每一个使用单独的执行程序。

- 处理来自Hive Metastore目标的事件记录

  Hive Metastore目标每次更改表时都会生成一个事件记录，并将表名放在“表”记录头属性中。使用以下查询更新Impala元数据缓存：`invalidate metadata ${record:attribute('/table')}`

  当Hive Query执行程序收到事件记录时，它将在事件记录中指定的表上运行Invalidate Metadata查询。

- 处理Hadoop FS目标中的事件记录

  每次关闭文件时，Hadoop FS目标都会生成一个事件记录。它将文件路径放在事件记录的“文件路径”字段中。如果为每个目标使用单独的Hive Query执行程序，请使用以下查询更新Impala缓存：`invalidate metadata `${file:pathElement(record:value('/filepath'), -3)}`. `${file:pathElement(record:value('/filepath'), -2)}`      `

  此表达式将路径的倒数第二部分用作数据库名称，并将路径的倒数第二部分用作表名称。

  如果要使用相同的Hive Query执行程序来处理Hive Query和Hadoop FS中的记录，请在Hadoop FS事件流中添加一个Expression Evaluator来执行此处理。有关示例，请参阅[案例研究：Dive for Hive的Impala元数据更新](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_szz_xwm_lx)。

## 事件产生

Hive Query执行程序可以生成可在事件流中使用的事件。

执行器每次收到事件记录时都会向Hive提交查询。启用事件生成后，执行程序每次确定提交的查询是否完成时都会生成事件。

配置单元查询事件可以任何逻辑方式使用。例如：

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

由Hive Query执行程序生成的事件记录具有以下与事件相关的记录头属性。记录标题属性存储为字符串值：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下事件类型之一：成功查询-执行者确定Hive成功运行提交的查询时生成。failed-query-当执行程序确定Hive无法运行查询时生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

Hive Query执行程序可以生成以下类型的事件记录：

- 查询成功

  当Hive成功完成查询时，执行程序将生成成功的查询事件记录。成功的查询事件记录的sdc.event.type记录头属性设置为**成功查询，**并包含以下字段：活动栏位名称描述询问Hive成功运行的查询。

- 查询失败

  当Hive无法运行查询时，执行程序将生成失败的查询事件记录。失败的查询事件记录的sdc.event.type记录头属性设置为**failed-query，**并且可以包括以下字段：活动栏位名称描述询问Hive无法运行的查询。未执行的查询任何其他未执行的查询。仅当您将执行程序配置为在查询失败时跳过运行后续查询时，事件记录才包含此字段。

## 配置Hive查询执行器

配置Hive查询执行程序以在执行程序收到事件记录时在Hive或Impala上执行查询。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_zrl_mhn_lx) | 发生事件时生成事件记录。用于[事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **配置单元”**选项卡上，配置以下属性：

   | 蜂巢属性         | 描述                                                         |
   | :--------------- | :----------------------------------------------------------- |
   | JDBC URL         | Hive的JDBC URL。您可以使用默认值，也可以在适当时用特定的数据库名称替换数据库名称的表达式。如果您的URL包含带有特殊字符的密码，则必须对特殊字符进行URL编码（也称为百分比编码）。否则，在验证或运行管道时将发生错误。例如，如果您的JDBC URL如下所示：`jdbc:hive2://sunnyvale:12345/default;user=admin;password=a#b!c$e`对您的密码进行URL编码，以便您的JDBC URL如下所示：`jdbc:hive2://sunnyvale:12345/default;user=admin;password=a%23b%21c%24e`要模拟与Hive的连接中的当前用户，您可以编辑 sdc.properties文件以将Data Collector配置为自动模拟该用户，而无需在URL中指定代理用户。请参阅[配置数据收集器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/ConfiguringDataCollector.html#task_lxk_kjw_1r)。有关指定URL的更多信息，请参见我们的[Ask StreamSets帖子](https://ask.streamsets.com/question/7/how-do-you-configure-a-hive-impala-jdbc-driver-for-data-collector/?answer=8#post-id-8)。 |
   | JDBC驱动程序名称 | 完全限定的JDBC驱动程序名称。在使用Impala JDBC驱动程序之前，请将驱动程序安装为Hive Query执行程序使用的阶段库的外部库。有关更多信息，请参阅[安装Impala驱动程序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HiveQuery.html#concept_rfq_xk4_nbb)。 |
   | 其他JDBC配置属性 | 传递给JDBC驱动程序的其他JDBC配置属性。使用 [简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击添加以添加其他属性并定义属性名称和值。使用JDBC驱动程序期望的属性名称和值。 |
   | Hadoop配置目录   | 包含Hive和Hadoop配置文件的目录的绝对路径。对于Cloudera Manager安装，请输入hive-conf。该阶段使用以下配置文件：core-site.xmlhdfs-site.xmlhive-site.xml**注意：**配置文件中的属性被此阶段定义的单个属性覆盖。 |
   | 额外的Hadoop配置 | 要使用的其他属性。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击添加以添加其他属性并定义属性名称和值。使用HDFS和Hive期望的属性名称和值。 |

3. 在**查询**选项卡上，配置以下属性：

   | 查询属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [SQL查询](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HiveQuery.html#concept_jzl_yrr_mx) | 收到事件记录后，将在Hive或Impala上执行一个或多个SQL查询。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以添加其他查询。执行程序按顺序处理多个查询，并在继续下一个查询之前等待每个查询完成。若要在将带有Hive的Hive的Drift同步解决方案与Impala结合使用时处理Hive Metastore或Hadoop FS目标中的事件，可以使用以下查询：`invalidate metadata ${record:attribute('/table')}`有关更多信息，请参阅[针对Hive的漂移同步解决方案的Impala查询](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HiveQuery.html#concept_hqg_nzh_vx)。 |
   | 在查询失败时停止                                             | 查询失败时，跳过其余查询以获取事件记录。默认情况下，在查询失败时，执行程序将继续为事件记录运行所有配置的查询。选中后，执行程序将跳过其余查询，并开始为下一个事件记录运行查询。 |