# MapR DB JSON

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310172619054.png) 资料收集器

MapR DB JSON来源从MapR DB JSON表读取JSON文档。来源将每个文档转换为一条记录。

MapR DB JSON表是其中每一行都是JSON文档的表。每个JSON文档在_id字段中都有一个唯一的标识符，该标识符又用作行键，以唯一地标识表中的每一行。

在配置来源时，您定义要读取的JSON表。原点使用每个JSON文档中的_id字段作为偏移量字段。您可以选择定义初始偏移值以开始读取。

当管道停止时，MapR DB JSON起源会记录它在何处停止读取。当管道再次启动时，原点将从默认情况下停止的地方继续进行处理。您可以重置原点以处理所有可用数据。

**提示：** Data Collector 提供了多个MapR来源来满足不同的需求。有关快速比较表以帮助您选择合适的表，请参阅[比较MapR起源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ip2_szg_qbb)。

在管道中使用任何MapR阶段之前，必须执行其他步骤以使Data Collector能够处理MapR数据。有关更多信息，请参阅Data Collector 文档中的 [MapR先决条件](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Installation/MapR-Prerequisites.html%23concept_jgs_qpg_2v)。

## 处理_id字段

当原始将JSON文档转换为记录时，它在记录中包含JSON文档的_id字段。如果需要，您可以在管道中使用Field Remover处理器删除_id字段。

JSON文档中的_id字段可以包含字符串或二进制数据。MapR DB JSON起源可以从JSON表中读取，这些JSON表包含具有有效类型之一的_id字段。例如，当表中的所有文档均具有字符串_id字段或所有文档均具有二进制_id字段时，可从JSON表读取源。无法从具有_id字段类型组合的表中读取源。

当JSON文档包含字符串_id字段时，源将在记录中将_id字段创建为字符串。

当JSON文档包含二进制_id字段时，原点会将数据转换为String，然后将该字段包含在记录中。

**注意：** JSON文档中的二进制_id字段必须包含数字数据，以便原始可以正确处理数据。此外，二进制_id字段对于表中的所有行或JSON文档必须具有相同的宽度。

## 配置MapR DB JSON来源

配置MapR DB JSON源，以从MapR DB JSON表读取JSON文档。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **MapR DB JSON”**选项卡上，配置以下属性：

   | MapR DB JSON属性 | 描述                                                         |
   | :--------------- | :----------------------------------------------------------- |
   | 表名             | 要读取的MapR DB JSON表的名称。输入表格名称。如果您不包括该表的路径，那么此阶段将假定该表存在于用户的主目录中。例如， `/user//`。输入表名时，可以包括相对于用户主目录的路径，也可以包括绝对路径。对于默认集群中的表，将绝对路径指定为`/`。对于特定集群中的表，将绝对路径指定为`/mapr//`。 |
   | 初始偏移         | JSON文档中_id字段的值，或者表中的行键，您要在其中开始读取源。默认情况下，源读取JSON表中的所有行。您可以选择定义一个初始偏移值，以确定原点从JSON表中开始读取数据的位置。 |