# 数据源\

原始阶段代表管道的源。您可以在管道中使用单个原始阶段。

您可以根据管道的执行模式使用不同的来源：[Standalone](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_hpr_twm_jq__section_tvn_4bc_f2b)，[cluster](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_hpr_twm_jq__section_ffb_qbc_f2b)或[edge](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_hpr_twm_jq__section_rbz_qbc_f2b)。为了帮助创建或测试管道，您可以使用[开发源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_hpr_twm_jq__section_ewr_sbc_f2b)。

## 独立管道

在独立管道中，可以使用以下来源：

- [Amazon S3-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonS3.html#concept_kvs_3hh_ht)从Amazon S3读取对象。创建多个线程以在多线程管道中启用并行处理。
- [Amazon SQS使用者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonSQS.html#concept_xsh_knm_5bb) -从Amazon Simple Queue Services（SQS）中的队列读取数据。创建多个线程以在多线程管道中启用并行处理。
- [Azure Data Lake Storage Gen1-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/ADLS-G1.html#concept_osx_qgz_xhb)从Microsoft Azure Data Lake Storage Gen1读取数据。创建多个线程以在多线程管道中启用并行处理。
- [Azure Data Lake Storage Gen2-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/ADLS-G2.html#concept_osx_qgz_xhb)从Microsoft Azure Data Lake Storage Gen2读取数据。创建多个线程以在多线程管道中启用并行处理。
- [Azure IoT /事件中心使用者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AzureEventHub.html#concept_c1z_15q_1bb) -从Microsoft Azure事件中心读取数据。创建多个线程以在多线程管道中启用并行处理。
- [CoAP服务器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/CoAPServer.html#concept_wfy_ghn_sz) -侦听CoAP端点并处理所有授权的CoAP请求的内容。创建多个线程以在多线程管道中启用并行处理。
- [Cron Scheduler-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/CronScheduler.html#concept_nsz_mnr_2jb)生成具有cron表达式计划的当前日期时间的记录。
- [目录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_qcq_54n_jq) -从目录中读取完全写入的文件。创建多个线程以在多线程管道中启用并行处理。
- [Elasticsearch-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Elasticsearch.html#concept_f1q_vpm_2z)从Elasticsearch集群读取数据。创建多个线程以在多线程管道中启用并行处理。
- [文件尾](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/FileTail.html#concept_n1y_qyp_5q) -读取目录中的相关归档文件后，从活动文件中读取数据行。
- [Google BigQuery-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/BigQuery.html#concept_cg3_y3v_q1b)执行查询作业并从Google BigQuery读取结果。
- [Google Cloud](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/GCS.html#concept_iyd_wql_nbb) Storage-从Google Cloud Storage读取完全写入的对象。
- [Google Pub / Sub订阅服务器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/PubSub.html#concept_pjw_qtl_r1b) -消费来自Google Pub / Sub订阅的邮件。创建多个线程以在多线程管道中启用并行处理。
- [Groovy脚本编制](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/GroovyScripting.html#concept_chr_zjj_l3b) -运行Groovy脚本以创建Data Collector 记录。可以创建多个线程以启用多线程管道中的并行处理。
- [Hadoop FS独立服务器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HDFSStandalone.html#concept_djz_pdm_hdb) -从HDFS或Azure Blob存储读取完全写入的文件。创建多个线程以在多线程管道中启用并行处理。
- [HTTP客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HTTPClient.html#concept_wk4_bjz_5r) -从流HTTP资源URL读取数据。
- [HTTP服务器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HTTPServer.html#concept_s2p_5hb_4y) -侦听HTTP端点并处理所有授权的HTTP POST和PUT请求的内容。创建多个线程以在多线程管道中启用并行处理。
- [HTTP to Kafka（不建议使用）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HTTPtoKafka.html#concept_izh_mqd_dy) -在HTTP端点上侦听并将所有授权的HTTP POST请求的内容直接写入Kafka。
- [JavaScript脚本](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JavaScriptScripting.html#concept_kn5_bvt_m3b) -运行JavaScript脚本以创建数据收集器 记录。可以创建多个线程以启用多线程管道中的并行处理。
- [JDBC](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MultiTableJDBCConsumer.html#concept_zp3_wnw_4y)多表[使用者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MultiTableJDBCConsumer.html#concept_zp3_wnw_4y) -通过JDBC连接从多个表中读取数据库数据。创建多个线程以在多线程管道中启用并行处理。
- [JDBC查询使用者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JDBCConsumer.html#concept_qhf_hjr_bs) -使用用户定义的SQL查询通过JDBC连接读取数据库数据。
- [JMS使用者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JMS.html#concept_rhh_4nj_dt) -从JMS读取消息。
- [Jython脚本](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JythonScripting.html#concept_fxz_35t_m3b) -运行Jython脚本以创建Data Collector 记录。可以创建多个线程以启用多线程管道中的并行处理。
- [Kafka Consumer-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/KConsumer.html#concept_msz_wnr_5q)从单个Kafka主题读取消息。
- [Kafka Multitopic Consumer-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/KafkaMultiConsumer.html#concept_ccs_fn4_x1b)从多个Kafka主题读取消息。创建多个线程以在多线程管道中启用并行处理。
- [Kinesis Consumer-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/KinConsumer.html#concept_anh_4y3_yr)从Kinesis Streams读取数据。创建多个线程以在多线程管道中启用并行处理。
- [MapR DB CDC-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRdbCDC.html#concept_qwj_5vm_pbb)读取已更改的MapR DB数据，该数据已写入MapR流。创建多个线程以在多线程管道中启用并行处理。
- [MapR DB JSON-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRDBJSON.html#concept_ywh_k15_3y)从MapR DB JSON表读取JSON文档。
- [MapR FS Standalone-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRFSStandalone.html#concept_b43_3qc_mdb)从MapR FS读取完全写入的文件。创建多个线程以在多线程管道中启用并行处理。
- [MapR Multitopic Streams使用者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRStreamsMultiConsumer.html#concept_hvd_hww_lbb) -从多个MapR Streams主题读取消息。创建多个线程以在多线程管道中启用并行处理。
- [MapR Streams使用者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRStreamsCons.html#concept_cvy_xsf_2v) -从MapR Streams读取消息。
- [MongoDB-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDB.html#concept_bk4_2rs_ns)从MongoDB或Microsoft Azure Cosmos DB读取文档。
- [MongoDB Oplog-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MongoDBOplog.html#concept_mjn_yqw_4y)从MongoDB Oplog读取条目。
- [MQTT订阅服务器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MQTTSubscriber.html#concept_ukz_3vt_lz) -订阅MQTT代理上的主题以从代理读取消息。
- [MySQL Binary Log-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MySQLBinaryLog.html#concept_kqg_1yh_xx)读取MySQL二进制日志以生成变更数据捕获记录。
- [NiFi HTTP服务器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/NiFi.html#concept_ynn_vdb_p3b) -监听来自NiFi PutHTTP处理器的请求并处理NiFi FlowFiles。
- [Omniture-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Omniture.html#concept_dsr_xmw_1s)从Omniture报告API读取Web使用情况报告。
- [OPC UA客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OPCUAClient.html#concept_nmf_1ly_f1b) -从OPC UA服务器读取数据。
- [Oracle Bulkload-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleBulk.html#concept_lnz_kzp_zgb)从多个Oracle数据库表中读取数据，然后停止管道。创建多个线程以在多线程管道中启用并行处理。
- [Oracle CDC客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/OracleCDC.html#concept_rs5_hjj_tw) -读取LogMiner重做日志以生成更改数据捕获记录。
- [PostgreSQL CDC客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/PostgreSQL.html#concept_cfs_4m4_n2b) -读取PostgreSQL WAL数据以生成变更数据捕获记录。
- [Pulsar Consumer-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/PulsarConsumer.html#concept_o2b_1pc_r2b)从Apache Pulsar主题读取消息。
- [RabbitMQ使用者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/RabbitMQ.html#concept_dyg_lq1_h5) -从RabbitMQ读取消息。
- [Redis使用者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Redis.html#concept_plr_t3v_jw) -从Redis读取消息。
- [REST服务](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/RESTService.html#concept_hfg_2sn_p2b) - 侦听HTTP端点，解析所有授权请求的内容，并将响应发送回原始REST API。创建多个线程以在多线程管道中启用并行处理。仅在微服务管道中使用。
- [Salesforce-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Salesforce.html#concept_odf_vr3_rx)从Salesforce读取数据。
- [SDC RPC-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SDC_RPCorigin.html#concept_agb_5c1_ct)从SDC RPC管道中的SDC RPC目标读取数据。
- [SDC RPC到Kafka（不建议使用）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SDCRPCtoKafka.html#concept_tdk_slk_pw) -从SDC RPC管道中的SDC RPC目标读取数据，并将其写入Kafka。
- [SFTP / FTP / FTPS客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SFTP.html#concept_ic5_bzd_5v) -从SFTP，FTP或FTPS服务器读取文件。
- [SQL Server 2019 BDC多表使用者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerBDCMultitable.html#SQLServerBDCMultitable) -通过JDBC连接从Microsoft SQL Server 2019大数据群集（BDC）读取数据。创建多个线程以在多线程管道中启用并行处理。
- [SQL Server CDC客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerCDC.html#concept_ut3_ywc_v1b) -从Microsoft SQL Server CDC表读取数据。创建多个线程以在多线程管道中启用并行处理。
- [SQL Server更改跟踪](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SQLServerChange.html#concept_ewq_b2s_r1b) -从Microsoft SQL Server更改跟踪表读取数据，并生成每个记录的最新版本。创建多个线程以在多线程管道中启用并行处理。
- [启动管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/StartPipeline.html#concept_h1l_xpr_2jb) -启动 Data Collector， Data Collector Edge或Transformer管道。
- [TCP服务器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/TCPServer.html#concept_ppm_xb1_4z) -侦听指定的端口并通过TCP / IP连接处理传入的数据。创建多个线程以在多线程管道中启用并行处理。
- [Teradata使用者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Teradata.html#concept_zp3_wnw_4y) -通过JDBC连接从Teradata数据库表读取数据。创建多个线程以在多线程管道中启用并行处理。
- [UDP多线程源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/UDPMulti.html#concept_wng_g5f_5bb) -从一个或多个UDP端口读取消息。创建多个线程以在多线程管道中启用并行处理。
- [UDP源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/UDP.html#concept_rst_2y5_1s) -从一个或多个UDP端口读取消息。
- [UDP到Kafka（不建议使用）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/UDPtoKafka.html#concept_jzq_jcz_pw) -从一个或多个UDP端口读取消息，并将数据写入Kafka。
- [WebSocket客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/WebSocketClient.html#concept_unk_nzk_fbb) - 从WebSocket服务器端点读取数据。可以将响应作为微服务管道的一部分发送回原始系统。
- [WebSocket服务器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/WebSocketServer.html#concept_u2r_gpc_3z) - 侦听WebSocket端点并处理所有授权的WebSocket客户端请求的内容。创建多个线程以在多线程管道中启用并行处理。可以将响应作为微服务管道的一部分发送回原始系统。

## 集群管道

在群集管道中，可以使用以下来源：

- [Hadoop FS-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HadoopFS-origin.html#concept_lw2_tnm_vs)使用Hadoop FileSystem接口从HDFS，Amazon S3或其他文件系统读取数据。
- [Kafka Consumer-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/KConsumer.html#concept_msz_wnr_5q)从Kafka读取消息。使用源的群集版本。
- [MapR FS-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRFS.html#concept_psz_db4_lx)从MapR FS读取数据。

## 边缘管道

在边缘管道中，可以使用以下来源：

- [目录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_qcq_54n_jq) -从目录中读取完全写入的文件。
- [文件尾](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/FileTail.html#concept_n1y_qyp_5q) -读取目录中的相关归档文件后，从活动文件中读取数据行。
- [gRPC客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/gRPCClient.html#concept_yp1_4zs_yfb) -从gRPC服务器读取数据。
- [HTTP客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HTTPClient.html#concept_wk4_bjz_5r) -从流HTTP资源URL读取数据。
- [HTTP服务器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HTTPServer.html#concept_s2p_5hb_4y) -侦听HTTP端点并处理所有授权的HTTP POST和PUT请求的内容。
- [MQTT订阅服务器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MQTTSubscriber.html#concept_ukz_3vt_lz) -订阅MQTT代理上的主题以从代理读取消息。
- [系统指标](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/SystemMetrics.html#concept_gzy_gmv_32b) -从安装了SDC Edge的边缘设备读取系统指标。
- [WebSocket客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/WebSocketClient.html#concept_unk_nzk_fbb) -从WebSocket服务器端点读取数据。
- [Windows事件日志](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/WindowsLog.html#concept_agf_5jv_sbb) -从Windows计算机上的Microsoft Windows事件日志中读取数据。

## 发展起源

为了帮助创建或测试管道，可以使用以下开发来源：

- 开发数据生成器
- 开发随机源
- 开发原始数据源
- 带缓冲的Dev SDC RPC
- 开发快照重播
- 传感器读取器

有关更多信息，请参见[开发阶段](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DevStages.html#concept_czx_ktn_ht)。

## 比较HTTP起源

我们有几种HTTP起源，请确保使用最佳的HTTP起源。以下是一些主要差异的快速细分：

| 起源                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [HTTP客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HTTPClient.html#concept_wk4_bjz_5r) | 向外部系统发起HTTP请求。同步处理数据。处理JSON，文本和XML数据。可以处理一系列HTTP请求。可以在带有处理器的管道中使用。 |
| [HTTP服务器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HTTPServer.html#concept_s2p_5hb_4y) | 侦听传入的HTTP请求，并在发送方等待确认时处理它们。同步处理数据。创建多线程管道，因此适合传入数据的高吞吐量。处理几乎所有数据格式。处理HTTP POST和PUT请求。可以在带有处理器的管道中使用。 |

Data Collector还提供了[HTTP到Kafka的起源，](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HTTPtoKafka.html#concept_izh_mqd_dy) 用于从多个HTTP客户端读取大量数据并将数据立即写入Kafka，而无需进行其他处理。但是，HTTP到Kafka的起源现在已被弃用，并将在以后的版本中删除。我们建议使用HTTP Server源。

## 比较MapR起源

我们有多个MapR来源，请确保使用最适合您的需求。以下是一些主要差异的快速细分：

| 起源                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [MapR DB CDC](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRdbCDC.html#concept_qwj_5vm_pbb) | 使用MapR流读取更改数据捕获MapR DB数据。在记录标题属性中包括CDC信息。在独立执行模式管道中使用。 |
| [MapR DB JSON](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRDBJSON.html#concept_ywh_k15_3y) | 从MapR DB读取JSON文档。将每个JSON文档转换为一条记录。在独立执行模式管道中使用。 |
| [MapR FS](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRFS.html#concept_psz_db4_lx) | 从MapR FS读取文件。可以与Kerberos身份验证一起使用。在集群执行模式管道中使用。 |
| [MapR FS独立版](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRFSStandalone.html#concept_b43_3qc_mdb) | 从MapR FS读取文件。可以使用多个线程来启用文件的并行处理。可以与Kerberos身份验证一起使用。在独立执行模式管道中使用。 |
| [MapR多主题流](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRStreamsMultiConsumer.html#concept_hvd_hww_lbb) | 从MapR Streams流数据。可以使用多个线程来读取多个主题。在独立执行模式管道中使用。 |
| [MapR流消费者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRStreamsCons.html#concept_cvy_xsf_2v) | 从MapR Streams流数据。使用单个线程从单个主题读取。在独立执行模式管道中使用。 |

## 比较UDP源

UDP源和UDP多线程源的起源非常相似。主要区别在于UDP多线程源可以使用多个线程来处理管道中的数据。

UDP多线程源具有一个有助于多线程处理的处理队列。但是，在某些情况下，使用此队列可能会减慢处理速度。

下表描述了您可能希望使用每个来源的某些情况：

| 起源                                                         | 理想用于                                                     |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [UDP多线程源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/UDPMulti.html#concept_wng_g5f_5bb) | Epoll支持允许使用多个接收器线程将数据传递到管道。复杂的管道需要更长的处理时间。要么缺乏epoll支持仅允许单个接收器线程将数据传递到管道。大量数据。 |
| [UDP来源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/UDP.html#concept_rst_2y5_1s) | Epoll支持允许使用多个接收器线程将数据传递到管道。相对简单的管道可以加快数据收集器的处理速度。 |

Data Collector还提供[UDP至Kafka的源，](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/UDPtoKafka.html#concept_jzq_jcz_pw) 用于从多个UDP端口读取大量数据并将数据立即写入Kafka，而无需进行其他处理。但是，不推荐使用从Kafka到UDP的UDP，并且在以后的版本中将其删除。我们建议使用UDP多线程源起源。

## 比较WebSocket起源

我们有两个WebSocket起源，请确保使用最合适的一个来满足您的需求。以下是一些主要差异的快速细分：

| 起源                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [WebSocket客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/WebSocketClient.html#concept_unk_nzk_fbb) | 启动与WebSocket服务器端点的连接，然后等待WebSocket服务器推送数据。 |
| [WebSocket服务器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/WebSocketServer.html#concept_u2r_gpc_3z) | 侦听传入的WebSocket请求并在发送方等待确认时处理它们。创建多线程管道，因此适合传入数据的高吞吐量。 |

## 批次大小和等待时间

对于原始阶段，批处理大小确定一次通过管道发送的最大记录数。批处理等待时间确定源在发送批处理之前等待数据的时间。在等待时间结束时，无论批次包含多少记录，它都会发送批次。

例如，文件尾的来源配置为批处理大小为20条记录，批处理等待时间为240秒。当数据迅速到达时，“文件尾”将用20条记录填充批处理，并立即通过管道将其发送，创建新批处理，并在其满后立即再次发送。随着传入数据的速度变慢，剩余的一批将包含一些记录，并定期获得额外的记录。创建批处理后240秒，“文件尾”将通过管道发送部分填充的批处理。它立即创建一个新批次并开始新的倒计时。

根据您的处理需求配置批处理等待时间。您可以减少批处理等待时间，以确保在指定的时间范围内处理所有数据或与管道目标进行定期联系。如果您不想处理部分或空批次，请使用默认值或增加等待时间。

## 最大记录大小

大多数数据格式都有一个属性，该属性限制了源可以解析的记录的最大大小。例如，定界数据格式具有“最大记录长度”属性，JSON数据格式具有“最大对象长度”，而文本数据格式具有“最大行长”。

当原点处理大于指定长度的数据时，行为会因原点和数据格式而异。例如，对于某些数据格式，将根据为源配置的记录错误处理来处理超大记录。当采用其他数据格式时，原点可能会截断数据。有关起点如何处理每种数据格式的尺寸超限的详细信息，请参见起点文档的“数据格式”部分。

可用时，最大记录大小属性受数据收集器 解析器缓冲区大小限制，默认情况下为1048576字节。因此，在源中增加最大记录大小属性不会更改源的行为时，您可能需要通过在Data Collector配置文件中配置parser.limit属性来增加Data Collector解析器缓冲区大小。有关更多信息，请参阅[数据收集](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23task_lxk_kjw_1r)器文档中的[配置数据收集](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23task_lxk_kjw_1r)器。

请注意，大多数最大记录大小属性均以字符指定，而数据收集器限制以字节为单位定义。

## 预览原始数据

某些来源允许您预览原始源数据。在查看数据时预览原始源数据可能有助于原始配置。

预览文件数据时，可以使用实际目录和实际源文件。或者在适当的时候，您可以使用与源文件相似的其他文件。

预览Kafka数据时，请输入Kafka群集的连接信息。

在预览管道的数据时，不使用在原始阶段用于原始源预览的数据。

1. 在原始阶段的“属性”面板中，单击“ **原始预览”**选项卡。

2. 对于目录或文件尾的来源，请输入目录和文件名。

3. 对于Kafka使用者或Kafka多主题使用者，请输入以下信息：

   | Kafka Raw Preview属性 | 描述                                       |
   | :-------------------- | :----------------------------------------- |
   | 话题                  | 卡夫卡主题阅读。                           |
   | 划分                  | 分区读取。                                 |
   | 经纪人主机            | 经纪人主机名。使用与该分区关联的任何代理。 |
   | 经纪人港口            | 代理端口号。                               |
   | 最大等待时间（秒）    | 预览等待从Kafka接收数据的最长时间。        |

4. 点击**预览**。

Raw Source Preview区域显示预览。