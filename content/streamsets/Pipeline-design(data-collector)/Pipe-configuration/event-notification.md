# 通知事项

您可以将管道配置为在管道更改为指定状态时发送电子邮件或Webhook。

例如，当有人手动停止管道时，您可能会发送一封电子邮件，导致其转换为“已停止”状态。或者，当管道变为Start_Error或Run_Error状态时，您可能会发送Slack或文本消息。

![img](imgs/icon-Edge-20200310110706924.png)在Data Collector Edge管道中不可用。

当管道转换为以下状态时，您可以发送通知：

- 跑步
- Start_Error
- Run_Error
- Running_Error
- 已停止
- 已完成
- 断线
- 连接中
- Stop_Error

您可以指定多个状态来触发通知，但是此时您不能将管道配置为基于不同的管道状态更改发送不同的通知。例如，如果您为“正在运行”和“已完成”状态配置通知，则当管道同时更改为两种状态时，管道会发送通知。

但是，在配置Webhook时，可以在有效负载中使用webhook参数来指示触发通知的状态更改。有关webhooks的更多信息，请参见[Webhooks](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/Webhooks.html#concept_mp1_t3l_rz)。

要发送电子邮件通知， 必须将Data Collector配置为发送电子邮件。要启用发送电子邮件，请配置电子邮件警报数据收集器 属性。有关更多信息，请参阅[数据收集](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html)器 文档 中的[配置数据收集](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html)器。

有关管道状态的更多信息，请参阅[了解管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Maintenance/PipelineStates-Understanding.html#concept_s4p_ns5_nz)状态。