# 第二章 MQTT控制报文格式 MQTT Control Packet format

## 目录

- [第一章 - 介绍](01-Introduction.md)
- [第二章 – MQTT控制报文格式](02-ControlPacketFormat.md)
- [第三章 – MQTT控制报文](03-ControlPackets.md)
- [第四章 – 操作行为](04-OperationalBehavior.md)
- [第五章 – 安全](05-Security.md)
- [第六章 – 使用WebSocket](06-WebSocket.md)
- [第七章 – 一致性目标](07-Conformance.md)
- [附录B - 强制性规范声明](08-AppendixB.md)

## 2.1 MQTT控制报文的结构 Structure of an MQTT Control Packet

MQTT协议通过交换预定义的MQTT控制报文来通信。这一节描述这些报文的格式。

MQTT控制报文由三部分组成，按照 [图例 2.1 –MQTT控制报文的结构](#_Figure_2.1_-) 描述的顺序：

##### 图例 2.1 –MQTT控制报文的结构

| Fixed header    | 固定报头，所有控制报文都包含 |
|-----------------|------------------------------|
| Variable header | 可变报头，部分控制报文包含   |
| Payload         | 有效载荷，部分控制报文包含   |

### 2.1.1 固定报头 Fixed header

每个MQTT控制报文都包含一个固定报头。[图例 2.2 -固定报头的格式](#_Figure_2.2_-) 描述了固定报头的格式。

##### 图例 2.2 -固定报头的格式

<table style="text-align:center">
  <tr>
    <td align="center"><strong>Bit</strong></td>
    <td align="center"><strong>7</strong></td>
    <td align="center"><strong>6</strong></td>
    <td align="center"><strong>5</strong></td>
    <td align="center"><strong>4</strong></td>
    <td align="center"><strong>3</strong></td>
    <td align="center"><strong>2</strong></td>
    <td align="center"><strong>1</strong></td>
    <td align="center"><strong>0</strong></td>
  </tr>
  <tr>
    <td>byte 1</td>
    <td colspan="4" align="center">MQTT控制报文的类型</td>
    <td colspan="4" align="center">用于指定控制报文类型的标志位</td>
  </tr>
  <tr>
    <td>byte 2...</td>
    <td colspan="8" align="center">剩余长度</td>
  </tr>
</table>

### 2.1.2 MQTT控制报文的类型 MQTT Control Packet type

**位置：**第1个字节，二进制位7-4。

表示为4位无符号值，这些值的定义见 [表格 2.1 -控制报文的类型](#_Table_2.1_-)

##### 表格 2.1 -控制报文的类型 

| **名字**    | **值** | **报文流动方向** | **描述**                            |
|-------------|--------|------------------|-------------------------------------|
| Reserved    |      0 | 禁止             | 保留                                |
| CONNECT     |      1 | 客户端到服务端   | 客户端请求连接服务端                |
| CONNACK     |      2 | 服务端到客户端   | 连接报文确认                        |
| PUBLISH     |      3 | 两个方向都允许   | 发布消息                            |
| PUBACK      |      4 | 两个方向都允许   | QoS 1消息发布收到确认               |
| PUBREC      |      5 | 两个方向都允许   | 发布收到（保证交付第一步）          |
| PUBREL      |      6 | 两个方向都允许   | 发布释放（保证交付第二步）          |
| PUBCOMP     |      7 | 两个方向都允许   | QoS 2消息发布完成（保证交互第三步） |
| SUBSCRIBE   |      8 | 客户端到服务端   | 客户端订阅请求                      |
| SUBACK      |      9 | 服务端到客户端   | 订阅请求报文确认                    |
| UNSUBSCRIBE |     10 | 客户端到服务端   | 客户端取消订阅请求                  |
| UNSUBACK    |     11 | 服务端到客户端   | 取消订阅报文确认                    |
| PINGREQ     |     12 | 客户端到服务端   | 心跳请求                            |
| PINGRESP    |     13 | 服务端到客户端   | 心跳响应                            |
| DISCONNECT  |     14 | 客户端到服务端   | 客户端断开连接                      |
| Auth        |     15 | 两个方向都允许   | 认证交换                            |

### 2.1.3 标志 Flags

固定报头第1个字节的剩余的4位 \[3-0\]包含每个MQTT控制报文类型特定的标志，见 [表格 2.2 -标志位](#表格-2.2--标志位)。<mark>表格 2.2中任何标记为“保留”的标志位，都是保留给以后使用的，**必须**设置为表格中列出的值</mark> \[MQTT-2.2.2-1\]。如果收到非法的标志，接收者**必须**关闭网络连接。有关错误处理的详细信息见 4.8节 \[MQTT-2.2.2-2\]。

#####  表格 2.2 - 标志位 Flag Bits

| **控制报文** | **固定报头标志**   | **Bit 3**       | **Bit 2**       | **Bit 1**       | **Bit 0**          |
|--------------|--------------------|-----------------|-----------------|-----------------|--------------------|
| CONNECT      | Reserved           | 0               | 0               | 0               | 0                  |
| CONNACK      | Reserved           | 0               | 0               | 0               | 0                  |
| PUBLISH      | Used in MQTT 3.1.1 | DUP<sup>1</sup> | QoS<sup>2</sup> | QoS<sup>2</sup> | RETAIN<sup>3</sup> |
| PUBACK       | Reserved           | 0               | 0               | 0               | 0                  |
| PUBREC       | Reserved           | 0               | 0               | 0               | 0                  |
| PUBREL       | Reserved           | 0               | 0               | 1               | 0                  |
| PUBCOMP      | Reserved           | 0               | 0               | 0               | 0                  |
| SUBSCRIBE    | Reserved           | 0               | 0               | 1               | 0                  |
| SUBACK       | Reserved           | 0               | 0               | 0               | 0                  |
| UNSUBSCRIBE  | Reserved           | 0               | 0               | 1               | 0                  |
| UNSUBACK     | Reserved           | 0               | 0               | 0               | 0                  |
| PINGREQ      | Reserved           | 0               | 0               | 0               | 0                  |
| PINGRESP     | Reserved           | 0               | 0               | 0               | 0                  |
| DISCONNECT   | Reserved           | 0               | 0               | 0               | 0                  |

- DUP<sup>1</sup> =控制报文的重复分发标志
- QoS<sup>2</sup> = PUBLISH报文的服务质量等级
- RETAIN<sup>3</sup> = PUBLISH报文的保留标志

PUBLISH控制报文中的DUP, QoS和RETAIN标志的描述见 [3.3.1节](#anchor-3.1.1)。

### 2.1.4 剩余长度 Remaining Length
剩余长度是一个用来表示当前控制包剩余字节数量的可变字节整形，包含可变报头和有效载荷。剩余长度不包括用于编码剩余长度字段本身的字节数。包的大小是单个 MQTT 控制包的字节的总数量，相当于固定报头加上剩余长度的总长度。

## 2.2 可变报头 Variable header

某些MQTT控制报文包含一个可变报头部分。它在固定报头和负载之间。可变报头的内容根据报文类型的不同而不同。可变报头的报文标识符（Packet Identifier）字段存在于在多个类型的报文里。

### 2.2.1 报文标识符 Packet Identifier

很多控制报文的可变报头部分包含一个两字节的报文标识符字段。这些报文是PUBLISH（QoS > 0时）， PUBACK，PUBREC，PUBREL，PUBCOMP，SUBSCRIBE, SUBACK，UNSUBSCIBE，UNSUBACK。

需要报文标识符的控制报文在 [表格 2.5 -包含报文标识符的控制报文](#_Table_2.5_-) 中列出。

##### 表格 2.5 -包含报文标识符的控制报文 Control Packets that contain a Packet Identifier

| **控制报文** | **报文标识符字段**  |
|--------------|---------------------|
| CONNECT      | 不需要              |
| CONNACK      | 不需要              |
| PUBLISH      | 需要（如果QoS > 0） |
| PUBACK       | 需要                |
| PUBREC       | 需要                |
| PUBREL       | 需要                |
| PUBCOMP      | 需要                |
| SUBSCRIBE    | 需要                |
| SUBACK       | 需要                |
| UNSUBSCRIBE  | 需要                |
| UNSUBACK     | 需要                |
| PINGREQ      | 不需要              |
| PINGRESP     | 不需要              |
| DISCONNECT   | 不需要              |
| AUTH         | 不需要              |

<mark>QoS等于0的PUBLISH报文**不能**包含报文标识符</mark> \[MQTT-2.2.1-2\]。

<mark>每次客户端发送新的 SUBSCRIBE, UNSUBSCRIBE 或 PUBLISH (如果 QoS > 0) MQTT 控制包的时候，它都必须给当前未使用的包分配一个非 0 的报文标识符</mark>\[MQTT-2.2.1-3\]。

<mark>每次服务端发送新的 PUBLISH (如果 QoS > 0) MQTT 控制包的时候，它都必须给当前未使用的包分配一个非 0 的报文标识符</mark>\[MQTT-2.2.1-4\]。

在发送方处理相应的确认包（如下定义）之后，报文标识符可以重用。 在 QoS 1 PUBLISH 包的情况下，确认包为相应的 PUBACK; 在QoS 2 PUBLISH 包的情况下，确认包是 PUBCOMP 或 PUBREC，原因码为 128 或更高。 对于 SUBSCRIBE 或 UNSUBSCRIBE ，确认包为相应的 SUBACK 或UNSUBACK。

与 PUBLISH，SUBSCRIBE 和 UNSUBSCRIBE 数据包一起使用的数据包标识符分别为会话中的客户端和服务器构成单个统一的标识符集。无论何时，报文标识符都不能被多个命令使用。

<mark>PUBACK, PUBREC, PUBREL报文**必须**包含与最初发送的PUBLISH报文相同的报文标识符</mark> \[MQTT-2.2.1-5\]。<mark>类似地，SUBACK和UNSUBACK**必须**包含在对应的SUBSCRIBE和UNSUBSCRIBE报文中使用的报文标识符</mark> \[MQTT-2.2.1-6\]。

客户端和服务端彼此独立地分配报文标识符。因此，客户端服务端组合使用相同的报文标识符可以实现并发的消息交换。

> **非规范评注**

> 客户端发送标识符为 0x1234 的 PUBLISH 报文，它有可能会在收到那个报文的 PUBACK 之前，先收到服务端发送的另一个不同的但是报文标识符也为 0x1234 的 PUBLISH 报文。

| Client                           |      | Server                           |
|----------------------------------|------|----------------------------------|
| PUBLISH Packet Identifier=0x1234 | ---> |                                  |
|                                  | <--- | PUBLISH Packet Identifier=0x1234 |
| PUBACK Packet Identifier=0x1234  | ---> |                                  |
|                                  | <--- | PUBLISH Packet Identifier=0x1234 |

### 2.2.2 属性
CONNECT, CONNACK, PUBLISH, PUBACK, PUBREC, PUBREL, PUBCOMP, SUBSCRIBE, SUBACK, UNSUBACK, DISCONNECT 以及 AUTH 报文的可变报头的最后一个字段是一套属性的集合。在 CONNECT 包中的有效载荷的遗嘱属性字段中也有一套可选的属性。

属性集合由属性长度和属性组成。

#### 2.2.2.1 属性长度
属性长度由可变字节整形编码。属性长度不包含用于编码它自身的字节，但是包含其它属性的长度。如果没有其它属性，属性长度**必须**设为 0。

#### 2.2.2.2 属性
属性由标识符构成，标识符定义了属性的使用，数据类型以及属性的值。标识符由可变字节整形编码。倘若控制报文包含对其数据报文类型无效的标识符或包含非指定数据类型的值的控制报文，则该控制报文是畸形报文。如果接收到这样的报文，则使用带有原因码 0x81（畸形报文）的 CONNACK 报文或 DISCONNECT 报文去按照 [4.13 节](#anchor-4.13) 所描述的那样处理错误。带有不同标识符的属性的顺序没有意义。

##### 表格 2.4 - 属性
<table class="MsoTableGrid" border="1" cellspacing="0" cellpadding="0" width="685" style="width:513.9pt;border-collapse:collapse;border:none">
 <tbody><tr style="page-break-inside:avoid">
  <td width="95" colspan="2" valign="top" style="width:71.55pt;border:solid windowtext 1.0pt;
  background:white;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><b>标识符 Identifier</b></p>
  </td>
  <td width="188" rowspan="2" valign="top" style="width:141.3pt;border:solid windowtext 1.0pt;
  border-left:none;background:white;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><b>名称（用法）Name(usage)</b></p>
  </td>
  <td width="150" rowspan="2" valign="top" style="width:112.6pt;border:solid windowtext 1.0pt;
  border-left:none;background:white;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><b>类型 Type</b></p>
  </td>
  <td width="251" rowspan="2" valign="top" style="width:188.45pt;border:solid windowtext 1.0pt;
  border-left:none;background:white;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><b>报文/遗嘱属性 Packet/Will Properties</b></p>
  <p class="MsoNormal"><b>&nbsp;</b></p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;background:white;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><b>十进制 Dec</b></p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  background:white;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><b>十六进制 Hex</b></p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">1</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x01</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">有效载荷格式指示器 Payload Format Indicator</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">字节</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH, 遗嘱属性 Will Properties</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">2</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x02</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">消息到期间隔 Message Exipiry Interval</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">四字节整形</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH，遗嘱属性</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">3</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x03</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">内容类型 Content Type</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 编码字符串</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH，遗嘱属性</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">8</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x08</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">响应主题 Response Topic</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 编码字符串</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH，遗嘱属性</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">9</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x09</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">关联数据 Correlation Data</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">二进制数据 Binary Data</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH，遗嘱属性</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">11</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x0B</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">订阅标识符 Subscription Identifier</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">可变字节整形</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH, SUBSCRIBE</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">17</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x11</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">会话到期间隔 Session Expiry Interval</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">四字节整形</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT,  CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">18</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x12</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify"> 已分配客户端标识符 Assigned Client Identifier</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 编码字符串</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">19</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x13</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">服务器保活 Server Keep Alive</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">两字节整形</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">21</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x15</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">认证方法 Authentication Method</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 编码字符串</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT, CONNACK, AUTH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">22</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x16</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">认证数据 Authentication Data</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">二进制数据</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT, CONNACK, AUTH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">23</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x17</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">请求问题信息 Request Problem Information</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">字节</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">24</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x18</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">遗嘱延迟间隔 Will Delay Interval</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">四字节整形</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">遗嘱属性 Will Properties</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">25</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x19</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">请求响应信息 Request Response Information</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">字节</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">26</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x1A</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">响应信息 Response Information</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 编码字符串</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">28</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x1C</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">服务器引用 Server Reference</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 编码字符串</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">31</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x1F</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" style="text-align:justify">原因字符串 Reason String</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 编码字符串</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, PUBACK, PUBREC, PUBREL, PUBCOMP, SUBACK,
  UNSUBACK, DISCONNECT, AUTH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">33</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x21</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">接收最大值 Receive Maximum</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">两字节整形</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT, CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">34</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x22</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">主题别名最大值 Topic Alias Maximum</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">两字节整形</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT, CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">35</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x23</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">主题别名 Topic Alias</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">两字节整形</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBLISH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">36</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x24</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">服务质量最大值 Maximum QoS</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">字节</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">37</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x25</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">保留可用 Retain Available</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">字节</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">38</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x26</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">用户属性 User Property</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UTF-8 字符串对 UTF-8 String Pair</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT, CONNACK, PUBLISH, PUBACK, PUBREC, PUBREL,
  PUBCOMP, SUBACK, UNSUBACK, DISCONNECT, AUTH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">39</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x27</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">最大报文大小 Maximum Packet Size </p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">四字节整形</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNECT, CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">40</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x28</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">可用通配符订阅 Wildcard Subscription Available</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">字节</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">41</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x29</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">可用订阅标识符 Subscription Identifier Available</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">Byte</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="45" valign="top" style="width:33.5pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">42</p>
  </td>
  <td width="51" valign="top" style="width:38.05pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x2A</p>
  </td>
  <td width="188" valign="top" style="width:141.3pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">可用共享订阅 Shared Subscription Available</p>
  </td>
  <td width="150" valign="top" style="width:112.6pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">字节</p>
  </td>
  <td width="251" valign="top" style="width:188.45pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
</tbody></table>

> **非规范评注**

> 虽然属性标识符以可变字节整形的形式定义，但是在本版本协议中，所有的属性标识符都是单字节长度。

##  2.3 有效载荷 Payload

某些MQTT控制报文在报文的最后部分包含一个有效载荷，这将在第三章论述。对于PUBLISH来说有效载荷就是应用消息。

##### 表格 2.6 – 包含有效载荷的控制报文 Control Packets that contain a Payload

| **控制报文** | **有效载荷** |
|--------------|--------------|
| CONNECT      | 需要         |
| CONNACK      | 不需要       |
| PUBLISH      | 可选         |
| PUBACK       | 不需要       |
| PUBREC       | 不需要       |
| PUBREL       | 不需要       |
| PUBCOMP      | 不需要       |
| SUBSCRIBE    | 需要         |
| SUBACK       | 需要         |
| UNSUBSCRIBE  | 需要         |
| UNSUBACK     | 不需要       |
| PINGREQ      | 不需要       |
| PINGRESP     | 不需要       |
| DISCONNECT   | 不需要       |

## 2.4 原因码 Reason Code

原因码是用来表明操作结果的一个单字节无符号值，小于 0x80 的原因码表明操作的结果是成功的，正常的成功操作返回的原因码值为 0。 如果返回的原因码大于等于 0x80，就说明操作失败了。

CONNACK, PUBACK, PUBREC, PUBREL, PUBCOMP, DISCONNECT 和 AUTH 控制包有单个原因码作为一部分在可变报头中。而 SUBACK 和 UNSUBACK 包在 Payload 中包含了一张一个或多个原因码的列表。

以下的 Reason Codes 一览列表:

<table class="MsoNormalTable" border="1" cellspacing="0" cellpadding="0" width="673" style="width:504.9pt;border-collapse:collapse;border:none">
 <tbody><tr style="page-break-inside:avoid">
  <td width="113" colspan="2" valign="top" style="width:84.5pt;border:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>原因码</b></p>
  </td>
  <td width="211" rowspan="2" valign="top" style="width:158.5pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>名称</b></p>
  </td>
  <td width="349" rowspan="2" valign="top" style="width:261.9pt;border:solid windowtext 1.0pt;
  border-left:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>报文</b></p>
  <p class="MsoNormal" align="center" style="text-align:center"><b>&nbsp;</b></p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>十进制</b></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal" align="center" style="text-align:center"><b>十六进制</b></p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x00</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">成功 Success</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, PUBACK, PUBREC, PUBREL, PUBCOMP, UNSUBACK, AUTH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x00</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">正常断连 Normal disconnection</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x00</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">准许 QoS 0  Granted QoS 0</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">1</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x01</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"> 准许 QoS 1 Granted QoS 1</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">2</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x02</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"> 准许 QoS 2  Granted QoS 2</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">4</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x04</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">以遗嘱消息断开连接 Disconnect with Will Message</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">16</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x10</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"> 没有匹配的订阅者 No matching subscribers</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBACK, PUBREC</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">17</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x11</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">没有订阅 No subscription existed</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">UNSUBACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">24</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x18</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">继续认证 Continue authentication</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">AUTH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">25</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x19</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">重新认证 Re-authenticate</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">AUTH</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">128</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x80</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">未指定错误 Unspecified error</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, PUBACK, PUBREC, SUBACK, UNSUBACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">129</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x81</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">畸形报文 Malformed Packet</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">130</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x82</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">协议错误 Protocol Error</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">131</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x83</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">实现特有错误 Implementation specific error</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, PUBACK, PUBREC, SUBACK, UNSUBACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">132</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x84</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">不支持的协议版本 Unsupported Protocol Version</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">133</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x85</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">客户端标识符无效 Client Identifier not valid</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">134</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x86</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">错误的用户名和密码 Bad User Name or Password</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">135</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x87</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">未授权 Not authorized</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"> CONNACK, PUBACK, PUBREC, SUBACK, UNSUBACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">136</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x88</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">服务器不可用 Server unavailable</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">137</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x89</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">服务器繁忙 Server busy</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">138</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x8A</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">禁止访问 Banned</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">139</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x8B</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">服务器关机中 Server shutting down</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">140</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x8C</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">错误验证方法 Bad authentication method</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">141</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x8D</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">保活超时 Keep Alive timeout</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">142</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x8E</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">会话被接管 Session taken over</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">143</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x8F</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">主题过滤器无效 Topic Filter invalid</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK, UNSUBACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">144</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x90</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">主题名无效 Topic Name invalid</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, PUBACK, PUBREC, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">145</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x91</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">报文标识符在使用中 Packet Identifier in use</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBACK, PUBREC, SUBACK, UNSUBACK</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">146</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x92</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">没有发现报文标识符 Packet Identifier not found</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBREL, PUBCOMP</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">147</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x93</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">超出接收最大值 Receive Maximum exceeded</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">148</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x94</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">主题别名无效 Topic Alias invalid</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">149</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x95</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">报文太大 Packet too large</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">150</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x96</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">消息传输速率太高 Message rate too high</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">151</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x97</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"> 超出限额 Quota exceeded</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, PUBACK, PUBREC, SUBACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">152</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x98</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">管理行为 Administrative action</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">153</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x99</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">有效载荷格式无效 Payload format invalid</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">PUBACK, PUBREC, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">154</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x9A</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">不支持消息保留 Retain not supported</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">155</p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x9B</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">不支持的QoS QoS not supported</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span style="font-size:10.5pt;color:#333333">156</span></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x9C</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">使用另一台服务器 Use another server</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span style="font-size:10.5pt;color:#333333">157</span></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x9D</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">服务器被移除 Server moved</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span style="font-size:10.5pt;color:#333333">158</span></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x9E</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">不支持的共享订阅 Shared Subscription not supported</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span style="font-size:10.5pt;color:#333333">159</span></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0x9F</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">超出连接速率 Connection rate exceeded</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">CONNACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span style="font-size:10.5pt;color:#333333">160</span></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0xA0</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">最大连接时间 Maximum connect time</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span style="font-size:10.5pt;color:#333333">161</span></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0xA1</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">不支持的订阅标识符 Subscription Identifiers not supported</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK, DISCONNECT</p>
  </td>
 </tr>
 <tr style="page-break-inside:avoid">
  <td width="66" valign="top" style="width:49.2pt;border:solid windowtext 1.0pt;
  border-top:none;padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal"><span style="font-size:10.5pt;color:#333333">162</span></p>
  </td>
  <td width="47" valign="top" style="width:35.3pt;border-top:none;border-left:none;
  border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">0xA2</p>
  </td>
  <td width="211" valign="top" style="width:158.5pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">不支持的通配符订阅 Wildcard Subscription not supported</p>
  </td>
  <td width="349" valign="top" style="width:261.9pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  padding:0in 5.4pt 0in 5.4pt">
  <p class="MsoNormal">SUBACK, DISCONNECT</p>
  </td>
 </tr>
</tbody></table>

> 非正式评注

> 若原因码为 0x91 (报文标识符在使用中)，对此的响应是尝试修复的状态，或者通过将 Clean Start 设置为 1 来重置会话状态，或者确定客户端或服务器实现是否有缺陷。
