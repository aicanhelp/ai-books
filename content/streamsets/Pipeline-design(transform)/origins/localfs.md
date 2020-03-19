# 文件

文件源从Hadoop分布式文件系统（HDFS）或本地文件系统中的文件读取数据。每个文件都必须完整写入，包括支持相同格式的数据，并使用[相同的schema](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/SchemaInference.html#concept_bbv_wkr_1jb)。

文件原点使用存储在Hadoop配置文件中的连接信息从HDFS读取。

批量读取多个文件时，原始服务器首先读取最早的文件。成功读取文件后，原始服务器可以删除文件，将其移动到存档目录或保留在目录中。

当管道停止时，原点会记录它处理的最后一个文件的最后修改的时间戳，并将其存储为偏移量。当管道再次启动时，默认情况下原点将从上次保存的偏移开始继续处理。需要时，您可以[重置管道偏移](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Offsets.html#concept_ygg_ryx_gjb)以处理所有可用文件。

配置文件来源时，可以指定要使用的目录和要读取的文件的名称模式。您还可以为文件的子集配置文件名模式以将其排除在处理之外。您可以指定数据的数据格式，相关的数据格式属性以及如何处理成功读取文件。需要时，您可以定义要批量读取的最大文件数。

您选择数据的数据格式并配置相关属性。处理定界数据或JSON数据时，您可以定义一个自定义架构来读取数据并配置相关属性。

您也可以为与HDFS兼容的系统指定HDFS配置属性。任何指定的属性都会覆盖Hadoop配置文件中定义的那些属性。

您可以将源配置为仅加载一次数据，并缓存数据以在整个管道运行中重复使用。或者，您可以配置源以缓存每一批数据，以便可以将其有效地传递到多个下游批次。 您还可以将原点配置为[跳过跟踪偏移量](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Offsets.html#concept_qqc_xsx_gjb)，从而可以在每次启动管道时读取整个数据集。

## 模式要求

由文件原点处理的所有文件必须具有相同的架构。

当文件具有不同的架构时，产生的行为取决于数据格式和所使用的Spark版本。例如，原点可能会跳过处理具有不同模式的定界文件，但会向具有不同模式的Parquet文件添加空值。

## 目录路径

文件来源从特定目录读取文件。在每个批次中，源读取自上一个批次完成以来添加到目录中的所有文件。

要指定目录，请输入目录的路径。目录路径的格式取决于您要读取的文件系统：

- HDFS

  要读取HDFS中的文件，请对目录路径使用以下格式：

  hdfs://<authority>/<path>

  例如，要从 HDFS上的/ user / hadoop / files目录读取，请输入以下路径：

  hdfs://nameservice/user/hadoop/files

- 本地文件系统

  要读取本地文件系统中的文件，请对目录路径使用以下格式：

  `file:///`

  例如，要从 本地文件系统上的/ Users / transformer / source目录读取，请输入以下路径：

  file:///Users/transformer/source

## 分区

与运行其他任何应用程序一样，Spark运行Transformer管道，将数据拆分为多个分区，并在分区上并行执行操作。 Spark根据流水线的来源确定如何将流水线数据拆分为初始分区。

对于文件来源，Spark根据正在读取的文件的数据格式确定分区：

- 分隔，JSON，文本或XML

  从本地文件系统读取基于文本的文件时，Spark为每个读取的文件创建一个分区。

  从HDFS读取基于文本的文件时，Spark可以将文件分为多个分区进行处理，具体取决于基础文件系统。多行JSON文件无法拆分，因此会在单个分区中进行处理。

- Avro，ORC或镶木地板

  读取Avro，ORC或Parquet文件时，Spark可以将文件分成多个分区进行处理。

除非处理器使Spark乱序处理数据，否则Spark会在整个管道中使用这些分区。当您需要更改管道中的分区时，请使用[Repartition处理器](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/Repartition.html#concept_cm5_lfg_wgb)。

## 资料格式

“文件”原点根据指定的数据格式生成记录。

原点可以读取以下数据格式：

- 阿夫罗

  原点会为Avro容器文件中的每个Avro记录生成一条记录。每个文件必须包含Avro模式。源使用Avro模式生成记录。

  配置原点时，必须指定适用于Spark版本的Avro选项以运行管道：Spark 2.3或Spark 2.4或更高版本。

  使用Spark 2.4或更高版本时，可以定义要使用的Avro模式。模式必须为JSON格式。您还可以配置源，以处理指定位置中的所有文件。默认情况下，原点仅处理带有`.avro` 扩展名的文件。

- 定界

  原点为定界文件中的每一行生成一条记录。您可以指定数据中使用的自定义定界符，引号和转义符。

  默认情况下，原点使用第一行中的值作为字段名称，并从文件的第二行开始创建记录。默认情况下，原点从数据推断数据类型。

  您可以清除Includes Header属性，以指示文件不包含标题行。如果文件不包含标题行，则源将命名为第一个字段`_c0`，第二个字段`_c1`，依此类推。默认情况下，起源还从数据推断数据类型。您可以使用Field Renamer处理器重命名下游的字段，也可以在源中指定自定义架构。

  指定[自定义架构时](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ntb_ttd_hhb)，源将使用[架构中定义](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ntb_ttd_hhb)的字段名称和数据类型，将架构中的第一个字段应用于记录中的第一个字段，依此类推。

  默认情况下，当原点遇到解析错误时，它将停止管道。使用自定义架构处理数据时，原始服务器根据配置的[错误处理来](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ywp_xct_hhb)处理解析错误。

  文件必须`\n`用作换行符。原点跳过空行。

- JSON格式

  默认情况下，原点会为JSON Lines文件中的每一行生成一条记录。文件中的每一行都应包含一个有效的JSON对象。有关详细信息，请访问[JSON Lines网站](http://jsonlines.org/)。

  如果JSON Lines文件包含跨越多行的对象，则必须配置源以处理多行JSON对象。处理多行JSON对象时，原点会为每个JSON对象生成一条记录，即使它跨越多行也是如此。

  可以将标准的单行JSON Lines文件拆分为多个分区并进行并行处理。多行JSON文件无法拆分，因此必须在单个分区中进行处理，这会降低管道性能。

  默认情况下，原点使用数据中的字段名称，字段顺序和数据类型。

  指定[自定义架构时](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ntb_ttd_hhb)，源将架构中的字段名称与数据中的字段名称匹配，然后应用架构中定义的数据类型和字段顺序。

  默认情况下，当原点遇到解析错误时，它将停止管道。使用自定义架构处理数据时，原始服务器根据配置的[错误处理来](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ywp_xct_hhb)处理解析错误。

- 兽人

  原点为“优化行列”（ORC）文件中的每一行生成一条记录。

- 木地板

  原点为文件中的每个Parquet记录生成记录。该文件必须包含Parquet模式。原点使用Parquet模式生成记录。

- 文本

  原点为文本文件中的每一行生成一条记录。该文件必须`\n`用作换行符。

  生成的记录由一个名为String的字段组成， `Value`其中包含数据。

- XML格式

  原点为XML文件中定义的每一行生成一条记录。您可以指定文件中使用的根标签和用于定义记录的行标签。

## 配置文件来源

配置文件源以从HDFS或本地文件系统中的文件读取数据。

**注意：**所有已处理的文件必须共享[相同的架构](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/File.html#concept_rtl_k1m_t3b)。

1. 在“属性”面板上的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 仅加载一次数据                                               | 批量读取数据并缓存结果以备重用。用于在流执行模式管道中[执行查找](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Lookups.html#concept_f2z_5yw_g3b)。使用原点执行查找时， 请勿限制批处理大小。所有查询数据都应在一个批次中读取。在批处理执行模式下，将忽略此属性。 |
   | [缓存数据](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/CachingData.html#concept_q2r_xm4_33b) | 缓存处理后的数据，以便可以在多个下游阶段重用该数据。当阶段将数据传递到多个阶段时，用于提高性能。当管道以[荒谬的方式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Ludicrous.html#concept_m4y_lbq_g3b)运行时，缓存会限制下推式优化。未启用“仅一次加载数据”时可用。当原点一次加载数据时，它也会缓存数据。 |
   | 跳过偏移跟踪                                                 | 跳过跟踪偏移量。在流传输管道中，这导致读取每个批次中的所有可用数据。在批处理管道中，这导致每次管道启动时都读取所有可用数据。 |

2. 在**文件**选项卡上，配置以下属性：

   | 文件属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [目录路径](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/File.html#concept_mdr_p4j_zgb) | 存储源文件的目录的路径。要从HDFS读取，请使用以下格式：hdfs://<authority>/<path>要从本地文件系统读取，请使用以下格式：`file:///` |
   | 文件名模式                                                   | 球形模式，用于定义要处理的文件名。您可以使用UNIX样式的通配符，例如*或？。例如，该模式`??a`表示以“ a”结尾的三个字符的文件名。该模式`*.txt`代表一个或多个以“ .txt”结尾的字符的文件名。您不能在模式中使用波浪号（〜）或斜杠（/）。您不能在模式的开头使用句点（。）。原点将句点视为模式中其他位置的文字。默认值为`*`，将处理所有文件。 |
   | 排除方式                                                     | 球形模式，用于定义要从处理中排除的文件名。用于排除“文件名模式”属性所包含的文件子集。例如，如果将“文件名模式”设置`*`为处理目录中的所有文件，则可以将“文件名排除模式”设置`*.log`为从处理中排除日志文件。 |
   | 每批最大文件数                                               | 批量处理的最大文件数。对于 [批处理管道](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/ExecutionMode.html#concept_lgy_24q_qgb__batch)，此属性确定每个管道运行中处理的文件总数。对于[流管道](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/ExecutionMode.html#concept_lgy_24q_qgb__Streaming)，此属性确定一次处理的文件数。此属性在需要处理文件的初始积压的流传输管道中很有用。默认为0，无限制。 |
   | 后期处理                                                     | 采取措施成功读取文件：删除-从目录中删除文件。存档-将文件移动到存档目录。无-将文件保留在找到的目录中。 |
   | 档案目录                                                     | 当原始文件归档文件时，用于存储成功读取文件的目录。           |
   | 附加配置                                                     | 要使用的其他HDFS配置属性。指定的属性将覆盖Hadoop配置文件中的属性。要添加属性，请单击“ **添加”** 图标并定义HDFS属性名称和值。使用您的Hadoop版本所期望的属性名称和值。 |

3. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/File.html#File-DataFormats) | 数据格式。选择以下格式之一：Avro（Spark 2.4或更高版本）-适用于Spark 2.4或更高版本处理的Avro数据。Avro（Spark 2.3）-适用于Spark 2.3处理的Avro数据。定界JSON格式兽人木地板文本XML格式 |

4. 对于由Spark 2.4或更高版本处理的Avro数据，可以选择配置以下属性：

   | Avro / Spark 2.4属性 | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | Avro模式             | 用于处理数据的可选Avro模式。指定的Avro架构将覆盖文件中包含的任何架构。以JSON格式指定Avro模式。 |
   | 忽略扩展             | 处理指定目录中的所有文件。未启用时，原点仅处理带有`.avro`扩展名的文件 。 |

5. 对于定界数据，在“ **数据格式”**选项卡上，可以选择配置以下属性：

   | 定界财产 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 分隔符   | 数据中使用的分隔符。选择一个可用选项，或选择“其他”以输入自定义字符。您可以输入使用格式为Unicode控制符\uNNNN，其中*ñ*是数字0-9或字母AF十六进制数字。例如，输入 \u0000以使用空字符作为分隔符或 \u2028使用行分隔符作为分隔符。 |
   | 引用字符 | 数据中使用的引号字符。                                       |
   | 转义符   | 数据中使用的转义符                                           |
   | 包括标题 | 指示数据包括标题行。选中后，原点将使用第一行创建字段名称，并从第二行开始读取。 |

6. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

   | JSON属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 多行     | 启用处理多行JSON行数据。默认情况下，原点在文件的每一行上都需要一个JSON对象。使用此选项来处理跨越多行的JSON对象。 |

7. 对于XML数据，在“ **数据格式”**选项卡上，配置以下属性：

   | XML属性 | 描述                                                       |
   | :------ | :--------------------------------------------------------- |
   | 根标签  | 用作根元素的标签。默认值为ROWS，它表示<ROWS>根元素。       |
   | 行标签  | 用作记录轮廓的标记。默认值为ROW，它表示<ROW>记录描述元素。 |

8. 要将[自定义模式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ntb_ttd_hhb)用于定界或JSON数据，请单击“ **模式”**选项卡并配置以下属性：

   | 架构属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 模式模式                                                     | 确定处理数据时要使用的架构的模式：从数据推断原点从数据中推断出字段名称和数据类型。使用自定义架构-DDL格式源使用以DDL格式定义的 [自定义架构](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ntb_ttd_hhb)。使用自定义架构-JSON格式源使用以JSON格式定义的 [自定义架构](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ntb_ttd_hhb)。请注意，根据数据的数据格式，[应用模式的方式有所](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_a14_hnx_jhb)不同。 |
   | 架构图                                                       | 用于处理数据的自定义架构。根据所选的架构模式，以[DDL](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_oqw_pgm_hhb)或[JSON](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_pzp_sfm_hhb)格式输入架构。 |
   | [错误处理](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/CustomSchemas.html#concept_ywp_xct_hhb) | 确定原点如何处理解析错误：允许-起源在解析记录中的任何字段时遇到问题时，它将创建一个记录，该记录具有在模式中定义的字段名称，但每个字段中的值为空。格式不正确的删除-当源在解析记录中的任何字段时遇到问题时，它将从管道中删除整个记录。快速失败-当源在解析记录中的任何字段时遇到问题时，它将停止管道。 |
   | 原始数据字段                                                 | 当原点无法解析记录时，将原始记录中的数据写入的字段。将原始记录写入字段时，必须将该字段作为String字段添加到自定义架构中。使用宽松的错误处理时可用。 |