# MQTT发布者

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310202015279.png) 资料收集器![img](imgs/icon-Edge-20200310202015484.png) 数据收集器边缘

MQTT发布者目标将消息发布到MQTT代理上的主题。该目标充当发布消息的MQTT客户端，将每个记录写为一条消息。

配置目标时，可以指定连接到MQTT代理所需的信息。当MQTT代理需要用户名和密码时，必须定义连接凭证。您还可以配置SSL / TLS属性，包括默认的传输协议和密码套件。

您可以在目标将消息传递到的MQTT代理上指定主题。

您还可以配置服务质量级别和目标用来启用可靠消息传递的持久性机制。

## 边缘管道先决条件

![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中， MQTT阶段需要使用中间MQTT代理。

例如，边缘发送管道使用MQTT发布器目标写入MQTT代理。MQTT代理临时存储数据，直到Data Collector接收管道中的MQTT订阅服务器源读取数据为止。

## 话题

MQTT发布者目标将消息写入MQTT代理上的单个主题。订阅该主题的任何MQTT客户端都会收到消息。主题是代理用来过滤每个已连接客户端的消息的字符串。

配置目标时，请定义主题名称。您可以在一个主题中包括多个主题级别。例如，以下主题具有三个主题级别：

```
sales/US/NorthernRegion
```

您不能在MQTT Publisher目标使用的主题名称中使用MQTT通配符。

有关更多信息，请参见[关于MQTT主题](http://www.hivemq.com/blog/mqtt-essentials-part-5-mqtt-topics-best-practices)的[HiveMQ文档](http://www.hivemq.com/blog/mqtt-essentials-part-5-mqtt-topics-best-practices)。

## 资料格式

MQTT Publisher目标根据您选择的数据格式将消息写入MQTT代理。

MQTT Publisher目标处理数据格式如下：

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

## 配置MQTT发布者目标

配置MQTT发布者目标，以将消息写入MQTT代理。

![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中， MQTT Publisher目标需要一个[中间MQTT代理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MQTTPublisher.html#concept_jv1_23b_qgb)。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **MQTT”**选项卡上，配置以下属性：

   | MQTT属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 经纪人网址                                                   | MQTT代理URL。输入以下格式：`://:`使用ssl与代理进行安全连接。例如：`tcp://localhost:1883` |
   | 客户编号                                                     | MQTT客户端ID。该ID在连接到同一代理的所有客户端上必须是唯一的。您可以定义一个计算结果为客户端ID的表达式。例如，输入以下表达式以使用唯一的管道ID作为客户端ID：`${pipeline:id()}`如果管道包含多个MQTT阶段，并且您想将唯一的管道ID用作两个阶段的客户机ID，请在客户机ID前面加上以下字符串：`sub-${pipeline:id()} and pub-${pipeline:id()} `否则，所有阶段将使用相同的客户端ID。这可能会导致问题，例如消息消失。 |
   | [话题](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MQTTPublisher.html#concept_bbq_w5q_mz) | 要发布到的主题。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击**添加**图标以阅读其他主题。 |
   | 服务质量                                                     | 确定用于保证消息传递的服务质量级别：最多一次（0）至少一次（1）恰好一次（2）有关更多信息，请参阅[有关服务质量级别](http://www.hivemq.com/blog/mqtt-essentials-part-6-mqtt-quality-of-service-levels)的[HiveMQ文档](http://www.hivemq.com/blog/mqtt-essentials-part-6-mqtt-quality-of-service-levels)。 |
   | 客户持久化机制                                               | 确定服务质量级别至少一次或恰好一次时，目标用来保证消息传递的持久性机制。选择以下选项之一：内存-将消息存储在Data Collector计算机的内存中，直到完成消息传递为止。文件-将消息存储在Data Collector计算机上的本地文件中，直到完成消息传递为止。当服务质量级别最多为一次时不使用。有关更多信息，请参阅[有关客户端持久性](http://www.hivemq.com/blog/mqtt-essentials-part-7-persistent-session-queuing-messages)的[HiveMQ文档](http://www.hivemq.com/blog/mqtt-essentials-part-7-persistent-session-queuing-messages)。 |
   | 客户端持久性数据目录                                         | 配置文件持久性时，Data Collector计算机上的本地目录，目标将文件中的消息临时存储在该目录中。启动Data Collector的用户必须具有对该目录的读写权限。 |
   | 保持活动间隔（秒）                                           | 允许与MQTT代理的连接保持空闲状态的最长时间（以秒为单位）。在此时间段内目标未发布任何消息后，连接将关闭。目标必须重新连接到MQTT代理。默认值为60秒。 |
   | 使用凭证                                                     | 在“凭据”选项卡上启用输入凭据。当MQTT代理要求用户名和密码时使用。 |
   | 保留留言                                                     | 确定在没有订阅任何MQTT客户端收听主题时，MQTT代理是否保留目标最后发布的消息。选中后，MQTT代理将保留目标发布的最后一条消息。先前发布的所有消息都将丢失。清除后，目的地发布的所有消息都会丢失。有关MQTT保留消息的更多信息，请参见http://www.hivemq.com/blog/mqtt-essentials-part-8-retained-messages。 |

3. 在“ **凭据”**选项卡上，输入启用身份验证后要使用的MQTT凭据。

   **提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。

4. 要使用SSL / TLS，请在“ **TLS”**选项卡上配置以下属性：

   ![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中，仅“ **使用TLS”**和“ **信任库文件”**属性有效。启用TLS之后，为使用PEM格式的信任库文件输入绝对路径。在Data Collector Edge管道中，MQTT Publisher目标始终使用默认协议和密码套件。它忽略所有其他TLS属性。

   | TLS属性                                                      | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 使用TLS                                                      | 启用TLS的使用。                                              |
   | [信任库文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z) | 信任库文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。默认情况下，不使用任何信任库。![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中，输入使用PEM格式的文件的绝对路径。 |
   | 信任库类型                                                   | 要使用的信任库的类型。使用以下类型之一：Java密钥库文件（JKS）PKCS＃12（p12文件）默认值为Java密钥库文件（JKS）。 |
   | 信任库密码                                                   | 信任库文件的密码。密码是可选的，但建议使用。**提示：**为了保护敏感信息（如密码），可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 信任库信任算法                                               | 用于管理信任库的算法。默认值为SunX509。                      |
   | 使用默认协议                                                 | 确定要使用的传输层安全性（TLS）协议。默认协议是TLSv1.2。要使用其他协议，请清除此选项。 |
   | [传输协议](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_mvs_cxf_5z) | 要使用的TLS协议。要使用默认TLSv1.2以外的协议，请单击“ **添加”**图标并输入协议名称。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加协议。**注意：**较旧的协议不如TLSv1.2安全。 |
   | [使用默认密码套件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_cwx_dyf_5z) | 对SSL / TLS握手使用默认的密码套件。要使用其他密码套件，请清除此选项。 |
   | 密码套房                                                     | 要使用的密码套件。要使用不属于默认密码集的密码套件，请单击“ **添加”**图标并输入密码套件的名称。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加密码套件。输入要使用的其他密码套件的Java安全套接字扩展（JSSE）名称。 |

5. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MQTTPublisher.html#concept_xn3_zxq_mz) | 消息的数据格式。使用以下数据格式之一：二元JSON格式[SDC记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/SDCRecordFormat.html#concept_qkk_mwk_br)文本 |

6. 对于二进制数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 二元性质     | 描述                   |
   | :----------- | :--------------------- |
   | 二进制场路径 | 包含二进制数据的字段。 |

7. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

   | JSON属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | JSON内容 | 写入JSON数据的方法：JSON对象数组-每个文件都包含一个数组。在数组中，每个元素都是每个记录的JSON表示形式。多个JSON对象-每个文件包含多个JSON对象。每个对象都是记录的JSON表示形式。 |
   | 字符集   | 写入数据时使用的字符集。                                     |

8. 对于文本数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 文字属性                       | 描述                                                         |
   | :----------------------------- | :----------------------------------------------------------- |
   | 文字栏位路径                   | 包含要写入的文本数据的字段。所有数据必须合并到指定字段中。   |
   | 记录分隔符                     | 用于分隔记录的字符。使用任何有效的Java字符串文字。例如，当写入Windows时，您可能会\r\n用来分隔记录。默认情况下，目标使用 \n。 |
   | 在失落的田野上                 | 当记录不包含文本字段时，确定目标是将丢失的字段报告为错误还是忽略该丢失的字段。 |
   | 如果没有文本，则插入记录分隔符 | 当配置为忽略缺少的文本字段时，插入配置的记录分隔符字符串以创建一个空行。如果未选择，则丢弃没有文本字段的记录。 |
   | 字符集                         | 写入数据时使用的字符集。                                     |