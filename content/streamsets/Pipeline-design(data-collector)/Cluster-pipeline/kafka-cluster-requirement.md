# Kafka群集要求

从Kafka集群读取的集群模式管道具有以下要求：

| 零件                    | 需求                                                         |
| :---------------------- | :----------------------------------------------------------- |
| 用于集群流模式的Spark流 | Spark 2.1版或更高版本                                        |
| 阿帕奇·卡夫卡           | YARN上的Spark Streaming需要Apache Kafka集群版本0.10.0.0或更高版本的Cloudera或Hortonworks发行版。Mesos上的Spark Streaming需要Apache Mesos上的Apache Kafka。 |

**注意：**默认情况下，Cloudera CDH集群将Kafka-Spark集成版本设置为0.9。但是，Data Collector 群集流传输管道需要Kafka-Spark集成的0.10版。结果，在Data Collector [环境配置文件](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/DCEnvironmentConfig.html#concept_rng_qym_qr) - `sdc.env.sh`或sdcd.env.sh中，默认情况下将SPARK_KAFKA_VERSION环境变量设置为0.10。不要更改此环境变量值。

## 卡夫卡消费者最大批量

在集群模式下使用[Kafka Consumer来源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/KConsumer.html#concept_msz_wnr_5q)时，“最大批处理大小”属性将被忽略。而是，有效的批次大小为<批次等待时间> x <每个分区的速率限制>。

例如，如果“批处理等待时间”为60秒，“每个分区的速率限制”为1000条消息/秒，则从Spark Streaming透视图来看，有效批处理大小为60 x 1000 = 60000条消息/秒。在此示例中，只有一个分区，因此仅生成了一个集群管道，该管道的批处理大小为60000。

如果有两个分区，那么从Spark Streaming透视图来看，有效批处理大小为60 x 1000 x 2 = 120000消息/秒。默认情况下，创建两个集群管道。如果每个分区中的消息数相等，则每个管道将在一批中接收60000条消息。但是，如果所有120000消息都在单个分区中，则处理该分区的集群管道将接收所有120000消息。

要减少最大批处理大小，请减少等待时间或减少每个分区的速率限制。同样，要增加最大批处理大小，请增加等待时间或增加每个分区的速率限制。



## 为Kafka配置群集YARN流

完成以下步骤，配置集群管道以从YARN上的Kafka集群读取：

1. 验证是否已将Kafka，Spark Streaming和YARN安装为集群管理器。

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

   例如，使用CDH Spark 2.1时，默认情况下，spark-submit脚本位于以下目录中：/ usr / bin / spark2-submit。然后，您可以使用以下命令来定义脚本的位置：

   ```
   export SPARK_SUBMIT_YARN_COMMAND=/usr/bin/spark2-submit
   ```

   或者，如果使用包含Spark 2.2.0的Hortonworks Data Platform（HDP）2.6，则默认情况下，spark-submit脚本位于以下目录中：/usr/hdp/2.6/spark2/bin/spark-submit。然后，您可以使用以下命令来定义脚本的位置：

   ```
   export SPARK_SUBMIT_YARN_COMMAND=/usr/hdp/2.6/spark2/bin/spark-submit
   ```

   **注意：**如果更改spark-submit脚本的位置，则必须重新启动Data Collector才能捕获更改。

5. 要使Data Collector能够提交YARN作业，请执行以下任务之一：

   - 在YARN上，将min.user.id设置为等于或小于与Data Collector用户ID（通常称为“ sdc”）关联的用户ID的值。
   - 在YARN上，将Data Collector用户名（通常为“ sdc”）添加到allowed.system.users属性中。

6. 在YARN上，验证Spark日志记录级别设置为INFO或更低的严重性。

   YARN默认将Spark日志记录级别设置为INFO。要更改日志记录级别：

   1. 编辑位于以下目录中的log4j.properties文件：

      ```
      <spark-home>/conf/log4j.properties
      ```

   2. 将**log4j.rootCategory**属性设置为INFO或更低的严重性，例如DEBUG或TRACE。

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

10. 在管道中，使用Kafka Consumer来源。

    如有必要，请在原点的“ **常规”**选项卡上选择一个集群模式阶段库 。

    **注意：**在群集模式下，Kafka Consumer来源的批处理等待时间将被忽略。有关更多信息，请参阅[Kafka Consumer Maximum Batch Size](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/KafkaRequirements.html#concept_al2_cxh_cdb)。

11. 如果将Kafka群集配置为使用SSL / TLS，Kerberos或同时使用两者，请按照[为群集YARN流启用安全性中的说明](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/KafkaRequirements.html#concept_bb2_m5p_tdb)，将Kafka使用者来源配置为安全地连接到群集 。

**相关信息**

[配置管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ConfiguringAPipeline.html#task_xlv_jdw_kq)

[卡夫卡消费者](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/KConsumer.html#concept_msz_wnr_5q)

## 为群集YARN流启用安全性

当使用群集管道从YARN上的Kafka群集中读取数据时，可以将Kafka Consumer来源配置为通过SSL / TLS，Kerberos或两者安全地连接。

### 启用SSL / TLS

执行以下步骤以在YARN上的群集流传输管道中启用Kafka Consumer来源，以使用SSL / TLS连接到Kafka。

1. 要使用SSL / TLS进行连接，首先请确保按照[Kafka文档](http://kafka.apache.org/documentation.html#security_ssl)中的[说明](http://kafka.apache.org/documentation.html#security_ssl)为Kafka配置了SSL / TLS 。

2. 在群集管道中“ Kafka Consumer”来源的“ **常规”**选项卡上，将“ **舞台库”**属性设置为Apache Kafka 0.10.0.0或更高版本。

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

5. 将SSL truststore和密钥库文件存储在Data Collector 计算机上和YARN群集中的每个节点上的同一位置。

例如，以下属性允许该阶段使用SSL / TLS通过客户端身份验证连接到Kafka：

![img](imgs/Kafka-SSLoptions-20200310204919491.png)

### 启用Kerberos（SASL）

使用Kerberos身份验证时，Data Collector使用Kerberos主体和密钥表连接到Kafka。

执行以下步骤以在YARN上的群集流传输管道中启用Kafka Consumer来源，以使用Kerberos连接到Kafka：

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

4. 将JAAS配置和Kafka keytab文件存储在Data Collector 计算机上以及YARN群集中每个节点上的相同位置。

5. 在群集管道中“ Kafka Consumer”来源的“ **常规”**选项卡上，将“ **舞台库”**属性设置为Apache Kafka 0.10.0.0或更高版本。

6. 在**Kafka**选项卡上，添加 **security.protocol** Kafka配置属性，并将其设置为**SASL_PLAINTEXT**。

7. 然后，添加 **sasl.kerberos.service.name**配置属性，并将其设置为**kafka**。

例如，以下Kafka属性允许使用Kerberos连接到Kafka：

![img](imgs/Kafka-Kerberos-20200310204920706.png)

### 启用SSL / TLS和Kerberos

您可以在YARN上的群集流传输管道中启用Kafka Consumer来源，以使用SSL / TLS和Kerberos连接到Kafka。

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

4. 将JAAS配置和Kafka keytab文件存储在Data Collector 计算机上以及YARN群集中每个节点上的相同位置。

5. 在群集管道中“ Kafka Consumer”来源的“ **常规”**选项卡上，将“ **舞台库”**属性设置为Apache Kafka 0.10.0.0或更高版本。

6. 在**Kafka**选项卡上，添加 **security.protocol**属性并将其设置为 **SASL_SSL**。

7. 然后，添加 **sasl.kerberos.service.name**配置属性，并将其设置为**kafka**。

8. 然后添加并配置以下SSL Kafka属性：

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

9. 将SSL truststore和密钥库文件存储在Data Collector 计算机上和YARN群集中的每个节点上的同一位置。

## 为Kafka配置集群Mesos流

完成以下步骤，配置集群管道以从Mesos上的Kafka集群读取：

1. 验证是否已将Kafka，Spark Streaming和Mesos安装为集群管理器。

2. 在Spark and Mesos网关节点上安装Data Collector。

3. 要启用检查点元数据存储，请授予用户环境变量中定义的用户对/ user / $ SDC_USER的写许可权 。

   用户环境变量定义用于将Data Collector作为服务运行的系统用户。定义用户环境变量的文件取决于您的操作系统。有关更多信息，请参见 [服务启动的用户和组](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/DCUserGroupServiceStart.html#concept_htz_t1s_3v)。

   例如，说$ SDC_USER被定义为sdc。然后，您可以使用以下命令创建目录并配置必要的写权限：

   ```
   $sudo -u hdfs hadoop fs -mkdir /user/sdc
   $sudo -u hdfs hadoop fs -chown sdc /user/sdc
   ```

4. 如有必要，请指定指向Spark版本2.1或更高版本的spark-submit脚本的位置。

   Data Collector假定用于将作业请求提交到Spark Streaming的spark-submit脚本位于以下目录中：

   ```
   /usr/bin/spark-submit
   ```

   如果脚本不在此目录中，请使用SPARK_SUBMIT_MESOS_COMMAND环境变量来定义脚本的位置。

   脚本的位置可能会有所不同，具体取决于您使用的Spark版本和发行版。

   例如，使用CDH Spark 2.1时，默认情况下，spark-submit脚本位于以下目录中：/ usr / bin / spark2-submit。然后，您可以使用以下命令来定义脚本的位置：

   ```
   export SPARK_SUBMIT_MESOS_COMMAND=/usr/bin/spark2-submit
   ```

   **注意：**如果更改spark-submit脚本的位置，则必须重新启动Data Collector才能捕获更改。

5. 在管道属性中的“ **常规”**选项卡上，将“ **执行模式”**属性设置 为“ **集群Mesos流”**。

6. 在“ **群集”**选项卡上，配置以下属性：

   | 集群属性                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | Mesos分派器URL                                               | Mesos调度程序的主URL。例如：`mesos://dispatcher:7077`        |
   | 检查点配置目录 [![img](imgs/icon_moreInfo-20200310204919920.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/ClusterPipelines.html#concept_cs4_lcg_j5) | HDFS配置文件的位置，该文件指定是将检查点元数据写入HDFS还是Amazon S3。在Data Collector资源目录中使用目录或符号链接。该目录应包含以下文件：core-site.xmlhdfs-site.xml |

7. 在管道中，将Kafka Consumer来源用于群集模式。

   如有必要，请在原点的“ **常规”**选项卡上选择一个集群模式阶段库 。

   **注意：**在群集模式下，Kafka Consumer来源的批处理等待时间将被忽略。有关更多信息，请参阅[Kafka Consumer Maximum Batch Size](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Cluster_Mode/KafkaRequirements.html#concept_al2_cxh_cdb)。