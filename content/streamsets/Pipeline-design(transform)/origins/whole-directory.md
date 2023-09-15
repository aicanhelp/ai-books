# 整个目录

整个目录源将批量读取HDFS或本地文件系统上指定目录内的所有文件。每个文件都必须完整写入，包括支持相同格式的数据，并使用[相同的schema](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/SchemaInference.html#concept_bbv_wkr_1jb)。

**重要说明：**整个目录原点不跟踪偏移量，因此，每次管道运行时，原点都会读取目录中的所有文件。仅在适当的情况下才使用“整个目录”来源。

例如，您可能在批处理管道中使用“整个目录”来源，您希望在每次管道运行时重新读取文件目录。或者，您可以在[缓慢变化的维度管道](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/SCDimension.html#concept_rnp_nxr_j3b)中使用原点，该[管道](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/SCDimension.html#concept_rnp_nxr_j3b)会更新分区的文件维度数据。

要使用更传统的来源（跟踪偏移量并允许缓存）来读取文件，请使用[文件](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/File.html#concept_jcx_f2d_qgb)来源。

整个目录源使用存储在Hadoop配置文件中的连接信息从HDFS读取。

配置整个目录源时，可以指定要读取的目录。您选择数据的数据格式并配置相关属性。处理定界数据或JSON数据时，您可以定义一个自定义架构来读取数据并配置相关属性。

您也可以为与HDFS兼容的系统指定HDFS配置属性。任何指定的属性都会覆盖Hadoop配置文件中定义的那些属性。

## 资料格式

整个目录源根据指定的数据格式生成记录。

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

## 配置整个目录来源



配置整个目录源，以单批读取HDFS或本地文件系统上目录中的所有文件。

1. 在“属性”面板上的“ **常规”**选项卡上，配置以下属性：

   | 一般财产 | 描述       |
   | :------- | :--------- |
   | 名称     | 艺名。     |
   | 描述     | 可选说明。 |

2. 在**文件**选项卡上，配置以下属性：

   | 文件属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 目录路径 | 读取目录的路径。要从HDFS读取，请使用以下格式：hdfs://<authority>/<path>要从本地文件系统读取，请使用以下格式：`file:///` |
   | 附加配置 | 要传递给与HDFS兼容的文件系统的其他HDFS属性。指定的属性将覆盖Hadoop配置文件中的属性。要添加属性，请单击“ **添加”** 图标并定义HDFS属性名称和值。使用您的Hadoop版本所期望的属性名称和值。 |

3. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/WholeDirectory.html#concept_llr_3cy_q3b) | 数据格式。选择以下格式之一：Avro（Spark 2.4或更高版本）-适用于Spark 2.4或更高版本处理的Avro数据。Avro（Spark 2.3）-适用于Spark 2.3处理的Avro数据。定界JSON格式兽人木地板文本XML格式 |

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