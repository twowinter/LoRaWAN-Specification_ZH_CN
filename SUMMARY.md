# Summary

* [前言](README.md)

* [第1章 介绍](chapter/1 Introduction.md)
    * [1.1 LoRaWAN Classes](chapter/1 Introduction.md#1.1)
    * [1.2 文档约定](chapter/1 Introduction.md#1.2)

* [第2章 LoRaWAN Classes 类型介绍](chapter/2 Introduction on LoRaWAN options.md)
    * [2.1 LoRaWAN Classes](chapter/2 Introduction on LoRaWAN options.md#2.1)
    * [2.2 文档范围](chapter/2 Introduction on LoRaWAN options.md#2.2)

* [CLASS A - ALL END-DEVICE 所有终端](chapter/CLASS A - ALL END-DEVICE.md)

* [第3章 PHY 帧格式](chapter/3 Physical Message Formats.md)
    * [3.1 上行消息](chapter/3 Physical Message Formats.md#3.1)
    * [3.2 下行消息](chapter/3 Physical Message Formats.md#3.2)
    * [3.3 接收窗口](chapter/3 Physical Message Formats.md#3.3)
        * [3.3.1 第一接收窗口的信道，数据速率和启动](chapter/3 Physical Message Formats.md#3.3.1)
        * [3.3.2 第二接收窗口的信道，数据速率和启动](chapter/3 Physical Message Formats.md#3.3.2)
        * [3.3.3 接收窗口的持续时间](chapter/3 Physical Message Formats.md#3.3.3)
        * [3.3.4 接收方在接收窗口期间的处理](chapter/3 Physical Message Formats.md#3.3.4)
        * [3.3.5 网络发送消息给终端](chapter/3 Physical Message Formats.md#3.3.5)
        * [3.3.6 接收窗口的重要事项](chapter/3 Physical Message Formats.md#3.3.6)

* [第4章 MAC帧格式](chapter/4 MAC Message Formats.md)
    * [4.1 MAC层](chapter/4 MAC Message Formats.md#4.1)
    * [4.2 MAC头(MHDR字段)](chapter/4 MAC Message Formats.md#4.2)
        * [4.2.1 第一接收窗口的信道，数据速率和启动](chapter/4 MAC Message Formats.md#4.2.1)
        * [4.2.2 数据消息的主版本(Major位字段)](chapter/4 MAC Message Formats.md#4.2.2)
    * [4.3 MAC载荷(MACPayload)](chapter/4 MAC Message Formats.md#4.3)
        * [4.3.1 帧头(FHDR)](chapter/4 MAC Message Formats.md#4.3.1)
        * [4.3.2 端口字段(FPort)](chapter/4 MAC Message Formats.md#4.3.2)
        * [4.3.3 MAC帧载荷加密(FRMPayload)](chapter/4 MAC Message Formats.md#4.3.3)
    * [4.4 消息校验码(MIC)](chapter/4 MAC Message Formats.md#4.4)

* [第5章 MAC命令](chapter/5 MAC Commands.md)
    * [5.1 Link Check 命令 (LinkCheckReq, LinkCheckAns)](chapter/5 MAC Commands.md#5.1)
    * [5.2 Link ADR 命令(LinkADRReq, LinkADRAns)](chapter/5 MAC Commands.md#5.2)
    * [5.3 终端发射占空比(DutyCycleReq, DutyCycleAns)](chapter/5 MAC Commands.md#5.3)
    * [5.4 接收窗口参数(RXParamSetupReq,RXParamSetupAns)](chapter/5 MAC Commands.md#5.4)
    * [5.5 终端状态(DevStatusReq, DevStatusAns)](chapter/5 MAC Commands.md#5.5)
    * [5.6 信道的创建和修改(NewChannelReq, NewChannelAns, DlChannelReq, DlChannelAns)](chapter/5 MAC Commands.md#5.6)
    * [5.7 TX 和 RX 之间的延时设置(RXTimingSetupReq, RXTimingSetupAns)](chapter/5 MAC Commands.md#5.7)
    * [5.8 终端发送参数(TxParamSetupReq, TxParamSetupAns)](chapter/5 MAC Commands.md#5.8)

* [第6章 终端激活](chapter/6 End-Device Activation.md)
    * [6.1 终端激活后的数据存储](chapter/6 End-Device Activation.md#6.1)
    * [6.2 空中激活 OTAA](chapter/6 End-Device Activation.md#6.2)
        * [6.2.1 终端 ID (DevEUI)](chapter/6 End-Device Activation.md#6.2.1)
        * [6.2.2 应用密钥(AppKey)](chapter/6 End-Device Activation.md#6.2.2)
        * [6.2.3 加网流程](chapter/6 End-Device Activation.md#6.2.3)
        * [6.2.4 Join-request 消息](chapter/6 End-Device Activation.md#6.2.4)
        * [6.2.5 Join-accept 消息](chapter/6 End-Device Activation.md#6.2.5)
    * [6.3 独立激活 ABP](chapter/6 End-Device Activation.md#6.3)

* [第7章 重传退避](chapter/7 Retransmissions back-off Activation.md)

* [CLASS B – BEACON 信标](chapter/CLASS B - BEACON.md)

* [第8章 Class B 介绍](chapter/8 Introduction to Class B.md)

* [第9章 下行同步网络的原理](chapter/9 Principle of synchronous network initiated downlink.md)

* [第10章 Class B 模式的上行帧](chapter/10 Uplink frame in Class B mode.md)

* [第11章 Class B 模式的下行帧(Class B选项)](chapter/11 Downlink Ping frame format.md)

* [第12章 信标的获得和追踪](chapter/12 Beacon acquisition and tracking.md)

* [第13章 Class B下行时隙时序](chapter/13 Class B Downlink slot timing.md)
    * [13.1 定义](chapter/13 Class B Downlink slot timing.md#13.1)
    * [13.2 时隙随机化](chapter/13 Class B Downlink slot timing.md#13.2)

* [第14章 Class B MAC命令](chapter/14 Class B MAC commands.md)
    * [14.1 PingSlotInfoReq MAC命令](chapter/14 Class B MAC commands.md#14.1)
    * [14.2 BeaconFreReq MAC命令](chapter/14 Class B MAC commands.md#14.2)
    * [14.3 PingSlotChannelReq MAC命令](chapter/14 Class B MAC commands.md#14.3)
    * [14.4 BeaconTimingReq MAC命令](chapter/14 Class B MAC commands.md#14.4)
    * [14.5 BeaconTimingAns MAC命令](chapter/14 Class B MAC commands.md#14.5)

* [第15章 信标(Class B选项)](chapter/15 Beaconing[Class B option].md)
    * [15.1 信标物理层](chapter/15 Beaconing[Class B option].md#15.1)
    * [15.2 信标物理帧格式](chapter/15 Beaconing[Class B option].md#15.2)
    * [15.3 信标 GwSpecific 域格式](chapter/15 Beaconing[Class B option].md#15.3)
    * [15.4 信标准确的时隙](chapter/15 Beaconing[Class B option].md#15.4)
    * [15.5 网络下行链路路由更新要求](chapter/15 Beaconing[Class B option].md#15.5)

* [第16章 Class B单播/多播下行信道频率](chapter/16 Class B unicast & multicast downlink channel frequencies.md)
    * [16.1 欧盟 863-870MHz ISM 频段](chapter/16 Class B unicast & multicast downlink channel frequencies.md#16.1)
    * [16.2 美国 902-928MHz ISM 频段](chapter/16 Class B unicast & multicast downlink channel frequencies.md#16.2)

* [CLASS C - CONTINUOUSLY LISTENING 持续接收](chapter/CLASS C – CONTINUOUSLY LISTENING.md)

* [第17章 持续接收的终端](chapter/17 Class C Continuously listening end-device.md)
    * [17.1 Class C 的第二接收窗口持续时间](chapter/17 Class C Continuously listening end-device.md#17.1)
    * [17.2 Class C 对多播下行的处理](chapter/17 Class C Continuously listening end-device.md#17.2)

