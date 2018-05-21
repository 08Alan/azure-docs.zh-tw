---
title: 選擇 Azure MFA 雲端或伺服器 | Microsoft Docs
description: 藉由提出我要保護什麼和使用者所在位置等問題，選擇最合適的 Multi-Factor Authentication 安全性解決方案。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 8314d72aa2cc6787d3f65dd48cd693a0ac332c0a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2018
---
# <a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>選擇您的 Azure Multi-Factor Authentication 方案
因為 Azure Multi-Factor Authentication (MFA) 的種類繁多，我們必須回答幾個問題，以找出最合適的版本。  這些問題如下：

* [我要保護什麼](#what-am-i-trying-to-secure)
* [使用者位於何處](#where-are-the-users-located)
* [我需要哪些功能？](#what-features-do-i-need)

下列各節會提供如何確定以上問題之答案的指引。

## <a name="what-am-i-trying-to-secure"></a>我要保護什麼？
為了判斷出正確的雙步驟驗證方案，首先我們必須回答一個問題：您試圖使用第二個驗證方法來保護什麼？  它是 Azure 中的應用程式嗎？  或是遠端存取系統？  藉由判斷我們嘗試保護的東西，我們可以對該在何處啟用 Multi-Factor Authentication 這個問題做出回答。  

| 您想要保護什麼 | 雲端 MFA | MFA Server |
| --- |:---:|:---:|
| 第一方 Microsoft 應用程式 |● |● |
| 應用程式資源庫中的 SaaS 應用程式 |● |  |
| 透過 Azure AD App Proxy 發佈的 Web 應用程式 |● |  |
| 非透過 Azure AD App Proxy 發佈的 IIS 應用程式 | |● |
| VPN、RDG 等遠端存取 | ● | ● |

## <a name="where-are-the-users-located"></a>使用者位於何處
接下來，查看使用者的所在位置有助於判斷出合適的解決方案，不論是雲端中或使用 MFA Server 的內部部署。

| 使用者位置 | 雲端 MFA | MFA Server |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD 和使用 AD FS 同盟的內部部署 AD |● |● |
| Azure AD 和使用 DirSync、Azure AD 同步、Azure AD Connect 的內部部署 AD - 沒有密碼雜湊同步或傳遞驗證 |● |● |
| Azure AD 和使用 DirSync、Azure AD 同步、Azure AD Connect 的內部部署 AD - 具有密碼雜湊同步或傳遞驗證 |● | |
| 內部部署 Active Directory | |● |

## <a name="what-features-do-i-need"></a>我需要哪些功能？
下表是雲端中 Multi-Factor Authentication 和 Multi-factor Authentication Server 的可用功能比較。

| 功能 | 雲端 MFA | MFA Server |
| --- |:---:|:---:|
| 以行動應用程式通知做為第二個因素 | ● | ● |
| 以行動應用程式驗證碼做為第二個因素 | ● | ● |
| 以撥打電話做為第二個因素 | ● | ● |
| 以單向 SMS 做為第二個因素 | ● | ● |
| 以雙向 SMS 做為第二個因素 | | ●  (取代)| 
| 以硬體權杖做為第二個因素 | | ● |
| 不支援 MFA 之 Office 365 用戶端的應用程式密碼 | ● | |
| 系統管理員控制驗證方法 | ● | ● |
| PIN 模式 | | ● |
| 詐騙警示 |● | ● |
| MFA 報告 |● | ● |
| 一次性略過 | | ● |
| 通話的自訂問候語 | ● | ● |
| 可自訂的通話來電者 ID | ● | ● |
| 信任的 IP | ● | ● |
| 記住受信任裝置的 MFA | ● | |
| 條件式存取 | ● | ● |
| 快取 |  | ● |

## <a name="next-steps"></a>後續步驟

既然您已了解雲端 Azure Multi-Factor Authentication 或內部部署 MFA Server 之間的差異，就可以開始設定和使用 Azure Multi-factor Authentication。 **選取代表您案例的圖示**

<center>

[![雲端 MFA](./media/concept-mfa-whichversion/cloud2.png)](howto-mfa-getstarted.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [![MFA Server](./media/concept-mfa-whichversion/server2.png)](howto-mfaserver-deploy.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </center>
