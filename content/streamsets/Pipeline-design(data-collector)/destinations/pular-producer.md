# 脉冲星生产者

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310202100187.png) 资料收集器

Pulsar Producer目标将数据写入Apache Pulsar集群中的主题。Pulsar生产者目的地附加到主题，并将消息发布到Pulsar代理进行处理。

当配置Pulsar Producer目标时，您将定义URL以连接到Pulsar。您还定义了将消息发布到的主题。如果在配置的主题名称中包含表达式，则可以配置目标以将消息发布到单个主题或多个主题。

您可以配置目标以使用Pulsar安全功能。您还可以根据需要配置高级属性，例如在发布消息时要使用的分区或压缩类型，或者目标是异步还是同步发布消息。

有关Pulsar主题和生产者的更多信息，请参阅[Apache Pulsar文档](https://pulsar.incubator.apache.org/docs/en/concepts-messaging/)。

## 启用安全性

如果Pulsar群集使用安全功能，则必须将Pulsar Producer目标配置为使用相同的安全功能连接到Pulsar。

Pulsar群集可以使用以下安全功能：

- TLS传输加密

  为[TLS传输加密](https://pulsar.incubator.apache.org/docs/en/security-tls-transport/)配置后，Pulsar群集将使用TLS加密Pulsar服务器与客户端之间的所有流量。Pulsar服务器使用密钥和证书，客户端使用该密钥和证书来验证服务器的身份。

- 相互TLS身份验证

  为TLS传输加密配置后，可以将Pulsar群集配置为使用[相互TLS身份验证](https://pulsar.incubator.apache.org/docs/en/security-tls-authentication/)。通过相互身份验证，客户端还使用服务器用来验证客户端身份的密钥和证书。

1. 在阶段的“ **Pulsar”**选项卡上，将“ **Pulsar URL”**属性设置为代理服务的安全URL。

   URL使用以下格式：

   ```
   pulsar+ssl://<host name>:<broker service TLS port>/
   ```

   例如：

   ```
   pulsar+ssl://pulsar.us-west.example.com:6651/
   ```

2. 在阶段的“ **安全性”**选项卡上，选择“ **启用TLS”**。

3. 将包含签署Pulsar群集证书的证书颁发机构（CA）的PEM文件存储在Data Collector资源目录$ SDC_RESOURCES中。

   有关为Pulsar集群创建证书的信息，请参阅 [Pulsar文档](https://pulsar.incubator.apache.org/docs/en/security-tls-authentication/)。

4. 在阶段的“ **安全性”**选项卡上，在“ **CA证书PEM”** 属性中输入CA证书PEM文件的名称。

5. 如果还为Pulsar群集配置了相互TLS身份验证，请在阶段的“ **安全性”**选项卡上选择“ **启用相互身份验证** ” 。

6. 创建客户端证书和客户端私钥PEM文件以供该阶段使用。

   有关为Pulsar创建客户端证书的信息，请参阅[Pulsar文档](https://pulsar.incubator.apache.org/docs/en/security-tls-authentication/)。

7. 将为该阶段创建的客户端证书和客户端私钥PEM文件存储在Data Collector 资源目录$ SDC_RESOURCES中。

8. 在阶段的“ **安全性”**选项卡上，在“ **客户端证书PEM”**和“ **客户端密钥PEM”**属性中输入客户端文件的名称。

## 资料格式

Pulsar Producer目标根据您选择的数据格式将数据写入Pulsar。您可以使用以下数据格式：

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

## 配置脉冲星生产者

配置Pulsar Producer目标以将数据写入Pulsar主题。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **Pulsar”**选项卡上，配置以下属性：

   | 脉冲星地产           | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | 脉冲星网址           | Pulsar Web服务或代理服务的URL。如果未为Pulsar群集启用TLS，请以以下格式输入Web服务或代理服务URL：Web服务URL- `http://:`。例如： `http://pulsar.us-west.example.com:8080`。经纪人服务网址- `pulsar://:`。例如： `pulsar://pulsar.us-west.example.com:6650`如果为Pulsar群集启用了TLS，请以以下格式输入安全代理服务URL：`pulsar+ssl://:`例如： `pulsar+ssl://pulsar.us-west.example.com:6651` |
   | 话题                 | 要向其发布消息的主题的名称。输入以下格式的主题名称：`{persistent|non-persistent}:////`例如，发布到一个名为持久的话题 `my-sdc-topic`在 `my-namespace`该范围内的命名空间 `my-tenant`的租户，输入以下内容作为主题名称：`persistent://my-tenant/my-namespace/my-sdc-topic`如果仅输入主题名称，则Pulsar使用默认`persistent://public/default/` 位置。例如，要发布到名称空间中属于`public`租户 的永久主题`default`，只需输入主题名称，如下所示：`my-sdc-topic`如果指定的主题不存在，则Pulsar在管道启动时创建主题。您可以使用表达式来定义主题名称。例如，如果`my-topic`记录中的 字段包含主题名称，请输入以下内容作为主题名称：`persistent://my-tenant/my-namespace/${record:value("/my-topic")}` |
   | 保持活动间隔（毫秒） | 允许与Pulsar的连接保持空闲状态的毫秒数。在此时间段内目标未发布任何消息后，连接将关闭。目的地必须重新连接到Pulsar。默认值为30,000毫秒。 |
   | 操作超时（毫秒）     | 在将操作标记为失败之前，允许Pulsar Producer创建操作完成的毫秒数。默认值为30,000毫秒。 |

3. 要启用安全性，请单击“ **安全性”**选项卡并配置以下属性：

   | 担保财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [启用TLS](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/PulsarProducer.html#task_gwh_3jt_y2b) | 使舞台能够通过TLS加密安全地连接到Pulsar。                    |
   | 启用相互认证                                                 | 使阶段能够使用相互TLS身份验证来安全地连接到Pulsar。          |
   | CA证书PEM                                                    | PEM文件的路径，该文件包含对Pulsar群集证书签名的证书颁发机构（CA）。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。 |
   | 客户证书PEM                                                  | 如果启用了相互身份验证，则是包含为Data Collector创建的客户端证书的PEM文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。 |
   | 客户密钥PEM                                                  | 如果启用了相互身份验证，则是包含为Data Collector创建的客户端专用密钥的PEM文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。 |

4. 在“ **高级”**选项卡上，可以选择配置高级属性。

   这些属性的默认值在大多数情况下都应该起作用：

   | 先进物业                 | 描述                                                         |
   | :----------------------- | :----------------------------------------------------------- |
   | 分区类型                 | 将消息发布到主题时要使用的分区类型：单循环赛默认为单。       |
   | 散列方案                 | 选择向哪个分区写入消息时使用的哈希方案。                     |
   | 留言键                   | 消息密钥，用于计算分区的哈希值。输入密钥或输入计算结果为该密钥的表达式。 |
   | 压缩类型                 | 适用于已发布消息的压缩类型：没有LZ4ZLIB默认为无。            |
   | 异步发送                 | 使目标能够异步发布消息。清除以同步发布消息。有关可用的发送模式的更多信息，请参阅[Apache Pulsar文档](https://pulsar.apache.org/docs/v1.19.0-incubating/getting-started/ConceptsAndArchitecture/#Sendmodes-03zb1l)。默认启用。 |
   | 最大待处理邮件           | 异步发送消息时，可以在队列中等待Pulsar代理确认的最大消息数。默认值为1,000。 |
   | 启用批处理               | 异步发送消息时，启用在单个请求中发送一批消息。清除以在每个请求中发送一条消息。默认启用。 |
   | 最大批处理大小（消息）   | 异步发送消息并启用批处理时，要包含在批处理中的最大消息数。默认值为2,000。 |
   | 批处理最大发布延迟（ms） | 当异步发送消息并启用了批处理功能时，发送下一个批处理之前要等待的最大毫秒数。默认值为1000毫秒。 |
   | 脉冲星配置属性           | 要使用的其他Pulsar配置属性。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以添加属性。定义Pulsar属性名称和值。使用Pulsar期望的属性名称和值。 |

5. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/PulsarProducer.html#concept_ovk_2mt_y2b) | 要读取的数据类型。使用以下选项之一：二元定界JSON格式原虫SDC记录文本XML格式 |

6. 对于二进制数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 二元性质     | 描述                   |
   | :----------- | :--------------------- |
   | 二进制场路径 | 包含二进制数据的字段。 |

7. 对于定界数据，在“ **数据格式”**选项卡上，配置以下属性：

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

8. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

   | JSON属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | JSON内容 | 写入JSON数据的方法：JSON对象数组-每个文件都包含一个数组。在数组中，每个元素都是每个记录的JSON表示形式。多个JSON对象-每个文件包含多个JSON对象。每个对象都是记录的JSON表示形式。 |
   | 字符集   | 写入数据时使用的字符集。                                     |

9. 对于protobuf数据，在“ **数据格式”**选项卡上，配置以下属性：

   | Protobuf属性       | 描述                                                         |
   | :----------------- | :----------------------------------------------------------- |
   | Protobuf描述符文件 | 要使用的描述符文件（.desc）。描述符文件必须位于Data Collector资源目录中`$SDC_RESOURCES`。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。 |
   | 讯息类型           | 写入数据时使用的消息类型的全限定名称。使用以下格式： `.`。使用在描述符文件中定义的消息类型。 |

10. 对于文本数据，在“ **数据格式”**选项卡上，配置以下属性：

    | 文字属性                       | 描述                                                         |
    | :----------------------------- | :----------------------------------------------------------- |
    | 文字栏位路径                   | 包含要写入的文本数据的字段。所有数据必须合并到指定字段中。   |
    | 记录分隔符                     | 用于分隔记录的字符。使用任何有效的Java字符串文字。例如，当写入Windows时，您可能会\r\n用来分隔记录。默认情况下，目标使用 \n。 |
    | 在失落的田野上                 | 当记录不包含文本字段时，确定目标是将丢失的字段报告为错误还是忽略该丢失的字段。 |
    | 如果没有文本，则插入记录分隔符 | 当配置为忽略缺少的文本字段时，插入配置的记录分隔符字符串以创建一个空行。如果未选择，则丢弃没有文本字段的记录。 |
    | 字符集                         | 写入数据时使用的字符集。                                     |

11. 对于XML数据，在“ **数据格式”**选项卡上，配置以下属性：

    | XML属性  | 描述                                                         |
    | :------- | :----------------------------------------------------------- |
    | 漂亮格式 | 添加缩进以使生成的XML文档更易于阅读。相应地增加记录大小。    |
    | 验证架构 | 验证生成的XML是否符合指定的架构定义。具有无效架构的记录将根据为目标配置的错误处理进行处理。**要点：**无论是否验证XML模式，目的地都需要特定格式的记录。有关更多信息，请参见[记录结构要求](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WritingXML.html#concept_cmn_hml_r1b)。 |
    | XML模式  | 用于验证记录的XML模式。                                      |