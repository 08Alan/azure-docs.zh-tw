---
title: "Azure AD UserPrincipalName 填入"
description: "下列文件說明 UserPrincipalName 屬性的填入方式。"
author: billmath
ms.author: billmath
ms.date: 02/02/2018
ms.topic: article
ms.workload: identity
ms.service: active-Directory
manager: mtillman
ms.openlocfilehash: 1fca41a8498cec506298748acd3511a5c5802d26
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/03/2018
---
# <a name="azure-ad-userprincipalname-population"></a>Azure AD UserPrincipalName 填入

本文說明在 Azure Active Directory (Azure AD) 中填入 UserPrincipalName 屬性的方式。
UserPrincipalName 屬性值是使用者帳戶的 Azure AD 使用者名稱。

## <a name="upn-terminology"></a>UPN 術語
本文使用下列術語：

|詞彙|說明|
|-----|-----|
|初始網域|Azure AD 租用戶中的預設網域 (onmicrosoft.com)。 例如，contoso.onmicrosoft.com。|
|Microsoft 線上電子郵件路由位址 (MOERA)|Azure AD 會將 Azure AD MailNickName 屬性的 MOERA 和 Azure AD 初始網域計算為 &lt;MailNickName&gt;&#64;&lt;初始網域&gt;。|
|內部部署 mailNickName 屬性|Active Directory 的屬性，其值代表 Exchange 組織使用者的別名。|
|內部部署 mail 屬性|Active Directory 的屬性，其值代表使用者的電子郵件地址|
|主要 SMTP 位址|Exchange 收件者物件的主要電子郵件地址。 例如： SMTP:user@contoso.com。|
|替代登入識別碼|非 UserPrincipalName 的內部部署屬性 (例如 mail 屬性)，可用於登入。|

## <a name="what-is-userprincipalname"></a>何謂 UserPrincipalName？
UserPrincipalName 是依據網際網路標準 [RFC 822](http://www.ietf.org/rfc/rfc0822.txt) 所定義的一個屬性，可作為使用者的網際網路樣式登入名稱。 

### <a name="upn-format"></a>UPN 格式
UPN 是由 UPN 前置詞 (使用者帳戶名稱) 和 UPN 尾碼 (DNS 網域名稱) 所組成。 前置詞會使用 "@" 符號與尾碼連結。 例如，"someone@example.com"。 在目錄樹系的所有安全性主體物件之間，UPN 必須是唯一的。 

## <a name="upn-in-azure-ad"></a>Azure AD 中的 UPN 
Azure AD 會使用 UPN 來允許使用者登入。  使用者所能使用的 UPN 取決於網域是否已驗證。  如果網域已驗證，則系統會允許具有該尾碼的使用者登入 Azure AD。  

Azure AD Connect 會同步處理屬性。  在安裝期間，您可以檢視已驗證和尚未驗證的網域。

   ![未驗證的網域](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png) 

## <a name="alternate-login-id"></a>替代登入識別碼
某些環境由於具有公司原則或內部部署企業營運應用程式相依性，所以使用者可能只會知道其電子郵件地址，而不知道其 UPN。

替代登入識別碼可讓您設定登入體驗，讓使用者可以透過其 UPN 以外的屬性 (例如 mail) 來進行登入。

若要在 Azure AD 啟用替代登入識別碼，且您使用的是 Azure AD Connect，則不需要進行額外的設定步驟。 您可以直接從精靈設定替代識別碼。 請在 [同步處理] 區段底下查看使用者的 Azure AD 登入設定。在 [使用者主體名稱] 下拉式清單底下，選取替代登入識別碼的屬性。

![未驗證的網域](./media/active-directory-aadconnect-userprincipalname/altloginid.png)  

如需詳細資訊，請參閱[設定替代登入識別碼](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)和 [Azure AD 登入設定](active-directory-aadconnect-get-started-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>未驗證的 UPN 尾碼
如果未向 Azure AD 租用戶驗證內部部署 UserPrincipalName 屬性/替代登入識別碼尾碼，系統會將 Azure AD UserPrincipalName 屬性值設定為 MOERA。 Azure AD 會將 Azure AD MailNickName 屬性的 MOERA 和 Azure AD 初始網域計算為 &lt;MailNickName&gt;&#64;&lt;初始網域&gt;。

## <a name="verified-upn-suffix"></a>已驗證的 UPN 尾碼
如果已向 Azure AD 租用戶驗證內部部署 UserPrincipalName 屬性/替代登入識別碼尾碼，Azure AD UserPrincipalName 屬性值將會和內部部署 UserPrincipalName 屬性/替代登入識別碼值一樣。

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Azure AD MailNickName 屬性值計算
因為 Azure AD UserPrincipalName 屬性值有可能設定為 MOERA，所以請務必要了解 Azure AD MailNickName 屬性值 (也就是 MOERA 前置詞) 的計算方式。

使用者物件在第一次同步處理至 Azure AD 租用戶時，Azure AD 會以給定的順序檢查下列項目，然後將 MailNickName 屬性值設定為現有的第一個項目：

- 內部部署 mailNickName 屬性
- 內部部署 mail 屬性的前置詞
- 主要 SMTP 位址的前置詞
- 內部部署 UserPrincipalName 屬性/替代登入識別碼的前置詞

當使用者物件更新同步處理至 Azure AD 租用戶時，Azure AD 只會在有內部部署 mailNickName 屬性值更新的情況下才會更新 MailNickName 屬性值。

>[!IMPORTANT]
>只有在內部部署 UserPrincipalName 屬性/替代登入識別碼值的更新同步處理至 Azure AD 租用戶時，Azure AD 才會重新計算 UserPrincipalName 屬性值。 
>
>Azure AD 每次重新計算 UserPrincipalName 屬性時，也會重新計算 MOERA。 

## <a name="upn-scenarios"></a>UPN 案例
以下是在給定案例的情況下，UPN 會如何計算的案例範例。

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>案例 1：未驗證的 UPN 尾碼 – 初始同步處理

內部部署使用者物件：
- mailNickName：&lt;未設定&gt;
- mail：us1@contoso.com
- proxyAddresses：{SMTP:us2@contoso.com}
- userPrincipalName：us3@contoso.com

第一次將使用者物件同步處理至 Azure AD 租用戶
- 將 Azure AD MailNickName 屬性設定為內部部署 mail 屬性前置詞。
- 將 MOERA 設定為 &lt;MailNickName&gt;&#64;&lt;初始網域&gt;。
- 將 Azure AD UserPrincipalName 屬性設定為 MOERA。

Azure AD 租用戶使用者物件：
- MailNickName：us1           
- UserPrincipalName：us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>案例 2：未驗證的 UPN 尾碼 – 設定內部部署 mailNickName 屬性

內部部署使用者物件：
- mailNickName：us4
- mail：us1@contoso.com
- proxyAddresses：{SMTP:us2@contoso.com}
- userPrincipalName：us3@contoso.com

將內部部署 mailNickName 屬性的更新同步處理至 Azure AD 租用戶
- 將 Azure AD MailNickName 屬性更新為內部部署 mailNickName 屬性。
- 由於內部部署 userPrincipalName 屬性沒有更新，所以 Azure AD UserPrincipalName 屬性不會變更。

Azure AD 租用戶使用者物件：
- MailNickName：us4
- UserPrincipalName：us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>案例 3：未驗證的 UPN 尾碼 – 更新內部部署 userPrincipalName 屬性

內部部署使用者物件：
- mailNickName：us4
- mail：us1@contoso.com
- proxyAddresses：{SMTP:us2@contoso.com}
- userPrincipalName：us5@contoso.com

將內部部署 userPrincipalName 屬性的更新同步處理至 Azure AD 租用戶
- 內部部署 userPrincipalName 屬性的更新會觸發 MOERA 和 Azure AD UserPrincipalName 屬性的重新計算。
- 將 MOERA 設定為 &lt;MailNickName&gt;&#64;&lt;初始網域&gt;。
- 將 Azure AD UserPrincipalName 屬性設定為 MOERA。

Azure AD 租用戶使用者物件：
- MailNickName：us4
- UserPrincipalName：us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-on-premises-mail-attribute-and-primary-smtp-address"></a>案例 4：未驗證的 UPN 尾碼 – 更新內部部署 mail 屬性和主要 SMTP 位址

內部部署使用者物件：
- mailNickName：us4
- mail：us6@contoso.com
- proxyAddresses：{SMTP:us7@contoso.com}
- userPrincipalName：us5@contoso.com

將內部部署 mail 屬性和主要 SMTP 位址的更新同步處理至 Azure AD 租用戶
- 使用者物件進行過首次同步處理後，內部部署 mail 屬性和主要 SMTP 位址的更新就不會影響 Azure AD MailNickName 和 UserPrincipalName 屬性。

Azure AD 租用戶使用者物件：
- MailNickName：us4
- UserPrincipalName：us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>案例 5：已驗證的 UPN 尾碼 – 更新內部部署 userPrincipalName 屬性尾碼

內部部署使用者物件：
- mailNickName：us4
- mail：us6@contoso.com
- proxyAddresses：{SMTP:us7@contoso.com}
- serPrincipalName：us5@verified.contoso.com

將內部部署 userPrincipalName 屬性的更新同步處理至 Azure AD 租用戶
- 內部部署 userPrincipalName 屬性的更新會觸發 Azure AD UserPrincipalName 屬性的重新計算。
- 由於已向 Azure AD 租用戶驗證 UPN 尾碼，所以系統會將 Azure AD UserPrincipalName 屬性設定為內部部署 userPrincipalName 屬性。

Azure AD 租用戶使用者物件：
- MailNickName：us4     
- UserPrincipalName：us5@verified.contoso.com

## <a name="next-steps"></a>後續步驟
- [整合您的內部部署目錄與 Azure Active Directory](active-directory-aadconnect.md)
- [自訂 Azure AD Connect 安裝](active-directory-aadconnect-get-started-custom.md)
