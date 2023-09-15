# 日志数据格式

使用原点读取日志数据时，可以定义要读取的日志文件的格式。

您可以阅读使用以下日志格式的日志文件：

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

  您也可以指定在解析行时原点遇到错误时要采取的操作。您可以跳过这一行，还可以选择记录错误。如果您知道不可解析的信息是堆栈跟踪的一部分，则可以使源包含不可解析的信息，作为到上一条可解析行的堆栈跟踪。

- 通用事件格式（CEF）

  安全设备用于生成日志事件的可自定义事件格式。CEF是HP ArcSight的本机格式。

- 日志事件扩展格式（LEEF）

  安全设备用于生成日志事件的可自定义事件格式。LEEF是IBM Security QRadar的本机格式。

对于支持这种数据格式起源的完整列表，请参阅[起源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-DataFormats/DataFormat_Title.html#concept_kgd_11c_kv) “数据格式的舞台”附录。