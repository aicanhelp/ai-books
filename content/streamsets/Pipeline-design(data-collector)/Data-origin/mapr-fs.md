# MapR FS

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310172649514.png) 资料收集器

MapR FS源从MapR FS读取文件。仅在为群集批处理管道执行模式配置的管道中使用此源。

配置MapR FS原点时，可以为要读取的数据指定输入路径和数据格式。您可以配置源以从所有子目录读取，并为包含多个对象的记录生成单个记录。

原始读取基于Hadoop支持的所有压缩编解码器的文件扩展名的压缩数据。

必要时，您可以启用Kerberos身份验证。您还可以指定要模拟的Hadoop用户，定义Hadoop配置文件目录，并根据需要添加Hadoop配置属性。

MapR FS原点生成记录头属性，使您能够在管道处理中使用记录的原点。

**提示：** Data Collector 提供了多个MapR来源来满足不同的需求。有关快速比较表以帮助您选择合适的表，请参阅[比较MapR起源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ip2_szg_qbb)。

在管道中使用任何MapR阶段之前，必须执行其他步骤以使Data Collector能够处理MapR数据。有关更多信息，请参阅Data Collector 文档中的 [MapR先决条件](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Installation/MapR-Prerequisites.html%23concept_jgs_qpg_2v)。

## Kerberos身份验证

您可以使用Kerberos身份验证连接到MapR。使用Kerberos身份验证时，Data Collector 使用Kerberos主体和密钥表连接到MapR。默认情况下，Data Collector 使用启动它的用户帐户进行连接。

Kerberos主体和密钥表在Data Collector 配置文件中定义`$SDC_CONF/sdc.properties`。要使用Kerberos身份验证，请在Data Collector 配置文件中配置所有Kerberos属性，然后在 MapR FS源中启用Kerberos。

有关为Data Collector启用Kerberos身份验证的详细信息，请参阅Data Collector文档中的[Kerberos身份验证](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_hnm_n4l_xs)。

## 使用Hadoop用户

Data Collector 可以使用当前登录的Data Collector用户或在 MapR FS来源中配置的用户从MapR FS读取文件。

可以设置需要使用当前登录的Data Collector用户的Data Collector配置属性 。如果未设置此属性，则可以在源中指定一个用户。有关Hadoop模拟和Data Collector属性的更多信息，请参阅Data Collector文档中的[Hadoop Impersonation Mode](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_pmr_sy5_nz)。

请注意，来源使用其他用户帐户连接到MapR FS。默认情况下，Data Collector使用启动它的用户帐户连接到外部系统。使用Kerberos时，Data Collector使用Kerberos主体。

要将MapR FS来源中的用户配置为从MapR FS读取，请执行以下任务：

1. 在MapR上，将用户配置为代理用户，并授权该用户模拟Hadoop用户。

   有关更多信息，请参见MapR文档。

2. 在MapR FS来源中的**Hadoop FS**选项卡上，配置 **Hadoop FS用户**属性。

## Hadoop属性和配置文件

您可以将MapR FS原始配置为使用单个Hadoop属性或Hadoop配置文件：

- Hadoop配置文件

  您可以将以下Hadoop配置文件与MapR FS一起使用：core-site.xmlhdfs-site.xmlyarn-site.xmlmapred-site.xml

  要使用Hadoop配置文件：将文件或指向文件的符号链接存储在Data Collector资源目录中。在MapR FS原点中，指定文件的位置。

- 个别属性

  您可以在源中配置各个Hadoop属性。要添加Hadoop属性，请指定确切的属性名称和值。MapR FS来源不验证属性名称或值。**注意：**各个属性会覆盖Hadoop配置文件中定义的属性。

## 记录标题属性

MapR FS源创建记录头属性，该属性包含有关记录的源文件的信息。

您可以使用`record:attribute`或 `record:attributeOrDefault`函数来访问属性中的信息。有关使用记录标题属性的更多信息，请参见[使用标题属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_rd2_ghz_dz)。

MapR FS原始创建以下记录头属性：

- 文件-提供记录起源的文件路径和文件名。
- offset-提供文件偏移量（以字节为单位）。文件偏移量是记录在文件中的原始位置。

## 资料格式

MapR FS源根据您选择的数据格式对数据进行不同的处理。源处理以下类型的数据：

- 阿夫罗

  为每个Avro记录生成一条记录。每个小数字段都包含 `precision`和`scale` [字段属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/FieldAttributes.html#concept_xfm_wtp_1z)。

  该阶段在`avroSchema` [记录头属性中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_wn2_jcz_dz)包括Avro模式 。您可以使用以下方法之一来指定Avro模式定义的位置：**消息/数据包含架构** -在文件中使用架构。**在“管道配置”中** -使用您在阶段配置中提供的架构。**Confluent Schema Registry-**从Confluent Schema Registry检索架构。Confluent Schema Registry是Avro架构的分布式存储层。您可以配置阶段以通过阶段配置中指定的模式ID或主题在Confluent Schema Registry中查找模式。

  在阶段配置中使用架构或从Confluent Schema Registry检索架构会覆盖文件中可能包含的任何架构，并可以提高性能。

  该阶段读取不需要Avro支持的压缩编解码器压缩的文件，而无需进行其他配置。

- 定界

  为每个定界线生成一条记录。您可以使用以下定界格式类型：**默认CSV-**包含逗号分隔值的文件。忽略文件中的空行。**RFC4180 CSV-**严格遵循RFC4180准则的逗号分隔文件。**MS Excel CSV** -Microsoft Excel逗号分隔文件。**MySQL CSV** -MySQL逗号分隔文件。**制表符分隔的值** -包含制表符分隔的值的文件。**PostgreSQL CSV** -PostgreSQL逗号分隔文件。**PostgreSQL文本** -PostgreSQL文本文件。**自定义** -使用用户定义的定界符，转义符和引号字符的文件。**多字符**定界-使用多个用户定义的字符定界字段和行以及单个用户定义的转义和引号字符的文件。

  您可以将列表或列表映射根字段类型用于定界数据，并且可以选择在标题行中包括字段名称（如果有）。有关根字段类型的更多信息，请参见定界[数据根字段类型](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Delimited.html#concept_zcg_bm4_fs)。

  使用标题行时，可以启用带有其他列的记录处理。其他列使用自定义的前缀和顺序递增的顺序整数，如命名 `_extra_1`， `_extra_2`。当您禁止其他列时，包含其他列的记录将发送到错误。

  您也可以将字符串常量替换为空值。

  当记录超过为该阶段定义的最大记录长度时，该阶段将根据为该阶段配置的错误处理来处理对象。

- 文本

  根据自定义定界符为每行文本或每段文本生成一条记录。

  当线或线段超过为原点定义的最大线长时，原点会截断它。原点添加了一个名为Truncated的布尔字段，以指示该行是否被截断。

  有关使用自定义定界符处理文本的更多信息，请参见[使用自定义定界符的文本数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/TextCDelim.html#concept_lg2_gcg_jx)。

## 配置MapR FS原点

配置MapR FS源以从MapR FS读取文件。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **Hadoop FS”**选项卡上，配置以下属性：

   | Hadoop FS属性                                                | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | Hadoop FS URI                                                | 可选的Hadoop URI。要连接到特定集群，请输入`maprfs:///mapr/`。例如：`maprfs:///mapr/my.cluster.com/`保留为空以使用默认值`maprfs:///`，该默认值使用`$MAPR_HOME/conf/mapr-clusters.conf` 文件中定义的第一个条目 。 |
   | 输入路径                                                     | 要读取的输入数据的位置。输入如下路径： `/`。例如：`/user/mapr/directory` |
   | 包括所有子目录                                               | 从指定输入路径内的所有目录中读取。                           |
   | 产生单条记录                                                 | 当一条记录包含多个对象时，将生成一条记录。                   |
   | [Kerberos身份验证](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRFS.html#concept_kfv_rg4_lx) | 使用Kerberos凭据连接到MapR。选中后，将使用Data Collector配置文件中 定义的Kerberos主体和密钥表`$SDC_CONF/sdc.properties`。 |
   | [Hadoop FS配置目录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRFS.html#concept_ojx_k34_lx) | Hadoop配置文件的位置。在Data Collector资源目录中使用目录或符号链接。您可以将以下文件与MapR FS一起使用：core-site.xmlhdfs-site.xmlyarn-site.xmlmapred-site.xml**注意：**配置文件中的属性被阶段中定义的单个属性覆盖。 |
   | [Hadoop FS用户](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRFS.html#concept_ymk_4h4_lx) | 模仿Hadoop的用户从MapR读取数据。使用此属性时，请确保正确配置了MapR。未配置时，管道将使用当前登录的Data Collector用户。将Data Collector配置为使用当前登录的Data Collector用户时，不可配置。有关更多信息，请参阅Data Collector 文档 中的[Hadoop模拟模式](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCConfig.html%23concept_pmr_sy5_nz)。 |
   | [Hadoop FS配置](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRFS.html#concept_ojx_k34_lx) | 要使用的其他Hadoop配置属性。要添加属性，请单击**添加**并定义属性名称和值。使用MapR FS期望的属性名称和值。 |
   | 最大批次大小（记录）                                         | 一次处理的最大记录数。接受的值最高为Data Collector的最大批处理大小。默认值是1000 数据采集器默认设置为1000。 |

3. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                               |
   | :----------------------------------------------------------- | :------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/MapRFS.html#concept_bk4_dj4_lx) | 要读取的数据类型。使用以下选项之一：阿夫罗定界文本 |

4. 对于Avro数据，在“ **数据格式”**选项卡上，配置以下属性：

   | Avro物业             | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | Avro模式位置         | 处理数据时要使用的Avro模式定义的位置：消息/数据包含架构-在文件中使用架构。在“管道配置”中-使用阶段配置中提供的架构。Confluent Schema Registry-从Confluent Schema Registry检索架构。在阶段配置中或在Confluent Schema Registry中使用架构可以提高性能。 |
   | Avro模式             | 用于处理数据的Avro模式定义。覆盖与数据关联的任何现有模式定义。您可以选择使用该 `runtime:loadResource`函数来加载存储在运行时资源文件中的架构定义。 |
   | 架构注册表URL        | 汇合的架构注册表URL，用于查找架构。要添加URL，请单击**添加**，然后以以下格式输入URL：`http://:` |
   | 基本身份验证用户信息 | 使用基本身份验证时连接到Confluent Schema Registry所需的用户信息。`schema.registry.basic.auth.user.info`使用以下格式从Schema Registry中的设置中输入密钥和机密 ：`:`**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 查找架构             | 在Confluent Schema Registry中查找架构的方法：主题-查找指定的Avro模式主题。架构ID-查找指定的Avro架构ID。覆盖与数据关联的任何现有模式定义。 |
   | 模式主题             | Avro架构需要在Confluent Schema Registry中查找。如果指定的主题具有多个架构版本，则源使用该主题的最新架构版本。要使用旧版本，请找到相应的架构ID，然后将“ **查找架构**依据 **”**属性设置为“架构ID”。 |
   | 架构编号             | 在Confluent Schema Registry中查找的Avro模式ID。              |

5. 对于定界数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 定界财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 分隔符格式类型                                               | 分隔符格式类型。使用以下选项之一：**默认CSV-**包含逗号分隔值的文件。忽略文件中的空行。**RFC4180 CSV-**严格遵循RFC4180准则的逗号分隔文件。**MS Excel CSV** -Microsoft Excel逗号分隔文件。**MySQL CSV** -MySQL逗号分隔文件。**制表符分隔的值** -包含制表符分隔的值的文件。**PostgreSQL CSV** -PostgreSQL逗号分隔文件。**PostgreSQL文本** -PostgreSQL文本文件。**自定义** -使用用户定义的定界符，转义符和引号字符的文件。**多字符**定界-使用多个用户定义的字符定界字段和行以及单个用户定义的转义和引号字符的文件。 |
   | 标题行                                                       | 指示文件是否包含标题行以及是否使用标题行。                   |
   | 允许额外的列                                                 | 使用标题行处理数据时，允许处理的记录列数超过标题行中的列数。 |
   | 额外的列前缀                                                 | 用于任何其他列的前缀。额外的列使用前缀和顺序递增的整数来命名，如下所示： ``。例如，`_extra_1`。默认值为 `_extra_`。 |
   | 最大记录长度（字符）                                         | 记录的最大长度（以字符为单位）。较长的记录无法读取。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
   | 分隔符                                                       | 自定义分隔符格式的分隔符。选择一个可用选项，或使用“其他”输入自定义字符。您可以输入使用格式为Unicode控制符\uNNNN，其中*ñ*是数字0-9或字母AF十六进制数字。例如，输入 \u0000以使用空字符作为分隔符或 \u2028使用行分隔符作为分隔符。默认为竖线字符（\|）。 |
   | 多字符字段定界符                                             | 用于分隔多字符定界符格式的字段的字符。默认值为两个竖线字符（\|\|）。 |
   | 多字符行定界符                                               | 以多字符定界符格式分隔行或记录的字符。默认值为换行符（\ n）。 |
   | 转义符                                                       | 自定义或多字符定界符格式的转义字符。                         |
   | 引用字符                                                     | 自定义或多字符定界符格式的引号字符。                         |
   | 启用评论                                                     | 自定义定界符格式允许注释的数据被忽略。                       |
   | 评论标记                                                     | 为自定义定界符格式启用注释时，标记注释的字符。               |
   | 忽略空行                                                     | 对于自定义分隔符格式，允许忽略空行。                         |
   | [根字段类型](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Delimited.html#concept_zcg_bm4_fs) | 要使用的根字段类型：列表映射-生成数据索引列表。使您能够使用标准功能来处理数据。用于新管道。列表-生成带有索引列表的记录，该列表带有标头和值的映射。需要使用定界数据功能来处理数据。仅用于维护在1.1.0之前创建的管道。 |
   | 跳过的线                                                     | 读取数据前要跳过的行数。                                     |
   | 解析NULL                                                     | 将指定的字符串常量替换为空值。                               |
   | 空常量                                                       | 字符串常量，用空值替换。                                     |
   | 字符集                                                       | 要处理的文件的字符编码。                                     |
   | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |

6. 对于文本数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 文字属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 最大线长                                                     | 一行允许的最大字符数。较长的行被截断。向记录添加一个布尔字段，以指示该记录是否被截断。字段名称为“截断”。此属性可以受数据收集器解析器缓冲区大小的限制。有关更多信息，请参见[最大记录大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_svg_2zl_d1b)。 |
   | [使用自定义分隔符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/TextCDelim.html#concept_lg2_gcg_jx) | 使用自定义定界符来定义记录而不是换行符。                     |
   | 自定义定界符                                                 | 用于定义记录的一个或多个字符。                               |
   | 字符集                                                       | 要处理的文件的字符编码。                                     |
   | [忽略控制字符](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ControlCharacters.html#concept_hfs_dkm_js) | 除去制表符，换行符和回车符以外的所有ASCII控制字符。          |