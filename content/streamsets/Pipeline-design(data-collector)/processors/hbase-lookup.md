# HBase查找

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310180731039.png) 资料收集器

HBase查找处理器在HBase中执行键值查找，并将查找值传递给字段。使用HBase查找可使用其他数据丰富记录。

例如，您可以配置处理器以使用department_ID字段作为键来在HBase中查找部门名称值，并将这些值传递给新的department_name输出字段。

在配置HBase查找处理器时，可以指定处理器是对批处理中的所有键执行批量查找，还是对记录中的每个键执行单独查找。您定义要在HBase中查找的键，并指定输出字段以将查找值写入其中。

您可以将处理器配置为在本地缓存键值对以提高性能。

您还可以指定HBase配置属性，包括ZooKeeper Quorum，父znode和表名。必要时，您可以启用Kerberos身份验证，指定HBase用户并添加其他HBase配置属性。

## 查找键

定义查找键时，可以指定要在HBase中查找的行以及列和时间戳（可选）。

下表描述了可用于定义查找键的每个查找参数：

| 查找参数 | 描述                                               |
| :------- | :------------------------------------------------- |
| 行       | 在HBase中查找的行。                                |
| 柱       | 要使用的行的列。该列必须使用以下格式：`:`          |
| 时间戳记 | 与行和列关联的时间戳。时间戳记必须是Datetime类型。 |

您可以使用以下任何查找参数组合来定义查找关键字：

- 行，列和时间戳

  定义所有查找参数时，HBase查找处理器将返回指定的行，列和时间戳的值。处理器将单个String值传递给输出字段。

- 行和列

  定义行和列查找参数时，HBase查找处理器将返回具有最新时间戳的指定行和列的值。处理器将单个String值传递给输出字段。

- 行和时间戳

  定义行和时间戳查找参数时，HBase查找处理器将在具有指定时间戳的所有列中查找该行的所有值。处理器传递一个String值映射，其中包含HBase列系列，限定符和指定行的值。

  例如，如果该行存在于具有指定时间戳记的三列中，则处理器以以下格式返回字符串值的映射：`/:  /:  /: `

- 行

  当仅定义行查找参数时，HBase查找处理器将在具有最新时间戳的所有列中查找该行的所有值。处理器传递一个String值映射，其中包含HBase列系列，限定符和指定行的值。

  例如，如果该行存在三列，则处理器以以下格式返回字符串值的映射：`/:  /:  /: `

## 查找缓存

为了提高管道性能，可以将HBase查找处理器配置为本地缓存从HBase返回的键值对。

处理器缓存键值对，直到缓存达到最大大小或到期时间。当达到第一个限制时，处理器从缓存中逐出键值对。

您可以配置以下方式从缓存中逐出键值对：

- 基于规模的驱逐

  配置处理器缓存的键/值对的最大数量。当达到最大数量时，处理器将从高速缓存中逐出最旧的键值对。

- 基于时间的驱逐

  配置键值对可以保留在缓存中而不被写入或访问的时间。当到达到期时间时，处理器从高速缓存中逐出密钥。驱逐策略确定处理器是否测量自上次写入值或自上次访问值以来的到期时间。

  例如，您将逐出策略设置为在上次访问后到期，并将到期时间设置为60秒。处理器在60秒内未访问键值对之后，处理器会将其从缓存中逐出。

当您停止管道时，处理器将清除缓存。

## Kerberos身份验证

您可以使用Kerberos身份验证连接到HBase。使用Kerberos身份验证时，Data Collector使用Kerberos主体和密钥表连接到HBase。默认情况下，Data Collector使用启动它的用户帐户进行连接。

Kerberos主体和密钥表在Data Collector 配置文件中定义`$SDC_CONF/sdc.properties`。要使用Kerberos身份验证，请在数据收集器 配置文件中配置所有Kerberos属性。

有关为Data Collector启用Kerberos身份验证的详细信息，请参阅Data Collector文档中的[Kerberos身份验证](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_hnm_n4l_xs)。

## 使用HBase用户

Data Collector 可以使用当前登录的Data Collector用户或在 处理器中配置的用户在HBase中查找数据。

可以设置需要使用当前登录的Data Collector用户的Data Collector配置属性 。如果未设置此属性，则可以在源中指定一个用户。有关Hadoop模拟和Data Collector属性的更多信息，请参阅Data Collector文档中的[Hadoop Impersonation Mode](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_pmr_sy5_nz)。

请注意，处理器使用其他用户帐户连接到HDFS。默认情况下，Data Collector使用启动它的用户帐户连接到外部系统。使用Kerberos时，Data Collector使用Kerberos主体。

要将处理器中的用户配置为在HBase中查找数据，请执行以下任务：

1. 在HBase上，将用户配置为代理用户，并授权该用户模拟HBase用户。

   有关更多信息，请参见HBase文档。

2. 在HBase查找处理器中，输入HBase用户名。

## HDFS属性和配置文件

您可以将HBase查找处理器配置为使用各个HDFS属性或HDFS配置文件：

- HBase配置文件

  您可以将以下HDFS配置文件与HBase配置文件一起使用：hbase-site.xml

  要使用HDFS配置文件：将文件或指向文件的符号链接存储在Data Collector资源目录中。在HBase查找处理器中，指定文件的位置。**注意：**对于Cloudera Manager安装，Data Collector会自动创建一个名为的文件的符号链接 `hbase-conf`。输入 `hbase-conf`文件在HBase查找处理器中的位置。

- 个别属性

  您可以在HBase查找处理器中配置各个HBase属性。要添加HBase属性，请指定确切的属性名称和值。HBase查找处理器不验证属性名称或值。**注意：**各个属性会覆盖HBase配置文件中定义的属性。

## 配置HBase查找

配置HBase查找处理器，以在HBase中执行键值查找。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **查找”**选项卡上，配置以下属性：

   | 查找属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 模式                                                         | 用于执行查找的模式：每批次-对批次中的所有密钥执行批量查找。处理器对每个批次执行一次查找。每个记录中的每个键-对每个记录中的每个键执行单独的查找。如果配置多个键表达式，则处理器将为每个记录执行多个查找。默认值为“每批”。 |
   | 行表达式 [![img](imgs/icon_moreInfo-20200310180731042.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/HBaseLookup.html#concept_txl_x4s_bw) | 在HBase中查找行。输入行名或输入定义该行的表达式。例如，输入以下表达式以将department_id字段中的数据用作行：`${record:value('/department_id')}` |
   | 列表达                                                       | 查找的可选列族和行的限定符。输入列名或输入定义该列的表达式。列名必须使用以下格式：`:`如果为空，则处理器为每一列返回该行的值。 |
   | 时间戳记表达                                                 | 查找行和列的可选时间戳记。输入具有Datetime类型的值或计算为Datetime类型的表达式。如果为空，则处理器返回带有最近时间戳记的值。 |
   | 输出场                                                       | 记录中要传递查找值的字段名称。您可以指定现有字段或新字段。如果该字段不存在，则HBase查找将创建该字段。 |
   | 启用本地缓存 [[![img](imgs/icon_moreInfo-20200310180731042.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/HBaseLookup.html#concept_xvz_zjy_bw) | 指定是否在本地缓存返回的键值对。                             |
   | 缓存的最大条目数                                             | 要缓存的键/值对的最大数量。当达到最大数量时，处理器将从高速缓存中逐出最旧的键值对。默认值为-1，表示无限制。 |
   | 驱逐政策类型                                                 | 过期时间过后，用于从本地缓存中逐出键/值对的策略：上次访问后过期-计算自读取或写入最后一次访问键值对以来的过期时间。上次写入后过期-测量自创建键值对以来或自上次替换值以来的过期时间。 |
   | 到期时间                                                     | 键值对可以保留在本地缓存中而不被访问或写入的时间。默认值为1秒。 |
   | 时间单位                                                     | 到期时间的时间单位。默认值为秒。                             |

3. 在“ **HBase”**选项卡上，配置以下属性：

   | HBase属性                                                    | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | ZooKeeper法定人数                                            | ZooKeeper仲裁中服务器的逗号分隔列表。使用以下格式：`..com`要确保连接，请输入其他代理URI。 |
   | ZooKeeper客户端端口                                          | 客户端使用的端口号连接到ZooKeeper服务器。                    |
   | ZooKeeper父Znode                                             | 包含HBase群集使用的所有znode的根节点。                       |
   | 表名                                                         | 要使用的HBase表的名称。输入表名或名称空间和表名，如下所示：<namespace>。<tablename>。如果不输入表名，则HBase使用默认名称空间。 |
   | Kerberos身份验证[![img](imgs/icon_moreInfo-20200310180731042.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/HBaseLookup.html#concept_nqg_lcy_bw) | 使用Kerberos凭据连接到HBase。选中后，将使用Data Collector配置文件中 定义的Kerberos主体和密钥表`$SDC_CONF/sdc.properties`。 |
   | HBase用户[![img](imgs/icon_moreInfo-20200310180731042.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/HBaseLookup.html#concept_bzv_cdy_bw) | 用于从HBase查找数据的HBase用户。使用此属性时，请确保正确配置了HBase。未配置时，管道将使用当前登录的Data Collector用户。将Data Collector配置为使用当前登录的Data Collector用户时，不可配置。有关更多信息，请参阅Data Collector 文档 中的[Hadoop模拟模式](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_pmr_sy5_nz)。 |
   | HBase配置目录[![img](imgs/icon_moreInfo-20200310180731042.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/HBaseLookup.html#concept_nks_rdy_bw) | HDFS配置文件的位置。对于Cloudera Manager安装，请输入`hbase-conf`。对于所有其他安装，请使用Data Collector资源目录中的目录或符号链接。您可以在HBase中使用以下文件：hbase-site.xml**注意：**配置文件中的属性被阶段中定义的单个属性覆盖。 |
   | HBase配置                                                    | 要使用的其他HBase配置属性。要添加属性，请单击**添加**并定义属性名称和值。使用HBase期望的属性名称和值。 |