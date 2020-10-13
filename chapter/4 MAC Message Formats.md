


## **第4章 MAC帧格式**

LoRa所有上下行链路消息都会携带PHY载荷，PHY载荷以1字节MAC头(MHDR)开始，紧接着MAC载荷(MACPayload)，最后是4字节的MAC校验码(MIC)。

射频PHY层：
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
      <td bgcolor="#CCCCCC" >Join-Request</td>
      <td>MIC</td>
   </tr>
</table>
或者
<table>
   <tr>
      <td>MHDR</td>
      <td bgcolor="#CCCCCC" >Join-Response</td>
      <td>MIC</td>
   </tr>
</table>
图6.PHY载荷结构


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

LoRaWAN定义了六个不同的MAC消息类型：join request, join accept, unconfirmed data up/down, 以及 confirmed data up/down 。

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
      <td>RFU</td>
   </tr>
   <tr>
      <td>111</td>
      <td>Proprietary</td>
   </tr>
</table>

表1.MAC消息类型


- 4.2.1.1 Join-request and join-accept 消息

join-request和join-accept都是用在空中激活流程中，具体见章节6.2

- 4.2.1.2 Data messages

Data messages 用来传输MAC命令和应用数据，这两种命令也可以放在单个消息中发送。
Confirmed-data message 接收者需要应答。
Unconfirmed-data message 接收者则不需要应答。
Proprietary messages 用来处理非标准的消息格式，不能和标准消息互通，只能用来和具有相同拓展格式的消息进行通信。

不同消息类型用不同的方法保证消息一致性，下面会介绍每种消息类型的具体情况。


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

> 注意：Major定义了激活过程中(join procedure)使用的消息格式（见章节6.2）和MAC Payload的前4字节（见第4章）。终端要根据不同的主版本号实现不同最小版本的消息格式。终端使用的最小版本应当提前通知网络服务器。


### <a name="4.3">4.3 MAC载荷(MACPayload)</a>

MAC载荷，也就是所谓的“数据帧”，包含：帧头（FHDR）、端口（FPort）以及帧载荷(FRMPayload），其中端口和帧载荷是可选的。


#### <a name="4.3.1">4.3.1 帧头(FHDR)</a>

FHDR是由终端短地址(DevAddr)、1字节帧控制字节(FCtrl)、2字节帧计数器(FCnt)和用来传输MAC命令的帧选项(FOpts，最多15个字节)组成。

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
      <td>ADRACKReq</td>
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
	  <td bgcolor="#CCCC00" >RFU</td>
	  <td>FOptsLen</td>
   </tr>
</table>

- 4.3.1.1 帧头中 自适应数据速率 的控制(ADR, ADRACKReq in FCtrl)

LoRa网络允许终端采用任何可能的数据速率。LoRaWAN协议利用该特性来优化固定终端的数据速率。这就是自适应数据速率(Adaptive Data Rate (ADR))。当这个使能时，网络会优化使得尽可能使用最快的数据速率。

移动的终端由于射频环境的快速变化，数据速率管理就不再适用了，应当使用固定的数据速率。

如果**ADR**的位字段有置位，网络就会通过相应的MAC命令来控制终端设备的数据速率。如果**ADR**位没设置，网络则无视终端的接收信号强度，不再控制终端设备的数据速率。**ADR**位可以根据需要通过终端及网络来设置或取消。不管怎样，ADR机制都应该尽可能使能，帮助终端延长电池寿命和扩大网络容量。

> 注意：即使是移动的终端，可能在大部分时间也是处于非移动状态。因此根据它的移动状态，终端也可以请求网络使用ADR来帮助优化数据速率。

如果终端被网络优化过的数据速率高于自己默认的数据速率，它需要定期检查下网络仍能收到上行的数据。每次上行帧计数都会累加(是针对于每个新的上行包，重传包就不再增加计数)，终端增加 ADR_ACK_CNT 计数。如果直到ADR_ACK_LIMIT次上行(ADR_ACK_CNT >= ADR_ACK_LIMIT)都没有收到下行回复，它就得置高ADR应答请求位(**ADRACKReq**)。 网络必须在规定时间内回复一个下行帧，这个时间是通过ADR_ACK_DELAY来设置，上行之后收到任何下行帧就要把ADR_ACK_CNT的计数重置。当终端在接收时隙中的任何回复下行帧的ACK位字段不需要设置，表示网关仍在接收这个设备的上行帧。如果在下一个ADR_ACK_DELAY上行时间内都没收到回复(例如，在总时间ADR_ACK_LIMIT+ADR_ACK_DELAY之后)，终端必须切换到下一个更低速率，使得能够获得更远传输距离来重连网络。终端如果在每次ADR_ACK_DELAY到了之后依旧连接不上，就需要每次逐步降低数据速率。如果终端用它的默认数据速率，那就不需要置位**ADRACKReq**，因为无法帮助提高链路距离。

> 注意：不要ADRACKReq立刻回复，这样给网络预留一些余量，让它做出最好的下行调度处理。

> 注意：上行传输时，如果 ADR_ACK_CNT >= ADR_ACK_LIMIT 并且当前数据速率比设备的最小数据速率高，就要设置 ADRACKReq，其它情况下不需要。

- 4.3.1.2 消息应答位及应答流程(ACK in FCtrl)

收到confirmed类型的消息时，接收端要回复一条应答消息(应答位ACK要进行置位)。如果发送者是终端，网络就利用终端发送操作后打开的两个接收窗口之一进行回复。如果发送者是网关，终端就自行决定是否发送应答。 
应答消息只会在收到消息后回复发送，并且不重发。

> 注意：为了让终端尽可能简单，尽可能减少状态，在收到confirmation类型需要确认的数据帧，需要立即发送一个严格的应答数据帧。或者，终端会延迟发送应答，在它下一个数据帧中再携带。

- 4.3.1.3 重传流程

当需要应答却没收到应答时就会进行重发，重发的个数由终端自己定，可能每个终端都不一样，这个参数也可以由网络服务器来设置调整。

> 注意：一些应答机制的示例时序图在第18章中有提供。

> 注意：如果终端设备重发次数到达了最大值，它可以降低数据速率来重连。至于后面是否再重发还是说丢弃不管，都取决于终端自己。

> 注意：如果网络服务器重发次数到达了最大值，它就认为该终端掉线了，直到它再收到终端的消息。一旦和终端设备的连接出现问题时，要不要重发都取决于网络服务器自己。

> 注意：在重传期间的数据速率回退的建议策略在章节18.4中有描述。


- 4.3.1.4 帧挂起位(FPending in FCtrl 只在下行有效)

帧挂起位(FPending)只在下行交互中使用，表示网关还有挂起数据等待下发，需要终端尽快发送上行消息来再打开一个接收窗口。

**FPending**的详细用法在章节18.3。

- 4.3.1.5 帧计数器(FCnt)

每个终端有两个计数器跟踪数据帧的个数，一个是上行链路计数器（FCntUp），由终端在每次上行数据给网络服务器时累加；另一个是下行链路计数器（FCntDown），由服务器在每次下行数据给终端时累计。 网络服务器为每个终端跟踪上行帧计数及产生下行帧计数。  终端入网成功后，终端和服务端的上下行帧计数同时置0。 每次发送消息后，发送端与之对应的 FCntUp 或 FCntDown 就会加1。 如果收到的帧计数相比当前计数有增长，同时两者相差小于 MAX_FCNT_GAP(考虑了计数翻转)，接收方就按接收的帧计数更新对应值。如果两者相差大于 MAX_FCNY_GAP 就说明中间丢失了很多数据，这条数据包就被丢掉。

LoRaWAN的帧计数器可以用16位和32位两种，节点上具体执行哪种计数，需要在带外通知网络侧，告知计数器的位数。
如果采用16位帧计数，**FCnt**字段的值可以使用帧计数器的值，此时有需要的话通过在前面填充0（值为0）字节来补足；如果采用32位帧计数，
**FCnt**就对应计数器32位的16个低有效位(上行数据使用上行FCnt，下行数据使用下行FCnt)。

终端在相同应用和网络密钥下，不能重复用相同的FCntUp数值，除非是重传。

- 4.3.1.6 帧可选项(FOptsLen in FCtrl, FOpts)
FCtrl 字节中的FOptsLen位字段描述了整个帧可选项(FOpts)的字段长度。

FOpts字段存放MAC命令，最长15字节，详细的MAC命令见章节4.4。

如果FOptsLen为0，则FOpts为空。在FOptsLen非0时，则反之。如果MAC命令在FOpts字段中体现，port0不能用(FPort要么不体现，要么非0)。

MAC命令不能同时出现在FRMPayload和FOpts中，如果出现了，设备丢掉该组数据。


#### <a name="4.3.2">4.3.2 端口字段(FPort)</a>

如果帧载荷字段不为空，端口字段必须体现出来。端口字段有体现时，若FPort的值为0表示FRMPayload只包含了MAC命令；具体见章节4.4中的MAC命令。  FPort的数值从1到223(0x01..0xDF)都是由应用层使用。  FPort的值从224到255(0xE0..0xFF)是保留用做未来的标准应用拓展。

<table>
   <tr>
      <td><b>Size(bytes)</b></td>   
      <td>7..23</td>
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

N是应用程序载荷的字节个数。N的有效范围具体在第7章有定义。

N应该小于等于：
N <= M - 1 - (FHDR长度)
M是MAC载荷的最大长度。

#### <a name="4.3.3">4.3.3 MAC帧载荷加密(FRMPayload)</a>
如果数据帧携带了载荷，FRMPayload必须要在MIC计算前进行加密。
加密机制是采用IEEE802.15.4/2006的AES128算法。

默认的，加密和解密由LoRaWAN层来给所有的FPort来执行。如果加密/解密由应用层来做更方便的话，也可以在LoRaWAN层之上给特定FPorts来执行，除了端口0。具体哪个节点的哪个FPort在LoRaWAN层之外要做加解密，必须要和服务器通过out-of-band信道来交互(见第19章)。

- 4.3.3.1 LoRaWAN的加密

密钥K根据不同的FPort来使用：

<table>
   <tr>
      <td><b>FPort</b></td>   
      <td><b>K</b></td> 
   </tr>
   <tr>
      <td>0</td>
      <td>NwkSKey</td>
   </tr>
   <tr>
      <td>1..255</td>
      <td>AppSKey</td>
   </tr>
</table>
表3: FPort列表

具体加密是这样：
pld = FRMPayload
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
	  <td>FCntUp or FCntDown</td>
	  <td>0x00</td>
	  <td>i</td>
   </tr>
</table>

方向字段(**Dir**)在上行帧时为0，在下行帧时为1.
块Ai通过加密，得到一个由块Si组成的序列S。
> Si = aes128_encrypt(K, Ai) for i = 1..k
> S = S1 | S2 | .. | Sk

通过异或计算对payload进行加解密：

- 4.3.3.2 LoRaWAN层之上的加密  

如果LoRaWAN之上的层级在已选的端口上(但不能是端口0，这是给MAC命令保留的)提供了预加密的FRMPayload给LoRaWAN，LoRaWAN则不再对FRMPayload进行修改，直接将FRMPayload从MACPayload传到应用层，以及从应用层传到MACPayload。

## <a name="4.4">4.4 消息校验码(MIC)</a>
消息检验码要计算消息中所有字段。
msg = MHDR | FHDR | FPort | FRMPayload

MIC是按照[RFC4493]来计算：
> cmac = aes128_cmac(NwkSKey, B0 | msg)
> MIC = cmac[0..3]

块B0的定义如下：

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
      <td>4 x 0x00</td>
      <td>Dir</td>
      <td>DevAddr</td>
	  <td>FCntUp or FCntDown</td>
	  <td>0x00</td>
	  <td>len(msg)</td>
   </tr>
</table>

方向字段(**Dir**)在上行帧时为0，在下行帧时为1。

