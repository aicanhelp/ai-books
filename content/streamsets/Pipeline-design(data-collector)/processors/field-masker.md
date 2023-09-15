# 场掩蔽者

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310180032285.png) 资料收集器

字段掩码器根据选定的掩码类型来掩码字符串值。您可以使用可变长度，固定长度，自定义或正则表达式掩码。自定义掩码可以显示部分字符串值。

使用字段屏蔽器可屏蔽敏感的字符串数据。例如，您可以使用自定义掩码来掩码电话号码的后四位。

**提示：**要屏蔽非字符串数据，您可以使用Field Type Converter处理器将非字符串数据转换为String，然后将数据传递给Field Masker。

## 口罩类型

您可以使用以下掩码类型来掩码数据：

- 定长

  用固定长度的掩码替换值。当您要掩盖数据长度的变化时使用。

  下面的示例使用固定长度的掩码来隐藏密码：**原始密码****定长口罩**`1234``donKey``022367snowfall``asd302kd0``2v03msO3d``L92m1sN3q`

- 可变长度

  用可变长度掩码替换值。当您要显示原始数据的长度时使用。

  以下示例使用可变长度掩码来隐藏相同的密码：**原始密码****可变长度遮罩**`1234``donKey``022367snowfall``asd3``2v03ms``L92m1sN3q0jaOmE67Ws`

- 自订

  根据用户定义的模式用掩码替换值。在定义遮罩的图案时，可以使用井号（＃）来显示该位置的字符。所有其他字符在掩码中用作常量。

  以下示例`###-xxx-xxxx`用作掩蔽模式，在掩盖该号码的其余部分时显示电话号码的区号：**原始电话号码****自定义遮罩（###-xxx-xxxx）**`415-333-3434``301-999-0987``617-567-8888``415-xxx-xxxx``301-xxx-xxxx``617-xxx-xxxx`**提示：**为避免使掩蔽字符与真实数据混淆，请使用一个掩蔽字符而不是混合使用掩蔽字符。

  自定义掩码的长度是原始数据长度或掩码图案长度，以较小者为准。例如，您`###xx`用作遮罩图案，以显示三位数的邮政编码范围，同时遮盖其余邮政编码。遮罩图案长度为五个字符。当Field Masker将掩码应用于具有十个字符的原始邮政编码时，它将使用五个字符的最小长度，并删除原始邮政编码的后五个字符。当处理器将掩码应用于具有三个字符的原始邮政编码时，它将使用三个字符的最小长度，显示这三个字符，然后不对任何字符进行掩码，如下所示：

  **原始邮递区号****自定义遮罩（### xx）**`94105``94086-6161``80123``703``941xx``940xx``801xx``703`

- 正则表达式

  用可变长度掩码替换值组。您可以使用正则表达式定义数据结构，并使用括号定义值组。您可以选择指定不想屏蔽的任何数据组。如果未指定组，则“字段屏蔽器”将屏蔽所有值。

  例如，您使用以下正则表达式来描述将五位数代码附加到社会保险号的数据：`([0-9]{5}) - ([0-9]{3}-[0-9]{2}-[0-9]{4}) `

  括号会创建两组数据。如果将阶段配置为显示第一组，则掩码的结果可能如下所示：`Regex Mask 30529-xxx-xx-xxxx 10384-xxx-xx-xxxx 95833-xxx-xx-xxxx`

## 配置字段屏蔽器

配置一个Field Masker来屏蔽敏感数据。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **掩码”**选项卡上，配置以下属性：

   | 场掩蔽属性                                                   | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 要遮盖的字段                                                 | 一个或多个要用相同掩码类型掩码的String字段。您可以使用星号通配符表示 [数组索引和映射元素](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/Expressions.html#concept_vqr_sqc_wr)。您可以指定单个字段，也可以使用[字段路径表达式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/Expressions.html#concept_ir4_rxt_3cb)指定一组字段。 |
   | 口罩类型 [![img](imgs/icon_moreInfo-20200310180032543.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldMasker.html#concept_vwp_gh4_wq) | 遮罩类型以隐藏字段值。选择以下选项之一：固定长度-用固定长度的掩码替换值。可变长度-用掩码替换原始值的长度的值。自定义-用用户定义的掩码替换值。正则表达式-根据正则表达式定义的组和要显示的组替换值的组。 |
   | 定制面膜[![img](imgs/icon_moreInfo-20200310180032543.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldMasker.html#concept_vwp_gh4_wq) | 自定义遮罩的遮罩图案。输入您要使用的模式。使用井号（＃）在指定位置显示字符。使用其他任何字符作为掩蔽字符。 |
   | 正则表达式                                                   | 描述屏蔽字段中数据的正则表达式。如果要显示一组数据，请使用括号在模式中定义组。例如，[[0-9] {5}）-（[0-9] {3}-[0-9] {2}-[0-9] {4}）。 |
   | 要显示的群组                                                 | 要显示的可选的逗号分隔的组列表。使用1代表第一组。            |

3. 要掩盖另一个字段，请单击“ **添加”**图标，然后重复上一步。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来掩盖另一个字段。