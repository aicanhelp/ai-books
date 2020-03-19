# 什么是管道？

管道描述了从原始系统到目标系统的数据流，并定义了如何沿途转换数据。

您可以使用单个[原始](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_hpr_twm_jq)阶段来表示原始系统，可以使用多个[处理器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Processors_overview.html#concept_hpr_twm_jq)阶段来转换数据，并可以使用多个[目标](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Destinations_overview.html#concept_hpr_twm_jq) 阶段来表示目标系统。

开发管道时，可以使用[开发阶段](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DevStages.html#concept_czx_ktn_ht)来提供示例数据并生成错误以测试错误处理。您可以使用[数据预览](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Preview/DataPreview_Title.html#concept_jtn_s3m_lq)来确定阶段如何更改管道中的数据。

您可以使用[执行程序](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Executors-overview.html#concept_stt_2lk_fx)阶段执行[事件触发的任务执行](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)或保存事件信息。要处理大量数据，可以使用[多线程管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Multithreaded_Pipelines/MultithreadedPipelines.html#concept_zpp_2xc_py)或[集群模式管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/ClusterPipelines.html#concept_hmh_kfn_1s)。

在写入[Hive或Parquet](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Hive_Drift_Solution/HiveDriftSolution_title.html#concept_phk_bdf_2w)或[PostgreSQL的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/JDBC_DriftSolution/JDBC_DriftSyncSolution_title.html#concept_ljq_knr_4cb)管道中，您可以实现数据漂移解决方案，该解决方案可以检测传入数据中的漂移并更新目标系统中的表。

完成管道开发后，您可以[发布管道](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Pipelines/ConfigurePipelines_PDesigner.html#concept_n2b_lpq_ccb)并创建作业以执行管道中定义的数据流。启动作业时，Control Hub在与作业关联的可用Data Collector上运行作业。有关作业的更多信息，请参见[作业概述](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Jobs/Jobs.html#concept_omz_yn1_4w)。