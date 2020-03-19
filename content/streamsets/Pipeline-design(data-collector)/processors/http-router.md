# HTTP路由器

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310180941891.png) 资料收集器

HTTP路由器处理器根据记录头属性中的HTTP方法和URL路径将记录传递到数据流。

您可以在具有创建以下记录头属性的来源的管道中使用HTTP路由器处理器：

- method-请求的HTTP方法，例如GET，POST或DELETE。
- path-URL的路径。

该[HTTP服务器的起源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HTTPServer.html#concept_s2p_5hb_4y) 和[REST服务起源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/RESTService.html#concept_hfg_2sn_p2b)产生这些记录头属性。例如，在[微服务管道中，](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Microservice/Microservice_Title.html#concept_qfh_xdm_p2b)您可以根据记录头属性中的方法和路径，使用HTTP路由器处理器从REST服务源传递数据。

配置HTTP路由器处理器时，可以通过标识要包含在流中的记录的记录头属性中的方法和路径来定义数据流。如果输入记录不具有与定义的流匹配的记录头属性，则处理器将错误处理应用于记录。

## 配置HTTP路由器处理器



配置HTTP路由器处理器，以根据记录头属性中的HTTP方法和URL路径将记录传递到一个或多个数据流。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **路由器”**选项卡上，为要创建的每个数据流或输出位置配置以下属性：

   | 路由器属性 | 描述                                                         |
   | :--------- | :----------------------------------------------------------- |
   | HTTP方法   | 记录头属性中指定的HTTP方法。选择以下方法之一：得到放开机自检补丁头删除 |
   | 路径参数   | 记录标题属性中指定的URL路径。                                |

   如果输入记录不具有与配置的数据流匹配的记录头属性，则处理器将错误处理应用于记录