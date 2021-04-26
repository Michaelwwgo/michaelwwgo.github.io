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
2. 然后 1 个 CCE 包含 6 个 REG，即 72 个 RE，其中DMRS 传输占用每个 REG 中索引为 1、5、9 的子载波，一共 18 个 RE，实际可用于 DCI 传输有 54 个 RE。
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

## DCI Format

5G 定义了 UE-specific DCI 与 Group-common DCI。下面将分别介绍。

<table>
	<tr>
	    <th>类别</th>
	    <th>DCI Format</th>
	    <th>作用</th>
        <th>说明</th>
	</tr >
	<tr >
	    <td rowspan="8">UE-specific DCI</td>
	    <td>0_0</td>
	    <td>PUSCH 调度（Fallback 格式）</td>
        <td>只包含基本功能域，域大小尽可能固定，尽量少依赖 RRC 配置</td>
	</tr>
	<tr>
	    <td>0_1</td>
	    <td>PUSCH 调度（正常格式）</td>
        <td>包含全部功能域，域大小依赖 RRC 配置</td>
	</tr>
	<tr>
	    <td>0_2</td>
	    <td>PUSCH 调度（Compact 格式）</td>
        <td>R16 增强 URLLC 场景下 PDCCH 高可靠性引入</td>
	</tr>
	<tr>
	    <td>1_0</td>
	    <td>PDSCH 调度（Fallback 格式）</td>
        <td>只包含基本功能域，域大小尽可能固定，尽量少依赖 RRC 配置</td>
	</tr>
	<tr><td>1_1</td>
	    <td>PDSCH 调度（正常格式）</td>
        <td>包含全部功能域，域大小依赖 RRC 配置</td>
	</tr>
	<tr>
	    <td>1_2</td>
	    <td>PDSCH 调度（Compact 格式）</td>
        <td>R16 增强 URLLC 场景下 PDCCH 高可靠性引入</td>
	</tr>
	<tr>
	    <td>3_0</td>
	    <td>NR Sidelink 调度</td>
        <td>NR V2X 引入</td>
	</tr>
    <tr>
	    <td>3_1</td>
	    <td>LTE Sidelink 调度</td>
        <td>NR V2X 引入</td>
	</tr>
    	<tr >
	    <td rowspan="8">Group-common DCI</td>
	    <td>2_0</td>
	    <td>传输 SFI；可用 RB 集\COT 和 SSS 组切换信息</td>
        <td>为 D-TDD 引入，动态指示时隙结构；IAB\NR-U 和双激活协议栈切换等新特性引入</td>
	</tr>
	<tr>
	    <td >2_1</td>
	    <td>下行 Pre-emption Indication</td>
	    <td>为 URLLC 引入，下行预清空指示，支持 URLLC 与 eMBB 业务的复用</td>
	</tr>
	<tr>
	    <td>2_2</td>
	    <td>上行功控</td>
	    <td></td>
	</tr>
	<tr>
	    <td>2_3</td>
	    <td>SRS 功控</td>
	    <td></td>
	</tr>
	<tr>
	    <td>2_4</td>
	    <td>上行 Cancellation Indication</td>
	    <td>R16 增强 URLLC 上行发送取消引入</td>
	</tr>
	<tr>
	    <td>2_5</td>
	    <td>指示 IAB 系统中的软资源（Soft Resource）</td>
	    <td>为 IAB 引入</td>
	</tr>
    	<tr>
	    <td>2_6</td>
	    <td>终端节能信号</td>
	    <td>R16 UE Power Saving 引入</td>
	</tr>
</table>

小容量的 UE-specific 控制信息在 UE-specific DCI 和 Group-common DCI 中都可以传输。所谓的 Group-common DCI 其实是把一组终端的 DCI 打包然后一起发送给终端，每个终端然后在 PDCCH 中找到属于自己的 DCI 信息。

## 5G PUCCH 设计

