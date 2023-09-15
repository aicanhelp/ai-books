- # 事件产生

  当管道启动和停止时，事件框架会为Data Collector独立管道生成管道事件。

  ![img](imgs/icon-Edge-20200310110557075.png)在Data Collector Edge管道中不可用。

  在Data Collector 独立管道中，您可以将管道事件传递给执行程序或另一个管道以进行其他处理。默认情况下，这些事件被丢弃。有关管道事件的更多信息，请参见[管道事件生成](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_amg_2qr_t1b)。

  有关事件框架的一般信息，请参见“ [数据流触发器概述”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

  ## 管道事件记录

  管道事件记录具有以下与事件相关的记录头属性。记录标题属性存储为字符串值：

  | 记录标题属性                 | 描述                                                         |
  | :--------------------------- | :----------------------------------------------------------- |
  | sdc.event.type               | 事件类型。使用以下类型之一：pipeline-start-在管道启动时生成。pipeline-stop-在管道停止时生成。 |
  | sdc.event.version            | 整数，指示事件记录类型的版本。                               |
  | sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

  事件框架生成以下类型的管道事件：

  - 管道启动

    事件框架在管道初始化之后，紧随其开始之后以及初始化各个阶段之前都会生成启动事件。

    开始事件记录已`sdc.event.type`设置为 `pipeline-start`，并且包含以下字段：管道开始事件字段描述pipelineId启动的管道的ID。pipelineTitle用户定义的启动管道的名称。用户启动管道的用户。参数启动管道时使用的任何参数。

  - 流水线停止

    当管道停止时，事件框架会手动，以编程方式或由于故障而生成停止事件。在所有阶段都已完成处理和清理临时资源（例如删除临时文件）之后，将生成stop事件。

    停止事件记录已`sdc.event.type`设置为 `pipeline-stop`，并且包含以下字段：

    管道停止事件字段描述pipelineId停止的管道的ID。pipelineTitle停止的管道的用户定义名称。原因管道停止的原因。可以设置为以下原因：错误-管道正在运行时发生错误。已完成-管道完成了所有预期的处理。用户-用户停止了管道。用户相关时停止管道的用户。