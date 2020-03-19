# 日志解析器

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310181330507.png) 资料收集器

日志解析器处理器根据指定的日志格式解析字段中的日志数据。使用日志解析器来处理管道中的日志数据。要直接从源系统读取日志数据，可以使用处理日志数据格式的源，例如File Tail或Kafka Consumer。

配置日志解析器时，您将定义包含日志行的字段和包含已解析字段的字段。

如果记录中除要解析的字段外还包含其他字段，则默认情况下会传递这些字段。解析的字段将写入指定位置，从而覆盖所有现有数据。

## 日志格式

使用Log Parser解析日志数据时，可以定义要读取的日志文件的格式。

您可以使用以下日志格式：

- 通用日志格式

  Web服务器用于生成日志文件的标准文本格式。也称为NCSA（国家超级计算应用中心）的通用日志格式。

- 合并日志格式

  基于包含附加信息的通用日志格式的标准化文本格式。也称为Apache / NCSA组合日志格式。

- Apache错误日志格式

  由Apache HTTP Server 2.2生成的标准化错误日志格式。

- Apache访问日志自定义格式

  由Apache HTTP Server 2.2生成的可自定义的访问日志。使用Apache HTTP Server 2.2版语法定义日志文件的格式。

- 正则表达式

  使用正则表达式定义日志数据的结构，然后分配由每个组表示的一个或多个字段。

  使用任何有效的正则表达式。

- 格罗模式

  使用grok模式定义日志数据的结构。您可以使用Data Collector支持的grok模式。您还可以定义一个自定义的grok模式，然后将其用作日志格式的一部分。

  有关支持的grok模式的更多信息，请参阅[定义Grok模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-GrokPatterns/GrokPatterns_title.html#concept_vdk_xjb_wr)。

- log4j

  由Apache Log4j 1.2日志记录实用程序生成的可自定义格式。您可以使用默认格式或指定自定义格式。使用Apache Log4j 1.2版语法定义日志文件的格式。

## 配置日志解析器

配置日志解析器以解析字段中的日志数据。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **解析”**选项卡上，配置以下属性：

   | 日志解析器属性                                               | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 要解析的字段                                                 | 包含要解析的日志数据的字段路径。                             |
   | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |
   | 新解析的字段                                                 | 用作新解析字段的根字段的字段。                               |
   | 日志格式                                                     | 日志数据格式。使用以下格式之一：通用日志格式合并日志格式Apache错误日志格式Apache访问日志自定义格式正则表达式格罗模式Log4j通用事件格式（CEF）日志事件扩展格式（LEEF） |

   - 当选择“ **Apache访问日志自定义格式”时**，请使用Apache日志格式字符串定义“ **自定义日志格式”**。

   - 选择“ **正则表达式”时**，输入描述日志格式的正则表达式，然后将要包括的字段映射到每个正则表达式组。

   - 当您选择

     神交模式

     ，您可以定义任何自定义神交模式，要在使用

     神交模式定义

     字段，然后输入中的神交模式日志文件描述

     神交模式

     场。

     有关支持的grok模式的更多信息，请参阅[定义Grok模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-GrokPatterns/GrokPatterns_title.html#concept_vdk_xjb_wr)。

   - 选择**Log4j时**，可以使用log4j变量定义自定义日志格式。