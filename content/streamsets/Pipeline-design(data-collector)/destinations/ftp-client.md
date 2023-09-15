# SFTP / FTP / FTPS客户端



[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310202349051.png) 资料收集器

SFTP / FTP / FTPS客户端目标使用安全文件传输协议（SFTP），文件传输协议（FTP）或FTP安全（FTPS）协议将整个文件写入URL。

配置SFTP / FTP / FTPS客户端目标时，请指定目标在远程服务器上写入文件的URL。目标可以创建不存在的路径。您还可以指定要写入的文件名的表达式以及服务器上文件已存在时要执行的操作。

如果服务器需要身份验证，请为所使用的协议配置凭据。对于SFTP协议，目标可能要求将服务器列在已知主机文件中。对于FTPS协议，目标可以使用客户端证书向服务器进行身份验证，并且可以从FTPS服务器对证书进行身份验证。

目的地可以为事件流生成事件。有关事件框架的更多信息，请参见《[数据流触发器概述》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

**注意：** StreamSets已使用vsftpd 3.0测试了该阶段。

## 证书

SFTP / FTP / FTPS客户端目标可以使用多种方法与远程服务器进行身份验证。在“凭据”选项卡中，配置远程服务器所需的身份验证。

每种协议的身份验证选项不同：

- 对于所有协议，请选择一种身份验证方法以登录到远程服务器。根据协议和远程服务器要求选择方法：
  - 无-阶段不通过服务器进行身份验证。
  - 密码-阶段使用用户名和密码向服务器认证。您必须指定用户名和密码。
  - 私钥-阶段使用私钥进行身份验证。仅与SFTP协议一起使用。您必须在本地文件或纯文本中指定私钥。
- 对于SFTP协议，该阶段可以要求将服务器列在已知主机文件中。您必须指定包含包含批准的SFTP服务器的主机密钥的已知主机文件的路径。
- 对于FTPS协议，该阶段可以使用证书对服务器进行身份验证。您必须指定密钥库文件和密码。您还可以通过指定信任库提供程序将阶段配置为对服务器进行身份验证。有关密钥库和信任库的更多信息，请参阅“ [密钥库和信任库配置”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SSL-TLS.html#concept_kqb_rqf_5z)。

## 事件产生



SFTP / FTP / FTPS客户端目标可以生成可在事件流中使用的事件。启用事件生成后，目的地每次关闭文件或完成流式传输整个文件时，目的地都会生成事件记录。

您可以以任何逻辑方式使用SFTP / FTP / FTPS客户端目标生成的事件。例如：

- 使用HDFS文件元数据执行程序可以移动或更改已关闭文件的权限。

  有关示例，请参见[案例研究：输出文件管理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_d1q_xl4_lx)。

- 使用电子邮件执行程序在收到事件后发送自定义电子邮件。

  有关示例，请参阅[案例研究：发送电子邮件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_t2t_lp5_xz)。

- 具有用于存储事件信息的目的地。

  有关示例，请参见[案例研究：事件存储](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_ocb_nnl_px)。

有关数据流触发器和事件框架的更多信息，请参见[数据流触发器概述](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。

### 活动记录



由SFTP / FTP / FTPS客户端目标生成的事件记录包括以下与事件相关的记录头属性。记录标题属性存储为字符串值：

| 记录标题属性                 | 描述                                                         |
| :--------------------------- | :----------------------------------------------------------- |
| sdc.event.type               | 事件类型。使用以下类型之一：文件关闭-在目标关闭文件时生成。wholeFileProcessed-在目标完成流式传输整个文件时生成。 |
| sdc.event.version            | 整数，指示事件记录类型的版本。                               |
| sdc.event.creation_timestamp | 舞台创建事件的时间戳记。                                     |

目标可以生成以下类型的事件记录：

- 文件关闭

  当目标关闭输出文件时，它将生成文件关闭事件记录。

  文件关闭事件记录的 `sdc.event.type`记录头属性设置为`file-closed`，包括以下字段：领域描述文件路径已关闭文件的绝对路径。文件名关闭文件的文件名。长度关闭文件的大小（以字节为单位）。

- 整个文件已处理

  目标在完成流式传输整个文件时会生成事件记录。整个文件事件记录的 `sdc.event.type`记录头属性设置为，`wholeFileProcessed`并且具有以下字段：领域描述sourceFileInfo关于已处理的原始整个文件的属性映射。这些属性包括：size-整个文件的大小（以字节为单位）。其他属性取决于原始系统提供的信息。targetFileInfo关于写入目标的整个文件的属性映射。这些属性包括：path-处理后的整个文件的绝对路径。

## 资料格式



SFTP / FTP / FTPS客户端目标以以下数据格式写入数据：

- 整个档案

  将整个文件流式传输到目标系统。目标将数据写入阶段中定义的文件和位置。如果已经存在相同名称的文件，则可以将目标配置为覆盖现有文件或将当前文件发送给错误文件。

  默认情况下，写入的文件使用目标系统的默认访问权限。您可以指定一个定义访问权限的表达式。

  有关整个文件数据格式的更多信息，请参见[整个文件数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_nfc_qkh_xw)。

## 配置SFTP / FTP / FTPS客户端目标



配置SFTP / FTP / FTPS客户端目标，以使用SFTP，FTP或FTPS将数据发送到URL。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [产生事件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/SFTP.html#concept_lvv_xvq_23b) | 发生事件时生成事件记录。用于 [事件处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Event_Handling/EventFramework-Title.html#concept_cph_5h4_lx)。 |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击**添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **SFTP / FTP / FTPS”**选项卡上，配置以下属性：

   | SFTP / FTP / FTPS属性  | 描述                                                         |
   | :--------------------- | :----------------------------------------------------------- |
   | 资源网址               | 目标在远程服务器上发送数据的URL。使用适当的格式：SFTP协议：`sftp://:/`FTP协议：`ftp://:/ `FTPS协议：`ftps://:/ `如果服务器使用标准端口号，则可以从URL中省略端口号：对于SFTP是22，对于FTP或FTPS是21。您可以选择在URL中包括用于登录SFTP，FTP或FTPS服务器的用户名。例如，对于FTP协议，可以使用以下格式：`ftp://:@/`您可以输入电子邮件地址作为用户名。**注意：**如果在资源URL中输入用户名，并在“凭据”选项卡上配置密码或私钥身份验证，则URL中输入的值优先。 |
   | 相对于用户主目录的路径 | 解释在资源URL中输入的相对于登录到远程服务器的用户的主目录的路径。您可以在URL中或在“凭据”选项卡上配置密码或私钥身份验证时指定用户名。 |
   | 建立路径               | 当路径不存在时，在远程服务器上创建指定的路径。               |
   | FTPS模式               | FTPS协议使用的加密协商模式：隐式-立即使用加密。显式-使用普通FTP连接到服务器，然后与服务器协商加密。 |
   | FTPS数据通道保护级别   | FTPS数据通道使用的保护级别：清除-仅加密与服务器的通信，不加密发送到服务器的数据。专用-加密与服务器的通信以及发送到服务器的数据。 |
   | 套接字超时             | TCP数据包之间允许的最大秒数。0表示没有限制。                 |
   | 连接超时               | 发起与SFTP，FTP或FTPS服务器的连接所允许的最大秒数。0表示没有限制。 |
   | 数据超时               | 传输的数据文件之间允许的最大秒数。0表示没有限制。            |

3. 在“ **凭据”**选项卡上，配置以下属性：

   | [凭证属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/SFTP.html#concept_tzs_pvl_f3b) | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 认证方式                                                     | 登录远程服务器的认证方式：无-不通过远程服务器进行身份验证。密码-使用用户名和密码向远程服务器进行身份验证。私钥-使用私钥向SFTP服务器进行身份验证。默认为无。 |
   | 用户名                                                       | 登录远程服务器的用户名。可用于密码和私钥认证。               |
   | 密码                                                         | 登录远程服务器的密码。可用于密码验证。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 私钥提供者                                                   | 提供私钥的源：文件-从本地文件读取私钥。纯文本-从纯文本字段读取私钥。使用私钥身份验证时可用。 |
   | 私钥文件                                                     | 用于登录远程服务器的本地私钥文件的完整路径。当提供程序是文件时，可用于私钥身份验证。 |
   | 私钥                                                         | 用于登录到远程服务器的私钥。当提供者为纯文本时，可用于私钥身份验证。 |
   | 私钥密码                                                     | 密码短语用于打开私钥。如果私钥受密码保护，则可用于私钥认证。 |
   | 严格的主机检查                                               | 要求SFTP服务器列在已知主机文件中。启用后，仅当服务器在已知主机文件中列出时，目的地才连接到服务器。需要已知主机文件包含RSA密钥。仅用于SFTP协议。 |
   | 已知主机文件                                                 | 本地已知主机文件的完整路径。如果选择严格的主机检查，则为必需。使用严格的主机检查时可用。 |
   | 对FTPS使用客户端证书                                         | 使用客户端证书向FTPS服务器进行身份验证。当FTPS服务器需要相互认证时，请选择此选项。您必须提供包含客户端证书的密钥库文件。仅用于FTPS协议。 |
   | FTPS客户端证书密钥库文件                                     | 包含客户机证书的密钥库文件的完整路径。对FTPS使用客户端证书时可用。 |
   | FTPS客户端证书密钥库类型                                     | 包含客户端证书的密钥库文件的类型。对FTPS使用客户端证书时可用。 |
   | FTPS客户端证书密钥库密码                                     | 包含客户机证书的密钥库文件的密码。密码是可选的，但建议使用。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。对FTPS使用客户端证书时可用。 |
   | FTPS信任库提供程序                                           | 目的地用来从FTPS服务器认证证书的方法：全部允许-允许任何证书，跳过身份验证。文件-使用指定的信任库文件对证书进行身份验证。JVM默认-使用JVM默认信任库对证书进行身份验证。仅用于FTPS协议。 |
   | FTPS信任库文件                                               | 包含服务器证书的信任库文件的完整路径。在将文件用作FTPS信任库提供程序时可用。 |
   | FTPS信任库类型                                               | 信任库类型：Java密钥库文件（JKS）PKCS-12（p12文件）在将文件用作FTPS信任库提供程序时可用。 |
   | FTPS信任库密码                                               | 信任库文件的密码。密码是可选的，但建议使用。**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。在将文件用作FTPS信任库提供程序时可用。 |

4. 在“ **数据格式”**选项卡上，配置以下属性：

   | 整个文件属性 | 描述                                                         |
   | :----------- | :----------------------------------------------------------- |
   | 资料格式     | 要写入的数据格式。目标使用 [整个文件数据格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_nfc_qkh_xw)。 |
   | 文件名表达   | 用于文件名的表达式。有关如何根据输入文件名命名文件的提示，请参阅“ [编写整个文件”](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WholeFile.html#concept_a2s_4jw_1x)。 |
   | 文件已存在   | 当输出目录中已经存在同名文件时采取的措施。使用以下选项之一：发送到错误-根据阶段错误记录处理来处理记录。覆盖-覆盖现有文件。 |