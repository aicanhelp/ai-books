# 数据收集器中的设计

除了在Control Hub 管道设计器中设计管道外，还可以登录到创作数据收集器以设计管道，然后将其添加到Control Hub 管道存储库中。您不能在Data Collector中设计管道片段。

您可以通过以下方式添加管道：

- 从发布注册管线数据采集器小号

  您可以从在Control Hub中注册的Data Collector发布管道。

- 从注册的进口管道数据采集器小号

  您可以[导入管道](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/ExportImport/Importing.html#task_qr5_szm_qx)从数据采集器不与注册小号控制集线器。使用Export for Control Hub选项从未注册的Data Collector导出管道，然后将管道导入Control Hub。

  导出管道以在Control Hub中使用时，Data Collector会生成一个以管道命名的JSON文件，如下所示： <管道名称> .json。生成的JSON文件包括管道中使用的每个阶段库的定义。

  **注意：**您还可以从注册的Data Collector导出和导入管道。但是，将数据收集器注册到Control Hub时，最简单的方法是将管道直接发布到Control Hub。

## 从Data Collector发布管道

在已注册的Data Collector中完成开发管道之后，您可以将管道发布到Control Hub 管道存储库。您可以发布有效的管道。

1. 登录到已注册的数据收集器。

2. 从**主页**上，在列表中选择管道，然后单击“ **发布管道”**图标：![img](imgs/icon_PublishPipeline.png)。或者，要从管道画布发布管道，请单击“ **控制中心选项”**图标（![img](imgs/icon_SCHOptions.png)），然后单击“ **发布管道”**。

   出现“ **发布管道”**对话框。

3. 输入提交消息。

   作为最佳实践，请说明此管道版本中的更改，以便您可以跟踪管道的提交历史记录。

   **注意：**如果要从**主页**发布多个管道，则所有管道都使用相同的提交消息。

4. 单击**发布管道**。

   登录Control Hub时，“管道”视图将列出已发布的管道。