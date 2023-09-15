# 目录

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310112951520.png) 资料收集器![img](imgs/icon-Edge-20200310112951612.png) 数据收集器边缘

目录源从目录中的文件读取数据。源可以使用多个线程来启用文件的并行处理。

要处理的文件必须全部共享文件名模式并被完全写入。要从仍在写入的活动文件中读取数据，请使用文件尾源。



当配置目录源时，可以定义要使用的目录，读取顺序，文件名模式，文件名模式模式以及要处理的第一个文件。您可以使用全局模式或正则表达式来定义要使用的文件名模式。

使用“上次修改的时间戳记”读取顺序时，可以将源配置为从子目录读取。要使用多个线程进行处理，请指定要使用的线程数。

您还可以启用读取压缩文件或延迟到达目录中的文件。处理文件后，目录原点可以保留，存档或删除文件。

管道停止时，目录原点会记录它停止读取的位置。当管道再次启动时，原点将从默认情况下停止的地方继续进行处理。您可以重置原点以处理所有请求的文件。

原点生成记录头属性，使您能够在管道处理中使用记录的原点。

源可以为事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

## 文件名模式和方式

使用文件名模式来定义目录来源处理的文件。您可以使用全局模式或正则表达式来定义文件名模式。

目录源根据文件名模式，文件名模式和指定目录处理文件。例如，如果您指定 `/logs/weblog/ `目录，全局模式并`*.json` 作为文件名模式，则源将处理目录中带有`json`扩展名的所有文件 `/logs/weblog/` 。

源根据指定的读取顺序按顺序处理文件。

有关glob语法的更多信息，请参见[Oracle Java文档](https://docs.oracle.com/javase/tutorial/essential/io/fileOps.html#glob)。有关正则表达式的更多信息，请参见[正则表达式概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-RegEx/RegEx-Title.html#concept_vd4_nsc_gs)。

## 阅读订单

目录源根据时间戳或文件名以升序读取文件：

- 上次修改的时间戳

  目录源可以根据与文件关联的时间戳以升序读取文件。原点会同时检查上次修改的时间戳和更改后的时间戳，然后在订购文件进行处理时使用两者中的最高者（最近者）。

  如果原始位置是从辅助位置读取的，而不是从创建和写入文件的目录读取的，则最后修改的时间戳应该反映文件何时移动到要处理的目录。将文件复制到辅助位置时，更改的时间戳应该反映文件何时复制到要处理的目录。

  **提示：**避免使用保留现有时间戳的命令来移动文件，例如`cp -p`。在某些情况下，例如在跨时区移动文件时，保留现有时间戳可能会出现问题。

  当基于时间戳排序时，具有相同时间戳的任何文件都将根据文件名以字典顺序升序读取。

  例如，当使用`log*.json`文件名模式读取文件时，源按以下顺序读取以下文件：

  `File Name``Last Modified``Changed Timestamp``log-1.json``APR 24 2016 14:03:35``APR 24 2016 14:03:35``log-903.json``APR 24 2016 14:05:03``APR 24 2016 14:05:03``log-0054.json``APR 24 2016 14:00:03``APR 24 2016 14:40:44``log-2.json``APR 24 2016 14:45:11``APR 24 2016 14:45:11``log-3.json``APR 24 2016 14:45:11``APR 24 2016 14:45:11`

  请注意，log-0054.json在log-903.json之后进行处理，因为其更改的时间戳晚于log-903.json的最后修改和更改的时间戳。log-2.json和log-3.json文件具有相同的时间戳，因此将根据其文件名按字典顺序升序处理。

- 按字典顺序升序的文件名

  目录源可以根据文件名按字典顺序升序读取文件。请注意，按字典顺序升序读取数字1到11如下：

  `1, 10, 11, 2, 3, 4... 9`

  例如，当使用`web*.log`文件名模式读取文件时，目录将按以下顺序读取以下文件：`web-1.log web-10.log web-11.log web-2.log web-3.log web-4.log web-5.log web-6.log web-7.log web-8.log web-9.log`

  要按逻辑和字典顺序的升序读取这些文件，可以将前导零添加到文件命名约定中，如下所示：`web-0001.log web-0002.log web-0003.log ... web-0009.log web-0010.log web-0011.log`

## 多线程处理

目录源可以使用多个线程基于“线程数”属性执行并行处理。

![img](imgs/icon-Edge-20200310112951612.png)在Data Collector Edge管道中无效。在Data Collector Edge管道中，目录源仅使用一个线程来处理数据。“线程数”属性将被忽略。

在管道中使用多个线程时，每个线程都从一个文件读取数据，并且每个文件一次最多只能有一个线程读取数据。文件读取顺序基于“ [读取顺序”属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_b4d_fym_xv)的配置。

在管道运行时，每个线程都连接到原始系统，创建一批数据，然后将这批数据传递给可用的管道运行器。管道运行器是无源管道实例 - 管道的实例，包括管道中的所有处理器，执行程序和目的地，并在源之后处理所有管道处理。

每个管道运行程序一次处理一个批处理，就像在单个线程上运行的管道一样。当数据流减慢时，管道运行器会闲置等待，直到需要它们为止，并定期生成一个空批。您可以配置“运行者空闲时间”管道属性来指定间隔或选择退出空批次生成。

多线程管道保留每个批处理中的记录顺序，就像单线程管道一样。但是由于批处理 是由不同的流水线处理程序处理的，因此无法确保将批处理写入目的地的顺序。

例如，假设您将源配置为使用5个线程和“上次修改的时间戳”读取顺序从目录中读取文件。启动管道时，原始节点将创建五个线程，而Data Collector 会创建匹配数量的管道运行器。

目录源为目录中的五个最旧文件中的每个文件分配一个线程。每个线程处理其分配的文件，将成批的数据传递到源。接收到数据后，原点将批处理传递给每个管道运行器进行处理。

每个线程完成对文件的处理后，它将根据最后修改的时间戳继续到下一个文件，直到处理完所有文件。

有关多线程管道的更多信息，请参见《[多线程管道概述》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Multithreaded_Pipelines/MultithreadedPipelines.html#concept_zpp_2xc_py)。

## 从子目录读取

使用“上次修改的时间戳记”读取顺序时，目录源可以读取指定文件目录的子目录中的文件。

当您将源配置为从子目录中读取时，它将从所有子目录中读取文件。它会根据时间戳以升序读取文件，而不管文件在目录中的位置。

例如，您将目录配置为从/ logs /文件目录中读取，选择“上次修改的时间戳记”读取顺序，并启用从子目录中读取。目录会根据时间戳按以下顺序读取以下文件，即使文件被写入不同的子目录也是如此。

| `File Name`     | `Directory`   | `Last Modified Timestamp` |
| --------------- | ------------- | ------------------------- |
| `log-1.json`    | `/logs/west/` | `APR 24 2016 14:03:35`    |
| `log-0054.json` | `/logs/east/` | `APR 24 2016 14:05:03`    |
| `log-0055.json` | `/logs/west/` | `APR 24 2016 14:45:11`    |
| `log-2.json`    | `/logs/`      | `APR 24 2016 14:45:11`    |

### 后处理子目录

当目录源从子目录中读取时，在后处理期间归档文件时，它将使用子目录结构。

您可以在原始服务器完成处理文件或无法完全处理文件时存档文件。

例如，假设您配置源，以将已处理的文件归档到“已处理”的归档目录中。成功读取以上示例中的文件后，它将它们写入以下目录：

| `File Name`     | `Archive Directory`     |
| --------------- | ----------------------- |
| `log-1.json`    | `/processed/logs/west/` |
| `log-0054.json` | `/processed/logs/east/` |
| `log-0055.json` | `/processed/logs/west/` |
| `log-2.json`    | `/processed/logs/`      |

## 第一个要处理的文件

当您希望目录忽略目录中的一个或多个现有文件时，请配置第一个文件进行处理。

当您定义要处理的第一个文件时，Directory开始使用指定的文件进行处理，并根据读取顺序和文件名模式继续进行处理。当您不指定第一个文件时，目录将处理目录中与文件名模式匹配的所有文件。

例如，假设“目录”基于上次修改或更改的时间戳读取文件。要忽略所有早于特定文件的文件，请使用该文件名作为要处理的第一个文件。

同样，假设您具有“目录”基于词典升序的文件名读取文件，并且文件目录包含以下文件：web_001.log，web_002.log，web_003.log。

如果将web_002.log配置为第一个文件，目录将读取web_002.log并继续到web_003.log。跳过web_001.log。

## 后期目录

您可以将目录原点配置为读取较晚目录中的文件-管道启动后出现的目录。

从较晚的目录中读取时，启动管道时，源不验证目录路径。如果在管道启动时目录不存在，则源将无限期地等待目录和要处理的文件的出现。

例如，假设您读取以下目录中的文件：

```
/logs/server/
```

当您启动管道时，该目录不存在，因此目录 源将一直等待，直到该目录和与文件名模式匹配的文件出现，然后处理数据。

在出现/ logs / server目录之后，原始服务器可以处理写入该目录的以下文件：

```
/logs/server/log.json
/logs/server/log1.json
/logs/server/log2.json
```

## 记录标题属性

目录源创建记录头属性，该属性包含有关记录的源文件的信息。

当原始处理Avro数据时，它将在AvroSchema记录头属性中包含Avro架构。

您可以使用`record:attribute`或 `record:attributeOrDefault`函数来访问属性中的信息。有关使用记录标题属性的更多信息，请参见[使用标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_rd2_ghz_dz)。

目录源创建以下记录头属性：

- avroSchema-处理Avro数据时，提供Avro模式。
- baseDir-包含记录起源的文件的基本目录。

- filename-提供记录起源的文件名。
- 文件-提供记录起源的文件路径和文件名。
- mtime-提供文件的最后修改时间。
- offset-提供文件偏移量（以字节为单位）。文件偏移量是记录在文件中的原始位置。

## 事件产生

目录源可以生成可在事件流中使用的事件。启用事件生成后，起源在每次起源开始或完成读取文件时都会生成事件记录。当它完成对所有可用数据的处理并且已经过配置的批处理等待时间时，它也可以生成事件。

目录事件可以任何逻辑方式使用。例如：

- 当原始完成处理可用数据时，使用Pipeline Finisher执行程序停止管道并将管道转换为Finished状态。

  重新启动由Pipeline Finisher执行程序停止的管道时，原点将从上次保存的偏移开始继续处理，除非您重置原点。

  有关示例，请参见[案例研究：停止管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_kff_ykv_lz)。

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

![img](imgs/icon-Edge-20200310112951612.png)在Data Collector Edge管道中，您只能将事件传递到目标进行存储。由于Data Collector Edge管道不支持执行程序阶段，因此不能使用事件来触发任务。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

目录起源生成的事件记录具有以下与事件相关的记录头属性。记录标题属性存储为字符串值：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型之一：new-file-当原点开始处理新文件时生成。Finished-file-当原点完成文件处理时生成。no-more-data-在原始服务器完成对所有可用文件的处理并且经过了为“批处理等待时间”配置的秒数之后生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

目录源可以生成以下类型的事件记录：

- 新文件

  目录原点开始处理新文件时会生成新文件事件记录。

  新文件事件记录的sdc.event.type设置为new-file，并包含以下字段：事件记录字段描述文件路径源开始或完成处理的文件的路径和名称。

- 成品文件

  目录原点在完成文件处理后会生成一个完成文件事件记录。

  成品文件事件记录的sdc.event.type设置为成品文件，并包含以下字段：事件记录字段描述文件路径源开始或完成处理的文件的路径和名称。记录数从文件成功生成的记录数。错误计数从文件生成的错误记录数。

- 没有更多数据

  当目录原点完成对所有可用记录的处理并且经过了为“批处理等待时间”配置的秒数，而似乎没有任何新文件要处理时，目录原点将生成无数据事件记录。

  由目录源生成的无数据事件记录将sdc.event.type设置为无数据，并包含以下字段：事件记录字段描述记录数自管道启动或自上一次创建no-more-data事件以来成功生成的记录数。错误计数自管道启动或自上一次创建no-more-data事件以来生成的错误记录数。文件计数原始尝试处理的文件数。可以包含无法处理或未完全处理的文件。

## 缓冲区限制和错误处理

目录源将每个记录传递到缓冲区。缓冲区的大小决定了可以处理的记录的最大大小。当Data Collector 计算机上的内存受到限制时，请减小缓冲区限制。当有可用内存时，增加缓冲区限制以处理较大的记录。

当记录大于指定的限制时，目录源将根据阶段错误处理来处理源文件：

- 丢弃

  源丢弃该记录和文件中的所有剩余记录，然后继续处理下一个文件。

- 发送到错误

  出现缓冲区限制错误时，原点无法将记录发送到管道以进行错误处理，因为它无法完全处理记录。而是由原始服务器创建一条消息，指出发生缓冲区溢出错误。该消息包括发生缓冲区溢出错误的文件和偏移量。该信息显示在管道历史记录中。如果为该阶段配置了错误目录，则源将文件移至错误目录并继续处理下一个文件。

- 停止管道

  原点停止流水线并创建一条消息，指出发生缓冲区溢出错误。该消息包括发生缓冲区溢出错误的文件和偏移量。该信息显示在管道历史记录中。

  **注意：**您也可以检查Data Collector日志文件以获取错误详细信息。

## 资料格式

目录源根据数据格式不同地处理数据。

![img](imgs/icon-Edge-20200310112951612.png)在Data Collector Edge管道中， 源仅支持Delimited，JSON，SDC Record，Text和Whole File数据格式。

目录源处理数据格式如下：

- 阿夫罗

  为每个Avro记录生成一条记录。原点在`avroSchema` [记录头属性中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)包括Avro模式 。它还包括一个 `precision`与 `scale` [场属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)为每个小数字段。

  来源希望每个文件都包含Avro架构，并使用该架构来处理Avro数据。

  源读取由Avro支持的压缩编解码器压缩的文件，而无需其他配置。

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

## 配置目录来源

配置目录源以从目录中的文件读取数据。

配置目录时，可以定义文件属性，包括要处理的数据格式。然后，定义后处理选项和特定于数据格式的属性。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_ttg_vgn_qx) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **文件”**选项卡上，配置以下属性：

   | 文件属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 文件目录                                                     | Data Collector本地目录，用于存储源文件。输入绝对路径。       |
   | [线程数](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_pcl_nwn_qbb) | 原点生成并用于多线程处理的线程数。默认值为1。![img](imgs/icon-Edge-20200310112951612.png)在Data Collector Edge管道中无效。在Data Collector Edge管道中，目录源仅使用一个线程来处理数据，并且将忽略此属性。 |
   | 文件名模式                                                   | 文件名模式的语法：球状正则表达式                             |
   | [文件名模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_xd5_5z4_4y) | 要处理的文件名的模式。根据指定的文件名模式使用全局模式或正则表达式。 |
   | [阅读订单](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_b4d_fym_xv) | 读取文件时使用的顺序：上次修改的时间戳-基于以下最新时间戳按升序读取文件：上次修改的时间戳或更改的时间戳。当文件具有匹配的时间戳时，源将根据文件名按字典顺序升序读取文件。字典上的升序文件名-根据文件名以字典上的升序读取文件。 |
   | [处理子目录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_qpt_rg3_cy) | 读取指定文件目录的任何子目录中的文件。不论文件目录中的位置如何，都基于上次修改或更改的时间戳以升序读取文件。将子目录用于任何已配置的后处理目录。仅在使用“上次修改的时间戳记”读取顺序时可用。 |
   | 批次大小                                                     | 一次通过管道的记录数。接受的值最高为Data Collector的最大批处理大小。默认值是1000 数据采集器默认设置为1000。 |
   | [批处理等待时间（秒）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ypd_vgr_5q) | 发送部分或空批次之前等待的秒数。                             |
   | [允许后期目录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_p52_xj1_4v) | 允许从管道启动后出现的目录中读取文件。启用后，原点将不验证文件路径。 |
   | [第一个要处理的文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_ltv_r3l_5q) | 要处理的第一个文件的名称。当您不输入第一个文件名时，源将读取具有指定文件名模式的目录中的所有文件。 |
   | 最大文件软限制                                               | 源一次可以添加到处理队列中的最大文件数。此值是一个软限制-表示原点可以暂时超过它。如果原点超过此软限制，则原点启动假脱机周期计时器。如果处理队列中的文件数低于软限制，则原始服务器会将更多文件从目录添加到队列中。如果在配置的假脱机时间段到期后，处理队列中的文件数仍保持在软限制之上，则在队列低于软限制之前，不会再将其他文件添加到队列中。将软限制配置为目录中预期的最大文件数。默认值为1000。 |
   | 假脱机时间（秒）                                             | 超过最大文件软限制后，继续将文件添加到处理队列的秒数。假脱机期间到期时，直到队列低于软限制，才将其他文件添加到处理队列中。默认值为5秒。 |
   | [缓冲区限制（KB）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_zp2_fqs_5r) | 最大缓冲区大小。缓冲区大小确定可以处理的记录的大小。减少Data Collector计算机上的内存有限的时间。可用时增加以处理更大的记录。 |

3. 在“ **后处理”**选项卡上，配置以下属性：

   | 后处理属性           | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | 错误目录             | 由于数据处理错误而无法完全处理的文件的目录。指定错误目录时，无法完全处理的文件将移至该目录。用于管理文件以进行错误处理和重新处理。 |
   | 文件后处理           | 处理文件后采取的措施：无-将文件保留在原位。存档-将文件移动到存档目录。删除-删除文件。 |
   | 档案目录             | 完全处理的文件的目录。指定存档目录时，文件在经过完全处理后将移至该目录。用于存档处理的文件。 |
   | 存档保留时间（分钟） | 已处理文件的分钟数保存在存档目录中。使用0可以无限期保留归档文件。 |

4. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_gz5_dqw_yq) | 源文件的数据格式。使用以下数据格式之一：阿夫罗定界电子表格JSON格式记录原虫[SDC记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/SDCRecordFormat.html#concept_qkk_mwk_br)文本[整个档案](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_nfc_qkh_xw)XML格式![img](imgs/icon-Edge-20200310112951612.png)在Data Collector Edge管道中， 源仅支持Delimited，JSON，SDC Record，Text和Whole File数据格式。 |

5. 对于定界数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 定界财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。![img](imgs/icon-Edge-20200310112951612.png)在Data Collector Edge管道中，源仅支持未压缩和压缩的文件，不支持存档或压缩的存档文件。 |
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

6. 对于Excel文件，在“ **数据格式”**选项卡上，配置以下属性：

   | Excel属性                                                    | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [Excel标头选项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Excel.html) | 指示文件是否包含标题行以及是否忽略标题行。标题行必须是文件的第一行。 |

7. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

   | JSON属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。![img](imgs/icon-Edge-20200310112951612.png)在Data Collector Edge管道中，源仅支持未压缩和压缩的文件，不支持存档或压缩的存档文件。 |
   | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |
   | JSON内容                                                     | JSON内容的类型。使用以下选项之一：对象数组多个物件           |
   | 最大对象长度（字符）                                         | JSON对象中的最大字符数。较长的对象将转移到管道以进行错误处理。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
   | 字符集                                                       | 要处理的文件的字符编码。                                     |
   | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

8. 对于日志数据，在“ **数据格式”**选项卡上，配置以下属性：

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

9. 对于protobuf数据，在“ **数据格式”**选项卡上，配置以下属性：

   | Protobuf属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
   | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |
   | Protobuf描述符文件                                           | 要使用的描述符文件（.desc）。描述符文件必须位于Data Collector资源目录中`$SDC_RESOURCES`。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。 |
   | 讯息类型                                                     | 读取数据时使用的消息类型的全限定名称。使用以下格式： `.`。使用在描述符文件中定义的消息类型。 |
   | 分隔消息                                                     | 指示一个文件是否可能包含多个protobuf消息。                   |

10. 对于“ SDC记录”数据，在“ **数据格式”**选项卡上，配置以下属性：

    | SDC记录属性                                                  | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。![img](imgs/icon-Edge-20200310112951612.png)在Data Collector Edge管道中，源仅支持未压缩和压缩的文件，不支持存档或压缩的存档文件。 |
    | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |

11. 对于文本数据，在“ **数据格式”**选项卡上，配置以下属性：

    | 文字属性                                                     | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。!![img](imgs/icon-Edge-20200310112951612.png)在Data Collector Edge管道中，源仅支持未压缩和压缩的文件，不支持存档或压缩的存档文件。 |
    | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |
    | 最大线长                                                     | 一行允许的最大字符数。较长的行被截断。向记录添加一个布尔字段，以指示该记录是否被截断。字段名称为“截断”。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
    | [使用自定义分隔符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/TextCDelim.html#concept_lg2_gcg_jx) | 使用自定义定界符来定义记录而不是换行符。                     |
    | 自定义定界符                                                 | 用于定义记录的一个或多个字符。                               |
    | 包括自定义定界符                                             | 在记录中包括定界符。                                         |
    | 字符集                                                       | 要处理的文件的字符编码。                                     |
    | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

12. 对于整个文件，在“ **数据格式”**选项卡上，配置以下属性：

    | 整个文件属性                                                 | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 缓冲区大小（字节）                                           | 用于传输数据的缓冲区大小。                                   |
    | [每秒速率](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_prp_jzd_py) | 要使用的传输速率。输入数字以指定速率（以字节/秒为单位）。使用表达式指定每秒使用不同度量单位的速率，例如$ {5 * MB}。使用-1退出此属性。默认情况下，原点不使用传输速率。 |

13. 对于XML数据，在“ **数据格式”**选项卡上，配置以下属性：

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