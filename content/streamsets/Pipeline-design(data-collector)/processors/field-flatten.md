# 	field-flattener

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310175752542.png) 资料收集器

字段展平器处理器展平列表和映射字段。处理器可以展平整个记录以生成没有嵌套字段的记录。或者它可以展平特定的列表或地图字段。

当您具有需要展平的嵌套字段时，请使用Field Flattener处理器。例如，用于Hive的Drift Synchronization Solution无法处理带有嵌套字段的记录，因此您可以使用Field Flattener处理器对记录进行扁平化，然后再将它们传递给Hive元数据处理器。

配置字段展平器处理器时，可以配置是展平整个记录还是记录中的特定字段。展平特定字段时，可以配置是将这些字段展平还是在其他字段中。您还配置名称分隔符以用于扁平化的字段名称。

## 整理整个记录

当场展平器展平整个记录时，它将展平记录中的所有嵌套结构，直到展平。

例如，假设您具有包含嵌套地图字段的以下记录：

```
{
  "store": {
     "id": "10342",
     "location": {
         "street": "34 2nd St",
         "city": "Wilma",
         "state": "OH",
         "zipcode": "33333"
      },
     "ip": "234.56.7890"
  }
}
```

如果您将字段展平器配置为展平整个记录并使用句点作为名称分隔符，则处理器将生成以下记录：

| store.id | store.location.street | 店铺位置 | store.location.state | store.location.zipcode | store.ip    |
| :------- | :-------------------- | :------- | :------------------- | :--------------------- | :---------- |
| 10342    | 第二街34号            | 威尔玛   | 哦                   | 33333                  | 234.56.7890 |

## 展平特定字段

字段展平器处理器可以展平包含其他嵌套列表或地图字段的指定字段（列表或地图字段）。展平列表或地图字段时，处理器将展平该字段中的所有嵌套结构，直到该字段展平。处理器可以将字段展平到位（在记录的当前位置处），或者处理器可以将字段展平到记录中的另一个列表或映射字段中，例如展平到根字段中。

例如，假设您具有包含嵌套地图字段的以下记录：

```
{
  "contact": {
     "name": "Jane Smith",
     "id": "557",
     "address": {
       "home": {
         "street": "101 3rd St",
         "city": "Huntsville",
         "state": "NC",
         "zipcode": "27023"
          },
       "work": {
         "street": "15 Main St",
         "city": "Jonestown",
         "state": "NC",
         "zipcode": "27011"
       }
      }
  }
}
```

如果将“字段拼合器”配置为`address`使用句点作为名称分隔符来拼合地图字段，则处理器将生成以下记录：

```
{
  "contact": {
     "name": "Jane Smith",
     "id": "557",
     "address": {
         "home.street": "101 3rd St",
         "home.city": "Huntsville",
         "home.state": "NC",
         "home.zipcode": "27023",
         "work.street": "15 Main St",
         "work.city": "Jonestown",
         "work.state": "NC",
         "work.zipcode": "27011"
      }
  }
}
```

如果不配置现场拼合，而是配置了“字段拼合”以使用句点作为名称分隔符将`address`地图字段拼合为 目标字段`contact`，则处理器会生成以下记录：

```
{
  "contact": {
     "name": "Jane Smith",
     "id": "557",
     "address": {
       "home": {
         "street": "101 3rd St",
         "city": "Huntsville",
         "state": "NC",
         "zipcode": "27023"
          },
       "work": {
         "street": "15 Main St",
         "city": "Jonestown",
         "state": "NC",
         "zipcode": "27011"
     "home.street": "101 3rd St",
     "home.city": "Huntsville",
     "home.state": "NC",
     "home.zipcode": "27023",
     "work.street": "15 Main St",
     "work.city": "Jonestown",
     "work.state": "NC",
     "work.zipcode": "27011"
  }
}
```

如果将处理器配置`address`为将`contact`字段展平到 字段中并删除展平的字段，则处理器将生成以下记录：

```
{
  "contact": {
     "name": "Jane Smith",
     "id": "557",
     "home.street": "101 3rd St",
     "home.city": "Huntsville",
     "home.state": "NC",
     "home.zipcode": "27023",
     "work.street": "15 Main St",
     "work.city": "Jonestown",
     "work.state": "NC",
     "work.zipcode": "27011"
  }
}
```

## 配置字段展平器

配置字段展平器处理器以展平字段。

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
   | [展平](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldFlattener.html#concept_k4x_rz1_hx) | 选择是展平整个记录还是展平特定字段。                         |
   | [领域](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/FieldFlattener.html#concept_vpx_zc1_xx) | 展平特定字段时，要展平的字段。您可以展平包含其他嵌套列表或地图字段的列表和地图字段。指定字段的路径，例如：/contact/address。要指定字段，您可以：键入用逗号分隔的字段路径。单击**字段**文本框，然后从可用字段路径列表中选择每个字段路径。单击**“使用预览数据选择字段”**以打开“ **字段选择器”**对话框，然后从预览数据中选择字段。 |
   | 展平到位                                                     | 展平特定字段时，使处理器能够展平记录中其当前位置的字段。     |
   | 目标领域                                                     | 如果未适当展平，则将地图或列表地图字段展平到其中。该字段必须已经存在于记录中。 |
   | 碰撞野战                                                     | 如果未进行展平，则当目标字段已经包含与展平字段同名的字段时，处理器将执行的操作。可能的动作：发送记录以进行错误处理覆盖新价值放弃新值 |
   | 删除展平字段                                                 | 当未适当展平时，使处理器能够在成功展平字段后从记录中删除包含未展平字段的原始字段。 |
   | 名称分隔符                                                   | 在嵌套字段名称之间使用一个或多个字符来创建展平的字段名称。例如，如果使用下划线字符，则`location` 嵌套在`store`字段中的字段将按以下方式展平：`store_location`。 |