# 管道设计

本章包含有关设计Transformer管道的信息。有关Transformer的一般概述，请参见[Transformers](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Transformer/Transformer.html#concept_znl_yxj_l3b)。

[什么是变压器管道？](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/TransformerPipeline.html#concept_iwf_2zr_qgb) 一个变压器管道描述数据从原点系统流动到目标系统，并定义了如何沿途转换数据。

[执行模式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/ExecutionMode.html#concept_lgy_24q_qgb) 变压器管道可以批处理或流模式运行。

[舞台库比赛要求](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Pipeline-StageLibMatch.html)

[本地管道](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Local.html#concept_wkf_vnj_2hb) 本地管道在 Transformer计算机上的本地Spark安装上运行。

[Hadoop YARN上的管道](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Hadoop.html#concept_lnz_xnj_2hb) 您可以使用部署在Hadoop YARN群集上的Spark运行 Transformer管道。当管道运行时，Spark在群集中的各个节点之间分配处理。

[Databricks上的管道](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Databricks.html#concept_bkm_31c_4hb) 您可以使用部署在Databricks群集上的Spark运行 Transformer管道。当管道运行时，Spark在群集中的各个节点之间分配处理。

[SQL Server 2019大数据群集上的](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-SQLServerBDC.html#concept_w5n_frw_zjb)管道可以使用部署在 SQL Server 2019大数据群集（BDC）上的Spark 运行 Transformer管道。当管道运行时，Spark在群集中的各个节点之间分配处理。

[SQL Server 2019 JDBC连接信息](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/SQLServer-JDBCConnectString.html#concept_bfs_3nm_1kb)

[额外的Spark配置](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/SparkConfig.html#concept_gyp_nmj_2hb) 在创建管道时，可以定义额外的Spark配置属性，这些属性确定管道在Spark上的运行方式。Transformer在启动Spark应用程序时将配置属性传递给Spark。

[分区](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Partitioning.html#concept_myb_22h_wgb) 启动管道时， StreamSets Transformer将启动Spark应用程序。与运行其他任何应用程序一样，Spark运行该应用程序，将管道数据拆分为多个分区，并在分区上并行执行操作。

[胶印处理](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Offsets.html#concept_a4b_hkw_gjb)

[交付保证](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/DeliveryGuarantee.html) Transformer的[偏移处理](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Offsets.html#concept_a4b_hkw_gjb)可确保在突发故障时 Transformer管道不会丢失数据-它至少处理一次数据。如果在特定时间发生突发故障，则最多可以重新处理一批数据。这是一次至少一次的交货保证。
[缓存数据](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/CachingData.html) 您可以配置大多数来源和处理器来缓存数据。当一个阶段将数据传递到多个下游阶段时，您可以启用缓存。

[执行查询](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Lookups.html#concept_f2z_5yw_g3b)

[可笑的处理模式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Ludicrous.html#concept_m4y_lbq_g3b) 您可以将 Transformer配置为以可笑的处理模式运行管道。

[预处理脚本](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/PreprocessingScript.html)[资料类型](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/DataTypes.html#concept_ec5_wlz_mhb)

[技术预览功能](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/TechPreview.html) 变压器包含带有技术预览名称的某些新功能和阶段。Technology Preview功能可用于开发和测试，但不适用于生产。

[配置管道](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Pipeline-Configuring.html#task_xlv_jlk_jq) 配置管道以定义数据流。配置管道后，可以启动管道。