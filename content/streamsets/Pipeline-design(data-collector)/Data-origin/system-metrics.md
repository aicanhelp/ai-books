# 系统指标

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310174612238.png) 资料收集器![img](imgs/icon-Edge-20200310174612383.png) 数据收集器边缘

系统指标来源从安装了StreamSets 数据收集器边缘（SDC Edge）的边缘设备读取系统指标。仅在为边缘执行模式配置的管道中使用系统指标原点。

“系统指标”来源会根据您配置的批次之间的延迟时间，定期从边缘设备读取指标。例如，如果将延迟时间设置为10分钟，则起点将每10分钟创建一个包含选定系统指标的新批次。

每个批次都包含一个记录，其中包括读取数据的时间戳和每个选定系统指标类型的映射字段。配置源时，选择要读取的系统指标的类型-包括主机信息以及CPU，内存，磁盘，网络和进程指标。

有关安装SDC Edge，设计边缘管道以及运行和维护边缘管道的更多信息，请参见[Edge Pipelines Overview](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Edge_Mode/EdgePipelines_Overview.html#concept_d4h_kkq_4bb)。

## 例

您要收集，监视和分析所有边缘设备的系统指标。

您在每个边缘设备上安装SDC Edge。您可以使用Data Collector 设计一条边缘发送管道，该管道包括系统度量标准起源和将系统度量标准发布到HTTP端点的HTTP客户端目标。您将边缘发送管道部署到所有边缘设备，然后在每个设备上运行管道。

您设计了一个数据收集器 接收管道，该管道包括一个HTTP Server源，该源读取发布到HTTP端点的系统指标。读取指标后，Data Collector 接收管道会对数据执行其他处理，然后将数据写入Elasticsearch以进行指标分析。您运行数据采集器 上接收管道数据采集器。

## 收集的系统指标

系统指标来源使用Go编程语言（或Golang）的psutil包来收集系统指标。

[Golang的psutil软件包](https://github.com/shirou/gopsutil)收集的值根据边缘设备的操作系统而有所不同。有关系统指标起源为每个操作系统收集的指标的完整列表，请运行边缘管道的预览。

例如，下图显示了系统指标起源的预览，该指标被配置为收集除过程指标之外的所有系统指标类型：

![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/SystemMetricsPreview.png)

当我们扩展hostInfo映射字段时，预览显示为Linux操作系统收集的主机信息：

![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/SystemMetricsPreviewHostInfo.png)

## 筛选过程指标

系统指标来源可以从边缘设备上运行的进程读取指标。配置为读取流程指标时，默认情况下，源将读取所有正在运行的流程的统计信息。

在“ **进程”**选项卡上，您可以按进程名称或命令或拥有该进程的用户过滤源读取的进程。要按流程名称或命令进行过滤，请在“ **流程”**属性中输入流程名称或流程命令的一部分。要按用户过滤，请输入**User**属性的用户名。

您可以使用对过程或用户求值的*正*则表达式或*regex*。用于这两个属性的以下默认正则表达式匹配所有用户拥有的所有正在运行的进程：

```
.*
```

例如，要仅读取名称以“ st”开头的进程的统计信息，请为**Processes**属性输入以下正则表达式：

```
st.*
```

要仅读取root用户拥有的进程的统计信息，请为**User**属性输入“ root” 。

有关将正则表达式与Data Collector一起使用的更多信息，请参见[正则表达式概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-RegEx/RegEx-Title.html#concept_vd4_nsc_gs)。

## 配置系统指标来源

配置系统指标来源以从安装了SDC Edge的边缘设备读取系统指标。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **系统指标”**选项卡上，配置以下属性：

   | 系统指标属性     | 描述                                                         |
   | :--------------- | :----------------------------------------------------------- |
   | 批次之间的延迟   | 创建下一批数据之前要等待的毫秒数。                           |
   | 获取主机信息     | 包括来自边缘设备的主机信息，例如主机名，操作系统和平台。     |
   | 提取CPU统计信息  | 包括来自边缘设备的CPU统计信息，例如可用核心数和正在使用的CPU百分比。 |
   | 获取内存统计信息 | 包括来自边缘设备的内存统计信息，例如设备上的可用和已用内存量。 |
   | 提取磁盘统计信息 | 包括来自边缘设备的磁盘统计信息，例如设备的序列号和磁盘分区。 |
   | 获取网络统计信息 | 包括来自边缘设备的网络统计信息，例如有关设备上打开的连接的信息。 |
   | 提取流程统计信息 | 包括来自边缘设备上运行的进程的统计信息。默认情况下，源读取所有正在运行的进程的统计信息。 |

3. 读取[流程指标时](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SystemMetrics.html#concept_trh_kgh_3fb)，可以选择在“ **流程”**选项卡上配置以下属性以过滤流程：

   | 工艺性质 | 描述                                       |
   | :------- | :----------------------------------------- |
   | 工艺流程 | 正则表达式，用于按进程名称或命令过滤进程。 |
   | 用户     | 使用正则表达式过滤拥有该过程的用户的过程。 |