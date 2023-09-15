# 现场合并

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310180122155.png) 资料收集器

字段合并将记录中的一个或多个字段合并到记录中的其他位置。仅用于具有列表或地图结构的记录。

配置字段合并时，请选择要合并的字段并定义目标字段。您可以将字段合并到新字段或现有字段中：

- 新目标领域

  定义新字段时，“字段合并”将创建新字段并将所选字段合并到新字段下。

  例如，您在JSON映射中选择以下字段：`/City /State`

  如果您创建一个名为的新目标字段`/Location`，则“字段合并”将如下合并这些字段：`/Location/City         /State`

- 现有目标领域

  当您选择现有字段作为目标字段时，“字段合并”会将所选字段合并到目标字段下。您可以选择覆盖同名的目标字段（如果已存在）。

## 配置字段合并

配置字段合并以合并或移动列表或地图记录中的字段。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **合并”**选项卡上，配置以下属性：

   | 字段合并属性 | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 源字段不存在 | 如果记录中不存在源字段，则采取以下措施：继续-为缺少的值传递null。发送到错误-将记录传递到管道以进行错误处理。 |
   | 覆盖字段     | 用与合并字段匹配的名称覆盖目标字段中的所有现有字段。         |
   | 合并领域     | 一个或多个要合并的字段。选择要合并的现有字段以及相应的目标字段。对于目标字段，请使用现有字段或输入要在合并之前创建的字段名称。 |