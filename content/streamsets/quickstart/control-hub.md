

# 认识StreamSets控制中心

StreamSets Control Hub TM是所有数据流管道的中央控制点。Control Hub允许团队大规模构建和执行大量复杂的数据流。

数据工程师团队使用Control Hub随附的共享存储库来协作构建管道。Control Hub 提供了管道的完整生命周期管理，使您可以跟踪版本历史记录，并完全控制不断发展的开发过程。通过控制中心 ，您可以根据管道处理需求，在不同类型的执行引擎上大规模部署和执行数据流。

您可以在单个可视拓扑中映射多个数据流，并且可以查看实时统计信息，以测量端到端或点对点跨每个拓扑的数据流性能。您还可以监视警报，以确保传入数据满足业务需求，以提高可用性和准确性。

组织内的多种类型的用户可以在Control Hub中扮演不同的角色。例如，数据架构师通常会针对数据如何流经多个系统创建高级设计。数据工程师团队使用此高级设计在Control Hub 管道设计器中构建单个管道，然后发布完成的管道。

DevOps或站点可靠性工程师将已发布的管道添加到作业中，然后跨多个执行引擎启动该作业，并在每个执行引擎上运行一个远程管道实例。数据架构师将相关作业映射到单个可视拓扑中，然后使用该拓扑监视和测量完整的数据流。DevOps工程师为拓扑创建数据SLA（服务水平协议），以定义数据流不能超过的阈值，从而确保及时交付数据。

让我们仔细看看数据架构师，数据工程师和DevOps工程师可以通过Control Hub完成哪些工作。

## 设计完整的数据架构

作为数据架构师-负责定义如何由不同系统存储，使用和管理数据的人员-您可以设计通过多个系统的完整数据流。



您可以在设计文档或图表中设计高级设计。然后，与您的团队一起开发满足这些数据流需求的管道。

例如，您需要通过收集组织的社交摘要，企业数据仓库和网站日志中捕获的所有客户数据来创建客户的360度视图。您需要使用Hive和Impala等工具将所有数据发送到Hadoop分布式文件系统（HDFS）进行进一步分析。您确定在将网站日志流式传输到HDFS之前，必须将其作为中介系统写入Kafka。

为了满足此需求，您可以对完整的数据流进行以下高级设计：

![img](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Graphic/DPM_DesignArchitecture.png)

然后，您的其他团队将使用此高级设计在Control Hub中开发必要的管道，作业和拓扑。

## 协同构建管道

作为数据工程师-负责确保数据在系统之间顺畅流动的人员-您构建实现设计的数据体系结构所需的管道。



选择可用的创作数据收集器或 转换器后，可以在Control Hub 管道设计器中设计管道。

在开发过程中，您与其他数据工程师共享管道，以便您可以使用组织的最佳实践作为团队协作构建管道。管道完成后，您可以将管道发布到管道存储库中。

Control Hub 提供管道的发布管理。典型的管道开发周期涉及对管道的迭代更改。Control Hub 维护每个已发布管道的版本历史记录。例如，在设计“社交订阅源数据流”管道时，您要测试该管道，然后对其进行更改。因此，您可能会 多次将管道发布到Control Hub管道存储库，如管道历史记录的以下图像中所示：

![img](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Graphic/DPM_ManagePipelineRepository.png)

在Control Hub中查看管道历史记录时，您可以查看任何管道版本的配置详细信息，并且可以并排比较管道版本。例如，如果您单击上图中版本3的“ **与以前版本比较”**图标![img](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Graphic/icon_DPM_ComparePreviousVersion.png)，则Control Hub 在比较窗口中显示版本2和版本3，如下所示：

![img](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Graphic/DPM_ComparePipelineVersions.png)

您可以看到管道的版本3添加了一个Expression Evaluator处理器。您可以深入了解每个管道阶段的详细信息，并比较版本之间每个阶段的配置。

您可以将标记添加到管道版本中，以标记发布点或分离开发和生产环境。例如，当您完成开发社交Feed数据流管道时，将“准备部署”标签添加到最新版本。该标签会通知您的DevOps工程师准备将哪个管道版本添加到作业中并运行。

## 大规模执行工作

管道是数据流的设计。作业是数据流的执行。数据工程师使用Control Hub Pipeline Designer构建管道。DevOps或站点可靠性工程师在执行引擎组上运行作业。



作为DevOps或站点可靠性工程师（负责确保所有服务和系统可扩展和可靠的人员），您需要在Control Hub中注册以下类型的执行引擎：

- 资料收集器

  一个执行引擎，该引擎运行可以从大量异构源和目标中读取和写入的管线。这些管道执行应用于单个记录或单个批处理的轻量级数据转换。

- 数据收集器边缘

  执行引擎运行流水线，这些流水线从边缘设备读取数据，或者从另一个流水线接收数据，然后对该数据进行操作以控制边缘设备。

- 变压器

  在Apache Spark（一种开放源代码群集计算框架）上运行数据处理管道的执行引擎。由于Transformer管道在群集上部署的Spark上运行，因此管道可以对整个数据集执行重量级转换，例如联接，聚合和排序。

一个控制中心 的工作运行在一个类型的执行引擎的管道。在一组执行引擎上启动作业时，Control Hub会 在该组中的每个执行引擎上远程运行管道。这使您能够管理和协调跨多个执行引擎运行的大规模数据流。

您可以按项目，地理区域或部门来组织工作。例如，您的数据工程师已经开发并发布了WebLog收集管道和EDW复制流管道。在数据中心中，您将在多个服务器上运行的Data Collector指定为Web服务器组。您可以将在其他服务器上运行的另一个Data Collector组指定为数据仓库组。然后，您创建一个作业以在Web服务器Data Collector的组上运行WebLog Collection管道。

下图显示了WebLog收集作业如何在Web服务器组中的每个Data Collector上运行远程管道实例。该作业不会在数据仓库组中的Data Collector上运行管道，这些管道是为从企业数据仓库读取的管道保留的Data Collector。

![img](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Graphic/DPM_ManageOrchestration.png)

## 将作业映射到拓扑

在Data Collector 或Transformer中，您可以监视和查看单个管道的详细信息。但是，您通常会运行多个中间管道，所有这些管道一起工作以创建完整的数据流。

作为数据架构师，您可以在Control Hub中创建拓扑 以将多个相关作业映射到一个视图中。拓扑在遍历多个管道时提供交互式的端到端数据视图。您可以将任意数量的作业添加到拓扑中。

继续我们的Customer 360示例，在WebLog收集管道读取Web服务器日志文件并将数据写入Kafka之后，另一个管道将使用Kafka数据，对其进行处理并将数据流式传输到HDFS。其他管道从Twitter社交摘要和企业数据仓库中读取，也将数据写入HDFS。在 Control Hub中，您可以创建一个包含所有管道作业的拓扑，如下所示：

![img](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Graphic/DPM_MapPipelinesTopology.png)

从拓扑中，您可以选择每个作业，然后深入研究每个管道的配置详细信息。例如，如果我们在上面的拓扑画布中选择“社交提要数据流”作业，则可以在右侧的详细信息窗格中的管道中包含三个阶段。

## 衡量数据流质量

作为数据架构师或DevOps或站点可靠性工程师，您可以测量拓扑的运行状况以及拓扑中包含的所有作业和连接系统的性能。



Control Hub 监视提供有关正在运行的管道的实时统计信息和错误信息。

例如，Customer 360拓扑的详细信息窗格提供了该拓扑中所有正在运行的管道的记录计数和吞吐量的单一视图：

![img](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Graphic/DPM_MeasureQualityTopology.png)

您可以在拓扑中选择作业或连接系统，以发现有关该作业或系统的更详细的监视。

在一组Data Collector上启动作业时，Control Hub将 提供完整作业统计信息的单个视图。在作业中，您可以查看单个管道的统计信息，也可以查看在一组Data Collector上运行的所有远程管道实例的汇总统计信息。

例如，如果我们在拓扑画布中选择“社交提要数据流”作业，则详细信息窗格将显示所选作业的指标：

![img](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Graphic/DPM_MeasureQualityJob.png)

## 监视数据流操作

作为DevOps或站点可靠性工程师，您可以通过定义数据SLA（服务水平协议）来监控您的日常操作，以确保传入的数据满足业务可用性和准确性的要求。



除了测量拓扑的运行状况之外，您还可以定义数据SLA，以定义数据吞吐率或错误记录率的预期阈值。当达到指定的阈值时，数据SLA会触发警报。数据SLA警报提供有关团队期望的数据处理速率的即时反馈。它们使您能够监视数据流操作并快速调查和解决出现的问题。

例如，您与运营分析团队具有服务水平协议，以确保在Customer 360拓扑中捕获和处理的所有数据都是干净的并且可用于立即分析。如果任何Customer 360作业遇到处理错误，则必须立即解决这些问题。您定义并激活一个数据SLA，该SLA在拓扑中的作业每秒遇到100条以上错误记录时触发警报。

如果警报触发，则控制中心 会在顶部工具栏中通过红色警报图标通知您：![img](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Graphic/icon_Notifications.png)。您可以深入研究数据SLA的详细信息，以发现达到哪个阈值并调查需要解决的问题。触发的数据SLA显示错误记录率的图表。图中的红线代表定义的阈值，如下所示：

![img](https://streamsets.com/documentation/controlhub/latest/help/controlhub/UserGuide/Graphic/DPM_MasterOperations.png)

我们已经看到了如何使用Control Hub 将数据流的高级体系结构图转换为管道和作业，然后可以从单个拓扑中对其进行管理和衡量。试试看，亲自看看使用Control Hub可以多么轻松地控制所有复杂的数据流管道。