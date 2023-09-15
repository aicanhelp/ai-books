# 错误记录处理

您可以在阶段级别和管道级别配置错误记录处理。您还可以指定记录的版本，以用作错误记录的基础。

当阶段在处理记录时发生错误时，Data Collector将根据阶段配置来处理记录。阶段选项之一是将记录传递到管道以进行错误处理。对于此选项，Data Collector基于管道错误记录处理配置来处理记录。

配置管道时，请注意阶段错误处理优先于管道错误处理。即，可以将管道配置为将错误记录写入文件，但是如果将阶段配置为丢弃错误记录，则将丢弃这些记录。您可能会使用此功能来减少为检查和重新处理而保存的错误记录的类型。

请注意，缺少必填字段的记录不会进入阶段。它们直接传递到管道以进行错误处理。

## 管道错误记录处理

管道错误记录处理确定了Data Collector如何处理阶段发送到管道以进行错误处理的错误记录。它还处理故意从管道中删除的记录，例如没有必填字段的记录。

管道根据“错误记录”选项卡上的“错误记录”属性处理错误记录。当Data Collector 遇到意外错误时，它将停止管道并记录该错误。

管道提供以下错误记录处理选项：

- 丢弃

  管道丢弃记录。数据收集器 包括错误记录计数和指标中的记录。

- 发送回复到起源

  管道将错误记录传回微服务源，以包含在对源REST API客户端的响应中。数据收集器 包括错误记录计数和指标中的记录。仅在[微服务管道中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Microservice/Microservice_Title.html#concept_qfh_xdm_p2b)使用。

  ![img](imgs/icon-Edge.png)在Data Collector Edge管道中无效。

- 写入Amazon S3

  管道将错误记录和相关详细信息写入Amazon S3。数据收集器 包括错误记录计数和指标中的记录。您定义Amazon S3配置属性。![img](imgs/icon-Edge.png)在Data Collector Edge管道中无效。

- 写到另一个管道

  管道将错误记录写入SDC RPC管道。数据收集器 包括错误记录计数和指标中的记录。

  当您写入另一个管道时，Data Collector 有效创建一个SDC RPC原始管道，以将错误记录传递到另一个管道。

  您需要创建一个SDC RPC目标管道来处理错误记录。管道必须包括SDC RPC源，该源配置为从该管道读取错误记录。

  有关SDC RPC管道的更多信息，请参见[SDC RPC管道概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/RPC_Pipelines/SDC_RPCpipelines_title.html#concept_lnh_z3z_bt)。

  ![img](imgs/icon-Edge.png)在Data Collector Edge管道中无效。

- 写入Azure事件中心

  管道将错误记录和相关详细信息写入Microsoft Azure事件中心。 数据收集器 包括错误记录计数和指标中的记录。

  您定义要使用的Azure事件中心的配置属性。

  ![img](imgs/icon-Edge.png)在Data Collector Edge管道中无效。

- 写信给Elasticsearch

  管道将错误记录和相关详细信息写入Elasticsearch。数据收集器 包括错误记录计数和指标中的记录。

  您定义要使用的Elasticsearch集群的配置属性。

  ![img](imgs/icon-Edge.png)在Data Collector Edge管道中无效。

- 写入文件

  管道将错误记录和相关详细信息写入本地目录。数据收集器 包括错误记录计数和指标中的记录。

  您定义要使用的目录和最大文件大小。错误文件是根据文件前缀管道属性命名的。

  目前群集管道不支持写入文件。

- 写入Google云端存储

  管道将错误记录和相关详细信息写入Google Cloud Storage。数据收集器 包括错误记录计数和指标中的记录。

  您定义Google Cloud Storage配置属性。

  ![img](imgs/icon-Edge.png)在Data Collector Edge管道中无效。

- 写入Google Pub / Sub

  管道将错误记录和相关详细信息写入Google Pub / Sub。数据收集器 包括错误记录计数和指标中的记录。

  您定义Google Pub / Sub配置属性。

  ![img](imgs/icon-Edge.png)在Data Collector Edge管道中无效。

- 写给卡夫卡

  管道将错误记录和相关详细信息写入Kafka。数据收集器 包括错误记录计数和指标中的记录。

  您定义要使用的Kafka群集的配置属性。

  ![img](imgs/icon-Edge.png)在Data Collector Edge管道中无效。

- 写给Kinesis

  管道将错误记录和相关详细信息写入Amazon Kinesis Streams。数据收集器 包括错误记录计数和指标中的记录。

  您可以定义要使用的Kinesis流的配置属性。

  ![img](imgs/icon-Edge.png)在Data Collector Edge管道中无效。

- 写入MapR流

  管道将错误记录和相关详细信息写入MapR流。数据收集器 包括错误记录计数和指标中的记录。

  您定义要使用的MapR Streams集群的配置属性。

  ![img](imgs/icon-Edge.png)在Data Collector Edge管道中无效。

- 写入MQTT

  管道将错误记录和相关详细信息写入MQTT代理。数据收集器 包括错误记录计数和指标中的记录。

  您定义要使用的MQTT代理的配置属性。

## 阶段错误记录处理

大多数阶段包括错误记录处理选项。处理记录时发生错误时，Data Collector将根据阶段的“常规”选项卡上的“记录错误”属性来处理记录。

阶段包括以下错误处理选项：

- 丢弃

  该阶段将静默丢弃该记录。Data Collector不会记录有关错误的信息，也不会记录遇到错误的特定记录。丢弃的记录不包括在错误记录计数或指标中。

- 发送到错误

  该阶段将记录发送到管道以进行错误处理。管道根据管道错误处理配置处理记录。

- 停止管道

  Data Collector 停止管道并记录有关错误的信息。停止管道的错误在管道历史记录中显示为错误。

  目前，群集模式管道不支持停止管道。

## 例

Kafka Consumer起源阶段读取的JSON数据的最大对象长度为4096个字符，并且该阶段遇到一个具有5000个字符的对象。根据阶段配置，Data Collector要么丢弃记录，停止管道，要么将记录传递给管道以进行错误记录处理。

将阶段配置为将记录发送到管道后，根据如何配置管道错误处理，将发生以下情况之一：

- 当管道丢弃错误记录时，Data Collector 会丢弃记录，而无需注意操作或原因。
- 当管道将错误记录写入目标时，Data Collector会将错误记录和其他错误信息写入目标。它还包括计数和度量标准中的错误记录。

## 错误记录和版本

当Data Collector 创建错误记录时，它将保留触发错误的记录中的数据和属性，然后将与错误相关的信息添加为记录头属性。有关错误标题属性和与记录关联的其他内部标题属性的列表，请参见[内部属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_itf_55z_dz)。

配置管道时，可以指定要使用的记录的版本：

- 原始记录-由原点最初生成的记录。当您想要原始记录而不进行任何其他管道处理时，请使用此记录。

- 当前记录-产生错误的阶段中的记录。根据发生的错误的类型，该记录可以在错误生成阶段进行不处理或部分处理。

  当您想要保留记录导致错误之前管道完成的任何处理时，请使用此记录。