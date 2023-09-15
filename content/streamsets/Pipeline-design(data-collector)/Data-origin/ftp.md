# SFTP / FTP / FTPS客户端

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310174335372.png) 资料收集器

SFTP / FTP / FTPS客户端源使用安全文件传输协议（SFTP），文件传输协议（FTP）或FTP安全（FTPS）协议从服务器读取文件。

配置SFTP / FTP / FTPS客户端来源时，请指定文件在远程服务器上的驻留URL。您可以指定是否处理子目录中的文件，文件名模式以及要处理的第一个文件。您可以使用全局模式或正则表达式来定义要使用的文件名模式。

如果服务器需要身份验证，请为所使用的协议配置凭据。对于SFTP协议，源可能要求将服务器列在已知主机文件中。对于FTPS协议，源可以使用客户端证书向服务器进行身份验证，并且可以从FTPS服务器对证书进行身份验证。

如果原始文件在读取文件时遇到错误，则可以将原始文件配置为将文件下载到存档目录。

当管道停止时，SFTP / FTP / FTPS客户端起源记录在它停止读取的位置。当管道再次启动时，原点将从默认情况下停止的地方继续进行处理。您可以重置原点以处理所有请求的文件。

源可以为事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

处理文件后，原点可以保留，存档或删除文件。

**注意：** StreamSets已使用vsftpd 3.0测试了该阶段。

## 文件名模式和方式

使用文件名模式来定义SFTP / FTP / FTPS客户端源处理的文件。您可以使用全局模式或正则表达式来定义文件名模式。

SFTP / FTP / FTPS客户端起源根据文件名模式和文件名模式处理指定路径下的文件。处理子目录时，源使用相同的模式在子目录中定位文件名。原点不使用该模式来定位子目录。

指定全局模式时，可以使用UNIX样式的通配符，例如*或？。例如，该模式`??a`表示三个字符的文件名，以结尾`a`。该模式`*.txt`代表一个或多个以结尾的字符的文件名`.txt`。

在全局模式中，不能使用波浪号（〜）或斜杠（/）。您不能在模式的开头使用句点（。）。原点将句点视为模式中其他位置的文字。

源根据指定的读取顺序按顺序处理文件。

有关glob语法的更多信息，请参见[Oracle Java文档](https://docs.oracle.com/javase/tutorial/essential/io/fileOps.html#glob)。有关正则表达式的更多信息，请参见[正则表达式概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-RegEx/RegEx-Title.html#concept_vd4_nsc_gs)。

默认值为`*`，将处理所有文件。

## 阅读订单

SFTP / FTP / FTPS客户端源根据上次修改的时间戳以升序读取文件。当源是从辅助位置（而不是从创建和写入文件的目录）读取时，最后修改的时间戳记应该是文件移至要处理的目录时的时间戳。

**提示：**避免使用保留现有时间戳的命令来移动文件，例如`cp -p`。在某些情况下，例如在跨时区移动文件时，保留现有时间戳可能会出现问题。

当基于时间戳排序时，具有相同时间戳的任何文件都将根据文件名以字典顺序升序读取。

例如，当使用`log*.json`文件名模式读取文件时，源按以下顺序读取以下文件：

| `File Name`     | `Last Modified Timestamp` |
| --------------- | ------------------------- |
| `log-1.json`    | `APR 24 2016 14:03:35`    |
| `log-0054.json` | `APR 24 2016 14:05:03`    |
| `log-0055.json` | `APR 24 2016 14:45:11`    |
| `log-2.json`    | `APR 24 2016 14:45:11`    |

## 第一个要处理的文件

当您希望SFTP / FTP / FTPS客户端来源忽略目录中的一个或多个现有文件时，请配置第一个文件进行处理。

当您定义要处理的第一个文件时，原始服务器将以指定的文件开始处理，并按预期的读取顺序继续处理文件：与文件名模式匹配的文件将基于最后修改的时间戳以升序排列。

如果不指定第一个文件，则原始文件将处理目录中与文件名模式匹配的文件，从最早的文件开始，然后以升序继续。

例如，如果您指定第一个文件的最后修改时间戳为6/01/2017 00:00:00，则原始服务器将开始使用该文件进行处理，并忽略目录中所有较旧的文件。

**注意：**当您重新启动已停止的管道时，原点将忽略此属性。除非您重设源文件，否则无论第一个文件名如何，它都会从中断处开始。

## 证书

SFTP / FTP / FTPS客户端源可以使用多种方法向远程服务器进行身份验证。在“凭据”选项卡中，配置远程服务器所需的身份验证。

每种协议的身份验证选项不同：

- 对于所有协议，请选择一种身份验证方法以登录到远程服务器。根据协议和远程服务器要求选择方法：
  - 无-阶段不通过服务器进行身份验证。
  - 密码-阶段使用用户名和密码向服务器认证。您必须指定用户名和密码。
  - 私钥-阶段使用私钥进行身份验证。仅与SFTP协议一起使用。您必须在本地文件或纯文本中指定私钥。
- 对于SFTP协议，该阶段可以要求将服务器列在已知主机文件中。您必须指定包含包含批准的SFTP服务器的主机密钥的已知主机文件的路径。
- 对于FTPS协议，该阶段可以使用证书对服务器进行身份验证。您必须指定密钥库文件和密码。您还可以通过指定信任库提供程序将阶段配置为对服务器进行身份验证。有关密钥库和信任库的更多信息，请参阅“ [密钥库和信任库配置”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z)。

## 记录标题属性

SFTP / FTP / FTPS客户端源创建记录头属性，其中包括有关记录的源文件的信息。当原始处理Avro数据时，它将在AvroSchema记录头属性中包含Avro架构。

您可以使用`record:attribute`或 `record:attributeOrDefault`函数来访问属性中的信息。有关使用记录标题属性的更多信息，请参见[使用标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_rd2_ghz_dz)。

SFTP / FTP / FTPS客户端来源将创建以下记录头属性：

- avroSchema-处理Avro数据时，提供Avro模式。
- filename-提供记录起源的文件名。
- 文件-提供记录起源的文件路径和文件名。
- mtime-提供文件的最后修改时间。
- remoteUri-提供舞台使用的资源URL。

## 事件产生

SFTP / FTP / FTPS客户端来源可以生成可在事件流中使用的事件。启用事件生成后，起源在每次起源开始或完成读取文件时都会生成事件记录。当它完成对所有可用数据的处理并且已经过配置的批处理等待时间时，它也可以生成事件。

SFTP / FTP / FTPS客户端起源事件可以任何逻辑方式使用。例如：

- 当原始完成处理可用数据时，使用Pipeline Finisher执行程序停止管道并将管道转换为Finished状态。

  重新启动由Pipeline Finisher执行程序停止的管道时，原点将从上次保存的偏移开始继续处理，除非您重置原点。

  有关示例，请参见[案例研究：停止管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_kff_ykv_lz)。

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

由SFTP / FTP / FTPS客户端来源生成的事件记录具有以下与事件相关的记录头属性。记录标题属性存储为字符串值：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型之一：new-file-当原点开始处理新文件时生成。Finished-file-当原点完成文件处理时生成。no-more-data-在原始服务器完成对所有可用文件的处理并且经过了为“批处理等待时间”配置的秒数之后生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

SFTP / FTP / FTPS客户端来源可以生成以下类型的事件记录：

- 新文件

  当SFTP / FTP / FTPS客户端源开始处理新文件时，它会生成一个新文件事件记录。

  新文件事件记录的`sdc.event.type`记录头属性设置为，`new-file`并包含以下字段：事件记录字段描述文件路径源开始或完成处理的文件的路径和名称。

- 成品文件

  SFTP / FTP / FTPS客户端源在完成文件处理后会生成一个完成文件事件记录。

  成品文件事件记录的`sdc.event.type`记录头属性设置为`finished-file`，包括以下字段：事件记录字段描述文件路径源开始或完成处理的文件的路径和名称。记录数从文件成功生成的记录数。错误计数从文件生成的错误记录数。

- 没有更多数据

  当SFTP / FTP / FTPS客户端源完成对所有可用记录的处理，并且为“批处理等待时间”配置的秒数过去了，而似乎未处理任何新文件时，该源记录将生成“无数据”事件记录。

  SFTP / FTP / FTPS客户端来源生成的无数据事件记录的 `sdc.event.type`记录头属性设置为 `no-more-data`，包括以下字段：事件记录字段描述记录数自管道启动或自上一次创建no-more-data事件以来成功生成的记录数。错误计数自管道启动或自上一次创建no-more-data事件以来生成的错误记录数。文件计数原始尝试处理的文件数。可以包含无法处理或未完全处理的文件。

## 后期处理



处理完整个文件以外的其他数据格式的文件后，SFTP / FTP / FTPS客户端源可以保留，存档或删除文件。

归档文件时，原点将文件移动到 <archive> / <source>目录，其中：

- <archive>是在“后处理”选项卡上指定的存档目录。您可以指定存档目录的绝对路径，也可以指定相对于原始登录用户的主目录的相对路径。
- 处理子目录时包含<source>。源创建一个与处理的每个子目录匹配的源目录。

例如，假设您 在远程主机上的/ home / data / orders目录中有文件。您将源配置为从/ home / data目录及其子目录读取文件 。您还可以配置源，以将已处理的文件归档到/ home / archive 目录。处理完文件后，原点将文件移动到 / home / archive / orders目录。

请注意，您选择指定相对于用户主目录的存档目录与您选择指定文件相对于用户主目录的原始位置的选择无关。

## 资料格式

SFTP / FTP / FTPS客户端源基于数据格式以不同方式处理数据。SFTP / FTP / FTPS客户端处理以下类型的数据：

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

  原点生成两个字段：一个用于文件引用，另一个用于文件信息。有关更多信息，请参见[整个文件数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_nfc_qkh_xw)。

- XML格式

  根据用户定义的定界符元素生成记录。在根元素下直接使用XML元素或定义简化的XPath表达式。如果未定义定界符元素，则源会将XML文件视为单个记录。

  默认情况下，生成的记录包括XML属性和名称空间声明作为记录中的字段。您可以配置阶段以将它们包括在记录中作为字段属性。

  您可以在字段属性中包含每个解析的XML元素和XML属性的XPath信息。这还将每个名称空间放置在xmlns记录头属性中。

  **注意：** 只有在目标中使用SDC RPC数据格式时，字段属性和记录头属性才会自动写入目标系统。有关使用字段属性和记录标题属性以及如何将它们包括在记录中的更多信息，请参见[字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)和[记录标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)。

  当记录超过用户定义的最大记录长度时，原点无法继续处理文件中的数据。已经从文件处理的记录将传递到管道。然后，原点的行为基于为该阶段配置的错误处理：丢弃-原点继续处理下一个文件，将部分处理的文件保留在目录中。错误-原点继续处理下一个文件。如果为该阶段配置了后处理错误目录，则原点会将部分处理的文件移动到错误目录。否则，它将文件保留在目录中。停止管道-原点停止管道。

  使用XML数据格式来处理有效的XML文档。有关XML处理的更多信息，请参见[阅读和处理XML数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/XMLDFormat.html#concept_lty_42b_dy)。**提示：** 如果要处理无效的XML文档，则可以尝试将文本数据格式与自定义分隔符一起使用。有关更多信息，请参见[使用自定义分隔符处理XML数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/TextCDelim.html#concept_okt_kmg_jx)。

## 配置SFTP / FTP / FTPS客户端来源

配置SFTP / FTP / FTPS客户端源以从SFTP，FTP或FTPS服务器读取文件。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SFTP.html#concept_jbf_cmr_mcb) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **SFTP / FTP / FTPS”**选项卡上，配置以下属性：

   | SFTP / FTP / FTPS属性                                        | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 资源网址                                                     | 文件驻留在远程服务器上的URL。使用适当的格式：SFTP协议：`sftp://:/`FTP协议：`ftp://:/ `FTPS协议：`ftps://:/ `如果服务器使用标准端口号，则可以从URL中省略端口号：对于SFTP是22，对于FTP或FTPS是21。您可以选择在URL中包括用于登录SFTP，FTP或FTPS服务器的用户名。例如，对于FTP协议，可以使用以下格式：`ftp://:@/`您可以输入电子邮件地址作为用户名。**注意：**如果在资源URL中输入用户名，并在“凭据”选项卡上配置密码或私钥身份验证，则URL中输入的值优先。 |
   | 相对于用户主目录的路径                                       | 解释在资源URL中输入的相对于登录到远程服务器的用户的主目录的路径。您可以在URL中或在“凭据”选项卡上配置密码或私钥身份验证时指定用户名。 |
   | 处理子目录                                                   | 处理指定路径的所有子目录中的文件。                           |
   | 文件名模式                                                   | 文件名模式的语法：球状正则表达式                             |
   | [文件名模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SFTP.html#concept_xd5_5z4_4y) | 要处理的文件名的模式。根据指定的文件名模式使用全局模式或正则表达式。 |
   | [第一个要处理的文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SFTP.html#concept_g5p_5ks_b1b) | 启动管道时要处理的第一个文件的名称。使用使用文件名模式的名称。保留空白以使用指定的命名约定读取目录中的所有文件。输入文件名时，原点开始使用指定的文件进行处理。重新启动已停止的管道时，原点将忽略此属性。除非您重设源文件，否则无论第一个文件名如何，它都会从中断处开始。 |
   | FTPS模式                                                     | FTPS协议使用的加密协商模式：隐式-立即使用加密。显式-使用普通FTP连接到服务器，然后与服务器协商加密。 |
   | FTPS数据通道保护级别                                         | FTPS数据通道使用的保护级别：清除-仅加密与服务器的通信，不加密发送到服务器的数据。专用-加密与服务器的通信以及发送到服务器的数据。 |
   | 禁用预读流                                                   | 禁用SFTP中的预读流。当源无法读取大文件时，请考虑禁用预读流。但是，禁用预读流会大大降低性能。 |
   | 套接字超时                                                   | TCP数据包之间允许的最大秒数。0表示没有限制。                 |
   | 连接超时                                                     | 发起与SFTP，FTP或FTPS服务器的连接所允许的最大秒数。0表示没有限制。 |
   | 数据超时                                                     | 传输的数据文件之间允许的最大秒数。0表示没有限制。            |
   | 最大批次大小（记录）                                         | 批量包含并一次通过管道发送的最大记录数。                     |
   | [批处理等待时间（毫秒）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ypd_vgr_5q) | 发送部分或空批次之前要等待的毫秒数。                         |

3. 在“ **凭据”**选项卡上，配置以下属性：

   | [凭证属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SFTP.html#concept_vnj_njp_yv) | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 认证方式                                                     | 登录远程服务器的认证方式：无-不通过远程服务器进行身份验证。密码-使用用户名和密码向远程服务器进行身份验证。私钥-使用私钥向SFTP服务器进行身份验证。默认为无。 |
   | 用户名                                                       | 登录远程服务器的用户名。可用于密码和私钥认证。               |
   | 密码                                                         | 登录远程服务器的密码。可用于密码验证。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 私钥提供者                                                   | 提供私钥的源：文件-从本地文件读取私钥。纯文本-从纯文本字段读取私钥。使用私钥身份验证时可用。 |
   | 私钥文件                                                     | 用于登录远程服务器的本地私钥文件的完整路径。当提供程序是文件时，可用于私钥身份验证。 |
   | 私钥                                                         | 用于登录到远程服务器的私钥。当提供者为纯文本时，可用于私钥身份验证。 |
   | 私钥密码                                                     | 密码短语用于打开私钥。如果私钥受密码保护，则可用于私钥认证。 |
   | 严格的主机检查                                               | 要求SFTP服务器列在已知主机文件中。启用后，仅当服务器在已知主机文件中列出时，目的地才连接到服务器。需要已知主机文件包含RSA密钥。仅用于SFTP协议。 |
   | 已知主机文件                                                 | 本地已知主机文件的完整路径。如果选择严格的主机检查，则为必需。使用严格的主机检查时可用。 |
   | 对FTPS使用客户端证书                                         | 使用客户端证书向FTPS服务器进行身份验证。当FTPS服务器需要相互认证时，请选择此选项。您必须提供包含客户端证书的密钥库文件。仅用于FTPS协议。 |
   | FTPS客户端证书密钥库文件                                     | 包含客户机证书的密钥库文件的完整路径。对FTPS使用客户端证书时可用。 |
   | FTPS客户端证书密钥库类型                                     | 包含客户端证书的密钥库文件的类型。对FTPS使用客户端证书时可用。 |
   | FTPS客户端证书密钥库密码                                     | 包含客户机证书的密钥库文件的密码。密码是可选的，但建议使用。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。对FTPS使用客户端证书时可用。 |
   | FTPS信任库提供程序                                           | 目的地用来从FTPS服务器认证证书的方法：全部允许-允许任何证书，跳过身份验证。文件-使用指定的信任库文件对证书进行身份验证。JVM默认-使用JVM默认信任库对证书进行身份验证。仅用于FTPS协议。 |
   | FTPS信任库文件                                               | 包含服务器证书的信任库文件的完整路径。在将文件用作FTPS信任库提供程序时可用。 |
   | FTPS信任库类型                                               | 信任库类型：Java密钥库文件（JKS）PKCS-12（p12文件）在将文件用作FTPS信任库提供程序时可用。 |
   | FTPS信任库密码                                               | 信任库文件的密码。密码是可选的，但建议使用。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。在将文件用作FTPS信任库提供程序时可用。 |

4. 在“ **错误处理”**选项卡上，配置以下属性：

   | 错误处理属性 | 描述                                                 |
   | :----------- | :--------------------------------------------------- |
   | 存档错误     | 在读取文件时遇到错误时，将文件下载并存档到本地目录。 |
   | 档案目录     | 本地目录以归档文件。                                 |

5. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SFTP.html#concept_jcx_ggs_wv) | 源文件的数据格式。使用以下格式之一：阿夫罗定界电子表格JSON格式记录原虫[SDC记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/SDCRecordFormat.html#concept_qkk_mwk_br)文本[整个档案](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_nfc_qkh_xw)XML格式 |

6. 对于Avro数据，在“ **数据格式”**选项卡上，配置以下属性：

   | Avro物业             | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | Avro模式位置         | 处理数据时要使用的Avro模式定义的位置：消息/数据包含架构-在文件中使用架构。在“管道配置”中-使用阶段配置中提供的架构。Confluent Schema Registry-从Confluent Schema Registry检索架构。在阶段配置中或在Confluent Schema Registry中使用架构可以提高性能。 |
   | Avro模式             | 用于处理数据的Avro模式定义。覆盖与数据关联的任何现有模式定义。您可以选择使用该 `runtime:loadResource`函数来加载存储在运行时资源文件中的架构定义。 |
   | 架构注册表URL        | 汇合的架构注册表URL，用于查找架构。要添加URL，请单击**添加**，然后以以下格式输入URL：`http://:` |
   | 基本身份验证用户信息 | 使用基本身份验证时连接到Confluent Schema Registry所需的用户信息。`schema.registry.basic.auth.user.info`使用以下格式从Schema Registry中的设置中输入密钥和机密 ：`:`**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 查找架构             | 在Confluent Schema Registry中查找架构的方法：主题-查找指定的Avro模式主题。架构ID-查找指定的Avro架构ID。覆盖与数据关联的任何现有模式定义。 |
   | 模式主题             | Avro架构需要在Confluent Schema Registry中查找。如果指定的主题具有多个架构版本，则源使用该主题的最新架构版本。要使用旧版本，请找到相应的架构ID，然后将“ **查找架构**依据 **”**属性设置为“架构ID”。 |
   | 架构编号             | 在Confluent Schema Registry中查找的Avro模式ID。              |

7. 对于定界数据，在“ **数据格式”**选项卡上，配置以下属性：

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

8. 对于Excel文件，在“ **数据格式”**选项卡上，配置以下属性：

   | Excel属性                                                    | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [Excel标头选项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Excel.html) | 指示文件是否包含标题行以及是否忽略标题行。标题行必须是文件的第一行。 |

9. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

   | JSON属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
   | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |
   | JSON内容                                                     | JSON内容的类型。使用以下选项之一：对象数组多个物件           |
   | 最大对象长度（字符）                                         | JSON对象中的最大字符数。较长的对象将转移到管道以进行错误处理。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
   | 字符集                                                       | 要处理的文件的字符编码。                                     |
   | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

10. 对于日志数据，在“ **数据格式”**选项卡上，配置以下属性：

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

11. 对于protobuf数据，在“ **数据格式”**选项卡上，配置以下属性：

    | Protobuf属性                                                 | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
    | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |
    | Protobuf描述符文件                                           | 要使用的描述符文件（.desc）。描述符文件必须位于Data Collector资源目录中`$SDC_RESOURCES`。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。 |
    | 讯息类型                                                     | 读取数据时使用的消息类型的全限定名称。使用以下格式： `.`。使用在描述符文件中定义的消息类型。 |
    | 分隔消息                                                     | 指示一个文件是否可能包含多个protobuf消息。                   |

12. 对于“ SDC记录”数据，在“ **数据格式”**选项卡上，配置以下属性：

    | SDC记录属性                                                  | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
    | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |

13. 对于文本数据，在“ **数据格式”**选项卡上，配置以下属性：

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

14. 对于整个文件，在“ **数据格式”**选项卡上，配置以下属性：

    | 整个文件属性                                                 | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 缓冲区大小（字节）                                           | 用于传输数据的缓冲区大小。                                   |
    | [每秒速率](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_prp_jzd_py) | 要使用的传输速率。输入数字以指定速率（以字节/秒为单位）。使用表达式指定每秒使用不同度量单位的速率，例如$ {5 * MB}。使用-1退出此属性。默认情况下，原点不使用传输速率。 |

15. 对于XML数据，在“ **数据格式”**选项卡上，配置以下属性：

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

16. 在“ **后处理”**选项卡上，配置以下属性。

    **注意：**当您选择整个文件数据格式时，此选项卡将被禁用。使用整个文件数据格式处理文件后，原始服务器无法存档或删除文件。

    | [后处理属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SFTP.html#concept_jbv_xvs_fhb) | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 文件后处理                                                   | 处理文件后采取的措施：无-将文件保留在原位。存档-将文件移动到存档目录。删除-删除文件。 |
    | 档案目录                                                     | 完全处理的文件的目录。指定存档目录时，文件在经过完全处理后将移至该目录。用于存档处理的文件。 |
    | 相对于用户主目录的路径                                       | 选择以指定相对于登录到远程服务器的用户的主目录的存档目录。您可以在URL中或在“凭据”选项卡上配置密码或私钥身份验证时指定用户名。 |