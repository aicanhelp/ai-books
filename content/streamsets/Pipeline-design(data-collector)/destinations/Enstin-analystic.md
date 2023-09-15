# 爱因斯坦分析

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310184007741.png) 资料收集器

Einstein Analytics目标将数据写入Salesforce Einstein Analytics。目标连接到Einstein Analytics，以将外部数据上传到数据集。

配置目标时，您将定义连接信息，包括目标用于连接到Einstein Analytics的API版本。

您指定要*向其*上载数据的数据集的*edgemart别名*或名称。您还可以选择定义包含数据集的edgemart容器或应用程序的名称。

目标可以使用附加，删除，覆盖或向上插入操作将外部数据上传到新数据集或现有数据集。根据操作类型，以JSON格式定义要上传的数据的元数据。

您可以选择使用HTTP代理连接到Salesforce Einstein Analytics。在Salesforce中启用后，您可以配置目标以使用相互身份验证来连接到Salesforce。

## 更改API版本

Data Collector随附43.0版的Salesforce Web服务连接器库。如果需要访问版本43.0中不存在的功能，则可以使用其他Salesforce API版本 。

1. 在**Salesforce**选项卡上，将**API Version**属性设置为要使用的版本，例如39.0。

2. 从Salesforce Web服务连接器（WSC）下载以下JAR文件的相关版本：

   - WSC JAR文件-force-wsc- <version> .0.0.jar
   - 合作伙伴API JAR文件-force-partner-api- <版本> .0.0.jar

   其中<version>是API版本号，例如39。

   有关从Salesforce WSC下载库的信息，请参阅https://developer.salesforce.com/page/Introduction_to_the_Force.com_Web_Services_Connector。

3. 在以下Data Collector目录中，将默认的force-wsc-43.0.0.jar和force-partner-api-43.0.0.jar文件替换为您下载的版本化JAR文件：

   ```
   $SDC_DIST/streamsets-libs/streamsets-datacollector-salesforce-lib/lib/
   ```

4. 重新启动Data Collector， 以使更改生效。

## 定义操作

将Einstein Analytics目标配置为在将外部数据上传到数据集时执行以下操作之一：

- 追加-将数据追加到数据集，如果不存在则创建数据集。
- 删除-从数据集中删除行。要删除的行必须包含一个具有唯一标识符的字段。
- 覆盖-替换数据集中的数据，如果不存在则创建数据集。
- Upsert-在数据集中插入或更新行，如果不存在则创建数据集。要向上更新的行必须包含具有唯一标识符的单个字段。

有关唯一标识符的更多信息，请参见[Salesforce Developer文档](https://developer.salesforce.com/docs/atlas.en-us.bi_dev_guide_ext_data_format.meta/bi_dev_guide_ext_data_format/bi_ext_data_schema_reference.htm)。

### 元数据JSON

将外部数据上传到Einstein Analytics数据集需要使用以下文件：

- 包含外部数据的数据文件。
- 可选的元数据文件，以JSON格式描述数据的架构。

爱因斯坦分析目标根据传入的记录创建数据文件。配置目标时，您可以JSON格式定义元数据。

您必须为添加，更新和删除操作定义元数据。对于追加和追加，元数据必须与要上传到的数据集的元数据匹配。要删除，元数据必须是数据集列的子集。

您可以选择为覆盖操作定义元数据，以便Einstein Analytics可以正确解释数据的数据类型。如果您不输入元数据，那么爱因斯坦分析会将每个字段都视为文本。

有关Einstein Analytics如何处理上传的外部数据的JSON元数据的更多信息，请参阅[Salesforce Developer文档](https://developer.salesforce.com/docs/atlas.en-us.bi_dev_guide_ext_data_format.meta/bi_dev_guide_ext_data_format/bi_ext_data_schema_overview.htm)。

## 数据流（不建议使用）

在以前的版本中，您可以配置目标以使用Einstein Analytics数据流将多个数据集组合在一起。但是，现在不建议使用数据流，并且在将来的版本中将删除该数据流。我们建议将目标配置为使用附加操作将数据合并到单个数据集中。

爱因斯坦分析数据流包括用于组合数据集的指令和转换。在Einstein Analytics中创建数据流。然后，当您配置Einstein Analytics目标时，请指定现有数据流的名称。数据流不应包含任何内容，因为Einstein Analytics目标将覆盖任何现有内容。

默认情况下，数据流每24小时运行一次。但是，您可以将数据流配置为在每次目的地关闭时运行，并将数据集上载到Einstein Analytics。在Einstein Analytics中，您可以在24小时内最多运行24次数据流。因此，如果您选择在每个数据集上传之后运行数据流，请确保配置的数据集等待时间超过一个小时。

有关创建数据流的更多信息，请参阅Salesforce Einstein Analytics文档。

## 配置爱因斯坦分析目的地

配置Einstein Analytics目标，以将数据写入Salesforce Einstein Analytics。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **分析”**选项卡上，配置以下属性：

   | 分析属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 用户名                                                       | Salesforce用户名，采用以下电子邮件格式： `@.com`。           |
   | 密码                                                         | Salesforce密码。如果运行Data Collector的计算机不在Salesforce环境中配置的受信任IP范围内，则必须生成安全令牌，然后将此属性设置为密码，后跟安全令牌。例如，如果密码为`abcd`，安全令牌为`1234`，则将此属性设置为abcd1234。有关生成安全令牌的更多信息，请参阅[重置安全令牌](https://help.salesforce.com/articleView?id=user_security_token.htm&type=0)。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 验证端点                                                     | Salesforce SOAP API身份验证端点。输入以下值之一：`login.salesforce.com` -用于连接到Production或Developer Edition组织。`test.salesforce.com` -用于连接到沙盒组织。默认值为`login.salesforce.com`。 |
   | API版本 [![img](imgs/icon_moreInfo-20200310184007960.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/WaveAnalytics.html#task_kff_4vn_jz) | 用于连接到Salesforce的Salesforce API版本。默认值为43.0。如果更改版本，则还必须从Salesforce Web服务连接器（WSC）下载相关的JAR文件。 |
   | Edgemart别名                                                 | 数据集名称。别名在组织中必须唯一。                           |
   | 将时间戳附加到别名                                           | 在edgemart别名或数据集名称后附加数据集上传的时间戳。要为每次上传数据创建一个新的数据集，请选择此选项。要将数据追加，删除，覆盖或追加到现有数据集中，请清除此选项。 |
   | Edgemart容器                                                 | 包含数据集的edgemart容器或应用程序的名称。输入开发人员名称或应用程序ID，而不是显示标签。例如，应用程序的开发人员名称为“ AnalyticsCloudPublicDatasets”，但应用程序的显示标签为“ Analytics Cloud公共数据集”。要获取开发人员名称或ID，请在Salesforce中运行以下查询：`SELECT Id,DeveloperName,Name, AccessType,CreatedDate,Type FROM Folder where Type = 'Insights' `如果目的地创建新数据集时未定义，则目的地将使用用户的私人应用程序。如果目的地上传到现有数据集时未定义，则Einstein Analytics会解析应用名称。如果在目标上传到现有数据集时定义，则该名称必须与包含现有数据集的当前应用程序的名称匹配。 |
   | 运作方式 [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/WaveAnalytics.html#concept_ryp_g4r_vcb) | 将外部数据上传到数据集时执行的操作。                         |
   | 数据集等待时间（秒）                                         | 等待新数据到达的最长时间（以秒为单位）。在这段时间内没有数据到达后，目标将数据上传到Einstein Analytics。数据集的等待时间必须至少与管道中原点的批处理等待时间一样长。 |
   | 使用数据流 [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/WaveAnalytics.html#concept_d4h_2lj_tx) | 确定是否使用Einstein Analytics数据流将多个数据集组合在一起。**重要：**现在不建议使用数据流，并且在以后的版本中将删除该数据流。我们建议将目标配置为使用附加操作将数据合并到单个数据集中。 |
   | 数据流名称                                                   | 现有数据流的名称。您必须在Einstein Analytics中创建数据流。   |
   | 上传后运行数据流                                             | 确定目的地是否在每次将数据集上传到Einstein Analytics时运行数据流。 |
   | 元数据JSON [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/WaveAnalytics.html#concept_qln_pct_vcb) | JSON格式的元数据，用于描述要上传的数据的架构。追加，追加和删除操作必需。对于覆盖操作是可选的。 |

3. 在“ **高级”**选项卡上，配置以下属性：

   | 先进物业                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 使用代理服务器                                               | 指定是否使用HTTP代理连接到Salesforce。                       |
   | 代理主机名                                                   | 代理主机。                                                   |
   | 代理端口                                                     | 代理端口。                                                   |
   | 代理需要凭证                                                 | 指定代理是否需要用户名和密码。                               |
   | 代理用户名                                                   | 代理凭据的用户名。                                           |
   | 代理密码                                                     | 代理凭证的密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 使用相互认证                                                 | 在Salesforce中启用后，您可以使用SSL / TLS相互身份验证来连接到Salesforce。默认情况下，在Salesforce中未启用相互身份验证。要启用相互身份验证，请联系Salesforce。在启用相互身份验证之前，必须将[相互身份验证证书](https://help.salesforce.com/articleView?id=security_keys_uploading_mutual_auth_cert.htm&type=0)存储在Data Collector资源目录中。有关更多信息，请参阅[密钥库和信任库配置](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z)。 |
   | [密钥库文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z) | 密钥库文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。默认情况下，不使用任何密钥库。 |
   | 密钥库类型                                                   | 要使用的密钥库的类型。使用以下类型之一：Java密钥库文件（JKS）PKCS＃12（p12文件）默认值为Java密钥库文件（JKS）。 |
   | 密钥库密码                                                   | 密钥库文件的密码。密码是可选的，但建议使用。**提示：**为了保护敏感信息（如密码），可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 密钥库密钥算法                                               | 用于管理密钥库的算法。默认值为 SunX509。                     |