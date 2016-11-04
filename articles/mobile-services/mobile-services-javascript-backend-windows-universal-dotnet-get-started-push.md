---
title: 將推播通知新增至通用 Windows 8.1 應用程式 | Microsoft Docs
description: 了解如何使用 Azure 通知中樞從 JavaScript 後端行動服務，傳送推播通知給通用 Windows 8.1 應用程式。
services: mobile-services,notification-hubs
documentationcenter: windows
author: ggailey777
manager: erikre
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# 將推播通知新增至行動服務應用程式
[!INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> 如需本主題的對等 Mobile Apps 版本，請參閱[新增推播通知至 Windows App](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md)。
> 
> 

本主題說明如何使用 Azure 行動服務與 JavaScript 後端傳送推播通知至通用 Windows 應用程式。在本教學課程中，您會透過 Azure 通知中心，啟用通用 Windows 應用程式專案中的推播通知功能。完成之後，每次 TodoList 資料表中插入記錄時，您的行動服務將會從 JavaScript 後端將推播通知傳送至所有註冊的 Windows 市集與 Windows Phone 市集應用程式。您所建立的通知中心可透過行動服務免費使用、可在行動服務以外個別管理，並且可供其他應用程式和服務使用。

> [!NOTE]
> 本主題說明如何使用 Visual Studio 2013 with Update 3 的工具，從行動服務將推播通知支援新增至通用 Windows 應用程式。要將推播通知從行動服務新增到 Windows 市集或 Windows Phone 8.1 應用程式時，也可使用相同的步驟進行。若要將推播通知新增至 Windows Phone 8 或 Windows Phone Silverlight 8.1 應用程式，請檢視此版本的[開始在行動服務中使用推播通知](mobile-services-javascript-backend-windows-phone-get-started-push.md)。
> 
> 

本教學課程將逐步引導您完成下列啟用推播通知的基本步驟：

1. [註冊應用程式以取得推播通知](#register)
2. [更新服務以傳送推播通知](#update-service)
3. [在應用程式中測試推播通知](#test)

若要完成此教學課程，您需要下列項目：

* 有效的 [Microsoft 市集帳戶](http://go.microsoft.com/fwlink/p/?LinkId=280045)。
* [Visual Studio 2013 Express for Windows](http://go.microsoft.com/fwlink/?LinkId=257546) (with Update 3 或更新版本)

## <a id="register"></a>針對推播通知註冊應用程式
[!INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

&nbsp;&nbsp;6.瀏覽至 [`\Services\MobileServices\your_service_name`] 專案資料夾並開啟產生的 push.register.cs 程式碼檔案，然後檢查用來將裝置通道 URL 向通知中心註冊的 **UploadChannel** 方法。

&nbsp;&nbsp;7.開啟 shared App.xaml.cs 程式碼檔案，並注意新的 **UploadChannel** 方法呼叫已經新增到 **OnLaunched** 事件處理常式。這可確保在每次啟動應用程式時嘗試註冊裝置。

&nbsp;&nbsp;8.重複上述步驟將推播通知新增至 Windows Phone 市集 App 專案，然後在共用的 App.xaml.cs 檔案中，移除額外的 **Mobile Service Client**、**UploadChannel** 呼叫以及剩餘的 `#if...#endif` 條件式包裝函式。這兩個專案現在都會共用 **UploadChannel** 的單一呼叫。

&nbsp;&nbsp;請注意，您也可以將使用 `#if...#endif` 包裝的 [MobileServiceClient] 定義統一至單一未包裝的定義，以供這兩個應用程式版本使用，藉此簡化產生的程式碼。

現在應用程式已經啟用了推播通知，您必須更新行動服務以傳送推播通知。

## <a id="update-service"></a>更新服務以傳送推播通知
下列步驟會更新 TodoItem 資料表裡註冊的插入指令碼。您可以在任何伺服器指令碼或是後端服務的任何一處實作類似的程式碼。

[!INCLUDE [mobile-services-javascript-update-script-notification-hubs](../../includes/mobile-services-javascript-update-script-notification-hubs.md)]

## <a id="test"></a>在應用程式中測試推播通知
[!INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>後續步驟
本教學課程示範如何啟用 Windows 市集應用程式以便使用行動服務與通知中心來傳送推播通知的基礎。接著，請考慮完成下列其中一個教學課程：

* [將推播通知傳送給驗證的使用者](mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md) <br/>了解如何利用標記，從行動服務將推播通知只傳送給已驗證的使用者。
* [將廣播通知傳送給訂閱者](../notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md) <br/>了解使用者如何註冊及接收其所需之類別的推播通知。
* [將跨平台通知傳送給訂閱者](../notification-hubs/notification-hubs-aspnet-cross-platform-notification.md) <br/>了解如何使用範本從您的行動服務傳送推播通知，但不必在後端製作平台特定的裝載。

在下列主題中深入了解行動服務和通知中心：

* [Azure 通知中樞 - 診斷指導方針](../notification-hubs/notification-hubs-push-notification-fixer.md) <br/>了解如何疑難排解推播通知問題。
* [開始使用驗證] <br/>了解如何使用行動服務驗證使用不同帳戶類型的應用程式使用者。
* [什麼是通知中心？] <br/>深入了解通知中心如何跨所有主要用戶端平台將通知傳遞到您的應用程式。
* [如何使用 Azure 行動服務的 .NET 用戶端] <br/>深入了解如何使用來自 C# Windows 應用程式的行動服務。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[開始使用驗證]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md

[Send push notifications to authenticated users]: mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md

[什麼是通知中心？]: ../notification-hubs-overview.md

[如何使用 Azure 行動服務的 .NET 用戶端]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030


<!---HONumber=AcomDC_0727_2016-->