# Amazon S3要求

集群EMR批处理和集群批处理模式管道可以处理来自Amazon S3的数据。

从Amazon S3读取的集群管道的要求取决于以下批处理模式：

- 群集EMR批处理模式

  集群EMR批处理模式管道使用Hadoop FS源，并在Amazon EMR集群上运行以处理来自Amazon S3的数据。群集EMR批处理模式管道需要具有Hadoop的受支持版本的Amazon EMR群集。有关受支持的Amazon EMR和Hadoop版本的列表，请参阅[Available Stage Libraries](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Installation/AvailableStageLibraries.html#concept_evs_xkm_s5)。

- 集群批处理模式

  集群批处理模式管道使用Hadoop FS起源，并在Hadoop（CDH）或Hortonworks Data Platform（HDP）集群的Cloudera发行版上运行，以处理来自Amazon S3的数据。从HDFS读取的群集模式管道需要CDH或HDP的受支持版本。有关受支持的CDH或HDP版本的列表，请参见[Available Stage Libraries](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Installation/AvailableStageLibraries.html#concept_evs_xkm_s5)。

## 为Amazon S3配置集群EMR批处理模式

集群EMR批处理模式管道在Amazon EMR集群上运行，以处理来自Amazon S3的数据。

集群EMR批处理模式管道可以在管道启动时配置的现有Amazon EMR集群或新的EMR集群上运行。设置新的EMR群集时，可以配置群集是保持活动状态还是在管道停止时终止。

可以将Data Collector安装在现有Amazon EMR集群中的网关节点上。或者，它可以安装在EMR群集之外-在本地计算机上或在另一个Amazon EC2实例上。无论Data Collector 的安装位置如何，您都可能需要修改Amazon EMR安全组，以允许 Data Collector 访问EMR集群中的主节点。安全组控制对EMR群集实例的入站和出站访问。有关为Amazon EMR集群配置安全组的信息，请参阅[Amazon EMR文档](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-security-groups.html)。

只要从Amazon EMR集群到处理器或目标使用的任何外部系统的网络连接正确配置，集群EMR批处理管道就支持集群管道中支持的所有处理器和目标。例如，如果您在集群EMR批处理管道中包括JDBC查找处理器，则必须确保Amazon EMR集群可以连接到数据库。

**注意：**群集EMR批处理模式管道目前不支持Kerberos身份验证。

完成以下步骤，配置集群EMR批处理模式管道以从Amazon S3读取：

1. 在Amazon EMR中，修改EMR集群使用的主安全组，以允许Data Collector访问集群中的主节点。

   有关为EMR集群配置安全组的信息，请参阅[Amazon EMR文档](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-security-groups.html)。

2. 在管道属性的“ **常规”**选项卡上，将“ **执行模式”**属性设置 为“ **群集EMR批处理”**。

3. 在管道的“ **群集”**选项卡上，配置以下属性：

   | 集群属性       | 描述                                                         |
   | :------------- | :----------------------------------------------------------- |
   | 辅助Java选项   | 管道的其他Java属性。用空格分隔属性。默认情况下设置以下属性。XX：+ UseConcMarkSweepGC和XX：+ UseParNewGC设置为并发标记扫描（CMS）垃圾收集器。Dlog4j.debug启用log4j的调试日志记录。不建议更改默认属性。您可以添加任何有效的Java属性。 |
   | 日志级别       | 管道在Amazon EMR集群上运行时使用的日志级别。默认值为INFO严重级别。 |
   | 工作记忆（MB） | 分配给集群中每个Data Collector Worker的最大内存量。默认值为1024 MB。 |

4. 在管道的“ **EMR”**选项卡上，配置以下属性：

   | EMR物业     | 描述                                                         |
   | :---------- | :----------------------------------------------------------- |
   | 区域        | 包含EMR集群的AWS区域。如果该区域未显示在列表中，请选择“ **自定义”**，然后输入AWS区域的名称。 |
   | AWS访问密钥 | AWS访问密钥ID。                                              |
   | AWS密钥     | AWS秘密访问密钥。管道使用访问密钥对将凭证传递到Amazon Web Services以连接到EMR集群。**提示：**为了保护敏感信息（例如访问密钥对）的安全，可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | S3登台URI   | Amazon S3中的临时暂存位置，用于存储运行管道所需的资源和配置文件。管道停止时，Data Collector将从文件夹中删除内容。每个管道的位置必须唯一。使用以下格式：`s3:///`该存储桶必须存在。如果指定路径中的文件夹不存在，则会创建该文件夹。 |
   | 设置新集群  | 管道启动时置备新的EMR群集。                                  |
   | 集群ID      | 现有EMR群集的ID。                                            |

5. 如果选择配置新的EMR群集，请在管道的“ **EMR”**选项卡上配置以下属性。

   有关配置EMR集群所需属性的更多信息，请参阅[Amazon EMR文档](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-gs.html)。

   | EMR属性以配置新集群 | 描述                                                         |
   | :------------------ | :----------------------------------------------------------- |
   | 群集名称前缀        | 预置的EMR群集名称的前缀。数据收集器ID和管道ID如下所示附加到前缀：`::::` |
   | 终止群集            | 当管道停止时终止集群。清除后，当管道停止时，群集将保持活动状态。 |
   | 启用日志            | 启用登录集群。启用日志记录后，Amazon EMR会将集群日志文件写入您指定的Amazon S3位置。 |
   | S3日志URI           | 集群在Amazon S3中写入日志数据的位置。每个管道的位置必须唯一。使用以下格式：`s3:///`该存储桶必须存在。如果指定路径中的文件夹不存在，则会创建该文件夹。 |
   | 启用调试            | 在群集上启用调试。启用调试后，您可以使用Amazon EMR控制台查看集群日志文件。 |
   | 服务角色            | 群集在配置资源和执行其他服务级别任务时使用的EMR角色。默认值为EMR_DefaultRole。有关为Amazon EMR配置角色的更多信息，请参阅[Amazon EMR文档](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-iam-roles.html)。 |
   | 工作流程角色        | 群集内EC2实例使用的EC2的EMR角色。默认值为EMR_EC2_DefaultRole。有关为Amazon EMR配置角色的更多信息，请参阅[Amazon EMR文档](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-iam-roles.html)。 |
   | 对所有用户可见      | 确定您帐户下的所有AWS Identity and Access Management（IAM）用户是否可以访问群集。 |
   | EC2子网ID           | 用于启动集群的EC2子网标识符。                                |
   | 主安全组            | 集群中主节点的安全组ID。**重要说明：**验证主安全组是否允许Data Collector访问EMR群集中的主节点。有关为EMR集群配置安全组的信息，请参阅[Amazon EMR文档](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-security-groups.html)。 |
   | 奴隶安全小组        | 集群中从节点的安全组ID。                                     |
   | 实例数              | 要初始化的Amazon EC2实例数。每个实例对应于EMR群集中的一个从属节点。 |
   | 主实例类型          | 为EMR集群中的主节点初始化的Amazon EC2实例类型。如果实例类型未显示在列表中，请选择“ **自定义”**，然后输入实例类型。 |
   | 从实例类型          | 已为EMR集群中的从属节点初始化Amazon EC2实例类型。如果实例类型未显示在列表中，请选择“ **自定义”**，然后输入实例类型。 |

6. 在管道中，将Hadoop FS起源用于群集EMR批处理模式。

7. 在来源的“ **常规”**选项卡上，为集群EMR批处理模式选择适当的EMR阶段库。

8. 在源的**Hadoop FS**选项卡上，配置Hadoop FS URI属性以指向要读取的Amazon S3存储桶。

   使用以下格式： `s3a://`

   例如：`s3a://WebServer`

   然后在Input Paths属性中，输入要在该Amazon S3存储桶中读取的数据的完整路径。您可以为“输入路径”属性输入多个路径，例如：

   - 输入路径1- `/2016/February`
   - 输入路径2- `/2016/March`

   有关更多信息，请参阅[从Amazon S3阅读](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HadoopFS-origin.html#concept_ud1_wd2_h2b)。

9. 在源的**S3**选项卡上，输入与在管道的EMR选项卡上输入的访问密钥对相同的访问密钥对。

   源使用访问密钥对将凭证传递到Amazon Web Services以从Amazon S3读取。

   **提示：**为了保护敏感信息（例如访问密钥对）的安全，可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。

**相关信息**

[配置管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ConfiguringAPipeline.html#task_xlv_jdw_kq)

[Hadoop FS](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HadoopFS-origin.html#concept_lw2_tnm_vs)

## 为Amazon S3配置集群批处理模式

集群批处理模式管道在Hadoop（CDH）或Hortonworks Data Platform（HDP）集群的Cloudera发行版上运行，以处理来自Amazon S3的数据。

完成以下步骤，配置集群批处理模式管道以从Amazon S3读取：

1. 验证HDFS和YARN的安装。

2. 在YARN网关节点上安装Data Collector。

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

   - 创建管道之后，请在Hadoop FS来源中指定Hadoop FS用户。

     对于Hadoop FS用户属性，输入ID高于min.user.id属性的用户，或输入allow.system.users属性中列出的用户名的用户。

5. 在YARN上，确认Hadoop日志记录级别设置为INFO或更低的严重性。

   YARN默认将Hadoop日志记录级别设置为INFO。要更改日志记录级别：

   1. 编辑log4j.properties文件。

      默认情况下，该文件位于以下目录中：

      ```
      /etc/hadoop/conf
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

9. 在管道中，将Hadoop FS起源用于群集批处理模式。

10. 在来源的“ **常规”**选项卡上，为集群模式选择适当的CDH或HDP舞台库。

11. 在源的**Hadoop FS**选项卡上，配置Hadoop FS URI属性以指向要读取的Amazon S3存储桶。

    使用以下格式： `s3a://`

    例如：`s3a://WebServer`

    然后在Input Paths属性中，输入要在该Amazon S3存储桶中读取的数据的完整路径。您可以为“输入路径”属性输入多个路径，例如：

    - 输入路径1- `/2016/February`
    - 输入路径2- `/2016/March`

    有关更多信息，请参阅[从Amazon S3阅读](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/HadoopFS-origin.html#concept_ud1_wd2_h2b)。

12. 如果将YARN配置为使用Kerberos身份验证，请在源的**Hadoop FS**选项卡上启用 **Kerberos身份验证**属性。

13. 在来源的**S3**选项卡上，输入用于将凭证传递到Amazon Web Services以便从Amazon S3读取的AWS访问密钥对。

    **提示：**为了保护敏感信息（例如访问密钥对）的安全，可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。