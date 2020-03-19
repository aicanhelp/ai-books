# Pulsar消费者

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310173514168.png) 资料收集器

Pulsar消费者来源从Apache Pulsar集群中的一个或多个主题读取消息。

Pulsar消费者来源订阅Pulsar主题，处理传入的消息，然后在读取消息时将确认发送回Pulsar。

在配置“ Pulsar消费者”来源时，您将定义要连接到Pulsar的URL。您还可以定义要用于原始来源和主题的订阅名称和使用者名称。当管道启动时，Pulsar用指定的使用者名称创建使用者。如果订阅和主题不存在，Pulsar还将创建订阅和主题。

您可以将源配置为使用Pulsar安全功能。您还可以根据需要配置高级属性，例如要创建的订阅类型或要开始读取的初始偏移量。

“ Pulsar消费者”来源可以包含记录标题属性，使您能够在管道处理中使用有关记录的信息。

有关Pulsar主题，订阅和使用者的更多信息，请参阅[Apache Pulsar文档](https://pulsar.apache.org/docs/en/concepts-overview/)。

## 主题选择器

脉冲星消费者来源可以订阅一个主题或多个主题。无论哪种情况，原点都使用一个线程进行读取。

**注意：**要对同一主题进行并行读取，请参阅[对主题的并行读取](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/PulsarConsumer.html#concept_yjj_bw4_z2b)。

要定义来源订阅的主题，请在“脉冲星”选项卡上配置“主题选择器”属性。

来源提供了以下订阅主题的方法：

- 单一主题

  订阅一个主题。使用以下格式来指定主题名称：`{persistent|non-persistent}:////`

  例如，订阅名为持久的话题 `my-sdc-topic`在`my-namespace` 该范围内的命名空间`my-tenant`的租户，输入以下内容作为主题名称：`persistent://my-tenant/my-namespace/my-sdc-topic`

  如果仅输入主题名称，则Pulsar使用默认 `persistent://public/default/`位置。例如，要订阅名称空间中属于`public` 承租人的持久性主题`default`，只需输入主题名称，如下所示：`my-sdc-topic`

  如果指定的主题不存在，则Pulsar在管道启动时创建主题。

  您可以使用表达式来定义主题名称。例如，要订阅以Data Collector主机名命名的主题，请输入以下内容作为主题名称：`persistent://my-tenant/my-namespace/${sdc:hostname()}`

- 主题清单

  订阅在主题名称列表中定义的多个主题。使用“添加”图标添加其他主题名称。使用单个主题所需的相同格式定义每个主题名称。

- 主题模式

  订阅由命名模式定义的多个主题。使用以下格式来指定模式：`{persistent|non-persistent}:////`

  例如，要预订名称以开头的所有持久主题 `sdc-`，请输入以下作为主题名称：`persistent://my-tenant/my-namespace/sdc-.*`

  此模式使用诸如`sdc-topic`或的 名称将源订阅给指定租户和名称空间中的所有主题`sdc-data`。

  **重要：**通过模式预订多个主题时，所有主题都必须位于同一命名空间中。

有关定义主题名称和订阅多个主题的更多信息，请参阅[Apache Pulsar文档](https://pulsar.incubator.apache.org/docs/en/concepts-messaging/#topics)。

### 并行阅读主题

脉冲星消费者来源可以订阅一个主题或多个主题。无论哪种情况，源都使用一个线程从一个或多个主题中读取。要从同一主题执行并行读取，可以使用订阅同一主题的Pulsar Consumer来源配置多个管道。

要将多个来源配置为订阅同一主题，您需要确定这些来源是使用单个共享订阅，多个独占订阅还是多个故障转移订阅。共享订阅允许多个使用者附加到同一订阅。独占订阅仅允许单个消费者附加到订阅。故障转移订阅允许多个使用者连接到同一订阅，但是一次只有一个使用者接收消息。如果最初的主使用者断开连接，则邮件将传递到下一个使用者。

有关Pulsar订阅模式的更多信息，请参阅Pulsar文档中的[订阅模式](https://pulsar.apache.org/docs/en/concepts-messaging/#subscription-modes)。

您可以通过以下方式配置多个Pulsar消费者来源来订阅相同的Pulsar主题：

- 单一共享订阅

  要将多个Pulsar Consumer来源配置为使用同一共享订阅来订阅同一主题，请配置以下来源属性：订阅名称-在“脉冲星”选项卡上，将每个来源配置为使用相同的订阅名称。主题-在“脉冲星”选项卡上，将每个来源配置为使用相同的主题名称。订阅类型-在“高级”选项卡上，将每个来源配置为使用共享订阅类型。

- 多个独家订阅

  要将多个Pulsar Consumer来源配置为使用多个互斥订阅来订阅同一主题，请配置以下来源属性：订阅名称-在“脉冲星”选项卡上，将每个来源配置为使用唯一的订阅名称。主题-在“脉冲星”选项卡上，将每个来源配置为使用相同的主题名称。订阅类型-在“高级”选项卡上，将每个来源配置为使用独占订阅类型。

- 多个故障转移订阅

  要将多个Pulsar Consumer来源配置为使用多个故障转移订阅来订阅同一主题，请配置以下来源属性：订阅名称-在“脉冲星”选项卡上，将每对来源配置为使用唯一的订阅名称。例如，将管道A和管道B中的起点配置为使用subscription1。如果管道A的来源断开连接，则管道A中的来源用作主使用者，管道B的来源是接收消息的下一行。然后，将pipelineC和pipelineD中的起点配置为使用subscription2。主题-在“脉冲星”选项卡上，将每个来源配置为使用相同的主题名称。订阅类型-在“高级”选项卡上，将每个来源配置为使用故障转移订阅类型。

## 启用安全性

如果Pulsar群集使用安全功能，则必须将Pulsar Consumer来源配置为使用相同的安全功能连接到Pulsar。

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

## 胶印管理

Pulsar消费者来源第一次收到来自某个主题的消息时，将为该订阅和主题创建一个抵消条目。偏移条目由Pulsar创建和维护。

根据是否存在存储的偏移条目，Pulsar消费者来源将开始在主题中接收消息：

- 没有存储的偏移

  当订阅和主题组合不具有先前存储的偏移时，Pulsar消费者来源将根据在来源的“高级”选项卡上定义的初始偏移的值开始接收消息。

  您将以下值用作初始偏移量：最新-在管道启动后开始阅读写入该主题的最新可用消息，而忽略该主题中所有现有的消息。这是默认的初始偏移量。最早-开始阅读尚未确认的主题中的最早可用消息。

- 先前存储的偏移

  当订阅和主题组合具有先前存储的偏移量时，Pulsar消费者来源将接收到从存储的偏移量之后的下一条未处理的消息开始的消息。例如，当您停止并重新启动管道时，处理将从最后提交的偏移量恢复。

## 记录标题属性

Pulsar消费者来源包括消息的属性字段中（净荷字段之外）记录中的任何信息，这些记录作为记录头属性记录在记录中。

您可以使用`record:attribute`或 `record:attributeOrDefault`函数来访问属性中的信息。有关使用记录标题属性的更多信息，请参见[使用标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_rd2_ghz_dz)。

## 资料格式

脉冲星消费者来源基于数据格式对数据的处理方式有所不同。Pulsar Consumer可以处理以下类型的数据：

- 二元

  生成一条记录，在记录的根部有一个单字节数组字段。

  当数据超过用户定义的最大数据大小时，原点将无法处理数据。因为未创建记录，所以源无法将记录传递到管道以将其写为错误记录。相反，原点会产生阶段误差。

- 数据报

  为每条消息生成一条记录。源可以处理[收集的](https://collectd.org/)消息，NetFlow 5和NetFlow 9消息以及以下类型的syslog消息：[RFC 5424](https://tools.ietf.org/html/rfc5424)[RFC 3164](https://tools.ietf.org/html/rfc3164)非标准通用消息，例如RFC 3339日期，没有版本数字

  在处理NetFlow消息时，该阶段会根据NetFlow版本生成不同的记录。处理NetFlow 9时，将基于NetFlow 9配置属性生成记录。有关更多信息，请参见[NetFlow数据处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/NetFlow_Overview.html#concept_thl_nnr_hbb)。

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

- 原虫

  为每个protobuf消息生成一条记录。默认情况下，来源假设邮件包含多个protobuf邮件。

  Protobuf消息必须与指定的消息类型匹配，并在描述符文件中进行描述。

  当记录的数据超过1 MB时，源将无法继续处理消息中的数据。源根据阶段错误处理属性处理消息，并继续读取下一条消息。

  有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。

- SDC记录

  为每条记录生成一条记录。用于处理由数据收集器 管道使用SDC记录数据格式生成的记录。

  对于错误记录，原点提供从原始管道中的原点读取的原始记录，以及可用于更正记录的错误信息。

  处理错误记录时，来源希望原始管道生成的错误文件名和内容。

- 文本

  根据自定义定界符为每行文本或每段文本生成一条记录。

  当线或线段超过为原点定义的最大线长时，原点会截断它。原点添加了一个名为Truncated的布尔字段，以指示该行是否被截断。

  有关使用自定义定界符处理文本的更多信息，请参见[使用自定义定界符的文本数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/TextCDelim.html#concept_lg2_gcg_jx)。

- XML格式

  根据用户定义的定界符元素生成记录。在根元素下直接使用XML元素或定义简化的XPath表达式。如果未定义定界符元素，则源会将XML文件视为单个记录。

  默认情况下，生成的记录包括XML属性和名称空间声明作为记录中的字段。您可以配置阶段以将它们包括在记录中作为字段属性。

  您可以在字段属性中包含每个解析的XML元素和XML属性的XPath信息。这还将每个名称空间放置在xmlns记录头属性中。**注意：** 只有在目标中使用SDC RPC数据格式时，字段属性和记录头属性才会自动写入目标系统。有关使用字段属性和记录标题属性以及如何将它们包括在记录中的更多信息，请参见[字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)和[记录标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)。

  当记录超过用户定义的最大记录长度时，原点将跳过该记录并继续处理下一条记录。它将跳过的记录发送到管道以进行错误处理。

  使用XML数据格式来处理有效的XML文档。有关XML处理的更多信息，请参见[阅读和处理XML数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_lty_42b_dy)。

  **提示：** 如果要处理无效的XML文档，则可以尝试将文本数据格式与自定义分隔符一起使用。有关更多信息，请参见 [使用自定义分隔符处理XML数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/TextCDelim.html#concept_okt_kmg_jx)。

## 配置脉冲星消费者来源

配置Pulsar使用者来源以从Apache Pulsar读取消息。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **Pulsar”**选项卡上，配置以下属性：

   | 脉冲星地产                                                   | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 脉冲星网址                                                   | Pulsar Web服务或代理服务的URL。如果未为Pulsar群集启用TLS，请以以下格式输入Web服务或代理服务URL：Web服务URL- `http://:`。例如： `http://pulsar.us-west.example.com:8080`。经纪人服务网址- `pulsar://:`。例如： `pulsar://pulsar.us-west.example.com:6650`如果为Pulsar群集启用了TLS，请以以下格式输入安全代理服务URL：`pulsar+ssl://:`例如： `pulsar+ssl://pulsar.us-west.example.com:6651` |
   | 订阅名称                                                     | 要为原点创建的订阅的名称。默认值为sdc-subscription。         |
   | 保持活动间隔（毫秒）                                         | 允许与Pulsar的连接保持空闲状态的毫秒数。在此时间段内，原始服务器未收到任何消息后，连接将关闭。原点必须重新连接到Pulsar。默认值为30,000毫秒。 |
   | 操作超时（毫秒）                                             | 允许Pulsar消费者创建，消费者订阅和消费者取消订阅操作完成之前将操作标记为失败的毫秒数。默认值为30,000毫秒。 |
   | 消费者名称                                                   | 要为原点创建的消费者的名称。输入使用者名称或定义一个计算得出该使用者名称的表达式。 |
   | [主题选择器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/PulsarConsumer.html#concept_ndm_4bm_y2b) | 订阅主题的方法：单个主题-按名称订阅单个主题。主题列表-订阅在主题名称列表中定义的多个主题。主题模式-订阅由命名模式定义的多个主题。 |
   | [话题](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/PulsarConsumer.html#concept_ndm_4bm_y2b) | 要订阅的单个主题的名称。输入以下格式的主题名称：`{persistent|non-persistent}:////` |
   | [主题清单](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/PulsarConsumer.html#concept_ndm_4bm_y2b) | 要订阅的主题名称列表。输入以下格式的每个主题名称：`{persistent|non-persistent}:////`使用“添加”图标添加其他主题名称。您可以使用 [简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加其他主题。 |
   | [主题模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/PulsarConsumer.html#concept_ndm_4bm_y2b) | 要订阅的主题名称的模式。输入以下格式的模式：`{persistent|non-persistent}:////` |
   | 最大批次大小（记录）                                         | 一次处理的最大记录数。接受的值最高为Data Collector的最大批处理大小。默认值是1000 数据采集器默认设置为1000。 |
   | [批处理等待时间（毫秒）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ypd_vgr_5q) | 发送部分或空批次之前要等待的毫秒数。                         |
   | 产生单条记录                                                 | 为包含多个对象的记录生成单个记录。如果未选中，则当一个记录包含多个对象时，原点将生成多个记录。 |

3. 要启用安全性，请单击“ **安全性”**选项卡并配置以下属性：

   | 担保财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [启用TLS](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/PulsarConsumer.html#task_fsr_wgs_y2b) | 使舞台能够通过TLS加密安全地连接到Pulsar。                    |
   | 启用相互认证                                                 | 使阶段能够使用相互TLS身份验证来安全地连接到Pulsar。          |
   | CA证书PEM                                                    | PEM文件的路径，该文件包含对Pulsar群集证书签名的证书颁发机构（CA）。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。 |
   | 客户证书PEM                                                  | 如果启用了相互身份验证，则是包含为Data Collector创建的客户端证书的PEM文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。 |
   | 客户密钥PEM                                                  | 如果启用了相互身份验证，则是包含为Data Collector创建的客户端专用密钥的PEM文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。 |

4. 在“ **高级”**选项卡上，可以选择配置高级属性。

   这些属性的默认值在大多数情况下都应该起作用：

   | 先进物业                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 订阅类型                                                     | 为Pulsar消费者创建的订阅类型：独家故障转移共享默认为独占。有关每种订阅类型的信息，请参阅[Pulsar文档](https://pulsar.apache.org/docs/en/concepts-messaging/#subscription-modes)。 |
   | 使用者队列大小                                               | Pulsar可以添加到使用者队列的消息数。默认值为1,000条消息。    |
   | [初始偏移](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/PulsarConsumer.html#concept_fqm_mjm_y2b) | 管道首次启动时使用的偏移值：最早的最新默认为最新。           |
   | 模式自动发现时间（分钟）                                     | 订阅由命名模式定义的主题时，是允许原点查找所有匹配主题的分钟数。默认值为一分钟。 |
   | 消费者优先级                                                 | 分配给“脉冲星消费者”来源的优先级。优先级较高的消费者会收到更多消息。默认值为0。 |
   | 阅读压缩                                                     | 从压缩的持久性主题中读取消息，而不是从主题的完整消息积压中读取消息。启用后，原点仅读取主题中每个键的最新值。仅适用于永久性主题。 |
   | 脉冲星配置属性                                               | 要使用的其他Pulsar配置属性。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以添加属性。定义Pulsar属性名称和值。使用Pulsar期望的属性名称和值。 |

5. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/PulsarConsumer.html#concept_cdw_f4m_y2b) | 要读取的数据类型。使用以下选项之一：二元数据报定界JSON格式记录原虫SDC记录文本XML格式 |

6. 对于二进制数据，请在“ **数据格式”**选项卡上并配置以下属性：

   | 二元性质             | 描述                                               |
   | :------------------- | :------------------------------------------------- |
   | 最大数据大小（字节） | 消息中的最大字节数。较大的消息无法处理或写入错误。 |

7. 对于数据报数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 数据报属性                                                   | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 数据报包格式                                                 | 数据包格式：已收集网络流系统日志原始/分离数据                |
   | TypesDB文件路径                                              | 用户提供的types.db文件的路径。覆盖默认的types.db文件。仅用于收集的数据。 |
   | 转换高分辨率时间和间隔                                       | 将收集的高分辨率时间格式间隔和时间戳转换为UNIX时间（以毫秒为单位）。仅用于收集的数据。 |
   | 排除间隔                                                     | 从输出记录中排除间隔字段。仅用于收集的数据。                 |
   | 认证文件                                                     | 可选身份验证文件的路径。使用认证文件接受签名和加密的数据。仅用于收集的数据。 |
   | [记录生成方式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/NetFlow_Overview.html#concept_jdh_hxk_3bb) | 确定要包含在记录中的值的类型。选择以下选项之一：仅原始仅解释原始和解释仅适用于NetFlow 9数据。 |
   | 缓存中的最大模板数                                           | 模板缓存中存储的最大模板数。有关模板的更多信息，请参见[缓存NetFlow 9模板](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/NetFlow_Overview.html#concept_ivr_j1l_3bb)。对于无限的缓存大小，默认值为-1。仅适用于NetFlow 9数据。 |
   | 模板缓存超时（毫秒）                                         | 缓存空闲模板的最大毫秒数。超过指定时间未使用的模板将从缓存中逐出。有关模板的更多信息，请参见 [缓存NetFlow 9模板](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/NetFlow_Overview.html#concept_ivr_j1l_3bb)。无限期缓存模板的默认值为-1。仅适用于NetFlow 9数据。 |
   | 字符集                                                       | 要处理的消息的字符编码。                                     |
   | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

8. 对于定界数据，在“ **数据格式”**选项卡上，配置以下属性：

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

9. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

   | JSON属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | JSON内容                                                     | JSON内容的类型。使用以下选项之一：对象数组多个物件           |
   | 最大对象长度（字符）                                         | JSON对象中的最大字符数。较长的对象将转移到管道以进行错误处理。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
   | 字符集                                                       | 要处理的文件的字符编码。                                     |
   | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

10. 对于日志数据，在“ **数据格式”**选项卡上，配置以下属性：

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

11. 对于protobuf数据，在“ **数据格式”**选项卡上，配置以下属性：

    | Protobuf属性       | 描述                                                         |
    | :----------------- | :----------------------------------------------------------- |
    | Protobuf描述符文件 | 要使用的描述符文件（.desc）。描述符文件必须位于Data Collector资源目录中 `$SDC_RESOURCES`。有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。 |
    | 讯息类型           | 读取数据时使用的消息类型的全限定名称。使用以下格式： `.`。使用在描述符文件中定义的消息类型。 |
    | 分隔消息           | 指示一条消息是否可能包含多个protobuf消息。                   |

12. 对于文本数据，在“ **数据格式”**选项卡上，配置以下属性：

    | 文字属性                                                     | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 最大线长                                                     | 一行允许的最大字符数。较长的行被截断。向记录添加一个布尔字段，以指示该记录是否被截断。字段名称为“截断”。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
    | [使用自定义分隔符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/TextCDelim.html#concept_lg2_gcg_jx) | 使用自定义定界符来定义记录而不是换行符。                     |
    | 自定义定界符                                                 | 用于定义记录的一个或多个字符。                               |
    | 包括自定义定界符                                             | 在记录中包括定界符。                                         |
    | 字符集                                                       | 要处理的文件的字符编码。                                     |
    | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

13. 对于XML数据，在“ **数据格式”**选项卡上，配置以下属性：

    | XML属性                                                      | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [分隔元素](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_tmc_4bc_dy) | 用于生成记录的定界符。省略定界符，将整个XML文档视为一条记录。使用以下之一：在根元素正下方的XML元素。使用不带尖括号（<>）的XML元素名称。例如，用msg代替<msg>。一个简化的XPath表达式，指定要使用的数据。使用简化的XPath表达式访问XML文档中更深的数据或需要更复杂访问方法的数据。有关有效语法的更多信息，请参见[简化的XPath语法](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_tmc_4bc_dy)。 |
    | [包含字段XPath](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_w3k_1ch_qz) | 在字段属性中包括每个解析的XML元素的XPath和XML属性。还包括xmlns记录头属性中的每个名称空间。如果未选中，则此信息不包含在记录中。默认情况下，未选择该属性。**注意：** 只有在目标中使用SDC RPC数据格式时，字段属性和记录头属性才会自动写入目标系统。有关使用字段属性和记录标题属性以及如何将它们包括在记录中的更多信息，请参见[字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)和[记录标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)。 |
    | 命名空间                                                     | 解析XML文档时使用的命名空间前缀和URI。当所使用的XML元素包含名称空间前缀或XPath表达式包含名称空间时，定义名称空间。有关将名称空间与XML元素一起使用的信息，请参见[将XML元素与名称空间一起使用](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_ilc_r3g_2y)。有关将名称空间与XPath表达式一起使用的信息，请参阅《[将XPath表达式与名称](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_mkk_3zj_dy)空间一起[使用》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_mkk_3zj_dy)。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以添加其他名称空间。 |
    | 输出字段属性                                                 | 在记录中包括XML属性和名称空间声明作为字段属性。如果未选择，则XML属性和名称空间声明作为字段包含在记录中。**注意：** 只有在目标中使用SDC RPC数据格式时，字段属性才会自动包含在写入目标系统的记录中。有关使用字段属性的更多信息，请参见[字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)。默认情况下，未选择该属性。 |
    | 最大记录长度（字符）                                         | 记录中的最大字符数。较长的记录将转移到管道以进行错误处理。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
    | 字符集                                                       | 要处理的文件的字符编码。                                     |
    | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |