# 记录重复数据删除器

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310181621528.png) 资料收集器

记录重复数据删除器评估记录中是否有重复数据，并将数据路由到两个流中-一个流用于唯一记录，一个流用于重复记录。使用记录重复数据删除器丢弃重复数据或通过不同的处理逻辑路由重复数据。

记录重复数据删除器可以比较整个记录或字段的子集。使用字段子集将比较重点放在关注的字段上。例如，要丢弃意外提交的采购不止一次，您可以比较有关采购商，选定商品和送货地址的信息，但忽略事件的时间戳。

为了提高管道性能，“记录重复数据删除器”对比较字段进行哈希处理，并使用哈希值评估重复项。在极少数情况下，哈希函数可能会产生冲突，从而导致记录被错误地视为重复项。

## 比较窗口

记录重复数据删除器缓存记录信息以进行比较，直到达到指定数量的记录为止。然后，它将丢弃缓存中的信息并重新开始。

您可以配置时间限制，以按固定的时间间隔触发缓存刷新。配置时间限制时，时间限制优先于记录限制。

当您停止管道时，记录重复数据删除器将丢弃内存中的所有信息。

## 配置记录重复数据删除器

使用记录重复数据删除器来路由或删除具有重复数据的记录。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |

2. 在“ **重复数据删除”**选项卡上，配置以下属性：

   

   