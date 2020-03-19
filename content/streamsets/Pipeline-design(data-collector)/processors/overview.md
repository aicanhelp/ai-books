# 处理器

处理器阶段代表您要执行的一种数据处理类型。您可以根据需要在管道中使用任意数量的处理器。

您可以根据管道的执行模式使用不同的处理器：[Standalone](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Processors_overview.html#concept_hpr_twm_jq__section_hrt_ll2_f2b)，[cluster](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Processors_overview.html#concept_hpr_twm_jq__section_ccz_ml2_f2b)或[edge](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Processors_overview.html#concept_hpr_twm_jq__section_p3m_4l2_f2b)。为了帮助创建或测试管道，可以使用[开发处理器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Processors_overview.html#concept_hpr_twm_jq__section_fcd_pl2_f2b)。

## 仅独立管道

在独立管道中，可以使用以下处理器：

- [记录重复数据删除器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/RDeduplicator.html#concept_z3m_v52_zq) -删除重复的记录。

## 独立或群集管道

在独立或群集管道中，可以使用以下处理器：

- [Base64字段解码器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Base64Decoder.html#concept_ujj_spy_kv) -将Base64编码的数据解码为二进制数据。
- [Base64字段编码器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Base64Encoder.html#concept_wtr_mpy_kv) -使用Base64编码二进制数据。
- [Control Hub API-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/ControlHubAPI.html#concept_akz_zsr_2jb)调用 Control Hub API。
- [Couchbase查找](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/CouchbaseLookup.html#concept_rxk_1dq_2fb) -在Couchbase Server中执行查找，以丰富数据记录。
- [Databricks ML评估器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/DatabricksML.html#concept_nlz_k3v_gfb) -使用通过Databricks ML模型导出导出的机器学习模型来生成评估，评分或数据分类。
- [数据生成器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/DataGenerator.html#concept_hw1_gq4_3fb) -使用指定的数据格式将记录序列化为字段。
- [数据解析器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/DataParser.html#concept_xw3_4xk_r1b) -解析嵌入在字段中的NetFlow或syslog数据。
- [延迟](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Delay.html#concept_ez5_pvf_wbb) -延迟将批处理传递到管道的其余部分。
- [加密和解密字段](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/EncryptDecrypt.html#concept_zs3_vfk_hfb) -加密或解密字段。
- [表达式计算器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Expression.html#concept_zm2_pp3_wq) -对数据执行计算。也可以添加或修改记录标题属性。
- [字段展平器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldFlattener.html#concept_njn_3kk_fx) -展[平](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldFlattener.html#concept_njn_3kk_fx)嵌套的字段。
- [Field Hasher-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldHasher.html#concept_ivv_c3k_wq)使用算法对敏感数据进行编码。
- [字段映射器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldMapper.html#concept_q5y_tdq_xgb) -将表达式[映射](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldMapper.html#concept_q5y_tdq_xgb)到一组字段以更改字段路径，字段名称或字段值。
- [字段](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldMasker.html#concept_hjc_t4k_wq)屏蔽器-屏蔽敏感的字符串数据。
- [字段合并](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldMerger.html#concept_pgm_tsl_gt) -合并复杂列表或地图中的字段。
- [字段顺序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldOrder.html#concept_krp_5fv_vy) -对地图或列表图根字段类型中的字段进行排序，并将字段输出为列表图或列表根字段类型。
- [字段透视器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/ListPivoter.html#concept_ekg_313_qw) - [透视](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/ListPivoter.html#concept_ekg_313_qw)列表，地图或列表地图字段中的数据，并为该字段中的每个项目创建一条记录。
- [字段](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldRemover.html#concept_jdd_blr_wq)删除[器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldRemover.html#concept_jdd_blr_wq) -从记录中删除字段。
- [字段](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldRenamer.html#concept_vyv_zsg_ht)重命名器-重命名记录中的字段。
- [字段](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldReplacer.html#concept_rw4_2d3_4cb)替换器-替换字段值。
- [字段](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldSplitter.html#concept_vlj_vph_yq)拆分[器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldSplitter.html#concept_vlj_vph_yq) -将[字段中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldSplitter.html#concept_vlj_vph_yq)的字符串值拆分为不同的字段。
- [字段类型转换器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldTypeConverter.html#concept_is3_zkp_wq) -转换字段的数据类型。
- [字段压缩](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldZip.html#concept_o3b_t1k_yx) -合并来自两个字段的列表数据。
- [Geo IP-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/GeoIP.html#concept_fch_fc3_ms)返回指定IP地址的地理位置和IP智能信息。
- [Groovy评估器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Groovy.html#concept_ldh_sct_gv) -根据自定义Groovy代码处理记录。
- [HBase查找](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/HBaseLookup.html#concept_mnj_zhq_bw) -在HBase中执行键-值查找，以丰富数据记录。
- [Hive元数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/HiveMetadata.html#concept_rz5_nft_zv) -与Hive Metastore目标一起使用，作为Hive [漂移同步解决方案的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Hive_Drift_Solution/HiveDriftSolution_title.html#concept_phk_bdf_2w)一部分。
- [HTTP客户端](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/HTTPClient.html#concept_ghx_ypr_fw) -HTTP客户端处理器将请求发送到HTTP资源URL，并将结果写入字段。
- [HTTP路由器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/HTTPRouter.html#concept_ghx_ypr_fw) - 根据记录头属性中的HTTP方法和URL路径将数据路由到不同的流。
- [JavaScript评估程序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/JavaScript.html#concept_n2p_jgf_lr) -根据自定义JavaScript代码处理记录。
- [JDBC查找](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/JDBCLookup.html#concept_ysc_ccy_hw) -通过JDBC连接在数据库表中执行查找。
- [JDBC Tee-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/JDBCTee.html#concept_qbx_lcy_hw)通过JDBC连接将数据写入数据库表，并使用生成的数据库列中的数据丰富记录。
- [JSON生成器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/JSONGenerator.html#concept_jmg_hw1_h1b) -将字段中的数据序列化为JSON编码的字符串。
- [JSON解析器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/JSONParser.html#concept_bs1_4t3_yq) -解析嵌入在字符串字段中的JSON对象。
- [Jython评估程序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Jython.html#concept_a1h_lkf_lr) -根据自定义Jython代码处理记录。
- [Kudu查找](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/KuduLookup.html#concept_a1x_3wl_p1b) -在Kudu中执行查找以用数据丰富记录。
- [日志解析器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/LogParser.html#concept_ulm_qdq_fs) -根据指定的日志格式解析字段中的日志数据。
- [MLeap评估器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/MLeap.html#concept_wnr_wlv_gfb) -使用存储在MLeap捆绑软件中的机器学习模型来生成评估，评分或数据分类。
- [MongoDB查找](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/MongoDBLookup.html#concept_rrp_t4w_2fb) -在MongoDB中执行查找以用数据丰富记录。
- [PMML评估器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/PMML.html#concept_r3s_3fv_gfb) -使用存储在PMML文档中的机器学习模型来生成数据的预测或分类。
- [PostgreSQL元数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/PostgreSQLMetadata.html#concept_lcp_ssh_qcb) -跟踪源数据中的结构更改，然后创建和更改PostgreSQL表，作为PostgreSQL [漂移同步解决方案的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/JDBC_DriftSolution/JDBC_DriftSyncSolution_title.html#concept_ljq_knr_4cb)一部分。
- [Redis查找](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/RedisLookup.html#concept_ng3_lpr_pv) -在Redis中执行键-值查找，以丰富数据记录。
- [Salesforce查找](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/SalesforceLookup.html#concept_k23_3rk_yx) -在Salesforce中执行查找以用数据丰富记录。
- [模式生成器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/SchemaGenerator.html#concept_rfz_ks3_x1b) -为每个记录生成一个模式，并将该模式写入记录头属性。
- [Spark Evaluator-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Spark.html#concept_cpx_1lm_zx)基于自定义Spark应用程序处理数据。
- [SQL解析器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/SQLParser.html#concept_zh2_kfj_tdb) -解析字符串字段中的SQL查询。
- [启动作业](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/StartJob.html#concept_irv_l5r_2jb) -启动控制中心 作业。
- [启动管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/StartPipeline.html#concept_bbc_cxr_2jb) -启动 Data Collector， Data Collector Edge或Transformer管道。
- [静态查找](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/StaticLookup.html#concept_aqz_t4r_pv) -在本地内存中执行键值查找。
- [流选择器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/StreamSelector.html#concept_tqv_t5r_wq) -根据条件将数据路由到不同的流。
- [TensorFlow评估器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/TensorFlow.html#concept_otg_csh_z2b) -使用TensorFlow机器学习模型来生成数据的预测或分类。
- [值](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/ValueReplacer.html#concept_o5k_dmf_zq) 替换器[（不建议使用）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/ValueReplacer.html#concept_o5k_dmf_zq) -用常量或空值替换现有的空值或指定的值。
- [整个文件转换器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/WholeFileTransformer.html#concept_nwg_rx4_l2b) -将Avro文件转换为Parquet。
- [窗口聚合器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Aggregator.html#concept_ofb_svm_5bb) -在一个时间范围内执行聚合，在“监视”模式下显示结果，并在启用后将结果写入事件。该处理器不更新正在评估的记录。
- [XML Flattener-](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/XMLFlattener.html#concept_ck4_255_sv)在字符串字段中展[平](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/XMLFlattener.html#concept_ck4_255_sv) XML数据。
- [XML解析器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/XMLParser.html#concept_dtt_q5q_k5) -解析字符串字段中的XML数据。

## 边缘管道

在边缘管道中，可以使用以下处理器：

- [表达式计算器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Expression.html#concept_zm2_pp3_wq) -对数据执行计算。也可以添加或修改记录标题属性。
- [字段](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldRemover.html#concept_jdd_blr_wq)删除[器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldRemover.html#concept_jdd_blr_wq) -从记录中删除字段。
- [JavaScript评估程序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/JavaScript.html#concept_n2p_jgf_lr) -根据自定义JavaScript代码处理记录。
- [流选择器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/StreamSelector.html#concept_tqv_t5r_wq) -根据条件将数据路由到不同的流。
- [TensorFlow评估器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/TensorFlow.html#concept_otg_csh_z2b) -使用TensorFlow机器学习模型来生成数据的预测或分类。

## 开发处理器

为了帮助创建或测试管道，可以使用以下开发处理器：

- 开发人员身份
- 开发随机误差
- 开发记录创建者

有关更多信息，请参见[开发阶段](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DevStages.html#concept_czx_ktn_ht)。