# 目的地

目标阶段代表管道的目标。您可以在管道中使用一个或多个目标。

您可以根据管道的执行模式使用不同的目标：[Standalone](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Destinations_overview.html#concept_hpr_twm_jq__section_bm2_fm2_f2b)，[cluster](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Destinations_overview.html#concept_hpr_twm_jq__section_wcp_gm2_f2b)或[edge](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Destinations_overview.html#concept_hpr_twm_jq__section_c4b_3m2_f2b)。为了帮助创建或测试管道，您可以使用[开发目标](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Destinations_overview.html#concept_hpr_twm_jq__section_yhf_km2_f2b)。

## 仅独立管道

在独立管道中，可以使用以下目标：

- [Rabbit MQ生产者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MongoDB.html#concept_eth_k5n_4v) -将数据写入RabbitMQ。
- 将响应发送到源 - 将具有指定响应的记录发送到管道中的微服务源。仅在[微服务管道中使用](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Microservice/Microservice_Title.html#concept_qfh_xdm_p2b)。

## 独立或群集管道

在独立或群集管道中，可以使用以下目标：

- [Aerospike-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Aerospike.html#concept_gyq_rpr_4cb)将数据写入Aerospike。
- [Amazon S3-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_avx_bnq_rt)将数据写入Amazon S3。
- [Azure Data Lake Storage（旧版）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/DataLakeStore.html#concept_jzm_kf4_zx) -将数据写入Azure Data Lake Storage Gen1。
- [Azure Data Lake Storage Gen1-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/ADLS-G1-D.html#concept_xzc_wfq_xhb)将数据写入Azure Data Lake Storage Gen1。
- [Azure Data Lake Storage Gen2-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/ADLS-G2-D.html#concept_ajp_1d2_vhb)将数据写入Azure Data Lake Storage Gen2。
- [Azure Event Hub生产者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AzureEventHubProducer.html#concept_xq5_d5q_1bb) -将数据写入Azure Event Hub。
- [Azure IoT中心生产者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AzureIoTHub.html#concept_pnd_jkq_1bb) -将数据写入Microsoft Azure IoT中心。
- [Cassandra-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Cassandra.html#concept_hfy_mfd_sr)将数据写入Cassandra集群。
- [CoAP客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/CoAPClient.html#concept_hw5_s3n_sz) -将数据写入CoAP端点。
- [Couchbase-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Couchbase.html#concept_ahq_1wq_h2b)将数据写入Couchbase数据库。
- [Elasticsearch-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Elasticsearch.html#concept_u5t_vpv_4r)将数据写入Elasticsearch集群。
- [爱因斯坦分析](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/WaveAnalytics.html#concept_hlx_r53_rx) -将数据写入Salesforce爱因斯坦分析。
- [Flume-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Flume.html#concept_pzn_hl4_yr)将数据写入Flume源。
- [Google BigQuery-将](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/BigQuery.html#concept_hj4_brk_dbb)数据流式传输到Google BigQuery。
- [Google Bigtable-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Bigtable.html#concept_pl5_tmq_tx)将数据写入Google Cloud Bigtable。
- [Google Cloud](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/GCS.html#concept_p4n_jrl_nbb) Storage-将数据写入Google Cloud Storage。
- [Google Pub / Sub发布者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/PubSubPublisher.html#concept_qsj_hk1_v1b) -将消息发布到Google Pub / Sub。
- [GPSS生产者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/GPSS.html#concept_qjf_xdz_q3b) -通过Greenplum流服务器（GPSS）将数据写入Greenplum数据库。
- [Hadoop FS-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/HadoopFS-destination.html#concept_awl_4km_zq)将数据写入HDFS或Azure Blob存储。
- [HBase-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/HBase.html#concept_wsz_5t5_vr)将数据写入HBase群集。
- [Hive Metastore-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/HiveMetastore.html#concept_gcr_z2t_zv)根据需要创建和更新Hive表。
- [Hive流](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Hive.html#concept_kvs_3hh_ht) -将数据写入Hive。
- [HTTP客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/HTTPClient.html#concept_khl_sg5_lz) - 将数据写入HTTP端点。可以向微服务管道中的微服务源发送响应。
- [InfluxDB-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/InfluxDB.html#concept_inf_db_sr)将数据写入InfluxDB。
- [JDBC Producer-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/JDBCProducer.html#concept_kvs_3hh_ht)通过JDBC连接将数据写入数据库表。
- [JMS Producer-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/JMSProducer.html#concept_sfz_ww5_n1b)将数据写入JMS。
- [卡夫卡生产者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_oq2_5jl_zq) - 将数据写入卡夫卡集群。可以向微服务管道中的微服务源发送响应。
- [Kinesis Firehose-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KinFirehose.html#concept_bjv_dpk_kv)将数据写入Kinesis Firehose传递流。
- [室壁运动制片人](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KinProducer.html#concept_swk_h1j_yr) - 写数据到室壁运动流。可以向微服务管道中的微服务源发送响应。
- [KineticaDB-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KineticaDB.html#concept_hxh_5xg_qbb)将数据写入Kinetica集群中的表。
- [Kudu-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Kudu.html#concept_chy_xxg_4v)将数据写入Kudu。
- [本地FS-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/LocalFS.html#concept_zvc_bv5_1r)将数据写入本地文件系统。
- [MapR DB-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MapRDB.html#concept_vxg_w2z_yv)将数据以文本，二进制数据或JSON字符串的形式写入MapR DB二进制表。
- [MapR DB JSON-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MapRDBJSON.html#concept_i4h_2kj_dy)将数据作为JSON文档写入MapR DB JSON表。
- [MapR FS-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MapRFS.html#concept_spv_xlc_fv)将数据写入MapR FS。
- [MapR Streams生产者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MapRStreamsProd.html#concept_cfj_qbn_2v) -将数据写入MapR Streams。
- [MemSQL快速加载程序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MemSQLLoader.html#concept_kvs_3hh_ht) -将数据写入MemSQL或MySQL。
- [MongoDB-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MongoDB.html#concept_eth_k5n_4v)将数据写入MongoDB或Microsoft Azure Cosmos DB。
- [MQTT发布者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MQTTPublisher.html#concept_odz_txt_lz) -将消息发布到MQTT代理上的主题。
- [命名管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/NamedPipe.html#concept_pl5_tdg_gcb) -将数据写入命名管道。
- [Pulsar Producer-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/PulsarProducer.html#concept_fq3_kpc_r2b)将数据写入Apache Pulsar主题。
- [Redis-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Redis.html#concept_ktc_gw2_gw)将数据写入Redis。
- [Salesforce-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Salesforce.html#concept_rlb_rt3_rx)将数据写入Salesforce。
- [SDC RPC-将](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/SDC_RPCdest.html#concept_lfk_hx2_ct)数据传递到SDC RPC管道中的SDC RPC源。
- [SFTP / FTP / FTPS客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/SFTP.html#concept_sgt_m2m_xhb) -使用SFTP，FTP或FTPS将数据发送到URL。
- [Snowflake-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_vxl_zzc_1gb)将数据写入Snowflake数据库中的表。
- [Solr-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Solr.html#concept_z2g_q1r_wr)将数据写入Solr节点或集群。
- [Splunk-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Splunk.html#concept_zzr_pqn_xdb)将数据写入Splunk。
- [SQL Server 2019 BDC批量加载器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/SQLServerBDCBulk.html#concept_hjv_5nn_r3b) -使用批量插入将数据写入Microsoft SQL Server 2019大数据群集（BDC）。
- [Syslog-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Syslog.html#concept_idr_ct5_w2b)将数据写入Syslog服务器。
- [到错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/ToError.html#concept_ryn_v3z_lr) -将记录传递到管道以进行错误处理。
- [废纸](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Trash.html#concept_htf_ydj_wq) --从管道中删除记录。
- [WebSocket客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/WebSocketClient.html#concept_l4d_mjn_lz) -将数据写入WebSocket端点。

## 边缘管道

在边缘管道中，可以使用以下目标：

- [Amazon S3-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/AmazonS3.html#concept_avx_bnq_rt)将数据写入Amazon S3。
- [CoAP客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/CoAPClient.html#concept_hw5_s3n_sz) -将数据写入CoAP端点。
- [HTTP客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/HTTPClient.html#concept_khl_sg5_lz) -将数据写入HTTP端点。
- [Kafka Producer-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_oq2_5jl_zq)将数据写入Kafka集群。
- [Kinesis Firehose-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KinFirehose.html#concept_bjv_dpk_kv)将数据写入Kinesis Firehose传递流。
- [Kinesis Producer-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KinProducer.html#concept_swk_h1j_yr)将数据写入Kinesis Streams。
- [MQTT发布者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/MQTTPublisher.html#concept_odz_txt_lz) -将消息发布到MQTT代理上的主题。
- [到错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/ToError.html#concept_ryn_v3z_lr) -将记录传递到管道以进行错误处理。
- [WebSocket客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/WebSocketClient.html#concept_l4d_mjn_lz) -将数据写入WebSocket端点。

## 发展目标

为了帮助创建或测试管道，可以使用以下开发目标：

- 前往赛事

有关更多信息，请参见[开发阶段](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DevStages.html#concept_czx_ktn_ht)。