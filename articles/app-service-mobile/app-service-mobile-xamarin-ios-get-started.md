<properties
	pageTitle="在 Xamarin.iOS app 中開始使用 Azure App Service Mobile Apps | Microsoft Azure"
	description="遵循本教學課程，開始使用 Mobile Apps 進行 Xamarin.iOS 開發。"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/13/2016"
	ms.author="normesta"/>


#建立 Xamarin.iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##概觀

本教學課程說明如何使用 Azure 行動 app 後端，將雲端型後端服務加入至 Xamarin.iOS 行動 app。您將建立新的行動 app 後端，以及簡單的 Todo list Xaamrin.iOS 應用程式，後者會在 Azure 中儲存 app 資料。

您必須先完成此教學課程，才能進行所有其他在 Azure App Service 中使用 Mobile Apps 的 Xamarin.iOS 相關教學課程。

##必要條件

若要完成此教學課程，您需要下列項目：

* 使用中的 Azure 帳戶。如果您沒有帳戶，可以註冊 Azure 試用版並取得最多 10 個免費的行動應用程式，即使在試用期結束之後仍可繼續使用這些應用程式。如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* Visual Studio 和 Xamarin。如需相關指示，請參閱[設定和安裝 Visual Studio 和 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx)。

* 已安裝 Xcode v7.0 或更新版本以及 Xamarin Studio Community 的 Mac。請參閱[設定和安裝 Visual Studio 和 Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) 以及[針對 Mac 使用者的設定、安裝和驗證](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN)。

>[AZURE.NOTE] 如果您想在註冊 Azure 帳戶之前先開始使用 Azure App Service，請前往[試用 App Service](https://tryappservice.azure.com/?appServiceName=mobile)。您可以於該處，在 App Service 中立即建立短期的入門行動 app - 不需信用卡，不需任何承諾。

## 建立新的 Azure 行動應用程式後端

依照下列步驟建立新的行動應用程式後端。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## 設定伺服器專案

您現在已佈建 Azure 行動應用程式後端，可供您的行動用戶端應用程式使用。接下來，您將下載簡易「待辦事項清單」後端的伺服器專案，然後將專案發佈至 Azure。

請遵循下列步驟來設定伺服器專案使用 Node.js 或 .NET 後端。

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]


## (選擇性) 在本機測試您的後端專案

如果您選擇上述的 .NET 後端組態，您可以在本機選擇性地測試後端。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]



## 下載並執行 Xamarin.iOS 應用程式

1. 在瀏覽器視窗中開啟 [Azure 入口網站]。

2. 在行動應用程式的設定刀鋒視窗上，按一下 [開始使用] > [Xamarin.Forms]。在步驟 3 中，按一下 [建立新的應用程式] \(如果尚未選取的話)。接著按一下 [下載] 按鈕。

  	這會下載包含連線到您行動應用程式之用戶端應用程式的專案。將此壓縮專案檔案儲存到您的本機電腦，並記錄儲存位置。

3. 將您下載的專案解壓縮，並在 Xamarin Studio (或 Visual Studio) 中開啟。

	![][9]

	![][8]

4. 按 F5 鍵，以建置專案並在 iPhone 模擬器中啟動 app。

5. 在應用程式中，輸入有意義的文字 (例如「了解 Xamarin (Learn Xamarin)」)，然後按一下 **+** 按鈕。

	![][10]

	如此會傳送 POST 要求到 Azure 中代管的新行動應用程式後端。要求中的資料會插入 TodoItem 資料表中。行動應用程式後端會傳回資料表中儲存的項目，而該資料會顯示在清單中。

>[AZURE.NOTE]您可以在 QSTodoService.cs C# 檔案中檢閱用來存取行動應用程式後端以查詢和插入資料的程式碼。

##後續步驟

* [將驗證新增至您的應用程式](app-service-mobile-xamarin-ios-get-started-users.md) <br/>了解如何使用身分識別提供者來驗證應用程式的使用者。

* [將推播通知新增至您的應用程式](app-service-mobile-xamarin-ios-get-started-push.md) <br/>了解如何將非常基本的推播通知傳送至應用程式。

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure 入口網站]: https://portal.azure.com/

<!---HONumber=AcomDC_0413_2016-->
