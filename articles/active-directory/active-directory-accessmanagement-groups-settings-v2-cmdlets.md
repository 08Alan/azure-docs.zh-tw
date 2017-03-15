---
title: "適用於 Azure AD 中之群組管理的 Azure Active Directory PowerShell Cmdlet |Microsoft Azure"
description: "此頁面會提供 PowerShell 範例以協助您管理 Azure Active Directory 中的群組"
keywords: "Azure AD, Azure Active Directory, PowerShell, 群組, 群組管理"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7a5023dc-2727-4c25-8254-b531fc3244ac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: f4aeeaf13604443e0902112b4cc998ae1dcce4c2
ms.lasthandoff: 03/08/2017


---
# <a name="azure-active-directory-preview-cmdlets-for-group-management"></a>適用於群組管理的 Azure Active Directory Preview Cmdlet
> [!div class="op_single_selector"]
> * [Azure 入口網站](active-directory-groups-create-azure-portal.md)
> * [Azure 傳統入口網站](active-directory-accessmanagement-manage-groups.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

下列文件將提供範例，說明如何使用 PowerShell 管理 Azure Active Directory (Azure AD) 中的群組。  其中也提供有關如何使用 Azure AD PowerShell Preview 模組完成設定的資訊。 首先，您必須 [下載 Azure AD PowerShell 模組](https://www.powershellgallery.com/packages/AzureAD/)。

## <a name="installing-the-azure-ad-powershell-module"></a>安裝 Azure AD PowerShell 模組
若要安裝 AzureAD PowerShell 模組，請使用下列命令︰

    PS C:\Windows\system32> install-module azuread

若要確認已安裝 Preview 模組，請使用下列命令︰

    PS C:\Windows\system32> get-module azureadpreview

    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     1.1.146.0  azureadpreview                      {Add-AzureADAdministrati...}

現在您可以開始在模組中使用 Cmdlet。 如需有關 Azure AD 模組中各式 Cmdlet 的完整描述，請參閱[線上參考文件](https://docs.microsoft.com/en-us/powershell/azuread/)。

## <a name="connecting-to-the-directory"></a>連線到目錄
使用 Azure AD PowerShell Cmdlet 開始管理群組之前，您必須先將 PowerShell 工作階段連線至想要管理的目錄。 若要這樣做，請使用下列命令：

    PS C:\Windows\system32> Connect-AzureAD

Cmdlet 會提示您輸入要用以存取目錄的認證。 在此範例中，我們會使用 karen@drumkit.onmicrosoft.com 存取示範目錄。 Cmdlet 將會傳回確認，表示工作階段已成功連接到目錄︰

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

現在您可以開始使用 AzureAD Cmdlet 來管理您目錄中的群組。

## <a name="retrieving-groups"></a>擷取群組
若要從目錄中擷取現有的群組，您可以使用 Get-AzureADGroups Cmdlet。 若要擷取目錄中的所有群組，請在使用 Cmdlet 時不要使用參數：

    PS C:\Windows\system32> get-azureadgroup

Cmdlet 將會傳回所連接目錄中的所有群組。

您可以使用 -objectID 參數來擷取您指定其群組 objectID 的特定群組：

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

現在，Cmdlet 將傳回 objectID 與您輸入的參數值相符的群組︰

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

您可以使用 -filter 參數搜尋特定群組。 此參數採用 ODATA 篩選子句，並傳回和篩選條件相符的所有群組，如下列範例所示︰

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

請注意，AzureAD PowerShell Cmdlet 會實作 OData 查詢標準，請參閱 [這裡](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter)的詳細資訊。

## <a name="creating-groups"></a>建立群組
若要在目錄中建立新群組，請使用 New-AzureADGroup Cmdlet。 這個 Cmdlet 會建立名為 “Marketing" 的新安全性群組︰

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="updating-groups"></a>更新群組
若要更新現有的群組，請使用 Set-AzureADGroup Cmdlet。 在這個範例中，我們要變更 “Intune Administrators” 群組的 DisplayName 屬性。 首先，我們使用 Get-AzureADGroup Cmdlet 找到該群組，然後使用 DisplayName 屬性進行篩選︰

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

接下來，我們要將 Description 屬性變更為新值 “Intune Device Administrators”︰

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

現在如果我們再次尋找該群組，就會看到 Description 屬性已經更新，以反映新的值︰

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="deleting-groups"></a>刪除群組
若要從目錄中刪除群組，請使用 Remove-AzureADGroup Cmdlet，如下所示︰

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="managing-members-of-groups"></a>管理群組成員
如果您需要將新成員新增至群組，請使用Add-AzureADGroupMember Cmdlet。 此命令會將成員新增至上述範例中所使用的 Intune Administrators 群組︰

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

我們想要新增成員的群組，其 ObjectID 就是 -ObjectId 參數，而我們想要新增為群組成員的使用者，其 ObjectID 為 -RefObjectId。

若要取得群組的現有成員，請使用 Get-AzureADGroupMember Cmdlet，如此範例所示︰

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User

若要移除先前加入群組的成員，請使用Remove-AzureADGroupMember Cmdlet，如此處所示︰

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

若要驗證使用者的群組成員資格，請使用 Select-AzureADGroupIdsUserIsMemberOf Cmdlet。 這個 Cmdlet 會將要檢查群組成員資格的使用者，其 ObjectId 以及一份要檢查成員資格的群組清單做為參數。 務必要以 “Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck” 複雜變數類型的形式提供群組清單，因此我們必須先使用該類型建立一個變數：

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

接下來，我們提供 groupIds 的值，以簽入此複雜變數的 "GroupIds" 屬性︰

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

現在，如果我們想要檢查 ObjectID 為 72cd4bbd-2594-40a2-935c-016f3cfeeeea 的使用者，是否具有 $g 中任何群組的群組成員資格，我們應該使用︰

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


傳回的值就是成員中有這位使用者的群組清單。 您也可以使用 Select-AzureADGroupIdsContactIsMemberOf、Select-AzureADGroupIdsGroupIsMemberOf 或 Select-AzureADGroupIdsServicePrincipalIsMemberOf，在檢查特定群組清單中的連絡人、群組或服務主體成員資格時，採用這個方法

## <a name="managing-owners-of-groups"></a>管理群組擁有者
若要將擁有者新增到群組，請使用 Add-AzureADGroupOwner Cmdlet︰

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

我們想要加入擁有者的群組，其 ObjectID 就是 -ObjectId 參數，而我們想要新增為群組擁有者的使用者，其 ObjectID 為 -RefObjectId。

若要擷取群組的擁有者，請使用 Get AzureADGroupOwner:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

Cmdlet 將會傳回所指定群組的擁有者清單︰

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User

如果您想要從群組中移除擁有者，請使用 Remove-AzureADGroupOwner：

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="next-steps"></a>後續步驟
您可以在 [Azure Active Directory Cmdlet](https://docs.microsoft.com/en-us/powershell/azuread/)中找到更多 Azure Active Directory PowerShell 文件。

* [使用 Azure Active Directory 群組來管理資源的存取權](active-directory-manage-groups.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

