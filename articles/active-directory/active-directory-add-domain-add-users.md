---
title: "將使用者指派給 Azure Active Directory 中的自訂網域 | Microsoft Docs"
description: "如何在 Azure Active Directory 中的自訂網域填入使用者帳戶。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 717b5a7c-7bc3-4ab1-98b5-4740b53338fe
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ccfa8d72b51c43e04d924d2b096e3cbc407af043
ms.contentlocale: zh-tw
ms.lasthandoff: 12/29/2016


---
# <a name="assign-users-to-a-custom-domain"></a>將使用者指派至自訂網域
在您將您的自訂網域新增至 Azure Active Directory 之後，您必須加入此網域的使用者帳戶，以便開始驗證它們。

## <a name="users-synced-in-from-a-directory-on-your-corporate-network"></a>從您的公司網路上的目錄中同步處理的使用者
如果您已經設定內部部署 Active Directory 與 Azure Active Directory 之間的連線，同步處理可以填入帳戶。 如需有關如何同步處理 Azure Active Directory 與內部部署 Active Directory 的詳細資訊，請參閱 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

## <a name="users-added-and-managed-in-the-cloud"></a>新增和管理雲端中的使用者
若要變更現有使用者帳戶的網域：

1. 使用全域管理員或使用者管理員帳戶開啟 Azure 傳統入口網站。
2. 開啟您的目錄。
3. 選取 [使用者]  索引標籤。
4. 從清單中選取使用者。
5. 變更使用者的網域，然後選取 [儲存] 。

使用 [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) 或 [Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations) 也可以完成此操作。

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>在建立新的使用者時選取自訂網域
1. 使用全域管理員或使用者管理員帳戶開啟 Azure 傳統入口網站。
2. 開啟您的目錄。
3. 選取 [使用者]  索引標籤。
4. 在命令列上選取 [新增] 。
5. 當您新增使用者名稱時，請從網域清單中選擇自訂網域。
6. 選取 [ **儲存**]。

## <a name="next-steps"></a>後續步驟
* [使用自訂網域名稱，以簡化您的使用者的登入體驗](active-directory-add-domain.md)
* [管理自訂網域名稱](active-directory-add-manage-domain-names.md)
* [了解 Azure AD 中的網域管理概念](active-directory-add-domain-concepts.md)


