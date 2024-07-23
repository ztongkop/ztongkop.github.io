---
title: 三自由度圆柱坐标机器人设计
summary: 该机器人的末端执行器有夹爪，洗盘，可以完成多种小零件的夹取和装配。
tags:
  - Control System
date: '2023-11-10T00:00:00Z'

# Optional external URL for project (replaces project detail page).
external_link: ''

image:
  caption: Photo by Zhang Tong on Unsplash
  focal_point: Smart

links:
  - icon: envelope
    icon_pack: fas
    name: Contact
    url: mailto:ztongkop@foxmail.com
url_code: ''
url_pdf: ''
url_slides: ''
url_video: ''

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: 
---

总体设计方案
=
圆柱坐标机器人使用西门子的伺服电机S-1FL6，额度功率0.4kW。Z轴采用了同步带传动，其型号为YTB6，行程为600mm；X轴采用同步带传动，其型号为YTB6，其行程600mm。选择这样的标准驱动器大大的减少了非标件的设计，节省了成本。

![Snipaste_2024-07-22_20-54-49.jpg](https://s2.loli.net/2024/07/22/b3CMXyadcshDUlr.jpg)

采用西门子的PLC进行控制，每个自由度上面都有三个接近传感器，其中两个限位开关一个原点位置开关，每次启动前先进行回原点操作从而找到其定位原点。其六个伺服驱动器通过总线与PLC进行连接，这样就不需外接高速脉冲输出的扩展模块，同时也节省了PLC的输出端口。其PLC也连接一个触摸屏来实现人机交互。当每次设备运行出现错误时期三色塔灯就会进行声光报警。

非标准件的设计与安装
=
需要先为底部的RV减速器设计连接件。减速器的输出端为直径64mm的内法兰，因此，本连接件的设计主体为与减速器输出端进行配合的外法兰，通过法兰盘中间的短轴与法兰外圈进行定位，通过八个螺钉与减速器进行紧固连接。为不干涉法兰盘上部与z轴的连接，本方案法兰连接螺钉采用沉头布置形式。

![.png](https://s2.loli.net/2024/07/22/8b5NB6xjyP9mgpk.png)

在连接件上部，需要与z轴进行固定连接，考虑到所选z轴结构在底部端面为平面，并无连接所需结构，因此，需设计另与z轴侧面螺纹孔相配合的连接件进行连接。该连接件整体呈L型，底部与上述主体进行螺栓连接，从而将z轴与减速器输出端固联，并起到传递扭矩的作用。

![.png](https://s2.loli.net/2024/07/22/ygC3mdoz1kKva2n.png)

为了更好的保护Z轴机器人，增大它的抗扭能力，在Z轴的机器人侧面加上了两个肋板，这样可以起到支撑和保护的作用。

![.png](https://s2.loli.net/2024/07/22/9otcaHq2kxry7NS.png)

X轴和Z轴机器人以相互垂直的方式连接，为减少连接件的数量并简化连接件的形状，本组采用将两轴的滑块同时固定在同一块连接件上的固定方式，在不影响传动的同时简化了机械结构与末端执行器的运动形式。具体设计形状如下图所示：

![X-Z.png](https://s2.loli.net/2024/07/22/78LOlUCpX4agrNG.png)

为了增加X轴单轴机器人的抗弯能力，特意将与X轴机器人连接的部分加长了一些，与Z轴连接使用的是沉头螺钉，与X轴连接的距离相对较远，不会和Z轴产生干涉，具体装配如图所示：

![1.png](https://s2.loli.net/2024/07/22/KhArfFtTcbJIONq.png)

![2.png](https://s2.loli.net/2024/07/22/7MKJ1YnwmSLlgvQ.png)


非标准件的强度校核
=
其主要受力部件为连接Z轴的侧板及肋板，我们对底部连接部分进行整体的静力学仿真，我们得出结果其最大应力为25MPa，对于侧面连接板及肋板我们选用的材料为钢材，其最大屈服强度为235MPa，在安全系数为5的条件下，其满足使用需求

![.png](https://s2.loli.net/2024/07/22/XWF2fgqCd4aBe7p.png)

完成底座结构设计后，需要对其进行静力学校核。校核方式同上述连接板的校核。根据上述x轴和z轴的受力分析，可得底座对应的受力情况：
配置相应约束与求解条件，进行求解，结果如下图所示：

![.png](https://s2.loli.net/2024/07/22/jQZ9MAXWPfDtiG8.png)


控制系统设计
=
硬件设计
-

1.主电路与电源供给的设计

输入为220V三相交流电，其中接入断路器、保险丝、接触器KM，热继电器FR；其输出端接三相U、V、W，并接入编码器。

![Snipaste_2024-07-22_23-19-20.jpg](https://s2.loli.net/2024/07/22/YoHfwxMytSDR2g5.jpg)

开关电源将220V交流电转换为24V直流电，为PLC与工业触摸屏及三色塔灯进行供电。

![.png](https://s2.loli.net/2024/07/22/Y6jSBzguGvPeNyU.png)

2.PLC接线与通讯设计

完成主电路与开关电源供给图后，是PLC引脚接线与通讯的设计。查阅控制器S7-1200和驱动器MR-JE-10A的产品说明书，使用PROFINET接口连接电脑与HMI触摸屏；MR-JE-10A支持Modbus RTU协议，通过总线使主站PLC与从站驱动器进行通讯。

![PLC.png](https://s2.loli.net/2024/07/22/vUiFSwQNyKRlWMI.png)

PLC的输入接口需要21个，每个自由度上有两个限位开关和一个原点，以及启动、急停、停止按钮；PLC的输出端口，需要配置两个控制两条机械臂气路中电磁阀的动作。此外，还需要配置颜色分别为红、黄、绿的三个指示灯用作启动报警和启动、停止指示功能。

![PLC1.png](https://s2.loli.net/2024/07/22/JM4w5asAxT2PXln.png)

![PLC2.png](https://s2.loli.net/2024/07/22/MG5z9cIubClPRtA.png)

软件设计
-
使用博图V16设计梯形图，具体内容如下：

1.驱动器的使用配置。首先我们需要为6个驱动器封装函数块，以便直接输入参数进行操作。封装的函数块中包含6个程序块，其中5个用于电机控制：POEWER、RESET、HOME、MOVE_ABSOLUTE、MOVE_JOG，1个用于输出轴的当前位置。

2.POWER函数。使能，电机启动，通过组态在触摸屏上按下启动按键可以使能。

3.RESET函数。用于当轴运行到限位或者出现故障后的复位。当故障排除后可使能后清楚警报。

4.HOME函数。用于轴的回原点操作。

5.所有轴的运行使用的函数都是MCMove_Absolute,运行到某个轴的移动时，其Execute由会置位1，检测到上升沿后使能该函数，将运行到指定的位置，即赋给其的POSITION；运行到该位置后函数的输出端Done会置位1，从而触发下一个轴运行的FLAG。该标志将刚才已经置位的Execute复位0，同时将下一个轴已复位的Execute置位1，如此进行下去。

6.MOVE_JOG函数。用于要求的手动操作，点动控制轴的运行，按住保持运行，松开后停止运行。

完整的代码链接：<a href="https://raw.githubusercontent.com/ztongkop/resume/b239aba9bce145d0ca11ecd4117767b2e272afca/PLC_Program.pdf"  download="PLC_Program.pdf">PLC源程序</a>

以下为实验室样机演示图片：

![Snipaste_2024-07-22_23-32-37.jpg](https://s2.loli.net/2024/07/22/nPeubowMLQOWRxN.jpg "夹爪夹取物体完成装配")

![Snipaste_2024-07-22_23-33-12.jpg](https://s2.loli.net/2024/07/22/FWJCYSTAehjBcvb.jpg "HMI界面")
