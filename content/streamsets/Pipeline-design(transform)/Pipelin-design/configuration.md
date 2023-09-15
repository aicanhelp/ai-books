# 配置管道

配置管道以定义数据流。配置管道后，可以启动管道。

流水线可以包括多个起点，处理器和目的地[阶段](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/TransformerPipeline.html#concept_iwf_2zr_qgb)。

1. 在**首页**或**入门** 页面中，点击**创建新管道**。

   **提示：**要转到**主页**，请单击“主页”图标。

2. 在“ **新建管道”**窗口中，配置以下属性：

   | 管道属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 标题                                                         | 管道的标题。Transformer使用为管道标题输入的字母数字字符作为生成的管道ID的前缀。例如，如果输入My Pipeline *&%&^^ 123作为管道标题，则管道ID具有以下值： `MyPipeline123tad9f592-5f02-4695-bb10-127b2e41561c`。 |
   | 描述                                                         | 管道的可选描述。                                             |
   | 标签                                                         | 分配给管道的可选标签。使用标签将相似的管道分组。例如，您可能想按数据库模式或测试或生产环境对管道进行分组。您可以使用嵌套标签来创建管道分组的层次结构。使用以下格式输入嵌套标签：`//`例如，您可能希望按原始系统在测试环境中对管道进行分组。您将标签Test / HDFS和Test / Elasticsearch添加到适当的管道。 |
   | [执行方式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/ExecutionMode.html#concept_lgy_24q_qgb) | 管道的执行方式：批处理-处理单个批处理，然后停止。流-连续运行，直到您手动将其停止，保持与原始系统的连接并定期处理数据。 |
   | 触发间隔                                                     | 处理批数据之间要等待的毫秒数。仅用于流执行模式。             |
   | [启用可笑模式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Ludicrous.html#concept_m4y_lbq_g3b) | 启用谓词和过滤器下推以优化查询，以便不处理不必要的数据。     |
   | [收集输入指标](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Ludicrous.html#concept_uvb_bhw_g3b) | 收集并显示以可笑模式运行的管道的管道输入统计信息。默认情况下，当管道以荒谬模式运行时，仅显示管道输出统计信息。**注意：**对于元数据中不包含度量的数据格式，例如Avro，CSV和JSON，Transformer必须重新读取原始数据以生成输入统计信息。这会降低管道性能。 |

3. 点击**保存**。

   管道画布显示管道标题，生成的管道ID和错误图标。错误图标表示管道为空。“属性”面板显示管道属性。

4. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 管道属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 标题                                                         | （可选）编辑管道的标题。由于生成的管道ID用于标识管道，因此管道标题的任何更改都不会反映在管道ID中。 |
   | 描述                                                         | （可选）编辑或添加管道的描述。                               |
   | 标签                                                         | 可选编辑或添加分配给管道的标签。                             |
   | [执行方式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/ExecutionMode.html#concept_lgy_24q_qgb) | 管道的执行方式：批处理-单个处理所有可用数据，然后管道停止。流-保持与原始系统的连接并在数据可用时对其进行处理。管道将连续运行，直到您手动将其停止为止。 |

5. 在“ **群集”**选项卡上，为“ **群集管理器类型”**属性选择以下选项之一：

   - 无（本地）- [在Transformer机器上本地](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Local.html#concept_wkf_vnj_2hb)运行管道。
   - Hadoop YARN-在[Hadoop YARN群集](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Hadoop.html#concept_lnz_xnj_2hb)上运行管道。
   - Databricks-在[Databricks群集](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Databricks.html#concept_bkm_31c_4hb)上运行管道。
   - SQL Server 2019大数据群集-在[SQL Server 2019 BDC](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-SQLServerBDC.html#concept_w5n_frw_zjb)上运行管道。

6. 根据所选的群集管理器类型，在“ **群集”**选项卡上配置其余属性。

   对于所有集群管理器类型，配置以下属性：

   | 集群属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 应用名称                                                     | 启动的Spark应用程序的名称。输入名称或输入计算得出该名称的StreamSets表达式。按Ctrl +空格键可查看可在表达式中使用的有效函数的列表。有关每个功能的说明，请参见[Data Collector文档](https://streamsets.com/documentation/datacollector/latest/help//datacollector/UserGuide/Expression_Language/Functions.html#concept_lhz_pyp_1r)。启动应用程序时，Spark将小写名称，删除名称中的空格，并将管道运行编号附加到名称中。例如，如果输入名称My Application，然后启动初始管道运行，Spark将使用以下名称启动应用程序：`myapplication_run1`默认值为表达式 `${pipeline:title()}`，它将管道标题用作应用程序名称。 |
   | 日志级别                                                     | 用于已启动的Spark应用程序的日志级别。                        |
   | [额外的Spark配置](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/SparkConfig.html#concept_gyp_nmj_2hb) | 要使用的其他Spark配置属性。要添加属性，请单击**添加**并定义属性名称和值。使用Spark期望的属性名称和值。 |

   对于在[Databricks群集](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Databricks.html#concept_bkm_31c_4hb)上运行的管道，还配置以下属性：

   | Databricks属性                                               | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 连接到Databricks的URL                                        | 您帐户的Databricks URL。使用以下格式：https：// <您的域> .cloud.databricks.com |
   | [暂存目录](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Databricks.html#concept_zwf_w4s_rhb) | Databricks文件系统（DBFS）上的暂存目录，Transformer在其中存储StreamSets资源和文件，以将管道作为Databricks作业运行。当管道在现有的交互式群集上运行时，请将管道配置为使用相同的暂存目录，以便在Databricks中创建的每个作业都可以重用存储在该目录中的公用文件。当管道在预配置的作业群集上运行时，最佳做法是使用管道的同一登台目录，但这不是必需的。默认值为 / streamsets。 |
   | 凭证类型                                                     | 用于连接到Databricks的凭据类型：用户名/密码或令牌。          |
   | 用户名                                                       | Databricks用户名。                                           |
   | 密码                                                         | 帐户密码。                                                   |
   | 代币                                                         | 帐户的个人访问令牌。                                         |
   | [设置新集群](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Databricks.html#concept_jpd_5cm_v3b) | 设置新的Databricks作业集群以在管道的首次运行时运行管道。清除此选项可在现有的交互式集群上运行管道。 |
   | [集群配置](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Databricks.html#concept_asn_twr_thb) | 已配置的Databricks作业集群的配置属性。配置列出的属性，并根据需要以JSON格式添加其他Databricks群集属性。Transformer将Databricks默认值用于未列出的Databricks属性。包括该 `instance_pool_id`属性以设置使用[现有实例池](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Databricks.html#concept_bmf_ms3_sjb)的群集。使用Databricks期望的属性名称和值。 |
   | 终止群集                                                     | 管道停止时终止供应的作业集群。                               |
   | [集群ID](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Databricks.html#concept_dwx_mtl_v3b) | 要运行管道的现有Databricks交互式集群的ID。不配置群集以运行管道时，请指定群集ID。**注意：**使用现有的交互式群集时，群集运行的所有 Transformer管道必须由相同版本的Transformer构建。 |

   对于在[Hadoop YARN集群](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Hadoop.html#concept_lnz_xnj_2hb)上运行的管道，还配置以下属性：

   | Hadoop YARN属性                                              | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [部署方式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Hadoop.html#concept_yst_brb_qhb) | 使用的部署模式：客户端-在本地启动Spark驱动程序。群集-在群集内部的节点之一上远程启动Spark驱动程序。有关部署模式的更多信息，请参阅 [Apache Spark文档。](https://spark.apache.org/docs/latest/submitting-applications.html#launching-applications-with-spark-submit) |
   | Hadoop用户名                                                 | Transformer模仿以启动Spark应用程序并访问Hadoop系统中的文件的Hadoop用户的名称。使用此属性时，请确保为Hadoop系统启用了模拟。如果未配置，则Transformer会模拟启动管道的用户。当Transformer使用Kerberos身份验证或配置为始终模拟启动管道的用户时，将忽略此属性。有关更多信息，请参见 [Kerberos身份验证](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Hadoop.html#concept_uct_3mc_qhb) 和[Hadoop模拟模式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Hadoop.html#concept_pqd_3dx_qhb)。 |
   | [使用YARN Kerberos Keytab](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Hadoop.html#concept_yws_m1x_qhb) | 使用Kerberos主体和密钥表启动Spark应用程序并访问Hadoop系统中的文件。Transformer在启动的Spark应用程序中包含keytab文件。如果未选中，则Transformer使用启动管道的用户作为代理用户来启动Spark应用程序并访问Hadoop系统中的文件。为Transformer启用[Kerberos身份验证](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-Hadoop.html#concept_uct_3mc_qhb)时，为长时间运行的管道启用。 |
   | Keytab来源                                                   | 用于管道keytab文件的源：属性文件-使用相同的密钥表的Kerberos和主要配置为变压器中变压器的配置文件， $ TRANSFORMER_DIST的/ etc / transformer.properties。管道配置-为此管道定义一个特定的Kerberos keytab文件和主体。 |
   | YARN Kerberos密钥表                                          | 存储在Transformer计算机上的密钥库文件的绝对路径。使用管道配置作为键表源时可用。 |
   | YARN Kerberos主体                                            | 管道运行时使用的Kerberos主体名称。指定的密钥表文件必须包含此Kerberos主体的凭据。使用管道配置作为键表源时可用。 |

   对于[在本地运行](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Local.html#concept_wkf_vnj_2hb)的管道，还配置以下属性：

   | 当地财产 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 主网址   | 用于连接到Spark的本地主URL。您可以按照[Spark Master URL文档中的说明](https://spark.apache.org/docs/latest/submitting-applications.html#master-urls)定义任何有效的本地主URL 。默认值是`local[*]`使用与计算机上逻辑核心相同数量的工作线程在本地Spark安装中运行管道。 |

   对于在[SQL Server 2019 BDC](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-SQLServerBDC.html#concept_w5n_frw_zjb)上运行的管道，还配置以下属性：

   | SQL Server 2019大数据群集属性                                | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | Livy端点                                                     | 启用提交Spark作业的SQL Server 2019 BDC Livy终结点。有关获取Livy端点的信息，请参阅 [获取连接信息](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-SQLServerBDC.html#concept_sxm_fmf_1kb)。 |
   | 用户名                                                       | 控制器用户名，用于通过Livy端点提交Spark作业。有关更多信息，请参见[获取连接信息](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-SQLServerBDC.html#concept_sxm_fmf_1kb)。**提示：**为了保护敏感信息，可以按照Data Collector文档中的说明使用 [运行时资源](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或[凭据存储](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。 |
   | 密码                                                         | 控制器用户名的Knox密码，允许通过Livy端点提交Spark作业。有关更多信息，请参见[获取连接信息](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-SQLServerBDC.html#concept_sxm_fmf_1kb)。**提示：**为了保护敏感信息，可以按照Data Collector文档中的说明使用 [运行时资源](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或[凭据存储](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。 |
   | [暂存目录](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Cluster-SQLServerBDC.html#concept_eds_n5v_bkb) | SQL Server 2019 BDC上的登台目录，Transformer在其中存储运行管道所需的StreamSets资源和文件。默认值为 / streamsets。 |

7. 要定义运行时参数，请在“ **参数”**选项卡上，单击“ **添加”**图标，然后为每个参数定义名称和默认值。

   有关运行时参数的更多信息，请参见[Data Collector文档](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_rjh_ntz_qr)。

8. 要配置[预处理脚本](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/PreprocessingScript.html#concept_lkg_nd3_3jb)，请单击“ **高级”**选项卡，然后指定要使用的脚本。

   使用适用于群集上安装的Spark版本的Spark API开发脚本，该版本必须与Scala 2.11.x兼容。

9. 使用“舞台库”面板添加原始舞台。在“属性”面板中，配置舞台属性。

   有关原始阶段的配置详细信息，请参见[Origins](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Origins-Overview.html#concept_snr_zj5_pgb)。

10. 使用“舞台库”面板添加要使用的下一个舞台，将原点连接到新舞台，然后配置新舞台。

    有关处理器的配置详细信息，请参阅[处理器](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/Processors-Overview.html#concept_j43_lk5_pgb)。

    有关目标的配置详细信息，请参阅[目标](https://streamsets.com/documentation/controlhub/latest/help/transformer/Destinations/Destinations-Overview.html#concept_qxy_zk5_pgb)。

11. 根据需要添加其他阶段。

12. 在任何时候，都可以使用“ **预览”**图标（![img](imgs/icon_Preview-20200310211400243.png)）预览数据以帮助配置管道。

    连接和配置所有现有阶段后，[预览](https://streamsets.com/documentation/controlhub/latest/help/transformer/Preview/Preview-Overview.html#concept_cgw_lkb_ghb)将在部分管道中可用。

13. 验证并完成管道后，请使用“ **开始”**图标运行管道。

    当Transformer启动管道时，“ [监视”模式将](https://streamsets.com/documentation/controlhub/latest/help/transformer/PipelineMonitoring/PipelineMonitoring.html#concept_qmj_2zx_hhb)显示管道的实时统计信息