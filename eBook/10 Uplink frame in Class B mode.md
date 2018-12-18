

## **第10章 Class B 模式的上行帧**

Class B 模式的上行帧和 Class A 的基本一样，除了帧头Fctrl字段的RFU位域有所不同。在 Class A 上行帧中这个位没有使用(RFU)，而在 Class B 中有使用。

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
      <td>Class B</td>
      <td>FOptsLen</td>
   </tr>
</table>

上行帧中的 Class B 位域置为1，用于通知network server设备已切换到 Class B 模式，准备好接收下行ping包。

下行帧的FPending位域的定义是不变的，仍然和Class A的定义一样，表示server有多个下行帧要下发，设备应当继续接收。

