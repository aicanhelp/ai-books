# 延迟

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310175611850.png) 资料收集器![img](imgs/icon-Edge-20200310175611863.png) 数据收集器边缘

延迟处理器将批处理传递到管道的其余部分延迟指定的毫秒数。使用“延迟”处理器延迟流水线处理。

要限制一次可以处理的数据量，请指定“ [速率限制”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/PipelineRateLimit.html#concept_erj_qg4_qv)管道属性。

## 配置延迟处理器

配置延迟处理器以延迟将批处理传递到管道的下一个阶段。

在“ **常规”**选项卡上，配置以下属性：

| 一般财产                                                     | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 名称                                                         | 艺名。                                                       |
| 描述                                                         | 可选说明。                                                   |
| 批次之间的延迟                                               | 将每个批次传递到管道的其余部分之前要保留的毫秒数。           |
| [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
| [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
| [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |