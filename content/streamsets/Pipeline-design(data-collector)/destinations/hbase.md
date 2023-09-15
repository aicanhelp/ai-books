# HBase的

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310184426757.png) 资料收集器

HBase目标将数据写入HBase群集。目标可以将数据作为文本，二进制数据或JSON字符串写入现有的HBase表。您可以为写入HBase的每一列定义数据格式。

配置HBase目标时，可以指定HBase配置属性，包括ZooKeeper Quorum，父znode和现有表名。您为表指定行键，然后将字段从管道映射到HBase列。

必要时，可以启用Kerberos身份验证并指定HBase用户。您还可以配置时间基准并添加其他HBase配置属性。

## 场映射

配置HBase目标时，会将字段从记录映射到HBase列。

您可以通过以下方式将字段映射到列：

- 显式字段映射

  默认情况下，HBase目标使用显式字段映射。您从记录中选择字段以映射到HBase列。使用以下格式指定HBase列：`:`。然后，您为HBase中的列定义存储类型。

  使用显式字段映射时，可以配置目标以忽略缺少的字段路径。如果目标遇到记录中不存在的映射字段路径，则目标将忽略缺少的字段路径，并将记录中的其余字段写入HBase。

- 隐式字段映射

  将HBase目标配置为使用隐式字段映射时，目标将根据匹配的字段名称写入数据。当字段路径使用以下格式时，可以使用隐式字段映射：`:`

  例如，如果字段路径是“ cf：a”，则目的地可以隐式将字段映射到具有列系列“ cf”和限定符“ a”的HBase表。

  使用隐式字段映射时，可以将目标配置为忽略无效的列。如果目标遇到无法映射到有效HBase列的字段路径，则目标将忽略无效列，并将记录中的其余字段写入HBase。

- 隐式和显式字段映射

  您可以将目标配置为使用隐式字段映射，然后可以通过为特定字段定义显式映射来覆盖映射。

  例如，一条记录可能包含一些使用该 `:`格式的字段路径和其他不使用所需格式的字段路径。您可以为不使用所需格式的字段路径添加显式字段映射。或者，您可以为使用所需格式但需要写入不同列的字段使用显式字段映射。

## Kerberos身份验证

您可以使用Kerberos身份验证连接到HBase。使用Kerberos身份验证时，Data Collector使用Kerberos主体和密钥表连接到HBase。默认情况下，Data Collector使用启动它的用户帐户进行连接。

Kerberos主体和密钥表在Data Collector 配置文件中定义 `$SDC_CONF/sdc.properties`。要使用Kerberos身份验证，请在数据收集器配置文件中配置所有Kerberos属性。

有关为Data Collector启用Kerberos身份验证的详细信息，请参阅Data Collector文档中的[Kerberos身份验证](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_hnm_n4l_xs)。

## 使用HBase用户

Data Collector 可以使用当前登录的Data Collector用户或在 目标中配置的用户来写入HBase。

可以设置需要使用当前登录的Data Collector用户的Data Collector配置属性 。如果未设置此属性，则可以在源中指定一个用户。有关Hadoop模拟和Data Collector属性的更多信息，请参阅Data Collector文档中的[Hadoop Impersonation Mode](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_pmr_sy5_nz)。

请注意，目标使用其他用户帐户连接到HBase。默认情况下，Data Collector使用启动它的用户帐户连接到外部系统。使用Kerberos时，Data Collector使用Kerberos主体。

要将目标中的用户配置为写入HBase，请执行以下任务：

1. 在HBase上，将用户配置为代理用户，并授权该用户模拟HBase用户。

   有关更多信息，请参见HBase文档。

2. 在HBase目标中，输入HBase用户名。

## 时间基础

时间基准确定为写入HBase的每一列添加的时间戳值。

您可以使用以下时间作为时间基础：

- 处理时间

  当您将处理时间用作时间基准时，目标将使用Data Collector的处理时间作为时间戳值。每批次计算一次处理时间。

  要将处理时间用作时间基准，请使用以下表达式： `${time:now()}`。

- 记录时间

  当您使用与记录关联的时间作为时间基准时，您可以在记录中指定日期或日期时间字段。目标使用字段值作为时间戳值。

  要使用与记录关联的时间，请使用一个表达式，该表达式调用一个字段并解析为日期或日期时间值，例如 `${record:value("/Timestamp")}`。

- 系统时间

  将“时间基础”字段保留为空时，目标位置将使用在将列写入HBase时由HBase自动生成的时间戳值。

  这是默认的时间基准。

## HDFS属性和配置文件

您可以将HBase目标配置为使用单个HDFS属性或HDFS配置文件：

- HBase配置文件

  您可以将以下HDFS配置文件与HBase配置文件一起使用：hbase-site.xml

  要使用HDFS配置文件：将文件或指向文件的符号链接存储在Data Collector资源目录中。在HBase目标中，指定文件的位置。**注意：**对于Cloudera Manager安装，Data Collector会自动创建一个名为的文件的符号链接 `hbase-conf`。输入 `hbase-conf`文件在HBase目标中的位置。

- 个别属性

  您可以在HBase目标中配置各个HBase属性。要添加HBase属性，请指定确切的属性名称和值。HBase目标不验证属性名称或值。**注意：**各个属性会覆盖HBase配置文件中定义的属性。

## 配置HBase目标

配置HBase目标以将数据写入HBase。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **HBase”**选项卡上，配置以下属性：

   | HBase属性                                                    | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | ZooKeeper法定人数                                            | ZooKeeper仲裁中服务器的逗号分隔列表。使用以下格式：`..com`要确保连接，请输入其他代理URI。 |
   | ZooKeeper客户端端口                                          | 客户端使用的端口号连接到ZooKeeper服务器。                    |
   | ZooKeeper父Znode                                             | 包含HBase群集使用的所有znode的根节点。                       |
   | 表名                                                         | 要使用的HBase表的名称。输入表名或名称空间和表名，如下所示：<namespace>。<tablename>。如果不输入表名，则HBase使用默认名称空间。 |
   | 行键                                                         | 表的行键。                                                   |
   | 存储类型                                                     | 行键的存储类型。                                             |
   | 领域 [![img](imgs/icon_moreInfo-20200310184426771.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/HBase.html#concept_vn5_cr5_4v) | 将字段从记录显式映射到HBase列，然后在HBase中定义该列的存储类型。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以创建其他显式字段映射。 |
   | 忽略缺少的字段路径                                           | 忽略缺少的字段路径。在定义显式字段映射时使用。如果选中该选项，并且目标遇到记录中不存在的映射字段路径，则目标将忽略缺少的字段路径，并将记录中的其余字段写入HBase。如果清除，并且目的地遇到记录中不存在的映射字段路径，则将记录发送到阶段以进行错误处理。 |
   | 隐式字段映射[![img](imgs/icon_moreInfo-20200310184426771.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/HBase.html#concept_vn5_cr5_4v) | 使用隐式字段映射，以便目标根据匹配的字段名称将数据写入HBase列。字段路径必须使用以下格式：`:` |
   | 忽略无效的列                                                 | 忽略无效的列。在配置隐式字段映射时使用。如果选择此选项，并且目标遇到无法映射到有效HBase列的字段路径，则目标将忽略无效列，并将记录中的其余字段写入HBase。如果清除并且目标遇到无效的列，则将该记录发送到阶段以进行错误处理。 |
   | Kerberos身份验证 [[![img](imgs/icon_moreInfo-20200310184426771.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/HBase.html#concept_xy5_4tm_vs) | 使用Kerberos凭据连接到HBase。选中后，将使用Data Collector配置文件中定义的Kerberos主体和密钥表`$SDC_CONF/sdc.properties`。 |
   | 验证表的存在                                                 | 在写入HBase表之前，先验证该表是否存在。选中后，目标将获取表描述符，以确定表是否存在并验证列族。这要求HBase用户具有HBase管理员权限。当您不想向HBase用户授予HBase管理员权限时，可能需要清除此属性。如果将目标配置为跳过验证并且表不存在，则管道会遇到错误。 |
   | HBase用户[![img](imgs/icon_moreInfo-20200310184426771.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/HBase.html#concept_gbt_fpt_ls) | 用于写入HBase的HBase用户。使用此属性时，请确保正确配置了HBase。未配置时，管道将使用当前登录的Data Collector用户。将Data Collector配置为使用当前登录的Data Collector用户时，不可配置。有关更多信息，请参阅Data Collector 文档 中的[Hadoop模拟模式](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_pmr_sy5_nz)。 |
   | 时间基础[![img](imgs/icon_moreInfo-20200310184426771.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/HBase.html#concept_t4g_vc2_wv) | 添加到写入HBase的每一列中的时间戳值使用的时间基准。使用以下表达式之一：`${time:now()}`-使用Data Collector的处理时间作为时间基准。`${record:value()}` -使用与记录关联的时间作为时间基准。或者，将其保留为空以使用由HBase自动生成的系统时间作为时间基准。 |
   | HBase配置目录[![img](imgs/icon_moreInfo-20200310184426771.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/HBase.html#concept_tjp_v5l_zq) | HDFS配置文件的位置。对于Cloudera Manager安装，请输入 `hbase-conf`。对于所有其他安装，请使用Data Collector资源目录中的目录或符号链接。您可以在HBase中使用以下文件：hbase-site.xml**注意：**配置文件中的属性被阶段中定义的单个属性覆盖。 |
   | HBase配置                                                    | 要使用的其他HBase配置属性。要添加属性，请单击**添加**并定义属性名称和值。使用HBase期望的属性名称和值。 |