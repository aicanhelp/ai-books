# Azure IoT /事件中心使用者

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310112815160.png) 资料收集器

Azure IoT /事件中心使用者源从Microsoft Azure事件中心读取数据。源可以使用多个线程来启用来自单个Azure事件中心的数据的并行处理。

在使用Azure IoT /事件中心使用者来源之前，请确保您具有必需的Microsoft Azure存储帐户和容器。

配置Azure IoT /事件中心使用者时，请指定Microsoft Azure命名空间和事件中心名称。您还可以定义共享访问策略名称和连接字符串键。您指定要使用的使用者组和与Azure Event Hub进行通信时原始使用的事件处理器前缀。

您可以配置存储帐户详细信息，例如存储帐户名称和密钥。然后您指定要在处理期间使用的线程数。

## 存储帐户和容器先决条件

在使用Azure IoT /事件中心使用者来源之前，您需要一个Microsoft Azure存储帐户和至少一个容器。

源将偏移量存储在存储帐户容器中，因此，为了确保偏移量信息的完整性，必须为包含Azure IoT / Event Hub使用者源的每个管道使用不同的容器。

例如，假设您将Azure IoT / Event Hub使用者用作IoT管道和Transactions管道的来源。为了使这些管道的偏移数据分开，您需要使用两个不同的存储帐户容器。它们可以在相同的存储帐户中，也可以在不同的存储帐户中。配置来源时，可以指定要使用的存储帐户和容器。

为管道创建新的容器：

1. 登录到Microsoft Azure门户：[https](https://portal.azure.com/) : [//portal.azure.com](https://portal.azure.com/)

2. 在“导航”面板中，单击“ **存储帐户”**。

3. 选择要使用的存储帐户。

   如果需要创建存储帐户，请单击“ **添加”**图标。输入存储帐户的名称，然后输入或选择资源组名称。您可以将默认值用于所有其他属性。

4. 在存储帐户视图中，单击**+容器**创建一个容器。

5. 输入容器名称，然后单击

   确定

   。

   **提示：**使用易于识别的名称作为您要在其中使用管道的容器。

如果这些步骤不再正确，请参阅Microsoft Azure Event Hub文档。

## 在事件中心重置原点

您不能使用Data Collector重置Azure IoT /事件中心使用者管道的源，因为偏移量存储在Azure事件中心中。

要在Microsoft Azure Event Hub中重置原点，请执行以下操作：

1. 在Microsoft Azure门户中，导航到该存储帐户。

2. 要删除为管道存储的偏移信息，请删除管道使用的容器。

   这可能需要一些时间。允许门户网站完成容器的移除，然后再继续。

3. 要在重新启动管道时使管道能够存储新的偏移信息，请创建一个具有相同名称的新容器。或者，使用其他名称并更新管道中的“容器名称”属性。

## 多线程处理

Azure IoT /事件中心使用者源执行并行处理，并允许创建多线程管道。

Azure IoT /事件中心使用者来源基于最大线程属性使用多个并发线程从事件中心读取。启动管道时，原点将创建“最大线程数”属性中指定的线程数。每个线程都连接到原始系统，创建一批数据，并将该批数据传递给可用的管道运行器。

管道运行器是无源管道实例 - 管道的实例，包括管道中的所有处理器，执行程序和目的地，并在源之后处理所有管道处理。 每个管道运行程序一次处理一个批处理，就像在单个线程上运行的管道一样。当数据流减慢时，管道运行器会闲置等待，直到需要它们为止，并定期生成一个空批。您可以配置“运行者空闲时间”管道属性来指定间隔或选择退出空批次生成。

多线程管道保留每个批处理中的记录顺序，就像单线程管道一样。但是由于批处理 是由不同的流水线处理程序处理的，因此无法确保将批处理写入目的地的顺序。

例如，假设您将“最大线程数”属性设置为5。启动管道时，原点将创建五个线程，而数据收集器将 创建匹配数量的管道运行器。 接收到数据后，原点将批处理传递给每个管道运行器进行处理。

每个管道运行器执行与其余管道相关联的处理。将一批写入管道目标之后，管道运行器就可用于另一批数据。每个批次的处理和写入均应尽快进行，与其他流水线处理程序处理的其他批次无关，因此批次的写入方式可能与读取顺序不同。

在任何给定的时刻，五个流水线运行者可以分别处理一个批处理，因此该多线程管道一次最多可以处理五个批处理。当传入数据变慢时，管道运行器将处于空闲状态，并在数据流增加时立即可用。

有关多线程管道的更多信息，请参见《[多线程管道概述》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Multithreaded_Pipelines/MultithreadedPipelines.html#concept_zpp_2xc_py)。

## 资料格式

Azure IoT /事件中心使用者来源会根据您选择的数据格式从Microsoft Azure事件中心读取数据。您可以使用以下数据格式：

- 二元

  生成一条记录，在记录的根部有一个单字节数组字段。

  当数据超过用户定义的最大数据大小时，原点将无法处理数据。因为未创建记录，所以源无法将记录传递到管道以将其写为错误记录。相反，原点会产生阶段误差。

- JSON格式

  为每个JSON对象生成一条记录。您可以处理包含多个JSON对象或单个JSON数组的JSON文件。

  当对象超过为原点定义的最大对象长度时，原点会根据为阶段配置的错误处理来处理对象。

- SDC记录

  为每条记录生成一条记录。用于处理由数据收集器 管道使用SDC记录数据格式生成的记录。

  对于错误记录，原点提供从原始管道中的原点读取的原始记录，以及可用于更正记录的错误信息。

  处理错误记录时，来源希望原始管道生成的错误文件名和内容。

- 文本

  根据自定义定界符为每行文本或每段文本生成一条记录。

  当线或线段超过为原点定义的最大线长时，原点会截断它。原点添加了一个名为Truncated的布尔字段，以指示该行是否被截断。

  有关使用自定义定界符处理文本的更多信息，请参见[使用自定义定界符的文本数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/TextCDelim.html#concept_lg2_gcg_jx)。

## 配置Azure IoT /事件中心使用者

配置Azure IoT /事件中心使用者来源以将数据写入Microsoft Azure事件中心。在配置源之前，请确保完成必要的先决条件。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **事件中心”**选项卡上，配置以下属性：

   | 活动中心属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 命名空间名称                                                 | 包含要使用的事件中心的名称空间的名称。                       |
   | 活动中心名称                                                 | 事件中心名称。                                               |
   | 共享访问策略名称                                             | 与名称空间关联的策略名称。若要检索策略名称，请登录到Azure门户后，导航到您的命名空间和事件中心，然后单击“共享访问策略”以获取策略列表。在适当的时候，您可以使用默认的共享访问密钥策略RootManageSharedAccessKey。 |
   | 连接字符串键                                                 | 与指定的共享访问策略关联的连接字符串键之一。若要检索连接字符串键，请在访问共享访问策略列表后，单击策略名称，然后复制“连接字符串-主键”值。该值通常以“端点”开头。 |
   | 消费群体                                                     | 消费群体使用。输入与指定事件中心关联的使用者组。您可以使用默认使用者组$ Default。若要查看可用使用者组的列表，请在Azure门户中查看事件中心时，单击“使用者组”。 |
   | 事件处理器前缀                                               | 标识管道的前缀。对于每个包含源的管道，请使用不同的前缀。用于与Azure Event Hub通信。 |
   | 储存帐号名称                                                 | 要使用的存储帐户的名称。有关创建存储帐户的信息，请参阅“ [存储帐户和容器先决条件”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AzureEventHub.html#concept_byn_5rf_bbb)。 |
   | 存储帐户密钥                                                 | 存储帐户的键之一。若要检索存储帐户密钥，请在Azure门户中查看存储帐户详细信息时，单击“访问密钥”。然后复制默认键值之一。 |
   | 容器名称                                                     | 用于管道的容器的名称。有关创建容器的信息，请参阅“ [存储帐户和容器先决条件”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AzureEventHub.html#concept_byn_5rf_bbb)。 |
   | 最大线程 [![img](imgs/icon_moreInfo-20200310112815495.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AzureEventHub.html#concept_ldf_chp_qy) | 原点生成并用于多线程处理的线程数。                           |

3. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 资料格式 [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AzureEventHub.html#concept_zpk_wsx_1bb) | 要写入的数据格式。使用以下选项之一：二元JSON格式SDC记录 [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/SDCRecordFormat.html#concept_qkk_mwk_br)文本 |

4. 对于二进制数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 二元性质                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
   | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |
   | 最大数据大小（字节）                                         | 消息中的最大字节数。较大的消息无法处理或写入错误。           |

5. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

   | JSON属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [压缩格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/DataFormats-Overview.html#concept_uxr_g52_qs) | 文件的压缩格式：无-仅处理未压缩的文件。压缩文件-处理受支持的压缩格式压缩的文件。存档-处理通过支持的存档格式存档的文件。压缩存档-处理通过支持的存档和压缩格式存档和压缩的文件。 |
   | 压缩目录中的文件名模式                                       | 对于归档文件和压缩归档文件，文件名模式表示要在压缩目录中处理的文件。您可以使用UNIX样式的通配符，例如星号或问号。例如，*。json。默认值为*，它处理所有文件。 |
   | JSON内容                                                     | JSON内容的类型。使用以下选项之一：对象数组多个物件           |
   | 最大对象长度（字符）                                         | JSON对象中的最大字符数。较长的对象将转移到管道以进行错误处理。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
   | 字符集                                                       | 要处理的文件的字符编码。                                     |
   | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

6. 对于文本数据，在“ **数据格式”**选项卡上，配置以下属性：

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