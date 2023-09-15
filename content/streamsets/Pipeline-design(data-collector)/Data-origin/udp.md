# UDP多线程源

[支持的管道类型：](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Pipeline_Configuration/ProductIcons_Doc.html#concept_mjg_ly5_pgb)![img](imgs/icon-SDC-20200310174751317.png) 资料收集器

UDP多线程源起源从一个或多个UDP端口读取消息。源可以创建多个工作线程，以在多线程管道中启用并行处理。

UDP多线程源为每个消息生成一条记录。UDP多线程源 可以处理[收集的](https://collectd.org/)消息，NetFlow 5和NetFlow 9消息以及以下类型的syslog消息：

- [RFC 5424](https://tools.ietf.org/html/rfc5424)
- [RFC 3164](https://tools.ietf.org/html/rfc3164)
- 非标准通用消息，例如RFC 3339日期，没有版本数字

在处理NetFlow消息时，该阶段会根据NetFlow版本生成不同的记录。处理NetFlow 9时，将基于NetFlow 9配置属性生成记录。有关更多信息，请参见[NetFlow数据处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/NetFlow_Overview.html#concept_thl_nnr_hbb)。

源也可以读取二进制或基于字符的原始数据。

配置UDP多线程源时，可以指定要使用的端口以及批处理大小和等待时间。您指定要在多线程处理中使用的辅助线程数，并且可以指定数据包队列大小。当epoll在Data Collector 计算机上可用时，您还可以指定用于增加流向数据包的吞吐量的接收器线程数。

您可以为数据指定数据格式，然后配置所有相关属性。

## 处理原始数据

使用原始/分离数据数据格式可启用UDP多线程源起源，以从二进制或基于字符的原始数据生成记录。

处理原始数据时，源可以为其接收的每个UDP数据包生成一条记录。或者，如果指定分隔符，则源可以从每个UDP数据包生成多个记录。

生成多个记录时，可以指定多个值的行为：一个仅包含第一个值的记录，一个包含所有值作为列表的记录，或多个记录，每个值包含一个记录。

您可以选择指定用于数据的输出字段。如果未指定，则原始将原始数据写入根字段。

您可以使用“原始/分离的数据”数据格式将原始数据写入到一个字段中，然后使用Data Parser处理器对其进行处理。这使您可以保留原始数据以供其他使用。

## 接收方线程和工作线程

UDP多线程源起源使用以下两种类型的线程：

- 接收线程

  用于将数据从操作系统套接字传递到原始数据包队列。默认情况下，源使用单个接收线程。当Data Collector在启用了epoll的计算机上运行时，可以将源配置为使用多个接收器线程。Epoll需要本机库，并且仅当Data Collector在最新版本的64位Linux上运行时才可用。启用多个接收器线程时，可以提高将数据传递到原始服务器的速率，但是会以标准增加线程管理开销的代价为代价。若要使用其他接收方线程，请选择“使用本机传输（epoll）”属性，然后配置“接收方线程数”。

- 工作线程

  用于执行多线程管道处理。默认情况下，原点使用单个线程进行管道处理。您可以增加用于并行处理大量数据的线程数。有关更多信息，请参见[多线程管道](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/UDPMulti.html#concept_qv5_yjg_5bb)。

  若要将其他工作线程用于并行处理，请增加“工作线程数”属性。

## 数据包队列

UDP多线程源起源使用数据包队列将传入的数据保存在内存中，直到可以将这些数据批量合并并通过管道传递为止。数据包队列已满时，传入的数据包将被丢弃。丢弃的数据包数量在[阶段度量中记录](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/UDPMulti.html#concept_lbj_slg_5bb)。

配置源时，可以指定队列中允许的最大数据包数。默认值为200,000。因为数据包队列使用Data Collector 堆内存，所以在增加队列的大小时，还应考虑增加 [Data Collector堆的大小](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Configuration/JavaHeapSize.html#concept_mdc_shg_qr)。

## 多线程管道

UDP多线程源起源执行并行处理，并允许创建多线程管道。

启用多线程处理时，UDP多线程源起源会根据“工作线程数”属性使用多个并发线程进行管道处理。启动管道时，源将创建属性中指定的线程数。

当数据包从指定的UDP端口到达时，它们进入数据包队列。每个管道只有一个数据包队列实例。所有接收方线程（使用epoll时，可以多于一个）将数据包放入队列中。同时，每个工作线程从队列中删除数据包，根据指定的数据格式解析它们，并使用管道运行器处理其余的管道。

管道运行器是无源管道实例 - 管道的实例，包括管道中的所有处理器，执行程序和目的地，并在源之后处理所有管道处理。 每个管道运行程序一次处理一个批处理，就像在单个线程上运行的管道一样。当数据流减慢时，管道运行器会闲置等待，直到需要它们为止，并定期生成一个空批。您可以配置“运行者空闲时间”管道属性来指定间隔或选择退出空批次生成。

多线程管道保留每个批处理中的记录顺序，就像单线程管道一样。但是由于批处理 是由不同的流水线处理程序处理的，因此无法确保将批处理写入目的地的顺序。

例如，假设您启用了多线程处理并将“工作线程数”属性设置为5。启动管道时，源将创建五个线程，而数据收集器将 创建匹配数量的管道运行器。源将输入的数据添加到数据包队列中，从队列中创建一批数据，然后将这些批处理传递给管道运行器进行处理。

每个管道运行器执行与其余管道相关联的处理。将一批写入管道目标之后，管道运行器就可用于另一批数据。每个批次的处理和写入均应尽快进行，与其他流水线处理程序处理的其他批次无关，因此批次的写入方式可能与读取顺序不同。

在任何给定的时刻，五个流水线运行者可以分别处理一个批处理，因此该多线程管道一次最多可以处理五个批处理。当传入数据变慢时，管道运行器将处于空闲状态，并在数据流增加时立即可用。

有关多线程管道的更多信息，请参见《[多线程管道概述》](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Multithreaded_Pipelines/MultithreadedPipelines.html#concept_zpp_2xc_py)。

## 性能调整指标

UDP多线程源起源提供了可以用来调整管道性能的数据包队列指标。

源提供以下数据包队列指标：

- 丢弃的数据包-由于数据包队列已满而被丢弃的数据包数。
- 队列大小-数据包队列的当前大小。
- 排队的数据包-已通过数据包队列进行处理的数据包总数。

这些指标可以帮助您确定如何提高管道性能。例如，如果丢弃的数据包数量很大，并且在监视管道时队列大小似乎已满，则可能会增加管道的工作线程数，以提高吞吐量。或者，如果您有相对较高的突发数据量，并且发现数据包在这些突发中丢失，请考虑增加数据包队列大小以更好地容纳它们。

如果队列大小没有达到最大，但是排队的数据包数量似乎没有您期望的那么高，则可能是在操作系统端丢弃了数据包。当epoll可用时-也就是说，当Data Collector 在64位Linux的最新版本上运行时-增加接收器线程数可以增加传递到源的数据包的数量。

## 配置UDP多线程源

配置UDP多线程源起源以使用多个工作线程来处理来自一个或多个UDP端口的消息。

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
   | 接收线程数 [![img](imgs/icon_moreInfo-20200310174751388.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/UDPMulti.html#concept_qtl_jzg_5bb) | 每个端口要使用的接收器线程数。例如，如果您在每个端口上配置两个线程，并将原始服务器配置为使用三个端口，则原始服务器总共使用六个线程。当epoll在Data Collector计算机上可用时，用于增加将数据传递到源的线程数。默认值为1。 |
   | 资料格式                                                     | UDP传递的数据格式：已收集网络流系统日志原始/分离数据         |
   | 最大批处理大小（消息）                                       | 批量包含并一次通过管道的最大消息数。接受的值最高为 Data Collector的最大批处理大小。默认值是1000 数据采集器默认设置为1000。 |
   | [批处理等待时间（毫秒）](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ypd_vgr_5q) | 发送部分或空批次之前要等待的毫秒数。                         |
   | 数据包队列大小 [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ypd_vgr_5q) | 要保留在数据包队列中以进行处理的最大数据包数。               |
   | 工作线程数 [![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/icon_moreInfo.png)](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Origins/Origins_overview.html#concept_ypd_vgr_5q) | 原点用来执行管道处理的线程数。                               |

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