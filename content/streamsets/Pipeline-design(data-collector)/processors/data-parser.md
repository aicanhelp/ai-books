# 数据解析器

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310175455194.png) 资料收集器

数据解析器处理器使您可以解析嵌入在字段中的受支持数据格式。您可以解析嵌入在字节数组字段中的NetFlow或嵌入在字符串字段中的syslog消息。

例如，假设您使用UDP源起源来读取包含安全设备生成的事件信息的系统日志消息。消息以公共事件格式（CEF）编写。您在源之后添加了一个数据解析器处理器，以使用CEF日志格式解析消息字段中的数据。

配置处理器时，可以指定要处理的字段和已解析数据的目标字段。您指出要处理的数据类型。

您还可以确定多值行为。当一个字段包含多个值时，您可以返回第一个值，所有值作为列表，或为每个值生成一条记录。

生成记录时，处理器将所有其他传入字段包括在生成的记录中。当由于已解析字段中的多个值而生成多个记录时，处理器会为每个生成的记录包括其他传入字段。

## 资料格式

Data Parser处理器根据您选择的数据格式来不同地处理数据。处理器处理以下类型的数据：

- 阿夫罗

  为每条消息生成一条记录。每个小数字段都包含 `precision`和`scale` [字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)。

  该阶段在`avroSchema` [记录头属性中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)包括Avro模式 。您可以使用以下方法之一来指定Avro模式定义的位置：**消息/数据包含架构** -在消息中使用架构。**在“管道配置”中** -使用您在阶段配置中提供的架构。**Confluent Schema Registry-**从Confluent Schema Registry检索架构。Confluent Schema Registry是Avro架构的分布式存储层。您可以配置阶段以通过消息中嵌入的模式ID或阶段配置中指定的模式ID或主题在Confluent Schema Registry中查找模式。

  在阶段配置中使用架构或从Confluent Schema Registry检索架构会覆盖消息中可能包含的任何架构，并可以提高性能。

- 定界

  为每个定界线生成一条记录。您可以使用以下定界格式类型：**默认CSV-**包含逗号分隔值的文件。忽略文件中的空行。**RFC4180 CSV-**严格遵循RFC4180准则的逗号分隔文件。**MS Excel CSV** -Microsoft Excel逗号分隔文件。**MySQL CSV** -MySQL逗号分隔文件。**制表符分隔的值** -包含制表符分隔的值的文件。**PostgreSQL CSV** -PostgreSQL逗号分隔文件。**PostgreSQL文本** -PostgreSQL文本文件。**自定义** -使用用户定义的定界符，转义符和引号字符的文件。**多字符**定界-使用多个用户定义的字符定界字段和行以及单个用户定义的转义和引号字符的文件。

  您可以将列表或列表映射根字段类型用于定界数据，并且可以选择在标题行中包括字段名称（如果有）。有关根字段类型的更多信息，请参见定界[数据根字段类型](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Delimited.html#concept_zcg_bm4_fs)。

  使用标题行时，可以启用带有其他列的记录处理。其他列使用自定义的前缀和顺序递增的顺序整数，如命名 `_extra_1`， `_extra_2`。当您禁止其他列时，包含其他列的记录将发送到错误。

  您也可以将字符串常量替换为空值。

  当记录超过为该阶段定义的最大记录长度时，该阶段将根据为该阶段配置的错误处理来处理对象。

- JSON格式

  为每个JSON对象生成一条记录。您可以处理包含多个JSON对象或单个JSON数组的JSON文件。

  当对象超过为原点定义的最大对象长度时，原点会根据为阶段配置的错误处理来处理对象。

- 记录

  为每个日志行生成一条记录。

  当一条线超过用户定义的最大线长时，原点会截断更长的线。

  您可以将处理后的日志行作为字段包含在记录中。如果日志行被截断，并且您在记录中请求日志行，则原点包括被截断的行。

  您可以定义要读取的[日志格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/LogFormats.html#concept_tr1_spd_sr)或类型。

- 网络流

  数据解析器处理器可以处理嵌入在字节数组字段中的NetFlow 5和NetFlow 9消息消息。

  在处理NetFlow消息时，该阶段会根据NetFlow版本生成不同的记录。处理NetFlow 9时，将基于NetFlow 9配置属性生成记录。有关更多信息，请参见[NetFlow数据处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/NetFlow_Overview.html#concept_thl_nnr_hbb)。

- 原虫

  为每个protobuf消息生成一条记录。默认情况下，来源假设邮件包含多个protobuf邮件。

  Protobuf消息必须与指定的消息类型匹配，并在描述符文件中进行描述。

  当记录的数据超过1 MB时，源将无法继续处理消息中的数据。源根据阶段错误处理属性处理消息，并继续读取下一条消息。

  有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。

- 系统日志消息

  数据解析器处理器可以根据[RFC 6587](https://tools.ietf.org/html/rfc6587)处理嵌入在字符串字段中的系统日志消息，但该处理器不支持方法更改。

  数据解析器处理器可以处理以下类型的系统日志消息：[RFC 5424](https://tools.ietf.org/html/rfc5424)[RFC 3164](https://tools.ietf.org/html/rfc3164)非标准通用消息，例如RFC 3339日期，没有版本数字

- XML格式

  根据用户定义的定界符元素生成记录。在根元素下直接使用XML元素或定义简化的XPath表达式。如果未定义定界符元素，则源会将XML文件视为单个记录。

  默认情况下，生成的记录包括XML属性和名称空间声明作为记录中的字段。您可以配置阶段以将它们包括在记录中作为字段属性。

  您可以在字段属性中包含每个解析的XML元素和XML属性的XPath信息。这还将每个名称空间放置在xmlns记录头属性中。**注意：** 只有在目标中使用SDC RPC数据格式时，字段属性和记录头属性才会自动写入目标系统。有关使用字段属性和记录标题属性以及如何将它们包括在记录中的更多信息，请参见[字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)和[记录标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)。

  当记录超过用户定义的最大记录长度时，原点将跳过该记录并继续处理下一条记录。它将跳过的记录发送到管道以进行错误处理。

  使用XML数据格式来处理有效的XML文档。有关XML处理的更多信息，请参见[阅读和处理XML数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_lty_42b_dy)。

  **提示：** 如果要处理无效的XML文档，则可以尝试将文本数据格式与自定义分隔符一起使用。有关更多信息，请参见 [使用自定义分隔符处理XML数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/TextCDelim.html#concept_okt_kmg_jx)。

## 配置数据解析器处理器

配置数据解析器处理器以解析嵌入在字段中的一种受支持的数据格式。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **解析器”**选项卡上，配置以下属性：

   | 数据解析器属性 | 描述                                                         |
   | :------------- | :----------------------------------------------------------- |
   | 要解析的字段   | 包含要分析的数据的字段。要处理NetFlow消息，请指定要使用的字节数组字段。要处理系统日志消息，请指定要使用的字符串字段。 |
   | 目标领域       | 解析数据的输出字段。                                         |
   | 多值行为       | 当字段中的数据包含多个值时要采取的操作：仅第一个值-返回第一个值。所有值作为列表-将所有值作为“列表”字段中的项目返回。拆分为多个记录-返回单独记录中的每个值。此选项生成多个记录，该记录对应于字段中的每个解析值。记录中的其他字段与每个记录一起保留。 |

3. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/DataParser.html#concept_g4s_frv_s1b) | 数据的数据格式：阿夫罗定界JSON格式记录NetFlow消息原虫系统日志消息XML格式 |

4. 对于Avro数据，在“ **数据格式”**选项卡上，配置以下属性：

   | Avro物业             | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | Avro模式位置         | 处理数据时要使用的Avro模式定义的位置：消息/数据包含架构-在消息中使用架构。在“管道配置”中-使用阶段配置中提供的架构。Confluent Schema Registry-从Confluent Schema Registry检索架构。在阶段配置中或在Confluent Schema Registry中使用架构可以提高性能。 |
   | Avro模式             | 用于处理数据的Avro模式定义。覆盖与数据关联的任何现有模式定义。您可以选择使用该 `runtime:loadResource`函数来加载存储在运行时资源文件中的架构定义。 |
   | 架构注册表URL        | 汇合的架构注册表URL，用于查找架构。要添加URL，请单击**添加**，然后以以下格式输入URL：`http://:` |
   | 基本身份验证用户信息 | 使用基本身份验证时连接到Confluent Schema Registry所需的用户信息。`schema.registry.basic.auth.user.info`使用以下格式从Schema Registry中的设置中输入密钥和机密 ：`:`**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 查找架构             | 在Confluent Schema Registry中查找架构的方法：主题-查找指定的Avro模式主题。架构ID-查找指定的Avro架构ID。嵌入式架构ID-查找每个消息中嵌入的Avro架构ID。覆盖与消息关联的任何现有模式定义。 |
   | 模式主题             | Avro架构需要在Confluent Schema Registry中查找。如果指定的主题具有多个架构版本，那么阶段将使用该主题的最新架构版本。要使用旧版本，请找到相应的架构ID，然后将“ **查找架构**依据 **”**属性设置为“架构ID”。 |
   | 架构编号             | 在Confluent Schema Registry中查找的Avro模式ID。              |

5. 对于定界数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 定界财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 分隔符格式类型                                               | 分隔符格式类型。使用以下选项之一：**默认CSV-**包含逗号分隔值的文件。忽略文件中的空行。**RFC4180 CSV-**严格遵循RFC4180准则的逗号分隔文件。**MS Excel CSV** -Microsoft Excel逗号分隔文件。**MySQL CSV** -MySQL逗号分隔文件。**制表符分隔的值** -包含制表符分隔的值的文件。**PostgreSQL CSV** -PostgreSQL逗号分隔文件。**PostgreSQL文本** -PostgreSQL文本文件。**自定义** -使用用户定义的定界符，转义符和引号字符的文件。**多字符**定界-使用多个用户定义的字符定界字段和行以及单个用户定义的转义和引号字符的文件。 |
   | 标题行                                                       | 指示文件是否包含标题行以及是否使用标题行。                   |
   | 允许额外的列                                                 | 使用标题行处理数据时，允许处理的记录列数超过标题行中的列数。 |
   | 额外的列前缀                                                 | 用于任何其他列的前缀。额外的列使用前缀和顺序递增的整数来命名，如下所示： ``。例如，`_extra_1`。默认值为 `_extra_`。 |
   | 最大记录长度（字符）                                         | 记录的最大长度（以字符为单位）。较长的记录无法读取。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
   | 分隔符                                                       | 自定义分隔符格式的分隔符。选择一个可用选项，或使用“其他”输入自定义字符。您可以输入使用格式为Unicode控制符\uNNNN，其中*ñ*是数字0-9或字母AF十六进制数字。例如，输入 \u0000以使用空字符作为分隔符或 \u2028使用行分隔符作为分隔符。默认为竖线字符（\|）。 |
   | 多字符字段定界符                                             | 用于分隔多字符定界符格式的字段的字符。默认值为两个竖线字符（\|\|）。 |
   | 多字符行定界符                                               | 以多字符定界符格式分隔行或记录的字符。默认值为换行符（\ n）。 |
   | 转义符                                                       | 自定义或多字符定界符格式的转义字符。                         |
   | 引用字符                                                     | 自定义或多字符定界符格式的引号字符。                         |
   | 启用评论                                                     | 自定义定界符格式允许注释的数据被忽略。                       |
   | 评论标记                                                     | 为自定义定界符格式启用注释时，标记注释的字符。               |
   | 忽略空行                                                     | 对于自定义分隔符格式，允许忽略空行。                         |
   | [根字段类型](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Delimited.html#concept_zcg_bm4_fs) | 要使用的根字段类型：列表映射-生成数据索引列表。使您能够使用标准功能来处理数据。用于新管道。列表-生成带有索引列表的记录，该列表带有标头和值的映射。需要使用定界数据功能来处理数据。仅用于维护在1.1.0之前创建的管道。 |
   | 跳过的线                                                     | 读取数据前要跳过的行数。                                     |
   | 解析NULL                                                     | 将指定的字符串常量替换为空值。                               |
   | 空常量                                                       | 字符串常量，用空值替换。                                     |
   | 字符集                                                       | 要处理的文件的字符编码。                                     |
   | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

6. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

   | JSON属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | JSON内容                                                     | JSON内容的类型。使用以下选项之一：对象数组多个物件           |
   | 最大对象长度（字符）                                         | JSON对象中的最大字符数。较长的对象将转移到管道以进行错误处理。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
   | 字符集                                                       | 要处理的文件的字符编码。                                     |
   | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

7. 对于日志数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 日志属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [日志格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/LogFormats.html) | 日志文件的格式。使用以下选项之一：通用日志格式合并日志格式Apache错误日志格式Apache访问日志自定义格式正则表达式格罗模式Log4j通用事件格式（CEF）日志事件扩展格式（LEEF） |
   | 最大线长                                                     | 日志行的最大长度。原点将截断较长的行。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
   | 保留原始行                                                   | 确定如何处理原始日志行。选择以将原始日志行作为字段包含在结果记录中。默认情况下，原始行被丢弃。 |
   | 字符集                                                       | 要处理的文件的字符编码。                                     |
   | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

   - 当选择“ **Apache访问日志自定义格式”时**，请使用Apache日志格式字符串定义“ **自定义日志格式”**。

   - 选择“ **正则表达式”时**，输入描述日志格式的正则表达式，然后将要包括的字段映射到每个正则表达式组。

   - 选择

     Grok Pattern时

     ，可以使用 

     Grok Pattern Definition

     字段定义自定义grok模式。您可以在每行上定义一个模式。

     在“ **Grok模式”**字段中，输入用于解析日志的模式。您可以使用预定义的grok模式，也可以使用**Grok Pattern Definition中定义的**模式创建自定义grok模式 。

     有关定义grok模式和支持的grok模式的更多信息，请参见[定义Grok模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-GrokPatterns/GrokPatterns_title.html#concept_vdk_xjb_wr)。

   - 选择

     Log4j时

     ，定义以下属性：

     | Log4j属性          | 描述                                                         |
     | :----------------- | :----------------------------------------------------------- |
     | 解析错误           | 确定如何处理无法解析的信息：跳过并记录错误-跳过读取行并记录阶段错误。跳过，没有错误-跳过读取行并且不记录错误。包括为堆栈跟踪-包含无法解析为先前读取的日志行的堆栈跟踪的信息。该信息将添加到最后一个有效日志行的消息字段中。 |
     | 使用自定义日志格式 | 允许您定义自定义日志格式。                                   |
     | 自定义Log4J格式    | 使用log4j变量定义自定义日志格式。                            |

8. 对于NetFlow数据，在“ **数据格式”**选项卡上，配置以下属性：

   处理早期版本的NetFlow数据时，将忽略这些属性。

   | Netflow 9属性                                                | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [记录生成方式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/NetFlow_Overview.html#concept_jdh_hxk_3bb) | 确定要包含在记录中的值的类型。选择以下选项之一：仅原始仅解释原始和解释 |
   | 缓存中的最大模板数                                           | 模板缓存中存储的最大模板数。有关模板的更多信息，请参见[缓存NetFlow 9模板](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/NetFlow_Overview.html#concept_ivr_j1l_3bb)。对于无限的缓存大小，默认值为-1。 |
   | 模板缓存超时（毫秒）                                         | 缓存空闲模板的最大毫秒数。超过指定时间未使用的模板将从缓存中逐出。有关模板的更多信息，请参见 [缓存NetFlow 9模板](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/NetFlow_Overview.html#concept_ivr_j1l_3bb)。无限期缓存模板的默认值为-1。 |

9. 对于protobuf数据，在“ **数据格式”**选项卡上，配置以下属性：

   | Protobuf属性       | 描述                                                         |
   | :----------------- | :----------------------------------------------------------- |
   | Protobuf描述符文件 | 要使用的描述符文件（.desc）。描述符文件必须位于Data Collector资源目录中 `$SDC_RESOURCES`。有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。 |
   | 讯息类型           | 读取数据时使用的消息类型的全限定名称。使用以下格式： `.`。使用在描述符文件中定义的消息类型。 |
   | 分隔消息           | 指示一条消息是否可能包含多个protobuf消息。                   |

10. 对于XML数据，在“ **数据格式”**选项卡上，配置以下属性：

    | XML属性                                                      | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [分隔元素](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_tmc_4bc_dy) | 用于生成记录的定界符。省略定界符，将整个XML文档视为一条记录。使用以下之一：在根元素正下方的XML元素。使用不带尖括号（<>）的XML元素名称。例如，用msg代替<msg>。一个简化的XPath表达式，指定要使用的数据。使用简化的XPath表达式访问XML文档中更深的数据或需要更复杂访问方法的数据。有关有效语法的更多信息，请参见[简化的XPath语法](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_tmc_4bc_dy)。 |
    | 最大记录长度（字符）                                         | 记录中的最大字符数。较长的记录将转移到管道以进行错误处理。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
    | 字符集                                                       | 要处理的文件的字符编码。                                     |
    | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |