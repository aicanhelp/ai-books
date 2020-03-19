# 旋转轴

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310180302245.png) 资料收集器

Field Pivoter在列表，地图或列表地图字段中旋转数据，并为该字段中的每个项目创建一条记录。

Field Pivoter处理器将数据旋转到一个字段中。若要透视其他字段或嵌套结构，请使用其他Field Pivoter。

配置“字段数据透视器”时，可以指定要旋转的列表，地图或列表地图字段，以及在何处写入数据透视表。默认情况下，处理器将数据透视表写入原始字段，但是您可以为数据指定另一个字段。

您还可以指定是否在结果记录中包括现有字段，并配置在字段不存在时要执行的操作。

## 生成的记录

当您旋转字段时，“字段旋转器”会为列表或地图中的每个第一级项目创建一个新记录。若要透视其他字段或嵌套结构，请使用其他Field Pivoter。

透视字段时，可以仅使用新记录中的透视数据，将现有字段包括在记录中或将其删除。您可以在原始字段或其他字段中指定将透视数据写入何处。您还可以指定是否在透视字段中保存第一级项目的字段名称。

例如，假设您要在以下记录集中透视Color_List数据，以便可以稍后在管道中根据颜色更新单位成本：

- 传入数据

  `PEN_TYPE``COLOR_LIST``UNIT_COST``ballpoint``black``blue``red`。`10``highlighter``light blue``yellow`。`20``felt tip``black`。`15`

- 转到现有字段，包括现有数据

  如果使用默认的字段处理器将Color_List字段中的列表旋转到同一字段并包括现有字段，则Field Pivoter会使用以下数据透视表覆盖Color_List字段中的列表：`PEN_TYPE``COLOR_LIST``UNIT_COST``ballpoint``black`。`10``ballpoint``blue`。`10``ballpoint``red`。`10``highlighter``light blue`。`20``highlighter``yellow`。`20``felt tip``black`。`15`

- 转到新字段，包括现有数据

  如果将处理器配置为将列表旋转到新的“颜色”字段并包括现有记录，则“ Field Pivoter”将生成以下记录：`PEN_TYPE``COLOR_LIST``COLOR``UNIT_COST``ballpoint``black``blue``red``black`。`10``ballpoint``black``blue``red``blue`。`10``ballpoint``black``blue``red``red`。`10``highlighter``light blue``yellow``light blue`。`20``highlighter``light blue``yellow``yellow`。`20``felt tip``black``black`。`15`

- 转到新字段，包括现有数据，并在透视字段中包括第一级项目的字段名称

  您可以在新记录的透视字段中包括第一级项目的字段名称。例如，假设Color_List字段按如下所示命名透视字段中的第一级项目：`value_*n*``  "Color_List": {      "value_1": "black",      "value_2": "blue",      "value_3": "red"    }`

  您可以将字段名称作为字段包含在新记录中。如果将处理器配置为将列表旋转到新的Color_Value字段，包括现有记录，并在Color_FieldName字段中包括第一级项目的字段名称，则Field Pivoter会生成以下记录：`value_*n*``PEN_TYPE``COLOR_LIST``COLOR_VALUE``COLOR_FIELDNAME``UNIT_COST``ballpoint``black``blue``red``black``value_1`。`10``ballpoint``black``blue``red``blue``value_2`。`10``ballpoint``black``blue``red``red``value_3`。`10``highlighter``light blue``yellow``light blue``value_1`。`20``highlighter``light blue``yellow``yellow``value_2`。`20``felt tip``black``black``value_1`。`15`

- 转到新字段，删除现有数据

  如果在不包括现有字段的情况下将数据透视图转到新的“颜色”字段，则“ Field Pivoter”将仅使用“颜色”字段生成记录。此选择在此示例中没有意义，但是在旋转嵌套列表或地图或计划在下游丰富数据时可能很有用：`COLOR``black``blue``red``light blue``yellow``black`

## 配置场轴

配置Field Pivoter，以透视列表，地图或列表地图字段中的数据，并为该字段中的每个项目生成一条记录。



1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **字段数据透视”**选项卡上，配置以下属性：

   | 旋转轴属性                                                   | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 旋转轴                                                       | 列表，地图或列表地图字段以进行透视。                         |
   | 复制所有字段 [![img](imgs/icon_moreInfo-20200310180303042.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/ListPivoter.html#concept_efn_wgw_tw) | 在生成的记录中包括所有现有字段。                             |
   | 枢纽项目路径                                                 | 写入透视数据的字段路径。如果该字段存在，则处理器将覆盖该字段中的所有数据。**注意：**不使用时，处理器会将数据透视表写入原始字段。 |
   | 保存原始字段名称                                             | 指定是否将第一级项目的字段名称保存在透视字段中。当您在生成的记录中包含所有现有字段时，可以保存原始字段名称。 |
   | 原始字段名称路径                                             | 字段路径，用于在透视字段中写入第一级项目的字段名称。         |
   | 字段不存在                                                   | 如果记录不包含要旋转的指定字段，则要采取的操作。             |