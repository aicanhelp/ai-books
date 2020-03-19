# 为管道创建作业

管道是数据流的设计。作业是数据流的执行。

作业定义要运行的管道以及运行管道的Data Collector或Edge Data Collector（SDC Edge）。创建作业时，您可以指定要运行的已发布管道版本，并为该作业选择标签。标签指示应运行哪一组Data Collector或Edge Data Collector。

当您启动包含独立管道或集群管道的作业时，Control Hub 在具有匹配标签的Data Collector上运行远程管道实例。当您启动包含边缘管道的作业时，Control Hub会 在具有匹配标签的Edge Data Collector上运行远程管道实例。

创建包含带有运行时参数的管道的作业时，可以将其指定为[作业模板](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Jobs/JobTemplates.html#concept_bkh_nzb_4fb)。作业模板使您可以从单个作业定义中使用不同的运行时参数值来运行多个作业实例。

有关作业的更多信息，请参见[作业概述](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Jobs/Jobs.html#concept_omz_yn1_4w)。

在管道视图中，您可以同时为单个管道或多个管道创建作业。

1. 在导航面板中，单击**管道存储库**> **管道**。

2. 要为单个管道创建作业，请将鼠标悬停在要为其创建作业的管道上，然后单击管道旁边的“ **创建作业”**图标（![img](imgs/icon_CreateJob-20200310104649914.png)）。

   或要为多个管道创建作业，请在列表中选择多个管道，然后单击管道列表顶部的“ **创建作业”**图标（![img](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Graphic/icon_CreateJob.png)）。

3. 在“ **添加作业”**窗口上，配置以下属性：

   | 工作性质                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 职务名称                                                     | 工作名称。                                                   |
   | 描述                                                         | 作业的可选说明。                                             |
   | 管道                                                         | 您要运行的已发布管道。                                       |
   | 管道提交/标记                                                | 分配给要运行的已发布管道版本的管道提交或管道标签。您可以为任何已发布的管道版本创建作业。默认情况下，Control Hub显示最新发布的管道版本。 |
   | [标签](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Labels/Labels.html#concept_lxv_zhf_gw) | 确定运行管道的执行引擎组的一个或多个标签。                   |
   | [启用工作模板](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Jobs/JobTemplates.html#concept_bkh_nzb_4fb) | 使作业能够用作作业模板。作业模板使您可以从单个作业定义中使用不同的运行时参数值来运行多个作业实例。仅对包含使用运行时参数的管道的作业启用。**注意：**您只能使作业在创建作业时就可以用作作业模板。您无法将现有作业用作作业模板。 |
   | 统计刷新间隔（毫秒）                                         | 在监视作业时自动刷新统计信息之前需要等待的毫秒数。最小值和默认值为60,000毫秒。 |
   | [启用时间序列分析](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Jobs/Jobs-Monitoring.html#concept_b2z_hzn_3db) | 使Control Hub可以存储时间序列数据，您可以在监视作业时进行分析。禁用时间序列分析后，您仍然可以查看作业的总记录数和吞吐量，但是无法查看一段时间内的数据。例如，您无法查看最近五分钟或最后一小时的记录计数。 |
   | [实例数](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Jobs/Jobs-PipelineInstances.html#concept_abz_mkl_rz) | 要为该作业运行的管道实例数。仅在将管道设计为横向扩展时才增加该值。默认值为1，它在运行最少管道的可用Data Collector或SDC Edge上运行一个管道实例。可用的 Data Collector或SDC Edge包括分配了为作业指定的所有标签的任何组件。仅适用于Data Collector或SDC Edge作业。 |
   | [启用故障转移](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Jobs/PipelineFailover.html#concept_oht_krp_qz) | 当运行管道的原始引擎关闭或管道遇到Run_Error或Start_Error状态时，使Control Hub可以在另一个可用的执行引擎上重新启动失败的管道。默认设置为禁用。仅适用于Data Collector或SDC Edge作业。 |
   | [每个数据收集器的故障转移重试](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Jobs/PipelineFailover.html#concept_i1l_4vj_jfb) | 尝试对每个可用的Data Collector进行管道故障转移重试的最大次数。 仅当管道转换到错误状态时，控制中心才会增加故障转移重试次数并应用重试限制。如果 运行管道的 Data Collector关闭，则始终会发生故障转移，并且 Control Hub 不会增加故障转移重试次数。使用-1无限期重试。使用0尝试零重试。 |
   | 管道强制停止超时                                             | 强制停止远程管道实例之前要等待的毫秒数。在某些情况下，当您停止作业时，远程管道实例可以保持“正在停止”状态。例如，如果管道中的脚本处理器包括具有定时等待或无限循环的代码，则管道将一直处于“停止”状态，直到强制停止为止。默认值为120,000毫秒，即2分钟。 |
   | 阅读政策                                                     | 用于作业的读[保护策略](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/ProtectionPolicies/ProtectionPolicies-Overview.html#concept_dgd_ghb_v2b)。为作业选择适当的读取策略。仅适用于[Data Protector](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/DataProtector/DataProtector-Overview.html#concept_ws1_w2b_v2b)。 |
   | 写政策                                                       | 用于作业的写[保护策略](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/ProtectionPolicies/ProtectionPolicies-Overview.html#concept_dgd_ghb_v2b)。为作业选择适当的写入策略。仅适用于[Data Protector](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/DataProtector/DataProtector-Overview.html#concept_ws1_w2b_v2b)。 |
   | [运行时参数](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Jobs/RuntimeParameters.html#concept_dwq_33w_vz) | 用于启动管道实例的运行时参数值。覆盖为管道定义的默认参数值。单击“ **获取默认参数”**以显示管道中定义的参数和默认值，然后覆盖默认值。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)配置参数值。在批量编辑模式下，以JSON格式配置参数值。 |
   | 添加到拓扑                                                   | 要添加作业的拓扑。选择以下选项之一：**无** -不添加到拓扑。您可以在创建作业后将作业添加到拓扑中。**默认拓扑** -添加到名为默认拓扑的新拓扑中。Control Hub将创建默认拓扑并将作业添加到其中。您可以根据需要重命名拓扑。现有拓扑。添加到现有拓扑。 |

4. 如果为单个管道创建作业，请单击“ **添加另一个”**为同一管道添加另一个作业。配置其他作业的属性。

   完成所有作业的配置后，单击“ **保存”**。

5. 如果为多个管道创建作业，请单击“ **下一步”**为下一个作业配置属性。为每个选定的管道完成作业配置后，点击**创建**。

   Control Hub在作业视图中显示作业或作业模板。