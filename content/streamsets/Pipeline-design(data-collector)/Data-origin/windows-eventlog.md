# Windows事件日志

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310175021925.png) 资料收集器![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png) 数据收集器边缘

Windows事件日志源从Windows计算机上的Microsoft Windows事件日志中读取数据。原点为日志中的每个事件生成一条记录。

仅在为边缘执行模式配置的管道中使用Windows事件日志源。在Windows计算机上安装的StreamSets 数据收集器边缘（SDC Edge）上运行管道。

例如，您可以在边缘管道中使用Windows事件日志源从Windows服务器的Web或应用程序场中读取日志。您在要从中读取日志的每台Windows计算机上安装SDC Edge，并在每台SDC Edge安装上运行边缘管道。您可以设计边缘管道，以将日志数据传递到 在StreamSets Data Collector上运行的Data Collector接收管道。该数据采集器 接收管道对数据执行更复杂的处理，然后将数据写入大数据系统（例如Hadoop）。然后，您可以分析数据以检测安全漏洞，例如内部威胁或对Windows计算机的非法访问。

配置Windows事件日志来源时，可以指定要读取的Windows事件日志。您还可以指定源是读取日志中的所有事件，还是仅读取管道启动后发生的新事件。

您将源配置为使用事件日志记录API或Windows事件日志API从日志中读取。

当管道停止时，Windows事件日志源将记录它停止读取的位置。当管线再次开始时，原点将从上次保存的偏移开始继续处理。您可以重置原点以处理日志中的所有事件。

有关安装SDC Edge，设计边缘管道以及运行和维护边缘管道的更多信息，请参见[Edge Pipelines Overview](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Edge_Mode/EdgePipelines_Overview.html#concept_d4h_kkq_4bb)。

## 读取器API类型

源可以使用以下API之一从Microsoft Windows事件日志中读取数据：

- 事件记录API

  该[事件日志记录API](https://docs.microsoft.com/en-us/windows/desktop/EventLog/event-logging)被设计用于在Windows Server 2003，Windows XP或Windows 2000操作系统上运行的应用程序。

  使用事件记录API时，原点使用记录号来管理停止和重新启动管道时的偏移量。

- Windows事件日志API

  在[Windows事件日志API](https://docs.microsoft.com/en-us/windows/desktop/wes/windows-event-log)取代了事件日志记录API开始与Windows Vista的操作系统。Microsoft建议对在Windows Vista或更高版本的操作系统上运行的应用程序使用更新的Windows事件日志API。

  使用Windows事件日志API时，原点使用Windows事件日志API提供的[书签](https://docs.microsoft.com/en-us/windows/desktop/wes/bookmarking-events)来管理在停止和重新启动管道时的偏移量。您可以配置源服务器是使用推送还是请求[订阅模式](https://docs.microsoft.com/en-us/windows/desktop/wes/subscribing-to-events)从日志中读取事件。

  使用Windows事件日志API，源将读取每个事件的原始XML。然后，它将系统元数据和事件的日志消息传递到 记录中的`System`和`Message`字段。原始数据还将原始XML传递到`rawEventXML` 字段，具体取决于您如何配置原始数据以填充XML：错误时-仅当原始发生生成`System`or `Message`字段的错误时，原始才将原始XML包含在记录中 。始终-原点始终在记录中包含原始XML。

## 配置Windows事件日志来源

配置Windows事件日志源以从Windows事件日志中读取数据。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **Windows”**选项卡上，配置以下属性：

   | Windows属性                                                  | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | Windows日志以读取                                            | 要从中读取的Windows事件日志的名称：应用系统安全自订          |
   | 自定义日志名称                                               | 要读取的自定义Windows事件日志的名称。输入事件日志名称或输入一个计算结果为该名称的表达式。 |
   | 读模式                                                       | 确定原点如何读取日志：全部-读取日志中的所有事件。新建-仅读取管道启动后发生的日志中的新事件。 |
   | [读取器API类型](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/WindowsLog.html#concept_ewn_yvp_2gb) | 用于从日志读取的API类型：事件记录Windows事件日志Microsoft建议对在Windows Vista或更高版本的操作系统上运行的应用程序使用更新的Windows事件日志API。 |
   | 缓冲区大小                                                   | 最大缓冲区大小。缓冲区大小确定可以处理的记录的大小。缓冲区限制有助于防止内存不足错误。减少SDC Edge计算机上的内存有限的时间。可用时增加以处理更大的记录。出现缓冲区限制错误时，源服务器会记录一条消息，指出发生了缓冲区溢出错误。默认值为-1，表示未设置限制，并且原点使用所有可用内存。 |
   | 订阅模式                                                     | 使用Windows事件日志API时，用于[订阅事件](https://docs.microsoft.com/en-us/windows/desktop/wes/subscribing-to-events)的模式：推送模式拉模式 |
   | 填充原始事件XML                                              | 使用Windows事件日志API时，确定原始记录中何时包括记录中每个事件的原始XML：错误时总是 |
   | 最大等待时间（秒）                                           | 使用Windows事件日志API时，源在生成批处理之前等待接收事件的最长时间（以秒为单位）。 |