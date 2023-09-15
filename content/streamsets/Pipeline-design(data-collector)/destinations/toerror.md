# 错误

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310202648359.png) 资料收集器![img](imgs/icon-Edge-20200310202648350.png) 数据收集器边缘

To Error目标将记录传递到管道以进行错误处理。使用“ To Error”目标将记录流发送到管道错误处理。

通常，错误记录是分别生成和从管道中删除的。使用某些处理器（例如流选择器或JavaScript评估器），您可以创建多个数据流，包括您认为是错误记录的记录流。您可以将错误流连接到To Error目标，以将记录传递到管道以进行错误处理。

管道根据为管道配置的“错误记录”属性执行错误处理。

与其他目标一样，您可以检查发送到目标的数据，作为数据预览的一部分。

“出错”目标没有配置选项。要在管道中使用目标，只需添加阶段并将管道连接到阶段即可。