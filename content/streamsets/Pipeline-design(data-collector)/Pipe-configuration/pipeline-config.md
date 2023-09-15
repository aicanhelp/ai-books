# 配置管道

配置管道以定义数据流。配置管道后，可以启动管道。

管道可以包括以下阶段：

- 单一起源阶段
- 多处理器阶段
- 多个目的地阶段
- 多个执行者阶段
- 多个管道片段

1. 在“管道存储库”视图中，单击“ **添加”**图标。

2. 在“ **新建管道”**窗口中，输入管道标题和可选描述，然后选择要创建的管道类型：

   - 数据收集器管道-选择以设计在数据收集器上运行的独立或集群执行模式管道。
   - Data Collector Edge Pipeline-选择以设计在Data Collector Edge上运行的边缘执行模式管道。

3. 选择您要如何创建管道，然后单击“ **下一步”**。

   - 空白管道-使用空白画布进行管道开发。
   - 管道模板-使用现有[模板](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Pipelines/PipelineTemplates.html#concept_wms_j5t_1jb)作为管道开发的基础。

4. 如果选择了管道模板，则在“ **选择管道模板”**对话框中，按模板类型进行过滤，选择要使用的模板，然后单击“ **下一步”**。

5. 在“ **选择创作数据收集器”**对话框中，选择要使用的创作数据收集器，然后单击“ **创建”**。

   管道设计器 将打开一个空白画布或选定的管道模板。

6. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 管道属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 标题                                                         | 管道的标题。管道设计器使用为管道标题输入的字母数字字符作为生成的管道ID的前缀。例如，如果输入 My Pipeline *&%&^^ 123作为管道标题，则管道ID具有以下值：MyPipeline123tad9f592-5f02-4695-bb10-127b2e41561c。您可以编辑管道标题。但是，由于使用管道ID来标识管道，因此对管道标题的任何更改都不会反映在管道ID中。 |
   | 描述                                                         | 管道的可选描述。                                             |
   | 标签                                                         | 分配给管道的可选标签。使用标签将相似的管道分组。例如，您可能想按数据库模式或测试或生产环境对管道进行分组。将标签分配templates给要用作其他管道模板的管道。您可以使用嵌套标签来创建管道分组的层次结构。使用以下格式输入嵌套标签：`//`例如，在由源系统测试环境组管道，您可以添加标签`Test/HDFS`，并`Test/Elasticsearch`以适当的管道。 |
   | [执行方式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/ClusterPipelines.html#concept_hmh_kfn_1s) | 管道的执行方式：独立-单个Data Collector进程运行管道。群集批处理- 数据收集器会根据需要产生其他工作程序，以处理HDFS或MapR中的数据。处理所有可用数据，然后停止管道。群集纱线流- 默认情况下，Data Collector会根据需要生成其他工作线程来处理数据。您可以使用“工作人员计数”集群属性来限制工作人员的数量。而且，您可以使用Extra Spark Configuration属性将Spark配置传递给spark-submit脚本。用于从使用YARN上的Spark Streaming的Kafka或MapR群集中流式传输数据。集群Mesos流传输- 数据收集器会根据需要产生其他工作程序来处理数据。用于从使用Mesos上的Spark Streaming的Kafka群集中流式传输数据。[边缘](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Edge_Mode/EdgePipelines_Overview.html#concept_d4h_kkq_4bb) -单个Data Collector Edge（SDC Edge）进程在边缘设备上运行管道。 |
   | 数据收集器边缘 URL                                           | 仅用于在Data Collector中设计的边缘管道，然后直接发布到未在Control Hub中注册的SDC Edge并在其上进行管理。您可以为在Pipeline Designer中设计的边缘管道保留默认值。![img](imgs/icon-SDC-20200310111145128.png)在Data Collector管道中不可用。 |
   | [交货保证](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DatainMotion.html#concept_ffz_hhw_kq) | 确定在意外事件导致管道停止运行后，Data Collector如何处理数据：至少一次-确保处理所有数据并将其写入目的地。可能导致重复的行。最多一次-确保不重新处理数据，以防止将重复的数据写入目标。可能会导致缺少行。默认值为“至少一次”。 |
   | [测试原点](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/TestOrigin.html#concept_sgt_s5v_g2b) | 提供数据预览的虚拟数据源。仅在“预览配置”对话框中选择“测试原点”选项时使用。要启用测试原点，请选择原点以访问测试数据，然后在“测试原点”选项卡上配置原点属性。您可以使用任何可用的来源。默认值为开发原始数据源来源。 |
   | 开始事件                                                     | 确定如何处理开始事件。选择以下选项之一：舍弃-不想使用该事件时使用。执行程序-要使用事件触发任务，请选择要使用的执行程序。有关执行程序的更多信息，请参见[执行程序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Executors-overview.html#concept_stt_2lk_fx)。写入另一个管道-用于将事件传递到另一个管道以进行更复杂的处理。仅在独立的Data Collector管道中使用。有关管道事件的更多信息，请参见[管道事件生成](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_amg_2qr_t1b)。![img](imgs/icon-Edge-20200310111145515.png)在Data Collector Edge管道中不可用。 |
   | 停止事件                                                     | 确定如何处理stop事件。选择以下选项之一：舍弃-不想使用该事件时使用。执行程序-要使用事件触发任务，请选择要使用的执行程序。有关执行程序的更多信息，请参见[执行程序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Executors-overview.html#concept_stt_2lk_fx)。写入另一个管道-用于将事件传递到另一个管道以进行更复杂的处理。仅在独立的Data Collector管道中使用。有关管道事件的更多信息，请参见[管道事件生成](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_amg_2qr_t1b)。![img](imgs/icon-Edge-20200310111145515.png)在Data Collector Edge管道中不可用。 |
   | [重试管道错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/Retry.html#concept_cgm_ktz_2t) | 出错时重试管道。![img](imgs/icon-Edge-20200310111145515.png)在Data Collector Edge管道中不可用。 |
   | 重试尝试                                                     | 尝试重试的次数。使用-1无限期重试。重试之间的等待时间从15秒开始，翻倍直到达到5分钟。![img](imgs/icon-Edge-20200310111145515.png)在Data Collector Edge管道中不可用。 |
   | [速率限制（记录/秒）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/PipelineRateLimit.html#concept_erj_qg4_qv) | 管道在一秒钟内可以读取的最大记录数。使用0或无值设置无速率限制。默认值为0。![img](imgs/icon-Edge-20200310111145515.png)在Data Collector Edge管道中不可用。 |
   | 最大跑步者                                                   | 多线程管道中使用的最大管道运行器数。无限制地使用0。设置为0时，Data Collector最多生成源中配置的最大线程数或并行性。您可以使用此属性来帮助调整管道性能。有关更多信息，请参见[调整线程和运行器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Multithreaded_Pipelines/MultithreadedPipelines.html#concept_fmg_pjd_mz)。默认值为0。![img](imgs/icon-Edge-20200310111145515.png)在Data Collector Edge管道中不可用。 |
   | 跑步者空闲时间（秒）                                         | 管道运行器在空闲之前在生成空批次之前等待的最小秒数。管道运行器生成的空批次数在监视方式运行时统计信息中显示为“空闲批次计数”。用于确保定期生成批处理，即使不需要处理任何数据也是如此。使用-1可使管道运行器在空闲时无限期等待，而不会生成空批。仅适用于独立管道。![img](imgs/icon-Edge-20200310111145515.png)在Data Collector Edge管道中不可用。 |
   | 创建故障快照                                                 | 如果管道由于与数据相关的错误而失败，则自动创建快照。可用于对管道进行故障排除。![img](imgs/icon-Edge-20200310111145515.png)在Data Collector Edge管道中不可用。 |

7. 要定义运行时参数，请在“ **参数”**选项卡上，单击“ **添加”**图标，然后为每个参数定义名称和默认值。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加参数。

   有关更多信息，请参见[使用运行时参数](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_rjh_ntz_qr)。

8. 要基于管道状态的更改来配置通知，请在“ **通知”**选项卡上配置以下属性：

   ![img](imgs/icon-Edge-20200310111145515.png)在Data Collector Edge管道中不可用。

   | 通知属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [通知管道状态更改](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/Notifications.html#concept_mtn_k4j_rz) | 当管道遇到列出的管道状态时发送通知。                         |
   | 电邮编号                                                     | 当管道状态更改为指定状态之一时，用于接收通知的电子邮件地址。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以添加其他地址。 |
   | [网络挂钩](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/Webhooks.html#concept_mp1_t3l_rz) | 当管道状态更改为指定状态之一时发送的Webhook。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以添加其他webhooks。 |
   | Webhook URL                                                  | 发送HTTP请求的URL。                                          |
   | 标头                                                         | 可选的HTTP请求标头。                                         |
   | HTTP方法                                                     | HTTP方法。使用以下方法之一：得到放开机自检删除头             |
   | [有效载荷](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/Webhooks.html#concept_rby_1rl_rz) | 要使用的可选有效负载。可用于PUT，POST和DELETE方法。使用任何有效的内容类型。您可以在有效负载中使用webhook参数来包含有关触发事件的信息，例如管道名称或状态。将webhook参数括在双大括号中，如下所示： `{{PIPELINE_STATE}}`。 |
   | 内容类型                                                     | 有效负载的可选内容类型。当请求标头中未声明内容类型时，请配置此属性。 |
   | 认证类型                                                     | 要包括在请求中的可选身份验证类型。使用无，基本，摘要或通用。使用基本进行表单身份验证。 |
   | 用户名                                                       | 使用身份验证时要包括的用户名。                               |
   | 密码                                                         | 使用身份验证时要包括的密码。                                 |

9. 单击“ **错误记录”**选项卡，然后配置以下错误处理选项：

   | 错误记录属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [错误记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_kgc_l4y_5r) | 确定如何处理无法按预期处理的记录。使用以下选项之一：丢弃-丢弃错误记录。将响应发送到源-将错误记录传递回微服务源，以包含在对源REST API客户端的响应中。仅在 [微服务管道中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Microservice/Microservice_Title.html#concept_qfh_xdm_p2b)使用。写入Amazon S3-将错误记录写入Amazon S3。写入另一个管道-将错误记录写入另一个管道。要使用此选项，您需要一个SDC RPC目标管道来处理错误记录。写入Azure事件中心-将错误记录写入指定的Microsoft Azure事件中心。写入Elasticsearch-将错误记录写入指定的Elasticsearch集群。写入文件-将错误记录写入指定目录中的文件。目前，群集模式不支持“写入文件”。写入Google Cloud Storage-将错误记录写入Google Cloud Storage。写入Google Pub / Sub-将错误记录写入Google Pub / Sub。写入Kafka-将错误记录写入指定的Kafka集群。写入Kinesis-将错误记录写入指定的Kinesis流。写入MapR Streams-将错误记录写入指定的MapR Streams集群。写入MQTT-将错误记录写入指定的MQTT代理。![img](imgs/icon-Edge-20200310111145515.png)在Data Collector Edge管道中，可以使用“丢弃”，“写入文件”或“写入MQTT”。 |
   | 错误记录政策                                                 | 确定要用作错误记录基础的记录版本。有关更多信息，请参见[错误记录和版本](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_itr_mzw_j1b)。 |

10. 将错误写入“将响应发送到原始位置”时，可以选择单击“ **错误记录-将响应发送到原始位置”**选项卡并配置以下属性：

    | 发送回复给Origin属性 | 描述                                                         |
    | :------------------- | :----------------------------------------------------------- |
    | 状态码               | 错误记录的HTTP状态代码。默认值为500，表示内部服务器错误。所有错误记录都作为错误记录包含在响应中。 |

11. 将错误记录写入Amazon S3时，单击**错误记录-写入Amazon S3**选项卡并配置以下属性：

    | Amazon S3属性                                                | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [访问密钥ID](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_bmp_zlg_vw) | AWS访问密钥ID。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。 |
    | [秘密访问密钥](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_bmp_zlg_vw) | AWS秘密访问密钥。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。 |
    | 区域                                                         | Amazon S3地区。                                              |
    | 终点                                                         | 当您为区域选择“其他”时要连接的端点。输入端点名称。           |
    | [使用服务器端加密](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_adm_kn1_mw) | 启用服务器端加密。                                           |
    | 服务器端加密选项                                             | Amazon S3用于管理加密密钥的选项：SSE-S3-使用Amazon S3托管密钥。SSE-KMS-使用Amazon Web Services KMS管理的密钥。SSE-C-使用客户提供的密钥。默认值为SSE-S3。 |
    | AWS KMS密钥ARN                                               | AWS KMS主加密密钥的Amazon资源名称（ARN）。使用以下格式：`:::::/`仅用于SSE-KMS加密。 |
    | 加密上下文                                                   | 用于加密上下文的键值对。单击**添加**以添加键值对。仅用于SSE-KMS加密。 |
    | 客户加密密钥                                                 | 要使用的256位和Base64编码的加密密钥。仅用于SSE-C加密。       |
    | 客户加密密钥MD5                                              | 根据RFC 1321，加密密钥的128位和Base64编码的MD5摘要。仅用于SSE-C加密。 |
    | 终点                                                         | 当您为区域选择“其他”时要连接的端点。输入端点名称。           |
    | [桶](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_bnp_gwp_f1b) | 写入记录时要使用的存储桶。输入存储桶名称或定义一个计算结果为存储桶名称的表达式。在表达式中使用datetime变量时，请确保为该阶段配置时间基准。 |
    | 通用前缀                                                     | 确定对象写入位置的通用前缀。                                 |
    | 定界符                                                       | Amazon S3用于定义前缀层次结构的定界符。默认为斜杠（/）。     |
    | [分区前缀](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_qw5_gtq_yv) | 可选的分区前缀，用于指定要使用的分区。使用特定的分区前缀或定义一个计算结果为分区前缀的表达式。在表达式中使用datetime变量时，请确保为该阶段配置时间基准。 |
    | [数据时区](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_qtb_njg_vw) | 目标系统的时区。与时间一起使用以解析基于时间的存储桶或分区前缀中的日期时间。 |
    | [时间基础](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_qtb_njg_vw) | 用于写入基于时间的存储桶或分区前缀的时间基准。使用以下表达式之一：`${time:now()}` -将处理时间与指定的数据时区一起用作时间基准。该表达式调用一个字段并解析为日期时间值，例如 `${record:value()}`。使用与记录关联的时间作为时间基础，并针对指定的数据时区进行调整。如果“存储桶”和“分区前缀”属性没有时间分量，则可以忽略此属性。默认值为`${time:now()}`。 |
    | [对象名称前缀](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_lkp_jd3_yv) | 定义目标写入的对象名称的前缀。默认情况下，对象名称的开头`sdc`如下：`sdc--`。 |
    | 对象名称后缀                                                 | 用于对象名称的后缀，例如txt或json。使用目标时，目标会添加一个句点和配置的后缀，如下所示：<对象名称>。<后缀>。您可以在后缀中包含句点，但不要以句点开头。不允许使用正斜杠。不适用于整个文件数据格式。 |
    | 用Gzip压缩                                                   | 在写入Amazon S3之前，使用gzip压缩文件。                      |
    | 连接超时                                                     | 关闭连接之前等待响应的秒数。默认值为10秒。                   |
    | 套接字超时                                                   | 等待查询响应的秒数。                                         |
    | 重试计数                                                     | 重试请求的最大次数。                                         |
    | 使用代理服务器                                               | 指定是否使用代理进行连接。                                   |
    | 代理主机                                                     | 代理主机。                                                   |
    | 代理端口                                                     | 代理端口。                                                   |
    | 代理用户                                                     | 代理凭据的用户名。                                           |
    | 代理密码                                                     | 代理凭证的密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
    | 并行上传的线程池大小                                         | 并行上传的线程池的大小。在写入多个分区并在多个部分中写入大型对象时使用。当写入多个分区时，将此属性设置为要写入的分区数可以提高性能。有关此属性和以下属性的更多信息，请参阅Amazon S3 TransferManager文档。 |
    | 分段上传阈值                                                 | 目标使用分段上传的最小批处理大小（以字节为单位）。           |
    | 最小上传部分大小                                             | 分段上传的最小分段大小（以字节为单位）。                     |

12. 将错误记录写入SDC RPC管道时，请单击“ **错误记录-写入另一个管道”**选项卡并配置以下属性：

    | 写入管道属性                                                 | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [SDC RPC连接](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/SDC_RPCdest.html#concept_icz_wzw_dt) | 目标管道继续处理数据的连接信息。使用以下格式： `:`。对每个目标管道使用单个RPC连接。使用 [简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，根据需要添加其他连接。配置接收数据的SDC RPC源时，请使用端口号。 |
    | SDC RPC ID                                                   | 用户定义的ID，以允许目标将数据传递到SDC RPC源。在所有SDC RPC源中使用此ID来处理来自目标的数据。 |
    | 每批重试                                                     | 目标尝试将批处理写入SDC RPC源的次数。当目标无法在配置的重试次数内写入批次时，它将使批次失败。默认值为3。 |
    | 退避期                                                       | 重试将批处理写入SDC RPC源之前要等待的毫秒数。每次重试后，输入的值将呈指数增加，直到达到5分钟的最大等待时间。例如，如果将退避时间设置为10，则目标将在等待10毫秒后尝试第一次重试，在等待100毫秒后尝试第二次重试，并在等待1000毫秒后尝试第三次重试。设置为0可立即重试。默认值为0。 |
    | 连接超时（毫秒）                                             | 建立与SDC RPC来源的连接的毫秒数。目标根据“每批重试”属性重试连接。默认值为5000毫秒。 |
    | 读取超时（毫秒）                                             | 等待SDC RPC源从批处理中读取数据的毫秒数。目标根据“每批重试次数”属性重试写入。默认值为2000毫秒。 |
    | 使用压缩 [![img](imgs/icon_moreInfo-20200310111145839.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/SDC_RPCdest.html#concept_zdq_rdj_r5) | 使目标能够使用压缩将数据传递到SDC RPC源。默认启用。          |
    | 使用TLS                                                      | 启用TLS的使用。                                              |
    | [信任库文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z) | 信任库文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。默认情况下，不使用任何信任库。 |
    | 信任库类型                                                   | 要使用的信任库的类型。使用以下类型之一：Java密钥库文件（JKS）PKCS＃12（p12文件）默认值为Java密钥库文件（JKS）。 |
    | 信任库密码                                                   | 信任库文件的密码。密码是可选的，但建议使用。**提示：**为了保护敏感信息（如密码），可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
    | 信任库信任算法                                               | 用于管理信任库的算法。默认值为SunX509。                      |
    | 使用默认协议                                                 | 确定要使用的传输层安全性（TLS）协议。默认协议是TLSv1.2。要使用其他协议，请清除此选项。 |
    | [传输协议](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_mvs_cxf_5z) | 要使用的TLS协议。要使用默认TLSv1.2以外的协议，请单击“ **添加”**图标并输入协议名称。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加协议。**注意：**较旧的协议不如TLSv1.2安全。 |
    | [使用默认密码套件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_cwx_dyf_5z) | 对SSL / TLS握手使用默认的密码套件。要使用其他密码套件，请清除此选项。 |
    | 密码套房                                                     | 要使用的密码套件。要使用不属于默认密码集的密码套件，请单击“ **添加”**图标并输入密码套件的名称。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加密码套件。输入要使用的其他密码套件的Java安全套接字扩展（JSSE）名称。 |

13. 将错误记录写入Microsoft Azure事件中心时，请单击“ **错误记录-写入事件中心”**选项卡并配置以下属性：

    | 活动中心属性     | 描述                                                         |
    | :--------------- | :----------------------------------------------------------- |
    | 命名空间名称     | 包含要使用的事件中心的名称空间的名称。                       |
    | 活动中心名称     | 事件中心名称。                                               |
    | 共享访问策略名称 | 与名称空间关联的策略名称。若要检索策略名称，请登录到Azure门户后，导航到您的命名空间和事件中心，然后单击“共享访问策略”以获取策略列表。在适当的时候，您可以使用默认的共享访问密钥策略RootManageSharedAccessKey。 |
    | 连接字符串键     | 与指定的共享访问策略关联的连接字符串键之一。若要检索连接字符串键，请在访问共享访问策略列表后，单击策略名称，然后复制“连接字符串-主键”值。该值通常以“端点”开头。 |

14. 将错误记录写入Elasticsearch时，单击“ **错误记录-写入Elasticsearch”**选项卡并配置以下属性：

    | Elasticsearch属性                                            | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 群集HTTP URI                                                 | 用于连接到集群的HTTP URI。使用以下格式：`:`                  |
    | 其他HTTP参数                                                 | 您想要作为查询字符串参数发送到Elasticsearch的其他HTTP参数。输入Elasticsearch期望的确切参数名称和值。 |
    | 检测群集中的其他节点                                         | 根据配置的集群URI检测集群中的其他节点。选择此属性等效于将client.transport.sniff Elasticsearch属性设置为true。仅在数据收集器与Elasticsearch群集共享同一网络时使用。请勿用于弹性云或Docker群集。 |
    | 使用安全                                                     | 指定是否在Elasticsearch集群上启用安全性。                    |
    | [时间基础](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Elasticsearch.html#concept_dd3_vhk_r5) | 用于写入基于时间的索引的时间基准。使用以下表达式之一：`${time:now()}`-使用处理时间作为时间基准。处理时间是与运行管道的数据收集器相关的时间。该表达式调用一个字段并解析为日期时间值，例如 `${record:value()}`。使用datetime结果作为时间基准。如果Index属性不包含datetime变量，则可以忽略此属性。默认值为`${time:now()}`。 |
    | 数据时区                                                     | 目标系统的时区。用于解析基于时间的索引中的日期时间。         |
    | 指数                                                         | 生成文档的索引。输入索引名称或计算结果为该索引名称的表达式。例如，如果输入`customer`作为索引，则目标将在`customer`索引中写入文档 。如果在表达式中使用datetime变量，请确保适当配置时间基准。有关日期[时间变量的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/DateTimeVariables.html#concept_gh4_qd2_sv)详细信息，请参见[日期时间变量](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/DateTimeVariables.html#concept_gh4_qd2_sv)。 |
    | 制图                                                         | 生成文档的映射类型。输入映射类型，计算结果为该映射类型的表达式或包含该映射类型的字段。例如，如果输入`user`作为映射类型，则目标将使用`user`映射类型写入文档 。 |
    | [文件编号](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Elasticsearch.html#concept_yr2_1tf_z5) | 该表达式的计算结果为生成的文档的ID。当您不指定ID时，Elasticsearch会为每个文档创建一个ID。默认情况下，目的地允许Elasticsearch创建ID。 |
    | 家长编号                                                     | 生成的文档的可选父ID。输入父ID或计算结果为父ID的表达式。用于在同一索引中的文档之间建立父子关系。 |
    | 路由                                                         | 生成的文档的可选定制路由值。输入路由值或计算结果为该路由值的表达式。Elasticsearch根据为文档定义的路由值将文档路由到索引中的特定分片。您可以为每个文档定义一个自定义值。如果您未定义自定义路由值，Elasticsearch将使用父ID（如果已定义）或文档ID作为路由值。 |
    | 数据字符集                                                   | 要处理的数据的字符编码。                                     |

    如果启用了安全性，请配置以下安全性属性：

    | 担保财产        | 描述                                                         |
    | :-------------- | :----------------------------------------------------------- |
    | 模式            | 使用的身份验证方法：基本-使用Elasticsearch用户名和密码进行身份验证。为Amazon Elasticsearch Service之外的Elasticsearch集群选择此选项。AWS Signature V4-向AWS进行身份验证。为Amazon Elasticsearch Service中的Elasticsearch集群选择此选项。 |
    | 安全用户名/密码 | Elasticsearch用户名和密码。使用以下语法输入用户名和密码：`:`使用基本身份验证时可用。 |
    | 区域            | 托管Elasticsearch域的Amazon Web Services区域。使用AWS Signature V4身份验证时可用。 |
    | 访问密钥ID      | AWS访问密钥ID。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。使用AWS Signature V4身份验证时可用。 |
    | 秘密访问密钥    | AWS秘密访问密钥。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。使用AWS Signature V4身份验证时可用。 |
    | SSL信任库路径   | 信任库文件的位置。配置此属性等效于配置shield.ssl.truststore.path Elasticsearch属性。对于弹性云集群而言不是必需的。 |
    | SSL信任库密码   | 信任库文件的密码。配置此属性等效于配置shield.ssl.truststore.password Elasticsearch属性。对于弹性云集群而言不是必需的。 |

15. 将错误记录写入文件时，请单击“ **错误记录-写入文件”**选项卡并配置以下属性：

    | 写入文件属性       | 描述                                                         |
    | :----------------- | :----------------------------------------------------------- |
    | 目录               | 错误记录文件的本地目录。                                     |
    | 文件前缀           | 用于错误记录文件的前缀。用于将错误记录文件与目录中的其他文件区分开。默认情况下使用前缀sdc-$ {sdc：id（）}。前缀的计算结果为sdc- < 数据收集器 ID>。如果多个Data Collector写入同一目录，则这提供了默认区分。该数据采集器 ID存储在以下文件： $ SDC_DATA / sdc.id文件。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。 |
    | 文件等待时间（秒） | Data Collector等待错误记录的秒数。在那之后，它将创建一个新的错误记录文件。您可以输入秒数或使用默认表达式输入以分钟为单位的时间。 |
    | 档案大小上限（MB） | 错误文件的最大大小。超过此大小将创建一个新的错误文件。使用0避免使用此属性。 |

16. 将错误记录写入Google Cloud Storage时，请单击“ **错误记录-写入Google Cloud Storage”**选项卡并配置以下属性：

    | Google Cloud Storage资源                                     | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 专案编号                                                     | 要连接的项目ID。                                             |
    | 桶                                                           | 写入记录时要使用的存储桶。**注意：**存储桶名称必须符合DNS。有关存储区命名约定的更多信息，请参阅[Google Cloud Storage文档](https://cloud.google.com/storage/docs/naming)。 |
    | [凭证提供者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/GCS.html#concept_otx_vxn_sbb) | 用于连接的凭据提供者：默认凭证提供者服务帐户凭证文件（JSON） |
    | 凭证文件路径（JSON）                                         | 使用Google Cloud服务帐户凭据文件时，原始文件用于连接到Google Cloud Storage的文件的路径。凭证文件必须是JSON文件。输入相对于Data Collector资源目录`$SDC_RESOURCES`的路径，或输入绝对路径。 |
    | 通用前缀                                                     | 确定对象写入位置的通用前缀。                                 |
    | [分区前缀](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/GCS.html#concept_qsx_ryn_sbb) | 可选的分区前缀，用于指定要使用的分区。使用特定的分区前缀或定义一个计算结果为分区前缀的表达式。在表达式中使用datetime变量时，请确保为该阶段配置时间基准。 |
    | 数据时区                                                     | 目标系统的时区。用于解析基于时间的分区前缀中的日期时间。     |
    | [时间基础](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/GCS.html#concept_ffn_tc4_sbb) | 用于写入基于时间的存储桶或分区前缀的时间基准。使用以下表达式之一：`${time:now()}` -将处理时间与指定的数据时区一起用作时间基准。该表达式调用一个字段并解析为日期时间值，例如 `${record:value()}`。使用与记录关联的时间作为时间基础，并针对指定的数据时区进行调整。如果“分区前缀”属性没有时间分量，则可以忽略此属性。默认值为`${time:now()}`。 |
    | [对象名称前缀](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/GCS.html#concept_s1m_j24_sbb) | 定义目标写入的对象名称的前缀。默认情况下，对象名称以“ sdc”开头，如下所示：`sdc-`。整个文件数据格式不是必需的。 |

17. 将错误记录写入Google Pub / Sub时，请单击“ **错误记录-写入Google Pub / Sub”**选项卡并配置以下属性：

    | Google Pub / Sub属性                                         | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 主题编号                                                     | Google Pub / Sub主题ID，可向其中写入消息。                   |
    | 专案编号                                                     | 要连接的Google Pub / Sub项目ID。                             |
    | [凭证提供者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/PubSubPublisher.html#concept_snf_1wq_v1b) | 用于连接到Google Pub / Sub的凭据提供者：默认凭证提供者服务帐户凭证文件（JSON） |
    | 凭证文件路径（JSON）                                         | 使用Google Cloud服务帐户凭据文件时，该路径是目标用来连接到Google Pub / Sub的文件的路径。凭证文件必须是JSON文件。输入相对于Data Collector资源目录`$SDC_RESOURCES`的路径，或输入绝对路径。 |

18. 将错误记录写入Kafka时，请单击“ **错误记录-写入Kafka”**选项卡并配置以下属性：

    | 写给卡夫卡房地产                                             | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 经纪人URI                                                    | Kafka代理的连接字符串。使用以下格式： `:`。要确保连接，请输入其他代理URI的逗号分隔列表。 |
    | [运行时主题解析](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_ok1_cwr_xr) | 在运行时评估表达式，以确定每个记录要使用的主题。             |
    | 主题表达                                                     | 该表达式用于确定使用运行时主题解析时每个记录的写入位置。使用计算结果为主题名称的表达式。 |
    | 主题白名单                                                   | 使用运行时主题解析时要写入的有效主题名称的列表。用于避免写入无效的主题。解析为无效主题名称的记录将传递到阶段以进行错误处理。使用星号（*）允许写入任何主题名称。默认情况下，所有主题名称均有效。 |
    | 话题                                                         | 要使用的主题。使用运行时主题解析时不可用。                   |
    | [分区策略](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_qpm_xp4_4r) | 用于写入分区的策略：Round Robin-轮流写入不同的分区。随机-随机写入分区。表达式-使用表达式将数据写入不同的分区。将记录写到表达式结果指定的分区中。默认-使用表达式从记录中提取分区键。根据分区键的哈希将记录写入分区。 |
    | [分区表达](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_qpm_xp4_4r) | 与默认或表达式分区策略一起使用的表达式。使用默认分区策略时，请指定一个表达式，该表达式从记录中返回分区键。该表达式必须计算为字符串值。使用表达式分区策略时，请指定一个表达式，该表达式的计算结果为您希望将每个记录写入的分区。分区号以0开头。表达式必须计算为数值。（可选）单击**Ctrl +空格键**以帮助创建表达式。 |
    | 每批一封邮件                                                 | 对于每个批次，将记录作为一条消息写入每个分区。               |
    | Kafka配置                                                    | 要使用的其他Kafka属性。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标，然后定义Kafka属性名称和值。使用Kafka期望的属性名称和值。不要使用broker.list属性。有关启用与Kafka的安全连接的信息，请参阅[启用安全性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_znr_b3c_rw)。 |

19. 将错误记录写入Kinesis时，单击“ **错误记录-写入Kinesis”**选项卡并配置以下属性：

    | Kinesis属性                                                  | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [访问密钥ID](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KinProducer.html#concept_bpp_54g_vw) | AWS访问密钥ID。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。 |
    | [秘密访问密钥](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KinProducer.html#concept_bpp_54g_vw) | AWS秘密访问密钥。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。 |
    | 区域                                                         | 托管Kinesis集群的Amazon Web Services地区。                   |
    | 终点                                                         | 当您为区域选择“其他”时要连接的端点。输入端点名称。           |
    | 流名称                                                       | Kinesis流名称。                                              |
    | 分区策略                                                     | 将数据写入Kinesis分片的策略：随机-生成随机分区密钥。表达式-使用表达式的结果作为分区键。 |
    | 分区表达                                                     | 用于生成用于将数据传递到不同分片的分区键的表达式。用于表达式分区策略。 |
    | Kinesis生产者配置                                            | 其他Kinesis属性。添加配置属性时，输入确切的属性名称和值。Kinesis生产者不验证属性名称或值。 |
    | 保留记录顺序                                                 | 选择以保留记录顺序。启用此选项可能会降低管道性能。           |

20. 将错误记录写入MapR Streams群集时，请单击“ **错误记录-写入MapR Streams”**选项卡并配置以下属性：

    | MapR Streams生产者属性                                       | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | [运行时主题解析](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_ok1_cwr_xr) | 在运行时评估表达式，以确定每个记录要使用的主题。             |
    | 话题                                                         | 要使用的主题。使用运行时主题解析时不可用。                   |
    | 主题表达                                                     | 该表达式用于确定使用运行时主题解析时每个记录的写入位置。使用计算结果为主题名称的表达式。 |
    | 主题白名单                                                   | 使用运行时主题解析时要写入的有效主题名称的列表。用于避免写入无效的主题。解析为无效主题名称的记录将传递到阶段以进行错误处理。使用星号（*）允许写入任何主题名称。默认情况下，所有主题名称均有效。 |
    | [分区策略](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_qpm_xp4_4r) | 用于写入分区的策略：Round Robin-轮流写入不同的分区。随机-随机写入分区。表达式-使用表达式将数据写入不同的分区。将记录写到表达式结果指定的分区中。默认-使用表达式从记录中提取分区键。根据分区键的哈希将记录写入分区。 |
    | [分区表达](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_qpm_xp4_4r) | 与默认或表达式分区策略一起使用的表达式。使用默认分区策略时，请指定一个表达式，该表达式从记录中返回分区键。该表达式必须计算为字符串值。使用表达式分区策略时，请指定一个表达式，该表达式的计算结果为您希望将每个记录写入的分区。分区号以0开头。表达式必须计算为数值。（可选）单击**Ctrl +空格键**以帮助创建表达式。 |
    | 每批一封邮件                                                 | 对于每个批次，将记录作为一条消息写入每个分区。               |
    | [MapR流配置](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MapRStreamsProd.html#concept_lzy_xlg_2v) | 要使用的其他配置属性。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标，然后定义MapR Streams属性名称和值。使用MapR期望的属性名称和值。您可以使用MapR Streams属性和MapR Streams支持的Kafka属性集。 |

21. 将错误记录写入MQTT代理时，单击“ **错误记录-写入MQTT”**选项卡并配置以下属性：

    | MQTT属性                                                     | 描述                                                         |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 经纪人网址                                                   | MQTT代理URL。输入以下格式：`://:`使用ssl与代理进行安全连接。例如：`tcp://localhost:1883` |
    | 客户编号                                                     | MQTT客户端ID。该ID在连接到同一代理的所有客户端上必须是唯一的。您可以定义一个计算结果为客户端ID的表达式。例如，输入以下表达式以使用唯一的管道ID作为客户端ID：`${pipeline:id()}`如果管道包含多个MQTT阶段，并且您想将唯一的管道ID用作两个阶段的客户机ID，请在客户机ID前面加上以下字符串：`sub-${pipeline:id()} and pub-${pipeline:id()} `否则，所有阶段将使用相同的客户端ID。这可能会导致问题，例如消息消失。 |
    | [话题](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MQTTPublisher.html#concept_bbq_w5q_mz) | 要发布到的主题。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击**添加**图标以阅读其他主题。 |
    | 服务质量                                                     | 确定用于保证消息传递的服务质量级别：最多一次（0）至少一次（1）恰好一次（2）有关更多信息，请参阅[有关服务质量级别](http://www.hivemq.com/blog/mqtt-essentials-part-6-mqtt-quality-of-service-levels)的[HiveMQ文档](http://www.hivemq.com/blog/mqtt-essentials-part-6-mqtt-quality-of-service-levels)。 |
    | 客户持久化机制                                               | 确定服务质量级别至少一次或恰好一次时，目标用来保证消息传递的持久性机制。选择以下选项之一：内存-将消息存储在Data Collector计算机的内存中，直到完成消息传递为止。文件-将消息存储在Data Collector计算机上的本地文件中，直到完成消息传递为止。当服务质量级别最多为一次时不使用。有关更多信息，请参阅[有关客户端持久性](http://www.hivemq.com/blog/mqtt-essentials-part-7-persistent-session-queuing-messages)的[HiveMQ文档](http://www.hivemq.com/blog/mqtt-essentials-part-7-persistent-session-queuing-messages)。 |
    | 客户端持久性数据目录                                         | 配置文件持久性时，Data Collector计算机上的本地目录，目标将文件中的消息临时存储在该目录中。启动Data Collector的用户必须具有对该目录的读写权限。 |
    | 保持活动间隔（秒）                                           | 允许与MQTT代理的连接保持空闲状态的最长时间（以秒为单位）。在此时间段内目标未发布任何消息后，连接将关闭。目标必须重新连接到MQTT代理。默认值为60秒。 |
    | 使用凭证                                                     | 启用输入MQTT凭证。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
    | 用户名                                                       | MQTT用户名。                                                 |
    | 密码                                                         | MQTT密码。                                                   |
    | 保留留言                                                     | 确定在没有订阅任何MQTT客户端收听主题时，MQTT代理是否保留目标最后发布的消息。选中后，MQTT代理将保留目标发布的最后一条消息。先前发布的所有消息都将丢失。清除后，目的地发布的所有消息都会丢失。有关MQTT保留消息的更多信息，请参见http://www.hivemq.com/blog/mqtt-essentials-part-8-retained-messages。 |
    | 使用TLS                                                      | 启用TLS的使用。                                              |
    | [信任库文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z) | 信任库文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。默认情况下，不使用任何信任库。 |
    | 信任库类型                                                   | 要使用的信任库的类型。使用以下类型之一：Java密钥库文件（JKS）PKCS＃12（p12文件）默认值为Java密钥库文件（JKS）。 |
    | 信任库密码                                                   | 信任库文件的密码。密码是可选的，但建议使用。**提示：**为了保护敏感信息（如密码），可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
    | 信任库信任算法                                               | 用于管理信任库的算法。默认值为SunX509。                      |
    | 使用默认协议                                                 | 确定要使用的传输层安全性（TLS）协议。默认协议是TLSv1.2。要使用其他协议，请清除此选项。 |
    | [传输协议](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_mvs_cxf_5z) | 要使用的TLS协议。要使用默认TLSv1.2以外的协议，请单击“ **添加”**图标并输入协议名称。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加协议。**注意：**较旧的协议不如TLSv1.2安全。 |
    | [使用默认密码套件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_cwx_dyf_5z) | 对SSL / TLS握手使用默认的密码套件。要使用其他密码套件，请清除此选项。 |
    | 密码套房                                                     | 要使用的密码套件。要使用不属于默认密码集的密码套件，请单击“ **添加”**图标并输入密码套件的名称。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来添加密码套件。输入要使用的其他密码套件的Java安全套接字扩展（JSSE）名称。 |

22. 使用群集批处理或流执行模式时，请单击“ **群集”**选项卡并配置群集属性。

    有关配置集群模式管道的信息，请参阅[集群批处理和流执行模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/ClusterPipelines.html#concept_rjc_4m5_lx)。

23. 使用集群EMR批处理执行模式时，单击 **EMR**选项卡并配置在Amazon EMR集群上运行管道所需的属性。

    有关配置群集EMR批处理模式管道以处理Amazon S3中的数据的信息，请参阅[Amazon S3要求](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/AmazonS3Requirements.html#concept_opj_jmf_f2b)。

24. 在“ **统计信息”**选项卡上配置管道以聚合统计信息。

    有关Control Hub聚合统计信息的信息，请参见[管道统计信息](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/DPM/AggregatedStatistics.html#concept_h2q_mb5_xw)。

25. 要配置测试原点，请在“ **测试原点”**选项卡上配置原点属性。

    所有原点属性都显示在“测试原点”选项卡上。

    详细的配置信息为特定原点，请参阅“配置<原点型>起源” [起源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_hpr_twm_jq) 章。

    若要使用其他测试原点，请在“ **常规”**选项卡的“ **测试原点”**属性中 选择要使用的原点 。

26. 如果使用管道的开始或停止事件，请在**<事件类型>-<事件消费者>**选项卡上配置相关的事件消费者属性。

    事件使用者的所有属性都显示在选项卡上。

    详细的配置信息为特定执行器，请参阅“配置<执行人类型>执行人” [执行人](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Executors-overview.html#concept_stt_2lk_fx)章。

    有关写入另一个管道的详细信息，请参阅“ [配置SDC RPC目标”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/SDC_RPCdest.html#task_nbl_r2x_dt__step-SDCRPCdesttab)。

    若要使用其他事件使用者，请在“ **常规”**选项卡上的“ **开始事件”**或“ **停止事件”**属性中 选择要使用的使用者。

27. 使用“舞台库”面板添加原始舞台。在“属性”面板中，配置舞台属性。

    或者，要使用包含原点的管道片段，请使用“舞台库”面板添加片段。

    有关原始阶段的配置详细信息，请参见[Origins](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_hpr_twm_jq)。

    有关管道片段的更多信息，请参见[管道片段](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Fragments/PipelineFragments.html#concept_msg_4hf_ndb)。

28. 使用“舞台库”面板添加要使用的下一个舞台，将原点连接到新舞台，然后配置新舞台。

    有关处理器的配置详细信息，请参阅[处理器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Processors_overview.html#concept_hpr_twm_jq)。

    有关目标的配置详细信息，请参阅[目标](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Destinations_overview.html#concept_hpr_twm_jq)。

    有关执行程序的配置详细信息，请参见[执行程序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Executors-overview.html#concept_stt_2lk_fx)。

    有关管道片段的更多信息，请参见[管道片段](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Fragments/PipelineFragments.html#concept_msg_4hf_ndb)。

29. 根据需要添加其他阶段。

30. 您可以随时使用“ **预览”**图标预览数据，以帮助配置管道。有关更多信息，请参见[数据预览概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Preview/DataPreview_Title.html#concept_jtn_s3m_lq)。

31. （可选）您可以创建指标或数据警报以跟踪有关管道运行的详细信息并创建阈值警报。有关更多信息，请参阅[规则和警报](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Alerts/RulesAlerts_title.html#concept_pgk_brx_rr)。

    ![img](imgs/icon-Edge-20200310111145515.png)在Data Collector Edge管道中无效。 Data Collector Edge管道不支持规则或警报。

32. 验证并完成管道后，可以使用“ **发布管道”**图标来发布管道，然后使用“ **创建作业”**图标来创建作业。