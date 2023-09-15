# Kinesis Firehose

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310184832017.png) 资料收集器![img](imgs/icon-Edge-20200310184832017.png) 数据收集器边缘

Kinesis Firehose目标将数据写入Amazon Kinesis Firehose交付流。Firehose自动将数据传递到您在传递流中指定的Amazon S3存储桶或Amazon Redshift表。

要将数据写入Amazon Kinesis Streams，请使用Kinesis Producer目标。要将数据直接写入Amazon S3，请使用Amazon S3目标。

当您使用Kinesis Firehose目标将数据传递到Amazon S3时，Firehose可以在将数据传递到Amazon S3之前将传入的记录缓冲成更大的文件大小。创建传送流时，可以配置缓冲区大小和缓冲区间隔。

配置Kinesis Firehose目标时，您可以指定要写入的现有交付流，AWS凭证和区域以及要使用的数据格式。

## AWS凭证

当Data Collector将数据写入Kinesis Firehose目标时，它必须将凭证传递给Amazon Web Services。

使用以下方法之一来传递AWS凭证：

- IAM角色

  当执行数据收集器 在Amazon EC2实例上运行时，您可以使用AWS管理控制台为EC2实例配置IAM角色。Data Collector使用IAM实例配置文件凭证自动连接到AWS。

  要使用IAM角色，请不要在目标中配置访问密钥ID和秘密访问密钥属性。

  有关将IAM角色分配给EC2实例的更多信息，请参阅Amazon EC2文档。

- AWS访问密钥对

  当执行数据收集器未在Amazon EC2实例上运行或EC2实例不具有IAM角色时，您必须 在目标中指定**访问密钥ID**和**秘密访问密钥**属性。

  **提示：**为了保护敏感信息（例如访问密钥对）的安全，可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。

**提示：**为了保护敏感信息（例如访问密钥对）的安全，可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。

## 投放流

Kinesis Firehose目标将数据写入Amazon Kinesis Firehose中的现有交付流。在使用Kinesis Firehose目标之前，请使用AWS管理控制台创建到Amazon S3存储桶或Amazon Redshift表的交付流。

有关创建Firehose交付流的更多信息，请参阅Amazon Kinesis Firehose文档。

## 资料格式

Kinesis Firehose目标根据您选择的数据格式将数据写入Kinesis Firehose传递流。

![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中， 目标仅支持JSON数据格式。

Kinesis Firehose目标处理数据格式如下：

- 定界

  目标将记录写为定界数据。使用此数据格式时，根字段必须是list或list-map。

  您可以使用以下定界格式类型：**默认CSV-**包含逗号分隔值的文件。忽略文件中的空行。**RFC4180 CSV-**严格遵循RFC4180准则的逗号分隔文件。**MS Excel CSV** -Microsoft Excel逗号分隔文件。**MySQL CSV** -MySQL逗号分隔文件。**制表符分隔的值** -包含制表符分隔的值的文件。**PostgreSQL CSV** -PostgreSQL逗号分隔文件。**PostgreSQL文本** -PostgreSQL文本文件。**自定义** -使用用户定义的定界符，转义符和引号字符的文件。**多字符**定界-使用多个用户定义的字符定界字段和行以及单个用户定义的转义和引号字符的文件。

- JSON格式

  目标将记录作为JSON数据写入。使用多个对象格式，其中每个文件都包含多个JSON对象。每个对象都是记录的JSON表示形式。**注意：** Kinesis Firehose目标不支持对象格式的JSON数组。

## 配置Kinesis Firehose目标

配置Kinesis Firehose目标，以将数据写入Amazon Kinesis Firehose交付流。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在**Kinesis**选项卡上，配置以下属性：

   | Kinesis属性                                                  | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 访问密钥ID [![img](imgs/icon_moreInfo-20200310184832074.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KinFirehose.html#concept_b14_24g_vw) | AWS访问密钥ID。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。 |
   | 秘密访问密钥[![img](imgs/icon_moreInfo-20200310184832074.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KinFirehose.html#concept_b14_24g_vw) | AWS秘密访问密钥。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。 |
   | 目的地类型                                                   | 要写入的亚马逊目的地的类型。选择“ **现有流”**。              |
   | 区域                                                         | Amazon Web Services地区。                                    |
   | 终点                                                         | 当您为区域选择“其他”时要连接的端点。输入端点名称。           |
   | 流名称[![img](imgs/icon_moreInfo-20200310184832074.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KinFirehose.html#concept_fm4_txq_kv) | 要写入的现有传递流。使用AWS管理控制台创建到Amazon S3存储桶或Amazon Redshift表的交付流。 |
   | 最大记录大小（KB）                                           | 单个记录的最大大小。当记录超过此大小时，目标将根据为该阶段配置的错误记录处理来处理记录。**警告：** Firehose记录的最大大小为1,000 KB。如果配置的最大大小大于1,000 KB，则Firehose不接受目标写入的任何数据。 |

3. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KinFirehose.html#concept_wrk_rzp_kv) | 要使用的数据格式。使用以下数据格式之一：定界JSON格式![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中， 目标仅支持JSON数据格式。 |

4. 对于定界数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 定界财产   | 描述                                                         |
   | :--------- | :----------------------------------------------------------- |
   | 分隔符格式 | 分隔数据的格式：**默认CSV-**包含逗号分隔值的文件。忽略文件中的空行。**RFC4180 CSV-**严格遵循RFC4180准则的逗号分隔文件。**MS Excel CSV** -Microsoft Excel逗号分隔文件。**MySQL CSV** -MySQL逗号分隔文件。**制表符分隔的值** -包含制表符分隔的值的文件。**PostgreSQL CSV** -PostgreSQL逗号分隔文件。**PostgreSQL文本** -PostgreSQL文本文件。**自定义** -使用用户定义的定界符，转义符和引号字符的文件。 |
   | 标题行     | 指示是否创建标题行。                                         |
   | 替换换行符 | 用配置的字符串替换换行符。在将数据写为单行文本时推荐使用。   |
   | 换行符替换 | 用于替换每个换行符的字符串。例如，输入一个空格，用空格替换每个换行符。留空以删除新行字符。 |
   | 分隔符     | 自定义分隔符格式的分隔符。选择一个可用选项，或使用“其他”输入自定义字符。您可以输入使用格式\ U A的Unicode控制符*NNNN*，其中*ñ*是数字0-9或字母AF十六进制数字。例如，输入\ u0000将空字符用作分隔符，或者输入\ u2028将行分隔符用作分隔符。默认为竖线字符（\|）。 |
   | 转义符     | 自定义分隔符格式的转义符。选择一个可用选项，或使用“其他”输入自定义字符。默认为反斜杠字符（\）。 |
   | 引用字符   | 自定义分隔符格式的引号字符。选择一个可用选项，或使用“其他”输入自定义字符。默认为引号字符（“”）。 |
   | 字符集     | 写入数据时使用的字符集。                                     |

5. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

   

   