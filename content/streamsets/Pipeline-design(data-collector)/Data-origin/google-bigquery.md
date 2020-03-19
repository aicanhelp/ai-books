# Google BigQuery

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310113540593.png) 资料收集器

Google BigQuery来源执行查询作业，并从Google BigQuery读取结果。

原点提交您定义的查询，然后Google BigQuery将查询作为交互式查询运行。查询完成后，源将读取查询结果以生成记录。原点运行一次查询，然后在完成读取所有查询结果后管道停止。如果再次启动管道，则原点会再次提交查询。

配置原点时，可以定义查询以使用有效的BigQuery标准SQL或旧版SQL语法运行。默认情况下，BigQuery将所有查询结果写入一个临时的缓存结果表中。您可以选择禁用检索缓存的结果，并强制BigQuery计算查询结果。

您还定义了用于连接到Google BigQuery的项目和凭据提供程序。源可以从Google应用程序默认凭据或Google Cloud服务帐户凭据文件中检索凭据。

源可以为事件流生成事件。有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

## 证书

当Google BigQuery来源执行查询作业并从Google BigQuery读取结果时，它必须将凭据传递给Google BigQuery。配置来源以从Google应用程序默认凭据或Google Cloud服务帐户凭据文件中检索凭据。

### 默认凭据提供程序

配置为使用Google Application Default Credentials时，来源检查`GOOGLE_APPLICATION_CREDENTIALS`环境变量中定义的凭据文件 。如果环境变量不存在，并且Data Collector在Google Cloud Platform（GCP）中的虚拟机（VM）上运行，则源使用与虚拟机实例关联的内置服务帐户。

有关默认凭据的更多信息，请参阅Google Developer文档中的Google [Application默认凭据](https://developers.google.com/identity/protocols/application-default-credentials)。

完成以下步骤以在环境变量中定义凭证文件：

1. 使用Google Cloud Platform Console或 

   ```
   gcloud
   ```

   命令行工具创建一个Google服务帐户，并使您的应用程序使用该帐户进行API访问。

   例如，要使用命令行工具，请运行以下命令：

   ```
   gcloud iam service-accounts create my-account
   gcloud iam service-accounts keys create key.json --iam-account=my-account@my-project.iam.gserviceaccount.com
   ```

2. 将生成的凭证文件存储在Data Collector计算机上。

3. 将

   ```
   GOOGLE_APPLICATION_CREDENTIALS
   ```

    环境变量添加到适当的文件，并将其指向凭据文件。

   使用安装类型所需的方法。

   如下设置环境变量：

   ```
   export GOOGLE_APPLICATION_CREDENTIALS="/var/lib/sdc-resources/keyfile.json"
   ```

4. 重新启动Data Collector以启用更改。

5. 在该阶段的“ **凭据”**选项卡上， 为凭据提供者选择“ **默认凭据提供**者”。

### 服务帐户凭据文件（JSON）

配置为使用Google Cloud Service帐户凭据文件时，原点检查在原点属性中定义的文件。

完成以下步骤以使用服务帐户凭据文件：

1. 生成JSON格式的服务帐户凭据文件。

   使用Google Cloud Platform Console或`gcloud`命令行工具来生成和下载凭据文件。有关更多信息，请参阅Google Cloud Platform文档中的[生成服务帐户凭据](https://cloud.google.com/storage/docs/authentication#generating-a-private-key)。

2. 将生成的凭证文件存储在

   Data Collector

   计算机上。

   最佳做法是将文件存储在 Data Collector资源目录中 `$SDC_RESOURCES`。

3. 在该阶段的“ **凭据”**选项卡上，为凭据提供者选择“ **服务帐户凭据文件”**，然后输入凭据文件的路径。

## BigQuery数据类型

Google BigQuery来源将Google BigQuery数据类型转换为Data Collector数据类型。

下表列出了Google BigQuery原始数据支持的 数据类型以及原始数据将其转换为的Data Collector数据类型：

| BigQuery数据类型 | 数据收集器数据类型 |
| :--------------- | :----------------- |
| 布尔型           | 布尔型             |
| 字节数           | 字节数组           |
| 日期             | 日期               |
| 约会时间         | 约会时间           |
| 浮动             | 双                 |
| 整数             | 长                 |
| 数字             | 小数               |
| 串               | 串                 |
| 时间             | 约会时间           |
| 时间戳记         | 约会时间           |

### 日期时间转换



在Google BigQuery中，Datetime，Time和Timestamp数据类型的精度为微秒，但Data Collector中相应的Datetime数据类型的精度为毫秒。数据类型之间的转换会导致一些精度损失。

为了在数据类型转换过程中保留可能丢失的精度，Google Big Query源会生成`bq.fullValue`field属性，该属性存储一个包含具有微秒精度的原始值的字符串。您可以使用 `record:fieldAttribute`或 `record:fieldAttributeOrDefault`函数来访问属性中的信息。

| 生成的字段属性 | 描述                                               |
| :------------- | :------------------------------------------------- |
| bq.fullValue   | 为“日期时间”，“时间”和“时间戳记”字段提供原始精度。 |

有关字段属性的更多信息，请参见[字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)。

## 事件产生

查询成功完成后，Google BigQuery来源会生成一个事件。

Google BigQuery事件可以任何逻辑方式使用。例如：

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储有关已完成查询的信息的目标。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录

由Google BigQuery来源生成的事件记录具有以下与事件相关的记录标题属性：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型：big-query-success-在原点成功完成查询时生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

源可以生成以下类型的事件记录：

- 查询成功

  原点完成对查询返回的数据的处理后，将生成查询成功事件记录。

  查询成功事件记录的`sdc.event.type` 记录头属性设置为`big-query-success`，包括以下字段：领域描述询问查询已成功完成。时间戳记查询完成时的时间戳。行数已处理的行数。源偏移查询完成后的偏移量。

## 配置Google BigQuery来源

配置Google BigQuery原点以执行查询作业并从Google BigQuery读取结果。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 产生事件 [![img](imgs/icon_moreInfo-20200310113540668.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/BigQuery.html#concept_vsm_khx_q1b) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在**BigQuery**标签上，配置以下属性：

   | BigQuery属性         | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | 询问                 | 用于查询作业的SQL查询。使用有效的BigQuery标准SQL或旧版SQL语法编写查询。不要在查询中包含`#legacySql`或 `#standardSql`前缀。而是，选择或清除“ **使用旧版SQL”**属性以指定SQL语法类型。 |
   | 使用旧版SQL          | 指定查询是使用标准SQL还是旧式SQL语法。清除使用标准SQL。选择以使用旧版SQL。 |
   | 使用查询缓存         | 确定Google BigQuery是否检索缓存的结果（如果存在）。选择以检索缓存的结果。清除以禁用检索缓存的结果。 |
   | 查询超时（秒）       | 等待查询完成的最大秒数。如果查询未能在超时时间内完成，则源将中止查询，并且管道也会失败。输入时间（以秒为单位）或使用 分钟 要么 小时 用于定义时间增量的表达式中的常量。默认值为五分钟，定义如下： $ {5 * MINUTES}。 |
   | 最大批次大小（记录） | 批处理中包含的最大记录数。                                   |

3. 在“ **凭据”**选项卡上，配置以下属性：

   | 凭证属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 专案编号                                                     | 要连接的Google BigQuery项目ID。                              |
   | 凭证提供者 [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/BigQuery.html#concept_vl2_bbx_q1b) | 用于连接到Google BigQuery的凭据提供者：默认凭证提供者服务帐户凭证文件（JSON） |
   | 凭证文件路径（JSON）                                         | 使用Google Cloud服务帐户凭据文件时，原始文件用于连接到Google BigQuery的文件的路径。凭证文件必须是JSON文件。输入相对于Data Collector资源目录`$SDC_RESOURCES`的路径，或输入绝对路径。 |