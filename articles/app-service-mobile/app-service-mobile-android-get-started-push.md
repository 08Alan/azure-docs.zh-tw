---
title: "使用 Azure 行動應用程式將推播通知新增至 Android 應用程式"
description: "了解如何使用 Azure 行動應用程式將推播通知傳送至 Android 應用程式。"
services: app-service\mobile
documentationcenter: android
manager: erikre
editor: 
author: ysxu
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ac50b4986d6abd6f3a4af660560f4252b8c99fdb


---
# <a name="add-push-notifications-to-your-android-app"></a>將推播通知新增至 Android 應用程式
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Overview
在本教學課程中，您會將推播通知新增至 [Android 快速入門]專案，以便在每次插入一筆記錄時傳送推播通知至裝置。

如果您不要使用下載的快速入門伺服器專案，則需要推播通知擴充套件。 如需詳細資訊，請參閱[使用 Azure Mobile Apps 的 .NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

## <a name="prerequisites"></a>必要條件
您需要下列項目：

* 根據您的專案後端的 IDE：
  
  * 如果此應用程式有 Node.js 後端，則為 [Android Studio](https://developer.android.com/sdk/index.html)。
  * 如果此應用程式有 .Net 後端，則為 [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) 或更新版本。
* Android 2.3 或更新版本、Google Repository 版本 27 或更高版本，以及 Firebase 雲端傳訊適用的 Google Play 服務 9.0.2 或更高版本。
* 完成 [Android 快速入門]。

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>建立支援 Firebase 雲端通訊的專案
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>設定通知中樞
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>設定 Azure 來傳送推播通知
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>啟用伺服器專案的推播通知
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>將推播通知新增至應用程式
本節中，您必須更新用戶端 Android 應用程式，以處理推播通知。

### <a name="verify-android-sdk-version"></a>驗證 Android SDK 版本
[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

下一個步驟是安裝 Google Play 服務。 Google 雲端通訊在開發和測試方面有一些 API 層級的最低需求，這些是資訊清單中的 **minSdkVersion** 屬性所必須遵守。

如果您要以較舊的裝置進行測試，請參考[設定 Google Play 服務 SDK]，以確認此值可以設得多低，並加以適當設定。

### <a name="add-google-play-services-to-the-project"></a>新增 Google Play 服務至專案
[!INCLUDE [Add Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

### <a name="add-code"></a>新增程式碼
[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>對已發佈的行動服務進行應用程式測試
您可以使用 USB 纜線直接連接 Android 手機，或使用模擬器中的虛擬裝置，對應用程式進行測試。

## <a name="more"></a>更多資訊
<!-- URLs -->
[Android 快速入門]: app-service-mobile-android-get-started.md

[設定 Google Play 服務 SDK]:https://developers.google.com/android/guides/setup



<!--HONumber=Nov16_HO3-->


