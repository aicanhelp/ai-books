# SDC RPC

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310202249460.png) 资料收集器

SDC RPC目标启用两个SDC RPC管道之间的连接。SDC RPC目标将数据传递到一个或多个SDC RPC源。将SDC RPC目标用作SDC RPC原始管道的一部分。

您可以跨一台计算机上的单个Data Collector实例，或者跨本地网络或公共Internet到远程Data Collector使用SDC RPC管道。

配置SDC RPC目标时，可以指定要使用的RPC ID和RPC连接。您可以选择启用加密以安全地传递数据并定义重试和超时属性。您还可以配置SSL / TLS属性，包括默认的传输协议和密码套件。

## RPC连接

在SDC RPC目标中，RPC连接定义目标将数据传递到的位置。

RPC连接是接收数据的SDC RPC来源的主机和端口号。定义多个连接，以允许SDC RPC目标在出现瓶颈的情况下通过多个管道轮循。

## 禁用压缩

将数据传递到SDC RPC源时，默认情况下，SDC RPC目标压缩数据。必要时，可以在目标位置禁用压缩。

SDC RPC目标压缩数据以增强性能。当目标处理小记录时，压缩可能不会提高管道性能。调整管道性能时，您可以尝试在处理小型记录时在目标中禁用压缩。

## 配置SDC RPC目标

配置SDC RPC目标，以将数据传递到另一管道中的一个或多个SDC RPC源。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **RPC”**选项卡上，配置以下属性：

   | RPC属性                                                      | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [SDC RPC连接](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/SDC_RPCdest.html#concept_icz_wzw_dt) | 目标管道继续处理数据的连接信息。使用以下格式： `:`。对每个目标管道使用单个RPC连接。使用 [简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，根据需要添加其他连接。配置接收数据的SDC RPC源时，请使用端口号。 |
   | SDC RPC ID                                                   | 用户定义的ID，以允许目标将数据传递到SDC RPC源。在所有SDC RPC源中使用此ID来处理来自目标的数据。 |
   | 验证服务器证书中的主机                                       | 验证SDC RPC原始密钥库文件中的主机。                          |

3. 在“ **高级”**选项卡上，配置以下属性：

   | 先进物业                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 每批重试                                                     | 目标尝试将批处理写入SDC RPC源的次数。当目标无法在配置的重试次数内写入批次时，它将使批次失败。默认值为3。 |
   | 退避期                                                       | 重试将批处理写入SDC RPC源之前要等待的毫秒数。每次重试后，输入的值将呈指数增加，直到达到5分钟的最大等待时间。例如，如果将退避时间设置为10，则目标将在等待10毫秒后尝试第一次重试，在等待100毫秒后尝试第二次重试，并在等待1000毫秒后尝试第三次重试。设置为0可立即重试。默认值为0。 |
   | 连接超时（毫秒）                                             | 建立与SDC RPC来源的连接的毫秒数。目标根据“每批重试”属性重试连接。默认值为5000毫秒。 |
   | 读取超时（毫秒）                                             | 等待SDC RPC源从批处理中读取数据的毫秒数。目标根据“每批重试次数”属性重试写入。默认值为2000毫秒。 |
   | 使用压缩 [![img](imgs/icon_moreInfo-20200310202249094.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/SDC_RPCdest.html#concept_zdq_rdj_r5) | 使目标能够使用压缩将数据传递到SDC RPC源。默认启用。          |

4. 要使用SSL / TLS，请在“ **TLS”**选项卡上配置以下属性：

   | TLS属性                                                      | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 启用TLS[![img](imgs/icon_moreInfo-20200310202249094.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/RPC_Pipelines/SDC_RPCpipelines_title.html#concept_mrm_qhf_2t) | 启用TLS的使用。                                              |
   | [信任库文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z) | 信任库文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。默认情况下，不使用任何信任库。 |
   | 信任库类型                                                   | 要使用的信任库的类型。使用以下类型之一：Java密钥库文件（JKS）PKCS＃12（p12文件）默认值为Java密钥库文件（JKS）。 |
   | 信任库密码                                                   | 信任库文件的密码。密码是可选的，但建议使用。**提示：**为了保护敏感信息（如密码），可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 信任库信任算法                                               | 用于管理信任库的算法。默认值为SunX509。                      |
   | 使用默认协议                                                 | 确定要使用的传输层安全性（TLS）协议。默认协议是TLSv1.2。要使用其他协议，请清除此选项。 |
   | [传输协议](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_mvs_cxf_5z) | 要使用的TLS协议。要使用默认TLSv1.2以外的协议，请单击“ **添加”**图标并输入协议名称。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加协议。**注意：**较旧的协议不如TLSv1.2安全。 |
   | [使用默认密码套件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_cwx_dyf_5z) | 对SSL / TLS握手使用默认的密码套件。要使用其他密码套件，请清除此选项。 |
   | 密码套房                                                     | 要使用的密码套件。要使用不属于默认密码集的密码套件，请单击“ **添加”**图标并输入密码套件的名称。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加密码套件。输入要使用的其他密码套件的Java安全套接字扩展（JSSE）名称。 |