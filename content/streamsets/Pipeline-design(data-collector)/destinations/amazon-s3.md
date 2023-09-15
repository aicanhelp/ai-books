# 亚马逊S3

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310182647569.png) 资料收集器![img](imgs/icon-Edge-20200310182647655.png) 数据收集器边缘

Amazon S3目标将数据写入Amazon S3。要将数据写入Amazon Kinesis Firehose交付系统，请使用Kinesis Firehose目标。要将数据写入Amazon Kinesis Streams，请使用Kinesis Producer目标。

使用Amazon S3目标，您可以配置区域，存储桶和公共前缀来定义在何处写入对象。您可以使用分区前缀来指定要写入的S3分区。您还可以为对象名称配置前缀和后缀，为阶段配置时间基准和数据时区。

当写入多个前缀时，Amazon S3目标可以异步写入数据以提高性能。您可以配置高级属性来调整性能。

您可以将目标配置为使用Amazon Web Services服务器端加密来保护写入Amazon S3的数据。写入Amazon S3时，您还可以使用代理用户并使用gzip压缩数据。

Amazon S3目标为写入Amazon S3的每批数据创建一个对象。

目的地可以为事件流生成事件。有关事件框架的更多信息，请参见《[数据流触发器概述》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

## AWS凭证

当Data Collector将数据写入Amazon S3目标时，它必须将凭证传递给Amazon Web Services。

使用以下方法之一来传递AWS凭证：

- IAM角色

  当执行数据收集器 在Amazon EC2实例上运行时，您可以使用AWS管理控制台为EC2实例配置IAM角色。Data Collector使用IAM实例配置文件凭证自动连接到AWS。

  要使用IAM角色，请不要在目标中配置访问密钥ID和秘密访问密钥属性。

  有关将IAM角色分配给EC2实例的更多信息，请参阅Amazon EC2文档。

- AWS访问密钥对

  当执行数据收集器未在Amazon EC2实例上运行或EC2实例不具有IAM角色时，您必须 在目标中指定**访问密钥ID**和**秘密访问密钥**属性。

  **提示：**为了保护敏感信息（例如访问密钥对）的安全，可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。

## 桶

在配置应在其中写入记录的存储桶时，可以指定确切的存储桶名称，也可以使用计算结果为存储桶名称的表达式。

**注意：**存储桶名称必须符合DNS。有关存储桶命名约定的更多信息，请参阅[Amazon S3文档](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html)。

例如，要基于“类型”字段中的数据写入存储桶，可以使用以下表达式定义存储桶：`${record:value('/Type)}`。

使用此表达式，目标将根据“类型”字段中的数据将记录写入存储桶。如果表达式计算得出的存储桶不存在，则目标将根据阶段中配置的错误处理来处理记录。

如果在表达式中使用datetime变量，请确保为该阶段配置[时间基准](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_qtb_njg_vw)。

## 分区前缀

您可以使用分区前缀按分区组织对象。您可以使用分区前缀来写入现有分区或根据需要创建新分区。当分区前缀中指定的分区不存在时，目标将创建该分区。

您可以为分区前缀指定确切的分区名称，也可以使用计算结果为分区名称的表达式。

例如，要基于“国家/地区”字段中的数据写入分区，可以使用以下表达式作为分区前缀： `${record:value('/Country')}`。

使用此表达式，目标将根据记录中的国家/地区数据将记录写入分区，并为尚无分区的国家/地区创建分区。

如果在表达式中使用datetime变量，请确保为该阶段配置[时间基准](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_qtb_njg_vw)。您可能还需要配置数据时区属性。

## 基于时间的存储桶和分区前缀的时间基础和数据时区

时基和数据时区包括Amazon S3目标将记录写入基于时间的存储桶或分区前缀所用的时间。如果配置的存储桶或分区前缀不包括基于时间的功能，则可以忽略时间基准属性。

当存储桶或分区前缀包含日期时间变量（例如`${YYYY()}`或`${DD()}`）或包含计算结果为日期时间值的表达式 （例如）时，它具有时间分量。`${record:valueOrDefault("/Timestamp")}.`

有关日期[时间变量的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/DateTimeVariables.html#concept_gh4_qd2_sv)详细信息，请参见[日期时间变量](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/DateTimeVariables.html#concept_gh4_qd2_sv)。

您可以使用以下时间作为时间基础：

- 处理时间

  当您将处理时间用作时间基准时，目标将根据处理时间以及配置的存储桶和分区前缀执行写操作。处理时间是默认情况下与运行管道的Data Collector关联的时间。您可以通过配置数据时区属性来指定其他时区。要将处理时间用作时间基准，请使用以下表达式：`${time:now()}`这是默认的时间基准。

- 记录时间

  当您使用与记录关联的时间作为时间基准时，您可以在记录中指定日期字段。目标根据与记录关联的日期时间写入数据，并调整为“数据时区”属性指定的值。

  要使用与记录关联的时间，请使用一个表达式，该表达式调用一个字段并解析为日期时间值，例如 `${record:value("/Timestamp")}`。

例如，假设您使用以下datetime变量定义Partition Prefix属性：

```
logs-${YYYY()}-${MM()}-${DD()}
```

如果使用处理时间作为时间基准，则目标将根据处理每个记录的时间将记录写入分区。如果使用与数据相关联的时间（例如事务时间戳记），那么目标将根据该时间戳记将记录写入分区。如果不存在分区，则目标将创建所需的分区。

或者，说您按以下方式定义Bucket属性：

```
${YYYY()}-${MM()}
```

如果使用处理时间作为时间基准，则目标将根据处理每个记录的时间将记录写入存储桶。如果您使用与数据相关联的时间（例如事务时间戳记），那么目标将根据该时间戳记将记录写入存储桶。如果存储桶不存在，则目标将根据为该阶段配置的错误记录处理该记录。

## 对象名称

Amazon S3目标为每一批写入的数据创建一个对象或文件。对象通常使用以下命名约定：

```
<prefix>-<UTC timestamp>-<counter>
```

例如：`sdc-1462405014177-1`。

您配置对象名称前缀。

UTC时间戳是创建对象的时间，以毫秒为单位。在同一毫秒内创建多个对象时使用计数器。

您可以选择为所有数据格式（整个文件除外）配置一个对象名称后缀。配置后缀时，后缀将在一段时间后添加到对象名称中，如下所示：

```
<prefix>-<UTC timestamp>-<counter>.<optional suffix>
```

例如：`sdc-1462405014177-1.txt`。

### 整个文件名

使用整个文件数据格式时，对象名称前缀是可选的。整个文件是基于“文件名表达式”整个文件属性来命名的。如果配置对象名称前缀，则整个文件的命名如下：

```
<prefix>-<results of the file name expression>
```

## 事件产生

Amazon S3目标可以生成可在事件流中使用的事件。启用事件生成后，Amazon S3目标每次在写入对象或流式传输整个文件后都会生成事件记录。

![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中，目标流传输整个文件后不会生成事件记录。

Amazon S3事件可以任何逻辑方式使用。例如：

- 使用[Amazon S3执行程序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/AmazonS3.html#concept_mvh_bnm_f1b)在收到事件后将元数据添加到关闭的对象或整个文件中。

- 使用[Spark执行](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Shell.html#concept_jsr_zpw_tz)程序在收到事件后运行Spark应用程序。

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

Amazon S3目标生成的事件记录具有以下与事件相关的记录头属性。记录标题属性存储为字符串值：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型之一：S3 Object Writed（写入的S3对象）-当目标完成对对象的写入时生成。wholeFileProcessed-在目标完成流式传输整个文件时生成。![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中不可用。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

Amazon S3目标可以生成以下类型的事件记录：

- 书面对象

  当目的地完成对对象的写入时，它会生成一个对象写入事件记录。

  对象写的事件记录的`sdc.event.type`记录头属性设置为`S3 Object Written`，包括以下字段：领域描述桶对象所在的存储桶。objectKey写入的对象密钥名称。recordCount写入对象的记录数。

- 整个文件已处理

  ![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中不可用。目标在完成流式传输整个文件时会生成事件记录。整个文件事件记录的 `sdc.event.type`记录头属性设置为 `wholeFileProcessed`，包括以下字段：领域描述sourceFileInfo关于已处理的原始整个文件的属性映射。属性名称取决于源系统提供的信息。targetFileInfo关于写入目标系统的整个文件的属性映射。这些属性包括：bucket-写入整个文件的存储桶。objectKey-写入的对象密钥名称。校验和为写入文件生成的校验和。仅当您将目标配置为在事件记录中包括校验和时才包括。校验和算法用于生成校验和的算法。仅当您将目标配置为在事件记录中包括校验和时才包括。

## 服务器端加密

您可以将目标配置为使用Amazon Web Services服务器端加密（SSE）来保护写入Amazon S3的数据。在为服务器端加密配置后，目标会将所需的服务器端加密配置值传递给Amazon S3。Amazon S3使用这些值来加密写入Amazon S3的数据。

在为目标启用服务器端加密时，您选择以下Amazon S3管理加密密钥的方式之一：

- Amazon S3托管的加密密钥（SSE-S3）

  当您将服务器端加密与Amazon S3托管密钥一起使用时，Amazon S3会为您管理加密密钥。

- AWS KMS管理的加密密钥（SSE-KMS）

  当您将服务器端加密与AWS Key Management Service（KMS）结合使用时，请指定要使用的AWS KMS主加密密钥的Amazon资源名称（ARN）。您还可以指定用于加密上下文的键值对。

- 客户提供的加密密钥（SSE-C）

  当使用服务器端加密和客户提供的密钥时，请指定以下信息：Base64编码的256位加密密钥使用RFC 1321对加密密钥进行Base64编码的128位MD5摘要

有关使用服务器端加密保护Amazon S3中的数据的更多信息，请参阅Amazon S3文档。

## 资料格式

Amazon S3目标根据您选择的数据格式将数据写入Amazon S3。

![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中， 目标仅支持Binary，JSON，SDC Record，Text和Whole File数据格式。

Amazon S3目标按以下方式处理数据格式：

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

  目标将记录作为JSON数据写入。您可以使用以下格式之一：数组-每个文件都包含一个数组。在数组中，每个元素都是每个记录的JSON表示形式。多个对象-每个文件都包含多个JSON对象。每个对象都是记录的JSON表示形式。

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

- 整个档案

  将整个文件流式传输到目标系统。目标将数据写入阶段中定义的文件和位置。如果已经存在相同名称的文件，则可以将目标配置为覆盖现有文件或将当前文件发送给错误文件。

  写入的文件使用目标系统中定义的默认权限。

  您可以配置目标以生成写入文件的校验和，并将校验和信息传递到事件记录中的目标系统。

  有关整个文件数据格式的更多信息，请参见[整个文件数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_nfc_qkh_xw)。

## 配置Amazon S3目标

配置Amazon S3目标以将对象写入Amazon S3。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_aqq_tt2_px) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击**添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **Amazon S3”**选项卡上，配置以下属性：

   | Amazon S3属性                                                | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [访问密钥ID](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_bmp_zlg_vw) | AWS访问密钥ID。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。 |
   | [秘密访问密钥](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_bmp_zlg_vw) | AWS秘密访问密钥。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。 |
   | 区域                                                         | Amazon S3地区。                                              |
   | 终点                                                         | 当您为区域选择“其他”时要连接的端点。输入端点名称。           |
   | [桶](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_bnp_gwp_f1b) | 写入记录时要使用的存储桶。输入存储桶名称或定义一个计算结果为存储桶名称的表达式。在表达式中使用datetime变量时，请确保为该阶段配置时间基准。 |
   | 通用前缀                                                     | 确定对象写入位置的通用前缀。                                 |
   | 定界符                                                       | Amazon S3用于定义前缀层次结构的定界符。默认为斜杠（/）。     |
   | [分区前缀](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_qw5_gtq_yv) | 可选的分区前缀，用于指定要使用的分区。使用特定的分区前缀或定义一个计算结果为分区前缀的表达式。在表达式中使用datetime变量时，请确保为该阶段配置时间基准。 |
   | [数据时区](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_qtb_njg_vw) | 目标系统的时区。与时间一起使用以解析基于时间的存储桶或分区前缀中的日期时间。 |
   | [时间基础](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_qtb_njg_vw) | 用于写入基于时间的存储桶或分区前缀的时间基准。使用以下表达式之一：`${time:now()}` -将处理时间与指定的数据时区一起用作时间基准。该表达式调用一个字段并解析为日期时间值，例如 `${record:value()}`。使用与记录关联的时间作为时间基础，并针对指定的数据时区进行调整。如果“存储桶”和“分区前缀”属性没有时间分量，则可以忽略此属性。默认值为`${time:now()}`。 |
   | [对象名称前缀](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_lkp_jd3_yv) | 定义目标写入的对象名称的前缀。默认情况下，对象名称的开头`sdc`如下：`sdc--`。 |
   | 对象名称后缀                                                 | 用于对象名称的后缀，例如txt或json。使用目标时，目标会添加一个句点和配置的后缀，如下所示：<对象名称>。<后缀>。您可以在后缀中包含句点，但不要以句点开头。不允许使用正斜杠。不适用于整个文件数据格式。 |
   | 用Gzip压缩                                                   | 在写入Amazon S3之前，使用gzip压缩文件。                      |

3. 在“ **SSE”**选项卡上，可以选择启用服务器端加密：

   | 上交所物业                                                   | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [使用服务器端加密](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_adm_kn1_mw) | 启用服务器端加密。                                           |
   | 服务器端加密选项                                             | Amazon S3用于管理加密密钥的选项：SSE-S3-使用Amazon S3托管密钥。SSE-KMS-使用Amazon Web Services KMS管理的密钥。SSE-C-使用客户提供的密钥。默认值为SSE-S3。 |
   | AWS KMS密钥ARN                                               | AWS KMS主加密密钥的Amazon资源名称（ARN）。使用以下格式：`:::::/`仅用于SSE-KMS加密。 |
   | 加密上下文                                                   | 用于加密上下文的键值对。单击**添加**以添加键值对。仅用于SSE-KMS加密。 |
   | 客户加密密钥                                                 | 要使用的256位和Base64编码的加密密钥。仅用于SSE-C加密。       |
   | 客户加密密钥MD5                                              | 根据RFC 1321，加密密钥的128位和Base64编码的MD5摘要。仅用于SSE-C加密。 |

4. 在“ **高级”**选项卡上，可以选择配置代理信息和调整性能：

   | 先进物业             | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | 连接超时             | 关闭连接之前等待响应的秒数。默认值为10秒。                   |
   | 套接字超时           | 等待查询响应的秒数。                                         |
   | 重试计数             | 重试请求的最大次数。                                         |
   | 使用代理服务器       | 指定是否使用代理进行连接。                                   |
   | 代理主机             | 代理主机。                                                   |
   | 代理端口             | 代理端口。                                                   |
   | 代理用户             | 代理凭据的用户名。                                           |
   | 代理密码             | 代理凭证的密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 并行上传的线程池大小 | 并行上传的线程池的大小。在写入多个分区并在多个部分中写入大型对象时使用。当写入多个分区时，将此属性设置为要写入的分区数可以提高性能。有关此属性和以下属性的更多信息，请参阅Amazon S3 TransferManager文档。 |
   | 分段上传阈值         | 目标使用分段上传的最小批处理大小（以字节为单位）。           |
   | 最小上传部分大小     | 分段上传的最小分段大小（以字节为单位）。                     |

5. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_k2z_ncx_rt) | 数据格式写入数据：阿夫罗二元定界JSON格式原虫[SDC记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/SDCRecordFormat.html#concept_qkk_mwk_br)文本[整个档案](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_nfc_qkh_xw)![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中， 目标仅支持Binary，JSON，SDC Record，Text和Whole File数据格式。 |

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

   | JSON属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | JSON内容 | 写入JSON数据的方法：JSON对象数组-每个文件都包含一个数组。在数组中，每个元素都是每个记录的JSON表示形式。多个JSON对象-每个文件包含多个JSON对象。每个对象都是记录的JSON表示形式。 |
   | 字符集   | 写入数据时使用的字符集。                                     |

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

12. 对于整个文件，在“ **数据格式”**选项卡上，配置以下属性：

    | 整个文件属性                                                 | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 文件名表达                                                   | 用于文件名的表达式。有关如何根据输入文件名命名文件的提示，请参阅“ [编写整个文件”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_a2s_4jw_1x)。 |
    | 文件已存在                                                   | 当输出目录中已经存在同名文件时采取的措施。使用以下选项之一：发送到错误-根据阶段错误记录处理来处理记录。覆盖-覆盖现有文件。 |
    | [在事件中包括校验和](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_ojv_sr4_vx) | 在整个文件事件记录中包括校验和信息。仅在目标生成事件记录时使用。 |
    | 校验和算法                                                   | 生成校验和的算法。                                           |