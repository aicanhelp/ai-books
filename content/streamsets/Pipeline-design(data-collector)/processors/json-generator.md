# JSON生成器

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310181123968.png) 资料收集器

JSON生成器将字段中的数据序列化为JSON编码的字符串。您可以从“列表”，“地图”或“列表地图”字段中序列化数据。

配置处理器时，请选择要转换的字段以及向其写入JSON字符串数据的字段。

如果目标字段存在，则JSON Generator会覆盖该字段中的数据。如果数据不存在，JSON生成器将创建该字段。

## 配置JSON生成器



配置JSON生成器以将字段中的数据序列化为字符串字段中的JSON数据。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **解析”**选项卡上，配置以下属性：

   | JSON生成器属性 | 描述                                                         |
   | :------------- | :----------------------------------------------------------- |
   | 要序列化的字段 | 包含要处理的数据的字段。选择一个列表，地图或列表地图字段。   |
   | 目标领域       | 将结果JSON数据写入的字段。如果指定现有字段，则现有数据将被覆盖。如果指定新字段，则处理器将创建该字段。 |