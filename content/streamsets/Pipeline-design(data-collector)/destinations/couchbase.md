# Couchbase

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310183907295.png) 资料收集器

Couchbase目标将数据写入Couchbase服务器。Couchbase Server是一个分布式NoSQL面向文档的数据库。

目标将每个记录写入Couchbase数据库中现有存储桶中的文档。每个Couchbase文档都有一个唯一的ID或标识该文档的密钥。您可以为目标在其中写入每个记录的文档指定键。您可以将目标配置为使用比较和交换（CAS）值来在写入记录之前检测与其他进程的冲突。

Couchbase目标可以使用在`sdc.operation.type`记录头属性中定义的CRUD操作 来写入数据。您可以为没有标题属性或值的记录定义默认操作。您还可以配置如何处理不受支持的操作的记录。 有关Data Collector更改数据处理以及启用CDC的来源的列表的信息，请参见[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

配置Couchbase目标时，您将输入连接信息，例如要连接的节点和存储桶，以及连接的超时属性。（可选）您可以为连接启用TLS。您还输入信息以通过Couchbase Server进行身份验证。

## 定义CRUD操作

Couchbase目标可以插入，更新，删除或追加数据。目标根据CRUD操作标头属性或与操作相关的阶段属性中定义的CRUD操作写入记录。

您可以通过以下方式定义CRUD操作：

- CRUD记录标题属性

  您可以在CRUD操作记录标题属性中定义CRUD操作。目标在`sdc.operation.type`记录头属性中寻找要使用的CRUD操作 。

  该属性可以包含以下数值之一：INSERT为12个代表删除3更新4个用于UPSERT

  如果您的管道包括启用CRUD的原始数据源，该原始数据元处理已更改的数据，则目标位置仅从`sdc.operation.type`原始数据源生成的标头属性中读取操作类型 。如果管道使用非CDC来源，则可以使用表达式评估器或脚本处理器来定义记录头属性。有关Data Collector 更改的数据处理以及启用CDC的来源的列表的详细信息 ，请参阅 [处理更改的数据。](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

- 操作阶段属性

  您在目标属性中定义默认操作。`sdc.operation.type`未设置记录头属性时，目标使用默认操作 。

  您还可以定义如何使用`sdc.operation.type`header属性中定义的不受支持的操作来处理记录 。目标可以丢弃它们，将它们发送给错误，或使用默认操作。

## 比较和交换

您可以将Couchbase目标配置为使用Couchbase比较和交换（CAS）值来在写入记录之前检测与其他进程的冲突。

所述[Couchbase查找处理器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/CouchbaseLookup.html#concept_rxk_1dq_2fb)创建`couchbase.cas`为键/值查找记录标题属性。该属性存储一个值，该值代表查找的文档的状态。

当配置为使用CAS并且`couchbase.cas`记录头属性存在时，目标将向Couchbase Server发送带有写操作请求的属性值。Couchbase Server将属性值与文档的当前CAS值进行比较：

- 如果记录标题属性的值等于当前CAS值，则Couchbase Server会根据请求将记录写入文档。
- 如果记录头属性的值不等于CAS值，则Couchbase Server会感知到与另一个进程的冲突，并将未写入的记录发送回目的地。目标将记录发送到管道以进行错误处理。

当`couchbase.cas`记录头属性不存在时（例如对于源自N1QL查找的记录），目标无法使用CAS值来检测冲突。

## 资料格式



Couchbase目标根据您选择的数据格式将数据写入Couchbase服务器。

Couchbase目标处理数据格式如下：

- 阿夫罗

  目标根据Avro架构写入记录。您可以使用以下方法之一来指定Avro模式定义的位置：

  **在“管道配置”中** -使用您在阶段配置中提供的架构。**在记录标题中** -使用avroSchema记录标题属性中包含的架构。**Confluent Schema Registry-**从Confluent Schema Registry检索架构。Confluent Schema Registry是Avro架构的分布式存储层。您可以配置目标以通过架构ID或主题在Confluent Schema Registry中查找架构。如果在阶段或记录头属性中使用Avro架构，则可以选择配置目标以向Confluent Schema Registry注册Avro架构。

  目标在每个文件中都包含架构定义。

  您可以使用Avro支持的压缩编解码器压缩数据。使用Avro压缩时，请避免在目标位置使用其他压缩属性。

- 二元

  该阶段将二进制数据写入记录中的单个字段。

- 定界

  目标将记录写为定界数据。使用此数据格式时，根字段必须是list或list-map。

  您可以使用以下定界格式类型：**默认CSV-**包含逗号分隔值的文件。忽略文件中的空行。**RFC4180 CSV-**严格遵循RFC4180准则的逗号分隔文件。**MS Excel CSV** -Microsoft Excel逗号分隔文件。**MySQL CSV** -MySQL逗号分隔文件。**制表符分隔的值** -包含制表符分隔的值的文件。**PostgreSQL CSV** -PostgreSQL逗号分隔文件。**PostgreSQL文本** -PostgreSQL文本文件。**自定义** -使用用户定义的定界符，转义符和引号字符的文件。**多字符**定界-使用多个用户定义的字符定界字段和行以及单个用户定义的转义和引号字符的文件。

- JSON格式

  目标将记录作为JSON数据写入。

- 原虫

  在每个文件中写入一批消息。

  在描述符文件中使用用户定义的消息类型和消息类型的定义在文件中生成消息。

  有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。

- SDC记录

  目标以SDC记录数据格式写入记录。

- 文本

  目标将数据从单个文本字段写入目标系统。配置阶段时，请选择要使用的字段。

  您可以配置字符以用作记录分隔符。默认情况下，目标使用UNIX样式的行尾（\ n）分隔记录。

  当记录不包含选定的文本字段时，目标可以将缺少的字段报告为错误或忽略缺少的字段。默认情况下，目标报告错误。

  当配置为忽略缺少的文本字段时，目标位置可以丢弃该记录或写入记录分隔符以为该记录创建一个空行。默认情况下，目标丢弃记录。

## 配置Couchbase目标

配置Couchbase目标以将数据写入Couchbase数据库。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **Couchbase”**选项卡上，配置以下属性：

   | Couchbase属性                                                | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 节点清单                                                     | Couchbase群集中的一个或多个节点，以逗号分隔。                |
   | 桶                                                           | 要连接的现有Couchbase存储桶的名称。                          |
   | 键值超时（毫秒）                                             | 执行每个键值操作所允许的最大毫秒数。                         |
   | 连接超时（毫秒）                                             | 连接到Couchbase服务器所允许的最大毫秒数。                    |
   | 断开连接超时（毫秒）                                         | 正常关闭连接所允许的最大毫秒数。                             |
   | 高级环境设置                                                 | 与Couchbase Server连接的客户端设置。有关可用设置，请参阅[Couchbase Java SDK文档](https://docs.couchbase.com/java-sdk/2.7/client-settings.html)。 |
   | 使用TLS                                                      | 启用TLS的使用。                                              |
   | [密钥库文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z) | 密钥库文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。默认情况下，不使用任何密钥库。 |
   | 密钥库类型                                                   | 要使用的密钥库的类型。使用以下类型之一：Java密钥库文件（JKS）PKCS＃12（p12文件）默认值为Java密钥库文件（JKS）。 |
   | 密钥库密码                                                   | 密钥库文件的密码。密码是可选的，但建议使用。**提示：**为了保护敏感信息（如密码），可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 密钥库密钥算法                                               | 用于管理密钥库的算法。默认值为 SunX509。                     |
   | [信任库文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z) | 信任库文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。默认情况下，不使用任何信任库。 |
   | 信任库类型                                                   | 要使用的信任库的类型。使用以下类型之一：Java密钥库文件（JKS）PKCS＃12（p12文件）默认值为Java密钥库文件（JKS）。 |
   | 信任库密码                                                   | 信任库文件的密码。密码是可选的，但建议使用。**提示：**为了保护敏感信息（如密码），可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 信任库信任算法                                               | 用于管理信任库的算法。默认值为SunX509。                      |

3. 在“ **凭据”**选项卡上，配置以下属性：

   | 凭证属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 认证方式 | 使用Couchbase服务器进行身份验证的方法：存储桶身份验证-使用存储桶密码进行身份验证。用于Couchbase Server 4.x和更早版本。用户身份验证-使用Couchbase用户名和密码进行身份验证。用于Couchbase Server 5.0和更高版本。 |
   | 桶密码   | 如果存储桶在Couchbase数据库中受保护，则访问该存储桶的密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。可用于存储桶身份验证。 |
   | 用户名   | Couchbase用户名。可用于用户认证。                            |
   | 密码     | Couchbase密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。可用于用户认证。 |

4. 在“ **文档处理”**选项卡上，配置以下属性：

   | 文件属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 文件金钥                                                     | 目标写入数据的文档的唯一ID或键。例如，您可以指定一个解析为文档关键字的表达式。 |
   | 文档生存时间（秒）                                           | 创建后文档过期的秒数。0或空白表示文档没有过期。预设值为0。   |
   | [默认写操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Couchbase.html#concept_i1c_vby_g3b) | 在`sdc.operation.type`记录头属性或子文档操作中未设置操作类型时要使用的默认写操作：插入更新资料增补删除默认值为Upsert。 |
   | 不支持的操作处理                                             | `sdc.operation.type`不支持在记录头属性或子文档操作中定义的CRUD操作类型时采取的措施：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。使用默认操作-使用默认操作将记录写入目标系统。 |
   | [使用CAS](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Couchbase.html#concept_ws2_15j_j3b) | 在写入记录之前，使用Couchbase比较和交换（CAS）值来检测与其他进程的冲突。 |
   | 允许写子文件                                                 | 允许写入子文档。                                             |
   | 子文件路径                                                   | 目标写入记录的子文档的路径。使用点表示法分隔路径中的组件。有关更多信息，请参见[Couchbase文档](https://docs.couchbase.com/java-sdk/2.7/subdocument-operations.html#path-syntax)。如果未指定，目标将写入完整文档。当允许写入子文档时可用。 |
   | 子文件操作                                                   | 子文档的写操作。目标支持以下对子文档的写操作：插入更换UPSERT删除ARRAY_PREPENDARRAY_APPENDARRAY_ADD_UNIQUE如果未指定，则目标使用`sdc.operation.type`记录头属性中指定的写操作 或默认写操作。当允许写入子文档时可用。 |
   | 复制到                                                       | 复制文档的次数。目标仅在同步内存中复制后才认为写入成功。使用默认值NONE，目标在考虑写入完成之前不会等待同步复制。Couchbase将异步复制数据。必须小于或等于为存储桶配置的副本数。 |
   | 坚持到                                                       | 将文档写入磁盘的节点数。在同步磁盘持久化之后，目标仅认为写入成功。使用默认值NONE时，目标在考虑写入完成之前不会等待同步持久性。Couchbase将异步保留数据。如果选择MASTER，则在文档保留在主节点上之后，目标位置就认为写入成功。必须小于或等于为存储桶配置的副本数加一。 |

5. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Couchbase.html#concept_djp_pxx_g3b) | 要写入的数据格式。使用以下数据格式之一：阿夫罗二元定界JSON格式原虫[SDC记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/SDCRecordFormat.html#concept_qkk_mwk_br)文本 |

6. 对于Avro数据，在“ **数据格式”**选项卡上，配置以下属性：

   | Avro物业             | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | Avro模式位置         | 写入数据时要使用的Avro模式定义的位置：在“管道配置”中-使用您在阶段配置中提供的架构。在记录头中-在avroSchema [记录头属性中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_lmn_gdc_1w)使用架构 。仅在为所有记录定义avroSchema属性时使用。Confluent Schema Registry-从Confluent Schema Registry检索架构。 |
   | Avro模式             | 用于写入数据的Avro模式定义。您可以选择使用该`runtime:loadResource` 函数来加载存储在运行时资源文件中的架构定义。 |
   | 注册架构             | 向Confluent Schema Registry注册新的Avro架构。                |
   | 架构注册表URL        | 汇合的架构注册表URL，用于查找架构或注册新架构。要添加URL，请单击 **添加**，然后以以下格式输入URL：`http://:` |
   | 基本身份验证用户信息 | 使用基本身份验证时连接到Confluent Schema Registry所需的用户信息。`schema.registry.basic.auth.user.info`使用以下格式从Schema Registry中的设置中输入密钥和机密 ：`:`**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 查找架构             | 在Confluent Schema Registry中查找架构的方法：主题-查找指定的Avro模式主题。架构ID-查找指定的Avro架构ID。 |
   | 模式主题             | Avro架构可以在Confluent Schema Registry中查找或注册。如果要查找的指定主题有多个架构版本，则目标对该主题使用最新的架构版本。要使用旧版本，请找到相应的架构ID，然后将“ **查找架构**依据**”** 属性设置为“架构ID”。 |
   | 架构编号             | 在Confluent Schema Registry中查找的Avro模式ID。              |
   | 包含架构             | 在每个文件中包含架构。**注意：**省略模式定义可以提高性能，但是需要适当的模式管理，以避免丢失与数据关联的模式的跟踪。 |
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

   | JSON属性 | 描述                     |
   | :------- | :----------------------- |
   | 字符集   | 写入数据时使用的字符集。 |

10. 对于protobuf数据，在“ **数据格式”**选项卡上，配置以下属性：

    | Protobuf属性       | 描述                                                         |
    | :----------------- | :----------------------------------------------------------- |
    | Protobuf描述符文件 | 要使用的描述符文件（.desc）。描述符文件必须位于Data Collector资源目录中`$SDC_RESOURCES`。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。 |
    | 讯息类型           | 写入数据时使用的消息类型的全限定名称。使用以下格式： `.`。使用在描述符文件中定义的消息类型。 |

11. 对于文本数据，在“ **数据格式”**选项卡上，配置以下属性：

    | 文字属性                       | 描述                                                         |
    | :----------------------------- | :----------------------------------------------------------- |
    | 文字栏位路径                   | 包含要写入的文本数据的字段。所有数据必须合并到指定字段中。   |
    | 记录分隔符                     | 用于分隔记录的字符。使用任何有效的Java字符串文字。例如，当写入Windows时，您可能会\r\n用来分隔记录。默认情况下，目标使用 \n。 |
    | 在失落的田野上                 | 当记录不包含文本字段时，确定目标是将丢失的字段报告为错误还是忽略该丢失的字段。 |
    | 如果没有文本，则插入记录分隔符 | 当配置为忽略缺少的文本字段时，插入配置的记录分隔符字符串以创建一个空行。如果未选择，则丢弃没有文本字段的记录。 |
    | 字符集                         | 写入数据时使用的字符集。                                     |