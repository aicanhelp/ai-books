# SQL Server CDC客户端

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310174454899.png) 资料收集器

SQL Server CDC客户端源处理Microsoft SQL Server更改数据捕获（CDC）表中的数据。源获取时间窗口中的更改，并使用多个线程来启用数据的并行处理。

使用SQL Server CDC客户端源从CDC表生成记录。要从Microsoft SQL Server更改跟踪表中读取数据，请使用[SQL Server更改跟踪源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerChange.html#concept_ewq_b2s_r1b)。有关CDC和变更跟踪数据之间差异的更多信息，请参见[Microsoft SQL Server文档](https://docs.microsoft.com/en-us/sql/relational-databases/track-changes/track-data-changes-sql-server)。要从SQL Server临时表中读取数据，请使用[JDBC多](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MultiTableJDBCConsumer.html#concept_zp3_wnw_4y)表[使用者原点](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MultiTableJDBCConsumer.html#concept_zp3_wnw_4y)或[JDBC查询使用者原点](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JDBCConsumer.html#concept_qhf_hjr_bs)。有关时态表的更多信息，请参见[Microsoft文档](https://docs.microsoft.com/en-us/sql/relational-databases/tables/temporal-tables?view=sql-server-ver15)。

SQL Server CDC客户端起源在记录头属性中包括CRUD操作类型，因此启用CRUD的目标可以轻松处理生成的记录。有关Data Collector更改的数据处理的概述以及启用CRUD的目标的列表，请参阅[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

您可以使用此来源执行数据库复制。您可以将单独的管道与JDBC查询使用者或JDBC多表使用者起源一起使用，以读取现有数据。然后使用 SQL Server CDC客户端源启动管道，以处理后续更改。

配置原始服务器时，可以指定SQL Server捕获实例名称-原始服务器处理相关的CDC表。您可以在同一数据库中定义表组以及要使用的任何初始偏移量。省略初始偏移量时，原点将处理CDC表中的所有可用数据。

您可以启用后期表处理，以允许源处理流水线启动后出现的表。您还可以配置源，以检查已处理表中的架构更改，并在发现更改后生成事件。

要确定源如何连接到数据库，请指定连接信息，查询间隔，重试次数以及所需的任何自定义JDBC配置属性。您可以配置高级连接属性。

源可以为事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

## 数据库版本



StreamSets 已使用SQL Server 2017测试了SQL Server CDC客户端来源。

连接到Microsoft SQL Server时，不需要安装JDBC驱动程序。Data Collector包括SQL Server所需的JDBC驱动程序。

## 支持的运营

SQL Server CDC客户端源支持SQL Server插入和删除操作。在更新操作之后捕获的更新被视为更新，而在更新操作之前捕获的更新被视为不支持的操作。

## 时间窗



SQL Server CDC客户端起源从时间窗口中的表中获取更改。默认情况下，原点在一个时间窗口中获取更改，从最后提交的偏移量开始，到最新数据结束。当您有大量数据时，可以通过配置多个时间窗口来提高性能。

若要配置多个时间窗口，请将“最大事务长度”属性设置为所需的时间窗口大小。第一次时间窗口从初始偏移或最后保存的偏移开始，并在指定的秒数后结束。下一个窗口从上一个结束处开始，并在指定的秒数后结束，依此类推。在每个时间窗口内，原产地基于批次设置创建批次。

启用事件生成后，即使在随后的时间窗口中仍有待处理的情况下，起点也会在每个时间窗口的末尾产生一个无数据事件。

在第一个时间窗口内没有任何更改时，预览数据将不显示任何值。

## 多线程处理

SQL Server CDC客户端源执行并行处理，并允许创建多线程管道。

启动管道时，SQL Server CDC客户端源将检索与表配置中定义的源表关联的CDC表列表。然后，源将根据“线程数”属性使用多个并发线程。每个线程都从单个表中读取数据。

**注意：** “高级”选项卡上的“最大池大小”属性定义了源可以与数据库建立的最大连接数。它必须等于或大于为“线程数”属性定义的值。

在管道运行时，每个线程都连接到原始系统，创建一批数据，然后将其传递给可用的管道运行器。管道运行器是无源管道实例 - 管道的实例，包括管道中的所有处理器，执行程序和目的地，并在源之后处理所有管道处理。

每个管道运行程序一次处理一个批处理，就像在单个线程上运行的管道一样。当数据流减慢时，管道运行器会闲置等待，直到需要它们为止，并定期生成一个空批。您可以配置“运行者空闲时间”管道属性来指定间隔或选择退出空批次生成。

多线程管道保留每个批处理中的记录顺序，就像单线程管道一样。但是由于批处理 是由不同的流水线处理程序处理的，因此无法确保将批处理写入目的地的顺序。

有关多线程管道的更多信息，请参见《[多线程管道概述》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Multithreaded_Pipelines/MultithreadedPipelines.html#concept_zpp_2xc_py)。

### 例

假设您正在读取10张桌子。您将“线程数”属性设置为5，将“最大池大小”属性设置为6。启动管道时，源将检索表列表。然后，原始服务器创建五个线程以从前五个表中读取数据，默认情况下，Data Collector创建匹配数量的管道运行器。接收到数据后，线程会将批处理传递给每个管道运行器进行处理。

在任何给定的时刻，五个流水线运行者可以分别处理一个批处理，因此该多线程管道一次最多可以处理五个批处理。当传入数据变慢时，管道运行器将处于空闲状态，并在数据流增加时立即可用。

## 批次策略

每个原始线程都从单个表创建一批数据。您可以定义线程用于创建每个批次的以下策略之一：

- 处理表中所有可用的行

  每个线程从一个表创建多批数据，直到从该表中读取所有可用行。该线程针对从表创建的所有批处理运行一个SQL查询。然后，线程切换到下一个可用表，运行另一个SQL查询以读取该表中的所有可用行。

  例如，假设原点的批处理大小设置为100。原点被配置为使用两个并发线程并从四个表中读取，每个表包含1,000行。第一个线程运行一个SQL查询以从table1创建10批每100行的批处理，而第二个线程使用相同的策略从table2读取数据。完全读取table1和table2后，线程将切换到table3和table4并完成相同的过程。当第一个线程完成从table3的读取时，该线程切换回下一个可用表，以从上次保存的偏移量中读取所有可用数据。

- 切换表

  每个线程基于“结果集的批次”属性从一个表创建一组批次，然后切换到下一个可用表以创建下一组批次。该线程运行初始SQL查询，以从表中创建第一批批次。数据库将结果集中的其余行缓存在数据库中，以供同一线程再次访问，然后该线程切换到下一个可用表。在以下情况下有可用的表：该表没有打开的结果集缓存。在这种情况下，线程将运行初始SQL查询以创建第一个批处理，并将其余行缓存在数据库的结果集中。该表具有由同一线程创建的开放结果集缓存。在这种情况下，线程从数据库中的结果集缓存创建批处理，而不是运行另一个SQL查询。

  当表具有另一个线程创建的开放结果集缓存时，该表不可用。在关闭结果集之前，无法从该表读取其他线程。

  配置切换表策略时，请定义结果集缓存大小以及线程可以从结果集中创建的批处理数量。线程创建配置的批次数后，数据库将关闭结果集，然后可以从表中读取其他线程。

  **注意：**默认情况下，原点会指示数据库缓存无限数量的结果集。线程可以从该结果集中创建无限数量的批次。

  例如，假设源的批处理大小设置为100。将源配置为使用两个并发线程并从四个表中读取，每个表包含10,000行。您将结果集缓存大小设置为500，并将从结果集中读取的批处理数量设置为5。

  Thread1在table1上运行SQL查询，该查询返回所有10,000行。线程在读取前100行时会创建一个批处理。接下来的400行作为结果集缓存在数据库中。由于线程2同样处理table2，因此线程1切换到下一个可用的表table3，并重复相同的过程。从table3创建批处理后，线程1切换回table1，并从其先前在数据库中缓存的结果集中检索下一批行。

  线程1使用表1的结果集缓存创建了五个批次之后，数据库将关闭结果集缓存。线程1切换到下一个可用表。从最后保存的偏移量开始，另一个线程运行SQL查询以从table1中读取其他行。

## 表配置

配置SQL Server CDC客户端时，可以使用一组表配置属性来定义多个CDC表。您还可以定义多个表配置来处理多组CDC表。

定义表配置时，可以为每组表定义以下属性：

- 捕获实例名称

  确定要处理的CDC表。Microsoft SQL Server CDC表的命名约定为<捕获实例名称> _CT。指定此属性时，请使用捕获实例名称，而不要使用要处理的CDC表的名称。例如，指定`dbo.customer`源表，而不是关联的CDC表`dbo_customer_CT`。

  配置捕获实例名称属性时，可以指定一种模式，该模式描述要使用的一组捕获实例名称。如果未在数据库中设置捕获实例名称，则可以为要处理的表名称指定一个模式。

  您可以使用类似SQL的语法来定义要处理的CDC表集。例如，该模式`east%`匹配名称以“ east”开头的表。有关SQL LIKE语法的更多信息，请参见[https://msdn.microsoft.com/zh-cn/library/ms179859.aspx](https://msdn.microsoft.com/en-us/library/ms179859.aspx)。

  配置捕获实例名称属性时，可以使用以下格式之一：要处理与指定的捕获实例名称模式匹配的CDC表，请使用以下格式：``根据捕获实例名称创建CDC表时，请使用此格式。您可以使用该模式来处理全套CDC表或从处理中排除某些CDC表。例如，假设您有一个`Sales.Accounts`带有名为的CDC表的表`Sales_Accounts_CT`。将几列添加到表后，您将创建一个名为的新CDC表 `Sales_Accounts2_CT`。要处理两个CDC表，您可以指定以下捕获实例名称模式： `Sales_Accounts%`。要仅处理模式更改后发生的CDC数据，可以指定以下捕获实例名称：`Sales_Accounts2`。要处理指定数据表的所有可用CDC表，请使用以下格式：`_`基于数据表而不是捕获实例名称创建CDC表时，请使用此格式。例如，要处理Sales模式中数据表的所有可用CDC表，可以使用 `Sales_%`。或者，要处理与带有Transact前缀的一组数据表关联的CDC表，可以使用`Sales_Transact%`。要处理与模式关联的所有CDC表，请使用以下格式：`_%`例如，要处理销售模式中的所有表，请输入 `sales_%`。

- 表排除模式

  （可选）为要从查询中排除的表名指定一个正则表达式模式。例如，假设您要处理模式中的所有CDC表，但以“ dept”开头的表除外。您可以将默认％用于表名模式，并为表排除模式输入dept *。有关将正则表达式与Data Collector一起使用的更多信息，请参见[正则表达式概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-RegEx/RegEx-Title.html#concept_vd4_nsc_gs)。

- 初始偏移

  要处理现有数据，请指定初始偏移量。如果未设置，则原点将处理所有可用的CDC数据。

  SQL Server CDC客户端原点使用该`__$start_lsn` 列作为偏移列。要处理现有数据，请定义要使用的偏移值。该偏移量用于表配置中包括的所有表。**重要说明：**指定初始偏移量时，原点将从大于或等于指定偏移量的第一个值开始。

## 初始表订购策略

您可以定义原始位置用来读取表格的初始顺序。

定义以下初始表顺序策略之一：

- 没有

  按照数据库中列出的顺序读取表。

- 按字母顺序

  按字母顺序读取表。

原点仅在初始读取表时才使用表顺序策略。当线程切换回先前读取的表时，无论定义的顺序如何，它们都将从下一个可用表中读取。

## 允许后期处理表

您可以配置SQL Server CDC客户端以处理管道启动后出现的CDC表中的数据。

当允许后期处理表时，SQL Server CDC客户端起源使用后台线程来检查后期CDC表。原点检查以常规的用户定义间隔进行。

要启用后期表处理，请配置以下属性：

- 在“ JDBC”选项卡上，选择“允许延迟表”属性。
- 要定义检查新表之前的等待时间，请配置“新表发现间隔”属性。
- 在“高级”选项卡上，将“最大池大小”和“最小空闲连接”属性设置为比“线程数”属性多一个线程。

## 检查架构更改

您可以配置SQL Server CDC客户端源，以检查正在处理的表中的架构更改。在检查架构更改时，源在SQL查询中包含架构检查语句。

当原始检查架构更改时，它将执行以下任务：

1. 根据查询间隔属性，定期将当前表架构与原始表架构进行比较。

2. 如果确定表的模式已更改，则会生成一个模式更改事件，该事件说明具有更改后的模式的表或捕获实例名称。

   起源在每次找到架构更改时都会生成一个架构更改事件：每个具有架构更改的表都将发生一个。

   **注意：**由于原点会继续定期检查模式更改，直到管道停止，所以单个模式更改会生成大量事件。

3. 它可以将确切的列名或数据类型更改写入Data Collector日志。要启用写入日志，必须将日志级别设置为“跟踪”。有关更改日志级别的信息，请参阅“ [修改日志级别”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Administration/ModifyingLogLevel.html#task_lkv_g2f_wy)。

若要使SQL Server CDC客户端起源检查架构更改并在发现它们时生成事件，请启用以下两个属性：

- 在常规选项卡上，选择生产事件属性。
- 在“ JDBC”选项卡上，选择“启用架构更改事件”属性。

## 产生的记录

当SQL Server CDC客户端源生成记录时，它将CDC信息（例如CDC操作和起始LSN值）放置在记录头属性中。并将CDC表提供的行数据放入生成的记录的字段中。

### 记录标题属性

SQL Server CDC客户端起源会生成JDBC记录头属性，这些属性为每个记录提供SQL Server CDC数据，例如开始或结束日志序列号（LSN）。

来源还包括sdc.operation.type属性和SQL Server CDC表中的信息。SQL Server CDC客户端标头属性的前缀为 `jdbc`。标头属性名称中包含SQL Server CDC列名称的名称，如下所示：`jdbc.`。

您可以使用`record:attribute`或 `record:attributeOrDefault`函数来访问属性中的信息。有关使用记录标题属性的更多信息，请参见[使用标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_rd2_ghz_dz)。

源提供以下标头属性：

| 标头属性名称                   | 描述                                                         |
| :----------------------------- | :----------------------------------------------------------- |
| sdc.operation.type             | 原点使用以下值表示操作类型：1个用于插入2代表删除3个更新，包括更新操作后捕获的更新5，用于不受支持的操作，包括在更新操作之前捕获的更新 |
| jdbc.cdc.source_schema_name    | 提供CDC数据的源模式。                                        |
| jdbc.cdc.source_name           | 提供CDC数据的源表。                                          |
| jdbc.tables                    | 提供记录中字段的逗号分隔的源表列表。**注意：**并非所有的JDBC驱动程序都提供此信息。 |
| jdbc。<列名称> .jdbcType       | 提供记录中每个字段的原始SQL数据类型的数值。有关与数值对应的数据类型的列表，请参见[Java文档](https://docs.oracle.com/javase/8/docs/api/constant-values.html#java.sql.Types.ARRAY)。因为从SQL Server CDC表中读取的记录包含CDC列，所以生成的记录还包含那些列的对应的jdbc。<列名> .jdbcType标头属性。例如，由于原始数据包括__ $ start_lsn列，所以结果记录具有jdbc .__ $ start_lsn.jdbc.Type标头属性。它还具有jdbc .__ $ start_lsn属性，该属性由源单独生成，如下所述。 |
| jdbc。<列名称> .jdbc.precision | 提供所有数字和十进制字段的原始精度。                         |
| jdbc。<列名称> .jdbc.scale     | 提供所有数字和十进制字段的原始比例。                         |
| jdbc。__ $ command_id          | SQL Server CDC __ $ command_id列中的数据。                   |
| jdbc .__ $ end_lsn             | SQL Server CDC __ $ end_lsn列中的数据。                      |
| jdbc .__ $ operation           | 使用SQL Server代码的CRUD操作类型，如SQL Server CDC __ $ operation列中所定义。 |
| jdbc .__ $ seqval              | SQL Server CDC __ $ seqval列中的数据。                       |
| jdbc .__ $ start_lsn           | SQL Server CDC __ $ start_lsn列中的数据。                    |
| jdbc .__ $ update_mask         | SQL Server CDC __ $ update_mask列中的数据。                  |

有关CDC属性的详细信息，请参见[SQL Server文档](https://docs.microsoft.com/en-us/sql/relational-databases/system-functions/cdc-fn-cdc-get-all-changes-capture-instance-transact-sql)。

### CRUD操作标头属性

生成记录时，SQL Server CDC客户端来源在以下两个记录头属性中指定操作类型：

- sdc.operation.type

  SQL Server CDC客户端源将操作类型写入sdc.operation.type记录头属性。

  原点在sdc.operation.type记录头属性中使用以下值表示操作类型：1个用于插入2代表删除3个更新，包括更新操作后捕获的更新5，用于不受支持的操作，包括在更新操作之前捕获的更新

  如果您在诸如JDBC Producer或Elasticsearch之类的管道中使用启用CRUD的目标，则该目标可以在写入目标系统时使用操作类型。必要时，可以使用表达式评估器或脚本处理器来处理`sdc.operation.type`header属性中的值 。有关Data Collector更改的数据处理的概述以及启用CRUD的目标的列表，请参阅[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

  使用启用CRUD的目标时，目标仅在sdc.operation.type属性中查找操作类型。

- jdbc .__ $ operation

  SQL Server CDC客户端来源将来自SQL Server __ $ operation列的值放在jdbc .__ $ operation记录标题属性中。结果，jdbc .__ $ operation记录标头属性包含使用SQL Server CDC代码定义的CRUD操作类型。

  请注意，启用CRUD的阶段仅使用sdc.operation.type标头属性，而不检查jdbc .__ $ operation标头属性。

  SQL Server CDC使用以下代码定义操作类型：1个要删除2个用于插入3用于更新操作之前捕获的更新4用于更新操作后捕获的更新

## 事件产生

SQL Server CDC客户端起源可以生成可在事件流中使用的事件。启用事件生成后，原始将在完成对所有表的指定查询返回的数据的处理后，生成一个事件。

如果启用模式更改事件生成，则起源在每次找到模式更改时也会生成一个事件。

SQL Server CDC客户端事件可以任何逻辑方式使用。例如：

- 当原始完成处理可用数据时，使用Pipeline Finisher执行程序停止管道并将管道转换为Finished状态。

  重新启动由Pipeline Finisher执行程序停止的管道时，原点将从上次保存的偏移开始继续处理，除非您重置原点。

  有关示例，请参见[案例研究：停止管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_kff_ykv_lz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

由SQL Server CDC客户端起源生成的事件记录具有以下与事件相关的记录头属性：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型之一：no-more-data-当起点在一个[时间窗口中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_v4l_m1l_jhb)完成对所有请求的CDC数据的处理时生成 。schema-change-模式更改-仅在启用[检查模式更改的检查](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_avq_s2q_qbb)并且源确定要处理的一个表发生[模式更改](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_avq_s2q_qbb)时才生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

SQL Server CDC客户端源可以生成以下类型的事件记录：

- 没有更多数据

  当原始服务器在一个[时间窗口中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_v4l_m1l_jhb)完成对所有可用数据的处理，并且为事件生成延迟配置的秒数过去了，而似乎没有任何新文件被处理时，原始服务器将生成无数据事件记录。即使后续的时间窗口仍待处理，该来源也会生成事件记录。由源生成的no-more-data事件记录的sdc.event.type设置为no-more-data，并且不包括任何其他字段。

- 模式变更

  仅当您启用起点[检查架构更改时](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_avq_s2q_qbb)，起点才生成架构更改事件记录，并且起点发现架构更改。由源生成的架构更改事件记录将sdc.event.type设置为架构更改，并包含以下字段：事件记录字段描述捕获实例名称与具有架构更改的表关联的捕获实例或CDC表的名称。源表模式名称包含数据表的架构的名称。源表名称具有模式的数据表的名称已更改。

## 配置SQL Server CDC客户端来源

配置一个SQL Server CDC客户端源，以处理Microsoft SQL Server CDC表中的数据。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_byp_dgv_s1b) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **JDBC”**选项卡上，配置以下属性：

   | JDBC属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | JDBC连接字符串                                               | 用于连接数据库的连接字符串。                                 |
   | 使用凭证                                                     | 在“凭据”选项卡上启用输入凭据。在JDBC连接字符串中不包括凭据时使用。 |
   | 每秒查询                                                     | 在所有分区和表中每秒运行的最大查询数。无限制地使用0。默认值是10。 |
   | [线程数](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_ofh_gns_r1b) | 原点生成并用于多线程处理的线程数。在“高级”选项卡上将“最大池大小”属性配置为等于或大于此值。 |
   | 使用直接表查询                                               | 直接查询CDC表。选中后，原点将直接从CDC表中获取数据。否则，源将使用系统查询从CDC表中获取数据。[Microsoft](https://docs.microsoft.com/en-us/sql/integration-services/change-data-capture/create-the-function-to-retrieve-the-change-data?view=sql-server-2017) 建议在大多数情况下使用系统查询。在适合您的服务器负载时，请使用直接表查询。 |
   | [每批次策略](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_qms_jns_r1b) | 创建每批数据的策略：切换表-仅执行多线程表处理时，每个线程从一个表创建一批数据，然后切换到下一个可用表以创建下一批。配置交换表策略时，定义结果集缓存大小和结果集批次属性。处理表中的所有可用行-仅执行多线程表处理时，每个线程都会从一个表中创建多批数据，直到从该表中读取所有可用行。当执行多线程分区处理或表和分区处理的混合时，每个批处理策略的行为都更加复杂。有关详细信息，请参见[处理队列](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MultiTableJDBCConsumer.html#concept_czt_ql2_r1b)。 |
   | 最大批次大小（记录）                                         | 批处理中包含的最大记录数。                                   |
   | [结果集中的批次](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_qms_jns_r1b) | 从结果集中创建的批次数。在一个线程创建了此批次数量之后，数据库关闭结果集，然后另一个线程可以从同一表中读取。使用正整数设置对从结果集中创建的批次数量的限制。使用-1退出此属性。默认情况下，原产地从结果集中创建无限数量的批次，使结果集保持尽可能长的打开时间。 |
   | [结果集缓存大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_qms_jns_r1b) | 要缓存在数据库中的结果集数。使用正整数设置对缓存结果集数量的限制。使用-1退出此属性。默认情况下，原点缓存无限数量的结果集。 |
   | 最大布料大小（字符）                                         | Clob字段中要读取的最大字符数。较大的数据将被截断。           |
   | 最大Blob大小（字节）                                         | Blob字段中要读取的最大字节数。                               |
   | SQL错误重试次数                                              | 线程在收到SQL错误后尝试读取一批数据的次数。线程重试此次数后，线程将根据为源配置的错误处理来处理错误。用于处理瞬态网络或连接问题，这些问题阻止线程读取一批数据。默认值为0。 |
   | 允许晚餐桌                                                   | 允许原点处理在管道启动后出现的表。启用后，原点将使用后台线程检查要处理的其他表。有关调整相关配置属性的信息，请参阅“ [允许后期处理表”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_nxm_1lp_qbb)。 |
   | 新表发现间隔                                                 | 等待检查其他表的时间。如果启用了允许延迟表，则可用。         |
   | [启用架构更改事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_avq_s2q_qbb) | 启用对架构更改的常规检查。启用后，源将根据查询间隔属性定期检查所有已处理表的模式更改。起源在每次发现架构更改时都会生成一个架构更改事件。 |
   | [最大交易时长](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_v4l_m1l_jhb) | 原点用来获取数据的时间窗口的大小。以秒为单位或使用时间表达式指定。默认值为-1。设置为-1时，原点将在一个时间窗口中获取更改，从最后提交的偏移量开始，到最新数据结束。设置值可提高处理大量数据的性能。设置值时，原点获取会在多个时间窗口内发生变化。该属性定义时间窗口的大小。 |
   | [没有数据事件生成延迟（秒）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_ckx_z3v_s1b) | 处理所有行后延迟无数据事件生成的秒数。用于在生成no-more-data事件之前留出时间让其他数据到达。 |
   | 将时间戳转换为字符串                                         | 使原点能够将时间戳记写为字符串值而不是日期时间值。字符串保持存储在源数据库中的精度。例如，字符串可以保持高精度SQL Server datetime2字段的精度。在将时间戳写入不存储纳秒的Data Collector日期或时间数据类型时，原点会将距时间戳的任何纳秒存储在[field属性中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)。 |
   | 提取大小                                                     | 要获取并存储在Data Collector计算机上的内存中的最大行数。大小不能为零。默认值为1,000。 |
   | 其他JDBC配置属性                                             | 要使用的其他JDBC配置属性。要添加属性，请单击 **添加**并定义JDBC属性名称和值。使用JDBC期望的属性名称和值。 |

3. 在**CDC**选项卡上，定义一个或多个表配置。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击**添加**图标以定义另一个表配置。

   | [中华网物业](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_sx3_d11_s1b) | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 捕获实例名称                                                 | 确定要处理的CDC表集。使用SQL LIKE语法为表名定义表名模式。使用以下格式之一：要处理与指定的捕获实例名称模式匹配的CDC表，请使用以下格式：``要处理指定数据表的所有可用CDC表，请使用以下格式：`_`要处理与模式关联的所有CDC表，请使用以下格式：`_%`默认值为dbo_％，它将处理默认dbo模式中的所有可用CDC表。 |
   | 表排除模式                                                   | 表名的模式，此表配置要从中排除这些表名。使用基于Java的正则表达式或regex定义模式。如果不需要排除任何表，请留空。 |
   | 初始偏移                                                     | 用于此表配置的偏移值。当管道启动时，偏移值确定原点在哪里开始处理：0或更高-从offset列中的第一个值大于或等于offset值开始。空白-从偏移列中的最小值开始。-1-从新的传入更改开始，而忽略现有数据。 |

4. 如果在**JDBC**选项卡上将源配置为与JDBC连接字符串分开输入JDBC凭据，则在“ **凭据”** 选项卡上配置以下属性：

   | 凭证属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 用户名   | JDBC连接的用户名。                                           |
   | 密码     | JDBC帐户的密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |

5. 使用低于4.0的JDBC版本时，在“ **旧版驱动程序”**选项卡上，可以选择配置以下属性：

   | 旧版驱动程序属性     | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | JDBC类驱动程序名称   | JDBC驱动程序的类名。早于版本4.0的JDBC版本必需。              |
   | 连接运行状况测试查询 | 可选查询，用于测试连接的运行状况。仅当JDBC版本低于4.0时才建议使用。 |

6. 在“ **高级”**选项卡上，可以选择配置以下属性：

   这些属性的默认值在大多数情况下都应该起作用：

   | 先进物业                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 最大游泳池                                                   | 创建的最大连接数。必须等于或大于“线程数”属性的值。默认值为1。 |
   | 最小空闲连接                                                 | 创建和维护的最小连接数。要定义固定连接池，请设置为与“最大池大小”相同的值。默认值为1。 |
   | 连接超时                                                     | 等待连接的最长时间。在表达式中使用时间常数来定义时间增量。默认值为30秒，定义如下：`${30 * SECONDS}` |
   | 空闲超时                                                     | 允许连接空闲的最长时间。在表达式中使用时间常数来定义时间增量。使用0以避免删除任何空闲连接。当输入的值接近或超过连接的最大生存期时，Data Collector将忽略空闲超时。默认值为10分钟，定义如下：`${10 * MINUTES}` |
   | 最大连接寿命                                                 | 连接的最大寿命。在表达式中使用时间常数来定义时间增量。使用0设置最大寿命。设置最大寿命时，最小有效值为30分钟。默认值为30分钟，定义如下：`${30 * MINUTES}` |
   | 自动提交                                                     | 确定是否启用自动提交模式。在自动提交模式下，数据库为每个记录提交数据。默认设置为禁用。 |
   | 强制执行只读连接                                             | 创建只读连接以避免任何类型的写入。默认启用。不建议禁用此属性。 |
   | 交易隔离                                                     | 用于连接数据库的事务隔离级别。默认是为数据库设置的默认事务隔离级别。您可以通过将级别设置为以下任意值来覆盖数据库默认值：阅读已提交阅读未提交可重复读可序列化 |
   | 初始化查询                                                   | 在阶段连接到数据库后立即执行的SQL查询。用于根据需要设置数据库会话。例如，以下查询为MySQL数据库设置会话的时区： `SET time_zone = timezone;` |
   | [初始表订购策略](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_nff_2hx_4y) | 用于读取表的初始顺序：无-按表在数据库中列出的顺序读取表。按字母顺序-按字母顺序读取表。 |
   | 在未知类型上                                                 | 原点遇到数据类型不受支持的记录时要采取的措施：停止管道-完成对先前记录的处理后，停止管道。转换为字符串-将数据转换为字符串并继续处理。 |