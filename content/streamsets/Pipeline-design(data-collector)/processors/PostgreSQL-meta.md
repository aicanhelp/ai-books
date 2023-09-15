# PostgreSQL元数据

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310181522787.png) 资料收集器

的PostgreSQL的元数据处理器确定其中每个记录应写入PostgreSQL的表，记录结构对表结构进行比较，然后根据需要创建或改变的表。

将PostgreSQL元数据处理器用作PostgreSQL [漂移同步解决方案的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/JDBC_DriftSolution/JDBC_DriftSyncSolution_title.html#concept_ljq_knr_4cb)一部分。由于这是PostgreSQL漂移同步解决方案的测试版，因此只能将PostgreSQL元数据处理器用于开发或测试。不要在生产管道中使用处理器。

处理数据时，PostgreSQL元数据处理器使用表名表达式来确定用于每个记录的目标表的名称。如果目标表不在处理器的缓存中，则处理器会在数据库中查询表信息并缓存结果。当目标表在高速缓存中时，处理器将记录结构与高速缓存的表结构进行比较。

当记录中包含表中不存在的字段时，PostgreSQL元数据处理器将根据需要更改表，然后更新缓存中的表信息。当将一条记录写入不存在的表中时，处理器将根据记录中的字段创建该表。

像其他与数据库相关的阶段一样，当您配置PostgreSQL元数据处理器时，您可以指定自定义JDBC属性，输入连接凭证并配置高级属性，例如初始查询和超时。

有关PostgreSQL漂移同步解决方案和案例研究的更多信息，请参见[PostgreSQL漂移同步解决方案](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/JDBC_DriftSolution/JDBC_DriftSyncSolution_title.html#concept_ljq_knr_4cb)。

计划在将来的版本中支持其他数据库。要声明首选项，请对此[问题](https://issues.streamsets.com/browse/SDC-8051)发表评论。

## 数据库版本



StreamSets 已使用以下数据库版本测试了PostgreSQL Metadata处理器：

- PostgreSQL 9.4.18
- PostgreSQL 9.6.2
- PostgreSQL 9.6.9
- PostgreSQL 10.4

连接到PostgreSQL数据库时，不需要安装JDBC驱动程序。Data Collector包括PostgreSQL所需的JDBC驱动程序。

## 架构和表名称

在配置应在其中写入记录的架构和表时，可以使用解析为要使用的架构和表的实际架构和表名称或表达式。

当所有数据都可以写入同一模式或表时，请使用名称。使用表达式可使用记录中的信息来确定要写入的架构或表。

例如，JDBC多表使用者源将原始表名写在`jdbc.tables`记录头属性中。 如果要将记录写入同名的表，则可以将其`${record:attribute('jdbc.tables')}` 用于表名属性。

同样，当将JDBC查询使用者`.tables`配置为创建记录头属性时 ，JDBC查询使用者会将其源表名称写入记录头属性中。因此，如果要将记录写到同名的表中，可以将其`${record:attribute('.tables')}`用于表名属性。

架构和表名称表达式的提示：

- 如果将所有记录都写到单个模式或表，则可以输入模式或表名而不是表达式。
- 如果可以从记录数据或标题属性中推断模式或表名称，则可以使用计算结果为模式或表名称的表达式。
- 必要时，可以在管道中的较早位置使用表达式计算器来执行计算，并将结果写入新字段或标头属性。然后，配置PostgreSQL元数据处理器以使用该信息。

## 小数精度和小数位数字段属性

使用“十进制精度”属性和“十进制小数位数”属性可以为PostgreSQL元数据处理器创建的“十进制”列指定精度和小数位数。

虽然其他数据类型具有处理器用来在数据库表中创建列的硬编码定义，但十进制列则需要指定的精度和小数位数。

处理来自JDBC查询使用者或JDBC多表使用者来源的数据时，请使用默认属性名称“ precision”和“ scale”。这两个原点在每个小数字段的“ precision”和“ scale”字段属性中存储小数列的精度和小数位数。

处理来自其他来源的数据时，可以在管道中更早地使用Expression Evaluator处理器为Decimal字段创建precision和scale字段属性。

## 缓存信息

处理记录时，PostgreSQL元数据处理器会在数据库中查询必要的表信息并缓存结果。创建或更改表后，它将更新缓存中的表信息。处理器尽可能使用高速缓存进行记录比较，以避免不必要的查询。

**要点：** 不要在管道运行时更改管道可能使用的任何表。由于PostgreSQL元数据处理器缓存有关表结构的信息并创建和更改表，因此处理器必须具有有关表的准确信息。

## 配置PostgreSQL元数据处理器

将PostgreSQL元数据处理器配置为PostgreSQL [漂移同步解决方案的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/JDBC_DriftSolution/JDBC_DriftSyncSolution_title.html#concept_ljq_knr_4cb)一部分。

由于这是PostgreSQL漂移同步解决方案的测试版，因此只能将PostgreSQL元数据处理器用于开发或测试。不要在生产管道中使用处理器。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **JDBC”**选项卡上，配置以下属性：

   | JDBC属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | JDBC连接字符串                                               | 用于连接数据库的连接字符串。使用以下格式：`jdbc:postgresql://:/` |
   | 使用凭证                                                     | 在“凭据”选项卡上启用输入凭据。在JDBC连接字符串中不包括凭据时使用。用于连接凭据的用户帐户必须对数据库具有“创建表”和“更改表”权限。 |
   | 架构图 [![img](imgs/icon_moreInfo-20200310181522787.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/PostgreSQLMetadata.html#concept_qpz_cdx_qcb) | 要使用的架构的名称。您可以输入计算结果为架构名称的表达式。   |
   | 表名[![img](imgs/icon_moreInfo-20200310181522787.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/PostgreSQLMetadata.html#concept_qpz_cdx_qcb) | 要使用的数据库表的名称。输入以下内容之一：现有数据库表的名称。计算结果为表名的表达式。例如，要在“ tableName”记录标题属性中使用表名称，请输入以下表达式：`${record:attribute('tableName')}`如果表不存在，处理器将创建该表，并在需要时更新该表。 |
   | 小数位数属性[![img](imgs/icon_moreInfo-20200310181522787.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/PostgreSQLMetadata.html#concept_fyr_gdx_qcb) | 包含小数字段小数位的字段属性。将默认值用于由JDBC查询使用者或JDBC多表使用者来源生成的数据。 |
   | 小数精度属性[![img](imgs/icon_moreInfo-20200310181522787.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/PostgreSQLMetadata.html#concept_fyr_gdx_qcb) | 包含小数字段精度的字段属性。将默认值用于由JDBC查询使用者或JDBC多表使用者来源生成的数据。 |
   | 其他JDBC配置属性                                             | 要使用的其他JDBC配置属性。要添加属性，请单击 **添加**并定义JDBC属性名称和值。使用JDBC期望的属性名称和值。 |

3. 如果在**JDBC**选项卡上将源配置为与JDBC连接字符串分开输入JDBC凭据，则在“ **凭据”** 选项卡上配置以下属性：

   | 凭证属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 用户名   | JDBC连接的用户名。                                           |
   | 密码     | JDBC帐户的密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |

4. 使用低于4.0的JDBC版本时，在“ **旧版驱动程序”**选项卡上，可以选择配置以下属性：

   | 旧版驱动程序属性     | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | JDBC类驱动程序名称   | JDBC驱动程序的类名。早于版本4.0的JDBC版本必需。              |
   | 连接运行状况测试查询 | 可选查询，用于测试连接的运行状况。仅当JDBC版本低于4.0时才建议使用。 |

5. 在“ **高级”**选项卡上，可以选择配置高级属性。

   这些属性的默认值在大多数情况下都应该起作用：

   | 先进物业     | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 最大游泳池   | 创建的最大连接数。默认值为1。建议值为1。                     |
   | 最小空闲连接 | 创建和维护的最小连接数。要定义固定连接池，请设置为与“最大池大小”相同的值。默认值为1。 |
   | 连接超时     | 等待连接的最长时间。在表达式中使用时间常数来定义时间增量。默认值为30秒，定义如下：`${30 * SECONDS}` |
   | 空闲超时     | 允许连接空闲的最长时间。在表达式中使用时间常数来定义时间增量。使用0以避免删除任何空闲连接。当输入的值接近或超过连接的最大生存期时，Data Collector将忽略空闲超时。默认值为10分钟，定义如下：`${10 * MINUTES}` |
   | 最大连接寿命 | 连接的最大寿命。在表达式中使用时间常数来定义时间增量。使用0设置最大寿命。设置最大寿命时，最小有效值为30分钟。默认值为30分钟，定义如下：`${30 * MINUTES}` |
   | 交易隔离     | 用于连接数据库的事务隔离级别。默认是为数据库设置的默认事务隔离级别。您可以通过将级别设置为以下任意值来覆盖数据库默认值：阅读已提交阅读未提交可重复读可序列化 |
   | 初始化查询   | 在阶段连接到数据库后立即执行的SQL查询。用于根据需要设置数据库会话。例如，以下查询为MySQL数据库设置会话的时区： `SET time_zone = timezone;` |