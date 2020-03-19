# MapR DB CDC

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310172548442.png) 资料收集器

MapR DB CDC源从已写入MapR流的MapR DB读取更改的数据。源可以使用多个线程来启用数据的并行处理。

您可以使用此来源执行数据库复制。您可以使用具有MapR DB JSON来源的单独管道来读取现有数据。然后使用MapR DB CDC原点启动管道，以处理后续更改。

配置MapR DB CDC原始时，将配置要处理的MapR Streams使用者组名称和主题，以及要使用的线程数。您可以根据需要指定其他MapR流和受支持的Kafka配置属性。

MapR DB CDC原点在记录标题属性中包括CRUD操作类型，因此生成的记录可以由启用CRUD的目标轻松处理。有关Data Collector 更改的数据处理的概述以及启用CRUD的目标的列表，请参阅[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

**提示：** Data Collector 提供了多个MapR来源来满足不同的需求。有关快速比较表以帮助您选择合适的表，请参阅[比较MapR起源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ip2_szg_qbb)。

在管道中使用任何MapR阶段之前，必须执行其他步骤以使Data Collector能够处理MapR数据。有关更多信息，请参阅Data Collector 文档中的 [MapR先决条件](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Installation/MapR-Prerequisites.html%23concept_jgs_qpg_2v)。

## 多线程处理

MapR DB CDC源执行并行处理，并允许创建多线程管道。原点从已写入MapR流的MapR DB中读取已更改的数据。

MapR DB CDC起源基于“线程数”属性使用多个并发线程。MapR Streams在组中的所有使用者之间平均分配分区。

执行多线程处理时，MapR DB CDC来源检查要处理的主题列表并创建指定数量的线程。每个线程都连接到MapR Streams，并从MapR Streams分配的分区创建一批数据。然后，它将批次传递到可用的管道运行器。

管道运行器是无源管道实例 - 管道的实例，包括管道中的所有处理器，执行程序和目的地，并在源之后处理所有管道处理。 每个管道运行程序一次处理一个批处理，就像在单个线程上运行的管道一样。当数据流减慢时，管道运行器会闲置等待，直到需要它们为止，并定期生成一个空批。您可以配置“运行者空闲时间”管道属性来指定间隔或选择退出空批次生成。

多线程管道保留每个批处理中的记录顺序，就像单线程管道一样。但是由于批处理 是由不同的流水线处理程序处理的，因此无法确保将批处理写入目的地的顺序。

例如，假设您将“线程数”属性设置为5。启动管道时，源将创建五个线程，而数据收集器将 创建匹配数量的管道运行器。线程被分配给MapR Streams定义的不同分区。接收到数据后，原点将批处理传递给每个管道运行器进行处理。

在任何给定的时刻，五个流水线运行者可以分别处理一个批处理，因此该多线程管道一次最多可以处理五个批处理。当传入数据变慢时，管道运行器将处于空闲状态，并在数据流增加时立即可用。

有关多线程管道的更多信息，请参见《[多线程管道概述》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Multithreaded_Pipelines/MultithreadedPipelines.html#concept_zpp_2xc_py)。有关MapR Streams的更多信息，请参见MapR Streams文档。

## 处理_id字段

所有MapR DB更改的数据都包含_id字段。MapR DB CDC原点包括_id字段作为生成的记录中的字段。如果需要，您可以在管道中使用Field Remover处理器删除_id字段。

_id字段可以包含字符串或二进制数据。MapR DB CDC原点可以处理包含字符串或二进制数据的数据。原点无法读取包含字符串和二进制数据组合的_id字段。

当传入数据包括字符串_id字段时，源将在记录中将_id字段创建为字符串。当传入数据包括二进制_id字段时，原始数据将数据转换为String，然后将该字段包括在记录中。

**注意：**二进制_id字段必须包含要正确处理的数字数据。

## CRUD操作和CDC标头属性

MapR DB CDC原点在sdc.operation.type记录标题属性中包括CRUD操作类型。

如果您在诸如JDBC Producer或Elasticsearch之类的管道中使用启用CRUD的目标，则该目标可以在写入目标系统时使用操作类型。必要时，可以使用表达式评估器或脚本处理器来处理`sdc.operation.type`header属性中的值 。有关Data Collector更改的数据处理的概述以及启用CRUD的目标的列表，请参阅[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

MapR DB CDC来源在其他记录头属性中包含其他CDC信息。下表描述了MapR DB CDC源生成的记录头属性：

| 记录标题属性          | 描述                                                         |
| :-------------------- | :----------------------------------------------------------- |
| mapr.op.timestamp     | 与更改关联的时间戳。                                         |
| mapr.server.timestamp | 与更改关联的服务器时间戳。                                   |
| 划分                  | 数据起源的分区。                                             |
| 抵销                  | 数据的偏移量。                                               |
| sdc.operation.type    | 与记录关联的CRUD操作类型。sdc.operation.type记录头属性可以包含以下值：INSERT为12个代表删除3更新 |
| 话题                  | 数据来源的主题。                                             |

## 其他特性

您可以将自定义配置属性添加到MapR DB CDC源。您可以使用MapR Streams支持的任何MapR或Kafka属性。有关更多信息，请参见MapR Streams文档。

添加配置属性时，输入确切的属性名称和值。MapR DB CDC来源不验证属性名称或值。

**注意：** MapR DB CDC来源使用以下MapR Streams配置属性。原点会忽略这些属性的用户定义值：

- 自动提交间隔
- enable.auto.commit
- group.id
- 最大投票记录

## 配置MapR DB CDC原始

配置MapR DB CDC原点以处理已写入MapR流的MapR DB更改的数据。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **MapR DB CDC”**选项卡上，配置以下属性：

   | MapR DB CDC属性                                              | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 消费群体                                                     | 数据收集器所属的使用者组。                                   |
   | 主题清单                                                     | 要阅读的主题。在左侧，输入流名称和主题，如下所示：`/:`例如， /data/sales:changelog。在右侧，输入表的名称，如下所示：`/`例如：/west。单击 **添加**以添加其他主题。 |
   | 线程数                                                       | 原点生成并用于[多线程处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRdbCDC.html#concept_cwt_r3h_qbb)的[线程数](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRdbCDC.html#concept_cwt_r3h_qbb)。 |
   | 最大批次大小（记录）                                         | 一次处理的最大记录数。接受的值最高为Data Collector的最大批处理大小。默认值是1000 数据采集器默认设置为1000。 |
   | [批处理等待时间（毫秒）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ypd_vgr_5q) | 发送部分或空批次之前要等待的毫秒数。                         |
   | MapR流配置 [![img](imgs/icon_moreInfo-20200310172549006.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRdbCDC.html#concept_ccr_d4h_qbb) | 要使用的其他配置属性。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以添加属性。使用MapR Streams期望的属性名称和值。您可以使用MapR Streams属性和MapR Streams支持的Kafka属性集。 |