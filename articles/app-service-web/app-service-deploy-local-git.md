<properties
	pageTitle="本機 Git 部署至 Azure App Service"
	description="了解如何啟用本機 Git 部署至 Azure App Service。"
	services="app-service"
	documentationCenter=""
	authors="dariagrigoriu"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/13/2016"
	ms.author="dariagrigoriu"/>
    
# 本機 Git 部署至 Azure App Service

本教學課程會示範如何將應用程式從本機電腦上的 Git 儲存機制部署到 [Azure App Service]。App Service 支援使用這個方法和 [Azure 入口網站]的 [本機 Git] 部署選項。本文中描述的許多 Git 命令，會在使用[這裡](app-service-web-get-started.md)描述的[Azure 命令列工具介面]來建立 App Service 應用程式時自動執行。

## 必要條件

若要完成本教學課程，您需要：

- Git。您可以在[這裡](http://www.git-scm.com/downloads)下載安裝二進位檔。  
- Git 基本知識。
- Microsoft Azure 帳戶。如果您沒有這類帳戶，可以[申請免費試用](https://azure.microsoft.com/pricing/free-trial)，或是[啟用自己的 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)。

>[AZURE.NOTE] 如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期的入門應用程式。不需要信用卡；無需承諾。

## <a id="Step1"></a>步驟 1：建立本機儲存機制

請執行下列工作以建立新的 Git 儲存機制。

1. 啟動命令列工具，例如 **GitBash** (Windows) 或 **Bash** (Unix Shell)。在 OS X 系統上，您可以透過 **[終端機]** 應用程式來存取命令列。

2. 瀏覽至部署內容所在的目錄。

3. 使用下列命令來初始化新的 Git 儲存機制：

		git init

## <a id="Step2"></a>步驟 2︰認可內容

App Service 支援以各種程式設計語言建立的應用程式。

1. 如果您的儲存機制已經包含內容，請略過這點，並移至下面的第 2 點。如果您的儲存機制尚未包含內容，請只要填入靜態的 .html 檔案，如下所示︰ 

    - 使用文字編輯器，在 Git 儲存機制的根目錄建立新檔案 **index.html**。
    - 在 index.html 檔案中加入並儲存下列文字內容：*Hello Git!*
        
2. 從命令列，驗證您位在 Git 儲存機制的根目錄下。然後使用下列命令將檔案加入儲存機制中：

		git add -A 

4. 接著，使用下列命令來認可對儲存機制的變更：

		git commit -m "Hello Azure App Service"

## <a id="Step3"></a>步驟 3︰啟用 App Service 應用程式儲存機制

執行下列步驟以啟用 App Service 應用程式的 Git 儲存機制。

1. 登入 [Azure 入口網站]。

2. 在 App Service 應用程式的刀鋒視窗中，按一下 [設定] > [部署來源]。依序按一下 [選擇來源]、[本機 Git 儲存機制] 以及 [確定]。

	![本機 Git 儲存機制](./media/app-service-deploy-local-git/local_git_selection.png)

3. 如果這是您第一次在 Azure 中設定儲存機制，就需要為它建立登入認證。您將使用這些認證來登入 Azure 儲存機制，並推播來自您本機 Git 儲存機制的變更。從應用程式的刀鋒視窗中，按一下 [設定] > [部署認證]，然後設定您的部署使用者名稱和密碼。完成後，按一下 [儲存]。

	![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a id="Step4"></a>步驟 4：部署專案

使用下列步驟，使用本機 Git 將應用程式發佈至 App Service。

1. 在 Azure 入口網站上的應用程式刀鋒視窗中，按一下 [Git URL] 的 [設定] > [屬性]。

	![](./media/app-service-deploy-local-git/git_url.png)

	**Git URL** 是從本機儲存機制部署的遠端參考。在下列步驟中，您將使用此 URL。

2. 使用命令列驗證您位在 Git 儲存機制的根目錄。

3. 使用 `git remote` 加入從步驟 1 的 **Git URL** 中所列的遠端參考。您的命令將類似以下範例：

		git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git         
    > [AZURE.NOTE] **remote** 命令會將指定的參考新增至遠端儲存機制。在此範例中，它會為您 Web 應用程式的儲存機制建立名為 'azure' 的參考。

4. 使用剛建立的新 **zure** 遠端將您的內容推送至 App Service。

		git push azure master

	系統將會提示您輸入在 Azure 入口網站中重設部署認證時所建立的密碼。輸入密碼 (請注意，當您輸入密碼時，Gitbash 不會對主控台回應星號)。
       
5. 回到 Azure 入口網站中的應用程式。最近推送的記錄項目應該會顯示在 [部署] 刀鋒視窗中。

	![](./media/app-service-deploy-local-git/deployment_history.png)

6. 按一下應用程式刀鋒視窗頂端的 [瀏覽] 按鈕確認是否已部署內容。
    
## <a id="Step5"></a>疑難排解

使用 Git 發佈至 Azure 的 App Service 應用程式時，下列是經常會遇到的錯誤或問題：

****

**徵兆**：無法存取 '[siteURL]'：無法連接至 [scmAddress]

**原因**：如果應用程式尚未啟動並執行，就會發生這個錯誤。

**解決方式**：在 Azure 入口網站中啟動應用程式。除非應用程式正在執行，否則 Git 部署將無法運作。


****

**徵兆**：無法解析主機 'hostname'

**原因**：如果建立 'azure' 遠端時所輸入的位址資訊不正確，便有可能發生此錯誤。

**解決方式**：使用 `git remote -v` 命令，列出所有遠端以及相關聯的 URL。驗證 'azure' 遠端的 URL 是否正確。如有需要，移除此遠端並使用正確的 URL 重新建立。

****

**徵兆**：通常沒有參考且沒有指定；不執行任何動作。或許您應該指定分支，例如 'master'。

**原因**：執行 git 推送操作時，如果您沒有指定分支，且沒有設定 Git 所使用的 push.default 值，便有可能發生此錯誤。

**解決方式**：指定主要分支，重新執行推送操作。例如：

	git push azure master

****

**徵兆**：src refspec [branchname] 沒有任何符合項目。

**原因**：如果您嘗試在 'azure' 遠端上推送至除了主要以外的分支，便有可能發生此錯誤。

**解決方式**：指定主要分支，重新執行推送操作。例如：

	git push azure master

****

**徵兆**：錯誤 - 對遠端儲存機制認可變更，但您的 Web 應用程式未更新。

**原因**：如果您打算部署包含 package.json 檔案的 Node.js 應用程式，但該檔案指出需要額外的模組，便有可能發生此錯誤。

**解決方式**：在發生此錯誤之前應該會有記錄其他包含 'npm ERR!' 的訊息，這些訊息可提供其他關於此失敗的內容。下列是此錯誤的已知原因及其對應的 'npm ERR!' 訊息：

* **格式錯誤的 package.json 檔案**：npm ERR! 無法讀取相依性。

* **原生模組沒有適用於 Windows 的二進位檔發佈**：

	* npm ERR! `cmd "/c" "node-gyp rebuild"` failed with 1

		或

	* npm ERR! [modulename@version] preinstall: `make || gmake`


## 其他資源

* [Git 文件](http://git-scm.com/documentation)
* [專案 Kudu 文件](https://github.com/projectkudu/kudu/wiki)
* [持續部署至 Azure App Service](app-service-continous-deployment.md)
* [如何使用適用於 Azure 的 PowerShell](../powershell-install-configure.md)
* [如何使用 Azure 命令列介面](../xplat-cli-install.md)

[Azure App Service]: https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/
[Azure Developer Center]: http://azure.microsoft.com/develop/overview/
[Azure 入口網站]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Azure 命令列工具介面]: https://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart

<!---HONumber=AcomDC_0615_2016-->