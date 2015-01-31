
# MQTT Version 3.1.1

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

### 1.3 标准引用（略）

### 1.4 非标准引用（略）

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

----

## 2 MQTT控制包格式

### 2.1 MQTT控制包结构

MQTT通过交换一些预定义的MQTT控制包来工作。这一节描述这些包的格式。
一个MQTT控制包包含三部分，按照下图的顺序Figure 2.1 - Structure of an MQTT Control Packet.

    Figure 2.1 - Structure of an MQTT Control Packet

    |固定头部，存在于所有MQTT控制包
    |可变头部，存在于某些MQTT控制包
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

固定头部字节1中剩下的位[3-0]包含了每个MQTT控制包类型的特殊标识，如下表Table 2,2 - Flag Bits。表中被标识为“预留”的标识位也必须赋值[MQTT-2.2.2-1]。如果收到不可用的标识，接收方必须关闭网络连接。

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

>解码剩余长度字段的算法如下：
    
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


### 2.3 可变头部

某些类型的MQTT控制包包含一个可变头部结构。位于固定头部和载荷之间。可变头部的内容取决于包的类型。可变包头中的包标识符字段在大多类型的包中比较常见。

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
    PUBLISH Packet Identifier = 0x1234 \-\>
    \<\- PUBLISH Packet Identifier = 0x1234
    PUBACK Packet Identifier = 0x1234 \-\>
    \<\-PUBACK Packet Identifier = 0x1234

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

载荷包含一个或多个编码字段。用来指定客户端的唯一标识，话题，信息，用户名和密码。除了客户端唯一标识，其他都是可选项，是否存在取决于可变头部里的标识。

#### 3.1.1 固定包头

    Figure 3.1 - CONNECT Packet fixed header

    |Bit     |7       |6       |5       |4       |3       |2       |1       |0
    |byte 1  |MQTT Control Packet type (1)       |Reserved
    |        |0       |0       |0       |1       |0       |0       |0       |0
    |byte 2… |Remaining Length

**剩余长度字段**

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

**剩余长度字段**

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

如果RETAIN标识被设置为1，在一个从客户端发送到服务端的PUBLISH包中，服务端必须存储应用信息和QoS，以便可以发送给订阅这个话题的后来的订阅者[MQTT-3.3.1-5]。当一个新的订阅建立，最后一个保留的消息，如果有的话，而且匹配订阅话题，必须发送给订阅者[MQTT-3.3.1-6]。如果服务端收到一个QoS 0并且RETAIN标识设置为1的消息，它必须清空之前为这个话题保存的所有消息。服务端应该存储新的QoS 0的消息作为这个话题新的保留消息，但是也可以选择在任何时候清空保留消息——如果这样做了，那这个话题就没有保留消息了[MQTT-3.3.1-7]。存储状态详见4.1节。

如果消息是作为客户端新的订阅的结果从服务端发回客户端，服务端必须将RETAIN标识设置为1[MQTT-3.3.1-8]。当PUBLISH包发送给客户端，必须设置RETAIN为0，因为不管标识如何设置，它都是已订阅的消息。











