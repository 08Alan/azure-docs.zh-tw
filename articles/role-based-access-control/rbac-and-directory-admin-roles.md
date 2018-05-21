---
title: 了解 Azure 中的資源存取 | Microsoft Docs
description: 本主題說明有關使用訂用帳戶管理員來控制整個 Azure 入口網站中資源存取的概念
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: rolyon
ms.custom: it-pro;
ms.openlocfilehash: b993721a614f380aa1d5748bc925bd3bc9151841
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
---
# <a name="understanding-resource-access-in-azure"></a>了解 Azure 中的資源存取

在 Azure 中從計費觀點著手的存取控制。 Azure 帳戶的擁有者 (藉由造訪 [Azure 帳戶中心](https://account.azure.com)來存取) 為帳戶管理員 (AA)。 訂用帳戶是計費容器，但它們也可做為安全性界限：每個訂用帳戶都有一個服務管理員 (SA)，此管理員可以使用 [Azure 入口網站](https://portal.azure.com/)來新增、移除及修改該訂用帳戶中的 Azure 資源。 新訂用帳戶的預設 SA 為 AA，但 AA 可以在 Azure 帳戶中心變更 SA。

<br><br>![Azure 帳戶][1]

訂用帳戶也會與目錄產生關聯。 目錄會定義一組使用者。 他們可以是公司或學校中建立目錄的使用者，也可以是外部來賓使用者。 訂用帳戶可供已被指派為服務管理員 (SA) 或共同管理員 (CA) 的某些目錄使用者存取；唯一的例外就是，由於舊版原因，Microsoft 帳戶 (先前稱為 Windows Live ID) 不需存在目錄中，即可被指派為 SA 或 CA。

<br><br>![Azure 中的存取控制][2]

Azure 入口網站中的功能可讓使用 Microsoft 帳戶登入的 SA 變更訂用帳戶所關聯的目錄。 此作業會影響該訂用帳戶的存取控制。

<br><br>![簡單的使用者登入流程][3]

簡而言之，組織 (例如 Contoso) 將會強制執行同一組訂用帳戶的計費和存取控制。 也就是說，此目錄會與單一 Azure 帳戶所擁有的訂用帳戶相關聯。 成功登入 Azure 入口網站之後，使用者會看到兩個資源集合 (在上圖中以橘色表示)：

* 其使用者帳戶所在的目錄 (來自或加入為外部主體)。 請注意，用於登入的目錄與此計算無關，所以不論您在哪裡登入，一律會顯示您的目錄。
* 屬於訂用帳戶的資源，而這些訂用帳戶會與用來登入且使用者可以存取的目錄 (他們是 SA 或 CA) 相關聯。

<br><br>![具有多個訂用帳戶和目錄的使用者][4]

具有跨多個目錄之訂用帳戶的使用者能夠使用訂用帳戶篩選，來切換 Azure 入口網站的目前內容。 實際上，這會導致個別登入到不同的目錄，但卻是使用單一登入 (SSO) 順利完成的。

在訂用帳戶之間移動資源等作業，可能會因為訂用帳戶的這個單一目錄檢視而變得更加困難。 若要執行資源移轉，可能需要先在 [設定] 中的 [訂用帳戶] 頁面使用 [編輯目錄] 命令，將訂用帳戶關聯到相同的目錄。

## <a name="next-steps"></a>後續步驟
* 若要深入了解如何變更 Azure 訂用帳戶的管理員，請參閱 [如何新增或變更 Azure 管理員角色](../billing/billing-add-change-azure-subscription-administrator.md)
* 如需有關 Azure Active Directory 與您的 Azure 訂用帳戶產生關聯之方式的詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](../active-directory/active-directory-how-subscriptions-associated-directory.md)
* 如需有關如何在 Azure AD 中指派角色的詳細資訊，請參閱 [在 Azure Active Directory 中指派系統管理員角色](../active-directory/active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/rbac-and-directory-admin-roles/IC707931.png
[2]: ./media/rbac-and-directory-admin-roles/IC707932.png
[3]: ./media/rbac-and-directory-admin-roles/IC707933.png
[4]: ./media/rbac-and-directory-admin-roles/IC707934.png
