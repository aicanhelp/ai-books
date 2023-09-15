# 额外的Spark配置

创建管道时，可以定义其他Spark配置属性，这些属性确定管道如何在Spark上运行。Transformer在启动Spark应用程序时将配置属性传递给Spark。

您可以添加任何其他Spark配置属性，如[Spark配置文档中所述](https://spark.apache.org/docs/latest/configuration.html#available-properties)。

您还可以添加Transformer提供的以下额外配置属性。这些不是Spark配置属性：

| 配置属性   | 描述                                                         |
| :--------- | :----------------------------------------------------------- |
| spark.home | 覆盖计算机上设置的SPARK_HOME环境变量。例如，假设在Transformer计算机上本地安装了多个Spark版本。您可以添加`spark.home` 配置属性以在环境变量中未设置的Spark版本上运行管道。 |

## 性能调整属性

要调整在集群上运行的管道的性能，可以添加以下Spark配置属性以覆盖默认的Spark值：

| Spark配置属性            | 描述                                                         |
| :----------------------- | :----------------------------------------------------------- |
| spark.executor.instances | 管道在其上运行的Spark执行程序的数量。默认情况下，Spark使用所需的执行程序来运行管道。当您需要限制集群中执行程序的使用时，请使用此配置属性。 |
| spark.executor.memory    | 每个Spark执行程序用于运行管道的最大内存量。                  |
| spark.executor.cores     | 每个Spark执行程序用于运行管道的核心数。Databricks不允许覆盖此配置属性。在Databricks群集上运行管道时，请勿使用此属性。 |

有关这些配置属性的更多信息，请参见[Spark配置文档](https://spark.apache.org/docs/latest/configuration.html#available-properties)。