


## **第4章 MAC帧格式**

LoRa所有上下行链路消息都会携带PHYPayload(物理层载荷)，物理层载荷以1字节MAC头(**MHDR**)开始，紧接着MAC层载荷(**MACPayload**)[1]，最后是4字节的MAC校验码(**MIC**)。

射频物理层结构：
<table>
   <tr>
      <td>Preamble</td>
      <td>PHDR</td>
      <td>PHDR_CRC</td>
      <td bgcolor="#CCCCCC" >PHYPayload</td>
      <td>CRC</td>
   </tr>
</table>
图5.射频PHY结构(注意 CRC只有上行链路消息中存在)


PHY载荷：
<table>
   <tr>
      <td>MHDR</td>
      <td bgcolor="#CCCCCC" >MACPayload</td>
      <td>MIC</td>
   </tr>
</table>
或者
<table>
   <tr>
      <td>MHDR</td>
      <td bgcolor="#CCCCCC" >Join-Request 或 Rejoin-Request</td>
      <td>MIC</td>
   </tr>
</table>
或者
<table>
   <tr>
      <td>MHDR</td>
      <td bgcolor="#CCCCCC" >Join-Accept[2]</td>
   </tr>
</table>
图6.PHY载荷结构

> 注：
[1] 最大有效载荷的长度详见第6章。
[2] 对于Join-Accept帧，MIC字段使用有效载荷加密，而不是单独的字段.

MAC载荷：
<table>
   <tr>
      <td bgcolor="#CCCCCC" >FHDR</td>
      <td>FPort</td>
      <td>FRMPayload</td>
   </tr>
</table>
图7.MAC载荷结构


FHDR：
<table>
   <tr>
      <td>DevAddr</td>
      <td>FCtrl</td>
      <td>FCnt</td>
      <td>FOpts</td>
   </tr>
</table>

图8.帧头结构


图9.LoRa帧格式元素(即图5~8)  


### <a name="4.1">4.1 MAC层(PHYPayload)</a>
<table>
   <tr>
      <td><b>Size (bytes)</b></td>   
      <td>1</td>
      <td>1..M</td>
      <td>4</td>
   </tr>
   <tr>
      <td><b>PHYPayload</b></td>   
      <td>MHDR</td>
      <td>MACPayload</td>
      <td>MIC</td>
   </tr>
</table>

MACPayload字段的最大长度M，在第6章有详细说明。

### <a name="4.2">4.2 MAC头(MHDR字段)</a>
<table>
   <tr>
      <td><b>Bit#</b></td>   
      <td>7..5</td>
      <td>4..2</td>
      <td>1..0</td>
   </tr>
   <tr>
      <td><b>MHDR bits</b></td>   
      <td>MType</td>
      <td>RFU</td>
      <td>Major</td>
   </tr>
</table>

MAC头中指定了消息类型(MType)和帧编码所遵循的LoRaWAN规范的主版本号(Major)。

#### <a name="4.2.1">4.2.1 消息类型(MType位字段)</a>

LoRaWAN定义了8个不同的MAC消息类型：Join-request, Rejoin-request, Join-accept, unconfirmed data up/down, and confirmed data up/down，proprietary。

<table>
   <tr>
      <td><b>MType</b></td>   
      <td><b>描述</b></td>   
   </tr>
   <tr>
      <td>000</td>
      <td>Join Request</td>
   </tr>
   <tr>
      <td>001</td>
      <td>Join Accept</td>
   </tr>
   <tr>
      <td>010</td>
      <td>Unconfirmed Data Up</td>
   </tr>
   <tr>
      <td>011</td>
      <td>Unconfirmed Data Down</td>
   </tr>
   <tr>
      <td>100</td>
      <td>Confirmed Data Up</td>
   </tr>
   <tr>
      <td>101</td>
      <td>Confirmed Data Down</td>
   </tr>
   <tr>
      <td>110</td>
      <td>Rejoin-request</td>
   </tr>
   <tr>
      <td>111</td>
      <td>Proprietary</td>
   </tr>
</table>

表1.MAC消息类型


- 4.2.1.1 Join-request and join-accept 消息

join-request、Rejoin-request和join-accept都是用在空中激活流程中，具体见章节6.2，用于漫游目的。

- 4.2.1.2 Data messages

Data messages 用来传输MAC命令数据和应用数据，这两种数据也可以放在单个消息中发送。
Confirmed-data message 接收者需要应答[1]。
Unconfirmed-data message 接收者则不需要应答。
Proprietary messages 用来处理非标准的消息格式，不能和标准消息互通，只能用来和具有相同拓展格式的消息进行通信。
当终端设备或网络服务器收到未知的消息类型时，应该丢弃。

不同消息类型用不同的方法保证消息一致性，下面会介绍每种消息类型的具体情况。

> 注：
[1] 第19章给出了确认机制的详细时序图。

#### <a name="4.2.2">4.2.2 数据消息的主版本(Major位字段)</a>

<table>
   <tr>
      <td><b>Major位字段</b></td>   
      <td><b>描述</b></td>   
   </tr>
   <tr>
      <td>00</td>
      <td>LoRaWAN R1</td>
   </tr>
   <tr>
      <td>01..11</td>
      <td>RFU</td>
   </tr>
</table>

表2.Major列表

> 注意：Major定义了激活过程中(join procedure)使用的消息格式（见章节6.2）和MAC Payload的前4字节（见第4章）。终端要根据不同的主版本号实现不同最小版本的消息格式。必须事先使用带外消息（例如，作为设备个性化信息的一部分）使网络服务器知道终端设备使用的次要版本。 当设备或网络服务器收到携带未知或不受支持的LoRaWAN版本的帧时，应该以丢弃它。


### <a name="4.3">4.3 MAC载荷(MACPayload)</a>

MAC载荷，也就是所谓的“数据帧”，包含：帧头（FHDR）、端口（FPort）以及帧载荷(FRMPayload），其中端口和帧载荷是可选的。

当一个帧具有有效FHDR，但不含Fopts（FoptsLen = 0），不含Fport且不含FRMPayload，这样的帧也是有效的。


#### <a name="4.3.1">4.3.1 帧头(FHDR)</a>

FHDR是由终端短地址(DevAddr)、1字节帧控制字节(FCtrl)、2字节帧计数器(FCnt)和用来传输MAC命令的帧选项(FOpts，最多15个字节)组成。FOpts字段如果存在，应使用NwkSEncKey加密，如4.3.1.6节所述。

<table>
   <tr>
      <td><b>Size(bytes)</b></td>   
      <td>4</td>
      <td>1</td>
      <td>2</td>
	  <td>0..15</td>
   </tr>
   <tr>
      <td><b>FHDR</b></td>   
      <td>DevAddr</td>
      <td>FCtrl</td>
      <td>FCnt</td>
	  <td>FOpts</td>
   </tr>
</table>

FCtrl在上下行消息中有所不同，下行消息如下：
<table>
   <tr>
      <td><b>Bit#</b></td>   
      <td>7</td>
      <td>6</td>
      <td>5</td>
	  <td>4</td>
	  <td>[3..0]</td>
   </tr>
   <tr>
      <td><b>FCtrl bits</b></td>   
      <td>ADR</td>
      <td>RFU</td>
      <td>ACK</td>
	  <td>FPending</td>
	  <td>FOptsLen</td>
   </tr>
</table>

上行消息如下：
<table>
   <tr>
      <td><b>Bit#</b></td>   
      <td>7</td>
      <td>6</td>
      <td>5</td>
      <td>4</td>
      <td>[3..0]</td>
   </tr>
   <tr>
      <td><b>FCtrl bits</b></td>   
      <td>ADR</td>
      <td>ADRACKReq</td>
      <td>ACK</td>
      <td>ClassB</td>
      <td>FOptsLen</td>
   </tr>
</table>

- 4.3.1.1 帧头中 自适应数据速率 的控制(ADR, ADRACKReq in FCtrl)

LoRa网络允许终端采用任何可能的数据速率。LoRaWAN协议利用该特性来优化固定终端的数据速率。这就是自适应数据速率(Adaptive Data Rate (ADR))。当这个使能时，网络会优化使得尽可能使用最快的数据速率。

当无线电信道衰减快速且不断变化时，可能无法进行自适应数据速率控制。 当网络服务器无法控制设备的数据速率时，设备的应用层应该控制它。 在这种情况下，建议使用各种不同的数据速率。 应用层应该总是尽量减少设备积累的空中停留时间。

如果**ADR**的位置高，网络就会通过相应的MAC命令来控制终端设备的数据速率。如果**ADR**位没设置，网络则无视终端的接收信号强度，不再控制终端设备的数据速率。**ADR**位可以根据需要通过终端及网络来设置或取消。

当下行链路ADR位置高时，它通知终端设备网络服务器将要发送ADR命令。 设备可以设置/取消上行链路ADR位。

当下行链路ADR位未设置时，它向终端设备发信号通知由于无线电信道的快速变化，网络暂时无法估计最佳数据速率。 在这种情况下，设备可以选择

- 上行链路ADR位置低，并按照自己的策略控制其上行链路数据速率。 这应该是移动终端设备的典型策略。

- 忽略它（保持上行链路ADR位置高）并在没有ADR下行链路命令的情况下应用正常数据速率衰减。 这应该是固定式终端设备的典型策略。

ADR位可以由终端设备或网络服务器按需设置和取消设置。 但是，只要有可能，应该启用ADR方案以延长终端设备的电池寿命，并使网络容量最大化。

> 注意：即使移动终端设备在大多数情况下实际上也是不动的。 因此，根据其移动状态，终端设备可以请求网络使用上行链路ADR位来优化其数据速率。

默认发送功率是在考虑设备功能和区域监管限制时，设备允许的最大传输功率。 设备应使用此功率级别，直到网络通过LinkADRReq MAC命令减小功率。

如果终端被网络优化过的数据速率高于自己默认的数据速率，或者发送功率低于默认功率，它需要定期检查网络仍能收到上行的数据。每次上行帧计数都会累加(是针对于每个新的上行包，重传包就不再增加计数)，终端增加 ADR_ACK_CNT 计数。如果直到ADR_ACK_LIMIT次上行(ADR_ACK_CNT >= ADR_ACK_LIMIT)都没有收到下行回复，它就得置高ADR应答请求位(**ADRACKReq**)。 网络必须在规定时间内回复一个下行帧，这个时间是通过ADR_ACK_DELAY来设置，上行之后收到任何下行帧就要把ADR_ACK_CNT的计数重置。当终端在接收时隙中的任何回复下行帧的ACK位字段不需要设置，表示网关仍在接收这个设备的上行帧。如果在下一个ADR_ACK_DELAY上行时间内都没收到回复(例如，在总时间ADR_ACK_LIMIT+ADR_ACK_DELAY之后)，终端必须切换到下一个更低速率，使得能够获得更远传输距离来重连网络。终端如果在每次ADR_ACK_DELAY到了之后依旧连接不上，就需要每次逐步降低数据速率。如果终端用它的默认数据速率，那就不需要置位**ADRACKReq**，因为无法帮助提高链路距离。

> 注意：不要ADRACKReq立刻回复，这样给网络预留一些余量，让它做出最好的下行调度处理。

> 注意：上行传输时，如果 ADR_ACK_CNT >= ADR_ACK_LIMIT 并且当前数据速率比设备的最小数据速率高，或者发射功率比默认功率人氏，或者当前信道掩码仅使用默认通道的子集，这三种情况就要设置 ADRACKReq，其它情况下不需要。

下表提供了数据速率补偿序列的示例，假设ADR_ACK_LIMIT和ADR_ACK_DELAY常量均等于32。

<table>
   <tr>
      <td><b>ADR_ACK_CNT</b></td>   
      <td><b>ADRACKReq bit</b></td>
      <td><b>Data Rate</b></td>
      <td><b>TX power</b></td>
      <td><b>Channel Mask</b></td>
   </tr>
   <tr>
      <td>0 to 63</td>
      <td>0</td>
      <td>SF11</td>
      <td>Max – 9dBm</td>
      <td>Single channel enabled</td>
   </tr>
   <tr>
      <td>64 to 95</td>
      <td>1</td>
      <td>Keep</td>
      <td>Keep</td>
      <td>Keep</td>
   </tr>
   <tr>
      <td>96 to 127</td>
      <td>1</td>
      <td>Keep</td>
      <td><b>Max</b></td>
      <td>Keep</td>
   </tr>
   <tr>
      <td>128 to 159</td>
      <td>1</td>
      <td><b>SF12</b></td>
      <td>Max</td>
      <td>Keep</td>
   </tr>
   <tr>
      <td>>= 160</td>
      <td>0</td>
      <td>SF12</td>
      <td>Max</td>
      <td><b>All channels enabled</b></td>
   </tr>
</table>

- 4.3.1.2 消息应答位及应答流程(ACK in FCtrl)

收到confirmed类型的消息时，接收端要回复一条应答消息(应答位ACK要进行置位)。如果发送者是终端，网络就利用终端发送操作后打开的两个接收窗口之一进行回复。如果发送者是网关，终端就自行决定是否发送应答（见下面的**注意**）。 

应答消息的发送只是为了回复收到的最后一条消息，并且永远不重发。

> 注意：为了让终端尽可能简单，尽可能减少状态，在收到confirmation类型需要确认的数据帧，需要立即发送一个显式（也可能是空的）的应答数据帧。或者，终端会延迟发送应答，在它下一个数据帧中再携带。

- 4.3.1.3 重传流程

**下行链路帧：**

下行链路的“confirmed”或“unconfirmed”帧不应使用相同的帧计数器值重传。 在“confirmed”下行链路帧丢失时，如果没有接收到确认帧，则应用服务器可以决定重新发送新的“confirmed”帧。

**上行链路帧：**

上行链路“confirmed”和“unconfirmed”帧被发送“NbTrans”次（见5.3节），除非在其中一次传输之后接收到有效的下行链路消息。 网络管理器可以使用“NbTrans”参数来控制节点上行链路的冗余，以获得给定的服务质量。

如果接收到相应的下行链路确认帧，则设备应停止上行链路“confirmed”帧的重传。

只要在RX1时隙窗口期间接收到有效的单播下行链路消息，ClassB和ClassC设备就会停止上行链路“unconfirmed”帧的重传。

每当在RX1或RX2时隙窗口期间接收到有效的下行链路消息时，ClassA设备应停止上行链路“unconfirmed”帧的重传。

如果网络接收到多于NbTrans次相同上行链路帧，则这可能是 replay 攻击，或者可能设备故障，因此网络不应处理这些帧。

> 注意：检测重放攻击的网络可能采取其他措施，例如将NbTrans参数减少为1，或将原来信道接收的上行链路帧丢弃，这个信道已经传输了相同的帧，或者一些其他未指明的机制。


- 4.3.1.4 帧挂起位(FPending in FCtrl 只在下行有效)

帧挂起位(FPending)只在下行通信中使用，表示网关还有下行数据等待下发，需要终端尽快发送上行消息来再打开一个接收窗口。

**FPending**的详细用法在章节19.3。

- 4.3.1.5 帧计数器(FCnt)

每个终端有3个计数器跟踪数据帧的个数，一个是上行链路计数器（FCntUp），由终端在每次上行数据给网络服务器时累加；另一个是下行链路计数器（FCntDown），由服务器在每次下行数据给终端时累计。 

在下行链路方向上存在两种不同的帧计数器方案。 单个计数器方案，其中所有端口在设备使用 LoRaWAN1.0 时共享相同的下行链路帧计数器FCntDown。双计数器方案，在端口0和FPort字段缺失时，使用单独的NFCntDown进行MAC通信，当设备使用 LoRaWAN1.1 时，另一个AFCntDown用于除端口0外的其他端口。

下行链路双计数器方案中，NFCntDown由网络服务器管理，而AFCntDown由应用服务器管理。

> 注意：LoRaWAN v1.0及更早版本仅支持一个FCntDown计数器（在所有端口之间共享），并且网络服务器必须注意支持LoRaWAN v1.1之前的设备。

每当OTAA设备成功处理Join-accept消息时，上行帧计数器（FCntUp）和下行帧计数器（NFCntDown和AFCntDown）将重置为0。

ABP入网的设备帧计数器在制造时初始化为0。在ABP设备中，帧计数器不能在设备的运行期间复位。如果终端设备在其运行期间容易断电（例如更换电池），则在这种情况下帧计数器应该持久化（比如存储到flash中）。

随后，FCntUp随每个上行链路递增。 NFCntDown 随着每个下行链路递增（FPort 为 0 或 FPort 不存在）。 AFCntDown随着每个下行链路递增（FPort 不为 0）。在接收侧，相应的计数器与接收的值保持同步，前提是接收的值与当前计数器值相比递增，并且消息MIC字段与利用网络会话密钥计算出的MIC匹配。在多次传输 confirmed 或 unconfirmed 的帧的情况下，FCnt不会递增（请参阅NbTrans参数）。网络服务器应删除重复传输的帧，并仅将单个实例转发到应用程序服务器。

网络服务器为每个终端跟踪上行帧计数及产生下行帧计数。  终端入网成功后，终端和服务端的上下行帧计数同时置0。 每次发送消息后，发送端与之对应的 FCntUp 或 FCntDown 就会加1。 接收方会同步保存接收数据的帧计数，对比收到的计数值和当前保存的值，如果两者相差小于 MAX_FCNT_GAP （要考虑计数器滚动），接收方就按接收的帧计数更新对应值。如果两者相差大于 MAX_FCNY_GAP 就说明中间丢失了很多数据，这条以及后面的数据就被丢掉。

帧计数器是32位宽，**FCnt**字段对应于32位帧计数器的最低有效16位（即，用于发送上行链路的数据帧的FCntUp和用于下行链路发送的数据帧的AFCntDown / NFCntDown）。

终端设备不应重复使用相同的FCntUp值（在相同应用下）或网络会话密钥，除了重传相同的 confirmed 或 unconfirmed 的帧。

终端设备应该永远不会处理相同下行链路帧的任何重传。 应该忽略重传的帧，不做处理。

> 注意：这意味着一旦接收到下行链路 confirmed 帧，设备将仅回复消息（ACK），类似地，设备将仅在接收到设置了FPending位的帧之后生成单个上行链路消息。

> 注意：由于FCnt字段仅携带32位帧计数器的最低有效16位，因此服务器必须从通信状况推断出帧计数器的16个最高有效位。

- 4.3.1.6 帧可选项(FOptsLen in FCtrl, FOpts)

FCtrl 字节中的**FOptsLen**位字段描述了整个帧可选项(**FOpts**)的字段长度。

FOpts字段存放MAC命令，最长15字节，详细的MAC命令见章节4.4。

如果FOptsLen为0，则FOpts为空。在FOptsLen非0时，则反之。如果MAC命令在FOpts字段中体现，port0不能用(FPort要么不存在，要么非0)。

MAC命令不能同时出现在FRMPayload和FOpts中，如果出现了，设备丢掉该组数据。

如果帧头部携带FOpts，则必须在计算消息完整性代码（MIC）之前对FOpts进行加密。

所使用的加密方案基于 IEEE 802.15.4/2006 Annex B [IEEE802154] 中描述的通用算法，其使用密钥长度为128位的AES。

使用的密钥K是上行链路和下行链路方向上的Fwpts字段的NwkSEncKey。

加密的字段是：pld = FOpts

对于每条消息，算法定义一个数据块A：

<table>
   <tr>
      <td><b>Size(bytes)</b></td>   
      <td>1</td>
      <td>4</td>
      <td>1</td>
	  <td>4</td>
	  <td>4</td>
	  <td>1</td>
	  <td>1</td>
   </tr>
   <tr>
      <td><b>A</b></td>
      <td>0x01</td>	  
      <td>4 x 0x00</td>
      <td>Dir</td>
      <td>DevAddr</td>
	  <td>FCntUp or NFCntDwn</td>
	  <td>0x00</td>
	  <td>0x00</td>
   </tr>
</table>

方向字段（Dir）当上行链路帧为0，当下行链路帧为1。

数据块A被加密以获得数据块S：

> S = aes128_encrypt（K，A）

通过将 （pld | pad16）xor S 截断到第一个len（pld）八位字节来完成FOpts的加密和解密。

#### <a name="4.3.2">4.3.2 端口字段(FPort)</a>

如果帧载荷字段不为空，端口字段必须体现出来。端口字段有体现时，若FPort的值为0表示FRMPayload只包含了MAC命令；具体见第5章中的MAC命令。  FPort的数值从1到223(0x01..0xDF)都是由应用层使用。FPort值224专用于LoRaWAN MAC层测试协议。 LoRaWAN实现应该丢弃FPort值不在1..224范围内的应用层的任何传输请求。

> 注意：FPort值224的目的是提供专用的FPort，以便在最终版本的设备上通过无线方式运行MAC一致性测试场景，而无需在实际方面依赖于设备的特定测试版本。该测试不应与实时操作同时进行，但设备的MAC层实现应完全是可正常的使用。通常使用AppSKey加密测试协议。这可确保网络服务器不能在没有设备所有者参与的情况下启用设备的测试模式。如果测试在实时网络连接设备上运行，则网络侧测试应用程序学习AppSKey的方式不在LoRaWAN规范的范围。如果测试在专用测试平台（不是实时网络）上使用OTAA运行，则AppKey与测试平台通信的方式，对于安全的JOIN过程，也不在规范范围内。

> 在应用层运行的测试协议是在LoRaWAN规范之外定义的，因为它是一个应用层协议。

FPort值225..255（0xE1..0xFF）保留用于将来的标准化应用程序扩展。

<table>
   <tr>
      <td><b>Size(bytes)</b></td>   
      <td>7..22</td>
      <td>0..1</td>
      <td>0..N</td>
   </tr>
   <tr>
      <td><b>MACPayload</b></td>   
      <td>FHDR</td>
      <td>FPort</td>
      <td>FRMPayload</td>
   </tr>
</table>

N是应用程序载荷的字节个数。N的有效范围具体在[PHY]（物理层参数文档）定义。

N应该小于等于：

> N < = M - 1 - (FHDR长度)

M是MAC载荷的最大长度。

#### <a name="4.3.3">4.3.3 MAC帧载荷加密(FRMPayload)</a>
如果数据帧携带了载荷，FRMPayload必须要在MIC计算前进行加密。

加密机制是采用 IEEE 802.15.4/2006 Annex B [IEEE802154] 的AES128算法。

使用的密钥K取决于数据消息的FPort：

<table>
   <tr>
      <td><b>FPort</b></td>   
      <td><b>Direction</b></td>   
      <td><b>K</b></td> 
   </tr>
   <tr>
      <td>0</td>
      <td>Uplink/downlink</td>
      <td>NwkSEncKey</td>
   </tr>
   <tr>
      <td>1..255</td>
      <td>Uplink/downlink</td>
      <td>AppSKey</td>
   </tr>
</table>
表3: FPort列表

加密的字段是：

> pld = FRMPayload

对于每个数据帧，算法定义了一个块序列Ai，i从1到k，k = ceil(len(pld) / 16)：
<table>
   <tr>
      <td><b>Size(bytes)</b></td>   
      <td>1</td>
      <td>4</td>
      <td>1</td>
	  <td>4</td>
	  <td>4</td>
	  <td>1</td>
	  <td>1</td>
   </tr>
   <tr>
      <td><b>Ai</b></td>
      <td>0x01</td>	  
      <td>4 x 0x00</td>
      <td>Dir</td>
      <td>DevAddr</td>
	  <td>FCntUp or NFCntDwn or AFCntDnw</td>
	  <td>0x00</td>
	  <td>i</td>
   </tr>
</table>

方向字段(**Dir**)在上行帧时为0，在下行帧时为1.
块Ai通过加密，得到一个由块Si组成的序列S。
> Si = aes128_encrypt(K, Ai) for i = 1..k
> S = S1 | S2 | .. | Sk

有效载荷的加密和解密通过截断来完成

> （pld | pad16）xor S.

到第一个len（pld）八位字节。

- 4.3.3.2 LoRaWAN层之上的加密  

如果LoRaWAN之上的层级在已选的端口上(但不能是端口0，这是给MAC命令保留的)提供了预加密的FRMPayload给LoRaWAN，LoRaWAN则不再对FRMPayload进行修改，直接将FRMPayload从MACPayload传到应用层，以及从应用层传到MACPayload。

## <a name="4.4">4.4 消息校验码(MIC)</a>
消息检验码要计算消息中所有字段。

> msg = MHDR | FHDR | FPort | FRMPayload

其中len（msg）表示以八位字节为单位的消息长度。

4.4.1 下行链路帧
下行链路帧的MIC计算如下[RFC4493]：

> cmac = aes128_cmac（SNwkSIntKey，B0 | msg）
> MIC = cmac [0..3]

块B0的定义如下：

<table>
   <tr>
      <td><b>Size(bytes)</b></td>   
      <td>1</td>
      <td>2</td>
      <td>2</td>
      <td>1</td>
	  <td>4</td>
	  <td>4</td>
	  <td>1</td>
	  <td>1</td>
   </tr>
   <tr>
      <td><b>B0</b></td>
      <td>0x49</td>	 
      <td>ConfFCnt</td>
      <td>2 x 0x00</td>
      <td>Dir=0x01</td>
      <td>DevAddr</td>
      <td>AFCntDwn or NFCntDwn</td>
      <td>0x00</td>
      <td>len(msg)</td>
   </tr>
</table>

如果设备连接到LoRaWAN1.1网络服务器并且下行链路帧的ACK位被设置，意味着该帧是确认上行链路的“confirmed”帧，则ConfFCnt是确认上行链路的“confirmed”帧帧计数器对 2 ^ 16 取模。 在所有其他情况下，ConfFCnt = 0x0000。

- 4.4.2上行链路帧

上行链路帧的MIC通过以下过程计算：

数据块B0定义如下：

<table>
   <tr>
      <td><b>Size(bytes)</b></td>
      <td>1</td>
      <td>4</td>
      <td>1</td>
      <td>4</td>
      <td>4</td>
      <td>1</td>
      <td>1</td>
   </tr>
   <tr>
      <td><b>B0</b></td>
      <td>0x49</td>
      <td>0x0000</td>
      <td>Dir=0x00</td>
      <td>DevAddr</td>
      <td>FCntUp</td>
      <td>0x00</td>
      <td>len(msg)</td>
   </tr>
</table>

数据块B1定义如下：
<table>
   <tr>
      <td><b>Size(bytes)</b></td>
      <td>1</td>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>4</td>
      <td>1</td>
      <td>1</td>
   </tr>
   <tr>
      <td><b>B1</b></td>
      <td>0x49</td>
      <td>ConfFCnt</td>
      <td>TxDr</td>
      <td>TxCh</td>
      <td>Dir = 0x00</td>
      <td>DevAddr</td>
      <td>FCntUp</td>
      <td>0x00</td>
      <td>len(msg)</td>
   </tr>
</table>

其中：

- TxDr是用于传输上行链路的数据速率。

- TxCh是用于传输的信道的索引值。

- 如果设置了上行链路帧的ACK位，意味着该帧正在确认下行链路“confirmed已确认”帧，则ConfFCnt是确认“confirmed”下行链路帧计数器值对2 ^ 16取模。 在所有其他情况下，ConfFCnt = 0x0000。

> cmacS = aes128_cmac(SNwkSIntKey, B1 | msg)
> cmacF = aes128_cmac(FNwkSIntKey, B0 | msg)

如果设备连接到LoRaWAN1.0网络服务器，则：

> MIC = cmacF[0..3]

如果设备连接到LoRaWAN1.1网络服务器，则：

> MIC = cmacS[0..1] | cmacF[0..1]