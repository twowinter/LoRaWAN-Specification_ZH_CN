## 第15章 信标(Class B选项)

### <a name="15.1">15.1 信标物理层</a>

所有网关除了可以为终端和网络服务器转发消息，还可以通过在可配置的固定时间间隔上发送信标(**BEACON_INTERVAL**)来参与提供一个时间同步机制。所有信标都以无线分组隐式模式进行发送，即没有 LoRa 物理帧头和 CRC 校验。

<table>
   <tr>
      <td><b>PHY</b></td>
      <td>Preamble</td>
      <td>BCNPayload</td>
   </tr>
</table>

信标的 **Preamble** 开始于(长于默认)10个未调制符号。这允许终端实现低功耗占空比信标搜索。

信标的帧长度与无线电物理层紧密耦合。因此实际的帧长度可能从一个区域实现变为另一个区域实现。更改字段在下面的部分以粗体显示。

#### 15.1.1 欧盟 863-870MHz ISM 频段

信标使用下面的设置进行传送:

<table>
   <tr>
      <td>DR</td>
      <td>3</td>
      <td>对应于125kHz带宽的SF9扩频因子</td>
   </tr>
   <tr>
      <td>CR</td>
      <td>1</td>
      <td>编码率=4/5</td>
   </tr>
   <tr>
      <td>frequency</td>
      <td>869.525MHz</td>
      <td>这是推荐的允许+27 dBm EIRP的频率。</td>
   </tr>
</table>

只要符合ETSI的要求，网络运营商也可以使用一个不同的频率。

信标帧的内容如下:

<table>
   <tr>
      <td><b>Size(bytes)</b></td>
      <td>3</td>
      <td>4</td>
      <td>1</td>
      <td>7</td>
      <td>2</td>
   </tr>
   <tr>
      <td><b>BCNPayload</b></td>
      <td>NetID</td>
      <td>Time</td>
      <td><b>CRC</b></td>
      <td>GwSpecific</td>
      <td><b>CRC</b></td>
   </tr>
</table>

#### 15.1.2 美国 902-928 MHz ISM 频段

信标使用下面的设置进行传送:

<table>
   <tr>
      <td>DR</td>
      <td>10</td>
      <td>对应于500kHz带宽的SF10扩频因子</td>
   </tr>
   <tr>
      <td>CR</td>
      <td>1</td>
      <td>编码率=4/5</td>
   </tr>
   <tr>
      <td>frequency</td>
      <td>923.3到927.5MHz(以600kHz为单位)</td>
      <td>信标的传送与Class A规范中定义的下行链路所使用的信道相同。</td>
   </tr>
</table>

用于给定的信标所使用的下行链路信道是:

                          Channel = [floor(beacon_time/beacon_preiod)] modulo 8

- beacon_time是信标帧“Time”4字节字段的整数值。

- beacon_period是信标帧的周期，128s

- floor(x)意思是四舍五入到临近x的整数。

> 例子:第一个信标在923.3MHz上进行传送，第二次在932.9MHz，第九次再一次在923.3MHz进行传送。

<table>
   <tr>
      <td>Beacon channel nb</td>
      <td>Frequency[MHz]</td>
   </tr>
   <tr>
      <td>0</td>
      <td>923.3</td>
   </tr>
   <tr>
      <td>1</td>
      <td>923.9</td>
   </tr>
   <tr>
      <td>2</td>
      <td>924.5</td>
   </tr>
   <tr>
      <td>3</td>
      <td>925.1</td>
   </tr>
   <tr>
      <td>4</td>
      <td>925.7</td>
   </tr>
   <tr>
      <td>5</td>
      <td>926.3</td>
   </tr>
   <tr>
      <td>6</td>
      <td>926.9</td>
   </tr>
   <tr>
      <td>7</td>
      <td>927.5</td>
   </tr>
</table>

信标帧的内容如下:

<table>
   <tr>
      <td><b>Size(bytes)</b></td>
      <td>3</td>
      <td>4</td>
      <td>2</td>
      <td>7</td>
      <td>1</td>
      <td>2</td>
   </tr>
   <tr>
      <td><b>BCNPayload</b></td>
      <td>NetID</td>
      <td>Time</td>
      <td><b>CRC</b></td>
      <td>GwSpecific</td>
      <td><b>RFU</b></td>
      <td><b>CRC</b></td>
   </tr>
</table>

### <a name="15.2">15.2 信标帧内容</a>

信标帧的**BCNPayload**载荷由一个网络的公共部分和一个网关的特定部分组成。

<table>
   <tr>
      <td><b>Size(bytes)</b></td>
      <td>3</td>
      <td>4</td>
      <td>1/2</td>
      <td>7</td>
      <td>0/1</td>
      <td>2</td>
   </tr>
   <tr>
      <td><b>BCNPayload</b></td>
      <td>NetID</td>
      <td>Time</td>
      <td>CRC</td>
      <td>GwSpecific</td>
      <td>RFU</td>
      <td>CRC</td>
   </tr>
</table>

网络的公共部分包含了一个网络的标识符**NetID**，用于唯一标识发送信标的网络还有一个时间戳**Time**(单位为s)，这个时间戳是从1970年1月1日的[Coordinated Universal Time](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)(UTC) 00:00:00开始计时的。信标网络的公共部分的完整性由8位或者16位的 CRC 校验码来进行保护，是8位还是16位取决于PHY层参数。CRC-16是在IEEE 802.15.4-2003 7.2.1.8部分所定义的NetID+Time字段上进行计算。当需要8位CRC时计算CRC-16的低8位就会被使用。

例如：这是一个有效的 EU868 信标帧:

                    AA BB CC| 00 00 02 CC | 7E | 00 | 01 20 00 | 00 81 03 | DE 55

字节是从左向右进行传送。相对应字段的值是：

<table>
   <tr>
      <td><b>Field</b></td>
      <td>NetID</td>
      <td>Time</td>
      <td><b>CRC</b></td>
      <td>InfoDesc</td>
      <td>lat</td>
      <td>long</td>
      <td><b>CRC</b></td>
   </tr>
   <tr>
      <td><b>Value Hex</b></td>
      <td>CCBBAA</td>
      <td>CC020000</td>
      <td>7E</td>
      <td>0</td>
      <td>002001</td>
      <td>038100</td>
      <td>55DE</td>
   </tr>
</table>

NetID+Time字段的CRC-16校验码是0xC87E，但是在这种情况之下只使用低8位。

**NetID**的7个最低有效位被称之为**NwkID** ，与终端短地址的7位最高有效位相匹配。相邻的或者重叠的网络必须有不同的**NwkID**。

网络的特定部分提供网关发送一个信标的额外信息，因此对于每个网关可能不同。当RFU字段适用时(区域特定)应该等于0。可选择的部分由GwSpecific+RFU字段计算出的CRC-16校验码进行保护。CRC-16的定义与强制部分相同。

例如：这是一个有效的 美国900 信标

<table>
   <tr>
      <td><b>Field</b></td>
      <td>NetID</td>
      <td>Time</td>
      <td><b>CRC</b></td>
      <td>InfoDesc</td>
      <td>lat</td>
      <td>long</td>
      <td>RFU</td>
      <td><b>CRC</b></td>
   </tr>
   <tr>
      <td><b>Value Hex</b></td>
      <td>CCBBAA</td>
      <td>CC020000</td>
      <td>C87E</td>
      <td>0</td>
      <td>002001</td>
      <td>038100</td>
      <td>00</td>
      <td>D450</td>
   </tr>
</table>

在空中，字节以以下顺序进行发送：

                    AA BB CC| 00 00 02 CC | 7E C8 | 00 | 01 20 00 | 00 81 03 | 00 | 50 D4

监听和同步网络的公共部分足以在Class B模式去操作一个固定的终端。一个移动的终端也应该解调出信标的网关特定部分，以便信标在从一个网络移动到另一个网络时可以通知网络服务器。

> **注意**：如前所述，所有的网关在同一个时间点(即时间同步)发送他们的信标，因此对于网络公共部分来说，即使一个终端同时从多个网关接收信标，监听的终端也不存在明显的空中冲突。至于网关的特定部分，当冲突发生时，位于多个网关附近的一个终端仍然有能力以高概率去解码最强的信标。

### <a name="15.3">15.3 信标GwSpecific字段格式</a>

**GwSpecific**字段的内容如下所述:

<table>
   <tr>
      <td><b>Size(bytes)</b></td>
      <td>1</td>
      <td>6</td>
   </tr>
   <tr>
      <td><b>GwSpecific</b></td>
      <td>InfoDesc</td>
      <td>Info</td>
   </tr>
</table>

**InfoDesc**描述符描述了如何解释**Info**字段信息。

<table>
   <tr>
      <td><b>InfoDesc</b></td>
      <td><b>Meaning</b></td>
   </tr>
   <tr>
      <td>0</td>
      <td>网关第一天线的GPS坐标</td>
   </tr>
   <tr>
      <td>1</td>
      <td>网关第二天线的GPS坐标</td>
   </tr>
   <tr>
      <td>2</td>
      <td>网关第三天线的GPS坐标</td>
   </tr>
   <tr>
      <td>3:127</td>
      <td>RFU</td>
   </tr>
   <tr>
      <td>128:255</td>
      <td>为自定义网络特定广播预留</td>
   </tr>
   <tr>
      <td></td>
      <td></td>
   </tr>
</table>

对于一个单一的全向天线网关，当广播GPS坐标时**InfoDesc**的值为0。例如，对于一个具有3扇区电线的站点，第一天线广播信标时**InfoDesc**的值为0，第二天线广播信标时**InfoDesc**的值为1，等等...


#### 15.3.1 网关GPS坐标:InfoDesc = 0，1或者2

对于**InfoDesc**=0，1或者2，**Info**字段所包含的内容编码了天线广播信标的GPS坐标

<table>
   <tr>
      <td><b>Size(bytes)</b></td>
      <td>3</td>
      <td>3</td>
   </tr>
   <tr>
      <td><b>Info</b></td>
      <td>Lat</td>
      <td>Lng</td>
   </tr>
</table>

纬度和经度字段(分别对应于**Lat**和**Lng**)编码了网关的地理位置，如下:

- 南北纬度使用24位字来进行编码，-2^23对应于南90°(南极点)，2^23对应于北90°(北极点)。赤道对应于0。

- 东西经度使用24位字来进行编码，-2^23对应于西180°，2^23对应于东180°。格林尼治子午线对应于0。

### <a name="15.4">15.4 信标精确定时</a>

信标从[Coordinated Universal Time(UTC)](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) ，1970年1月1日00:00:00 加上 NwkID 加 TBeaconDelay 开始，每128秒发送一次。因此信标是在 Coordinated Universal Time(UTC) 1970年1月1日00:00:00 之后 
        Bt = k*128 + NwkID + TBeaconDelay 
的时间点进行发送。

其中 k 是最小的整数: k*128 + NwkID > T

其中 T = 从1970年1月1日的 Coordinated Universal Time(UTC) 00:00:00 以后的秒数。

> **注意**: T 不是 Unix 时间。类似于 GPS 时间，不像 Unix 时间，T 是严格单调递增的并且不受闰秒的影响。


> **KevinCao注**:闰秒，是指为保持协调世界时接近于世界时时刻，由国际计量局统一规定在年底或年中（也可能在季末）对协调世界时增加或减少1秒的调整。由于地球自转的不均匀性和长期变慢性（主要由潮汐摩擦引起的），会使世界时（民用时）和原子时之间相差超过到±0.9秒时，就把协调世界时向前拨1秒（负闰秒，最后一分钟为59秒）或向后拨1秒（正闰秒，最后一分钟为61秒）； 闰秒一般加在公历年末或公历六月末。

其中**TBeaconDelay**是网络的特定延时，范围在0到50ms之间。**TBeaconDelay**在不同的网络之间可能不同，并且它意味着通信时允许网关有轻微的延时。**TBeaconDelay**对于给定的一个网络中的所有网关必须相同。**TBeaconDelay**必须小于50ms。所有终端的ping时隙使用信标传输时间作为定时基准。因此网络在调度Class B下行时需要将TBeaconDelay时间考虑在内。

### <a name="15.5">15.5 网络下行路由更新要求</a>

当网络使用Class B下行时隙去与终端进行通信时，当网络接收到最后一个上行数据帧之后，它会从最接近终端的一个网关进行下行数据发送。因此网络服务器需要追踪Class B终端的粗略位置。

只要一个Class B终端移动并且改变网络，它需要告知服务器以更新下行路由。可以通过发送“confirmed”类型或者“unconfirmed”类型的上行数据帧来完成更新，可能没有应用载荷。

终端可以在2个基础策略之间做出选择:

- 系统周期上行:最简单的方式，不需要对信标的“gateway specific”字段解调。只适用于缓慢移动的或者固定的终端。对于这些周期性上行链路没有要求。

- 网络改变的上行:终端对信标的“gateway specific”字段进行解调，检测到广播其解调的信标的网关的ID已经改变并且发送上行数据帧。在这种情况之下终端应当遵守信标解调和上行数据帧发送之间0~120s的伪随机延时。这用于确保当信标广播之后，同一个信标周期内进入或者离开网络的多个Class B设备的上行数据帧不会立即系统性地同时发生。

无法告知网络改变将会导致 Class B 的下行暂时性地无法运行。网络服务器可能必须等到下一个终端上行才能传输下行。
