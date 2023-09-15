# 索尔

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310202503785.png) 资料收集器

Solr目标将数据写入Solr节点或集群。

配置Solr目标时，将配置节点或集群的连接信息。您可以将目标配置为单独或成批索引记录。

您可以配置目标如何将记录中的字段映射到Solr中的字段。您可以使目标根据名称自动将记录中的字段映射到Solr模式中的字段。或者，您可以将特定的传入字段映射到Solr字段。您还可以指定当记录缺少架构或映射字段中的字段时要执行的操作，并且可以配置目标以忽略缺少的可选字段。

您可以指定目标是否验证与Solr的连接。而且，您可以配置写入属性，以确定目标是否在继续写入其他数据之前是否等待Solr完成所有处理。

必要时，可以启用目标以使用Kerberos身份验证。

## 索引模式

索引模式确定在写入Solr时Solr目标索引如何记录。索引模式还确定目标如何处理错误。

您可以使用以下索引模式：

- 记录

  目标一次索引一个记录，然后将该记录传递给Solr。

  如果发生错误，则目标会将记录传递到阶段以进行错误处理。

- 批量

  目标一次索引了一批记录，然后将其传递给Solr。

  如果发生错误，则目标将回滚所有已建立索引的记录，并将整个批处理传递到阶段以进行错误处理。

## Kerberos身份验证

您可以使用Kerberos身份验证来连接到Solr节点或集群。使用Kerberos身份验证时，Data Collector使用Kerberos主体和keytab连接到Solr。

Kerberos主体和密钥表在Data Collector 配置文件中定义`$SDC_CONF/sdc.properties`。要使用Kerberos身份验证，请在Data Collector 配置文件中配置所有Kerberos属性，然后在Solr目标中启用Kerberos。

有关为Data Collector启用Kerberos身份验证的详细信息，请参阅Data Collector文档中的[Kerberos身份验证](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_hnm_n4l_xs)。

## 配置Solr目标

配置Solr目标以将数据写入Solr节点或集群。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **Solr”**选项卡上，配置以下属性：

   | Solr属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 实例类型                                                     | 要写入的Solr实例类型：单节点-写入单个Solr节点。SolrCloud-写入Solr集群。 |
   | Solr URI                                                     | 当写入单个节点时，该节点的URI。使用以下格式：`http://:/solr/` |
   | ZooKeeper连接字符串                                          | 写入Solr集群时，请使用ZooKeeper连接字符串。使用以下格式：`:`如果集群使用多个ZooKeeper实例，请输入逗号分隔的连接字符串列表。 |
   | 默认集合名称                                                 | 写入Solr集群时，该集群的默认集合名称。                       |
   | [记录索引模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Solr.html#concept_z4y_bgr_wr) | 确定如何索引记录。                                           |
   | 自动映射字段                                                 | 根据匹配的名称，将记录中的字段自动映射到Solr模式中的字段。如果选择了“忽略可选字段”，那么除非记录缺少Solr架构中必需的字段，否则目标将处理每个记录。如果未选择“忽略可选字段”，则每个记录必须包含模式中指定的所有字段，是否必填。仅当记录中的字段与Solr模式中的字段具有相同的名称和兼容的数据类型时，才使用此选项。 |
   | 数据的现场路径                                               | 目标写入Solr的记录字段的路径。当目标自动映射字段时可用。默认值为`/`，表示字段位于根级别。 |
   | 领域                                                         | 记录中的字段到Solr字段的映射。当目的地未自动映射字段时可用。映射的字段必须具有兼容的数据类型。例如，您必须将记录中的“列表”和“映射”字段映射到多值的Solr字段。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以创建其他字段映射。 |
   | 忽略可选字段                                                 | 忽略记录中不存在的非必需字段。选择后，缺少可选字段的记录将不带有可选字段。如果未选中，则将根据“缺少字段”属性来处理任何缺少可选字段的记录。 |
   | 遗失领域                                                     | 如果记录不包含架构中的字段或映射字段，请采取的措施：丢弃-丢弃记录并继续处理后续记录。发送到错误-根据为该阶段配置的错误处理来处理记录。停止管道-停止管道。选择“忽略可选字段”时，此属性不适用于缺少的可选字段。 |
   | [Kerberos身份验证](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Solr.html#concept_npl_fss_zw) | 使用Kerberos凭据连接到Solr节点或集群。选中后，将使用Data Collector配置文件中 定义的Kerberos主体和密钥表`$SDC_CONF/sdc.properties`。 |
   | 跳过验证                                                     | 确定目标是否验证与Solr的连接。当Solr配置文件`solrconfig.xml`并未定义默认搜索字段（“ df”）参数时，将目标配置为跳过验证 。 |
   | 等待冲洗                                                     | 确定目标是否在处理另一批数据之前，先等待Solr将一批数据写入磁盘。默认情况下，目的地等待。您可以禁用此属性以提高写入性能，但是如果Solr服务器无法完成对磁盘的写入，则数据可能会丢失。 |
   | 等待搜寻者                                                   | 确定目标是否在处理另一批数据之前等待Solr使一批数据可搜索。默认情况下，目的地等待。如果不需要在Data Collector提交数据之前在Solr中搜索数据，则可以禁用此属性。 |
   | 软提交                                                       | 确定Solr是执行软提交还是硬提交。软提交将在一批数据完全可用之前刷新索引视图。仅在批处理完全可用后，硬提交才会更新索引。默认情况下，目标请求硬提交。如果不需要立即显示数据，则可以禁用此属性以提高写入性能。 |
   | 连接超时（毫秒）                                             | 发起与Solr节点或集群的连接所允许的最大毫秒数。0表示没有限制。 |
   | 套接字超时（毫秒）                                           | 数据流可以中断的最大毫秒数。0表示没有限制。                  |