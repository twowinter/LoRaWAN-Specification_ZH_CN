
## **第5章 MAC命令**

对网络管理者而言，有一套专门的MAC命令用来在服务器和终端MAC层之间交互。这套MAC命令对应用程序或者应用服务器或者运行在终端设备上的应用程序是不可见的。

单个数据帧中可以包含MAC命令序列，要么在FOpts字段中捎带，要么作为独立帧将**FPort**设成0后放在FRMPayload里。如果采用FOpts捎带的方式，MAC命令不进行加密并且长度不能超过15字节。如果采用独立帧放在**FRMPayload**的方式，那就必须采用加密方式，并且不能超过**FRMPayload**的最大长度。

> 注意：如果MAC命令不想被窃听，那就必须以独立帧形式放在FRMPayload中进行发送。

每个MAC命令是由 1字节命令码 (CID) 跟着一段可能为空的特定命令字节序列组成的。

<table>
   <tr>
      <td rowspan="2" ><b>CID</b></td>
      <td rowspan="2" ><b>Command</b></td>
      <td colspan="2" ><b>由谁发送</b></td>
      <td rowspan="2" ><b>描述</b></td>
   </tr>
   <tr>
      <td>终端</td>
      <td>网关</td>
   </tr>
   <tr>
      <td>0x02</td>
      <td>LinkCheckReq</td>
      <td>x</td>
      <td></td>
      <td>终端利用这个命令来判断网络连接质量</td>
   </tr>
   <tr>
      <td>0x02</td>
      <td>LinkCheckAns</td>
      <td></td>
      <td>x</td>
      <td>LinkCheckReq的回复。包含接收信号强度，告知终端接收质量</td>
   </tr>
   <tr>
      <td>0x03</td>
      <td>LinkADRReq</td>
      <td></td>
      <td>x</td>
      <td>向终端请求改变数据速率，发射功率，重传率以及信道</td>
   </tr>
   <tr>
      <td>0x03</td>
      <td>LinkADRAns</td>
      <td>x</td>
      <td></td>
      <td>LinkADRReq的回复。</td>
   </tr>
   <tr>
      <td>0x04</td>
      <td>DutyCycleReq</td>
      <td></td>
      <td>x</td>
      <td>向终端设置发送的最大占空比。</td>
   </tr>
   <tr>
      <td>0x04</td>
      <td>DutyCycleAns</td>
      <td>x</td>
      <td></td>
      <td>DutyCycleReq的回复。</td>
   </tr>
   <tr>
      <td>0x05</td>
      <td>RXParamSetupReq</td>
      <td></td>
      <td>x</td>
      <td>向终端设置接收时隙参数。</td>
   </tr>
   <tr>
      <td>0x05</td>
      <td>RXParamSetupAns</td>
      <td>x</td>
      <td></td>
      <td>RXParamSetupReq的回复。</td>
   </tr>
   <tr>
      <td>0x06</td>
      <td>DevStatusReq</td>
      <td></td>
      <td>x</td>
      <td>向终端查询其状态。</td>
   </tr>
   <tr>
      <td>0x06</td>
      <td>DevStatusAns</td>
      <td>x</td>
      <td></td>
      <td>返回终端设备的状态，即电池余量和链路解调预算。</td>
   </tr>
   <tr>
      <td>0x07</td>
      <td>NewChannelReq</td>
      <td></td>
      <td>x</td>
      <td>创建或修改 1个射频信道 定义。</td>
   </tr>
   <tr>
      <td>0x07</td>
      <td>NewChannelAns</td>
      <td>x</td>
      <td></td>
      <td>NewChannelReq的回复。</td>
   </tr>
   <tr>
      <td>0x08</td>
      <td>RXTimingSetupReq</td>
      <td></td>
      <td>x</td>
      <td>设置接收时隙的时间。</td>
   </tr>
   <tr>
      <td>0x08</td>
      <td>RXTimingSetupAns</td>
      <td>x</td>
      <td></td>
      <td>RXTimingSetupReq的回复。</td>
   </tr>
   <tr>
      <td>0x09</td>
      <td>TxParamSetupReq</td>
      <td></td>
      <td>x</td>
      <td>网络服务器用于设置基于当地规定的终端的最大允许驻留时间和最大EIRP</td>
   </tr>
   <tr>
      <td>0x09</td>
      <td>TxParamSetupAns</td>
      <td>x</td>
      <td></td>
      <td>TxParamSetupReq的回复。</td>
   </tr>
   <tr>
      <td>0x0A</td>
      <td>DlChannelReq</td>
      <td></td>
      <td>x</td>
      <td>通过从上行链路频率移位下行链路频率（即创建非对称信道）来修改下行链路RX1无线电信道的定义</td>
   </tr>
   <tr>
      <td>0x0A</td>
      <td>DlChannelAns</td>
      <td>x</td>
      <td></td>
      <td>DlChannelReq的回复。</td>
   </tr>
   <tr>
      <td>0x80~0xFF</td>
      <td>私有</td>
      <td>x</td>
      <td>x</td>
      <td>给私有网络命令拓展做预留。</td>
   </tr>
</table>

表4：MAC命令表

> 注意：MAC命令的长度虽然没有明确给出，但是MAC执行层必须要知道。因此未知的MAC命令无法被忽略，且前面未知的MAC命令会终止MAC命令的处理队列。所以建议按照LoRaWAN协议介绍的MAC命令来处理MAC命令。这样所有基于LoRaWAN协议的MAC命令都可以被处理，即使是更高版本的命令。

---

> 注意：由网络服务器调整的任何值（例如，RX2、新的或已调整的信道定义）仅在终端设备的下一次加网之前有效。因此，在每个成功加网之后，终端设备将再次使用默认参数，并由网络服务器根据需要重新调整值。

### <a name="5.1">5.1 Link Check 命令 (LinkCheckReq, LinkCheckAns)</a>

通过LinkCheckReq命令，终端可以知道是否已连接上服务器。该命令没有载荷。

当网络服务器通过一个或者多个网关接收到LinkCheckReq命令时，它会以LinkCheckAns命令进行回复。

<table>
   <tr>
      <td><b>Size (bytes)</b></td>   
      <td>1</td>   
      <td>1</td>  
   </tr>
   <tr>
      <td><b>LinkCheckAns Payload</b></td>
      <td>Margin</td>
      <td>GwCnt</td>
   </tr>
</table>

解调预算(**Margin**)是一个范围为0~254的8位无符号整数，表示成功接收最新的**LinkCheckReq**命令的链路预算(单位为dB)。若 Margin 值为"0"则意味着数据帧是在解调水平上进行接收(0 dB或者没有预算)，当 Margin 值为"20"时则意味着数据帧到达在解调水平之上20dB的网关。

网关计数(**GwCnt**)是成功接收最新的**LinkCheckReq**命令的网关个数。

### <a name="5.2">5.2 Link ADR 命令(LinkADRReq, LinkADRAns)</a>

通过 LinkADRReq 命令，NS(网络服务器)可以调整终端的数据速率。

<table>
   <tr>
      <td><b>Size (bytes)</b></td>   
      <td>1</td>   
      <td>2</td>  
      <td>1</td>  
   </tr>
   <tr>
      <td><b>LinkADRReq Payload</b></td>
      <td>DataRate_TXPower</td>
      <td>ChMask</td>
      <td>Redundancy</td>
   </tr>
</table>

<table>
   <tr>
      <td><b>Bits</b></td>   
      <td>[7:4]</td>   
      <td>[3:0]</td>  
   </tr>
   <tr>
      <td><b>DataRate_TXPower</b></td>
      <td>DataRate</td>
      <td>TXPower</td>
   </tr>
</table>

所请求的数据速率(**DataRate**)和发射功率(**TXPower**)是根据区域规定，体现在[LoRaWAN协议中文版_配套文件 地区参数(物理层) ](http://blog.csdn.net/iotisan/article/details/55056092)中。命令中的发射功率字段指的是设备可操作的最大发射功率。如果命令中的发射功率高于终端实际发射功率的最大值，终端也要应答成功，这种情况下，将终端的发射功率尽可能提高到最大值。 信道掩码(ChMask)字段指示了上行链路的可用信道，从最低位bit0表示开始。

<table>
   <tr>
      <td><b>Bit#</b></td>   
      <td><b>Usable channels</b></td>   
   </tr>
   <tr>
      <td>0</td>
      <td>Channel 1</td>
   </tr>
   <tr>
      <td>1</td>
      <td>Channel 2</td>
   </tr>
   <tr>
      <td>..</td>
      <td>..</td>
   </tr>
   <tr>
      <td>15</td>
      <td>Channel 16</td>
   </tr>
</table>

表5：信道状态表

ChMask 字段的对应位如果设置为1，则表示对应的信道可以进行上行传输，只要该信道允许终端使用该数据速率。如果对应位设置为0，则表示相应信道不可用。

<table>
   <tr>
      <td><b>Bits</b></td>   
      <td>7</td>   
      <td>[6:4]</td>  
      <td>[3:0]</td>  
   </tr>
   <tr>
      <td><b>Redundancy bits</b></td>
      <td>RFU</td>
      <td>ChMaskCntl</td>
      <td>NbTrans</td>
   </tr>
</table>

Redundancy 字段中的 **NbTrans** 位域，指的是每个上行消息的发送次数，这仅对 "unconfirmed" 消息有作用。这个字段的默认值为1，相对应的是每个数据帧只进行单次传输，有效范围是[1:15]。如果收到 **NbTrans** == 0，终端需要使用默认值。这个位域可以被NS(网络服务器)用来控制节点上行的 Redundancy 从而获得QOS(服务质量)。在重传帧时节点通常会跳频，每次重传需要等到接收窗口超时。只要在RX1期间收到下行消息，该上行消息则不再进行任何重传。对于 Class A 设备，RX2时隙的接收也是一样处理。

ChMaskCntl 位域和之前定义的 ChMask 字段有关，它控制了ChMask所指定的16个信道块。也可以对所用信道进行全局的打开或关闭。这个位域的使用是根据区域规定，体现在[LoRaWAN协议中文版_配套文件 地区参数(物理层) ](http://blog.csdn.net/iotisan/article/details/55056092)中。

NS(网络服务器)可能会在单个下行帧中包含多个 LinkAdrReq 命令。终端为了配置 channel mask ，将会按照下行消息中的命令块的顺序，逐一地处理所有的 LinkAdrReq 消息。 终端可能会接收或者拒绝命令块中所有 channel mask 的控制，在逐个 LinkAdrAns 命令块中体现连续的 Channel Mask ACK 状态，来指示相应的 channel mask 接受与否。 终端在连续命令块时只处理最后一个消息中的 DataRate, TXPower 和 NbTrans 字段，因为这些参数将会决定终端的全局状态。终端需要在每一个 LinkAdrAns 命令中体现 ACK 状态，来指示对这些最终设置的接受与否。

信道频点信息是按地区规定，在第6章中有定义。终端使用 LinkADRAns 命令来应答 LinkADRReq 命令。

<table>
   <tr>
      <td><b>Size (bytes)</b></td>   
      <td>1</td>   
   </tr>
   <tr>
      <td><b>LinkADRAns Payload</b></td>
      <td>Status</td>
   </tr>
</table>


<table>
   <tr>
      <td><b>Bits</b></td>   
      <td>[7:3]</td>   
      <td>2</td>  
      <td>1</td>  
      <td>0</td>  
   </tr>
   <tr>
      <td><b>Status bits</b></td>
      <td>RFU</td>
      <td>Power ACK</td>
      <td>Data rate ACK</td>
      <td>Channel mask ACK</td>
   </tr>
</table>

LinkADRAns 的 Status 位域按照如下定义：

<table>
   <tr>
      <td> </td>   
      <td><b>Bit = 0</b></td> 
      <td><b>Bit = 1</b></td> 
   </tr>
   <tr>
      <td><b>Channel mask ACK</b></td>
      <td>所发的 channel mask 使能了未定义的信道或者禁用了所有信道。命令被丢弃，终端状态不变。</td>
      <td>所发的 channel mask 已成功解析，已按照 mask 设置了当前的信道状态。</td>
   </tr>
   <tr>
      <td><b>Data rate ACK</b></td>
      <td>所请求的数据速率，终端无法识别，或者无法应用在当前信道中（不支持任何使能的信道)。命令被丢弃，终端状态不变。</td>
      <td>数据速率成功设置。</td>
   </tr>
   <tr>
      <td><b>Power ACK</b></td>
      <td>所请求的发射功率不能在终端上执行。命令被丢弃，终端状态不变。</td>
      <td>功率等级成功设置。</td>
   </tr>
</table>

如果这三个位中有任何一位等于0，则**LinkADRReq**命令没有成功，节点保持之前的状态。


### <a name="5.3">5.3 终端发射占空比(DutyCycleReq, DutyCycleAns)</a>
DutyCycleReq命令被网络协调者用来限制终端的**最大总计发射占空比**。**最大总计发射占空比**覆盖所有子频段上的发射占空比。

<table>
   <tr>
      <td><b>Size (bytes)</b></td>   
      <td>1</td>   
   </tr>
   <tr>
      <td><b>DutyCycleReq Payload</b></td>
      <td>DutyCyclePL</td>
   </tr>
</table>

<table>
   <tr>
      <td><b>Bits</b></td>   
      <td>7:4</td>  
      <td>3:0</td>   
   </tr>
   <tr>
      <td><b>DutyCyclePL</b></td>
      <td>RFU</td>
      <td>MaxDCycle</td> 
   </tr>
</table>

终端所允许的最大发射占空比为： aggregated duty cycle = 1/(2^MaxDcycle)

**MaxDutyCycle**的有效范围为[0:15].MaxDutyCycle的值若为0则表示“无发射占空比限制”，除非各地区有对发射占空比进行限制。

### <a name="5.4">5.4 接收窗口参数(RXParamSetupReq,RXParamSetupAns)</a>

**RXParamSetupReq**命令可以对每个上行消息之后的第二接收窗口(RX2)的频率以及数据速率进行改变。该命令还可以对上行数据速率和RX1下行数据速率的偏移量进行改变。

<table>
   <tr>
      <td><b>Size (bytes)</b></td>   
      <td>1</td>   
      <td>3</td>
   </tr>
   <tr>
      <td><b>RXParamSetupReq Payload</b></td>
      <td>DLsettings</td>
      <td>Frequency</td>
   </tr>
</table>

<table>
   <tr>
      <td><b>Bits</b></td>   
      <td>7</td>  
      <td>6:4</td>   
      <td>3:0</td>
   </tr>
   <tr>
      <td><b>DLsettins</b></td>
      <td>RFU</td>
      <td>RX1DRoffset</td> 
      <td>RX2DataRate</td> 
   </tr>
</table>

**RX1DRoffset**位域设置上行数据速率和RX1下行数据速率的偏移量。默认情况下偏移量为0（意思就是上行数据速率与下行数据速率相等)。偏移量用于考虑一些地区的基站最大功率密度限制和平衡上下行射频链路预算。

**RX2DataRate**位域定义了第二接收窗口的下行链路数据速率，遵循与**LinkADRReq**命令相同的规则（例如，0表示DR0/125kHz)。**Frequency**位域所设置的是第二接收窗口所使用信道的频率，该频率按照与**NewChannelReq**命令相同的规则进行定义。

终端使用**RXParamSetupAns**命令对**RXParamSetupReq**命令进行应答。**RXParamSetupAns**命令应该添加在所有的上行链路数据帧的**Fopt**字段中直到终端接收到一个Class A类型的下行链路数据帧。这样就可以保证即使在上行链路帧丢失的情况之下，网络服务器总是可以知道终端所使用的下行链路参数。

**RXParamSetupAns**命令的载荷为一个字节的状态信息。

<table>
   <tr>
      <td><b>Size (bytes)</b></td>   
      <td>1</td>   
   </tr>
   <tr>
      <td><b>RXParamSetupAns Payload</b></td>
      <td>Status</td>
   </tr>
</table>

**Status**各位的含义如下：
<table>
   <tr>
      <td><b>Bits</b></td>   
      <td>7:3</td>  
      <td>2</td>   
      <td>1</td>
      <td>0</td>
   </tr>
   <tr>
      <td><b>Status bits</b></td>
      <td>RFU</td>
      <td>RX1DRoffset ACK</td> 
      <td>RX2 Data Rate ACK</td> 
      <td>Channel ACK</td>
   </tr>
</table>

<table>
   <tr>
      <td> </td>   
      <td><b>Bit = 0</b></td>  
      <td><b>Bit = 1</b></td>   
   </tr>
   <tr>
      <td><b>Channel ACK</b></td>
      <td>终端无法使用请求的频率</td>
      <td>RX2时隙信道频率设置成功</td> 
   </tr>
   <tr>
      <td><b>RX2 Data rate ACK</b></td>
      <td>终端无法识别请求的数据速率</td>
      <td>RX2时隙数据速率设置成功</td> 
   </tr>
   <tr>
      <td><b>RX1DRoffset ACK</b></td>
      <td>上行数据速率与RX1下行数据速率的偏移量不在允许的范围之内</td>
      <td>RX1DRoffset设置成功</td> 
   </tr>
</table>

如果3个位的任何一位为0，则**RXParamSetupReq**命令不成功，节点保持之前的状态。


### <a name="5.5">5.5 终端状态(DevStatusReq, DevStatusAns)</a>

通过 **DevStatusReq** 命令，NS(网络服务器)可以获取终端的状态信息。该命令无载荷。一旦终端收到 DevStatusReq 命令，则会回复 **DevStatusAns** 命令。

<table>
   <tr>
      <td><b>Size (bytes)</b></td>   
      <td>1</td>   
      <td>1</td>    
   </tr>
   <tr>
      <td><b>DevStatusAns Payload</b></td>
      <td>Battery</td>
      <td>Margin</td>   
   </tr>
</table>

报告电池电量（**Battery**)的编码如下:

<table>
   <tr>
      <td><b>Battery</b></td>   
      <td><b>Description</b></td>   
   </tr>
   <tr>
      <td>0</td>
      <td>终端连接到外部电源</td>
   </tr>
   <tr>
      <td>1..254</td>
      <td>数值表示电池电量，1表示最低，254表示最高</td>
   </tr>
   <tr>
      <td>255</td>
      <td>终端无法测量电池电量</td>
   </tr>
</table>

图8：电池电量码表

**Margin**是最近一次成功接收**DevStatusReq**命令的解调信噪比(该值必须是四舍五入到最近的整数值，单位为dB）。它是6位的有符号整数（最小值为 -32，最大值为31）。

<table>
   <tr>
      <td><b>Bits</b></td>   
      <td>7:6</td>   
      <td>5:0</td>   
   </tr>
   <tr>
      <td><b>Status bits</b></td>
      <td>RFU</td>
      <td>Margin</td>   
   </tr>
</table>


### <a name="5.6">5.6 信道的创建和修改(NewChannelReq, NewChannelAns, DlChannelReq, DlChannelAns)</a>

**NewChannelReq**命令可以用于修改现有的双向信道或者创建一个新的信道。这个命令设置了新信道的中心频率还有上行数据速率的可用范围：

<table>
   <tr>
      <td><b>Size (bytes)</b></td>   
      <td>1</td>   
      <td>3</td>   
      <td>1</td>  
   </tr>
   <tr>
      <td><b>NewChannelReq Payload</b></td>
      <td>Chlndex</td>
      <td>Freq</td>   
      <td>DrRange</td>  
   </tr>
</table>

信道索引**Chlndex**是正在创建或者正在修改的信道的索引。根据所使用的区域和频带，LoRaWAN规范强加了所有设备通用的默认信道，该信道不能被**NewChannelReq**命令修改(具体见章节6)。如果默认信道的个数为N，则默认信道的编号从0~(N-1)，并且**Chlndex**的可接受范围为N~15。一个设备必须至少能处理16个 不同的信道定义。在某些特定的区域，设备可能必须存储超过16个信道定义。

**Freq**位域是一个24位无符号整数。实际信道频率为（100×**Freq**），单位为HZ，其中表示低于100MHz的频率数值将会保留供将来使用。**Freq**可以设置从100MHz~1.67GHz之间的信道频率，但必须以100Hz为单位(因为实际信道频率为（100×**Freq**））。终端必须检测该频率是否能被射频硬件所使用，若不行则需返回错误。

**DrRange**位域规定了这个信道所允许的上行数据速率范围。该位域被分为两个4位字段：

<table>
   <tr>
      <td><b>Bits</b></td>   
      <td>7:4</td>   
      <td>3:0</td>   
   </tr>
   <tr>
      <td><b>DrRange</b></td>
      <td>MaxDR</td>
      <td>MinDR</td>   
   </tr>
</table>

按照[章节5.2](#5.2)所定义的规则，最小数据速率**MinDR**字段规定了这个信道所允许的最低上行数据速率。例如0表示DR0/125kHz。类似的，最大数据速率**MaxDR**规定了最高上行数据速率。例如，若**DrRange**=0x77则表示一个信道只允许50kbps的GFSK；若**DrRange**=0x50表示一个信道支持DR0/125kHz到DR5/125kHZ的频率范围。

最近定义以及修改的信道被使能之后可以立刻用于通信。RX1的下行频率与上行频率相等。

终端以**NewChannelAns**命令对**NewChannelReq**进行应答。**NewChannelAns**命令的载荷包含了以下信息：

<table>
   <tr>
      <td><b>Size (bytes)</b></td>   
      <td>1</td>   
   </tr>
   <tr>
      <td><b>NewChannelAns Payload</b></td>
      <td>Status</td>
   </tr>
</table>

**Status**位有以下含义：

<table>
   <tr>
      <td><b>Bits</b></td>   
      <td>7:2</td>   
      <td>1</td>   
      <td>0</td>  
   </tr>
   <tr>
      <td><b>Status</b></td>
      <td>RFU</td>
      <td>Data rate range ok</td>   
      <td>Channel frequency ok</td>  
   </tr>
</table>

<table>
   <tr>
      <td> </td>   
      <td>Bit = 0</td>   
      <td>Bit = 1</td>   
   </tr>
   <tr>
      <td><b>Data rate range ok</b></td>
      <td>指定的数据速率超出了终端当前定义的范围</td>
      <td>该数据速率与终端能够兼容</td>   
   </tr>
   <tr>
      <td><b>Channel frequency ok</b></td>
      <td>终端无法使用该频率</td>
      <td>终端能够使用该频率</td>   
   </tr>
</table>

如果以上两位其中之一为0，则**NewChannelReq**命令不成功，新的信道将不会产生。

**DlChannelReq**命令允许服务器在RX1时隙使用不同的下行链路频率。这个命令可以适用于所有支持**NewChannelReq**命令的地理区域(例如欧盟和中国，但是不适用于美国和澳大利亚，具体详见《LoRaWAN Regional Parameters document [PARAMS]》。

该命令用于设置RX1时隙的下行消息中心频率，如下：

<table>
   <tr>
      <td><b>Size (bytes)</b></td>   
      <td>1</td>  
      <td>3</td>   
   </tr>
   <tr>
      <td><b>DlChannelReq Payload</b></td>
      <td>Chlndex</td>
      <td>Freq</td>   
   </tr>
</table>

**Chlndex**是要修改下行频率的信道的索引。

**Freq**位域是24位的无符号整数。实际信道频率为（100×**Freq**），单位为HZ，其中表示低于100MHz的频率数值将会保留供将来使用。终端必须检测该频率是否能被射频硬件所使用，若不行则需返回错误。

终端以**DlChannelAns**命令对**DlChannelReq**命令进行应答。**DlChannelAns**命令在终端没有接收到一个下行数据之前必须添加在所有的上行数据**FOpt**位域中。这样才能保证在上行数据包丢失的情况之下，网络服务器总是能够知道终端所使用的下行频率。

该命令的载荷有如下信息：

<table>
   <tr>
      <td><b>Size (bytes)</b></td>   
      <td>1</td>   
   </tr>
   <tr>
      <td><b>DlChannelAns Payload</b></td>
      <td>Status</td>
   </tr>
</table> 

**Status**位有以下的含义：

<table>
   <tr>
      <td><b>Bits</b></td>   
      <td>7:2</td>   
      <td>1</td>   
      <td>0</td>  
   </tr>
   <tr>
      <td><b>Status</b></td>
      <td>RFU</td>
      <td>上行频率可用</td>   
      <td>信道频率可用</td>  
   </tr>
</table>

<table>
   <tr>
      <td> </td>   
      <td>Bit = 0</td>   
      <td>Bit = 1</td>   
   </tr>
   <tr>
      <td><b>Channel frequency ok</b></td>
      <td>终端无法使用该频率</td>
      <td>终端无法使用该频率</td>   
   </tr>
   <tr>
      <td><b>Channel frequency ok</b></td>
      <td>该信道无法使用此上行频率，只能为已经具有一个有效上行频率的信道设置下行频率</td>
      <td>信道的上行频率有效</td>   
   </tr>
</table>

### <a name="5.7">5.7 TX 和 RX 之间的延时设置(RXTimingSetupReq, RXTimingSetupAns)</a>

**RXTimingSetupReq**命令允许配置TX上行链路发送完毕之后与第一个接收窗口打开之间的延时。第二接收窗口在第一接收窗口打开之后的1秒打开。

<table>
   <tr>
      <td><b>Size (bytes)</b></td>   
      <td>1</td>   
   </tr>
   <tr>
      <td><b>RXTimingSetupReq Payload</b></td>
      <td>Settings</td>
   </tr>
</table>     

**Delay**位域指定了延时时间。这个位域被分为两个4位字段：

<table>
   <tr>
      <td><b>Bits</b></td>   
      <td>7:4</td>   
      <td>3:0</td> 
   </tr>
   <tr>
      <td><b>Settings</b></td>
      <td>RFU</td>
      <td>Del</td> 
   </tr>
</table>   

延时时间的单位为秒。Del的值为0时对应的延时时间为1s。

<table>
   <tr>
      <td><b>Del</b></td>   
      <td><b>Delay[s]</b></td>   
   </tr>
   <tr>
      <td>0</td>
      <td>1</td>
   </tr>
   <tr>
      <td>1</td>
      <td>1</td>
   </tr>
   <tr>
      <td>2</td>
      <td>2</td>
   </tr>
   <tr>
      <td>3</td>
      <td>3</td>
   </tr>
   <tr>
      <td>..</td>
      <td>..</td>
   </tr>
   <tr>
      <td>15</td>
      <td>15</td>
   </tr>
</table>   

图11：延时时间映射表

终端用于应答**RXTimingSetupReq**命令的**RXTimingSetupAns**命令没有载荷。

**RXTimingSetupAns**命令在终端没有接收到一个下行数据之前必须添加在所有的上行数据**FOpt**位域中。这样才能保证在上行数据包丢失的情况之下，网络服务器总是能够知道终端所使用的下行频率。

### <a name="5.8">5.8 终端发送参数(TxParamSetupReq, TxParamSetupAns)</a>

该命令只需要在特定的可调节地区进行使用。具体请参考《LoRaWAN Regional Parameters document [PARAMS]》。

**TxParamSetupReq**可以用于通知终端的最大允许驻留时间，换言之，一包数据在空中的最大持续传输时间,以及终端所允许的最大**等效全向辐射功率(EIRP)**。

> KevinCao注：
> **EIRP**解释：为无线电发射机供给天线的功率与在给定方向上天线绝对增益的乘积。各方向具有相同单位增益的理想全向天线，通常作为无线通信系统的参考天线。EIRP定义为：EIRP=Pt*Gt，它表示同全向天线相比，可由发射机获得的在最大天线增益方向上的 发射功率。Pt表示发射机的发射功率，Gt表示发射天线的天线增益。在无线通信工程中，通常用来衡量干扰的强度，以及发射机发射强信号的能力。

<table>
   <tr>
      <td><b>Size (bytes)</b></td>   
      <td>1</td>   
   </tr>
   <tr>
      <td><b>TxParamSetup payload</b></td>
      <td>EIRP_DwellTime</td>
   </tr>
</table>     

**EIRP_DwellTime**位域的结构如下所述：

<table>
   <tr>
      <td><b>Bits</b></td>   
      <td>7：6</td>   
      <td>5</td>   
      <td>4</td>  
      <td>3：0</td>  
   </tr>
   <tr>
      <td><b>MaxDwellTime</b></td>
      <td>RFU</td>
      <td>DownlinkDwellTime</td>   
      <td>UplinkDwellTime</td>  
      <td>MaxEIRP</td>  
   </tr>
</table>

**TxParamSetupReq**命令的[0..3]位是用于表示**EIRP**的最大值，每个编码值对应的最大**EIRP**值的映射表如下。这张表中的最大**EIRP**的值的变化范围由各个地区来进行自行规定。

<table>
   <tr>
      <td><b>Coded Value</b></td>   
      <td>0</td>   
      <td>1</td>   
      <td>2</td>  
      <td>3</td>  
      <td>4</td>  
      <td>5</td>  
      <td>6</td>  
      <td>7</td>  
      <td>8</td>  
      <td>9</td>  
      <td>10</td>  
      <td>11</td>  
      <td>12</td>  
      <td>13</td>  
      <td>14</td>  
      <td>15</td>  
   </tr>
   <tr>
      <td><b>Max EIRP(dBm)</b></td>   
      <td>8</td>   
      <td>10</td>   
      <td>12</td>  
      <td>13</td>  
      <td>14</td>  
      <td>16</td>  
      <td>18</td>  
      <td>20</td>  
      <td>21</td>  
      <td>24</td>  
      <td>26</td>  
      <td>27</td>  
      <td>29</td>  
      <td>30</td>  
      <td>33</td>  
      <td>36</td>  
   </tr>
</table>

最大**EIRP**指的是设备无线电发射功率的上限。设备不需要使用该功率进行传输，但是绝不会辐射超过指定的EIRP。

第4位和第5位分别定义了上行链路和下行链路的驻留时间，驻留时间的映射编码表如下所示：

<table>
   <tr>
      <td><b>Coded Value</b></td>   
      <td><b>Dwell Time</b></td>   
   </tr>
   <tr>
      <td>0</td>
      <td>No Limit</td>
   </tr>
   <tr>
      <td>1></td>
      <td>400 ms</td>
   </tr>
</table>  

当该Mac命令生效时，(因区域而定）终端会以**TxParamSetupAns**命令对**TxParamSetupReq**命令进行回复。**TxParamSetupAns**命令不包含任何载荷。

当在某个区域内是不需要**TxParamSetupReq**命令时，则终端不会对该命令进行任何处理并且不会进行回复。

