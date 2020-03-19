# 雪花

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310202435311.png) 资料收集器

Snowflake目标将数据写入Snowflake数据库中的一个或多个表。您可以使用Snowflake目标写入任何可访问的Snowflake数据库，包括Amazon S3，Microsoft Azure和私有Snowflake安装上托管的数据库。

Snowflake目标将CSV文件转储到Amazon S3或Microsoft Azure中的内部Snowflake阶段或外部阶段。然后，目标将命令发送到Snowflake以处理已暂存的文件。可以使用[JDBC Producer目标](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/JDBCProducer.html#concept_kvs_3hh_ht)直接写入Snowflake ，但出于性能原因不建议使用。

您可以使用Snowflake目标将新数据写入或将数据捕获（CDC）数据更改为Snowflake。处理新数据时，目标可以使用COPY命令或Snowpipe将数据加载到Snowflake。处理CDC数据时，目标使用MERGE命令。

Snowflake目标根据匹配的名称将数据从记录字段写入表列。当新字段和表引用出现在记录中时，目标可以通过在Snowflake中创建新的列和表来补偿数据漂移。

配置Snowflake目标时，可以指定Snowflake区域，帐户和连接信息以及用于写入Snowflake的连接数。您还可以根据需要定义其他Snowflake连接属性。

您可以配置要使用的Snowflake仓库，数据库，架构和表，还可以选择启用属性来处理数据漂移。您可以指定加载方法属性和登台详细信息，还可以选择定义Amazon S3或Microsoft Azure的高级属性。

您可以配置行的根字段，以及要从记录中排除的任何第一级字段。您还可以配置目标，以使用指定的默认值替换缺少的字段或包含无效数据类型的字段，并以指定的字符替换字符串字段中的换行符。您可以指定引号模式，定义引号和转义符，并配置目标以修剪空格。

处理CDC数据时，可以为每个表指定主键列，也可以使目标查询Snowflake获得该信息。

在使用Snowflake目标之前，必须安装Snowflake阶段库并完成其他[先决任务](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_ysy_fcj_ggb)。Snowflake 阶段库是一个[Enterprise阶段库](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Installation/EnterpriseStageLibraries.html#concept_s1r_1gg_dhb)，仅供开发用途免费。有关购买用于生产的舞台库的信息，请[联系StreamSets](https://streamsets.com/contact-us/)。

**注意：**使用Snowflake目标时，StreamSets建议将Snowflake仓库配置为在收到新查询时自动恢复。

## 样例用例

以下是使用Snowflake目标的几种常见方案：

- 复制数据库

  假设您要复制写入Oracle数据库模式中五个表的数据。您想将现有数据和传入CDC数据都写入Snowflake。为此，您创建了两个管道，一个用于加载现有数据，另一个用于处理传入数据，如下所示：复制数据的第一个管道-第一个管道使用多线程JDBC Multitable Consumer起源从要复制的表中读取。要利用Snowflake的批量加载功能，您可以将原点配置为使用非常大的批处理大小-每批介于20,000至50,000条记录之间。您可以将线程数设置为五个，以同时读取所有五个表中的数据，并将连接池的大小增加到五个，以允许同时写入Snowflake中的五个表。在管道中，可以使用所需数量的处理器来处理数据。然后，您将Snowflake目标配置为将数据加载到Snowflake。如果希望将数据写入Snowflake后立即可用，则可以使用默认的COPY命令加载方法。但是，由于可以忍受一点延迟，因此可以使用更快，更便宜的Snowpipe来加载数据。使用Snowpipe需要在Snowflake中执行一些[必备步骤](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_nzd_mgj_2gb)。初始加载完成后，您将停止第一个管道并启动第二个管道以处理传入的CDC数据。CDC数据的第二条管道-在第二条管道中，您使用Oracle CDC客户端源和Snowflake目标。您也可以将此来源配置为使用非常大的批次大小，每批次介于20,000至50,000条记录之间。在目标中，选择“使用CDC”属性在写入Snowflake时执行CRUD操作。这将导致使用MERGE命令将数据加载到Snowflake中的目标。您可以在记录中指定一个字段，其中包含写入到Snowflake时要使用的表名，并为每个表定义键列，或者将目标配置为查询Snowflake以获取键列。为了提高性能，您还增加了连接池的大小。有关更多信息，请参见 [性能优化](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_mzv_wjw_2gb)。

- 从Hadoop卸载

  假设您有一个要移入Snowflake的Hadoop数据湖。在这种情况下，您只需要一个管道即可，该管道包括多线程Hadoop FS Standalone源，所需的所有处理器以及Snowflake目标。

  在Snowflake目标中，如果可以在写入数据后在数据可用性方面留一点延迟，则可以使用Snowpipe。但是Snowpipe只写已经存在的表。要允许目标创建新表来补偿漂移数据，请使用默认的COPY命令加载数据。如前所述，您可以使用大批处理，多个线程和多个连接来[优化管道性能](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_mzv_wjw_2gb)。

  由于数据湖中的数据可能不如典型的数据库数据那么结构化，因此请在目标位置配置以下数据偏移量和高级数据属性以平滑过渡：启用“数据漂移”属性，以允许在出现新字段时在表中创建新列。启用表自动创建属性以根据需要创建新表。为避免生成不必要的错误记录：使用“忽略缺少的字段”属性将丢失的数据替换为默认值。使用忽略无效类型的字段属性将无效数据类型的数据替换为默认值。定义用于每种Snowflake数据类型的默认值。用指定的字符替换字符串字段中的换行符。

## 先决条件

在配置Snowflake目标之前，请完成以下先决条件：

1. [安装Snowflake舞台库](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#task_gyy_wkw_2gb)。

2. 创建一个内部或外部Snowflake阶段

   。

   如果要在Snowflake内部用户界面上暂存数据，则可以跳过此步骤。

3. 要使用COPY命令加载数据，请完成[COPY先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_iqj_4cj_5jb)。

4. 要使用Snowpipe加载数据，请完成[Snowpipe前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_nzd_mgj_2gb)。

5. 要使用MERGE命令加载数据，请完成[MERGE先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_b3x_zt4_5jb)。

### 安装雪花舞台库

您必须先安装Snowflake舞台库，然后才能使用Snowflake目标。Snowflake阶段库包括目标用来访问Snowflake的Snowflake JDBC驱动程序。

Snowflake 阶段库是一个Enterprise阶段库，仅供开发用途免费。有关购买用于生产的舞台库的信息，请[联系StreamSets](https://streamsets.com/contact-us/)。

您可以使用Package Manager来安装Enterprise阶段库以进行tarball Data Collector的安装，也可以将其作为定制阶段库来进行tarball，RPM或Cloudera Manager Data Collector的 安装。

#### 支持的版本

下表列出了与特定的Data Collector 版本一起使用的Snowflake Enterprise阶段库的版本：

| 数据收集器版本                 | 支持的舞台库版本                                             |
| :----------------------------- | :----------------------------------------------------------- |
| Data Collector 3.8.x及更高版本 | Snowflake Enterprise Library 1.0.1、1.0.2、1.1.0、1.2.0或1.3.0 |
| 数据收集器 3.7.x               | 雪花企业库1.0.1                                              |

#### 使用软件包管理器安装

您可以使用Package Manager在tarball Data Collector 安装中安装Snowflake阶段库。

1. 单击“程序包管理器”图标：![img](imgs/icon_PackageManager-20200310202435852.png)。

2. 在导航面板中，单击**Enterprise Stage Libraries**。

3. 选择**Snowflake Enterprise Library**，然后单击 **Install**图标：![img](imgs/icon_InstallLib-20200310202435668.png)。

4. 阅读StreamSets 订阅服务条款。如果您同意，请选中复选框，然后单击“ **安装”**。

   Data Collector将安装所选的舞台库。

5. 重新启动Data Collector。

#### 作为自定义舞台库安装

您可以在tarball，RPM或Cloudera Manager Data Collector 安装中将Snowflake Enterprise阶段库安装为自定义阶段库。

1. 要下载舞台库，请转到[StreamSets下载企业连接器](https://streamsets.com/download/enterprise-connectors/)页面。

   该网页显示按发布日期组织的Enterprise阶段库，并在页面顶部显示最新版本。

2. 单击您要下载的Enterprise阶段库名称和版本。

3. 在“ **下载企业连接器”**表单中，输入您的姓名和联系信息。

4. 阅读StreamSets订阅服务条款。如果您同意，请接受服务条款，然后单击“ **提交”**。

   舞台库下载。

5. 将Enterprise阶段库安装和管理为自定义阶段库。

   有关更多信息，请参见[Custom Stage Libraries](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/CustomStageLibraries.html#concept_pmc_jk1_1x)。

### 创建一个雪花舞台

在管道中使用目标之前，必须创建一个Snowflake内部或外部阶段。

Snowflake目标将CSV文件转储到Amazon S3或Microsoft Azure中的内部Snowflake阶段或外部阶段。然后，目标将命令发送到Snowflake以处理已暂存的文件。

要使用外部平台，请使用托管Snowflake仓库的云服务提供商来创建外部平台。

根据需要创建以下雪花阶段之一：

- 雪花内部舞台

  您可以在Snowflake内部用户阶段或命名阶段中暂存数据。不要使用内部表阶段。

  默认情况下，为每个用户创建用户阶段。有关如何创建命名阶段的步骤，请参见Snowflake SQL命令参考文档中的[CREATE STAGE](https://docs.snowflake.net/manuals/sql-reference/sql/create-stage.html#create-stage)。

  您可以对用户阶段和命名阶段都使用默认的Snowflake配置。

  有关Snowflake阶段的更多信息，请参见[Snowflake文档](https://docs.snowflake.net/manuals/user-guide/data-load-local-file-system-create-stage.html)。

- Amazon S3外部舞台

  要在Amazon S3外部阶段中暂存数据，请在托管Snowflake虚拟仓库的同一S3区域中的存储桶中创建Snowflake外部阶段。例如，如果您的Snowflake仓库在AWS US West内，则在AWS US West区域的存储桶中创建Snowflake外部平台。

  创建Snowflake外部舞台时，可以指定一个URL，该URL定义舞台的名称和位置。在URL中使用斜杠以确保Snowflake加载所有暂存的数据。您还可以在阶段名称中包含前缀，以指示外部阶段用于Data Collector。

  例如，以下URL创建一个名为的外部阶段 `sdc-externalstage`， `s3://mybucket/`并将所有阶段数据加载到Snowflake：`s3://mybucket/sdc-externalstage/`

  您可以使用Snowflake Web界面或SQL创建S3阶段。有关更多信息，请参见Snowflake文档中的[创建S3舞台](https://docs.snowflake.net/manuals/user-guide/data-load-s3-create-stage.html)。

- Microsoft Azure外部阶段

  要在Microsoft Azure外部阶段中暂存数据，请完成以下任务：为要使用的Microsoft Azure Blob存储容器配置Snowflake身份验证。您可以使用SAS令牌或Azure帐户名称和密钥进行身份验证。有关配置SAS令牌身份验证的信息，请参阅 Snowflake文档中的[配置Azure容器以加载数据](https://docs.snowflake.net/manuals/user-guide/data-load-azure-config.html)。在容器中创建一个Snowflake外部舞台。创建Snowflake外部舞台时，可以指定一个URL，该URL定义舞台的名称和位置。在URL中使用斜杠以确保Snowflake加载所有暂存的数据。您还可以在阶段名称中包含前缀，以指示外部阶段用于Data Collector。例如，以下URL创建一个名为的外部阶段`sdc-externalstage`， `azure://myaccount.blob.core.windows.net/mycontainer/load/` 并将所有阶段数据加载到Snowflake：`azure://myaccount.blob.core.windows.net/mycontainer/load/sdc-externalstage/`您可以使用Snowflake Web界面或SQL创建一个Azure阶段。有关更多信息，请参见Snowflake文档中的[创建Azure阶段](https://docs.snowflake.net/manuals/user-guide/data-load-azure-create-stage.html)。

#### AWS凭证



当Snowflake目标在Amazon S3上暂存数据时，它必须将凭证传递给Amazon Web Services。

使用以下方法之一来传递AWS凭证：

- IAM角色

  当执行数据收集器 在Amazon EC2实例上运行时，您可以使用AWS管理控制台为EC2实例配置IAM角色。Data Collector使用IAM实例配置文件凭证自动连接到AWS。

  要使用IAM角色，**请** 在目标中选择“ **使用IAM角色”**属性。

  有关将IAM角色分配给EC2实例的更多信息，请参阅Amazon EC2文档。

- AWS访问密钥对

  当Snowflake目标使用AWS访问密钥对访问AWS时，您必须在目标中指定**访问密钥ID**和**秘密访问密钥**属性。**提示：**为了保护敏感信息（例如访问密钥对）的安全，可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。

**注意：**要在Amazon S3上暂存数据，您使用的角色或访问密钥对必须具有写入Amazon S3所需的权限，包括s3：GetBucketLocation和s3：PutObject。

### COPY先决条件

处理新数据时，可以配置目标以使用COPY命令将[数据加载到Snowflake表](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_w35_vsq_2gb)。

使用COPY命令来加载数据需要具有以下一组访问特权之一的角色：

- 使用内部Snowflake阶段时所需的特权：

  | 对象类型     | 特权       |
  | :----------- | :--------- |
  | 内部雪花阶段 | 读，写     |
  | 表           | 选择，插入 |

- 使用外部舞台时所需的特权：

  | 对象类型 | 特权       |
  | :------- | :--------- |
  | 外部舞台 | 用法       |
  | 表       | 选择，插入 |

如有必要，请在Snowflake中创建一个自定义角色，并为该角色授予所需的访问权限。然后在Snowflake中，将自定义角色分配为目标中配置的Snowflake用户帐户的默认角色，或者将自定义角色定义为目标的其他Snowflake连接属性。

有关为目标[定义角色的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_w3y_d14_5jb)更多信息，请参见[定义角色](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_w3y_d14_5jb)。

### Snowpipe先决条件

在处理新数据时，您可以使用Snowpipe连续提取引擎Snowpipe将[数据加载到Snowflake表中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_w35_vsq_2gb)。

使用Snowpipe之前，请完成以下先决条件：

1. 在Snowflake中，为Snowpipe创建一个管道以用于加载数据。

   有关创建管道的更多信息，请参见[Snowflake文档](https://docs.snowflake.net/manuals/user-guide/data-load-snowpipe-rest-gs.html#step-2-create-a-pipe)。

2. 在Snowflake中，生成私钥PEM和公钥PEM。

   有关密钥对身份验证的详细信息，请参见[Snowflake文档](https://docs.snowflake.net/manuals/user-guide/data-load-snowpipe-rest-gs.html#using-key-pair-authentication)。您无需按照步骤5中所述生成JSON Web令牌（JWT）。

   配置目标时，可以指定私钥PEM和密码以及公钥PEM。

3. 在Snowflake中，将公钥分配给目标中配置的Snowflake用户帐户。

   您可以使用Snowflake控制台或ALTER USER命令。

4. （可选）要保护私钥PEM和密码，请使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。

5. 在Snowflake中，创建一个自定义角色并向该角色授予使用Snowpipe所需的访问权限。

   有关所需的Snowpipe访问权限的列表，请参见[Snowflake文档](https://docs.snowflake.net/manuals/user-guide/data-load-snowpipe-rest-gs.html#granting-access-privileges)。

6. 然后在Snowflake中，将自定义角色分配为目标中配置的Snowflake用户帐户的默认角色，或者将自定义角色定义为目标的其他Snowflake连接属性。

   有关为目标[定义角色的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_w3y_d14_5jb)更多信息，请参见[定义角色](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_w3y_d14_5jb)。

### 合并先决条件

处理CDC数据时，可以配置目标以使用MERGE命令将[数据加载到Snowflake表](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_w35_vsq_2gb)。

使用MERGE命令加载数据需要具有以下一组访问特权之一的角色：

- 使用内部Snowflake阶段时所需的特权：

  | 对象类型     | 特权                   |
  | :----------- | :--------------------- |
  | 内部雪花阶段 | 读，写                 |
  | 表           | 选择，插入，更新，删除 |

- 使用外部舞台时所需的特权：

  | 对象类型 | 特权                   |
  | :------- | :--------------------- |
  | 外部舞台 | 用法                   |
  | 表       | 选择，插入，更新，删除 |

如有必要，请在Snowflake中创建一个自定义角色，并为该角色授予所需的访问权限。然后在Snowflake中，将自定义角色分配为目标中配置的Snowflake用户帐户的默认角色，或者将自定义角色定义为目标的其他Snowflake连接属性。

有关为目标[定义角色的](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_w3y_d14_5jb)更多信息，请参见[定义角色](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_w3y_d14_5jb)。

## 加载方式

Snowflake目标可以使用以下方法将数据加载到Snowflake：

- COPY命令以获取新数据

  COPY命令是默认的加载方法，它将对Snowflake执行批量同步加载，将所有记录视为INSERTS。使用此方法将新数据写入Snowflake表。

  COPY命令提供对写入数据的实时访问。但是，这确实会产生Snowflake仓库使用费，目前这笔费用会四舍五入到小时。使用[推荐的准则](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_mzv_wjw_2gb)来优化性能和成本效益。

  由于COPY命令是默认的加载方法，因此您无需配置Snowflake目标即可使用此命令。

- Snowpipe获取新数据

  Snowpipe是Snowflake连续摄取服务，它对Snowflake执行异步加载，将所有记录视为INSERTS。使用此方法将新数据写入Snowflake表。需要时，您可以将目标配置为使用自定义Snowflake端点。

  Snowpipe提供了稍微延迟的数据访问权限，通常不到一分钟。Snowpipe仅对用于执行写入操作的资源收取Snowflake费用。

  使用Snowpipe之前，请执行[必备步骤](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_nzd_mgj_2gb)。另外，请使用[推荐的准则](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_mzv_wjw_2gb)来优化性能和成本效益。

  要使用Snowpipe加载新数据，请在目标的Snowflake选项卡上启用Use Snowpipe属性。然后，在“ Snowpipe”选项卡上配置属性。

- CDC数据的MERGE命令

  像COPY命令一样，MERGE命令对Snowflake执行批量同步加载。但是，不是将所有记录都视为INSERT，而是适当地插入，更新和删除记录。使用此方法通过CRUD操作将更改数据捕获（CDC）数据写入Snowflake表。

  就像COPY命令一样，MERGE命令提供对写入数据的实时访问。并且，它会产生Snowflake仓库使用费，该费用目前已整整为一个小时。

  使用[推荐的准则](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_mzv_wjw_2gb)来优化性能和成本效益。

  **重要：**为了保持数据的原始顺序，在处理CDC数据时不要使用多线程或群集执行模式。

  要使用MERGE命令加载CDC数据，请在目标的“数据”选项卡上选择CDC数据属性。然后，输入“雪花”列以用作键列。

有关Snowpipe或COPY或MERGE命令的更多信息，请参见Snowflake文档。

## 定义角色

Snowflake目标需要一个Snowflake角色，该角色授予使用配置的load方法加载数据所需的所有特权。每个加载方法都需要一组不同的特权。

在配置目标之前，请确保您已授予Snowflake角色所需的特权，如[前提条件中所述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_ysy_fcj_ggb)。

如果使用所需的特权创建自定义角色，请通过以下方式之一定义目标使用的角色：

- 将自定义角色分配为用户的默认角色

  在Snowflake中，将自定义角色分配为目标中配置的Snowflake用户帐户的默认角色。Snowflake用户帐户与一个默认角色相关联。

- 用自定义角色覆盖用户的默认角色

  在配置Snowflake目标时，在Data Collector中将自定义角色的名称指定为其他Snowflake连接属性。在附加连接属性中指定的自定义角色将覆盖分配给Snowflake用户帐户的默认角色。

  例如，您可以在Snowflake中为特定数据源定义自定义角色，然后仅在使用Snowflake目标加载数据时指定这些自定义角色。

  在“雪花连接信息”选项卡上，添加其他连接属性，然后按如下所示定义自定义角色：名称-输入role。值-输入自定义角色的名称。例如，您可以输入copy_load。

## 性能优化

在使用Snowflake目标时，请使用以下技巧来优化性能和成本效益：

- 增加批次大小

  最大批次大小由管道中的来源决定，通常默认值为1,000条记录。要利用Snowflake的批量加载功能，请将管道来源中的最大批处理大小增加到20,000-50,000条记录。确保根据需要增加Data Collector的 [Java堆大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/JavaHeapSize.html#concept_mdc_shg_qr)。

  **重要：**强烈建议增加批次大小。使用默认的批量大小可能会很慢且成本很高。

- 使用多个线程

  使用Snowpipe或COPY命令写入Snowflake时，在管道中包含[多线程源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Multithreaded_Pipelines/MultithreadedPipelines.html#concept_wcz_tpd_py)时，可以使用多个线程来提高性能。当Data Collector资源允许时，使用多个线程可以并发处理多批数据。

  与增加批处理大小一样，在使用多个线程时，应确保适当地设置了Data Collector [Java堆大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/JavaHeapSize.html#concept_mdc_shg_qr)。

  **注意：**请勿使用多个线程通过MERGE命令将CDC数据写入Snowflake。使用多个线程处理数据时，不会保留数据的原始顺序。

- 启用与Snowflake的其他连接

  使用COPY或MERGE命令写入多个Snowflake表时，请增加Snowflake目标与Snowflake的连接数。每个其他连接都允许目标同时写入另一个表。

  例如，当仅通过一个连接写入10个表时，目标一次只能写入一个表。通过5个连接，目标可以一次写入5个表。10个连接允许同时写入所有10个表。

  默认情况下，目标对标准单线程管道使用一个连接。在多线程管道中，目标与管道使用的线程数匹配。也就是说，将多线程源配置为使用最多3个线程时，默认情况下，Snowflake目标使用3个连接写入Snowflake，每个线程一个。请注意，连接数是针对整个管道的，而不是针对每个线程的。因此，当使用多个线程写入多个表时，您还可以通过分配其他连接来提高性能。例如，当使用3个线程写入3个表时，可以将连接数增加到9，以实现最大吞吐量。使用“连接池大小”属性可以指定Snowflake目标可以使用的最大连接数。使用COPY或MERGE命令写入Snowflake时，请使用此属性。使用Snowpipe时，增加连接数不会提高性能。

## 行生成

将记录写到表时，默认情况下，雪花目标包含结果行中的所有记录字段。目标使用根字段， `/`作为结果行的基础。

您可以配置“行字段”属性，以指定记录中的地图或列表地图字段作为该行的基础。结果记录仅包含来自指定映射或列表映射字段的数据，而排除所有其他记录数据。当您要写入Snowflake的数据存在于记录中的单个地图或列表地图字段中时，请使用此功能。

如果要使用根字段，但不想在结果行中包括所有字段，则可以将目标配置为忽略所有指定的第一级字段。

Snowflake目标会将指定根字段内的所有地图或列表地图字段转换为Snowflake Variant数据类型。Snowflake目标完全支持Variant数据类型。

默认情况下，缺少字段或字段中的数据类型无效的记录将被视为错误记录。您可以配置目标，以用用户定义的默认值替换缺少的字段和无效类型的数据。然后，您指定要用于每种数据类型的默认值。您还可以配置目标，以使用替换字符替换字符串字段中的换行符。

## 写入多个表

您可以使用Snowflake目标写入雪花模式中的多个表。要写入多个表，请在记录中指定一个字段，该字段指定要写入的表。

例如，假设您有以公司部门（例如运营，销售和市场部）命名的Snowflake表。此外，正在处理的记录具有一个`dept`具有匹配值的 字段。您可以通过输入表属性下面的表达式配置雪花写入目的地的记录到各种表：`${record:value('/dept')}`。

使用COPY或MERGE命令加载数据时，可以将Snowflake目标配置为在指定字段中出现新值时自动创建表。例如，如果该`dept`字段突然包括 `Engineering`部门，则目的地可以在Snowflake中为新数据创建一个新的Engineering表。有关更多信息，请参见[创建数据漂移的列和表](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_cn1_msp_2gb)。

使用命令写入多个Snowflake表时，您可能还会增加目标用于写入的连接数。有关更多信息，请参见 [性能优化](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_mzv_wjw_2gb)。

## 创建用于数据漂移的列和表

您可以将Snowflake目标配置为自动补偿列或表要求的变化，也称为数据漂移。

启用数据漂移后，当新字段出现在记录中时，Snowflake目标将在Snowflake表中创建新列。例如，如果一条记录突然包含一个新`Address2`字段，则目标将`Address2`在目标表中创建一个新列。

默认情况下，目标根据新字段中的数据创建新列，例如为十进制数据创建一个Double列。但是，您可以配置目标以将所有新列创建为Varchar。

启用数据漂移后，您还可以配置目标以根据需要创建新表。例如，假设目的地根据`Region`字段中的区域名称将数据写入表中。当记录中出现新`SW-3`区域时，目标将`SW-3`在Snowflake中创建一个新表并将该记录写入新表中。

您可以使用此功能在空的Snowflake数据库架构中创建所有必要的表。

**注意：**由于Snowflake的限制，使用Snowpipe将数据加载到Snowflake时，目标无法创建表。仅当使用COPY或MERGE命令加载数据时，目标才能创建表。

要启用自动创建新列的功能，请在“雪花”选项卡上选择“启用数据漂移”属性。然后，要启用新表的创建，请选择“表自动创建”属性。

### 生成的数据类型

在创建新表或在现有表中创建新列时，Snowflake目标使用字段名称来生成新列名称。

您可以配置目标以将所有新列创建为Varchar。但是，默认情况下，Snowflake目标创建的列如下：

| 记录字段数据类型       | 雪花列数据类型 |
| :--------------------- | :------------- |
| 字节数组               | 二元           |
| 烧焦                   | 烧焦           |
| 串                     | Varchar        |
| 字节，整数，长，短     | 数             |
| 十进制，双精度，浮点型 | 双             |
| 布尔型                 | 布尔型         |
| 日期                   | 日期           |
| 约会时间               | Timestampntz   |
| 时间                   | 时间           |
| 分区日期时间           | 时间戳         |
| 地图，列表地图         | 变体           |

Snowflake目标完全支持Variant数据类型。

## 定义CRUD操作

将目标配置为处理CDC数据时，Snowflake目标可以插入，更新或删除数据。处理CDC数据时，目标使用MERGE命令将数据写入Snowflake。

写入数据时，Snowflake目标使用sdc.operation.type记录头属性中指定的CRUD操作。目标根据以下数值执行操作：

- INSERT为1
- 2个代表删除
- 3更新

如果您的管道包括启用CRUD的原始数据源，该原始数据元处理已更改的数据，则目标位置仅从`sdc.operation.type`原始数据源生成的标头属性中读取操作类型 。如果管道使用非CDC来源，则可以使用表达式评估器或脚本处理器来定义记录头属性。有关Data Collector 更改的数据处理以及启用CDC的来源的列表的详细信息 ，请参阅 [处理更改的数据。](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/CDC-Overview.html#concept_apw_l2c_ty)。

## 配置雪花目标

配置Snowflake目标以将数据写入Snowflake表。在管道中使用目标之前，请完成[所需的先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_ysy_fcj_ggb)。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **雪花连接信息”**选项卡上，配置以下属性：

   | 雪花连接属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 雪花地区                                                     | 雪花仓库所在的区域。选择以下选项之一：可用的雪花区域。其他-用于指定上面未列出的雪花区域。自定义JDBC URL-用于指定虚拟私有Snowflake安装。 |
   | 定制雪花区                                                   | 自定义雪花区域。在将其他用作雪花区域时可用。                 |
   | 虚拟私人雪花网址                                             | 使用虚拟私有Snowflake安装时要使用的自定义JDBC URL。          |
   | 帐户                                                         | 雪花帐户名称。                                               |
   | 用户                                                         | 雪花用户名。                                                 |
   | 密码                                                         | 雪花密码。                                                   |
   | [连接池大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_w15_crp_2gb) | 目标用来写入Snowflake的最大连接数。默认值为0，以确保目标使用与管道使用的线程相同数量的连接。使用COPY或MERGE命令写入多个表时，增加此属性可以提高性能。 |
   | 连接属性                                                     | 要使用的其他Snowflake连接属性。例如，您可以添加用于加载数据的自定义角色的名称，如[定义角色中所述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_w3y_d14_5jb)。要添加属性，请单击 **添加**并定义属性名称和值。使用Snowflake期望的属性名称和值。 |

3. 在“ **雪花”**选项卡上，配置以下属性：

   | 雪花                                                         | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 仓库                                                         | 雪花仓库。                                                   |
   | 数据库                                                       | 雪花数据库。                                                 |
   | 架构图                                                       | 雪花模式。                                                   |
   | 表                                                           | 要写入的雪花表。要写入单个表，请输入表名称。要写入[多个表](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_w15_crp_2gb)，请输入一个表达式，其结果为包含表名的记录中的字段。例如： `${record:value('/table')}`或者，要基于`jdbc.table`由JDBC Multitable Consumer起源定义的记录头属性中的表名写入表 ，可以使用以下表达式： `${record:attribute('jdbc.tables')}` |
   | 使用雪管                                                     | 启用使用Snowpipe写入Snowflake。仅在处理新数据时使用。您不能使用Snowpipe来加载CDC数据或自动创建表时。启用Snowpipe之前，请执行必要的[先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_nzd_mgj_2gb)。有关Snowpipe和其他加载方法的更多信息，请参见[加载方法](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_w35_vsq_2gb)。有关优化管道性能的信息，请参阅[性能优化](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_mzv_wjw_2gb)。 |
   | 大写模式和字段名称                                           | 将所有模式，表和字段名称转换为所有大写字母。启用后，目标创建的任何新表或字段也将使用所有大写字母。 |
   | [启用数据漂移](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_cn1_msp_2gb) | 当新字段出现在记录中时，使目标能够在Snowflake表中创建新列。  |
   | [表自动创建](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_cn1_msp_2gb) | 在需要时启用自动创建表的功能。启用“数据漂移”属性并且禁用“使用雪管”属性时可用。由于Snowpipe只能写入新表，因此使用Snowpipe加载数据时，目标不会创建新表。如果未显示此属性，请清除“使用Snowpipe”属性。 |
   | 创建新列作为Varchar                                          | 使目标能够将所有新列创建为Varchar。默认情况下，目标根据字段中[的数据类型](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_n31_3s4_ggb)创建新列。 |

4. 使用Snowpipe加载数据时，在**Snowpipe**选项卡上，配置以下属性：

   | 雪管物业               | 描述                                                         |
   | :--------------------- | :----------------------------------------------------------- |
   | 管                     | 使用Snowpipe将数据加载到Snowflake时要使用的管道。仅在处理新数据时使用Snowpipe。在配置这些Snowpipe属性之前，请确保完成所有[Snowpipe必备](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_nzd_mgj_2gb)任务。 |
   | 私钥PEM                | 私钥PEM。在Snowflake中生成，作为 [Snowpipe必备](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_nzd_mgj_2gb)任务的一部分。为了保护敏感信息，可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 私钥密码               | 私钥密码。在Snowflake中生成，作为[Snowpipe必备](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_nzd_mgj_2gb)任务的一部分。 |
   | 公钥PEM                | 公钥PEM。在Snowflake中生成，作为 [Snowpipe必备](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_nzd_mgj_2gb)任务的一部分。 |
   | 使用自定义Snowpipe端点 | 启用使用自定义Snowpipe端点。                                 |
   | 自定义Snowpipe协议     | 自定义Snowpipe端点的协议：HTTPHTTPS                          |
   | 自定义Snowpipe主机     | 自定义Snowpipe端点的主机名。                                 |
   | 自定义雪管端口         | 自定义Snowpipe端点的端口号。                                 |

5. 在“ **暂存”**选项卡上，配置以下属性：

   | 临时物业                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 舞台位置                                                     | 雪花舞台的位置：亚马逊S3Azure Blob存储雪花内部舞台此属性配置确定在此选项卡和“登台高级”选项卡上显示的属性。 |
   | 舞台数据库                                                   | Snowflake阶段的可选数据库。当阶段位于与Snowflake表不同的数据库中时，请配置此属性。如果未定义，目标将使用“雪花”选项卡上为雪花表定义的数据库。 |
   | 阶段架构                                                     | Snowflake阶段的可选模式。当阶段位于与Snowflake表不同的架构中时，请配置此属性。如果未定义，目标将使用在“雪花”选项卡上为雪花表定义的架构。 |
   | 雪花的舞台名称                                               | 用于暂存数据的Snowflake暂存器的名称。除非使用Snowflake内部用户阶段，否则您将此阶段创建为[Snowflake前提任务的一部分](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_ysy_fcj_ggb)。要使用Snowflake内部用户界面，请输入波浪号（`~`）。 |
   | 摄取后清除阶段文件                                           | 将阶段文件的数据写入Snowflake后，将其删除。使用Snowpipe写入Snowflake时请勿使用。 |
   | [使用IAM角色](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_sm4_l1s_hgb) | 启用使用IAM角色写入Amazon S3上的外部阶段。仅当Data Collector在Amazon EC2实例上运行时使用。 |
   | [AWS访问密钥ID](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_sm4_l1s_hgb) | AWS访问密钥ID。不使用IAM角色写入Amazon S3上的外部阶段时需要。 |
   | [AWS密钥ID](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_sm4_l1s_hgb) | AWS秘密访问密钥。不使用IAM角色写入Amazon S3上的外部阶段时需要。 |
   | S3 Stage文件名前缀                                           | 外部舞台名称的可选前缀。                                     |
   | S3压缩文件                                                   | 在将文件写入S3之前启用压缩文件。保持此选项为最佳性能。       |
   | Azure身份验证                                                | 用于连接到Azure的身份验证类型：帐户名称和密钥SAS令牌         |
   | Azure帐户名称                                                | Azure帐户名称。                                              |
   | Azure帐户密钥                                                | Azure帐户密钥。仅用于帐户名和密钥验证。为了保护敏感信息， 可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | Azure SAS令牌                                                | Azure SAS令牌。仅用于SAS令牌认证。为了保护敏感信息， 可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | Azure Stage文件名前缀                                        | 外部舞台名称的可选前缀。                                     |
   | Azure压缩文件                                                | 启用压缩文件，然后再将其写入Azure。保持此选项为最佳性能。    |

6. 使用Snowflake外部舞台时，在“登台**高级”**选项卡上，配置以下属性。

   此选项卡根据外部舞台的位置显示不同的属性。

   在Amazon S3中使用外部平台时，您可以配置以下属性：

   | Amazon S3高级属性        | 描述                                                         |
   | :----------------------- | :----------------------------------------------------------- |
   | S3连接超时               | 关闭连接之前等待响应的秒数。默认值为10秒。                   |
   | S3套接字超时             | 等待查询响应的秒数。                                         |
   | S3最大错误重试           | 重试请求的最大次数。                                         |
   | S3上传线程               | 并行上传的线程池的大小。在写入多个分区并在多个部分中写入大型对象时使用。当写入多个分区时，将此属性设置为要写入的分区数可以提高性能。有关此属性和以下属性的更多信息，请参阅Amazon S3 TransferManager文档。 |
   | S3最小上传部分大小（MB） | 分段上传的最小分段大小（以字节为单位）。                     |
   | S3分段上传阈值（MB）     | 目标使用分段上传的最小批处理大小（以字节为单位）。           |
   | S3代理已启用             | 指定是否使用代理进行连接。                                   |
   | S3代理主机               | 代理主机。                                                   |
   | S3代理端口               | 代理端口。                                                   |
   | S3代理身份验证已启用     | 指示使用代理身份验证。                                       |
   | S3代理用户               | S3代理用户。                                                 |
   | S3代理密码               | S3代理密码。                                                 |
   | S3加密                   | Amazon S3用于管理加密密钥的选项：没有SSE-S3-使用Amazon S3托管密钥。SSE-KMS-使用Amazon Web Services KMS管理的密钥。默认为无。 |
   | S3加密KMS ID             | AWS KMS主加密密钥的Amazon资源名称（ARN）。使用以下格式：`:::::/`仅用于SSE-KMS加密。 |
   | S3加密上下文             | 用于加密上下文的键值对。单击**添加**以添加键值对。仅用于SSE-KMS加密。 |

   在Azure中使用外部阶段时，可以配置以下属性：

   | Azure高级属性         | 描述                                                         |
   | :-------------------- | :----------------------------------------------------------- |
   | 使用自定义Blob服务URL | 启用使用自定义Azure Blob存储URL。                            |
   | 自定义Blob服务网址    | 自定义Azure Blob存储URL。通常使用以下格式：`https://.blob.core.windows.net` |
   | Azure加密             | 此时启用使用Azure默认加密。                                  |

7. 在“ **数据”**选项卡上，配置以下属性：

   | 数据属性                | 描述                                                         |
   | :---------------------- | :----------------------------------------------------------- |
   | 行字段                  | Map或list-map字段用作[生成的row](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_c3g_qfj_ggb)的基础。默认值为`/`，其中包括结果行中的所有记录字段。 |
   | 要忽略的列字段          | 写入目标时要忽略的字段列表。您可以输入以逗号分隔的第一级字段列表，以将其忽略。 |
   | 空值                    | 用于表示空值的字符。默认 `\N`值为，雪花的空值字符。          |
   | CDC资料                 | 启用执行CRUD操作并使用MERGE命令写入Snowflake表的功能。选择以处理CDC数据。使用Snowpipe写入数据时无法使用。**重要：**为了保持数据的原始顺序，在处理CDC数据时不要使用多线程或群集执行模式。有关MERGE命令和其他加载方法的更多信息，请参见[加载方法](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_w35_vsq_2gb)。有关优化管道性能的信息，请参阅[性能优化](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/Snowflake.html#concept_mzv_wjw_2gb)。 |
   | 从Snowflake获取主键信息 | 查询雪花以获取每个表的主键列。仅在Snowflake表中定义了主键列时使用。知道键列后，手动输入它们比查询Snowflake更有效。 |
   | 表键列                  | 每个雪花表使用的键列。单击 **添加**图标以添加其他表。单击“ **键列”**字段中的“ **添加”**图标可为表添加其他键列。 |

8. 在“ **数据高级”**选项卡上，配置以下属性：

   | 数据高级属性       | 描述                                                         |
   | :----------------- | :----------------------------------------------------------- |
   | 雪花文件格式       | 允许使用自定义Snowflake CSV文件格式。除非StreamSets客户支持建议，否则不应使用。 |
   | 忽略缺少的字段     | 允许将缺少字段的记录写入Snowflake表。将指定的默认值用于缺少字段的数据类型。如果未启用，则缺少字段的记录将被视为错误记录。 |
   | 忽略无效类型的字段 | 允许将包含无效类型数据的字段替换为该数据类型的指定默认值。如果未启用，则具有无效类型数据的记录将被视为错误记录。 |
   | 布尔默认           | 当用无效数据替换缺少的布尔字段或布尔字段时使用的默认值。默认值为\ N，代表Snowflake中的空值。 |
   | 字符默认           | 将缺失的Char字段或Char字段替换为无效数据时使用的默认值。默认值为\ N，代表Snowflake中的空值。 |
   | 号码默认           | 在用无效数据替换缺少的“数字”字段或“数字”字段时使用的默认值。默认值为\ N，代表Snowflake中的空值。 |
   | 双重默认           | 将缺失的Double字段或Double字段替换为无效数据时使用的默认值。默认值为\ N，代表Snowflake中的空值。 |
   | 日期默认           | 将缺失的日期字段或日期字段替换为无效数据时使用的默认值。默认值为\ N，代表Snowflake中的空值。 |
   | Timestampntz默认   | 用无效数据替换缺少的Timestampntz字段或Timestampntz字段时使用的默认值。默认值为\ N，代表Snowflake中的空值。 |
   | 时间戳默认值       | 当用无效数据替换缺少的Timestamptz字段或Timestamptz字段时使用的默认值。默认值为\ N，代表Snowflake中的空值。 |
   | 时间预设           | 将缺失的“时间”字段或“时间”字段替换为无效数据时使用的默认值。默认值为\ N，代表Snowflake中的空值。 |
   | Varchar默认        | 将缺失的Varchar字段或Varchar字段替换为无效数据时使用的默认值。默认值为\ N，代表Snowflake中的空值。 |
   | 二进制默认         | 当用无效数据替换缺少的Binary字段或Binary字段时使用的默认值。默认值为\ N，代表Snowflake中的空值。 |
   | 变式默认           | 将缺失的Variant字段或Variant字段替换为无效数据时使用的默认值。默认值为\ N，代表Snowflake中的空值。 |
   | 更换换行符         | 用指定的替换字符替换字符串字段中的换行符。                   |
   | 换行符             | 用于替换换行符的字符。                                       |
   | 柱分离器           | 用作列分隔符的字符。                                         |
   | 报价方式           | 处理数据中特殊字符的模式，例如列分隔符和换行符：带引号-用指定的引号将每个字段中的数据括起来。以下示例使用星号将数据括在字段中：`*string data, more string data*`转义的-在特殊字符前加上指定的转义字符。以下示例使用反引号对字段中的逗号列分隔符进行转义：`string data`, more string data` |
   | 引用字符           | 包含字段数据的字符。使用报价模式时可用。                     |
   | 转义符             | 字段数据中特殊字符之前的字符。使用退出模式时可用。           |
   | 修剪空间           | 修剪字段数据中的前导和尾随空格。                             |