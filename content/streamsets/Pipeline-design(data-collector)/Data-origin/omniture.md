# Omniture

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310173200874.png) 资料收集器

Omniture来源处理由Omniture报告API生成的JSON网站使用情况报告。Omniture也称为Adobe Marketing Cloud。

配置Omniture来源时，可以指定连接信息，请求间隔和报告描述。您可以选择使用代理连接到原始系统。

请求间隔是Omniture来源在请求其他报告之前等待的时间。但是，如果Omniture API尚未响应先前的请求，则原始服务器将一直等待直到收到响应，然后立即发送新请求。

有关Omniture报告的信息，请参阅Adobe Marketing Cloud文档。

## 配置Omniture来源

配置Omniture来源以处理来自Omniture报告API的Web使用情况报告。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **Omniture”**选项卡上，配置以下属性：

   | 多功能物业             | 描述                                                         |
   | :--------------------- | :----------------------------------------------------------- |
   | Omniture REST URL      | 供Omniture报告API使用的REST URL。                            |
   | 请求超时（毫秒）       | 连接超时之前的毫秒数。                                       |
   | 模式                   | 用于请求报告的模式。默认为轮询。                             |
   | 报告请求间隔（毫秒）   | 在两次请求之间等待的毫秒数。必要时，源会延迟请求，直到API响应上一个请求为止。默认值为5,000。 |
   | 最大批次大小（报告）   | 批处理中包含的最大报告数。默认值为1。                        |
   | 批处理等待时间（毫秒） | 发送空批次或部分批次之前要等待的毫秒数。默认值为5,000。      |
   | 用户名                 | Omniture用户名。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 共享秘密               | Omniture共享秘密。                                           |
   | 使用代理服务器         | 选择以使用HTTP代理连接到原始系统。                           |

3. 在“ **报告”**选项卡上，输入Omniture报告的描述。

4. 要使用HTTP代理，请在“ **代理”**选项卡上配置以下属性：

   | HTTP代理属性 | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 代理URI      | Omniture代理URI。                                            |
   | 用户名       | 代理用户名。                                                 |
   | 密码         | 代理密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |