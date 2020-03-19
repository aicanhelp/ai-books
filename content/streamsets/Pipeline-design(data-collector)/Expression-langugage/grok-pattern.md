# 格罗模式

## 定义Grok模式

您可以使用本附录中的grok模式来定义日志数据的结构。

您可以使用单个模式或组合多个模式来定义更大的模式，或创建自定义模式。

在Data Collector 阶段中定义grok模式时，将配置以下属性：

- Grok模式定义

  用于定义复杂或自定义的grok模式。您可以使用此属性为单个grok模式定义一个模式，或为在较大模式中使用而定义多个模式。

  配置模式定义时，请说明模式名称，然后说明模式说明，如下所示：`  `

  下面的示例定义了几种模式，MYHOSTTIMESTAMP，MYCUSTOMPATTERN（在MYHOSTTIMESTAMP上扩展）和DURATIONLOG：`MYHOSTTIMESTAMP %{CISCOTIMESTAMP:timestamp} %{HOST:host} MYCUSTOMPATTERN %{MYHOSTTIMESTAMP} %{WORD:program}%{NOTSPACE} %{NOTSPACE} DURATIONLOG %{NUMBER:duration}%{NOTSPACE} %{GREEDYDATA:kernel_logs}`

- 格罗模式

  定义用于评估数据的实际grok模式。您可以输入预定义的grok模式，例如％{COMMONAPACHELOG}。或者，要定义自定义的grok模式，可以使用本附录中列出的模式或在Grok模式描述中定义的模式。

  例如，在“ Grok模式描述”字段中定义了上面的模式后，可以使用以下模式来配置“ Grok模式”：`%{MYCUSTOMPATTERN} %{DURATIONLOG}`

下图显示了UI中的配置示例：

![img](https://streamsets.com/documentation/controlhub/latest/help/datacollector/UserGuide/Graphics/GrokProperties.png)

## 常规Grok模式

您可以使用以下常规grok模式来定义日志数据的结构：

- 用户

  ％{用户名}

- 用户名

  [a-zA-Z0-9 ._-] +

- BASE10NUM

  （？<！[0-9。+-]）（？> [+-]？（？：（？：[0-9] +（？：\。[0-9] +）？）|（？ ：\。[0-9] +）））

- BASE16FLOAT

  \ b（？<！[0-9A-Fa-f。]）（？：[+-]？（?: 0x）？（？：（？：[0-9A-Fa-f] +（?: \。[0-9A-Fa-f] *）？）|（？：\。[0-9A-Fa-f] +）））\ b

- INT

  （？：[+-]？（？：[0-9] +））

- 诺金

  \ b（？：[0-9] +）\ b

- 数

  （？：％{BASE10NUM}）BASE16NUM（？<！[0-9A-Fa-f]）（？：[+-]？（?: 0x）？（？：[0-9A-Fa-f] + ））

- 正点

  \ b（？：[1-9] [0-9] *）\ b

- 字

  \ b \ w + \ b

- 非空格

  \ S +

- 空间

  \ s *

- 数据

  。*？

- 贪婪数据

  。*

- 限定字符串

  （？>（？<！\\）（？>“（？> \\ .. [[^ \\”] +）+“ |”“ |（？>'（？> \\。| [^ \\ '] +）+'）|''|（？>`（？> \\。| [^ \\`] +）+`）|``））

- UUID

  [A-Fa-f0-9] {8}-（？：[A-Fa-f0-9] {4}-）{3} [A-Fa-f0-9] {12}

## 日期和时间Grok模式

您可以使用以下日期和时间grok模式来定义日志数据的结构：

- 月

  \ b（？：Jan（？：uary）？| Feb（？：ruary）？| Mar（？：ch）？| Apr（？：il）？| May | Jun（？：e）？| Jul（？ ：y）？| Aug（？：ust）？| Sep（？：tember）？| Oct（？：ober）？| Nov（？：ember）？| Dec（？：ember）？）\ b

- MONTHNUM

  （？：0？[1-9] | 1 [0-2]）

- MONTHNUM2

  （？：0 [1-9] | 1 [0-2]）

- 月日

  （？:( ?: 0 [1-9]）|（？：[12] [0-9]）|（？：3 [01]）| [1-9]）

- 天

  （？：Mon（？：day）？| Tue（？：sday）？| Wed（？：nesday）？| Thu（？：rsday）？| Fri（？：day）？| Sat（？：urday）？ | Sun（？：day）？）

- 年

  （？> \ d \ d）{1,2}

- 小时

  （？：2 [0123] | [01]？[0-9]）

- 分钟

  （？：[0-5] [0-9]）

- 第二

  （？：（？：[0-5]？[0-9] | 60）（？：[：。，] [0-9] +）？）TIME（？！<[0-9]）％{ HOUR}：％{MINUTE}（？::％{SECOND}）（？！[0-9]）**注意：** 在大多数时间标准中，60是a秒。

- DATE_US

  ％{MONTHNUM} [/-]％{MONTHDAY} [/-]％{YEAR}

- DATE_EU

  ％{MONTHDAY} [./-]％{MONTHNUM} [./-]％{YEAR}

- ISO8601_TIMEZONE

  （？：Z | [+-]％{HOUR}（？::？％{MINUTE}））

- ISO8601_SECOND

  （？：％{SECOND} | 60）

- TIMESTAMP_ISO8601

  ％{YEAR}-％{MONTHNUM}-％{MONTHDAY} [T]％{HOUR}：？％{MINUTE}（？::？％{SECOND}）？％{ISO8601_TIMEZONE}？

- 日期

  ％{DATE_US} |％{DATE_EU}

- 日期戳

  ％{约会时间}

- TZ

  （？：[PMCE] [SD] T | UTC）

- DATESTAMP_RFC822

  ％{DAY}％{MONTH}％{MONTHDAY}％{YEAR}％{TIME}％{TZ}

- DATESTAMP_RFC2822

  ％{DAY}，％{MONTHDAY}％{MONTH}％{YEAR}％{TIME}％{ISO8601_TIMEZONE}

- DATESTAMP_OTHER

  ％{DAY}％{MONTH}％{MONTHDAY}％{TIME}％{TZ}％{YEAR}

- DATESTAMP_EVENTLOG

  ％{YEAR}％{MONTHNUM2}％{MONTHDAY}％{HOUR}％{MINUTE}％{SECOND}



## Java Grok模式

您可以使用以下与Java相关的grok模式来定义日志数据的结构：

- JAVACLASS

  （？：[a-zA-Z $ _] [a-zA-Z $ _0-9] * \。）* [a-zA-Z $ _] [a-zA-Z $ _0-9] *

  

- JAVAFILE

  （？：[A-Za-z0-9_。-] +）

  允许使用空格字符来匹配特殊情况，例如本机方法或未知源。

- 茉莉

  （？：（<init>）| [a-zA-Z $ _] [a-zA-Z $ _0-9] *）

- JAVASTACKTRACEPART

  ％{SPACE} at％{JAVACLASS：class} \。％{JAVAMETHOD：method} \（％{JAVAFILE：file}（？::％{NUMBER：line}）？\）

  在特殊情况下，例如本机方法或未知源，行号是可选的。

## 对数格罗模式

您可以使用以下与日志相关的grok模式来定义日志数据的结构：

- 系统日志时间戳

  ％{MONTH} +％{MONTHDAY}％{TIME}节目（？：[\ w ._ /％-] +）

- SYSLOGPROG

  ％{PROG：program}（？：\ [％{POSINT：pid} \]）？

- 系统日志主机

  ％{IPORHOST}

- 系统日志功能

  <％{NONNEGINT：facility}。％{NONNEGINT：priority}>

- 系统日志

  ％{SYSLOGTIMESTAMP：timestamp}（？：％{SYSLOGFACILITY}）？％{SYSLOGHOST：logsource}％{SYSLOGPROG}：

- HTTPDATE

  ％{MONTHDAY} /％{MONTH} /％{YEAR}：％{TIME}％{INT}

- 质量体系

  ％{QUOTEDSTRING}

- 公共记录日志

  ％{IPORHOST：clientip}％{USER：ident}％{USER：auth} \ [％{HTTPDATE：timestamp} \]“（（？：％{WORD：verb}％{NOTSPACE：request}（?: HTTP /％ {NUMBER：httpversion}）？|％{DATA：rawrequest}）“％{NUMBER：response}（？：％{NUMBER：bytes} |-）

- COMBINEDAPACHELOG

  ％{COMMONAPACHELOG}％{QS：referrer}％{QS：agent}

- 逻辑水平

  （[Aa] lert | ALERT | [Tt]种族| TRACE | [Dd] ebug | DEBUG | [Nn] otice | NOTICE | [Ii] nfo | INFO | [Ww] arn？（?: ing）？| WARN？ （？：ING）？| [Ee] rr？（?: or）？| ERR？（?: OR）？| [Cc] rit？（?: ical）？| CRIT？（?: ICAL）？| [ Ff] atal | FATAL | [Ss] evere | SeverE | EMERG（？：ENCY）？| [Ee] merg（？：ency）？）

## 网络Grok模式

您可以使用以下与网络相关的grok模式来定义日志数据的结构：

- 苹果电脑

  （？：％{CISCOMAC} |％{WINDOWSMAC} |％{COMMONMAC}）

- CISCOMAC

  （？：（？：[A-Fa-f0-9] {4} \。）{2} [A-Fa-f0-9] {4}）

- 通用MAC

  （？：（？：[A-Fa-f0-9] {2}：）{5} [A-Fa-f0-9] {2}）

- WINDOWSMAC

  （？：（？：[A-Fa-f0-9] {2}-）{5} [A-Fa-f0-9] {2}）

- 主办

  ％{主机名}

- 主机名

  \ b（？：[0-9A-Za-z] [0-9A-Za-z-] {0,62}）（？：\。（？：[0-9A-Za-z] [0- 9A-Za-z-] {0,62}））*（\。？| \ b）

- 港口

  ％{IPORHOST}：％{POSINT}

- IPORHOST

  （？：％{HOSTNAME} |％{IP}）

- 知识产权

  （？：％{IPV6} |％{IPV4}）

- IPV6

  （（（25 [0-5] | 2 [0-4] \ d | 1 \ d \ d | [1-9]？\ d）（\。（25 [0-5] | 2 [0-4] \ d | 1 \ d \ d | [1-9]？\ d））{3}））| :)）|（：（（（（：[0-9A-Fa-f] {1,4}） {1,7}）|（（:: [0-9A-Fa-f] {1,4}）{0,5}：（（25 [0-5] | 2 [0-4] \ d | 1 \ d \ d | [1-9]？\ d）（\。（25 [0-5] | 2 [0-4] \ d | 1 \ d \ d | [1-9]？\ d）） {3}））| ::）））（％。+）？IPV4（？<！[0-9]）（？:( ?: 25 [0-5] | 2 [0-4] [0-9] | [0-1]？[0-9] {1， 2}）[。]（?: 25 [0-5] | 2 [0-4] [0-9] | [0-1]？[0-9] {1,2}）[。]（？ ：25 [0-5] | 2 [0-4] [0-9] | [0-1]？[0-9] {1,2}）[。]（?: 25 [0-5] | 2 [0-4] [0-9] | [0-1]？[0-9] {1,2}））（（!! [0-9]）

## 路径格罗模式

您可以使用以下路径grok模式来定义日志数据的结构：

- 路径

  （？：％{UNIXPATH} |％{WINPATH}）

- UNIX路径

  （？> /（？> [\ w _％！$ @：。，〜-] + | \\。）*）+ TTY（？：/ dev /（pts | tty（[pq]）？）（\ w + ）？/？（？：[0-9] +））

- WINPATH

  （？> [A-Za-z] +：| \\）（？：\\ [^ \\？*] *）+ URIPROTO [A-Za-z] +（\ + [A-Za-z +] +）？

- URIHOST

  ％{IPORHOST}（？::％{POSINT：port}）？

- URI路径

  （？：/ [A-Za-z0-9 $。+！*'（）{}，〜：; = @＃％_ \-] *）+ #URIPARAM \？（？：[A-Za-z0 -9] +（？：=（？：[^＆] *））？（？：＆（？：[A-Za-z0-9] +（？：=（？：[^＆] *）） ？）？）*）？

- URIPARAM

  \？[A-Za-z0-9 $。+！*'|（）{}，〜@＃％＆/ =：; _？\-\ [\]] *

- URIPATHPARAM

  ％{URIPATH}（？：％{URIPARAM}）？

- URI

  ％{URIPROTO}：//（？：％{USER}（？:: [^ @] *）？@）？（？：％{URIHOST}）？（？：％{URIPATHPARAM}）？