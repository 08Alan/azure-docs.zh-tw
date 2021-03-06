---
title: "如何在 Azure API 管理中使用角色型存取控制 | Microsoft Docs"
description: "了解如何在 Azure API 管理中使用內建角色和建立自訂角色"
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: b048a1db135d217b319541b92cf3c30b345d1a66
ms.openlocfilehash: cedab98452ad336748d805f0efbf50633badf8ab


---

# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>如何在 Azure API 管理中使用角色型存取控制
Azure API 管理需要 Azure 角色型存取控制 (RBAC) 才能針對 API 管理服務及實體 (例如 API、原則) 啟用細部存取管理。 本文章將為您提供 API 管理中內建角色和自訂角色的概觀。 如果您要深入了解 Azure 入口網站中的存取管理，請參閱[開始在 Azure 入口網站中使用存取管理](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/)

## <a name="built-in-roles"></a>內建角色
API 管理目前提供 3 個內建角色，並將在不久的將來再新增 2 個角色。 這些角色可以指派到不同的範圍，包括訂用帳戶、資源群組，以及個別 API 管理執行個體。 例如，假設「Azure API 管理服務讀者」角色指派給資源群組層級的使用者，則該使用者會有該資源群組內所有 API 管理執行個體的讀取權限。 

下表提供內建角色的簡短描述。 您可以使用 Azure 入口網站或其他工具 (包括 Azure [PowerShell](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-manage-access-powershell)、Azure [命令列介面](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-manage-access-azure-cli)和 [REST API](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-manage-access-rest)) 指派這些角色。 如需如何指派內建角色的詳細資料，請參閱[使用角色指派來管理 Azure 訂用帳戶資源的存取權](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/)。

| 角色          | 讀取權限<sup>[1]</sup> | 寫入權限<sup>[2]</sup> | 服務建立、刪除、調整，VPN 及自訂網域設定 | 存取舊版發行者入口網站 | 說明
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| Azure API 管理服務參與者 | ✓ | ✓  | ✓  | ✓ | 進階使用者。 具有 API 管理服務及實體 (例如 API、原則) 的完整 CRUD 存取權限。 具有舊版發行者入口網站的存取權限。 |
| Azure API 管理服務讀者 | ✓ | | || 具有 API 管理服務及實體的唯讀權限。 |
| Azure API 管理服務操作員 | ✓ | | ✓ | | 可以管理 API 管理服務，但無法管理實體。|
| Azure API 管理服務編輯者<sup>*</sup> | ✓ | ✓ | |  | 可以管理 API 管理實體，但無法管理服務。|
| Azure API 管理內容管理員<sup>*</sup> | ✓ | | | ✓ | 可以管理開發人員入口網站。 具有服務和實體的唯讀權限。|

<sup>[1] API 管理服務及實體 (例如 API、原則) 的讀取權限</sup>

<sup>[2] API 管理服務及實體的寫入權限，下列作業除外：1) 建立、刪除和調整執行個體 2) VPN 組態  3) 自訂網域名稱設定</sup>

<sup>\* 當我們將現有發行者入口網站的所有系統管理 UI 移轉至 Azure 入口網站後，將會提供「服務編輯者」角色。在發行者入口網站重構為僅包含管理開發人員入口網站的相關功能之後，將會提供「內容管理者」角色。</sup>  


## <a name="custom-roles"></a>自訂角色
如果內建角色都無法滿足您的特定需求，您可以建立自訂角色來提供更精細的 API 管理實體存取管理。 例如，您可以建立自訂角色，使其具有 API 管理服務的唯讀權限，但只有單一特定 API 的寫入權限。 若要深入了解自訂角色，請參閱 [Azure RBAC 中的自訂角色](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles)。 

當您建立自訂角色時，從其中一個內建角色開始會比較輕鬆。 編輯屬性來加入 Actions、NotActions 或 AssignableScopes，然後將變更另存為新角色。 以下範例從「Azure API 管理服務讀者」角色開始，建立一個名為「計算機 API 編輯者」的自訂角色。 自訂角色可以只指派給特定 API，如此就只能存取該 API。 

```
$role = Get-AzureRmRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzureRmRoleDefinition -Role $role
New-AzureRmRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

## <a name="watch-a-video-overview"></a>觀看影片概觀

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
> 
> 

## <a name="next-steps"></a>後續步驟

* 深入了解 Azure 中的角色型存取控制
  * [開始在 Azure 入口網站中使用存取管理](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/)
  * [使用角色指派來管理 Azure 訂用帳戶資源的存取權](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/)
  * [Azure RBAC 中的自訂角色](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles)



<!--HONumber=Feb17_HO1-->


