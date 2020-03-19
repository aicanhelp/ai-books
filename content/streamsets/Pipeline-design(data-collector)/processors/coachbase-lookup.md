# Couchbase查找

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310175353883.png) 资料收集器

Couchbase查找处理器在Couchbase Server中查找文档，并将值返回到记录中的字段。使用Couchbase查找处理器可以用其他数据丰富记录。

例如，假设Couchbase Server具有多个部门文档，每个文档都列出一个部门中的雇员。您可以配置管道以在[记录标头属性中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)存储唯一标识每个部门的Couchbase文档密钥。然后，包括一个Couchbase查找处理器以查找匹配的文档，并将值返回到`department_employees` 记录中的新字段。

Couchbase查找处理器可以使用文档密钥或Couchbase服务器查询语言N1QL查找文档。对于键查找，处理器可以将整个文档中的数据返回到指定的映射字段。或者，对于键和N1QL查找，处理器可以将数据从子文档返回到指定的映射字段。对于N1QL查找，当查找导致多个匹配的文档时，Couchbase查找处理器可以从第一个匹配的文档中返回值，或者从单独的记录中的所有匹配的文档中返回值。

配置Couchbase Lookup处理器时，您输入连接信息，例如要连接的节点和存储桶，以及连接的超时属性。（可选）您可以为连接启用TLS。您还输入信息以通过Couchbase Server进行身份验证。

## 记录标题属性



对于键/值查找，Couchbase查找处理器将创建一个记录头属性`couchbase.cas`，该属性 存储一个表示查找文档状态的值。

配置为使用[CAS（比较和交换）时](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Couchbase.html#concept_ws2_15j_j3b)，Couchbase目标使用此属性值来防止与其他进程冲突。

## 配置Couchbase查找处理器



配置Couchbase查找处理器以在Couchbase服务器中查找数据。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **Couchbase”**选项卡上，配置以下属性：

   | Couchbase属性                                                | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 节点清单                                                     | Couchbase群集中的一个或多个节点，以逗号分隔。                |
   | 桶                                                           | 要连接的现有Couchbase存储桶的名称。                          |
   | 键值超时（毫秒）                                             | 执行每个键值操作所允许的最大毫秒数。                         |
   | 连接超时（毫秒）                                             | 连接到Couchbase服务器所允许的最大毫秒数。                    |
   | 断开连接超时（毫秒）                                         | 正常关闭连接所允许的最大毫秒数。                             |
   | 高级环境设置                                                 | 与Couchbase Server连接的客户端设置。有关可用设置，请参阅[Couchbase Java SDK文档](https://docs.couchbase.com/java-sdk/2.7/client-settings.html)。 |
   | 使用TLS                                                      | 启用TLS的使用。                                              |
   | [密钥库文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z) | 密钥库文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。默认情况下，不使用任何密钥库。 |
   | 密钥库类型                                                   | 要使用的密钥库的类型。使用以下类型之一：Java密钥库文件（JKS）PKCS＃12（p12文件）默认值为Java密钥库文件（JKS）。 |
   | 密钥库密码                                                   | 密钥库文件的密码。密码是可选的，但建议使用。**提示：**为了保护敏感信息（如密码），可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 密钥库密钥算法                                               | 用于管理密钥库的算法。默认值为 SunX509。                     |
   | [信任库文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z) | 信任库文件的路径。输入文件的绝对路径或相对于Data Collector资源目录的路径：$ SDC_RESOURCES。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。默认情况下，不使用任何信任库。 |
   | 信任库类型                                                   | 要使用的信任库的类型。使用以下类型之一：Java密钥库文件（JKS）PKCS＃12（p12文件）默认值为Java密钥库文件（JKS）。 |
   | 信任库密码                                                   | 信任库文件的密码。密码是可选的，但建议使用。**提示：**为了保护敏感信息（如密码），可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 信任库信任算法                                               | 用于管理信任库的算法。默认值为SunX509。                      |

3. 在“ **凭据”**选项卡上，配置以下属性：

   | 凭证属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 认证方式 | 使用Couchbase服务器进行身份验证的方法：存储桶身份验证-使用存储桶密码进行身份验证。用于Couchbase Server 4.x和更早版本。用户身份验证-使用Couchbase用户名和密码进行身份验证。用于Couchbase Server 5.0和更高版本。 |
   | 桶密码   | 如果存储桶在Couchbase数据库中受保护，则访问该存储桶的密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。可用于存储桶身份验证。 |
   | 用户名   | Couchbase用户名。可用于用户认证。                            |
   | 密码     | Couchbase密码。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。可用于用户认证。 |

4. 在“ **查找”**选项卡上，配置以下属性：

   | 查找属性             | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | 查询类型             | 指定查找的方法：键/值-使用文档键来查找文档。N1QL-使用Couchbase Server查询语言查找文档。 |
   | 文件金钥             | 处理器查找的文档的唯一ID或密钥。例如，您可以指定一个解析为文档关键字的表达式。可用于键/值查找。 |
   | N1QL查询             | 返回文档的查询。在N1QL中指定Couchbase服务器查询语言。有关更多信息，请参见[Couchbase文档](https://docs.couchbase.com/server/current/getting-started/try-a-query.html)。可用于N1QL查找。 |
   | 返回属性             | 返回特定的子文档而不是完整的文档。可用于键/值查找。          |
   | 属性映射             | 将返回的子文档映射到记录中的字段的列表。输入以下内容：属性名称-返回的子文档。使用点表示法语法可分隔文档层次结构中的组件。有关更多信息，请参见 [Couchbase文档](https://docs.couchbase.com/java-sdk/2.7/subdocument-operations.html)。SDC字段-处理器返回子文档的记录中的映射字段的名称。返回子文档时可用于键/值查询，也可用于N1QL查询。 |
   | SDC领域              | 记录中处理器从其中返回数据的映射字段的名称。您可以指定现有字段或新字段。如果该字段不存在，则Couchbase查找处理器将创建该字段。不返回子文档时可用于键/值查找。 |
   | 作为准备好的陈述提交 | 将查询作为准备好的语句提交到Couchbase。可用于N1QL查找。      |
   | 查询超时（毫秒）     | Couchbase服务器完成查询所允许的最大毫秒数。可用于N1QL查找。  |
   | 多价值行为           | 当查找找到多个文档时要采取的操作：仅第一个值-返回第一个文档中的值。拆分为多个记录-将每个文档中的值返回到单独的记录。可用于N1QL查找。 |
   | 价值缺失行为         | 查找不返回任何文档时要采取的措施：发送到错误-将记录发送到错误。沿管道传递记录不变-传递没有查找返回值的记录。 |