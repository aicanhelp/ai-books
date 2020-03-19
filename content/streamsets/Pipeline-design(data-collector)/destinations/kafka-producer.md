# 卡夫卡制片人

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310184802328.png) 资料收集器![img](imgs/icon-Edge-20200310184802607.png) 数据收集器边缘

Kafka Producer目标将数据写入Kafka集群。当在[微服务管道中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Microservice/Microservice_Title.html#concept_qfh_xdm_p2b)使用时，目的地还可以向[微服务源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Microservice/Microservice_Title.html#concept_rzc_rbf_kfb)发送响应。

在配置Kafka Producer时，您将定义连接信息，分区策略和要使用的数据格式。您还可以配置Kafka Producer来确定要在运行时写入的主题。

Kafka Producer根据您选择的分区策略将数据传递到Kafka主题中的分区。您可以选择将一批记录作为一条消息写到Kafka集群。当您希望目标将响应发送到微服务管道中的微服务源时，可以指定要发送的响应的类型。

您可以根据需要添加其他Kafka配置属性。您还可以将源配置为使用Kafka安全功能。

您可以将Kafka Producer配置为与Confluent Schema Registry配合使用。Confluent Schema Registry是Avro架构的分布式存储层，该架构使用Kafka作为其底层存储机制。

## 经纪人名单

Kafka Producer根据您指定的主题和关联的代理连接到Kafka。为了确保在指定的代理发生故障时进行连接，请列出尽可能多的代理。

## 运行时主题解析

Kafka Producer可以根据表达式将记录写到主题。当Kafka Producer评估记录时，它会根据记录值计算表达式并将记录写到结果主题中。

执行运行时主题解析时，默认情况下，Kafka Producer可以写入任何主题。您可以创建主题白名单以限制Kafka Producer尝试使用的主题数。创建白名单时，任何可解决未列出主题的记录都将发送到阶段以进行错误处理。当记录数据可能解析为无效的主题名称时，请使用白名单。

## 分区策略

分区策略确定如何将数据写入Kafka分区。您可以使用分区策略来平衡工作负载或在语义上写入数据。

Kafka Producer提供以下分区策略：

- 轮循

  使用循环顺序将每个记录写入不同的分区。用于负载均衡。

- 随机

  使用随机顺序将每个记录写入不同的分区。用于负载均衡。

- 表达

  根据分区表达式的结果将每个记录写入分区。用于执行语义分区。

  配置分区表达式时，请定义该表达式以求出要在其中写入每个记录的分区。该表达式必须返回一个数值。

  例如，以下表达式根据“年龄”字段中的值将记录写入两个分区：`${record:value('/Age') < 21 ? 0 : 1}`

  下面的示例根据Age字段的值写入三个分区：`${record:value('/a') < 21 ? 0 : record:value('/a') < 55 ? 1 : 2}`

- 默认

  使用Kafka提供的默认分区策略写入每个记录。

  使用默认分区策略时，您将配置一个分区表达式，该表达式从记录中返回分区键，例如 `${record:value('/partitionkey')}`。该表达式必须返回一个字符串值。Kafka Producer根据分区键的哈希将每个记录写入分区。

## 发送微服务响应

当您在微服务管道中使用目标时，Kafka Producer目标可以将响应发送到微服务源。

发送对微服务源的响应时，目标可以发送以下内容之一：

- 所有成功书面记录。
- 来自目标系统的响应-有关可能的响应的信息，请参阅目标系统的文档。

## 卡夫卡的其他属性

您可以将自定义Kafka配置属性添加到Kafka Producer目标。

添加Kafka配置属性时，请输入确切的属性名称和值。该阶段不验证属性名称或值。

默认情况下定义了几个属性，您可以根据需要编辑或删除这些属性。

**注意：**由于该阶段使用多个配置属性，因此它将忽略以下属性的用户定义值：

- key.serializer.class
- metadata.broker.list
- partitioner.class
- 生产者类型
- serializer.class

## 启用安全性

您可以将Kafka Producer配置为通过SSL / TLS，Kerberos或两者安全地连接到Kafka。

![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中， Kafka Producer目标仅支持SSL / TLS。

### 启用SSL / TLS

执行以下步骤，使Kafka Producer能够使用SSL / TLS连接到Kafka。

![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中，仅**security.protocol**， **ssl.truststore.location**和 **ssl.keystore.location** Kafka配置属性有效。对于信任库和密钥库位置，为使用PEM格式的信任库和密钥库文件输入绝对路径。在Data Collector Edge管道中，请勿添加这些说明中列出的任何其他SSL Kafka属性。

1. 要使用SSL / TLS进行连接，首先请确保按照[Kafka文档](http://kafka.apache.org/documentation.html#security_ssl)中的[说明](http://kafka.apache.org/documentation.html#security_ssl)为Kafka配置了SSL / TLS 。

2. 在阶段的“ **常规”**选项卡上，将“ **阶段库”**属性设置为适当的Apache Kafka版本。

3. 在**Kafka**选项卡上，添加 **security.protocol** Kafka配置属性并将其设置为**SSL**。

4. 然后添加并配置以下SSL Kafka属性：

   - ssl.truststore.location
   - ssl.truststore.password

   当Kafka代理要求客户端身份验证时-ssl.client.auth代理属性设置为“必需”时-添加并配置以下属性：

   - ssl.keystore.location
   - ssl.keystore.password
   - ssl.key.password

   一些经纪人可能还需要添加以下属性：

   - ssl.enabled.protocols
   - ssl.truststore.type
   - ssl.keystore.type

   有关这些属性的详细信息，请参见Kafka文档。

例如，以下属性允许该阶段使用SSL / TLS通过客户端身份验证连接到Kafka：

![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/Kafka-SSLoptions.png)

### 启用Kerberos（SASL）

使用Kerberos身份验证时，Data Collector使用Kerberos主体和密钥表连接到Kafka。执行以下步骤，以使Kafka Producer目标能够使用Kerberos连接到Kafka。

![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中无效。在Data Collector Edge管道中，Kafka Producer目标仅支持SSL / TLS。

1. 要使用Kerberos，首先请确保按照[Kafka文档](http://kafka.apache.org/documentation.html#security_sasl)中的[说明](http://kafka.apache.org/documentation.html#security_sasl)为Kafka配置了Kerberos 。

2. 确保Kerberos身份验证是否启用了数据采集，如在[Kerberos身份验证](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/Kerberos.html#concept_hnm_n4l_xs)。

3. 根据您的安装和身份验证类型，添加Kafka客户端所需的Java身份验证和授权服务（JAAS）配置属性：

   - 没有LDAP身份验证的RPM，tarball或Cloudera Manager安装

      -如果

     Data Collector

     不使用LDAP身份验证，请在

     Data Collector

      计算机上创建一个单独的JAAS配置文件。将以下

     ```
     KafkaClient
     ```

     登录部分添加到文件中：

     ```
     KafkaClient {
         com.sun.security.auth.module.Krb5LoginModule required
         useKeyTab=true
         keyTab="<keytab path>"
         principal="<principal name>/<host name>@<realm>";
     };
     ```

     例如：

     ```
     KafkaClient {
         com.sun.security.auth.module.Krb5LoginModule required
         useKeyTab=true
         keyTab="/etc/security/keytabs/sdc.keytab"
         principal="sdc/sdc-01.streamsets.net@EXAMPLE.COM";
     };
     ```

     然后修改SDC_JAVA_OPTS环境变量，使其包含以下选项，这些选项定义了JAAS配置文件的路径：

     ```
     -Djava.security.auth.login.config=<JAAS config path>
     ```

     使用安装类型所需的方法。

   - 使用LDAP认证的RPM或tarball安装

      -如果在RPM或tarball的安装中启用了LDAP认证，则将属性添加到

     Data Collector

     使用的JAAS配置文件中-该 

     ```
     $SDC_CONF/ldap-login.conf
     ```

     文件。将以下

     ```
     KafkaClient
     ```

     登录部分添加 到 

     ```
     ldap-login.conf
     ```

      文件末尾：

     ```
     KafkaClient {
         com.sun.security.auth.module.Krb5LoginModule required
         useKeyTab=true
         keyTab="<keytab path>"
         principal="<principal name>/<host name>@<realm>";
     };
     ```

     例如：

     ```
     KafkaClient {
         com.sun.security.auth.module.Krb5LoginModule required
         useKeyTab=true
         keyTab="/etc/security/keytabs/sdc.keytab"
         principal="sdc/sdc-01.streamsets.net@EXAMPLE.COM";
     };
     ```

   - 使用LDAP身份验证的Cloudera Manager安装

      -如果在Cloudera Manager安装中启用了LDAP身份验证，请在Cloudera Manager中为StreamSets服务启用LDAP配置文件替换（ldap.login.file.allow.substitutions）属性。

     如果启用了“使用安全阀编辑LDAP信息（use.ldap.login.file）”属性，并且在ldap-login.conf字段的“数据收集器高级配置代码段（安全阀）”中配置了LDAP身份验证，则添加JAAS配置属性与ldap-login.conf安全阀相同。

     如果通过LDAP属性而不是ldap-login.conf安全值配置LDAP身份验证，则将JAAS配置属性添加到generate-ldap-login-append.conf字段的数据收集器高级配置代码片段（安全阀）中。

     将以下`KafkaClient`登录部分添加 到适当的字段，如下所示：

     ```
     KafkaClient {
         com.sun.security.auth.module.Krb5LoginModule required
         useKeyTab=true
         keyTab="_KEYTAB_PATH"
         principal="<principal name>/_HOST@<realm>";
     };
     ```

     例如：

     ```
     KafkaClient {
         com.sun.security.auth.module.Krb5LoginModule required
         useKeyTab=true
         keyTab="_KEYTAB_PATH"
         principal="sdc/_HOST@EXAMPLE.COM";
     };
     ```

     Cloudera Manager会生成适当的密钥表路径和主机名。

4. 在阶段的“ **常规”**选项卡上，将“ **阶段库”**属性设置为适当的Apache Kafka版本。

5. 在**Kafka**选项卡上，添加 **security.protocol** Kafka配置属性，并将其设置为**SASL_PLAINTEXT**。

6. 然后，添加 **sasl.kerberos.service.name**配置属性，并将其设置为**kafka**。

例如，以下Kafka属性允许使用Kerberos连接到Kafka：

![img](imgs/Kafka-Kerberos-20200310184802515.png)

### 启用SSL / TLS和Kerberos

您可以启用Kafka Producer来使用SSL / TLS和Kerberos连接到Kafka。

![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中无效。在Data Collector Edge管道中，Kafka Producer目标[仅](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_czm_55c_rw)支持使用[SSL / TLS](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_czm_55c_rw)连接到Kafka。

要使用SSL / TLS和Kerberos，请组合所需的步骤以启用每个步骤并按如下所示设置security.protocol属性：

1. 确保Kafka已配置为使用以下Kafka文档中所述的SSL / TLS和Kerberos（SASL）：

   - http://kafka.apache.org/documentation.html#security_ssl
   - http://kafka.apache.org/documentation.html#security_sasl

2. 确保Kerberos身份验证是否启用了数据采集，如在[Kerberos身份验证](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/Kerberos.html#concept_hnm_n4l_xs)。

3. 根据您的安装和身份验证类型，添加Kafka客户端所需的Java身份验证和授权服务（JAAS）配置属性：

   - 没有LDAP身份验证的RPM，tarball或Cloudera Manager安装

      -如果

     Data Collector

     不使用LDAP身份验证，请在

     Data Collector

      计算机上创建一个单独的JAAS配置文件。将以下

     ```
     KafkaClient
     ```

     登录部分添加到文件中：

     ```
     KafkaClient {
         com.sun.security.auth.module.Krb5LoginModule required
         useKeyTab=true
         keyTab="<keytab path>"
         principal="<principal name>/<host name>@<realm>";
     };
     ```

     例如：

     ```
     KafkaClient {
         com.sun.security.auth.module.Krb5LoginModule required
         useKeyTab=true
         keyTab="/etc/security/keytabs/sdc.keytab"
         principal="sdc/sdc-01.streamsets.net@EXAMPLE.COM";
     };
     ```

     然后修改SDC_JAVA_OPTS环境变量，使其包含以下选项，这些选项定义了JAAS配置文件的路径：

     ```
     -Djava.security.auth.login.config=<JAAS config path>
     ```

     使用安装类型所需的方法。

   - 使用LDAP认证的RPM或tarball安装

      -如果在RPM或tarball的安装中启用了LDAP认证，则将属性添加到

     Data Collector

     使用的JAAS配置文件中-该 

     ```
     $SDC_CONF/ldap-login.conf
     ```

     文件。将以下

     ```
     KafkaClient
     ```

     登录部分添加 到 

     ```
     ldap-login.conf
     ```

      文件末尾：

     ```
     KafkaClient {
         com.sun.security.auth.module.Krb5LoginModule required
         useKeyTab=true
         keyTab="<keytab path>"
         principal="<principal name>/<host name>@<realm>";
     };
     ```

     例如：

     ```
     KafkaClient {
         com.sun.security.auth.module.Krb5LoginModule required
         useKeyTab=true
         keyTab="/etc/security/keytabs/sdc.keytab"
         principal="sdc/sdc-01.streamsets.net@EXAMPLE.COM";
     };
     ```

   - 使用LDAP身份验证的Cloudera Manager安装

      -如果在Cloudera Manager安装中启用了LDAP身份验证，请在Cloudera Manager中为StreamSets服务启用LDAP配置文件替换（ldap.login.file.allow.substitutions）属性。

     如果启用了“使用安全阀编辑LDAP信息（use.ldap.login.file）”属性，并且在ldap-login.conf字段的“数据收集器高级配置代码段（安全阀）”中配置了LDAP身份验证，则添加JAAS配置属性与ldap-login.conf安全阀相同。

     如果通过LDAP属性而不是ldap-login.conf安全值配置LDAP身份验证，则将JAAS配置属性添加到generate-ldap-login-append.conf字段的数据收集器高级配置代码片段（安全阀）中。

     将以下`KafkaClient`登录部分添加 到适当的字段，如下所示：

     ```
     KafkaClient {
         com.sun.security.auth.module.Krb5LoginModule required
         useKeyTab=true
         keyTab="_KEYTAB_PATH"
         principal="<principal name>/_HOST@<realm>";
     };
     ```

     例如：

     ```
     KafkaClient {
         com.sun.security.auth.module.Krb5LoginModule required
         useKeyTab=true
         keyTab="_KEYTAB_PATH"
         principal="sdc/_HOST@EXAMPLE.COM";
     };
     ```

     Cloudera Manager会生成适当的密钥表路径和主机名。

4. 在阶段的“ **常规”**选项卡上，将“ **阶段库”**属性设置为适当的Apache Kafka版本。

5. 在**Kafka**选项卡上，添加 **security.protocol**属性并将其设置为 **SASL_SSL**。

6. 然后，添加 **sasl.kerberos.service.name**配置属性，并将其设置为**kafka**。

7. 然后添加并配置以下SSL Kafka属性：

   - ssl.truststore.location
   - ssl.truststore.password

   当Kafka代理要求客户端身份验证时-ssl.client.auth代理属性设置为“必需”时-添加并配置以下属性：

   - ssl.keystore.location
   - ssl.keystore.password
   - ssl.key.password

   一些经纪人可能还需要添加以下属性：

   - ssl.enabled.protocols
   - ssl.truststore.type
   - ssl.keystore.type

   有关这些属性的详细信息，请参见Kafka文档。

## 资料格式

Kafka Producer目标根据您选择的数据格式将数据写入Kafka。

![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中， 目标仅支持Binary，JSON，SDC Record和Text数据格式。

Kafka Producer目标处理数据格式如下：

- 阿夫罗

  目标根据Avro架构写入记录。

  您可以使用以下方法之一来指定Avro模式定义的位置：

  **在“管道配置”中** -使用您在阶段配置中提供的架构。**在记录标题中** -使用avroSchema记录标题属性中包含的架构。**Confluent Schema Registry-**从Confluent Schema Registry检索架构。Confluent Schema Registry是Avro架构的分布式存储层。您可以配置目标以通过架构ID或主题在Confluent Schema Registry中查找架构。您必须指定Kafka Producer用于以Avro格式序列化消息的方法。要在目标写入的每条消息中嵌入Avro模式ID，请在“ **Kafka”**选项卡上将键和值序列化器设置为“ Confluent” 。如果在阶段或记录头属性中使用Avro架构，则可以选择配置目标以在Confluent Schema Registry中注册Avro架构。您还可以选择在消息中包括架构定义。

  您可以在输出中包括Avro模式。您还可以使用Avro支持的压缩编解码器压缩数据。

- 二元

  该阶段将二进制数据写入记录中的单个字段。

- 定界

  目标将记录写为定界数据。使用此数据格式时，根字段必须是list或list-map。

  您可以使用以下定界格式类型：**默认CSV-**包含逗号分隔值的文件。忽略文件中的空行。**RFC4180 CSV-**严格遵循RFC4180准则的逗号分隔文件。**MS Excel CSV** -Microsoft Excel逗号分隔文件。**MySQL CSV** -MySQL逗号分隔文件。**制表符分隔的值** -包含制表符分隔的值的文件。**PostgreSQL CSV** -PostgreSQL逗号分隔文件。**PostgreSQL文本** -PostgreSQL文本文件。**自定义** -使用用户定义的定界符，转义符和引号字符的文件。**多字符**定界-使用多个用户定义的字符定界字段和行以及单个用户定义的转义和引号字符的文件。

- JSON格式

  目标将记录作为JSON数据写入。您可以使用以下格式之一：数组-每个文件都包含一个数组。在数组中，每个元素都是每个记录的JSON表示形式。多个对象-每个文件都包含多个JSON对象。每个对象都是记录的JSON表示形式。

- 原虫

  在一条消息中写入一条记录。在描述符文件中使用用户定义的消息类型和消息类型的定义来生成消息。

  有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。

- SDC记录

  目标以SDC记录数据格式写入记录。

- 文本

  目标将数据从单个文本字段写入目标系统。配置阶段时，请选择要使用的字段。

  您可以配置字符以用作记录分隔符。默认情况下，目标使用UNIX样式的行尾（\ n）分隔记录。

  当记录不包含选定的文本字段时，目标可以将缺少的字段报告为错误或忽略缺少的字段。默认情况下，目标报告错误。

  当配置为忽略缺少的文本字段时，目标位置可以丢弃该记录或写入记录分隔符以为该记录创建一个空行。默认情况下，目标丢弃记录。

- XML格式

  目标为每个记录创建一个有效的XML文档。目标要求记录具有一个包含其余记录数据的单个根字段。有关如何完成此操作的详细信息和建议，请参阅[记录结构要求](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WritingXML.html#concept_cmn_hml_r1b)。目的地可以包括缩进以产生人类可读的文档。它还可以验证所生成的XML是否符合指定的架构定义。具有无效架构的记录将根据为目标配置的错误处理进行处理。

## 配置Kafka生产者目的地

配置Kafka Producer目标，以将数据写入Kafka集群。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | 舞台库                                                       | 您要使用的库版本。                                           |
   | [必填项](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_dnj_bkm_vq) | 必须包含用于将记录传递到阶段的记录的数据的字段。**提示：**您可能包括舞台使用的字段。根据为管道配置的错误处理，处理不包含所有必填字段的记录。 |
   | [前提条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/DroppingUnwantedRecords.html#concept_msl_yd4_fs) | 必须评估为TRUE的条件才能使记录进入处理阶段。单击 **添加**以创建其他前提条件。根据为阶段配置的错误处理，处理不满足所有前提条件的记录。 |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **卡夫卡”**选项卡上，配置以下属性：

   | 卡夫卡房产                                                   | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 经纪人URI                                                    | Kafka代理的连接字符串。使用以下格式： `:`。要确保连接，请输入其他代理URI的逗号分隔列表。 |
   | [运行时主题解析](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_ok1_cwr_xr) | 在运行时评估表达式，以确定每个记录要使用的主题。             |
   | 话题                                                         | 要使用的主题。使用运行时主题解析时不可用。                   |
   | 主题表达                                                     | 该表达式用于确定使用运行时主题解析时每个记录的写入位置。使用计算结果为主题名称的表达式。 |
   | 主题白名单                                                   | 使用运行时主题解析时要写入的有效主题名称的列表。用于避免写入无效的主题。解析为无效主题名称的记录将传递到阶段以进行错误处理。使用星号（*）允许写入任何主题名称。默认情况下，所有主题名称均有效。 |
   | [分区策略](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_qpm_xp4_4r) | 用于写入分区的策略：Round Robin-轮流写入不同的分区。随机-随机写入分区。表达式-使用表达式将数据写入不同的分区。将记录写到表达式结果指定的分区中。默认-使用表达式从记录中提取分区键。根据分区键的哈希将记录写入分区。 |
   | [分区表达](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_qpm_xp4_4r) | 与默认或表达式分区策略一起使用的表达式。使用默认分区策略时，请指定一个表达式，该表达式从记录中返回分区键。该表达式必须计算为字符串值。使用表达式分区策略时，请指定一个表达式，该表达式的计算结果为您希望将每个记录写入的分区。分区号以0开头。表达式必须计算为数值。（可选）单击**Ctrl +空格键**以帮助创建表达式。 |
   | 每批一封邮件                                                 | 对于每个批次，将记录作为一条消息写入每个分区。               |
   | Kafka配置                                                    | 要使用的其他Kafka属性。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标，然后定义Kafka属性名称和值。使用Kafka期望的属性名称和值。不要使用broker.list属性。有关启用与Kafka的安全连接的信息，请参阅[启用安全性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_znr_b3c_rw)。 |
   | [密钥序列化器](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_lww_3b3_kr) | 当配置的数据格式为Avro时，用于序列化Kafka消息密钥的方法。设置为Confluent以将Avro模式ID嵌入到Kafka Producer编写的每条消息中。 |
   | 值序列化器                                                   | 当配置的数据格式为Avro时，用于序列化Kafka消息值的方法。设置为Confluent以将Avro模式ID嵌入到Kafka Producer编写的每条消息中。 |

3. 在“ **数据格式”**选项卡上，配置以下属性：

   | 数据格式属性                                                 | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [资料格式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_lww_3b3_kr) | 消息的数据格式。使用以下格式之一：阿夫罗二元定界JSON格式原虫[SDC记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/SDCRecordFormat.html#concept_qkk_mwk_br)文本XML格式![img](https://streamsets.com/documentation/controlhub/latest/help/reusable-content/shared-graphics/icon-Edge.png)在Data Collector Edge管道中， 目标仅支持Binary，JSON，SDC Record和Text数据格式。 |

4. 对于Avro数据，在“ **数据格式”**选项卡上，配置以下属性：

   | Avro物业             | 描述                                                         |
   | :------------------- | :----------------------------------------------------------- |
   | Avro模式位置         | 写入数据时要使用的Avro模式定义的位置：在“管道配置”中-使用您在阶段配置中提供的架构。在记录头中-在avroSchema [记录头属性中](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/RecordHeaderAttributes.html#concept_lmn_gdc_1w)使用架构 。仅在为所有记录定义avroSchema属性时使用。Confluent Schema Registry-从Confluent Schema Registry检索架构。 |
   | Avro模式             | 用于写入数据的Avro模式定义。您可以选择使用该`runtime:loadResource` 函数来加载存储在运行时资源文件中的架构定义。 |
   | 注册架构             | 在Confluent Schema Registry中注册新的Avro架构。              |
   | 架构注册表URL        | 汇合的架构注册表URL，用于查找架构或注册新架构。要添加URL，请单击 **添加**，然后以以下格式输入URL：`http://:` |
   | 基本身份验证用户信息 | 使用基本身份验证时连接到Confluent Schema Registry所需的用户信息。`schema.registry.basic.auth.user.info`使用以下格式从Schema Registry中的设置中输入密钥和机密 ：`:`**提示：** 为了保护敏感信息，例如用户名和密码，可以使用 [运行时资源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/RuntimeValues.html#concept_bs4_5nm_2s)或凭据存储。有关凭证存储的更多信息，请参阅Data Collector文档中的[凭证存储](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/CredentialStores.html)。 |
   | 查找架构             | 在Confluent Schema Registry中查找架构的方法：主题-查找指定的Avro模式主题。架构ID-查找指定的Avro架构ID。 |
   | 模式主题             | Avro架构可以在Confluent Schema Registry中查找或注册。如果要查找的指定主题有多个架构版本，则目标对该主题使用最新的架构版本。要使用旧版本，请找到相应的架构ID，然后将“ **查找架构**依据**”** 属性设置为“架构ID”。 |
   | 架构编号             | 在Confluent Schema Registry中查找的Avro模式ID。              |
   | 包含架构             | 在每个消息中包含架构。**注意：**如果您将Kafka Producer配置为在所写的每条消息中嵌入Avro模式ID，请清除此属性。 |
   | Avro压缩编解码器     | 要使用的Avro压缩类型。使用Avro压缩时，请勿在目标中启用其他可用压缩。 |

5. 对于二进制数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 二元性质     | 描述                   |
   | :----------- | :--------------------- |
   | 二进制场路径 | 包含二进制数据的字段。 |

6. 对于定界数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 定界财产   | 描述                                                         |
   | :--------- | :----------------------------------------------------------- |
   | 分隔符格式 | 分隔数据的格式：**默认CSV-**包含逗号分隔值的文件。忽略文件中的空行。**RFC4180 CSV-**严格遵循RFC4180准则的逗号分隔文件。**MS Excel CSV** -Microsoft Excel逗号分隔文件。**MySQL CSV** -MySQL逗号分隔文件。**制表符分隔的值** -包含制表符分隔的值的文件。**PostgreSQL CSV** -PostgreSQL逗号分隔文件。**PostgreSQL文本** -PostgreSQL文本文件。**自定义** -使用用户定义的定界符，转义符和引号字符的文件。 |
   | 标题行     | 指示是否创建标题行。                                         |
   | 替换换行符 | 用配置的字符串替换换行符。在将数据写为单行文本时推荐使用。   |
   | 换行符替换 | 用于替换每个换行符的字符串。例如，输入一个空格，用空格替换每个换行符。留空以删除新行字符。 |
   | 分隔符     | 自定义分隔符格式的分隔符。选择一个可用选项，或使用“其他”输入自定义字符。您可以输入使用格式\ U A的Unicode控制符*NNNN*，其中*ñ*是数字0-9或字母AF十六进制数字。例如，输入\ u0000将空字符用作分隔符，或者输入\ u2028将行分隔符用作分隔符。默认为竖线字符（\|）。 |
   | 转义符     | 自定义分隔符格式的转义符。选择一个可用选项，或使用“其他”输入自定义字符。默认为反斜杠字符（\）。 |
   | 引用字符   | 自定义分隔符格式的引号字符。选择一个可用选项，或使用“其他”输入自定义字符。默认为引号字符（“”）。 |
   | 字符集     | 写入数据时使用的字符集。                                     |

7. 对于JSON数据，在**数据格式**选项卡上，配置以下属性：

   | JSON属性 | 描述                                                         |
   | :------- | :----------------------------------------------------------- |
   | JSON内容 | 写入JSON数据的方法：JSON对象数组-每个文件都包含一个数组。在数组中，每个元素都是每个记录的JSON表示形式。多个JSON对象-每个文件包含多个JSON对象。每个对象都是记录的JSON表示形式。 |
   | 字符集   | 写入数据时使用的字符集。                                     |

8. 对于protobuf数据，在“ **数据格式”**选项卡上，配置以下属性：

   | Protobuf属性       | 描述                                                         |
   | :----------------- | :----------------------------------------------------------- |
   | Protobuf描述符文件 | 要使用的描述符文件（.desc）。描述符文件必须位于Data Collector资源目录中`$SDC_RESOURCES`。有关环境变量的更多信息，请参阅 Data Collector 文档中的Data Collector [环境配置](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html)。有关生成描述符文件的信息，请参阅[Protobuf数据格式先决条件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/Protobuf-Prerequisites.html)。 |
   | 讯息类型           | 写入数据时使用的消息类型的全限定名称。使用以下格式： `.`。使用在描述符文件中定义的消息类型。 |

9. 对于文本数据，在“ **数据格式”**选项卡上，配置以下属性：

   | 文字属性                       | 描述                                                         |
   | :----------------------------- | :----------------------------------------------------------- |
   | 文字栏位路径                   | 包含要写入的文本数据的字段。所有数据必须合并到指定字段中。   |
   | 记录分隔符                     | 用于分隔记录的字符。使用任何有效的Java字符串文字。例如，当写入Windows时，您可能会\r\n用来分隔记录。默认情况下，目标使用 \n。 |
   | 在失落的田野上                 | 当记录不包含文本字段时，确定目标是将丢失的字段报告为错误还是忽略该丢失的字段。 |
   | 如果没有文本，则插入记录分隔符 | 当配置为忽略缺少的文本字段时，插入配置的记录分隔符字符串以创建一个空行。如果未选择，则丢弃没有文本字段的记录。 |
   | 字符集                         | 写入数据时使用的字符集。                                     |

10. 对于XML数据，在“ **数据格式”**选项卡上，配置以下属性：

    | XML属性  | 描述                                                         |
    | :------- | :----------------------------------------------------------- |
    | 漂亮格式 | 添加缩进以使生成的XML文档更易于阅读。相应地增加记录大小。    |
    | 验证架构 | 验证生成的XML是否符合指定的架构定义。具有无效架构的记录将根据为目标配置的错误处理进行处理。**要点：**无论是否验证XML模式，目的地都需要特定格式的记录。有关更多信息，请参见[记录结构要求](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/WritingXML.html#concept_cmn_hml_r1b)。 |
    | XML模式  | 用于验证记录的XML模式。                                      |

11. 在微服务管道中使用目标时，在“ **响应”**选项卡上，配置以下属性。在非微服务管道中，这些属性将被忽略。

    | [响应属性](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Destinations/KProducer.html#concept_jlq_btd_rfb) | 描述                                                     |
    | :----------------------------------------------------------- | :------------------------------------------------------- |
    | 发送回复到起源                                               | 启用发送对微服务源的响应。                               |
    | 回应类型                                                     | 发送到微服务源的响应：成功写入记录。来自目标系统的响应。 |