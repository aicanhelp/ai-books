# 弹性搜索

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310183936969.png) 资料收集器

Elasticsearch目标将数据写入到Elasticsearch集群，包括Elastic Cloud集群（以前称为Found集群）和Amazon Elasticsearch Service集群。目标使用Elasticsearch HTTP模块访问Bulk API，并将每条记录作为文档写入Elasticsearch。

配置Elasticsearch目标时，将配置集群名称，HTTP URI和与文档相关的信息。

当Data Collector与Elasticsearch群集共享同一网络时，您可以输入一个或多个节点URI，并自动检测群集上的其他Elasticsearch节点。

Elasticsearch目标可以使用在`sdc.operation.type`记录头属性中定义的CRUD操作 来写入数据。您可以为没有标题属性或值的记录定义默认操作。您还可以配置如何处理不受支持的操作的记录。 有关Data Collector更改数据处理以及启用CDC的来源的列表的信息，请参见[处理更改的数据](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

您还可以根据需要添加高级Elasticsearch属性。

## 安全



为Elasticsearch集群启用安全性后，您必须指定身份验证方法：

- 基本的

  对Amazon Elasticsearch Service外部的Elasticsearch集群使用基本身份验证。使用基本身份验证，目标将传递Elasticsearch用户名和密码。

- AWS签名V4

  对Amazon Elasticsearch Service中的Elasticsearch集群使用AWS Signature V4身份验证。目标必须使用Amazon Web Services凭据签署HTTP请求。有关详细信息，请参阅 [Amazon Elasticsearch Service文档](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-request-signing.html)。使用以下方法之一来使用AWS凭证进行签名：IAM角色当执行数据收集器 在Amazon EC2实例上运行时，您可以使用AWS管理控制台为EC2实例配置IAM角色。Data Collector使用IAM实例配置文件凭证自动连接到AWS。要使用IAM角色，请不要在目标中配置访问密钥ID和秘密访问密钥属性。有关将IAM角色分配给EC2实例的更多信息，请参阅Amazon EC2文档。AWS访问密钥对当执行数据收集器未在Amazon EC2实例上运行或EC2实例不具有IAM角色时，您必须 在目标中指定**访问密钥ID**和**秘密访问密钥**属性。**提示：**为了保护敏感信息（例如访问密钥对）的安全，可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。

**提示：**为了保护敏感信息，例如用户名和密码或访问密钥对，可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。

## 时间基础和基于时间的索引

时间基准是Elasticsearch目标将记录写入基于时间的索引所用的时间。当索引没有时间成分时，可以忽略时间基准属性。

您可以将处理时间或与数据关联的时间用作时间基准。

例如，假设您使用以下日期时间变量定义Index属性：

```
logs-${YYYY()}-${MM()}-${DD()}
```

如果使用处理时间作为时间基准，则目标将根据记录的处理时间将记录写入索引。如果使用与数据相关联的时间（例如事务时间戳记），那么目标将根据该时间戳记将记录写入索引。

您可以使用以下时间作为时间基础：

- 处理时间

  使用处理时间作为时间基准时，目标将根据处理时间和索引写入索引。要将处理时间用作时间基准，请使用以下表达式：`${time:now()}`这是默认的时间基准。

- 记录时间

  当您使用与记录关联的时间作为时间基准时，您可以在记录中指定日期字段。目标根据与记录关联的日期时间将数据写入索引。

  要使用与记录关联的时间，请使用一个表达式，该表达式调用一个字段并解析为日期时间值，例如 `${record:value("/Timestamp")}`。

## 文件编号

在适当的时候，您可以指定一个定义文档ID的表达式。当您不指定表达式时，Elasticsearch会为每个文档生成ID。

在配置目标以执行创建，更新或删除操作时，必须定义文档ID。

例如，要对具有基于EmployeeID字段的ID的文档执行更新，请将写入操作定义为update，并按如下所示定义Document ID ： `${record:value('/EmployeeID')}`。

您还可以为每个文档定义一个父ID，以定义同一索引中的文档之间的父/子关系。

## 定义CRUD操作

Elasticsearch目标可以创建，更新，删除或索引数据。目标根据CRUD操作标头属性或与操作相关的阶段属性中定义的CRUD操作写入记录。

您可以通过以下方式定义CRUD操作：

- CRUD记录标题属性

  您可以在CRUD操作记录标题属性中定义CRUD操作。目标在`sdc.operation.type`记录头属性中寻找要使用的CRUD操作 。

  该属性可以包含以下数值之一：1代表CREATE，相当于INSERT2个代表删除3更新INDEX为4，相当于UPSERT8表示UPDATE `doc_as_upsert`，等于MERGE

  如果您的管道包括启用CRUD的原始数据源，该原始数据元处理已更改的数据，则目标位置仅从`sdc.operation.type`原始数据源生成的标头属性中读取操作类型 。如果管道使用非CDC来源，则可以使用表达式评估器或脚本处理器来定义记录头属性。有关Data Collector 更改的数据处理以及启用CDC的来源的列表的详细信息 ，请参阅 [处理更改的数据。](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

- 操作阶段属性

  您在目标属性中定义默认操作。`sdc.operation.type`未设置记录头属性时，目标使用默认操作 。

  您还可以定义如何使用`sdc.operation.type`header属性中定义的不受支持的操作来处理记录 。目标可以丢弃它们，将它们发送给错误，或使用默认操作。

## 配置Elasticsearch目标

配置Elasticsearch目标以将数据写入Elasticsearch集群。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在**Elasticsearch**选项卡上，配置以下属性：

   | Elasticsearch属性                                            | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 群集HTTP URI                                                 | 用于连接到集群的HTTP URI。使用以下格式：`:`                  |
   | 其他HTTP参数                                                 | 您想要作为查询字符串参数发送到Elasticsearch的其他HTTP参数。输入Elasticsearch期望的确切参数名称和值。 |
   | 检测群集中的其他节点                                         | 根据配置的集群URI检测集群中的其他节点。选择此属性等效于将client.transport.sniff Elasticsearch属性设置为true。仅在数据收集器与Elasticsearch群集共享同一网络时使用。请勿用于弹性云或Docker群集。 |
   | 使用安全                                                     | 指定是否在Elasticsearch集群上启用安全性。                    |
   | [时间基础](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Elasticsearch.html#concept_dd3_vhk_r5) | 用于写入基于时间的索引的时间基准。使用以下表达式之一：`${time:now()}`-使用处理时间作为时间基准。处理时间是与运行管道的数据收集器相关的时间。该表达式调用一个字段并解析为日期时间值，例如 `${record:value()}`。使用datetime结果作为时间基准。如果Index属性不包含datetime变量，则可以忽略此属性。默认值为`${time:now()}`。 |
   | 数据时区                                                     | 目标系统的时区。用于解析基于时间的索引中的日期时间。         |
   | 指数                                                         | 生成文档的索引。输入索引名称或计算结果为该索引名称的表达式。例如，如果输入`customer`作为索引，则目标将在`customer`索引中写入文档 。如果在表达式中使用datetime变量，请确保适当配置时间基准。有关日期[时间变量的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/DateTimeVariables.html#concept_gh4_qd2_sv)详细信息，请参见[日期时间变量](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/DateTimeVariables.html#concept_gh4_qd2_sv)。 |
   | 制图                                                         | 生成文档的映射类型。输入映射类型，计算结果为该映射类型的表达式或包含该映射类型的字段。例如，如果输入`user`作为映射类型，则目标将使用`user`映射类型写入文档 。 |
   | [文件编号](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Elasticsearch.html#concept_yr2_1tf_z5) | 该表达式的计算结果为生成的文档的ID。当您不指定ID时，Elasticsearch会为每个文档创建一个ID。默认情况下，目的地允许Elasticsearch创建ID。 |
   | 家长编号                                                     | 生成的文档的可选父ID。输入父ID或计算结果为父ID的表达式。用于在同一索引中的文档之间建立父子关系。 |
   | 路由                                                         | 生成的文档的可选定制路由值。输入路由值或计算结果为该路由值的表达式。Elasticsearch根据为文档定义的路由值将文档路由到索引中的特定分片。您可以为每个文档定义一个自定义值。如果您未定义自定义路由值，Elasticsearch将使用父ID（如果已定义）或文档ID作为路由值。 |
   | 数据字符集                                                   | 要处理的数据的字符编码。                                     |
   | [默认操作](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Elasticsearch.html#concept_w2r_ktb_ry) | 如果`sdc.operation.type`未设置记录头属性，则执行默认的CRUD操作。 |
   | 不支持的操作处理                                             | `sdc.operation.type`不支持在记录头属性中定义的CRUD操作类型时采取的措施 ：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。使用默认操作-使用默认操作将记录写入目标系统。 |
   | 其他特性                                                     | 动作声明中包含的额外字段。以JSON格式指定。例如，您可以使用该 `_retry_on_conflict`字段指定在存在版本冲突时重试更新的次数。要指定三个重试，包括以下内容：`"_retry_on_conflict" : 3`有关更多信息，请参阅[Elasticsearch文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html)。 |

3. 如果启用了安全性，请在“ **安全性”**选项卡上配置以下属性：

   | 担保财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Elasticsearch.html#concept_cxd_l1z_xfb) | 使用的身份验证方法：基本-使用Elasticsearch用户名和密码进行身份验证。为Amazon Elasticsearch Service之外的Elasticsearch集群选择此选项。AWS Signature V4-向AWS进行身份验证。为Amazon Elasticsearch Service中的Elasticsearch集群选择此选项。 |
   | 安全用户名/密码                                              | Elasticsearch用户名和密码。使用以下语法输入用户名和密码：`:`使用基本身份验证时可用。 |
   | 区域                                                         | 托管Elasticsearch域的Amazon Web Services区域。使用AWS Signature V4身份验证时可用。 |
   | [访问密钥ID](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Elasticsearch.html#concept_cxd_l1z_xfb__dt-AWS-Signature-V4) | AWS访问密钥ID。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。使用AWS Signature V4身份验证时可用。 |
   | [秘密访问密钥](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Elasticsearch.html#concept_cxd_l1z_xfb__dt-AWS-Signature-V4) | AWS秘密访问密钥。不将IAM角色与IAM实例配置文件凭据一起使用时是必需的。使用AWS Signature V4身份验证时可用。 |
   | SSL信任库路径                                                | 信任库文件的位置。配置此属性等效于配置shield.ssl.truststore.path Elasticsearch属性。对于弹性云集群而言不是必需的。 |
   | SSL信任库密码                                                | 信任库文件的密码。配置此属性等效于配置shield.ssl.truststore.password Elasticsearch属性。对于弹性云集群而言不是必需的。 |