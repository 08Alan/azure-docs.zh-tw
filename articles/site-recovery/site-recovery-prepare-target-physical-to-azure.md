---
title: "準備目標 (實體至 Azure) | Microsoft Docs"
description: "本文將說明如何準備您的 Azure 環境，以便開始將執行 Windows 或 Linux 的實體伺服器複寫至 Azure。"
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/22/2018
ms.author: bsiva
ms.openlocfilehash: 6704ddc24db8415051a6064bbde79a6fc527871a
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2018
---
# <a name="prepare-target-physical-to-azure"></a>準備目標 (實體至 Azure)
> [!div class="op_single_selector"]
> * [VMware 至 Azure](./site-recovery-prepare-target-vmware-to-azure.md)
> * [實體至 Azure](./site-recovery-prepare-target-physical-to-azure.md)

本文將說明如何準備您的 Azure 環境，以便開始將執行 Windows 或 Linux 的實體伺服器 (x64) 複寫至 Azure。

## <a name="prerequisites"></a>先決條件

本文假設：
- 您已建立復原服務保存庫，用以保護實體伺服器。 您可以從 [Azure 入口網站] (http://portal.azure.com "Azure 入口網站")建立復原服務保存庫。
- 您已[設定內部部署環境](./site-recovery-set-up-physical-to-azure.md)以將實體伺服器複寫至 Azure。

## <a name="prepare-target"></a>準備目標

完成**步驟 1：選取保護目標**和**步驟 2︰準備來源**之後，即會進入**步驟 3︰目標**

![準備目標](./media/site-recovery-prepare-target-physical-to-azure/prepare-target-physical-to-azure.png)

1. **訂用帳戶︰**從下拉式功能表中選取您想要的訂用帳戶，以將實體伺服器複寫至該訂用帳戶。
2. **部署模型︰**選取部署模型 (傳統或資源管理員)

根據所選的部署模型，系統會執行驗證以確保您的目標訂用帳戶中有至少一個相容的儲存體帳戶和虛擬網路，以便將您的實體伺服器複寫和容錯移轉至其中。

驗證成功完成後，按一下 [確定] 以移至下一個步驟。

如果您沒有相容的資源管理員儲存體帳戶或虛擬網路，您可以按一下頁面頂端的 [+ 儲存體帳戶] 或 [+ 網路] 按鈕來建立一個。

## <a name="next-steps"></a>後續步驟
[設定複寫設定](./site-recovery-setup-replication-settings-vmware.md)。
