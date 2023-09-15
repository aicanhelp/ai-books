# 数据报数据格式

读取数据报数据时，起源会为每条消息生成一条记录。

源可以处理[收集的](https://collectd.org/)消息，NetFlow 5和NetFlow 9消息以及以下类型的syslog消息：

- [RFC 5424](https://tools.ietf.org/html/rfc5424)
- [RFC 3164](https://tools.ietf.org/html/rfc3164)
- 非标准通用消息，例如RFC 3339日期，没有版本数字

在处理NetFlow消息时，该阶段会根据NetFlow版本生成不同的记录。处理NetFlow 9时，将基于NetFlow 9配置属性生成记录。有关更多信息，请参见[NetFlow数据处理](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Data_Formats/NetFlow_Overview.html#concept_thl_nnr_hbb)。

有关处理数据报数据的来源列表，请参见[Origins](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Apx-DataFormats/DataFormat_Title.html#concept_kgd_11c_kv)。