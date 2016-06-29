<properties
	pageTitle="SaaS 應用程式的 Azure 條件式存取預覽 | Microsoft Azure"
	description="Azure AD 中的條件式存取可讓您設定每個應用程式的多因素驗證存取規則，且能夠封鎖不在受信任網路上的使用者存取。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2016"
	ms.author="femila"/>

# SaaS 應用程式的 Azure 條件式存取預覽

SaaS 應用程式的 Azure 條件式存取已可公開預覽。預覽版本可讓您設定每個應用程式的多重要素驗證 (MFA) 存取規則，且能夠封鎖不在受信任網路上的使用者存取。

您可以將多因素驗證規則套用至所有已指派給應用程式的使用者，或只套用至指定的安全性群組內的使用者。如果使用者是從組織網路內的 IP 位址存取應用程式，則可從多因素驗證需求中排除這些使用者。購買 Azure Active Directory Premium 授權的客戶可使用這些功能。

## 案例必要條件
* Azure Active Directory Premium 的授權

* 同盟或受管理的 Azure Active Directory 租用戶

* 同盟租用戶需要啟用多重要素驗證。

## 此預覽版本中已知的問題
此預覽版本適用於預先整合的同盟 SaaS 應用程式、使用密碼單一登入的應用程式、已註冊開發和商務營運的應用程式，以及 Azure AD 應用程式 Proxy。其他應用程式仍然啟用中。

## 設定每個應用程式的存取規則

本節描述如何設定每個應用程式的存取規則。

1. 使用 Azure AD 全域管理員身分的帳戶登入 Azure 傳統入口網站。
2. 在左窗格中選取 [Active Directory]。
3. 在 [目錄] 索引標籤中，選取您的目錄。
4. 選取 [應用程式] 索引標籤。
5. 選取要設定規則的應用程式。
6. 選取 [設定] 索引標籤。
7. 向下捲動至存取規則區段。選取所需的存取規則。
8. 指定將套用此規則的使用者。
9. 將 [啟用] 設定為 [開啟] 以啟用原則。

##了解存取規則

本節提供 Azure 條件式應用程式存取預覽中支援的存取規則的詳細描述。

### 指定套用存取規則的使用者

根據預設，規則會套用至所有可存取應用程式的使用者。不過，您也可以將原則限制為指定安全性群組的成員使用者。[加入群組] 按鈕用來從群組選取對話方塊中，選取將套用存取規則的一或多個群組。此對話方塊也可用來移除選取的群組。當選擇將規則套用至「群組」時，只會對屬於其中一個指定安全性群組的使用者強制執行存取規則。

選取 [除了] 選項並指定一個或多個群組，可以明確地從原則中排除安全性群組。屬於 [除了] 清單中的群組的使用者，即使是屬於套用存取規則的群組，也不受限於多因素驗證需求。以下顯示的存取規則會要求「管理員」群組中的所有使用者在存取應用程式時使用多因素驗證。

![使用 MFA 設定條件式存取規則](./media/active-directory-conditional-access/conditionalaccess-saas-apps.png)

## 條件式存取規則和 MFA
如果已使用每個使用者的多重要素驗證功能來設定使用者，則使用者的這項設定將與應用程式的多重要素驗證規則相結合。這表示已設定每個使用者多因素驗證的使用者，即使已從應用程式多因素驗證規則中免除，也都必須執行多因素驗證。深入了解多因素驗證和每個使用者設定。

### 存取規則選項
目前的預覽版本支援下列選項：

* **需要多重要素驗證**︰使用此選項時，套用存取規則的使用者必須先完成多重要素驗證，才能存取套用規則的應用程式。

* **不在工作時需要多重要素驗證**︰使用此選項時，來自受信任 IP 位址的使用者不需要執行多重要素驗證。受信任的 IP 位址範圍可以在 [多重要素驗證設定] 頁面上設定，或藉由在目錄設定索引標籤上設定公用 IP 位址範圍來設定。

* **不工作時封鎖存取**：使用此選項時，將封鎖不是來自受信任 IP 位址的使用者。可以在 [Multi-Factor Authentication 設定] 頁面上設定受信任的 IP 位址範圍。

### 設定規則狀態
存取規則狀態可讓您開啟或關閉規則。當存取規則關閉時，將不會強制執行多重要素驗證需求。

### 存取規則評估

當使用者存取使用 OAuth 2.0、OpenID Connect、SAML 或 WS-同盟的同盟應用程式時，就會評估存取規則。此外，當 OAuth 2.0 和 OpenID Connect 以及重新整理權杖用來取得存取權杖時，也會評估存取規則。如果使用重新整理權杖時原則評估失敗，則會傳回錯誤 invalid\_grant，這表示使用者必須向用戶端重新驗證。設定同盟服務以提供多因素驗證

對於同盟的租用戶，Multi-Factor Authentication (MFA) 可能由 Azure Active Directory 或內部部署 AD FS 伺服器執行。

根據預設，MFA 會發生在 Azure Active Directory 所裝載的頁面上。若要設定內部部署 MFA，必須使用 Windows PowerShell 的 Azure AD 模組，在 Azure Active Directory 中將 –SupportsMFA 屬性設定為 **true**。

下列範例示範如何在 consoso.com 租用戶上使用 [Set-MsolDomainFederationSettings Cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) 來啟用內部部署 MFA︰

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

除了設定這個旗標，同盟租用戶 AD FS 執行個體必須設為執行多因素驗證。依照指示在內部部署 Microsoft Azure Multi-Factor Authentication。

## 相關文章

- [保護對 Office 365 及其他連接至 Azure Active Directory 之應用程式的存取](active-directory-conditional-access.md)
- [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)

<!---HONumber=AcomDC_0615_2016-->