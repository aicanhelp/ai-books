# Base64现场编码器

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310175248997.png) 资料收集器

Base64字段编码器使用Base64对二进制数据进行编码。使用处理器对必须在需要ASCII数据的通道上发送的二进制数据进行编码。

例如，您可以使用Base64编码将图像数据直接嵌入HTML源代码中。对数据进行编码时，可以防止将诸如“ <”和“>”之类的字符解释为标签。

当配置Base64 Field Encoder时，可以指定要编码的字节数组字段和将编码值传递到的目标字节数组字段。您还可以指定处理器对数据进行编码，以便可以安全地将其发送到URL中。

## 配置Base64字段编码器

配置Base64字段编码器以使用Base64编码二进制数据。处理器可以对来自单个字节数组字段的数据进行编码。要对其他字段进行编码，请将其他Base64字段编码器添加到管道中。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **Base64”**选项卡上，配置以下属性：

   | Base64字段编码器属性 | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | 要编码的字段         | 字节数组字段进行编码。                                       |
   | 目标领域             | 记录中的字节数组字段，用于编码数据。您可以指定相同的字段，以将原始二进制数据替换为编码数据。或者，您可以指定另一个现有字段或新字段。如果该字段不存在，Base64字段编码器将创建该字段。 |
   | 网址安全             | 指定是否对字段进行编码，以便可以安全地在URL中发送该字段。例如，当选择时，Base64 Field Encoder会确保编码的数据不包含正斜杠（/），因为它是URL保留字符。 |