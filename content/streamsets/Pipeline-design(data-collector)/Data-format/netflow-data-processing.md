# NetFlow数据处理

您可以使用数据收集器来处理NetFlow 5和NetFlow 9数据。

在处理NetFlow 5数据时，数据收集器 将根据数据包头中的信息处理流记录。Data Collector 期望在同一连接上发送带有标头和流记录的多个数据包，并且中间没有字节。因此，在处理NetFlow 5消息时，没有要配置的与数据相关的属性。

在处理基于模板的NetFlow 9消息时，Data Collector会 基于缓存的模板，数据包头中的信息以及阶段中的NetFlow 9配置属性来生成记录。根据您使用的阶段类型，NetFlow 9属性显示在不同的位置：

- 对于直接从网络处理消息的来源（例如UDP Source来源），可以在NetFlow 9选项卡上配置NetFlow 9属性。
- 对于处理其他类型数据（例如JSON或protobuf）的大多数源和处理器，在选择数据报或NetFlow作为数据格式后，可以在“数据格式”选项卡上配置NetFlow 9属性。
- 对于TCP服务器，请指定NetFlow TCP模式，然后在“ NetFlow 9”选项卡上配置NetFlow 9属性。

处理NetFlow 5消息时，该阶段将忽略任何已配置的NetFlow 9属性。

## 缓存NetFlow 9模板

处理NetFlow 9数据需要缓存用于处理消息的模板。配置NetFlow 9属性时，可以指定要缓存的最大模板数，以及允许未使用的模板保留在缓存中的时间。您还可以配置该阶段，以在无限长的时间内允许无限数量的模板在缓存中。

配置缓存限制时，可以在以下情况下从缓存中弹出模板：

- 当缓存已满并且出现新模板时。
- 模板超过指定的空闲时间。

配置NetFlow 9缓存属性，以允许阶段保留模板以逻辑方式进行处理。当记录需要使用不在缓存中的模板时，该记录将传递到阶段以进行错误处理。

例如，假设您使用UDP Source源来处理来自五台服务器的NetFlow 9数据。每个服务器使用不同的模板发送数据，因此要处理来自这五个服务器的数据，可以将缓存大小设置为五个模板。但是为了允许以后添加其他服务器，可以将模板缓存设置为更高的数量。

大多数服务器会定期重新发送模板，因此在配置缓存超时时，您可能会考虑此刷新间隔。

例如，假设您的服务器每三分钟重新发送一次模板。如果将缓存超时设置为两分钟，则将驱逐两分钟内未使用的模板。如果服务器发送了需要逐出模板的数据包，则该阶段将生成错误记录，因为该模板不可用。如果将缓存超时设置为四分钟且缓存大小不受限制，则来自所有服务器的模板将保留在缓存中，直到被新版本的模板替换。

**注意：** Data Collector将缓存的模板保留在内存中。如果您需要缓存大量模板，则可能需要增加数据收集器堆大小相应。有关更多信息，请参阅Data Collector文档中的[Java堆大小](https://streamsets.com/documentation/datacollector/latest/help/#datacollector/UserGuide/Configuration/DCEnvironmentConfig.html%23concept_mdc_shg_qr)。

## NetFlow 5生成的记录

处理NetFlow 5记录时，数据收集器将 忽略任何已配置的NetFlow 9配置属性。

生成的NetFlow 5记录包括已处理的数据作为记录中的字段，没有其他元数据，如下所示：

```
 {
      "tcp_flags" : 27,
      "last" : 1503089880145,
      "length" : 360,
      "raw_first" : 87028333,
      "flowseq" : 0,
      "count" : 7,
      "proto" : 6,
      "dstaddr" : 1539135649,
      "seconds" : 1503002821,
      "id" : "27a647b5-9e3a-11e7-8db3-874a63bd401c",
      "engineid" : 0,
      "srcaddr_s" : "172.17.0.4",
      "sender" : "/0:0:0:0:0:0:0:1",
      "srcas" : 0,
      "readerId" : "/0:0:0:0:0:0:0:0:9999",
      "src_mask" : 0,
      "nexthop" : 0,
      "snmpinput" : 0,
      "dPkts" : 11214,
      "raw_sampling" : 0,
      "timestamp" : 1503002821000,
      "enginetype" : 0,
      "samplingint" : 0,
      "dstaddr_s" : "91.189.88.161",
      "samplingmode" : 0,
      "srcaddr" : -1408172028,
      "first" : 1503089849333,
      "raw_last" : 87059145,
      "dstport" : 80,
      "nexthop_s" : "0.0.0.0",
      "version" : 5,
      "uptime" : 0,
      "dOctets" : 452409,
      "nanos" : 0,
      "dst_mask" : 0,
      "packetid" : "b58f5750-7ccd-1000-8080-808080808080",
      "srcport" : 51156,
      "snmponput" : 0,
      "tos" : 0,
      "dstas" : 0
   }
```

## NetFlow 9生成的记录

NetFlow 9记录是根据您为NetFlow 9阶段属性选择的“记录生成模式”生成的。您可以在NetFlow 9记录中包括“解释的”或已处理的值，原始数据，或两者都包括。

NetFlow 9记录可以包括以下字段：

| NetFlow 9字段名称 | 描述                                                         | 包括...                                                      |
| :---------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| flowKind          | 指示要处理的流的类型：用于流程集中数据的FLOWSET。选项来自选项流。 | 在所有NetFlow 9记录中。                                      |
| 价值观            | 根据字段在数据包头中指定的模板，由阶段处理的具有字段名称和值的映射字段。 | 在NetFlow 9记录中，当您配置记录生成方式属性以在记录中包括“解释的”数据时。 |
| packetHeader      | 包含有关数据包信息的映射字段。通常包括信息，例如源ID和数据包中的记录数。 | 在所有NetFlow 9记录中。                                      |
| 原始值            | 一个映射字段，其中包含由关联的模板定义的字段以及这些字段的未经处理的原始字节。 | 在NetFlow 9记录中，当您配置记录生成方式属性以在记录中包括原始数据时。 |

### **原始记录和解释记录样本**

当您将“记录生成方式”属性设置为“原始数据和解释数据”时，结果记录包括所有可能的NetFlow 9字段，如下所示：

```
{
      "flowKind" : "FLOWSET",
      "values" : {
         "ICMP_TYPE" : 0,
         "L4_DST_PORT" : 9995,
         "TCP_FLAGS" : 0,
         "L4_SRC_PORT" : 52767,
         "INPUT_SNMP" : 0,
         "FIRST_SWITCHED" : 86400042,
         "PROTOCOL" : 17,
         "IN_BYTES" : 34964,
         "OUTPUT_SNMP" : 0,
         "LAST_SWITCHED" : 86940154,
         "IPV4_SRC_ADDR" : "127.0.0.1",
         "SRC_AS" : 0,
         "IN_PKTS" : 29,
         "IPV4_DST_ADDR" : "127.0.0.1",
         "DST_AS" : 0,
         "SRC_TOS" : 0,
         "FORWARDING_STATUS" : 0
      },
      "packetHeader" : {
         "flowRecordCount" : 8,
         "sourceIdRaw" : "AAAAAQ==",
         "version" : 9,
         "sequenceNumber" : 0,
         "unixSeconds" : 1503002821,
         "sourceId" : 1,
         "sysUptimeMs" : 0
      },
      "rawValues" : {
         "OUTPUT_SNMP" : "AAA=",
         "IN_BYTES" : "AACIlA==",
         "LAST_SWITCHED" : "BS6Z+g==",
         "IPV4_SRC_ADDR" : "fwAAAQ==",
         "SRC_AS" : "AAA=",
         "IPV4_DST_ADDR" : "fwAAAQ==",
         "IN_PKTS" : "AAAAHQ==",
         "DST_AS" : "AAA=",
         "FORWARDING_STATUS" : "AA==",
         "SRC_TOS" : "AA==",
         "ICMP_TYPE" : "AAA=",
         "TCP_FLAGS" : "AA==",
         "L4_DST_PORT" : "Jws=",
         "L4_SRC_PORT" : "zh8=",
         "INPUT_SNMP" : "AAA=",
         "FIRST_SWITCHED" : "BSZcKg==",
         "PROTOCOL" : "EQ=="
      }
   }
```

### 样本解释记录

当您将“记录生成方式”属性设置为“仅解释”时，结果记录将省略记录中的rawValues字段，如下所示：

```
{
      "flowKind" : "FLOWSET",
      "values" : {
         "ICMP_TYPE" : 0,
         "L4_DST_PORT" : 9995,
         "TCP_FLAGS" : 0,
         "L4_SRC_PORT" : 52767,
         "INPUT_SNMP" : 0,
         "FIRST_SWITCHED" : 86400042,
         "PROTOCOL" : 17,
         "IN_BYTES" : 34964,
         "OUTPUT_SNMP" : 0,
         "LAST_SWITCHED" : 86940154,
         "IPV4_SRC_ADDR" : "127.0.0.1",
         "SRC_AS" : 0,
         "IN_PKTS" : 29,
         "IPV4_DST_ADDR" : "127.0.0.1",
         "DST_AS" : 0,
         "SRC_TOS" : 0,
         "FORWARDING_STATUS" : 0
      },
      "packetHeader" : {
         "flowRecordCount" : 8,
         "sourceIdRaw" : "AAAAAQ==",
         "version" : 9,
         "sequenceNumber" : 0,
         "unixSeconds" : 1503002821,
         "sourceId" : 1,
         "sysUptimeMs" : 0
      },
   }
```

### 原始记录样本

当您将“记录生成方式”属性设置为“仅原始”时，结果记录将忽略包含已处理数据的value字段，如下所示：

```
{
      "flowKind" : "FLOWSET",
       "packetHeader" : {
         "flowRecordCount" : 8,
         "sourceIdRaw" : "AAAAAQ==",
         "version" : 9,
         "sequenceNumber" : 0,
         "unixSeconds" : 1503002821,
         "sourceId" : 1,
         "sysUptimeMs" : 0
      },
      "rawValues" : {
         "OUTPUT_SNMP" : "AAA=",
         "IN_BYTES" : "AACIlA==",
         "LAST_SWITCHED" : "BS6Z+g==",
         "IPV4_SRC_ADDR" : "fwAAAQ==",
         "SRC_AS" : "AAA=",
         "IPV4_DST_ADDR" : "fwAAAQ==",
         "IN_PKTS" : "AAAAHQ==",
         "DST_AS" : "AAA=",
         "FORWARDING_STATUS" : "AA==",
         "SRC_TOS" : "AA==",
         "ICMP_TYPE" : "AAA=",
         "TCP_FLAGS" : "AA==",
         "L4_DST_PORT" : "Jws=",
         "L4_SRC_PORT" : "zh8=",
         "INPUT_SNMP" : "AAA=",
         "FIRST_SWITCHED" : "BSZcKg==",
         "PROTOCOL" : "EQ=="
      }
   }
```