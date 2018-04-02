---
layout: post
title: nRF24L01 数据手册
date: 2018-4-2
categories: [blog, Datasheet]
description: 单片 2.4G 无线射频收发芯片 nRF24L01 数据手册。
keywords: GFSK, nRF24L01, wireless
---
 
> nRF24L01 是由 NORDIC 生产的工作在 2.4GHz ~ 2.5GHz 的 ISM 频段的单片无线收发器芯片。无线收发器包括：频率发生器、增强型「[SchockBurst](http://www.elecbench.com/%e5%af%b9shockburst%e4%b8%8eenhance-shockburst%e7%9a%84%e7%90%86%e8%a7%a3/)」模式控制器、功率放大器、晶体振荡器、调制器和解调器。  
输出功率频道选择和协议的设置可以通过 SPI 接口进行设置。几乎可以连接到各种单片机芯片，并完成无线数据传送工作。


**目录**

* TOC
{:toc}  

## 1. 介绍

### 1.1 结构框图

图 1 结构框图  
![][P1]

### 1.2 引脚及功能

图 2 引脚排列（顶视），4x4封装  
![][P2]

引脚功能

| 引脚 | 名称   | 引脚功能 | 描述                               |
| ---- | ------ | -------- | ---------------------------------- |
| 1    | CE     | 数字输入 | RX 或 TX 模式选择                  |
| 2    | CSN    | 数字输入 | SPI 片选信号                       |
| 3    | SCK    | 数字输入 | SPI 时钟                           |
| 4    | MOSI   | 数字输入 | 从 SPI 数据输入脚                  |
| 5    | MISO   | 数字输出 | 从 SPI 数据输出脚，三态选择        |
| 6    | IRQ    | 数字输出 | 可屏蔽中断脚，低有效               |
| 7    | VDD    | 电源     | 电源 +3V（+1.9 ~ +3.6V DC）        |
| 8    | VSS    | 电源     | 接地 0V                            |
| 9    | XC2    | 模拟输出 | 晶振 2 脚                          |
| 10   | XC1    | 模拟输入 | 晶振 1 脚／外部时钟输入             |
| 11   | VDD_PA | 电源输出 | 给 RF 的功率放大器提供的+1.8V 电源 |
| 12   | ANT1   | 天线     | 天线接口 1                         |
| 13   | ANT2   | 天线     | 天线接口 2                         |
| 14   | VSS    | 电源     | 接地 0V                            |
| 15   | VDD    | 电源     | 电源 +3V（+1.9 ~ +3.6V DC）        |
| 16   | IREF   | 模拟输入 | 参考电流                           |
| 17   | VSS    | 电源     | 接地 0V                            |
| 18   | VDD    | 电源     | 电源 +3V（+1.9 ~ +3.6V DC）        |
| 19   | DVDD   | 电源输出 | 去耦电路电源正极端                 |
| 20   | VSS    | 电源     | 接地 0V                            |

### 1.3 缩略词

| 缩略词  | 描述                 |
| ------- | -------------------- |
| ACK     | 确认信号 应答信号    |
| ART     | 自动重发             |
| CE      | 芯片使能             |
| CLK     | 时钟信号             |
| CRC     | 循环冗余校验         |
| CSN     | 片选非               |
| ESB     | 增强型 ShockBrust TM |
| GFSK    | 高斯键控频移         |
| IRQ     | 中断请求             |
| ISM     | 工业 科学 医学       |
| LNA     | 低噪声放大           |
| LSB     | 最低有效位           |
| LSByte  | 最低有效字节         |
| Mbps    | 兆位/秒              |
| MCU     | 微控制器             |
| MISO    | 主机输入从机输出     |
| MOSI    | 主机输出从机输入     |
| MSB     | 最高有效位           |
| MSByte  | 最高有效字节         |
| PCB     | 印刷电路板           |
| PER     | 数据包误码率         |
| PID     | 数据包识别位         |
| PLD     | 载波                 |
| PRX     | 接收源               |
| PTX     | 发射源               |
| PWR_DWN | 掉电                 |
| PWR_UP  | 上电                 |
| RX      | 接收                 |
| RX_DR   | 接收数据准备就绪     |
| SPI     | 串行可编程接口       |
| TX      | 发送                 |
| TX_DS   | 已发送数据           |

## 2. 功能描述

### 2.1 工作模式

nRF24L01 可以设置为以下几种主要的模式
| 模式        | PWR_UP | PRIM_RX | CE  | FIFO 寄存器状态               |
| ----------- | ------ | ------- | --- | ----------------------------- |
| 接收模式    | 1      | 1       | 1   | -                             |
| 发送模式    | 1      | 0       | 1   | 数据在 TX FIFO 寄存器中       |
| 发送模式    | 1      | 0       | 1→0 | 停留在发送模式 直至数据发送完 |
| 待机模式 II | 1      | 0       | 1   | TX FIFO 为空                  |
| 待机模式 I  | 1      | -       | 0   | 无数据传输                    |
| 掉电模式    | 0      | -       | -   | -                             |

nRF24L01 在不同模式下的引脚功能
| 引脚名称 | 方向     | 发送模式                | 接收模式 | 待机模式 | 掉电模式 |
| -------- | -------- | ----------------------- | -------- | -------- | -------- |
| CE       | 输入     | 高电平>10us             | 高电平   | 低电平   | -        |
| CSN      | 输入     | SPI 片选使能 低电平使能 |
| SCK      | 输入     | SPI 时钟                |
| MOSI     | 输入     | SPI 串行输入            |
| MISO     | 三态输出 | SPI 串行输出            |
| IRQ      | 输出     | 中断 低电平使能         |


**待机模式**  

待机模式 I 在保证快速启动的同时减少系统平均电流消耗。在待机模式 I 下，晶振正常工作。在待机模式 II 下部分时钟缓冲器处在工作模式。当发送端 TX FIFO 寄存器为空并且 CE 为高电平时进入待机模式 II。在待机模式期间，寄存器配置字内容保持不变。

**掉电模式**

在掉电模式下，nRF24L01 各功能关闭，保持电流消耗最小。进入该模式后，nRF24L01 停止工作，但寄存器内容保持不变。启动时间见工作时序。掉电模式由寄存器中 PWR_UP 位控制。

**数据包处理方式**

有如下几种数据包处理方式：
- ShockBurst (TM) （与 nRF2401，nRF24E1，nRF2402，nRF24E2 数据传输率为 1 Mbps 时相同）
- 增强型 ShockBurst (TM) 模式

<u>ShockBust (TM) 模式</u>

ShockBust 模式下 nRF24L01 可与成本较低的 MCU 相连。高速信号处理是由芯片内部的射频协议处理的，nRF24L01 提供 SPI 接口，数据率取决于 MCU 本身接口速度。ShockBust 模式通过允许与 MCU 低速通信而无线部分高速通信，减小了通信的平均电流消耗。

在 *ShockBust 接收模式*下，当接收到有效的地址和数据时 IRQ 通知 MCU，随后 MCU 可将接收到的数据从 RX FIFO 寄存器中读出。

在 *ShockBust 发送模式*下，nRF24L01 自动生成前导码及 CRC 校验。数据发送完毕后 IRQ 通知 MCU。减少了 MCU 的查询时间，也就意味着减少了 MCU 的工作量，同时减少了软件的开发时间。nRF24L01 内部有三个不同的 RX FIFO 寄存器（6 个通道共享此寄存器）和三个不同的 TX FIFO 寄存器。在掉电模式下、待机模式下和数据传输的过程中 MCU 可以随时访问 FIFO 寄存器。这就允许 SPI 接口可以以低速进行数据传送，并且可以应用于 MCU 硬件上没有 SPI 接口的情况下。

<u>增强型的 ShockBust 模式</u>

该模式可使得双向链接协议执行起来更为容易、有效。典型的双向链接为：发送方要求终端设备在接收到数据后有应答信号，以便于发送方检测有无数据丢失。一旦数据丢失，则通过重新发送功能将丢失的数据恢复。该模式可以同时控制应答及重发功能而无需增加 MCU 工作量。

nRF24L01 在接收模式下可接收 6 路不同通道的数据，每一个数据通道使用不同的地址，但是共用相同的频道。也就是说 6 个不同的 nRF24L01 设置为发送模式后可以与同一个设置为接收模式的 nRF24L01 进行通讯，而设置为接收模式的 nRF24L01 可以对这 6 个发射端进行识别。数据通道 0 是唯一的一个可以配置为 40 位自身地址的数据通道。1 ~ 5 数据通道都为 8 位自身地址和 32 位共用地址。所有的数据通道都可以设置为增强型 ShockBust 模式。

nRF24L01 在确认收到数据后记录地址，并以此地址为目标地址发送应答信号。在发送端，数据通道 0 被用作接收应答信号，因此，数据通道 0 的接收地址要与发送端地址相等以确保接收到正确的应答信号。

    在发射器中，通道 0 要接收应答回来的信号，所以应该与发送通道地址相同。

nRF24L01 配置为增强型的 ShockBust 发送模式下时，只要 MCU 有数据要发送，nRF24L01 就会启动 ShockBust 模式来发送数据。在发送完数据后 nRF24L01 转到接收模式并等待终端的应答信号。如果没有收到应答，nRF24L01 将重发相同的数据包，直到收到应答信号或重发次数超过 `SETUP_RETR_ARC` 寄存器中设置的值为止，如果重发次数超过设定值，则产生 `MAX_RT` 中断。

只要收到确认信号，nRF24L01 就认为最后一包数据已经发送成功（接收方已经收到数据），把 TX_FIFO 中的数据清除掉并产生 `TX_DS` 中断（IRQ 引脚置高）。

在增强型 ShockBust 模式下，nRF24L01 有如下特征：
- 当工作在应答模式时，快速的空中传输及启动时间，极大地降低了电流消耗
- 低成本。集成了所有高速链路层操作，比如：重发丢失数据包和产生应答信号。无需单片机硬件上一定有 SPI 口与其相连。SPI 接口可以利用单片机通用 I/O 口进行模拟。
- 由于空中传输时间很短，极大的降低了无线传输中的碰撞现象。
- 由于链路层完全集成在芯片上，非常便于软硬件的开发。

*增强型 ShockBust 发送模式：*

1. 配置寄存器位 `PRIM_RX` 为低
2. 当 MCU 有数据要发送时，接收节点地址（`TX_ADDR`）和有效数据（`TX_PLD`）通过 SPI 接口写入 nRF24L01。发送数据的长度以字节计数从 MCU 写入 TX FIFO。当 CSN 引脚为低时数据被不断地写入。发送端发送完数据后，将通道 0 设置为接收模式来接收应答信号，其接收地址（`RX_ADDR_P0`）与~~接收端~~发送端地址（`TX_ADDR`）相同。
3. 设置 CE 为高，启动发射。CE 高电平持续时间最小为 10 μs。
4. nRF24L01 ShockBust 模式：
    - 无线系统上电
    - 启动内部 16 MHz 时钟
    - 无线发送数据打包（见数据包描述）
    - 高速发送数据（由 MCU 设定为 1 Mbps 或 2 Mbps）
5. 如果启动了自动应答模式（自动重发计数器不等于 0，ENAA_P0 = 1），无线芯片立即进入接收模式。如果在有效应答时间范围内收到应答信号，则认为数据成功发送到了接收端，此时状态寄存器的 `TX_DS` 位置高并把数据从 TX FIFO 中清除掉。如果在设定时间范围内没有接收到应答，则重新发送数据。如果自动重发计数器（`ARC_CNT`）溢出（超过了编程设定的值），则状态寄存器的 `MAX_RT` 位置高。不清楚 TX FIFO 中的数据。当 `MAX_TR` 或 `TX_DS` 为高电平时 IRQ 引脚产生中断。IRQ 中断通过写状态寄存器来复位。如果重发次数在达到设定的最大重发次数时还没有收到应答信号的话，在 `MAX_RX` 中断清除之前不会重发数据包。数据包丢失计数器（`PLOS_CNT`）在每次产生 `MAX_RT` 中断后加一。也就是说：重发计数器 `ARC_CNT` 计算重发数据包次数，`PLOS_CNT` 计算在达到最大允许重发次数时仍没有发送成功的数据包个数。
6. 如果 CE 拉低，则系统进入待机模式 I。如果不设置 CE 为低，则系统会发送 TX FIFO 寄存器中下一包数据。如果 TX FIFO 寄存器为空并且 CE 为高则系统进入待机模式 II。
7. 如果系统在待机模式 II，当 CE 拉低后系统立即进入待机模式 I。

*增强型 ShockBust 接收模式：*

1. ShockBust 接收模式是通过设置寄存器中 `PRIM_RX` 位为高来选择的。准备接收数据的通道必须被使能（`EN_RXADDR` 寄存器），所有工作在增强型 ShockBust 模式下的数据通道的自动应答功能是由（`EN_AA` 寄存器）来使能的，有效数据宽度是由 `RX_PW_Px` 寄存器来设置的。
2. 接收模式由设置 CE 为高来启动。
3. 130 μs 后 nRF24L01 开始检测空中信息。
4. 接收到有效的数据包后（地址匹配、CRC 检验正确），数据存储在 `RX_FIFO` 中，同时 `RX_DR` 位置高，并产生中断。状态寄存器中 `RX_P_NO` 位显示数据是由哪个通道接收到的。
5. 如果使能自动确认信号，则发送确认信号。
6. MCU 设置 CE 引脚为低，进入待机模式 I（低功耗模式）。
7. MCU 将数据以合适的速率通过 SPI 口将数据读出。
8. 芯片准备好进入发送模式、接收模式或掉电模式。

**两种数据双方向的通讯方式**

如果想要数据在双方向上通讯，`PRIM_RX` 寄存器必须紧随芯片工作模式的变化而变化。处理器必须保持 PTX 和 PRX 端的同步性。在 RX_FIFO 和 TX_FIFO 寄存器中可能同时存有数据。

**自动应答（RX）**

自动应答功能减少了外部 MCU 的工作量，并且在鼠标／键盘等应用中也可以不要求硬件一定有 SPI 接口，因此降低成本减少电流消耗。自动应答功能可以通过 SPI 口对不同的数据通道分别进行配置。

在自动应答模式使能的情况下，收到有效的数据包后，系统将进入发送模并发送确认信号。发送完确认信号后，系统进入正常工作模式（工作模式由 PRIM_RX 位和 CE 引脚决定）。

**自动重发功能（ART）（TX）：**

自动重发功能是针对自动应答系统的发送方。SETUP_PETR 寄存器设置：启动重发数据的时间长度。在每次发送结束后系统都会进入接收模式并在设定的时间范围内等待应答信号。接收到应答信号后，系统转入正常发送模式。如果 TX FIFO 中没有待发送的数据且 CE 脚电平为低，则系统将进入待机模式 I。如果没有收到确认信号，则系统返回到发送模式并重发数据直到收到确认信号或重发次数超过设定值（达到最大的发送次数）。有新的数据发送或 PRIM_TX 寄存器配置改变时丢包计数器复位。

**数据包识别和 CRC 校验应用于增强型 ShockBust 模式下：**

每一包数据都包括两位的 PID（数据包识别）来识别接收的数据是新数据包还是重发的数据包。PID 识别可以防止接收端同一数据包多次送入 MCU。在发送方每从 MCU 取得一包新数据后 PID 值加一。PID 和 CRC 校验应用在接收方识别接收的数据是重发的数据包还是新数据包。如果在链接中有一些数据丢失了，则 PID 值与上一包数据的 PID 值相同。如果一包数据拥有与上一包数据相同的 PID 值，nRF24L01 将对两包数据的 CRC 值进行比较。如果 CRC 值也是相同的话就认为后面一包是前一包的重发数据包而被舍弃。


【18/04/02 U】











<!-- 缩略词 -->
*[CAN]: Controller Area Network
