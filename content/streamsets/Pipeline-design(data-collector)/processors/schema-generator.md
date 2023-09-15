# 模式生成器

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310181820805.png) 资料收集器

模式生成器处理器基于记录的结构生成模式，并将该模式写入记录头属性。模式生成器处理器此时将生成Avro模式。

当模式未知时，请使用模式生成器处理器生成基本模式。例如，在将记录写入目标系统之前，您可以在管道中使用处理器来生成最新版本的Avro模式。

**注意：**模式生成器处理器提供有限的自定义。如果您需要比处理器提供的更多定制功能，请考虑编写自己的模式生成器。

配置模式生成器处理器时，可以为Avro模式指定名称空间和描述。您可以指定架构字段是否应允许为空，以及架构字段是否应默认为null。您可以为大多数Avro基本类型指定默认值，并且可以允许处理器将较大的数据类型用于Avro类型，而无需直接等效。

您可以为精度指定名称，为十进制值指定比例属性。而且，您可以为没有该信息或无效的精度或小数位数的任何十进制字段配置默认精度和小数位数。

在适当的时候，您可以配置模式生成器以缓存许多模式，并根据“缓存键表达式”属性中定义的表达式将模式应用于记录。

## 使用avroSchema标头属性

默认情况下，模式生成器处理器将Avro模式写入avroSchema记录头属性。所有Avro处理来源也将传入记录的Avro模式写入avroSchema标头属性。另外，任何写入Avro数据的目标都可以使用avroSchema标头属性中的架构。

在处理Avro数据时，一种逻辑工作流程是在流水线中紧接目标之前添加模式生成器。这允许处理器在将数据写入目标系统之前生成新的Avro模式。

如果要保留avroSchema的早期版本，可以在模式生成器之前使用Expression Evaluator处理器，将avroSchema标头属性中的现有模式移动到其他标头属性，例如avroSchema_previous。

## 生成的Avro模式

架构生成器创建的Avro架构包括以下信息：

- 模式类型设置为“记录”。
- 基于“架构名称”属性的架构名称。
- 基于名称空间属性的名称空间（在配置时）。
- 配置时，基于Doc属性的doc字段中的架构描述。
- 字段名称映射，具有基于记录模式和阶段中定义的相关属性的相关属性，例如字段是否可以包含空值。

例如，将Name属性设置为MyAvroSchema并忽略可选的Namespace和Doc属性时，将生成以下Avro模式：

```
{"type":"record","name":"MyAvroSchema","namespace":"","doc":"","fields":[{"name":"name","type":["null","string"],"default":null},{"name":"id","type":["null","int"],"default":null},{"name":"instock","type":["null","boolean"],"default":false},{"name":"cost","type":["null",{"type":"bytes","logicalType":"decimal","precision":10,"scale":2}],"default":null}]}
```

此架构描述的记录包括以下字段：

- name-一个字符串字段。
- id-一个整数字段。
- 库存-布尔值字段。
- cost-一个十进制字段。

处理器配置为允许架构字段中为null，并使用null作为默认值。

## 缓存架构

您可以配置模式生成器以缓存许多模式，并根据“缓存键表达式”属性中定义的表达式将模式应用于记录。

当一组记录可以在逻辑上使用完全相同的架构，并且记录中包含可以用来确定要使用的架构的值时，缓存架构可以提高性能。

例如，假设您的管道使用JDBC Multitable Consumer来读取多个数据库表。源将用于生成每个记录的表的名称写入jdbc.tables记录头属性。假设每个记录中的所有数据都来自一个表。

要使用与每个记录关联的架构，可以按如下方式配置“缓存键表达式”属性：`${record:attribute(jdbc.tables)}`。

**警告：**请谨慎使用模式缓存-将错误的模式应用于记录会在写入目标系统时导致错误。

## 配置模式生成器

配置模式生成器处理器以为每个记录生成一个模式，并将该模式写入记录头属性。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **架构”**选项卡上，配置以下属性：

   | 架构属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 模式类型                                                     | 要生成的模式类型。处理器此时将生成Avro模式。                 |
   | 模式名称                                                     | 用于结果架构的名称。                                         |
   | 标头属性 [![img](imgs/icon_moreInfo-20200310181820820.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/SchemaGenerator.html#concept_zm3_ttp_1bb) | 包含结果模式的header属性。默认值为avroSchema。当您将目标的Avro Schema Location属性配置为使用In Record Header选项时，目标可以使用avroSchema标头属性来写入Avro数据。 |

3. 在“ **Avro”**选项卡上，配置以下属性：

   | Avro物业     | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 命名空间     | 在Avro模式中使用的命名空间。                                 |
   | 文件         | Avro模式的可选描述。                                         |
   | 可空字段     | 通过创建字段类型和null类型的并集，允许字段包含null值。默认情况下，字段不能包含空值。 |
   | 默认为空     | 在架构字段中允许使用空值时，请使用null作为所有字段的默认值。 |
   | 展开类型     | 当确切的等效项不可用时，允许将较大的Data Collector数据类型用于Avro数据类型。 |
   | 类型的默认值 | （可选）为Avro数据类型指定默认值。单击**添加**以配置默认值。默认值适用于指定数据类型的所有字段。您可以为以下Avro类型指定默认值：布尔型整数长浮动双串 |

4. 在“ **类型”**选项卡上，可以选择配置以下属性：

   | 类型属性     | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 精度字段属性 | 存储十进制字段精度的模式属性的名称。                         |
   | 比例字段属性 | 存储十进制字段的小数位数的架构属性的名称。                   |
   | 默认精度     | 未指定精度或无效时用于小数字段的默认精度。使用-1退出此选项。**注意：**如果十进制字段没有有效的精度和小数位数，则阶段会将记录发送到错误。 |
   | 默认比例     | 未指定精度或无效时用于小数字段的默认标度。使用-1退出此选项。**注意：**如果十进制字段没有有效的精度和小数位数，则阶段会将记录发送到错误。 |

5. 在“ **高级”**选项卡上，可以选择配置以下属性：

   | 先进物业     | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 启用缓存     | 启用缓存架构。在特定条件下可以提高性能。有关更多信息，请参见[缓存模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/SchemaGenerator.html#concept_rjk_y1q_1bb)。 |
   | 快取大小     | 要缓存的最大架构数。                                         |
   | 缓存键表达式 | 计算结果为有效缓存键的表达式。                               |