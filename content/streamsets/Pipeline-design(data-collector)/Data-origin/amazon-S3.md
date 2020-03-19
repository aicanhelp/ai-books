# 亚马逊S3

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310112458072.png) 资料收集器

Amazon S3原始读取存储在Amazon S3中的对象。对象名称必须共享前缀模式，并且应完全写成。要从Amazon SQS阅读消息，请使用[Amazon SQS消费者来源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonSQS.html#concept_xsh_knm_5bb)。Amazon S3源可以与多个线程并行处理对象。

**注意：** Amazon S3来源只能在独立管道中使用。要使用集群管道来读取Amazon S3，请在运行于Amazon EMR集群的集群EMR批管道中使用Hadoop FS源。或者，在运行于Hadoop（CDH）或Hortonworks Data Platform（HDP）集群的Cloudera分发上的集群批处理管道中使用Hadoop FS源。有关更多信息，请参阅[Amazon S3](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/AmazonS3Requirements.html#concept_opj_jmf_f2b)集群管道[要求](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/AmazonS3Requirements.html#concept_opj_jmf_f2b)。

使用Amazon S3来源，您可以定义区域，存储桶，前缀模式，可选的公共前缀和读取顺序。这些属性确定原始处理的对象。您可以选择在记录中包括Amazon S3对象元数据作为记录标题属性。

处理对象后或遇到错误时，原点可以保留，存档或删除对象。归档时，原点可以复制或移动对象。

管道停止时，Amazon S3原始记录会记录它停止读取的位置。当管道再次启动时，原点将从默认情况下停止的地方继续进行处理。您可以重置原点以处理所有请求的对象。

您可以配置源，以使用服务器端加密和客户提供的加密密钥解密存储在Amazon S3上的数据。您可以选择使用代理连接到Amazon S3。

源可以为事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

## AWS凭证

当Data Collector从Amazon S3来源读取数据时，它必须将凭证传递给Amazon Web Services。

使用以下方法之一来传递AWS凭证：

- IAM角色

  当执行数据收集器 在Amazon EC2实例上运行时，您可以使用AWS管理控制台为EC2实例配置IAM角色。Data Collector使用IAM实例配置文件凭证自动连接到AWS。

  要使用IAM角色，请不要配置“访问密钥ID”和“秘密访问密钥”属性。

  有关将IAM角色分配给EC2实例的更多信息，请参阅Amazon EC2文档。

- AWS访问密钥对

  当执行数据收集器未在Amazon EC2实例上运行或EC2实例不具有IAM角色时，您必须配置**访问密钥ID**和**秘密访问密钥** 属性。

  **提示：**为了保护敏感信息（例如访问密钥对）的安全，可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。

## 通用前缀，前缀模式和通配符

Amazon S3原始服务器将公共前缀附加到前缀模式，以定义原始服务器处理的对象。您可以指定精确的前缀模式，也可以使用Ant样式的路径模式来递归读取多个对象。

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

## 多线程处理

Amazon S3来源使用多个并发线程根据“线程数”属性来处理数据。

每个线程都从单个对象读取数据，并且每个对象一次最多只能有一个线程从中读取数据。对象的读取顺序基于“ [读取顺序”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonS3.html#concept_ltv_r3l_5q)属性的配置。

在管道运行时，每个线程都连接到原始系统，创建一批数据，然后将其传递给可用的管道运行器。管道运行器是无源管道实例 - 管道的实例，包括管道中的所有处理器，执行程序和目的地，并在源之后处理所有管道处理。

每个管道运行程序一次处理一个批处理，就像在单个线程上运行的管道一样。当数据流减慢时，管道运行器会闲置等待，直到需要它们为止，并定期生成一个空批。您可以配置“运行者空闲时间”管道属性来指定间隔或选择退出空批次生成。

多线程管道保留每个批处理中的记录顺序，就像单线程管道一样。但是由于批处理 是由不同的流水线处理程序处理的，因此无法确保将批处理写入目的地的顺序。

例如，假设您将原点配置为使用五个线程以最后修改的时间戳的顺序读取对象。启动管道时，原始节点将创建五个线程，而Data Collector 会创建匹配数量的管道运行器。

Amazon S3源为五个最早的对象分配一个线程。每个线程处理其分配的对象，将成批的数据传递到源。接收到数据后，原点将批处理传递给每个管道运行器进行处理。

线程完成对象的处理后，源将根据上次修改的时间戳将线程分配给下一个对象，直到处理完所有对象。

有关多线程管道的更多信息，请参见《[多线程管道概述》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Multithreaded_Pipelines/MultithreadedPipelines.html#concept_zpp_2xc_py)。

## 记录标题属性

当Amazon S3原始处理Avro数据时，它将在AvroSchema记录标题属性中包含Avro架构。您还可以配置源，以在记录标题属性中包括Amazon S3对象元数据。

您可以使用`record:attribute`或 `record:attributeOrDefault`函数来访问属性中的信息。有关使用记录标题属性的更多信息，请参见[使用标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_rd2_ghz_dz)。

### 记录标题属性中的对象元数据

您可以在记录标题属性中包括Amazon S3对象元数据。当您想使用信息来帮助处理记录时，请包括元数据。例如，如果您要基于上次修改的时间戳将记录路由到管道的不同分支，则可能包含元数据。

使用“ **包括元数据”**属性可以在记录标题属性中包括元数据。在记录标题属性中包含元数据时，Amazon S3来源包括以下信息：

- 系统定义的元数据

  来源包括以下系统定义的元数据：名称-对象名称。存储桶和前缀信息如下：`//`缓存控制内容配置内容编码内容长度内容MD5内容范围内容类型标签过期上一次更改

  有关Amazon S3系统定义的元数据的更多信息，请参阅Amazon S3文档。

- 用户定义的元数据

  如果可用，Amazon S3来源还将在记录标题属性中包括用户定义的元数据。

  Amazon S3要求使用以下前缀来命名用户定义的元数据：`x-amz-meta-`。

  生成记录头属性时，来源会省略前缀。

  例如，如果您有一个名为“ `x-amz-meta-extraInfo`”的用户定义的元数据，则原始名称将记录头属性命名如下：`extraInfo`。

有关记录标题属性的更多信息，请参见[记录标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)。

## 阅读订单

Amazon S3源根据对象密钥名称或最后修改的时间戳以升序读取对象。为了在读取大量对象时获得最佳性能，请配置源以根据键名读取对象。

您可以配置以下读取顺序之一：

- 按字典顺序升序的键名

  Amazon S3源可以根据键名按字典顺序升序读取对象。请注意，按字典顺序升序读取数字1到11如下：

  `1, 10, 11, 2, 3, 4... 9`

  例如，您将Amazon S3原点配置为使用基于键名的字典升序从以下存储桶，通用前缀和前缀模式中读取：`Bucket: WebServer Common Prefix: 2016/ Prefix Pattern: **/web*.log`

  原点按以下顺序读取以下对象：`s3://WebServer/2016/February/web-10.log s3://WebServer/2016/February/web-11.log s3://WebServer/2016/February/web-5.log s3://WebServer/2016/February/web-6.log s3://WebServer/2016/February/web-7.log s3://WebServer/2016/February/web-8.log s3://WebServer/2016/February/web-9.log s3://WebServer/2016/January/web-1.log s3://WebServer/2016/January/web-2.log s3://WebServer/2016/January/web-3.log s3://WebServer/2016/January/web-4.log`

  要以逻辑上和字典上的升序读取这些对象，可以将前导零添加到文件命名约定中，如下所示：`s3://WebServer/2016/February/web-0005.log s3://WebServer/2016/February/web-0006.log ... s3://WebServer/2016/February/web-0010.log s3://WebServer/2016/February/web-0011.log s3://WebServer/2016/January/web-0001.log s3://WebServer/2016/January/web-0002.log s3://WebServer/2016/January/web-0003.log s3://WebServer/2016/January/web-0004.log`

- 上次修改的时间戳

  Amazon S3源可以根据上次修改的时间戳以升序读取对象。当启动管道时，原点将使用与通用前缀和前缀模式匹配的最早的对象开始处理数据，然后按时间顺序进行。如果两个或多个对象具有相同的时间戳记，则源按字典名的顺序按字典顺序升序处理这些对象。

  要处理包含比处理的对象更早的时间戳记的对象，请重置原点以读取所有可用的对象。

  例如，您将原点配置为`ServerEast`使用`LogFiles/`通用前缀和`*.log`前缀模式从存储桶中 读取 。您需要根据最后修改的时间戳，使用升序处理来自两个不同服务器的以下日志文件：`s3://ServerEast/LogFiles/fileA.log        04-30-2016 12:03:23 s3://ServerEast/LogFiles/fileB.log        04-30-2016 15:34:51 s3://ServerEast/LogFiles/file1.log        04-30-2016 12:00:00 s3://ServerEast/LogFiles/file2.log        04-30-2016 18:39:44`原点按时间戳顺序读取这些对象，如下所示：`s3://ServerEast/LogFiles/file1.log        04-30-2016 12:00:00 s3://ServerEast/LogFiles/fileA.log        04-30-2016 12:03:23 s3://ServerEast/LogFiles/fileB.log        04-30-2016 15:34:51 s3://ServerEast/LogFiles/file2.log        04-30-2016 18:39:44`如果新对象的时间戳为04-29-2016 12:00:00，则除非您重置原点，否则Amazon S3原点不会处理该对象。

## 缓冲区限制和错误处理

Amazon S3来源使用缓冲区将对象读取到内存中以生成记录。缓冲区的大小决定了可以处理的记录的最大大小。

缓冲区限制有助于防止内存不足错误。当Data Collector计算机上的内存受到限制时，请减小缓冲区限制。当有可用内存时，增加缓冲区限制以处理较大的记录。

当记录大于指定的限制时，源将根据阶段错误处理来处理对象：

- 丢弃

  源丢弃该记录和对象中的所有剩余记录，然后继续处理下一个对象。

- 发送到错误

  出现缓冲区限制错误时，原点无法将记录发送到管道以进行错误处理，因为它无法完全处理记录。而是在原点显示一条消息，指示在管道历史记录中发生缓冲区溢出错误。如果为该阶段配置了错误目录，则原点会将对象移动到错误目录并继续处理下一个对象。

- 停止管道

  原点停止管道，并显示一条消息，指示发生缓冲区溢出错误。该消息包括发生缓冲区溢出错误的对象和偏移量。该信息显示在管道历史记录中。

**注意：**您也可以检查Data Collector日志文件以获取错误详细信息。

## 服务器端加密

您可以配置源以使用Amazon Web Services服务器端加密来解密存储在Amazon S3上的数据。

当配置为服务器端加密时，源使用客户提供的加密密钥来解密数据。要使用服务器端加密，请提供以下信息：

- Base64编码的256位加密密钥
- 使用RFC 1321对加密密钥进行Base64编码的128位MD5摘要

有关在原始系统中实施客户提供的加密密钥的信息，请参阅Amazon S3文档。

## 事件产生

Amazon S3源可以生成您可以在事件流中使用的事件。启用事件生成后，在每次开始处理或完成读取对象时以及在对所有可用数据进行所有处理之后，经过配置的批处理等待时间后，源都会生成事件记录。

Amazon S3事件可以任何逻辑方式使用。例如：

- 当原始完成处理可用数据时，使用Pipeline Finisher执行程序停止管道并将管道转换为Finished状态。

  重新启动由Pipeline Finisher执行程序停止的管道时，原点将从上次保存的偏移开始继续处理，除非您重置原点。

  有关示例，请参见[案例研究：停止管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_kff_ykv_lz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

Amazon S3来源生成的事件记录具有以下与事件相关的记录标题属性。记录标题属性存储为字符串值：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型之一：新文件-当原点开始处理新对象时生成。finish-file-当原点完成处理对象时生成。no-more-data-原点完成对所有可用对象的处理并且经过了为“批处理等待时间”配置的秒数后生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

Amazon S3来源可以生成以下类型的事件记录：

- 新文件

  Amazon S3原始服务器开始处理新对象时会生成新文件事件记录。

  新文件事件记录的`sdc.event.type`记录头属性设置为，`new-file`并包含以下字段：事件记录字段描述文件路径原点开始处理的对象的路径和名称。

- 成品文件

  当完成处理对象时，Amazon S3源将生成一个完成文件事件记录。

  成品文件事件记录的`sdc.event.type`记录头属性设置为`finished-file`，包括以下字段：事件记录字段描述文件路径原点完成处理的对象的路径和名称。记录数从对象成功生成的记录数。错误计数从对象生成的错误记录数。

- 没有更多数据

  当Amazon S3原始服务器完成对所有可用记录的处理，并且经过了为“批处理等待时间”配置的秒数，而似乎没有任何新对象要处理时，它将生成无数据事件记录。

  没有数据事件记录的`sdc.event.type`记录头属性设置为`no-more-data`，包括以下字段：事件记录字段描述记录数自管道启动或自上一次创建no-more-data事件以来成功生成的记录数。错误计数自管道启动或自上一次创建no-more-data事件以来生成的错误记录数。文件计数原点尝试处理的对象数。可以包含无法处理或未完全处理的对象。

## 资料格式

Amazon S3原始服务器根据数据格式对数据的处理方式有所不同。源处理以下类型的数据：

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

- XML格式

  根据用户定义的定界符元素生成记录。在根元素下直接使用XML元素或定义简化的XPath表达式。如果未定义定界符元素，则源会将XML文件视为单个记录。

  默认情况下，生成的记录包括XML属性和名称空间声明作为记录中的字段。您可以配置阶段以将它们包括在记录中作为字段属性。

  您可以在字段属性中包含每个解析的XML元素和XML属性的XPath信息。这还将每个名称空间放置在xmlns记录头属性中。

  **注意：** 只有在目标中使用SDC RPC数据格式时，字段属性和记录头属性才会自动写入目标系统。有关使用字段属性和记录标题属性以及如何将它们包括在记录中的更多信息，请参见[字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)和[记录标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)。

  当记录超过用户定义的最大记录长度时，原点无法继续处理文件中的数据。已经从文件处理的记录将传递到管道。然后，原点的行为基于为该阶段配置的错误处理：丢弃-原点继续处理下一个文件，将部分处理的文件保留在目录中。错误-原点继续处理下一个文件。如果为该阶段配置了后处理错误目录，则原点会将部分处理的文件移动到错误目录。否则，它将文件保留在目录中。停止管道-原点停止管道。

  使用XML数据格式来处理有效的XML文档。有关XML处理的更多信息，请参见[阅读和处理XML数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_lty_42b_dy)。**提示：** 如果要处理无效的XML文档，则可以尝试将文本数据格式与自定义分隔符一起使用。有关更多信息，请参见[使用自定义分隔符处理XML数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/TextCDelim.html#concept_okt_kmg_jx)。

## 配置Amazon S3来源

配置Amazon S3源，以从Amazon S3中的对象读取数据。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonS3.html#concept_vtn_ty4_jbb) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **Amazon S3”**选项卡上，配置以下属性：

   | Amazon S3属性                                                | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [访问密钥ID](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonS3.html#concept_uhy_ttg_vw) | AWS访问密钥ID。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。 |
   | [秘密访问密钥](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonS3.html#concept_uhy_ttg_vw) | AWS秘密访问密钥。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。 |
   | 区域                                                         | Amazon S3地区。                                              |
   | 终点                                                         | 当您为区域选择“其他”时要连接的端点。输入端点名称。           |
   | 桶                                                           | 包含要读取的对象的存储桶。**注意：**存储桶名称必须符合DNS。有关存储桶命名约定的更多信息，请参阅[Amazon S3文档](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html)。 |
   | [通用前缀](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonS3.html#concept_elk_jr4_ht) | 描述对象位置的可选通用前缀。定义后，公共前缀将充当前缀模式的根。 |
   | 定界符                                                       | Amazon S3用于定义前缀层次结构的定界符。默认为斜杠（/）。     |
   | [包含元数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonS3.html#concept_inh_qjx_yw) | 在记录头属性中包含系统定义的元数据和用户定义的元数据。       |
   | [前缀模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonS3.html#concept_elk_jr4_ht) | 前缀模式，描述要处理的对象。您可以包括对象的完整路径。您还可以使用Ant样式的路径模式来递归读取对象。 |
   | [阅读订单](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonS3.html#concept_ltv_r3l_5q) | 读取对象时使用的顺序：字典上的升序键名-根据键名按字典上的升序读取对象。上次修改的时间戳-根据上次修改的时间戳以升序读取对象。当对象具有匹配的时间戳时，将根据键名按字典顺序升序读取对象。为了在读取大量对象时获得最佳性能，请根据键名使用字典顺序。 |
   | 文件池大小                                                   | 在加载和排序S3上存在的所有文件后，原始存储在内存中以进行处理的最大文件数。当Data Collector资源允许时，增加此数目可以提高管道性能。默认值为100。 |
   | 缓冲区限制（KB）                                             | 最大缓冲区大小。缓冲区大小确定可以处理的记录的大小。减少Data Collector计算机上的内存有限的时间。可用时增加以处理更大的记录。默认值为128 KB。 |
   | 最大批次大小（记录）                                         | 一次处理的最大记录数。接受的值最高为Data Collector的最大批处理大小。默认值是1000 数据采集器默认设置为1000。 |
   | [批处理等待时间（毫秒）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ypd_vgr_5q) | 发送部分或空批次之前要等待的毫秒数。                         |

3. 要使用服务器端加密，请在“ **SSE”**选项卡上配置以下属性：

   | 上交所物业                                                   | 描述                                               |
   | :----------------------------------------------------------- | :------------------------------------------------- |
   | [使用服务器端加密](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonS3.html#concept_ctg_xh4_sx) | 启用服务器端加密的使用。                           |
   | 客户加密密钥                                                 | Base64编码的256位加密密钥。                        |
   | 客户加密密钥MD5                                              | 使用RFC 1321的加密密钥的Base64编码的128位MD5摘要。 |

4. 在“ **错误处理”**选项卡上，配置以下属性：

   | 错误处理属性 | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 错误处理选项 | 处理对象时发生错误时采取的措施：无-将对象保持在原位。存档-将对象复制或移动到另一个前缀或存储桶。删除-删除对象。归档处理的对象时，最佳实践是也归档无法处理的对象。 |
   | 存档选项     | 归档无法处理的对象时采取的措施。您可以将对象复制或移动到另一个前缀或存储桶。使用其他前缀时，请输入前缀。使用其他存储桶时，请输入前缀和存储桶。复制对象会将原始对象保留在原位。 |
   | 错误前缀     | 不能处理的对象的前缀。                                       |
   | 错误桶       | 为无法处理的对象存储桶。                                     |

5. 在“ **后处理”**选项卡上，配置以下属性：

   | 后处理属性   | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 后处理选项   | 成功处理对象后采取的措施：无-将对象保持在原位。存档-将对象复制或移动到另一个位置。删除-删除对象。 |
   | 存档选项     | 归档已处理对象时要执行的操作。您可以将对象复制或移动到另一个前缀或存储桶。使用其他前缀时，请输入前缀。使用其他存储桶时，请输入前缀和存储桶。复制对象会将原始对象保留在原位。 |
   | 后期处理前缀 | 处理对象的前缀。                                             |
   | 后处理桶     | 用于处理对象的存储桶。                                       |

6. 在“ **高级”**选项卡上，可以选择配置线程数和代理信息：

   | 先进物业                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [线程数](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonS3.html#concept_pcl_nwn_qbb) | 原点生成并用于多线程处理的线程数。默认值为1。                |
   | 连接超时                                                     | 关闭连接之前等待响应的秒数。默认值为10秒。                   |
   | 套接字超时                                                   | 等待查询响应的秒数。                                         |
   | 重试计数                                                     | 重试请求的最大次数。                                         |
   | 使用代理服务器                                               | 指定是否使用代理进行连接。                                   |
   | 代理主机                                                     | 代理主机。                                                   |
   | 代理端口                                                     | 代理端口。                                                   |
   | 代理用户                                                     | 代理凭据的用户名。                                           |
   | 代理密码                                                     | 代理凭证的密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |

7. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonS3.html#concept_gz5_dqw_yq) | 源文件的数据格式。使用以下格式之一：阿夫罗定界电子表格JSON格式记录原虫[SDC记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/SDCRecordFormat.html#concept_qkk_mwk_br)文本[整个档案](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_nfc_qkh_xw)XML格式 |

8. 对于Avro数据，在“ **数据格式”**选项卡上，配置以下属性：

   | Avro物业             | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | Avro模式位置         | 处理数据时要使用的Avro模式定义的位置：消息/数据包含架构-在文件中使用架构。在“管道配置”中-使用阶段配置中提供的架构。Confluent Schema Registry-从Confluent Schema Registry检索架构。在阶段配置中或在Confluent Schema Registry中使用架构可以提高性能。 |
   | Avro模式             | 用于处理数据的Avro模式定义。覆盖与数据关联的任何现有模式定义。您可以选择使用该 `runtime:loadResource`函数来加载存储在运行时资源文件中的架构定义。 |
   | 架构注册表URL        | 汇合的架构注册表URL，用于查找架构。要添加URL，请单击**添加**，然后以以下格式输入URL：`http://:` |
   | 基本身份验证用户信息 | 使用基本身份验证时连接到Confluent Schema Registry所需的用户信息。`schema.registry.basic.auth.user.info`使用以下格式从Schema Registry中的设置中输入密钥和机密 ：`:`**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 查找架构             | 在Confluent Schema Registry中查找架构的方法：主题-查找指定的Avro模式主题。架构ID-查找指定的Avro架构ID。覆盖与数据关联的任何现有模式定义。 |
   | 模式主题             | Avro架构需要在Confluent Schema Registry中查找。如果指定的主题具有多个架构版本，则源使用该主题的最新架构版本。要使用旧版本，请找到相应的架构ID，然后将“ **查找架构**依据 **”**属性设置为“架构ID”。 |
   | 架构编号             | 在Confluent Schema Registry中查找的Avro模式ID。              |

9. 对于定界数据，在“ **数据格式”**选项卡上，配置以下属性：

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

10. 对于Excel文件，在“ **数据格式”**选项卡上，配置以下属性：

    | Excel属性                                                    | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [Excel标头选项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Excel.html) | 指示文件是否包含标题行以及是否忽略标题行。标题行必须是文件的第一行。 |

11. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

    | JSON属性                                                     | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
    | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |
    | JSON内容                                                     | JSON内容的类型。使用以下选项之一：对象数组多个物件           |
    | 最大对象长度（字符）                                         | JSON对象中的最大字符数。较长的对象将转移到管道以进行错误处理。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
    | 字符集                                                       | 要处理的文件的字符编码。                                     |
    | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

12. 对于日志数据，在“ **数据格式”**选项卡上，配置以下属性：

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

13. 对于protobuf数据，在“ **数据格式”**选项卡上，配置以下属性：

    | Protobuf属性                                                 | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
    | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |
    | Protobuf描述符文件                                           | 要使用的描述符文件（.desc）。描述符文件必须位于Data Collector资源目录中`$SDC_RESOURCES`。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。 |
    | 讯息类型                                                     | 读取数据时使用的消息类型的全限定名称。使用以下格式： `.`。使用在描述符文件中定义的消息类型。 |
    | 分隔消息                                                     | 指示一个文件是否可能包含多个protobuf消息。                   |

14. 对于“ SDC记录”数据，在“ **数据格式”**选项卡上，配置以下属性：

    | SDC记录属性                                                  | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
    | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |

15. 对于文本数据，在“ **数据格式”**选项卡上，配置以下属性：

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

16. 对于整个文件，在“ **数据格式”**选项卡上，配置以下属性：

    | 整个文件属性                                                 | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 缓冲区大小（字节）                                           | 用于传输数据的缓冲区大小。                                   |
    | [每秒速率](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_prp_jzd_py) | 要使用的传输速率。输入数字以指定速率（以字节/秒为单位）。使用表达式指定每秒使用不同度量单位的速率，例如$ {5 * MB}。使用-1退出此属性。默认情况下，原点不使用传输速率。 |
    | 验证校验和                                                   | 在读取期间验证校验和。                                       |

17. 对于XML数据，在“ **数据格式”**选项卡上，配置以下属性：

    | XML属性                                                      | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
    | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |
    | [分隔元素](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_tmc_4bc_dy) | 用于生成记录的定界符。省略定界符，将整个XML文档视为一条记录。使用以下之一：在根元素正下方的XML元素。使用不带尖括号（<>）的XML元素名称。例如，用msg代替<msg>。一个简化的XPath表达式，指定要使用的数据。使用简化的XPath表达式访问XML文档中更深的数据或需要更复杂访问方法的数据。有关有效语法的更多信息，请参见[简化的XPath语法](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_tmc_4bc_dy)。 |
    | [包含字段XPath](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_w3k_1ch_qz) | 在字段属性中包括每个解析的XML元素的XPath和XML属性。还包括xmlns记录头属性中的每个名称空间。如果未选中，则此信息不包含在记录中。默认情况下，未选择该属性。**注意：** 只有在目标中使用SDC RPC数据格式时，字段属性和记录头属性才会自动写入目标系统。有关使用字段属性和记录标题属性以及如何将它们包括在记录中的更多信息，请参见[字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)和[记录标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)。 |
    | 命名空间                                                     | 解析XML文档时使用的命名空间前缀和URI。当所使用的XML元素包含名称空间前缀或XPath表达式包含名称空间时，定义名称空间。有关将名称空间与XML元素一起使用的信息，请参见[将XML元素与名称空间一起使用](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_ilc_r3g_2y)。有关将名称空间与XPath表达式一起使用的信息，请参阅《[将XPath表达式与名称](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_mkk_3zj_dy)空间一起[使用》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_mkk_3zj_dy)。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以添加其他名称空间。 |
    | 输出字段属性                                                 | 在记录中包括XML属性和名称空间声明作为字段属性。如果未选择，则XML属性和名称空间声明作为字段包含在记录中。**注意：** 只有在目标中使用SDC RPC数据格式时，字段属性才会自动包含在写入目标系统的记录中。有关使用字段属性的更多信息，请参见[字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)。默认情况下，未选择该属性。 |
    | 最大记录长度（字符）                                         | 记录中的最大字符数。较长的记录将转移到管道以进行错误处理。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
    | 字符集                                                       | 要处理的文件的字符编码。                                     |
    | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |