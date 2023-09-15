# 文档中的管道类型和图标

在Data Collector中，您可以配置Data Collector运行的管道和Data Collector Edge运行的管道。

在每个阶段的文档中，概述列出了可以使用该阶段的管道类型：Data Collector， Data Collector Edge或两者。例如，[目录来源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_qcq_54n_jq)的概述列出了 Data Collector 和Data Collector Edge，但是[Amazon S3来源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/AmazonS3.html#concept_kvs_3hh_ht)的概述仅列出了Data Collector。

在一个阶段内，某些功能或特性可能仅限于特定的管道类型。例如，目录来源的[多线程处理文档](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Directory.html#concept_pcl_nwn_qbb)指示多线程处理不适用于Data Collector Edge管道。

当功能内有限制时，这些限制会在功能文档中说明。例如，对于HTTP客户端来源，[批处理模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HTTPClient.html#concept_erx_tjp_fs)在Data Collector Edge管道中无效。

类似地，当属性对于管道类型无效时，在属性描述中会指出此限制。

如果未提及任何限制，则阶段，功能部件或属性的文档对于与父阶段或功能部件关联的所有管道类型均有效。例如，文件尾源的“ [第一个要处理的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/FileTail.html#concept_xrx_btm_b1b)文件”文档没有规定的限制，因为该信息对Origin [支持的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/FileTail.html#concept_n1y_qyp_5q)每种管道类型均有效。

该文档使用以下图标突出显示特定于特定管道类型的信息：

| 管道类型图标                                 | 描述                                  |
| :------------------------------------------- | :------------------------------------ |
| ![img](imgs/icon-SDC-20200310110249069.png)  | 与数据收集器管道有关的信息。          |
| ![img](imgs/icon-Edge-20200310110249082.png) | 与Data Collector Edge管道有关的信息。 |