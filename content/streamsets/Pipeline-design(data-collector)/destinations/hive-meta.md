# Hive Metastore

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310184522366.png) 资料收集器

Hive Metastore目标与Hive元数据处理器和Hadoop FS或MapR FS目标一起使用，作为Hive漂移同步解决方案的一部分。

Hive Metastore目标使用Hive元数据处理器生成的元数据记录来创建和更新Hive表。这使Hadoop FS和MapR FS目标能够将漂移的Avro或Parquet数据写入HDFS或MapR FS。

Hive Metastore目标将元数据记录中的信息与Hive表进行比较，然后根据需要创建或更新表。例如，当Hive元数据处理器遇到需要新Hive表的记录时，它将元数据记录传递到Hive Metastore目标，并且该目标将创建表。

配置单元表名称，列名称和分区名称使用小写字母创建。包含大写字母的名称在Hive中变为小写。

请注意，Hive Metastore目标不会处理数据。它仅处理由Hive元数据处理器生成的元数据记录，并且必须在处理器的元数据输出流的下游。

配置Hive Metastore时，可以定义Hive的连接信息，Hive和Hadoop配置文件的位置，并可以选择指定其他必需的属性。您还可以启用Kerberos身份验证。您还可以设置目标的最大高速缓存大小，确定如何创建和存储新表，以及配置自定义记录头属性。

目的地还可以为事件流生成事件。有关事件框架的更多信息，请参见《[数据流触发器概述》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

**重要：**在多个管道中使用目标时，请注意避免对同一表的并发写入或冲突写入。

有关Hive的漂移同步解决方案以及处理Avro和Parquet数据的案例研究的更多信息，请参见[Hive的漂移同步解决方案](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Hive_Drift_Solution/HiveDriftSolution_title.html#concept_phk_bdf_2w)。有关教程，请查看我们[在GitHub上](https://github.com/streamsets/tutorials/blob/master/tutorial-hivedrift/readme.md)的[教程](https://github.com/streamsets/tutorials/blob/master/tutorial-hivedrift/readme.md)。

## 元数据处理

处理记录时，Hive Metastore目标将执行以下任务：

- 根据需要创建或更新Hive表

  对于每个包含创建或更新表请求的元数据记录，目标都会为该表检查Hive。如果元数据记录中的表不存在或与Hive表不同，则目的地将根据需要创建或更新该表。配置单元表名称，列名称和分区名称使用小写字母创建。有关更多信息，请参见[配置单元名称和支持的字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/HiveMetadata.html#concept_vt5_z5l_nx)。

  **注意：** 目标可以创建表和分区。它可以将列添加到表中，而忽略现有列。它不会从表中删除现有列。

- 根据需要创建新的Avro模式

  当针对Hive的Drift Synchronization Solution处理Avro数据时，您可以配置Hive Metastore目标以生成Avro模式。在这种情况下，Hive Metastore将执行以下任务：

  对于每个包含架构更改的元数据记录，目标都会检查Hive中指定表的当前列集。当存在兼容的差异时，目标将生成一个新的Avro架构，其中包含了差异。当一个单独的实体在Hive Metadata处理器评估与Hive Metastore目标之间的瞬间更改目标表时，可能会发生这种情况。

## 蜂巢表生成

当Hive的Drift Synchronization Solution处理Parquet数据时，目标在生成表时使用Stored as Parquet子句，因此它不需要为每次更改都生成新的架构。

当针对Hive的Drift同步解决方案处理Avro数据时，Hive Metastore目标可以使用以下方法生成Hive表：

- 使用“存储为Avro”子句

  使用包含“存储为Avro”子句的查询生成表。使用Stored As Avro子句时，目标不需要为Hive表中的每个更改生成Avro模式。

  这是表生成的默认和推荐方法。启用“ **存储为Avro”**属性以使用此方法。

- 没有存储为Avro子句

  生成查询中不包含“存储为Avro”子句的表。而是，目标为每个Hive表更新生成一个Avro模式。目标使用以下格式作为模式名称： `avro_schema___.avsc`。

  目标将Avro架构存储在HDFS中。您可以配置目标保存模式的位置。您可以指定完整路径或相对于表目录的路径。默认情况下，目标将模式保存在表目录的.schemas子文件夹中。

  您可以配置目标以指定的HDFS用户身份生成和存储架构。必须将Data Collector配置为HDFS中的代理用户。

## 快取

Hive Metastore目标向Hive查询信息并缓存结果。如果可能，它将使用缓存来避免不必要的Hive查询。

目标缓存以下Hive元数据：

- 要写入的数据库和表
- 蜂巢表属性
- 表中的列名和类型
- 分区值

### 缓存大小和逐出

您可以配置缓存的最大大小。当缓存达到指定的限制时，它将使用LRU逐出策略，该策略将删除最近最少使用的数据，以允许将新条目写入缓存。

## 事件产生

Hive Metastore目标可以生成可在事件流中使用的事件。启用事件生成后，目的地每次更新Hive Metastore时（包括创建表，添加列或创建分区时），都会创建事件记录。在生成新的Avro模式文件并将其写入目标系统时，它还会创建事件。

**注意：**由于目标位置不会更改或删除现有结构，因此对现有表，列和分区的更新将视为创建更新。

Hive Metastore事件可以任何逻辑方式使用。例如：

- 使用Hive Query执行程序在更新Hive Metastore之后运行Hive或Impala查询。

  有关示例，请参阅[案例研究：Dive for Hive的Impala元数据更新](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_szz_xwm_lx)。

- 使用HDFS文件元数据执行程序可以移动或更改已关闭文件的权限。

  有关示例，请参见[案例研究：输出文件管理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_d1q_xl4_lx)。

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

Hive Metastore事件记录包括以下与事件相关的记录头属性。记录标题属性存储为字符串值：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型之一：new-table-目标创建新表时生成。new-columns-在目标创建新列时生成。新分区-在目标创建新分区时生成。avro-schema-store-在目标生成新的Avro模式文件并将其写入目标系统时生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

Hive Metastore可以生成以下类型的事件记录：

- 新表

  目标在创建新表时会生成新表事件记录。新表事件记录的`sdc.event.type`记录头属性设置为，`new-table`并包含以下字段：领域描述表使用以下格式的标准表格名称： `''.''`。列每个新列均包含以下信息的列表列表字段：栏名Hive数据类型，精度和小数位数例如：密钥：`id`值：INT密钥：`desc`值：STRING隔断具有以下信息的列表列表字段：分区名称分区值例如：名称：`dt`价值：2017-01-01

- 新专栏

  目标在表中创建新列时会生成新的列事件记录。新列事件记录的`sdc.event.type`记录标题属性设置为，`new-column`并包括以下字段：领域描述表使用以下格式的标准表格名称：`.`。列每个新列均包含以下信息的列表列表字段：栏名Hive数据类型，精度和小数位数

- 新分区

  目标在表中创建新分区时会生成新的分区事件记录。新的分区事件记录的`sdc.event.type` 记录头属性设置为`new-partition`，包括以下字段：领域描述表使用以下格式的标准表格名称：`.`。隔断具有以下信息的列表列表字段：分区名称分区值

- 新的Avro模式文件

  如果“ Hive的漂移同步解决方案”处理Avro数据并且未启用目标中的“存储为Avro”选项，则目标每次创建或更新表时都会生成并写入Avro模式文件事件。

  新的Avro模式文件的`sdc.event.type`Record Header属性设置为，`avro-schema-store`并包含以下字段：领域描述表使用以下格式的标准表格名称：`.`。avro_schema新的Avro模式。schema_location写入模式文件的位置。

## Kerberos身份验证

使用Kerberos身份验证时，Data Collector 使用Kerberos主体和密钥表连接到HiveServer2。默认情况下，Data Collector使用启动它的用户帐户进行连接。

Kerberos主体和密钥表在Data Collector 配置文件中定义` $SDC_CONF/sdc.properties`。要使用Kerberos身份验证，请在Data Collector 配置文件中配置所有Kerberos属性，并将Kerberos主体包括在HiveServer2 JDBC URL中。

有关为Data Collector启用Kerberos身份验证的详细信息，请参阅Data Collector文档中的[Kerberos身份验证](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_hnm_n4l_xs)。

## 配置单元属性和配置文件

您必须将Hive Metastore配置为使用Hive和Hadoop配置文件以及各个属性。

- 配置文件

  Hive Metastore目标需要以下配置文件：core-site.xmlhdfs-site.xmlhive-site.xml

- 个别属性

  您可以在目标中配置单个Hive属性。要添加Hive属性，请指定确切的属性名称和值。处理器不验证属性名称或值。**注意：**各个属性会覆盖配置文件中定义的属性。

## 配置Hive Metastore目标

配置Hive Metastore目标以处理来自Hive元数据处理器的元数据记录，并根据需要更新Hive表和Avro模式。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/HiveMetastore.html#concept_drg_lwc_rx) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | 必填项                                                       | 由于目标仅处理元数据记录，因此该属性不相关。                 |
   | 前提条件                                                     | 由于目标仅处理元数据记录，因此该属性不相关。                 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **配置单元”**选项卡上，配置以下属性：

   | 蜂巢属性         | 描述                                                         |
   | :--------------- | :----------------------------------------------------------- |
   | JDBC URL         | Hive的JDBC URL。您可以使用默认值，也可以在适当时用特定的数据库名称替换数据库名称的表达式。如果您的URL包含带有特殊字符的密码，则必须对特殊字符进行URL编码（也称为百分比编码）。否则，在验证或运行管道时将发生错误。例如，如果您的JDBC URL如下所示：`jdbc:hive2://sunnyvale:12345/default;user=admin;password=a#b!c$e`对您的密码进行URL编码，以便您的JDBC URL如下所示：`jdbc:hive2://sunnyvale:12345/default;user=admin;password=a%23b%21c%24e`要模拟与Hive的连接中的当前用户，您可以编辑 sdc.properties文件以将Data Collector配置为自动模拟该用户，而无需在URL中指定代理用户。请参阅[配置数据收集器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/ConfiguringDataCollector.html#task_lxk_kjw_1r)。有关指定URL的更多信息，请参见我们的[Ask StreamSets帖子](https://ask.streamsets.com/question/7/how-do-you-configure-a-hive-impala-jdbc-driver-for-data-collector/?answer=8#post-id-8)。 |
   | JDBC驱动程序名称 | 完全限定的JDBC驱动程序名称。                                 |
   | 其他JDBC配置属性 | 传递给JDBC驱动程序的其他JDBC配置属性。使用 [简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击添加以添加其他属性并定义属性名称和值。使用JDBC驱动程序期望的属性名称和值。 |
   | Hadoop配置目录   | 包含Hive和Hadoop配置文件的目录的绝对路径。对于Cloudera Manager安装，请输入hive-conf。该阶段使用以下配置文件：core-site.xmlhdfs-site.xmlhive-site.xml**注意：**配置文件中的属性被此阶段定义的单个属性覆盖。 |
   | 额外的Hadoop配置 | 要使用的其他属性。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击添加以添加其他属性并定义属性名称和值。使用HDFS和Hive期望的属性名称和值。 |

3. 在“ **高级”**选项卡上，可以选择配置以下属性：

   | 先进物业                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [存储为Avro](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/HiveMetastore.html#concept_wyr_5jv_hw) | 当“ Hive的漂移同步解决方案”处理Avro数据时，在生成Hive表的SQL命令中使用“存储为Avro”子句。选中后，查询中将不包含Avro模式URL。 |
   | 架构文件夹位置                                               | 未选择“存储为Avro”时存储Avro模式的位置。使用斜杠（/）指定完全限定的路径。省略斜杠以指定相对于表目录的路径。如果未指定，则架构存储在表目录的.schema子文件夹中。 |
   | HDFS用户                                                     | 生成架构时要使用的目标的可选HDFS用户。                       |
   | 标头属性表达式                                               | 生成事件时，将指定的标题属性添加到事件记录。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击添加图标以包括自定义记录标题属性。然后，输入属性的名称和值。您可以使用表达式来定义要使用的名称和值。 |
   | [最大缓存大小（条目）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/HiveMetastore.html#concept_f4y_spy_dw) | 高速缓存中的最大条目数。当高速缓存达到最大大小时，将逐出最旧的高速缓存条目以允许新数据。默认值为-1，无限的缓存大小。 |