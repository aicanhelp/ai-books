# MapR DB JSON

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310201813708.png) 资料收集器

MapR DB JSON目标将数据作为JSON文档写入MapR DB JSON表。目标将每个记录转换为一个JSON文档，并将该文档写入您指定的JSON表。要将文本，二进制数据或JSON字符串写入MapR DB二进制表，请使用[MapR DB destination](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MapRDB.html#concept_vxg_w2z_yv)。

MapR DB JSON表是其中每一行都是JSON文档的表。表中的JSON文档不需要具有相同的结构。例如，一个JSON表可以包含任意数量的JSON文档，这些文档仅共享一些公共字段。

MapR DB JSON目标可以使用sdc.operation.type记录头属性中定义的CRUD操作来写入数据。当未在记录中指定CRUD操作时，目标会将其视为插入记录。有关Data Collector更改数据处理以及启用CDC的来源的列表的信息，请参见[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

在配置MapR DB JSON目标时，您可以指定表名称以及目标是否应创建该表（如果该表不存在）。您为表指定行键。然后，您配置插入API和设置API属性，这会影响目标将数据写入MapR DB JSON表的方式。

在管道中使用任何MapR阶段之前，必须执行其他步骤以使Data Collector能够处理MapR数据。有关更多信息，请参阅Data Collector 文档中的 [MapR先决条件](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Installation/MapR-Prerequisites.html%23concept_jgs_qpg_2v)。

## 行键

MapR DB使用行键来唯一标识JSON表中的每一行。行密钥由存储在行中的JSON文档的_id字段定义。

配置MapR DB JSON目标时，您在记录中定义一个字段用作行键。该字段必须包含唯一值。目标将指定字段的值写入JSON文档中的_id字段。目标保留JSON文档中的原始字段。

例如，假设您将记录中的customer_ID字段定义为行键。当目标将customer_ID为034667的记录转换为JSON文档时，JSON文档同时包含_id字段和值为034667的customer_ID字段。MapR DB在JSON文档中使用值为034667的_id字段作为JSON表中的行键。

如果记录中不存在定义为行键的字段，则将该记录发送到阶段以进行错误处理。

### 行键数据类型

您可以将MapR DB JSON目标配置为将行键处理为字符串或二进制数据。如有必要，MapR DB JSON目标将转换行键字段的数据类型，然后将转换后的值写入JSON文档中的_id字段。

**注意：**目标无法转换List，Map或List-Map数据类型。结果，您不能将具有这些数据类型的字段定义为行键。

目标将定义为行键的字段处理为以下数据类型之一：

- 串

  当您配置目标以将行键作为字符串数据处理时，可以将具有任何数据类型的字段分配为行键，但具有List，Map或List-Map数据类型的字段除外。源将行键数据作为String处理，并根据需要转换数据类型。字节数组字段是此规则的例外。即使将目标配置为将行键作为字符串数据处理，目标也会将定义为行键的字节数组字段作为二进制数据进行处理。

  默认情况下，MapR DB JSON目标将行键作为字符串数据处理。

- 二元

  在将目标配置为将行键作为二进制数据处理时，可以将具有以下数据类型的字段分配为行键：字节数组日期约会时间整数长短串时间

  如果定义为行键的字段是任何其他数据类型，则将该记录发送到阶段以进行错误处理。

  源将行键数据作为字节数组处理，并根据需要转换数据类型。日期，日期时间和时间字段首先转换为以毫秒为单位的纪元时间，然后转换为字节数组。

  要将目标配置为将行键处理为二进制数据，请选择“将行键处理为二进制”属性。

## 写入MapR DB JSON

当MapR DB JSON目标写入MapR DB JSON表时，它会在记录标题属性中使用CRUD操作（如果可用）。当记录不包括CRUD操作时，目标会将它们视为插入记录。

您还可以配置插入API和设置API属性，这些属性定义当记录已存在于目标中时如何处理记录。

### 定义CRUD操作

您可以使用CRUD操作写入MapR DB JSON。要使用CRUD操作，请为管道中较早的每个记录定义CRUD操作记录标题属性。没有定义属性的记录将被视为插入。

要使用CRUD操作写入记录，请设置以下CRUD操作记录标题属性：

- sdc.operation.type

  定义后，当写入MapR DB JSON表时，MapR DB JSON目标在sdc.operation.type记录标题属性中使用CRUD操作。目标为sdc.operation.type属性支持以下值：INSERT为12个代表删除3更新

  如果您的管道包括启用CRUD的原始数据源，该原始数据元处理已更改的数据，则目标位置仅从`sdc.operation.type`原始数据源生成的标头属性中读取操作类型 。如果管道使用非CDC来源，则可以使用表达式评估器或脚本处理器来定义记录头属性。有关Data Collector 更改的数据处理以及启用CDC的来源的列表的详细信息 ，请参阅 [处理更改的数据。](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

### 插入和设置API属性

插入API和设置API属性确定在MapR DB JSON表中存在具有相同行键的记录时如何处理记录。

- 插入API

  用于插入记录。这包括CRUD操作标头属性设置为“插入”的记录，以及根本没有设置该属性的记录。您可以使用以下MapR API之一：MapR插入API-如果表中不存在匹配的行键，则MapR DB JSON目标会将记录插入到MapR DB JSON表中。当表具有匹配的行键时，目标将使用为阶段配置的错误处理将记录发送到错误。MapR InsertOrReplace API-如果表中没有匹配的行键，则MapR DB JSON目标会将记录插入到MapR DB JSON表中。当表具有匹配的行键时，目标将替换现有行。这是默认的API。

- 设定API

  仅用于更新记录。这仅包括CRUD操作标头属性设置为Update的记录。您可以使用以下MapR API之一：MapR Set API-仅当记录中字段的数据类型与现有行中的相应字段匹配时，MapR DB JSON才会执行更新。当数据类型不匹配时，目标将使用为阶段配置的错误处理将记录发送到错误。MapR SetOrReplace API-MapR DB JSON更新现有行，而不管记录中的数据类型是否与现有行中的数据类型匹配。这是默认的API。

## 配置MapR DB JSON目标

配置MapR DB JSON目标，以将数据作为JSON文档写入MapR DB JSON表。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **MapR DB JSON”**选项卡上，配置以下属性：

   | MapR DB JSON属性                                             | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 表名                                                         | 要写入的MapR DB JSON表的名称。输入以下内容之一：表名。计算结果为表名的表达式。例如，如果表名称存储在“ tableName”记录属性中，请输入以下表达式：`${record:attribute('tableName')}`如果您不包括该表的路径，那么此阶段将假定该表存在于用户的主目录中。例如， `/user//`。输入表名时，可以包括相对于用户主目录的路径，也可以包括绝对路径。对于默认集群中的表，将绝对路径指定为`/`。对于特定集群中的表，将绝对路径指定为`/mapr//`。 |
   | 建立表格                                                     | 确定目标是否创建表（如果不存在）。选中后，目标将创建该表（如果不存在）。清除后，目标在尝试写入不存在的表时会产生错误。 |
   | 行键 [![img](imgs/icon_moreInfo-20200310201814234.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MapRDBJSON.html#concept_mrg_nx3_4y) | 表的行键。定义记录中的哪个字段用作行键。                     |
   | 将行键处理为二进制 [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MapRDBJSON.html#concept_etz_vd3_qy) | 确定目标是将行键作为字符串还是二进制数据进行处理。清除后，目标会将行键字段转换为字符串。选中后，目标会将行键字段转换为字节数组。 |
   | 插入API [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MapRDBJSON.html#concept_gd5_fwg_xbb) | 确定目标如何将数据插入MapR DB JSON表：使用MapR InsertOrReplace API-如果记录具有唯一的行键，则将其插入表中。如果目标在表中找到匹配的行键，它将替换该行。使用MapR插入API-如果记录具有唯一的行键，则将其插入表中。如果目标在表中找到匹配的行键，它将记录发送到错误。默认值为使用MapR InsertOrReplace API。 |
   | 设定API [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MapRDBJSON.html#concept_gd5_fwg_xbb) | 确定目标如何更新MapR DB JSON表中的数据：使用MapR SetOrReplace API-对所有标记为更新的记录执行更新，而不管字段数据类型是否不匹配。使用MapR设置API-仅在记录中的数据类型与相应行匹配时才执行更新。当它们不匹配时，目标会将记录发送到错误。默认值为使用MapR SetOrReplace API。 |