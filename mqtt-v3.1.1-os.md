
# MQTT Version 3.1.1

原文地址：http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html

##1.介绍

###1.1 MQTT的组织结构

本规范分为七个主要章节：

- 第一章 \- 介绍
- 第二章 \- MQTT控制包格式
- 第三章 \- MQTT控制包
- 第四章 \- 操作行为
- 第五章 \- 安全
- 第六章 \- 用WebSocket传输
- 第七章 \- 一致性目标

###1.2 术语

规范中的关键字“MUST”，“MUST NOT”，“REQUIRED”，“SHALL”，“SHALL NOT”，“SHOULD”，“SHOULD NOT”，“RECOMMENDED”，“MAY”，“OPTIONAL” 的解释见IETF RFC 2119 \[RFC2119\]。

**网络连接**

MQTT需要底层传输协议提供的构造

- 底层传输协议能够连通客户端和服务端
- 底层传输协议提供有序的，可靠的，双向字节流

详见4.2节

**应用消息**

指通过MQTT在网络中传输的应用程序数据。当应用消息通过MQTT传输的时候会附加上质量服务（QoS）和一个话题名称。

**客户端**

指使用MQTT的程序或设备。客户端总是去连接服务端。它可以

- 发布其他客户端可能会感兴趣的应用消息
- 订阅自己感兴趣的的应用消息
- 退订应用消息
- 从服务端断开连接

**服务端**

扮演订阅或发布应用消息的客户端之间的中间人。一个服务端

- 接受客户端的网络连接
- 接受客户端的应用消息订阅
- 处理客户端订阅和退订的请求
- 转发匹配客户端订阅的应用消息

**订阅**

一个订阅由一个话题过滤器和一个最大的QoS组成。一个订阅关联一个会话。一个会话可以包含多个订阅。每个订阅都有不同的话题过滤器。

**话题名称**

指附着于应用消息的标签，服务端用它来匹配订阅。服务端给每个匹配到的客户端发送一份应用信息的拷贝。

**话题过滤器**

包含在订阅里的一个表达式，来表示一个或多个感兴趣的话题。话题过滤器可以包含通配符。

**会话**

一个有状态的客户端和服务端的交互。一些会话的存续期取决于网络连接，还有一些则可以跨越一个客户端和服务端之间的多个连续的网络连接。

**MQTT控制包**

通过网络连接发送的包含一定信息的数据包。MQTT规范定义了14个不同类型的控制包，其中一个（PUBLISH包）用来传输应用信息。

### 1.3 标准引用

[RFC2119]  
Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels", BCP 14, RFC 2119, March 1997.  
http://www.ietf.org/rfc/rfc2119.txt  
 
[RFC3629]  
Yergeau, F., "UTF-8, a transformation format of ISO 10646", STD 63, RFC 3629, November 2003   
http://www.ietf.org/rfc/rfc3629.txt  
 
[RFC5246]  
Dierks, T. and E. Rescorla, "The Transport Layer Security (TLS) Protocol Version 1.2", RFC 5246, August 2008.  
http://www.ietf.org/rfc/rfc5246.txt  
 
[RFC6455]  
Fette, I. and A. Melnikov, "The WebSocket Protocol", RFC 6455, December 2011.  
http://www.ietf.org/rfc/rfc6455.txt  
 
[Unicode]  
The Unicode Consortium. The Unicode Standard.  
http://www.unicode.org/versions/latest/  

### 1.4 非标准引用

[RFC793]  
Postel, J. Transmission Control Protocol. STD 7, IETF RFC 793, September 1981.  
http://www.ietf.org/rfc/rfc793.txt  
 
[AES]  
Advanced Encryption Standard (AES) (FIPS PUB 197).  
http://csrc.nist.gov/publications/fips/fips197/fips-197.pdf  
 
[DES]  
Data Encryption Standard (DES).  
http://csrc.nist.gov/publications/fips/fips46-3/fips46-3.pdf  
 
[FIPS1402]  
Security Requirements for Cryptographic Modules (FIPS PUB 140-2)  
http://csrc.nist.gov/publications/fips/fips140-2/fips1402.pdf  
 
[IEEE 802.1AR]  
IEEE Standard for Local and metropolitan area networks - Secure Device Identity  
http://standards.ieee.org/findstds/standard/802.1AR-2009.html  
 
[ISO29192]  
ISO/IEC 29192-1:2012 Information technology -- Security techniques -- Lightweight cryptography -- Part 1: General  
http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=56425  
 
[MQTT NIST]  
MQTT supplemental publication, MQTT and the NIST Framework for Improving Critical Infrastructure Cybersecurity  
http://docs.oasis-open.org/mqtt/mqtt-nist-cybersecurity/v1.0/mqtt-nist-cybersecurity-v1.0.html  
 
[MQTTV31]  
MQTT V3.1 Protocol Specification.  
http://public.dhe.ibm.com/software/dw/webservices/ws-mqtt/mqtt-v3r1.html  
 
[NISTCSF]  
Improving Critical Infrastructure Cybersecurity Executive Order 13636  
http://www.nist.gov/itl/upload/preliminary-cybersecurity-framework.pdf  
 
[NIST7628]  
NISTIR 7628 Guidelines for Smart Grid Cyber Security  
http://www.nist.gov/smartgrid/upload/nistir-7628_total.pdf  
 
[NSAB]  
NSA Suite B Cryptography  
http://www.nsa.gov/ia/programs/suiteb_cryptography/  
 
[PCIDSS]  
PCI-DSS Payment Card Industry Data Security Standard  
https://www.pcisecuritystandards.org/security_standards/  
 
[RFC1928]  
Leech, M., Ganis, M., Lee, Y., Kuris, R., Koblas, D., and L. Jones, "SOCKS Protocol Version 5", RFC 1928, March 1996.  
http://www.ietf.org/rfc/rfc1928.txt  
 
[RFC4511]  
Sermersheim, J., Ed., "Lightweight Directory Access Protocol (LDAP): The Protocol", RFC 4511, June 2006.  
http://www.ietf.org/rfc/rfc4511.txt  
 
[RFC5077]  
Salowey, J., Zhou, H., Eronen, P., and H. Tschofenig, "Transport Layer Security (TLS) Session Resumption without Server-Side State", RFC 5077, January 2008.  
http://www.ietf.org/rfc/rfc5077.txt  
 
[RFC5280]  
Cooper, D., Santesson, S., Farrell, S., Boeyen, S., Housley, R., and W. Polk, "Internet X.509 Public Key Infrastructure Certificate and Certificate Revocation List (CRL) Profile", RFC 5280, May 2008.  
http://www.ietf.org/rfc/rfc5280.txt  
 
[RFC6066]  
Eastlake 3rd, D., "Transport Layer Security (TLS) Extensions: Extension Definitions", RFC 6066, January 2011.  
http://www.ietf.org/rfc/rfc6066.txt  
 
[RFC6749]  
Hardt, D., Ed., "The OAuth 2.0 Authorization Framework", RFC 6749, October 2012.  
http://www.ietf.org/rfc/rfc6749.txt  
 
[RFC6960]  
Santesson, S., Myers, M., Ankney, R., Malpani, A., Galperin, S., and C. Adams, "X.509 Internet Public Key Infrastructure Online Certificate Status Protocol - OCSP", RFC 6960, June 2013.  
http://www.ietf.org/rfc/rfc6960.txt  
 
[SARBANES]  
Sarbanes-Oxley Act of 2002.  
http://www.gpo.gov/fdsys/pkg/PLAW-107publ204/html/PLAW-107publ204.htm  
 
[USEUSAFEHARB]  
U.S.-EU Safe Harbor  
http://export.gov/safeharbor/eu/eg_main_018365.asp  

### 1.5 数据表示

#### 1.5.1 位

一个字节有8个位，从0到7。位7是最重要的位，最不重要的是位0。

#### 1.5.2 整型数据值

整型数据值是16位大端序列：高阶字节在低阶字节之前。这意味着一个16位的字被放到网络上的时候，前面是最高有效位，后面是最低有效位。

#### 1.5.3 UTF-8编码字符串

控制包中的文本字段被编码为UTF-8字符串。UTF-8是一种高效的Unicode编码方式，它优化了ASCII字符的编码，来支持基于文本的通信。

每个字符串都有一个两个字节的字段作为前缀，给出UTF-8编码字符串本真的长度。如下图所示（Figure 1.1Structure of UTF-8 encoded strings。因此这种UTF-8编码的字符的大小有一定的限制，编码后不能超过65535个字节。

除非特别说明，所有的UTF-8编码字符串的长度都可以是0到65535个字节。

    Figure 1.1 Structure of UTF-8 encoded strings
    bit     7       6       5       4       3       2       1       0
    byte 1  string length MSB
    byte 2  string length LSB
    byte 3  UTF-8 Encoded CharacterData, if length > 0.

**UTF-8编码的字符串中的字符数据必须是Unicode规范里定义的并在RFC 3629里重申的符合规范的UTF-8。尤其是这些数据不能包含介于U+D800到U+DFFF之间的编码。如果客户端或服务端收到了控制包包含不合规的UTF编码，就必须关闭网络连接。[MQTT-1.5.3-1]**

**UTF-8编码字符串不能包含空字符U+0000。如果收到控制包包含U+0000，服务端或客户端必须关闭网络连接。[MQTT-1.5.3-2]**

数据不应该包含如下编码。如果收到控制包包含下列任一代码，应该关闭网络连接。
    
    U+0001..U+001F 控制字符
    U+007F..U+009F 控制字符
    Unicode规范定义的非字符代码（例如U+0FFFF）

**UTF-8编码序列0xEF 0xBB 0xBF总是被解读为U+FEFF（“零宽度不中断空格”）不论它是否出现在一个字符串中，而且接收方不能跳过或忽略。[MQTT-1.5.3-3].**


###### 1.5.3.1 非标准的例子

例如，字符串A�是一个LATIN CAPITAL字符A，后面是U+2A6D4（代表一个CJK IDEOGRAPH EXTENSION B 字符），编码如下

    Figure 1.2 UTF-8 encoded string no normative example

    |bit     |7       |6       |5       |4       |3       |2       |1       |0
    |byte1   |String Length MSB(0x00)
    |        |0       |0       |0       |0       |0       |0       |0       |0
    |byte2   |String Length LSB(0x05)
    |        |0       |0       |0       |0       |0       |1       |0       |1
    |byte3   |'A'(0x41)
    |        |0       |1       |0       |0       |0       |0       |0       |1
    |byte4   |(0xF0)
    |        |1       |1       |1       |1       |0       |0       |0       |0
    |byte5   |(0xAA)
    |        |1       |0       |1       |0       |1       |0       |1       |0
    |byte6   |(0x9B)
    |        |1       |0       |0       |1       |1       |0       |1       |1
    |byte7   |(0x94)
    |        |1       |0       |0       |1       |0       |1       |0       |0

### 1.6 编写惯例（略）

在本规范中黄色高亮的文本表示一致性描述。每个一致性描述都会被分配一个[MQTT-x.x.x-y]的引用。

----

## 2 MQTT控制包格式

### 2.1 MQTT控制包结构

MQTT通过交换一些预定义的MQTT控制包来工作。这一节描述这些包的格式。
一个MQTT控制包包含三部分，按照下图的顺序Figure 2.1 - Structure of an MQTT Control Packet.

    Figure 2.1 - Structure of an MQTT Control Packet

    |固定包头，存在于所有MQTT控制包
    |可变包头，存在于某些MQTT控制包
    |载荷，存在于某些MQTT控制包

### 2.2 固定包头

每一个MQTT控制包都包含一个固定包头。Figure 2.2 - Fixed header formmat阐明了包头的格式。

    Figure 2.2 - Fixed header format

    |Bit         |7       |6       |5       |4       |3       f2       |1       |0
    |byte 1      |控制包类型                         |每个控制包类型的特定标识
    |byte 2      |剩下的长度

#### 2.2.1 MQTT控制包类型

位置：字节1，位7-4
表现为4位无符号值，如下表Table 2.1 - Control packet types.

    Table 2.1 - Control packet types

    |助记符         |枚举           |流向                   |描述
    |Reserved       |0              |禁用                   |保留
    |CONNECT        |1              |客户端到服务端         |客户端请求连接服务端
    |CONNACK        |2              |服务端到客户端         |连接确认
    |PUBLISH        |3              |双向                   |发布消息
    |PUBACK         |4              |双向                   |发布确认
    |PUBREC         |5              |双向                   |接受到了发布
    |PUBREL         |6              |双向                   |释放了发布
    |PUBCOMP        |7              |双向                   |发布完成
    |SUBSCRIBE      |8              |客户端到服务端         |客户端订阅请求
    |SUBACK         |9              |服务端到客户端         |订阅确认
    |UNSUBSCRIBE    |10             |客户端到服务端         |客户端请求取消订阅
    |UNSUBACK       |11             |服务端到客户端         |取消订阅确认
    |PINGREQ        |12             |客户端到服务端         |PING请求
    |PINGRESP       |13             |服务端到客户端         |PING响应
    |DISCONNECT     |14             |客户端到服务端         |客户端断开连接
    |Reserved       |15             |禁用                   |保留

#### 2.2.2 标识

固定包头字节1中剩下的位[3-0]包含了每个MQTT控制包类型的特殊标识，如下表Table 2,2 - Flag Bits。表中被标识为“预留”的标识位也必须赋值[MQTT-2.2.2-1]。如果收到不可用的标识，接收方必须关闭网络连接。

    Table 2.2 -Flag Bits

    |控制包             |固定头标识             |bit3           |bit2           |bit1           |bit0
    |CONNECT            |Reserved               |0              |0              |0              |0
    |CONNACK            |Reserved               |0              |0              |0              |0
    |PUBLISH            |Used in MQTT 3.1.1     |DUP1           |QoS2           |QoS2           |RETAIN3
    |PUBACK             |Reserved               |0              |0              |0              |0
    |PUBREC             |Reserved               |0              |0              |0              |0
    |PUBREL             |Reserved               |0              |0              |1              |0
    |PUBCOMP            |Reserved               |0              |0              |0              |0
    |SUBSCRIBE          |Reserved               |0              |0              |1              |0
    |SUBACK             |Reserved               |0              |0              |0              |0
    |UNSUBSCRIBE        |Reserved               |0              |0              |1              |0
    |UNSUBACK           |Reserved               |0              |0              |0              |0
    |PINGREQ            |Reserved               |0              |0              |0              |0
    |PINGRESP           |Reserved               |0              |0              |0              |0
    |DISCONNECT         |Reserved               |0              |0              |0              |0

DUP2 = 重复发送PUBLISH控制包
QoS2 = PUBLISH质量服务
RETAIN3 = PUBLISH保留标识
关于PUBLISH控制包的DUP，QoS，以及RETAIN标识，在3.3.1节有详细描述。

#### 2.2.3 剩余长度

位置：从第二个字节开始

剩余长度是指当前包中的剩余字节，包括可变包头的数据以及载荷。剩余长度不包含用来编码剩余长度的字节。

剩余长度使用了一种可变长度的结构来编码，这种结构使用单一字节表示0-127的值。大于127的值如下处理。每个字节的低7位用来编码数据，最高位用来表示是否还有后续字节。因此每个字节可以编码128个值，再加上一个标识位。剩余长度最多可以用四个字节来表示。

>**非规范注释**

>例如十进制的数字64可以被编码成一个单独的字节，十进制为64，八进制为0x40。十进制数字321（=65+2×128）被编码为两个字节，低位在前。第一个字节是65+128 = 193。注意最高位的128表示后面至少还有一个字节。第二个字节是2。（翻译注：321 = 11000001 00000010，第一个字节是“标识符后面还有一个字节”+65，第二个字节是“标识符后面没有字节了”+256）


>**非规范注释**

>这将允许应用发送最多256M大小的控制包。这个数字用16进制表示为：0xFF，0xFF，0xFF，0x7F。Table 2.4 展示了随着字节数的增多，剩余长度可表示的值。

    Table 2.4 Size of Remaining Length field

    |Digits |From                                   |To
    |1      |0 (0x00)                               |127 (0x7F)
    |2      |128 (0x80, 0x01)                       |16 383 (0xFF, 0x7F)
    |3      |16 384 (0x80, 0x80, 0x01)              |2 097 151 (0xFF, 0xFF, 0x7F)
    |4      |2 097 152 (0x80, 0x80, 0x80, 0x01)     |268 435 455 (0xFF, 0xFF, 0xFF, 0x7F)


>**非规范注释**

>编码一个非负整数（X）为可变长度编码结构的算法如下：
    
    do
        encodedByte = X MOD 128
        X = X DIV 128
        // if there are more data to encode, set the top bit of this byte
        if ( X > 0 )
            encodedByte = encodedByte OR 128
        endif
            'output' encodedByte
    while ( X > 0 )

>MOD是取模操作（C语言的%），DIV是整除操作（C语言的/），OR是按位或操作（C语言的|）。


>**非规范注释**

>解码Remaining Length的算法如下：
    
    multiplier = 1
    value = 0
    do
        encodedByte = 'next byte from stream'
        value += (encodedByte AND 127) * multiplier
        multiplier *= 128
        if (multiplier > 128*128*128)
            throw Error(Malformed Remaining Length)
    while ((encodedByte AND 128) != 0)

>AND是按位与操作（C语言的&）。

>算法运行完之后，变量value就是剩余长度的值。


### 2.3 可变包头

某些类型的MQTT控制包包含一个可变包头结构。位于固定包头和载荷之间。可变包头的内容取决于包的类型。可变包头中的包标识符字段在大多类型的包中比较常见。

#### 2.3.1 包唯一标识

    Figure 2.3 -Packet Identifier bytes

    |Bit            |7      |6      |5      |4      |3      |2      |1      |0
    |byte 1         |Packet Identifier MSB
    |byte 2         |Packet Identifier LSB

很多类型的控制包的可变包头结构都包含了2字节的唯一标识字段。这些控制包是PUBLISH（QoS > 0），PUBACK，PUBREC，PUBREL，PUBCOMP，SUBSCRIBE，SUBACK，UNSUBSCRIBE，UNSUBACK。

SUBSCRIBE，UNSUBSCRIBE，PUBLISH（QoS > 0 的时候）控制包必须包含非零的唯一标识[MQTT-2.3.1-1]。每次客户端发送上述控制包的时候，必须分配一个未使用过的唯一标识[MQTT-2.3.1-2]。如果一个客户端重新发送一个特别的控制包，必须使用相同的唯一标识符。唯一标识会在客户端收到相应的确认包之后变为可用。例如PUBLIST在QoS1的时候对应PUBACK；在QoS2时对应PUBCOMP。对于SUBSCRIBE和UNSUBSCRIBE对应SUBACK和UNSUBACK[MQTT-2.3.1-3]。服务端发送QoS>0的PUBLISH时，上述内容同样适用。

QoS为0的PUBLISH包不允许包含唯一标识。

PUBACK，PUBREC，PUBREL包的唯一标识必须和对应的PUBLISH相同[MQTT-2.3.1-6]。同样的SUBACK和UNSUBACK的唯一标识必须与对应的SUBSCRIBE和UNSUBSCRIBE包相同[MQTT-2.3.1-7]。

控制包所需的标识符如下表所列，Table 2.5 - Control Packets that contain a Packet Identifier。

    Table 2.5 - Control Packets that contain a Packet Identifier

    |Control Packet                 |Packet Identifier field
    |CONNECT                        |NO
    |CONNACK                        |NO
    |PUBLISH                        |YES (If QoS > 0)
    |PUBACK                         |YES
    |PUBREC                         |YES
    |PUBREL                         |YES
    |PUBCOMP                        |YES
    |SUBSCRIBE                      |YES
    |SUBACK                         |YES
    |UNSUBSCRIBE                    |YES
    |UNSUBACK                       |YES
    |PINGREQ                        |NO
    |PINGRESP                       |NO
    |DISCONNECT                     |NO

客户端和服务端各自独立分配唯一标识。因此，一对客户端和服务端交换数据的时候可以使用相同的唯一标识。

>**非规范注释**

>有这种可能，客户端发送一个PUBLISH包，唯一标识位0x1234，在收到相应的PUBACK之前，接着先收到了一个服务端发来的不同的PUBLISH包，唯一标识同样是0x1234。

    Client                     Server
    PUBLISH Packet Identifier = 0x1234 ->
    <- PUBLISH Packet Identifier = 0x1234
    PUBACK Packet Identifier = 0x1234 ->
    <- PUBACK Packet Identifier = 0x1234

### 2.4 载荷

一些MQTT控制包的最后一部分包含载荷，第三章会有详细描述。例如PUBLISH包相当于应用消息。Table 2.6 - Control Packets that contain a Payload显示了控制包对载荷的需求。

    Table 2.6 - Control Packets that contain a Payload

    |Control Packet             |Payload
    |CONNECT                    |Required
    |CONNACK                    |None
    |PUBLISH                    |Optional
    |PUBACK                     |None
    |PUBREC                     |None
    |PUBREL                     |None
    |PUBCOMP                    |None
    |SUBSCRIBE                  |Required
    |SUBACK                     |Required
    |UNSUBSCRIBE                |Required
    |UNSUBACK                   |None
    |PINGREQ                    |None
    |PINGRESP                   |None
    |DISCONNECT                 |None

----

## 3 MQTT控制包

### 3.1 CONNECT - 客户端请求连接服务器

客户端和服务端建立网络连接后，第一个从客户端发送给服务端的包必须是CONNECT包[MQTT-3.1.0-1]。

每个网络连接客户端只能发送一次CONNECT包。服务端必须把客户端发来的第二个CONNECT包当作违反协议处理，并断开与客户端的连接[MQTT-3.1.0-2]。错误处理详见4.8节。

载荷包含一个或多个编码字段。用来指定客户端的唯一标识，话题，信息，用户名和密码。除了客户端唯一标识，其他都是可选项，是否存在取决于可变包头里的标识。

#### 3.1.1 固定包头

    Figure 3.1 - CONNECT Packet fixed header

    |Bit     |7       |6       |5       |4       |3       |2       |1       |0
    |byte 1  |MQTT Control Packet type (1)       |Reserved
    |        |0       |0       |0       |1       |0       |0       |0       |0
    |byte 2… |Remaining Length

**Remaining Length**

剩余长度是指可变包头长度（10字节）加上载荷的长度。编码方式的描述见2.2.3节。

#### 3.1.2 可变包头

CONNECT包的可变包头由四个字段按照如下顺序构成：协议名字，协议等，连接标识，保持连接

##### 3.1.2.1 协议名

    Figure 3.2 - Protocol Name bytes

    |                |Description     |7       |6       |5       |4       |3       |2       |1       |0
    |Protocol Name   | 
    |byte 1          |Length MSB (0)  |0       |0       |0       |0       |0       |0       |0       |0
    |byte 2          |Length LSB (4)  |0       |0       |0       |0       |0       |1       |0       |0
    |byte 3          |‘M’             |0       |1       |0       |0       |1       |1       |0       |1
    |byte 4          |‘Q’             |0       |1       |0       |1       |0       |0       |0       |1
    |byte 5          |‘T’             |0       |1       |0       |1       |0       |1       |0       |0
    |byte 6          |‘T’             |0       |1       |0       |1       |0       |1       |0       |0

协议名字是一个UTF-8编码的字符串“MQTT”，全大写。这个字符串的偏移量和长度不会在未来版本的MQTT规范中有所改变。

如果协议名字不正确，服务端可以选择断开连接，也可以选择基于其他规范继续保持连接。后一种情况，服务端就不能按照本规范处理CONNECT包了。

>**非规范注释**

>数据包过滤器，例如防火墙，可以用协议名来鉴别MQTT传输。


##### 3.1.2.2 协议等级

    Figure 3.3 - Protocol Level byte

    |                |Description     |7 |6 |5 |4 |3 |2 |1 |0
    |Protocol        |Level
    |byte 7          |Level(4)        |0 |0 |0 |0 |0 |1 |0 |0 

8位无符号值表示客户端的版本等级。3.1.1版本的协议等级是4（0x04）。如果协议等级不被服务端支持，服务端必须响应一个包含代码0x01（不接受的协议等级）CONNACK包，然后断开和客户端的连接[MQTT-3.1.2-2]。

##### 3.1.2.3 连接标识

连接标识包含了一些参数来指定MQTT的连接行为，它也能表示负载中的字段是否存在。

    Figure 3.4 - Connect Flag bits
    
    |Bit 	|7 			|6 		|5 		|4 |3 		|2 		|1 		|0
    | 		|User Name Flag 	|Password Flag 	|Will Retain 	|Will QoS 	|Will Flag 	|Clean Session 	|Reserved
    |byte 8 	|X 			|X 		|X 		|X |X 		|X 		|X 		|0

服务端必须验证CONNECT控制包的预留字段是否为0，如果不为0断开与客户端的连接[MQTT-3.1.2-3].

##### 3.1.2.4 Clean Session

位置：连接标识字节的bit 1。

这个bit指明了会话状态的处理方式。

客户端和服务端可以存储会话状态，以便能够在一系列的网络连接中可靠的传递消息。这个bit用来控制会话状态的生命周期。

如果CleanSession被设置为0，服务器必须根据当前的会话状态恢复与客户端的通信（客户端的唯一标识作为会话的标识）。如果没有与客户端唯一标识相关的绘画，服务端必须创建一个新的会话。客户端和服务端在断开连接后必须存储会话[MQTT-3.1.2-4]。当CleanSession为0的会话断开后，服务器还必须将所有和客户端订阅相关的QoS1和QoS2的消息作为会话状态的一部分存储起来[MQTT-3.1.2-5]。也可以选择把QoS0的消息也存储起来。

如果CleanSession被设置为1，客户端和服务端必须断开之前的会话启动一个新的会话。只要网络连接存在会话就存在。一个会话的状态数据一定不能被随后的会话复用[MQTT-3.1.2-6]。

客户端会话状态的构成：

- 已经发送到服务端，但没有收到确认的QoS 1和QoS 2消息
- 接收到的从服务端QoS 2消息，还没有收到确认的

服务端会话状态的构成：

- 即使会话状态为空，会话本身也必须存在。
- 客户端的订阅。
- 发送到客户端的但没有得到确认的QoS 1和QoS 2消息。
- 等待发送到客户端的QoS 1和QoS 2消息。
- 从客户端收到的QoS 2消息，但还没有确认的。
- 可选项，等待发送到客户端的QoS 0消息。

在服务端，保留消息不属于会话状态，它们不必在会话结束的时候被删除。

状态的存储细节和限制详见4.1节。

当CleanSession被设置为1，客户端和服务端不需要自动处理状态的删除。

>**非规范注释**

>为了确保在故障的情况下状态一致，客户端应该将CleanSession设置为1重复尝试连接，直到连接成功。


>**非规范注释**

>通常，客户端应该一直使用CleanSession 0或一直使用CleanSession 1来建立连接，而不要切换来切换去。选择哪个由应用程序决定。使用CleanSession 1的客户端不会收到旧的应用消息，而且每次连接都要重新订阅感兴趣的话题。客户端使用CleanSession 0会收到上次短线之后的QoS 1和QoS 2的消息。因此，为了确保消息不会再断线时丢失，使用QoS 1或QoS 2并把CleanSession设为0。


>**非规范注释**

>当客户端使用CleanSession 0建立了连接，在断开连接后，就要求服务端保存它的MQTT会话状态。如果客户端打算在晚些时候重连服务端，那么应该将CleanSession设置为0。当客户端认为没有进一步使用会话的必要了，它应该最后再连接一次服务器，将CleanSession设置为1，然后断开连接。


##### 3.1.2.5 Will Flag

位置：连接标识的bit 2

如果Will Flag被设置为1，这意味着，如果连接请求被接受，服务端必须存储一个Will Message，并和网络连接关联起来。之后在网络连接断开的时候必须发布Will Message，除非服务端收到DISCONNECT包删掉了Will Message[MQTT-3.1.2-8]。

Will Message会在某些情况下发布，包括但不限于：

- 服务端发现I/O错误或网络失败。
- 客户端在Keep Alive时间内通信失败。
- 客户端没有发送DISCONNECT包就关闭了网络连接。
- 服务端因协议错误关闭了网络连接。

如果Will Flag被设置为1，连接标识中的Will QoS和Will Retain字段将会被服务端用到，而且Will Topic和Will Message字段必定会出现在载荷中[MQTT-3.1.2-9]。

Will Message必须从服务端存储的会话状态中移除，一旦被发不过，或者服务端收到了客户端的DISCONNECT包[MQTT-3.1.2-10]。

如果Will Flag被设置为0，连接标识中的Will QoS和Will Retain字段必须设置为零，并且Will Topic和Will Message字段不能够出现在载荷中[MQTT-3.1.2-11]。

如果Will Flag被设置为0，Will Message将不会再网络连接结束的时候发布[MQTT-3.1.2-12]。

服务端应该尽快发布Will Message。例如服务端关闭或故障，只能推迟Will Message的发布，直到服务端重启。如果这种情况发生，就会出现从服务器故障到Will Message被发布之间的延迟。

##### 3.1.2.6 Will QoS

位置：连接标识的bit 4和3

这两个bit表示发布Will Message时使用QoS的等级。

如果Will Flag设置为0，那么Will QoS也必须设置为0[MQTT-3.1.2-13].

如果Will Flag设置为1，那么Will QoS的值可是是0（0x00），1（0x01），2（0x02）。一定不会是3（0x03）[MQTT-3.1.2-14]。

##### 3.1.2.7 Will Retain

位置：连接标识的bit 5。

这个bit表示Will Message在发布之后是否需要保留。

如果Will Flag设置为0，那么Will Retain必须是0[MQTT-3.1.2-15]。

如果Will Flag设置为1：

- 如果Will Retain设置为0，那么服务端必须发布Will Message，不必保存[MQTT-3.1.2-16]。
- 如果Will Retain设置为1，那么服务端必须发布Will Message，并保存[MQTT-3.1.2-17]。

##### 3.1.2.8 User Name Flag

位置：连接标识的bit 7。

如果User Name Flag设置为0，那么用户名不必出现在载荷中[MQTT-3.1.2-18]。

如果User Name Flag设置为1，那么用户名必须出现在载荷中[MQTT-3.1.2-19]。

##### 3.1.2.9 Password Flag

位置：连接标识的bit 6。

如果Password Flag设置为0，那么密码不必出现在载荷中[MQTT-3.1.2-20]。

如果Password Flag设置为1，那么密码必须出现在载荷中[MQTT-3.1.2-21]。

如果User Name Flag设置为0，那么Password Flag必须设置为0[MQTT-3.1.2-22]。

##### 3.1.2.10 Keep Alive

    Figure 3.5 Keep Alive bytes

    |Bit 	|7 |6 |5 |4 |3 |2 |1 |0
    |byte 9 	|Keep Alive MSB
    |byte 10 	|Keep Alive LSB

Keep Alive是以秒为单位的时间间隔。用16-bit字表示，它指的是客户端从发送完成一个控制包到开始发送下一个的最大时间间隔。客户端有责任确保两个控制包发送的间隔不能超过Keep Alive的值。如果没有其他控制包可发，客户端必须发送PINGREQ包[MQTT-3.1.2-23]。

客户端可以在任何时间发送PINGREQ包，不用关心Keep Alive的值，用PINGRESP来判断与服务端的网络连接是否正常。

如果Keep Alive的值非0，而且服务端在一个半Keep Alive的周期内没有收到客户端的控制包，服务端必须作为网络故障断开网络连接[MQTT-3.1.2-24]。

如果客户端在发送了PINGREQ后，在一个合理的时间都没有收到PINGRESP包，客户端应该关闭和服务端的网络连接。

Keep Alive的值为0，就关闭了维持的机制。这意味着，在这种情况下，服务端不会断开静默的客户端。

注意，服务端在任何时候都可以断开它认为不活跃或没有响应的客户端，而不需要遵照客户端提供的Keep Alive。

>**非规范注释**

>Keep Alive的实际值是由应用程序指定的；典型的是几分钟。最大值是18小时12分钟15秒。


##### 3.1.2.11 可变包头的非规范用例

    Figure 3.6 - Variable header non normative example

    | 	 	|Description 		|7 6 5 4 3 2 1 0
    |Protocol Name
    |byte 1 	|Length MSB (0) 	|0 0 0 0 0 0 0 0
    |byte 2 	|Length LSB (4) 	|0 0 0 0 0 1 0 0
    |byte 3 	|‘M’ 			|0 1 0 0 1 1 0 1
    |byte 4 	|‘Q’ 			|0 1 0 1 0 0 0 1
    |byte 5 	|‘T’ 			|0 1 0 1 0 1 0 0
    |byte 6 	|‘T’ 			|0 1 0 1 0 1 0 0
    |Protocol Level
    |byte 7 	|Level (4) 		|0 0 0 0 0 1 0 0
    |Connect Flags 
    |byte 8 	|User Name Flag (1) 	|1 1 0 0 1 1 1 0
    | 		|Password Flag (1)
    | 		|Will Retain (0)
    | 		|Will QoS (01)
    | 		|Will Flag (1)
    | 		|Clean Session (1)
    | 		|Reserved (0)
    |Keep Alive
    |byte 9  	|Keep Alive MSB (0) 	|0 0 0 0 0 0 0 0
    |byte 10 	|Keep Alive LSB (10) 	|0 0 0 0 1 0 1 0

#### 3.1.3 载荷

CONNECT包的载荷可以包含一个或多个带有长度前缀的字段，这取决于可变包头的标识。这些字段，如果存在，必须按照这样的顺序，客户端唯一标识，Will Topic，Will Message，User Name，Password[MQTT-3.1.3-1]。

##### 3.1.3.1 客户端标识

客户端标识（ClientId）可以帮助服务端识别客户端。每一个客户端连接服务端都带有唯一的ClientId。客户端和服务端都必须用ClientId来标识他们所持有的相应的MQTT会话的状态[MQTT-3.1.3-2]。

客户端标识（ClientId）必须存在，而且必须是CONNECT包载荷的第一个字段[MQTT-3.1.3-3]。

ClientId必须是1.5.3节定义的UTF-8编码的字符串[MQTT-3.1.3-4]。

服务端必须能够接纳的ClientId是长度为1到23的UTF-8编码的字节，而且只包含字符“0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ”[MQTT-3.1.3-5]。

服务端也可以接纳大于23个字节编码的ClientId。服务端也可以允许ClientId包含上述字符之外的字符。

服务端可以允许客户端提供0字节长度的ClientId，但是如果这样，服务端必须把这也当作一个特例，作为客户端的唯一ClientId。然后就当作客户端已经提供了唯一ClientId，接着处理CONNECT包[MQTT-3.1.3-6]。

如果客户端提供了一个0字节的CLientId，客户端必须设置CleanSession为1[MQTT-3.1.3-7]。

如果客户端提供了一个0字节的ClientId，而且CleanSession设置为0，那么服务端必须响应一个CONNACK包，带有代码0x02（标识被拒绝），然后关闭网络连接[MQTT-3.1.3-8].

如果服务端拒绝了ClientId，必须返回一个CONNACK包，带有代码0x02（标识被拒绝），然后关闭网络连接[MQTT-3.1.3-9]。

>**非规范注释**

>客户端实现应该提供一个便利的方法生成随机ClientId。当CleanSession设置为0的时候，这种随机的方法应该尽量避免。


##### 3.1.3.2 Will Topic

如果Will Flag设置为1，Will Topic是载荷的下一个字段。Will Topic必须是1.5.3节定义的UTF-8编码的字符串[MQTT-3.1.3-10]。

##### 3.1.3.3 Will Message

如果Will Flag设置为1，Will Message是载荷的iayige字段。Will Message定义了3.1.2.5节中描述的将要发布给Will Topic的应用消息。这个字段的前两个字节表示长度，后面是一段字节序列。长度代表字节序列的字节数，不包括长度本身的两个字节。

当Will Message被发布给Will Topic的时候，只是包括字节序列那部分数据，不包含代表长度的头两个字节。

##### 3.1.3.4 User Name

如果User Name Flag设置为1，这将是载荷的下一个字段。User Name必须是1.5.3节定义的UTF-8编码的字符串[MQTT-3.1.3-11]。服务端根据它来认证和授权。

##### 3.1.3.5 Password

如果Password Flag设置为1，这将是载荷的下一个字段。Password字段包含0-65535字节的二进制数据，数据前面有两个字节来定义二进制数据占用的字节数（不包括这两个字节本身）。

    Figure 3.7 - Password bytes

    |Bit 		|7 	|6 	|5 	|4 	|3 	|2 	|1 	|0
    |byte 1 		|Data length MSB
    |byte 2 		|Data length LSB
    |byte 3 ….  	|Data, if length > 0.

#### 3.1.4 响应

注意，服务端可能在同一个TCP端口或其他网络终端会支持多种协议（包括本协议的早期版本）。如果服务端确认协议是MQTT 3.1.1，那么它必须确认下列连接请求。

1. 如果服务端没有收到CONNECT包，在网络连接建立后的一个合理的时间内，服务端应该关闭网络连接。
2. 服务端必须验证CONNECT包是否符合3.1节的内容，如果不符合，关闭网络连接，不需要发送CONNACK[MQTT-3.1.4-1].
3. 服务端可以对CONNECT包的内容进行进一步的限制验证，可以检查认证和授权。如果任何检查失败，应该发送适合的CONNACK包，带有3.2节描述的非0代码，然后关闭网络连接。

如果验证成功，服务端执行下列步骤。

1. 如果ClientId标识的客户端已经连接了服务端，服务端必须断开已经存在的客户端[MQTT-3.1.4-2]。
2. 服务端必须处理3.1.2.4节描述的CleanSession[MQTT-3.1.4-3]。
3. 服务端必须用一个包含返回码0的CONNACK包作为CONNECT包的确认。
4. 启动消息传递和维持监测。

客户端可以在发送CONNECT包之后立马发送其他控制包；客户端不需要等待CONNACK包。如果服务端拒绝了CONNECT，不要处理客服端在CONNECT包之后发送的任何数据[MQTT-3.1.4-5]。

>**非规范注释**

>客户端通常等待CONNACK包，然而，如果客户端能够灵活地在收到CONNACK包之前就发送控制包，将会简化客户端的实现，因为不需要关心连接状态。客户端要明白的是在没有收到CONNACK包之前发送的数据，如果服务端拒绝连接的话，将不会被处理。


#### 3.2 CONNACK - 确认收到连接请求

CONNACK包是服务端发送的用来相应客户端CONNECT包的一种数据包。从服务端发往客户端的第一个包一定是CONNACK包[MQTT-3.2.0-1]。

如果客户端在一个合理的时间内没有收到服务端的CONNACK包，客户端应该关闭网络连接。“合理”的时间取决于应用的类型和沟通。

##### 3.2.1 固定包头

固定包头的结构如下表所示Figure 3.8 - CONNACK Packet fixed header。

    Figure 3.8 - CONNACK Packet fixed header

    |Bit 	|7 6 5 4 			|3 2 1 0
    |byte 1 	|MQTT Control Packet Type (2) 	|Reserved
    | 		|0 0 1 0 			|0 0 0 0
    |byte 2 	|Remaining Length (2)
    | 		|0 0 0 0 			|0 0 1 0

**Remaining Length**

这个是可变包头的长度。对于CONNACK包来说这个值是2。

#### 3.2.2 可变包头

可变包头的格式如下表 Figure 3.9 - CONNACK Packet variable header。

    Figure 3.9 - CONNACK Packet variable header

    | 		|Description 	|7 |6 |5 |4 |3 |2 |1 	|0
    |Connect Acknowledge Flags 	|Reserved 		|SP1
    |byte 1 			|0 |0 |0 |0 |0 |0 |0 	|X
    |Connect Return code
    |byte 2 			|X |X |X |X |X |X |X 	|X

##### 3.2.2.1 连接确认标识

字节1是“连接确认标识”。位7-1是保留位必须设置为0。

位0（SP1）是Session Present标识

##### 3.2.2.2 Session Present

位置：连接确认标识的位0。

如果服务端接受了一个CleanSession设置为1的连接，服务端必须将CONNACK包中的Session Present设置为0，并且CONNACK包的返回码也设置为0。

如果服务端接受了一个CleanSession设置为0的连接，Session Present的值取决于服务端是否已经存储了客户端Id对应的绘画状态。如果服务端已经存储了会话状态，CONNACK包中的Session Present必须设置为1[MQTT-3.2.2-2]。如果服务端没有存储会话状态，CONNACK包的Session Present必须设置为0。另外CONNACK包中的返回码必须设为0[MQTT-3.2.2-3]。

Session Present标识使得客户端能够建立连接，不论客户端和服务端在是否已经存储了会话状态上达成共识。

一旦会话的初始设置完成，存储会话状态的客户端会期望服务端也存储了会话状态。万一Session Present的值不符合预期，客户端可以选择是继续处理这个会话还是断开连接。客户端可以通过断开连接，把CleanSession设置为1重新连接，然后再断开连接，来决定客户端和服务端的会话状态。

##### 3.2.2.3 连接返回码

可变包头的第二个字节。

无符号单字节连接返回码字段的值如下表所列 Table 3.1 - Connect Return code values。如果服务端收到了一个格式良好的CONNECT包，但是服务端由于某种原因不能处理，那么服务端应该尝试发送一个带有非零返回码的CONNACK包。如果服务端发送了一个包含非零返回码的CONNACK包，必须关闭网络连接[MQTT-3.2.2-5]。

    Table 3.1 - COnnect Return code values

    |Value 	|Return Code Response 					|Description
    |0 		|0x00 Connection Accepted 				|Connection accepted
    |1 		|0x01 Connection Refused, unacceptable protocol version |The Server does not support the level of the MQTT protocol requested by the Client
    |2 		|0x02 Connection Refused, identifier rejected 		|The Client identifier is correct UTF-8 but not allowed by the Server
    |3 		|0x03 Connection Refused, Server unavailable 		|The Network Connection has been made but the MQTT service is unavailable
    |4 		|0x04 Connection Refused, bad user name or password 	|The data in the user name or password is malformed
    |5 		|0x05 Connection Refused, not authorized 		|The Client is not authorized to connect
    |6-255 	|							|Reserved for future use

如果上表中的返回码都不适用，那么服务端必须直接关闭网络连接，不发送CONNACK包[MQTT-3.2.2-6]。

#### 3.2.3 载荷

CONNACK包没有载荷。

### 3.3 PUBLISH - 发布消息

PUBLISH控制包可以从服务端发送给客户端也可以从客户端发送给服务端，来运送应用消息。

#### 3.3.1 固定包头

Figure 3.10 - PUBLISH Packet fixed header 展现了固定包头的格式：

    Figure 3.10 - PUBLISH Packet fixed header

    |Bit 	|7 	|6 	|5 	|4 	|3 		|2 	|1 	|0
    |byte 1 	|MQTT Control Packet type (3) 	|DUP flag 	|QoS level 	|RETAIN
    | 		|0 	|0 	|1 	|1	|X 		|X 	|X 	|X
    |byte 2 	|Remaining Length

##### 3.3.1.1 DUP

位置：byte 1，bit 3。

如果DUP标识被设置为0，标识这是服务端或客户端第一次尝试发送MQTT PUBLISH包。如果DUP标识被设置为1，标识这可能是在重复发送早前尝试发送过的数据包。

DUP标识必须被设置为1，当客户端或是服务端试图重新发送PUBLISH包的时候[MQTT-3.3.1-1]。所有QoS为0的消息DUP标识必须也设置为0[MQTT-3.3.1-2]。

服务端收到带有DUP值的PUBLISH包，当服务端发送这个PUBLISH包给订阅者的时候，DUP的值不会传播。发送的PUBLISH包的DUP标识独立于收到的PUBLISH包的DUP标识，它的值只由是否是重复发送决定[MQTT-3.3.1-3]。

**非规范注释**

接收方在收到DUP标识设置为1的控制包时，不能当作已经收到较早的控制包。

**非规范注释**

重要的是，DUP标识只作用于控制包本身，而不是它包含的应用程序消息。使用QoS 1时，有可能为客户获得发布包DUP标志设置为0，包含一个已经收到过的应用程序的重复信息，但是有不同的数据包标识。2.3.1节提供了更多关于包标识符的信息。

##### 3.3.1.2 QoS

位置：byte 1，bits 2-1。

这个字段表明了应用消息传送的保障水平。QoS等级如下表所列 Table 3.2 - QoS definitions。

    Table 3.2 - QoS definitions

    |QoS value 	|Bit 2 	|bit 1 	|Description
    |0 		|0 	|0 	|At most once delivery
    |1 		|0 	|1 	|At least once delivery
    |2 		|1 	|0 	|Exactly once delivery
    |- 		|-1 	|1 	|Reserved – must not be used

PUBLISH包QoS必须不能设置为1。如果服务端或客户端收到PUBLISH包，PUBLISH包的另个bit都设置为1，必须关闭网络连接。

##### 3.3.1.3 RETAIN

位置：byte 1，bit 0。

如果RETAIN标识被设置为1，在一个从客户端发送到服务端的PUBLISH包中，服务端必须存储应用消息和QoS，以便可以发送给之后订阅这个话题的订阅者[MQTT-3.3.1-5]。当一个新的订阅发生，最后一个保留的消息，如果有的话，而且匹配订阅话题，必须发送给订阅者[MQTT-3.3.1-6]。如果服务端收到一个QoS 0并且RETAIN标识设置为1的消息，它必须清空之前为这个话题保存的所有消息。服务端应该存储新的QoS 0的消息作为这个话题新的保留消息，但是也可以选择在任何时候清空保留消息——如果这样做了，那这个话题就没有保留消息了[MQTT-3.3.1-7]。存储状态详见4.1节。

如果消息是作为客户端新的订阅的结果从服务端发送PUBLISH包给客户端，服务端必须将RETAIN标识设置为1[MQTT-3.3.1-8]。当PUBLISH包发送给客户端，必须设置RETAIN为0，因为不管标识如何设置，它都是已订阅的消息。

RETAIN标识被设置为1，而且载荷包含0个字节的PUBLISH包也会被服务端像平常一样处理，发送给匹配话题的客户端。而且所有这个话题下的保留消息都会被清除，这个话题接下来的订阅者都不会收到保留消息[MQTT-3.3.1-10]. "平常"意味着现存客户端收到的消息RETAIN标识都没有设置。0字节的保留消息一定不会作为保留消息存储在服务端[MQTT-3.3.1-11].

如果RETAIN标识位0，在客户端发送给服务端的PUBLISH包中，服务端一定不能存储这个消息，也一定不能删除或替换任何已存在的保留消息[MQTT-3.3.1-12].

>非规范注释

>在发布者会不定期发送状态消息的地方，保留消息非常有用。新的订阅者会受到最近的状态。

**Remaining Length**

可变包头的长度加上载荷的长度

#### 3.3.2 可变包头

可变包头按顺序包含以下字段：话题名，包唯一标识。

##### 3.3.2.1 话题名

话题名指载荷数据发布的信息通道。

话题名必须是PUBLISH包可变包头的第一个字段。必须是UTF-8编码字符串[MQTT-3.3.2-1]就像1.5.3节定义的。

PUBLISH中的话题名一定不能包含通配符[MQTT-3.3.2-2]。

根据4.7节定义的的匹配过程，服务端发送给客户端的PUBLISH包中的话题名必须匹配订阅话题过滤器[MQTT-3.3.2-3]。但是，因为服务端允许重写话题名，所以可能话题名和最初的PUBLISH包不同。

##### 3.3.2.2 包唯一标识

包唯一标识只在QoS等级为1或2的PUBLISH包中存在。2.3.1节提供了包标唯一标识的更多信息。

##### 3.3.2.3 可变包头的非规范用例

Figure 3.11 - Publish Packet variable header non normative example 展示了一个PUBLISH包可变包头的例子，Table 3.3 - Publish Packet non normative example 是简化的描述。

    Table 3.3 - Publish Packet non normative example

    |Field 			|Value
    |Topic Name 		|a/b
    |Packet Identifier 		|10
    
    Figure 3.11 - Publish Packet variable header non normative example

    | 		|Description 			|7 |6 |5 |4 |3 |2 |1 |0
    |Topic Name
    |byte 1 	|Length MSB (0) 		|0 |0 |0 |0 |0 |0 |0 |0
    |byte 2 	|Length LSB (3) 		|0 |0 |0 |0 |0 |0 |1 |1
    |byte 3 	|‘a’ (0x61) 			|0 |1 |1 |0 |0 |0 |0 |1
    |byte 4 	|‘/’ (0x2F) 			|0 |0 |1 |0 |1 |1 |1 |1
    |byte 5 	|‘b’ (0x62) 			|0 |1 |1 |0 |0 |0 |1 |0
    |Packet Identifier
    |byte 6 	|Packet Identifier MSB (0)  	|0 |0 |0 |0 |0 |0 |0 |0
    |byte 7 	|Packet Identifier LSB (10) 	|0 |0 |0 |0 |1 |0 |1 |0

#### 3.3.3 载荷

载荷包含了发布的应用消息。内容和格式由应用决定。载荷的长度可以由固定包头中的Remaining Length减去可变包头长度得到，也适用于载荷长度为零的PUBLISH包。

#### 3.3.4 响应

PUBLISH包的接收方必须根据Table 3.4 - Expected Publish Packet response，由PUBLISH包的QoS来决定如何响应[MQTT-3.3.4-1]。

    Table 3.4 - Expected Publish Packet response

    |QoS Level 		|Expected Response
    |QoS 0 		|None
    |QoS 1 		|PUBACK Packet
    |QoS 2 		|PUBREC Packet

#### 3.3.5 行为

客户端使用PUBLISH包发送应用消息给服务端，为了分发给匹配订阅的客户端。

服务端使用PUBLISH包发送应用消息给每一个匹配订阅的客户端。

当客户端通过带有通配符的话题过滤器订阅的时候，订阅之间可能会有重叠，以至于发布的消息会匹配多个过滤器。这种情况下，服务端必须使用客户端所有匹配的订阅中的最大的QoS来派发消息[MQTT-3.3.5-1]。而且，服务端可能会发送多个消息副本，每个匹配的订阅使用相应的QoS发送一次。

当收到PUBLISH包的时候，接收者的行为依赖于4.3节描述的QoS等级。

如果服务器实现没有授权客户端执行PUBLISH；没有方法通知那个客户端。服务端要么根据QoS规则做出积极的确认，要么就关闭网络连接[MQTT-3.3.5-2]。

### 3.4 PUBACK - 发布确认

PUBACK包用来响应QoS等级为1的PUBLISH包。

#### 3.4.1 固定包头

    Figure 3.12 - PUBACK Packet fixed header

    |Bit 	|7 6 5 4 			|3 2 1 0
    |byte 1 	|MQTT Control Packet type (4) 	|Reserved
    | 		|0 1 0 0 			|0 0 0 0
    |byte 2 	|Remaining Length (2)
    | 		|0 0 0 0 			|0 0 1 0

**Remaining Length**

可变包头的长度，对PUBACK包来说这个值是2。

#### 3.4.2 可变包头

包含需要确认的那个PUBLISH包的包唯一标识。

    Figure3.13 - PUBACK Packet variable header

    |Bit 	|7 6 5 4 3 2 1 0
    |byte 1 	|Packet Identifier MSB
    |byte 2 	|Packet Identifier LSB

#### 3.4.3 载荷

PUBACK包没有载荷。

#### 3.4.4 行为

完整的描述见4.3.2节。

### 3.5 PUBREC - 发布收到（收到QoS 2的发布，第1部分）

PUBREC包用来响应QoS 2的PUBLISH包。这是QoS 2协议交换的第二个包。

#### 3.5.1 固定包头

    Figure 3.14 – PUBREC Packet fixed header

    |Bit 	|7 6 5 4 			|3 2 1 0
    |byte 1 	|MQTT Control Packet type (5) 	|Reserved
    | 		|0 1 0 1 			|0 0 0 0
    |byte 2 	|Remaining Length(2)
    | 		|0 0 0 0 			|0 0 1 0

**Remaining Length**

可变包头的长度。对PUBREC包来说值为2。

#### 3.5.2 可变包头

包含需要确认的那个PUBLISH包的包唯一标识。

    Figure 3.15 – PUBREC Packet variable header
    
    |Bit 	|7 6 5 4 3 2 1 0
    |byte 1 	|Packet Identifier MSB
    |byte 2 	|Packet Identifier LSB

#### 3.5.3 载荷

PUBREC包没有载荷。

#### 3.5.4 行为

完整的描述见4.3.3节。

### 3.6 PUBREL - Publish release（收到QoS 2的发布，第2部分）

PUBREL包用来响应PUBREC包。是QoS 2协议交换的第三部分。

#### 3.6.1 固定包头

    Figure 3.16 – PUBREL Packet fixed header
    
    |Bit 	|7 6 5 4 			|3 2 1 0
    |byte 1 	|MQTT Control Packet type (6) 	|Reserved
    |       	|0 1 1 0 			|0 0 1 0
    |byte 2 	|Remaining Length (2)
    |       	|0 0 0 0 			|0 0 1 0

PUBREL控制包的固定包头的位3，2，1，0是保留位，而且必须被分别设置为0，0，1，0。服务端必须将其他值作为畸形，并且关闭网络连接[MQTT-3.6.1-1]。

#### 3.6.2 可变包头

可变包头包含需要确认的PUBREC包相同的包唯一和标识。

    Figure 3.17 – PUBREL Packet variable header
    
    |Bit 	|7 6 5 4 3 2 1 0
    |byte 1 	|Packet Identifier MSB
    |byte 2 	|Packet Identifier LSB

#### 3.6.3 载荷

PUBREL包没有载荷

#### 3.6.4 行为

完整的描述见4.3.3节。

### 3.7 PUBCOMP - 发布完成（QoS 2发布接收，第3部分）

PUBCOMP包用来响应PUBREL包。这是QoS 2协议交换的第四个也是最后一个包。

#### 3.7.1 固定包头

    Figure 3.18 – PUBCOMP Packet fixed header
    
    |Bit 	|7 6 5 4 			|3 2 1 0
    |byte 1 	|MQTT Control Packet type (7) 	|Reserved
    | 		|0 1 1 1 			|0 0 0 0
    |byte 2 	|Remaining Length (2)
    | 		|0 0 0 0 			|0 0 1 0

**Remaining Length**

这是可变包头的禅固定，对于PUBCOMP包来说值为2。

#### 3.7.2 可变包头

可变包头包含需要确认的PUBREL包相同的包唯一和标识。

    Figure 3.19 – PUBCOMP Packet variable header

    |Bit 	|7 6 5 4 3 2 1 0
    |byte 1 	|Packet Identifier MSB
    |byte 2 	|Packet Identifier LSB

#### 3.7.3 载荷

PUBCOMP包没有载荷

#### 3.7.4 行为

完整的表述见4.3.3节。

### 3.8 SUBSCRIBE - 订阅话题

SUBSCRIBE包从客户端发送到服务端创建一个或多个订阅。每个订阅注册客户端感兴趣的一个或多个话题。服务端向客户端发送PUBLISH包来分发匹配订阅的应用消息。SUBSCRIBE包也（为每个订阅）指定服务端发送给客户端的应用消息的最大QoS。

#### 3.8.1 固定包头

    Figure 3.20 – SUBSCRIBE Packet fixed header
    
    |Bit 	|7 6 5 4 			|3 2 1 0
    |byte 1 	|MQTT Control Packet type (8) 	|Reserved
    | 		|1 0 0 0 			|0 0 1 0
    |byte 2 	|Remaining Length

SUBSCRIBE控制包的固定包头的3，2，1，0位是保留位，而且必须设置为0，0，1，0。服务端必须把其他值作为畸形对待，然后关闭网络连接[MQTT-3.8.1-1]。

**Remaining Length**

可变包头的长度（2个字节）再加上载荷的长度。

#### 3.8.2 可变包头

可变包头包含包唯一标识。2.3.1节提供了包唯一标识的更多信息。

##### 3.8.2.1 可变包头的非规范用例

Figure 3.21展示了带有包唯一标识10的可变包头。

    Figure 3.21 - Variable header with a Packet Identifier of 10, Non normative example
     
    | 		|Description 			|7 6 5 4 3 2 1 0
    |Packet Identifier
    |byte 1 	|Packet Identifier MSB (0) 	|0 0 0 0 0 0 0 0
    |byte 2 	|Packet Identifier LSB (10) 	|0 0 0 0 1 0 1 0

#### 3.8.3 载荷

SUBSCRIBE包的载荷包含了话题过滤器的列表，指示客户端想要订阅的主题。SUBSCRIBE包载荷中的主体过滤器必须是1.5.3节定义的UTF-8编码字符串[MQTT-3.8.3-1]。服务端应该支持4.7.1节中定义的包含通配符的话题过滤器。如果选择不支持带有通配符的话题过滤器，就必须拒绝所有带有通配符过滤器的订阅[MQTT-3.8.3-2]。每个过滤器后面都带有一个字节的QoS。这指定了服务端发送给客户端应用消息的最大QoS等级。

SUBSCRIBE包的载荷至少包含一个成对的话题过滤器/QoS。没有载荷的SUBSCRIBE包是违反协议的[MQTT-3.8.3-3]。错误处理见4.8节。

在UTF-8编码的话题名之后是一个字节的最大QoS字段，这些话题过滤器/QoS对一个接一个地连续的被封装。

    Figure 3.22 – SUBSCRIBE Packet payload format
    
    |Description 	|7 6 5 4 3 2 	|1 0
    |Topic Filter
    |byte 1 		|Length MSB
    |byte 2 		|Length LSB
    |bytes 3..N 	|Topic Filter
    |Requested QoS
    | 			|Reserved 	|QoS
    |byte N+1 		|0 0 0 0 0 0 	|X X

QoS字节的高6位在当前版本的协议中没有使用。这是为未来预留的。如果载荷中预留的位非零，或者QoS不是0，1，2，那么服务端必须把SUBSCRIBE包当作畸形关闭网络连接[MQTT-3.8.3-4]。

##### 3.8.3.1 载荷非规范用例

Figure 3.23 - Payload byte format non normative example显示了SUBSCRIBE包的载荷，简短描述见Table 3.5 - Payload non normative example。

    Table 3.5 - Payload non normative example
    
    |Topic Name 	|“a/b”
    |Requested QoS 	|0x01
    |Topic Name 	|“c/d”
    |Requested QoS 	|0x02

    Figure 3.23 - Payload byte format non normative example
    
    | 		|Description 		|7 6 5 4 3 2 1 0
    |Topic Filter
    |byte 1 		|Length MSB (0) 	|0 0 0 0 0 0 0 0
    |byte 2 		|Length LSB (3) 	|0 0 0 0 0 0 1 1
    |byte 3 		|‘a’ (0x61) 		|0 1 1 0 0 0 0 1
    |byte 4 		|‘/’ (0x2F) 		|0 0 1 0 1 1 1 1
    |byte 5 		|‘b’ (0x62) 		|0 1 1 0 0 0 1 0
    |Requested QoS
    |byte 6 		|Requested QoS(1) 	|0 0 0 0 0 0 0 1
    |Topic Filter
    |byte 7 		|Length MSB (0) 	|0 0 0 0 0 0 0 0
    |byte 8 		|Length LSB (3) 	|0 0 0 0 0 0 1 1
    |byte 9 		|‘c’ (0x63) 		|0 1 1 0 0 0 1 1
    |byte 10 	|‘/’ (0x2F) 		|0 0 1 0 1 1 1 1
    |byte 11 	|‘d’ (0x64) 		|0 1 1 0 0 1 0 0
    |Requested QoS
    |byte 12 	|Requested QoS(2) 	|0 0 0 0 0 0 1 0

#### 3.8.4 响应

当服务端收到客户端发来的SUBSCRIBE包，服务端必须响应一个SUBACK包[MQTT-3.8.4-1]。SUBACK包必须包含与相应SUBSCRIBE包相同的包唯一标识[MQTT-3.8.4-2]。

服务端在发送SUBACK包之前，就可以开始发送订阅的PUBLISH包。

如果服务端收到一个SUBSCRIBE包，其中包含的话题过滤器与一个已经存在的话题过滤器完全相同，服务端必须用新的订阅替换已经存在的订阅。新的订阅中的话题过滤器与之前订阅中的完全一样，但是最大QoS的值可能不同。所有保存的符合该话题过滤器的消息必须被重发，但是发布流一定不能被打断[MQTT-3.8.4-3]。

如果话题过滤器与已存在订阅的过滤器都不相同，新的订阅会被创建，并且所有的匹配的保留消息都会被发送。

如果服务端收到的SUBSCRIBE包包含多个话题过滤器，必须像顺序的收到多个SUBSCRIBE包一样处理它，然后再把所有的响应合并为一个SUBACK响应[MQTT-3.8.4-4]。

从服务端发送到客户端的SUBACK包中，每一个话题过滤器/QoS对都有一个对应的返回码。返回码必须是这次订阅的最大QoS，否则就订阅失败[MQTT-3.8.4-5]。服务端可能会授予一个较低等级的最大QoS，相比较于订阅者要求的。订阅响应的载荷消息的QoS必须是原始发布消息的最小QoS，而且是服务端授予的最大QoS。服务端可以给订阅者发布多个消息拷贝，来应对原始消息使用QoS 1发布，最大QoS为QoS 0的情况[MQTT-3.8.4-6]。

**非规范用例**

如果有一个订阅客户端的一个话题过滤器的最大QoS为1，然后有一个QoS为0的符合过滤器的应用消息，这个消息被发送给客户端的时候QoS也是0。这意味着至少有一份消息拷贝会被客户端收到。另一方面，如果匹配相同话题过滤器的消息的QoS为2，服务端会把它降级为QoS为1的消息分发给客户端，客户端可能会收到多份消息的拷贝。

如果订阅客户端的QoS为0，应用消息的原始QoS为2，在发送给客户端的时候可能会丢失消息，但是服务端永远都不应该重复发送这个消息。相同话题的QoS为1的消息可能会丢失，也可能会重复发送给客户端。

**非规范注释**

使用QoS为2的话题过滤器进行订阅，相当于说"我希望收到匹配过滤器的消息，不要改变他们发布时的QoS"。这就意味着发布者有责任决定消息被派发的最大QoS，而订阅者可以要求服务端降级QoS到更适合的等级。

### 3.9 SUBACK - 订阅确认

SUBACK包从服务端发送给客户端，用来确认收到并处理了SUBSCRIBE包。

SUBACK包包含了返回码的列表，指定了SUBSCRIBE包中的每个订阅的最大QoS等级。

#### 3.9.1 固定包头

    Figure 3.24 – SUBACK Packet fixed header
    
    |Bit 	|7 6 5 4 			|3 2 1 0
    |byte 1 	|MQTT Control Packet type (9) 	|Reserved
    | 		|1 0 0 1 			|0 0 0 0
    |byte 2 	|Remaining Length

**Remaining Length**

可变包头的长度（2字节）再加上载荷的长度。

#### 3.9.2 可变包头

可变包头包含了需要确认的SUBSCRIBE包的包唯一标识。Figure 3.25 - variable header format 展示了可变包头的格式。

    Figure 3.25 – SUBACK Packet variable header
    
    |Bit 	|7 6 5 4 3 2 1 0
    |byte 1 	|Packet Identifier MSB
    |byte 2 	|Packet Identifier LSB

#### 3.9.3 载荷

载荷包含了返回码的列表。每个返回码对应SUBSCRIBE包中需要被确认的一个话题过滤器。SUBACK包中的返回码的顺序必须匹配SUBSCRIBE包中的话题过滤器的顺序[MQTT-3.9.3-1]。

Figure 3.26 - Payload format 展示了载荷中编码在一个字节中的返回码字段。

    Figure 3.26 – SUBACK Packet payload format
    
    |Bit 	|7 6 5 4 3 2 1 0
    | 		|Return Code
    |byte 	|1 X 0 0 0 0 0 X X

允许的返回码：

0x00 - 成功 - 最大QoS为0
0x01 - 成功 - 最大QoS为1
0x02 - 成功 - 最大QoS为2
0x80 - 失败

除了0x00,0x01,0x02,0x80之外的SUBACK返回码是保留的，而且不允许使用[MQTT-3.9.3-2]。

##### 3.9.3.1 载荷的非规范用例

Figure 3.27 - Payload byte format non normative example 展示了SUBACK包的载荷，简化描述见Table 3.6 - Payload non normative example。

    Table 3.6 - Payload non normative example
    
    |Success - Maximum QoS 0 	|0
    |Success - Maximum QoS 2 	|2
    |Failure 			|128

    Figure 3.27 - Payload byte format non normative example
    
    | 		|Description 			|7 6 5 4 3 2 1 0
    |byte 1 	|Success - Maximum QoS 0 	|0 0 0 0 0 0 0 0
    |byte 2 	|Success - Maximum QoS 2 	|0 0 0 0 0 0 1 0
    |byte 3 	|Failure 			|1 0 0 0 0 0 0 0

### 3.10 UNSUBSCRIBE - 退订话题

UNSUBSCRIBE包从客户端发往服务端，用来退订话题。

#### 3.10.1 固定包头

    Figure 3.28 – UNSUBSCRIBE Packet Fixed header
    
    |Bit	|7 6 5 4 			|3 2 1 0
    |byte 1 	|MQTT Control Packet type (10) 	|Reserved
    | 		|1 0 1 0 			|0 0 1 0
    |byte 2 	|Remaining Length

UNSUBSCRIBE控制包固定包头的3，2，1，0位是预留的，而且必须分别被设置为0，0，1，0。服务端必须把其他值作为畸形，并且关闭网络连接[MQTT-3.10.1-1]。

**Remaining Length**

可变包头的长度（2字节）再加上载荷的长度。

#### 3.10.2 可变包头

可变包头包含了包唯一标识。2.3.1节提供了包唯一标识的更多信息。

    Figure 3.29 – UNSUBSCRIBE Packet variable header
    
    |Bit 	|7 6 5 4 3 2 1 0
    |byte 1 	|Packet Identifier MSB
    |byte 2 	|Packet Identifier LSB

#### 3.10.3 载荷

UNSUBSCRIBE包包含了客户端希望退订的话题过滤器列表。UNSUBSCRIBE包中的被连续打包的话题过滤器必须是1.5.3节定义的UTF-8编码字符串[MQTT-3.10.3-1]。

UNSUBSCRIBE包的载荷必须包含至少一个话题过滤器。没有载荷的UNSUBSCRIBE包是违反协议的[MQTT-3.10.3-2]。关于错误处理详见4.8节。

##### 3.10.3.1 载荷非规范用例

Figure 3.30 - Payload byte format non normative example 显示了UNSUBSCRIBE包的载荷，简化描述见Table3.7 - Payload non normative example。

    Table3.7 - Payload non normative example
    
    |Topic Filter 	|“a/b”
    |Topic Filter 	|“c/d”

    Figure 3.30 - Payload byte format non normative example
    
    | 		|Description 		|7 6 5 4 3 2 1 0
    |Topic Filter
    |byte 1 	|Length MSB (0) 	|0 0 0 0 0 0 0 0
    |byte 2 	|Length LSB (3) 	|0 0 0 0 0 0 1 1
    |byte 3 	|‘a’ (0x61) 		|0 1 1 0 0 0 0 1
    |byte 4 	|‘/’ (0x2F) 		|0 0 1 0 1 1 1 1
    |byte 5 	|‘b’ (0x62) 		|0 1 1 0 0 0 1 0
    |Topic Filter
    |byte 6 	|Length MSB (0) 	|0 0 0 0 0 0 0 0
    |byte 7 	|Length LSB (3) 	|0 0 0 0 0 0 1 1
    |byte 8 	|‘c’ (0x63) 		|0 1 1 0 0 0 1 1
    |byte 9 	|‘/’ (0x2F) 		|0 0 1 0 1 1 1 1
    |byte 10 	|‘d’ (0x64) 		|0 1 1 0 0 1 0 0

#### 3.10.4 响应

UNSUBSCRIBE包中的话题过滤器（无论是否包含通配符），都必须逐个字符的与客户端在服务器中的现有订阅集合做比较。如果过滤器匹配成功，那么删除相应的订阅，否则就不处理[MQTT-3.10.4-1]。

如果服务端删除了订阅：

- 必须停止追加新的派发给客户端的消息[MQTT-3.10.4-2]。
- 必须完成所有已经开始发送给客户端的QoS 1或QoS 2消息的派发。
- 可以继续发送派发消息队列中已经存在的消息给客户端。

服务端必须发送UNSUBACK包来响应UNSUBSCRIBE。UNSUBACK包必须和UNSUBSCRIBE包有相同的包唯一标识[MQTT-3.10.4-4]。即使没有话题订阅被删除，服务端也必须响应一个UNSUBACK[MQTT-3.10.4-5]。

如果服务端收到一个UNSUBSCRIBE包，包含多个话题过滤器，服务端必须像按顺序收到多个UNSUBSCRIBE包一样来处理，但是只发送一个UNSUBACK包来响应[MQTT-3.10.4-6]。

### 3.11 UNSUBACK - 退订确认

UNSUBACK包从服务端发往客户端来确认收到UNSUBSCRIBE包。

#### 3.11.1 固定包头

    Figure 3.31 – UNSUBACK Packet fixed header
    
    |Bit 	|7 6 5 4 			|3 2 1 0
    |byte 1 	|MQTT Control Packet type (11) 	|Reserved
    | 		|1 0 1 1 			|0 0 0 0
    |byte 2 	|Remaining Length (2)
    | 		|0 0 0 0 			|0 0 1 0

**Remaining Length**

可变包头的长度，对UNSUBACK包来说值为2。

#### 3.11.2 可变包头

可变包头包含了需要确认的UNSUBSCRIBE包的包唯一标识。

    Figure 3.32 – UNSUBACK Packet variable header
    
    |Bit 	|7 6 5 4 3 2 1 0
    |byte 1 	|Packet Identifier MSB
    |byte 2 	|Packet Identifier LSB

#### 3.11.3 载荷

UNSUBACK包没有载荷

### 3.12 PINGREQ - PING请求

PINGREQ包从客户端发往服务端，可以用来：

1. 在没有其他控制包从客户端发送给服务端的时候，告知服务端客户端的存活状态。
2. 请求服务端响应，来确认服务端是否存活。
3. 确认网络连接的有效性。

这个包在Keep Alive处理中用到，更多的细节见3.1.2.10节。

#### 3.12.1 固定包头

    Figure 3.33 – PINGREQ Packet fixed header
    
    |Bit 	|7 6 5 4 			|3 2 1 0
    |byte 1 	|MQTT Control Packet type (12) 	|Reserved
    | 		|1 1 0 0 			|0 0 0 0
    |byte 2 	|Remaining Length (0)
    | 		|0 0 0 0 			|0 0 0 0

#### 3.12.2 可变包头

PINGREQ包没有可变包头。

#### 3.12.3 载荷

PINGREQ包没有载荷。

#### 3.12.4 响应

服务端必须发送PINGRESP包响应PINGREQ包[MQTT-3.12.4-1]。

### 3.13 PINGRESP - PING响应

PINGRESP包从服务端发送给客户端来响应PINGREQ包。它代表服务端是存活的。

这个包在Keep Alive处理中用到，更多的细节见3.1.2.10节。

#### 3.13.1 固定包头

    |Figure 3.34 – PINGRESP Packet fixed header

    |Bit 	|7 6 5 4 			|3 2 1 0
    |byte 1 	|MQTT Control Packet type (13) 	|Reserved
    | 		|1 1 0 0 			|0 0 0 0
    |byte 2 	|Remaining Length (0)
    | 		|0 0 0 0 			|0 0 0 0

#### 3.13.2 可变包头

PINGRESP包没有可变包头。

#### 3.13.3 载荷

PINGRESP包没有载荷。

### 3.14 DISCONNECT - 断开连接通知

DISCONNECT包是客户端发给服务端的最后一个控制包。它表示客户端正在干净利索的断开连接。

#### 3.14.1 固定包头

    |Figure 3.35 – DISCONNECT Packet fixed header

    |Bit 	|7 6 5 4 			|3 2 1 0
    |byte 1 	|MQTT Control Packet type (14) 	|Reserved
    | 		|1 1 0 0 			|0 0 0 0
    |byte 2 	|Remaining Length (0)
    | 		|0 0 0 0 			|0 0 0 0

#### 3.14.2 可变包头

DISCONNECT包没有可变包头。

#### 3.14.3 载荷

DISCONNECT包没有载荷。

#### 3.14.4 响应

客服端发送DISCONNECT包之后：

- 必须关闭网络连接[MQTT-3.14.4-1]。
- 不允许在这个网络连接上再发送任何控制包[MQTT-3.14.4-2]。

服务端收到DISCONNECT包：

- 必须丢弃所有和当前连接有关的Will Message，不要发布，就像3.1.2.5节描述的[MQTT-3.14.4-3]。 
- 应该关闭网络连接，如果客户端还没有这么做。

----

## 4 操作行为

### 4.1 存储状态

客户端和服务端为了提供Quality of Service（QoS）的保障，存储Session状态是有必要的。客户端和服务端必须在整个Session期间都存储Session状态[MQTT-4.1.0-1]。只要有效的网络连接存在，Session就必须存在[MQTT-4.1.0-2]。

保留消息不是服务端Session状态的组成部分。服务端应该保留这些消息直到被客户端删除。

**非规范注释**

客户端和服务端存储功能的实现当然会受限于容量或者可能是管理策略，例如网络连接Session存储的最大时间。被存储的Session状态可以因为管理员的行为被丢弃，包括对已定义条件的自动响应。终止Session会产生一定的影响。这些动作可能是因为资源受限或其他操作理由。应该谨慎的评估客户端和服务端的存储能力来确保他们是充足的。

**非规范注释**

硬件或软件的崩溃都有可能使存储在客户端或服务端的Session状态丢失或损坏。

**非规范注释**

正常的客户端服务端操作可能会因为管理员行为，硬件故障，软件故障导致存储的状态丢失或损坏。管理员行为可能是对已定义条件的自动响应。这些行为可能是因为资源受限或其他操作理由。例如服务端可能基于外部知识决定一条过多条消息不再分发给当前或未来的客户端。

**非规范注释**

MQTT使用者应该评估MQTT客户端和服务单的存储功能的实现，以确保能够满足他们的需求。

#### 4.1.1 非规范用例

例如，一个希望收集电表读数的用户可能决定使用QoS 1的消息，因为他们需要保证读数在网络传输过程中不能丢失，他们确定供电是充足可靠的，所以客户端和服务端的数据存储在易失性的内存中丢失的风险也不会太大。

相反一个停车付款的应用提供者可能认为付款消息绝不能丢失，所以他们要求所有的数据在通过网络发送之前都要强制写入非易失性的内存中去。

### 4.2 网络连接

MQTT协议需要一个底层传输层来提供有序，可靠的客户端和服务器之间的字节流。

**非规范注释**

用来搭载MQTT 3.1的传出协议曾经是[RFC793]定义的TCP/IP。MQTT 3.1.1也可以使用TCP/IP。以下也适用：

- TLS [RFC5246]
- WebSocket [RFC6455]

**非规范注释**

TCP端口8883和1883是IANA为MQTT TLS和非TLS通信注册的。

无连接的网络传输例如用户数据包协议（UDP）不适用，因为它们可能丢失或重排数据。

### 4.3 质量服务等级和协议流

MQTT根据质量服务（QoS）等级分发应用消息。分发协议是对称的，客户端和服务端都既可以是发送者也可以是接收者。分发协议唯一关心的是应用消息的分发是从一个单一的发送者到一个单一的接收者。当服务端分发一个应用消息给多个客户端，每个客户端都被当作是独立的。向外分发应用消息给客户端的QoS等级可能与收到应用消息的QoS等级有所不同。

下面小节的非规范的流程图展示了可能的实现方式。

#### 4.3.1 QoS 0: 最多分发一次

消息分发以来底层网络的性能。接收者不发送响应，发送者也不重新尝试。接收者只能收到一次消息，或者一次也收不到。

在QoS 0的分发协议中，发送者

- 必须发送带有QoS=0，DUP=0的PUBLISH包[MQTT-4.3.1-1].

在QoS 0的分发协议中，接收者

- 接收者在收到PUBLISH包的时候就相当于接受了所有权。

    Figure 4.1 – QoS 0 protocol flow diagram, non normative example
    
    |Sender Action 		|Control Packet 		|Receiver Action
    |PUBLISH QoS 0, DUP=0 	| 				|
    | 				|----------> 			|
    | 				|				|Deliver Application Message to appropriate onward recipient(s)

#### 4.3.2 QoS 1:最少分发一次

这种情况的质量服务确保消息至少一次抵达接收者。QoS 1的PUBLISH包的可变包头包含包唯一标识，而且有PUBACK包确认。2.3.1节提供了更多包唯一标识的信息。

在QoS 1的分发协议中，发送者

- 每次发布新的应用消息，必须分配一个没有用过的包唯一标识。
- 包含唯一标识的PUBLISH包也必须是QoS=1，DUP=0。
- 必须把PUBLISH包当作“未确认的”，直到从接收者那里收到对应的PUBACK包。未确认消息的讨论见4.4节。

[MQTT-4.3.2-1]。

一旦发送者收到PUBACK包，包唯一标识就可以重用了。

注意，发送者在鞥带接收确认的时候，可以发送不同包唯一标识的PUBLISH包。

在QoS 1的分发协议中，接收者

- 必须响应一个包含与PUBLISH包相同的包唯一标识，来接受应用消息的所属关系。
- 在发送PUBACK包之后，接收者必须把所有收到的包含相同包唯一标识的PUBLISH包当作一个新的发布，无论是否设置了DUP标识。

[MQTT-4.3.2-2]。

    Figure 4.2 – QoS 1 protocol flow diagram, non normative example
    
    |Sender Action 					|Control Packet 	|Receiver action
    |Store message 					| 			|
    |Send PUBLISH QoS 1, DUP 0, <Packet Identifier> 	|----------> 		|
    | 							| 			|Initiate onward delivery of the Application Message1
    | 							|<---------- 		|Send PUBACK <Packet Identifier>
    |Discard message 					| 			|

><sup>1<sup>接收者不需要在发送PUBACK之前完整的分发应用消息。当原始的发送者收到PUBACK包，应用消息的所属关系就转给了接收者。

#### 4.3.3 QoS 2 精确一次分发

这是最高的服务质量，用在丢失和重复消息都不能被接受的情况。这种服务质量会增加开销。

QoS 2消息的可变包头包含包唯一标识。2.3.1节提供了更多关于包唯一标识的信息。QoS 2的PUBLISH包的接收者分两步确认接收。

在QoS 2的分发协议中，发送者

- 每次发布新的应用消息，必须分配一个没有用过的包唯一标识。
- 包含唯一标识的PUBLISH包也必须是QoS=1，DUP=0。
- 必须把PUBLISH包当作“未确认的”，直到从接收者那里收到对应的PUBACK包。未确认消息的讨论见4.4节。
- 当从接收者那里收到PUBREC包时要发送PUBREL包。PUBREL包必须包含与原始PUBLISH包相同的包唯一标识。
- 必须把PUBREL包当作“未确认的”，直到从接收者那里收到对应的PUBCOMP包。
- 一旦已经发送了相应的PUBREL包，就不能再重发PUBLISH包。

[MQTT-4.3.3-1]。

当发送者收到PUBCOMP包之后，包唯一标识就可以重用了。

注意，发送者在鞥带接收确认的时候，可以发送不同包唯一标识的PUBLISH包。

在QoS 2的分发协议中，接收者

- 必须响应一个包含与PUBLISH包相同的包唯一标识，来接受应用消息的所属关系。
- 直到收到相应的PUBREL包，接收者必须发送包含相同包唯一标识的PUBREC来确认后来的PUBLISH包。这样一定不会导致重复的消息被分发到接下来的接收者。
- 必须用包含相同包唯一标识的PUBCOMP包来响应一个PUBREL包。
- 在发送PUBCOMP之后，接收者必须把所有收到的包含相同包唯一标识的PUBLISH包当作一个新的发布。

[MQTT-4.3.3-2]。

    Figure 4.3 – QoS 2 protocol flow diagram, non normative example
    
    |Sender Action 						|Control Packet 	|Receiver Action
    |Store message 					        | 			| 
    |PUBLISH QoS 2, DUP 0 <Packet Identifier> 			|			| 
    | 								|----------> 		|
    | 								|			|Method A, Store message or Method B, Store <Packet Identifier> then Initiate onward delivery of the Application Message1
    | 								|			|PUBREC <Packet Identifier>
    | 								|<---------- 		|
    |Discard message, Store PUBREC received <Packet Identifier> |			|
    |PUBREL <Packet Identifier> 				| 			|
    | 								|----------> 		|
    | 								|			|Method A, Initiate onward delivery of the Application Message1  then discard message or Method B, Discard <Packet Identifier>
    | 								|			|Send PUBCOMP <Packet Identifier> 
    | 								|<----------  		|
    |Discard stored state 					| 			|

><sup>1<sup>接收者不需要在发送PUBACK之前完整的分发应用消息。当原始的发送者收到PUBACK包，应用消息的所属关系就转给了接收者。

>Figure 4.3展示了接收者能处理QoS 2的两种方法。流程中不同哦一点是在哪一步开始继续向下分发。选择方法A或方法B由实现决定。只要实现选择一种方法去处理，就不会影响QoS 2流程哦可靠性。

### 4.4 消息分发重试

当客户端将CleanSession设置为0进行重连，客户端和服务端都不许重发未确认的PUBLISH包（当QoS > 0时），PUBREL包使用原始包唯一标识[MQTT-4.4.0-1]。这是客户端和服务端被要求重发消息的唯一情况。

**非规范注释**

控制包的重传在历史上是为了克服在古老哦TCP网络上丢失数据。现在这也是一个问题，MQTT3.1.1的实现有可能部署在这样的环境中。

### 4.5 消息接收

当服务端获得了发来的应用消息的所有权，必须追加到那些符合订阅的客户端的Session状态中。匹配规则定义在4.7节[MQTT-4.5.0-1]。

在正常的环境下，客户端接收已创建订阅的消息。客户端也会收到一些没有订阅的消息。如果服务端自动指派了一个订阅给客户端就会出现何种情况。当UNSUBSCRIBE操作在处理中的时候，客户端也可能收到这个订阅的消息。客户端必须根据QoS规则确认所有收到的PUBLISH包，不论是否处理应用消息的内容[MQTT-4.5.0-2]。

### 4.6 消息顺序

在实现本章定义的协议流程的时候，客户端必须遵循如下规则：

- 当重发PUBLISH包的时候，必须按照原始PUBLISH包的发送顺序重发它们（作用于QoS 1和QoS 2的消息）[MQTT-4.6.0-1]
- 必须按照收到PUBLISH包的顺序发送相应的PUBACK包
- 必须按照收到PUBLISH包的顺序发送相应的PUBREC包
- 必须按照收到PUBREC包的顺序发送相应的PUBREL包

服务端必须默认把每个话题都当作“有序话题”。可以提供管理上或其他机制允许的一个或多个话题作为“无序话题”[MQTT-4.6.0-5]。

当服务端处理一个被发布给有序话题的消息时，在分发消息给订阅者时，必须遵循上述规则。而且必须按照收到PUBLISH包的顺序把它们发送给消费者（相同的话题和QoS）[MQTT-4.6.0-6]。

**非规范注释**

上述规则保证了，当一个QoS 1订阅的消息流被发布，订阅者收到的每个消息的最终拷贝都会按照原始发布的顺序，但是消息重复的可能性会导致较早的消息在重发后，会比后续的消息更晚收到。例如发布者可能按照1，2，3，4的顺序发送消息，订阅者可能按照1，2，3，2，3，4的顺序收到消息。

如果客户端和服务端都确定同一时间只有一条消息“正在飞”（除非收到上一个消息哦确认，否则不发送消息），那么QoS 1的消息会在其他消息之后被收到 - 例如，订阅者可能收到的顺序是1，2，3，3，4而非1，2，3，2，3，4。将“正在飞”的窗口设置为1也意味着顺序会被保存，即使发布者在一个话题上发送QoS等级不同的一个序列的消息。

### 4.7 话题名称和话题过滤器

#### 4.7.1 话题通配符

话题层级分隔符把层级结构引入到话题名称中。如果存在分隔符，话题名称会被分割为多个“话题层级”。

一个订阅话题过滤器可以包含指定的通配符，通配符允许一次订阅多个话题。

通配符可以被用在话题过滤器中，但不能用在话题名称里[MQTT-4.7.1-1]。

##### 4.7.1.1 话题层级分隔符

斜杠（‘/’ U+002F）被用来分割话题书的每个层级，为话题名称提供一个分级结构。话题层级分割符的是有象征意义的，当订阅客户端指定的话题过滤器中遇到两个通配符的时候。话题层级分隔符可以唉话题名称或话题过滤器哦任何地方出现。相邻的层级分隔符标识一个零长度的话题层级。
 
#### 4.7.1.2 多层级通配符

井号（‘#’ U+0023）匹配一个话题的任意数量的层级。多层级通配符代表父层级和任意数量的子层级。多层级通配符必须要么独自存在要么在话题层级分隔符之后。两种情况中都必须是话题过滤器的最后一个字符[MQTT-4.7.1-2]。

**非规范注释**

例如，如果客户端订阅“sport/tennis/player1/#”，使用下列话题名称的消息会被收到：

- "sport/tennis/player1"
- "sport/tennis/player1/ranking"
- "sport/tennis/player1/score/wimbledon"

**非规范注释**

- "sport/#" 也会匹配 "sport"，因为#包含父层级
- "#"是可用的，而且会收到所有的应用消息
- "sport/tennis/#"是可用的
- "sport/tennis#"是不可用的
- "sport/tennis/#/ranking"是不可用的

#### 4.7.1.3 单层级通配符

加号（‘+’ U+002B）通配符只能匹配单层话题。

单层级通配符可以用在话题过滤器的任意层级，包括首层和末层。使用的时候必须占据整个层[MQTT-4.7.1-3]。可以在话题过滤器的多个层级使用，也可以和多层通配符联合使用。

**非规范注释**

例如，"sport/tennis/+"匹配"sport/tennis/player1"和"sport/tennis/player2"，但是不匹配"sport/tennis/player1/ranking"。而且因为单层级通配符只能匹配一个层级，所以"sport/+"不能匹配"sport"，但是可以匹配"sport/"。

**非规范注释**

- "+"可用
- "+/tennis/#"可用
- "sprot+"不可用
- "sport/+/player1"可用
- "/finance"匹配"+/+"和"/+"，但是不匹配"+"

### 4.7.2 话题由$开始

服务端不能用以通配符（#或+）开头的话题过滤器去匹配以$开头的话题名字[MQTT-4.7.2-1]。服务端应该避免客户端使用这样的话题名称与其他客户端交换消息。服务端实现可以使用以$开头的话题名字做其他目的。

**非规范注释**

- $SYS/ 广泛地被用作包含服务端特别信息的话题前缀或控制APIs
- 应用不能将$开头的话题用于自身目的

**非规范注释**

- "#"订阅不会收到任何使用$开头的话题发布的消息
- "+/monitor/Client"不会收到任何"$SYS/monitor/Clients"发布的消息
- "$SYS/#"可以收到发布给"$SYS/"的消息
- "$SYS/monitor/+"会收到"$SYS/monitor/Client"的消息
- 为了让客户端既能收到以$SYS/开头的话题，又能收到不以$开头的话题，必须既订阅"#"又订阅"$SYS/#"

#### 4.7.3 话题的语义和用法

下列规则适用于话题名称和话题过滤器：

- 所有的话题名称和话题过滤器都要至少一个字符长度[MQTT-4.7.3-1]
- 话题名称和话题过滤器区分大小写
- 话题名称和话题过滤器可以包含空格
- 以‘/’开头或结尾是截然不同的话题名称或话题过滤器
- 只包含‘/’字符的话题名称或话题过滤器是有效的
- 话题名称和话题过滤器不能包含空字符（Unicode U+0000）[Unicode]\[MQTT-4.7.3-2]
- 话题名称和话题过滤器是UTF-8编码字符串，不能多于65535个字节[MQTT-4.7.3-3]。见1.5.3节

话题名称和话题过滤器的层级数没有限制，但是受到UTF-8编码字符串长度的限制。

当执行匹配的时候，服务端不能执行任何的话题名称和话题过滤器的规范行为或修改或替换非法字符[MQTT-4.7.3-4]。话题过滤器里每个非通配符的层级都必须逐个字符的匹配话题名称里的相应层级才算匹配成功。

**非规范注释**

UTF-8编码规则意味着话题名称和话题过滤器的比较既可以通过对比UTF-8编码后的字节，也可以通过对比解码后的Unicode字符

**非规范注释**

- "ACCOUNTS"和"Accounts"是连个不同的话题名称
- "Accounts payable"是可用的话题名称
- "/finance"和"finance"是不同的

应用消息会被发送给每一个客户端订阅，只要客户端的话题过滤器和应用消息的话题名称匹配。话题资源既可以通过管理员在服务端预定义，也可以被服务端动态的创建，当服务端收到包含话题名称的第一个订阅或应用消息。服务端也可以对给定客户端使用安全组件在话题资源上有选择的进行操作。

### 4.8 错误处理

除非另有说明，如果服务端或客户端遇到违反协议的情况，必须关闭收到违反协议的控制包的网络连接[MQTT-4.8.0-1]。

客户端或服务端实现可能遇到一个瞬时错误（例如缓冲区已满）阻碍成功处理MQTT包。

如果客户端或服务端正在处理外来的控制包时遇到瞬时错误必须关闭控制包对应的网络连接[MQTT-4.8.0-2]。如果服务端发现了瞬时错误不应该断开与其他客户端的连接或对其他客户端产生别的影响。

----

## 5 安全

### 5.1 简介

这一章仅仅是提供一个指引，而且是非规范性的。但是强烈推荐服务端实现提供TLS[RFC5246]，使用TCP协议8883端口（IANA服务名称：secure-mqtt）。

有一些风险是解决方案的提供者应该考虑到的，例如：

- 设备可能被盗用
- 客户端和服务端的静态数据容易被访问到
- 协议行为可能会有副作用（例如时间攻击）
- 拒绝服务攻击
- 通信可能被拦截，篡改，重定向，泄漏
- 注入欺骗控制包

MQTT解决方案经常部署在通信恶略的环境。这种情况下，实现通常需要提供如下机制：

- 用户和设备的认证
- 服务端资源访问授权
- MQTT控制包及其包含的数据的完整性
- MQTT控制包及其包含的数据的私密性

作为一个传输协议，MQTT只关心消息的传输，而提供适当的安全特性是实现者的责任。通常使用TLS[RFC5246]可以实现。

除了技术上的安全，还应该考虑地理（e.g. U.S.-EU SafeHarbor [USEUSAFEHARB]），行业（e.g. PCI DSS [PCIDSS]），监管（e.g. Sarbanes-Oxley [SARBANES]）的问题。

### 5.2 MQTT解决方案：安全和认证

一个实现可能需要提供符合特定行业标准的安全框架，例如NIST网络安全框架[NISTCSF]，PCI-DSS [PCIDSS], FIPS-140-2 [FIPS1402] and NSA Suite B [NSAB].

使用NIST网络安全框架的指引可以在MQTT的补充补充出版资料中找到，《MQTT和NIST改善关键基础设施网络安全框架》[MQTT NIST]。行业使用证明，独立的验证和认证技术可以帮助达到要求。

### 5.3 轻量的加密和受限的设备

高级加密标准[AES]和数据加密标准[DES]被广泛采用。

ISO 29192 [ISO29192]使加密可以在受限的低端设备上运行。

### 5.4 实现注意

当实现或使用MQTT时，要考虑的安全问题有很多，而不仅仅限于下节涉及到的。下一节不应被视为一个“检查表”。

一个实现可能完成到下列某些或全部：

#### 5.4.1 服务端认证客户端

CONNECT包含有Username和Password字段。实现可以选择如何使用这些字段的内容。他们也可能提供自己的认证机制，使用额外的认证系统，例如LDAP [RFC4511] or OAuth [RFC6749] tokens，或者利用操作系统认证机制。

实现传送认证数据，不论用明文，还是混淆数据元素，亦或不要求认证，都应该意识到，这样会有中间人攻击或重播攻击的风险。5.4.5节介绍了确保数据私密性的方法。

客户端和服务端之间的虚拟私有网络（VPN）可以保证只会从认证过的客户端收到数据。

如果使用了TLS[RFC5246]，从客户端发来的SSL证书可以让服务端用来认证客户端。

实现可能允许认证凭证通过应用消息从客户端发送到服务端。

#### 5.4.2 服务端授权客户端

服务端可以通过客户端提供的用户名，客户端唯一标识，主机名/ip地址，或者其他授权机制来控制对服务端资源的访问。

#### 5.4.3 客户端认证服务端

MQTT协议不是对称信任，不提供客户端认证服务端的机制。

如果使用TLS[RFC5246]，客户端可以用服务端发来的SSL证书来认证。如果实现提供一个ip地址的多个主机名的MQTT服务，应该注意RFC 6066[RFC6066]第3节定义的TLS服务器名扩展。这允许客户端告知服务端尝试连接服务器的主机名。

实现可能允许认证凭证通过应用消息从服务端发送到客户端。

客户端和服务端之间的VPN可以保证客户端连接的是指定的服务端。

#### 5.4.4 应用消息和控制包的完整性

应用可以在他们的应用消息中独立地包含哈希值。这可以提供发布控制包在网路传输及其他情况下的完整性。

TLS[RFC5246]为通过网络传输的数据提供了哈希算法，来验证其完整性。

使用VPN网络连接客户端和服务端可以保证数据在VPN段传输的完整性。

#### 5.4.5 应用消息和控制包的私密性

TLS[RFC5246]可以提供数据加密。有效的TLS密码套件包含一个空的加密算法，不会加密数据。确保私密的客户端和服务端没有使用这些套件。

应用可能自己实现应用消息内容的加密。这可以保证应用消息在网络传输或其他情况下的私密性。但不会保护应用消息其他属性的私密性，例如话题名称。

客户端和服务端实现可以提供数据的加密存储，例如应用消息作为Session的一部分被存储的时候。

使用VPN网络连接客户端和服务端可以保证数据在VPN上传输的私密性。

#### 5.4.6 消息传输的不可抵赖性

应用设计者或许需要考虑合适的策略来实现端对端的不可抵赖。

#### 5.4.7 客户端和服务端的检测妥协

使用TLS[RFC5246]的客户端和服务端实现, 应该在初始化TLS[RFC5246]连接的时候，有能力确保SSL证书能够与客户端和服务端的主机名匹配。

使用TLS[RFC5246]的客户端和服务端实现，可以选择提供检测证书撤销列表（CRLS[RFC5280]）和在线证书状态协议（OSCP）[RFC6960]，来防止撤销证书的使用。

在物理部署的时候可能会用到防干扰的设备传输应用消息的指定数据。例如嵌入式的GPS来确保没有在未认证的地区使用。[IEEE 802.1AR]是使用密码绑定标识符识别设备惟一性哦标准实现。

#### 5.4.8 检测反常行为

服务器实现可以监视客户端的行为，以便发现潜在的安全事件。例如：

- 重复连接
- 重复真正
- 异常退出
- 话题扫描（试图发送或订阅太多话题）
- 发送无法投递的消息（没有人订阅这个话题）
- 客户端连接后不发送数据

服务端实现会点开违反安全规则的客户端连接。

服务端实现检测不受欢迎的行为，生成一个动态的基于Ip地址或客户端唯一标识的禁止访问列表。

部署可能会使用网络层级控制（可用的话）来实现基于IP地址或基于其他信息的访问控制。

#### 5.4.9 其他安全考虑

如果客户端或服务端的SSL证书丢失或者被盗用，应该注销证书（利用CRLs[RFC5280]和/或OSCP[RFC6960]）。

客户端和服务端证书，就像用户名和密码，如果丢失或怀疑被盗用，应该被吊销和/或重新颁发。

在持续的长连接的情况下：

- 使用TLS[RFC5246]的客户端和服务端实现应该允许会话重新协商来建立新的密码参数（替换会话keys，改变加密组件，改变认证证书）。
- 服务端应该断开客户端的连接并且要求它们用新的证书重新认证。

受约束的设备和客户端在受约束的网络中，可以利用TLS会话重获[RFC5077]，以便减少重建TLS[RFC5246]会话的成本。

连接到同一个服务器的客户端之间有信任传递的关系，同样具有发布话题的权利。

#### 5.4.10 SOCKS的使用

客户端的实现应该认识到，有些环境要求使用SOCKSv5[RFC1928]代理对外连接。有些MQTT实现或许会通过SOCKS使用安全隧道（即SSH）。选择使用SOCKS的实现应该支持匿名或用户名密码认证方式的SOCKS代理。在使用用户名密码的情况下，实现应该认识到SOCKS认证或许会用纯文本，所以应该避免使用相同的证书去连接一个MQTT服务端。

#### 5.4.11 安全配置

实现和解决方案的设计者或许希望把安全当多一组作用于MQTT协议的配置来考虑。一个分层的安全体系展现如下。

##### 5.4.11.1 干净的通信配置

当使用干净的通信配置，MQTT协议运行在一个开放的网络中，没有附带适当的安全通信机制。

##### 5.4.11.2 安全的网络通信配置

当使用安全网络通信配置，MQTT协议运行在一个物理或虚拟的网络中，有安全控制的VPN网络或物理安全网络。

##### 5.4.11.3 安全传输配置

当使用安全传输配置，MQTT协议运行在一个物理或虚拟的网络中，使用TLS[RFC5246]提供的认证机制，完整性和私密性。

TLS[RFC5246]客户端认证可以用来替代MQTT用户名密码字段提供的客户端认证。

##### 5.4.11.4 工业级的安全配置

MQTT协议期望被设计在工业级的应用配置中，每种工业级应用都会定义风险模型及其对应的安全机制。推荐的常用安全机制如下所列：

[NISTCSF] NIST Cyber Security Framework
[NIST7628] NISTIR 7628 Guidelines for Smart Grid Cyber Security
[FIPS1402] Security Requirements for Cryptographic Modules (FIPS PUB 140-2)
[PCIDSS] PCI-DSS Payment Card Industry Data Security Standard
[NSAB] NSA Suite B Cryptography

----

## 6 使用WebSocket作为网络传输层

如果MQTT在WebSocket[RFC6455]上传输，如下条件必须实现：

- MQTT控制包必须通过WebSocket二进制数据帧发送。如果任何其他类型的数据帧被收到，接收者必须关闭网络连接[MQTT-6.0.0-1]。
- 一个WebSocket数据帧可以包含多个或部分MQTT控制包。接收者不能假定MQTT控制包和WebSocket帧边界对齐[MQTT-6.0.0-2]。
- 客户端必须将“mqtt”包含其提供的在WebSocket子协议列表中[MQTT-6.0.0-3]。
- 服务端选择和返回的WebSocket子协议名必须是“mqtt”[MQTT-6.0.0-4]。
- 连接客户端和服务端的WebSocket URI不能对MQTT协议产生影响。

### 6.1 IANA 注意事项

本协议要求IANA将WebSocket MQTT子协议注册在“WebSocket Subprotocol Name”下，注册数据：

    Figure 6.1 - IANA WebSocket Identifier
    |Subprotocol Identifier         |mqtt
    |Subprotocol Common Name        |mqtt
    |Subprotocol Definition         |http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.html

----

## 7 一致性

MQTT规范说明定义了MQTT客户端实现和服务器实现的一致性。

一个MQTT实现可以即符合MQTT客户端也符合MQTT服务端的实现。一个既能响应连接，也能向其他服务端发起连接的服务端必须既是一个MQTT客户端也是一个MQTT服务端[MQTT-7.0.0-1]。

一致的实现不能使用任何本规范之外的扩展，以便于其他一致的实现交互[MQTT-7.0.0-2]。

### 7.1 一致目标

#### 7.1.1 MQTT 服务端

MQTT服务端只有在下列陈述都满足的情况下才算符合本规范：

1. 服务端发送的所有控制包格式都符合第2章和第3章的描述。
2. 符合4.7节描述的话题匹配规则。
3. 满足所有下列章节中必须性的要求，除非指明只适用于客户端。
    - 第1章 介绍
    - 第2章 MQTT控制包格式
    - 第3章 MQTT控制包
    - 第4章 操作行为
    - 第6章 （如果MQTT通过WebSocket连接传输）
    - 第7章 一致性目标

一致性的服务端必须支持一个或多个能够提供有序，可靠，双向字节流的底层传输协议[MQTT-7.1.1-1]。但是一致性不依赖于任何特殊的传输层协议。服务端或许会支持4.2节所列的传输层协议，或者是其他符合要求的传输层协议[MQTT-7.1.1-2]。

#### 7.1.2 MQTT 客户端

MQTT客户端只有在下列陈述都满足的情况下才算符合本规范：

1. 客户端发送的所有控制包格式都符合第2章和第3章的描述。
2. 符合4.7节描述的话题匹配规则。
3. 满足所有下列章节中必须性的要求，除非指明只适用于客户端。
    - 第1章 介绍
    - 第2章 MQTT控制包格式
    - 第3章 MQTT控制包
    - 第4章 操作行为
    - 第6章 （如果MQTT通过WebSocket连接传输）
    - 第7章 一致性目标

一致性的客户端必须支持一个或多个能够提供有序，可靠，双向字节流的底层传输协议[MQTT-7.1.2-1]。但是一致性不依赖于任何特殊的传输层协议。客户端或许会支持4.2节所列的传输层协议，或者是其他符合要求的传输层协议[MQTT-7.1.2-2]。

----

# 附录（略）
