---
title: 使用 Azure Active Directory 中的條件式存取來管理 Azure 管理的存取
description: 深入了解如何使用 Azure AD 中的條件式存取來管理 Azure 管理的存取。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: b824d122a5d26c17c41a0e2ea1c595c9e2dd7206
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354343"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>使用條件式存取來管理 Azure 管理的存取

Azure Active Directory (Azure AD) 中的條件式存取會根據您指定的特定條件，控制雲端應用程式的存取。 若要允許存取，您可建立條件式存取原則，以根據原則中的需求是否相符來允許或封鎖存取。 

一般而言，您可使用條件式存取來控制雲端應用程式的存取。 您也可以設定原則，以根據特定條件 (例如登入風險、位置或裝置) 來控制 Azure 管理的存取，以及強制執行各項需求 (如多重要素驗證)。

若要建立 Azure 管理的原則，您可在選擇要套用原則的應用程式時，選取 [雲端應用程式] 之下的 [Microsoft Azure 管理]。

![Azure 管理的條件式存取](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

您建立的原則會套用至所有的 Azure 管理端點，包括 Azure 入口網站、Azure Resource Manager 提供者、傳統服務管理 API、Azure PowerShell 和 Visual Studio 訂用帳戶管理員入口網站。 請注意，此原則適用於可呼叫 Azure Resource Manager API 的 Azure PowerShell。 不適用於呼叫 Microsoft Graph 的[Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)。

> [!CAUTION]
> 在設定原則來管理 Azure 管理的存取之前，務必了解條件式存取的運作方式。 請確定您未建立可能會封鎖您自己存取入口網站的條件。

如需有關如何設定和使用條件式存取的詳細資訊，請參閱 [Azure Active Directory 中的條件式存取](../active-directory/active-directory-conditional-access-azure-portal.md)。