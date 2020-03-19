# 本地管道

本地管道在Transformer计算机上的本地Spark安装上运行。

当将Transformer和Spark安装在Spark在本地运行的同一台计算机上时，必须将所有管道配置为在本地运行。

如果将Transformer安装在配置为将Spark作业提交到群集的计算机上，则可以将管道配置为在本地或群集上运行。在生产环境中，将管道配置为在集群上运行以利用Spark提供的性能和规模。但是，在开发过程中，您可能希望在本地Spark安装上运行管道。例如，如果Transformer计算机无法临时访问群集，则将管道配置为在本地运行，以便您可以继续开发管道。

**重要：**仅在开发环境中使用本地管道。不要在生产环境中使用本地管道，也不要访问通过Kerberos身份验证保护的Hadoop系统上的HDFS文件。

要在本地Spark安装上运行管道，请在“群集”选项卡上将管道配置为不使用任何群集管理器，然后定义用于连接到Spark的本地主URL。

您可以按照[Spark Master URL文档中的说明](https://spark.apache.org/docs/latest/submitting-applications.html#master-urls)定义任何有效的本地主URL 。默认主URL 是`local[*]`它运行使用相同数量的工作线程在机器上逻辑内核在当地星火管道安装。

下图显示了配置为在本地Spark安装上运行的管道：

![img](https://streamsets.com/documentation/controlhub/latest/help/transformer/Graphics/LocalPipeline.png)