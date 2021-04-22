---
layout: post
title: PDCCH & PUCCH
categories: 5G_NR
description: 本篇主要阐述 5G 中控制信道相关内容，即 PDCCH 与 PUCCH。
keywords: PUCCH PDCCH
---

## 5G PDCCH 设计

终端接收 PDSCH 和发送 PUCCH、PUSCH 使用的资源都是通过 PDCHH 来进行调度的。换句话说，网络通过 PDCCH 通知终端在特定的资源去收发数据。然而，PDCCH 携带的控制信息本身也是一种数据，显然终端不可能收到调度调度信息，即调度 PDCCH 的信息。所以，终端只能在一个预定好的位置去监听 PDCCH，该过程也就是所谓的盲检。

