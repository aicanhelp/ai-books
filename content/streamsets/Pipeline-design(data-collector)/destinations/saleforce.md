# 销售队伍

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310202217582.png) 资料收集器

Salesforce目标将数据写入Salesforce对象。

在配置Salesforce目标时，您将定义连接信息，包括目标用于连接到Salesforce的API类型和版本。您可以通过输入对象名称或定义一个计算结果为对象名称的表达式来指定要写入的Salesforce对象。

您可以使用平台事件API名称（例如Notification__e）而不是Salesforce对象类型API名称（例如Account或Widget__c），在写入任何Salesforce对象时编写Salesforce平台事件。

Salesforce目标可以使用在`sdc.operation.type`记录头属性中定义的CRUD操作 来写入数据。您可以为没有标题属性或值的记录定义默认操作。您还可以配置如何处理不受支持的操作的记录。 有关Data Collector更改数据处理以及启用CDC的来源的列表的信息，请参见[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

默认情况下，Salesforce目标通过匹配区分大小写的字段名称将数据写入Salesforce对象。您可以通过定义CRUD操作所需的特定映射来覆盖默认字段映射。对于更新和删除操作，传入记录必须包含记录ID。

您可以选择使用HTTP代理连接到Salesforce。在Salesforce中启用后，您可以配置目标以使用相互身份验证来连接到Salesforce。

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

## 定义CRUD操作

使用SOAP API时，Salesforce目标可以插入，更新，向上插入，删除或取消删除数据。使用批量API时，目标可以插入，更新，向上插入或删除数据。

目标根据CRUD操作标头属性或与操作相关的阶段属性中定义的CRUD操作写入记录：

- CRUD记录标题属性

  您可以在CRUD操作记录标题属性中定义CRUD操作。目标在`sdc.operation.type`记录头属性中寻找要使用的CRUD操作 。

  该属性可以包含以下数值之一：INSERT为12个代表删除3更新4个用于UPSERTUNDELETE为6（Salesforce Bulk API不支持）

  如果您的管道包括启用CRUD的原始数据源，该原始数据元处理已更改的数据，则目标位置仅从`sdc.operation.type`原始数据源生成的标头属性中读取操作类型 。如果管道使用非CDC来源，则可以使用表达式评估器或脚本处理器来定义记录头属性。有关Data Collector 更改的数据处理以及启用CDC的来源的列表的详细信息 ，请参阅 [处理更改的数据。](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

- 操作阶段属性

  您在目标属性中定义默认操作。`sdc.operation.type`未设置记录头属性时，目标使用默认操作 。

  您还可以定义如何使用`sdc.operation.type`header属性中定义的不受支持的操作来处理记录 。目标可以丢弃它们，将它们发送给错误，或使用默认操作。

## 场映射

配置Salesforce目标时，可以通过将记录中的特定字段映射到Salesforce对象中的现有字段来覆盖区分大小写的字段名称的默认映射。

要映射字段，请输入以下内容：

- SDC字段-记录中包含要写入的数据的字段的名称。
- Salesforce字段-接收数据的Salesforce对象中现有字段的API名称。输入字段名称或输入定义该字段的表达式。

映射目标使用的CRUD操作所需的字段：

- 删除或取消删除

  要删除或取消删除数据，请仅映射Salesforce记录ID以删除或取消删除。创建单个字段映射，该映射将包含Salesforce记录ID的值的记录中的字段映射到名为“ Id”的Salesforce字段。**注意：** Salesforce Bulk API不支持取消删除。

- 插入，更新或更新

  要插入，更新或更新数据，可以创建多个字段映射。定义Salesforce字段时，请使用配置的API类型所需的字段名语法：批量API-使用冒号（:)或句点（。）作为字段分隔符。例如，`Parent__r:External_Id__c`或 `Parent__r.External_Id__c`都是有效的Salesforce字段。SOAP API-使用句点（。）作为字段分隔符。例如， `Parent__r.External_Id__c`是有效的Salesforce字段。

  要更新数据，还必须配置“外部ID字段”属性，该属性指定Salesforce对象中的外部ID字段以用于更新操作。

## 配置Salesforce目标

配置Salesforce目标以将数据写入Salesforce。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **Salesforce”**选项卡上，配置以下属性：

   | Salesforce财产                                               | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 用户名                                                       | Salesforce用户名，采用以下电子邮件格式： `@.com`。           |
   | 密码                                                         | Salesforce密码。如果运行Data Collector的计算机不在Salesforce环境中配置的受信任IP范围内，则必须生成安全令牌，然后将此属性设置为密码，后跟安全令牌。例如，如果密码为`abcd`，安全令牌为`1234`，则将此属性设置为abcd1234。有关生成安全令牌的更多信息，请参阅[重置安全令牌](https://help.salesforce.com/articleView?id=user_security_token.htm&type=0)。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 验证端点                                                     | Salesforce SOAP API身份验证端点。输入以下值之一：`login.salesforce.com` -用于连接到Production或Developer Edition组织。`test.salesforce.com` -用于连接到沙盒组织。默认值为`login.salesforce.com`。 |
   | [API版本](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Salesforce.html#task_es3_25d_dy) | 用于连接到Salesforce的Salesforce API版本。默认值为43.0。如果更改版本，则还必须从Salesforce Web服务连接器（WSC）下载相关的JAR文件。 |
   | 使用批量API                                                  | 确定阶段是使用Salesforce批量API还是SOAP API写入Salesforce。选择以使用批量API。清除以使用SOAP API。批量API经过优化，可处理大量数据。SOAP API支持更复杂的查询，但是在处理大量数据时不太实用。有关何时使用Bulk或SOAP API的更多信息，请参阅Salesforce Developer文档。 |
   | SObject类型                                                  | 要写入的Salesforce对象。输入对象的名称，例如Account。或定义一个表达式，其结果为对象名称。例如，如果管道从Salesforce原点读取，则原点会生成名为的Salesforce记录标头属性 `salesforce.sobjectType`。此标头属性提供记录的源对象。要写入相同的Salesforce对象，请为此属性输入以下表达式：`${record:attribute('spectroscopically')}` |
   | [默认操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Salesforce.html#concept_opg_tyg_4z) | 如果`sdc.operation.type`未设置记录头属性，则执行默认的CRUD操作。 |
   | 不支持的操作处理                                             | `sdc.operation.type`不支持在记录头属性中定义的CRUD操作类型时采取的措施 ：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。使用默认操作-使用默认操作将记录写入目标系统。 |
   | 外部ID字段                                                   | Salesforce对象中的外部ID字段，用于upsert操作。输入Salesforce字段名称，例如`Customer_Id__c`。或输入定义字段的表达式，例如 `${record:value('/ExternalIdField')}`。 |
   | [场图](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Salesforce.html#concept_xql_wbj_mcb) | 记录中的字段到Salesforce对象中现有字段的可选映射。默认情况下，Salesforce目标通过匹配区分大小写的字段名称将数据写入Salesforce对象。要覆盖默认映射，请根据目标使用的CRUD操作的要求映射字段。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以创建其他字段映射。 |

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