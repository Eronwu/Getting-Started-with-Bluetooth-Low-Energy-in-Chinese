# 第二章节，基本协议

虽然用户总是仅仅直接与BLE协议栈的上层接口进行交互，但最好是对整个栈开始有一个基本的概观，了解这个栈提供了一个可靠的基础来更好理解这些东西是如何操作以及原因。

正如图2-1展示，一个完整的单模BLE设备被分为三块：控制器（controller）、主机（host）和应用（application）。

*应用*

​		就像其他类型的系统一样，应用是在最高层，负责了逻辑、用户接口，以及所有与应用实现的实际用例相关的数据处理。一个应用的结构设计高度依赖于每一个特殊的实现。

*主机*

​		包含了以下层：

				- 通用访问配置文件 Generic Access Profile（GAP）
				- 通用属性配置文件 Generic Attribute Profile（GATT）
				- 逻辑链路控制和适配协议 Logical Link Control and Adaption Protocol （L2CAP）
				- 属性协议 Attribute Protocol（ATT）
				- 安全管理 Security Manager（SM）
				- 主机控制器接口 Host Controller Interface（HCI），主机端

*控制器*

​		包含了以下层：

		- 主机控制器接口 Host Controller Interface（HCI），控制器端
		- 链路层 Link Layer（LL）
		- 物理层 Physical Layer（PHY）

在这一章，各部分的顺序将会从下（天线 antenna）到上（用户接口 user interface）介绍不同的部分的方式，来组成一个BLE设备。

![figure2-1](./pic/figure2-1.png)

*图2-1. BLE协议栈*

## 物理层

物理层（PHY）是实际包含了模拟通信电路、调制解调和转化为电信号的能力。

