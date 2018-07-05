---
title: 在 Azure Active Directory 中指派系統管理員角色 | Microsoft Docs
description: 系統管理員角色可以新增使用者、指派系統管理角色、重設使用者密碼、管理使用者授權或管理網域。 獲指派管理員角色的使用者對於貴公司訂閱的所有雲端服務，具有相同的權限。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 06/07/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 1de2482b7795bbed82874b6eea29f89f1ff52560
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939743"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中指派系統管理員角色

使用 Azure Active Directory (Azure AD) 時，您可以指定個別的系統管理員來執行不同的功能。 系統管理員可以存取 Azure 入口網站中的各種功能，視其角色而定，將可以建立或編輯使用者、將系統管理角色指派給其他人、重設使用者密碼、管理使用者授權，以及管理網域等。 不論您是在 Office 365 入口網站還是 Azure 入口網站中指派角色，或是使用適用於 Windows PowerShell 的 Azure AD 模組來指派角色，使用者只要獲指派系統管理員角色，在您組織所訂閱的所有雲端服務中都擁有相同的權限。

## <a name="details-about-the-global-administrator-role"></a>全域管理員角色的詳細資料
全域管理員可以存取所有系統管理功能。 註冊 Azure 訂用帳戶的人員預設會獲指派目錄的全域管理員角色。 只有全域管理員才能指派其他系統管理員角色。

## <a name="assign-or-remove-administrator-roles"></a>指派或移除系統管理員角色
若要了解如何將系統管理角色指派給 Azure Active Directory 中的使用者，請參閱[在 Azure Active Directory 中將使用者指派給系統管理員角色](fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="available-roles"></a>可用的角色
可用的系統管理員角色如下：

* **應用程式系統管理員**：此角色中的使用者可以建立和管理企業應用程式、應用程式註冊和應用程式 Proxy 設定的所有層面。 此角色也會授與能力來同意委派的權限以及 Microsoft Graph 和 Azure AD Graph 以外的應用程式權限。 建立新的應用程式註冊或企業應用程式時，不會加入此角色的成員作為擁有者。

* **應用程式開發人員**：將 [使用者可以註冊應用程式] 設定設為 [否] 時，此角色中的使用者可以建立應用程式註冊。 將 [使用者可同意應用程式代表自己存取公司資料] 設定設為 [否] 時，此角色也允許成員代表他們自己同意。 建立新的應用程式註冊或企業應用程式時，不會加入此角色的成員作為擁有者。

* **計費管理員**：進行採購、管理訂用帳戶、管理支援票證，以及監控服務健全狀況。

* **雲端應用程式系統管理員**：此角色中的使用者具有與應用程式系統管理員角色相同的權限，但不包括管理應用程式 Proxy 的能力。 此角色會授與能力來建立和管理企業應用程式和應用程式註冊的所有層面。 此角色也會授與能力來同意委派的權限以及 Microsoft Graph 和 Azure AD Graph 以外的應用程式權限。 建立新的應用程式註冊或企業應用程式時，不會加入此角色的成員作為擁有者。

* **合規性管理員**：此角色的使用者擁有 Office 365 安全性與合規性中心和 Exchange 系統管理中心的管理權限。 如需詳細資訊，請參閱[關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

* **條件式存取系統管理員**：具有此角色的使用者能夠管理 Azure Active Directory 條件式存取設定。
  > [!NOTE]
  > 若要在 Azure 中部署 Exchange ActiveSync 條件式存取原則，使用者也必須是全域系統管理員。
  
* **裝置系統管理員**：此角色是只可指派為[裝置設定](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)中的其他本機系統管理員。 具有此角色的使用者，會在已加入 Azure Active Directory 的所有 Windows 10 裝置上，成為本機電腦系統管理員。 它們並沒有在 Azure Active Directory 中管理裝置物件的能力。

* **目錄讀取器**︰這是舊版角色，用來指派給不支援 [同意架構](active-directory-integrating-applications.md)的應用程式。 不應將它指派給任何使用者。

* **目錄同步作業帳戶**：請勿使用。 此角色會自動指派給 Azure AD Connect 服務，不適用於也不支援任何其他用途。

* **目錄寫入器**︰這是舊版角色，用來指派給不支援 [同意架構](active-directory-integrating-applications.md)的應用程式。 不應將它指派給任何使用者。

* **管理員**︰具備此角色的使用者在有 Microsoft Dynamics 365 服務時，於該服務內具有全域權限，以及管理支援票證和監控服務健全狀況的能力。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

* **Exchange 服務管理員**︰在有 Microsoft Exchange Online 服務時，具備此角色的使用者在該服務內會具有全域權限。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

* **全域系統管理員 / 公司系統管理員 / 租用戶系統管理員**︰具有此角色的使用者可以存取 Azure Active Directory 中所有的系統管理功能，以及同盟 Azure Active Directory 的服務，例如 Exchange Online、SharePoint Online 和 Skype for Business Online。 註冊 Azure Active Directory 租用戶的人員會變成全域管理員。 只有全域管理員才能指派其他系統管理員角色。 您的公司可以有多位全域管理員。 全域系統管理員可以為任何使用者和所有其他系統管理員重設密碼。

  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 及 Azure AD PowerShell 中，是將此角色識別為「公司系統管理員」。 它是 [Azure 入口網站](https://portal.azure.com)中的「全域管理員」。
  >
  >

* **來賓邀請者**︰當「可邀請成員」使用者設定設為 [否] 時，此角色中的使用者可管理 Azure Active Directory B2B 來賓使用者邀請。 在[關於 Azure AD B2B 共同作業](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)中查看 B2B 共同作業的詳細資訊。 這不包含任何其他權限。

* **資訊保護系統管理員**：具備此角色的使用者只在「Azure 資訊保護」服務上具有使用者權限。 他們並未取得「Identity Protection 中心」、Privileged Identity Management、「監視 Office 365 服務健康狀況」及「Office 365 安全與規範中心」上的使用者權限。 他們可以設定「Azure 資訊保護」原則的標籤、管理保護範本，以及啟用保護。

* **Intune 服務管理員**︰在有 Microsoft Intune Online 服務時，具備此角色的使用者在該服務內會具有全域權限。 此外，此角色包含管理使用者和裝置的能力，可相關聯原則以及建立和管理群組。

* **信箱管理員**︰此角色僅用來當作 RIM Blackberry 裝置的 Exchange Online 電子郵件支援的一部分。 如果您的組織不使用 RIM Blackberry 裝置上的 Exchange Online 電子郵件，請勿使用此角色。

* **訊息中心讀者**：此角色中的使用者可以在 [Office 365 訊息中心](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)內，為他們的組織監視所設服務 (例如 Exchange、Intune 和 Microsoft Teams) 的通知和諮詢健康情況更新。 訊息中心讀者每週會收到貼文的電子郵件摘要和更新，並且可以在 Office 365 中分享訊息中心的貼文。 在 Azure AD 中，指派至此角色的使用者只會有 Azure AD 服務的唯讀存取權，與使用者和群組一樣。 

* **合作夥伴第 1 層支援**︰請勿使用。 此角色已被取代，而且未來將從 Azure AD 中移除。 此角色僅供少數 Microsoft 轉售合作夥伴使用，不適用於一般用途。

* **合作夥伴第 2 層支援**︰請勿使用。 此角色已被取代，而且未來將從 Azure AD 中移除。 此角色僅供少數 Microsoft 轉售合作夥伴使用，不適用於一般用途。

* **密碼管理員 / 技術支援中心管理員**：具備此角色的使用者可以變更密碼、管理服務要求，以及監視服務健康狀態。 技術支援中心管理員只能變更使用者及其他技術支援中心管理員的密碼。 

  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中，會將此角色識別為「技術支援中心管理員」。 它是 [Azure 入口網站](https://portal.azure.com/)中的「密碼管理員」。
  >
  >
  
* **Power BI 服務管理員**︰具備此角色的使用者在有 Microsoft Power BI 服務時，於該服務內具有全域權限，以及管理支援票證和監控服務健全狀況的能力。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-001&ad=US)。

* **特殊權限角色管理員**：具備此角色的使用者可以管理 Azure Active Directory 中，以及 Azure AD Privileged Identity Management 內的角色指派。 此外，這個角色允許各個層面的 Privileged Identity Management 管理。

* **報告讀者**：具備此角色的使用者可以檢視 Office 365 系統管理中心內的使用情況報告資料和報告儀表板，以及 PowerBI 中的採用內容套件。 此外，此角色還可讓使用者存取 Azure AD 中的登入報告與活動，以及 Microsoft Graph 報告 API 所傳回的資料。 獲指派「報告讀者」角色的使用者只能存取相關的使用情況和採用計量。 他們並不具備任何系統管理權限，因此無法進行設定或存取產品特定的系統管理中心 (例如 Exchange)。 

* **安全性系統管理員**︰具有此角色的使用者擁有安全性讀取者角色的所有唯讀權限，再加上管理與安全性相關之服務設定的能力︰Azure Active Directory Identity Protection、Azure 資訊保護、Privileged Identity Management 和 Office 365 安全與規範中心。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)中取得。

* **安全性讀取者**︰具有此角色的使用者具有全域唯讀存取權，包括 Azure Active Directory、身分識別保護、Privileged Identity Management，以及能夠讀取 Azure Active Directory 登入報表和稽核記錄檔的所有資訊。 角色也會在 Office 365 安全性與法規遵循中心授與唯讀權限。 關於 Office 365 權限的詳細資訊可在 [Office 365 安全性與法規遵循中心的權限](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)中取得。

* **服務支援系統管理員**︰具有此角色的使用者可以使用 Microsoft 開啟 Azure 和 Office 365 服務的支援要求，以及檢視 Azure 入口網站的服務儀表板和訊息中心以及 Office 365 系統管理入口網站。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

* **SharePoint 服務管理員**︰具備此角色的使用者在有 Microsoft SharePoint Online 服務時，於該服務內具有全域權限，以及管理支援票證和監控服務健全狀況的能力。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

* **商務用 Skype / Lync 服務管理員**︰在有 Microsoft 商務用 Skype 服務時，具備此角色的使用者在該服務內會具有全域權限，以及在 Azure Active Directory 中管理 Skype 特定的使用者屬性。 此外，此角色會授與管理支援票證及監控服務健全狀況的能力。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中，會將此角色識別為「Lync 服務管理員」。 它是 [Azure 入口網站](https://portal.azure.com/)中的「商務用 Skype 服務系統管理員」。
  >
  >

* **使用者帳戶管理員**︰具有此角色的使用者可以建立及管理使用者和群組的所有層面。 此外，此角色包含管理支援票證及監控服務健全狀況的能力。 適用某些限制。 例如，此角色並不允許刪除全域管理員。 使用者帳戶管理員只能變更使用者、技術支援中心管理員及其他使用者帳戶管理員的密碼。

## <a name="administrator-permissions"></a>系統管理員權限

### <a name="application-administrator"></a>應用程式系統管理員

| 可以執行 | 無法執行 |
| --- | --- |
| 讀取所有目錄資訊<br>建立應用程式註冊<br>更新應用程式註冊屬性<br>擷取企業應用程式<br>管理應用程式註冊權限<br>刪除應用程式註冊<br>管理企業應用程式單一登入設定<br>管理企業應用程式佈建設定<br>管理企業應用程式自助設定<br>管理企業應用程式權限設定<br>管理應用程式存取<br>管理佈建設定<br>刪除企業應用程式<br>代表所有人同意所有委派的權限要求<br>代表所有人同意 Azure AD Graph 或 Microsoft Graph 以外的所有應用程式權限要求<br>管理應用程式 Proxy 設定<br>存取服務設定<br>監視服務健康情況<br>管理支援票證<br>讀取隱藏的群組成員資格 | 建立、編輯及刪除群組<br>管理使用者授權<br>使用目錄同步作業<br>檢視登入報告與稽核記錄 | 

### <a name="application-developer"></a>應用程式開發人員

| 可以執行 | 無法執行 |
| --- | --- |
| 讀取所有目錄資訊<br>建立應用程式註冊<br>代表自己同意 | 檢視登入與稽核記錄<br>讀取隱藏的群組成員資格 |

### <a name="billing-administrator"></a>計費管理員

| 可以執行 | 無法執行 |
| --- | --- |
|<p>檢視公司與使用者資訊</p><p>建立 Office 支援票證</p><p>執行 Office 產品的計費和購買作業</p> |<p>重設使用者密碼</p><p>建立和管理使用者檢視</p><p>建立、編輯和刪除使用者與群組，以及管理使用者授權</p><p>管理網域</p><p>管理公司資訊</p><p>將系統管理角色委派給其他人</p><p>使用目錄同步作業</p><p>檢視稽核記錄檔</p> |

### <a name="cloud-application-administrator"></a>雲端應用程式系統管理員

| 可以執行 | 無法執行 |
| --- | --- |
| 讀取所有目錄資訊<br>建立應用程式註冊<br>更新應用程式註冊屬性<br>擷取企業應用程式<br>管理應用程式註冊權限<br>刪除應用程式註冊<br>管理企業應用程式單一登入設定<br>管理企業應用程式佈建設定<br>管理企業應用程式自助設定<br>管理企業應用程式權限設定<br>管理應用程式存取<br>管理佈建設定<br>刪除企業應用程式<br>代表所有人同意所有委派的權限要求<br>代表所有人同意 Azure AD Graph 或 Microsoft Graph 以外的所有應用程式權限要求<br>存取服務設定<br>監視服務健康情況<br>管理支援票證<br>讀取隱藏的群組成員資格 | 管理應用程式 Proxy 設定<br>建立、編輯及刪除群組<br>管理使用者授權<br>使用目錄同步作業<br>檢視登入報告與稽核記錄 |

### <a name="conditional-access-administrator"></a>條件式存取系統管理員

| 可以執行 | 無法執行 |
| --- | --- |
|<p>檢視公司與使用者資訊</p><p>管理條件式存取設定</p> |<p>重設使用者密碼</p><p>建立和管理使用者檢視</p><p>建立、編輯和刪除使用者與群組，以及管理使用者授權</p><p>管理網域</p><p>管理公司資訊</p><p>將系統管理角色委派給其他人</p><p>使用目錄同步作業</p><p>檢視稽核記錄檔</p>|

### <a name="global-administrator"></a>全域管理員
| 可以執行 | 無法執行 |
| --- | --- |
|<p>檢視公司與使用者資訊</p><p>建立 Office 支援票證</p><p>執行 Office 產品的計費和購買作業</p><p>重設使用者密碼</p><p>重設其他系統管理員的密碼</p> <p>建立和管理使用者檢視</p><p>建立、編輯和刪除使用者與群組，以及管理使用者授權</p><p>管理網域</p><p>管理公司資訊</p><p>將系統管理角色委派給其他人</p><p>使用目錄同步作業</p><p>啟用或停用多重要素驗證</p><p>檢視稽核記錄檔</p> |N/A |

### <a name="password-administrator--helpdesk-administrator"></a>密碼管理員 / 技術支援中心管理員
| 可以執行 | 無法執行 |
| --- | --- |
| <p>檢視公司與使用者資訊</p><p>建立 Office 支援票證</p><p>只能變更使用者及其他技術支援中心管理員的密碼</p>|<p>執行 Office 產品的計費和購買作業</p><p>建立和管理使用者檢視</p><p>建立、編輯和刪除使用者與群組，以及管理使用者授權</p><p>管理網域</p><p>管理公司資訊</p><p>將系統管理角色委派給其他人</p><p>使用目錄同步作業</p><p>檢視報告</p>|

### <a name="information-protection-administrator"></a>資訊保護管理員
在 | 可以執行
-------- | ---------
Azure 資訊保護 | <li>在全域和有範圍的原則中設定標籤和設定<li>設定及管理保護範本<li>啟用或停用保護--
 
### <a name="reports-reader"></a>報告讀者 
可以執行 | 無法執行
------ | ----------
檢視 Azure AD 登入報告與稽核記錄<br>檢視公司與使用者資訊<br>存取 Office 365 使用情況儀表板 | 建立和管理使用者檢視<br>建立、編輯和刪除使用者與群組，以及管理使用者授權<br>將系統管理角色委派給其他人<br>管理公司資訊

### <a name="security-reader"></a>安全性讀取者
| 在 | 可以執行 |
| --- | --- |
| 身分識別防護中心 |讀取安全性功能的所有安全性報告和設定資訊<ul><li>反垃圾郵件<li>加密<li>資料外洩防護<li>反惡意程式碼<li>進階威脅防護<li>防網路釣魚<li>郵件流程規則 |
| Privileged Identity Management |<p>以唯讀方式存取 Azure AD PIM 中所顯示的一切資訊︰Azure AD 角色指派的原則和報告、安全性檢閱，以及在未來還可透過讀取來存取 Azure AD 角色指派以外案例的原則資料和報告。<p>**無法**註冊 Azure AD PIM 或對它進行任何變更。 擔任此角色的人員可以在 PIM 的入口網站中，或是透過 PowerShell，為其他角色 (例如「全域管理員」或「特殊權限角色管理員」) 的候選人啟用角色。 |
| <p>監視 Office 365 服務健康狀況</p><p>Office 365 安全性與法規遵循中心</p> |<ul><li>讀取和管理警示<li>讀取安全性原則<li>讀取威脅情報、執行 Cloud App Discovery，以及在搜尋和調查時執行隔離<li>讀取所有報告 |

### <a name="security-administrator"></a>安全性系統管理員
| 在 | 可以執行 |
| --- | --- |
| 身分識別防護中心 |<ul><li>「安全性讀取者」角色的所有權限。<li>此外，還能夠執行除了重設密碼以外的所有 IPC 作業。 |
| Privileged Identity Management |<ul><li>「安全性讀取者」角色的所有權限。<li>**無法**管理 Azure AD 角色成員資格或設定。 |
| <p>監視 Office 365 服務健康狀況</p><p>Office 365 安全性與法規遵循中心 |<ul><li>「安全性讀取者」角色的所有權限。<li>可以設定「進階威脅防護」功能中的所有設定 (惡意程式碼和病毒防護、惡意 URL 組態、URL 追蹤等)。 |

### <a name="service-administrator"></a>服務管理員
| 可以執行 | 無法執行 |
| --- | --- |
| <p>檢視公司與使用者資訊</p><p>建立 Office 支援票證</p> |<p>重設使用者密碼</p><p>執行 Office 產品的計費和購買作業</p><p>建立和管理使用者檢視</p><p>建立、編輯和刪除使用者與群組，以及管理使用者授權</p><p>管理網域</p><p>管理公司資訊</p><p>將系統管理角色委派給其他人</p><p>使用目錄同步作業</p><p>檢視稽核記錄檔</p> |

### <a name="user-account-administrator"></a>使用者帳戶管理員
| 可以執行 | 無法執行 |
| --- | --- |
| <p>檢視公司與使用者資訊</p><p>建立 Office 支援票證</p><p>只能變更使用者、技術支援中心管理員及其他使用者帳戶管理員的密碼</p><p>建立和管理使用者檢視</p><p>建立、編輯和刪除使用者與群組，以及管理使用者授權，但有限制。 他無法刪除全域管理員或建立其他管理員。</p> |<p>執行 Office 產品的計費和購買作業</p><p>管理網域</p><p>管理公司資訊</p><p>將系統管理角色委派給其他人</p><p>使用目錄同步作業</p><p>啟用或停用多重要素驗證</p><p>檢視稽核記錄檔</p> |

### <a name="to-add-a-user-as-a-global-administrator"></a>將使用者新增為全域管理員

1. 使用具備租用戶目錄全域管理員身分的帳戶來登入 [Azure Active Directory 系統管理中心](https://aad.portal.azure.com)。

   ![開啟 Azure AD 系統管理中心](./media/active-directory-assign-admin-roles-azure-portal/active-directory-admin-center.png)

2. 選取 [使用者] > [所有使用者]。

3. 開啟您要指定為全域管理員之使用者的頁面。

4. 在命令列上選取 [目錄角色]。

5. 選取 [加入角色]。

6. 在目錄角色頁面上，選取 [全域管理員] 角色，然後按一下 [選取] 來儲存。

## <a name="deprecated-roles"></a>已過時的角色

以下是不應使用的角色。 它們已過時，而且未來將從 Azure AD 中移除。

* AdHoc 授權管理員
* 傳送電子郵件給經過驗證的使用者建立者
* 加入裝置
* 裝置管理員
* 裝置使用者
* 加入工作場所裝置

## <a name="next-steps"></a>後續步驟

* 若要深入了解如何變更 Azure 訂用帳戶的系統管理員，請參閱[新增或變更 Azure 訂用帳戶系統管理員](../billing-add-change-azure-subscription-administrator.md)
* 若要深入了解如何在 Microsoft Azure 中控制資源存取，請參閱 [了解 Azure 中的資源存取](../role-based-access-control/rbac-and-directory-admin-roles.md)
* 如需有關 Azure Active Directory 與您的 Azure 訂用帳戶產生關聯之方式的詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [管理使用者](active-directory-create-users.md)
* [管理密碼](active-directory-manage-passwords.md)
* [管理群組](fundamentals/active-directory-manage-groups.md)
