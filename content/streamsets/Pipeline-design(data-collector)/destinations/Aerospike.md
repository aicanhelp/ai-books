# Aerospike

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310182602559.png) 资料收集器

Aerospike目标将数据写入Aerospike。

目标可以写入单个Aerospike节点或Aerospike节点群集。配置目标时，可以定义要写入的Aerospike节点，并配置目标尝试重试与Aerospike的连接的次数。

您指定要使用的Aerospike命名空间和可选集。您还可以指定要写入的Aerospike记录的键或唯一标识符。然后，将“ 数据收集器” 记录中的字段映射到Aerospike记录中的容器。

## 配置Aerospike目的地

配置Aerospike目标以将数据写入Aerospike。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **Aerospike”**选项卡上，配置以下属性：

   | Aerospike物业 | 描述                                                         |
   | :------------ | :----------------------------------------------------------- |
   | 飞刺节点      | 要使用的Aerospike节点。使用以下格式：`:`例如： `localhost:3000`如果要写入Aerospike群集，请添加群集中的每个节点。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以列出其他节点。 |
   | 重试尝试      | 尝试连接到Aerospike数据库的最大次数。默认值为0，这意味着目标不尝试任何重试。 |

3. 在“ **映射”**选项卡上，配置以下属性：

   | 映射属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 命名空间 | 要使用的Aerospike名称空间。输入名称空间名称或输入定义该名称空间的表达式。 |
   | 组       | 在名称空间中使用的可选Aerospike集。输入集合名称或输入定义集合的表达式。 |
   | 键       | 要使用的Aerospike记录的密钥或唯一标识符。输入密钥名称或输入定义该密钥的表达式。 |
   | 垃圾箱   | 将数据收集器记录中的字段映射到Aerospike记录中的容器。输入以下内容：箱名称表达式-定义要写入的Aerospike箱名称的表达式。箱值表达式-定义要写入Aerospike箱的值的表达式。Bin值类型-指定bin值的数据类型：字符串，长整数或双精度型。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击“ **添加”**图标以创建其他字段以对箱映射。 |