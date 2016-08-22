<properties
	pageTitle="Azure AD Connect：常見問題集 |Microsoft Azure"
	description="此頁面包含有關 Azure AD Connect 的常見問題集。"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="billmath"/>

# Azure AD Connect 常見問題集

## 一般安裝
**問：如果 Azure AD 全域管理員已啟用 2FA，安裝是否能夠運作？** 從 2016 年 2 月的組建開始便提供這項支援。

**問：是否有方法可自動安裝 Azure AD Connect？** 它僅支援使用安裝精靈來安裝 Azure AD Connect。不支援自動和無訊息安裝。

**問：我有一個樹系，但無法連線到其中的網域。如何安裝 Azure AD Connect？** 從 2016 年 2 月的組建開始便提供這項支援。

**問︰AD DS 健康情況代理程式是否是在伺服器核心上運作？** 是。安裝代理程式之後，您可以使用下列 PowerShell Commandlet 來完成註冊程序︰

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

## 網路
**問：我的防火牆、網路裝置或其他軟硬體會限制在網路上開啟連線的時間上限。使用 Azure AD Connect 時，用戶端逾時閥值時間應該多長？** 所有網路軟體、實體裝置或其他軟硬體限制連線開啟時間上限的閥值應該至少為 5 分鐘 (300 秒)，以便讓安裝 Azure AD Connect 用戶端的伺服器與 Azure Active Directory 連線。這也適用於所有先前發行的 Microsoft Identity 同步處理工具。

**問：是否支援 SLD (單一標籤網域)？** 否，Azure AD Connect 不支援使用 SLD 的內部部署樹系/網域。

**問：是否支援有句點的 NetBios 名稱？** 否，Azure AD Connect 不支援 NetBios 名稱包含句點的內部部署樹系/網域。

## 同盟
**問：如果我收到一封電子郵件，要求我更新我的 Office 365 憑證，該怎麼辦？** 請參考[更新憑證](active-directory-aadconnect-o365-certs.md)主題中概述的指導方針來了解如何更新憑證。

**問：我已經針對 O365 信賴憑證者設定「自動更新信賴憑證者」。當我的權杖簽署憑證自動換用時，需要採取任何動作嗎？** 請參考[更新憑證](active-directory-aadconnect-o365-certs.md)一文中概述的指導方針。

## Environment
**問：安裝 Azure AD Connect 之後，是否支援重新命名伺服器？** 否。變更伺服器名稱將會導致同步處理引擎無法連接到 SQL 資料庫，服務將無法啟動。

## 身分識別資料
**問：Azure AD 中的 UPN (userPrincipalName) 屬性不符合內部部署的 UPN，為什麼？** 請參閱以下文章：

- [Office 365、Azure 或 Intune 中的使用者名稱不符合內部部署的 UPN 或替代登入識別碼](https://support.microsoft.com/zh-TW/kb/2523192)
- [在您將使用者帳戶的 UPN 變更為使用不同的同盟網域後，Azure Active Directory 同步作業工具未同步處理變更](https://support.microsoft.com/zh-TW/kb/2669550)

您也可以將 Azure AD 設定為允許同步處理引擎更新 userPrincipalName，如 [Azure AD Connect 同步處理服務功能](active-directory-aadconnectsyncservice-features.md)中所述。

## 自訂組態
**問：Azure AD Connect 適用的 PowerShell Cmdlet 記載於何處？** 除了記載於本網站上的 Cmdlet，在 Azure AD Connect 中找到的其他 PowerShell Cmdlet 不支援客戶使用。

**問：我是否可以使用 *Synchronization Service Manager* 中的「伺服器匯出/伺服器匯入」，在伺服器之間移動組態？** 否。此選項將不會擷取所有組態設定，因此不應使用。您應該改用精靈在第二部伺服器上建立基底組態，並使用同步處理規則編輯器產生 PowerShell 指令碼，以在伺服器之間移動任何自訂規則。請參閱[把自訂組態從作用中伺服器移動到預備伺服器](active-directory-aadconnect-upgrade-previous-version.md#move-custom-configuration-from-active-to-staging-server)。

## 疑難排解
**問：如何取得 Azure AD Connect 的說明？**

[搜尋 Microsoft 知識庫 (KB)](https://www.microsoft.com/zh-TW/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- 在 Microsoft 知識庫 (KB) 中搜尋有關 Azure AD Connect 支援的常見協助修正問題的技術解決方案。

[Microsoft Azure Active Directory 論壇](https://social.msdn.microsoft.com/Forums/azure/zh-TW/home?forum=WindowsAzureAD)

- 按一下[這裡](https://social.msdn.microsoft.com/Forums/azure/zh-TW/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)，即可在社群中搜尋和瀏覽技術問題和答案，或提出自己的問題。

[Azure AD Connect 客戶支援](https://manage.windowsazure.com/?getsupport=true)

- 使用此連結透過 Azure 入口網站取得支援。

<!---HONumber=AcomDC_0810_2016-->