# 场类型转换器

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310180500404.png) 资料收集器

字段类型转换器处理器将字段的数据类型转换为兼容的数据类型。您可以在执行计算之前使用处理器转换字段的数据类型。您也可以使用处理器更改小数数据的小数位数。

您将处理器配置为使用以下方法之一转换数据类型：

- 按字段名称

  转换具有指定名称的字段的数据类型。例如，您可以将String数据类型的名为dropoff_datetime的字段转换为Date数据类型。

- 按数据类型

  用指定的类型转换所有字段的数据类型。例如，您可以将所有具有Decimal数据类型的字段转换为String数据类型。

您可以按字段名称或数据类型转换数据类型。您不能在同一阶段使用两种方法。

为适当的兼容数据类型配置转换。还应考虑该字段中的实际数据，因为即使有效的转换也可以截断数据。例如，将字段从整数转换为十进制是有效的。将字段从十进制转换为整数也是有效的，但是该转换可以截断数据中的任何十进制值。

**提示：**您可以使用数据预览来验证字段中的数据。

将字符串数据转换为日期，日期时间或时间数据类型时，或将日期，日期时间或时间数据转换为字符串数据类型时，都可以指定要使用的日期格式。您可以使用任何有效格式。

## 有效的类型转换

下表列出了可以转换为另一种数据类型的数据类型。列表，地图和列表地图数据类型无法转换。

| 目标数据类型 | 源数据类型                                                   |
| :----------- | :----------------------------------------------------------- |
| 布尔型       | 字节，十进制，双精度，浮点型，整数，长，短，字符串           |
| 字节         | 十进制，双精度，浮点数，整数，长，短，字符串                 |
| 字节数组     | 串                                                           |
| 字符         | 串                                                           |
| 日期         | 日期时间，长整数，字符串，时间                               |
| 约会时间     | 日期，长字符串                                               |
| 小数         | 字节，双精度，浮点型，整数，长，短，字符串                   |
| 双           | 字节，十进制，整数，浮点数，长，短，字符串                   |
| 浮动         | 字节，十进制，双精度，整数，长，短，字符串                   |
| 整数         | 布尔1，字节，十进制，双精度，浮点型，长，短，字符串          |
| 长           | 布尔1，字节，日期，日期时间，十进制，双精度，浮点型，整数，短整数，字符串 |
| 短           | 布尔1，字节，十进制，双精度，浮点数，整数，长整数，字符串    |
| 串           | 所有受支持的数据类型（列表，地图和列表地图除外）             |
| 时间         | 日期，日期时间，字符串，长                                   |
| 分区日期时间 | 日期，日期时间，字符串                                       |

1 从布尔数据类型转换时，处理器会将TRUE转换为1，将FALSE转换为0。

## 更改小数字段的小数位数

您可以使用字段类型转换器处理器来更改小数字段的小数位数。例如，您可能有一个小数字段，其值是12345.6789115，并且您想将小数位减小到4，以便该值是12345.6789。

要更改比例，可以将处理器配置为将十进制字段转换为Decimal数据类型，并指定要使用的比例。减小比例时，还可以指定舍入策略。例如，您可以将处理器配置为向上舍入或向下舍入。

您可以按名称更改小数字段的小数位数。或者，您可以使用“十进制”数据类型更改所有字段的比例。

## 配置字段类型转换器

配置字段类型转换器处理器以转换字段的数据类型。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **转换”**选项卡上，配置以下属性：

   | 字段类型转换器属性                                           | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 转换方式                                                     | 指定是按字段名称还是按数据类型转换数据类型。                 |
   | 要转换的字段                                                 | 一个或多个要转换为相同数据类型的字段。仅在按字段名称转换时使用。您可以使用星号通配符表示 [数组索引和映射元素](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/Expressions.html#concept_vqr_sqc_wr)。您可以指定单个字段，也可以使用[字段路径表达式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/Expressions.html#concept_ir4_rxt_3cb)指定一组字段。 |
   | 来源类型                                                     | 要转换的字段的数据类型。仅在按数据类型转换时使用。           |
   | [转换为类型](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldTypeConverter.html#concept_ixz_s5q_wq) | 转换的数据类型。选择一个有效的类型。                         |
   | 数据语言环境                                                 | 字段数据的语言环境。可以确定处理器如何格式化转换后的数据，例如使用逗号或句点作为小数点分隔符。适用于受语言环境影响的类型。 |
   | 将输入字段视为日期                                           | 将长字段转换为String数据类型时，将输入字段视为日期时间。选择何时要将长字段中的时间戳（例如时期或UNIX时间）转换为字符串，例如“ 2017-02-01 12:00:00”。处理器首先将long值转换为日期时间，然后使用指定的日期格式转换为字符串。清除后，处理器会将一个长值（例如1485979200）转换为字符串值“ 1485979200”。 |
   | [规模](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldTypeConverter.html#concept_sym_c4g_xx) | 转换为十进制数据类型时要缩放。输入零或正数以指示小数点右边的位数。如果输入负数，则处理器将数字的未标度值乘以10到标度取反的幂。有关指定比例的更多信息，请参见https://docs.oracle.com/javase/8/docs/api/java/math/BigDecimal.html。 |
   | 四舍五入策略                                                 | 在小数位数转换期间使用的舍入策略。有关每种舍入策略的描述，请参见https://docs.oracle.com/javase/8/docs/api/java/math/BigDecimal.html。 |
   | 日期格式                                                     | 要转换的日期，日期时间或时间数据的格式。用于将不带时区或UTC偏移量详细信息的日期时间数据转换为日期，日期时间或时间。或者将日期，日期时间或时间数据转换为String。选择要使用的格式或创建自定义格式。要使用时区或偏移量信息转换日期时间数据，请使用“分区日期时间格式”属性。**注意：** 数据预览使用浏览器语言环境的默认格式显示日期，日期时间和时间数据。例如，如果浏览器使用en_US语言环境，则预览将使用以下格式显示日期：MMM d，yh：mm：ss a。 |
   | 其他日期格式                                                 | 用于输入自定义日期格式。有关创建自定义日期格式的更多信息，请参见[Oracle Java文档](https://docs.oracle.com/javase/tutorial/i18n/format/simpleDateFormat.html)。 |
   | 分区日期时间格式                                             | 要转换的日期，日期时间或时间数据的格式。用于将带有时区或偏移量信息的日期时间数据转换为“区域日期时间”格式，或将“区域日期时间”数据转换为字符串。选择以下选项之一：yyyy-MM-dd'T'HH：mm：ssX-用于具有UTC偏移量的日期时间值。yyyy-MM-dd'T'HH：mm：ssX [VV]-用于具有UTC偏移量和时区的日期时间值。如果datetime值不包含UTC偏移量，则阶段将使用指定时间标记的最小偏移量。其他-用于输入其他区域日期时间格式。若要转换不带时区或偏移量信息的日期时间数据，请使用“日期格式”属性。 |
   | 其他分区的日期时间格式                                       | 用于输入自定义分区的日期时间格式。有关创建自定义分区日期时间格式的更多信息，请参见[Oracle Java文档](https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html)。 |
   | 字符集                                                       | 要转换的数据的字符编码。适用于受编码影响的类型。             |

3. 要配置其他类型转换，请单击“ **添加”** 图标，然后重复上一步。您可以使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)来配置其他转化。

   您可以按字段名称配置其他转换，也可以按数据类型配置其他转换。您不能在同一阶段使用两种方法。