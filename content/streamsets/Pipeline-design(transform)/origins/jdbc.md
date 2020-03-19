# JDBC

JDBC源通过JDBC驱动程序从数据库表中读取数据。

源可以读取表中的所有列或仅读取表中的指定列。在每个批次中，源读取指定数量的行，并将这些行均匀地分布在指定分区上。读取最后一行时，原点会保存指定偏移量列中的值。在随后的批次中，原点使用偏移量来定位最后读取的行，并从下一行开始读取。

配置JDBC原始时，可以指定数据库连接信息以及要使用的任何其他JDBC配置属性。您将表配置为读取，并可以选择指定要从表中读取的列。您定义偏移量列，每个批处理中要包括的最大行数以及用于从数据库表读取的分区数。您可以选择配置与JDBC驱动程序相关的高级属性。

您可以将源配置为仅加载一次数据，并缓存数据以在整个管道运行中重复使用。或者，您可以配置源以缓存每一批数据，以便可以将其有效地传递到多个下游批次。 您还可以将原点配置为[跳过跟踪偏移量](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Offsets.html#concept_qqc_xsx_gjb)，从而可以在每次启动管道时读取整个数据集。

## 数据库供应商和驱动程序

JDBC源可以从多个数据库供应商读取数据库数据。

StreamSets已使用以下数据库供应商，版本和JDBC驱动程序测试了此来源：

| 数据库供应商        | 版本和驱动程序                                               |
| :------------------ | :----------------------------------------------------------- |
| Microsoft SQL服务器 | 带有SQL Server JDBC 4.4.0驱动程序的SQL Server 2017           |
| 的MySQL             | 带有MySQL Connector / J 8.0.12驱动程序的MySQL 5.7            |
| PostgreSQL的        | 带有PostgreSQL 9.4.1212驱动程序的PostgreSQL 9.6.2（JDBC 4.2） |

## 安装JDBC驱动程序

在使用JDBC源之前，必须为源连接到的数据库安装JDBC驱动程序。在安装必需的驱动程序之前，原始服务器无法访问数据库。

有关安装驱动程序的说明，请参阅[Data Collector文档](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/ExternalLibs.html#concept_pdv_qlw_ft)。

默认情况下，Transformer将已安装的JDBC驱动程序捆绑到启动的Spark应用程序中，以便该驱动程序在群集中的每个节点上都可用。如果您希望在每个Spark节点上手动下载JDBC驱动程序，则可以将该阶段配置为在阶段的“高级”选项卡上跳过捆绑驱动程序。

如果安装以下供应商之一提供的JDBC驱动程序，则该阶段会自动从配置的JDBC连接字符串中检测JDBC驱动程序类名称：

- 阿帕奇德比
- IBM DB2
- Microsoft SQL服务器
- 的MySQL
- 甲骨文
- PostgreSQL的
- Teradata

如果安装定制JDBC驱动程序或其他供应商提供的驱动程序，则必须在阶段的“高级”选项卡上指定JDBC驱动程序类名称。

## 偏移列

JDBC原点使用offset列来跟踪在数据库表中处理的行。默认情况下，原点使用表中的主键作为偏移列。

当您要使用其他列作为偏移量或主键为组合键时，可以将另一列指定为偏移量。原点不能使用复合主键作为偏移量列。原点需要单个列作为偏移列。

offset列必须是表中具有唯一非空值的列，例如主键或索引列，并且必须是以下数据类型之一：

- 比金特
- 小数或数值小数位数为0
- 整数
- Smallint

读取批处理中的最后一行时，原点会保存偏移量列中的值。在随后的批次中，原点将从下一行开始读取。

例如，假设一个orders表具有一个`order_id`存储自动生成的主键的列。当原点读取订单表时，它将 `order_id`用作偏移列。原点保存每个批次中读取的最后一个订单，并通过读取下一个订单开始下一个批次。

## 分区

与运行其他任何应用程序一样，Spark运行Transformer管道，将数据拆分为多个分区，并在分区上并行执行操作。 Spark根据流水线的来源确定如何将流水线数据拆分为初始分区。

对于JDBC源，Spark根据您为源配置的分区数确定分区。Spark为每个分区创建一个到数据库的连接。

在定义要使用的分区数时，请考虑以下事项：

- 集群的大小和配置。
- 正在处理的数据量。
- 可以与数据库建立的并发连接数。

如果管道由于JDBC原始遇到内存不足错误而失败，则可能需要增加该原始分区的数量。

除非处理器使Spark乱序处理数据，否则Spark会在整个管道中使用这些分区。当您需要更改管道中的分区时，请使用[Repartition处理器](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/Repartition.html#concept_cm5_lfg_wgb)。

## 配置JDBC原始

配置JDBC源，以使用JDBC驱动程序从数据库表中读取数据。

1. 在“属性”面板上的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 仅加载一次数据                                               | 批量读取数据并缓存结果以备重用。用于在流执行模式管道中[执行查找](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Lookups.html#concept_f2z_5yw_g3b)。使用原点执行查找时， 请勿限制批处理大小。所有查询数据都应在一个批次中读取。在批处理执行模式下，将忽略此属性。 |
   | [缓存数据](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/CachingData.html#concept_q2r_xm4_33b) | 缓存处理后的数据，以便可以在多个下游阶段重用该数据。当阶段将数据传递到多个阶段时，用于提高性能。当管道以[荒谬的方式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Ludicrous.html#concept_m4y_lbq_g3b)运行时，缓存会限制下推式优化。未启用“仅一次加载数据”时可用。当原点一次加载数据时，它也会缓存数据。 |
   | 跳过偏移跟踪                                                 | 跳过跟踪偏移量。在流传输管道中，这导致读取每个批次中的所有可用数据。在批处理管道中，这导致每次管道启动时都读取所有可用数据。 |

2. 在“ **连接”**选项卡上，配置以下属性：

   | 连接属性         | 描述                                                         |
   | :--------------- | :----------------------------------------------------------- |
   | JDBC连接字符串   | 用于连接数据库的连接字符串。使用数据库所需的连接字符串格式。您可以选择在连接字符串中包括用户名和密码。有关连接到 SQL Server 2019大数据群集数据库的信息，请参阅[SQL Server 2019 JDBC连接信息](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/SQLServer-JDBCConnectString.html#concept_bfs_3nm_1kb)。在运行管道之前，必须为此数据库安装JDBC驱动程序。 |
   | 使用凭证         | 在“连接”选项卡上启用输入凭据。在JDBC连接字符串中不包括凭据时使用。 |
   | 用户名           | JDBC连接的用户名。**提示：**为了保护敏感信息，可以按照Data Collector文档中的说明使用 [运行时资源](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或[凭据存储](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。 |
   | 密码             | JDBC用户名的密码。**提示：**为了保护敏感信息，可以按照Data Collector文档中的说明使用 [运行时资源](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或[凭据存储](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。 |
   | 其他JDBC配置属性 | 要使用的其他JDBC配置属性。要添加属性，请单击**添加**并定义JDBC属性名称和值。使用JDBC期望的属性名称和值。 |

3. 在**表格**选项卡上，配置以下属性：

   | 表属性                                                       | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 架构图                                                       | 表所在的模式的名称。定义何时数据库需要架构。                 |
   | 表                                                           | 数据库表读取。                                               |
   | [偏移列](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/JDBC.html#concept_h5j_qwy_vgb) | 表列用于跟踪已处理的行。默认情况下，原点使用表中的主键作为偏移列。您可以将另一列指定为偏移列。 |
   | 每批最大行数                                                 | 批处理中包含的最大表行数。对于 [批处理管道](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/ExecutionMode.html#concept_lgy_24q_qgb)，此属性确定每个管道运行中处理的总行数。对于流传输管道，此属性确定一次处理的行数。使用-1读取单个批处理中的所有行。 |
   | [分区数](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/JDBC.html#concept_jmy_mq3_xgb) | 读取批处理时使用的分区数。预设值为10。                       |
   | 阅读列                                                       | 要从表中读取的列。如果您未指定任何列，则原点将读取表中的所有列。单击**添加**图标以指定其他列。 |

4. 在“ **高级”**选项卡上，可以选择配置高级属性。

   这些属性的默认值在大多数情况下都应该起作用：

   | 先进物业                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 最小分区大小                                                 | 每个分区中包含的最小记录数。输入较大的值，以避免创建较小的分区。使用-1设置无限制。默认值为10,000条记录。 |
   | 指定提取大小                                                 | 指定原点使用的提取大小。                                     |
   | 提取大小                                                     | 每个数据库往返要获取的最大行数。有关配置访存大小的更多信息，请参见数据库文档。 |
   | 使用自定义最小/最大查询                                      | 使用自定义的最小和最大SQL查询，而不是由来源生成的查询。      |
   | 自定义最小/最大查询                                          | 自定义运行的最小和最大SQL查询。该查询必须恰好返回两个Long列的一行。默认情况下，原点生成并运行以下查询以从offset列中检索最小和最大值：`SELECT MIN(), MAX() FROM `如果您的数据库包含带有索引的单独表，例如偏移列的每日或每周最大值和最小值，则可以定义一个自定义的最小值和最大值查询。 |
   | [JDBC驱动程序](https://streamsets.com/documentation/controlhub/latest/help/transformer/Destinations/JDBC-D.html#concept_us1_mmq_jhb) | 确定Transformer如何将JDBC驱动程序捆绑到Spark应用程序中：不捆绑驱动程序-当您希望在集群中的每个Spark节点上手动下载JDBC驱动程序时使用。捆绑来自连接字符串的驱动程序-用于捆绑大多数数据库供应商的驱动程序。该阶段从配置的JDBC连接字符串中自动检测驱动程序类名称。捆绑定制驱动程序-当阶段无法从已配置的JDBC连接字符串检测到驱动程序时，用于捆绑驱动程序。该阶段使用您输入的驱动程序类名称。默认是从JDBC连接字符串捆绑驱动程序。 |
   | 驱动程序类别名称                                             | JDBC驱动程序的类名。                                         |