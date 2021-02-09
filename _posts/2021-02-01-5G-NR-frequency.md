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

1. NR 绝对信道编号（NR Absolute Radio Frequency Channel Number，NR-ARFCN）

NR-ARFCN 是 NR 为了方便，将 0~100GHz 的频谱范围进行了编号，相当于一把全局的标尺，NR 工作频谱范围就可以通过该标尺方便地表示。R17 给出的 NR-ARFCN 的定义如下表[^2]所示。

| Range of frequencies (MHz) | $ΔF_{Global}$ (kHz) | $F_{REF-Offs}$ (MHz) | $N_{REF-Offs}$ | Range of $N_{REF}$ |
| ---- | ---- | ---- | ---- | ---- |
| 0 - 300 | 5 | 0 | 0 | 0 - 599999 |
| 3000 - 24250 | 15 | 3000 | 600000 | 600000 - 2016666 |
| 24250 - 100000 | 60 | 24250.08 | 201667 | 2016667 – 3279165 |

有了上述表格定义，绝对频率值（MHz）FREF 可以通过 NR-ARFCN NREF 由下式计算得出：
$F_{REF} = F_{REF-Offs} + ΔF_{Global} (N_{REF} – N_{REF-Offs}).$

2. 信道栅格（Channel Raster）
在定义了 NR-ARFCN 后，NR 工作带宽可以方便的表示。NR X


[^1]:[38.101-1  Table 5.1-1: Definition of frequency ranges]
[^2]:[38.104 Table 5.4.2.1-1: NR-ARFCN parameters for the global frequency raster]

