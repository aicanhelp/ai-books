# 电子邮件执行器

电子邮件执行程序在收到事件后将配置的电子邮件发送给指定的收件人。您还可以将执行程序配置为根据条件（例如特定事件类型的到来）发送电子邮件。

您可以配置电子邮件执行程序以发送多封电子邮件，每封电子邮件都有其自身的条件，一组收件人和电子邮件。您可以在所有电子邮件字段中使用表达式。

在管道中使用电子邮件执行程序之前，必须启用数据收集器才能发送电子邮件。有关配置Data Collector 和其他配置通知的方式的更多信息，请参阅[发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/SendingEmail.html#concept_it1_wwg_xz)。

有关使用电子邮件执行程序的[案例研究](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

## 先决条件

在运行包含电子邮件执行程序的管道之前，请在数据收集器 配置文件$ SDC_CONF / sdc.properties中定义电子邮件警报属性。

有关使Data Collector能够发送电子邮件以及其发送电子邮件的其他方式的更多信息，请参阅“ [发送电子邮件”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/SendingEmail.html#concept_it1_wwg_xz)。

## 条件

您可以将电子邮件执行程序配置为根据条件（例如触发电子邮件的事件类型）发送电子邮件。如果您省略条件，则电子邮件执行程序在每次收到事件时都会发送电子邮件。

例如，假设您有一个使用JDBC查询使用者来源的管道。源生成几种类型的事件：一种用于查询成功，一种用于查询失败，另一种用于处理所有可用数据。要使“电子邮件”执行程序仅在查询失败时才发送电子邮件，请在电子邮件条件中使用事件类型，如下所示：

```
${record:eventType() == 'jdbc-query-failure'} 
```

事件类型存储在每个事件记录的记录标题属性中。表达式语言提供了`record:eventType()`返回事件记录的事件类型的功能。

要确定要在条件右侧使用的事件类型，请查看阶段的[“事件记录”文档](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/JDBCConsumer.html#concept_rzl_s1t_kz)。在这种情况下，jdbc-query-failure是JDBC查询使用者查询失败事件的事件类型。

**提示：**通过使用条件，您可以将多个事件流路由到同一电子邮件执行程序，并在执行程序中配置所有事件驱动的电子邮件通知。有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

## 使用表达式

您可以在电子邮件执行程序的任何电子邮件属性中使用表达式。例如，您可以将条件基于事件记录中的信息，并将有关管道的信息包括在电子邮件中。

您可以使用适用于您的用例的任何功能，但是这里有一些建议：

- 管道功能

  您可以使用管道功能来提供管道信息，例如管道名称和ID。例如，您可以在电子邮件中使用`pipeline:title()`和 `pipeline:id()`来指示生成事件和电子邮件的管道。当电子邮件配置为在管道整理器停止管道之后发送时，您可能会使用以下消息：`Heads up! ${pipeline:title()}, ${pipeline:id()}, has successfully completed.`

- 记录功能

  您可以使用记录功能来提供事件记录中的信息。例如，`record:eventType()`如果管道将多种类型的事件路由到电子邮件执行程序，并且只想在接收到特定事件类型时才发送电子邮件，则应在这种情况下使用。如果将执行程序与JDBC查询使用者一起使用，则可以使用以下条件在查询成功完成时发送电子邮件：`${record:eventType() == 'jdbc-query-success'}`您还可以使用该`record:eventCreation()`功能在邮件中包含事件发生的时间。创建时间以纪元时间返回，因此要创建可读的时间戳，可以使用以下表达式：`${time:millisecondsToDateTime(record:eventCreation() * 1000)}`当然，您可以使用该`record:value`功能包括事件记录中的信息，例如成功完成的查询。

- 文件功能

  您可以使用文件功能来提供有关已关闭或已写入文件的信息。

  例如，您可以使用该`file:fileName`函数从Hadoop FS文件关闭事件的文件路径字段中提取关闭文件的名称，如下所示：`${file:fileName(record:value('/filepath'))}`

## 配置电子邮件执行程序

配置电子邮件执行程序以在接收事件时发送电子邮件。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **电子邮件”**选项卡上，配置以下属性：

   | 电子邮件配置属性                                             | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 条件 [![img](imgs/icon_moreInfo-20200310203202567.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Email.html#concept_v4p_hh5_xz) | 何时发送电子邮件的可选条件。使用评估为true或false的条件。当条件评估为true时，执行程序将发送配置的电子邮件。如果不使用执行程序，则每次执行者收到事件时都会发送一封电子邮件。 |
   | 电邮编号                                                     | 要使用的电子邮件地址。单击 **添加**图标以添加其他收件人。    |
   | 电子邮件主题                                                 | 显示在电子邮件主题字段中的信息。                             |
   | 电子邮件内文                                                 | 显示在电子邮件正文中的信息。                                 |

   **注意：**您可以在所有电子邮件配置属性中使用表达式。有关更多信息，请参见[使用表达式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Executors/Email.html#concept_tgb_vbm_wz)。

3. 使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击**添加**图标以配置其他电子邮件。