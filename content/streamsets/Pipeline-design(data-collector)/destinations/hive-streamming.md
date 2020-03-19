# 蜂巢流

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310184544851.png) 资料收集器

Hive流目标将数据写入以ORC（最佳行列）文件格式存储的Hive表中。

Hive流目标需要Hive 0.13或更高版本。在使用目标之前，请验证您的Hadoop实施是否支持Hive流。

配置Hive流时，您可以指定Hive元存储和以ORC文件格式存储的存储桶表。您定义Hive和Hadoop配置文件的位置，并可以选择指定其他必需属性。默认情况下，目标会根据需要创建新的分区。

Hive Streaming根据匹配的字段名称将数据写入表。您可以定义覆盖默认字段映射的自定义字段映射。

在管道中将Hive Streaming目标与MapR库一起使用之前，必须执行其他步骤以使Data Collector能够处理MapR数据。有关更多信息，请参阅Data Collector 文档中的 [MapR先决条件](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Installation/MapR-Prerequisites.html%23concept_jgs_qpg_2v)。

## 配置单元属性和配置文件

您可以配置Hive流以使用Hive和Hadoop配置文件以及其他属性：

- 配置文件

  Hive Streaming目标需要以下配置文件：core-site.xmlhdfs-site.xmlhive-site.xml

  要使用配置文件：将文件或指向文件的符号链接存储在Data Collector 资源目录中或Data Collector本地路径中的其他位置。如果文件存储在资源目录中，请在阶段中指定文件的相对路径。如果文件存储在资源目录之外，请指定文件的绝对路径。**注意：**对于Cloudera Manager安装，Data Collector会 自动创建一个名为的文件的符号链接 `hive-conf`。输入 `hive-conf`文件在阶段中的位置。

- 个别属性

  您可以在目标中配置单个Hive属性。要添加Hive属性，请指定确切的属性名称和值。目的地不验证属性名称或值。**注意：**各个属性会覆盖配置文件中定义的属性。

## 配置配置单元流目的地

使用Hive流目标将数据写入Hive：

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **配置单元”**选项卡上，配置以下属性：

   | 蜂巢属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | Hive Metastore Thrift URL                                    | Hive Metastore的节俭URI。使用以下格式：`thrift://:`端口号通常为9083。 |
   | 架构图                                                       | 配置单元架构。                                               |
   | 表                                                           | 存储为ORC文件的存储分区的Hive表。                            |
   | 配置单元配置目录 [![img](imgs/icon_moreInfo-20200310184545461.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Hive.html#concept_xh5_y4d_br) | 包含Hive和Hadoop配置文件的目录的绝对路径。对于Cloudera Manager安装，请输入`hive-conf`。目标使用以下配置文件：core-site.xmlhdfs-site.xmlhive-site.xml**注意：**配置文件中的属性被此目标中定义的单个属性覆盖。 |
   | 字段到列的映射                                               | 用于覆盖默认字段到列的映射。默认情况下，字段被写入具有相同名称的列。 |
   | 创建分区                                                     | 在需要时自动创建分区。仅用于分区表。                         |

3. 在“ **高级”**选项卡上，可以选择配置以下属性：

   | 先进物业         | 描述                                                         |
   | :--------------- | :----------------------------------------------------------- |
   | 交易批次大小     | 表中每个分区在批处理中要请求的事务数。有关更多信息，请参见Hive文档。默认值为1000个事务。 |
   | 缓冲区限制（KB） | 要写入目标的记录的最大大小。增加大小以容纳更大的记录。根据为该阶段配置的错误处理来处理超出限制的记录。 |
   | 蜂巢配置         | 要使用的其他Hive属性。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标并定义属性名称和值。使用Hive期望的属性名称和值。 |