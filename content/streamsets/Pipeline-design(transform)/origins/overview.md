# 起源概述

原始阶段代表管道的源。您可以在管道中使用单个原始阶段。或者，您可以在管道中使用多个原始阶段，然后使用[Join处理器](https://streamsets.com/documentation/controlhub/latest/help/transformer/Processors/Join.html#concept_xdr_slq_sgb)将原始合并。

您可以在Transformer管道中使用以下来源：

- [ADLS Gen1-](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/ADLS-G1.html#concept_dxr_pyl_thb)读取Azure Data Lake Storage Gen1中的文件。
- [ADLS Gen2-](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/ADLS-G2.html#concept_cnw_rzs_thb)读取Azure Data Lake Storage Gen2中的文件。
- [Amazon S3-](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/AmazonS3.html#concept_gww_1kw_shb)读取Amazon S3中的对象。
- [Delta](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/DLake.html#concept_c3r_l4n_y3b) Lake-从Delta Lake表中读取数据。
- [文件](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/File.html#concept_jcx_f2d_qgb) -读取HDFS或本地文件系统中的文件。
- [蜂巢](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Hive.html#concept_nvg_112_f3b) -从蜂巢表中读取数据。
- [JDBC-](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/JDBC.html#concept_ojy_msd_qgb)使用JDBC驱动程序从数据库表中读取数据。
- [Kafka-](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Kafka.html#concept_yvm_53j_zgb)从Apache Kafka集群中的主题读取数据。
- [Snowflake-](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/Snowflake.html#concept_vnd_xnp_3jb)从Snowflake数据库读取数据。
- [整个目录](https://streamsets.com/documentation/controlhub/latest/help/transformer/Origins/WholeDirectory.html#concept_kw1_slt_43b) -批量读取目录中的所有文件。