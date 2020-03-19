# 雪花

Snowflake来源从Snowflake数据库读取数据。您可以使用Snowflake来源从任何可访问的Snowflake数据库读取，包括在Amazon S3，Microsoft Azure和私有Snowflake安装上托管的数据库。

从Snowflake读取数据时，原点会在内部阶段暂存数据。源可以从指定的表或使用指定的查询读取数据。它还可以执行增量读取。

配置原点时，可以指定要使用的Snowflake区域，数据库，表和架构。您还配置用户帐户和密码。确保用户帐户具有[所需的Snowflake特权](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Snowflake.html#concept_pvj_d1z_lkb)。

您定义要执行的读取类型和相关属性，例如要使用的表或查询。如果启用增量读取，则还可以指定要使用的初始offset和offset列。Snowflake原点支持数字和日期时间偏移量。

默认情况下，原始服务器执行批量读取，也称为副本卸载。不执行副本卸载时，可以指定要使用的分区大小。您可以配置原点以保留列名的现有大小写。您还可以指定要使用的连接数并启用下推优化。

**注意：** 当管道在Databricks群集上运行时，请使用Databricks运行时6.1或更高版本，以实现最佳兼容性和下推式优化。

## 所需特权

为了允许从雪花表中读取雪花原点，在原点中指定的雪花用户帐户必须具有所需的雪花特权。

**提示：**要使用具有所需特权的自定义Snowflake角色，该角色必须是源中指定的用户帐户的默认角色。

源中指定的用户帐户必须具有以下特权：

| 宾语         | 特权 |
| :----------- | :--- |
| 内部雪花阶段 | 读   |
| 表           | 选择 |

## 读模式

读取模式确定雪花源如何从雪花中读取数据。

原点提供以下读取模式：

- 表格-读取指定表格中的所有列。
- [查询](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Snowflake.html#concept_ux1_4zt_hkb) -根据指定的查询从表中读取数据。您可以使用查询模式从表中读取列的子集，或读取多个表的联接或联合。

## 完整或增量读取

每次运行管道时，Snowflake源都可以执行完全读取或增量读取。默认情况下，原点对指定的表或查询执行完全读取。

当原点执行完全读取时，每次管道运行时，原点都会处理表中可用或查询返回的所有数据。

当原点执行增量读取时，第一个管道运行与完全读取相同。当流水线停止时，原点将存储停止处理的偏移量。对于后续的管道运行，原点从最后保存的偏移量开始读取表或查询。

## SQL查询准则

当源在查询读取模式下运行时，必须指定要使用的SQL查询。

SQL查询定义了从Snowflake返回的数据。您可以使用任何有效的SQL查询，但是查询的准则取决于您将源配置为执行完全读取还是增量读取。

### 增量读取查询

当您为增量读取定义SQL查询时，该查询必须包含WHERE子句和ORDER BY子句。

定义WHERE和ORDER BY子句时，请遵循以下准则：

- WHERE子句

  在WHERE子句中，包括offset列和offset值。使用`$offset`变量表示偏移值。

  在原点中，还可以配置属性以定义偏移列和初始偏移值。这些属性与SQL查询一起使用，以确定传递给 Snowflake 的查询。原点支持数字和日期时间偏移量。

  例如，假设您将来源配置为使用以下查询：`SELECT * FROM employees WHERE employeeId > $offset`

  您还可以将其指定`employeeId`为偏移量列，`20052`并将其指定为原点属性中的初始偏移量。当管道开始，起源内容替换 `$offset`与`20052`查询，如下所示：`SELECT * FROM employees WHERE employeeId > 20052`

  当管道停止时，原点将在其停止处存储偏移量，然后`$offset`在下次启动管道时使用该偏移值替换 。

- ORDER BY子句

  在ORDER BY子句中，将offset列作为第一列，以避免返回重复数据。**注意：**在ORDER BY子句中使用不是主键或索引列的列可能会降低性能。

  例如，以下查询从“发票”表返回数据，其中该`id`列是“偏移量”列。查询返回`id`大于偏移值的所有数据，并按`id` 列对数据排序：`SELECT * FROM invoice WHERE id > $offset ORDER BY id`

### 全读查询

全读查询没有特定要求或限制。

例如，您可以运行以下查询以从发票表返回所有数据：

```
SELECT * FROM invoice
```

当您为完全模式定义SQL查询时，可以选择使用与增量模式相同的准则来包括WHERE和ORDER BY子句。但是，使用这些子句从大表中读取可能会导致性能问题。

## 下推式优化

Snowflake起源可以在使用Spark 2.4.0或更高版本的集群中执行下推优化。启用下推后，原点会将所有可能的处理推送到Snowflake数据库，这可以提高性能，尤其是对于大型数据集。

您可以独立于[Ludicrous模式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Ludicrous.html#concept_m4y_lbq_g3b)在Snowflake源中启用下推[功能](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Ludicrous.html#concept_m4y_lbq_g3b)。但是，将其与可笑模式结合使用应可提供最佳效果。有关可以下推到Snowflake的Spark SQL运算符的详细信息，请参见[Snowflake文档](https://docs.snowflake.net/manuals/user-guide/spark-connector-use.html#pushdown)。

使用“连接”选项卡上的“启用下推”属性可以为雪花起点启用下推。

## 配置雪花来源

配置Snowflake来源以从Snowflake数据库读取数据。

1. 在“属性”面板上的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 用于连接到Snowflake的舞台库：Snowflake群集提供的库-运行管道的群集已安装Snowflake库，因此具有运行管道的所有必需的库。Snowflake Transformer提供的库-Transformer将必需的库与管道一起传递以启用运行管道。在本地运行管道或运行管道的集群不包含Snowflake库时使用。**注意：**在管道中使用其他Snowflake阶段时，请确保它们使用[相同的阶段库](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Pipeline-StageLibMatch.html#concept_r4g_n3x_shb)。 |
   | 仅加载一次数据                                               | 批量读取数据并缓存结果以备重用。用于在流执行模式管道中[执行查找](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Lookups.html#concept_f2z_5yw_g3b)。使用原点执行查找时， 请勿限制批处理大小。所有查询数据都应在一个批次中读取。在批处理执行模式下，将忽略此属性。 |
   | [缓存数据](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/CachingData.html#concept_q2r_xm4_33b) | 缓存处理后的数据，以便可以在多个下游阶段重用该数据。当阶段将数据传递到多个阶段时，用于提高性能。当管道以[荒谬的方式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Ludicrous.html#concept_m4y_lbq_g3b)运行时，缓存会限制下推式优化。未启用“仅一次加载数据”时可用。当原点一次加载数据时，它也会缓存数据。 |

2. 在“ **连接”**选项卡上，配置以下属性：

   | 连接属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 雪花地区                                                     | 雪花仓库所在的区域。选择以下区域之一：可用的雪花区域。其他-用于指定属性中未列出的雪花区域。自定义JDBC URL-用于指定虚拟专用雪花。 |
   | 定制雪花区                                                   | 要使用的自定义雪花区域。在将其他用作雪花区域时可用。         |
   | 自定义雪花网址                                               | 用于虚拟私有Snowflake安装的自定义JDBC URL。                  |
   | 帐户                                                         | 雪花帐户名称。                                               |
   | 用户                                                         | 雪花用户名。用户帐户必须具有[所需的Snowflake特权](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Snowflake.html#concept_pvj_d1z_lkb)。 |
   | 密码                                                         | 雪花密码。                                                   |
   | 仓库                                                         | 雪花仓库。                                                   |
   | 数据库                                                       | 雪花数据库。                                                 |
   | 架构图                                                       | 雪花模式。                                                   |
   | [启用下推](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Snowflake.html#concept_mss_ktt_hkb) | 将所有可能的处理推送到Snowflake数据库，这可以提高性能，尤其是对于大型数据集。仅在运行管道的群集使用Spark 2.4.0或更高版本时使用。 |

3. 在**表格**选项卡上，配置以下属性：

   | 表属性                                                       | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [读模式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Snowflake.html#concept_qkn_vfj_gkb) | 读取模式使用：表-源从指定表中读取所有列。查询-源根据指定的SQL查询读取数据。 |
   | 复制卸载                                                     | 使原点能够使用COPY INTO命令执行数据的批量读取。清除后，原点将使用SELECT命令读取数据。默认情况下，此选项处于选中状态，这是默认的Snowflake读取。 |
   | 分区大小                                                     | 用于读取的分区大小，以MB为单位。未选择“复制卸载”时可用。     |
   | 保管箱                                                       | 防止在包含除大写字母，数字和下划线之外的字符的列名称周围添加引号。 |
   | 表                                                           | 表读取。在表格读取模式下可用。                               |
   | [SQL查询](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Snowflake.html#concept_ux1_4zt_hkb) | 用于读取的SQL查询。在查询读取模式下可用。有关为完全和增量读取创建查询的准则，请参阅《[SQL查询准则》](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Snowflake.html#concept_ux1_4zt_hkb)。 |
   | [增量模式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Snowflake.html#concept_kr2_ctj_gkb) | 使原点能够在后续管道运行期间从最后保存的偏移量读取数据。     |
   | 初始偏移                                                     | 在查询中使用的初始偏移值。`$offset`启动管道时，此值将替换变量。原点支持数字和日期时间偏移量。增量读取可用且必需。 |
   | 偏移列                                                       | 列跟踪读取进度。原点 支持数字和日期时间偏移量。最佳做法是，偏移列应为不包含空值的增量且唯一的列。强烈建议在此列上建立索引，因为基础查询在此列上使用ORDER BY子句和不等式运算符。增量读取可用且必需。 |

4. （可选）在“ **高级”**选项卡上，配置以下属性：

   | 先进物业 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 连接池   | 源从Snowflake读取的最大连接数。默认值是4。增加此属性可以提高性能。但是，Snowflake警告说，将此属性设置为任意高的值可能会对性能产生不利影响。默认值为推荐值。 |