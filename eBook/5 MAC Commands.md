
## **第5章 MAC命令**

对网络管理者而言，有一套专门的MAC命令用来在服务器和终端MAC层之间交互。这套MAC命令对应用程序(不管是服务器端还是终端设备的应用程序)是不可见的。

单个数据帧中可以携带MAC命令，要么在FOpts字段中捎带，要么在独立帧中将FPort设成0后放在FRMPayload里。如果采用FOpts捎带的方式，MAC命令是不加密并且不长度超过15字节。如果采用独立帧放在FRMPayload的方式，那就必须采用加密方式，并且不超过FRMPayload的最大长度。

> 注意：如果MAC命令不想被窃听，那就必须以独立帧形式放在FRMPayload中。

每个MAC命令是由 1字节CID 跟着一段可能为空的字节序列 组成的。

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
### <a name="5.1">5.1 Link Check 命令 (LinkCheckReq, LinkCheckAns)</a>

### <a name="5.2">5.2 Link ADR 命令(LinkADRReq, LinkADRAns)</a>

通过 LinkADRReq 命令，NS(网络服务器)可以调整终端的速率。

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

所请求的数据速率(DataRate)和发射功率(TXPower)是根据区域规定，体现在[LoRaWAN协议中文版_配套文件 地区参数(物理层) ](http://blog.csdn.net/iotisan/article/details/55056092)中。命令中的发射功率字段指的是设备可操作的最大发射功率。如果命令中的发射功率高于终端实际发射功率的最大值，终端也要应答成功，这种情况下，将终端的发射功率尽可能提高到最大值。 ChMask 字段指示了上行的可用信道，从最低位bit0表示开始。

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

Redundancy 字段中的 NbTrans 位域，指的是每个上行消息的发送个数，这仅对 "unconfirmed" 消息有作用。对于单帧发送情况相应的默认值为1，有效范围是[1:15]。如果收到 NbTrans == 0，终端需要用默认值。这个位域可以被NS(网络服务器)用来控制节点上行的 Redundancy 从而获得QOS(服务质量)。在重传帧时节点通常会调频，每次重传不用等到接收窗口超时。只要在RX1期间收到下行消息，该上行消息则不再进行任何重传。对于 Class A 设备，RX2时隙的接收也是一样处理。

ChMaskCntl 位域和之前定义的 ChMask 字段有关，它控制了ChMask所指定的16个信道块。也可以对所用信道进行全局的打开或关闭。这个位域的使用是根据区域规定，体现在[LoRaWAN协议中文版_配套文件 地区参数(物理层) ](http://blog.csdn.net/iotisan/article/details/55056092)中。

NS(网络服务器)可能会在单个下行帧中包含多个 LinkAdrReq 命令。终端为了配置 channel mask ，将会按照下行消息中的命令块的顺序，逐一地处理所有的 LinkAdrReq 消息。 终端可能会接收或者拒绝命令块中所有 channel mask 的控制，在逐个 LinkAdrAns 命令块中体现连续的 Channel Mask ACK 状态，来指示相应的 channel mask 接受与否。 终端在连续命令块时只处理最后一个消息中的 DataRate, TXPower 和 NbTrans 字段。终端需要在每一个 LinkAdrAns 命令中体现 ACK 状态，来指示对这些最终设置的接受与否。


信道频点信息是按地区规定，在第6章中有定义。终端使用 LinkADRAns 命令来应答 LinkADRReq 命令。终端为了配置

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
      <td><b> /b></td>   
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
      <td>所请求的数据速率，终端无法识别，或者无法应用在当前信道中。命令被丢弃，终端状态不变。</td>
	  <td>数据速率成功设置。</td>
   </tr>
   <tr>
      <td><b>Power ACK</b></td>
      <td>所请求的发射功率不能在终端上执行。命令被丢弃，终端状态不变。</td>
	  <td>功率等级成功设置。</td>
   </tr>
</table>

如果这三个位中有任何一位等于0，则命令没有成功，节点保持之前的状态。

### <a name="5.3">5.3 终端发射占空比(DutyCycleReq, DutyCycleAns)</a>

### <a name="5.4">5.4 接收窗口参数(RXParamSetupReq,RXParamSetupAns)</a>


### <a name="5.5">5.5 终端状态(DevStatusReq, DevStatusAns)</a>

通过 DevStatusReq 命令，NS(网络服务器)可以获取终端的状态信息。该命令无载荷。一旦终端收到 DevStatusReq 命令，则会回复 DevStatusAns 命令。

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



### <a name="5.6">5.6 信道的创建和修改(NewChannelReq, NewChannelAns, DlChannelReq, DlChannelAns)</a>

### <a name="5.7">5.7 TX 和 RX 之间的延时设置(RXTimingSetupReq, RXTimingSetupAns)</a>

### <a name="5.8">5.8 终端发送参数(TxParamSetupReq, TxParamSetupAns)</a>

---
未完待续。

