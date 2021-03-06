---
title: "在 Azure Active Directory 中指派系統管理員角色 | Microsoft Docs"
description: "說明 Azure Active Directory 可用的系統管理員角色和其指派方式。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7fc27e8e-b55f-4194-9b8f-2e95705fb731
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: fdb4ed95b25519fb7ff319fe20a0dc5c54506506
ms.lasthandoff: 04/28/2017


---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中指派系統管理員角色
> [!div class="op_single_selector"]
> * [Azure 入口網站](active-directory-assign-admin-roles-azure-portal.md)
> * [Azure 傳統入口網站](active-directory-assign-admin-roles.md)
>
>

使用 Azure Active Directory (Azure AD) 時，您可以指定個別的系統管理員來執行不同的功能。 這些系統管理員可以存取 Azure 入口網站或 Azure 傳統入口網站中的各種功能，此外，根據其角色，將可以建立或編輯使用者、將系統管理角色指派給其他人、重設使用者密碼、管理使用者授權，以及管理網域。 不論您是在 Office 365 入口網站還是 Azure 傳統入口網站中指派角色，或是使用適用於 Windows PowerShell 的 Azure AD 模組來指派角色，獲指派系統管理員角色的使用者在您組織所訂閱的所有雲端服務中都擁有相同的權限。

可用的系統管理員角色如下：

* **計費管理員**：進行採購、管理訂用帳戶、管理支援票證，以及監控服務健全狀況。
* **全域管理員/公司系統管理員**：可以存取所有系統管理功能。 註冊 Azure 帳戶的人員會變成全域管理員。 只有全域管理員才能指派其他系統管理員角色。 您的公司可以有多位全域管理員。

  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 及 Azure AD PowerShell 中，是將此角色識別為「公司系統管理員」。 它是 [Azure 入口網站](https://portal.azure.com)中的「全域管理員」。
  >
  >

* **合規性管理員**：具有此角色的使用者擁有 Office 365 安全與規範中心和 Exchange 系統管理中心的管理權限，以及在 Office 365 管理中心讀取稽核記錄的存取權。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

* **CRM 服務管理員**︰具備此角色的使用者在有 Microsoft CRM Online 服務時，於該服務內具有全域權限。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。
* **客戶加密箱存取核准者**︰已啟用「加密箱」服務時，具備此角色的使用者可以核准要求讓 Microsoft 工程師存取公司資訊。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。
* **裝置系統管理員**︰具備此角色的使用者會在所有已加入 Azure Active Directory 的 Windows 10 裝置上成為「系統管理員」。
* **目錄讀取器**︰這是舊版角色，用來指派給不支援 [同意架構](active-directory-integrating-applications.md)的應用程式。 不應將它指派給任何使用者。
* **目錄同步作業帳戶**：請勿使用。 此角色會自動指派給 Azure AD Connect 服務，不適用於也不支援任何其他用途。
* **目錄寫入器**︰這是舊版角色，用來指派給不支援 [同意架構](active-directory-integrating-applications.md)的應用程式。 不應將它指派給任何使用者。
* **Exchange 服務管理員**︰在有 Microsoft Exchange Online 服務時，具備此角色的使用者在該服務內會具有全域權限。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。
* **Intune 服務管理員**︰在有 Microsoft Intune Online 服務時，具備此角色的使用者在該服務內會具有全域權限。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。
* **商務用 Skype 服務管理員**︰在有 Microsoft 商務用 Skype 服務時，具備此角色的使用者在該服務內會具有全域權限。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。 這個角色先前稱為「Lync 服務管理員」  角色。
* **來賓邀請者**：具備此角色的使用者可以管理來賓邀請。 這不包含任何其他權限。
* **信箱管理員**︰此角色僅用來為 RIM Blackberry 裝置提供 Exchange Online 電子郵件支援。 如果您的組織不使用 RIM Blackberry 裝置上的 Exchange Online 電子郵件，請勿使用此角色。
* **合作夥伴第 1 層支援**︰請勿使用。 此角色已被取代，而且未來將從 Azure AD 中移除。 此角色僅供少數 Microsoft 轉售合作夥伴使用，不適用於一般用途。
* **合作夥伴第 2 層支援**︰請勿使用。 此角色已被取代，而且未來將從 Azure AD 中移除。 此角色僅供少數 Microsoft 轉售合作夥伴使用，不適用於一般用途。
* **密碼管理員/技術支援中心管理員**：重設密碼、管理服務要求，以及監視服務健全狀況。 密碼管理員可以僅重設使用者和其他密碼管理員的密碼。

  > [!NOTE]
  > 在 Microsoft Graph API、Azure AD Graph API 和 Azure AD PowerShell 中，會將此角色識別為「技術支援中心管理員」。
  >
  >

* **Power BI 服務管理員**︰在有 Microsoft Power BI 服務時，具備此角色的使用者在該服務內會具有全域權限。 如需詳細資訊，請參閱[讓管理 Power BI 變得更簡單 (英文)](https://powerbi.microsoft.com/en-us/blog/making-it-easier-to-administer-power-bi/)。
* **特殊權限角色管理員**：具備此角色的使用者可以管理 Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)，以及更新其他使用者的角色指派。
* **SharePoint 服務管理員**︰在有 Microsoft SharePoint Online 服務時，具備此角色的使用者在該服務內會具有全域權限。 如需詳細資訊，請參閱 [關於 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)。
* **服務管理員**：管理服務要求，以及監控服務健全狀況。

  > [!NOTE]
  > 若要指派服務管理員角色給使用者，全域管理員必須先指派管理權限給服務中的該使用者，例如 Exchange Online，再指派服務管理員角色給 Azure 傳統入口網站中的使用者。
  >
  >
* **使用者帳戶管理員**：重設密碼、監視服務健全狀況，以及管理使用者帳戶、使用者群組和服務要求。 部分限制適用於使用者管理管理員的權限。 例如，他們無法刪除全域管理員，或建立其他管理員。 此外，他們無法重設計費、全域和服務管理員的密碼。
* **安全性讀取者**︰能夠以唯讀方式存取「Identity Protection 中心」、Privileged Identity Management、「監視 Office 365 服務健康狀況」及「Office 365 安全性與規範中心」的一些安全性功能。
* **安全性系統管理員**︰具備「安全性讀取者」角色的所有唯讀權限，再加上下列相同服務的一些額外系統管理權限︰「Identity Protection 中心」、Privileged Identity Management、「監視 Office 365 服務健康狀況」及「Office 365 安全性與規範中心」。

## <a name="administrator-permissions"></a>系統管理員權限
### <a name="billing-administrator"></a>計費管理員
| 可以執行 | 無法執行 |
| --- | --- |
| <p>檢視公司與使用者資訊</p><p>建立 Office 支援票證</p><p>執行 Office 產品的計費和購買作業</p> |<p>重設使用者密碼</p><p>建立和管理使用者檢視</p><p>建立、編輯和刪除使用者與群組，以及管理使用者授權</p><p>管理網域</p><p>管理公司資訊</p><p>將系統管理角色委派給其他人</p><p>使用目錄同步作業</p><p>檢視報告</p> |

### <a name="global-administrator"></a>全域管理員
| 可以執行 | 無法執行 |
| --- | --- |
| <p>檢視公司與使用者資訊</p><p>建立 Office 支援票證</p><p>執行 Office 產品的計費和購買作業</p> <p>重設使用者密碼</p><p>建立和管理使用者檢視</p><p>建立、編輯和刪除使用者與群組，以及管理使用者授權</p><p>管理網域</p><p>管理公司資訊</p><p>將系統管理角色委派給其他人</p><p>使用目錄同步作業</p><p>啟用或停用多重要素驗證</p><p>檢視報告</p> |N/A |

### <a name="password-administrator"></a>密碼管理員
| 可以執行 | 無法執行 |
| --- | --- |
| <p>檢視公司與使用者資訊</p><p>建立 Office 支援票證</p><p>重設使用者密碼</p> |<p>執行 Office 產品的計費和購買作業</p><p>建立和管理使用者檢視</p><p>建立、編輯和刪除使用者與群組，以及管理使用者授權</p><p>管理網域</p><p>管理公司資訊</p><p>將系統管理角色委派給其他人</p><p>使用目錄同步作業</p><p>檢視報告</p> |

### <a name="service-administrator"></a>服務管理員
| 可以執行 | 無法執行 |
| --- | --- |
| <p>檢視公司與使用者資訊</p><p>建立 Office 支援票證</p> |<p>重設使用者密碼</p><p>執行 Office 產品的計費和購買作業</p><p>建立和管理使用者檢視</p><p>建立、編輯和刪除使用者與群組，以及管理使用者授權</p><p>管理網域</p><p>管理公司資訊</p><p>將系統管理角色委派給其他人</p><p>使用目錄同步作業</p><p>檢視報告</p> |

### <a name="user-administrator"></a>使用者管理員
| 可以執行 | 無法執行 |
| --- | --- |
| <p>檢視公司與使用者資訊</p><p>建立 Office 支援票證</p><p>重設使用者密碼，但有限制。 他不能重設計費、全域及服務管理員的密碼。</p><p>建立和管理使用者檢視</p><p>建立、編輯和刪除使用者與群組，以及管理使用者授權，但有限制。 他無法刪除全域管理員或建立其他管理員。</p> |<p>執行 Office 產品的計費和購買作業</p><p>管理網域</p><p>管理公司資訊</p><p>將系統管理角色委派給其他人</p><p>使用目錄同步作業</p><p>啟用或停用多重要素驗證</p><p>檢視報告</p> |

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

## <a name="details-about-the-global-administrator-role"></a>全域管理員角色的詳細資料
全域管理員可以存取所有系統管理功能。 註冊 Azure 訂用帳戶的人員預設會獲指派目錄的全域管理員角色。 只有全域管理員才能指派其他系統管理員角色。

## <a name="assign-or-remove-administrator-roles"></a>指派或移除系統管理員角色
1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下 [Active Directory]，然後按一下您組織的目錄名稱。
2. 在 [ **使用者** ] 頁面上，按一下您想要編輯之使用者的顯示名稱。
3. 在 [組織角色] 清單中，選取您想要指派給這位使用者的系統管理員角色，或選取 [使用者] (如果您想要移除現有的系統管理員角色)。
4. 在 [ **替代電子郵件地址** ] 方塊中，輸入電子郵件地址。 此電子郵件地址用於重要通知 (包括密碼自行重設)，因此，不論使用者是否可以存取 Azure，使用者都必須可以存取電子郵件帳戶。
5. 選取 [允許] 或 [封鎖] 以指定是否要允許使用者登入和存取服務。
6. 從 [ **使用位置** ] 下拉式清單中，指定位置。
7. 完成時，請按一下 [ **儲存**]。

## <a name="deprecated-roles"></a>已被取代的角色

以下是不應使用的角色。 它們已被取代，而且未來將從 Azure AD 中移除。

* AdHoc 授權管理員
* 傳送電子郵件給經過驗證的使用者建立者
* 加入裝置
* 裝置管理員
* 裝置使用者
* 加入工作場所裝置

## <a name="next-steps"></a>後續步驟
* 若要深入了解如何變更 Azure 訂用帳戶的系統管理員，請參閱 [如何新增或變更 Azure 系統管理員角色](../billing-add-change-azure-subscription-administrator.md)
* 若要深入了解如何在 Microsoft Azure 中控制資源存取，請參閱 [了解 Azure 中的資源存取](active-directory-understanding-resource-access.md)
* 如需 Azure Active Directory 如何與您 Azure 訂用帳戶產生關聯的詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](active-directory-how-subscriptions-associated-directory.md)
* [管理使用者](active-directory-create-users.md)
* [管理密碼](active-directory-manage-passwords.md)
* [管理群組](active-directory-manage-groups.md)

