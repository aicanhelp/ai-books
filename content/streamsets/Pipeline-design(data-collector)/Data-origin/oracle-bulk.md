# Oracle批量加载

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310173329825.png) 资料收集器

Oracle Bulkload源从多个Oracle表读取所有可用数据，然后停止管道。源可以使用多个线程来启用数据的并行处理。

使用Oracle Bulkload起源可以快速读取数据库表，例如当您要将表迁移到另一个数据库或系统时。您可以使用源从静态表或非静态表中读取。

在配置Oracle Bulkload源时，可以指定连接信息和要读取的表。您还可以配置高级属性，例如要使用的线程数，每个事务请求中要包括的批处理数，最大批处理大小以及在执行查询时是否考虑架构和表的大小写。

源可以为事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

在使用Oracle Bulkload源之前，必须完成[先决任务](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleBulk.html#concept_qz3_4yk_1hb)，包括安装Oracle Bulkload阶段库。Oracle Bulkload 阶段库是一个[Enterprise阶段库](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Installation/EnterpriseStageLibraries.html#concept_s1r_1gg_dhb)，仅供开发用途免费。有关购买用于生产的舞台库的信息，请[联系StreamSets](https://streamsets.com/contact-us/)。

**注意：** Oracle Bulkload原点在处理期间不保持偏移量。每次管道运行时，它都会处理所有可用数据。因此，即使管道在完成所有处理之前就停止了，在重新启动管道时它也会再次处理所有可用数据。

## 先决条件

在使用Oracle Bulkload源之前，请完成以下先决条件：

- [安装Oracle Bulkload阶段库](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleBulk.html#concept_yxw_5lf_1hb)。
- [安装Oracle JDBC驱动程序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleBulk.html#concept_zsr_31x_4y)。

### 安装Oracle Bulkload Stage库

在使用Oracle Bulkload源之前，必须安装Oracle Bulkload阶段库。

Oracle Bulkload 阶段库是一个Enterprise阶段库，仅供开发用途免费。有关购买用于生产的舞台库的信息，请[联系StreamSets](https://streamsets.com/contact-us/)。

您可以使用Package Manager来安装Enterprise阶段库以进行tarball Data Collector的安装，也可以将其作为定制阶段库来进行tarball，RPM或Cloudera Manager Data Collector的 安装。

#### 支持的版本

下表列出了要用于特定Data Collector 版本的Oracle Enterprise阶段库的版本：

| 数据收集器版本                  | 支持的舞台库版本                            |
| :------------------------------ | :------------------------------------------ |
| Data Collector 3.8.x及更高版本  | Oracle企业库1.1.0                           |
| 数据收集器 3.8.x，3.9.x和3.10.x | Oracle Enterprise Library 1.0.0（技术预览） |

#### 使用软件包管理器安装

您可以使用Package Manager在tarball Data Collector 安装中安装Oracle Enterprise阶段库。

1. 单击“程序包管理器”图标：![img](imgs/icon_PackageManager-20200310173329822.png)。

2. 在导航面板中，单击**Enterprise Stage Libraries**。

3. 选择**Oracle Enterprise Library**，然后单击 **Install**图标：![img](imgs/icon_InstallLib.png)。

4. 阅读StreamSets 订阅服务条款。如果您同意，请选中复选框，然后单击“ **安装”**。

   Data Collector将安装所选的舞台库。

5. 重新启动Data Collector。

#### 作为自定义舞台库安装

您可以在tarball Data Collector 安装中将Oracle Enterprise阶段库安装为自定义阶段库。

1. 要下载舞台库，请转到[StreamSets下载企业连接器](https://streamsets.com/download/enterprise-connectors/)页面。

   该网页显示按发布日期组织的Enterprise阶段库，并在页面顶部显示最新版本。

2. 单击您要下载的Enterprise阶段库名称和版本。

3. 在“ **下载企业连接器”**表单中，输入您的姓名和联系信息。

4. 阅读StreamSets订阅服务条款。如果您同意，请接受服务条款，然后单击“ **提交”**。

   舞台库下载。

5. 将Enterprise阶段库安装和管理为自定义阶段库。

   有关更多信息，请参见[Custom Stage Libraries](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/CustomStageLibraries.html#concept_pmc_jk1_1x)。

### 安装Oracle JDBC驱动程序

在使用Oracle Bulkload源之前，请为数据库安装Oracle JDBC驱动程序。在安装此驱动程序之前，原始服务器无法访问数据库。

1. 从Oracle网站下载Oracle JDBC驱动程序。

   **注意：**将XML数据写入Oracle需要安装Oracle Data Integrator Driver for XML。有关更多信息，请参见[Oracle文档](https://docs.oracle.com/cd/E29597_01/integrate.1111/e12644/xml_file.htm)。

2. 将驱动程序安装为Oracle Enterprise阶段库的外部库。

有关安装其他驱动程序的信息，请参阅 Data Collector 文档 中的“ [安装外部库](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/ExternalLibs.html%23concept_pdv_qlw_ft) ”。

**注意：** StreamSets已使用Oracle 11g版和Oracle ojdbc8.jar驱动程序测试了Oracle Bulkload起源。

## 静态和非静态表

您可以使用Oracle Bulkload源读取静态表（在管道运行时不会更改的表），或读取非静态表（在源运行时会更改的表）。

当使用源来读取可能随管道运行而变化的非静态表时，请将阶段配置为使用隔离级别。启用隔离级别后，源将使用可序列化的隔离级别，并且仅读取管道启动时提交的更改。在管道运行时，原点无法捕获对表所做的更改。在这种隔离级别上进行的Oracle一致性检查可以在具有许多并发事务的环境中显着降低吞吐量。

在使用原始数据从静态表迁移数据之后，可以使用包含[Oracle CDC客户端原始](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_rs5_hjj_tw)数据的单独管道来处理LogMiner重做日志或[JDBC Multitable Consumer原始](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MultiTableJDBCConsumer.html#concept_zp3_wnw_4y)数据中的CDC数据，以从表中连续读取数据。

使用源从非静态表迁移数据后，处理CDC数据无法捕获在迁移数据时所做的更改，从而导致数据丢失。因此，在使用源从非静态表迁移数据后，不建议处理CDC数据。

## 批量处理

与大多数Data Collector 源不同，Oracle Bulkload源仅执行批处理。处理完所有数据后，它停止管道，而不是像流传输管道一样等待其他数据。

Oracle Bulkload原点在处理期间不保持偏移量。每次您运行包含Oracle Bulkload源的管道时，该源都会处理指定表中的所有可用数据，然后停止管道。

**提示：**如果管道在处理完成之前停止，则可能需要在重新启动管道之前清除已处理记录的目标系统。

## 架构和表名称



配置Oracle Bulkload源时，可以指定要读取的表。要指定表，请定义架构和表名称模式。

您可以使用SQL通配符在一个模式内或跨多个模式定义一组表。

例如，假设您要处理`sales`以开头的模式中的所有表`SALES_`。您可以使用以下配置来指定要处理的表：

- 架构： `sales`
- 表名称模式： `SALES_%`

您可以配置源，以在执行查询时考虑方案名称和表名称的大小写。

## 多线程处理

Oracle Bulkload源执行并行处理，并允许创建多线程管道。

启动管道时，Oracle Bulkload源将检索表配置中定义的表列表。然后，原始服务器将基于“高级”选项卡上的“最大池大小”属性使用多个并发线程进行处理。

在管道运行时，Oracle在内存中创建数据块。Oracle Bulkload起源从数据块创建任务，并将其传递给可用的管道运行器。管道运行器基于为原点配置的最大批次大小，从任务创建批次进行处理。

管道运行器是无源管道实例 - 管道的实例，包括管道中的所有处理器，执行程序和目的地，并在源之后处理所有管道处理。 每个管道运行程序一次处理一个批处理，就像在单个线程上运行的管道一样。

当从Oracle块创建的任务小于所需的大小时（例如，它们小于最大批处理大小），可以配置源以合并小任务。使用“高级”选项卡上的“最小任务大小”属性来指定要包含在任务中的最小记录数。设置后，较小的任务将合并以实现更有效的处理。

多线程管道保留每个批处理中的记录顺序，就像单线程管道一样。但是，由于任务是由不同的管道运行程序处理的，因此无法确保将批处理写入目标的顺序。

有关多线程管道的更多信息，请参见《[多线程管道概述》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Multithreaded_Pipelines/MultithreadedPipelines.html#concept_zpp_2xc_py)。

## 事件产生



Oracle Bulkload源可以生成可在事件流中使用的事件。

Oracle Bulkload事件可以任何逻辑方式使用。例如：

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录



Oracle Bulkload原始生成的事件记录具有以下与事件相关的记录头属性：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型：table-finished-当原点完成对表中所有行的处理时生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

Oracle Bulkload源可以生成以下事件记录：

- 表完成

  当Oracle Bulkload源完成对表中所有数据的处理后，将生成一个表完成的事件记录。

  表完成的事件记录具有以下附加字段：事件记录字段描述图式与表关联的模式，没有剩余要处理的数据。表没有剩余数据要处理的表。记录数成功处理的记录数。

## 配置Oracle Bulkload Origin

配置Oracle Bulkload源以从一个或多个静态数据库表中读取数据。

在管道中使用原点之前，请完成[先决条件任务](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleBulk.html#concept_qz3_4yk_1hb)。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleBulk.html#concept_vpv_cx3_lhb) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **JDBC”**选项卡上，配置以下JDBC属性：

   | JDBC属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | JDBC连接字符串                                               | 用于连接数据库的连接字符串。**注意：**如果在连接字符串中包括JDBC凭据，请使用为源创建的用户帐户。 |
   | 使用凭证                                                     | 在“凭据”选项卡上启用输入凭据。在JDBC连接字符串中不包括凭据时使用。 |
   | [桌子](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleBulk.html#concept_wvc_rt3_lhb) | 表读取。为要读取的每个表或表集配置属性。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以定义另一个表配置。 |
   | 模式名称                                                     | 使用的架构。您可以输入架构名称或使用SQL通配符定义多个架构。  |
   | 表名                                                         | 定义要读取的表的表名模式。您可以输入表名或使用SQL通配符定义多个表。 |
   | 其他JDBC配置属性                                             | 要使用的其他JDBC配置属性。要添加属性，请单击 **添加**并定义JDBC属性名称和值。使用JDBC期望的属性名称和值。 |

3. 要与JDBC连接字符串分开输入JDBC凭据，请在“ **凭据”**选项卡上配置以下属性：

   | 凭证属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 用户名   | Oracle用户名。用户必须具有以下Oracle特权：在正在读取的表上执行SELECT。在SYS.DBA_EXTENTS系统表上执行SELECT。在SYS.USER_OBJECTS系统表上读取。 |
   | 密码     | 帐户密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |

4. 在“ **高级”**选项卡上，可以选择配置以下属性：

   | 先进物业                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 最大游泳池                                                   | 用于[多线程处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleBulk.html#concept_mxv_hyp_lhb)的线程数。 |
   | 每个请求的批次                                               | 每个请求中要从数据库中获取的批处理数。默认值为50。           |
   | 最大批次大小（记录）                                         | 一次处理的最大记录数。接受的值最高为Data Collector的最大批处理大小。默认值是1000 数据采集器默认设置为1000。 |
   | 最小空闲连接                                                 | 创建和维护的最小连接数。要定义固定连接池，请设置为与“最大池大小”相同的值。默认值为1。 |
   | 最小任务大小                                                 | 任务中允许的最小记录数。此属性确定是否应将较小的任务与较大的任务合并以进行处理。任务基于Oracle提供的数据块。使用-1选择退出使用此属性。 |
   | 停止SQL异常                                                  | 在遇到SQL异常时停止管道。                                    |
   | 区分大小写                                                   | 执行查询时考虑架构和表的情况。                               |
   | 空闲超时                                                     | 允许连接空闲的最长时间。在表达式中使用时间常数来定义时间增量。使用0以避免删除任何空闲连接。当输入的值接近或超过连接的最大生存期时，Data Collector将忽略空闲超时。默认值为10分钟，定义如下：`${10 * MINUTES}` |
   | [使用隔离级别](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleBulk.html#concept_zlv_fyq_s3b) | 隔离原始读取数据时对表所做的更改。仅当从非静态表中读取可能随着管道运行而改变的表时，才选择此选项。 |
   | 连接超时                                                     | 等待连接的最长时间。在表达式中使用时间常数来定义时间增量。默认值为30秒，定义如下：`${30 * SECONDS}` |