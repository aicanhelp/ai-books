# Google BigQuery

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310184055436.png) 资料收集器

Google BigQuery目标将数据流式传输到Google BigQuery。您可以使用其他目标写入[Google Bigtable](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Bigtable.html#concept_pl5_tmq_tx)，[Google Cloud Storage](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/GCS.html#concept_p4n_jrl_nbb)和[Google Pub / Sub](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/PubSubPublisher.html#concept_qsj_hk1_v1b)。

配置目标时，您将定义现有的BigQuery数据集和表以将数据流式传输到其中。目标将每个记录流式传输到BigQuery表中的一行中。您可以选择定义一个表达式以指定要插入或更新的插入ID。插入ID是每一行的唯一ID。如果未指定插入ID，则目标会将每个记录插入到新行中。

目标根据匹配的名称和兼容的数据类型将字段从记录映射到BigQuery列。您可以将目标配置为在目标无法将字段映射到现有BigQuery列时忽略无效列。您还可以配置目标的表缓存大小。

您还定义了用于连接到Google BigQuery的项目和凭据提供程序。目标可以从Google应用程序默认凭据或Google Cloud服务帐户凭据文件检索凭据。

有关将数据流式传输到Google BigQuery的更多信息，请参阅[Google BigQuery文档](https://cloud.google.com/bigquery/streaming-data-into-bigquery)。

## BigQuery数据类型

Google BigQuery目标根据匹配的名称和兼容的数据类型将字段从记录映射到现有表中的BigQuery列。如果需要，目标会将Data Collector数据类型转换为BigQuery数据类型。

下表列出 了目标将其转换为的数据收集器数据类型和BigQuery数据类型：

| 数据收集器数据类型 | BigQuery数据类型 |
| :----------------- | :--------------- |
| 布尔型             | 布尔型           |
| 字节数组           | 字节数           |
| 日期               | 日期             |
| 约会时间           | 日期时间或时间戳 |
| 双                 | 浮动             |
| 浮动               | 浮动             |
| 整数               | 整数             |
| 清单               | 数组             |
| 列表图             | 用重复的字段记录 |
| 长                 | 整数             |
| 地图               | 记录             |
| 短                 | 整数             |
| 串                 | 串               |
| 时间               | 时间             |

Google BigQuery目标无法转换以下Data Collector 数据类型：

- 字节
- 字符
- 小数

## 证书

当Google BigQuery目标将数据流式传输到Google BigQuery时，它必须将凭据传递给BigQuery。配置目标以从Google应用程序默认凭据或Google Cloud服务帐户凭据文件检索凭据。

### 默认凭据提供程序

配置为使用Google应用程序默认凭据时，目标将检查`GOOGLE_APPLICATION_CREDENTIALS` 环境变量中定义的凭据文件。如果环境变量不存在，并且Data Collector在Google Cloud Platform（GCP）中的虚拟机（VM）上运行，则目标使用与虚拟机实例关联的内置服务帐户。

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

当配置为使用Google Cloud服务帐户凭据文件时，目标将检查目标属性中定义的文件。

完成以下步骤以使用服务帐户凭据文件：

1. 生成JSON格式的服务帐户凭据文件。

   使用Google Cloud Platform Console或`gcloud`命令行工具来生成和下载凭据文件。有关更多信息，请参阅Google Cloud Platform文档中的[生成服务帐户凭据](https://cloud.google.com/storage/docs/authentication#generating-a-private-key)。

2. 将生成的凭证文件存储在

   Data Collector

   计算机上。

   最佳做法是将文件存储在 Data Collector资源目录中 `$SDC_RESOURCES`。

3. 在该阶段的“ **凭据”**选项卡上，为凭据提供者选择“ **服务帐户凭据文件”**，然后输入凭据文件的路径。

## 配置Google BigQuery目标

配置Google BigQuery目标以将数据流式传输到Google BigQuery。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击**添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在**BigQuery**标签上，配置以下属性：

   | BigQuery属性 | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 数据集       | 要写入的BigQuery数据集。输入现有数据集的名称或计算结果为现有数据集名称的表达式。例如，如果数据集名称存储在“数据集”记录属性中，请输入以下表达式：`${record:attribute('dataset')}` |
   | 表名         | 要写入的BigQuery表的名称。输入现有表的名称或计算结果为现有表名称的表达式。例如，如果表名称存储在“表”记录属性中，请输入以下表达式：`${record:attribute('table')}` |
   | 插入ID表达式 | 该表达式的计算结果为要插入或更新的BigQuery插入ID。插入ID是每一行的唯一ID。保留空白可将每条记录插入新行。有关用于将数据流传输到BigQuery的插入ID属性的更多信息，请参阅[Google BigQuery文档](https://cloud.google.com/bigquery/streaming-data-into-bigquery#dataconsistency)。 |
   | 忽略无效的列 | 忽略无效的列。如果选择此选项，并且目的地遇到无法映射到具有相同名称和兼容数据类型的BigQuery列的字段路径，那么目的地将忽略无效列，并将记录中的其余字段写入BigQuery。如果清除并且目标遇到无效的列，则将该记录发送到阶段以进行错误处理。 |
   | 表缓存大小   | 要在本地缓存的表ID条目的最大数量。当目标评估要写入的数据集和表名称时，它将检查BigQuery中是否存在表ID，然后缓存该表ID。可能的情况下，目标使用缓存来避免从BigQuery进行不必要的检索。当高速缓存达到最大大小时，将逐出最旧的高速缓存条目以允许新数据。默认值为-1，无限的缓存大小。 |

3. 在“ **凭据”**选项卡上，配置以下属性：

   | 凭证属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 专案编号                                                     | 要连接的Google BigQuery项目ID。                              |
   | 凭证提供者 [![img](imgs/icon_moreInfo-20200310184055452.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/BigQuery.html#concept_ym2_mww_hbb) | 用于连接到Google BigQuery的凭据提供者：默认凭证提供者服务帐户凭证文件（JSON） |
   | 凭证文件路径（JSON）                                         | 使用Google Cloud服务帐户凭据文件时，该路径是目标用来连接到Google BigQuery的文件的路径。凭证文件必须是JSON文件。输入相对于Data Collector资源目录`$SDC_RESOURCES`的路径，或输入绝对路径。 |