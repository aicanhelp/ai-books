# 亚马逊SQS消费者

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310112547803.png) 资料收集器

使用Amazon SQS使用者来源从Amazon Simple Queue Services（SQS）中的队列读取数据。源可以使用多个线程来启用数据的并行处理。要从Amazon S3读取数据，请使用 [Amazon S3 origin](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonS3.html#concept_kvs_3hh_ht)。

在配置Amazon SQS消费者来源时，您将定义区域和要使用的队列名称前缀集。这些属性确定原始处理的对象。您可以使用IAM角色或AWS访问密钥对来访问数据。

您可以选择在记录中包括Amazon SQS消息属性和发送者属性作为记录标题属性。

## AWS凭证

当Data Collector从Amazon SQS消费者来源读取数据时，它必须将凭证传递给Amazon Simple Queue Services。

使用以下方法之一来传递AWS凭证：

- IAM角色

  当执行数据收集器 在Amazon EC2实例上运行时，您可以使用AWS管理控制台为EC2实例配置IAM角色。Data Collector使用IAM实例配置文件凭证自动连接到AWS。

  要使用IAM角色，请不要配置“访问密钥ID”和“秘密访问密钥”属性。

  有关将IAM角色分配给EC2实例的更多信息，请参阅Amazon EC2文档。

- AWS访问密钥对

  当执行数据收集器未在Amazon EC2实例上运行或EC2实例不具有IAM角色时，您必须配置**访问密钥ID**和**秘密访问密钥** 属性。

  **提示：**为了保护敏感信息（例如访问密钥对）的安全，可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。

## 队列名称前缀

Amazon SQS消费者来源使用队列名称前缀来确定要处理的队列。您可以定义多个队列名称前缀。

当您指定队列名称前缀时，输入一个字符串，该字符串代表您要使用的队列名称的开头。源服务器使用匹配的前缀处理来自每个队列的数据。您不能在队列名称前缀中使用通配符。

例如，假设您有以下队列：

```
sales-eu-france
sales-eu-germany
sales-us
sales-egypt
```

如果使用“ sales”作为前缀，则源将处理来自所有队列的消息。

如果使用“ sales-eu”作为前缀，则源仅处理sales-eu-france和sales-eu-德国。

如果使用“ sales-e”作为前缀，则源将处理除sales-us外的所有队列。

## 多线程处理

Amazon SQS消费者来源执行并行处理，并允许创建多线程管道。Amazon SQS消费者来源基于Max Threads属性使用多个并发线程。

执行多线程处理时，Amazon SQS使用者来源将确定要处理的队列数并创建指定的线程数。当队列多于线程时，队列将被划分并分配给不同的线程。每个线程处理来自特定队列集合的数据，并循环遍历该队列集合。

当线程从队列请求数据时，该队列将根据配置的“每个请求的消息数”属性返回消息。该线程创建一批数据，并将该批数据传递给可用的管道运行器。处理完批处理后，线程将继续到下一个分配的队列。

管道运行器是无源管道实例 - 管道的实例，包括管道中的所有处理器，执行程序和目的地，并在源之后处理所有管道处理。 每个管道运行程序一次处理一个批处理，就像在单个线程上运行的管道一样。当数据流减慢时，管道运行器会闲置等待，直到需要它们为止，并定期生成一个空批。您可以配置“运行者空闲时间”管道属性来指定间隔或选择退出空批次生成。

多线程管道保留每个批处理中的记录顺序，就像单线程管道一样。但是不能确保将批处理写入目的地的顺序。

例如，假设您将“最大线程数”属性设置为5，并且将原点配置为处理20个队列。启动管道时，原始节点将创建五个线程，而Data Collector 会创建匹配数量的管道运行器。每个线程分配有4个队列进行处理。每个线程在队列中循环，一次创建一批数据，然后将其传递给管道运行器进行处理。

在任何给定的时刻，五个流水线运行者可以分别处理一个批处理，因此该多线程管道一次最多可以处理五个批处理。当传入数据变慢时，管道运行器将处于空闲状态，并在数据流增加时立即可用。

有关多线程管道的更多信息，请参见《[多线程管道概述》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Multithreaded_Pipelines/MultithreadedPipelines.html#concept_zpp_2xc_py)。

## 包括SQS消息属性

Amazon SQS消费者来源可以在记录中包含SQS消息属性作为记录标题属性。您可以根据选择的SQS消息属性级别将不同的SQS属性组添加到记录：

- 基本-包括一些基本的SQS属性。
- 所有属性-包括所有标准SQS属性。

SQS属性添加到记录头使用以下命名约定属性：`sqs.`。

下表列出了每个属性级别中包含的SQS属性：

| SQS属性级别 | 描述                                                         |
| :---------- | :----------------------------------------------------------- |
| 基本的      | 包括以下标准SQS属性：sqs.messageId-SQS消息ID。sqs.queueNamePrefix-您为阶段配置的队列名称前缀。sqs.region-消息的AWS区域。 |
| 所有属性    | 包括上面列出的标准SQS属性和以下其他SQS属性：sqs.body-完整的原始邮件正文。sqs.bodyMd5-原始邮件正文的MD5哈希。sqs.queueUrl-始发队列的完整URL。sqs.attrsMd5-消息中包含的所有SQS属性的MD5哈希。 |

有关SQS消息属性的更多信息，请参阅Amazon SQS文档。

### 包括发件人属性

除了SQS消息属性外，您还可以在记录头中包含发件人属性。发件人属性是消息发件人包含在消息中的自定义属性。

要包括消息发件人属性，请执行以下步骤：

1. 将“ SQS消息属性级别”属性设置为“所有属性”。
2. 配置SQS发送者属性属性，添加要包括在记录中的每个属性的名称。

SQS属性添加到记录头使用以下命名约定属性：`sqs.messageAttr.`。

例如，一个`senderId`属性在记录标题中显示为 `sqs.messageAttr.senderId`。

## 资料格式

Amazon SQS消费者来源基于数据格式对数据的处理方式有所不同。Amazon SQS使用者可以处理以下类型的数据：

- 阿夫罗

  为每条消息生成一条记录。每个小数字段都包含 `precision`和`scale` [字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)。

  源包括`avroSchema` [记录头属性中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)的Avro模式 。您可以使用以下方法之一来指定Avro模式定义的位置：**消息/数据包含架构** -在消息中使用架构。**在“管道配置”中** -使用您在阶段配置中提供的架构。**Confluent Schema Registry-**从Confluent Schema Registry检索架构。Confluent Schema Registry是Avro架构的分布式存储层。您可以配置源，以通过消息中嵌入的模式ID或阶段配置中指定的模式ID或主题在Confluent Schema Registry中查找模式。您必须指定来源用于反序列化消息的方法。如果每个消息中都嵌入了Avro模式ID，请在“ **Kafka”**选项卡上将键和值反序列化器设置为“ Confluent” 。

  在阶段配置中使用架构或从Confluent Schema Registry检索架构会覆盖消息中可能包含的任何架构，并可以提高性能。

- 二元

  生成一条记录，在记录的根部有一个单字节数组字段。

  当数据超过用户定义的最大数据大小时，原点将无法处理数据。因为未创建记录，所以源无法将记录传递到管道以将其写为错误记录。相反，原点会产生阶段误差。

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

## 配置Amazon SQS使用者

配置Amazon SQS消费者来源以从Amazon SQS读取消息。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **SQS”**选项卡上，配置以下属性：

   | SQS物业                                                      | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [访问密钥ID](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonSQS.html#concept_t43_bzb_vbb) | AWS访问密钥ID。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。 |
   | [秘密访问密钥](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonSQS.html#concept_t43_bzb_vbb) | AWS秘密访问密钥。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。 |
   | 区域                                                         | 亚马逊SQS地区。                                              |
   | [队列名称前缀](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonSQS.html#concept_isr_jzb_vbb) | 要处理的队列的公共前缀。不允许使用通配符。                   |
   | 每个请求的消息数                                             | 每个请求要请求的消息数。Amazon Simple Queue Service最多允许10条消息。默认值是10。 |
   | 最大批次大小（记录）                                         | 一次处理的最大记录数。接受的值最高为Data Collector的最大批处理大小。默认值是1000 数据采集器默认设置为1000。 |
   | [批处理等待时间（毫秒）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ypd_vgr_5q) | 发送部分或空批次之前要等待的毫秒数。                         |
   | [最大线程](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonSQS.html#concept_hcg_5cc_vbb) | 用于处理消息的线程数。默认值为 `${runtime:availableProcessors()}`，它返回管道启动时可用的Data Collector处理器数。 |
   | 轮询等待时间（秒）                                           | 等待请求响应的秒数。指定等待时间以使用[Amazon SQS长轮询](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-long-polling.html#sqs-short-long-polling-differences)来最大程度地减少空响应的影响。进行配置后，原始服务器将在等待指定秒数的消息后继续进入另一个队列。使用-1退出此属性。选择退出时，当队列中没有数据时，原点将立即继续到下一个队列。 |
   | SQS消息属性级别                                              | 确定记录中包含的消息属性作为记录头属性。选择以下选项之一：无属性-原始记录中的消息中不包含任何属性。基本属性-原始记录中包括一小套基本SQS属性。所有属性-源包括所有SQS生成的属性信息，例如消息正文和队列URL。指定后，还可以包括由消息发送者生成的SQS属性。有关消息中包含的属性的更多信息，请参见[包括SQS消息属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonSQS.html#concept_lk3_rfc_vbb)。 |
   | [包括发件人SQS属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonSQS.html#concept_y2j_xgk_vbb) | 当在记录头属性中包括所有SQS消息属性时，您还可以包括消息发件人生成的属性。指定要包括的消息发件人属性。输入确切的属性名称。 |

3. 在“ **高级”**选项卡上，可以选择配置代理信息：

   | 先进物业       | 描述                                                         |
   | :------------- | :----------------------------------------------------------- |
   | 连接超时       | 关闭连接之前等待响应的秒数。默认值为10秒。                   |
   | 套接字超时     | 等待查询响应的秒数。                                         |
   | 重试计数       | 重试请求的最大次数。                                         |
   | 使用代理服务器 | 指定是否使用代理进行连接。                                   |
   | 代理主机       | 代理主机。                                                   |
   | 代理端口       | 代理端口。                                                   |
   | 代理用户       | 代理凭据的用户名。                                           |
   | 代理密码       | 代理凭证的密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |

4. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonSQS.html#concept_mkm_q1j_vbb) | 要读取的数据类型。使用以下选项之一：阿夫罗二元定界JSON格式记录文本原虫[SDC记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/SDCRecordFormat.html#concept_qkk_mwk_br)XML格式 |

5. 对于Avro数据，在“ **数据格式”**选项卡上，配置以下属性：

   | Avro物业             | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | Avro模式位置         | 处理数据时要使用的Avro模式定义的位置：消息/数据包含架构-在消息中使用架构。在“管道配置”中-使用阶段配置中提供的架构。Confluent Schema Registry-从Confluent Schema Registry检索架构。在阶段配置中或在Confluent Schema Registry中使用架构可以提高性能。 |
   | Avro模式             | 用于处理数据的Avro模式定义。覆盖与数据关联的任何现有模式定义。您可以选择使用该 `runtime:loadResource`函数来加载存储在运行时资源文件中的架构定义。 |
   | 架构注册表URL        | 汇合的架构注册表URL，用于查找架构。要添加URL，请单击**添加**，然后以以下格式输入URL：`http://:` |
   | 基本身份验证用户信息 | 使用基本身份验证时连接到Confluent Schema Registry所需的用户信息。`schema.registry.basic.auth.user.info`使用以下格式从Schema Registry中的设置中输入密钥和机密 ：`:`**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 查找架构             | 在Confluent Schema Registry中查找架构的方法：主题-查找指定的Avro模式主题。架构ID-查找指定的Avro架构ID。嵌入式架构ID-查找每个消息中嵌入的Avro架构ID。覆盖与消息关联的任何现有模式定义。 |
   | 模式主题             | Avro架构需要在Confluent Schema Registry中查找。如果指定的主题具有多个架构版本，那么阶段将使用该主题的最新架构版本。要使用旧版本，请找到相应的架构ID，然后将“ **查找架构**依据 **”**属性设置为“架构ID”。 |
   | 架构编号             | 在Confluent Schema Registry中查找的Avro模式ID。              |

6. 对于二进制数据，请在“ **数据格式”**选项卡上并配置以下属性：

   | 二元性质             | 描述                                               |
   | :------------------- | :------------------------------------------------- |
   | 最大数据大小（字节） | 消息中的最大字节数。较大的消息无法处理或写入错误。 |

7. 对于定界数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 定界财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 分隔符格式类型                                               | 分隔符格式类型。使用以下选项之一：**默认CSV-**包含逗号分隔值的文件。忽略文件中的空行。**RFC4180 CSV-**严格遵循RFC4180准则的逗号分隔文件。**MS Excel CSV** -Microsoft Excel逗号分隔文件。**MySQL CSV** -MySQL逗号分隔文件。**制表符分隔的值** -包含制表符分隔的值的文件。**PostgreSQL CSV** -PostgreSQL逗号分隔文件。**PostgreSQL文本** -PostgreSQL文本文件。**自定义** -使用用户定义的定界符，转义符和引号字符的文件。**多字符**定界-使用多个用户定义的字符定界字段和行以及单个用户定义的转义和引号字符的文件。 |
   | 标题行                                                       | 指示文件是否包含标题行以及是否使用标题行。                   |
   | 允许额外的列                                                 | 使用标题行处理数据时，允许处理的记录列数超过标题行中的列数。 |
   | 额外的列前缀                                                 | 用于任何其他列的前缀。额外的列使用前缀和顺序递增的整数来命名，如下所示： ``。例如，`_extra_1`。默认值为 `_extra_`。 |
   | 最大记录长度（字符）                                         | 记录的最大长度（以字符为单位）。较长的记录无法读取。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
   | 分隔符                                                       | 自定义分隔符格式的分隔符。选择一个可用选项，或使用“其他”输入自定义字符。您可以输入使用格式为Unicode控制符\uNNNN，其中*ñ*是数字0-9或字母AF十六进制数字。例如，输入 \u0000以使用空字符作为分隔符或 \u2028使用行分隔符作为分隔符。默认为竖线字符（\|）。 |
   | 多字符字段定界符                                             | 用于分隔多字符定界符格式的字段的字符。默认值为两个竖线字符（\|\|）。 |
   | 多字符字段定界符                                             | 用于分隔多字符定界符格式的字段的字符。默认值为两个竖线字符（\|\|）。 |
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

8. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

   | JSON属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | JSON内容                                                     | JSON内容的类型。使用以下选项之一：对象数组多个物件           |
   | 最大对象长度（字符）                                         | JSON对象中的最大字符数。较长的对象将转移到管道以进行错误处理。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
   | 字符集                                                       | 要处理的文件的字符编码。                                     |
   | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

9. 对于日志数据，在“ **数据格式”**选项卡上，配置以下属性：

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

10. 对于protobuf数据，在“ **数据格式”**选项卡上，配置以下属性：

    | Protobuf属性       | 描述                                                         |
    | :----------------- | :----------------------------------------------------------- |
    | Protobuf描述符文件 | 要使用的描述符文件（.desc）。描述符文件必须位于Data Collector资源目录中 `$SDC_RESOURCES`。有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。 |
    | 讯息类型           | 读取数据时使用的消息类型的全限定名称。使用以下格式： `.`。使用在描述符文件中定义的消息类型。 |
    | 分隔消息           | 指示一条消息是否可能包含多个protobuf消息。                   |

11. 对于文本数据，在“ **数据格式”**选项卡上，配置以下属性：

    | 文字属性                                                     | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 最大线长                                                     | 一行允许的最大字符数。较长的行被截断。向记录添加一个布尔字段，以指示该记录是否被截断。字段名称为“截断”。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
    | [使用自定义分隔符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/TextCDelim.html#concept_lg2_gcg_jx) | 使用自定义定界符来定义记录而不是换行符。                     |
    | 自定义定界符                                                 | 用于定义记录的一个或多个字符。                               |
    | 包括自定义定界符                                             | 在记录中包括定界符。                                         |
    | 字符集                                                       | 要处理的文件的字符编码。                                     |
    | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

12. 对于XML数据，在“ **数据格式”**选项卡上，配置以下属性：

    | XML属性                                                      | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [分隔元素](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_tmc_4bc_dy) | 用于生成记录的定界符。省略定界符，将整个XML文档视为一条记录。使用以下之一：在根元素正下方的XML元素。使用不带尖括号（<>）的XML元素名称。例如，用msg代替<msg>。一个简化的XPath表达式，指定要使用的数据。使用简化的XPath表达式访问XML文档中更深的数据或需要更复杂访问方法的数据。有关有效语法的更多信息，请参见[简化的XPath语法](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_tmc_4bc_dy)。 |
    | [包含字段XPath](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_w3k_1ch_qz) | 在字段属性中包括每个解析的XML元素的XPath和XML属性。还包括xmlns记录头属性中的每个名称空间。如果未选中，则此信息不包含在记录中。默认情况下，未选择该属性。**注意：** 只有在目标中使用SDC RPC数据格式时，字段属性和记录头属性才会自动写入目标系统。有关使用字段属性和记录标题属性以及如何将它们包括在记录中的更多信息，请参见[字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)和[记录标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)。 |
    | 命名空间                                                     | 解析XML文档时使用的命名空间前缀和URI。当所使用的XML元素包含名称空间前缀或XPath表达式包含名称空间时，定义名称空间。有关将名称空间与XML元素一起使用的信息，请参见[将XML元素与名称空间一起使用](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_ilc_r3g_2y)。有关将名称空间与XPath表达式一起使用的信息，请参阅《[将XPath表达式与名称](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_mkk_3zj_dy)空间一起[使用》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_mkk_3zj_dy)。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以添加其他名称空间。 |
    | 输出字段属性                                                 | 在记录中包括XML属性和名称空间声明作为字段属性。如果未选择，则XML属性和名称空间声明作为字段包含在记录中。**注意：** 只有在目标中使用SDC RPC数据格式时，字段属性才会自动包含在写入目标系统的记录中。有关使用字段属性的更多信息，请参见[字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)。默认情况下，未选择该属性。 |
    | 最大记录长度（字符）                                         | 记录中的最大字符数。较长的记录将转移到管道以进行错误处理。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
    | 字符集                                                       | 要处理的文件的字符编码。                                     |
    | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |