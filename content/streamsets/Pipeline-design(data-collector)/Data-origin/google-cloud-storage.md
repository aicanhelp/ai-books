# 谷歌云存储

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310113616092.png) 资料收集器

Google Cloud Storage原始读取存储在Google Cloud Storage中的对象。这些对象必须完全写入并驻留在单个存储桶中。对象名称必须共享前缀模式。

借助Google Cloud Storage的来源，您可以定义存储桶，前缀模式和可选的公共前缀。这些属性确定原始处理的对象。

您还定义了用于连接到Google Cloud Storage的项目和凭据提供程序。源可以从Google应用程序默认凭据或Google Cloud服务帐户凭据文件中检索凭据。

处理对象时发生错误时，原点可以保留，存档或删除对象。归档时，原点可以复制或移动对象。

当管道停止时，Google Cloud Storage原始记录会记录停止读取的位置。当管道再次启动时，原点将从默认情况下停止的地方继续进行处理。您可以重置原点以处理所有请求的对象。

源可以为事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

## 证书

在读取Google Cloud Storage中的对象之前，Google Cloud Storage源必须将凭据传递给Google Cloud Storage。配置来源以从Google应用程序默认凭据或Google Cloud服务帐户凭据文件中检索凭据。

### 默认凭据提供程序

配置为使用Google Application Default Credentials时，来源检查`GOOGLE_APPLICATION_CREDENTIALS`环境变量中定义的凭据文件 。如果环境变量不存在，并且Data Collector在Google Cloud Platform（GCP）中的虚拟机（VM）上运行，则源使用与虚拟机实例关联的内置服务帐户。

有关默认凭据的更多信息，请参阅Google Developer文档中的Google [Application默认凭据](https://developers.google.com/identity/protocols/application-default-credentials)。

完成以下步骤以在环境变量中定义凭证文件：

1. 使用Google Cloud Platform Console或 

   ```
   gcloud
   ```

   命令行工具创建一个Google服务帐户，并使您的应用程序使用该帐户进行API访问。

   例如，要使用命令行工具，请运行以下命令：

   ```
   gcloud iam service-accounts create my-account
   gcloud iam service-accounts keys create key.json --iam-account=my-account@my-project.iam.gserviceaccount.com
   ```

2. 将生成的凭证文件存储在Data Collector计算机上。

3. 将

   ```
   GOOGLE_APPLICATION_CREDENTIALS
   ```

    环境变量添加到适当的文件，并将其指向凭据文件。

   使用安装类型所需的方法。

   如下设置环境变量：

   ```
   export GOOGLE_APPLICATION_CREDENTIALS="/var/lib/sdc-resources/keyfile.json"
   ```

4. 重新启动Data Collector以启用更改。

5. 在该阶段的“ **凭据”**选项卡上， 为凭据提供者选择“ **默认凭据提供**者”。

### 服务帐户凭据（JSON）

配置为使用Google Cloud Service帐户凭据文件时，原点检查在原点属性中定义的文件。

完成以下步骤以使用服务帐户凭据文件：

1. 生成JSON格式的服务帐户凭据文件。

   使用Google Cloud Platform Console或`gcloud`命令行工具来生成和下载凭据文件。有关更多信息，请参阅Google Cloud Platform文档中的[生成服务帐户凭据](https://cloud.google.com/storage/docs/authentication#generating-a-private-key)。

2. 将生成的凭证文件存储在

   Data Collector

   计算机上。

   最佳做法是将文件存储在 Data Collector资源目录中 `$SDC_RESOURCES`。

3. 在该阶段的“ **凭据”**选项卡上，为凭据提供者选择“ **服务帐户凭据文件”**，然后输入凭据文件的路径。

## 通用前缀，前缀模式和通配符

Google Cloud Storage原点将公共前缀附加到前缀模式，以定义原点处理的对象。您可以指定精确的前缀模式，也可以使用Ant样式的路径模式来递归读取多个对象。

蚂蚁风格的路径模式可以包含以下通配符：

- 问号（？）以匹配单个字符
- 星号（*）匹配零个或多个字符
- 双星号（**）可以匹配零个或多个目录

例如，要处理所有日志文件`US/East/MD/`和所有嵌套前缀，可以使用以下公共前缀和前缀模式：

```
Common Prefix: US/East/MD/
Prefix Pattern: **/*.log
```

如果要包括的未命名的嵌套前缀出现在层次结构中较早的位置（例如）` US/**/weblogs/`，则可以在前缀模式中包括嵌套前缀，或在前缀模式中定义整个层次结构，如下所示：

```
Common Prefix: US/
Prefix Pattern: **/weblogs/*.log

Common Prefix: 
Prefix Pattern: US/**/weblogs/*.log
```

## 事件产生

Google Cloud Storage原始服务器在完成所有可用数据的处理并且已配置的批处理等待时间过去时，可以生成事件。

Google Cloud Storage事件可以任何逻辑方式使用。例如：

- 当原始完成处理可用数据时，使用Pipeline Finisher执行程序停止管道并将管道转换为Finished状态。

  重新启动由Pipeline Finisher执行程序停止的管道时，原点将从上次保存的偏移开始继续处理，除非您重置原点。

  有关示例，请参见[案例研究：停止管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_kff_ykv_lz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

由Google Cloud Storage来源生成的事件记录具有以下与事件相关的记录标题属性。记录标题属性存储为字符串值：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型：no-more-data-原点完成对所有可用对象的处理并且经过了为“批处理等待时间”配置的秒数后生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

Google Cloud Storage来源可以生成以下事件记录：

- 没有更多数据

  当Google Cloud Storage原点完成对所有可用记录的处理并且经过为“批处理等待时间”配置的秒数，而未显示任何新对象时，将生成no-more-data事件记录。

  由源生成的无数据事件记录将sdc.event.type设置为无数据，并包含以下字段：事件记录字段描述记录数自管道启动或自上一次创建no-more-data事件以来成功生成的记录数。错误计数自管道启动或自上一次创建no-more-data事件以来生成的错误记录数。文件计数原点尝试处理的对象数。可以包含无法处理或未完全处理的对象。

## 资料格式

Google Cloud Storage原始数据格式会根据数据格式进行不同的处理。源处理以下类型的数据：

- 阿夫罗

  为每个Avro记录生成一条记录。每个小数字段都包含 `precision`和`scale` [字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)。

  该阶段在`avroSchema` [记录头属性中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)包括Avro模式 。您可以使用以下方法之一来指定Avro模式定义的位置：**消息/数据包含架构** -在文件中使用架构。**在“管道配置”中** -使用您在阶段配置属性中提供的架构。**Confluent Schema Registry-**从Confluent Schema Registry检索架构。Confluent Schema Registry是Avro架构的分布式存储层。您可以配置阶段以通过阶段配置中指定的模式ID或主题在Confluent Schema Registry中查找模式。

  在阶段配置中使用架构或从Confluent Schema Registry检索架构会覆盖文件中可能包含的任何架构，并可以提高性能。

  该阶段读取不需要Avro支持的压缩编解码器压缩的文件，而无需进行其他配置。要使阶段能够读取其他编解码器压缩的文件，请在阶段中使用compression format属性。

- 定界

  为每个定界线生成一条记录。您可以使用以下定界格式类型：**默认CSV-**包含逗号分隔值的文件。忽略文件中的空行。**RFC4180 CSV-**严格遵循RFC4180准则的逗号分隔文件。**MS Excel CSV** -Microsoft Excel逗号分隔文件。**MySQL CSV** -MySQL逗号分隔文件。**制表符分隔的值** -包含制表符分隔的值的文件。**PostgreSQL CSV** -PostgreSQL逗号分隔文件。**PostgreSQL文本** -PostgreSQL文本文件。**自定义** -使用用户定义的定界符，转义符和引号字符的文件。**多字符**定界-使用多个用户定义的字符定界字段和行以及单个用户定义的转义和引号字符的文件。

  您可以将列表或列表映射根字段类型用于定界数据，并且可以选择在标题行中包括字段名称（如果有）。有关根字段类型的更多信息，请参见定界[数据根字段类型](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Delimited.html#concept_zcg_bm4_fs)。

  使用标题行时，可以启用带有其他列的记录处理。其他列使用自定义的前缀和顺序递增的顺序整数，如命名 `_extra_1`， `_extra_2`。当您禁止其他列时，包含其他列的记录将发送到错误。

  您也可以将字符串常量替换为空值。

  当记录超过为阶段定义的最大记录长度时，原点 无法继续读取文件。已经从文件中读取的记录将传递到管道。然后，原点的行为基于为该阶段配置的错误处理：丢弃-原点继续处理下一个文件，将部分处理的文件保留在目录中。错误-原点继续处理下一个文件。如果为该阶段配置了后处理错误目录，则原点会将部分处理的文件移动到错误目录。否则，它将文件保留在目录中。停止管道-原点停止管道。

- 电子表格

  为文件中的每一行生成一条记录。可以处理 `.xls`或`.xlsx` 归档。您可以指定文件是否包含标题行以及是否忽略标题行。标题行必须是文件的第一行。 无法识别垂直标题列。原点无法处理具有大量行的Excel文件。您可以在Excel中将这些文件另存为CSV文件，然后使用原点处理定界数据格式。

- JSON格式

  为每个JSON对象生成一条记录。您可以处理包含多个JSON对象或单个JSON数组的JSON文件。

  当对象超过为原点定义的最大对象长度时，原点将无法继续处理文件中的数据。已经从文件处理的记录将传递到管道。然后，原点的行为基于为该阶段配置的错误处理：丢弃-原点继续处理下一个文件，将部分处理的文件保留在目录中。错误-原点继续处理下一个文件。如果为该阶段配置了后处理错误目录，则原点会将部分处理的文件移动到错误目录。否则，它将文件保留在目录中。停止管道-原点停止管道。

- 记录

  为每个日志行生成一条记录。

  当一条线超过用户定义的最大线长时，原点会截断更长的线。

  您可以将处理后的日志行作为字段包含在记录中。如果日志行被截断，并且您在记录中请求日志行，则原点包括被截断的行。

  您可以定义要读取的[日志格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/LogFormats.html#concept_tr1_spd_sr)或类型。

- 原虫

  为每个protobuf消息生成一条记录。

  Protobuf消息必须与指定的消息类型匹配，并在描述符文件中进行描述。

  当记录数据超过1 MB时，原始数据将无法继续处理文件中的数据。源根据文件错误处理属性处理文件，并继续读取下一个文件。

  有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。

- SDC记录

  为每条记录生成一条记录。用于处理由数据收集器 管道使用SDC记录数据格式生成的记录。

  对于错误记录，原点提供从原始管道中的原点读取的原始记录，以及可用于更正记录的错误信息。

  处理错误记录时，来源希望原始管道生成的错误文件名和内容。

- 文本

  根据自定义定界符为每行文本或每段文本生成一条记录。

  当线或线段超过为原点定义的最大线长时，原点会截断它。原点添加了一个名为Truncated的布尔字段，以指示该行是否被截断。

  有关使用自定义定界符处理文本的更多信息，请参见[使用自定义定界符的文本数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/TextCDelim.html#concept_lg2_gcg_jx)。

- 整个档案

  将整个文件从源系统流式传输到目标系统。您可以指定传输速率或使用所有可用资源来执行传输。

  源使用校验和来验证数据传输的完整性。

  原点生成两个字段：一个用于文件引用，另一个用于文件信息。有关更多信息，请参见[整个文件数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_nfc_qkh_xw)。

## 配置Google Cloud Storage原始

配置Google Cloud Storage源以从Google Cloud Storage中的对象读取数据。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/GCS.html#concept_fkf_bmn_sbb) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在**GCS**标签上，配置以下属性：

   | GCS物业                                                      | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 桶                                                           | 包含要读取的对象的存储桶。**注意：**存储桶名称必须符合DNS。有关存储区命名约定的更多信息，请参阅[Google Cloud Storage文档](https://cloud.google.com/storage/docs/naming)。 |
   | [通用前缀](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/GCS.html#concept_gmw_2tn_sbb) | 描述对象位置的可选通用前缀。定义后，公共前缀将充当前缀模式的根。 |
   | [前缀模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/GCS.html#concept_gmw_2tn_sbb) | 前缀模式，描述要处理的对象。您可以包括对象的完整路径。您还可以使用Ant样式的路径模式来递归读取对象。 |
   | 最大批次大小（记录）                                         | 一次处理的最大记录数。接受的值最高为Data Collector的最大批处理大小。默认值是1000 数据采集器默认设置为1000。 |
   | [批处理等待时间（毫秒）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ypd_vgr_5q) | 发送部分或空批次之前要等待的毫秒数。                         |

3. 在“ **错误处理”**选项卡上，配置以下属性：

   | 错误处理属性 | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 错误处理选项 | 处理对象时发生错误时采取的措施：无-将对象保持在原位。存档-将对象复制或移动到另一个前缀或存储桶。删除-删除对象。 |
   | 存档选项     | 归档无法处理的对象时采取的措施。您可以将对象复制或移动到另一个前缀或存储桶。使用其他前缀时，请输入前缀。使用其他存储桶时，请输入前缀和存储桶。复制对象会将原始对象保留在原位。 |
   | 错误前缀     | 不能处理的对象的前缀。                                       |
   | 错误桶       | 为无法处理的对象存储桶。                                     |

4. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/GCS.html#concept_czx_ltn_sbb) | 源文件的数据格式。使用以下格式之一：阿夫罗定界电子表格JSON格式记录原虫[SDC记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/SDCRecordFormat.html#concept_qkk_mwk_br)文本[整个档案](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_nfc_qkh_xw) |

5. 对于Avro数据，在“ **数据格式”**选项卡上，配置以下属性：

   | Avro物业             | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | Avro模式位置         | 处理数据时要使用的Avro模式定义的位置：消息/数据包含架构-在文件中使用架构。在“管道配置”中-使用阶段配置中提供的架构。Confluent Schema Registry-从Confluent Schema Registry检索架构。在阶段配置中或在Confluent Schema Registry中使用架构可以提高性能。 |
   | Avro模式             | 用于处理数据的Avro模式定义。覆盖与数据关联的任何现有模式定义。您可以选择使用该 `runtime:loadResource`函数来加载存储在运行时资源文件中的架构定义。 |
   | 架构注册表URL        | 汇合的架构注册表URL，用于查找架构。要添加URL，请单击**添加**，然后以以下格式输入URL：`http://:` |
   | 基本身份验证用户信息 | 使用基本身份验证时连接到Confluent Schema Registry所需的用户信息。`schema.registry.basic.auth.user.info`使用以下格式从Schema Registry中的设置中输入密钥和机密 ：`:`**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 查找架构             | 在Confluent Schema Registry中查找架构的方法：主题-查找指定的Avro模式主题。架构ID-查找指定的Avro架构ID。覆盖与数据关联的任何现有模式定义。 |
   | 模式主题             | Avro架构需要在Confluent Schema Registry中查找。如果指定的主题具有多个架构版本，则源使用该主题的最新架构版本。要使用旧版本，请找到相应的架构ID，然后将“ **查找架构**依据 **”**属性设置为“架构ID”。 |
   | 架构编号             | 在Confluent Schema Registry中查找的Avro模式ID。              |

6. 对于定界数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 定界财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
   | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |
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

7. 对于Excel文件，在“ **数据格式”**选项卡上，配置以下属性：

   | Excel属性                                                    | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [Excel标头选项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Excel.html) | 指示文件是否包含标题行以及是否忽略标题行。标题行必须是文件的第一行。 |

8. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

   | JSON属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
   | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |
   | JSON内容                                                     | JSON内容的类型。使用以下选项之一：对象数组多个物件           |
   | 最大对象长度（字符）                                         | JSON对象中的最大字符数。较长的对象将转移到管道以进行错误处理。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
   | 字符集                                                       | 要处理的文件的字符编码。                                     |
   | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

9. 对于日志数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 日志属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
   | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |
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

10. 对于protobuf数据，在“ **数据格式”**选项卡上，配置以下属性：

    | Protobuf属性                                                 | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
    | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |
    | Protobuf描述符文件                                           | 要使用的描述符文件（.desc）。描述符文件必须位于Data Collector资源目录中`$SDC_RESOURCES`。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。 |
    | 讯息类型                                                     | 读取数据时使用的消息类型的全限定名称。使用以下格式： `.`。使用在描述符文件中定义的消息类型。 |
    | 分隔消息                                                     | 指示一个文件是否可能包含多个protobuf消息。                   |

11. 对于“ SDC记录”数据，在“ **数据格式”**选项卡上，配置以下属性：

    | SDC记录属性                                                  | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
    | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |

12. 对于文本数据，在“ **数据格式”**选项卡上，配置以下属性：

    | 文字属性                                                     | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
    | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |
    | 最大线长                                                     | 一行允许的最大字符数。较长的行被截断。向记录添加一个布尔字段，以指示该记录是否被截断。字段名称为“截断”。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
    | [使用自定义分隔符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/TextCDelim.html#concept_lg2_gcg_jx) | 使用自定义定界符来定义记录而不是换行符。                     |
    | 自定义定界符                                                 | 用于定义记录的一个或多个字符。                               |
    | 包括自定义定界符                                             | 在记录中包括定界符。                                         |
    | 字符集                                                       | 要处理的文件的字符编码。                                     |
    | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

13. 对于整个文件，在“ **数据格式”**选项卡上，配置以下属性：

    | 整个文件属性                                                 | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 缓冲区大小（字节）                                           | 用于传输数据的缓冲区大小。                                   |
    | [每秒速率](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_prp_jzd_py) | 要使用的传输速率。输入数字以指定速率（以字节/秒为单位）。使用表达式指定每秒使用不同度量单位的速率，例如$ {5 * MB}。使用-1退出此属性。默认情况下，原点不使用传输速率。 |
    | 验证校验和                                                   | 在读取期间验证校验和。                                       |

14. 在“ **凭据”**选项卡上，配置以下属性：

    | 凭证属性                                                     | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 专案编号                                                     | 要连接的Google Cloud Storage项目ID。                         |
    | [凭证提供者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/GCS.html#concept_g1r_5rn_sbb) | 用于连接到Google Cloud Storage的凭据提供商：默认凭证提供者服务帐户凭证文件（JSON） |
    | 凭证文件路径（JSON）                                         | 使用Google Cloud服务帐户凭据文件时，原始文件用于连接到Google Cloud Storage的文件的路径。凭证文件必须是JSON文件。输入相对于Data Collector资源目录`$SDC_RESOURCES`的路径，或输入绝对路径。 |