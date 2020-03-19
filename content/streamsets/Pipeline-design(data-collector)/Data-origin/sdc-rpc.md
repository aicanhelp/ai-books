# SDC RPC

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310174127781.png) 资料收集器

SDC RPC起源支持两个SDC RPC管道之间的连接。SDC RPC源读取从SDC RPC目标传递的数据。将SDC RPC起源用作SDC RPC目标管道的一部分。

您可以跨一台计算机上的单个Data Collector实例，或者跨本地网络或公共Internet到远程Data Collector使用SDC RPC管道。您还可以配置SSL / TLS属性，包括默认的传输协议和密码套件。

配置SDC RPC原点时，可以定义原点侦听数据的端口。必须在SDC RPC目标中定义的RPC连接之一中指定端口号。

同样，SDC RPC源中的RPC ID必须与SDC RPC目标中使用的RPC ID相匹配。

## 配置SDC RPC原始

配置SDC RPC源以处理来自SDC RPC目标的数据。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **RPC”**选项卡上，配置以下属性：

   | RPC属性                                                      | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | RPC侦听端口                                                  | 侦听数据的端口号。必须与与提供数据的SDC RPC目标关联的端口号之一匹配。**注意：**没有其他管道或进程已经可以绑定到侦听端口。侦听端口只能由单个管道使用。 |
   | RPC ID                                                       | 用户定义的ID。必须与SDC RPC目标中定义的RPC ID匹配。          |
   | [批处理等待时间（毫秒）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ypd_vgr_5q) | 发送部分或空批次之前要等待的毫秒数。                         |

3. （可选）单击“ **高级”**选项卡以配置高级属性：

   | 先进物业           | 描述                                                         |
   | :----------------- | :----------------------------------------------------------- |
   | 最大记录大小（MB） | 记录的最大大小。较大的记录将路由到管道以进行错误处理。默认值是10。 |

4. 要使用SSL / TLS，请在“ **TLS”**选项卡上配置以下属性：

   | TLS属性                                                      | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 启用TLS [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/RPC_Pipelines/SDC_RPCpipelines_title.html#concept_mrm_qhf_2t) | 启用TLS的使用。                                              |
   | [信任库文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z) | 信任库文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。默认情况下，不使用任何信任库。 |
   | 信任库类型                                                   | 要使用的信任库的类型。使用以下类型之一：Java密钥库文件（JKS）PKCS＃12（p12文件）默认值为Java密钥库文件（JKS）。 |
   | 信任库密码                                                   | 信任库文件的密码。密码是可选的，但建议使用。**提示：**为了保护敏感信息（如密码），可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 信任库信任算法                                               | 用于管理信任库的算法。默认值为SunX509。                      |
   | 使用默认协议                                                 | 确定要使用的传输层安全性（TLS）协议。默认协议是TLSv1.2。要使用其他协议，请清除此选项。 |
   | [传输协议](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_mvs_cxf_5z) | 要使用的TLS协议。要使用默认TLSv1.2以外的协议，请单击“ **添加”**图标并输入协议名称。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加协议。**注意：**较旧的协议不如TLSv1.2安全。 |
   | [使用默认密码套件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_cwx_dyf_5z) | 对SSL / TLS握手使用默认的密码套件。要使用其他密码套件，请清除此选项。 |
   | 密码套房                                                     | 要使用的密码套件。要使用不属于默认密码集的密码套件，请单击“ **添加”**图标并输入密码套件的名称。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加密码套件。输入要使用的其他密码套件的Java安全套接字扩展（JSSE）名称。 |