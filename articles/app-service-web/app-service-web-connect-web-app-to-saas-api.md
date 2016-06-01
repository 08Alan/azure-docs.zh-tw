<properties 
	pageTitle="在 Azure App Service 中將 Web 應用程式連接至 API 應用程式" 
	description="本教學課程示範如何透過 Azure App Service 中裝載的 ASP.NET Web 應用程式使用 API 應用程式。" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="syntaxc4" 
	manager="yochayk" 
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na" 
	ms.date="02/26/2016"
	ms.author="cfowler"/>

# 在 Azure App Service 中將 Web 應用程式連接至 API 應用程式

本教學課程示範如何透過 [App Service](https://azure.microsoft.com/services/app-service/) 中裝載的 ASP.NET Web 應用程式使用 API 應用程式。

## 必要條件

本教學課程是根據[建立 Azure API 應用程式](../app-service-api/app-service-api-dotnet-get-started.md)所建置。

## 在 Visual Studio 中建立 ASP.NET MVC 應用程式

1. 開啟 Visual Studio。使用 [ **新專案**] 對話方塊，加入新的 [**ASP.NET Web 應用程式**]。按一下 [確定]。

	![檔案 > 新增 > Web > ASP.NET Web 應用程式](./media/app-service-web-connect-web-app-to-saas-api/1-Create-New-MVC-App-For-Consumption.png)

1. 選取 [**MVC**] 範本。按一下 [**變更驗證**]，並選取 [**不驗證**]，然後按兩次 [**確定**]。

	![新增 ASP.NET 應用程式](./media/app-service-web-connect-web-app-to-saas-api/2-Change-Auth-To-No-Auth.png)

1. 在 [方案總管] 中，於新建立的 Web 應用程式專案上按一下滑鼠右鍵，然後選取 [新增] > [REST API 用戶端...]。

	![新增 Azure API 應用程式參考...](./media/app-service-web-connect-web-app-to-saas-api/3-Add-Azure-API-App-SDK.png)

1. 在 [加入 REST API 用戶端] 中，選取從 Microsoft Azure API 應用程式下載，然後按一下瀏覽。選取您要連結的 API 應用程式。

	![選取現有的 API 應用程式](./media/app-service-web-connect-web-app-to-saas-api/4-Add-Azure-API-App-SDK-Dialog.png)

	>[AZURE.NOTE] 將會從 Swagger API 端點自動產生連接到 API 應用程式的用戶端程式碼。

1. 若要利用產生的 API 程式碼，請開啟 HomeController.cs 檔案，並以下列程式碼取代 `Contact` 動作：

	    public async Task<ActionResult> Contact()
        {
            ViewBag.Message = "Your contact page.";

            var contacts = new ContactsList12242015();
            var contactList = await contacts.Contacts.GetAsync();
            
            return View(contactList);
        }

	![HomeController.cs 程式碼更新](./media/app-service-web-connect-web-app-to-saas-api/5-Write-Code-Which-Leverages-Swagger-Generated-Code.png)

1. 使用下面的程式碼來更新 `Contact` 檢視，以反映連絡人動態清單：
	<pre>// Add to the very top of the view file
	@model IList&lt;MyContactsList.Web.Models.Contact>
	
	// Replace the default email addresses with the following
	&lt;h3>Public Contacts&lt;/h3>
	&lt;ul>
	    @foreach (var contact in Model)
	    {
	        &lt;li>&lt;a href="mailto:@contact.EmailAddress">@contact.Name &amp;lt;@contact.EmailAddress&amp;gt;&lt;/a>&lt;/li>
	    }
	&lt;/ul> 
	</pre>

	![Contact.cshtml 程式碼更新](./media/app-service-web-connect-web-app-to-saas-api/6-Update-View-To-Reflect-Changes.png)

## 將 Web 應用程式部署到應用程式服務中的 Web 應用程式

請遵循[如何部署 Azure Web 應用程式中的指示](web-sites-deploy.md)。

>[AZURE.NOTE] 如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
 

<!---HONumber=AcomDC_0518_2016-->