<properties
	pageTitle="如何為內部部署應用程式提供安全的遠端存取"
	description="涵蓋如何使用 Azure AD 應用程式 Proxy 為您的內部部署應用程式提供安全的遠端存取。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/12/2016"
	ms.author="kgremban"/>

# 如何為內部部署應用程式提供安全的遠端存取

> [AZURE.NOTE] 應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

您想要為擁有各種裝置 (受管理及未受管理；平板電腦、智慧型手機和膝上型電腦) 的遠端使用者提供存取。但是，為大量的資源提供安全的存取可能會很複雜。近年來，反向 Proxy 是提供安全的遠端存取的一種常見方式，但它們必須在難以保護，且難以提供高度可用性的防火牆後面。

## 雲端的安全遠端存取
在現代的雲端環境中，我們用 Azure Active Directory (AD) 中的應用程式 Proxy，將遠端存取帶到下一個層級。應用程式 Proxy 是 Azure AD 當做服務提供的一個功能，也就是說，部署與使用都非常容易。它整合了 Office 365 所使用的相同身分識別平台，也就是 Azure AD。

## 什麼是 Azure Active Directory 應用程式 Proxy？
應用程式 Proxy 針對 Web 應用程式託管的內部部署，提供單一登入 (SSO) 及安全的遠端存取，例如 SharePoint 網站和 Outlook Web Access。您的內部部署 Web 應用程式的存取方式現在與 Azure Active Directory 中的 SaaS 應用程式存取方式相同，不需要 VPN，也不需要變更網路基礎結構。應用程式 Proxy 可讓您發佈應用程式，而員工可以從家裡使用自己的裝置登入您的應用程式，並透過這個雲端 Proxy 進行驗證。

## 運作方式

應用程式 Proxy 使用三個基本步驟。首先，連接器是部署在內部部署網路上。接著，此連接器會連線到雲端服務。最後，連接器和雲端服務會將使用者流量路由傳送至應用程式。

 ![AzureAD 應用程式 Proxy 圖表](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

1. 使用者會透過應用程式 Proxy 存取應用程式，然後被導向 Azure AD 登入頁面進行驗證。
2. 成功登入之後，會產生權杖並傳送給使用者。
3. 使用者會將權杖傳送至應用程式 Proxy，而該應用程式 Proxy 會擷取權杖的使用者主體名稱 (UPN) 和安全性主體名稱 (SPN)，然後將要求導向至連接器。
4. 連接器會代表模擬使用者要求可用於內部 (Windows) 驗證的 Kerberos 票證。這就是所謂的 Kerberos 限制委派。
5. Kerberos 票證擷取自 Active Directory。
6. 票證會傳送到應用程式伺服器和加以驗證。
7. 回應會透過應用程式 Proxy 傳送給使用者。

### 啟用存取
應用程式 Proxy 的運作方式是透過在網路內部安裝一個稱為連接器的精簡型 Windows Server 服務。連接器不一定需要開放任何輸入連接埠，而且您不需要在 DMZ 中放置任何內容。如果您的應用程式有大量的流量，您可以新增更多連接器，而且該服務將會負責負載平衡。連接器是無狀態的，而且必要時，會從雲端提取所有內容。

當使用者從遠端存取任何裝置上的應用程式時，他會經過 Azure Active Directory 的驗證，並取得應用程式的存取權。

### 單一登入
Azure AD 應用程式 Proxy 針對使用整合式 Windows 驗證 (IWA)，或宣告感知應用程式的應用程式提供單一登入功能。如果您的應用程式使用 IWA，應用程式 Proxy 會模擬使用 Kerberos 限制委派的使用者來提供單一登入 (SSO)。如果您有信任 Azure Active Directory 的宣告感知應用程式，則可以使用 SSO，因為使用者已經由 Azure AD 驗證。

## 如何開始使用
請確定您有 Azure AD 基本或進階的訂用帳戶，以及您是全域管理員的 Azure AD 目錄。您也需要 Azure AD 基本或進階的授權，目錄系統管理員和使用者才能存取應用程式。如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

### 開始為內部部署應用程式啟用遠端存取
設定應用程式 Proxy 是以兩個步驟完成：

1. [啟用應用程式 Proxy 並設定連接器](active-directory-application-proxy-enable.md)  
2. [發佈應用程式](active-directory-application-proxy-publish.md)：使用快速且簡單的精靈發佈內部部署應用程式並提供遠端存取。

## 後續步驟
應用程式 Proxy 還有其他更多用途：

- [使用您自己的網域名稱發行應用程式](active-directory-application-proxy-custom-domains.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [使用宣告感知應用程式](active-directory-application-proxy-claims-aware-apps.md)
- [啟用條件式存取](active-directory-application-proxy-conditional-access.md)


### 深入了解應用程式 Proxy
- [看看我們的線上說明](active-directory-application-proxy-enable.md)
- [查閱應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
- [觀看我們在 Channel 9 上的影片！](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 其他資源
- [Article index for application management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
- [以組織身分註冊 Azure](sign-up-organization.md)
- [Azure 身分識別](fundamentals-identity.md)

<!---HONumber=AcomDC_0518_2016-->