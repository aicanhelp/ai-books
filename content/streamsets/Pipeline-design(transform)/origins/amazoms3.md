# 亚马逊S3

Amazon S3原始读取存储在Amazon Simple Storage Service（也称为Amazon S3）中的对象。这些对象必须完全写入，包括相同支持格式的数据，并使用相同的架构。

批量读取多个对象时，原点首先读取最早的对象。成功读取对象后，原始对象可以删除该对象，将其移动到存档目录或将其保留在目录中。

当管道停止时，原点会记录它处理的最后一个对象的最后修改的时间戳，并将其存储为偏移量。当管道再次启动时，默认情况下原点将从上次保存的偏移开始继续处理。您可以[重置管道偏移](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Offsets.html#concept_ygg_ryx_gjb)以处理所有可用对象。

Amazon S3源使用存储在Hadoop配置文件中的连接信息从Amazon S3读取。在本地管道中使用原点之前，请完成[先决条件任务](https://streamsets.com/documentation/controlhub/latest/help/transformer/Installation/StagePrerequisites.html#concept_owd_4ld_h3b)。

配置源时，可以指定要使用的连接安全性和相关属性。您可以定义对象的位置以及要读取的对象的名称模式。您可以选择配置另一个名称模式，以从处理中排除对象，并定义成功读取对象的后处理动作。

您选择数据的数据格式并配置相关属性。处理定界数据或JSON数据时，您可以定义一个自定义架构来读取数据并配置相关属性。

您可以配置高级属性，例如与性能相关的属性和代理服务器属性。您还可以指定要使用的最大连接数，连接和套接字超时以及重试计数。

您可以将源配置为仅加载一次数据，并缓存数据以在整个管道运行中重复使用。或者，您可以配置源以缓存每一批数据，以便可以将其有效地传递到多个下游批次。 您还可以将原点配置为[跳过跟踪偏移量](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Offsets.html#concept_qqc_xsx_gjb)，从而可以在每次启动管道时读取整个数据集。

## 模式要求

Amazon S3原始处理的所有对象必须具有相同的架构。

当对象具有不同的架构时，产生的行为取决于数据格式和所使用的Spark版本。例如，原点可能会跳过处理具有不同模式的定界数据，但会向具有不同模式的Parquet数据添加空值。

## 连接安全

您可以指定来源连接到Amazon S3的安全性。原点可以通过以下方式连接：

- IAM角色

  当Transformer在Amazon EC2实例上运行时，您可以使用AWS管理控制台为Transformer EC2实例配置IAM角色。然后，Transformer使用IAM实例配置文件凭证自动连接到Amazon S3。

  有关将IAM角色分配给EC2实例的更多信息，请参阅Amazon EC2文档。

- AWS密钥

  当Transformer未在Amazon EC2实例上运行或EC2实例不具有IAM角色时，您可以使用AWS访问密钥对进行连接。使用AWS访问密钥对时，请在源中指定访问密钥ID和秘密访问密钥属性。**提示：**为了保护敏感信息，可以按照Data Collector文档中的说明使用 [运行时资源](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或[凭据存储](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。

- 没有

  访问公共存储桶时，可以不使用安全性进行匿名连接。

## 分区

与运行其他任何应用程序一样，Spark运行Transformer管道，将数据拆分为多个分区，并在分区上并行执行操作。 Spark根据流水线的来源确定如何将流水线数据拆分为初始分区。

对于Amazon S3来源，Spark根据正在读取的数据的数据格式确定分区：

- 分隔，JSON，文本或XML

  读取基于文本的数据时，Spark可以将对象分为多个分区进行处理，具体取决于基础文件系统。多行JSON文件无法拆分。

- Avro，ORC或镶木地板

  读取Avro，ORC或Parquet数据时，Spark可以将对象分成多个分区进行处理。

除非处理器使Spark乱序处理数据，否则Spark会在整个管道中使用这些分区。当您需要更改管道中的分区时，请使用[Repartition处理器](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/Repartition.html#concept_cm5_lfg_wgb)。

## 资料格式

Amazon S3原始服务器根据指定的数据格式生成记录。

原点可以读取以下数据格式：

- 阿夫罗

  原点为对象中的每个Avro记录生成一条记录。每个对象必须包含Avro模式。源使用Avro模式生成记录。

  配置原点时，必须指定适用于Spark版本的Avro选项以运行管道：Spark 2.3或Spark 2.4或更高版本。

  使用Spark 2.4或更高版本时，可以定义要使用的Avro模式。模式必须为JSON格式。您还可以配置原点以处理指定位置中的所有对象。默认情况下，原点仅处理带有`.avro`扩展名的对象 。

- 定界

  原点为对象中的每个定界线生成一条记录。您可以指定数据中使用的自定义定界符，引号和转义符。

  默认情况下，原点使用第一行中的值作为字段名称，并创建从对象第二行开始的记录。默认情况下，原点从数据推断数据类型。

  您可以清除Includes Header属性，以指示对象不包含标题行。当对象不包括标题行时，原点将命名为第一个字段`_c0`，第二个字段`_c1`，依此类推。默认情况下，起源还从数据推断数据类型。您可以使用Field Renamer处理器重命名下游的字段，也可以在源中指定自定义架构。

  指定[自定义架构时](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ntb_ttd_hhb)，源将使用[架构中定义](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ntb_ttd_hhb)的字段名称和数据类型，将架构中的第一个字段应用于记录中的第一个字段，依此类推。

  默认情况下，当原点遇到解析错误时，它将停止管道。使用自定义架构处理数据时，原始服务器根据配置的[错误处理来](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ywp_xct_hhb)处理解析错误。

  对象必须`\n`用作换行符。原点跳过空行。

- JSON格式

  默认情况下，原点会为对象中的每一行生成一条记录。对象中的每一行都必须包含有效的JSON Lines数据。有关详细信息，请访问 [JSON Lines网站](http://jsonlines.org/)。

  如果JSON行数据包含跨多行的对象，则必须配置源以处理多行JSON对象。处理多行JSON对象时，原点会为每个JSON对象生成一条记录，即使它跨越多行也是如此。

  可以将标准的单行JSON Lines对象拆分为多个分区并进行并行处理。多行JSON对象无法拆分，因此必须在单个分区中进行处理，这会降低管道性能。

  默认情况下，原点使用数据中的字段名称，字段顺序和数据类型。

  指定[自定义架构时](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ntb_ttd_hhb)，源将架构中的字段名称与数据中的字段名称匹配，然后应用架构中定义的数据类型和字段顺序。

  默认情况下，当原点遇到解析错误时，它将停止管道。使用自定义架构处理数据时，原始服务器根据配置的[错误处理来](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ywp_xct_hhb)处理解析错误。

- 兽人

  原点为对象中的每个优化行列（ORC）行生成一条记录。

- 木地板

  原点为对象中的每个Parquet记录生成记录。该对象必须包含Parquet模式。原点使用Parquet模式生成记录。

- 文本

  原点为对象中的每个文本行生成一条记录。该对象必须`\n`用作换行符。

  生成的记录由一个名为String的字段组成， `Value`其中包含数据。

- XML格式

  原点为对象中的每一行生成一条记录。您可以指定文件中使用的根标签和用于定义记录的行标签。

## 配置Amazon S3来源

配置Amazon S3源以读取Amazon S3中的数据。在本地管道中使用原点之前，请完成[先决条件任务](https://streamsets.com/documentation/controlhub/latest/help/transformer/Installation/StagePrerequisites.html#concept_owd_4ld_h3b)。

**注意：**所有处理的对象必须共享[相同的架构](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/AmazonS3.html#concept_yty_n1m_t3b)。

1. 在“属性”面板上的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 用于连接到Amazon S3的舞台库：AWS集群提供的库-运行管道的集群安装了Apache Hadoop Amazon Web Services库，因此具有运行管道所需的所有必需库。AWS变压器提供的库Hadoop的3.2.0 - 变压器通过与管道必要的库，使运行的管道。在本地运行管道时或管道运行所在的集群不包括Amazon Web Services库版本3.2.0时使用。**注意：**在管道中使用其他Amazon阶段时，请确保它们使用[相同的阶段库](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Pipeline-StageLibMatch.html#concept_r4g_n3x_shb)。 |
   | 仅加载一次数据                                               | 批量读取数据并缓存结果以备重用。用于在流执行模式管道中[执行查找](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Lookups.html#concept_f2z_5yw_g3b)。使用原点执行查找时， 请勿限制批处理大小。所有查询数据都应在一个批次中读取。在批处理执行模式下，将忽略此属性。 |
   | [缓存数据](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/CachingData.html#concept_q2r_xm4_33b) | 缓存处理后的数据，以便可以在多个下游阶段重用该数据。当阶段将数据传递到多个阶段时，用于提高性能。当管道以[荒谬的方式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Ludicrous.html#concept_m4y_lbq_g3b)运行时，缓存会限制下推式优化。未启用“仅一次加载数据”时可用。当原点一次加载数据时，它也会缓存数据。 |
   | 跳过偏移跟踪                                                 | 跳过跟踪偏移量。在流传输管道中，这导致读取每个批次中的所有可用数据。在批处理管道中，这导致每次管道启动时都读取所有可用数据。 |

2. 在“ **Amazon S3”**选项卡上，配置以下属性：

   | Amazon S3属性                                                | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [安全](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/AmazonS3.html#concept_ekg_jrw_shb) | 用于连接到Amazon S3的模式：AWS密钥-使用AWS访问密钥对进行连接。IAM角色-使用分配给Transformer EC2实例的IAM角色进行连接。无-不使用安全性连接到公共存储桶。 |
   | 访问密钥ID                                                   | AWS访问密钥ID。使用AWS密钥连接到Amazon S3时需要。**提示：**为了保护敏感信息，可以按照Data Collector文档中的说明使用 [运行时资源](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或[凭据存储](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。 |
   | 秘密访问密钥                                                 | AWS秘密访问密钥。使用AWS密钥连接到Amazon S3时需要。**提示：**为了保护敏感信息，可以按照Data Collector文档中的说明使用 [运行时资源](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或[凭据存储](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。 |
   | 桶                                                           | 要读取的对象的位置。使用以下格式：`s3a:////`                 |
   | 对象名称模式                                                 | Glob模式，定义要处理的对象的名称。例如，要读取指定存储桶中的所有对象，请使用星号（*）。 |
   | 排除方式                                                     | 球形模式，定义要从处理中排除的对象的名称。                   |
   | 每批最大对象数                                               | 批处理中包含的最大对象数。                                   |

3. 在“ **后处理”**选项卡上，可以选择配置以下属性：

   | 后处理属性 | 描述                                                         |
   | :--------- | :----------------------------------------------------------- |
   | 后期处理   | 成功处理对象后要采取的措施：无-将对象保持在原位。存档-将对象复制或移动到另一个位置。删除-删除对象。 |
   | 档案目录   | 存储成功处理的对象的位置。使用以下格式：`s3a:////`           |

4. 在“ **高级”**选项卡上，可以选择配置以下属性：

   | 先进物业       | 描述                                                         |
   | :------------- | :----------------------------------------------------------- |
   | 附加配置       | 要传递给与HDFS兼容的文件系统的其他HDFS属性。指定的属性将覆盖Hadoop配置文件中的属性。要添加属性，请单击“ **添加”** 图标并定义HDFS属性名称和值。使用您的Hadoop版本所期望的属性名称和值。 |
   | 块大小         | 读取数据时使用的块大小（以字节为单位）。默认值为33554432。   |
   | 缓冲提示       | TCP套接字缓冲区大小提示，以字节为单位。默认值为8192。        |
   | 最大连接数     | 与Amazon S3的最大连接数。默认值为1。                         |
   | 连接超时       | 关闭连接之前等待响应的毫秒数。默认值为200000毫秒，即3.33分钟。 |
   | 套接字超时     | 等待查询响应的毫秒数。默认值为5000毫秒，即5秒。              |
   | 重试计数       | 重试请求的最大次数。默认值为20。                             |
   | 使用代理服务器 | 启用使用代理服务器连接到Amazon S3的功能。                    |
   | 代理主机       | 代理服务器的主机名。使用代理服务器时必需。                   |
   | 代理端口       | 代理服务器的可选端口号。                                     |
   | 代理用户       | 代理服务器凭据的用户名。使用凭据是可选的。                   |
   | 代理密码       | 代理服务器凭据的密码。使用凭据是可选的。**提示：**要保护敏感信息（例如用户名和密码），可以按照Data Collector文档中的说明使用[运行时资源](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或[凭据存储](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。 |
   | 代理域         | 代理服务器的可选域名。                                       |
   | 代理工作站     | 代理服务器的可选工作站。                                     |

5. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/File.html#File-DataFormats) | 数据格式。选择以下格式之一：Avro（Spark 2.4或更高版本）-适用于Spark 2.4或更高版本处理的Avro数据。Avro（Spark 2.3）-适用于Spark 2.3处理的Avro数据。定界JSON格式兽人木地板文本XML格式 |

6. 对于由Spark 2.4或更高版本处理的Avro数据，可以选择配置以下属性：

   | Avro / Spark 2.4属性 | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | Avro模式             | 用于处理数据的可选Avro模式。指定的Avro架构将覆盖对象中包含的任何架构。以JSON格式指定Avro模式。 |
   | 忽略扩展             | 处理指定目录中的所有文件。未启用时，原点仅处理带有`.avro`扩展名的对象 。 |

7. 对于定界数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 定界财产 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 分隔符   | 数据中使用的分隔符。选择一个可用选项，或使用“其他”输入自定义字符。您可以输入使用格式为Unicode控制符\uNNNN，其中*ñ*是数字0-9或字母AF十六进制数字。例如，输入 \u0000以使用空字符作为分隔符或 \u2028使用行分隔符作为分隔符。 |
   | 引用字符 | 在数据中使用引号字符。                                       |
   | 转义符   | 数据中使用的转义字符                                         |

8. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

   | JSON属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 多行     | 启用处理多行JSON行数据。默认情况下，原点在文件的每一行上都需要一个JSON对象。使用此选项来处理跨越多行的JSON对象。 |

9. 对于XML数据，在“ **数据格式”**选项卡上，配置以下属性：

   | XML属性 | 描述                                                       |
   | :------ | :--------------------------------------------------------- |
   | 根标签  | 用作根元素的标签。默认值为ROWS，它表示<ROWS>根元素。       |
   | 行标签  | 用作记录轮廓的标记。默认值为ROW，它表示<ROW>记录描述元素。 |

10. 要将[自定义模式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ntb_ttd_hhb)用于定界或JSON数据，请单击“ **模式”**选项卡并配置以下属性：

    | 架构属性                                                     | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 模式模式                                                     | 确定处理数据时要使用的架构的模式：从数据推断原点从数据中推断出字段名称和数据类型。使用自定义架构-DDL格式源使用以DDL格式定义的 [自定义架构](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ntb_ttd_hhb)。使用自定义架构-JSON格式源使用以JSON格式定义的 [自定义架构](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ntb_ttd_hhb)。请注意，根据数据的数据格式，[应用模式的方式有所](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_a14_hnx_jhb)不同。 |
    | 架构图                                                       | 用于处理数据的自定义架构。根据所选的架构模式，以[DDL](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_oqw_pgm_hhb)或[JSON](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_pzp_sfm_hhb)格式输入架构。 |
    | [错误处理](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ywp_xct_hhb) | 确定原点如何处理解析错误：允许-起源在解析记录中的任何字段时遇到问题时，它将创建一个记录，该记录具有在模式中定义的字段名称，但每个字段中的值为空。格式不正确的删除-当源在解析记录中的任何字段时遇到问题时，它将从管道中删除整个记录。快速失败-当源在解析记录中的任何字段时遇到问题时，它将停止管道。 |
    | 原始数据字段                                                 | 当原点无法解析记录时，将原始记录中的数据写入的字段。将原始记录写入字段时，必须将该字段作为String字段添加到自定义架构中。使用宽松的错误处理时可用。 |