# 蜂巢

Hive源从Hive表中读取数据。Hive是一个事务存储层，可在Hadoop分布式文件系统（HDFS）和Apache Spark上运行。Hive将文件存储在HDFS上的表中。

默认情况下，来源使用存储在Transformer计算机上Hive配置文件中的连接信息从Hive读取。或者，源可以使用存储在您指定的外部Hive Metastore中的连接信息。

配置Hive原点时，您指示原点应在增量模式还是完全查询模式下运行。您定义要使用的查询，offset列以及（可选）要使用的初始偏移量。需要时，您可以为存储配置信息的外部Hive Metastore指定URI。

您可以将源配置为仅加载一次数据，并缓存数据以在整个管道运行中重复使用。或者，您可以配置源以缓存每一批数据，以便可以将其有效地传递到多个下游批次。 您还可以将原点配置为[跳过跟踪偏移量](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Offsets.html#concept_qqc_xsx_gjb)，从而可以在每次启动管道时读取整个数据集。

## 分区

与运行其他任何应用程序一样，Spark运行Transformer管道，将数据拆分为多个分区，并在分区上并行执行操作。 Spark根据流水线的来源确定如何将流水线数据拆分为初始分区。

使用Hive源，Spark根据Hive源表中配置的分区确定分区。如果未配置任何分区，则源将读取单个分区内的所有可用数据。

除非处理器使Spark乱序处理数据，否则Spark在整个管道中使用由源创建的分区。当您需要更改管道中的分区时，请使用[Repartition处理器](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/Repartition.html#concept_cm5_lfg_wgb)。

## 读取Delta Lake托管表

您可以使用Hive原点以流执行模式或具有偏移量跟踪的批处理模式读取Delta Lake管理表。对于所有其他情况，例如读取非托管表或在不执行偏移量跟踪的情况下以批处理方式读取，请使用[Delta Lake原点](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/DLake.html#concept_c3r_l4n_y3b)。

## 增量和完整查询模式

Hive原点可以在完全查询模式或增量模式下运行。默认情况下，原点以完全查询模式运行。

当原点以完全查询模式运行且未定义初始偏移量时，每次管道运行时，原点都会处理所有可用数据。如果配置初始偏移，则每次管道运行时，原点都会以初始偏移开始读取。

当原点以增量模式运行时，第一个管道运行与完全查询模式相同：如果已定义，则原点以初始偏移量开始读取。否则，它将读取所有可用数据。当流水线停止时，原点将存储停止处理的偏移量。对于后续的管道运行，原点从最后保存的偏移量开始读取。

### SQL查询

SQL查询定义了从Hive返回的数据。您可以使用任何有效的SQL查询，但是查询准则取决于您将源配置为以增量模式还是完全查询模式运行。

#### 增量模式准则

当您为增量模式定义SQL查询时，Hive原点在查询中需要WHERE子句和ORDER BY子句。

定义WHERE和ORDER BY子句时，请遵循以下准则：

- WHERE子句

  在WHERE子句中，包括offset列和offset值。使用`$offset`变量表示偏移值。

  在原点中，还可以配置属性以定义偏移列和初始偏移值。这些属性与SQL查询一起使用，以确定传递给 Hive 的查询。

  例如，假设您将来源配置为使用以下查询：`SELECT * FROM employees WHERE employeeId > $offset`

  您还可以将其指定`employeeId`为偏移量列，`20052`并将其指定为原点属性中的初始偏移量。当管道开始，起源内容替换 `$offset`与`20052`查询，如下所示：`SELECT * FROM employees WHERE employeeId > 20052`

  当管道停止时，原点将在其停止处存储偏移量，然后`$offset`在下次启动管道时使用该偏移值替换 。

  **提示：**当偏移值是字符串时，请`$offset`用单引号引起来。

- ORDER BY子句

  在ORDER BY子句中，将offset列作为第一列，以避免返回重复数据。**注意：**在ORDER BY子句中使用不是主键或索引列的列可能会降低性能。

  例如，以下查询从“发票”表返回数据，其中该`id`列是“偏移量”列。查询返回`id`大于偏移值的所有数据，并按`id` 列对数据排序：`SELECT * FROM invoice WHERE id > $offset ORDER BY id`

#### 全模式准则

您可以为完全模式定义任何类型的SQL查询。

例如，您可以运行以下查询以从发票表返回所有数据：

```
SELECT * FROM invoice
```

当您为完全模式定义SQL查询时，可以选择使用与增量模式相同的准则来包括WHERE和ORDER BY子句。但是，使用这些子句从大表中读取可能会导致性能问题。

## 其他配置单元配置属性

如果需要，您可以将其他Hive配置属性传递给Hive作为其他Spark配置属性。在管道属性面板的“群集”选项卡上配置其他Spark配置属性。

管道中定义的Hive配置属性将覆盖Hive配置文件中定义的属性。

## 配置配置单元来源

配置Hive来源以从Hive表中读取。

1. 在“属性”面板上的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 仅加载一次数据                                               | 批量读取数据并缓存结果以备重用。用于在流执行模式管道中[执行查找](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Lookups.html#concept_f2z_5yw_g3b)。使用原点执行查找时， 请勿限制批处理大小。所有查询数据都应在一个批次中读取。在批处理执行模式下，将忽略此属性。 |
   | [缓存数据](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/CachingData.html#concept_q2r_xm4_33b) | 缓存处理后的数据，以便可以在多个下游阶段重用该数据。当阶段将数据传递到多个阶段时，用于提高性能。当管道以[荒谬的方式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Ludicrous.html#concept_m4y_lbq_g3b)运行时，缓存会限制下推式优化。未启用“仅一次加载数据”时可用。当原点一次加载数据时，它也会缓存数据。 |
   | 跳过偏移跟踪                                                 | 跳过跟踪偏移量。在流传输管道中，这导致读取每个批次中的所有可用数据。在批处理管道中，这导致每次管道启动时都读取所有可用数据。 |

2. 在“ **配置单元”**选项卡上，配置以下属性：

   | 蜂巢属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [增量模式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Hive.html#concept_gxj_wmf_f3b) | 使原点以增量模式运行。未启用时，原点以完全查询模式运行。     |
   | Hive Metastore URI                                           | 外部Hive元存储库的URI的逗号分隔列表（包含要使用的Hive连接信息）。使用以下URI格式：`thrift://:`如果未定义，则起点使用在Transformer计算机上的`hive-site.xml`和 `core-site.xml`Hive配置文件中定义的连接信息 。 |
   | [SQL查询](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Hive.html#concept_qkt_v3f_f3b) | 要使用的SQL查询。查询要求因源是以增量模式还是完全查询模式运行而异。 |
   | 初始偏移                                                     | 可选的初始偏移值。`$offset`在查询中使用 参数时，启动管道时，该值将替换为参数。在增量模式下必需。 |
   | 偏移列                                                       | 列跟踪读取进度。最佳做法是，偏移列应为不包含空值的增量且唯一的列。强烈建议在此列上建立索引，因为基础查询在此列上使用ORDER BY子句和不等式运算符。在增量模式下必需。 |