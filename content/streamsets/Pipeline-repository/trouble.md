# 故障排除

使用以下技巧来帮助进行管道管理：

- 将管道导入Control Hub时出现以下错误：

  `Selected pipeline file doesn't contain library definitions.`

  使用“导出”选项而非“ 控制中心导出”选项从Data Collector导出了管道。

  使用“导出”选项时，生成的JSON文件不包含阶段库定义。使用“导出”选项创建备份或将管道与另一个Data Collector一起使用。

  当使用“导出为控制中心”选项时，生成的JSON文件包括管道中使用的每个阶段库的定义。Control Hub需要文件中的阶段库定义。使用“导出为Control Hub”选项导出管道，然后可以将其导入Control Hub。

- 我为管道分配了模板标签，但该管道未作为用户模板列出

  除了将标签分配给`templates`管道外，还必须发布管道并授予用户读取管道的权限，然后他们才能将管道用作模板来创建管道。