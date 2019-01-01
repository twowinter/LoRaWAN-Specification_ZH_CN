

## **第17章 持续接收的终端**

具备Class C 能力的终端，通常应用于供电充足的场景，因此不必精简接收时间。

Class C 的终端不能执行 Class B 。

Class C 终端会尽可能地使用 RX2 窗口来监听。按照 Class A 的规定，终端是在 RX1 无数据收发才进行 RX2 接收。为了满足这个规定，终端会在上行发送结束和 RX1 接收窗口开启之间，打开一个短暂的 RX2 窗口，一旦 RX1 接收窗口关闭，终端会立即切换到 RX2 接收状态； RX2 接收窗口会持续打开，除非终端需要发送其他消息。

> 注意：没有规定节点必须要告诉服务端它是 Class C 节点。这完全取决于服务端的应用程序，它们可以在 join 流程通过协议交互来获知是否是 Class C 节点。

### <a name="17.1">17.1 Class C 的第二接收窗口持续时间</a>

Class C 设备执行和 Class A 一样的两个接收窗口，但它们没有关闭 RX2 ，除非他们需要再次发送数据。因此它们几乎可以在任意时间用 RX2 来接收下行消息，包括MAC命令和ACK传输的下行消息。另外在发送结束和 RX1 开启之间还打开了一个短暂的RX2窗口。

![](/img/lorawan_ClassCed_reception_slot_timing.png)

图13.Class C 终端的接收时隙时序图

### <a name="17.2">17.2 Class C 对多播下行的处理</a>

和 Class B 类似，Class C 设备也可以接收多播下行帧。多播地址和相关的 NWKSKEY 及 APPSKEY 都需要从应用层获取。Class C 多播下行帧也有相同的限制：

- 不允许携带MAC命令，既不能放在FOpts域中，也不能放在 port 0 的 payload 中，因为多播下行无法像单播帧那样具备相同的鲁棒性。

- ACK 和 ADRACKReq 位必须要为0。MType 域需要为  Unconfirmed Data Down 类型的数值。

- FPending 位表明有更多的多播数据要发送。考虑到 Classs C 设备在大部分时间处于接收状态，FPending位不触发终端的任何特殊行为。

