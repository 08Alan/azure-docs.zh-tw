<properties
	pageTitle="將驗證新增至 Universal Windows 8.1 應用程式 | Microsoft Azure"
	description="了解如何使用行動服務透過眾多識別提供者 (包括 Google、Facebook、Twitter 和 Microsoft) 驗證 Universal Windows 8.1 應用程式的使用者。"
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="glenga"/>

# 在您的行動服務應用程式中新增驗證
[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> 如需本主題的對等 Mobile Apps 版本，請參閱[將驗證新增至您的 Windows 應用程式](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md)。

## 概觀

本主題說明如何從您的通用 Windows 應用程式在 Azure 行動服務中驗證使用者。在本教學課程中，您將使用行動服務支援的身分識別提供者，將驗證加入至快速入門專案。由行動服務成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程會以行動服務快速入門為基礎。您還必須先完成[開始使用行動服務]或[將行動服務新增至現有應用程式](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md)教學課程。

>[AZURE.NOTE]本教學課程說明如何在 Windows 市集與 Windows Phone 市集 8.1 應用程式中使用伺服器導向的使用者驗證。如需用戶端導向的驗證相關資訊，請參閱[使用 Google、Microsoft 和 Facebook SDK 登入 Azure 行動服務](https://azure.microsoft.com/blog/2014/10/27/logging-in-with-google-microsoft-and-facebook-sdks-to-azure-mobile-services/)。

##<a name="register"></a>註冊應用程式進行驗證，並設定行動服務

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>限制只有通過驗證的使用者具有權限

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;6.在 Visual Studio 中，以滑鼠右鍵按一下 TodoList 應用程式的 Windows 市集專案，然後按一下 [設定為啟始專案]。

&nbsp;&nbsp;7.在共用專案中，開啟 App.xaml.cs 專案檔案並找到 [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) 的定義，然後確保該定義已設定為連接至 Azure 中執行的行動服務。

>[AZURE.NOTE]當您使用 Visual Studio 工具將您的應用程式連接到行動服務時，此工具會分別針對個別用戶端平台需要產生共兩組 **MobileServiceClient** 定義。這時您可以順勢將使用 `#if...#endif` 包裝的 **MobileServiceClient** 定義統一至單一未包裝的定義以供這兩個應用程式版本使用，藉此簡化產生的程式碼。如果您是從 [Azure 傳統入口網站]下載快速入門應用程式，則不需要這麼做。

&nbsp;&nbsp;8.按 F5 鍵執行 Windows 市集應用程式；確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。

&nbsp;&nbsp;發生此情況是因為 App 嘗試以未驗證的使用者身分來存取行動服務，但 TodoItem 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求行動服務的資源之前必須驗證使用者。

##<a name="add-authentication"></a>將驗證新增至應用程式

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]

>[AZURE.NOTE]如果您已向行動服務註冊您的 Windows 市集應用程式套件資訊，則應該為 *useSingleSignOn* 參數提供 **true** 值來呼叫 <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> 方法。若您沒有這麼做，則每次呼叫登入方法時，您的使用者會繼續看到登入提示。

##<a name="tokens"></a>將授權權杖儲存在用戶端上

先前範例所示範的標準登入，在每次應用程式啟動時，皆需要用戶端連絡身分識別提供者和行動服務。這個方法不只效率不彰，而且如果同時有許多用戶試圖啟用您的應用程式時，還可能遇到使用量相關的問題。更好的方法就是快取行動服務傳回的驗證權杖，然後嘗試在使用提供者形式登入前先使用此方法。

>[AZURE.NOTE]無論您使用用戶端管理或服務管理驗證，皆可以快取行動服務發行的權杖。本教學課程使用服務管理驗證。

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]


## <a name="next-steps"> </a>後續步驟

在下一個[行動服務使用者的伺服器端授權][Authorize users with scripts]教學課程中，您將使用由行動服務根據經驗證的使用者而提供的使用者識別碼值，來篩選行動服務傳回的資料。

##另請參閱

+ [增強型使用者功能](https://azure.microsoft.com/blog/2014/10/02/custom-login-scopes-single-sign-on-new-asp-net-web-api-updates-to-the-azure-mobile-services-net-backend/)<br/> 您可藉由呼叫 .NET 後端中的 **ServiceUser.GetIdentitiesAsync()** 方法，來取得由行動服務中的身分識別提供者所維護的其他使用者資料。

+ [行動服務 .NET 作法概念參考資料]<br/>深入了解如何搭配 .NET 用戶端使用行動服務。


<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[開始使用行動服務]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: mobile-services-dotnet-backend-service-side-authorization.md
[JavaScript and HTML]: mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[Azure 傳統入口網站]: https://manage.windowsazure.com/
[行動服務 .NET 作法概念參考資料]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=AcomDC_0727_2016-->