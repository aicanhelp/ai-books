# XML拼合器

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310182435994.png) 资料收集器

XML Flattener处理器对嵌入在字符串字段中的格式良好的XML文档进行扁平化，并将扁平化后的数据作为附加字段或作为单个字段中的映射添加到记录中。

配置XML Flattener时，可以指定包含XML数据的字段。您可以指定记录定界符以从XML文档生成多个记录。指定记录定界符时，请在根元素下直接使用XML元素。

您可以配置处理器是将所有字段保留在原始记录中，还是仅保留展平的字段。

您还可以指定一个输出字段。定义输出字段时，处理器将展平的字段作为映射写入输出字段。您可以选择配置字符串，以在扁平化的字段名称中分隔实体名称和属性。

## 生成的记录

XML Flattener基于用户定义的记录定界符，从格式良好的XML文档中生成多个记录。分隔符指定用于创建记录的XML元素。在根元素下直接使用XML元素。

当未定义记录定界符时，处理器会将字段的全部内容读取为单个记录。

例如，字符串字段包含以下XML：

```
<contacts>
    <contact>
        <name type="maiden">NAME1</name>
        <phone>(111)111-1111</phone>
        <phone>(222)222-2222</phone>
    </contact>
    <contact>
        <name type="maiden">NAME2</name>
        <phone>(333)333-3333</phone>
        <phone>(444)444-4444</phone>      
    </contact>
</contacts>
```

如果将`contact`元素指定为记录定界符，则XML Flattener会创建两个记录。记录1包含以下字段：

```
contact.name: NAME1
contact.name#type: maiden
contact.phone(0): (111)111-1111
contact.phone(1): (222)222-2222
```

记录2包含以下字段：

```
contact.name: NAME2
contact.name#type: maiden
contact.phone(0): (333)333-3333
contact.phone(1): (444)444-4444
```

**注意：**当您配置处理器以将原始字段保留在传入记录中时，每个生成的记录也将包括原始字段。

如果未指定记录定界符，则XML Flattener会创建一条包含以下字段的记录：

```
contacts.contact(0).name: NAME1
contacts.contact(0).name#type: maiden
contacts.contact(0).phone(0): (111)111-1111
contacts.contact(0).phone(1): (222)222-2222
contacts.contact(1).name: NAME2
contacts.contact(1).name#type: maiden
contacts.contact(1).phone(0): (333)333-3333
contacts.contact(1).phone(1): (444)444-4444
```

## 配置XML Flattener

配置XML Flattener以展平嵌入在字符串字段中的XML数据。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“展**平”**选项卡上，配置以下属性：

   | 扁平化属性                                                   | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 展平场                                                       | 字符串字段，其中包含要展平的格式正确的XML文档。              |
   | 保留原始字段                                                 | 指定是否将所有字段保留在原始记录中。选中后，处理器将展平指定的字段并将所有其他字段保留在记录中。清除后，处理器将展平指定的字段并删除记录中的所有其他字段。要保留原始字段，记录的根字段必须是Map或List-Map。 |
   | 覆盖现有字段                                                 | 用与新的展平字段匹配的名称覆盖所有现有字段。将展平字段写入输出字段时，允许处理器覆盖现有字段。 |
   | 输出场                                                       | 指定要写入的展平字段的输出字段。您可以使用现有字段，也可以命名要创建的新字段。 |
   | 记录分隔符 [![img](imgs/icon_moreInfo-20200310182436461.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/XMLFlattener.html#concept_eqj_vgw_vv) | 指示用于生成记录的数据的XML元素。用于从XML文档创建多个记录。在根元素下直接使用XML元素。要将数据作为单个记录读取，请忽略此属性。 |
   | 字段定界符                                                   | 用于在扁平字段名称中分隔实体名称的字符串。例如，在以下拼合的字段名称中，句点（。）被定义为字段定界符：`contact.name=NAME1 contact.name#type=maiden`以下字符不能用作字段定界符： `[ ] ' " /`默认为期间。 |
   | 属性分隔符                                                   | 用于在扁平字段名称中分隔属性的字符串。例如，在以下扁平化的字段名称中，将井号（＃）定义为属性定界符：`contact.name#type=maiden`以下字符不能用作属性定界符： `[ ] ' " /`默认值为井号。 |
   | 忽略属性                                                     | 忽略为XML元素定义的属性。选择是否不想在扁平化字段中包含属性。 |
   | 忽略命名空间URI                                              | 忽略为XML元素定义的名称空间URI。选择是否不想在扁平化字段中包含名称空间URI。 |