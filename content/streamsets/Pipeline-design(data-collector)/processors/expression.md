# 表达评估器

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310175707700.png) 资料收集器![img](imgs/icon-Edge-20200310175707700.png) 数据收集器边缘

表达式计算器执行计算，并将结果写入新的或现有的字段。您也可以使用表达式计算器来添加或修改记录标题属性和字段属性。

若要创建表达式，请定义字段名称，记录标题属性或字段属性以接收表达式的结果。然后，使用表达式语言来定义要使用的表达式。

您可以在表达式中使用运行时参数。当您要在启动管道时为管道属性指定值时，请定义运行时参数。

您还可以使用time：now（）函数将Data Collector 服务器时间包括在记录中。

## 输出字段和属性

配置表达式时，表达式计算器会将表达式的结果写入输出字段或属性。您可以使用现有的字段或属性，也可以创建一个新的字段或属性。

当您使用现有字段或属性时，表达式计算器将输入值替换为新值。当您使用新的字段或属性时，表达式计算器将其添加到记录中并传递表达式的结果。

## 记录标题属性表达式

您可以使用表达式来添加或修改记录的标题属性。

例如，您可以使用Expression Evaluator设置sdc.operation.type属性以允许写入MongoDB。

某些目标可以使用记录头属性来执行基于记录的写入。有关更多信息，请参见[基于记录的写入的记录头属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_lmn_gdc_1w)。有关记录标题属性的一般信息，请参见“ [记录标题属性”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)。

## 字段属性表达式

您可以使用表达式来添加或修改记录的字段属性。例如，您可以基于记录数据创建字段属性，然后将记录传递到基于该属性值路由数据的流选择器。

您还可以在字段表达式中使用字段属性功能，以在记录中包括字段属性值。

有关字段属性的更多信息，请参见[字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)。有关字段属性函数的更多信息，请参见[记录函数](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/Functions.html#concept_p1z_ggv_1r)。

## 配置表达式计算器

配置表达式计算器以逐条记录地执行计算。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 要配置字段表达式，请单击“ **表达式”**选项卡并配置以下信息：

   | 字段表达属性 | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 输出场       | 该字段将表达式的结果传递到下一个阶段。输入新的或现有字段的名称，如下所示：/ FieldName。如果使用现有字段，则表达式计算器将替换现有值。您可以使用星号通配符表示 [数组索引和映射元素](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/Expressions.html#concept_vqr_sqc_wr)。 |
   | 表达         | 要评估的表达式。（可选）单击**Ctrl +空格键**以帮助创建表达式。 |

3. 使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击**添加**图标以添加其他字段表达式。

4. 要配置记录头属性表达式，请配置以下信息：

   | 记录标题属性表达式属性                                       | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 标头属性 [![img](imgs/icon_moreInfo-20200310175708174.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Expression.html#concept_qf3_mfq_f5) | 记录头属性，用于将表达式的结果传递到下一个阶段。输入新的或现有属性的名称，如下所示：<属性名称>。使用现有属性时，表达式计算器将替换现有值。**注意：**避免更改由Data Collector生成的标头属性的值。 |
   | 表达                                                         | 要评估的表达式。（可选）单击**Ctrl +空格键**以帮助创建表达式。 |

5. 使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击**添加**图标以添加其他标题属性表达式。

6. 要配置字段属性表达式，请配置以下信息：

   | 字段属性表达式属性                                           | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 栏位属性[![img](imgs/icon_moreInfo-20200310175708174.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/Expression.html#concept_mb2_fsp_1z) | 将表达式的结果传递到下一个阶段的field属性。输入新的或现有属性的名称，如下所示：<属性名称>。使用现有属性时，表达式计算器将替换现有值。 |
   | 表达                                                         | 要评估的表达式。（可选）单击**Ctrl +空格键**以帮助创建表达式。 |

7. 使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击**添加**图标以添加其他字段属性表达式。