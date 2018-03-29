---
title: 可用性區域概觀 | Microsoft Docs
description: 本文提供 Azure 中可用性區域的概觀。
services: ''
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2018
ms.author: markgal
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: b4db442a54b4360b75df40156ca0d4e4ee1eb0d1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="overview-of-availability-zones-in-azure-preview"></a>Azure 中可用性區域的概觀 (預覽)

可用性區域有助於防護資料中心層級的失敗。 其位於 Azure 地區內，而每個區域都有自己獨立的電源、網路和冷卻系統。 若要確保復原，所有已啟用的地區中至少要有三個不同的區域。 地區內可用性區域的實體和邏輯區隔可防止應用程式和資料遭遇區域層級的失敗。 

![地區中細分至一個區域的概念性檢視](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>支援可用性區域的地區

- 美國東部 2
- 美國中部
- 西歐
- 法國中部
- 東南亞

## <a name="services-that-support-availability-zones"></a>支援可用性區域的服務

支援可用性區域的 Azure 服務：

- Linux 虛擬機器
- Windows 虛擬機器
- 虛擬機器擴展集
- 受控磁碟
- 負載平衡器
- 公用 IP 位址
- 區域備援儲存體
- SQL Database

## <a name="get-started-with-the-availability-zones-preview"></a>開始使用可用性區域預覽

可用性區域預覽適用於美國東部 2、美國中部、西歐和法國中部地區的特定 Azure 服務。 

1. [註冊可用性區域預覽](http://aka.ms/azenroll)。 
2. 登入您的 Azure 訂用帳戶。
3. 選擇支援可用性區域的地區。
4. 您可以使用下列其中一個連結來開始搭配使用可用性區域與您的服務。 
    - [建立虛擬機器](../virtual-machines/windows/create-portal-availability-zone.md)
    - [建立虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
    - [使用 PowerShell 來新增受控磁碟](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
    - [負載平衡器](../load-balancer/load-balancer-standard-overview.md)

## <a name="next-steps"></a>後續步驟
- [快速入門範本](http://aka.ms/azqs)
