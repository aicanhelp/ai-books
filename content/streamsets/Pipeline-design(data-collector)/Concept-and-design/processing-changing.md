# 处理更改的数据

某些阶段使您能够轻松地在管道中处理数据更改，例如更改捕获数据（CDC）或事务数据。

启用CDC的源可以读取更改捕获数据。一些专门读取更改捕获数据，其他可以配置为读取它。当读取更改的数据时，它们确定与数据关联的CRUD操作，并将CRUD操作（例如插入，更新，向上插入或删除）包括在`sdc.operation.type`记录头属性中。

启用CRUD的处理器和目标`sdc.operation.type`在写入记录时可以在标头属性中使用CRUD操作类型，从而使外部系统能够执行适当的操作。

在管道中使用启用CDC的原始和启用CRUD的阶段，可以轻松地将更改的数据从一个系统写入另一个系统。您还可以使用启用CDC的源来写入非CRUD目的地，使用非CDC原始来写入启用CRUD的阶段。有关其工作原理的信息，请参见[用例](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_y5y_5xd_5y)。

## CRUD操作标题属性

`sdc.operation.type`当读取更改的数据时，读取的具有CDC功能的原点将在所有记录中包括 记录头属性。

启用CRUD的处理器和目标`sdc.operation.type`在写入记录时可以在标头属性中使用CRUD操作类型，从而使外部系统能够执行适当的操作。

该`sdc.operation.type`记录标题属性使用下列整数表示CRUD操作：

- 1用于INSERT记录
- 2用于删除记录
- 3用于UPDATE记录
- 4个用于UPSERT记录
- 5用于不受支持的操作或代码
- UNDELETE记录为6
- 7用于替换记录
- MERGE记录的8

**注意：**根据来源系统支持的操作，某些来源仅使用操作的子集。同样，目标仅识别目标系统支持的操作子集。有关支持的操作的详细信息，请参阅来源和目标文档。

### 较早的实现

在早期版本中，使用不同的记录头属性为CDC启用了某些起源，但是现在它们都包含了 `sdc.operation.type`记录头属性。保留所有较早的CRUD标头属性以实现向后兼容。

同样，启用CRUD的目标可以在其他标头属性中查找CRUD操作类型，现在可以先查找`sdc.operation.type` 记录标头属性，然后再查找替代属性。保留备用标头属性功能是为了向后兼容。

## 启用CDC的阶段

启用CDC的阶段在`sdc.operation.type`记录头属性中提供CRUD操作类型。一些来源提供备用和附加的标题属性。

以下阶段提供CRUD记录头属性：

| 启用CDC的阶段        | CRUD记录标题属性                                             |
| :------------------- | :----------------------------------------------------------- |
| MapR DB CDC          | 在`sdc.operation.type`记录头属性中包括CRUD操作类型 。在记录标题属性中包括其他CDC信息。有关更多信息，请参见[CRUD操作和CDC标头属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRdbCDC.html#concept_oq4_mhh_qbb)。 |
| MongoDB Oplog        | 在`sdc.operation.type`记录头属性中包括CRUD操作类型 。可以在记录标题属性（例如`op`和 `ns`属性）中包含其他CDC信息。有关更多信息，请参见[生成的记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDBOplog.html#concept_wc3_byl_5y)。 |
| MySQL二进制日志      | 在`sdc.operation.type`记录头属性中包括CRUD操作类型 。在记录字段中包括其他CDC信息。有关更多信息，请参见[生成的记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MySQLBinaryLog.html#concept_rfd_15l_dy)。 |
| Oracle CDC客户端     | 在以下两个标头中均包括CRUD操作类型：`sdc.operation.type``oracle.cdc.operation`有关更多信息，请参见[CRUD操作头属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_x4h_m42_5y)。在带有前缀的记录标头属性中包含[其他CDC信息](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_nn1_lxd_dx)`oracle.cdc`，例如 `oracle.cdc.table`。 |
| PostgreSQL CDC客户端 | [在记录中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/PostgreSQL.html#concept_zwv_tw5_n2b)包括CRUD操作类型[。](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/PostgreSQL.html#concept_zwv_tw5_n2b)在带有前缀的记录标头属性中包含[其他CDC信息](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/PostgreSQL.html#concept_zzt_pb5_n2b)`postgres.cdc`，例如`postgres.cdc.lsn`。 |
| 销售队伍             | 在`sdc.operation.type`记录头属性中包括CRUD操作类型 。有关更多信息，请参见[CRUD操作标头属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_yns_y2m_5y)。 |
| SQL解析器            | 在以下两个标头中均包括CRUD操作类型：`sdc.operation.type``oracle.cdc.operation`有关更多信息，请参见[生成的记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/SQLParser.html#concept_qwp_cwf_wdb)。 |
| SQL Server CDC客户端 | 在`sdc.operation.type`记录头属性中包括CRUD操作类型 。在名为的标头属性中包含CDC信息 `jdbc.`。有关更多信息，请参见[记录头属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_pc4_xts_r1b)。 |
| SQL Server更改跟踪   | 在`sdc.operation.type`记录头属性中包括CRUD操作类型 。在`jdbc.SYS_CHANGE`标头属性中包含来自更改表的其他信息 。有关更多信息，请参见[记录头属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerChange.html#concept_pc4_xts_r1b)。 |

## SQL Server或Azure SQL数据库中的数据更改

SQL Server和Azure SQL数据库提供了多种方法来跟踪数据更改。在Data Collector中，适当的来源取决于数据库用来跟踪更改的方法，如下表所示：

| 跟踪变更的方法 | 数据收集器来源                                               |
| :------------- | :----------------------------------------------------------- |
| CDC表          | [SQL Server CDC客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_ut3_ywc_v1b)有关CDC表的更多信息，请参见[Microsoft文档](https://docs.microsoft.com/en-us/sql/relational-databases/track-changes/about-change-data-capture-sql-server?view=sql-server-ver15)。 |
| 变更追踪表     | [SQL Server更改跟踪](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerChange.html#concept_ewq_b2s_r1b)有关变更跟踪表的更多信息，请参见[Microsoft文档](https://docs.microsoft.com/en-us/sql/relational-databases/track-changes/about-change-tracking-sql-server?view=sql-server-ver15)。 |
| 时间表         | [JDBC多表使用者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MultiTableJDBCConsumer.html#concept_zp3_wnw_4y)或[JDBC查询使用者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JDBCConsumer.html#concept_qhf_hjr_bs)有关时态表的更多信息，请参见[Microsoft文档](https://docs.microsoft.com/en-us/sql/relational-databases/tables/temporal-tables?view=sql-server-ver15)。 |

## 启用CRUD的阶段

以下阶段识别存储在记录头属性中的CRUD操作，并可以基于这些值执行写操作。某些阶段还提供与CRUD相关的属性。

| 启用CRUD的阶段      | 支持的运营                                                   | 舞台处理                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| JDBC Tee处理器      | 插入更新删除                                                 | 根据以下条件确定要使用的操作：`sdc.operation.type` 记录标题属性阶段中的默认操作和不支持的操作处理属性包括“更改日志”属性，该属性使您能够根据您使用的启用CDC的来源处理记录。有关更多信息，请参见[定义CRUD操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/JDBCTee.html#concept_qfd_tpm_5y)。 |
| Couchbase目的地     | 插入更新UPSERT删除                                           | 根据以下条件确定要使用的操作：`sdc.operation.type` 记录标题属性该阶段中的默认写操作和不支持的操作处理属性有关更多信息，请参见[定义CRUD操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Couchbase.html#concept_i1c_vby_g3b)。 |
| Elasticsearch目的地 | 创建（插入）更新索引（UPSERT）删除用`doc_as_upsert`（MERGE）更新 | 根据以下条件确定要使用的操作：`sdc.operation.type` 记录标题属性阶段中的默认操作和不支持的操作处理属性有关更多信息，请参见[定义CRUD操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Elasticsearch.html#concept_w2r_ktb_ry)。 |
| GPSS生产者目的地    | 插入更新合并                                                 | 根据以下条件确定要使用的操作：`sdc.operation.type` 记录标题属性阶段中的默认操作和不支持的操作处理属性有关更多信息，请参见[定义CRUD操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/GPSS.html#concept_alc_2bf_r3b)。 |
| JDBC生产者目标      | 插入更新删除                                                 | 根据以下条件确定要使用的操作：`sdc.operation.type` 记录标题属性阶段中的默认操作和不支持的操作处理属性包括“更改日志”属性，该属性使您能够根据您使用的启用CDC的来源处理记录。有关更多信息，请参见[定义CRUD操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/JDBCProducer.html#concept_plv_jpn_5y)。 |
| 九都目的地          | 插入更新UPSERT删除                                           | 根据以下条件确定要使用的操作：`sdc.operation.type` 记录标题属性阶段中的默认操作和不支持的操作处理属性有关更多信息，请参见[定义CRUD操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Kudu.html#concept_dvg_vvj_wx)。 |
| MapR DB JSON目标    | 插入更新删除                                                 | 根据以下条件确定要使用的操作：`sdc.operation.type` 记录标题属性在阶段中插入API和设置API属性有关更多信息，请参见[写入MapR DB JSON](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MapRDBJSON.html#concept_hy5_3nb_xbb)。 |
| MongoDB目的地       | 插入更新更换删除                                             | 根据以下条件确定要使用的操作：`sdc.operation.type` 记录标题属性在阶段中增加属性有关更多信息，请参见[定义CRUD操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MongoDB.html#concept_bkc_m24_4v)。 |
| Redis目的地         | 插入更新更换删除                                             | 根据以下条件确定要使用的操作：`sdc.operation.type` 记录标题属性有关更多信息，请参见[定义CRUD操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Redis.html#concept_dz2_4xh_xbb)。 |
| Salesforce目的地    | 插入更新UPSERT删除未删除                                     | 根据以下条件确定要使用的操作：`sdc.operation.type` 记录标题属性阶段中的默认操作和不支持的操作处理属性有关更多信息，请参见[定义CRUD操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Salesforce.html#concept_opg_tyg_4z)。 |
| 雪花目的地          | 插入更新删除                                                 | 根据以下条件确定要使用的操作：`sdc.operation.type` 记录标题属性有关更多信息，请参见[定义CRUD操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_umx_qgj_ggb)。 |

## 处理记录

更改日志可以提供不同格式的记录数据。JDBC Tee处理器和JDBC Producer目标可以解码大多数更改日志格式，以基于源更改日志生成记录数据。使用其他启用CRUD的目标时，可能需要向管道添加其他处理以更改记录的格式。

例如，由JDBC Query Consumer源创建的Microsoft SQL CDC记录除记录数据外，还包含记录中的CDC字段。您可以使用Field Remover处理器从记录中删除所有不必要的字段。

相反，由My SQL Binary Log源读取的MySQL Server二进制日志在New Data map字段中提供新数据或更新数据，并在Changed Data映射字段中提供更改或删除的数据。您可能想要使用Field Flattener处理器使用所需的数据来展平地图字段，并使用Field Remover处理器删除所有不必要的字段。

有关生成的记录格式的详细信息，请参阅启用CDC的原始文档。

## 用例

您可以在管道中一起或单独使用启用CDC的源和启用CRUD的目的地。以下是一些典型的用例：

- 启用CDC的源和启用CRUD的目的地

  您可以使用启用CDC的源和启用CRUD的目标来轻松处理已更改的记录并将其写入目标系统。

  例如，假设您要将CDC数据从Microsoft SQL Server写入Kudu。为此，您可以使用启用CDC的SQL Server CDC客户端源从Microsoft SQL Server更改捕获表中读取数据。源将CRUD操作类型放置在 `sdc.operation.type`标头属性中，在这种情况下：1表示INSERT，2表示DELETE，3表示UPDATE。您配置管道以写入启用了CRUD的Kudu目标。在Kudu目标中，可以为`sdc.operation.type` 属性中未设置任何值的任何记录指定默认操作，并且可以为无效值配置错误处理。您将默认值设置为INSERT，并将目标配置为使用此默认值表示无效值。在`sdc.operation.type` 属性中，Kudu目标为INSERT支持1，对于DELETE支持2，对于UPDATE支持3，对于UPSERT支持4。当您运行管道时，SQL Server CDC客户端起源将确定每个记录的CRUD操作类型，并将其写入 `sdc.operation.type`记录头属性。Kudu目标使用`sdc.operation.type`属性中的操作 来通知Kudu目标系统如何处理每个记录。任何在`sdc.operation.type`属性中具有未声明值的记录，例如由管道创建的记录，都将被视为INSERT记录。任何具有无效值的记录都使用相同的默认行为。

- 启用CDC的来源到非CRUD目的地

  如果需要将更改的数据写入没有启用CRUD的目标的目标系统，则可以使用表达式评估器或脚本处理器将CRUD操作信息从 `sdc.operation.type`标头属性移动到字段，因此该信息将保留在记录中。例如，假设您要从Oracle LogMiner重做日志中读取并将记录与记录字段中的所有CDC信息一起写入Hive表。为此，您将使用Oracle CDC Client源读取重做日志，然后添加一个Expression Evaluator来将CRUD信息从`sdc.operation.type`标头属性中拉到记录中。Oracle CDC客户端将其他CDC信息（例如表名和scn）写入 `oracle.cdc`标头属性，因此您也可以使用表达式将该信息提取到记录中。然后，您可以使用Hadoop FS目标将增强的记录写入Hive。

- 非CDC来源到CRUD目的地

  从非CDC来源读取数据时，可以使用Expression Evaluator或脚本处理器来定义 `sdc.operation.type`标头属性。

  例如，假设您要从事务数据库表中读取数据，并使维度表与更改保持同步。您将使用JDBC查询使用者来读取源表，并使用JDBC查找处理器来检查维度表中每个记录的主键值。然后，根据查找处理器的输出，您知道表中是否有匹配的记录。使用表达式评估器，设置 `sdc.operation.type`记录标题属性-3以更新具有匹配记录的记录，而1以插入新记录。

  当您将记录传递到JDBC Producer目标时，目标将使用`sdc.operation.type`header属性中的操作来确定如何将记录写入维度表。