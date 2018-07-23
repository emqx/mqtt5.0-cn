## 3.1 CONNECT – 连接服务端

<mark>客户端到服务端的网络连接建立后，客户端发送给服务端的第一个报文**必须**是CONNECT报文</mark> \[MQTT-3.1.0-1\]。

在一个网络连接上，客户端只能发送一次CONNECT报文。<mark>服务端**必须**将客户端发送的第二个CONNECT报文当作协议违规处理并断开客户端的连接</mark> \[MQTT-3.1.0-2\]。有关错误处理的信息请查看4.8节。

有效载荷包含一个或多个编码的字段。包括客户端的唯一标识符，Will主题，Will消息，用户名和密码。除了客户端标识之外，其它的字段都是可选的，基于标志位来决定可变报头中是否需要包含这些字段。

### 3.1.1 CONNECT 固定报头 Fixed header

##### 图例 3.1 –CONNECT报文的固定报头

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
     <td colspan="4" align="center">MQTT报文类型 (1)</td>
     <td colspan="4" align="center">Reserved 保留位</td>
   </tr>
   <tr>
     <td></td>
      <td align="center">0</td>
      <td align="center">0</td>
      <td align="center">0</td>
      <td align="center">1</td>
      <td align="center">0</td>
      <td align="center">0</td>
      <td align="center">0</td>
      <td align="center">0</td>
   </tr>
   <tr>
     <td>byte 2...</td>
     <td colspan="8" align="center">剩余长度</td>
   </tr>
 </table>

**剩余长度字段**

剩余长度等于可变报头的长度（10字节）加上有效载荷的长度。编码方式见 2.2.3节的说明。

### 3.1.2 可变报头 Variable header

CONNECT报文的可变报头按下列次序包含四个字段：协议名（Protocol Name），协议级别（Protocol Level），连接标志（Connect Flags）和保持连接（Keep Alive）和属性。属性编码的规则在[secion 2.2.2](#anchor-2.2.2)中有详细描述。

#### 3.1.2.1 协议名 Protocol Name

##### 图例 3.2 -协议名字节构成

|        | **说明**     | **7** | **6** | **5** | **4** | **3** | **2** | **1** | **0** |
|--------|--------------|-------|-------|-------|-------|-------|-------|-------|-------|
| 协议名 |
| byte 1 | 长度 MSB (0) | 0     | 0     | 0     | 0     | 0     | 0     | 0     | 0     |
| byte 2 | 长度 LSB (4) | 0     | 0     | 0     | 0     | 0     | 1     | 0     | 0     |
| byte 3 | ‘M’          | 0     | 1     | 0     | 0     | 1     | 1     | 0     | 1     |
| byte 4 | ‘Q’          | 0     | 1     | 0     | 1     | 0     | 0     | 0     | 1     |
| byte 5 | ‘T’          | 0     | 1     | 0     | 1     | 0     | 1     | 0     | 0     |
| byte 6 | ‘T’          | 0     | 1     | 0     | 1     | 0     | 1     | 0     | 0     |

协议名是表示协议名 **MQTT** 的 UTF-8 编码的字符串。MQTT规范的后续版本不会改变这个字符串的偏移和长度。

支持多个协议的服务器通过使用协议名来确定是否数据是 MQTT 数据。<mark>协议名必须是 UTF-8 字符串 “MQTT” 。如果服务器不愿接收 CONNECT 报文，并且会告知它是 MQTT 服务器，并且返回一个 原因码为 0x84 （不支持的协议版本）的 CONNACK 包，它**必须**关闭网络连接</mark>。\[MQTT-3.1.2-1\]。

> 非正式评注
> 报文指示器，比如防火墙，可以使用协议名来标识 MQTT 流量。

#### 3.1.2.2 协议级别 Protocol Level

##### 图例 3.3 - Protocol Level byte协议级别字节构成

|          | **说明** | **7** | **6** | **5** | **4** | **3** | **2** | **1** | **0** |
|----------|----------|-------|-------|-------|-------|-------|-------|-------|-------|
| 协议级别 |
| byte 7   | Level(4** | 0     | 0     | 0     | 0     | 0     | 1     | 0     |  1    |

单字节无符号值，表示客户端使用协议的修订级别。 协议版本5.0的协议版本字段的值为5（0x05）。

 使用多版本的 MQTT 协议的服务器协议版本来决定客户端使用哪一个版本的 MQTT。<mark>如果协议版本非 5 并且服务器不愿接收 CONNECT 报文，那么服务器可以发送带有原因码 0x84 （不支持的协议版本）的 CONNACK 报文，那么**必须**关闭网络连接</mark>\[MQTT-3.1.2-2\]。
 
#### 3.1.2.3 连接标志 Connect Flags

##### 图例 3.4 -连接标志位

<table style="text-align:center">
 <tbody>
 <tr>
  <td><b>Bit</b></td>
  <td><b>7</b></td>
  <td><b>6</b></td>
  <td><b>5</b></td>
  <td><b>4</b></td>
  <td><b>3</b></td>
  <td><b>2</b></td>
  <td><b>1</b></td>
  <td><b>0</b></td>
 </tr>
 <tr>
  <td></td>
  <td>User Name Flag</td>
  <td>Password Flag</td>
  <td>Will Retain</td>
  <td colspan="2">Will QoS</td>
  <td>Will Flag</td>
  <td>Clean Start</td>
  <td>Reserved</td>
 </tr>
 <tr>
  <td>Byte 8</td>
  <td>X</td>
  <td>X</td>
  <td>X</td>
  <td>X</td>
  <td>X</td>
  <td>X</td>
  <td>X</td>
  <td>X</td>
 </tr>
 </tbody>
</table>

<mark>服务端**必须**验证CONNECT控制报文的保留标志位（第0位）是否为 0 ，如果不为 0 ，则视其为畸形报文</mark> \[MQTT-3.1.2-3\]。关于错误处理的更多信息请参阅[4.13 节](#anchor-4.13)。

#### 3.1.2.4 清理开始 Clean Start

**位置：**连接标志字节的第1位

这个二进制位指定了本连接是开启一个新的会话还是继续之前已存在的会话。这里可以参阅[section 4.1](#anchor-4.1) 中会话状态(Session State)的定义

<mark>如果接收到的 CONNECT 报文中的清理开始（Clean Start） 设为 1，那么服务器和客户端就**必须**丢弃之前存在的会话并开启新的会话。会话到期间隔（Session Expiry interval）用来表示断开连接后会话留存的时间\[MQTT-3.1.2-4\]</mark>。因此当清理开始（Clean Start） 被设为 1 的时候， CONNACK 报文中的会话存在标识(Session Present flag)皆设为 0。

<mark>如果接收到的 CONNECT 报文中的清理开始（Clean Start） 设为 0，且　有一个与客户端标识符相关的会话，那么服务器必须基于已存在的会话状态来恢复和客户端的通信\[MQTT-3.1.2-5\]。如果如果接收到的 CONNECT 报文中的清理开始（Clean Start） 设为 0，但是没有一个与客户端标识符相关的会话，那么服务器必须创建一个新的会话</mark>\[MQTT-3.1.2-6\]，

#### 3.1.2.5 遗嘱标志 Will Flag

**位置：**连接标志的第2位。

<mark>遗嘱标识（Will Flag）若被设为 1，那么遗嘱消息（Will Message）必须存在服务器上，并且和会话关联起来</mark>\[MQTT-3.1.2-7\]。在 CONNECT 的有效载荷中的遗嘱消息（Will Message）包含了遗嘱属性（Will Properties），遗嘱主题（Will Topic）以及遗嘱有效载荷（Will Payload）字段。<mark>在网络连接关闭且遗嘱延迟间隔（Will Delay interval）过期或者会话结束的时候，遗嘱消息（Will Message）必须被发布出来，除非在服务器在收到原因码为 0x00（正常断连）DISCONNECT 包之前就已经把遗嘱消删除了或者在遗嘱延迟间隔过期前就建立了新的网络连接</mark>\[MQTT-3.1.2-8\]。

遗嘱消息发布的条件，包括但不限于：

-   服务端检测到了一个I/O错误或者网络故障。
-   客户端在保持连接（Keep Alive）的时间内未能通讯。
-   客户端没有先发送原因码为 0x00 （正常断连）的 DISCONNECT 报文直接关闭了网络连接。
-   服务器没有先发送原因码为 0x00 （正常断连）的 DISCONNECT 报文直接关闭了网络连接。

<mark>如果遗嘱标识（Will Flag）被设为 1，那么有效载荷中**必须**要有遗嘱属性，遗嘱主题和遗嘱有效载荷字段</mark>\[MQTT-3.1.2-9\]。<mark>服务器中的遗嘱消息**必须**要从存储会话状态中移除</mark>\[MQTT-3.1.2-10\]。

在网络连接关闭且遗嘱延迟间隔（Will Delay interval）过期或者会话结束的时候，不管哪种情况先出现，服务端都应该迅速发布遗嘱消息。在关机或故障的情况下，服务端可以推迟遗嘱消息的发布直到之后的重启。如果发生了这种情况，在服务器故障和遗嘱消息被发布之间可能会有一个延迟。

关于遗嘱延迟间隔的更多信息请参阅[3.1.3.2 节](#anchor-3.1.3.2)。

> 非正式评注
> 客户端可以通过将遗嘱延迟间隔设为长于会话到期间隔，并发送带有原因码 0x04 (以遗嘱消息断开)的 DISCONNECT 包安排遗嘱消息通知会话已经到期。

#### 3.1.2.6 遗嘱 QoS Will QoS

**位置：**连接标志的第4和第3位。

这两位用于指定发布遗嘱消息时使用的服务质量等级。

<mark>如果遗嘱标志被设置为0，遗嘱 QoS 也**必须**设置为0(0x00)</mark> \[MQTT-3.1.2-11\]。

<mark>如果遗嘱标志被设置为1，遗嘱 QoS 的值可以等于 0(0x00)，1(0x01)，2(0x02)</mark>\[MQTT-3.1.2-12\]。当它的值等于 3 的时候，它是一个畸形报文。关于错误处理的更多信息请参阅[4.13 节](#anchor-4.13)。

#### 3.1.2.7 遗嘱保留 Will Retain

**位置：**连接标志的第5位。

如果遗嘱消息被发布时需要保留，需要指定这一位的值。

<mark>如果遗嘱标志被设置为 0 ，遗嘱保留（Will Retain）标志也**必须**设置为 0 </mark>\[MQTT-3.1.2-13\]。<mark>如果遗嘱标志被设置为1：如果遗嘱保留被设置为0，服务端**必须**将遗嘱消息当作非保留消息发布</mark> \[MQTT-3.1.2-14\]。<mark>如果遗嘱保留被设置为1，服务端**必须**将遗嘱消息当作保留消息发布</mark> \[MQTT-3.1.2-15\]。

#### 3.1.2.8 用户名标志 User Name Flag

**位置：**连接标志的第7位。

<mark>如果用户名（User Name）标志被设置为0，有效载荷中**不能**包含用户名字段</mark> \[MQTT-3.1.2-16\]。<mark>如果用户名（User Name）标志被设置为1，有效载荷中**必须**包含用户名字段</mark> \[MQTT-3.1.2-17\]。

#### 3.1.2.9 密码标志 Password Flag

**位置：**连接标志的第6位。

<mark>如果密码（Password）标志被设置为0，有效载荷中**不能**包含密码字段</mark> \[MQTT-3.1.2-18\]。<mark>如果密码（Password）标志被设置为1，有效载荷中**必须**包含密码字段</mark> \[MQTT-3.1.2-19\]。

> 非正式评注
> 本版本的 MQTT 协议允许在不带用户名的情况下发送密码，MQTT 3.1.1 版就做不到这一点。通常会在使用证书的情况下只发送密码。

#### 3.1.2.10 保持连接 Keep Alive

##### 图例 3.5保持连接字节

| **Bit** | **7**                   | **6** | **5** | **4** | **3** | **2** | **1** | **0** |
|---------|-------------------------|-------|-------|-------|-------|-------|-------|-------|
| byte 9  | 保持连接 Keep Alive MSB |
| byte 10 | 保持连接 Keep Alive LSB |

保持连接（Keep Alive）是一个以秒为单位的时间间隔，用两个字节整形来表示，它是指在客户端传输完成一个控制报文的时刻到发送下一个报文的时刻，两者之间允许空闲的最大时间间隔。客户端负责保证控制报文发送的时间间隔不超过保持连接的值。<mark>如果没有任何其它的控制报文可以发送，客户端**必须**发送一个 PINGREQ 报文</mark> \[MQTT-3.1.2-20\]。

<mark>如果服务器在 CONNACK 报文中返回了一个服务器保持连接字段，客户端必须使用新的返回的值而非之前它发送的保持连接的值</mark>\[MQTT-3.1.2-21\]。

不管保持连接的值是多少，客户端任何时候都可以发送 PINGREQ 报文，并且使用PINGRESP报文判断网络和服务端的活动状态。

<mark>如果保持连接的值非零，并且服务端在一点五倍的保持连接时间内没有收到客户端的控制报文，它**必须**关闭客户端的网络连接，认为网络连接失败</mark> \[MQTT-3.1.2-24\]。

客户端发送了 PINGREQ 报文之后，如果在合理的时间内仍没有收到 PINGRESP 报文，它**应该**关闭到服务端的网络连接。

保持连接的值为零表示关闭保持连接机制。如果保持连接的值为 0，则客户没有义务按任何特定时间计划发送 MQTT 控制数据包。

> **非正式评注**
>
> 服务器可能会因为其它原因而断开与客户端的连接，比如它可能关机。设置了 Keep Alive 值不能保证客户端能保持连接。

> **非正式评注**
>
> 保持连接的实际值是由应用指定的，一般是几分钟。允许的最大值是18小时12分15秒。

#### 3.1.2.11 CONNECT 属性 CONNECT Properties

##### 属性长度 Property Length
用不定长字节整形来编码的 Properties 长度

##### 会话过期间隔 Session Expiry Interval
17(0x11)字节，会话过期间隔标识符。

四个字节整形表示的Session Expiry Interval，单位是秒，如果这个属性在一个控制包中出现了两次，就会视为协议错误。

如果 Session Expiry Interval 设置为 0 或者没有设置，那么当网络连接关闭的时候 Session 就会结束。

如果 Sessoin Expiry Interval 设为 0xFFFFFFFF(UINT_MAX)，那么这个 Session 就不会过期。

<mark>如果 Session Expiry Interval 大于 0，那么 服务器和客户端在关闭网络连接的之后还要保存网络连接。</mark>\[MQTT-3.1.2-23\]

> **非正式评注**
>
> 客户端或服务器中可能在部分时间间隔段中未运行，比如因为客户端或服务器未运行。 这可能会导致删除状态的延迟。

关于会话的更多信息，请参阅[4.1 节](#anchor-4.1)。关于存储状态的细节和限制请参阅[4.1.1 节](#anchor-4.1.1)。

当会话过期时，客户端和服务器不需要以原子方式处理状态的删除。


> **非正式评注**
> 
> 将清理开始（Clean Start）设置为 1 且会话到期间隔设置为 0， 相当于 MQTT 3.1.1 版本标准中将清理会话（Clean Session） 设置为 1。将清理开始（Clean Start）设置为 0 且会话到期间隔不设置，就相当于 MQTT 3.1.1 版本中将清理会话（Clean Session）设置为 0。
>
> **非正式评注**
> 
> 只愿意在连接时处理消息的客户端可以把清理开始（Clean Start）的值设置为 1 并将会话到期间隔设置为 0 。它不会收到连接之前发布的应用消息，并且每次连接时都必须重新订阅它感兴趣的任何主题。
>
> **非正式评注**
>
> 当客户端连接到服务器的网络非常不稳定的情况下，客户端可以设置一个较短的 Session Expiry Interval，以便它在网络恢复可用状态的时候重新建立连接并继续进行可靠的消息传输。若客户端没有重新连接，那么就让 Session 过期，然后丢失应用消息。
> 
> **非正式评注**
> 
> 当客户端建立一个带有很长的会话到期间隔或者会话根本不会过期的连接的时候，它会请求服务器在它断开连接后的很长一段时间内维护它的 MQTT 会话。假如有客户端在之后的某个时间点需要重新和服务器建立连接的这种情况，客户端应该仅仅在这种情况下建立带有长会话到期间隔的连接。如果客户端决定将来不会在用到会话的时候，它就应该在断开连接的时候把会话到期间隔设为 0。
> 
> **非正式评注**
> 
>客户端应该一直在 CONNACK 包中使用会话存在标识（Session Present flag）去判断是否服务器一直为该客户端维持会话状态。
> 
> **非正式评注**
> 
>客户端可以避开实现它自己的会话到期间隔，它可以通过依赖服务器返回的会话存在标识来判断会话是否到期。如果客户端实现了它自己的会话到期间隔，那么它就需要存储会话状态的删除时间作为它自己会话的一部分。


##### 接收最大值 Receive Maximum

33(0x21)字节，接收最大值标识符。

接收最大值由 2 个字节的整形来表示，如果单个报文中该属性出现了多次，则协议错误。

客户端通过使用这个值来限制同时并行处理 QoS1 和 QoS2 发布的消息数量。现在还没有机制来限制服务器可能会发布的 QoS0　消息。

接收最大值只会应用在当前网络连接中，如果没有设置接收最大值，那么它就会设为默认值 65535。值被设为0 或者该属性重复从而引发协议错误。

请参阅[4.9 节](#anchor-4.9)中的流程控制，去了解如何使用接收最大值。

##### 最大报文长度 Maximum Packet Size
39(0x27)字节，最大报文长度标识符。

接收最大值由 4 个字节的整形来表示客户端愿意接收的最大报文长度，如果不存在最大报文长度属性，作为剩余长度编码和协议头大小的结果，除了协议的限制外，不限制数据包大小。

如果单个报文中该属性出现了多次，则协议错误。

> 非正式评注
> 当需要限制最大报文大小的时候，由应用程序来选择合适的最大报文长度。

这里的报文大小指的是整个 MQTT 控制包的所有字节数。客户端使用最大报文大小来通知服务器，让它不要处理超过这个大小的数据包。

<mark>服务器 **不能** 发送超过最大报文长度的包给客户端</mark>。\[MQTT-3.1.2-24\]如果客户端收到了超出限制的报文，那么会视为协议错误，并在断开连接的时候返回一个 0x95（报文太大）的 。

<mark>如果 Packet 太大以至于不能正常发送，那么服务器就需要丢弃那些 Packet 并且表现得好像已经完成应用消息发送那样。</mark>\[MQTT-3.1.2-25\]

在共享订阅的情况下，有可能消息太大不能发送给部分客户端，但是另外一部分客户端可以接收到，服务器可以选择不向任何客户端发送消息并丢弃所有的消息，也可以只向那些可以接收到消息的客户端发送消息。

> 非正式评注
> 如果数据包在未发送的情况下被丢弃，则服务器可以将丢弃的数据包放在“死信队列(dead letter queue)”上或执行其他诊断操作。 此类行为超出了本规范的范围。


##### 主题别名最大值 Topic Alias Maximum

34(0x22)字节，主题别名最大值标识符。
主题别名最大值由 2 个字节的整形来表示，如果单个报文中该属性出现了多次，则协议错误。若未设定主题别名最大值，则就将其默认设为 0。

##### 请求响应信息 Request Response Information

25(0x19)字节，请求响应信息标识符。

这个字节只能表示 0 或者 1，如果它表示的值是 0 或 1 以外的值，或者该属性出现多次，那么就会视为协议错误。若未指定请求响应消息，则将其值设为默认值 0。

客户端使用该值去请求服务器，服务器会在 CONNACK 包中返回响应信息。当请求响应信息设为 0 的时候，意味着服务器不应该返回响应信息了。如果值为 1，那么服务器会在 CONNACK 包中返回响应信息。

>服务器可以在客户端请求响应信息的时候选择不在 CONNACK 中包含响应信息。

##### 请求问题信息 Request Problem Information

23(0x17)字节，请求问题信息标识符。

这个字节只能表示 0 或者 1，如果它表示的值是 0 或 1 以外的值，或者该属性出现多次，那么就会视为协议错误。若未指定请求响应消息，则将其值设为默认值 1。

客户端使用该值来表示是否用户属性或原因码发送失败。

如果请求问题信息的值被设为 0，服务器可以在 CONNACK 或 DISCONNECT 报文中返回一个原因字符串或用户属性。但是不能发送原因属性或用户属性在其它任何包中，PUBLIHS, CONNACK 或 DISCONNECT 包除外。如果值设为 0，而 客户端却在 PUBLISH,CONNACK,DISCONNECT 包以外收到了原因码或用户属性, 那么就应该用一个带有原因码 0x82（协议错误） 的 DISCONNECT 报文去断开连接。

如果值设为 1，那么服务器就可以在任何被允许的报文中返回原因码或用户属性。

##### 用户属性 User Property
38(0x26)字节，用户属性标识符。
由一个 UTF-8 的字符串对表示。

用户属性可以在单个报文中出现多次，用于表示多个名称，值对。相同的名称也可以出现多次。
> 在 CONNECT 报文中的用户属性在客户端发送到服务器的过程中可以被用来发送和连接相关的属性。这些属性的含义不由本规范定义。

##### 验证方法 Authentication Method
21(0x15)字节，验证方法标识符。
该属性由包含用于扩展验证的扩展方法名的 UTF-8 编码字符串表示，当该属性在同一报文中出现多次时，会被视为协议错误。如果不设置验证方法，默认情况下扩展验证不会被启用。

<mark>如果客户端在 CONNECT 报文中设置了验证方法，那么客户端在到 CONNACK 报文前就**不能**再发送除 AUTH 包和 DISCONNECT 报文以外的报文。</mark>\[MQTT-3.1.2-30\]

##### 验证数据 Authentication Data
22(0x16)字节，验证数据标识符
由包含验证数据的二进制数据表示，如果 CONNECT 报文在包含验证数据的情况下却不包含验证方法，或者当该属性在 CONNECT 报文中出现时，则会被视为协议错误。

该数据的内容由验证方法定义，关于扩展验证的更多信息请参考[4.12节](#anchor-4.12)。


#### 3.1.2.12 可变报头的非规范示例

<table style="text-align:center">
  <tr>
    <td align="center"><b></b></td>
    <td align="center"><b>描述</b></td>
    <td align="center"><b>7</b></td>
    <td align="center"><b>6</b></td>
    <td align="center"><b>5</b></td>
    <td align="center"><b>4</b></td>
    <td align="center"><b>3</b></td>
    <td align="center"><b>2</b></td>
    <td align="center"><b>1</b></td>
    <td align="center"><b>0</b></td>
  </tr>
  <tr>
    <td align="center" colspan="10">协议名</td>
  </tr>
  <tr>
    <td align="center">字节1</td>
    <td align="center">MSB 长度（0）</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
  </tr>
  <tr>
    <td align="center">字节2</td>
    <td align="center">MSB 长度（0）</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
    <td align="center">0</td>
  </tr>
  <tr>
    <td align="center">字节3</td>
    <td align="center">'M'</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">1</td>
    <td align="center">0</td>
    <td align="center">1</td>
  </tr>
  <tr>
    <td align="center">字节4</td>
    <td align="center">'Q'</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">1</td>
  </tr>
  <tr>
    <td align="center">字节5</td>
    <td align="center">'T'</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
    <td align="center">0</td>
  </tr>
  <tr>
    <td align="center">字节6</td>
    <td align="center">'T'</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
    <td align="center">0</td>
  </tr>
  <tr>
    <td align="center" colspan="10">协议版本</td>
  </tr>
  <tr>
    <td align="center"><b></b></td>
    <td align="center"><b>描述</b></td>
    <td align="center"><b>7</b></td>
    <td align="center"><b>6</b></td>
    <td align="center"><b>5</b></td>
    <td align="center"><b>4</b></td>
    <td align="center"><b>3</b></td>
    <td align="center"><b>2</b></td>
    <td align="center"><b>1</b></td>
    <td align="center"><b>0</b></td>
  </tr>
  <tr>
    <td align="center">字节7</td>
    <td align="center">版本（5）</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
    <td align="center">1</td>
  </tr>
  <tr>
    <td align="center" colspan="10">连接标识</td>
  </tr>
  <tr>
    <td align="center" rowspan="7"> 字节 8</td>
    <td align="center"> 用户名标识（1）</td>
    <td align="center" rowspan="7">1</td>
    <td align="center" rowspan="7">1</td>
    <td align="center" rowspan="7">0</td>
    <td align="center" rowspan="7">0</td>
    <td align="center" rowspan="7">1</td>
    <td align="center" rowspan="7">1</td>
    <td align="center" rowspan="7">1</td>
    <td align="center" rowspan="7">0</td>
  </tr>
  <tr>
    <td align="center"> 密码标识（1）</td>
  </tr>
  <tr>
    <td align="center"> 遗嘱保留（1）</td>
  </tr>
  <tr>
    <td align="center"> 遗嘱QoS（01）</td>
  </tr>
  <tr>
    <td align="center"> 遗嘱标识（1）</td>
  </tr>
  <tr>
    <td align="center"> 清除开始（1）</td>
  </tr>
  <tr>
    <td align="center"> 保留（0）</td>
  </tr>
  <tr>
    <td align="center" colspan="10">保持连接</td>
  </tr>
  <tr>
    <td align="center">字节 9</td>
    <td align="center"> 保持连接（MSB）</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
 </tr>
 <tr>
    <td align="center">字节 10</td>
    <td align="center"> 保持连接（LSB）</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
 </tr>
 <tr>
    <td align="center" colspan="10">属性</td>
 </tr>
 <tr>
    <td align="center">字节 11</td>
    <td align="center"> 长度（LSB）</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
    <td align="center">1</td>
 </tr>
 <tr>
    <td align="center">字节 12</td>
    <td align="center"> 会话到期间隔标识符（17）</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">1</td>
 </tr>
 <tr>
    <td align="center">字节 13</td>
    <td align="center" rowspan="4">会话到期间隔（10）</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
 </tr>
 <tr>
    <td align="center">字节 14</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
 </tr>
 <tr>
    <td align="center">字节 15</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
 </tr>
 <tr>
    <td align="center">字节 16</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
    <td align="center">1</td>
    <td align="center">0</td>
 </tr>
</table>

### 3.1.3 有效载荷 Payload

<mark>CONNECT报文的有效载荷（payload）包含一个或多个以长度为前缀的字段，可变报头中的标志决定是否包含这些字段。如果包含的话，**必须**按这个顺序出现：客户端标识符，遗嘱属性，遗嘱主题，遗嘱消息，遗嘱有效负载，用户名，密码</mark> \[MQTT-3.1.3-1\]。

#### 3.1.3.1 客户端标识符 Client Identifier
服务端使用客户端标识符 (ClientId) 识别客户端。连接服务端的每个客户端都有唯一的客户端标识符（ClientId）。<mark>客户端和服务端都必须使用 ClientId 识别两者之间的 MQTT 会话相关的状态</mark> \[MQTT-3.1.3-2\]。关于会话状态的更多信息请参阅[4.1 节](#anchor-4.1)。

<mark>客户端标识符 (ClientId) **必须**存在而且**必须**是CONNECT报文有效载荷的第一个字段</mark> \[MQTT-3.1.3-3\]。

<mark>客户端标识符**必须**是[1.5.3节](#anchor-1.5.3)定义的UTF-8编码字符串</mark> \[MQTT-3.1.3-4\]。 

<mark>服务端**必须**允许1到23个字节长的UTF-8编码的客户端标识符，客户端标识符只能包含这些字符：“0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ”（大写字母，小写字母和数字）</mark>\[MQTT-3.1.3-5\]。

服务端**可以**允许编码后超过23个字节的客户端标识符 (ClientId)。服务端**可以**允许包含不是上面列表字符的客户端标识符 (ClientId)。

<mark>服务端**可以**允许客户端提供一个零字节的客户端标识符 (ClientId) ，如果这样做了，服务端**必须**将这看作特殊情况并分配唯一的客户端标识符给那个客户端</mark>\[MQTT-3.1.3-6\]。<mark>然后它**必须**假设客户端提供了那个唯一的客户端标识符，正常处理这个CONNECT报文，并且必须返回 CONNACK 包中被分配的客户端标识符</mark>\[MQTT-3.1.3-7\]。

如果服务器拒绝了客户端标识符，那么它**可以**按照在[4.13节](#anchor-4.13) 错误处理所描述的那样使用带有 0x85 （客户端标识符无效）原因码的 CONNACK 报文去响应 CONNECT 报文，然后**必须**关闭网络连接。

> 非正式评注
> 客户端实现可以提供一个方便的方法用于生成随机的客户端标识符。使用此方法的客户端应注意避免创建长期孤立的会话。

#### 3.1.3.2 遗嘱属性 Will Properties

如果遗嘱标识 (Will Flag) 设为 1，Will Properties 就会是 Payload 中的下一个字段。Will Properties 字段是用来定义当消息被发布的时候会和 Will Message 一起发送的 Application Message  Properties 以及何时发布 Will Message 的属性。

##### 3.1.3.2.1 属性长度 Property Length
遗嘱属性中属性的长度由一个可变字节长度编码。
 
##### 3.1.3.2.2 遗嘱延迟间隔 Will Delay Interval
24(0x18) 字节，遗嘱延迟间隔标识符。
由四字节整形来表示以秒为单位的遗嘱延迟间隔，当单个 CONNECT 报文中重复出现了该属性则会被视为协议错误，如果遗嘱延迟间隔没有设置，那么会将默认值设为 0，也就意味着遗嘱消息的发布不会有任何延迟。

服务器只有在遗嘱延迟间隔过期或者会话结束的时候才可以发布客户端的遗嘱消息。如果在遗嘱延迟间隔过期之前在这个会话上建立了一个新的网络连接，那么服务器就不应该再发送任何遗嘱消息了。

> 非正式评注
>遗嘱延迟间隔其中一个用途是在临时网络断开并且客户端在遗嘱消息发布之前成功地重新连接并继续其会话的情况下避免发布遗嘱消息。

> 非正式评注
>如果网络连接使用了已存在的到服务器的网络连接的客户端标识符，那么已存在的连接中的遗嘱消息就会被发送，除非新的连接指明了清除开始（Clean Start）为 0 且遗嘱延迟大于 0。如果遗嘱延迟是 0，那么遗嘱消息就会在已存在的网络连接关闭的时候发送出去，如果清除开始设置为 1，那么遗嘱消息会因为会话结束。

##### 3.1.3.2.3 有效载荷格式指示器 Payload Format Indicator
1(0x01) 字节，有效载荷格式指示器标识符。

有效载荷格式指示器的值分以下两种：
- 当该属性值为 0（0x00） 的时候，意味着遗嘱消息是未确定的字节，相当于不发送有效载荷格式指示器。
- 当该属性值为 1（0x01） 的时候，意味着遗嘱消息是 UTF-8 的字符数据，在有效载荷中的 UTF-8 数据必须是由 Unicode 规范定义且在 [RFC3629] 中定义的格式良好的 UTF-8 数据。

<mark>服务器**必须**发送所有未选择有效载荷格式指示器给所有接收应用消息的订阅者</mark>\[MQTT--3.3.2-4\]。接收者也**可以**验证格式指定的有效载荷，以及是否它没有如同[4.13 节](#anchor-4.13)所描述的那样发送带有 0x99 （有效载荷格式无效）原因码的 PUBACK，PUBREC 或 DISCONNECT报文。

##### 3.1.3.2.4 消息过期间隔 Message Expiry Interval
2(0x02)字节，消息过期间隔标识符。

本属性由 4 字节整形来表示。如果 CONNECT 报文重复出现该属性则会被视为协议错误。

如果存在该属性，那么这个 4 字节的值就用来表示单位为秒的遗嘱消息的生命周期，并且当服务器发布遗嘱消息的时候会被作为发布过期间隔发送。

##### 3.1.3.2.5 内容类型 Content Type
3 (0x03)字节, 内容类型标识符。
该属性是以 UTF-8 编码，用来描述遗嘱消息内容的字符串，当该属性在同一报文中出现多次时，则会被视为协议错误。该属性的内容由收发消息的应用程序来定义。

##### 3.1.3.2.6 响应主题 Response Topic
8 (0x08) 字节，响应主题标识符。
由 UTF-8 编码的字符串，通常是用来作为响应消息中的主题名。当该属性在同一报文中重复出现时，则会被视为协议错误。若响应主题存在，会将遗嘱消息视为一个请求。

关于请求/响应的更多信息请参考[4.10 节](#anchor-4.10)。

##### 3.1.3.2.7 关联数据 Correlation Data
9 (0x09)字节，关联数据标识符。

由二进制数据表示， 该数据通常是给请求消息的发送者中用来鉴别哪一个才是它收到的响应消息的请求。若该属性在同一报文中重复出现，则会被视为协议错误。如果没有设定关联数据 ，那么请求者就不需要任何关联数据。

##### 3.1.3.2.8 用户属性 User Property
38(0x26)字节，用户属性标识符。

用户属性可以在单个报文中出现多次，用于表示多个名称，值对。相同的名称也可以出现多次。

<mark>当发布遗嘱消息的时候，服务器**必须**维护用户属性的顺序</mark>。\[MQTT-3.1.3-10\]

> 非正式评注
> 该服务器用于给传输应用层提供一个名称-值(name-value)标签，该标签仅由负责收发的应用程序来解释和了解。

#### 3.1.3.3 遗嘱主题 (Will Topic)
如果遗嘱标识符设为 1，那么遗嘱主题会是有效载荷的下一个字段，<mark>遗嘱主题**必须**像[1.5.4 节](#anchor-1.5.4) 所描述的那样是一个 UTF-8 编码的字符串</mark>\[MQTT-3.1.3-11\]。

#### 3.1.3.4 遗嘱有效载荷 (Will Payload)
如果遗嘱标识符设为 1，那么遗嘱有效载荷会是有效载荷的下一个字段，遗嘱有效载荷如同[3.1.2.5 节](#anchor-3.1.2.5) 所描述的那样定义了要被发布给遗嘱主题的应用消息有效载荷。该字段包含二进制数据。

#### 3.1.3.5 用户名 (User Name)
<mark>如果用户名标识设为 1，那么用户名会是有效载荷的下一个字段，用户名**必须**如同[1.5.4 节](#anchor-1.5.4) 所描述的那样是一个 UTF-8 编码的字符串</mark>\[MQTT-3.1.3-12\]。它可以被服务器用来验证和授权。

#### 3.1.3.6 密码 (Password)
如果密码标识设为 1，那么密码会是有效载荷的下一个字段。密码字段是二进制数据，虽然该字段名为“密码”，但是它可以用来存放任何证书信息。

### 3.1.4 连接行为 (CONNECT Actions)

注意：服务器可以在同一个TCP端口或其他网络端点上支持多种协议（包括本协议的早期版本）。如果服务器确定协议是 MQTT 5.0 ，那么它会按照下面的方法验证连接请求。

1.  网络连接建立后，如果服务端在合理的时间内没有收到CONNECT报文，服务端**应该**关闭这个连接。
2.  <mark>服务端**必须**按照3.1节的要求验证CONNECT报文，如果报文不符合规范，服务端不发送CONNACK报文直接关闭网络连接</mark> \[MQTT-3.1.4-1\]。服务器**可以**如4.13 节所描述的那样在网络连接关闭之前，发送一个带有的原因码大于等于 0x80 的 CONNACK 报文，
3.  服务端**可以**检查 CONNECT 报文的内容是不是满足任何进一步的限制，**可以**执行身份验证和授权检查。如果任何一项检查没通过，按照 3.2 节的描述，它**应该**发送一个适当的、返回码非零的 CONNACK 响应，并且**必须**关闭这个网络连接\[MQTT-3.1.4-2\]。在关闭网络之前，它可以如同[3.2 节]和[4.13 节]所描述的那样发送一个带有原因码大于等于 0x80 的 一个适合的 CONNACK 响应。

如果验证成功了，服务器还要执行以下几步：
1. 如果客户端标识符代表的客户端已经连接到了服务器，那么服务器如[4.13 节]所描述的那样发送一个带有 0x8E （会话被接管）的 DISCONNECT 报文，并且**必须**关闭已存客户端的网络连接\[MQTT-3.1.4-3\]， 如果已存客户端已经有了遗嘱消息，那么遗嘱消息应该像 3.1.2.5 节所描述的那样被发布。
> 非正式评注
> 如果已存网络连接的遗嘱延迟间隔为 0，并且存在遗嘱消息，那么它将会因为网络连接被关闭而被发送，如果已存网络连接的会话间隔是 0，或者新的网络连接将清除开始(Clean Start) 设为 1，那么如果已存网络连接有一个遗嘱消息，则会发送该消息，因为原始会话在接管时会结束。
2. <mark>服务器**必须** 按照[3.1.2.4 节](#anchor-3.1.2.4)执行清除开始(Clean Start)的处理</mark>\[MQTT-3.1.4-4\]。
3. 服务器**必须**用带有 0x00 (成功) 原因码的 CONNACK 包去确认 CONNECT 包。\[MQTT-3.1.4-5\]

> 非正式评注
> 如果服务器被用于处理关键的商业数据，还需要执行验证和授权检查。如果检查通过，服务器会通过发送 Reason Code 为 0x00(Success) 的 CONNACK 包来响应。如果失败的话，就不会发送 CONNACK 包了，因为这可能会提醒潜在的攻击者存在MQTT服务器，并鼓励此类攻击者发起拒绝服务或猜测密码攻击。

4. 开始传输消息，并且保持监控状态。

允许客户端在发送CONNECT报文之后立即发送其它的控制报文；客户端不需要等待服务端的CONNACK报文。如果服务端拒绝了CONNECT，它**不能**处理客户端在CONNECT报文之后发送的任何数据 \[MQTT-3.1.4-5\]。

> 非正式评注
> 客户端通常会等待一个 CONNACK 报文。然而客户端有权在收到CONNACK之前发送控制报文，由于不需要维持连接状态，这可以简化客户端的实现。如果服务器拒绝连接，那么客户端在从服务器接收到 CONNACK 报文前发送的任何数据都不会被服务器处理。

> 非正式评注
> 收到 CONNACK 包之前发送 MQTT 控制报文的客户端将不会知道服务器的限制以及是否有已经存在的会话已经被使用了。

> 非正式评注
> 如果服务器在验证完成前发送了太多的数据，服务器可以限制网络连接的读取或者关闭网络连接。这是用来对抗拒绝服务攻击的一种方式。

### 第三章目录 MQTT控制报文

- [3.1 CONNECT – 连接服务端](0301-CONNECT.md)
- [3.2 CONNACK – 确认连接请求](0302-CONNACK.md)
- [3.3 PUBLISH – 发布消息](0303-PUBLISH.md)
- [3.4 PUBACK –发布确认](0304-PUBACK.md)
- [3.5 PUBREC – 发布收到（QoS 2，第一步）](0305-PUBREC.md)
- [3.6 PUBREL – 发布释放（QoS 2，第二步）](0306-PUBREL.md)
- [3.7 PUBCOMP – 发布完成（QoS 2，第三步）](0307-PUBCOMP.md)
- [3.8 SUBSCRIBE - 订阅主题](0308-SUBSCRIBE.md)
- [3.9 SUBACK – 订阅确认](0309-SUBACK.md)
- [3.10 UNSUBSCRIBE –取消订阅](0310-UNSUBSCRIBE.md)
- [3.11 UNSUBACK – 取消订阅确认](0311-UNSUBACK.md)
- [3.12 PINGREQ – 心跳请求](0312-PINGREQ.md)
- [3.13 PINGRESP – 心跳响应](0313-PINGRESP.md)
- [3.14 DISCONNECT – 断开连接](0314-DISCONNECT.md)
- [3.15 AUTH – 认证交换](0314-DISCONNECT.md)

### 项目主页

- [MQTT 5.0协议中文版](https://github.com/Gilbert-Wong/mqtt_v5.0)
