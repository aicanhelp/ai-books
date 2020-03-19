# 动能数据库

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310194505969.png) 资料收集器

KineticaDB目标使用Kinetica批量插入器将数据写入Kinetica集群中的表。

配置KineticaDB目标时，可以指定Kinetica头节点的URL，连接凭据和表名。您可以为批量插入程序指定批处理大小，以及在将数据传递给Kinetica之前是否压缩数据。

必要时，可以禁用多头摄取，并且可以指定正则表达式来过滤大容量插入器使用的IP地址。

## 多头摄取

默认情况下，KineticaDB目标在可能的情况下使用多头摄取向Kinetica写入。

使用多头摄取时，目标可以将数据直接发送到适当的分片管理器。写入复制表时，目标仅将数据传递到头节点，然后头节点按预期复制数据。

您可以将Kinetica DB目标配置为仅将数据发送到Kinetica头节点。例如，当Kinetica工作节点位于防火墙后面时，您可能需要禁用多头摄取。

若要禁用多头摄取，请在“连接”选项卡上选择“禁用多头摄取”属性。有关多头摄取的更多信息，请参见Kinetica文档。

## 插入和更新

默认情况下，写入Kinetica时，KineticaDB目标会插入所有新记录。如果目标在表中找到具有相同主键的现有记录，则它将按原样保留现有记录，并丢弃新记录。

您可以配置目标以替换现有记录。要用相同的主键替换现有记录，请在“表”选项卡上选择“更新现有PK”属性。

## 配置KineticaDB目标

配置KineticaDB目标，以将数据写入KineticaDB集群。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **连接”**选项卡上，配置以下属性：

   | 连接属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | Kinetica URL                                                 | Kinetica群集的头节点的URL。使用以下格式：`http://: `例如：`http://kinetica.acme.com:9191` |
   | 批量大小                                                     | Kinetica批量插入器要使用的批量大小。默认值为10,000条记录。   |
   | 运输压缩                                                     | 在写入Kinetica之前先压缩数据。                               |
   | 禁用多头摄取 [![img](imgs/icon_moreInfo-20200310194506029.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KineticaDB.html#concept_jx1_25h_sbb) | 禁用默认的多头摄取处理。选中后，目标会将数据传递到Kinetica头节点以进行重新分发。 |
   | IP正则表达式                                                 | 用于指定要写入的IP地址的正则表达式。用于过滤与多宿主主机关联的无效IP地址。例如，如果Kinetica主机同时具有内部和外部IP地址，则可以输入正则表达式以仅写入外部IP地址。 |
   | 自定义工作程序网址列表                                       | 覆盖默认工作节点URL的工作节点URL的列表。您可以配置自定义工作程序节点URL的列表，以便目标使用主机名而不是IP地址连接到工作程序节点。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标并定义每个工作程序节点URL。URL必须按顺序列出，并且必须包含所有等级。例如，如果Kinetica群集包含三个工作程序节点，则为每个节点定义一个自定义URL，如下所示：`http://kinetica.acme.com:9191/gpudb-1 http://kinetica.acme.com:9191/gpudb-2 http://kinetica.acme.com:9191/gpudb-3` |

3. 在“ **凭据”**选项卡上，配置以下属性：

   | 凭证属性 | 描述           |
   | :------- | :------------- |
   | 用户名   | 连接的用户名。 |
   | 密码     | 连接的密码。   |

4. 在**表格**选项卡上，配置以下属性：

   | 表属性                                                       | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 表名                                                         | 要写入的表。表名区分大小写。                                 |
   | 现有PK更新[![img](imgs/icon_moreInfo-20200310194506029.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KineticaDB.html#concept_jhz_bwc_rbb) | 确定具有相同主键的记录已经在Kinetica表中时的行为。选择以允许更新现有记录。默认情况下，当具有相同主键的目标已经存在时，目标不写入记录。 |