# Azure Event Hub生产者

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310182951003.png) 资料收集器

Azure事件中心生产者将数据写入Microsoft Azure事件中心。若要写入Microsoft Azure Data Lake Storage，请使用Azure Data Lake Storage目标。若要写入Microsoft Azure IoT中心，请使用Azure IoT中心生产者目标。

配置Azure事件中心生产者时，可以指定Microsoft Azure名称空间和事件中心名称。您还可以定义共享访问策略名称和连接字符串键。

## 资料格式

Azure Event Hub生产者目标根据您选择的数据格式将数据写入Microsoft Azure Event Hub。您可以使用以下数据格式：

- 二元

  该阶段将二进制数据写入记录中的单个字段。

- JSON格式

  目标将记录作为JSON数据写入。您可以使用以下格式之一：数组-每个文件都包含一个数组。在数组中，每个元素都是每个记录的JSON表示形式。多个对象-每个文件都包含多个JSON对象。每个对象都是记录的JSON表示形式。

- SDC记录

  目标以SDC记录数据格式写入记录。

- 文本

  目标将数据从单个文本字段写入目标系统。配置阶段时，请选择要使用的字段。

  您可以配置字符以用作记录分隔符。默认情况下，目标使用UNIX样式的行尾（\ n）分隔记录。

  当记录不包含选定的文本字段时，目标可以将缺少的字段报告为错误或忽略缺少的字段。默认情况下，目标报告错误。

  当配置为忽略缺少的文本字段时，目标位置可以丢弃该记录或写入记录分隔符以为该记录创建一个空行。默认情况下，目标丢弃记录。

- XML格式

  目标为每个记录创建一个有效的XML文档。目标要求记录具有一个包含其余记录数据的单个根字段。有关如何完成此操作的详细信息和建议，请参阅[记录结构要求](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WritingXML.html#concept_cmn_hml_r1b)。目的地可以包括缩进以产生人类可读的文档。它还可以验证所生成的XML是否符合指定的架构定义。具有无效架构的记录将根据为目标配置的错误处理进行处理。

## 配置Azure Event Hub生产者目标

配置Azure事件中心生产者目标以将数据写入Microsoft Azure事件中心。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_zrl_mhn_lx) | 发生事件时生成事件记录。用于[事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **事件中心”**选项卡上，配置以下属性：

   | 活动中心属性     | 描述                                                         |
   | :--------------- | :----------------------------------------------------------- |
   | 命名空间名称     | 包含要使用的事件中心的名称空间的名称。                       |
   | 活动中心名称     | 事件中心名称。                                               |
   | 共享访问策略名称 | 与名称空间关联的策略名称。若要检索策略名称，请登录到Azure门户后，导航到您的命名空间和事件中心，然后单击“共享访问策略”以获取策略列表。在适当的时候，您可以使用默认的共享访问密钥策略RootManageSharedAccessKey。 |
   | 连接字符串键     | 与指定的共享访问策略关联的连接字符串键之一。若要检索连接字符串键，请在访问共享访问策略列表后，单击策略名称，然后复制“连接字符串-主键”值。该值通常以“端点”开头。 |

   有关Microsoft Azure Event Hub的更多信息，请参见[Event Hub文档](https://docs.microsoft.com/en-us/azure/event-hubs/)。

3. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AzureEventHubProducer.html#concept_zpk_wsx_1bb) | 要写入的数据格式。使用以下选项之一：二元JSON格式[SDC记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/SDCRecordFormat.html#concept_qkk_mwk_br)文本XML格式 |

4. 对于二进制数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 二元性质     | 描述                   |
   | :----------- | :--------------------- |
   | 二进制场路径 | 包含二进制数据的字段。 |

5. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

   | JSON属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | JSON内容 | 写入JSON数据的方法：JSON对象数组-每个文件都包含一个数组。在数组中，每个元素都是每个记录的JSON表示形式。多个JSON对象-每个文件包含多个JSON对象。每个对象都是记录的JSON表示形式。 |
   | 字符集   | 写入数据时使用的字符集。                                     |

6. 对于文本数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 文字属性                       | 描述                                                         |
   | :----------------------------- | :----------------------------------------------------------- |
   | 文字栏位路径                   | 包含要写入的文本数据的字段。所有数据必须合并到指定字段中。   |
   | 记录分隔符                     | 用于分隔记录的字符。使用任何有效的Java字符串文字。例如，当写入Windows时，您可能会\r\n用来分隔记录。默认情况下，目标使用 \n。 |
   | 在失落的田野上                 | 当记录不包含文本字段时，确定目标是将丢失的字段报告为错误还是忽略该丢失的字段。 |
   | 如果没有文本，则插入记录分隔符 | 当配置为忽略缺少的文本字段时，插入配置的记录分隔符字符串以创建一个空行。如果未选择，则丢弃没有文本字段的记录。 |
   | 字符集                         | 写入数据时使用的字符集。                                     |

7. 对于XML数据，在“ **数据格式”**选项卡上，配置以下属性：

   | XML属性  | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 漂亮格式 | 添加缩进以使生成的XML文档更易于阅读。相应地增加记录大小。    |
   | 验证架构 | 验证生成的XML是否符合指定的架构定义。具有无效架构的记录将根据为目标配置的错误处理进行处理。**要点：**无论是否验证XML模式，目的地都需要特定格式的记录。有关更多信息，请参见[记录结构要求](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WritingXML.html#concept_cmn_hml_r1b)。 |
   | XML模式  | 用于验证记录的XML模式。                                      |