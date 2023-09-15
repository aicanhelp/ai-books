# 设计边缘管道

边缘管道以边缘执行模式运行。您可以在Data Collector 或Control Hub Pipeline Designer中设计边缘管道。

设计边缘管道之后，将已发布的管道添加到Control Hub 作业，然后在已注册的SDC Edge上运行这些作业。使用边缘管道启动作业时，请确保在启动发送管道之前先启动接收管道。

您可以设计以下类型的边缘管道：

- 边缘发送管道

  边缘发送管道使用特定于边缘设备的起源来读取驻留在该设备上的本地数据。在将数据发送到数据收集器接收管道之前，管道可以对数据执行最少的处理。

  （可选）您还可以设计边缘发送管道以监视正在处理的数据，然后将数据发送到在同一SDC Edge上运行的边缘接收管道。边缘接收管道对数据起作用以控制边缘设备。

- 边缘接收管道

  边缘接收管道侦听在Data Collector或SDC Edge上运行的另一个管道发送的 数据，然后对该数据进行操作以控制边缘设备。

  边缘接收管道包括要从发送数据的管道中的目标读取的相应起点。例如，如果发送管道写入HTTP客户端目标，则边缘接收管道将使用HTTP Server源读取数据。

边缘管道支持有限数量的起点，处理器，目的地，错误记录处理选项和数据格式。边缘管道当前不支持任何执行程序。

## 起源

边缘管道支持有限数量的起点。

原点在边缘管道中的功能与在其他管道中的功能相同。但是，如下所述，某些起源在边缘管道中有局限性。另外，边缘管线中的阶段支持有限数量的 [数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Edge_Mode/EdgePipelineTypes.html#concept_i32_2vf_pbb)。此外，边缘管道中的源可以处理未压缩或压缩的文件，但不能处理存档或压缩的存档文件。

边缘管道支持以下来源：

| 支持的来源                                                   | 局限性                                                       |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [开发数据生成器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DevStages.html#concept_czx_ktn_ht) | 没有                                                         |
| [开发随机记录源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DevStages.html#concept_czx_ktn_ht) | 没有                                                         |
| [开发原始数据源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DevStages.html#concept_czx_ktn_ht) | 没有                                                         |
| [目录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_qcq_54n_jq) | 边缘管道不支持多线程处理。在边缘管道中，目录原点始终创建单个线程来读取文件，即使您将其配置为使用多个线程也是如此。 |
| [文件尾](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/FileTail.html#concept_n1y_qyp_5q) | 在边缘管道中，“文件尾”源可以为存档文件使用以下命名约定：带有反向计数器的活动文件匹配模式的文件如果将源配置为使用其他活动文件命名约定，则源将使用带有反向计数器的活动文件约定。 |
| [gRPC客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/gRPCClient.html#concept_yp1_4zs_yfb) | 没有                                                         |
| [HTTP客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HTTPClient.html#concept_wk4_bjz_5r) | 在边缘管道中，HTTP客户端源不支持批处理模式，分页或OAuth2授权。 |
| [HTTP服务器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HTTPServer.html#concept_s2p_5hb_4y) | 边缘管道不支持多线程处理。在边缘管道中，HTTP Server源始终创建一个线程来读取文件，即使您将其配置为使用多个线程也是如此。 |
| [MQTT订户](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MQTTSubscriber.html#concept_ukz_3vt_lz) | 使用MQTT阶段的边缘管道需要使用中间MQTT代理。例如，边缘发送管道使用MQTT发布器目标写入MQTT代理。MQTT代理临时存储数据，直到Data Collector接收管道中的MQTT订阅服务器源读取数据为止。 |
| [传感器读取器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DevStages.html#concept_czx_ktn_ht) | 没有                                                         |
| [系统指标](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SystemMetrics.html#concept_gzy_gmv_32b) | 没有                                                         |
| [WebSocket客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/WebSocketClient.html#concept_unk_nzk_fbb) | 没有                                                         |
| [Windows事件日志](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/WindowsLog.html#concept_agf_5jv_sbb) | 没有                                                         |

## 处理器

边缘管道支持有限数量的处理器。处理器在边缘管道中的功能与在其他管道中的相同。但是，某些处理器在边缘流水线中有局限性，如下所述。

边缘管道支持以下处理器：

| 支持的处理器                                                 | 局限性                                                       |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [延迟](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Delay.html#concept_ez5_pvf_wbb) | 没有                                                         |
| [开发人员身份](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DevStages.html#concept_czx_ktn_ht) | 没有                                                         |
| [开发随机误差](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DevStages.html#concept_czx_ktn_ht) | 没有                                                         |
| [表达评估器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Expression.html#concept_zm2_pp3_wq) | 没有                                                         |
| [场去除剂](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldRemover.html#concept_jdd_blr_wq) | 没有                                                         |
| [JavaScript评估器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/JavaScript.html#concept_n2p_jgf_lr) | 在边缘管道中，JavaScript Evaluator处理器不支持sdcFunctions脚本对象。 |
| [流选择器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/StreamSelector.html#concept_tqv_t5r_wq) | 没有                                                         |
| [TensorFlow评估器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/TensorFlow.html#concept_otg_csh_z2b) | 在边缘管道中，TensorFlow评估程序处理器可以评估每条记录。它无法评估整个批次。 |

## 目的地

边缘管道支持数量有限的目的地。

目的地在边缘管道中的功能与在其他管道中的相同。但是，某些目的地在边缘管道方面有局限性，如下所述。另外，边缘管线中的阶段支持有限数量的 [数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Edge_Mode/EdgePipelineTypes.html#concept_i32_2vf_pbb)。

边缘管道支持以下目标：

| 目的地                                                       | 局限性                                                       |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [亚马逊S3](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_avx_bnq_rt) | 在边缘管道中，Amazon S3目标在流传输整个文件后不会生成事件记录。 |
| [CoAP客户](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/CoAPClient.html#concept_hw5_s3n_sz) | 没有                                                         |
| [HTTP客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/HTTPClient.html#concept_khl_sg5_lz) | 在边缘管道中，HTTP客户端目标不支持OAuth 2身份验证。          |
| [卡夫卡制片人](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_oq2_5jl_zq) | 在边缘管道中，Kafka Producer目标不支持Kerberos身份验证来连接到Kafka。 |
| [Kinesis Firehose](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KinFirehose.html#concept_bjv_dpk_kv) | 没有                                                         |
| [动因制片人](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KinProducer.html#concept_swk_h1j_yr) | 没有                                                         |
| [MQTT发布者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MQTTPublisher.html#concept_odz_txt_lz) | 使用MQTT阶段的边缘管道需要使用中间MQTT代理。例如，边缘发送管道使用MQTT发布器目标写入MQTT代理。MQTT代理临时存储数据，直到Data Collector接收管道中的MQTT订阅服务器源读取数据为止。 |
| [错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/ToError.html#concept_ryn_v3z_lr) | 没有                                                         |
| [前往赛事](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DevStages.html#concept_czx_ktn_ht) | 没有                                                         |
| [垃圾](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Trash.html#concept_htf_ydj_wq) | 没有                                                         |
| [WebSocket客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/WebSocketClient.html#concept_l4d_mjn_lz) | 没有                                                         |

## 错误记录处理

您可以为边缘管道配置以下错误记录处理选项：

- 丢弃

  管道丢弃记录。

- 写入文件

  管道将错误记录和相关详细信息写入边缘设备上的本地目录。创建具有Directory原点的另一个边缘管道，以处理写入文件的错误记录。

- 写入MQTT

  管道将错误记录和相关详细信息发布到MQTT代理上的主题。使用MQTT订阅服务器源创建另一个边缘或独立的Data Collector管道，以处理发布到代理的错误记录。

## 资料格式

边缘管道中包含的阶段可以处理有限数量的数据格式。

边缘管道中包含的源可以处理以下数据格式：

- 二元
- 定界
- JSON格式
- SDC记录
- 文本
- 整个档案

边缘管道中的源只能处理未压缩或压缩的文件，不能处理存档或压缩的存档文件。

边缘管道中包含的目标可以处理以下数据格式：

- 二元
- JSON格式
- SDC记录
- 文本
- 整个档案

配置相应的阶段以使用相同的数据格式。例如，如果边缘发送管道中的MQTT发布者目标使用JSON数据格式，则在Data Collector 接收管道中将MQTT订阅服务器源配置为也使用JSON数据格式。

## 边缘管道限制

边缘管道在SDC Edge上运行，SDC Edge是没有UI的轻量级代理。结果，一些可用于独立管道的功能目前不适用于边缘管道。在将来的版本中，我们将为边缘管道中的某些功能提供支持。

请注意边缘管道的以下限制：

- 边缘管道中的源只能处理二进制，定界，JSON，SDC记录，文本和整个文件数据格式。
- 边缘管道中的源可以处理未压缩或压缩的文件，但不能处理存档或压缩的存档文件。
- 边缘管道中的目标只能处理Binary，JSON，SDC Record，Text和Whole File数据格式。
- 为SSL / TLS启用边缘管道中的阶段时，密钥库和信任库文件必须使用PEM格式。
- 边缘管道无法发送电子邮件和Webhook通知。
- 边缘管道中不使用规则和警报。
- 您无法将边缘管道配置为重试错误。
- 您无法为边缘管道配置管道内存或速率限制。
- 边缘管道仅在StreamSets表达式语言中支持以下功能：
  - 所有作业功能。
  - 所有管道功能。
  - 该`sdc:hostname()`函数返回边缘设备的主机名。
  - 有限数量的记录，数学和字符串函数。
- 接收数据流触发器时，边缘管道不支持使用执行程序阶段执行任务。
- 边缘管道不支持多线程处理。
- 您无法捕获边缘管道的快照。
- 边缘管道只能将统计信息直接写入Control Hub。结果，Control Hub无法显示在多个SDC Edge实例上运行的作业的汇总统计信息 。监视作业时，可以分别查看每个远程管道实例的统计信息。