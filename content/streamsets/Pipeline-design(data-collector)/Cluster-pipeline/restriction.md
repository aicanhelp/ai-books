# 集群管道限制

请注意群集管道中的以下限制：

- 非群集起源-不要在群集管道中使用非群集起源。有关要使用的来源的说明，请参阅“ [群集批处理和流执行模式”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/ClusterPipelines.html#concept_rjc_4m5_lx)。

- 管道事件-您不能在集群管道中使用管道事件。

- 记录重复数据删除器处理器-群集管道目前不支持此处理器。

- RabbitMQ Producer目标-当前集群管道中不支持此目标。

- 脚本处理器-状态对象仅可用于定义它的处理器阶段的实例。如果管道以集群模式执行，则状态对象不会在节点之间共享。

- Spark Evaluator处理器-仅在群集流管道中使用。不要在群集批处理管道中使用。您也可以在独立管道中使用Spark Evaluator。

- Spark Evaluator处理器和Spark执行器-使用Spark阶段时，这些阶段必须使用与集群相同的Spark版本。例如，如果集群使用Spark 2.1，则Spark Evaluator必须使用Spark 2.1阶段库。

  这两个阶段在几个CDH和MapR阶段库中都可用。要验证舞台库包含的Spark版本，请参阅CDH或MapR文档。有关包含Spark Evaluator的阶段库的更多信息，请参阅Data Collector 文档 中的[Available Stage Libraries](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Installation/AddtionalStageLibs.html%23concept_evs_xkm_s5)。