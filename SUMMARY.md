
# Contents

## [前言](README.md)

* [第1章 介绍](eBook/1 Introduction.md)
    * [1.1 LoRaWAN Classes](eBook/1 Introduction.md#1.1)
    * [1.2 文档约定](eBook/1 Introduction.md#1.2)

* [第2章 LoRaWAN Classes 类型介绍](eBook/2 Introduction on LoRaWAN options.md)
    * [2.1 LoRaWAN Classes](eBook/2 Introduction on LoRaWAN options.md#2.1)
    * [2.2 文档范围](eBook/2 Introduction on LoRaWAN options.md#2.2)

## [CLASS A - ALL END-DEVICE 所有终端](eBook/CLASS A - ALL END-DEVICE.md)

* [第3章 PHY 帧格式](eBook/3 Physical Message Formats.md)
    * [3.1 上行消息](eBook/3 Physical Message Formats.md#3.1)
    * [3.2 下行消息](eBook/3 Physical Message Formats.md#3.2)
    * [3.3 接收窗口](eBook/3 Physical Message Formats.md#3.3)
        * [3.3.1 第一接收窗口的信道，数据速率和启动](eBook/3 Physical Message Formats.md#3.3.1)
        * [3.3.2 第二接收窗口的信道，数据速率和启动](eBook/3 Physical Message Formats.md#3.3.2)
        * [3.3.3 接收窗口的持续时间](eBook/3 Physical Message Formats.md#3.3.3)
        * [3.3.4 接收方在接收窗口期间的处理](eBook/3 Physical Message Formats.md#3.3.4)
        * [3.3.5 网络发送消息给终端](eBook/3 Physical Message Formats.md#3.3.5)
        * [3.3.6 接收窗口的重要事项](eBook/3 Physical Message Formats.md#3.3.6)
		
* [第4章 MAC帧格式](eBook/4 MAC Message Formats.md)
    * [4.1 MAC层](eBook/4 MAC Message Formats.md#4.1)
    * [4.2 MAC头(MHDR字段)](eBook/4 MAC Message Formats.md#4.2)
        * [4.2.1 第一接收窗口的信道，数据速率和启动](eBook/4 MAC Message Formats.md#4.2.1)
        * [4.2.2 数据消息的主版本(Major位字段)](eBook/4 MAC Message Formats.md#4.2.2)		
    * [4.3 MAC载荷(MACPayload)](eBook/4 MAC Message Formats.md#4.3)
        * [4.3.1 帧头(FHDR)](eBook/4 MAC Message Formats.md#4.3.1)
        * [4.3.2 端口字段(FPort)](eBook/4 MAC Message Formats.md#4.3.2)
        * [4.3.3 MAC帧载荷加密(FRMPayload)](eBook/4 MAC Message Formats.md#4.3.3)
    * [4.4 消息校验码(MIC)](eBook/4 MAC Message Formats.md#4.4)
	
* [第5章 MAC命令](eBook/5 MAC Commands.md)
    * [5.1 Link Check 命令 (LinkCheckReq, LinkCheckAns)](eBook/5 MAC Commands.md#5.1)
    * [5.2 Link ADR 命令(LinkADRReq, LinkADRAns)](eBook/5 MAC Commands.md#5.2)
    * [5.3 终端发射占空比(DutyCycleReq, DutyCycleAns)](eBook/5 MAC Commands.md#5.3)
    * [5.4 接收窗口参数(RXParamSetupReq,RXParamSetupAns)](eBook/5 MAC Commands.md#5.4)
    * [5.5 终端状态(DevStatusReq, DevStatusAns)](eBook/5 MAC Commands.md#5.5)
    * [5.6 信道的创建和修改(NewChannelReq, NewChannelAns, DlChannelReq, DlChannelAns)](eBook/5 MAC Commands.md#5.6)
    * [5.7 TX 和 RX 之间的延时设置(RXTimingSetupReq, RXTimingSetupAns)](eBook/5 MAC Commands.md#5.7)
    * [5.8 终端发送参数(TxParamSetupReq, TxParamSetupAns)](eBook/5 MAC Commands.md#5.8)	
	
* [第6章 终端激活](eBook/6 End-Device Activation.md)
    * [6.1 终端激活后的数据存储](eBook/6 End-Device Activation.md#6.1)
    * [6.2 空中激活 OTAA](eBook/6 End-Device Activation.md#6.2)
        * [6.2.1 终端 ID (DevEUI)](eBook/6 End-Device Activation.md#6.2.1)
        * [6.2.2 应用密钥(AppKey)](eBook/6 End-Device Activation.md#6.2.2)
        * [6.2.3 加网流程](eBook/6 End-Device Activation.md#6.2.3)
        * [6.2.4 Join-request 消息](eBook/6 End-Device Activation.md#6.2.4)
        * [6.2.5 Join-accept 消息](eBook/6 End-Device Activation.md#6.2.5)
    * [6.3 独立激活 ABP](eBook/6 End-Device Activation.md#6.3)
	

	
	
	
	