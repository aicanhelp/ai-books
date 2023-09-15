# 集群管道概述

一组管道是在执行集群模式下运行的管道。您可以在独立执行模式或集群执行模式下运行管道。

在独立模式下，单个Data Collector进程运行管道。默认情况下，管道以独立模式运行。

在集群模式下，Data Collector使用集群管理器和集群应用程序根据需要生成其他工作程序。使用集群模式可从Kafka集群，MapR集群，HDFS或Amazon S3读取数据。

什么时候选择独立模式或集群模式？假设您要从应用程序服务器提取日志并执行计算量大的转换。为此，您可以使用一组独立的管道将日志数据从每个应用程序服务器流式传输到Kafka或MapR集群。然后，使用集群管道处理集群中的数据并执行昂贵的转换。

或者，您可以使用群集模式将数据从HDFS移至另一个目标，例如Elasticsearch。

## 群集批处理和流执行模式

Data Collector 可以使用群集批处理或群集流执行模式运行群集管道。

Data Collector可以使用的执行模式取决于群集管道从中读取的原始系统：

- 卡夫卡集群

  Data Collector可以集群流模式处理来自Kafka集群的数据。在集群流模式下，Data Collector会连续处理数据，直到您停止管道为止。

  Data Collector作为Spark Streaming（一个开源集群计算应用程序）中的应用程序运行。

  Spark Streaming在Mesos或YARN集群管理器上运行，以处理来自Kafka集群的数据。集群管理器和Spark Streaming 为Kafka集群中的每个主题分区生成一个Data Collector worker。结果，每个分区都有一个Data Collector worker处理数据。如果将分区添加到Kafka主题，则必须重新启动管道以使Data Collector能够生成新的工作程序以从新的分区读取。

  当Spark Streaming在YARN上运行时，可以通过配置[Worker Count集群管道属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/KafkaRequirements.html#task_hhk_bfv_cy__Kafka-YARNWorker)来限制产生的worker [数量](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/KafkaRequirements.html#task_hhk_bfv_cy__Kafka-YARNWorker)。您还可以使用Extra Spark Configuration属性将Spark配置传递给spark-submit脚本。另外，您可以在YARN上的群集流传输管道中配置Kafka Consumer来源，以通过SSL / TLS，Kerberos或同时通过两者进行[安全连接](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/KafkaRequirements.html#concept_bb2_m5p_tdb)。使用Kafka Consumer来源以集群流模式处理来自Kafka集群的数据。

- MapR丛集

  Data Collector可以在两种执行模式下处理来自MapR集群的数据：群集批处理模式-在群集批处理模式下，Data Collector处理所有可用数据，然后停止管道。Data Collector作为应用程序在MapReduce（一种开源集群计算框架）之上运行。MapReduce在YARN群集管理器上运行。YARN和MapReduce会根据需要生成其他工作节点。MapReduce为每个MapR FS块创建一个地图任务。使用MapR FS原点以集群批处理模式处理来自MapR的数据。群集流传输模式-在群集流传输模式下，Data Collector会连续处理数据，直到停止管道为止。Data Collector作为Spark Streaming（一个开源集群计算应用程序）中的应用程序运行。Spark Streaming在YARN群集管理器上运行，以处理来自MapR群集的数据。集群管理器和Spark Streaming 为MapR集群中的每个主题分区生成一个Data Collector worker。结果，每个分区都有一个Data Collector worker处理数据。如果将分区添加到MapR主题，则必须重新启动管道以使Data Collector能够生成新的工作程序以从新的分区读取。您可以通过配置“ [工作人员计数”集群管道属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/MapRRequirements.html#task_i3h_q3w_hx__MapR-YARNworker)来限制产生的[工作人员数量](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/MapRRequirements.html#task_i3h_q3w_hx__MapR-YARNworker)。使用MapR Streams使用者来源以群集流模式处理来自MapR群集的数据。

- HDFS

  Data Collector可以集群批处理模式处理HDFS中的数据。在群集批处理模式下，Data Collector处理所有可用数据，然后停止管道。

  Data Collector作为应用程序在MapReduce（一种开源集群计算框架）之上运行。MapReduce在YARN群集管理器上运行。YARN和MapReduce会根据需要生成其他工作节点。MapReduce为每个HDFS块创建一个地图任务。使用Hadoop FS源，以群集批处理模式处理来自HDFS的数据。

- 亚马逊S3

  Data Collector可以以下群集批处理模式处理来自Amazon S3的数据：集群EMR批处理模式-在集群EMR批处理模式下，Data Collector在Amazon EMR集群上运行以处理Amazon S3数据。Data Collector可以在管道启动时配置的现有EMR群集或新的EMR群集上运行。设置新的EMR群集时，可以配置群集是保持活动状态还是在管道停止时终止。集群批处理模式-在集群批处理模式下，Data Collector在Hadoop（CDH）或Hortonworks Data Platform（HDP）集群的Cloudera发行版上运行，以处理Amazon S3数据。

  在这两种模式下，Data Collector都会处理所有可用数据，然后停止管道。

  Data Collector作为应用程序在EMR，CDH或HDP集群中的MapReduce之上运行。MapReduce在YARN群集管理器上运行。MapReduce为每个HDFS块创建一个地图任务。

  使用Hadoop FS源以集群EMR或集群批处理模式处理来自Amazon S3的数据。

## 数据收集器配置

运行集群管道时，除以下属性外，在网关节点上定义的Data Collector 配置文件`$SDC_CONF/sdc.properties`传播到工作节点：

- `sdc.base.http.url`
- `http.bindHost`

如果您修改 网关节点上的`sdc.base.http.url`和`http.bindHost`属性以配置特定的主机名或端口号或配置Data Collector 绑定到的特定IP地址，则修改后的值不会传播到辅助节点。工作节点始终使用`sdc.base.http.url`和 `http.bindHost`属性的默认值，以便工作节点可以动态确定主机名并可以绑定到任何IP地址。

为防止其他配置属性传播到工作节点，请`sdc.properties`在网关节点上的文件中添加以下属性：

```
cluster.slave.configs.remove=<property1>,<property2>
```

有关配置Data Collector 配置文件的更多信息，请参阅[Data Collector配置](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/DCConfig.html#concept_pq5_xjq_kr)。

## 启用HTTPS

您可以在运行集群管道时使Data Collector能够使用HTTPS。默认情况下，Data Collector使用HTTP。

要为群集管道配置HTTPS，首先必须将Data Collector配置为使用HTTPS。然后，为群集中的每个工作节点生成一个SSL / TLS证书。 Data Collector 在集群中的主网关节点上运行，因此网关节点使用为Data Collector配置的相同密钥库文件。

然后，您可以在Data Collector 配置文件中为工作节点指定生成的密钥库文件和密钥库密码文件`$SDC_CONF/sdc.properties`。您可以选择为网关节点和辅助节点生成信任库文件。

欲了解更多信息，请参阅[启用HTTPS ](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/HTTP_protocols.html#concept_xyp_lt4_cw)[启用HTTPS](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/DCConfig.html#concept_xyp_lt4_cw)的在数据采集器 的文档。

## 临时目录

Data Collector 要求集群中网关节点上的Java临时目录是可写的。

Java临时目录由Java系统属性指定`java.io.tmpdir`。在UNIX上，此属性的默认值通常为/ tmp并且可写。

在运行集群管道之前，请验证网关节点上的Java临时目录是否可写。

## 日志

由于群集管道可以作为MapReduce或Spark应用程序运行，因此群集中的每个Data Collector 工作器都管理自己的日志。 

该数据采集 人员发送日志消息基于集群执行模式不同的位置：

- 集群批处理模式管道

  对于群集批处理模式管道，每个Data Collector工作程序都将日志消息发送到工作程序节点上的syslog文件。您可以使用YARN Resource Manager UI来查看每个MapReduce任务的syslog文件。

- 集群流模式管道

  对于集群流传输模式管道，每个Data Collector工作程序都将日志消息发送到工作程序节点上的stderr。您可以使用Spark UI来查看每个Spark应用程序的stderr。

群集管道日志的大小会随着时间增长，尤其是对于连续运行的群集流管道而言。您可以选择配置 安装在网关节点上的Data Collector，以使用log4j滚动文件追加程序将日志消息写入sdc.log文件。此配置将传播到工作程序节点，以便每个Data Collector 工作程序将日志消息写入YARN应用程序目录内的sdc.log文件。

log4j滚动文件附加器会自动滚动或归档当前日志文件，然后继续记录另一个文件。`$SDC_CONF/sdc-log4j.properties`为 安装在网关节点上的Data Collector配置的 文件确定滚动文件附加程序滚动文件的频率。默认情况下，它最多将日志消息写入10个文件，当当前文件达到256 MB时，它将滚动到下一个文件。

在将Data Collector配置为使用滚动文件附加程序时，可以使用YARN Resource Manager UI在YARN应用程序目录中找到sdc.log文件，从而查看每个工作节点的日志文件。

要使Data Collector能够使用滚动文件追加器，请将以下行添加到在网关节点上定义的Data Collector 配置文件`$SDC_CONF/sdc.properties`：

```
cluster.pipelines.logging.to.stderr=false
```

## 流管道的检查点存储

当数据收集器在Mesos或YARN上运行群集流传输管道时，数据收集器将 生成并存储检查点元数据。检查点元数据提供原点的偏移量。

该数据收集器 存储在HDFS上或亚马逊S3以下路径检查点的元数据：

```
/user/$USER/.streamsets-spark-streaming/<DataCollector ID>/<Kafka topic>/<consumer group>/<pipelineName>
```

当您在YARN上运行群集流传输管道时，数据收集器 会将元数据存储在HDFS上。

在Mesos上运行集群管道时，数据收集器 可以将元数据存储在HDFS或Amazon S3上。

### 配置Mesos的位置

在Mesos上运行集群管道时，数据收集器可以将检查点信息写入HDFS或Amazon S3。

定义检查点存储的位置：

1. 配置core-site.xml和 hdfs-site.xml文件以定义将检查点信息写入何处。
2. 将文件存储在Data Collector资源目录中。
3. 在“ **群集”** >“ **检查点配置目录”**管道属性中输入文件的位置。

## 错误处理限制

请注意当前管道配置选项的以下限制：

- **错误记录** -将错误记录写入Kafka或丢弃记录。目前不支持停止管道或将记录写入文件。