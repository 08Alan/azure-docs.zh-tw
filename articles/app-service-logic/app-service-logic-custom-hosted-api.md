---
title: "在 Logic Apps 中呼叫自訂 API"
description: "將您裝載在 App Service 上的自訂 API 與邏輯應用程式一起使用"
author: stepsic-microsoft-com
manager: dwrede
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: de758467622c700eccc661bd9457dc165a84afc8


---
# <a name="using-your-custom-api-hosted-on-app-service-with-logic-apps"></a>將您裝載在 App Service 上的自訂 API 與邏輯應用程式一起使用
雖然邏輯應用程式有一系列 40 餘個各式各樣的連接器，但您可能會想要呼叫自訂的 API 以執行自己的程式碼。 是裝載您自己的自訂 Web API，最簡單且最具擴充性的方法之一是使用 App Service。 本文說明如何呼叫裝載在 App Service API 應用程式、Web 應用程式或行動應用程式中的任何 Web API。

如需將 API 建置為觸發程序或邏輯應用程式中的動作，請參閱 [這篇文章](app-service-logic-create-api-app.md)。

## <a name="deploy-your-web-app"></a>部署 Web 應用程式
首先，您必須將 API 部署為 App Service 中的 Web 應用程式。 此處的指示說明的是基本部署： [建立 ASP.NET Web 應用程式](../app-service-web/web-sites-dotnet-get-started.md)。  雖然您可以從邏輯應用程式呼叫任何 API，但是為了取得最佳體驗，我們建議您加入 Swagger 中繼資料，以便輕易地與邏輯應用程式動作整合。  您可以在 [加入 Swagger](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui)中取得詳細資訊。

### <a name="api-settings"></a>API 設定
為了讓 Logic Apps 設計工具能夠剖析您的 Swagger，請務必啟用 CORS，並設定 Web 應用程式的 APIDefinition 屬性。  這很容易就能在 Azure 入口網站中設定。  只需開啟您 Web 應用程式的 [設定] 刀鋒視窗，並在 API 區段下方，將 [API 定義] 設定為 swagger.json 檔案的 URL (這通常是 https://{name}.azurewebsites.net/swagger/docs/v1)，並針對 '*' 新增 CORS 原則，以允許來自邏輯應用程式設計工具的要求。

## <a name="calling-into-the-api"></a>呼叫 API
在 Logic Apps 入口網站中，如果您已設定 CORS 和 API 定義屬性，您應該能夠輕鬆地在您的流程中加入自訂 API 動作。  在設計工具中，您可以選擇瀏覽您的訂用帳戶網站，以列出已定義 swagger URL 的網站。  您也可以使用 HTTP + Swagger 動作來指向 swagger，並列出可用的動作和輸入。  最後，您永遠可以使用 HTTP 動作建立要求來呼叫任何 API，即使是沒有或不公開 swagger 文件的要求也一樣。

如果您想要保護 API，則有幾種不同的方式可供執行：

1. 無需變更程式碼 - Azure Active Directory 可用來保護您的 API，而不需要任何程式碼變更或重新部署。
2. 在 API 的程式碼中強制執行基本驗證、AAD 驗證或憑證驗證。

## <a name="securing-calls-to-your-api-without-a-code-change"></a>保護您 API 的呼叫而不變更程式碼
在本節中，您將建立兩個 Azure Active Directory 應用程式 – 一個用於邏輯應用程式，一個用於 Web 應用程式。  您將會使用與邏輯應用程式的 AAD 應用程式相關聯的服務主體 (用戶端識別碼和密碼)，驗證對您 Web 應用程式的呼叫。 最後，您將會在邏輯應用程式定義中納入應用程式識別碼。

### <a name="part-1-setting-up-an-application-identity-for-your-logic-app"></a>第 1 部分：設定邏輯應用程式的應用程式身分識別碼
這是邏輯應用程式用來對 Active Directory 進行驗證的項目。 您只 *需要* 為您的目錄執行此動作一次。 例如，您可以選擇讓您所有的邏輯應用程式使用相同的身分識別碼，儘管只要您願意，您也可以為每個邏輯應用程式建立唯一的身分識別碼。 您可以在 UI 中執行這項操作，或使用 PowerShell。

#### <a name="create-the-application-identity-using-the-azure-classic-portal"></a>使用 Azure 傳統入口網站建立應用程式身分識別碼
1. 瀏覽至 [Azure 傳統入口網站中的 Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) ，然後選取您要用於 Web 應用程式的目錄
2. 按一下 [應用程式]  索引標籤。
3. 在頁面底部的命令列中按一下 [新增] 
4. 為您的身分識別碼指定使用名稱，按 [下一步] 箭號
5. 在兩個文字方塊中放入格式化為網域的唯一字串，然後按一下核取記號
6. 按一下此應用程式的 [設定]  索引標籤
7. 複製 [用戶端識別碼] ，這將會用於您的邏輯應用程式
8. 在 [金鑰] 區段中按一下 [選取持續時間]，然後選擇 1 年或 2 年
9. 在畫面底部按一下 [儲存]  按鈕 (您可能需要等候數秒)
10. 現在，請確實將金鑰複製到方塊中。 這也會用於您的邏輯應用程式

#### <a name="create-the-application-identity-using-powershell"></a>使用 PowerShell 建立應用程式身分識別碼
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. 請確實複製 [租用戶識別碼]、[應用程式識別碼] 和您所使用的密碼

### <a name="part-2-protect-your-web-app-with-an-aad-app-identity"></a>第 2 部分：使用 AAD 應用程式身分識別碼保護您的 Web 應用程式
如果 Web 應用程式已部署，您可以直接在入口網站中加以啟用。 否則，您可以啟用 Azure 資源管理員部署的授權部分。

#### <a name="enable-authorization-in-the-azure-portal"></a>在 Azure 入口網站中啟用授權
1. 瀏覽至 Web 應用程式，然後在命令列中按一下 [設定]  。
2. 按一下 [授權/驗證] 。
3. 加以 [開啟] 。

此時，系統會自動為您建立應用程式。 在第 3 部分需要此應用程式的用戶端識別碼，因此您需要：

1. 移至 [Azure 傳統入口網站中的 Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) ，然後選取您的目錄。
2. 在搜尋方塊中搜尋應用程式
3. 在清單中加以點按
4. 按一下 [設定]  索引標籤
5. 您應會看見 [用戶端識別碼] 

#### <a name="deploying-your-web-app-using-an-arm-template"></a>使用 ARM 範本部署 Web 應用程式
首先，您必須為 Web 應用程式建立應用程式。 這應與用於邏輯應用程式的應用程式不同。 首先請遵循第 1 部分中的前述步驟，但是現在對於 **HomePage** 和 **IdentifierUris**，請使用 Web 應用程式的實際 https://**URL**。

> [!NOTE]
> 當您建立 Web 應用程式的應用程式時，您必須使用 [Azure 傳統入口網站途徑](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory)，因為 PowerShell Cmdlet 不會設定讓使用者登入網站的必要權限。
> 
> 

在您具備用戶端識別碼和租用戶識別碼後，請在部署範本中納入下列項目做為 Web 應用程式的子資源：

```
"resources" : [
    {
        "apiVersion" : "2015-08-01",
        "name" : "web",
        "type" : "config",
        "dependsOn" : [
            "[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
        ],
        "properties" : {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

若要自動執行部署以同時部署使用 AAD 的空白 Web 應用程式和邏輯應用程式，請按一下下列按鈕：

[![部署至 Azure](./media/app-service-logic-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

如需完整範本，請參閱 [以邏輯應用程式呼叫裝載於 App Service 上且受到 AAD 保護的自訂 API](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json)。

### <a name="part-3-populate-the-authorization-section-in-the-logic-app"></a>第 3 部分：填入邏輯應用程式中的授權區段
在 [HTTP] 動作的 [授權] 區段中：`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| 元素 | 說明 |
| --- | --- |
| 類型 |驗證類型。 針對 ActiveDirectoryOAuth 驗證，值為 ActiveDirectoryOAuth。 |
| tenant |用來識別 AD 租用戶的租用戶識別碼。 |
| audience |必要。 您要連接到的資源。 |
| clientID |Azure AD 應用程式的用戶端識別碼。 |
| secret |必要。 要求權杖之用戶端的密碼。 |

上述範本已設定此項目，但如果您要直接撰寫邏輯應用程式，您必須包含完整的授權區段。

## <a name="securing-your-api-in-code"></a>保護您在程式碼中的 API
### <a name="certificate-auth"></a>憑證驗證
您可以使用用戶端憑證來驗證對您 Web 應用程式的傳入要求。 請參閱 [如何設定 Web 應用程式的 TLS 相互驗證](../app-service-web/app-service-web-configure-tls-mutual-auth.md) ，以了解如何設定您的程式碼。

在 [授權] 區段中，您應提供：`{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`。

| 元素 | 說明 |
| --- | --- |
| 類型 |必要。 驗證類型。若為 SSL 用戶端憑證，值必須是 ClientCertificate。 |
| pfx |必要。 Base64 編碼的 PFX 檔案內容。 |
| password |必要。 存取 PFX 檔案的密碼。 |

### <a name="basic-auth"></a>基本驗證
您可以使用基本驗證 (例如使用者名稱和密碼) 來驗證傳入要求。 基本驗證是常見模式，你可以使用您用來建置應用程式的任何語言執行此驗證。

在 [授權] 區段中，您應提供：`{"type": "basic","username": "test","password": "test"}`。

| 元素 | 說明 |
| --- | --- |
| 類型 |必要。 驗證類型。 若為基本驗證，值必須是 Basic。 |
| username |必要。 要驗證的使用者名稱。 |
| password |必要。 要驗證的密碼。 |

### <a name="handle-aad-auth-in-code"></a>在程式碼中處理 AAD 驗證
根據預設，您在入口網站中啟用的 Azure Active Directory 驗證並不會執行精細的授權。 例如，它不會鎖定您的 API 給特定使用者或應用程式使用，但只是鎖定給特定租用戶。

如果您要將 API 限定給邏輯應用程式使用 (例如在程式碼中)，您可以擷取包含 JWT 的標頭，並檢查呼叫端為何，而拒絕任何不相符的要求。

再進一步，如果您想要在自己的程式碼中加以完整實作，且不利用入口網站功能，您可以參閱這篇文章： [在 Azure App Service 中使用 Active Directory 進行驗證](../app-service-web/web-sites-authentication-authorization.md)。

您仍然必須遵循前述步驟建立邏輯應用程式的應用程式身分識別碼，並用它來呼叫 API。




<!--HONumber=Nov16_HO3-->


