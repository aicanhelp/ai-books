# 库杜

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310201630090.png) 资料收集器

Kudu目标将数据写入Kudu群集。

配置Kudu目标时，可以为一个或多个Kudu主数据库指定连接信息，定义要使用的表，并可以选择定义字段映射。默认情况下，目标将字段数据写入具有匹配名称的列。您还可以启用Kerberos身份验证。

Kudu目标可以使用在`sdc.operation.type`记录头属性中定义的CRUD操作 来写入数据。您可以为没有标题属性或值的记录定义默认操作。您还可以配置如何处理不受支持的操作的记录。 有关Data Collector更改数据处理以及启用CDC的来源的列表的信息，请参见[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

如果目标从某些原始系统接收到更改数据捕获日志，则必须选择更改日志的格式。

您还可以配置外部一致性模式，操作超时以及要使用的最大工作线程数。

## 定义CRUD操作

Kudu目标可以插入，更新，删除或追加数据。目标根据CRUD操作标头属性或与操作相关的阶段属性中定义的CRUD操作写入记录。

您可以通过以下方式定义CRUD操作：

- CRUD记录标题属性

  您可以在CRUD操作记录标题属性中定义CRUD操作。目标在`sdc.operation.type`记录头属性中寻找要使用的CRUD操作 。

  该属性可以包含以下数值之一：INSERT为12个代表删除3更新4个用于UPSERT

  如果您的管道包括启用CRUD的原始数据源，该原始数据元处理已更改的数据，则目标位置仅从`sdc.operation.type`原始数据源生成的标头属性中读取操作类型 。如果管道使用非CDC来源，则可以使用表达式评估器或脚本处理器来定义记录头属性。有关Data Collector 更改的数据处理以及启用CDC的来源的列表的详细信息 ，请参阅 [处理更改的数据。](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

- 操作阶段属性

  您在目标属性中定义默认操作。`sdc.operation.type`未设置记录头属性时，目标使用默认操作 。

  您还可以定义如何使用`sdc.operation.type`header属性中定义的不受支持的操作来处理记录 。目标可以丢弃它们，将它们发送给错误，或使用默认操作。

## Kudu数据类型

Kudu目标将Data Collector 数据类型转换为以下兼容的Kudu数据类型：

| 数据收集器数据类型 | Kudu数据类型                                                 |
| :----------------- | :----------------------------------------------------------- |
| 布尔型             | 布尔                                                         |
| 字节               | 诠释8                                                        |
| 字节数组           | 二元                                                         |
| 小数               | 十进制。在Kudu 1.7版和更高版本中可用。如果使用早期版本的Kudu，请配置管道以将Decimal数据类型转换为其他Kudu数据类型。 |
| 双                 | 双                                                           |
| 浮动               | 浮动                                                         |
| 整数               | 32位                                                         |
| 长                 | Int64或Unixtime_micros。目标根据映射的Kudu列确定要使用的数据类型。该数据采集 Long数据类型存储毫秒值。Kudu Unixtime_micros数据类型存储微秒值。当转换为Unixtime_micros数据类型时，目标会将字段值乘以1000，以将值转换为微秒。 |
| 短                 | 16位                                                         |
| 串                 | 串                                                           |

目标无法转换以下Data Collector 数据类型。在管道中的较早位置使用字段类型转换器处理器将这些数据收集器 数据类型转换为与Kudu兼容的数据类型：

- 字符
- 日期
- 约会时间
- 清单
- 列表图
- 地图
- 时间

## Kerberos身份验证



您可以使用Kerberos身份验证连接到Kudu群集。使用Kerberos身份验证时，Data Collector 使用Kerberos主体和keytab连接到Kudu。默认情况下，Data Collector 使用启动它的用户帐户进行连接。

Kerberos主体和密钥表在Data Collector 配置文件中定义`$SDC_CONF/sdc.properties`。要使用Kerberos身份验证，请在数据收集器 配置文件中配置所有Kerberos属性。

有关为Data Collector启用Kerberos身份验证的详细信息，请参阅Data Collector文档中的[Kerberos身份验证](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_hnm_n4l_xs)。

## 配置Kudu目标

配置Kudu目标以写入Kudu群集。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **Kudu”**选项卡上，配置以下属性：

   | 酷渡地产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 苦杜大师                                                     | Kudu主设备使用的连接信息列表，以逗号分隔。使用以下格式：`:`  |
   | 表名                                                         | 要写入的表。输入以下内容之一：现有Kudu表的名称。如果该表不存在，则管道无法启动。该表达式的计算结果为现有Kudu表的名称。例如，如果表名称存储在“ tableName”记录属性中，请输入以下表达式：`${record:attribute('tableName')}`如果该表不存在，则将这些记录视为错误记录。**注意：**使用由Impala创建的表时，请使用前缀，`impala::` 后跟数据库名称和表名称。例如：`impala::. ` |
   | 字段到列的映射                                               | 用于定义记录字段和Kudu列之间的特定映射。默认情况下，目标将字段数据写入具有匹配名称的列。 |
   | [默认操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Kudu.html#concept_dvg_vvj_wx) | 如果`sdc.operation.type`未设置记录头属性，则执行默认的CRUD操作。 |
   | 更改日志格式                                                 | 如果传入数据是从以下源系统读取的变更数据捕获日志，请选择源系统，以便目标可以确定日志的格式：Microsoft SQL服务器Oracle CDC客户端MySQL二进制日志MongoDB Oplog对于任何其他源数据，请设置为“无”。 |

3. （可选）单击“ **高级”**选项卡并配置以下属性：

   | 先进物业                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 外部一致性                                                   | 用于写入Kudu的外部一致性模式：客户端传播-确保来自单个客户端的写入自动在外部保持一致。提交等待-一个实验性的外部一致性模型，可以紧密同步集群中所有计算机上的时钟。有关更多信息，请参见Kudu文档。 |
   | 突变缓冲空间                                                 | Kudu用于在记录中写入单批数据的缓冲区的大小。应等于或大于从管道传递的批处理中的记录数。默认值为1000条记录。 |
   | 最大工作线程数                                               | 用于执行阶段处理的最大线程数。默认值是Kudu默认值–是Data Collector计算机上可用内核数的两倍。使用此属性可以限制可以使用的线程数。要使用默认值，请保留空白或输入0。 |
   | 操作超时（毫秒）                                             | 允许进行写操作等的毫秒数。默认值为10000，即10秒。            |
   | 管理员操作超时（毫秒）                                       | 允许进行管理员类型操作（例如打开表或获取表模式）的毫秒数。默认值为30000，即30秒。 |
   | [不支持的操作处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Kudu.html#concept_dvg_vvj_wx) | `sdc.operation.type`不支持在记录头属性中定义的CRUD操作类型时采取的措施 ：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。使用默认操作-使用默认操作将记录写入目标系统。 |