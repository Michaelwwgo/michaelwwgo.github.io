---
layout: post
title: 5G 频谱资源设计
categories: 5G_NR
description: 5G 频谱资源设计
keywords: 5G, 频谱
---

 最近在编写系统仿真平台的过程中，感觉自己对与 5G 通信的基本流程以及一些新的设计理解不是很透彻。因此，决定重新再好好学习一下 5G 标准。本博客也将记录个人的学习历程以及相应问题。

## 5G频带范围（Frequency Ranges，FR）

理论上，5G通信可用的频谱可以是 0~100GHz，但是目前 R17 中定义的 5G NR 的工作范围如下表[^1]所示。


| Frequency range designation | Corresponding frequency range |
| ---- | ---- |
| FR1  | 410 MHz – 7125 MHz |
| FR2  | 24250 MHz – 52600 MHz |

##　信道栅格（Channel Raster）
信道是用来传输信息的，包括控制信令与数据。但是 5G 将频谱资源划分为信道栅格与同步栅格，分别用来传输同步信号（i.e. SSB/PBCH-block）和其他信令（包括除同步信号外的其他信令）与数据信号。具体缘由先按下不表。

### 全局信道栅格（Global Channel Raster）

首先来看 NR 绝对信道编号（NR Absolute Radio Frequency Channel Number，NR-ARFCN）。 NR-ARFCN 是将 0~100GHz 的频谱范围进行了编号，相当于一把全局的标尺，NR 工作频谱范围就可以通过该标尺方便地表示。R17 给出的 NR-ARFCN 的定义如下表[^2]所示。

| Range of frequencies (MHz) | $ΔF_{Global}$ (kHz) | $F_{REF-Offs}$ (MHz) | $N_{REF-Offs}$ | Range of $N_{REF}$ |
| ---- | ---- | ---- | ---- | ---- |
| 0 - 300 | 5 | 0 | 0 | 0 - 599999 |
| 3000 - 24250 | 15 | 3000 | 600000 | 600000 - 2016666 |
| 24250 - 100000 | 60 | 24250.08 | 201667 | 2016667 – 3279165 |
信道栅格定义为可以部署小区（至少理论上）的一系列特定频点。在不同的频域，依据不同的粒度，如上图所示，全局信道栅格与绝对频率编号相对应。
有了上述表格定义，对于给定频率编号 $N_{REF}$，绝对频率值（MHz）$F_{REF}$ 可以通过由下式计算得出：
$$F_{REF} = F_{REF-Offs} + ΔF_{Global} (N_{REF} – N_{REF-Offs}).$$

### 信道栅格（Channel Raster）
理论上，全局信道栅格都可以用来部署小区。但是实际上小区只能定义在特定频点。NR 信道栅格就是在全局信道栅格的基础上进一步进行定义，主要是选取了特定的频点以及配置了不同的栅格粒度。
NR 信道栅格在 FR1 上的定义如下表[^3]所示。篇幅所致，只截取部分表格。

| NR operating band | $ΔF_{Raster}$(kHz) | Uplink range of $N_{REF}$ (First – \<Step size\> – Last) | Downlink range of $N_{REF}$ (First – \<Step size> – Last) |
| ---- | ---- | ---- | ---- |
| n1 | 100 | 384000 - \<20\> - 396000 | 422000 - \<20\> - 434000 |
| n2 | 100 | 370000 - \<20\> - 382000 | 386000 - \<20\> - 398000 |
| n3 | 100 | 342000 - \<20\> - 357000 | 361000 - \<20\> - 376000 |
| ... | ... | ...... | ...... |
| n41 | 15 | 499200 - \<3\> - 537999 | 499200 - \<3\> - 537999 |
| | 30 | 499200 - \<6\> - 537999 | 499200 - \<6\> - 537999 |
| ... | ... | ...... | ...... |
| n98 | 100 | 376000 - \<20\> - 384000 | N/A |

NR 信道栅格在 FR2 上的定义如下表[^4]所示。

| NR operating band | $ΔF_{Raster}$(kHz) | Uplink range of $N_{REF}$ (First – \<Step size\> – Last) |
| ---- | ---- | ---- |
| n257 | 60 | 2054166 - \<1\> - 2104165 |
|  | 120 | 2054166 - \<2\> - 2104165 |
| n258 | 60 | 2016667 - \<1\> - 2070832  |
|  | 120 | 2016667 - \<2\> - 2070831 |
| n259 | 60 | 2270832 - \<1\> - 2337499  |
|  | 120 | 2270832 - \<2\> - 2337499 |
| n260 | 60 | 2229166 - \<1\> - 2279165  |
|  | 120 | 2229167 - \<2\> - 2279165 |
| n261 | 60 | 2070833 - \<1\> - 2084999  |
|  | 120 | 2070833 - \<2\> - 2084999 |

 

以 band n41 为例，信道栅格粒度取 15kHz，由 $N_{REF}$的范围可知，其起始频率：

$$F_{REF} = F_{REF-Offs} + ΔF_{Global} (N_{REF} – N_{REF-Offs})=0+5*（499200-0）=2496MHz.$$

终止频率为：

$$F_{REF} = F_{REF-Offs} + ΔF_{Global} (N_{REF} – N_{REF-Offs})=0+5*（537999-0）=2690MHz.$$

在 2496 - 2690MHz 之间，每隔 15kHz，也就是 3 个全局信道栅格粒度（5kHz），设置为一个信道栅格频点，可以用于部署小区。

信道栅格与全局信道栅格的关系可以通过下图表示。

![GlobalRasterChannelRaster](/images/5G_NR/GlobalRasterChannelRaster.png)


[^1]:[38.101-1  Table 5.1-1: Definition of frequency ranges].
[^2]:[38.104 Table 5.4.2.1-1: NR-ARFCN parameters for the global frequency raster].
[^3]:[38.104 Table 5.4.2.3-1: Applicable NR-ARFCN per operating band in FR1].
[^4]:[38.104 Table 5.4.2.3-2: Applicable NR-ARFCN per operating band in FR2].



