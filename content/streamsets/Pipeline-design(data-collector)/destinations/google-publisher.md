# Google Pub / Sub Publisher

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310184249948.png) 资料收集器

Google Pub / Sub Publisher目标将消息发布到Google Pub / Sub主题。您可以使用其他目标写入[Google BigQuery](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/BigQuery.html#concept_hj4_brk_dbb)，[Google Bigtable](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Bigtable.html#concept_pl5_tmq_tx)和[Google Cloud Storage](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/GCS.html#concept_p4n_jrl_nbb)。

配置目标时，您可以定义要向其写入消息的Google Pub / Sub主题ID。您还定义了用于连接到Google Pub / Sub的项目和凭据提供程序。目标可以从Google应用程序默认凭据或Google Cloud服务帐户凭据文件检索凭据。

默认情况下，Google Pub / Sub Publisher目标会批量写入消息。使用高级属性，可以配置触发写入新批处理或禁用批处理以分别写入消息的条件。您还可以配置目标在读取消息时比写入消息时快采取的操作。

Google Pub / Sub消息包含有效载荷和描述有效载荷内容的可选用户定义属性。当记录包含记录头属性时，Google Pub / Sub Publisher目标将记录头属性包含在邮件属性中。目标在消息属性中不包括内部记录头属性。

有关记录标题属性的更多信息，请参见[记录标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)。

## 证书

当Google Pub / Sub Publisher目的地将消息发布到Google Pub / Sub主题时，它必须将凭据传递给Google Pub / Sub。配置目标以从Google应用程序默认凭据或Google Cloud服务帐户凭据文件检索凭据。

### 默认凭据提供程序

配置为使用Google应用程序默认凭据时，目标将检查`GOOGLE_APPLICATION_CREDENTIALS`环境变量中定义的凭据文件 。如果环境变量不存在，并且Data Collector在Google Cloud Platform（GCP）中的虚拟机（VM）上运行，则目标使用与虚拟机实例关联的内置服务帐户。

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

### 服务帐户凭据文件（JSON）

当配置为使用Google Cloud服务帐户凭据文件时，目标将检查目标属性中定义的文件。

完成以下步骤以使用服务帐户凭据文件：

1. 生成JSON格式的服务帐户凭据文件。

   使用Google Cloud Platform Console或`gcloud`命令行工具来生成和下载凭据文件。有关更多信息，请参阅Google Cloud Platform文档中的[生成服务帐户凭据](https://cloud.google.com/storage/docs/authentication#generating-a-private-key)。

2. 将生成的凭证文件存储在

   Data Collector

   计算机上。

   最佳做法是将文件存储在 Data Collector资源目录中 `$SDC_RESOURCES`。

3. 在该阶段的“ **凭据”**选项卡上，为凭据提供者选择“ **服务帐户凭据文件”**，然后输入凭据文件的路径。

## 资料格式

Google Pub / Sub Publisher根据您选择的数据格式将数据写入Google Pub / Sub。您可以使用以下数据格式：

- 阿夫罗

  该阶段基于Avro模式写入记录。您可以使用以下方法之一来指定Avro模式定义的位置：

  **在“管道配置”中** -使用您在阶段配置中提供的架构。**在记录标题中** -使用avroSchema记录标题属性中包含的架构。**Confluent Schema Registry-**从Confluent Schema Registry检索架构。Confluent Schema Registry是Avro架构的分布式存储层。您可以配置目标以通过架构ID或主题在Confluent Schema Registry中查找架构。如果在阶段或记录头属性中使用Avro架构，则可以选择配置阶段以向Confluent Schema Registry注册Avro架构。您还可以选择在消息中包括架构定义。省略模式定义可以提高性能，但是需要适当的模式管理，以避免丢失与数据关联的模式的跟踪。

  您可以在输出中包括Avro模式。

  您还可以使用Avro支持的压缩编解码器压缩数据。使用Avro压缩时，请避免在阶段中配置任何其他压缩属性。

- 二元

  该阶段将二进制数据写入记录中的单个字段。

- 定界

  目标将记录写为定界数据。使用此数据格式时，根字段必须是list或list-map。

  您可以使用以下定界格式类型：**默认CSV-**包含逗号分隔值的文件。忽略文件中的空行。**RFC4180 CSV-**严格遵循RFC4180准则的逗号分隔文件。**MS Excel CSV** -Microsoft Excel逗号分隔文件。**MySQL CSV** -MySQL逗号分隔文件。**制表符分隔的值** -包含制表符分隔的值的文件。**PostgreSQL CSV** -PostgreSQL逗号分隔文件。**PostgreSQL文本** -PostgreSQL文本文件。**自定义** -使用用户定义的定界符，转义符和引号字符的文件。**多字符**定界-使用多个用户定义的字符定界字段和行以及单个用户定义的转义和引号字符的文件。

- JSON格式

  目标将记录作为JSON数据写入。您可以使用以下格式之一：数组-每个文件都包含一个数组。在数组中，每个元素都是每个记录的JSON表示形式。多个对象-每个文件都包含多个JSON对象。每个对象都是记录的JSON表示形式。

- 原虫

  在一条消息中写入一条记录。在描述符文件中使用用户定义的消息类型和消息类型的定义来生成消息。

  有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。

- SDC记录

  目标以SDC记录数据格式写入记录。

- 文本

  目标将数据从单个文本字段写入目标系统。配置阶段时，请选择要使用的字段。

  您可以配置字符以用作记录分隔符。默认情况下，目标使用UNIX样式的行尾（\ n）分隔记录。

  当记录不包含选定的文本字段时，目标可以将缺少的字段报告为错误或忽略缺少的字段。默认情况下，目标报告错误。

  当配置为忽略缺少的文本字段时，目标位置可以丢弃该记录或写入记录分隔符以为该记录创建一个空行。默认情况下，目标丢弃记录。

- XML格式

  目标为每个记录创建一个有效的XML文档。目标要求记录具有一个包含其余记录数据的单个根字段。有关如何完成此操作的详细信息和建议，请参阅[记录结构要求](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WritingXML.html#concept_cmn_hml_r1b)。目的地可以包括缩进以产生人类可读的文档。它还可以验证所生成的XML是否符合指定的架构定义。具有无效架构的记录将根据为目标配置的错误处理进行处理。

## 配置Google发布/订阅发布者目标

配置Google Pub / Sub发布者目标，以将消息写入Google Pub / Sub主题。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“发布**/订阅”**选项卡上，配置以下属性：

   | 发布/订阅属性 | 描述                                       |
   | :------------ | :----------------------------------------- |
   | 主题编号      | Google Pub / Sub主题ID，可向其中写入消息。 |

3. 在“ **凭据”**选项卡上，配置以下属性：

   | 凭证属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 专案编号                                                     | 要连接的Google Pub / Sub项目ID。                             |
   | [凭证提供者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/PubSubPublisher.html#concept_snf_1wq_v1b) | 用于连接到Google Pub / Sub的凭据提供者：默认凭证提供者服务帐户凭证文件（JSON） |
   | 凭证文件路径（JSON）                                         | 使用Google Cloud服务帐户凭据文件时，该路径是目标用来连接到Google Pub / Sub的文件的路径。凭证文件必须是JSON文件。输入相对于Data Collector资源目录`$SDC_RESOURCES`的路径，或输入绝对路径。 |

4. 在“ **高级”**选项卡上，配置以下属性：

   | 先进物业             | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | 请求字节阈值         | 触发批量发送消息的累积消息大小。以字节为单位指定。默认值为1000。 |
   | 邮件计数阈值         | 触发批量发送消息的累积消息数。默认值为100。                  |
   | 默认延迟阈值（毫秒） | 从第一条消息到达后开始触发批量发送消息所经过的时间。以毫秒为单位指定。默认值为1。 |
   | 批量启用             | 选择此选项可让目标分批发送消息。禁用后，目标将忽略阈值属性单独写入每个消息。 |
   | 最大未清邮件数       | 在采取措施控制消息流之前，目标存储在内存中的未处理消息数。当目标读取消息的速度比写入消息的速度快时，您可能希望控制消息的流向。设置为0永远不会基于消息计数来控制流。若要在使用批处理时控制消息流，请将其设置为大于消息计数阈值的数字。 |
   | 最大未清请求字节数   | 在采取措施控制消息流之前，目标存储在内存中的未处理字节数。设置为0永远不会根据消息大小控制流。若要在使用批处理时控制消息流，请将其设置为大于请求字节阈值的数字。 |
   | 超限行为             | 未处理邮件的数量或大小超过指定的限制时采取的措施。选择以下选项之一：引发异常-触发管道错误处理。阻止-停止处理新消息，直到已成功写入存储的消息。忽略-丢弃新消息，直到已成功写入存储的消息。 |

5. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/PubSubPublisher.html#concept_qwl_lyq_v1b) | 要写入的数据格式。使用以下选项之一：阿夫罗二元定界JSON格式原虫[SDC记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/SDCRecordFormat.html#concept_qkk_mwk_br)文本XML格式 |

6. 对于Avro数据，在“ **数据格式”**选项卡上，配置以下属性：

   | Avro物业             | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | Avro模式位置         | 写入数据时要使用的Avro模式定义的位置：在“管道配置”中-使用您在阶段配置中提供的架构。在记录头中-在avroSchema [记录头属性中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_lmn_gdc_1w)使用架构 。仅在为所有记录定义avroSchema属性时使用。Confluent Schema Registry-从Confluent Schema Registry检索架构。 |
   | Avro模式             | 用于写入数据的Avro模式定义。您可以选择使用该`runtime:loadResource` 函数来加载存储在运行时资源文件中的架构定义。 |
   | 注册架构             | 向Confluent Schema Registry注册新的Avro架构。                |
   | 架构注册表URL        | 汇合的架构注册表URL，用于查找架构或注册新架构。要添加URL，请单击 **添加**，然后以以下格式输入URL：`http://:` |
   | 基本身份验证用户信息 | 使用基本身份验证时连接到Confluent Schema Registry所需的用户信息。`schema.registry.basic.auth.user.info`使用以下格式从Schema Registry中的设置中输入密钥和机密 ：`:`**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 查找架构             | 在Confluent Schema Registry中查找架构的方法：主题-查找指定的Avro模式主题。架构ID-查找指定的Avro架构ID。 |
   | 模式主题             | Avro架构可以在Confluent Schema Registry中查找或注册。如果要查找的指定主题具有多个架构版本，则阶段将使用该主题的最新架构版本。要使用旧版本，请找到相应的架构ID，然后将“ **查找架构**依据**”**属性设置为“架构ID”。 |
   | 架构编号             | 在Confluent Schema Registry中查找的Avro模式ID。              |
   | 包含架构             | 在每个消息中包含架构。**注意：**省略模式定义可以提高性能，但是需要适当的模式管理，以避免丢失与数据关联的模式的跟踪。 |
   | Avro压缩编解码器     | 要使用的Avro压缩类型。使用Avro压缩时，请勿在目标中启用其他可用压缩。 |

7. 对于二进制数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 二元性质     | 描述                   |
   | :----------- | :--------------------- |
   | 二进制场路径 | 包含二进制数据的字段。 |

8. 对于定界数据，在“ **数据格式”**选项卡上，配置以下属性：

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

9. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

   | JSON属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | JSON内容 | 写入JSON数据的方法：JSON对象数组-每个文件都包含一个数组。在数组中，每个元素都是每个记录的JSON表示形式。多个JSON对象-每个文件包含多个JSON对象。每个对象都是记录的JSON表示形式。 |
   | 字符集   | 写入数据时使用的字符集。                                     |

10. 对于protobuf数据，在“ **数据格式”**选项卡上，配置以下属性：

    | Protobuf属性       | 描述                                                         |
    | :----------------- | :----------------------------------------------------------- |
    | Protobuf描述符文件 | 要使用的描述符文件（.desc）。描述符文件必须位于Data Collector资源目录中`$SDC_RESOURCES`。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。 |
    | 讯息类型           | 写入数据时使用的消息类型的全限定名称。使用以下格式： `.`。使用在描述符文件中定义的消息类型。 |
    | 写定界符           | 在每条消息后写一个定界符。                                   |

11. 对于文本数据，在“ **数据格式”**选项卡上，配置以下属性：

    | 文字属性                       | 描述                                                         |
    | :----------------------------- | :----------------------------------------------------------- |
    | 文字栏位路径                   | 包含要写入的文本数据的字段。所有数据必须合并到指定字段中。   |
    | 记录分隔符                     | 用于分隔记录的字符。使用任何有效的Java字符串文字。例如，当写入Windows时，您可能会\r\n用来分隔记录。默认情况下，目标使用 \n。 |
    | 在失落的田野上                 | 当记录不包含文本字段时，确定目标是将丢失的字段报告为错误还是忽略该丢失的字段。 |
    | 如果没有文本，则插入记录分隔符 | 当配置为忽略缺少的文本字段时，插入配置的记录分隔符字符串以创建一个空行。如果未选择，则丢弃没有文本字段的记录。 |
    | 字符集                         | 写入数据时使用的字符集。                                     |

12. 对于XML数据，在“ **数据格式”**选项卡上，配置以下属性：

    | XML属性  | 描述                                                         |
    | :------- | :----------------------------------------------------------- |
    | 漂亮格式 | 添加缩进以使生成的XML文档更易于阅读。相应地增加记录大小。    |
    | 验证架构 | 验证生成的XML是否符合指定的架构定义。具有无效架构的记录将根据为目标配置的错误处理进行处理。**要点：**无论是否验证XML模式，目的地都需要特定格式的记录。有关更多信息，请参见[记录结构要求](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WritingXML.html#concept_cmn_hml_r1b)。 |
    | XML模式  | 用于验证记录的XML模式。                                      |