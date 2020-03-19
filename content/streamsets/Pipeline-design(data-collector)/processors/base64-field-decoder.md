# Base64字段解码器

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310175211248.png) 资料收集器

Base64字段解码器将Base64编码的数据解码为二进制数据。在现场评估数据之前，请使用处理器对Base64编码的数据进行解码。

当配置Base64 Field Decoder时，您指定要解码的字节数组字段和将解码后的值传递到的目标字节数组字段。

## 配置Base64字段解码器

配置Base64字段解码器以将Base64编码的数据解码为二进制数据。处理器可以解码来自单个字节数组字段的数据。要解码其他字段，请将其他Base64字段解码器添加到管道中。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **Base64”**选项卡上，配置以下属性：

   | Base64字段解码器属性 | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | 要解码的字段         | 要解码的字节数组字段。                                       |
   | 目标领域             | 记录中的字节数组字段，用于解码的数据。您可以指定相同的字段，以将原始数据替换为解码后的数据。或者，您可以指定另一个现有字段或新字段。如果该字段不存在，Base64字段解码器将创建该字段。 |