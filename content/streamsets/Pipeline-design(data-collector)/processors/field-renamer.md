# 字段重命名器

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310180327605.png) 资料收集器

使用字段重命名器可以重命名记录中的字段。您可以指定单个字段来重命名，也可以使用正则表达式来重命名字段集。

当源字段不存在，具有匹配名称的目标字段已经存在以及源字段与多个源字段表达式匹配时，可以配置行为。

未重命名或覆盖的字段将进入下一阶段。

## 重命名字段集

您可以使用正则表达式或regex以及StreamSets [表达式语言](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/ExpressionLanguage_overview.html#concept_p54_4kl_vq)来重命名字段集。您可以使用正则表达式来定义要重命名和定义目标字段名称的源字段集。您还可以使用StreamSets表达式语言来定义目标字段名称。

下面是一些如何使用表达式重命名字段集的示例：

- 删除前缀或后缀

  假设您要从一组字段中删除OPS前缀。您可以通过使用以下表达式定义要更改的源字段来执行此操作：`/'OPS(.*)'`然后使用以下表达式删除OPS前缀：`/$1`

  或者说，在使用Field Flattener处理器展平XML数据之后，所有字段的后缀均为.0.value。您可以通过使用以下表达式指定源字段名称来删除后缀：`/'(.*)\.0\.value'`

  然后使用以下表达式定义目标字段名称：`/$1`

- 删除特殊字符

  要从字段名称中删除特殊字符，可以对源字段名称使用以下表达式：`/'([A-Z a-z]*)[^a-z A-Z 0-9]([A-Z a-z 0-9]*)'`

  然后对目标字段名称使用以下表达式：`/$1_invalid_character_removed_$2`

- 更改大小写

  要将字段名称更改为全部大写，请对源字段名称使用以下表达式：`/(.*)`然后对目标字段名称使用以下表达式：`/${str:toUpper("$1")}`

  要将字段名称更改为全部小写，请对源字段名称使用以下表达式：`/(.*)`然后对目标字段名称使用以下表达式：`/${str:toLower("$1")}`

**注意：**要在字段名称中包含正则表达式特殊字符（例如管道符号（|）），请使用单引号引起来。例如，如果您有一个名为`tag|attr`的字段，请按如下所示输入字段名称：

```
/'tag|attr'
```

## 配置字段重命名器

配置字段重命名器以重命名记录中的字段。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **重命名”**选项卡上，配置以下属性：

   | 字段重命名器属性 | 描述                                                         |
   | :--------------- | :----------------------------------------------------------- |
   | 重命名字段       | 要重命名字段，请在“源字段表达式”中输入或选择要重命名的字段，然后在“目标字段表达式”中输入该字段的新名称。单击 **添加**以重命名其他字段。要重命名字段集，请[使用表达式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldRenamer.html#concept_ogb_bqf_lw)：您可以在两个属性中使用正则表达式。您可以在“目标字段表达式”属性中使用StreamSets表达式语言。要重命名数组或映射，可以指定单个数组索引或映射元素，也可以[使用星号通配符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/Expressions.html#concept_vqr_sqc_wr)表示所有数组索引和映射元素。您不能使用正则表达式来选择数组索引和映射元素的子集。例如，如果除法数组包含20个索引，则不能使用以下正则表达式重命名前10个索引的字段路径：`/Division[0-9]`**注意：**如果重命名列表或列表映射字段中的字段，处理器将在列表或列表映射字段的末尾列出重命名的字段。您可以使用“字段顺序”处理器对列表映射字段中的字段重新排序。 |
   | 源字段不存在     | 记录中不存在源字段时的行为：继续-继续处理记录，忽略缺少的源字段。发送到错误-根据为该阶段配置的错误处理来处理记录。 |
   | 目标领域已经存在 | 当记录包含与建议的目标字段匹配的字段名称时的行为：替换-将现有字段替换为重命名的字段。追加数字-将数字追加到重命名字段中的所有重复项。继续-继续处理记录，不更改现有字段。发送到错误-根据为该阶段配置的错误处理来处理记录。 |
   | 多个源字段匹配   | 一个源字段匹配多个源字段表达式时的行为：继续-继续处理记录，跳过具有多个匹配项的字段。发送到错误-根据为该阶段配置的错误处理来处理记录。 |