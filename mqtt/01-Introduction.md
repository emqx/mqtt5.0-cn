
# 第一章 概述 Introduction

## 1.0 知识产权政策
本委员会规范是在 [OASIS 知识产权政策](https://www.oasis-open.org/policies-guidelines/ipr)的[非断言模式](https://www.oasis-open.org/policies-guidelines/ipr#Non-Assertion-Mode)下提供的，该模式是技术委员会成立时选择的模式。关于实现本规范必不可少的任何专利是否已公开，以及其它的专利许可条款相关的信息，请参考技术委员会网站的知识产权部分（(<https://www.oasis-open.org/committees/mqtt/ipr.php>）。


## 1.1 MQTT协议的组织结构 Organization of MQTT

本规范分为七个章节：

- [第一章 – 介绍](01-Introduction.md)
- [第二章 – MQTT控制报文格式](02-ControlPacketFormat.md)
- [第三章 – MQTT控制报文](03-ControlPackets.md)
- [第四章 – 操作行为](04-OperationalBehavior.md)
- [第五章 – 安全](05-Security.md)
- [第六章 – 使用WebSocket](06-WebSocket.md)
- [第七章 – 一致性目标](07-Conformance.md)
- [附录B – 强制性规范声明](08-AppendixB.md)

## 1.2 术语 Terminology

本规范中用到的关键字 **必须** MUST，**不能** MUST NOT，**要求** REQUIRED，**将会** SHALL，**不会** SHALL NOT，**应该** SHOULD，**不应该** SHOULD NOT，**推荐** RECOMMENDED，**可以** MAY，**可选** OPTIONAL 都是按照 IETF RFC 2119 \[[RFC2119](#RFC2119)\] 中的描述解释，除非它们出现在标记为非规范的文本中。

**网络连接 Network Connection**

MQTT使用的底层传输协议基础设施。

-   客户端使用它连接服务端。
-   它提供有序的、可靠的、双向字节流传输。
非规范的例子参阅 [4.2](#anchor-4.2)节。

**应用消息 Application Message**
MQTT协议通过网络传输应用数据。应用消息通过MQTT传输时，它们包含有效载荷数据（Payload Data），服务质量（QoS），一套属性（Properties）的集合和主题名（Topic Name）。

**客户端 Client**  
使用MQTT的程序或设备。它可以

-   打开到服务器的网络连接。
-   发布应用消息给其它相关的客户端。
-   订阅以请求接受相关的应用消息。
-   取消订阅以移除接受应用消息的请求。
-   从服务端断开连接。

**服务端 Server**  
一个程序或设备，作为发送消息的客户端和请求订阅的客户端之间的中介。服务端

-   接受来自客户端的网络连接。
-   接受客户端发布的应用消息。
-   处理客户端的订阅和取消订阅请求。
-   转发应用消息给符合条件的已订阅客户端。
-   关闭来自客户端的网络连接。

**会话 Session** 
客户端和服务器之间有状态交互。一些会话持续时间就是网络连接的时间，另一些会话的维持可以跨越客户端和服务器之间的多个连续的网络连接。

**订阅 Subscription**  
订阅包含一个主题过滤器（Topic Filter）和一个最大的服务质量（QoS）等级。订阅与单个会话（Session）关联。会话可以包含多于一个的订阅。会话的每个订阅都有一个不同的主题过滤器。

**共享订阅 Shared Subscription**  
共享订阅包含一个主题过滤器（Topic Filter） 和一个最大的服务质量（QoS）构成，共享订阅可以关联多个会话用于更多类型的消息交换范式。匹配共享订阅的应用消息只会发送给与这些会话关联的客户端。一个会话有多个共享订阅，也可以同时并存共享订阅和非共享订阅。

**通配符订阅 Wildcard Subscription**
通配符订阅是指带有一个包含一个或多个通配符字符的主题过滤器的订阅。

**主题名 Topic Name**  
附加在应用消息上的一个标签，服务端已知且与订阅匹配。

**主题过滤器 Topic Filter**  
订阅中包含的一个表达式，用于表示相关的一个或多个主题。主题过滤器可以使用通配符。

**控制报文 MQTT Control Packet**  
通过网络连接发送的信息数据包。MQTT规范定义了十五种不同类型的控制报文，其中一个（PUBLISH报文）用于传输应用消息。

**畸形报文 Malformed Packet**
无法根据本规范解析的控制数据包。参阅 [4.13](#anchor-4.13) 节关于错误处理的详细信息。

**协议错误 Protocol Error**
在报文被解析后发现报文包含了协议不允许的数据或者该数据与客户端和服务器的状态不一致。参阅 [4.13](#anchor-4.13) 节关于错误处理的详细信息。

**遗嘱消息 Will Message**
若网络连接关闭或非正常断开， 服务器需要发布的应用消息。参阅 [3.1.2.5](#anchor-3.1.2.5) 节关于错误处理的详细信息。

## 1.3 标准引用 Normative references

**[RFC2119]**
Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels**, BCP 14, RFC 2119, DOI 10.17487/RFC2119, March 1997,
<http://www.rfc-editor.org/info/rfc2119>

**[RFC3629]**

Yergeau, F., "UTF-8, a transformation format of ISO 10646**, STD 63, RFC 3629, DOI 10.17487/RFC3629, November 2003, <http://www.rfc-editor.org/info/rfc3629>

**[RFC6455]**

Fette, I. and A. Melnikov, "The WebSocket Protocol**, RFC 6455, DOI 10.17487/RFC6455, December 2011, <http://www.rfc-editor.org/info/rfc6455>

**[Unicode]**
The Unicode Consortium. The Unicode Standard, <http://www.unicode.org/versions/latest/>

## 1.4 非标准引用 Non-normative references

**[RFC0793]**
Postel, J., "Transmission Control Protocol", STD 7, RFC 793, DOI 10.17487/RFC0793, September 1981, <http://www.rfc-editor.org/info/rfc793>

**[RFC5246]**
Dierks, T. and E. Rescorla, "The Transport Layer Security (TLS) Protocol Version 1.2**, RFC 5246, DOI 10.17487/RFC5246, August 2008, <http://www.rfc-editor.org/info/rfc5246>

**[AES]**
Advanced Encryption Standard (AES) (FIPS PUB 197**.<https://csrc.nist.gov/csrc/media/publications/fips/197/final/documents/fips-197.pdf>

**[CHACHA20]**
ChaCha20 and Poly1305 for IETF Protocols <https://tools.ietf.org/html/rfc7539>

**[FIPS1402]**
Security Requirements for Cryptographic Modules (FIPS PUB 140-2** <https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf>

**[IEEE 802.1AR]**
IEEE Standard for Local and metropolitan area networks - Secure Device Identity <http://standards.ieee.org/findstds/standard/802.1AR-2009.html>

**[ISO29192]**
ISO/IEC 29192-1:2012 Information technology -- Security techniques -- Lightweight cryptography -- Part 1: General <https://www.iso.org/standard/56425.html>

**[MQTT NIST]**
MQTT supplemental publication, MQTT and the NIST Framework for Improving Critical Infrastructure Cybersecurity <http://docs.oasis-open.org/mqtt/mqtt-nist-cybersecurity/v1.0/mqtt-nist-cybersecurity-v1.0.html>

**[MQTTV311]**
MQTT V3.1.1 Protocol Specification <http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html>

**[ISO20922]**
MQTT V3.1.1 ISO Standard (ISO/IEC 20922:2016** <https://www.iso.org/standard/69466.html>

**[NISTCSF]**
Improving Critical Infrastructure Cybersecurity Executive Order 13636 <https://www.nist.gov/sites/default/files/documents/itl/preliminary-cybersecurity-framework.pdf>

**[NIST7628]**
NISTIR 7628 Guidelines for Smart Grid Cyber Security Catalogue <https://www.nist.gov/sites/default/files/documents/smartgrid/nistir-7628_total.pdf>

**[NSAB]**
NSA Suite B Cryptography <http://www.nsa.gov/ia/programs/suiteb_cryptography/>

**[PCIDSS]**
PCI-DSS Payment Card Industry Data Security Standard <https://www.pcisecuritystandards.org/pci_security/>

**[RFC1928]**
Leech, M., Ganis, M., Lee, Y., Kuris, R., Koblas, D., and L. Jones, "SOCKS Protocol Version 5**, RFC 1928, DOI 10.17487/RFC1928, March 1996, <http://www.rfc-editor.org/info/rfc1928>

**[RFC4511]**
Sermersheim, J., Ed., "Lightweight Directory Access Protocol (LDAP): The Protocol**, RFC 4511, DOI 10.17487/RFC4511, June 2006, <http://www.rfc-editor.org/info/rfc4511>

**[RFC5280]**
Cooper, D., Santesson, S., Farrell, S., Boeyen, S., Housley, R., and W. Polk, "Internet X.509 Public Key Infrastructure Certificate and Certificate Revocation List (CRL) Profile**, RFC 5280, DOI 10.17487/RFC5280, May 2008, <http://www.rfc-editor.org/info/rfc5280>

**[RFC6066]**
Eastlake 3rd, D., "Transport Layer Security (TLS) Extensions: Extension Definitions**, RFC 6066, DOI 10.17487/RFC6066, January 2011, <http://www.rfc-editor.org/info/rfc6066>

**[RFC6749]**
Hardt, D., Ed., "The OAuth 2.0 Authorization Framework**, RFC 6749, DOI 10.17487/RFC6749, October 2012, <http://www.rfc-editor.org/info/rfc6749>

**[RFC6960]**
Santesson, S., Myers, M., Ankney, R., Malpani, A., Galperin, S., and C. Adams, "X.509 Internet Public Key Infrastructure Online Certificate Status Protocol - OCSP**, RFC 6960, DOI 10.17487/RFC6960, June 2013, <http://www.rfc-editor.org/info/rfc6960>

**[SARBANES]**
Sarbanes-Oxley Act of 2002. <http://www.gpo.gov/fdsys/pkg/PLAW-107publ204/html/PLAW-107publ204.htm>

**[USEUPRIVSH]**
U.S.-EU Privacy Shield Framework <https://www.privacyshield.gov>

**[RFC3986]**
Berners-Lee, T., Fielding, R., and L. Masinter, "Uniform Resource Identifier (URI): Generic Syntax**, STD 66, RFC 3986, DOI 10.17487/RFC3986, January 2005, <http://www.rfc-editor.org/info/rfc3986>

**[RFC1035]**
Mockapetris, P., "Domain names - implementation and specification**, STD 13, RFC 1035, DOI 10.17487/RFC1035, November 1987, <http://www.rfc-editor.org/info/rfc1035>

**[RFC2782]**
Gulbrandsen, A., Vixie, P., and L. Esibov, "A DNS RR for specifying the location of services (DNS SRV)", RFC 2782, DOI 10.17487/RFC2782, February 2000, <http://www.rfc-editor.org/info/rfc2782>

## 1.5 数据表示 Data representations

### 1.5.1 二进制位 Bits

字节中的位从0到7。第7位是最高有效位，第0位是最低有效位。

### 1.5.2 两字节整形 Two Byte Integer

两字节整形数据的值是 16 位无符号大端序的整形。高阶字节先于低阶字节。这意味着在网络上将16位字显示为最高有效字节（MSB），然后是最低有效字节（LSB）。

### 1.5.3 四字节整形 Four Byte Integer

四字节整形数据的值是 32 位无符号大端序的整形。高阶字节先于连续的低阶字节。这意味着32位字在网络上显示为最高有效字节（MSB），然后是下一个最高有效字节（MSB），接着是下一个最高有效字节（MSB），其次是最低有效字节（LSB））。

### 1.5.4 UTF-8 编码字符串 UTF-8 encoded strings

后面会描述的控制报文中的文本字段编码为UTF-8格式的字符串。UTF-8 \[[RFC3629](#RFC3629)\] 是一个高效的Unicode字符编码格式，为了支持基于文本的通信，它对ASCII字符的编码做了优化。

每一个字符串都有一个两字节的长度字段作为前缀，它给出这个字符串UTF-8编码的字节数，它们在[图例 1.1 UTF-8编码字符串的结构](#_Figure_1.1_Structure) 中描述。因此可以传送的UTF-8编码的字符串大小有一个限制，不能超过 65535字节。

除非另有说明，所有的UTF-8编码字符串的长度都必须在0到65535字节这个范围内。

##### 图例 1.1 UTF-8编码字符串的结构 Structure of UTF-8 encoded strings

| **二进制位** | 7-0 |
|--------------|--------------------------------------------|
| byte 1       | 字符串长度的最高有效字节（MSB）            |
| byte 2       | 字符串长度的最低有效字节（LSB）            |
| byte 3 ….    | 如果长度大于0，这里是UTF-8编码的字符数据。 |

<mark>UTF-8编码字符串中的字符数据**必须**是按照Unicode规范 \[[Unicode](#Unicode)\] 定义的和在RFC3629 \[[RFC3629](#RFC3629)\] 中重申的有效的UTF-8格式。特别需要指出的是，这些数据**不能**包含字符码在U+D800和U+DFFF之间的数据。</mark>如果服务端或客户端收到了一个包含无效UTF-8字符的控制报文，该报文就是畸形报文。参阅 [4.13 节](#anchor-4.13)关于错误处理的信息。

<mark>UTF-8 编码的字符串**不能**包含空字符U+0000。</mark>\[MQTT-1.5.4-2\]如果客户端或服务端收到了一个包含U+0000的控制报文，该报文就是畸形报文**必须**关闭。参阅 [4.13 节](#anchor-4.13)关于错误处理的信息。

数据中**不应该**包含下面这些Unicode代码点的编码。如果一个接收者（服务端或客户端）收到了包含下列任意字符的控制报文，它**可以**将其视为畸形报文：

- U+0001 和 U+001F 之间的控制字符
- U+007F 和 U+009F 之间的控制字符
- Unicode 规范定义的非字符代码点（例如U+0FFFF）

<mark>UTF-8编码序列0XEF 0xBB 0xBF总是被解释为U+FEFF（零宽度非换行空白字符），无论它出现在字符串的什么位置，报文接收者都不能跳过或者剥离它</mark>  \[MQTT-1.5.4-3\]。


#### 非规范示例 Non normative example

> 例如，字符串 A𪛔 是一个拉丁字母A后面跟着一个代码点U+2A6D4(它表示一个中日韩统一表意文字扩展B中的字符)，这个字符串编码如下：

##### 图例 1.2 UTF-8编码字符串非规范示例 UTF-8 encoded string non normative example

| **Bit** | **7**                 | **6** | **5** | **4** | **3** | **2** | **1** | **0** |
|---------|-----------------------|-------|-------|-------|-------|-------|-------|-------|
| byte 1  | 字符串长度 MSB (0x00) |
|         | 0                     | 0     | 0     | 0     | 0     | 0     | 0     | 0     |
| byte 2  | 字符串长度 LSB (0x05) |
|         | 0                     | 0     | 0     | 0     | 0     | 1     | 0     | 1     |
| byte 3  | ‘A’ (0x41)            |
|         | 0                     | 1     | 0     | 0     | 0     | 0     | 0     | 1     |
| byte 4  | (0xF0)                |
|         | 1                     | 1     | 1     | 1     | 0     | 0     | 0     | 0     |
| byte 5  | (0xAA)                |
|         | 1                     | 0     | 1     | 0     | 1     | 0     | 1     | 0     |
| byte 6  | (0x9B)                |
|         | 1                     | 0     | 0     | 1     | 1     | 0     | 1     | 1     |
| byte 7  | (0x94)                |
|         | 1                     | 0     | 0     | 1     | 0     | 1     | 0     | 0     |

### 1.5.5 可变字节整形 Variable Byte Integer
可变字节整形使用的编码方案使用最高能表示到 127 的单个字节去编码。较大的值按如下方式处理。每个字节的最低有效7位对数据进行编码，最高有效位用于表明数据表示中是否存在字节。因此，每个字节编码 128 个值和一个“连续位”。 可变字节整形字段中的最大可以有 4 个字节。<mark>编码值必须使用可以表示该值\[MQTT-1.5.5-1\]所需的最小编码数</mark>。这些已经在表 1-1 可变字节整形的大小中展现出来了。

表 1-1 可变字节整形的大小
|------------|------------------------------------|--------------------------------------|
| **Digits** | **From**                           | **To**                               |
|------------|------------------------------------|--------------------------------------|
|          1 | 0 (0x00)                           | 127 (0x7F)                           |
|          2 | 128 (0x80, 0x01)                   | 16,383 (0xFF, 0x7F)                  |
|          3 | 16,384 (0x80, 0x80, 0x01)          | 2,097,151 (0xFF, 0xFF, 0x7F)         |
|          4 | 2,097,152 (0x80, 0x80, 0x80, 0x01) | 268,435,455 (0xFF, 0xFF, 0xFF, 0x7F) |

> **非规范评论**
> 将非负整数（X）编码为可变字节整形编码方案的算法如下：
>
```
do
    encodedByte = X MOD 128 
    X = X DIV 128 
    // if there are more data to encode, set the top bit of this byte 
    if (X > 0) 
        encodedByte = encodedByte OR 128
    endif 'output' encodedByte 
while (X > 0)
```
>MOD是模运算，DIV是整数除法，OR是位操作或（C语言中分别是%，/，|）

>**非规范评注**
>
>可变字节整形字段的解码算法如下：
>
```
multiplier = 1
value = 0
do
    encodedByte = 'next byte from stream'
    value += (encodedByte AND 127) * multiplier
    multiplier *= 128
    if (multiplier > 128*128*128)
       throw Error(Malformed Remaining Length)
while ((encodedByte AND 128) != 0)
```

>AND是位操作与（C语言中的&）

>这个算法终止时，value包含的就是剩余长度的值。

### 1.5.6 二进制数据
二进制数据由两个字节长度整形表示数据的字节数量。由于是两个字节长度，所以二进制数据的大小限制为 65535 字节。

### 1.5.7 UTF-8 字符串对
一个 UTF-8 字符串对包含了两个 UTF-8 编码字符串。这种类型的数据是用来表示键值对的。第一个字符串表示键，第二个字符串表示值。

<mark>这两个字符串都必须遵循 UTF-8 字符串的要求</mark>。\[MQTT-1.5.7-1\]如果接收者（服务器或客户端）收到的字符串对没有满足这些要求，那么包含这些字符串的报文就是畸形报文。关于错误信息处理的详细信息参阅  [4.13](#anchor-4.13)节。

## 1.6 安全性
MQTT 客户端和服务器的实现应该提供认证，授权和安全通信选项，诸如此类都会在第五章中讨论。强烈建议与关键基础设施，个人身份信息或其他个人的或敏感的信息的应用程序使用这些安全功能。

## 1.7 编辑惯例
本规范中用<mark>黄色</mark>高亮文本来标识一致性声明。每个一致性声明都需要以\[MQTT-x.x.x-y\] 的格式被分配一个应用，其中 x.x.x 表示章节数，而 y 表示一节中的某个序号。

## 1.8 更改历史

### 1.8.1 MQTT v3.1.1
MQTT v3.1.1 是 MQTT 的第一个 OASIS 标准版本。**[MQTTV311].**
MQTT v3.1.1 也是 ISO/IEC 标准　20922:2016 [ISO20922].

### 1.8.2 MQTT v5.0
MQTT v5.0 在保留很多核心功能的基础上添加了大量新的功能，主要功能目标是：

 * 增强了扩展性以及大型扩展系统。
 * 改善了错误报告。
 * 形式化了一些通用范式，包括功能发现（capability discovery）和请求响应。
 * 多了扩展机制，包括用户属性（user properties）
 * 性能改进以及对小客户端的支持

关于 MQTT v5.0 的更改摘要，请参阅附录 C。
