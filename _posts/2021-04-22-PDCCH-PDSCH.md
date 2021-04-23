---
layout: post
title: PDCCH & PUCCH
categories: 5G_NR
description: 本篇主要阐述 5G 中控制信道相关内容，即 PDCCH 与 PUCCH。
keywords: PUCCH PDCCH
---

## 5G PDCCH 设计

终端接收 PDSCH 和发送 PUCCH、PUSCH 使用的资源都是通过 PDCHH 来进行调度的。换句话说，网络通过 PDCCH 通知终端在特定的资源去收发数据。然而，PDCCH 携带的控制信息本身也是一种数据，显然终端不可能收到调度调度信息，即调度 PDCCH 的信息。所以，终端只能在一个预定好的位置去监听 PDCCH，该过程也就是所谓的盲检。

### CORESET-控制资源集

CORESET 通过 RRC 信令半静态地配置给终端，指明了 PDCCH 占用的一个时频资源块。频域上，CORESET 在 DL BWP 中占据的 PRB 可以是连续的或者不连续的，RRC；时域上，CORESET 占用 1~3 个 OFDM 符号，但是 CORESET 本身并不能确定时域位置。因此，CORESET 描述了一个频域位置确定，时域位置可以浮动的视频资源块，如下图所示。

![PDCCH-CORESE](/images/5G_NR/PDCCH-CORESET.png)
值得注意的是，和 BWP 类似，不同终端的 CORESET 在频域和时域都是可以重叠的。CORESET 配置主要有两个参数：
1. *frequencyDomainResource* ：PDCCH 的频域位置，BWP 中 6 个 RB 组成一个 RB 组，通过位图指示连续或不连续的 RB 组。
2. *Duration*：CORESET 时域上持续的时间，也就是PDCCH Monitoring Occasion 的长度，1~3个符号。

CORESET 内部是两级资源粒度构成：
1. 首先是频域上 1 个 PRB，时域上 1 个符号，构成一个 REG，即 1 个 REG 由 12 个 RE 构成。
2. 然后 1 个 CCE 包含 6 个 REG，即 72 个 RE，其中实际可用于 DCI 传输有 54 个，DMRS 传输占用 18 个RE。
3. 为了提高 PDCCH 传输性能，可以通过多个 CCE 重复。一个 PDCCH 包含的 CCE 数量称为聚合等级 AL。 AL 可以取值为 1、2、4、8 和 16。

REG 以时域优先的方式映射到 CCE，为了实现更好的频率分集需要在频域上把一个 CCE 的 REG 打散，同时为了在多个 REG 之间进行联合信道估计，打散 REG 是以 REG Bundle 为粒度进行的。一个 REG Bundle 包含 2、3 或 6 个 REG。即一个 CCE 的不同 REG 以 REG Bundle 为单位进行交织。如下图所示：

![PDCCH-CCE](/images/5G_NR/PDCCH-CCE.png)

简单起见，一个 CORESET 只能使用一种映射方式。每个 DL BWP 最多可以配置 3 个 CORESET，不同 CORESET 的配置参数是彼此独立的。

### SSS-搜索空间集

CORESET 指示了时域上浮动的时频资源，SSS 则指示了 CORESET 可能出现的时域位置。终端需要联合 CORESET 与 SSS 两个参数来确定 PDCCH 的具体时频位置。5G 还引入了 PDCCH Monitoring Occasion 的概念，长度为 CORESET 的 Duration。

SSS 的配置参数包括：
1. *monitoringSlotPeriodicityAndOffset*：指示 SSS 的时隙级周期 *ks* 和与周期起点的时隙级偏移量 *Os*。
2. *Duration*：SSS 持续的时隙数量。
3. *monitoringSymbolsWithinSlot*：通过 14bit 的位图指示一个实习内的 PDCCH Monitoring Occasion 的起始符号索引。一个时隙内可以有多个 PDCCH Monitoring Occasion。

![PDCCH-SSS-CORESET](/images/5G_NR/PDCCH-SSS-CORESET.png)

每个 DL BWP 可以配置 10 个 SSS。