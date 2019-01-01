## 第16章 Class B单播/多播下行信道频率

### <a name="16.1">16.1 欧盟 863-870MHz ISM 频段</a>

所有的 Class B 的下行单播和多播都使用由 “**PingSLotChannelReq**” MAC 命令所定义的单频信道。默认的频率是 869.525MHz。

### <a name="16.2">16.2 美国 902-928MHz ISM 频段</a>

默认的，Class B的下行使用最后一个信标(鉴信标帧格式内容)的 **Time** 字段的信道函数和 **DevAddr**。

                 Class B downlink channel = [DevAddr + floor(Beacon_Time/Beacon_period)] modulo 8

- 其中 Beacon_Time 是当前信标周期的 32 位 **Time** 字段。

- Beacon_period 是信标周期的长度(协议中定义的是128s)

- Floor 指的是四舍五入到临近的较低整数值。

- DevAddr 是终端的32位网络地址。

因此 Class B 的下行在 ISM 频段的 8 个信道进行跳跃并且所有的 Class B 终端平等地使用 8 个下行信道进行传输。

如果带有一个有效的非零参数的 “**PingSlotChannelReq**” 命令被用于设置 Class B 下行频率，则随后所有的 ping 时隙都应该只使用这个频率而不是上一个信标频率。

如果发送参数为零的 “**PingSlotChannelReq**” 命令，则终端应该恢复成默认的频率计划，同上所述，Class B ping 时隙在8个信道之间进行跳跃。

其基本思想是允许网络运营商在可行的情况之下配置终端使用一个专用的频段用于 Class B 下行，并且当 ISM 频段可用时尽可能地保持频率多样性。
