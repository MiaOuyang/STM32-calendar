# 1 时钟RTC简介
**RTC (Real Time Clock)**：实时时钟

**RTC是个独立的定时器。**RTC模块拥有一个连续计数的计数器，在相应的软件配置下，可以提供时钟日历的功能。修改计数器的值可以重新设置当前时间和日期 RTC还包含用于管理低功耗模式的自动唤醒单元。

![在这里插入图片描述](https://img-blog.csdnimg.cn/d0356ffb2c2445d4a10b534be6f2fde5.png)

**在断电情况下 RTC仍可以独立运行 只要芯片的备用电源一直供电,RTC上的时间会一直走。**

RTC实质是一个掉电后还继续运行的定时器,从定时器的角度来看,相对于通用定时器TIM外设,它的功能十分简单,只有计时功能(也可以触发中断)。但其高级指出也就在于掉电之后还可以正常运行。

两个 32 位寄存器包含二进码十进数格式 (BCD) 的秒、分钟、小时（ 12 或 24 小时制）、星期几、日期、月份和年份。此外，还可提供二进制格式的亚秒值。系统可以自动将月份的天数补偿为 28、29（闰年）、30 和 31 天。

上电复位后，所有RTC寄存器都会受到保护，以防止可能的非正常写访问。

无论器件状态如何（运行模式、低功耗模式或处于复位状态），只要电源电压保持在工作范围内，RTC使不会停止工作。


## 1.1 RTC特征
**可编程的预分频系数：分频系数高为220**。
● 32位的可编程计数器，可用于较长时间段的测量。
● 2个分离的时钟：用于APB1接口的PCLK1和RTC时钟(RTC时钟的频率必须小于PCLK1时钟 频率的四分之一以上)。
● 可以选择以下三种RTC的时钟源：
● HSE时钟除以128；
● LSE振荡器时钟；
● LSI振荡器时钟

 **2个独立的复位类型：**
● APB1接口由系统复位；
● RTC核心(预分频器、闹钟、计数器和分频器)只能由后备域复位

**3个专门的可屏蔽中断：**
● 1.闹钟中断，用来产生一个软件可编程的闹钟中断。

● 2.秒中断，用来产生一个可编程的周期性中断信号(长可达1秒)。

● 3.溢出中断，指示内部可编程计数器溢出并回转为0的状态。

**RTC时钟源：**
三种不同的时钟源可被用来驱动系统时钟(SYSCLK)：

● HSI振荡器时钟
● HSE振荡器时钟
● PLL时钟

这些设备有以下2种二级时钟源：

● 40kHz低速内部RC，可以用于驱动独立看门狗和通过程序选择驱动RTC。 RTC用于从停机/待机模式下自动唤醒系统。
● 32.768kHz低速外部晶体也可用来通过程序选择驱动RTC(RTCCLK)。

## 1.2 RTC原理框图

![在这里插入图片描述](https://img-blog.csdnimg.cn/0c10fda1ef964b0c86db0517bfd3acb3.png)
RTC时钟的框图还是比较简单的，这里我们把他分成 两个部分:

APB1 接口：用来和 APB1 总线相连。 此单元还包含一组 16 位寄存器，可通过 APB1 总线对其进行读写操作。APB1 接口由 APB1 总 线时钟驱动，用来与 APB1 总线连接。

通过APB1接口可以访问RTC的相关寄存器（预分频值，计数器值，闹钟值）。

RTC 核心接口：由一组可编程计数器组成，分成 两个主要模块 。

**第一个模块**是 RTC 的 预分频模块，它可编程产生 1 秒的 RTC 时间基准 TR_CLK。RTC 的预分频模块包含了一个 20 位的可编程分频器(RTC 预分频器)。如果在 RTC_CR 寄存器中设置了相应的允许位，则在每个 TR_CLK 周期中 RTC 产生一个中断(秒中断)。


**第二个模块**是一个 32 位的可编程计数器 （RTC_CNT），可被初始化为当前的系统时间，一个 32 位的时钟计数器，按秒钟计算，可以记 录 4294967296 秒，约合 136 年左右，作为一般应用，这已经是足够了的。

## 1.3 RTC具体流程
RTCCLK经过RTC_DIV预分频，RTC_PRL设置预分频系数，然后得到TR_CLK时钟信号，我们一般设置其周期为1s，RTC_CNT计数器计数，假如1970设置为时间起点为0s，通过当前时间的秒数计算得到当前的时间。RTC_ALR是设置闹钟时间，RTC_CNT计数到RTC_ALR就会产生计数中断，

RTC_Second为秒中断，用于刷新时间，
RTC_Overflow是溢出中断。
RTC Alarm 控制开关机
