# 实时消息传输协议（RTMP）详解

# 概述

概念：RTMP协议从属于应用层，被设计用来在适合的传输协议（如TCP）上复用和打包多媒体传输流（如音频、视频和互动内容）。RTMP提供了一套全双工的可靠的多路复用消息服务，类似于TCP协议[RFC0793]，用来在一对结点之间并行传输带时间戳的音频流，视频流，数据流。通常情况下，不同类型的消息会被分配不同的优先级，当网络传输能力受限时，优先级用来控制消息在网络底层的排队顺序。

## RTMP块流

实时消息传递协议块流(RTMP块流)。RTMP块流作为一款高级多媒体流协议提供了流的多路复用和打包服务。RTMP块流被设计用来传输实时消息协议，它可以使用任何协议来发送消息流。每个消息都包含时间戳和有效类型标识。RTMP块流和RTMP适用于各种视听传播的应用程序，包括一对一的，和一对多的视频直播、点播服务、互动会议应用程序。

当使用一个可靠的传输协议如TCP[RFC0793]时，RTMP块流提供了一种可以在多个流中，基于时间戳的端到端交付所有消息的方法。RTMP块流不提供任何优先级或类似形式的控制，但可以使用更高级别的协议来提供这样的优先级。

RTMP块流不仅包含了自己的协议控制信息，同时也提供了一个更高级别的协议机制，用来嵌入用户控制信息。

### RMTP消息格式

RMTP消息被分割成多个块，用来在更高的协议中支持多路复用。在消息格式时，应该包含以下字段:

#### 时间戳

消息的时间戳。这个字段占用4字节。

#### 长度

消息的有效长度。如果消息头不能被忽略，它应该包括长度。这个字段在块头中占用3字节。

#### 类型ID

各种类型的协议控制消息的ID。这些消息使用RTMP块流协议和更高级别的协议来传输信息。所有其他类型的ID可以用在高级协议，这对于RTMP块流来说，是不透明的。事实上，RTMP块流中没有要求使用这些值作为类型；所有(无协议的)消息可能是相同的类型，或者应用程序使用这个字段来区分多个连接，而不是类型。这个字段在块头中占用1字节。

#### 消息流ID

消息流ID可以是任意值。当同一个块流被复用到不同的消息流中时，可以通过消息流ID来区分它们。另外，对于RTMP块流而言，这是一个不透明值。该字段占用4字节，使用小端序。

**握手**  RTMP连接从握手开始。它包含三个固定大小的块，不像其他的协议，是由头部大小可变的块组成的。  客户端（初始化连接的一端）和服务端发送同样的三个块。为了方便描述，客户端发送的三个块命名为C0，C1，C2；服务端发送的三个块命名为S0，S1，S2。

**握手序列**  客户端通过发送C0和C1消息来启动握手过程。客户端必须接收到S1消息，然后发送C2消息。客户端必须接收到S2消息，然后发送其他数据。

服务端必须接收到C0或者C1消息，然后发送S0和S1消息。服务端必须接收到C1消息，然后发送S2消息。服务端必须接收到C2消息，然后发送其他数据。

**C0和S0格式**  C0和S0包由一个字节组成，下面是C0/S0包内的字段:

```javascript
0 1 2 3 4 5 6 7 +-+-+-+-+-+-+-+-+ | version | +-+-+-+-+-+-+-+-+ C0 and S0 bits
```

#### 版本(8比特)

在C0包内，这个字段代表客户端请求的RTMP版本号。在S0包内，这个字段代表服务端选择的RTMP版本号。此文档使用的版本是3。版本0-2用在早期的产品中，现在已经被弃用；版本4-31被预留用于后续产品；版本32-255（为了区分RTMP协议和文本协议，文本协议通常以可打印字符开始）不允许使用。如果服务器无法识别客户端的版本号，应该回复版本3。客户端可以选择降低到版本3，或者中止握手过程。  C1和S1格式  C1和S1包长度为1536字节，包含以下字段:

```javascript
0 1 2 3 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ | time (4 bytes) | +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ | zero (4 bytes) | +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ | random bytes | +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ | random bytes | | (cont) | | .... | +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ C1 and S1 bits
```

#### 时间(4字节)

本字段包含一个时间戳，客户端应该使用此字段来标识所有流块的时刻。时间戳取值可以为零或其他任意值。为了同步多个块流，客户端可能希望多个块流使用相同的时间戳。

#### 零(4字节)

本字段必须为零。

#### 随机数据(1528字节)

本字段可以包含任意数据。由于握手的双方需要区分另一端，此字段填充的数据必须足够随机(以防止与其他握手端混淆)。不过没必要为此使用加密数据或动态数据。  C2和S2格式  C2和S2包长度为1536字节，作为C1和S1的回应，包含以下字段:

```javascript
0 1 2 3 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ | time (4 bytes) | +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ | time2 (4 bytes) | +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ | random echo | +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ | random echo | | (cont) | | .... | +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+ C2 and S2 bits
```

#### 时间(4字节)

本字段必须包含对端发送的时间戳。

#### 时间(4字节)

本字段必须包含时间戳，取值为接收对端发送过来的握手包的时刻。

#### 随机数据(1528字节)

本字段必须包含对端发送过来的随机数据。握手的双方可以使用时间1和时间2字段来估算网络连接的带宽和/或延迟，但是不一定有用。

# RTMP协议拆分

RTMP协议中基本的数据单元称为消息（Message）。当RTMP协议在互联网中传输数据的时候，消息会被拆分成更小的单元，称为消息块（Chunk）。

## 消息

消息是RTMP协议中基本的数据单元。不同种类的消息包含不同的Message Type ID，代表不同的功能。RTMP协议中一共规定了十多种消息类型，分别发挥着不同的作用。例如，Message Type ID在1-7的消息用于协议控制，这些消息一般是RTMP协议自身管理要使用的消息，用户一般情况下无需操作其中的数据。Message Type ID为8，9的消息分别用于传输音频和视频数据。Message Type ID为15-20的消息用于发送AMF编码的命令，负责用户与服务器之间的交互，比如播放，暂停等等。消息首部（Message Header）有四部分组成：标志消息类型的Message Type ID，标志消息长度的Payload Length，标识时间戳的Timestamp，标识消息所属媒体流的Stream ID。消息的报文结构如下图所示。

![img](https://ask.qcloudimg.com/http-save/yehe-1148531/p89wyynntj.jpeg?imageView2/2/w/1620)

## 消息块

在网络上传输数据时，消息需要被拆分成较小的数据块，才适合在相应的网络环境上传输。RTMP协议中规定，消息在网络上传输时被拆分成消息块（Chunk）。消息块首部（Chunk Header）有三部分组成：用于标识本块的Chunk Basic Header，用于标识本块负载所属消息的Chunk Message Header，以及当时间戳溢出时才出现的Extended Timestamp。消息块的报文结构如下图所示。

![img](https://ask.qcloudimg.com/http-save/yehe-1148531/w026je6qd2.jpeg?imageView2/2/w/1620)

## 消息分块

在消息被分割成几个消息块的过程中，消息负载部分（Message Body）被分割成大小固定的数据块（默认是128字节，最后一个数据块可以小于该固定长度），并在其首部加上消息块首部（Chunk Header），就组成了相应的消息块。消息分块过程如下图所示，一个大小为307字节的消息被分割成128字节的消息块（除了最后一个）。

RTMP传输媒体数据的过程中，发送端首先把媒体数据封装成消息，然后把消息分割成消息块，最后将分割后的消息块通过TCP协议发送出去。接收端在通过TCP协议收到数据后，首先把消息块重新组合成消息，然后通过对消息进行解封装处理就可以恢复出媒体数据。

![img](https://ask.qcloudimg.com/http-save/yehe-1148531/wjkwe5l59y.jpeg?imageView2/2/w/1620)

而在RTMP协议中，最重要的就是流的建立，涉及到的握手协议。

# RTMP协议握手

## 包结构组成

rtmp消息包使用的是二进制数据流,它们使用AMF0/AMF3进行编码.与其它协议一样,rtmp消息也是也包括消息头与消息体,而消息头又可以分为basic header,chunk header,timestamp.

basic header是此包的唯一不变的部分,并且由一个独立的byte构成,这其中包括了2个作重要的标志位,chunk type以及stream id.chunk type决定了消息头的编码格式,该字段的长度完全依赖于stream id,stream id是一个可变长的字段.

message header该字段包含了将要发送的消息的信息(或者是一部分,一个消息拆成多个chunk的情况下是一部分)该字段的长度由chunk basic header中的trunk type决定.

timestamp扩展时间戳就比较好理解的,就是当chunk message header的时间戳大于等于0xffffff的时候chunk message header后面的四个字节就代表扩展时间.

## 握手协议

在rtmp连接建立后,服务端与客户端需要通过3次交换报文完成握手.  握手其他的协议不同,是由三个静态大小的块,而不是可变大小的块组成的,客户端与服务器发送相同的三个chunk,客户端发送c0,c1,c2 chunk,服务端发送s0,s1,s2 chunk。

1. 握手开始时,客户端将发送c0,c1 chunk,此时客户端必须等待,直到收到s1 chunk,才能发送c2 chunk。
2. 此时服务端必须等待,直到已收到c0后才能发送s0和s1,当然也可能会等到接收c1后才发送。
3. 当服务器收到c2后才能再发送的其他数据,同理,当客户端收到s2后才能发送其它数据。

## 握手状态

**未初始化**:在这个阶段,协议版本被发送,客户和服务端都是未初始化的,客户端在包c1中发送协议版本,如果服务端支持这个版本,它将会发送s0和s1作为响应,如果不支持,则服务端会用相应的动作来响应,在RTMP中这个动作是结束这个连接。

**版本发送完成**:客户端和服务端在未初始化状态之后都进入到版本发送完成状态,客户端等待包s1,而服务端等待包c1,在收到相应的包后,客户端发送包c2,而服务端发磅包s2,状态变成询问发送完成。

**询问发送完成**:客户端和服务端等待s2和c2。

**握手完成**:客户端和服务端开始交换消息。