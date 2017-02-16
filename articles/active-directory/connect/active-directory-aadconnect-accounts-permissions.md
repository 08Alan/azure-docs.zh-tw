---
title: "Azure AD Connect：帳戶與權限 | Microsoft 文件"
description: "本主題描述使用和建立的帳戶以及所需的權限。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 68e475891a91e4ae45a467cbda2b7b51c8020dbd
ms.openlocfilehash: e5f643d444fb2bf00aa91083f5d09962372e0dbb


---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect：帳戶與權限
Azure AD Connect 安裝精靈提供兩個不同的路徑：

* 在快速設定中，精靈需要更多權限，以便在不需要建立使用者或個別設定權限的情況下，輕鬆設定您的組態。
* 在自訂設定中，精靈會提供更多選擇和選項；但在某些情況下，您必須確定自己擁有正確的權限。

## <a name="related-documentation"></a>相關文件
如果您尚未閱讀有關[整合內部部署身分識別與 Azure Active Directory](../active-directory-aadconnect.md) 的文件，下表提供相關主題的連結。

|主題 |連結|  
| --- | --- |
|下載 Azure AD Connect | [下載 Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|使用快速設定進行安裝 | [快速安裝 Azure AD Connect](./active-directory-aadconnect-get-started-express.md)|
|使用自訂設定進行安裝 | [自訂 Azure AD Connect 安裝](./active-directory-aadconnect-get-started-custom.md)|
|從 DirSync 升級 | [從 Azure AD 同步作業工具 (DirSync) 升級](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|安裝後 | [驗證安裝和指派授權 ](active-directory-aadconnect-whats-next.md)|

## <a name="express-settings-installation"></a>快速設定安裝
在「快速設定」中，安裝精靈會要求提供「AD DS 企業系統管理員」認證，以便設定您的內部部署 Active Directory，使其具備必要的 Azure AD Connect 權限。 如果您是從 DirSync 升級，可透過 AD DS 企業系統管理員認證來重設 DirSync 所使用帳戶的密碼。 您也需要 Azure AD 全域管理員認證。

| 精靈頁面 | 收集的認證 | 所需的權限 | 用於 |
| --- | --- | --- | --- |
| N/A |執行安裝精靈的使用者 |本機伺服器的系統管理員 |<li>建立用來作為 [同步引擎服務帳戶](#azure-ad-connect-sync-service-account)的本機帳戶。 |
| 連接至 Azure AD |Azure AD 目錄認證 |Azure AD 中的全域管理員角色 |<li>啟用 Azure AD 目錄中的同步處理。</li>  <li>建立在 Azure AD 中用於持續同步處理作業的 [Azure AD 帳戶](#azure-ad-service-account)。</li> |
| 連線到 AD DS |內部部署 Active Directory 認證 |Active Directory 中 Enterprise Admins (EA) 群組成員 |<li>在 Active Directory 中建立[帳戶](#active-directory-account)並授與權限。 這個建立的帳戶是在同步處理期間用來讀取和寫入目錄資訊。</li> |

### <a name="enterprise-admin-credentials"></a>企業管理員認證
這些認證只能在安裝期間使用，安裝完成後則無法使用。 企業管理員而不是網域管理員應該確定可以在所有網域中設定 Active Directory 中的權限。

### <a name="global-admin-credentials"></a>全域管理員認證
這些認證只能在安裝期間使用，安裝完成後則無法使用。 它是用來建立 [Azure AD 帳戶](#azure-ad-service-account) ，用於同步處理變更至 Azure AD。 帳戶也會在 Azure AD 中啟用同步處理做為一項功能。

### <a name="permissions-for-the-created-ad-ds-account-for-express-settings"></a>所建立之快速設定 AD DS 帳戶的權限
為了讀取和寫入 AD DS 而建立的 [帳戶](#active-directory-account) 如果是由快速設定所建立，會具備下列權限：

| 權限 | 用於 |
| --- | --- |
| <li>複寫目錄變更</li><li>複寫目錄變更 (全部) |密碼同步處理 |
| 讀取/寫入所有屬性 (使用者) |匯入和 Exchange 混合 |
| 讀取/寫入所有屬性 (iNetOrgPerson) |匯入和 Exchange 混合 |
| 讀取/寫入所有屬性 (群組) |匯入和 Exchange 混合 |
| 讀取/寫入所有屬性 (連絡人) |匯入和 Exchange 混合 |
| 重設密碼 |啟用密碼回寫的準備工作 |

## <a name="custom-settings-installation"></a>自訂設定安裝
使用自訂設定時，必須在安裝之前建立用來連接到 Active Directory 的帳戶。 您可以在 [建立 AD DS 帳戶](#create-the-ad-ds-account)中找到必須授與此帳戶的權限。

| 精靈頁面 | 收集的認證 | 所需的權限 | 用於 |
| --- | --- | --- | --- |
| N/A |執行安裝精靈的使用者 |<li>本機伺服器的系統管理員</li><li>如果使用完整的 SQL Server，使用者必須是 SQL 中的系統管理員 (SA)</li> |預設會建立用來作為 [同步引擎服務帳戶](#azure-ad-connect-sync-service-account)的本機帳戶。 只有在管理員未指定特定帳戶時才會建立帳戶。 |
| 安裝同步處理服務，服務帳戶選項 |AD 或本機使用者帳戶認證 |使用者權限會由安裝精靈授與 |如果系統管理員指定帳戶，則此帳戶會做為同步處理服務帳戶。 |
| 連接至 Azure AD |Azure AD 目錄認證 |Azure AD 中的全域管理員角色 |<li>啟用 Azure AD 目錄中的同步處理。</li>  <li>建立在 Azure AD 中用於持續同步處理作業的 [Azure AD 帳戶](#azure-ad-service-account)。</li> |
| 連接您的目錄 |各個連線至 Azure AD 之樹系的內部部署 Active Directory 認證 |權限取決於啟用的功能，並且可在 [Create the AD DS account](#create-the-ad-ds-account) |這個帳戶是在同步處理期間用來讀取和寫入目錄資訊。 |
| AD FS 伺服器 |如果執行精靈的使用者登入認證權限不足無法連線，精靈就會收集清單中每個伺服器的認證 |網域系統管理員 |安裝和設定 AD FS 伺服器角色。 |
| Web 應用程式 Proxy 伺服器 |如果執行精靈的使用者登入認證權限不足無法連線，精靈就會收集清單中每個伺服器的認證 |目標電腦上的本機系統管理員 |安裝和設定 WAP 伺服器角色。 |
| Proxy 信任憑證 |Federation Service 信任認證 (Proxy 用來註冊 FS 信任憑證的認證) |網域帳戶是 AD FS 伺服器的本機系統管理員 |FS-WAP 信任憑證的首次註冊。 |
| AD FS 服務帳戶頁面，「使用網域使用者帳戶選項」 |AD 使用者帳戶認證 |網域使用者 |AD 使用者帳戶所提供的認證將做為 AD FS 服務的登入帳戶。 |

### <a name="create-the-ad-ds-account"></a>建立 AD DS 帳戶
安裝 Azure AD Connect 時，在 [連接您的目錄]  頁面上指定的帳戶必須存在於 Active Directory，並且獲得必要的權限。 安裝精靈不會驗證權限，在同步處理期間只會發現問題。

您需要的權限取決於您啟用的選用功能。 如果您有多個網域，則必須對樹系中的所有網域授與權限。 如果您未啟用任何一項功能，預設的 **網域使用者** 權限就已足夠。

| 功能 | 權限 |
| --- | --- |
| 密碼同步處理 |<li>複寫目錄變更</li>  <li>複寫目錄變更 (全部) |
| Exchange 混合式部署 |[Exchange 混合回寫](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback)中記載了使用者、群組和連絡人適用的屬性的寫入權限。 |
| 密碼回寫 |[開始使用密碼管理](../active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions)中記載了使用者適用的屬性的寫入權限。 |
| 裝置回寫 |[裝置回寫](active-directory-aadconnect-feature-device-writeback.md)中所述的使用 PowerShell 指令碼授與權限。 |
| 群組回寫 |讀取、建立、更新和刪除散發群組所在 OU 中的群組物件。 |

## <a name="upgrade"></a>升級
當您從一個 Azure AD Connect 版本升級到新版本時，需要下列權限：

| 主體 | 所需的權限 | 用於 |
| --- | --- | --- |
| 執行安裝精靈的使用者 |本機伺服器的系統管理員 |更新二進位檔案。 |
| 執行安裝精靈的使用者 |ADSyncAdmins 的成員 |對同步處理規則和其他組態進行變更。 |
| 執行安裝精靈的使用者 |如果您使用同步處理引擎資料庫的完整 SQL Server: DBO (或類似選項) |變更資料庫層級，例如更新含有新資料行的資料表。 |

## <a name="more-about-the-created-accounts"></a>已建立帳戶的相關資訊
### <a name="active-directory-account"></a>Active Directory 帳戶
如果您使用快速設定，則會在 Active Directory 中建立用於同步處理的帳戶。 建立的帳戶位於樹系根網域的使用者容器中，並且其名稱前面會是 **MSOL_**。 系統會使用不會過期的長複雜密碼建立帳戶。 如果在網域中有密碼原則，請確定允許這個帳戶使用長密碼和複雜密碼。

![AD 帳戶](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### <a name="azure-ad-connect-sync-service-accounts"></a>Azure AD Connect 同步處理服務帳戶
安裝精靈會建立本機服務帳戶 (除非您在自訂設定指定要使用的帳戶)。 此帳戶的前面會加上 **AAD_** 並用做實際同步處理服務的執行身分。 如果您在網域控制站上安裝 Azure AD Connect，則在網域中建立帳戶。 如果您使用執行 SQL Server 的遠端伺服器，或使用要求驗證的 Proxy，**AAD_** 服務帳戶就必須位於網域中。

![同步服務帳戶](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

系統會使用不會過期的長複雜密碼建立帳戶。

這個帳戶是安全儲存其他帳戶密碼的方式。 這些其他帳戶的密碼會加密儲存在資料庫中。 加密金鑰的私密金鑰是使用 Windows 資料保護 API (DPAPI) 的密碼編譯服務祕密金鑰加密來保護。 由於 Windows 基於安全性理由之後會摧毀加密金鑰，您不應該重設服務帳戶的密碼。

如果您使用完整的 SQL Server，那麼服務帳戶會是為同步引擎所建立的資料庫 DBO。 使用其他權限，服務將無法如預期般運作。 也會建立 SQL 登入。

此帳戶也會獲授與檔案、登錄機碼及與其他同步引擎相關的物件權限。

### <a name="azure-ad-service-account"></a>Azure AD 服務帳戶
系統會在 Azure AD 中建立帳戶供同步服務使用。 此帳戶可以由其顯示名稱來識別。

![AD 帳戶](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

使用帳戶所在伺服器的名稱可以透過使用者名稱的第二個部分來識別。 在圖中，伺服器名稱是 FABRIKAMCON。 如果您有預備伺服器，則每個伺服器會有自己的帳戶。 Azure AD 中有 10 個同步服務帳戶的限制。

系統會使用不會過期的長複雜密碼建立服務帳戶， 且該帳戶會獲得特殊角色**目錄同步處理帳戶**，其僅具有執行目錄同步處理工作的權限。 您無法透過 Azure AD Connect 精靈以外的方式授與特殊的內建角色，且 Azure 入口網站會顯示此帳戶具備**使用者**角色。

![AD 帳戶角色](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](../active-directory-aadconnect.md)。




<!--HONumber=Dec16_HO3-->


