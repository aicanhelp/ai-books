# 地理IP

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310180615233.png) 资料收集器

Geo IP处理器是一个查找处理器，可以返回指定IP地址的地理位置和IP智能信息。

Geo IP处理器使用MaxMind GeoIP2数据库文件进行查找。您必须提供自己的数据库文件副本。

**提示：** MaxMind提供了一些免费的数据库供您使用。

要使用Geo IP处理器，请指定数据库文件的位置和要使用的数据库类型。输入一个或多个IP地址输入字段，命名相应的输出字段，然后指定所需的返回信息。如果数据库文件没有IP地址，您还可以配置处理器采取的操作。

输入字段必须是传递IPv4或IPv6地址的Integer或String数据类型。

## 支持的数据库

您可以将大多数MaxMind GeoIP2数据库与GeoIP处理器一起使用。处理器支持以下GeoIP2数据库：

- 匿名IP
- 市
- 国家
- 连接类型
- 域
- 互联网服务提供商

## 数据库文件位置

要使用Geo IP处理器，请将要使用的MaxMind GeoIP2数据库文件保存在Data Collector本地目录中或 Data Collector 资源目录$ SDC_RESOURCES中。

然后，在配置处理器时指定数据库文件的位置。

## GeoIP字段类型

每个GeoIP2数据库提供您可以请求的一组不同的信息。在配置处理器时，请确保请求使用的数据库中存在的信息。

例如，如果您将处理器配置为使用“城市”和“国家”数据库，请不要请求域信息。要返回域详细信息，您需要使用域数据库。

在处理器中，您可以通过定义**GeoIP字段类型**属性来请求返回值。

下表列出了每个数据库的有效GeoIP字段类型。有关每种字段类型返回的信息的详细信息，请参见MaxMind GeoIP2文档。

| 数据库           | 有效的GeoIP字段类型                                          |
| :--------------- | :----------------------------------------------------------- |
| 匿名IP           | 匿名IP完整JSON是匿名的是匿名VPN是托管提供商是公共代理人是TOR出口节点 |
| 市               | 城市完整JSON城市名称国家国家ISO编码纬度经度                  |
| 国家             | 国家国家完整的JSON国家ISO编码                                |
| 连接类型         | 连接类型连接类型完整JSON                                     |
| 域               | 域域完整JSON                                                 |
| 互联网服务提供商 | 自治系统号自治系统组织互联网服务提供商ISP完整JSON组织        |

### 完整的JSON字段类型

GeoIP处理器为每个数据库提供完整的JSON字段类型。Full JSON字段类型返回字典中指定IP地址的所有可用数据。

当所需的信息在数据库中，但不能作为处理器中的一种字段类型提供时，请使用Full JSON字段类型。

Full JSON字段类型返回带有所有可用数据的JSON对象。您可以在下游使用JSON Parser处理器来解析对象并提取所需的信息。

**相关概念**

[JSON解析器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/JSONParser.html#concept_bs1_4t3_yq)

## 配置Geo IP处理器

配置Geo IP处理器以基于IP地址返回地理位置信息。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **地理位置”**选项卡上，配置以下属性：

   | Geo IP属性                                                   | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | GeoIP2数据库 [![img](imgs/icon_moreInfo-20200310180615706.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/GeoIP.html#concept_clx_bng_hx) | 您要使用的MaxMind GeoIP数据库。使用 [简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以添加其他数据库。 |
   | GeoIP2数据库文件                                             | GeoIP2数据库文件所在的目录。输入标准位置或数据收集器资源目录$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。 |
   | GeoIP2数据库类型                                             | 数据库类型。                                                 |
   | 数据库字段映射                                               | 每个输入字段，输出字段以及要在输出字段中返回的数据的映射。   |
   | 输入栏位名称                                                 | 使用IP地址的传入字段。该字段可以是Integer或String数据类型。  |
   | 输出字段名称                                                 | 要传递所选地理位置数据的字段名称。                           |
   | GeoIP2字段[![img](imgs/icon_moreInfo-20200310180615706.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/GeoIP.html#concept_ewg_mgh_hx) | 来自可用数据库的数据将传递到输出字段。                       |
   | 遗失地址动作                                                 | 指定数据库文件中缺少IP地址时要采取的措施：发送到错误-根据为该阶段配置的错误处理来处理记录。用空值替换-将所有指定的输出字段添加到记录中，用空值替换缺少的值。忽略-忽略丢失的数据，并且不将指定的输出字段添加到记录中。默认值为“发送到错误”。 |

3. 要返回其他地理位置数据，请单击“ **添加”** 图标。

   您可以返回相同输入字段或不同输入字段的地理位置数据。