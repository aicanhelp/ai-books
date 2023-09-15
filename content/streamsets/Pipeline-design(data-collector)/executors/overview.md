# 执行者

执行程序阶段在收到事件时会触发任务。

在事件流中，将执行程序用作数据流触发器的一部分，以执行事件驱动的，与管道相关的任务，例如在目标关闭时移动完全写入的文件。

有关事件框架的更多信息，请参见《[数据流触发器概述》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

您可以在管道中使用以下执行程序：

- [Amazon S3-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/AmazonS3.html#concept_mvh_bnm_f1b)为指定的内容创建新的Amazon S3对象，复制存储桶中的对象，或将标签添加到现有的Amazon S3对象。
- [ADLS Gen1文件元数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/ADLS-G1-FileMeta.html#concept_zhp_ldk_xhb) -收到事件后，更改文件元数据，创建一个空文件或删除Azure Data Lake Storage Gen1中的文件或目录。
- [ADLS Gen2文件元数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/ADLS-G2-FileMeta.html#concept_i22_k2k_xhb) -收到事件后，更改文件元数据，创建一个空文件或删除Azure Data Lake Storage Gen2中的文件或目录。
- [Databricks-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Databricks.html#concept_fdc_qrx_jz)收到事件记录后启动指定的Databricks作业。
- [电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Email.html#concept_sjs_sfp_qz) -在收到事件后向配置的收件人发送自定义电子邮件。
- [HDFS文件元数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HDFSMetadata.html#concept_wgj_slk_fx) -收到事件后，更改文件元数据，创建空文件或删除HDFS或本地文件系统中的文件或目录。
- [Hive查询](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/HiveQuery.html#concept_kjw_llk_fx) -收到事件记录后运行用户定义的Hive或Impala查询。
- [JDBC查询](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/JDBCQuery.html#concept_j3r_gcv_sx) -收到事件记录后运行用户定义的SQL查询。
- [MapR FS文件元数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/MapRFSFileMeta.html#concept_ohx_r5h_z1b) -收到事件后，更改文件元数据，创建空文件或删除MapR FS中的文件或目录。
- [MapReduce-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/MapReduce.html#concept_bj2_zlk_fx)收到事件记录后启动指定的MapReduce作业。
- [Pipeline Finisher-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/PipelineFinisher.html#concept_qzm_l4r_kz)收到事件记录后停止并将管道转换为Finished状态。
- [壳牌](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Shell.html#concept_jsr_zpw_tz) -在接收到事件记录执行shell脚本。
- [Spark-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Spark.html#concept_cvy_vxb_1z)收到事件记录后启动指定的Spark应用程序。