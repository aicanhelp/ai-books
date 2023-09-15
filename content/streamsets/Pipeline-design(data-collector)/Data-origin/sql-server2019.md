# QL Server 2019 BDC多表使用者

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310174419321.png) 资料收集器

SQL Server 2019 BDC多表使用者来源通过JDBC连接从Microsoft SQL Server 2019大数据群集（BDC）读取数据。使用源从同一数据库中的一个或多个模式读取多个表。例如，您可以使用源来复制数据库。

配置原始服务器时，可以指定连接信息和可选的自定义JDBC配置属性，以确定原始服务器如何连接到SQL Server 2019 BDC。您指定一个数据库，然后定义要从该数据库读取的表组。源根据您定义的表配置生成SQL查询，然后将数据作为具有列名和字段值的映射返回。

定义表配置时，可以选择覆盖默认键列并指定要使用的初始偏移量。默认情况下，起点使用主键列或用户定义的偏移量列来递增地处理表以跟踪其进度。您可以将原点配置为执行非增量处理，以使其也可以处理没有键或偏移列的表。

您可以将源配置为执行多线程分区处理，多线程表处理，或使用默认值-两者的混合。配置分区时，可以配置偏移量大小，活动分区数和偏移条件。

您可以定义源用于创建每批数据的策略以及要从每个结果集中创建的批数。您可以配置高级属性，例如从表读取的初始顺序，与连接相关的属性和事务隔离。并且您可以指定遇到不支持的数据类型时原点的作用：将数据转换为字符串或停止管道。

管道停止时，SQL Server 2019 BDC多表使用者来源记录会在停止读取的地方进行注释。当管道再次启动时，原点将从默认情况下停止的地方继续进行处理。您可以使用定义的任何初始偏移量来重置原点，以处理所有可用数据。

源可以为事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

在使用SQL Server 2019 BDC多表使用者来源之前，必须先安装SQL Server 2019大数据群集阶段库并完成其他先决任务。SQL Server 2019 Big Data Cluster 阶段库是一个[Enterprise阶段库](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Installation/EnterpriseStageLibraries.html#concept_s1r_1gg_dhb)，仅供开发用途免费。有关购买用于生产的舞台库的信息，请[联系StreamSets](https://streamsets.com/contact-us/)。

## 先决条件

在配置SQL Server 2019 BDC多表使用者来源之前，请完成以下先决条件：

1. 确保您可以使用SQL Server凭据访问SQL Server 2019 BDC。
2. [安装SQL Server 2019大数据群集阶段库](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCBulk_Prereq_InstallLib)。
3. 检索连接到SQL Server 2019 BDC所需的JDBC URL ，并使用该URL配置源。

### 安装SQL Server 2019大数据群集阶段库

您必须先安装SQL Server 2019大数据群集阶段库，然后才能使用SQL Server 2019 BDC多表使用者来源。SQL Server 2019大数据群集阶段库包含JDBC驱动程序，原始驱动程序用于访问SQL Server 2019大数据群集。

SQL Server 2019 Big Data Cluster 阶段库是一个Enterprise阶段库，仅供开发用途免费。有关购买用于生产的舞台库的信息，请[联系StreamSets](https://streamsets.com/contact-us/)。

您可以使用以下任何一种方法来安装SQL Server 2019 Big Data Cluster阶段库：

- 将库安装在现有的

  Data Collector中

  。使用对

  Data Collector

  安装有效的技术：

  - [使用软件包管理器进行安装](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCBulk_Prereq_InstallwPack) -可用于tarball Data Collector安装。
  - [作为自定义阶段库安装](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCBulk_Prereq_InstallAsCustom) -可用于tarball，RPM或Cloudera Manager Data Collector安装。

- 如果使用

  Control Hub

  ，请将库安装在业务流程框架（例如Kubernetes）一部分的预配

  Data Collector

  容器中。使用对您的环境有效的技术：

  - 在生产环境中，请参阅Control Hub主题[Provisioning Data Collectors](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/DataCollectorsProvisioned/ProvisionSteps.html#concept_wl2_snb_12b)。您必须在自定义的StreamSets Data Collector Docker映像中安装SQL Server 2019 Big Data Cluster阶段库。

  - 在开发环境中，您可以运行

    StreamSets

    开发的部署脚本，以通过Control Hub尝试使用带有Data Collector的

    SQL Server 2019 BDC

    。

    

    

    

    

    该脚本在Kubernetes群集上部署了Control Hub Provisioning代理和数据收集器。该脚本会在已部署的Data Collector中自动安装SQL Server 2019 Big Data Cluster阶段库。您可以将该数据收集器用作创作数据收集器来创建和测试SQL Server 2019 BDC管道。

    仅在开发环境中使用脚本。有关更多信息，请参见[Github中](https://github.com/streamsets/sql-server-bdc-deployment)的[部署脚本](https://github.com/streamsets/sql-server-bdc-deployment)。

#### 支持的版本

下表列出了要与特定Data Collector 版本一起使用的SQL Server 2019 Big Data Cluster阶段库的版本：

| 数据收集器版本              | 支持的舞台库版本                     |
| :-------------------------- | :----------------------------------- |
| 数据收集器 3.12.x及更高版本 | SQL Server 2019大数据群集企业库1.0.0 |

#### 使用软件包管理器安装

您可以使用程序包管理器在tarball Data Collector 安装上安装SQL Server 2019 Big Data Cluster阶段库。

1. 单击“程序包管理器”图标：![img](imgs/icon_PackageManager-20200310174419469.png)。

2. 在导航面板中，单击**Enterprise Stage Libraries**。

3. 选择**SQL Server 2019大数据群集企业库**，然后单击**安装**图标： ![img](imgs/icon_InstallLib-20200310174419745.png)。

4. 阅读StreamSets 订阅服务条款。如果您同意，请选中复选框，然后单击“ **安装”**。

   Data Collector将安装所选的舞台库。

5. 重新启动Data Collector。

#### 作为自定义舞台库安装

您可以在tarball，RPM或Cloudera Manager Data Collector 安装中将SQL Server 2019 Big Data Cluster Enterprise阶段库安装为自定义阶段库。

1. 要下载舞台库，请转到[StreamSets下载企业连接器](https://streamsets.com/download/enterprise-connectors/)页面。

   该网页显示按发布日期组织的Enterprise阶段库，并在页面顶部显示最新版本。

2. 单击您要下载的Enterprise阶段库名称和版本。

3. 在“ **下载企业连接器”**表单中，输入您的姓名和联系信息。

4. 阅读StreamSets订阅服务条款。如果您同意，请接受服务条款，然后单击“ **提交”**。

   舞台库下载。

5. 将Enterprise阶段库安装和管理为自定义阶段库。

   有关更多信息，请参见[Custom Stage Libraries](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/CustomStageLibraries.html#concept_pmc_jk1_1x)。

## 外部表

在SQL Server 2019 BDC中，您可以定义外部表来访问在SQL Server中虚拟化的数据。有关更多信息，请参见[Microsoft文档](https://docs.microsoft.com/en-us/sql/relational-databases/polybase/data-virtualization?view=sqlallproducts-allversions)。

要将SQL Server 2019 BDC多表使用者来源配置为从外部表读取：

- 在JDBC选项卡上，将Database属性设置为SQL Server数据库，SQL Server 2019 BDC将在该数据库中虚拟化外部表。
- 在“表”选项卡上，创建一个包含外部表的表配置。例如，您可以定义一个配置，该配置在“表名模式”属性中包括外部表名。

## 表配置

当配置SQL Server 2019 BDC多表使用者来源时，您可以为要读取的每组表定义一个表配置。表配置定义了一组具有相同表名称模式的表，这些表来自一个或多个具有相同名称模式的模式，并且具有适当的主键或相同的用户定义偏移列。

您可以定义一个或多个表配置。

例如，您可以定义一个表配置来复制一个数据库，该数据库的每个表都具有适当的主键。您只需输入模式名称，然后使用与模式`%`中的所有表匹配的默认表名称模式。

让我们看一个示例，您需要定义多个表配置。假设您要将关系数据库中的表复制到HBase群集。SALES模式包含十个表，但是您只想复制以下四个表：

- `store_a`
- `store_b`
- `store_c`
- `customers`

三个存储表用`orderID`作主键。您要覆盖客户表的主键，因此需要定义 `customerID`为该表的偏移量列。您要读取表中的所有可用数据，因此不需要定义初始偏移值。

您可以如下定义一个表配置，以便原始服务器可以读取三个存储表：

- 模式- SALES
- 表名称模式- store%

然后，按如下所示定义第二个表配置，以便原始服务器可以读取客户表：

- 模式- SALES
- 表名称模式- customers
- 覆盖偏移列- enabled
- 偏移列- customerID

### 架构和表名称模式

您可以通过定义表配置的架构和表名称模式来定义SQL Server 2019 BDC多表使用者来源读取的表组。源读取名称与架构中的表模式匹配且名称与架构模式匹配的所有表。

模式和表名称模式使用SQL LIKE语法。例如，LIKE语法使用百分比通配符（％）表示零个或多个字符的任何字符串。模式名称模式`st%`与名称以开头的模式匹配 `st`。默认表名称模式`%`与指定架构中的所有表匹配。

有关SQL LIKE语法的有效模式的更多信息，请参见[Microsoft文档](https://msdn.microsoft.com/en-us/library/ms179859.aspx)。

您可以选择定义一个架构或表排除模式，以排除某些架构或表被读取。模式和表排除模式使用基于Java的正则表达式或regex。有关将正则表达式与Data Collector一起使用的更多信息，请参见[正则表达式概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-RegEx/RegEx-Title.html#concept_vd4_nsc_gs)。

例如，假设您要读取`US_WEST` 和`US_EAST`模式中的所有表，但以开头的表除外 `dept`。您输入以下架构，表名称模式和表排除模式：

- 模式- US%
- 表名称模式- %
- 表排除模式- dept.*

由于不需要排除任何架构，因此只需将架构排除模式留空。

或者，假设您要从所有模式中读取所有表，除了 `sys`和`system`模式。您输入以下架构，表名模式和架构排除模式，并将表排除模式留为空白：

- 模式- %
- 表名称模式- %
- 模式排除模式- sys|system

### 偏移列和值

SQL Server 2019 BDC多表使用者来源使用偏移列和初始偏移值来确定从何处开始读取表和分区中的数据。

默认情况下，原点将表的主键用作偏移列，并且不使用初始偏移值。当您使用多线程表处理并且表具有复合主键时，原点会将每个主键用作偏移列。您不能将复合键与多线程分区处理一起使用。

默认情况下，在启动管道时，源会从每个表中读取所有可用数据。启动管道时，原始服务器使用以下语法生成SQL查询：

```
SELECT * FROM <table> ORDER BY <offset column_1>, <offset column_2>, ...
```

其中，代表表的每个主键，例如表具有复合主键时。当您重新启动管道时，或者当原点切换回先前读取的表时，原点会向SQL查询添加WHERE子句以继续从最后保存的偏移量进行读取。``

若要使用此默认行为，您不需要配置任何偏移属性。

您可以对原点处理偏移列和初始偏移值的方式进行以下更改：

- 覆盖主键作为偏移量列

  您可以覆盖主键并定义另一个或多个偏移量列。或者，如果表没有主键，则可以定义要使用的偏移列。

  **重要提示：**最佳做法是，用户定义的offset列应为不包含空值的增量且唯一的列。如果该列不是唯一的-也就是说，该列的多个行可以具有相同的值-管道重新启动时可能会丢失数据。有关详细信息，请参见[多重偏移值处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-Partition_MultiOffset)。

  强烈建议在此列上建立索引，因为基础查询在此列上使用ORDER BY和不等号运算符。

- 定义初始偏移值

  初始偏移值是您希望SQL Server 2019 BDC多表使用者原点开始读取的偏移列中的值。定义初始偏移值时，必须首先输入偏移列名称，然后输入该值。如果您使用默认主键作为偏移量列，请输入主键的名称。

  如果为单个偏移量列定义初始偏移量值，则原点将使用以下语法生成SQL查询：`SELECT * FROM  ORDER BY  WHERE  > ${offset}`

  如果定义了多个偏移量列，则必须按照定义这些列的顺序为每个列定义一个初始偏移值。原点使用所有列的初始偏移值来确定从何处开始读取数据。例如，重写主键与下列偏移列：`p1`，`p2`， `p3`并定义为每个列的初始偏移值。原点使用以下语法生成SQL查询：`SELECT * FROM  ORDER BY p1, p2, p3 WHERE (p1 > ${offset1}) OR (p1 = ${offset1} AND p2 > ${offset2}) OR (p1 = ${offset1} AND p2 = ${offset2} AND p3 > ${offset3})`**注意：** Data Collector将Datetime列的偏移量存储为Long值。对于具有Datetime数据类型的偏移列，输入初始值作为Long值。您可以使用时间函数将Datetime值转换为Long值。例如，以下表达式将以字符串形式输入的日期转换为Date对象，然后转换为Long值：`${time:dateTimeToMilliseconds(time:extractDateFromString('2017-05-01 20:15:30.915','yyyy-MM-dd HH:mm:ss.SSS'))} `

- 定义其他偏移列条件

  您可以使用表达式语言来定义附加条件，原始条件将用来确定从何处开始读取数据。源将定义的条件添加到SQL查询的WHERE子句中。

  您可以`offset:column`在条件中使用该函数按位置访问偏移列。例如，如果您的表具有偏移量列`p1`和`p2`，则 `offset:column(0)`返回值 `p1`while，`offset:column(1)`返回值`p2`。

  假设您将一`transaction_time`列定义为偏移列。当源读取表时，多个活动事务将使用该`transaction_time`列的当前时间戳写入表中。当原点完成使用当前时间戳读取第一条记录时，原点将继续读取下一个偏移量并跳过具有当前时间戳的某些行。您可以输入以下偏移列条件，以确保原点从所有偏移列中读取，且时间戳小于当前时间：`${offset:column(0)} < ${time:now()}`

  如果您的数据库要求使用特定格式的日期时间，则可以使用该 `time:extractStringFromDate`函数指定格式。例如：`$ {offset：column（0）} <'$ {time：extractStringFromDate（time：now（），“ yyyy-MM-dd HH：mm：ss”）}'`

### 从视图中阅读

除表外，SQL Server 2019 BDC多表使用者来源还可以从视图中读取。

源从定义的表配置中包括的所有表和视图中读取。如果表配置包含您不想阅读的视图，只需将其从配置中排除。

使用原点从简单视图中读取，这些视图从单个表中选择数据。

我们不建议使用原点从复杂的视图中读取数据，这些视图使用联接合并来自两个或多个表的数据。如果源从复杂的视图中读取，则它会并行运行多个查询，这可能会导致数据库上的工作量很大。

## 多线程处理模式

SQL Server 2019 BDC多表使用者来源执行并行处理并启用多线程管道的创建。源可以使用多个线程来处理整个表或表中的分区。

默认情况下，源对满足分区处理要求的表执行多线程分区处理，而对所有其他表执行多线程表处理。使用默认行为时，来源会在Data Collector 日志中记录允许分区处理的表。需要时，可以将源配置为要求对所有表进行分区处理或仅执行表处理。您还可以在需要时允许对表进行单线程[非增量处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_xwr_bhm_nbb)。

源提供以下多线程处理模式：

- [多线程表处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_tz5_fw5_gz) -每个表最多可使用一个线程。可以处理具有多个偏移量列的表。

- 多线程分区处理

   -每个表分区，源服务器最多可以使用一个线程。用于处理比多线程表处理更大的数据量。

  多线程分区处理需要单个主键或受支持的数据类型的用户定义的偏移量列，以及用于分区创建的其他详细信息。具有复合键或不支持的数据类型的键或用户定义的偏移量列的表无法分区。

在配置原始服务器时，可以指定要处理的表以及用于每组表的多线程分区处理模式：

- 关-用于执行多线程表处理。

  启用后，可用于执行没有键或偏移列的表的非增量加载。

- 开（尽力而为）-用于在可能的情况下执行分区处理，并允许对具有多个键或偏移列的表进行多线程表处理。

  启用后，可用于执行没有键或偏移列的表的非增量加载。

- 启用（必填）-用于对所有指定的表执行分区处理。

  不允许对不满足分区处理要求的表执行其他类型的处理。

### 多线程表处理

执行多线程表处理时，启动管道时，SQL Serer 2019 BDC多表使用者来源将检索表配置中定义的表的列表。然后，源将根据“线程数”属性使用多个并发线程。每个线程从一个表中读取数据，并且每个表一次最多只能有一个线程从中读取数据。

**注意：** “高级”选项卡上的“最大池大小”属性定义了源可以与数据库建立的最大连接数。它必须等于或大于为“线程数”属性定义的值。

在管道运行时，每个线程都连接到原始系统，创建一批数据，然后将其传递给可用的管道运行器。管道运行器是无源管道实例 - 管道的实例，包括管道中的所有处理器，执行程序和目的地，并在源之后处理所有管道处理。

每个管道运行程序一次处理一个批处理，就像在单个线程上运行的管道一样。当数据流减慢时，管道运行器会闲置等待，直到需要它们为止，并定期生成一个空批。您可以配置“运行者空闲时间”管道属性来指定间隔或选择退出空批次生成。

多线程管道保留每个批处理中的记录顺序，就像单线程管道一样。但是由于批处理 是由不同的流水线处理程序处理的，因此无法确保将批处理写入目的地的顺序。

批处理的顺序取决于许多因素。有关更多信息，请参见[处理队列](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_czt_ql2_r1c)。

有关多线程管道的更多信息，请参见《[多线程管道概述》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Multithreaded_Pipelines/MultithreadedPipelines.html#concept_zpp_2xc_py)。

#### 例

假设您正在从十张桌子中阅读。您将“线程数”属性设置为5，将“最大池大小”属性设置为6。启动管道时，源将检索表列表。然后，原始服务器创建五个线程以从前五个表中读取数据，默认情况下，Data Collector 创建匹配数量的管道运行器。接收到数据后，线程会将批处理传递给每个管道运行器进行处理。

在任何给定的时刻，五个流水线运行者可以分别处理一个批处理，因此该多线程管道一次最多可以处理五个批处理。当传入数据变慢时，管道运行器将处于空闲状态，并在数据流增加时立即可用。

### 多线程分区处理

默认情况下，SQL Server 2019 BDC多表使用者来源对满足分区处理要求的所有表执行多线程分区处理，并对所有其他表执行表处理。

要对表中的分区执行多线程处理，请在表配置中启用分区处理，然后指定分区大小和要使用的最大分区数。限制分区数量还限制了可用于处理表中数据的线程数量。

当您为无限分区配置一组表时，源创建的分区数量最多是管道线程数的两倍。例如，如果您有5个线程，则表最多可以有10个分区。

与多线程表处理类似，每个线程都从单个分区读取数据，并且每个分区一次最多只能有一个线程读取数据。

处理分区时，处理顺序取决于许多因素。有关完整描述，请参见[Processing Queue](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_czt_ql2_r1c)。

#### 分区处理要求

要对表执行多线程分区处理，该表必须满足以下要求：

- 单键或偏移列

  该表必须具有单个主键或用户定义的偏移量列。使用组合键对表执行多线程分区处理会产生错误并停止管道。

  如果表没有主键列，则可以使用“覆盖偏移量列”属性来指定要使用的有效偏移量列。强烈建议在offset列上使用升序索引，因为基础查询在此列上使用ORDER BY和不等号运算符。

- 数值数据类型

  要使用分区处理，主键或用户定义的偏移量列必须具有允许进行算术分区的数字数据类型。

  键或偏移量列必须是以下数据类型之一：基于整数：Integer，Smallint，Tinyint长基于：Bigint，日期，时间，时间戳。基于浮动的：浮动的，真实的基于双重：双重基于精度：十进制，数字

#### 多重偏移值处理

处理分区时，SQL Server 2019 BDC多表使用者来源可以处理具有相同偏移值的多个记录。例如，原点可以在`transaction_date`偏移列中处理具有相同时间戳的多个记录 。

**警告：**当处理多个具有相同偏移值的记录时，如果在原点处理一系列具有相同偏移值的记录时停止管道，则可以删除记录。

当原点正在处理具有相同偏移值的一系列记录时，停止管道时，原点会记录偏移量。然后，当您重新启动管道时，它从具有下一个逻辑偏移值的记录开始，跳过使用相同最后保存的偏移的所有未处理记录。

例如，假设您将datetime列指定为用户定义的偏移量列，并且表中的五个记录共享相同的datetime值。现在说您碰巧在处理第二条记录后停止管道。管道将datetime值存储为停止时的偏移量。当您重新启动管道时，处理将从下一个日期时间值开始，并跳过具有最后保存的偏移值的三个未处理的记录。

#### 尽力而为：处理不符合要求的表

要在表配置中处理可能不满足分区处理要求的表，可以在配置“多线程分区处理”模式属性时使用“打开（尽力而为”）选项。

选择尽力而为选项时，原始服务器将对所有满足分区处理要求的表执行多线程分区处理。源对包含多个键或偏移列的表执行多线程表处理。并且，如果启用了[非增量处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_xwr_bhm_nbb)，则原点还可以处理所有不包含键或偏移列的表。

## 非增量处理

您可以将SQL Server 2019 BDC多表使用者来源配置为对没有主键或用户定义的偏移量列的表执行非增量处理。默认情况下，原点执行增量处理，并且不处理没有键或偏移列的表。

您可以对“表”选项卡上的表配置中定义的表集启用非增量处理。

**注意：**对没有键或偏移列的表启用非增量处理时，您不能要求对表配置进行[多线程分区处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_gvy_yws_p1b)。也就是说，您不能在“多线程分区处理模式”属性设置为“开（必需）”的情况下运行管道。

使用开（尽力而为）或关来执行表格的非增量处理。无论选择哪个选项，都使用[单线程处理表](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_tz5_fw5_gz)，就像[多线程表处理一样](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_tz5_fw5_gz)。

启用非增量处理时，原始将按以下方式处理任何没有键或偏移列的表：

- 原点使用单个线程来处理表中的所有可用数据。

- 原点处理所有可用数据后，它会指出该表已作为偏移量进行处理。因此，如果在原点完成所有处理之后停止并重新启动管道，则原点不会重新处理表。

  如果要重新处理表中的数据，可以在重新启动管道之前重设源。这将重置原始处理的所有表的原始。

- 如果在原始服务器仍在处理可用数据时管道停止，则在管道重新启动时，原始服务器将重新处理整个表。发生这种情况是因为表没有键或偏移量列，无法跟踪进度。

例如，假设您将原点配置为使用五个线程，并处理一组表，其中包括没有键或偏移列的表。要处理此表中的数据，请启用“启用非增量负载”表配置属性。您还可以将“多线程分区处理模式”设置为“开”（“尽力而为”），以允许源服务器尽可能使用多线程分区处理，并在需要时允许非增量处理和多线程表处理。

启动管道时，源将一个线程分配给需要非增量处理的表。它使用多线程表处理来处理表数据，直到处理完所有数据为止。当线程完成所有可用数据的处理时，原点将其记录为偏移量的一部分，并且该线程可用于处理其他表中的数据。同时，在可能的情况下，其他四个线程使用多线程分区处理来处理其余表中的数据。

## 批次策略

您可以指定在处理数据时要使用的批处理策略。批处理策略的行为有所不同，具体取决于您使用的是多线程表处理还是多线程分区处理。该行为还可能受“来自结果集的批次”属性的影响。

### 处理所有可用行

根据原始处理的是完整表还是表中的分区，根据表批处理策略处理所有可用行略有不同。

- 多线程表处理

  当源对所有表执行多线程表处理时，每个线程都会从一个表创建多批数据，直到从该表中读取所有可用行。该线程针对从表创建的所有批处理运行一个SQL查询。然后，线程切换到下一个可用表，运行另一个SQL查询以读取该表中的所有可用行。例如，假设原点的批处理大小为100，并使用两个并发线程从四个表中读取，每个表包含1,000行。第一个线程运行一个SQL查询，以从table1创建10批每行100行的批处理，而第二个线程使用相同的策略从table2读取数据。完全读取table1和table2后，线程将切换到table3和table4并完成相同的过程。当第一个线程完成从table3的读取时，该线程切换回下一个可用表，以从上次保存的偏移量中读取所有可用数据。可以处理表的线程数受源的“线程数”属性限制。当要处理的表同时使用表处理和分区处理时，线程将按如下所述查询分区。有关表和分区如何在处理队列中旋转的详细信息，请参阅[Processing Queue](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_czt_ql2_r1c)。

- 多线程分区处理

  多线程分区处理与多线程表处理相似，不同之处在于它在分区级别上工作。每个线程从一个分区创建多批数据。它一次创建和处理的批次数基于“来自结果集的批次”属性。每个线程针对要从分区创建的批次运行一个SQL查询。然后，线程切换到下一个可用分区，并运行另一个SQL查询。例如，如果将“结果集的批次”属性设置为3，则线程将运行查询以从其处理的分区中创建3批数据。完成三个批次的处理后，就可以处理处理队列中的下一个分区或表。可以处理每个表的分区的线程数受源的“线程数”属性和“最大活动分区”表属性的限制。有关表和分区如何在处理队列中旋转的详细信息，请参阅[Processing Queue](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_czt_ql2_r1c)。

### 切换表

交换表批处理策略的差异很大，这取决于原始服务器是执行全表处理还是分区处理。一次创建和处理的批次数基于“来自结果集的批次”属性。

- 多线程表处理

  当原始服务器对所有表执行多线程表处理时，每个线程都会从一个表创建一组批处理，然后切换到下一个可用表以创建下一组批处理。该线程运行初始SQL查询，以从表中创建第一批批次。数据库将结果集中的其余行缓存在数据库中，以供同一线程再次访问，然后该线程切换到下一个可用表。在以下情况下有可用的表：该表没有打开的结果集缓存。在这种情况下，线程将运行初始SQL查询以创建第一个批处理，并将其余行缓存在数据库的结果集中。该表具有由同一线程创建的开放结果集缓存。在这种情况下，线程从数据库中的结果集缓存创建批处理，而不是运行另一个SQL查询。当表具有另一个线程创建的开放结果集缓存时，该表不可用。在关闭结果集之前，无法从该表读取其他线程。配置切换表策略时，请定义结果集缓存大小以及线程可以从结果集中创建的批处理数量。线程创建配置的批次数量后，可以从表中读取其他线程。**注意：**默认情况下，原点会指示数据库缓存无限数量的结果集。线程可以从该结果集中创建无限数量的批次。例如，假设一个来源的批处理大小为100，并使用两个并发线程从四个表中读取，每个表包含10,000行。您将结果集缓存大小设置为500，并将从结果集中读取的批处理数量设置为5。Thread1在table1上运行SQL查询，该查询返回所有10,000行。线程在读取前100行时会创建一个批处理。接下来的400行作为结果集缓存在数据库中。由于线程2同样处理table2，因此线程1切换到下一个可用的表table3，并重复相同的过程。从table3创建批处理后，线程1切换回table1，并从其先前在数据库中缓存的结果集中检索下一批行。在thread1之后，使用table1的结果集缓存创建了五个批次。然后，线程1切换到下一个可用表。从最后保存的偏移量开始，另一个线程运行SQL查询以从table1中读取其他行。当要处理的表同时使用表处理和分区处理时，线程将按如下所述查询分区。有关表和分区如何在处理队列中旋转的详细信息，请参阅[Processing Queue](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_czt_ql2_r1c)。

- 多线程分区处理

  多线程分区处理与多线程表处理类似，但有所不同-每个线程从一个表的一个分区创建一组批处理，然后将同一表中的所有分区移至处理队列的末尾。这允许原点切换到下一个可用表。缓存结果集和要从结果集中处理的批处理数量的行为相同，但是在分区级别。有关表和分区如何在处理队列中循环旋转的示例，请参阅[Processing Queue](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_czt_ql2_r1c)。

## 初始表订购策略

您可以定义原始位置用来读取表格的初始顺序。

定义以下初始表顺序策略之一：

- 没有

  按照数据库中列出的顺序读取表。

- 按字母顺序

  按字母顺序读取表。

- 参照约束

  根据表之间的依赖关系读取表。源首先读取父表，然后读取使用外键引用父表的子表。

  当要读取的表具有循环依赖性时，不能使用引用约束顺序。当源检测到周期性依赖关系时，管道将无法验证，并显示以下错误：`JDBC_68 Tables referring to each other in a cyclic fashion.`

  请注意，引用约束顺序可能导致流水线验证或初始化速度变慢，因为源必须在读取表之前对表进行排序。

原点仅将此表顺序用于表的初始读取。当线程切换回先前读取的表时，无论定义的顺序如何，它们都将从下一个可用表中读取。

## 处理队列

SQL Server 2019 BDC多表使用者来源会维护一个虚拟队列，以确定要从不同表中处理的数据。队列包括原始中定义的每个表。当要按分区处理表时，该表的多个分区会添加到队列中，受“表”选项卡上为每个表配置定义的“最大分区”属性的限制。

原点根据“每批策略”属性旋转并重新组织队列。然后，它使用“线程数”属性和“结果集的批次”属性中指定的线程处理队列中的数据。这三个属性是在JDBC选项卡上为原点定义的。

以下是一些有助于阐明队列工作方式的方案。

### 仅多线程表处理

在以下两种情况下，原始服务器仅执行多线程表处理：

- 多线程分区处理模式属性设置为关闭。
- “多线程分区处理模式”属性设置为“开”（“尽力而为”），并且没有表满足分区处理要求。

假设您有为表处理配置的表A，B，C和D。当启动管道时，源将所有表添加到队列中。如果已配置，则“初始表订购策略”高级属性可能会影响订单。假设我们没有参照约束，并使用字母顺序：

```
A  B  C  D
```

当线程变为可用时，它将处理队列中第一个表中的数据。批次数基于“来自结果集的批次”属性。表的处理取决于您如何定义“每批策略”属性：

- 处理表中所有可用的行

  使用此批处理策略，线程不会开始处理下一个表中的数据，直到为上一个表处理了所有可用数据为止。

  也就是说，表A保留在队列的最前面，直到处理完所有可用数据为止。然后，处理从表B开始。表A向后移动，保留在队列中，以防出现更多数据，如下所示：`B  C  D  A  `

- 切换表

  使用此批处理策略，队列的顺序保持不变，但是每个线程都会执行一个SQL查询，以基于“来自结果集的批处理”属性创建一组批处理。完成处理后，它将对队列中的下一个表执行相同的处理。

  线程从表A中获取一组批处理后，表A移至队列的后面：`B  C  D  A`下一个线程从表B中获取一组批处理。然后B移动到队列的后面：`C  D  A  B  `因此，在处理了4组批次之后，队列看起来就像开始时那样：`A  B  C  D`

### 仅多线程分区处理

在以下两种情况下，原始服务器仅执行多线程分区处理：

- 多线程分区处理模式属性设置为“开（必需）”。
- 多线程分区处理模式属性设置为开（尽力而为），并且所有表都满足分区处理要求。

假设您有表A，表B和表C，并且所有三个表都加载了许多要处理的数据。表A和B最多配置3个活动分区。而且由于表C的数据量最大，因此您可以允许无限数量的分区。同样，让我们使用按字母顺序排列的初始表顺序。

启动管道时，每个表都与最大活动分区数一起排队。对于表C，这意味着流水线的线程数增加了一倍。因此，如果我们为4个线程配置管道，则表C在任何给定时间可以在队列中最多包含8个分区。因此初始队列如下所示：

```
A1  A2  A3  B1  B2  B3  C1  C2  C3  C4  C5  C6  C7  C8 
```

分区将保留在队列中，直到起点确认分区中没有更多数据为止。当线程可用时，它将从队列中第一个表的第一个分区创建一组批处理。批次数基于“来自结果集的批次”属性。队列中表和分区的顺序取决于您如何定义“每批策略”，如下所示：

- 处理表中所有可用的行

  在处理分区时，此批处理策略保留队列的原始顺序，但随着每个线程处理一组批处理而在分区中轮换。**注意：**实际上，这意味着可以在完成上一个表之前处理后续表中的行，因为可用线程继续从队列中拾取分区。

  例如，四个线程开始处理队列中的前四个分区：A1，A2，A3和B1。这会将B2放在队列的最前面，为下一个可用线程做好准备。并且由于要处理的四个分区还有其他数据要处理，因此它们进入队列的后面。因此，在完全处理表A之前就开始处理表B数据。

  其余分区仍保持原始顺序，如下所示：`B2  B3  C1  C2  C3  C4  C5  C6  C7  C8  A1  A2  A3  B1`

  在四个线程处理完另外四批批次后，队列如下所示：`C3  C4  C5  C6  C7  C8  A1  A2  A3  B1  B2  B3  C1  C2`

- 切换表

  在处理分区时，每次线程处理分区中的一组批处理时，此批处理策略都会将同一表中的所有后续连续分区强制到队列的末尾。

  让我们从初始批处理订单开始：`A1  A2  A3  B1  B2  B3  C1  C2  C3  C4  C5  C6  C7  C8 `

  当线程处理来自A1的一组批处理时，它将表A的其余分区推到队列的末尾。这使下一张表（表B）排队等待处理。由于A1仍然包含数据，因此它占据了最后一个位置，如下所示：`B1  B2  B3  C1  C2  C3  C4  C5  C6  C7  C8  A2  A3  A1`当第二个线程处理来自B1的一组批处理时，其他B分区被发送回后台，并且由于B1仍然包含数据，因此它占据了最后一个位置，如下所示：`C1  C2  C3  C4  C5  C6  C7  C8  A2  A3  A1  B2  B3  B1`当第三个线程从C1获取一组批处理时，其余的C分区被推回后端，因此队列如下所示：`A2  A3  A1  B2  B3  B1  C2  C3  C4  C5  C6  C7  C8  C1`

### 多线程分区和表处理

在以下情况下，源服务器同时执行多线程分区处理和多线程表处理：

- “多线程分区处理模式”属性设置为“开”（“尽力而为”），某些表满足分区处理要求，而其他表则不满足。

处理完整表和分区表的混合时，队列的行为基本上与仅处理分区时相同，将完整表作为具有单个分区的分区表进行处理。让我们来看一看。

假设我们要处理的表A没有分区，表B最多配置3个分区，表C没有限制。如上例所示，管道有4个线程可使用，允许对表C进行8个分区。使用按字母顺序排列的初始表顺序，初始队列如下所示：

```
A  B1  B2  B3  C1  C2  C3  C4  C5  C6  C7  C8 
```

当线程可用时，它将处理队列中第一个表或分区中的一组批处理。批次数基于“来自结果集的批次”属性。队列的顺序取决于您如何定义“每批策略”，如下所示：

- 处理表中所有可用的行

  使用这种批处理策略，当每个线程从下一个表或分区中声明一组批处理时，队列将保持基本的初始顺序并轮换。未分区表A的处理类似于具有单个分区的表。请注意，未分区的表移到队列的最前面时不会完全处理。为此，请配置要处理的所有表而没有分区。或者，将“来自结果集的批次”属性设置为-1。当管道启动时，这四个线程将处理A表以及分区B1，B2和B3中的一组批处理。由于表和分区都仍然包含数据，因此它们将移动到队列末尾，如下所示：`C1  C2  C3  C4  C5  C6  C7  C8  A  B1  B2  B3 `每个线程完成处理后，它将从队列的最前面处理一组批处理。在4个线程中的每个线程进行另一组批处理之后，队列如下所示：`C5  C6  C7  C8  A  B1  B2  B3  C1  C2  C3  C4 `

- 切换表

  在处理表和分区时，此批处理策略会强制将同一表中的所有后续连续分区都移至队列末尾。它将未分区的表视为具有单个分区的表。结果，队列轮换是仅处理分区表的简化版本。因此，我们有以下初始顺序：`A  B1  B2  B3  C1  C2  C3  C4  C5  C6  C7  C8 `第一个线程处理表A中的一组批处理，并且由于没有相关的分区，因此它只到达队列末尾：`B1  B2  B3  C1  C2  C3  C4  C5  C6  C7  C8  A`第二个线程处理来自B1的一组批处理，将表B的其余分区推到队列的末尾，而B1在末尾登陆，因为它包含更多要处理的数据：`C1  C2  C3  C4  C5  C6  C7  C8  A  B2  B3  B1`第三个线程处理来自C1的一组批处理，将表C的其余分区推到末尾，而C1占用最后一个插槽：`A  B2  B3  B1  C2  C3  C4  C5  C6  C7  C8  C1`然后，第四个线程处理表A中的另一批批处理，并将A移动到队列的末尾：`B2  B3  B1  C2  C3  C4  C5  C6  C7  C8  C1  A`

## JDBC标头属性

SQL Server 2019 BDC多表使用者来源会产生JDBC记录标头属性，这些属性提供有关每个记录的其他信息，例如字段的原始数据类型或记录的源表。源从JDBC驱动程序接收这些详细信息。

您可以使用`record:attribute`或 `record:attributeOrDefault`函数来访问属性中的信息。有关使用记录标题属性的更多信息，请参见[使用标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_rd2_ghz_dz)。

JDBC记录头属性包含一个`jdbc`前缀，以区分JDBC属性和其他记录头属性。

源可以提供以下JDBC标头属性：

| JDBC标头属性             | 描述                                                         |
| :----------------------- | :----------------------------------------------------------- |
| jdbc.tables              | 提供记录中字段的逗号分隔的源表列表。                         |
| jdbc.partition           | 提供产生记录的分区的完整偏移键                               |
| jdbc.threadNumber        | 提供产生记录的线程号。                                       |
| jdbc。<列名称> .jdbcType | 提供记录中每个字段的原始SQL数据类型的数值。有关与数值对应的数据类型的列表，请参见[Java文档](https://docs.oracle.com/javase/8/docs/api/constant-values.html#java.sql.Types.ARRAY)。 |
| jdbc。<列名> .precision  | 提供所有数字和十进制字段的原始精度。                         |
| jdbc。<列名> .scale      | 提供所有数字和十进制字段的原始比例。                         |

## 事件产生

SQL Server 2019 BDC多表使用者来源 可产生可在事件流中使用的事件。启用事件生成后，原始将在完成对所有表的指定查询返回的数据的处理后，生成一个事件。完成处理从表返回的数据和从模式返回的数据时，它还会生成事件。

SQL Server 2019 BDC多表使用者事件可以任何逻辑方式使用。例如：

- 当原始完成处理可用数据时，使用Pipeline Finisher执行程序停止管道并将管道转换为Finished状态。

  重新启动由Pipeline Finisher执行程序停止的管道时，原点将从上次保存的偏移开始继续处理，除非您重置原点。

  有关示例，请参见[案例研究：停止管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_kff_ykv_lz)。

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储有关已完成查询的信息的目标。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

由SQL Server 2019 BDC多表使用者来源产生的事件记录具有以下与事件相关的记录标头属性：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型：no-more-data-当原点完成对所有表的查询返回的所有数据的处理时生成。schema-finished-当原点完成对模式中所有行的处理时生成。table-finished-当原点完成对表中所有行的处理时生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

SQL Server 2019 BDC多表使用者来源可产生以下事件记录：

- 没有更多数据

  当SQL Server 2019 BDC多表使用者来源完成对所有表的查询所返回的所有数据的处理后，会生成无数据事件记录。

  您可以将源配置为将no-more-data事件的生成延迟指定的秒数。您可以配置一个延迟，以确保在没有数据事件记录之前生成架构完成的事件或表完成的事件并将其传递到管道。

  要使用延迟，请配置“无数据事件生成延迟”属性。

  由起源生成的no-more-data事件记录的 `sdc.event.type`记录头属性设置为 `no-more-data`，并且不包括任何其他字段。

- 架构完成

  当SQL Server 2019 BDC多表使用者来源完成对架构中所有数据的处理后，会生成架构完成的事件记录。

  架构完成的事件记录具有以下附加字段：事件记录字段描述图式未返回任何剩余数据的架构。桌子模式中没有剩余数据的表的列表。

- 表完成

  当SQL Server 2019 BDC多表使用者起源完成对表中所有数据的处理后，会生成一个表完成的事件记录。

  表完成的事件记录具有以下附加字段：事件记录字段描述图式与表相关联的架构，没有剩余要处理的数据。表没有剩余数据要处理的表。

## 配置SQL Server 2019 BDC多表使用者来源

配置SQL Server 2019 BDC多表使用者来源以使用JDBC连接从SQL Server 2019 BDC读取数据。在管道中使用原点之前，请完成[所需的先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCBulk_Prereq)。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-EventGen) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **JDBC”**选项卡上，配置以下属性：

   | JDBC属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | SQL Server BDC JDBC连接字符串                                | 用于通过JDBC驱动程序连接到SQL Server 2019 BDC的字符串。连接字符串需要以下格式：`jdbc:sqlserver://:`默认情况下，该属性包含一种表达式语言函数：`jdbc:sqlserver://${sqlServerBDC:hostAndPort("master-svc-external")}`该函数在$ SDC_RESOURCES / sql-server-bdc-resources 文件夹中搜索sql-server-ip-and-port.json文件。在文件中，该函数使用键值对搜索JSON对象， `"serviceName":"master-svc-external"`并使用该对象中`ip`和`port`键所指定的IP地址和端口。如果您使用部署脚本安装了SQL Server 2019 Big Data Cluster阶段库，则可以使用默认字符串，因为该脚本会自动创建该功能所需的文件。如果不使用部署脚本，则可以编辑连接字符串以指定IP地址和端口，也可以使用默认字符串并使用以下JSON对象创建所需的文件：`{ "serviceName": "master-svc-external", "ip": "", "port":  } ` |
   | 数据库                                                       | 原始读取的SQL Server数据库的名称。若要从[外部表](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-ExternalTables)读取，请指定SQL Server 2019 BDC虚拟化外部表的SQL Server数据库。 |
   | 使用凭证                                                     | 在“凭据”选项卡上启用输入凭据。在JDBC连接字符串中不包括凭据时使用。 |
   | 每秒查询                                                     | 在所有分区和表中每秒运行的最大查询数。无限制地使用0。默认值是10。 |
   | [线程数](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#id_hdp_nwq_2kb) | 原点生成并用于多线程处理的线程数。在“高级”选项卡上将“最大池大小”属性配置为等于或大于此值。 |
   | [每批次策略](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-BatchStrategy) | 创建每批数据的策略：切换表-仅执行多线程表处理时，每个线程从一个表创建一批数据，然后切换到下一个可用表以创建下一批。配置交换表策略时，定义结果集缓存大小和结果集批次属性。处理表中的所有可用行-仅执行多线程表处理时，每个线程都会从一个表中创建多批数据，直到从该表中读取所有可用行。当执行多线程分区处理或表和分区处理的混合时，每个批处理策略的行为都更加复杂。有关详细信息，请参见[处理队列](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_czt_ql2_r1c)。 |
   | 最大批次大小（记录）                                         | 批处理中包含的最大记录数。                                   |
   | [结果集中的批次](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-BatchStrategy) | 从结果集中创建的批次数。在一个线程创建了此批次数量之后，数据库关闭结果集，然后另一个线程可以从同一表中读取。使用正整数设置对从结果集中创建的批次数量的限制。使用-1退出此属性。默认情况下，原产地从结果集中创建无限数量的批次，使结果集保持尽可能长的打开时间。 |
   | 结果集缓存大小                                               | 要缓存在数据库中的结果集数。使用正整数设置对缓存结果集数量的限制。使用-1退出此属性。默认情况下，原点缓存无限数量的结果集。 |
   | 最大布料大小（字符）                                         | Clob字段中要读取的最大字符数。较大的数据将被截断。           |
   | 最大Blob大小（字节）                                         | Blob字段中要读取的最大字节数。                               |
   | SQL错误重试次数                                              | 线程在收到SQL错误后尝试读取一批数据的次数。线程重试此次数后，线程将根据为源配置的错误处理来处理错误。用于处理瞬态网络或连接问题，这些问题阻止线程读取一批数据。默认值为0。 |
   | 数据时区                                                     | 用于评估基于日期时间的偏移列条件的时区。                     |
   | [没有数据事件生成延迟（秒）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-EventRecord) | 处理所有行后延迟无数据事件生成的秒数。用于在生成no-more-data事件之前留出时间让其他数据到达。 |
   | 引用字符                                                     | 引用字符以在查询中的模式，表和列名称周围使用。选择数据库在模式，表或列名称中允许使用小写，大小写或特殊字符的字符：无-查询中的名称周围不使用任何字符。例如： `select * from mySchema.myTable order by myOffsetColumn`。反引号-在查询中的名称周围使用反引号。例如： `select * from `mySchema`.`myTable` order by `myOffsetColumn``。双引号-在查询中的名称周围使用双引号。例如： `select * from "mySchema"."myTable" order by "myOffsetColumn"`。 |
   | 将时间戳转换为字符串                                         | 使原点能够将时间戳记写为字符串值而不是日期时间值。字符串保持存储在源数据库中的精度。在将时间戳写入不存储纳秒的Data Collector日期或时间数据类型时，原点会将距时间戳的任何纳秒存储在[field属性中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)。 |
   | 提取大小                                                     | 要获取并存储在Data Collector计算机上的内存中的最大行数。大小不能为零。默认值为1,000。有关配置访存大小的更多信息，请参见数据库文档。 |
   | 其他JDBC配置属性                                             | 要使用的其他JDBC配置属性。要添加属性，请单击 **添加**并定义JDBC属性名称和值。使用JDBC期望的属性名称和值。 |

3. 在“ **表”**选项卡上，定义一个或多个表配置。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击**添加**图标以定义另一个表配置。

   为每个[表配置配置](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-TableConfiguration)以下属性：

   | 表格属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [架构图](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-TableConfig_Patterns) | 该表配置中包含的模式名称的模式。使用SQL LIKE语法定义模式。输入 %以匹配所有模式。如果不输入任何值，则源仅从没有指定模式的表中读取。 |
   | [表名称模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-TableConfig_Patterns) | 要为此表配置读取的表名的模式。使用SQL LIKE语法定义模式。默认值为与模式中所有表匹配的通配符百分比（％）。 |
   | [表排除模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-TableConfig_Patterns) | 表名的模式，此表配置要从中排除这些表名。使用基于Java的正则表达式或regex定义模式。如果不需要排除任何表，请留空。 |
   | [模式排除模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-TableConfig_Patterns) | 对于此表配置，要读取的架构名称的模式。使用基于Java的正则表达式或regex定义模式。如果不需要排除任何模式，请保留为空。 |
   | [覆盖偏移列](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-TableConfig_Offset) | 确定是使用主键还是将其他列用作此表配置的偏移列。选择以覆盖主键并定义其他偏移列。清除以使用现有的主键作为偏移量列。要对具有多个键列或具有不受支持的数据类型的键列的表执行多线程分区处理，请选择此选项并指定有效的偏移量列。有关分区处理要求的更多信息，请参阅[分区处理要求](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-Partition_Requirements)。 |
   | 偏移列                                                       | 要使用的偏移列。最佳做法是，偏移列应为不包含空值的增量且唯一的列。强烈建议在此列上建立索引，因为基础查询在此列上使用ORDER BY和不等号运算符。 |
   | [初始偏移](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-TableConfig_Offset) | 管道启动时用于此表配置的偏移值。输入主键名称或偏移列名称和值。对于“日期时间”列，输入一个Long值。当定义多个偏移量列时，必须按照定义这些列的顺序为每个列定义一个初始偏移值。 |
   | [启用非增量负载](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_xwr_bhm_nbb) | 启用对不包含主键或偏移列的表的非增量处理。需要多线程分区处理时不要使用。 |
   | 多线程分区处理模式                                           | 确定原点如何执行多线程处理。选择以下选项之一：Off-源执行[多线程表处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_tz5_fw5_gz)。可用于对没有键或偏移列的表执行[非增量处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_xwr_bhm_nbb)。启用（尽力而为）-源对满足[分区处理要求的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-Partition_Requirements)所有表执行[多线程分区处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_gvy_yws_p1b)，并执行具有多个键或偏移列的多线程表分区表。可用于对没有键或偏移列的表执行[非增量处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#concept_xwr_bhm_nbb)。启用（必填）-源对所有表执行多线程分区处理。如果表配置包含不满足分区处理要求的表，则生成错误。 |
   | 分区大小                                                     | offset列中用于创建分区的值的范围。如果offset列是Datetime列，请提供分区大小（以毫秒为单位）。例如，要每小时创建一个分区，请输入3,600,000。使用多线程分区处理时可用。 |
   | 最大分区                                                     | 单个表一次要维护或处理的最大分区数。调整此值可以根据各种因素来提高吞吐量，这些因素包括运行Data Collector的计算机以及数据库服务器的类型和容量。最小正值为2，以确保原点可以通过分区前进。输入-1以使用默认行为，从而允许源为每个表创建的分区最多是源所使用的线程的两倍。最佳实践是从默认行为开始，并进行调整以调整性能。使用多线程分区处理时可用。 |
   | [偏移列条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-TableConfig_Offset) | 原始条件用来确定从何处开始读取此表配置的数据的其他条件。源将定义的条件添加到SQL查询的WHERE子句中。使用表达语言来定义条件。例如，您可以使用offset：column函数比较offset列的值。 |

4. 如果在**JDBC**选项卡上将源配置为与JDBC连接字符串分开输入JDBC凭据，则在“ **凭据”** 选项卡上配置以下属性：

   | 凭证属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 用户名   | JDBC连接的用户名。                                           |
   | 密码     | JDBC帐户的密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |

5. 在“ **高级”**选项卡上，可以选择配置高级属性。

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
   | 初始化查询                                                   | 在阶段连接到数据库后立即执行的SQL查询。用于根据需要设置数据库会话。 |
   | [初始表订购策略](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable-TableOrder) | 用于读取表的初始顺序：无-按表在数据库中列出的顺序读取表。按字母顺序-按字母顺序读取表。引用约束-根据表之间的依赖关系读取表。 |
   | 在未知类型上                                                 | 原点遇到数据类型不受支持的记录时要采取的措施：停止管道-完成对先前记录的处理后，停止管道。转换为字符串-将数据转换为字符串并继续处理。 |