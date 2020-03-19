# 加密和解密字段

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310175642608.png) 资料收集器

加密和解密字段处理器对字段值进行加密或解密。

您可以使用处理器来加密记录中的一个或多个字段。您还可以使用处理器解密由另一个“加密和解密字段”处理器加密的一个或多个字段。您不能使用处理器同时执行加密和解密。当您要执行两个任务时，请使用其他处理器。

加密和解密字段处理器使用Amazon AWS Encryption SDK加密和解密字段。在对字段进行加密时，处理器会对数据密钥和所有其他加密详细信息进行加密，并将加密的详细信息与加密的数据一起存储。在解密字段时，处理器提取加密的数据密钥和其他详细信息，解密密钥，然后使用它来解密数据。

您可以将Amazon AWS Key Management Service（KMS）用作处理器的密钥提供者，或者可以在处理器配置属性中提供数据密钥。使用Amazon AWS KMS时，您可以指定KMS密钥Amazon资源名称（ARN）。您可以使用IAM角色或AWS访问密钥对连接到Amazon AWS。使用用户提供的密钥时，您可以指定Base64编码的密钥，还可以选择配置密钥ID。

对于这两种密钥提供程序类型，您都指定要使用的密码套件和帧大小。加密数据时，可以选择定义加密上下文并配置数据密钥缓存。

**注意：**解密由“加密和解密字段”处理器加密的字段时，您需要使用加密数据的处理器所使用的相同密钥提供者，密码套件以及任何其他详细信息，例如加密上下文。

有关AWS加密数据结构的信息，请参阅[AWS Encryption SDK文档](https://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/message-format.html)。

## 支持的数据类型

在加密字段时，“加密和解密字段”处理器会将字段的数据类型包括在加密数据中。解密同一字段时，处理器会将字段恢复为其原始数据类型。

加密和解密字段处理器可以加密或解密字符串或字节数组数据。因此，您可以使用处理器来加密或解密可以转换为字符串或字节数组的数据。

您可以使用“加密和解密字段”处理器来加密或解密以下数据类型：

- 布尔型
- 字节
- 字节数组
- 字符
- 日期
- 约会时间
- 小数
- 双
- 浮动
- 整数
- 长
- 短
- 串
- 时间
- 分区日期时间

## 关键提供者

使用“加密和解密字段”处理器时，请为该阶段指定密钥提供者。

您可以将Amazon AWS Key Management System（KMS）用作密钥提供者，也可以使用自己的用户提供的密钥：

- 亚马逊AWS KMS

  使用AWS KMS服务提供的主密钥。

  需要配置KMS密钥ARN属性以标识客户主密钥（CMK）的Amazon资源名称（ARN）。有关查找关键ARN的信息，请参阅[AWS KMS文档](https://docs.aws.amazon.com/kms/latest/developerguide/viewing-keys.html#find-cmk-id-arn)。

  您可以选择使用AWS Access Key ID和Secret Access Key连接到AWS。

- 用户提供的密钥

  需要指定一个Base64编码的主密钥。

  您可以使用凭证功能[凭证功能](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Expression_Language/Functions.html#concept_yvc_3qs_r1b)来访问[受支持的凭证存储区中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)的密钥。您还可以使用[base64EncodeString（）函数](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/Functions.html#concept_ylk_v44_jw)对[函数](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/Functions.html#concept_ylk_v44_jw)返回的字符串进行编码。

  编码密钥的长度必须与所选密码期望的长度匹配。例如，当使用256位（32字节）密码套件时，密钥的长度必须为32个字节。

  您可以选择包括在加密数据时使用的字符串密钥ID。

### AWS凭证

当您使用Amazon AWS KMS作为密钥提供者时，Data Collector必须将凭证传递给AWS。

使用以下方法之一来传递AWS凭证：

- IAM角色

  当执行数据收集器 在Amazon EC2实例上运行时，您可以使用AWS管理控制台为EC2实例配置IAM角色。Data Collector使用IAM实例配置文件凭证自动连接到AWS。

  要使用IAM角色，请不要配置“访问密钥ID”和“秘密访问密钥”属性。

  有关将IAM角色分配给EC2实例的更多信息，请参阅Amazon EC2文档。

- AWS访问密钥对

  当执行数据收集器未在Amazon EC2实例上运行或EC2实例不具有IAM角色时，您必须配置**访问密钥ID**和**秘密访问密钥** 属性。

  **提示：**为了保护敏感信息（例如访问密钥对）的安全，可以使用[运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。

## 密码套件

使用“加密和解密字段”处理器时，可以指定要使用的密码套件。处理器使用选定的密码套件来加密或解密数据。

处理器提供以下密码套件：

- ALG_AES_256_GCM_IV12_TAG16_HKDF_SHA384_ECDSA_P384（默认）
- ALG_AES_192_GCM_IV12_TAG16_HKDF_SHA384_ECDSA_P384
- ALG_AES_128_GCM_IV12_TAG16_HKDF_SHA256_ECDSA_P256
- ALG_AES_256_GCM_IV12_TAG16_HKDF_SHA256（无签名）
- ALG_AES_192_GCM_IV12_TAG16_HKDF_SHA256（无签名）
- ALG_AES_128_GCM_IV12_TAG16_HKDF_SHA256（无签名）
- ALG_AES_256_GCM_IV12_TAG16_NO_KDF（不建议）
- ALG_AES_192_GCM_IV12_TAG16_NO_KDF（不建议）
- ALG_AES_128_GCM_IV12_TAG16_NO_KDF（不推荐）

有关AWS Encryption SDK如何支持密码套件的概述，请参阅[AWS Encryption SDK文档](https://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/supported-algorithms.html)。该文档还提供了有关[密码套件的](https://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/algorithms-reference.html)更多详细信息。

## 加密上下文



您可以指定要包含在加密数据中的加密上下文。加密上下文（也称为附加身份验证数据（AAD））是加密并包含在加密数据中的密钥值对。

（可选）使用加密上下文作为附加工具来防止篡改加密数据。

当用于加密数据时，也需要加密上下文来解密数据。

## 数据密钥缓存

默认情况下，“加密和解密字段”处理器会为每个加密操作生成一个新的数据密钥。在安全考虑允许的情况下，您可以启用缓存和重用数据密钥以提高管道性能。

在启用数据密钥缓存之前，请考虑可能的安全后果。这篇[AWS博客文章](https://aws.amazon.com/blogs/security/aws-encryption-sdk-how-to-decide-if-data-key-caching-is-right-for-your-application/)描述了一些要考虑的问题。有关数据密钥缓存如何工作的详细信息，请参阅[AWS Encryption SDK文档](https://docs.aws.amazon.com/encryption-sdk/latest/developer-guide/data-caching-details.html)。

启用数据密钥缓存时，可以配置以下属性：

- 缓存容量
- 最大数据密钥年龄
- 每个数据键的记录
- 每个数据密钥的字节数

## 加密和解密记录

通过将记录序列化为单个字段，然后再将其传递给处理器，可以使用“加密和解密字段”处理器来加密或解密整个记录。

您可以使用[数据生成器处理器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/DataGenerator.html#concept_hw1_gq4_3fb)将记录序列化到记录的根字段。配置数据生成器处理器时，可以指定用于序列化记录的数据格式。使用基于文本的格式（例如JSON）来生成字符串字段，或者使用二进制格式（例如Avro）来生成字节数组字段。

## 配置加密和解密字段处理器

配置加密和解密字段处理器以加密或解密字段值。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。对群集管道无效。 |

2. 在“ **操作”**选项卡上，配置以下属性：

   | 动作属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | 模式     | 处理器执行的操作：对指定字段中的数据进行加密或解密。         |
   | 领域     | 要加密的字段的字段路径。**提示：**要[加密整个记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/EncryptDecrypt.html#concept_agp_gkk_hfb)，可以在管道中的较早位置使用Data Generator处理器将记录序列化为单个字段。 |

3. 在“ **密钥提供者”**选项卡上，配置以下属性：

   | 密钥提供者属性                                               | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [主密钥提供者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/EncryptDecrypt.html#concept_ft3_jlk_hfb) | 用于编码或解码数据的数据密钥提供者：Amazon AWS KMS-使用Amazon AWS Key Management Service中的数据密钥。用户提供的密钥-使用Base64编码的用户提供的密钥。 |
   | [密码](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/EncryptDecrypt.html#concept_azn_zmk_hfb) | 用于编码或解码数据的密码套件：ALG_AES_256_GCM_IV12_TAG16_HKDF_SHA384_ECDSA_P384（默认）ALG_AES_192_GCM_IV12_TAG16_HKDF_SHA384_ECDSA_P384ALG_AES_128_GCM_IV12_TAG16_HKDF_SHA256_ECDSA_P256ALG_AES_256_GCM_IV12_TAG16_HKDF_SHA256（无签名）ALG_AES_192_GCM_IV12_TAG16_HKDF_SHA256（无签名）ALG_AES_128_GCM_IV12_TAG16_HKDF_SHA256（无签名）ALG_AES_256_GCM_IV12_TAG16_NO_KDF（不建议）ALG_AES_192_GCM_IV12_TAG16_NO_KDF（不建议）ALG_AES_128_GCM_IV12_TAG16_NO_KDF（不推荐） |
   | 镜框尺寸                                                     | 帧大小（以字节为单位）。用于将数据分为多个帧进行加密。要将数据分为多个帧，请指定要使用的帧大小。要使用单个框架，请输入0。默认值为4096。使用此默认值时，将在单个帧中加密少于4096字节的数据。 |
   | [访问密钥ID](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/EncryptDecrypt.html#concept_aj2_bcb_jfb) | AWS访问密钥ID。使用Amazon AWS KMS作为密钥提供者并且不将IAM角色与IAM实例配置文件凭证一起使用时是必需的。仅适用于Amazon AWS KMS密钥提供者。 |
   | 秘密访问密钥                                                 | AWS秘密访问密钥。使用Amazon AWS KMS作为密钥提供者并且不将IAM角色与IAM实例配置文件凭证一起使用时，这是必需的。仅适用于Amazon AWS KMS密钥提供者。 |
   | KMS密钥ARN                                                   | KMS密钥的Amazon资源名称（ARN）。有关查找关键ARN的信息，请参阅[AWS KMS文档](https://docs.aws.amazon.com/kms/latest/developerguide/viewing-keys.html#find-cmk-id-arn)。使用Amazon AWS KMS密钥提供程序时是必需的。 |
   | Base64编码密钥                                               | 使用[用户提供的密钥](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/EncryptDecrypt.html#concept_ft3_jlk_hfb)时要使用的Base64编码数据[密钥](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/EncryptDecrypt.html#concept_ft3_jlk_hfb)。您可以使用凭证功能[凭证功能](https://streamsets.com/documentation/datacollector/latest/help/datacollector/UserGuide/Expression_Language/Functions.html#concept_yvc_3qs_r1b)来访问[受支持的凭证存储区中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/CredentialStores.html#concept_bt1_bpj_r1b)的密钥。您还可以使用[base64EncodeString（）函数](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/Functions.html#concept_ylk_v44_jw)对[函数](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Expression_Language/Functions.html#concept_ylk_v44_jw)返回的字符串进行编码。编码密钥的长度必须与所选密码期望的长度匹配。例如，当使用256位（32字节）密码套件时，密钥的长度必须为32个字节。 |
   | 密钥ID                                                       | 使用用户提供的密钥时，除了Base64编码的密钥外，还将使用一个可选的密钥ID。使用字符串值。 |
   | [加密上下文（AAD）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/EncryptDecrypt.html#concept_mnh_ygg_3fb) | 用作加密上下文的密钥值对，也称为其他经过身份验证的数据。     |
   | [数据密钥缓存](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Processors/EncryptDecrypt.html#concept_ywv_3tk_hfb) | 启用缓存和重用数据密钥。在安全考虑允许的情况下，用于提高性能。 |
   | 缓存容量                                                     | 要缓存在内存中的最大密钥数。                                 |
   | 最大数据密钥年龄                                             | 淘汰数据密钥之前可以使用数据密钥的最大秒数。                 |
   | 每个数据密钥的最大记录                                       | 淘汰数据密钥之前，数据密钥可以加密的最大字段数。             |
   | 每个数据密钥的最大字节数                                     | 停用数据密钥之前，数据密钥可用于加密的最大字节数。           |