# 发送回复到起源

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310202326173.png) 资料收集器

将响应发送到目标目的地将记录传递到管道中的REST服务源，并允许您为这些记录指定HTTP状态代码。

在[微服务管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Microservice/Microservice_Title.html#concept_qfh_xdm_p2b)中将“将响应发送到目的地”目标与[REST服务起源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/RESTService.html#concept_hfg_2sn_p2b)一起使用[。](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Microservice/Microservice_Title.html#concept_qfh_xdm_p2b)

在配置“将响应发送到原始”目的地时，您可以为阶段指定名称和可选描述，以及用于阶段接收的记录的状态代码。

## 配置发送到原始目的地的响应

配置“向原点发送响应”目标，以将具有指定状态代码的记录发送到管道中的REST服务原点。这允许源将记录传递回源REST API。

在“ **常规”**选项卡上，配置以下属性：

| 一般财产 | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| 名称     | 艺名。                                                       |
| 描述     | 可选说明。                                                   |
| 状态码   | 包含在传递给[REST服务源的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/RESTService.html#concept_hfg_2sn_p2b)记录中的状态代码。有关HTTP状态代码的信息，请参见[Mozilla HTTP状态文档](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)。 |