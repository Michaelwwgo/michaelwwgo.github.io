---
layout: post
title: 5G 频谱资源设计
categories: 5G_NR
description: 最近在编写系统仿真平台的过程中，感觉自己对与 5G 通信的基本流程以及一些新的设计理解不是很透彻。因此，决定重新再好好学习一下 5G 标准。本博客也将记录个人的学习历程以及相应问题。
keywords: 5G, 频谱
---

## 5G频带范围（Frequency Ranges，FR）

理论上，5G通信可用的频谱可以是 0~100GHz，但是目前 R17 中定义的 5G NR 的工作范围如下表[^1]所示。


| Frequency range designation | Corresponding frequency range |
| ---- | ---- |
| FR1  | 410 MHz – 7125 MHz |
| FR2  | 24250 MHz – 52600 MHz |

## 信道栅格（Channel Raster）
信道栅格定义为可以部署小区（至少理论上）的一系列特定频点。5G 将频谱资源划分为信道栅格与同步栅格，分别用来传输同步信号（i.e. SSB/PBCH-block）和其他信令（包括除同步信号外的其他信令）与数据信号。具体缘由先按下不表。

### 全局信道栅格（Global Channel Raster）

首先来看 NR 绝对信道编号（NR Absolute Radio Frequency Channel Number，NR-ARFCN）。 NR-ARFCN 是将 0~100GHz 的频谱范围进行了编号，相当于一把全局的标尺，NR 工作频谱范围就可以通过该标尺方便地表示。R17 给出的 NR-ARFCN 的定义如下表[^2]所示。

| Range of frequencies | $ΔF_{Global}$ | $F_{REF-Offs}$ | $N_{REF-Offs}$ | Range of $N_{REF}$ |
| ---- | ---- | ---- | ---- | ---- |
| 0 - 300MHz | 5kHz | 0 | 0 | 0 - 599999 |
| 3000 - 24250MHz | 15kHz | 3000 | 600000 | 600000 - 2016666 |
| 24250 - 100000MHz | 60kHz | 24250.08 | 201667 | 2016667 – 3279165 |

在不同的频域，依据不同的粒度，如上表所示，全局信道栅格与绝对频率编号相对应。
有了上述表格定义，对于给定频率编号 $N_{REF}$，绝对频率值（MHz）$F_{REF}$ 可以通过由下式计算得出：
$$F_{REF} = F_{REF-Offs} + ΔF_{Global} (N_{REF} – N_{REF-Offs}).$$

### NR 信道栅格（Channel Raster）
理论上，全局信道栅格都可以用来部署小区。但是实际上小区只能定义在特定频点。NR 信道栅格就是在全局信道栅格的基础上进一步进行定义，主要是选取了特定的频点以及配置了不同的栅格粒度。
NR 信道栅格在 FR1 上的定义如下表[^3]所示。篇幅所致，只截取部分表格。

| NR operating band | $ΔF_{Raster}$ | Uplink range of $N_{REF}$ (First – \<Step size\> – Last) | Downlink range of $N_{REF}$ (First – \<Step size> – Last) |
| ---- | ---- | ---- | ---- |
| n1 | 100kHz | 384000 - \<20\> - 396000 | 422000 - \<20\> - 434000 |
| n2 | 100kHz | 370000 - \<20\> - 382000 | 386000 - \<20\> - 398000 |
| n3 | 100kHz | 342000 - \<20\> - 357000 | 361000 - \<20\> - 376000 |
| ... | ... | ...... | ...... |
| n41 | 15kHz | 499200 - \<3\> - 537999 | 499200 - \<3\> - 537999 |
| | 30kHz | 499200 - \<6\> - 537999 | 499200 - \<6\> - 537999 |
| ... | ... | ...... | ...... |
| n98 | 100kHz | 376000 - \<20\> - 384000 | N/A |

NR 信道栅格在 FR2 上的定义如下表[^4]所示。

| NR operating band | $ΔF_{Raster}$ | Uplink range of $N_{REF}$ (First – \<Step size\> – Last) |
| ---- | ---- | ---- |
| n257 | 60kHz | 2054166 - \<1\> - 2104165 |
|  | 120kHz | 2054166 - \<2\> - 2104165 |
| n258 | 60kHz | 2016667 - \<1\> - 2070832  |
|  | 120kHz | 2016667 - \<2\> - 2070831 |
| n259 | 60kHz | 2270832 - \<1\> - 2337499  |
|  | 120kHz | 2270832 - \<2\> - 2337499 |
| n260 | 60kHz | 2229166 - \<1\> - 2279165  |
|  | 120kHz | 2229167 - \<2\> - 2279165 |
| n261 | 60kHz | 2070833 - \<1\> - 2084999  |
|  | 120kHz | 2070833 - \<2\> - 2084999 |

以 band n41 为例，信道栅格粒度取 15kHz，由 $N_{REF}$的范围可知，其起始频率：

$$F_{REF} = F_{REF-Offs} + ΔF_{Global} (N_{REF} – N_{REF-Offs})=0+5*（499200-0）=2496MHz.$$

终止频率为：

$$F_{REF} = F_{REF-Offs} + ΔF_{Global} (N_{REF} – N_{REF-Offs})=0+5*（537999-0）=2690MHz.$$

在 2496 - 2690MHz 之间，每隔 15kHz，也就是 3 个全局信道栅格粒度（5kHz），设置为一个信道栅格频点，可以用于部署小区。

信道栅格与全局信道栅格的关系可以通过下图表示。图中，蓝色刻度代表全局信道栅格，红色刻度代表 band n41 上 NR 信道栅格所处频点。

![GlobalRasterChannelRaster](/images/5G_NR/GlobalRasterChannelRaster.png)

可以看出，在 FR1 频段，NR 信道栅格大部分采用了 100kHz 作为信道栅格粒度。这样做的原因是因为该部分频段目前部署了成熟的 4G LTE 网络，5G 在此与 4G 保持一致，可以很好的保持与 4G 的兼容性。此外，在 FR2 频段，5G 采用了与该段频谱所采用的子载波间隔大小相对应的信道栅格粒度，可以在部署网络时体现 5G 的灵活性，而不必与 4G 进行绑定。

## 同步栅格（Synchronization Raster）

同步栅格定义为发送同步信号（至少理论上）的一系列特定频点。终端通过同步信号来确定小区并与小区实现时频同步。可见同步栅格也必将时服务于信道栅格的。4G 将同步栅格与信道栅格绑定，即小区中心频点的位置也就是同步信号的位置。但是，5G 的带宽远远大于 4G 的系统带宽。若沿用 4G 的设计，将同步栅格的粒度设计为固定的 100kHz，那么在上百兆的带宽上将会有过多的同步信号。可以预见终端将会在搜索同步信号的过程中浪费大量的时间与电量。为此，5G 将同步栅格与信道栅格进行解耦，分别进行定义。为了保证终端能够成功搜索到小区，只要保证在小区带宽中至少包含一个同步信号即可。

### 全局同步栅格（Global Synchronization Raster）

类似于全局信道栅格与 NR 信道栅格之间的关系，NR 的同步栅格是定义在全局同步栅格上的。每个全局同步栅格对应一个全局同步编号，具体如下表[^5]所示。

| Frequency Range | SS block frequency position $SS_{REF}$ | GSCN | Range of GSCN|
| ---- | ---- | ---- | ---- |
| 0 - 3000MHz | N * 1200kHz + M * 50 kHz;N=1:2499, M={1,3,5} (Default M=3) | 3N + (M -3) / 2 | 2 - 7498 |
| 3000 - 24250MHz |　3000 MHz + N * 1.44 MHz;N= 0:14756 | 7499 + N | 7499 - 22255 |
| 24250 - 100000MHz | 24250.08 MHz + N * 17.28 MHz;N = 0:4383 | 22256 + N | 22256 - 26639 |

由上表可以看出：
1. 在 0 - 3GHz 范围内，同步栅格的基本粒度是 1.22MHz，两组同步栅格之间相隔 1.22MHz。之所以说是两组，是因为除了由 N 决定的 一个大的间隔之外。还有一个由 M 决定的正负 100kHz 的偏移。
2. 在 3GHz - 24.25GHz 范围，同步栅格的基本粒度是 1.44MHz。
3. 在 24.25GHz - 100GHz 范围，同步栅格的基本粒度是 17.28MHz。

### NR 同步栅格（Synchronization Raster）

在全局同步栅格的基础上，可以进一步定义 NR 同步栅格。

NR 同步栅格在 FR1 上的定义如下表[^6]所示。

| NR operating band | SS  Block SCS | SS Block pattern | Uplink range of GSCN (First – \<Step size\> – Last) |
| ---- | ---- | ---- | ---- |
| n1 | 15kHz | Case A | 5279 - \<1> - 5419 |
| n3 | 15kHz | Case A | 4517 - \<1> - 4693 |
| n5 | 15kHz | Case A | 2177 - \<1> - 2230 |
|    | 30kHz | Case B | 2183 - \<1> - 2224 |
| ...| ... | ... | ... |
| n41 | 15kHz | Case A | 6246 - \<3> - 6717 |
|    | 30kHz | Case C | 6252 - \<3> - 6714 |

NR 同步栅格在 FR2 上的定义如下表[^7]所示。

| NR operating band | SS  Block SCS | SS Block pattern | Uplink range of GSCN (First – \<Step size\> – Last) |
| ---- | ---- | ---- | ---- |
| n257 | 120kHz | Case D | 22388 - \<1> - 22558 |
|    | 240kHz | Case E | 22390 - \<2> - 22556 |
| n258 | 120kHz | Case D | 22257 - \<1> - 22443 |
|    | 240kHz | Case E | 22258 - \<2> - 22442 |
| n260 | 120kHz | Case D | 22995 - \<1> - 23166 |
|    | 240kHz | Case E | 22996 - \<2> - 23164 |
| n261 | 120kHz | Case D | 22446 - \<1> - 22492 |
|    | 240kHz | Case E | 22446 - \<2> - 22490 |

从上述两表可以看出，大部分的 NR 同步栅格与全局同步栅格一致，但是在高频范围，NR 同步栅格会按一定比例放大栅格之间的间隔以减小终端同步搜索的次数。



[^1]:[38.101-1 - Table 5.1-1: Definition of frequency ranges].
[^2]:[38.104 - Table 5.4.2.1-1: NR-ARFCN parameters for the global frequency raster].
[^3]:[38.104 - Table 5.4.2.3-1: Applicable NR-ARFCN per operating band in FR1].
[^4]:[38.104 - Table 5.4.2.3-2: Applicable NR-ARFCN per operating band in FR2].
[^5]:[38.104 - Table 5.4.3.1-1: GSCN parameters for the global frequency raster].
[^6]:[38.104 - Table 5.4.3.3-1: Applicable SS raster entries per operating band (FR1)].
[^7]:[38.104 - Table 5.4.3.3-2: Applicable SS raster entries per operating band (FR2)]


