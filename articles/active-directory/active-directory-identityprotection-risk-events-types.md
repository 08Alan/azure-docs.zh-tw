---
title: "Azure Active Directory 偵測到的風險事件類型 | Microsoft Docs"
description: "本主題詳述 Azure Active Directory 中可用的風險事件類型。"
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, 管理應用程式, 安全性, 風險, 風險層級, 弱點, 安全性原則"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: d39c5c7c-a4e5-459d-b78f-cbe31a46bb2f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: ffc64fc0469cd3588d6d13524411575b423ab4e5
ms.openlocfilehash: dc04ebb3c205c01ed42c9d8bc3e0eb009881074a


---
# <a name="types-of-risk-events-detected-by-azure-active-directory"></a>Azure Active Directory 偵測到的風險事件類型 
在 Azure Active Directory 中，風險事件是下列類型的事件︰

* 已標示為可疑
* 指出某個身分識別可能已遭入侵。 

本主題詳述可用的風險事件類型。

## <a name="leaked-credentials"></a>認證外洩
Microsoft 安全性研究人員發現外洩的認證公開張貼於黑暗網路 (Dark Web)。 這些認證通常在純文字中找到。 它們會根據 Azure AD 認證進行檢查，如果有相符項目，則會回報為「認證外洩」。

認證外洩風險事件會被歸類為「高」嚴重性風險事件，因為它們清楚指出攻擊者可使用使用者名稱和密碼。

## <a name="impossible-travel-to-atypical-locations"></a>不可能到達非典型位置的移動
此風險事件類型會識別來自距離遙遠的位置的兩次登入，而根據使用者過去的行為，其中至少有一個位置可能不尋常。 此外，兩次登入之間的時間比使用者從第一個位置移到第二個位置所需的時間還要短，這表示有不同的使用者正在使用相同的認證。 

這種機器學習演算法會忽略明顯的「誤判」，以致發生不可能的移動情況，例如，組織中的其他使用者定期使用的 VPN 和位置。  系統有為期 14 天的初始學習期間，它會在這段期間了解新使用者的登入行為。

不可能的移動通常會明顯指出駭客已能夠成功登入。 不過，當使用者使用新裝置或使用組織中其他使用者通常不會使用的 VPN 進行移動時，可能會發生誤判。 另一個誤判來源是誤將伺服器 IP 當作用戶端 IP 傳遞的應用程式，其可能會導致從裝載應用程式後端的資料中心進行登入 (這些通常是 Microsoft 資料中心，其可能導致從 Microsoft 擁有的 IP 位址進行登入)。 這些誤判以致此風險事件的風險層級為「**中**」。

## <a name="sign-ins-from-infected-devices"></a>從受感染的裝置登入
此風險事件類型會識別從感染惡意程式碼的裝置登入，已知這類登入會主動與 Bot 伺服器通訊。 讓使用者裝置的 IP 位址與聯繫 Bot 伺服器的 IP 位址相互關聯，即可判定此類型。 

此風險事件可識別 IP 位址，而不是使用者裝置。 如果單一 IP 位址背後有數個裝置，而只有某些裝置受 Bot 網路控制，則來自其他裝置的登入可能會不必要地觸發此事件，這就是將此風險事件歸類為「**低**」的原因。  

建議您連絡使用者並掃描使用者的所有裝置。 使用者的個人裝置也可能受到感染，或如前所述，可能是其他人從與使用者相同的 IP 位址使用受感染的裝置。 受感染的裝置通常是受到防毒軟體尚未識別的惡意程式碼所感染，這也表示不良的使用者習慣可能會導致裝置受到感染。

如需如何處理惡意程式碼感染的詳細資訊，請參閱 [惡意程式碼防護中心](http://go.microsoft.com/fwlink/?linkid=335773&clcid=0x409)。

## <a name="sign-ins-from-anonymous-ip-addresses"></a>從匿名 IP 位址登入
此風險事件類型會識別從被視為匿名 Proxy IP 位址的 IP 位址成功登入的使用者。 這些 Proxy 通常由想要隱藏其裝置 IP 位址的人員使用，而且可能用於惡意意圖。

我們建議您立即連絡使用者，確認他們是否使用匿名 IP 位址。 此風險事件類型的風險層級為「**中**」，因為匿名 IP 本身並未強烈指出帳戶遭到入侵。

## <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>從具有可疑活動的 IP 位址登入
此風險事件類型會識別在短期內透過多個使用者帳戶多次嘗試登入失敗的 IP 位址。 這符合攻擊者所使用的 IP 位址流量模式，而且強烈指出帳戶已經或即將遭到入侵。 這種機器學習演算法會忽略明顯的「誤判」，例如，組織中的其他使用者定期使用的 IP 位址。  系統有為期 14 天的初始學習期間，它會在這段期間了解新使用者和新租用戶的登入行為。

我們建議您連絡使用者，確認他們是否實際從標示為可疑的 IP 位址進行登入。 此事件類型的風險層級為「**中**」，因為相同 IP 位址背後可能有數個裝置，而只有某些裝置可能負責進行可疑的活動。 

## <a name="sign-in-from-unfamiliar-locations"></a>從不熟悉的位置登入
此風險事件類型是一種即時登入評估機制，它會考量過去的登入位置 (IP、經緯度和 ASN) 以判斷新的 / 不熟悉的位置。 系統會儲存有關使用者先前所用位置的資訊，並考量這些「熟悉的」位置。 從不在熟悉位置清單中的位置登入時，甚至會觸發此風險。 系統有為期 14 天的初始學習期間，在這段期間內，它不會將任何新位置標示為不熟悉的位置。 系統也會忽略從熟悉的裝置以及地理上靠近熟悉位置的位置進行的登入。 <br>
 不熟悉的位置可以強烈指出攻擊者可嘗試使用遭竊的身分識別。 當使用者正在移動，並試用新裝置或使用新的 VPN 時，可能會發生誤判。 這些誤判以致此事件類型的風險層級為「**中**」。

## <a name="azure-ad-anomalous-activity-reports"></a>Azure AD 異常活動報告
這些風險事件已有部分可透過 Azure 入口網站中的 Azure AD 異常活動報告取得。 下表列出各種風險事件類型和對應的 **Azure AD 異常活動報告** 。 Microsoft 將持續投入這個領域，並且計劃持續改善現有風險事件的偵測精確度，以及持續加入新的風險事件類型。 

| 風險事件類型 | 對應的 Azure AD 異常活動報告 |
|:--- |:--- |
| 認證外洩 |認證外洩的使用者 |
| 不可能到達非典型位置的移動 |異常的登入活動 |
| 從受感染的裝置登入 |從可能受感染的裝置登入 |
| 從匿名 IP 位址登入 |從不明來源登入 |
| 從具有可疑活動的 IP 位址登入 |從具有可疑活動的 IP 位址登入 |
| 從不熟悉的位置登入 |- |
| 鎖定事件 |- |

下列 Azure AD 異常活動報告不會納入為 Azure AD 中的風險事件，因此不會透過 Azure AD 提供。 這些報告仍可在 Azure 入口網站中取得，不過將會在未來某個時候淘汰，因為它們正由 Azure AD 中的風險事件所取代。

* 在多次失敗後登入
* 從多個地理區域登入

## <a name="see-also"></a>另請參閱
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)




<!--HONumber=Dec16_HO5-->


