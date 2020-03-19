# UDP来源

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310174816573.png) 资料收集器

UDP Source源从一个或多个UDP端口读取消息。要将多个线程用于管道处理，请使用[UDP Multithreaded Source](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/UDPMulti.html#concept_wng_g5f_5bb)。有关两个来源之间差异的讨论，请参见[比较UDP源来源](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ffh_5vf_5bb)。

UDP Source为每条消息生成一条记录。UDP Source 可以处理[收集的](https://collectd.org/)消息，NetFlow 5和NetFlow 9消息以及以下类型的syslog消息：

- [RFC 5424](https://tools.ietf.org/html/rfc5424)
- [RFC 3164](https://tools.ietf.org/html/rfc3164)
- 非标准通用消息，例如RFC 3339日期，没有版本数字

在处理NetFlow消息时，该阶段会根据NetFlow版本生成不同的记录。处理NetFlow 9时，将基于NetFlow 9配置属性生成记录。有关更多信息，请参见[NetFlow数据处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/NetFlow_Overview.html#concept_thl_nnr_hbb)。

源也可以读取二进制或基于字符的原始数据。

配置UDP Source时，可以指定要使用的端口以及批处理大小和等待时间。当epoll可用时，您可以指定用于增加数据包到管道吞吐量的接收器线程数。

您还可以为数据指定数据格式，然后配置所有相关属性。

## 处理原始数据

使用“原始/分离的数据”数据格式可启用UDP源起源以从二进制或基于字符的原始数据生成记录。

处理原始数据时，源可以为其接收的每个UDP数据包生成一条记录。或者，如果指定分隔符，则源可以从每个UDP数据包生成多个记录。

生成多个记录时，可以指定多个值的行为：一个仅包含第一个值的记录，一个包含所有值作为列表的记录，或多个记录，每个值包含一个记录。

您可以选择指定用于数据的输出字段。如果未指定，则原始将原始数据写入根字段。

您可以使用“原始/分离的数据”数据格式将原始数据写入到一个字段中，然后使用Data Parser处理器对其进行处理。这使您可以保留原始数据以供其他使用。

## 接收线程

接收器线程用于将数据从UDP源系统传递到源。默认情况下，源使用单个接收线程。

当Data Collector在启用了epoll的计算机上运行时，可以将UDP Source源配置为使用其他接收器线程。Epoll需要本机库，并且仅当Data Collector在最新版本的64位Linux上运行时才可用。启用多个接收器线程时，会同时增加可以传递到源的数据量。

若要使用其他接收方线程，请选择“使用本机传输（epoll）”属性，然后配置“接收方线程数”。



## 配置UDP源

配置UDP源起源以处理来自UDP源的消息。

1. 在“属性”面板的“ **常规”**选项卡上，配置以下属性：

   | 一般财产                                                     | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 名称                                                         | 艺名。                                                       |
   | 描述                                                         | 可选说明。                                                   |
   | [记录错误](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Design/ErrorHandling.html#concept_atr_j4y_5r) | 该阶段的错误记录处理：放弃-放弃记录。发送到错误-将记录发送到管道以进行错误处理。停止管道-停止管道。 |

2. 在“ **UDP”**选项卡上，配置以下属性：

   | UDP属性                                                      | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | 港口                                                         | 侦听数据的端口。使用[简单或批量编辑模式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/SimpleBulkEdit.html#concept_alb_b3y_cbb)，单击 **添加**图标以列出其他端口。要监听低于1024的端口，必须由具有root特权的用户运行Data Collector。否则，操作系统不允许Data Collector绑定到端口。**注意：**没有其他管道或进程已经可以绑定到侦听端口。侦听端口只能由单个管道使用。 |
   | 使用本机传输（epoll）                                        | 指定是否对每个端口使用多个接收器线程。使用多个接收器线程可以提高性能。您可以使用epoll使用多个接收器线程，当Data Collector在最新版本的64位Linux上运行时，该线程可以使用。 |
   | 接收线程数 [![img](imgs/icon_moreInfo-20200310174816577.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/UDP.html#concept_olw_hch_5bb) | 每个端口要使用的接收器线程数。例如，如果您在每个端口上配置两个线程，并将原始服务器配置为使用三个端口，则原始服务器总共使用六个线程。当epoll在Data Collector计算机上可用时，用于增加将数据传递到源的线程数。默认值为1。 |
   | 资料格式                                                     | UDP传递的数据格式：已收集网络流系统日志原始/分离数据         |
   | 最大批处理大小（消息）                                       | 批量包含并一次通过管道的最大消息数。接受的值最高为 Data Collector的最大批处理大小。默认值是1000 数据采集器默认设置为1000。 |
   | [批处理等待时间（毫秒）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ypd_vgr_5q) | 发送部分或空批次之前要等待的毫秒数。                         |

3. 在“ **系统日志”**选项卡上，定义数据的字符集。

4. 在“ **收集”**选项卡上，定义以下收集的属性：

   | 收集财产               | 物产                                                         |
   | :--------------------- | :----------------------------------------------------------- |
   | TypesDB文件路径        | 用户提供的types.db文件的路径。覆盖默认的types.db文件。       |
   | 转换高分辨率时间和间隔 | 将收集的高分辨率时间格式间隔和时间戳转换为UNIX时间（以毫秒为单位）。 |
   | 排除间隔               | 从输出记录中排除间隔字段。                                   |
   | 认证文件               | 可选身份验证文件的路径。使用认证文件接受签名和加密的数据。   |
   | 字符集                 | 数据的字符集。                                               |

5. 对于原始数据，在“ **原始/分离的数据”**选项卡上，定义以下属性：

   | 原始/分离的数据属性 | 描述                                                         |
   | :------------------ | :----------------------------------------------------------- |
   | 数据分隔符          | 可选的数据分隔符，用于将UDP数据包分隔为多个值。使用Java Unicode语法\ u <字符代码>指定字节文字。例如，默认换行符表示如下： `\u000A`。 |
   | 原始数据模式        | 要处理的原始数据类型：二进制或字符串数据。                   |
   | 字符集              | 字符串数据使用的字符集。                                     |
   | 输出场路径          | 原始数据的可选输出字段。当不使用原始数据时，原始数据会将原始数据写入根字段。 |
   | 多值行为            | 当数据分隔符中的数据从UDP数据包生成多个值时采取的操作：仅第一个值-返回带有第一个值的一条记录。所有值作为列表-返回一条记录，其中所有值都在列表中。拆分为多个记录-返回多个记录，每个值一个记录。 |

6. 对于NetFlow 9数据，在**NetFlow 9**选项卡上，配置以下属性：

   处理早期版本的NetFlow数据时，将忽略这些属性。

   | Netflow 9属性                                                | 描述                                                         |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | [记录生成方式](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/NetFlow_Overview.html#concept_jdh_hxk_3bb) | 确定要包含在记录中的值的类型。选择以下选项之一：仅原始仅解释原始和解释 |
   | 缓存中的最大模板数                                           | 模板缓存中存储的最大模板数。有关模板的更多信息，请参见[缓存NetFlow 9模板](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/NetFlow_Overview.html#concept_ivr_j1l_3bb)。对于无限的缓存大小，默认值为-1。 |
   | 模板缓存超时（毫秒）                                         | 缓存空闲模板的最大毫秒数。超过指定时间未使用的模板将从缓存中逐出。有关模板的更多信息，请参见 [缓存NetFlow 9模板](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/NetFlow_Overview.html#concept_ivr_j1l_3bb)。无限期缓存模板的默认值为-1。 |