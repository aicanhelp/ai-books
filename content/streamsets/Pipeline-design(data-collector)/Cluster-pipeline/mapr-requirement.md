# MapR要求

从MapR集群读取的集群模式管道具有以下要求：

| 零件                    | 需求                                                         |
| :---------------------- | :----------------------------------------------------------- |
| 用于集群流模式的Spark流 | Spark 2.1版或更高版本                                        |
| 地图                    | 以下MapR和MapR生态系统软件包（MEP）版本之一：MapR 5.2.0和MEP 3.0MapR 6.0.0和MEP 4.0MapR 6.0.1和MEP 5.0MapR 6.1.0和MEP 6.0 |

**重要提示：** MapR 5.2.0是[旧版舞台库](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Installation/LegacyLibraries.html#concept_fw3_zt3_tbb)。MapR已宣布自2019年4月起终止对5.x版本的维护，并建议升级到最新版本。

要查看MapR核心版本支持的MEP的完整列表，请参阅MapR核心版本[支持的MEP](https://mapr.com/docs/home/InteropMatrix/r_mep_support_core_version.html)。

## 为MapR配置群集批处理模式

完成以下步骤，配置集群管道以集群批处理模式从MapR读取。

1. 验证MapR和YARN的安装。

2. 在YARN网关节点上安装数据收集器。

3. 授予用户环境变量中定义的用户对/ user / $ SDC_USER的写许可权 。

   用户环境变量定义用于将Data Collector作为服务运行的系统用户。定义用户环境变量的文件取决于您的操作系统。有关更多信息，请参见 [服务启动的用户和组](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/DCUserGroupServiceStart.html#concept_htz_t1s_3v)。

   例如，假设用户环境变量定义为sdc，并且群集不使用Kerberos。然后，您可以使用以下命令创建目录并配置必要的写权限：

   ```
   $sudo -u hdfs hadoop fs -mkdir /user/sdc
   $sudo -u hdfs hadoop fs -chown sdc /user/sdc
   ```

4. 要使Data Collector能够提交YARN作业，请执行以下任务之一：

   - 在YARN上，将min.user.id设置为等于或小于与Data Collector用户ID（通常称为“ sdc”）关联的用户ID的值。
   - 在YARN上，将Data Collector用户名（通常为“ sdc”）添加到allowed.system.users属性中。

   - 创建管道之后，请在MapR FS来源中指定Hadoop FS用户。

     对于Hadoop FS用户属性，输入ID高于min.user.id属性的用户，或输入allow.system.users属性中列出的用户名的用户。

5. 在YARN上，确认Hadoop日志记录级别设置为INFO或更低的严重性。

   YARN默认将Hadoop日志记录级别设置为INFO。要更改日志记录级别：

   1. 编辑log4j.properties文件。

      默认情况下，该文件位于以下目录中：

      ```
      /opt/mapr/hadoop/<hadoop-version>/conf/
      ```

   2. 将**log4j.rootLogger**属性设置为INFO或更低的严重性，例如DEBUG或TRACE。

6. 如果将YARN配置为使用Kerberos身份验证，则将数据收集器配置为使用Kerberos身份验证。

   在为Data Collector配置Kerberos身份验证时，将使Data Collector能够使用Kerberos并定义主体和密钥表。

   **要点：**对于集群管道，在配置Data Collector时输入keytab的绝对路径。独立管道不需要绝对路径。

   启用后，Data Collector将 自动使用Kerberos主体和密钥表连接到使用Kerberos的任何YARN群集。有关为Data Collector启用Kerberos身份验证的详细信息，请参阅Data Collector文档中的[Kerberos身份验证](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_hnm_n4l_xs)。

7. 在管道属性中的“ **常规”**选项卡上，将“ **执行模式”**属性设置 为“ **群集批处理”**。

8. 在“ **群集”**选项卡上，配置以下属性：

   | 集群属性       | 描述                                                         |
   | :------------- | :----------------------------------------------------------- |
   | 辅助Java选项   | 管道的其他Java属性。用空格分隔属性。默认情况下设置以下属性。XX：+ UseConcMarkSweepGC和XX：+ UseParNewGC设置为并发标记扫描（CMS）垃圾收集器。Dlog4j.debug启用log4j的调试日志记录。不建议更改默认属性。您可以添加任何有效的Java属性。 |
   | 启动器环境配置 | 群集启动器的其他配置属性。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标并定义属性名称和值。 |
   | 工作记忆（MB） | 分配给集群中每个Data Collector Worker的最大内存量。默认值为1024 MB。 |

9. 在管道中，将MapR FS原点用于群集模式。

   如有必要，请在原点的“ **常规”**选项卡上选择一个集群模式阶段库 。

**相关信息**

[配置管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ConfiguringAPipeline.html#task_xlv_jdw_kq)

[MapR FS](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRFS.html#concept_psz_db4_lx)

## 为MapR配置群集流模式

完成以下步骤，配置集群管道以集群流模式从MapR读取。

1. 验证MapR，Spark Streaming和YARN的安装。

2. 在Spark和YARN网关节点上安装数据收集器。

3. 要启用检查点元数据存储，请授予用户环境变量中定义的用户对/ user / $ SDC_USER的写许可权 。

   用户环境变量定义用于将Data Collector作为服务运行的系统用户。定义用户环境变量的文件取决于您的操作系统。有关更多信息，请参见 [服务启动的用户和组](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/DCUserGroupServiceStart.html#concept_htz_t1s_3v)。

   例如，假设用户环境变量定义为sdc，并且群集不使用Kerberos。然后，您可以使用以下命令创建目录并配置必要的写权限：

   ```
   $sudo -u hdfs hadoop fs -mkdir /user/sdc
   $sudo -u hdfs hadoop fs -chown sdc /user/sdc
   ```

4. 如有必要，请指定指向Spark版本2.1或更高版本的spark-submit脚本的位置。

   Data Collector假定用于将作业请求提交到Spark Streaming的spark-submit脚本位于以下目录中：

   ```
   /usr/bin/spark-submit
   ```

   如果脚本不在此目录中，请使用SPARK_SUBMIT_YARN_COMMAND环境变量来定义脚本的位置。

   脚本的位置可能会有所不同，具体取决于您使用的Spark版本和发行版。

   例如，假设spark-submit脚本位于以下目录中： `/opt/mapr/spark/spark-2.1.0/bin/spark-submit`。然后，您可以使用以下命令来定义脚本的位置：

   ```
   export SPARK_SUBMIT_YARN_COMMAND=/opt/mapr/spark/spark-2.1.0/bin/spark-submit
   ```

   **注意：**如果更改spark-submit脚本的位置，则必须重新启动Data Collector才能捕获更改。

5. 要使Data Collector能够提交YARN作业，请执行以下任务之一：

   - 在YARN上，将min.user.id设置为等于或小于与Data Collector用户ID（通常称为“ sdc”）关联的用户ID的值。
   - 在YARN上，将Data Collector用户名（通常为“ sdc”）添加到allowed.system.users属性中。

6. 如有必要，将Spark日志记录级别的严重性设置为INFO或更低。

   默认情况下，MapR将Spark日志记录级别设置为WARN。要更改日志记录级别：

   1. 编辑位于以下目录中的log4j.properties文件：

      ```
      <spark-home>/conf/log4j.properties
      ```

   2. 将**log4j.rootCategory**属性设置为INFO或更低的严重性，例如DEBUG或TRACE。

   例如，使用Spark 2.1.0时，您可以进行编辑 `/opt/mapr/spark/spark-2.1.0/conf/log4j.properties`，并且可以按如下所示设置属性：

   ```
   log4j.rootCategory=INFO
   ```

7. 如果将YARN配置为使用Kerberos身份验证，则将数据收集器配置为使用Kerberos身份验证。

   在为Data Collector配置Kerberos身份验证时，将使Data Collector能够使用Kerberos并定义主体和密钥表。

   **要点：**对于集群管道，在配置Data Collector时输入keytab的绝对路径。独立管道不需要绝对路径。

   启用后，Data Collector将 自动使用Kerberos主体和密钥表连接到使用Kerberos的任何YARN群集。有关为Data Collector启用Kerberos身份验证的详细信息，请参阅Data Collector文档中的[Kerberos身份验证](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_hnm_n4l_xs)。

8. 在管道属性的“ **常规”**选项卡上，将“ **执行模式”**属性设置 为“ **群集YARN流”**。

9. 在“ **群集”**选项卡上，配置以下属性：

   | 集群属性        | 描述                                                         |
   | :-------------- | :----------------------------------------------------------- |
   | 工人数          | 群集纱线流传输管道中使用的工人数。用于限制产生用于处理的工作人员的数量。默认情况下，主题中的每个分区都会产生一个工作程序。每个分区的一个工作程序默认值为0。 |
   | 辅助Java选项    | 管道的其他Java属性。用空格分隔属性。默认情况下设置以下属性。XX：+ UseConcMarkSweepGC和XX：+ UseParNewGC设置为并发标记扫描（CMS）垃圾收集器。Dlog4j.debug启用log4j的调试日志记录。不建议更改默认属性。您可以添加任何有效的Java属性。 |
   | 启动器环境配置  | 群集启动器的其他配置属性。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标并定义属性名称和值。 |
   | 工作记忆（MB）  | 分配给集群中每个Data Collector Worker的最大内存量。默认值为1024 MB。 |
   | 额外的Spark配置 | 对于群集纱线流传输管道，可以配置其他Spark配置以传递到spark-submit脚本。输入Spark配置名称和要使用的值。将指定的配置传递给spark-submit脚本，如下所示：`spark-submit --conf =`例如，要限制分配给每个执行程序的堆外内存，可以使用`spark.yarn.executor.memoryOverhead` 配置并将其设置为要使用的MB数。Data Collector不会验证属性名称或值。有关可以使用的其他Spark配置的详细信息，请参阅所用Spark版本的Spark文档。 |

10. 在管道中，将MapR Streams消费者来源用于群集模式。

    如有必要，请在原点的“ **常规”**选项卡上选择一个集群模式阶段库 。