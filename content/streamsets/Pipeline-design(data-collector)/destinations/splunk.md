# Splunk

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310202523320.png) 资料收集器

Splunk目标使用Splunk HTTP事件收集器（HEC）将数据写入Splunk。

目标使用JSON数据格式将HTTP POST请求发送到HEC端点。目标为每个批次生成一个HTTP请求，一次发送多个记录。每条记录必须包含事件数据，并可选地包含[Splunk所需格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Splunk.html#concept_jvc_nzm_d2b)的事件元数据 。

在配置目标之前，您必须完成几个先决条件，包括在Splunk中启用HEC和创建HEC身份验证令牌。

配置Splunk目标时，需要提供Splunk API端点和HEC身份验证令牌。您可以配置超时，请求传输编码和身份验证类型。您可以将目标配置为使用Gzip或Snappy压缩格式来写入数据。您可以选择使用HTTP代理并配置SSL / TLS属性。

您还可以配置目标以记录请求和响应信息。

## 先决条件

必须先满足以下先决条件，然后才能写给Splunk：

- 启用HTTP事件收集器（HEC）

  默认情况下，Splunk中的HEC被禁用。如Splunk文档中所述[启用HEC](http://docs.splunk.com/Documentation/Splunk/latest/Data/HECWalkthrough)。

- 创建一个HTTP事件收集器（HEC）令牌

  要将数据发送到HEC，Splunk目标必须使用令牌对运行HEC的Splunk服务器进行身份验证。如Splunk文档中所述[创建HEC令牌](http://docs.splunk.com/Documentation/Splunk/latest/Data/HECWalkthrough)。

  在Data Collector中配置Splunk目标时，请输入此令牌值。

## 所需记录格式

Splunk要求在记录中正确设置事件数据和元数据的格式。如果记录的格式不正确，则会发生错误，并且目标无法写入Splunk。当使用Splunk目标设计管道时，必须确保发送到目标的记录使用必需的格式。

记录必须包含一个/ event字段，其中包含事件数据。/ event字段可以是字符串，映射或列表映射字段。有关更多信息，请参阅Splunk文档中的[事件数据](http://docs.splunk.com/Documentation/Splunk/7.1.1/Data/FormateventsforHTTPEventCollector)。

**重要：** Splunk目标不支持原始事件。必须在/ event字段中发送事件。

该记录可以选择包含事件元数据字段。Splunk包含几个可以包含在事件元数据中的预定义键。事件中未包括的所有元数据键值对均设置为为Splunk服务器上的令牌定义的值。有关事件元数据中可以包含的键的列表，请参阅Splunk文档中的[事件元数据](http://docs.splunk.com/Documentation/Splunk/7.1.1/Data/FormateventsforHTTPEventCollector)。

例如，以下记录包括三个键，这些键可以包含在事件元数据中，也可以使用Map数据类型包含在/ event字段中：

```
{
    "time": 1437522387,
    "host": "myserver.example.com",
    "source": "myapp",
    "event": { 
        "message": "Here is my message",
        "severity": "INFO"
    }
}
```

下面的记录包括五个可以使用String数据类型包含在事件元数据和/ event字段中的键：

```
{
    "time": 1426279439, // epoch time
    "host": "localhost",
    "source": "datasource",
    "sourcetype": "txt",
    "index": "main",
    "event": "Here is my event" 
}
```

## 记录请求和响应数据

Splunk目标可以将请求和响应数据记录到Data Collector 日志中。

启用日志记录时，可以配置以下属性：

- 细度

  要记录的消息中包括的数据类型：Headers_Only-包括请求和响应头。Payload_Text-包括请求和响应头以及任何文本有效载荷。Payload_Any-包括请求和响应头以及有效载荷，与类型无关。

- 日志级别

  要包含在数据收集器日志中的消息级别。选择级别时，还将记录更高级别的消息。即，如果选择警告日志级别，则将严重和警告消息写入数据收集器日志。

  **注意：**为Data Collector配置的日志级别可以限制所记录的详细信息级别。例如，如果将日志级别设置为“最高级”以记录详细的跟踪信息，但是将Data Collector配置为ERROR，则原始消息仅写入严重级别的消息。

  下表描述了启用日志记录所需的阶段日志级别和相应的Data Collector日志级别：阶段日志级别资料收集器描述严重错误仅显示严重故障的消息。警告警告消息警告潜在问题。信息信息信息性消息。精细调试基本跟踪信息。更细调试详细的跟踪信息。最好的跟踪高度详细的跟踪信息。

  此舞台记录器的名称为 `com.streamsets.http.RequestLogger`。

- 最大实体大小

  写入日志的消息数据的最大大小。用于限制任何单个消息写入数据收集器日志的数据量。

## 配置Splunk目标

配置Splunk目标，以使用Splunk HTTP事件收集器（HEC）将数据写入Splunk。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **Splunk”**选项卡上，配置以下属性：

   | Splunk物业     | 描述                                                         |
   | :------------- | :----------------------------------------------------------- |
   | Splunk API端点 | Splunk API端点以以下格式输入：`://:`例如：`https://server.example.com:8088`有关配置端点的更多信息，请参阅Splunk文档中的“ [将数据发送到HEC](http://docs.splunk.com/Documentation/Splunk/latest/Data/HECWalkthrough) ”。 |
   | Splunk代币     | 您为目的地创建的HEC令牌的值，如[前提条件中所述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Splunk.html#concept_xrt_ynm_d2b)。 |

3. 在“ **HTTP”**选项卡上，配置以下属性：

   | HTTP属性       | 描述                                                         |
   | :------------- | :----------------------------------------------------------- |
   | 请求传输编码   | 使用以下编码类型之一：缓冲-标准传输编码类型。块-分块传输数据。并非所有服务器都支持。默认为缓冲。 |
   | HTTP压缩       | 消息的压缩格式：没有活泼的压缩文件                           |
   | 连接超时       | 等待连接的最大毫秒数。使用0无限期等待。                      |
   | 读取超时       | 等待数据的最大毫秒数。使用0无限期等待。                      |
   | 认证类型       | 确定用于连接到服务器的身份验证类型：无-不执行身份验证。基本-使用基本身份验证。需要用户名和密码。与HTTPS一起使用，以避免传递未加密的凭据。摘要-使用摘要身份验证。需要用户名和密码。通用-建立匿名连接，然后在收到401状态和WWW-Authenticate标头请求后提供身份验证凭据。需要与基本或摘要身份验证关联的用户名和密码。仅用于响应此工作流程的服务器。OAuth-使用OAuth 1.0身份验证。需要OAuth凭据。 |
   | 使用代理服务器 | 启用使用HTTP代理连接到系统。                                 |

4. 要使用HTTP代理，请在“ **代理”**选项卡上配置以下属性：

   | HTTP代理属性 | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 代理URI      | 代理URI。                                                    |
   | 用户名       | 代理用户名。                                                 |
   | 密码         | 代理密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |

5. 要使用SSL / TLS，请在“ **TLS”**选项卡上配置以下属性：

   | TLS属性                                                      | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 使用TLS                                                      | 启用TLS的使用。                                              |
   | [密钥库文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z) | 密钥库文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。默认情况下，不使用任何密钥库。 |
   | 密钥库类型                                                   | 要使用的密钥库的类型。使用以下类型之一：Java密钥库文件（JKS）PKCS＃12（p12文件）默认值为Java密钥库文件（JKS）。 |
   | 密钥库密码                                                   | 密钥库文件的密码。密码是可选的，但建议使用。**提示：**为了保护敏感信息（如密码），可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 密钥库密钥算法                                               | 用于管理密钥库的算法。默认值为 SunX509。                     |
   | [信任库文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z) | 信任库文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。默认情况下，不使用任何信任库。 |
   | 信任库类型                                                   | 要使用的信任库的类型。使用以下类型之一：Java密钥库文件（JKS）PKCS＃12（p12文件）默认值为Java密钥库文件（JKS）。 |
   | 信任库密码                                                   | 信任库文件的密码。密码是可选的，但建议使用。**提示：**为了保护敏感信息（如密码），可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 信任库信任算法                                               | 用于管理信任库的算法。默认值为SunX509。                      |
   | 使用默认协议                                                 | 确定要使用的传输层安全性（TLS）协议。默认协议是TLSv1.2。要使用其他协议，请清除此选项。 |
   | [传输协议](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_mvs_cxf_5z) | 要使用的TLS协议。要使用默认TLSv1.2以外的协议，请单击“ **添加”**图标并输入协议名称。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加协议。**注意：**较旧的协议不如TLSv1.2安全。 |
   | [使用默认密码套件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_cwx_dyf_5z) | 对SSL / TLS握手使用默认的密码套件。要使用其他密码套件，请清除此选项。 |
   | 密码套房                                                     | 要使用的密码套件。要使用不属于默认密码集的密码套件，请单击“ **添加”**图标并输入密码套件的名称。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加密码套件。输入要使用的其他密码套件的Java安全套接字扩展（JSSE）名称。 |

6. 在“ **日志记录”**选项卡上，配置以下属性以记录请求和响应数据：

   | 记录属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 启用请求记录                                                 | 启用记录请求和响应数据。                                     |
   | 日志级别 [![img](imgs/icon_moreInfo-20200310202523859.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Splunk.html#concept_hgj_ytm_d2b) | 要记录的详细信息级别。选择可用选项之一。以下列表是从最低到最高的日志记录顺序。选择级别时，由所选级别以上的级别生成的消息也将写入日志：严重-仅指示严重故障的消息。警告-消息警告潜在问题。信息-信息性消息。精细-基本跟踪信息。更精细-详细的跟踪信息。最好-高度详细的跟踪信息。**注意：**为Data Collector配置的日志级别可以限制阶段写入的消息级别。验证Data Collector日志级别是否支持您要使用的级别。 |
   | 细度                                                         | 要记录的消息中包括的数据类型：Headers_Only-包括请求和响应头。Payload_Text-包括请求和响应头以及任何文本有效载荷。Payload_Any-包括请求和响应头以及有效载荷，与类型无关。 |
   | 最大实体大小                                                 | 写入日志的消息数据的最大大小。用于限制任何单个消息写入数据收集器日志的数据量。 |