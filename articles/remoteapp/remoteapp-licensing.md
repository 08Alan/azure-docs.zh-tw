---
title: "Azure RemoteApp 授權 | Microsoft Docs"
description: "了解 Azure RemoteApp 中的授權如何運作。"
services: remoteapp
documentationcenter: 
author: lizap
manager: mbaldwin
ms.assetid: ff8ebd20-61a1-4f10-87a6-234a170534c9
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: elizapo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f4429461139a41644f87185bbb06b1f6a8011345


---
# <a name="how-does-licensing-work-in-azure-remoteapp"></a>Azure RemoteApp 中的授權如何運作？
> [!IMPORTANT]
> Azure RemoteApp 即將中止。 如需詳細資訊，請參閱 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 。
> 
> 

您已設定好 Azure RemoteApp 服務和建立範本，準備向使用者發佈應用程式。 但還有最後一件事必須解決：授權。 只是，如何為 RemoteApp 和您透過 RemoteApp 共用的應用程式使用授權？

RemoteApp 不需要任何 Windows 授權或遠端桌面 CAL。 您的訂用帳戶本身會負責處理 RemoteApp 端的問題。 (請查看[價格方案](https://azure.microsoft.com/pricing/details/remoteapp)的詳細資訊)。

如果您使用訂用帳戶中包括的其中一個映像，則無需個別授權，即可共用該映像上安裝的任何應用程式。 例如，如果您使用 Windows Server 2012 R2 範本映像來組建您的集合，您可以與您的使用者共用 System Center Endpoint Protection。 這個規則的唯一例外狀況是 Office 365 ProPlus (其需要個別的訂用帳戶) 和 Office 2013 (其無法在生產集合中共用)。

如果您想要使用 RemoteApp 隨附的 Office 365 範本映像，您必須有 *現有的* Office 365 ProPlus 方案。 而您使用自訂範本發佈的任何 Office 365 應用程式也是如此。 您必須以自己的訂用帳戶啟用應用程式。 無論試用版或付費訂用帳戶都是如此。 如果您想在試用期間使用 Office 365 範本映像，「但您還沒有訂用帳戶」 ，請移至 Office 365 頁面以 [註冊](https://go.microsoft.com/fwlink/p/?LinkID=403802) 試用的訂用帳戶。 如需詳細資訊，請參閱 [RemoteApp 與 Office 如何共同運作？](remoteapp-o365.md) 。

如果試用期間內您不想取得 Office 365 試用訂用帳戶，請使用 RemoteApp 隨附的 Office 2013 Professional Plus 範本映像。 此範本映像只能使用 30 天，而且無法轉換成付費收藏。

如果是其他應用程式，您必須確定您有共用應用程式的授權。

這很合理，對吧？ 您可以發佈您有權共用的任何應用程式。 您必須確認您確實有權共用您的程式。

請注意，您不能在雲端收藏中使用 CAL 或大量授權合約。 您 *可以* 在混合式收藏中使用大量授權合約來啟用應用程式 (Office 除外)。 您只需從大量授權媒體將它們安裝在範本映像上。 請依照應用程式廠商提供的資訊，在遠端桌面環境中安裝授權。




<!--HONumber=Nov16_HO2-->


