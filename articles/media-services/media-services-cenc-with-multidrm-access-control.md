---
title: "具有多重 DRM 及存取控制的 CENC：Azure 與 Azure 媒體服務的參考設計和實作 | Microsoft Docs"
description: "了解如何授權 Microsoft® Smooth Streaming Client Porting Kit。"
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.assetid: 7814739b-cea9-4b9b-8370-538702e5c615
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: willzhan;kilroyh;yanmf;juliako
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: 527d011476b046add0842b1c7275fc6507be31d4
ms.contentlocale: zh-tw
ms.lasthandoff: 07/20/2017

---
# <a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>具有多重 DRM 及存取控制的 CENC：Azure 與 Azure 媒體服務的參考設計和實作
 
## <a name="introduction"></a>簡介
針對 OTT 或線上串流解決方案設計和建置 DRM 子系統是相當複雜的工作已眾所皆知。 運算子/線上視訊提供者將這個部分外包給專門的 DRM 服務提供者是常見的做法。 這份文件的目標是呈現 OTT 或線上串流解決方案中端對端 DRM 子系統的參考設計和實作。

這份文件的目標讀者是使用 OTT 或線上串流/多重螢幕解決方案的 DRM 子系統的工程師，或對於 DRM 子系統有興趣的任何讀者。 假設讀者都熟悉市場上至少一個 DRM 技術，例如 PlayReady、Widevine、FairPlay 或 Adobe Access。

根據 DRM，我們也包含 CENC (Common Encryption) 與多重 DRM。 線上串流和 OTT 業界中的主要趨勢是在各種用戶端平台上使用 CENC 與多重原生 DRM，是從先前對於各種用戶端平台使用單一 DRM 及其用戶端 SDK 的趨勢變化而來。 使用 CENC 與多重原生 DRM 時，PlayReady 和 Widevine 會依照 [Common Encryption (ISO/IEC 23001-7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) 規格加密。

使用 CENC 與多重 DRM 的優點如下：

1. 降低加密成本，因為對於不同平台使用其原生 DRM 進行單一加密處理；
2. 降低管理加密資產的成本，因為只需要一份加密資產；
3. 排除 DRM 用戶端授權成本，因為原生 DRM 用戶端在其原生平台上通常是免費的。

Microsoft 已經成為 DASH 和 CENC 與其他一些主要業界播放器的積極推動者。 Microsoft Azure 媒體服務已提供 DASH 和 CENC 的支援。 如需最新的通知，請參閱 Mingfei 的部落格：[Announcing Google Widevine license delivery services in Azure Media Services (宣佈在 Azure 媒體服務中推出 Google Widevine 授權傳遞服務)](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)，和 [Azure Media Services adds Google Widevine packaging for delivering multi-DRM stream (Azure 媒體服務新增 Google Widevine 封裝來傳遞多重 DRM 串流)](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)。  

### <a name="overview-of-this-article"></a>本文概觀：
本文的目標包含下列項目：

1. 提供使用 CENC 與多重 DRM 的 DRM 子系統的參考設計；
2. 提供 Microsoft Azure/Azure 媒體服務平台上的參考實作；
3. 討論某些設計和實作主題。

在本文章中，「多重 DRM」涵蓋下列主題：

1. Microsoft PlayReady (英文)
2. Google Widevine
3. Apple FairPlay 

下表摘要說明每個 DRM 支援的原生平台/原生應用程式和瀏覽器。

| **用戶端平台** | **原生 DRM 支援** | **瀏覽器/應用程式** | **串流格式** |
| --- | --- | --- | --- |
| **智慧型電視、運算子 STB、OTT STB** |主要為 PlayReady，及/或 Widevine，及/或其他的 DRM |Linux、Opera、WebKit 及其他瀏覽器/應用程式 |各種格式 |
| **Windows 10 裝置 (Windows 電腦、Windows 平板電腦、Windows Phone、Xbox)** |PlayReady |MS Edge/IE11/EME<br/><br/><br/>UWP |DASH (HLS 並不支援 PlayReady)<br/><br/>DASH、Smooth Streaming (HLS 並不支援 PlayReady) |
| **Android 裝置 (電話、平板電腦、電視)** |Widevine |Chrome/EME |DASH,HLS |
| **iOS (iPhone、iPad)、OS X 用戶端和 Apple 電視** |FairPlay |Safari 8+/EME |HLS |


就目前每種 DRM 的部署狀態而言，服務通常會想要實作 2 或 3 個 DRM，以確保您能以最佳方式來處理所有類型的端點。

您必須在服務邏輯複雜度和用戶端複雜度之間做出取捨，以便在各種不同的用戶端上讓使用者經驗能達到某種等級。

當您下決定時，請記住下列這幾點：

* PlayReady 原生實作在每種 Windows 裝置及部分 Android 裝置中，且可透過軟體 SDK 在幾乎所有平台上實作
* Widevine 原生實作在每種 Android 裝置、Chrome 及部分其他的裝置中
* FairPlay 只使用在 iOS 和 Mac OS 用戶端中，或是透過 iTunes 來提供。

因此典型的多重 DRM 會是：

* 選項 1：PlayReady 及 Widevine
* 選項 2：PlayReady、Widevine 和 FairPlay

## <a name="a-reference-design"></a>參考設計
在本節中，我們將探討與用來實作它的技術無關的參考設計。

DRM 子系統可能包含下列元件：

1. 金鑰管理
2. DRM 封裝
3. DRM 授權傳遞
4. 權利檢查
5. 驗證/授權
6. 播放器
7. 原始/CDN

下圖說明 DRM 子系統中元件之間的高階互動。

![DRM 子系統與 CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

在設計中有三個基本「層級」:

1. 後台系統層級 (黑色)，不會對外部公開；
2. DMZ 層級 (藍色)，包含所有公用面向端點；
3. 公用網際網路層級 (淺藍色)，包含具有跨公用網際網路流量的 CDN 和播放器。

應該有用來控制 DRM 保護的內容管理工具，無論是靜態或動態加密。 DRM 加密的輸入應該包括：

1. MBR 視訊內容；
2. 內容金鑰；
3. 授權取得 URL。

在播放期間，高階流程是：

1. 使用者已通過驗證；
2. 已為使用者建立授權權杖；
3. DRM 保護內容 (資訊清單) 已下載至播放器；
4. 播放器將授權取得要求以及金鑰識別碼與授權權杖提交至授權伺服器。

在進行至下一個主題之前，還有一些與金鑰管理設計相關的事項。

| **ContentKey–to-Asset** | **案例** |
| --- | --- |
| 1 對 1 |最簡單的案例。 它提供最佳的控制。 但是，通常會產生最高的授權傳遞成本。 每個受保護的資產需要至少一個授權要求。 |
| 1 對多 |您可以對多個資產使用相同的內容金鑰。 例如，對於如內容類型或內容類型子集 (或 Movie Gene) 的邏輯群組中的所有資產，您可以使用單一內容金鑰。 |
| 多對 1 |每個資產需要有多個內容金鑰。 <br/><br/>舉例來說，如果您需要針對 MPEG-DASH 套用具有多重 DRM 的 動態 CENC 保護，且針對 HLS 套用動態 AES-128 加密，您需要兩個不同的內容金鑰，兩者分別有自己的 ContentKeyType。 (針對用於動態 CENC 保護的內容金鑰，應使用 ContentKeyType.CommonEncryption，而用於動態 AES 128 加密的內容金鑰，應使用 ContentKeyType.EnvelopeEncryption。)<br/><br/>另一個範例中，理論上，在 DASH 內容的 CENC 保護中，可以使用一個內容金鑰來保護視訊串流，並使用其他內容金鑰來保護音訊串流。 |
| 多對多 |結合上述兩種案例：對於相同資料「群組」中的每個多重資產，使用一組內容金鑰。 |

另一個需要考慮的重要因素是持續性和非持續性授權的使用。

這些考量為何重要？

如果您針對授權傳遞使用公用雲端，它們對於授權傳遞成本有直接影響。 請考慮下列兩個不同的設計案例說明：

1. 每月訂用帳戶：使用持續性授權，以及 1 對多的內容金鑰對資產對應。 例如 對於所有兒童影片，我們會使用單一內容金鑰進行加密。 在此案例中：

    所有兒童影片/裝置的授權數總計 = 1
2. 每月訂用帳戶：使用非持續性授權，以及內容金鑰和資產之間的 1 對 1 對應。 在此案例中：

    所有兒童影片/裝置的授權數總計 = [觀賞影片數] x [工作階段數]

您可以輕易地發現，如果授權傳遞服務是由如 Azure 媒體服務的公用雲端提供，則兩個不同的設計會導致大不相同的授權要求模式，進而造成授權傳遞成本的不同。

## <a name="mapping-design-to-technology-for-implementation"></a>將設計對應至技術以進行實作
接下來，我們會將我們的一般設計對應至 Microsoft Azure/Azure 媒體服務平台上的技術，方法是指定要用於每個建置組塊的技術。

下表顯示對應：

| **建置組塊** | **Technology** |
| --- | --- |
| **播放器** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **身分識別提供者 (IDP)** |Azure Active Directory |
| **安全權杖服務 (STS)** |Azure Active Directory |
| **DRM 保護工作流程** |Azure 媒體服務動態保護 |
| **DRM 授權傳遞** |1.Azure 媒體服務授權傳遞 (PlayReady、Widevine、FairPlay) 或 <br/>2.Axinom 授權伺服器， <br/>3.自訂 PlayReady 授權伺服器 |
| **原始** |Azure 媒體服務串流端點 |
| **金鑰管理** |不需要參考實作 |
| **內容管理** |C# 主控台應用程式 |

換句話說，身分識別提供者 (IDP) 和安全權杖服務 (STS) 都將是 Azure AD。 對於播放器，我們將會使用 [Azure 媒體播放器 API](http://amp.azure.net/libs/amp/latest/docs/)。 Azure 媒體服務和 Azure Media Player 都支援具有多重 DRM 的 DASH 和 CENC。

下圖顯示上述技術對應的整體結構與流程。

![AMS 上的 CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

為了設定動態 CENC 加密，內容管理工具會使用下列輸入：

1. 開啟內容；
2. 來自金鑰產生/管理的內容金鑰；
3. 授權取得 URL；
4. Azure AD 中的資訊清單。

內容管理工具的輸出會是：

1. ContentKeyAuthorizationPolicy，包含授權傳遞如何驗證 JWT 權杖和 DRM 授權規格的規格；
2. AssetDeliveryPolicy，包含串流格式、DRM 保護和授權取得 URL 的規格。

在執行階段，流程如下所示：

1. 在使用者驗證時，會產生 JWT 權杖；
2. JWT 權杖中包含的其中一個宣告是「群組」宣告，包含「EntitledUserGroup」的群組物件識別碼。 這個宣告會用於傳遞「權利檢查」。
3. 播放器會下載 CENC 保護內容的用戶端資訊清單，並且「看到」下列項目：

   1. 金鑰識別碼，
   2. 內容是受 CENC 保護，
   3. 授權取得 URL。
4. 播放器會根據支援的瀏覽器/DRM 進行授權取得要求。 在授權取得要求中，也會提交金鑰識別碼和 JWT 權杖。 授權傳遞服務會驗證 JWT 權杖，在發行所需的授權之前包含宣告。

## <a name="implementation"></a>實作
### <a name="implementation-procedures"></a>實作程序
實作將包含下列步驟：

1. 準備測試資產：編碼/封裝測試視訊為 Azure 媒體服務中的多位元速率分散 MP4。 這項資產不受 DRM 保護。 DRM 保護稍後會由動態保護完成。
2. 建立金鑰識別碼和內容金鑰 (選擇性地從金鑰種子)。 對於我們的目的，不需要金鑰管理系統，因為我們只會針對一些測試資產處理一組金鑰識別碼和內容金鑰；
3. 使用 AMS API 來針對測試資產設定多重 DRM 授權傳遞服務。 如果您使用貴公司或貴公司的廠商的自訂授權伺服器，而不是 Azure 媒體服務中的授權服務，您可以略過此步驟，並且在設定授權傳遞的步驟中指定授權取得 URL。 需要 AMS API 以指定一些詳細組態，例如不同 DRM 授權服務的授權原則限制、授權回應範本等等。目前，Azure 入口網站尚未提供此組態所需的 UI。 您可以在 Julia Kornich 的文件中尋找 API 層級資訊和範例程式碼： [使用 PlayReady 和/或 Widevine Dynamic Common Encryption](media-services-protect-with-drm.md)。
4. 使用 AMS API 來針對測試資產設定資產傳遞原則。 您可以在 Julia Kornich 的文件中尋找 API 層級資訊和範例程式碼： [使用 PlayReady 和/或 Widevine Dynamic Common Encryption](media-services-protect-with-drm.md)。
5. 在 Azure 中建立和設定 Azure Active Directory 租用戶；
6. 在 Azure Active Directory 租用戶中建立幾個使用者帳戶和群組：您應該至少建立「EntitledUser」群組，並將使用者新增至此群組。 此群組中的使用者會通過授權取得的權利檢查，而不在此群組中的使用者將無法通過驗證檢查，且無法取得任何授權。 成為此「EntitledUser」群組的成員，是由 Azure AD 發出的 JWT 權杖中的必要「群組」宣告。 這個宣告需求應該在設定多重 DRM 授權傳遞服務步驟中指定。
7. 建立 ASP.NET MVC 應用程式，它將會裝載視訊播放器。 此 ASP.NET 應用程式會根據 Azure Active Directory 租用戶受到使用者驗證的保護。 使用者驗證之後，適當的宣告會包含在取得的存取權杖中。 建議在此步驟使用 OpenID Connect API。 您將需要安裝下列 NuGet 封裝：
   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
8. 使用 [Azure 媒體播放器 API](http://amp.azure.net/libs/amp/latest/docs/)來建立播放器。 [Azure 媒體播放器的 ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) 可讓您指定要在不同的 DRM 平台上使用哪個 DRM 技術。
9. 測試矩陣：

| **DRM** | **[瀏覽器]** | **有權限的使用者的結果** | **無權限的使用者的結果** |
| --- | --- | --- | --- |
| **PlayReady** |MS Edge 或 Windows 10 上的 IE11 |成功 |不合格 |
| **Widevine** |Windows 10 上的 Chrome |成功 |不合格 |
| **FairPlay** |TBD | | |

Azure 媒體服務團隊的 George Trifonov 所撰寫的部落格提供針對 ASP.NET MVC 播放器應用程式來設定 Azure Active Directory 的詳細步驟： [Integrate Azure Media Services OWIN MVC based app with Azure Active Directory and restrict content key delivery based on JWT claims (整合 Azure 媒體服務 OWIN MVC 型應用程式與 Azure Active Directory，並根據 JWT 宣告來限制內容金鑰傳遞)](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)。

George 也撰寫了一篇相關的部落格文章： [JWT token Authentication in Azure Media Services and Dynamic Encryption (Azure 媒體服務和動態加密中的 JWT 權杖驗證)](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)。 以下是他 [整合 Azure AD 與 Azure 媒體服務金鑰傳遞的範例](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/)。

如需 Azure Active Directory 的詳細資訊：

* 您可以在 [Azure Active Directory 開發人員指南](../active-directory/active-directory-developers-guide.md)中找到開發人員的資訊。
* 您可以在 [管理 Azure AD 目錄](../active-directory/active-directory-administer.md)中找到系統管理員的資訊。

### <a name="some-gotchas-in-implementation"></a>實作中的一些錯誤
實作中有一些「錯誤」。 希望下列「錯誤」清單可以在您遇到問題時協助您疑難排解。

1. **簽發者**的 URL 結尾應該是 **"/"**。  

    **受眾**應該是播放器應用程式用戶端識別碼，且您也應該在簽發者 URL 的結尾新增 **"/"**。

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />

    在 [JWT 解碼器](http://jwt.calebb.net/)中，您應該會在 JWT 權杖裡看到 **aud** 和 **iss**，如下所示：

    ![第 1 個錯誤](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)
2. 將權限新增至應用程式的 AAD 中 (在應用程式的 [設定] 索引標籤)。 對於每個應用程式這是必要的 (本機和已部署版本)。

    ![第 2 個錯誤](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)
3. 使用正確的發行者設定動態 CENC 保護：

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>

    下列項目無法運作：

        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />

    GUID 是 AAD 租用戶識別碼。 GUID 可以在 Azure 入口網站中的端點快顯視窗中找到。
4. 授與群組成員資格宣告權限。 確定在 AAD 應用程式資訊清單檔中，我們具有下列項目

    "groupMembershipClaims": "All"，(預設值是 null)
5. 建立限制需求時，設定適當的 TokenType。

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    由於新增除了 SWT (ACS) 以外的 JWT (AAD) 支援，預設 TokenType 是 TokenType.JWT。 如果您使用 SWT/ACS，您必須設定為 TokenType.SWT。

## <a name="additional-topics-for-implementation"></a>Additional Topics for Implementation (其他關於實作的主題)
接下來，我們將討論設計和實作中的其他主題。

### <a name="http-or-https"></a>HTTP 或 HTTPS？
我們建置的 ASP.NET MVC 播放器應用程式必須支援下列功能：

1. 透過 Azure AD (需要在 HTTPS 底下) 的使用者驗證；
2. 用戶端與 Azure AD (需要在 HTTPS 底下) 之間的 JWT 權杖交換；
3. 用戶端 (需要在 HTTPS 底下) 的 DRM 授權取得，如果授權傳遞是由 Azure 媒體服務提供。 當然，PlayReady 產品套件不會針對授權傳遞來授權 HTTPS。 如果您的 PlayReady 授權伺服器位於 Azure 媒體服務以外的地方，則可以使用 HTTP 或 HTTPS。

因此，ASP.NET 播放器應用程式會使用 HTTPS 做為最佳作法。 這表示 Azure Media Player 會在 HTTPS 底下的頁面上。 不過，對於串流我們比較喜歡 HTTP，因此我們必須考慮混合內容的問題。

1. 瀏覽器不允許混合的內容。 但是如 Silverlight 的外掛程式和適用於 Smooth 和 DASH 的 OSMF 外掛程式允許。 混合的內容有安全性考量 - 這是因為能插入惡意 JS 的威脅，它會造成客戶資料處於風險之中。  瀏覽器預設會封鎖這個內容，目前唯一的解決之道是在伺服器端 (來源) 允許所有網域 (不論 https 或 http)。 這可能也不是個好主意。
2. 我們應該避免混合的內容：都使用 HTTP 或都使用 HTTPS。 播放混合的內容時，silverlightSS 技術需要清除混合內容警告。 flashSS 技術會處理混合的內容，而沒有混合內容警告。
3. 如果您的串流端點是在 2014 年 8 月之前建立，它將不會支援 HTTPS。 在此情況下，請針對 HTTPS 建立並使用新的串流端點。

在參考實作中，對於 DRM 保護內容，應用程式和串流都會在 HTTTPS 之下。 對於開啟內容，播放器不需要驗證或授權，因此您可以自由使用 HTTP 或 HTTPS。

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory 簽署金鑰變換
這是對於您的實作需要納入考量的要點。 如果您未在實作中納入考量，整個系統最終會在最多 6 週之後完全停止運作。

Azure AD 使用業界標準，使用 Azure AD 在本身和應用程式之間建立信任。 具體而言，Azure AD 使用簽署金鑰，該金鑰是由公開和私密金鑰組所組成。 當 Azure AD 建立包含使用者相關資訊的安全性權杖時，此權杖是由 Azure AD 在其私密金鑰傳送回應用程式之前，使用該私密金鑰來簽署。 若要確認該權杖有效且確實來自 Azure AD，應用程式必須使用由 Azure AD 公開，包含在租用戶的同盟中繼資料文件中的公開金鑰來驗證權杖的簽章。 此公開金鑰 – 和它從其衍生的簽署金鑰 – 是用於 Azure AD 中所有租用戶的相同金鑰。

下列文件中提供 Azure AD 金鑰變換的詳細資訊： [Azure AD 中簽署金鑰變換的相關重要資訊](../active-directory/active-directory-signing-key-rollover.md)。

在 [公開/私密金鑰組](https://login.microsoftonline.com/common/discovery/keys/)之間，

* 私密金鑰是由 Azure Active Directory 用來產生 JWT 權杖；
* 公開金鑰是由如 AMS 中的 DRM 授權傳遞服務的應用程式用來驗證 JWT 權杖；

基於安全性目的，Azure Active Directory 會定期替換此憑證 (每隔 6 週)。 在發生安全性漏洞時，金鑰變換可以隨時發生。 因此，AMS 中的授權傳遞服務需要在 Azure AD 替換金鑰組時更新公開金鑰，否則 AMS 中的權杖驗證將會失敗，並且不會發出任何授權。

設定 DRM 授權傳遞服務時，這是藉由設定 TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument 來達成。

JWT 權杖流程如下：

1. Azure AD 會使用目前的私密金鑰為已驗證的使用者發出 JWT 權杖；
2. 當播放器看到 CENC 與多重 DRM 保護內容時，它會先找出 Azure AD 所發出的 JWT 權杖。
3. 播放器將具有 JWT 權杖的授權取得要求傳送至 AMS 中的授權傳遞服務；
4. AMS 中的授權傳遞服務會使用來自 Azure AD 的目前/有效公開金鑰來驗證 JWT 權杖，然後再發出授權。

DRM 授權傳遞服務永遠會檢查來自 Azure AD 的目前/有效公開金鑰。 Azure AD 所呈現的公開金鑰是用來驗證 Azure AD 所發出的 JWT 權杖的金鑰。

如果 AAD 產生 JWT 權杖之後，但是播放器將 JWT 權杖傳送至 AMS 中的 DRM 授權傳遞服務以進行驗證之前，發生金鑰變換？

因為金鑰可能會在任何時間變換，同盟中繼資料文件中永遠有一個以上的有效公開金鑰可用。 Azure 媒體服務授權傳遞可以使用文件中指定的任何金鑰，因為某個金鑰可能很快就會替換，而另一個可能會取代它，依此類推。

### <a name="where-is-the-access-token"></a>存取權杖在哪裡？
如果您在＜ [採用 OAuth 2.0 用戶端認證授與的應用程式識別](../active-directory/develop/active-directory-authentication-scenarios.md#web-application-to-web-api)＞一節中查看 Web 應用程式如何呼叫 API 應用程式，驗證流程如下：

1. 使用者在 Web 應用程式中登入 Azure AD (請參閱＜ [Web 瀏覽器到 Web 應用程式](../active-directory/develop/active-directory-authentication-scenarios.md#web-browser-to-web-application)＞)。
2. Azure AD 授權端點會將使用者代理程式重新導向回到具有授權碼的用戶端應用程式。 使用者代理程式會將授權碼傳回用戶端應用程式的重新導向 URI。
3. Web 應用程式需要取得存取權杖，才能向 Web API 驗證和擷取所需的資源。 它向 Azure AD 的權杖端點提出要求，並提供認證、用戶端識別碼和 Web API 的應用程式識別碼 URI。 它會呈現授權碼以證明使用者已同意。
4. Azure AD 驗證應用程式，並傳回用來呼叫 Web API 的 JWT 存取權杖。
5. Web 應用程式使用傳回的 JWT 存取權杖，透過 HTTPS，在對 Web API 的要求的 Authorization 標頭中加上 JWT 字串並指定 "Bearer"。 接著，Web API 驗證 JWT 權杖，如果驗證成功，則傳回所需的資源。

在此「應用程式身分識別」流程中，Web API 信任 Web 應用程式已驗證使用者。 基於這個理由，這種模式稱為受信任子系統。 [此頁面上的圖表](https://docs.microsoft.com/azure/active-directory/active-directory-protocols-oauth-code) 說明授權碼授與流程的運作方式。

在具有權杖限制的授權取得中，我們遵循相同的受信任子系統模式。 Azure 媒體服務中的授權傳遞服務是 Web API 資源，Web 應用程式需要存取的「後端資源」。 那麼存取權杖在哪裡？

的確，我們會從 Azure AD 取得存取權杖。 使用者驗證成功之後，會傳回授權碼。 然後使用授權碼，以及用戶端識別碼和應用程式金鑰，來交換存取權杖。 存取權杖是用來存取「指標」應用程式或代表 Azure 媒體服務授權傳遞服務。

我們需要依照下列步驟，在 Azure AD 中註冊並設定「指標」應用程式：

1. 在 Azure AD 租用戶中

   * 新增應用程式 (資源) 與登入 URL：

   https://[resource_name].azurewebsites.net/ 和

   * 應用程式識別碼 URL：

   https://[aad_tenant_name].onmicrosoft.com/[resource_name]；
2. 為資源應用程式新增新的金鑰；
3. 更新應用程式資訊清單檔案，讓 groupMembershipClaims 屬性具有下列值："groupMembershipClaims": "All"，  
4. 在Azure AD 應用程式中指向播放器 Web 應用程式，在 [其他應用程式的權限] 區段中，新增上述步驟 1 中已新增的資源應用程式。 在 [委派的權限] 底下勾選 [存取 [resource_name]] 核取記號。 這可給予 Web 應用程式權限以建立存取權杖，來存取資源應用程式。 如果您使用 Visual Studio 和 Azure Web 應用程式進行開發，您應該對本機和已部署版本的 Web 應用程式這麼做。

因此，Azure AD 所發出的 JWT 權杖確實是存取此「指標」資源的存取權杖。

### <a name="what-about-live-streaming"></a>即時串流呢？
以上的討論將重點放在隨選資產。 即時串流呢？

好消息是，您可以使用完全相同的設計和實作來保護 Azure 媒體服務中的即時串流，方法是將與程式相關聯的資產當成「VOD 資產」。

具體來說，大家都知道，若要在 Azure 媒體服務中執行即時串流，您需要建立通道，然後在通道底下建立程式。 若要建立程式，您需要建立資產，它包含程式的即時封存。 為了提供即時內容的 CENC 與多重 DRM 保護，您需要做的是在啟動程式之前將相同設定/處理套用至資產，如同它是「VOD 資產」一般。

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>在 Azure 媒體服務外部的授權伺服器呢？
通常，客戶可能會在他們自己的資料中心或由 DRM 服務提供者裝載的位置，投資授權伺服器陣列。 幸運的是，Azure 媒體服務內容保護可讓您在混合模式中運作：內容會在 Azure 媒體服務中裝載並且以動態方式保護，而 DRM 授權是由 Azure 媒體服務外部的伺服器傳遞。 在此情況下，有下列的變更考量：

1. 安全權杖服務必須發出權杖，授權伺服器陣列可接受及驗證該權杖。 例如，Axinom 所提供的 Widevine 授權伺服器要求特定 JWT 權杖，其中包含「權利訊息」。 因此，您需要有 STS 以發出這類的 JWT 權杖。 作者已完成這類實作，而您可以在 [Azure 文件中心](https://azure.microsoft.com/documentation/)的下列文件中找到詳細資料：[使用 Axinom 將 Widevine 授權傳遞到 Azure 媒體服務](media-services-axinom-integration.md)。
2. 您不再需要在 Azure 媒體服務中設定授權傳遞服務 (ContentKeyAuthorizationPolicy)。 您需要做的是當您在設定 CENC 與多重 DRM 中設定 AssetDeliveryPolicy 時，提供授權取得 URL (針對 PlayReady、Widevine 和 FairPlay)。

### <a name="what-if-i-want-to-use-a-custom-sts"></a>如果我想要使用自訂 STS？
可能有幾個原因讓客戶想要選擇使用自訂 STS (安全權杖服務) 提供 JWT 權杖。 以下是其中一些原因：

1. 客戶使用的身分識別提供者 (IDP) 不支援 STS。 在此情況下，自訂 STS 是一個選項。
2. 客戶在整合 STS 與客戶的訂閱者計費系統時可能需要更多彈性或更緊密的控制。 例如，MVPD 業者可能會提供多個 OTT 訂閱者套件，例如高階、基本、運動等。業者可能想要讓權杖中的宣告與訂閱者套件相符，如此一來，只有正確套件中的內容可供使用。 在此情況下，自訂 STS 提供所需的彈性和控制。

使用自訂 STS 時需要兩項變更：

1. 為資產設定授權傳遞服務時，您必須指定自訂 STS 用來驗證的安全性金鑰 (以下將詳細說明)，而不是 Azure Active Directory 的目前金鑰。
2. 產生 JTW 權杖時，會指定安全性金鑰，而不是 Azure Active Directory 中目前 x509 憑證的私密金鑰。

有兩種類型的安全性金鑰：

1. 對稱金鑰：相同金鑰用於產生和驗證 JWT 權杖；
2. 非對稱金鑰：搭配使用 x509 憑證中的私密-公開金鑰組，私密金鑰用來加密/產生 JWT 權杖，公開金鑰用來驗證權杖。

#### <a name="tech-note"></a>技術提示
如果您使用 .NET Framework/C# 做為開發平台，用於非對稱安全性金鑰的 x509 憑證的金鑰長度必須至少為 2048。 這是 .NET Framework 中的 System.IdentityModel.Tokens.X509AsymmetricSecurityKey 類別的需求。 否則，會擲回下列例外狀況：

IDX10630：用於簽署的 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' 不能小於 '2048' 位元。

## <a name="the-completed-system-and-test"></a>完整的系統和測試
我們將在完整的端對端系統中逐步進行幾個案例，讓讀者在取得登入帳戶之前可以有行為的基本「印象」。

您可以在 [這裡](https://openidconnectweb.azurewebsites.net/)找到播放器 Web 應用程式及其登入。

如果您需要的是「非整合式」案例：裝載在 Azure 媒體服務的視訊資產，未受保護或受 DRM 保護，但是沒有權杖驗證 (對任何提出要求的對象發出授權)，您可以測試它而不用登入 (方法是切換為 HTTP，如果您的視訊串流是透過 HTTP)。

如果您需要的是端對端整合案例：視訊資產是在 Azure 媒體服務的動態 DRM 保護下，權杖驗證與 JWT 權杖是由 Azure AD 產生，則您需要登入。

### <a name="user-login"></a>使用者登入
為了測試端對端整合 DRM 系統，您必須建立或新增「帳戶」。

哪個帳戶？

雖然 Azure 原先只允許 Microsoft 帳戶使用者進行存取，但它現在可讓這兩個系統的使用者進行存取。 此乃透過讓所有 Azure 屬性信任 Azure AD 進行驗證、讓 Azure AD 驗證組織使用者，以及透過建立同盟關係，讓 Azure AD 信任 Microsoft 帳戶消費者識別系統進行消費者驗證等方式達成。 如此一來，Azure AD 便能驗證「來賓」Microsoft 帳戶，以及「原生」Azure AD 帳戶。

因為 Azure AD 信任 Microsoft 帳戶 (MSA) 網域，您可以將下列任何網域的任何帳戶新增至自訂 Azure AD 租用戶，並使用帳戶登入：

| **網域名稱** | **網域** |
| --- | --- |
| **自訂 Azure AD 租用戶網域** |somename.onmicrosoft.com |
| **公司網域** |microsoft.com |
| **Microsoft 帳戶 (MSA) 網域** |outlook.com、live.com、hotmail.com |

您可以連絡任何作者為您建立或新增帳戶。

以下是不同網域帳戶所使用的不同登入頁面的螢幕擷取畫面。

**自訂 Azure AD 租用戶網域帳戶**：在此案例中，您會看到自訂 Azure AD 租用戶網域的自訂登入頁面。

![自訂 Azure AD 租用戶網域帳戶](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**採用智慧卡的 Microsoft 網域帳戶**：在此案例中，您會看到由 Microsoft Corporate 的 IT 自訂、採用雙因素驗證的登入頁面。

![自訂 Azure AD 租用戶網域帳戶](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft 帳戶 (MSA)**：在此案例中，您會看到取用者的 Microsoft 帳戶登入頁面。

![自訂 Azure AD 租用戶網域帳戶](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>Using Encrypted Media Extensions for PlayReady (使用 PlayReady 的加密媒體擴充)
在支援 PlayReady 的加密媒體擴充 (EME) 的最新瀏覽器 (例如 Windows 8.1 和更新版本上的 IE 11，以及 Windows 10 上的 Microsoft Edge 瀏覽器) 上，PlayReady 是 EME 的基本 DRM。

![針對 PlayReady 使用 EME](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

深色的播放器區域是由於 PlayReady 保護防止對受保護的視訊進行螢幕擷取。

下列畫面顯示播放器外掛程式和 MSE/EME 支援。

![針對 PlayReady 使用 EME](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

Windows 10 的 Microsoft Edge 及 IE 11 中的 EME，允許支援 [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) 的 Windows 10 裝置上的 PlayReady SL3000 叫用。 PlayReady SL3000 會解除增強型付費內容 (4K、HDR 等)，以及新的內容傳遞模型 (增強型內容的早期視窗) 流程的鎖定。

將焦點放在 Windows 裝置上：PlayReady 是 Windows 裝置可用的 HW 中唯一的 DRM (PlayReady SL3000)。 串流服務可透過 EME 或 UWP 應用程式來使用 PlayReady，並利用 PlayReady SL3000 來提供比其他 DRM 所能提供更高的影片品質， 一般而言，2K 內容將會流經 Chrome 或 Firefox，而 4K 內容會流過 Microsoft Edge/IE11 或相同裝置上的 UWP 應用程式 (取決於服務的設定及實作)。

#### <a name="using-eme-for-widevine"></a>針對 Widevine 使用 EME
在支援 EME/Widevine 的現代瀏覽器上，例如 Windows 10、Windows 8.1、Mac OSX Yosemite 上的 Chrome 41+，以及 Android 4.4.4 上的 Chrome，Google Widevine 是 EME 背後的 DRM。

![針對 Widevine 使用 EME](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

請注意，Widevine 不會防止對受保護的視訊進行螢幕擷取。

![針對 Widevine 使用 EME](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>不是有權限的使用者
如果使用者不是「有權限的使用者」群組的成員，使用者將無法通過「權利檢查」，且多重 DRM 授權服務將拒絕發出要求的授權，如下所示。 詳細的描述是「授權取得失敗」，這是根據設計。

![無權限的使用者](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>Running custom Secure Token Service (執行自訂安全權杖服務)
對於執行自訂安全權杖服務 (STS) 的案例，JWT 權杖將會由自訂 STS 使用對稱或非對稱金鑰來發出。

使用對稱金鑰的案例 (使用 Chrome)：

![執行自訂 STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

透過 x509 憑證使用非對稱金鑰的案例 (使用 Microsoft 現代瀏覽器)。

![執行自訂 STS](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

在上述兩個案例中，使用者驗證相同 – 透過 Azure AD。 唯一的差別在於，JWT 權杖是由自訂 STS 發出，而不是 Azure AD。 當然，設定動態 CENC 保護時，授權傳遞服務的限制會指定 JWT 權杖的類型，是對稱或非對稱金鑰。

## <a name="summary"></a>摘要
在本文件中，我們討論了透過權杖驗證的 CENC 與多重原生 DRM 和存取控制：它的設計和實作使用 Azure、Azure 媒體服務和 Azure Media Player。

* 參考設計會顯示，其中包含 DRM/CENC 子系統中的所有必要元件；
* Azure、Azure 媒體服務和 Azure Media Player 的參考實作。
* 同時也討論到直接牽涉設計和實作的某些主題。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
 

