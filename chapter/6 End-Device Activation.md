

## **第6章 终端激活**

为了加入LoRaWAN网络，每个终端需要初始化及激活。

终端的激活有两种方式，一种是空中激活 Over-The-Air Activation (OTAA)，当设备部署和重置时使用; 另一种是独立激活 Activation By Personalization (ABP)，此时初始化和激活这两步就在一个步骤内完成。

> twowinter 备注： ABP 这个词不太好翻译，通常会翻成个性化激活，也就是通过独立配置参数的方式激活。但总感觉少点味道，与空中激活摆在一起，感觉独立激活这个词在语义上更有并列感。当然这是我的主观感觉，建议大家和同行交流时，还是说 ABP激活 吧。

### <a name="6.1">6.1 终端激活后的数据存储</a>

激活后，终端会存储如下信息：设备地址(DevAddr)，应用ID(AppEUI)，网络会话密钥(NwkSKey)，应用会话密钥(AppSKey)。

- 6.1.1 终端地址(DevAddr)

终端地址(DevAddr)由可标识当前网络设备的32位ID所组成，具体格式如下：

<table>
   <tr>
      <td><b>Bit#</b></td>
      <td>[31..25]</td>
      <td>[24..0]</td>
   </tr>
   <tr>
      <td><b>DevAddr bits</b></td>
      <td>NwkID</td>
      <td>NwkAddr</td>
   </tr>
</table>

它的高7位是NwkId，用来区别同一区域内的不同网络，另外也保证防止节点窜到别的网络去。它的低25位是NwkAddr，是终端的网络地址，可以由网络管理者来分配。

- 6.1.2 应用ID(AppEUI)

AppEUI是一个类似IEEE EUI64的全球唯一ID，标识终端的应用提供者。
APPEUI在激活流程开始前就存储在终端中。

- 6.1.3 网络会话密钥(NwkSKey)

NwkSKey被终端和网络服务器用来计算和校验所有消息的MIC，以保证数据完整性。也用来对单独MAC的数据消息载荷进行加解密。

- 6.1.4 应用会话密钥(AppSKey)

AppSKey被终端和网络服务器用来对应用层消息进行加解密。当应用层消息载荷有MIC时，也可以用来计算和校验该应用层MIC。

### <a name="6.2">6.2 空中激活 OTAA</a>

针对空中激活，终端必须按照加网流程来和网络服务器进行数据交互。如果终端丢失会话消息，则每次必须重新进行一次加网流程。
加网流程需要终端准备好如下这三个参数：DevEUI，AppEUI，AppKey。

APPEUI在上面的6.1.2已经做了描述。

> 注意：对于空中激活，终端不会初始化任何网络密钥。只有当终端加入网络后，才会被分配一个网络会话密钥，用来加密和校验网络层的传输。通过这样，使得终端在不同网络间的漫游处理变得方便。同时使用网络和应用会话密钥，使得网络服务器中的应用数据，不会被网络提供者读取或者篡改。

- <a name="6.2.1">6.2.1 终端 ID (DevEUI)</a>  
DevEUI 是一个类似IEEE EUI64的全球唯一ID，标识唯一的终端设备。

- <a name="6.2.2">6.2.2 应用密钥(AppKey)</a>  

AppKey 是由应用程序拥有者分配给终端，很可能是由应用程序指定的根密钥来衍生的，并且受提供者控制。当终端通过空中激活方式加入网络，AppKey用来产生会话密钥NwkSKey和AppSKey，会话密钥分别用来加密和校验网络层和应用层数据。

- <a name="6.2.3">6.2.3 加网流程</a>

从终端角度看，加网流程是由和服务器的两个MAC命令交互组成的，分别是 join request 和 join accept。

- <a name="6.2.4">6.2.4 Join-request 消息</a>

加网流程总是由终端发送 join-request 来发起。

<table>
   <tr>
      <td><b>Size (bytes)</b></td>
      <td>8</td>
      <td>8</td>
	  <td>2</td>
   </tr>
   <tr>
      <td><b>Join Request</b></td>
      <td>AppEUI</td>
      <td>DevEUI</td>
	  <td>DevNonce</td>
   </tr>
</table>

join-request 消息包含了AppEUI 和 DevEUI ，后面还跟了2个字节的声明 DevNonce。

DevNonce 是一个随机值。网络服务器为每个终端记录过去的 DevNonce 数值，如果相同设备发出相同的 DevNonce 的join request就会忽略。

join-request 消息的MIC数值(见第4章 MAC帧格式)按照如下公式计算：

> cmac = aes128_cmac(AppKey, MHDR | AppEUI | DevEUI | DevNonce)
> MIC = cmac[0..3]

join-request 消息不用加密。


- <a name="6.2.5">6.2.5 Join-accept 消息</a>

如果网络服务器准许终端加入网络，就会用 **join-accept** 对 **join-request** 进行应答。**join-accept** 是作为一个普通下行帧进行下发的，唯一的区别是它使用的是 JOIN_ACCEPT_DELAY1 或者 JOIN_ACCEPT_DELAY2 (分别代替 RECEIVE_DELAY1 和 RECEIVE_DELAY2 )但是它所使用的两个接收窗口的信道频率和数据率和 LoRaWAN 地区参数文件[PARAMS]"接收窗口"部分所描述的 RX1 和 RX2 接收窗口相同。

如果 **join-request** 不被接受，则终端不会收到回应。

**join-accept** 消息的帧格式包括3字节的应用随机数(**AppNonce**)，网络标识符(**NetID**)，终端地址(**DevAddr**)，TX和RX之间的延时(**RxDelay**)，用于终端所加入的网络的可选信道频率列表(**CFList**)。**CFList** 的选择是由区域指定的，在 LoRaWAN 地区参数文件[PARAMS]中进行定义。

<table>
   <tr>
      <td><b>Size (bytes)</b></td>
      <td>3</td>
      <td>3</td>
      <td>4</td>
      <td>1</td>
      <td>1</td>
      <td>(16)Optional</td>
   </tr>
   <tr>
      <td><b>Join Accpet</b></td>
      <td>AppNonce</td>
      <td>NetID</td>
      <td>DevAddr</td>
      <td>DLSettings</td>
      <td>RXDelay</td>
      <td>CFList</td>
   </tr>
</table>

**AppNonce**是由网络服务器所提供的一个随机值或者某些形式的唯一ID，用于终端得到两个会话密钥**NwkSKey**和**AppSKey**，如下:

    NwkSKey = aes128_encrypt(AppKey, 0x01 | AppNonce | NetID | DevNonce | pad 16 )
    AppSKey = aes128_encrypt(AppKey, 0x02 | AppNonce | NetID | DevNonce | pad 16 )

**join-accept**的 MIC 值由如下计算得到:

    cmac = aes128_cmac(AppKey,MHDR | AppNonce | NetID | DevAddr | DLSettings | RxDelay | CFList) 
    MIC = cmac[0..3]

**join-accept**消息是使用**AppKey**进行加密的，如下:

    aes128_decrypt(AppKey, AppNonce | NetID | DevAddr | DLSettings | RxDelay | CFList | MIC) 

> 注意:网络服务器在 ECB 模式下使用一个 AES 解密操作去对 **join-accept** 消息进行加密，因此终端就可以使用一个 AES 加密操作去对消息进行解密。这样终端只需要去实现 AES 加密而不是 AES 解密。

> 注意:建立这两个会话密钥使得 网络服务器 中的网络运营商无法窃听应用层数据。在这样的设置中，应用提供商必须支持网络运营商处理终端的加网以及为终端生成 NwkSkey。同时应用提供商向网络运营商承诺，它将承担终端所产生的任何流量费用并且保持用于保护应用数据的AppSKey的完全控制权。

**NetID**的格式如下所述:**NetID**的7个最低有效位称为**NwkID**并且和之前所描述的终端的短地址的7个最高有效位相对应。保留的17个最高有效位可以由网络运营商进行自由选择。

**DLsettings**字段包含了下行配置:

<table>
   <tr>
      <td><b>Bits</b></td>
      <td>7</td>
      <td>6:4</td>
      <td>3:0</td>
   </tr>
   <tr>
      <td><b>DLsettings</b></td>
      <td>RFU</td>
      <td>RX1DRoffset</td>
      <td>RX2 Data rate</td>
   </tr>
</table>

**RX1DRoffset**位域设置上行数据速率和RX1下行数据速率的偏移量。默认情况下偏移量为0（意思就是上行数据速率与下行数据速率相等)。偏移量用于考虑一些地区的基站最大功率密度限制和平衡上下行射频链路预算。

上行和下行的数据率之间的实际关系是由区域指定的，在LoRaWAN地区参数文件[PARAM]中进行定义。

延时**RxDelay**和**RXTimingSetupReq**里的**Delay**字段有着相同的约定。

### <a name="6.3">6.3 独立激活 ABP</a>

在某些情况下，终端可以独立激活。独立激活是让终端绕过 join request - join accept的加网流程，直接加入到指定网络中。

独立激活终端，意味着 DevAddr 和两个会话密钥 NwkSKey 和 AppSKey 直接存储在终端中，而不是DevEUI，AppEUI，AppKey。终端在一开始就配置好了入网必要的信息。

每个终端必须要有唯一的 NwkSKey 和 AppSKey。这样，一个设备的密钥被破解也不会造成其他设备的安全性危险。创建那些密钥的过程中，密钥不允许通过公开可用信息获得(例如节点地址)。