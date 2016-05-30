<properties
	pageTitle="Azure Active Directory B2C 預覽︰權杖、工作階段及單一登入組態 | Microsoft Azure"
	description="Azure Active Directory B2C 中的權杖、工作階段及單一登入組態"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C 預覽︰權杖、工作階段及單一登入組態

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

這項功能可讓您以[每個原則為基礎](active-directory-b2c-reference-policies.md)，針對以下項目進行精細控制︰
 
1. Azure Active Directory (Azure AD) B2C 所發出之安全性權杖的存留期。
2. 由 Azure AD B2C 管理之 Web 應用程式工作階段的存留期。
3. B2C 租用戶中跨越多個應用程式和原則的單一登入 (SSO) 行為。

您可以在 B2C 租用戶中使用這項功能，如下所示︰

1. 遵循下列步驟以[瀏覽至 Azure 入口網站上的 B2C 功能刀鋒視窗](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [登入原則]。附註︰這項功能適用於任何原則類型，並不限於**登入原則**。
3. 按一下原則以予以開啟。例如，按一下 [B2C\_1\_SiIn]。
4. 按一下刀鋒視窗頂端的 [編輯]。
5. 按一下 [權杖、工作階段及單一登入組態]。
6. 變更需要的項目。了解後續章節中的可用屬性。
7. 按一下 [確定]。
8. 按一下刀鋒視窗頂端的 [儲存]。

![權杖、工作階段及單一登入組態的螢幕擷取畫面](./media/active-directory-b2c-token-session-sso/token-session-sso.png)

## 權杖存留期組態

Azure AD B2C 支援以 [OAuth 2.0 授權通訊協定](active-directory-b2c-reference-protocols.md)來啟用受保護資源的安全存取。為了實作這項支援，Azure AD B2C 會發出各種[安全性權杖](active-directory-b2c-reference-tokens.md)。以下是可用來管理 Azure AD B2C 所發出之安全性權杖存留期的屬性︰

- **存取和識別碼權杖存留期 (分鐘)**：用來存取受保護資源之 OAuth 2.0 持有人權杖的存留期。Azure AD B2C 目前只能發出識別碼權杖。當我們加入存取權杖的支援時，這個值也會套用至存取權杖。
   - 預設值 = 60 分鐘。
   - 最小值 (含) = 15 分鐘。
   - 最大值 (含) = 1440 分鐘。
- **重新整理權杖存留期 (天)**︰重新整理權杖可用來取得新存取權限或識別碼權杖 (如果您的應用程式已獲得 `offline_access` 範圍的授權，也可以選擇性地取得新重新整理權杖) 的最大期間。
   - 預設值 = 14 天。
   - 最小值 (含) = 1 天。
   - 最大值 (含) = 90 天。
- **重新整理權杖滑動視窗存留期 (天)**︰這段時間結束後，無論應用程式取得之最新重新整理權杖的有效期間為何，使用者都必須重新驗證。唯有將參數設定為 [受限制] 時才能提供。它必須大於或等於 [重新整理權杖存留期 (天)] 值。如果將參數設定為 [無限制]，您便無法提供特定的值。
   - 預設值 = 90 天。
   - 最小值 (含) = 1 天。
   - 最大值 (含) = 365 天。

以下是幾個可以透過這些屬性實現的使用案例︰

- 只要使用者在行動應用程式中持續保持作用狀態，便允許他們無限期地維持應用程式的登入狀態。透過在登入原則中將 [重新整理權杖滑動視窗存留期 (天)] 參數設定為 [無限制]，您可以實現此案例。
- 請設定適當的存取權杖存留期，以滿足業界的安全性和循規規範。

## 工作階段組態

Azure AD B2C 支援以 [OpenID Connect 驗證通訊協定](active-directory-b2c-reference-oidc.md)啟用 Web 應用程式的安全登入。以下是可用來管理 Web 應用程式工作階段的屬性︰

- **Web 應用程式工作階段存留期 (分鐘)**︰在成功通過驗證時，儲存於使用者瀏覽器上的 Azure AD B2C 工作階段 Cookie 存留期。
   - 預設值 = 1440 分鐘。
   - 最小值 (含) = 15 分鐘。
   - 最大值 (含) = 1440 分鐘。
- **Web 應用程式工作階段逾時**︰如果將此參數設定為 [絕對]，使用者必須在 [Web 應用程式工作階段存留期 (分鐘)] 指定的期間結束後重新驗證。如果將此參數設定為 [循環] \(預設設定)，只要使用者在 Web 應用程式中持續保持作用狀態，他們便能維持登入狀態。

以下是幾個可以透過這些屬性實現的使用案例︰

- 請設定適當的 Web 應用程式工作階段存留期，以滿足業界的安全性和循規規範。
- 當使用者與 Web 應用程式之高度安全組件的互動達到設定期間後，請強制他們重新驗證。 

## 單一登入組態

如果您的 B2C 租用戶中有多個應用程式和原則，可以使用**單一登入組態**屬性來管理使用者與它們之間的互動。您可以將屬性配置為以下任一設定︰

- **租用戶**：這是預設設定。此設定可允許 B2C 租用戶中的多個應用程式和原則共用同一個使用者工作階段。例如，一旦使用者登入應用程式 Contoso Shopping 後，他或她就可以在存取另一個應用程式 Contoso Pharmacy 時順暢地登入。
- **應用程式**︰可讓您保留應用程式專用的使用者工作階段，不受其他應用程式所限制。例如，如果您想讓使用者登入 Contoso Pharmacy (使用相同的認證)，即使他或她已登入 Contoso Shopping (同一個 B2C 租用戶上的另一個應用程式)。 
- **原則**︰可讓您保留原則專用的使用者工作階段，不論使用該使用者工作階段的應用程式為何。例如，如果使用者已登入並完成多重要素驗證 (MFA) 步驟，只要與原則繫結的工作階段未過期，他們就可以取得多個應用程式較高安全性之組件的存取權限。
- **已停用**︰強制使用者在每次原則執行時都必須完成整個使用者旅程。例如，這可讓多位使用者登入您的應用程式 (在共用桌面案例中)，即使有一位使用者在整段期間都保持登入狀態，也不受影響。

<!---HONumber=AcomDC_0518_2016-->