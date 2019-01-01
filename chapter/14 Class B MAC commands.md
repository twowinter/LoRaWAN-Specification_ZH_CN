## 第14章 Class B Mac命令

所有在 Class A 协议中描述的命令都应该在 Class B 中实现。Class B 协议还额外添加了如下的 **MAC** 命令。

<table>
   <tr>
      <td rowspan ="2" ><b>CID</b></td>
      <td rowspan ="2" ><b>Command</b></td>
      <td colspan ="2" ><b>由谁传输</b></td>
      <td rowspan ="2" ><b>描述</b></td>
   </tr>
   <tr>
      <td>终端</td>
      <td>网关</td>
   </tr>
   <tr>
      <td>0x10</td>
      <td><b>PingSlotInfoReq</b></td>
      <td>x</td>
      <td></td>
      <td>终端设备用于将 ping 单播时隙数据速率和周期性传送给网络服务器</td>
   </tr>
   <tr>
      <td>0x10</td>
      <td><b>PingSlotInfoAns</b></td>
      <td></td>
      <td>x</td>
      <td>用于网络应答<b>PingInfoSlotReq</b>命令</td>
   </tr>
   <tr>
      <td>0x11</td>
      <td><b>PingSlotChannelReq</b></td>
      <td></td>
      <td>x</td>
      <td>用于网络服务器设置一个终端的单播 ping 通道</td>
   </tr>
   <tr>
      <td>0x11</td>
      <td><b>PingSlotFreqAns</b></td>
      <td>x</td>
      <td></td>
      <td>终端用于应答<b>PingSlotChannelReq</b>命令</td>
   </tr>
   <tr>
      <td>0x12</td>
      <td><b>BeaconTimingReq</b></td>
      <td>x</td>
      <td></td>
      <td>用于终端向网络请求下一个信标时间和信道</td>
   </tr>
   <tr>
      <td>0x12</td>
      <td><b>BeaconTimingAns</b></td>
      <td></td>
      <td>x</td>
      <td>用于网络应答<b>BeaconTimingReq</b>命令</td>
   </tr>
   <tr>
      <td>0x13</td>
      <td><b>BeaconFreqReq</b></td>
      <td></td>
      <td>x</td>
      <td>用于网络服务器修改终端希望接收信标广播的频率</td>
   </tr>
   <tr>
      <td>0x13</td>
      <td><b>BeaconFreqAns</b></td>
      <td>x</td>
      <td></td>
      <td>用于终端应答<b>BeaconFreqReq</b>命令</td>
   </tr>
</table>

### <a name="14.1">14.1 PingSlotInfoReq</a>

终端可以使用**PingSlotInfoReq**命令来告知服务器它的单播 ping 时隙周期以及期望的数据速率。这个命令只能用于告知服务器单播 ping 时隙的参数。多播 ping 时隙完全由应用程序进行定义而不应该使用此命令设置。

<table>
   <tr>
      <td><b>Size(bytes)</b></td>
      <td>1</td>
   </tr>
   <tr>
      <td><b>PingSlotInfoReq Payload</b></td>
      <td>Periodicity & data rate</td>
   </tr>
</table>


<table>
   <tr>
      <td><b>Bit#</b></td>
      <td>7</td>
      <td>[6:4]</td>
      <td>[3:0]</td>
   </tr>
   <tr>
      <td><b>Periodicity & data rate</b></td>
      <td>RFU</td>
      <td>Periodicity</td>
      <td>Data rate</td>
   </tr>
</table>

**Periodicity**字段是一个无符号3位整数，用于终端当前使用的 ping 时隙周期的编码，编码公式如下:

                                 pingSlotPeriod = 2^Periodicity(单位是s)

- Periodicity = 0 表示终端每1s打开一个 ping 时隙。
- Periodicity = 7 表示终端每128s打开一个 ping 时隙，这是 LoRaWAN Class B 协议中所支持的最大 ping 时隙周期。

**Data rate** 字段表示终端期望收到 ping 时隙的数据率。它使用的编码方式与 Class A 协议中所描述的**LinkAdrReq**命令相同。

服务器需要知道终端的 ping 时隙周期或者期望的数据率，否则 Class B 的下行将不会成功。因此终端在 **PingSlotInfoReq** 命令发出之后必须收到 **PingSlotInfoAns** 命令的回复才能从 Class A 切换到 Class B。当终端需要改变 ping 时隙周期以及数据率时，需要先恢复到 Class A 模式，在发送 **PingSlotInfoReq** 命令并且收到服务器端的 **PinSlotInfoAns** 命令回复之后，就可以使用新的参数切换回 Class B模式。**PingSlotInfoReq** 命令可以和 **FHDRFOpt** 字段里的任何 MAC 命令进行连接，如 Class A 协议中的帧格式所述。

### <a name="14.2">14.2 BeaconFreqReq</a>

该命令由服务器发往终端，用于修改终端期望信标的频率。

<table>
   <tr>
      <td><b>Octets</b></td>
      <td>3</td>
   </tr>
   <tr>
      <td><b>PingSlotChannelReq Payload</b></td>
      <td>Frequency</td>
   </tr>
</table>

Frequency字段和Class A协议中定义的 **NewChannelReq** MAC命令有着相同的编码方式。

**Frequency**是24位的无符号整数。实际的信标信道频率是100 x Frequency,单位Hz。信标的信道以 100Hz 为基本单位，变化范围在 100MHz 到 1.67GHz 之间。终端必须检查该频率是不是射频硬件所允许的范围，不是则需要返回错误。

一个有效的非零频率将会强制终端以一个固定的频率信道去监听信标，即使默认是指定跳频信标(即美国ISM频段)。

若频率为0则表示终端使用“信标物理层”部分所定义的默认信标频率计划，在适用的情况之下恢复成跳频信标搜索。

### <a name="14.3">14.3 PingSlotChannnelReq</a>

该命令由服务器发往终端，用于修改终端期望下行 ping 的频率。

<table>
   <tr>
      <td><b>Octets</b></td>
      <td>3</td>
      <td>1</td>
   </tr>
   <tr>
      <td><b>PingSlotChannelReq Payload</b></td>
      <td>Frequency</td>
      <td>DrRange</td>
   </tr>
</table>

Frequency 字段和Class A协议中定义的 **NewChannelReq** MAC 命令有着相同的编码方式。

**Frequency**是24位的无符号整数。实际的信标信道频率是100 x Frequency,单位Hz。信标的信道以100Hz为基本单位，变化范围在100MHz到1.67GHz之间。终端必须检查该频率是不是射频硬件所允许的范围，不是则需要返回错误。

若频率为0则表示终端使用默认信标频率计划。

**DrRange**是信道允许的数据率范围。这个字节被等分为两个4位。

<table>
   <tr>
      <td><b>Bits</b></td>
      <td>[7:4]</td>
      <td>[3:0</td>
   </tr>
   <tr>
      <td><b>DrRange</b></td>
      <td>Max data rate</td>
      <td>Min data rate</td>
   </tr>
</table>

按照LoRaWAN地区参数文件[PARAMS]的定义，“Min data rate”字段指定了信道允许的最低数据率。例如0在欧盟物理层中指定DR0/125 kHz。类似的，“Max data rate”指定了最高数据率。例如在欧盟规范中，DrRange = 0x77 意味着在信道上只允许50 kpbs GFSK，DrRange = 0x50意味着支持 DR0/125kHz 到 DR5/125 kHz的数据率。

将终端接收到以上的命令之后，需要以**PingSlotFreqAns**命令进行回复。这个MAC命令的载荷包含了以下的信息:

<table>
   <tr>
      <td><b>Size(bytes)</b></td>
      <td>1</td>
   </tr>
   <tr>
      <td><b>pingSlotChannelAns Payload</b></td>
      <td>Status</td>
   </tr>
</table>

**Status**的位段有以下含义:

<table>
   <tr>
      <td><b>Bits</b></td>
      <td>[7:2]</td>
      <td>1</td>
      <td>0</td>
   </tr>
   <tr>
      <td><b>Statusd</b></td>
      <td>RFU</td>
      <td>Data rate range ok</td>
      <td>Channel frequency ok</td>
   </tr>
</table>

<table>
   <tr>
      <td></td>
      <td><b>Bit = 0</b></td>
      <td><b>Bit = 1</b></td>
   </tr>
   <tr>
      <td><b>Data rae range ok</b></td>
      <td>设置的数据率超过了该终端当前定义的范围，保持之前的数据率范围</td>
      <td>数据率范围与终端兼容</td>
   </tr>
   <tr>
      <td><b>Channel frequency ok</b></td>
      <td>终端无法使用该频率，保持之前的频率</td>
      <td>终端可以使用这个频率</td>
   </tr>
</table>

### <a name="14.4">14.4 BeaconTimingReq</a>

终端使用该命令来请求下一个信标的时间以及信道，该MAC命令没有载荷。**BeaconTimingReq** & **BeaconTimingAns**机制仅仅用于加快刚开始的信标搜索以降低终端的能量需求。

网络在给定的时间周期内可能只应答有限数量的请求。终端不能期望在发出**BeaconTimingReq**命令之后立刻收到**BeaconTimingAns**命令的应答。想要切换到Class B模式的处于Class A模式的终端一小时之内不应该发送超过一个**BeaconTimingReq**命令。

需要快速锁定信标的终端必须实现自主信标查找算法。

### <a name="14.5">14.5 BeaconTimingAns</a>

网络用该命令来应答**BeaconTimingReq**命令的请求。

<table>
   <tr>
      <td><b>Size(bytes)</b></td>
      <td>2</td>
      <td>1</td>
   </tr>
   <tr>
      <td><b>BeaconInfoReqPayload</b></td>
      <td>Delay</td>
      <td>Channel</td>
   </tr>
</table>

**"Delay"**字段是一个16位的无符号整数。如果当前下行帧的结束与下一个信标帧的开始之间的剩余时间记为 RTime:

                                  30ms x (Delay+1) > RTime >= 30ms x Delay

在信标交替使用多个信道的网络中，**“Channel”**字段是下一个信标广播所使用的信道编号。对于信标广播频率固定的网络来说，这个字段值为0。