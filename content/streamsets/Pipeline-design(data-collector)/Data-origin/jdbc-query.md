# JDBC查询使用者

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310172044820.png) 资料收集器

JDBC查询使用者源通过JDBC连接使用用户定义的SQL查询读取数据库数据。原点将数据作为具有列名和字段值的映射返回。

**提示：** Data Collector 包括使用JDBC连接的其他来源。使用符合您需求的原产地：

- 使用[JDBC Multitable Consumer](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MultiTableJDBCConsumer.html#concept_zp3_wnw_4y)起源进行数据库复制或从同一数据库中的多个表中读取。JDBC Multitable Consumer起源基于您定义的表配置生成SQL查询。
- 使用更改的数据源从特定的数据库读取更改的数据并处理后续更改。
- 使用[Teradata使用者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Teradata.html#concept_zp3_wnw_4y)源从Teradata数据库表中读取数据，并快速检索大量数据。

配置原点时，可以定义原点用来从单个表或表联接中读取数据的SQL查询。

在配置JDBC查询使用者时，您可以指定连接信息，查询间隔和自定义JDBC配置属性来确定源如何连接到数据库。您可以配置查询模式和SQL查询来定义数据库返回的数据。您也可以从SQL查询中调用存储过程。当源数据库具有高精度时间戳（例如IBM Db2 TIMESTAMP（9）字段）时，可以将源配置为写入字符串而不是日期时间值以保持精度。

您可以配置JDBC Query Consumer以对将信息存储在表中的数据库执行更改数据捕获。并且您可以指定遇到不支持的数据类型时原点的作用：将数据转换为字符串或停止管道。

您可以指定驱动程序需要的自定义属性。您可以配置高级连接属性。要使用低于4.0的JDBC版本，请指定驱动程序类名称并定义运行状况检查查询。

默认情况下，源创建JDBC标头属性以在记录标头中提供有关源数据的信息。

**注意：**处理Microsoft SQL Server CDC数据的功能已在此来源中弃用，并将在以后的版本中删除。要处理Microsoft SQL Server CDC表中的数据，请使用[SQL Server CDC客户端origin](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_ut3_ywc_v1b)。要处理Microsoft SQL Server更改跟踪表中的数据，请使用[SQL Server更改跟踪源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerChange.html#concept_ewq_b2s_r1b)。

源可以为事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

## 数据库供应商和驱动程序

JDBC查询使用者源可以从多个数据库供应商读取数据库数据。

StreamSets已使用以下数据库供应商，版本和JDBC驱动程序测试了该阶段：

| 数据库供应商        | 版本和驱动程序                                               |
| :------------------ | :----------------------------------------------------------- |
| 的MySQL             | 带有MySQL Connector / J 8.0.12驱动程序的MySQL 5.7带有MySQL Connector / J 8.0.12驱动程序的MySQL 8.0 |
| PostgreSQL的        | PostgreSQL 9.4.18PostgreSQL 9.6.2PostgreSQL 9.6.9PostgreSQL 10.4连接到PostgreSQL数据库时，不需要安装JDBC驱动程序。Data Collector包括PostgreSQL所需的JDBC驱动程序。 |
| 甲骨文              | 带有Oracle 11.2.0 JDBC驱动程序的Oracle 11g                   |
| Microsoft SQL服务器 | SQL Server 2017连接到Microsoft SQL Server时，不需要安装JDBC驱动程序。Data Collector包括SQL Server所需的JDBC驱动程序。 |

### Oracle数据类型



JDBC查询使用者源将Oracle数据类型转换为Data Collector 数据类型。

该阶段支持以下Oracle数据类型：

| Oracle数据类型     | 数据收集器数据类型 |
| :----------------- | :----------------- |
| 数                 | 小数               |
| 烧焦               | 串                 |
| Varchar            | 串                 |
| Varchar2           | 串                 |
| 恩查尔             | 串                 |
| NvarChar2          | 串                 |
| Binary_float       | 浮动               |
| Binary_double      | 双                 |
| 日期               | 约会时间           |
| 时间戳记           | 约会时间           |
| 带时区的时间戳     | Zoned_datetime     |
| 带本地时区的时间戳 | Zoned_datetime     |
| 长                 | 串                 |
| 斑点               | 字节数组           |
| b                  | 串                 |
| Nclob              | 串                 |
| XML类型            | 串                 |

## 安装JDBC驱动程序

在使用JDBC查询消费者来源之前，请为数据库安装JDBC驱动程序。您必须安装所需的驱动程序才能访问数据库。

**注意：** 连接到PostgreSQL数据库时，不需要安装JDBC驱动程序。Data Collector包括PostgreSQL所需的JDBC驱动程序。

有关安装其他驱动程序的信息，请参阅 Data Collector 文档 中的“ [安装外部库](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/ExternalLibs.html%23concept_pdv_qlw_ft) ”。

## 偏移列和偏移值

JDBC查询使用者使用偏移列和初始偏移值来确定从何处开始读取表中的数据。在SQL查询的WHERE子句中包括offset列和offset值。

偏移列必须是表中具有唯一非空值的列，例如主键或索引列。初始偏移量值是您希望JDBC查询使用者开始读取的偏移量列中的值。

原点执行增量查询时，必须配置偏移列和偏移值。对于完整查询，您可以选择配置它们。

## 完全和增量模式

JDBC查询使用者可以两种方式执行查询：

- 增量模式

  JDBC查询使用者执行增量查询时，它将初始偏移量用作第一个SQL查询中的偏移量值。当原点完成对第一个查询的结果的处理时，它将保存它处理的最后一个偏移值。然后，它将等待指定的查询间隔，然后再执行后续查询。

  原点执行后续查询时，它会根据最后保存的偏移量返回数据。您可以重设原点以使用初始偏移值。

  对于仅追加表或不需要捕获对旧行的更改时，请使用增量模式。默认情况下，JDBC查询使用者使用增量模式。

- 全模式

  当JDBC查询使用者源执行完整查询时，它将运行指定的SQL查询。如果您选择配置偏移量列和初始偏移值，则原点每次在请求数据时都会使用初始偏移作为SQL查询中的偏移值。

  当原始完成对完整查询结果的处理时，它将等待指定的查询间隔，然后再次执行相同的查询。

  使用完全模式捕获所有行更新。您可以在管道中使用Record Deduplicator，以最大程度地减少重复的行。不适合用于大型桌子。

  **提示：**如果要处理单个完整查询的结果，然后停止管道，则可以启用原点来生成事件，并使用Pipeline Finisher自动停止管道。有关更多信息，请参见 [事件生成](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JDBCConsumer.html#concept_o1c_kwr_kz)。

## 复苏

JDBC Query Consumer在执行增量查询时有意或意外停止后支持恢复。完整查询不支持恢复。

在增量模式下，JDBC查询使用者使用offset列中的offset值来确定在有意或意外停止后从何处继续处理。为确保增量模式下的无缝恢复，请使用主键或索引列作为偏移列。在JDBC Query Consumer处理数据时，它在内部跟踪偏移值。当管道停止时，JDBC查询使用者将记录停止处理数据的位置。重新启动管道时，它将从上次保存的偏移量继续。

当JDBC Query Consumer执行完整查询时，源在重新启动管道后再次运行完整查询。

## SQL查询

SQL查询定义了从数据库返回的数据。

您可以在“ JDBC”选项卡上的“ SQL查询”属性中定义查询。或者，您可以在[运行时资源中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)定义查询，然后使用`runtime:loadResource`SQL Query属性中的函数在运行时从资源文件加载查询。例如，您可以为属性输入以下表达式：

```
${runtime:loadResource("myquery.sql", false)}
```

SQL查询准则取决于您是将源配置为执行增量查询还是完整查询。

**注意：** 默认情况下，Oracle对模式，表和列名称使用所有大写字母。仅当使用名称周围的引号创建模式，表或列时，名称才可以是小写或大小写混合。

### 增量模式的SQL查询

当您为增量模式定义SQL查询时，JDBC查询使用者在查询中需要WHERE和ORDER BY子句。

在查询中定义WHERE和ORDER BY子句时，请使用以下准则：

- 在WHERE子句中，包括offset列和offset值

  原点使用偏移量列和值来确定返回的数据。将两者都包含在查询的WHERE子句中。

- 使用OFFSET常数表示偏移值

  在WHERE子句中，使用$ {OFFSET}表示偏移值。

  例如，当您启动管道时，以下查询将从表中返回所有数据，其中偏移列中的数据大于初始偏移值：`SELECT * FROM  WHERE  > ${OFFSET}`**提示：**当偏移量值为字符串时，请将$ {OFFSET}括在单引号中。

- 在ORDER BY子句中，将offset列作为第一列

  为避免返回重复的数据，请将offset列用作ORDER BY子句中的第一列。

  **注意：**在ORDER BY子句中使用不是主键或索引列的列可能会降低性能。

例如，下面的增量模式查询从“发票”表返回数据，其中“ ID”列为“ offset”列。查询返回ID大于偏移量的所有数据，并按ID排序数据：

```
 SELECT * FROM invoice WHERE id > ${OFFSET} ORDER BY id
```

### 全模式的SQL查询

您可以为完全模式定义任何类型的SQL查询。

例如，您可以运行以下查询以从发票表返回所有数据：

```
SELECT * FROM invoice
```

当您为完全模式定义SQL查询时，可以选择使用与增量模式相同的准则来包括WHERE和ORDER BY子句。但是，使用这些子句从大表中读取可能会导致性能问题。

### 存储过程

您可以将存储过程与JDBC Query Consumer源一起使用。

在完全模式下使用JDBC Query Consumer时，可以从SQL查询中调用存储过程。在增量模式下使用源时，请勿调用存储过程。

## JDBC记录标题属性

JDBC查询使用者可以生成JDBC记录头属性，这些属性提供有关每个记录的其他信息，例如字段的原始数据类型或记录的源表。源从JDBC驱动程序接收这些详细信息。

您可以使用`record:attribute`或 `record:attributeOrDefault`函数来访问属性中的信息。有关使用记录标题属性的更多信息，请参见[使用标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_rd2_ghz_dz)。

JDBC头属性包括一个用户定义的前缀，以区分JDBC头属性和其他记录头属性。默认情况下，前缀为“ jdbc”。

您可以使用“高级”选项卡上的“创建JDBC标头属性”和“ JDBC标头前缀属性”来更改原点使用的前缀，并且可以配置原点不创建JDBC标头属性。

源可以提供以下JDBC标头属性：

| JDBC标头属性                  | 描述                                                         |
| :---------------------------- | :----------------------------------------------------------- |
| <JDBC前缀> .tables            | 提供记录中字段的逗号分隔的源表列表。**注意：**并非所有的JDBC驱动程序都提供此信息。默认情况下，Oracle使用所有大写字母表示架构，表和列的名称。仅当使用名称周围的引号创建模式，表或列时，名称才可以是小写或大小写混合。 |
| <JDBC前缀>。<列名> .jdbcType  | 提供记录中每个字段的原始SQL数据类型的数值。有关与数值对应的数据类型的列表，请参见[Java文档](https://docs.oracle.com/javase/8/docs/api/constant-values.html#java.sql.Types.ARRAY)。 |
| <JDBC前缀>。<列名> .precision | 提供所有数字和十进制字段的原始精度。                         |
| <JDBC前缀>。<列名称> .scale   | 提供所有数字和十进制字段的原始比例。                         |

### 漂移同步解决方案的标题属性

当您将JDBC查询使用方与“漂移同步解决方案”一起使用时，请确保源创建了JDBC标头属性。

JDBC标头属性允许Hive元数据处理器使用属性中的精度和小数位数信息来定义小数字段。

要使Hive元数据处理器能够根据需要定义十进制字段，请执行以下步骤：

1. 在“ JDBC查询使用者”的“ **高级”**选项卡上，确保已选择“ **创建JDBC标头属性”**。

2. 在同一选项卡上，可以选择配置**JDBC Header Prefix**。

3. 在Hive元数据处理器中，如有必要，在**Hive**选项卡上配置

   Decimal Precision Expression

   和

   Decimal Scale Expression

   属性。

   

   

   如果您更改了JDBC查询使用者中JDBC标头前缀的默认值，请更新“ jdbc”。表达式中的字符串以使用正确的JDBC标头前缀。

   如果您没有更改JDBC Header Prefix的默认值，则对属性使用默认表达式。

**相关概念**

[蜂巢漂移同步解决方案](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Hive_Drift_Solution/HiveDriftSolution_title.html#concept_phk_bdf_2w)

## 适用于Microsoft SQL Server的CDC

**重要说明：**不建议使用此功能，将来的版本中将删除该功能。要处理Microsoft SQL Server CDC表中的数据，请使用[SQL Server CDC客户端origin](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_ut3_ywc_v1b)。要处理Microsoft SQL Server更改跟踪表中的数据，请使用[SQL Server更改跟踪源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerChange.html#concept_ewq_b2s_r1b)。

您可以使用JDBC查询使用者来处理Microsoft SQL Server中的更改捕获数据。

要处理Microsoft SQL Server更改的捕获数据，请执行以下任务：

1. 在“ JDBC查询使用者”源中的“ **JDBC”**选项卡上，确保启用了“ **增量模式”**。

2. 配置“ 

   偏移列”

   属性以使用 

   __ $ start_lsn

   。

   Microsoft SQL Server使用_ $ start_lsn作为更改数据捕获表中的偏移量列。

3. 配置

   初始偏移量

   属性。

   这确定了在启动管道时原点从何处开始读取。要读取所有可用数据，请将其设置为0。

4. 配置

   SQL查询

   属性：

   - 在SELECT语句中，使用CDC表名称。
   - 在WHERE子句中，使用__ $ start_lsn作为偏移量列，并且由于__ $ start_lsn以二进制格式存储偏移量，因此添加命令以将整数偏移量转换为Binary（10）。
   - 在ORDER BY子句中，使用__ $ start_lsn作为偏移量列，并可以选择指定读取的升序或降序。默认情况下，原点以升序读取。

   以下查询总结了这些要点：

   ```
   SELECT * from <CDC table name>
   WHERE __$start_lsn > CAST(0x${OFFSET} as binary(10))
   ORDER BY __$start_lsn <ASC | DESC>
   ```

5. 如果要将来自同一事务的行更新分组，请在“ 

   更改数据捕获”

   选项卡上配置属性：

   - 对于**事务ID列名称，请**使用 **__ $ start_lsn**。__ $ start_lsn列的偏移量中包含交易信息。
   - 设置**最大交易大小**。此属性将覆盖Data Collector的最大批处理大小。有关这两个属性的更多信息，请参见[按事务分组行](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JDBCConsumer.html#concept_e1j_jwv_ht)。

### CRUD记录标题属性

从Microsoft SQL Server读取更改捕获数据时，JDBC查询使用者来源在sdc.operation.type记录头属性中包括CRUD操作类型。

如果您在诸如JDBC Producer或Elasticsearch之类的管道中使用启用CRUD的目标，则该目标可以在写入目标系统时使用操作类型。必要时，可以使用表达式评估器或脚本处理器来处理`sdc.operation.type`header属性中的值 。有关Data Collector更改的数据处理的概述以及启用CRUD的目标的列表，请参阅[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

原点在sdc.operation.type记录头属性中使用以下值表示操作类型：

- INSERT为1
- 2个代表删除
- 3更新
- 5表示不支持的代码

### 按交易分组行

从Microsoft SQL Server读取时，从更改日志表读取时，JDBC Query Consumer可以将来自同一事务的行更新分组。在执行更改数据捕获时，这可以保持一致性。

要启用此功能，请指定交易ID列和最大交易规模。定义这些属性后，JDBC Query Consumer会按批处理数据，直到最大事务大小，然后覆盖Data Collector的最大批大小。

当事务大于最大事务大小时，JDBC查询使用者将根据需要使用多个批处理。

为了保持事务的完整性，请根据需要增加最大事务大小。请注意，将此属性设置得太高会导致内存不足错误。

## 事件产生

JDBC查询使用者来源可以生成可在事件流中使用的事件。启用事件生成后，原始将在完成对指定查询返回的数据的处理后生成一个事件。 当查询成功完成或失败时，源也会生成一个事件。

JDBC查询使用者事件可以任何逻辑方式使用。例如：

- 当原始完成处理可用数据时，使用Pipeline Finisher执行程序停止管道并将管道转换为Finished状态。

  重新启动由Pipeline Finisher执行程序停止的管道时，原始服务器将根据您配置原始服务器的方式来处理数据。例如，如果将原点配置为以增量模式运行，则当执行程序停止管道时，原点将保存偏移量。重新启动时，原点将从上次保存的偏移开始继续处理。相反，如果将原点配置为以完全模式运行，则在重新启动管道时，原点将使用初始偏移量（如果已指定）。

  有关示例，请参见[案例研究：停止管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_kff_ykv_lz)。

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储有关已完成查询的信息的目标。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

JDBC Query Consumer起源生成的事件记录具有以下与事件相关的记录头属性：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型之一：no-more-data-当原点完成对查询返回的所有数据的处理时生成。jdbc-query-success-在源成功完成查询时生成。jdbc-query-failure-在源无法完成查询时生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

源可以生成以下类型的事件记录：

- 没有更多数据

  当原始处理完查询返回的所有数据时，它会生成一个无数据事件记录。

  由源生成的无数据事件记录将sdc.event.type设置为无数据，并包含以下字段：事件记录字段描述记录数自管道启动或自上一次创建no-more-data事件以来成功生成的记录数。

- 查询成功

  原点完成对查询返回的数据的处理后，将生成查询成功事件记录。

  查询成功事件记录的sdc.event.type记录头属性设置为jdbc-query-success，并包括以下字段：领域描述询问查询已成功完成。时间戳记查询完成时的时间戳。行数已处理的行数。源偏移查询完成后的偏移量。

- 查询失败

  当原始服务器无法完成对查询返回的数据的处理时，它将生成查询失败事件记录。

  查询失败事件记录的sdc.event.type记录头属性设置为jdbc-query-failure，并包括以下字段：

  领域描述询问无法完成的查询。时间戳记查询未能完成的时间戳。行数查询中已处理的记录数。源偏移查询失败后的原点偏移量。错误第一条错误消息。

## 配置JDBC查询使用者

配置JDBC查询使用者来源，以使用单个配置的SQL查询通过JDBC连接读取数据库数据。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 产生事件 [![img](imgs/icon_moreInfo-20200310172045313.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JDBCConsumer.html#concept_o1c_kwr_kz) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **JDBC”**选项卡上，配置以下属性：

   | JDBC属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | JDBC连接字符串                                               | 用于连接数据库的连接字符串。某些数据库（例如PostgreSQL）需要连接字符串中的模式。使用数据库所需的连接字符串格式。 |
   | 增量模式[![img](imgs/icon_moreInfo-20200310172045313.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JDBCConsumer.html#concept_ets_gnr_bs) | 定义JDBC查询使用者如何查询数据库。选择以执行增量查询。清除以执行完整查询。要从Microsoft SQL Server处理CDC数据，请选择此选项。有关Microsoft SQL Server的CDC的更多信息，请参见[Microsoft SQL Server的CDC](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JDBCConsumer.html#concept_tyd_gbf_5y)。默认为增量模式。 |
   | SQL查询[![img](imgs/icon_moreInfo-20200310172045313.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JDBCConsumer.html#concept_uj4_mxy_bs) | 从数据库读取数据时使用的SQL查询。在属性中定义查询。或者，在[运行时资源中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)定义查询，然后`runtime:loadResource`在属性中使用 函数在运行时从资源文件加载查询。**注意：** 默认情况下，Oracle对模式，表和列名称使用所有大写字母。仅当使用名称周围的引号创建模式，表或列时，名称才可以是小写或大小写混合。 |
   | 初始偏移                                                     | 管道启动时使用的偏移值。在增量模式下必需。                   |
   | [偏移列](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JDBCConsumer.html#concept_nxz_2kz_bs) | 用于偏移值的列。最佳做法是，偏移列应为不包含空值的增量且唯一的列。强烈建议在此列上建立索引，因为基础查询在此列上使用ORDER BY和不等号运算符。在增量模式下必需。 |
   | 查询间隔                                                     | 在两次查询之间等待的时间。输入基于时间单位的表达式。您可以使用SECONDS，MINUTES或HOURS。默认值为10秒：$ {10 * SECONDS}。 |
   | 使用凭证                                                     | 在“凭据”选项卡上启用输入凭据。在JDBC连接字符串中不包括凭据时使用。 |
   | 根字段类型                                                   | 用于生成记录的根字段类型。除非在使用Data Collector 1.1.0或更早版本构建的管道中使用原点，否则请使用默认的List-Map选项。 |
   | 最大批次大小（记录）                                         | 批处理中包含的最大记录数。                                   |
   | 最大布料大小（字符）                                         | Clob字段中要读取的最大字符数。较大的数据将被截断。           |
   | 最大Blob大小（字节）                                         | Blob字段中要读取的最大字节数。                               |
   | SQL错误重试次数                                              | 源在接收到SQL错误后尝试执行查询的次数。重试此次数后，原始服务器将根据为原始服务器配置的错误处理来处理错误。用于处理瞬态网络或连接问题，这些问题会阻止源提交查询。默认值为0。 |
   | 将时间戳转换为字符串                                         | 使原点能够将时间戳记写为字符串值而不是日期时间值。字符串保持存储在源数据库中的精度。例如，字符串可以保持高精度IBM Db2 TIMESTAMP（9）字段的精度。在将时间戳写入不存储纳秒的Data Collector日期或时间数据类型时，原点会将距时间戳的任何纳秒存储在[field属性中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)。 |
   | 其他JDBC配置属性                                             | 要使用的其他JDBC配置属性。要添加属性，请单击 **添加**并定义JDBC属性名称和值。使用JDBC期望的属性名称和值。 |

3. 如果在**JDBC**选项卡上将源配置为与JDBC连接字符串分开输入JDBC凭据，则在“ **凭据”** 选项卡上配置以下属性：

   | 凭证属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 用户名   | JDBC连接的用户名。                                           |
   | 密码     | JDBC帐户的密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |

4. 要从Microsoft SQL Server处理变更捕获数据，请在“ **变更数据捕获”**选项卡上，配置以下属性以按事务对行进行分组：

   | 更改数据捕获属性                                             | 描述                                                     |
   | :----------------------------------------------------------- | :------------------------------------------------------- |
   | 交易ID列名称                                                 | 交易ID列名称，通常为__ $ start_lsn。                     |
   | 最大交易量（行）[![img](imgs/icon_moreInfo-20200310172045313.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JDBCConsumer.html#concept_e1j_jwv_ht) | 批处理中包含的最大行数。覆盖数据收集器的最大批处理大小。 |

5. 使用低于4.0的JDBC版本时，在“ **旧版驱动程序”**选项卡上，可以选择配置以下属性：

   | 旧版驱动程序属性     | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | JDBC类驱动程序名称   | JDBC驱动程序的类名。早于版本4.0的JDBC版本必需。              |
   | 连接运行状况测试查询 | 可选查询，用于测试连接的运行状况。仅当JDBC版本低于4.0时才建议使用。 |

6. 在“ **高级”**选项卡上，可以选择配置高级属性。

   这些属性的默认值在大多数情况下都应该起作用：

   | 先进物业                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 最大游泳池                                                   | 创建的最大连接数。默认值为1。建议值为1。                     |
   | 最小空闲连接                                                 | 创建和维护的最小连接数。要定义固定连接池，请设置为与“最大池大小”相同的值。默认值为1。 |
   | 连接超时                                                     | 等待连接的最长时间。在表达式中使用时间常数来定义时间增量。默认值为30秒，定义如下：`${30 * SECONDS}` |
   | 空闲超时                                                     | 允许连接空闲的最长时间。在表达式中使用时间常数来定义时间增量。使用0以避免删除任何空闲连接。当输入的值接近或超过连接的最大生存期时，Data Collector将忽略空闲超时。默认值为10分钟，定义如下：`${10 * MINUTES}` |
   | 最大连接寿命                                                 | 连接的最大寿命。在表达式中使用时间常数来定义时间增量。使用0设置最大寿命。设置最大寿命时，最小有效值为30分钟。默认值为30分钟，定义如下：`${30 * MINUTES}` |
   | 自动提交                                                     | 确定是否启用自动提交模式。在自动提交模式下，数据库为每个记录提交数据。默认设置为禁用。 |
   | 强制执行只读连接                                             | 创建只读连接以避免任何类型的写入。默认启用。不建议禁用此属性。 |
   | 交易隔离                                                     | 用于连接数据库的事务隔离级别。默认是为数据库设置的默认事务隔离级别。您可以通过将级别设置为以下任意值来覆盖数据库默认值：阅读已提交阅读未提交可重复读可序列化 |
   | 初始化查询                                                   | 在阶段连接到数据库后立即执行的SQL查询。用于根据需要设置数据库会话。例如，以下查询为MySQL数据库设置会话的时区： `SET time_zone = timezone;` |
   | 创建JDBC标头属性[![img](imgs/icon_moreInfo-20200310172045313.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JDBCConsumer.html#concept_egw_d4c_kw) | 将JDBC标头属性添加到记录。默认情况下，源将创建JDBC标头属性。**注意：**将原点与“漂移同步解决方案”一起使用时，请确保选中此属性。 |
   | JDBC标头前缀                                                 | JDBC标头属性的前缀。                                         |
   | 禁用查询验证                                                 | 禁用默认情况下发生的查询验证。用于避免费时的查询验证情况，例如查询Hive时，这需要使用MapReduce作业来执行验证。**警告：**查询验证会阻止使用无效查询运行管道。请谨慎使用此选项。 |
   | 在未知类型上                                                 | 原点遇到数据类型不受支持的记录时要采取的措施：停止管道-完成对先前记录的处理后，停止管道。转换为字符串-将数据转换为字符串并继续处理。 |