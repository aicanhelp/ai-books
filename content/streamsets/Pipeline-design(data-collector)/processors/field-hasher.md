# 野战哈塞尔

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310175853491.png) 资料收集器

Field Hasher处理器使用一种算法对数据进行编码。使用处理器对高度敏感的数据进行编码。例如，您可以使用Field Hasher处理器对社会安全号或信用卡号进行编码。

字段哈希器提供了几种对单个字段或整个记录进行散列的方法。您可以哈希可以转换为字符串的任何字段。产生的哈希是一个字符串值。

您可以将Field Hasher处理器配置为使用MD5，SHA1，SHA-256，SHA-512或MurmurHash3 128来哈希字段值。您可以选择在哈希之前将单个字段分隔符添加到字段中。

## 哈希方法

Field Hasher提供了几种散列数据的方法。当您对字段进行一次以上的哈希处理时，Field Hasher会在生成下一个哈希表时使用现有的哈希表。

字段哈希按以下顺序哈希。使用多种哈希方法时，请注意顺序会影响数据的哈希方式：

1. 散列

    -Field Hasher用散列值替换字段中的原始数据。

   您可以使用相同的算法指定多个要散列的字段。您还可以使用不同的算法来哈希不同的字段集。

2. 哈希到目标

    -字段哈希器对字段中的数据进行哈希处理并将其写入指定的字段，标头属性或两者。它将原始数据保留在原位。

   如果指定的目标字段或属性不存在，Field Hasher会创建它。

   如果您指定使用相同算法对多个字段进行哈希处理，则“字段哈希”会将字段哈希在一起。

   如果已对任何字段进行哈希处理，则字段哈希器将使用现有哈希值来生成新的哈希值。

3. 哈希记录

    -字段哈希器对记录进行哈希处理并将其写入指定的字段，标头属性或两者。您可以在哈希中包含记录标题。

   如果指定的目标字段或属性不存在，Field Hasher会创建它。

   如果记录包含已被哈希的字段，则哈希字段在记录哈希时将使用哈希值。

## 现场分离器

您可以配置Field Hasher处理器在所有要散列的所有字段的末尾添加一个字段分隔符。当您将多个字段哈希到单个字段或哈希整个记录时，可能需要添加字段分隔符。

当您使用字段分隔符时，Field Hasher处理器将字符添加到要哈希的每个字段的末尾，然后再对其进行哈希处理，因此字段分隔符将与该字段一起进行哈希处理。注意，由于将字段分隔符添加到每个字段，因此一组字段中的最后一个字段或记录中的最后一个字段在哈希中也包含字段分隔符。

启用使用字段分隔符后，可以选择字符选项之一-Tab，分号，逗号和空格-或选择Other并输入任何UTF-8字符的代码。

## 列表，地图和列表地图字段

Field Hasher不会哈希列表，地图或列表映射字段，但是可以哈希列表，地图和列表映射字段中的字段数据。要对列表，地图或列表地图字段中的数据进行哈希处理，请选择包含要哈希的实际数据的字段。

对整个记录进行哈希处理时，Field Hasher会对列表，地图和列表地图字段中的数据进行哈希处理。

## 配置现场哈希器

配置现场哈希器以编码敏感数据。您可以哈希整个记录或特定字段。您还可以将字段哈希在一起到目标字段或属性标题。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 要对字段进行哈希处理，请单击“ **哈希字段”**选项卡，并可以选择配置字段分隔符：

   | 哈希字段属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 现场分离器 [![img](imgs/icon_moreInfo-20200310175853990.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldHasher.html#concept_pmb_sws_f2b) | 用作字段分隔符的单个字符。已配置的字段分隔符在哈希之前添加到所有字段的末尾。选择以下选项之一：标签分号逗号空间其他选择其他时，输入要使用的UTF-8字符的字符代码。 |

3. 若要对字段进行哈希处理，请为要使用的每种哈希类型配置以下“就地**哈希”**属性。单击 **添加**以使用其他哈希类型。

   | 散列属性[![img](imgs/icon_moreInfo-20200310175853990.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldHasher.html#concept_ssq_5jb_mv) | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 哈希字段                                                     | 一个或多个要使用相同哈希类型进行哈希的字段。您可以指定单个字段，也可以使用[字段路径表达式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/Expressions.html#concept_ir4_rxt_3cb)指定一组字段。 |
   | 哈希类型                                                     | 用于哈希字段值的算法：MD5-产生一个128位（16字节）的哈希值，通常以文本格式表示为32位十六进制数。SHA1-产生一个160位（20字节）的哈希值。SHA256-产生256位（32字节）的哈希值。SHA512-产生512位（64字节）的哈希值。MURMUR3_128-产生一个128位（16字节）的哈希值。 |

4. 要将一个或多个字段哈希在一起并将它们写入字段或属性标题，请配置以下“ **哈希到目标”** 属性。单击**添加**以哈希其他字段。

   | 哈希到目标属性[![img](imgs/icon_moreInfo-20200310175853990.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldHasher.html#concept_ssq_5jb_mv) | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 哈希字段                                                     | 一个或多个要散列并写入目标字段或标头属性的字段。如果您输入多个字段，处理器将它们哈希在一起。您可以指定单个字段，也可以使用[字段路径表达式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/Expressions.html#concept_ir4_rxt_3cb)指定一组字段。 |
   | 哈希类型                                                     | 用于哈希字段值的算法：MD5-产生一个128位（16字节）的哈希值，通常以文本格式表示为32位十六进制数。SHA1-产生一个160位（20字节）的哈希值。SHA256-产生256位（32字节）的哈希值。SHA512-产生512位（64字节）的哈希值。MURMUR3_128-产生一个128位（16字节）的哈希值。 |
   | 目标领域                                                     | 记录中用于哈希数据的字段。如果该字段不存在，则“字段哈希器”将创建该字段。 |
   | 标头属性                                                     | 记录标题中的属性，用于哈希数据。如果属性不存在，Field Hasher会创建该属性。 |

5. 要配置字段级错误处理，请在“ **哈希字段”**选项卡上配置以下属性：

   | 字段错误处理属性 | 描述                                                         |
   | :--------------- | :----------------------------------------------------------- |
   | 现场问题         | 确定记录中缺少要散列的指定字段，包含空值或者是List，Map或List-Map数据类型时要采取的措施：包括但不进行处理-从记录中删除目标字段并继续进行处理。发送到错误-将记录传递到管道以进行错误处理。 |

6. 要对整个记录进行**哈希处理**，请在“ **哈希记录”**选项卡上配置以下属性：

   | 哈希记录属性[![img](imgs/icon_moreInfo-20200310175853990.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldHasher.html#concept_ssq_5jb_mv) | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 哈希全记录                                                   | 散列整个记录，并将其写入目标字段，属性标头或两者。           |
   | 包括记录标题                                                 | 在哈希中包含记录头。                                         |
   | 现场分离器[![img](imgs/icon_moreInfo-20200310175853990.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldHasher.html#concept_pmb_sws_f2b) | 用作字段分隔符的单个字符。已配置的字段分隔符在哈希之前添加到所有字段的末尾。选择以下选项之一：标签分号逗号空间其他选择其他时，输入要使用的UTF-8字符的字符代码。 |
   | 目标领域                                                     | 记录中用于哈希数据的字段。如果该字段不存在，则“字段哈希器”将创建该字段。 |
   | 标头属性                                                     | 记录标题中的属性，用于哈希数据。如果属性不存在，Field Hasher会创建该属性。 |