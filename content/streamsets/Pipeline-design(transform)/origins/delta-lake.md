# 三角洲湖

Delta Lake原点从Delta Lake表中读取数据。源可以从托管或非托管表中读取。

原点只能在批处理管道中使用，不能跟踪偏移量。结果，每次管道运行时，源都会读取所有可用数据。要以流模式或批处理模式在跟踪偏移量时处理Delta Lake托管表，请使用 [Hive origin](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Hive.html#concept_nvg_112_f3b)。Hive源无法处理非托管表。

**重要说明：** Delta Lake起源要求在Transformer计算机上以及群集中的每个节点上安装Apache Spark 2.4.2或更高版本。

配置Delta Lake原点时，可以指定要读取的表的路径。您可以选择启用时间旅行以查询表的较早版本。

您为表配置存储系统。从存储在Azure数据湖存储（ADLS）Gen1或ADLS Gen2上的表中读取时，您还要指定与连接有关的详细信息。对于Amazon S3或HDFS上的表，Transformer使用存储在Hadoop配置文件中的连接信息。您可以为与Amazon S3的连接配置安全性。

您可以将源配置为仅加载一次数据，并缓存数据以在整个管道运行中重复使用。或者，您可以配置源以缓存每一批数据，以便可以将其有效地传递到多个下游批次。

要访问存储在ADLS Gen1或ADLS Gen2上的表，请在运行管道之前完成必要的先决条件。另外，在为ADLS Gen1，ADLS Gen2或Amazon S3上的表运行本地管道之前，请完成以下其他[先决任务](https://streamsets.com/documentation/controlhub/latest/help/transformer/Installation/StagePrerequisites.html#concept_owd_4ld_h3b)。

## 储存系统

Delta Lake原点可以从以下存储系统上存储的Delta Lake表中读取数据：

- 亚马逊S3
- Azure数据湖存储（ADLS）第1代
- Azure数据湖存储（ADLS）第2代
- HDFS
- 本地文件系统

## ADLS Gen 1先决条件

使用Delta Lake原点读取存储在ADLS Gen1上的表之前，请完成以下先决条件。

1. 如有必要，为

   StreamSets Transformer

   创建一个新的Azure Active Directory应用程序。

   有关创建新应用程序的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal)。

2. 确保Azure Active Directory Transformer应用程序具有适当的访问控制以执行必要的任务。

   若要从Azure读取，Transformer应用程序需要具有读取和执行权限。如果还写入Azure，则该应用程序也需要“写入”权限。

   有关配置Gen1访问控制的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-security-overview)。

3. 在运行管道的群集上安装ADLS Gen1驱动程序。

   最新的群集版本包括ADLS Gen1驱动程序 `azure-datalake-store.jar`。但是，旧版本可能需要安装它。有关Hadoop对ADLS Gen1的支持的更多信息，请参阅[Hadoop文档](https://hadoop.apache.org/docs/r2.8.0/hadoop-azure-datalake/index.html)。

4. 

   从Azure门户[检索ADLS Gen1身份验证信息](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/DLake.html#concept_ekh_2gc_z3b)以配置来源。

   如果要使用在管道运行所在的群集中配置的Azure身份验证信息，则可以跳过此步骤。

5. 在本地管道中使用阶段之前，请确保[已完成与Hadoop相关的任务](https://streamsets.com/documentation/controlhub/latest/help/transformer/Installation/StagePrerequisites.html#concept_owd_4ld_h3b)。

### 检索身份验证信息

Delta Lake源使用Azure Active Directory服务主体身份验证（也称为服务到服务身份验证）连接到存储在ADLS Gen1上的Delta Lake表。

源需要几个Azure身份验证详细信息才能连接到ADLS Gen1。如果运行管道的群集配置了必要的Azure身份验证信息，则默认情况下使用该信息。但是，使用群集中配置的Azure身份验证信息时，数据预览不可用。

您还可以在阶段属性中指定Azure身份验证信息。阶段属性中指定的任何身份验证信息优先于集群中配置的身份验证信息。

源要求以下Azure身份验证信息：

- 应用程序ID- 

  Azure Active Directory Transformer应用程序的应用程序ID 。也称为客户端ID。

  有关从Azure门户访问应用程序ID的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)。

- 应用程序密钥

  -Azure Active Directory Transformer应用程序的身份验证密钥。也称为客户端密钥。

  有关从Azure门户访问应用程序密钥的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)。

- OAuth令牌端点- 用于Transformer的Azure Active Directory v1.0应用程序的OAuth 2.0令牌端点。例如： `https://login.microsoftonline.com//oauth2/token`。

## ADLS Gen2先决条件

使用Delta Lake原点读取存储在ADLS Gen2上的表之前，请完成以下先决条件。

1. 如有必要，为

   StreamSets Transformer

   创建一个新的Azure Active Directory应用程序。

   有关创建新应用程序的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal)。

2. 确保Azure Active Directory Transformer应用程序具有适当的访问控制以执行必要的任务。

   若要从Azure读取，Transformer应用程序需要具有读取和执行权限。如果还写入Azure，则该应用程序也需要“写入”权限。

   有关配置Gen2访问控制的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/storage/blobs/data-lake-storage-access-control)。

3. 在运行管道的群集上安装Azure Blob文件系统驱动程序。

   最新的群集版本包括Azure Blob文件系统驱动程序`azure-datalake-store.jar`。但是，旧版本可能需要安装它。有关Azure对Hadoop的Azure Data Lake Storage Gen2支持的更多信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/storage/blobs/data-lake-storage-abfs-driver)。

4. 

   从Azure门户[检索Azure Data Lake Storage Gen2身份验证信息](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/ADLS-G2.html#concept_bn3_55v_5hb)以配置来源。

   如果要使用在管道运行所在的群集中配置的Azure身份验证信息，则可以跳过此步骤。

5. 在本地管道中使用阶段之前，请确保[已完成与Hadoop相关的任务](https://streamsets.com/documentation/controlhub/latest/help/transformer/Installation/StagePrerequisites.html#concept_owd_4ld_h3b)。

### 检索身份验证信息

三角洲湖泊起源提供了几种方法来验证与ADLS Gen2的连接。根据您使用的身份验证方法，源要求不同的身份验证详细信息。

如果运行管道的群集配置了必要的Azure身份验证信息，则默认情况下使用该信息。但是，使用群集中配置的Azure身份验证信息时，数据预览不可用。

您还可以在阶段属性中指定Azure身份验证信息。阶段属性中指定的任何身份验证信息优先于集群中配置的身份验证信息。

来源使用的身份验证信息取决于所选的身份验证方法：

- OAuth

  使用OAuth身份验证进行连接时，来源需要以下信息：应用程序ID- Azure Active Directory Transformer应用程序的应用程序ID 。也称为客户端ID。有关从Azure门户访问应用程序ID的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)。应用程序密钥-Azure Active Directory Transformer应用程序的身份验证密钥。也称为客户端密钥。有关从Azure门户访问应用程序密钥的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)。OAuth令牌端点- 用于Transformer的Azure Active Directory v1.0应用程序的OAuth 2.0令牌端点。例如： `https://login.microsoftonline.com//oauth2/token`。

  要在本地运行管道，必须使用此身份验证方法。您还可以对在群集上运行的管道使用此身份验证方法。

- 托管服务身份

  使用受管服务身份验证进行连接时，源需要以下信息：应用程序ID - Azure Active Directory Transformer应用程序的应用程序ID 。也称为客户端ID。有关从Azure门户访问应用程序ID的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)。租户ID - Azure Active Directory Transformer应用程序的租户ID 。也称为目录ID。有关从Azure门户访问租户ID的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id)。

  您可以将此身份验证方法用于在群集上运行的管道。

- 共用金钥

  使用共享密钥身份验证进行连接时，来源需要以下信息：账户共享密钥 - 对于存储帐户生成Azure的共享访问的关键。有关从Azure门户访问共享访问密钥的更多信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-manage#access-keys)。

  您可以将此身份验证方法用于在群集上运行的管道。

## Amazon S3凭证模式

从存储在Amazon S3上的Delta Lake表中读取时，您可以指定Delta Lake原点连接到Amazon S3的安全性。原点可以使用以下凭据模式进行连接：

- IAM角色

  当Transformer在Amazon EC2实例上运行时，您可以使用AWS管理控制台为Transformer EC2实例配置IAM角色。然后，Transformer使用IAM实例配置文件凭证自动连接到Amazon S3。

  有关将IAM角色分配给EC2实例的更多信息，请参阅Amazon EC2文档。

- AWS密钥

  当Transformer未在Amazon EC2实例上运行或EC2实例不具有IAM角色时，您可以使用AWS访问密钥对进行连接。使用AWS访问密钥对时，请在源中指定访问密钥ID和秘密访问密钥属性。**提示：**为了保护敏感信息，可以按照Data Collector文档中的说明使用 [运行时资源](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或[凭据存储](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。

- 没有

  访问公共存储桶时，可以不使用安全性进行匿名连接。

## 从本地文件系统读取

在管道开发和测试过程中，Delta Lake起源可以读取存储在本地文件系统上的Delta Lake表。

1. 在管道属性的“ **群集”**选项卡上，将“ **群集管理器类型”**设置 为“ **无（本地）”**。
2. 在舞台属性的“ **常规”**选项卡上，将“ **舞台库”**设置 为**Delta Lake Transformer提供的库**。
3. 在“ **Delta Lake”**选项卡上，为“ **Table Directory Path”**属性指定要使用的目录。
4. 在**存储**选项卡上，将**存储系统**设置为**HDFS**。

## 配置三角洲湖原点

配置Delta Lake原点以批处理执行模式处理Delta Lake表中的数据。原点只能在批处理管道中使用，不能跟踪偏移量。因此，每次管道运行时，源都会读取所有可用数据。

在读取存储在[ADLS Gen1](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/DLake.html#concept_xcd_sfc_z3b)或[ADLS Gen2](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/DLake.html#concept_ghf_flt_yjb)上的表之前，请完成必要的先决条件。另外，在为ADLS Gen1，ADLS Gen2或Amazon S3上的表运行本地管道之前，请完成以下其他[先决任务](https://streamsets.com/documentation/controlhub/latest/help/transformer/Installation/StagePrerequisites.html#concept_owd_4ld_h3b)。

**重要说明：** Delta Lake起源要求在Transformer计算机上以及群集中的每个节点上安装Apache Spark 2.4.2或更高版本。

1. 在“属性”面板上的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 用于连接到Delta Lake的舞台库：Delta Lake群集提供的库-运行管道的群集已安装了Delta Lake库，因此具有运行管道的所有必需的库。Delta Lake Transformer提供的库-Transformer将必需的库与管道一起传递以启用管道运行。在本地运行管道或运行管道的群集不包括Delta Lake库时使用。**注意：**在管道中使用其他Delta Lake阶段时，请确保它们使用[相同的阶段库](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Pipeline-StageLibMatch.html#concept_r4g_n3x_shb)。 |
   | 仅加载一次数据                                               | 批量读取数据并缓存结果以备重用。用于在流执行模式管道中[执行查找](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Lookups.html#concept_f2z_5yw_g3b)。使用原点执行查找时， 请勿限制批处理大小。所有查询数据都应在一个批次中读取。在批处理执行模式下，将忽略此属性。 |
   | [缓存数据](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/CachingData.html#concept_q2r_xm4_33b) | 缓存处理后的数据，以便可以在多个下游阶段重用该数据。当阶段将数据传递到多个阶段时，用于提高性能。当管道以[荒谬的方式](https://streamsets.com/documentation/controlhub/latest/help/transformer/Pipelines/Ludicrous.html#concept_m4y_lbq_g3b)运行时，缓存会限制下推式优化。 |

2. 在“ **Delta Lake”**选项卡上，配置以下属性：

   | 三角洲湖地产     | 描述                                                         |
   | :--------------- | :----------------------------------------------------------- |
   | 表目录路径       | Delta湖表的路径。                                            |
   | 时间旅行         | 查询表的早期版本。有关时间旅行的更多信息，请参阅[Delta Lake文档](https://docs.delta.io/latest/quick-start.html#read-older-versions-of-data-using-time-travel)。 |
   | 时间旅行查询模式 | 用于访问表中较早版本的数据的模式：版本自-返回具有匹配版本号的时间旅行数据。截止日期-返回具有匹配日期或时间戳记的时间旅行数据。 |
   | 版               | 要使用的表的版本。                                           |
   | 时间戳字符串     | 用于查找匹配的时间旅行数据的日期或时间戳。                   |

3. 在“ **存储”**选项卡上，配置存储和连接信息：

   | 存储          | 描述                                                         |
   | :------------ | :----------------------------------------------------------- |
   | 储存系统      | Delta Lake桌子的存储系统：Amazon S3-用于存储在Amazon S3上的表。要进行连接，Transformer使用存储在HDFS配置文件中的连接信息。ADLS Gen1-用于存储在Azure Data Lake Storage Gen1上的表。要进行连接，Transformer使用指定的连接详细信息。ADLS Gen2-用于存储在Azure Data Lake Storage Gen2上的表。要进行连接，Transformer使用指定的连接详细信息。HDFS-用于存储在HDFS或本地文件系统上的表。为了连接到HDFS，Transformer使用存储在HDFS配置文件中的连接信息。为了连接到本地文件系统，Transformer使用为表指定的目录路径。 |
   | 凭证模式      | 用于连接到Amazon S3的模式：AWS密钥-使用AWS访问密钥对进行连接。IAM角色-使用分配给Transformer EC2实例的IAM角色进行连接。无-不使用安全性连接到公共存储桶。 |
   | 访问密钥ID    | AWS访问密钥ID。使用AWS密钥连接到Amazon S3时需要。**提示：**为了保护敏感信息，可以按照Data Collector文档中的说明使用 [运行时资源](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或[凭据存储](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。 |
   | 秘密访问密钥  | AWS秘密访问密钥。使用AWS密钥连接到Amazon S3时需要。**提示：**为了保护敏感信息，可以按照Data Collector文档中的说明使用 [运行时资源](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或[凭据存储](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。 |
   | 申请编号      | Azure Active Directory Transformer应用程序的应用程序ID 。也称为客户端ID。用于通过OAuth或托管服务身份验证连接到Azure Data Lake Storage Gen1或Azure Data Lake Storage Gen2。如果未指定，则该阶段使用在运行管道的群集中配置的等效Azure身份验证信息。有关从Azure门户访问应用程序密钥的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)。**提示：**为了保护敏感信息，可以按照Data Collector文档中的说明使用 [运行时资源](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或[凭据存储](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。 |
   | 应用密钥      | Azure Active Directory Transformer应用程序的身份验证密钥。也称为客户端密钥。用于通过OAuth身份验证连接到Azure Data Lake Storage Gen1或Azure Data Lake Storage Gen2。如果未指定，则该阶段使用在运行管道的群集中配置的等效Azure身份验证信息。有关从Azure门户访问应用程序密钥的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#get-application-id-and-authentication-key)。**提示：**为了保护敏感信息，可以按照Data Collector文档中的说明使用 [运行时资源](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或[凭据存储](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。 |
   | OAuth令牌端点 | 用于Transformer的Azure Active Directory v1.0应用程序的OAuth 2.0令牌终结点。例如： `https://login.microsoftonline.com//oauth2/token`。用于通过OAuth身份验证连接到Azure Data Lake Storage Gen1或Azure Data Lake Storage Gen2。如果未指定，则该阶段使用在运行管道的群集中配置的等效Azure身份验证信息。 |
   | 租户ID        | Azure Active Directory Transformer应用程序的租户ID 。也称为目录ID。用于通过托管服务身份身份验证连接到Azure Data Lake Storage Gen2。如果未指定，则该阶段使用在运行管道的群集中配置的等效Azure身份验证信息。有关从Azure门户访问租户ID的信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-id)。**提示：**为了保护敏感信息，可以按照Data Collector文档中的说明使用 [运行时资源](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或[凭据存储](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。 |
   | 帐户共享密钥  | Azure为存储帐户生成的共享访问密钥。用于通过共享密钥身份验证连接到Azure Data Lake Storage Gen2。如果未指定，则该阶段使用在运行管道的群集中配置的等效Azure身份验证信息。有关从Azure门户访问共享访问密钥的更多信息，请参见[Azure文档](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-manage#access-keys)。**提示：**为了保护敏感信息，可以按照Data Collector文档中的说明使用 [运行时资源](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或[凭据存储](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)。 |