# InfluxDB

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310184643703.png) 资料收集器

InfluxDB目标将数据写入InfluxDB数据库。

在配置InfluxDB目标时，您将定义连接信息，保留策略以及用作标记点的字段。如果在群集上设置了InfluxDB，则还可以定义写入一致性级别。

您可以使用UDP源起源来读取收集的消息，处理数据，然后以收集的本机格式将消息写入InfluxDB。如果使用读取不同数据格式的原点，则必须将记录映射到InfluxDB数据库中的点。

## 配置InfluxDB目标

配置InfluxDB目标以将数据写入InfluxDB数据库。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **InfluxDB”**选项卡上，配置以下属性：

   | InfluxDB属性   | 描述                                                         |
   | :------------- | :----------------------------------------------------------- |
   | 网址           | InfluxDB HTTP API的URL。                                     |
   | 用户名         | 访问InfluxDB数据库的用户名。如果禁用了InfluxDB身份验证，请输入任何值。 |
   | 密码           | 访问InfluxDB数据库的密码。如果禁用了InfluxDB身份验证，请输入任何值。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 数据库名称     | InfluxDB数据库的名称。                                       |
   | 自动创建数据库 | 指定是否在InfluxDB中创建数据库。选择指定的数据库是否不存在。 |
   | 保留政策       | 为数据库创建的保留策略的名称。如果不输入值，则Data Collector将使用默认的保留策略。 |
   | 一致性等级     | 在群集上设置InfluxDB时使用的写入一致性级别。选择以下选项之一：任意，一个，仲裁或全部。有关写一致性级别的更多信息，请参阅InfluxDB文档。 |
   | 记录映射       | 将记录映射到InfluxDB数据库中的点。选择以下选项之一：收集的-选择是否使用UDP源起源读取收集的数据。InfluxDB接受以收集的本机格式编写的数据。自定义映射-选择原点是否读取其他数据格式。然后，您可以将记录中的特定字段映射到点上的度量，时间戳记和键值字段。 |
   | 测量范围       | 如果配置自定义映射，则字段以映射到点上的测量。               |
   | 时间场         | 如果配置自定义映射，则字段以映射到点上的时间戳。             |
   | 时间单位       | 如果配置自定义映射，请在字段上映射到点上的时间戳单位。       |
   | 价值领域       | 如果配置自定义映射，则要映射到点上键值字段的字段。           |
   | 标签字段       | 用作点上标记的字段。                                         |