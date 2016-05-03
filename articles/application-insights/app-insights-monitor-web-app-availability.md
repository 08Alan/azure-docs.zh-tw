<properties
	pageTitle="監視任何網站的可用性和回應性 | Microsoft Azure"
	description="在 Application Insights 中設定 Web 測試。如果網站無法使用或回應緩慢，將收到警示。"
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/18/2016"
	ms.author="awills"/>

# 監視任何網站的可用性和回應性

開發 Web 應用程式之後，您可以設定 Web 測試來監視其可用性和回應性。Application Insights 會定期傳送來自全球各地的 Web 要求，如果應用程式回應太慢或完全沒有回應，則會警告您。

![Web 測試範例](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

您可以為公用網際網路可存取的任何 HTTP 或 HTTPS 端點設定 Web 測試。

Web 測試可分為兩種：

* [URL Ping 測試](#set-up-a-url-ping-test)：您可以在 Azure 入口網站中建立的簡單測試。
* [多步驟 Web 測試](#multi-step-web-tests)：您可以在 Visual Studio Ultimate 或 Visual Studio Enterprise 中建立並上傳至入口網站的測試。

每個應用程式資源最多可以建立 10 個 Web 測試。


## 設定 URL Ping 測試

### <a name="create"></a>1.建立新的資源？

如果您已經為這個應用程式[設定 Application Insights 資源][start]，而且想要在相同位置中查看可用性資料，請略過此步驟。

註冊 [Microsoft Azure](http://azure.com)，移至 [Azure 入口網站](https://portal.azure.com)，然後建立新的 Application Insights 資源。

![New > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

新資源的 [概觀] 刀鋒視窗隨即開啟。若要在 [Azure 入口網站](https://portal.azure.com)中隨時找到此資源，請按一下 [瀏覽]。

### <a name="setup"></a>2.建立 Web 測試

在您的 Application Insights 資源中，尋找 [可用性] 圖格。按一下以開啟應用程式的 [Web 測試] 刀鋒視窗，然後新增 Web 測試。

![Fill at least the URL of your website](./media/app-insights-monitor-web-app-availability/13-availability.png)

- **URL** 必須可在公用網際網路上顯示。它可以包含查詢字串 - 例如，您可以訓練一下您的資料庫。如果 URL 解析為重新導向，我們將會跟隨它，最多 10 個重新導向。
- **剖析相依要求**：測試時會要求映像、指令碼、樣式檔和頁面的其他資源。如果無法在逾時內為整個測試成功下載所有這些資源，則測試將會失敗。
- **啟用重試**：當測試失敗時，就會在短時間內進行重試。只有在連續三次重試失敗後，才會回報失敗。後續測試則會以一般測試頻率執行。重試會暫時停止，直到下次成功為止。此規則可個別套用在每個測試位置。(我們建議使用這個設定。平均來說，大約 80%的失敗會在重試後消失。)
- **測試頻率**：設定從每個測試位置執行測試的頻率。若頻率為 5 分鐘並有五個測試位置，將平均每一分鐘測試您的網站。
- **測試位置**是我們的伺服器將 Web 要求傳送至您的 URL 的位置。請選擇多個位置，以便區分網站問題與網路問題。您最多可以選取 16 個位置。

- **成功準則**：

    **測試逾時**：減少此值以警示回應變慢。如果未在這段時間內收到您網站的回應，則測試會視為失敗。如果已選取 [剖析相依要求]，則必須在這段時間內收到所有映像、樣式檔、指令碼和其他相依資源。

    **HTTP 回應**：視為成功的回覆狀態碼。200 是表示已傳回標準 Web 網頁的代碼。

    **內容比對**：字串，例如「歡迎！」 我們將測試它出現在每個回應中。必須是單純字串，不含萬用字元。別忘了，如果頁面內容變更，則可能需要更新。


- 如果五分鐘內有三個位置發生失敗，則依預設會將 [警示]傳送給您。某個位置的失敗很可能是網路問題，而不是您的網站發生問題。但您可以將臨界值變更為更敏感或更不敏感，也可以變更應該將電子郵件傳送給哪一個人。

    您可以設定會在產生警示時呼叫的 [Webhook](../azure-portal/insights-webhooks-alerts.md)。

#### 測試更多 URL

加入更多測試。例如，除了測試首頁，您也可以測試搜尋的 URL 來確定資料庫在執行中。


### <a name="monitor"></a>3.檢視可用性報告

1-2 分鐘後，在 [可用性/Web 測試] 刀鋒視窗上按一下 [重新整理]。(它不會自動重新整理)。

![Summary results on the home blade](./media/app-insights-monitor-web-app-availability/14-availSummary.png)

按一下上方摘要圖表上的任何橫條，以取得該時段的更詳細檢視。

這些圖表會結合此應用程式的所有 Web 測試的結果。

#### 網頁的元件

在測試中會要求您要測試的網頁的影像、樣式表、指令碼及其他靜態元件。

記錄的回應時間是所有元件完成載入所花費的時間。

如果有任何元件無法載入，測試會標示為失敗。

## <a name="failures"></a>如果您看到失敗...

按一下一個紅點。

![按一下一個紅點](./media/app-insights-monitor-web-app-availability/14-availRedDot.png)

或者，向下捲動並按一下您看到少於 100% 成功的測試。

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/15-webTestList.png)

這會顯示該測試的結果。

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/16-1test.png)

若是從數個位置執行測試，請挑選其中一個結果小於 100% 的位置。

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/17-availViewDetails.png)


向下捲動至 [失敗的測試] 並挑選結果。

按一下結果以在入口網站中評估，並查看失敗的原因。

![Webtest run result](./media/app-insights-monitor-web-app-availability/18-availDetails.png)


或者，您可以下載結果檔案，然後在 Visual Studio 中檢查。


看起來正常，但回報為失敗？ 請檢查所有映像、指令碼、樣式表和頁面載入的任何其他檔案。If any of them fails, the test will be reported as failed, even if the main html page loads OK.



## 多重步驟 Web 測試

您可以監視涉及一連串 URL 的案例。例如，如果您正在監視銷售網站，您可以測試將項目加入購物車正確運作。

若要建立多重步驟測試，您可以使用 Visual Studio 來記錄案例，然後將記錄結果上傳至 Application Insights。Application Insights 將會不時地重新執行案例，並確認回應。

請注意，您無法在測試中使用編碼的函式：做為 .webtest 檔案中的指令碼必須包含案例步驟。

#### 1\.記錄案例

使用 Visual Studio Enterprise 或 Ultimate 來記錄 Web 工作階段。

1. 建立 Web 效能測試專案。

    ![在 Visual Studio 中，從「Web 效能」和「負載測試」範本建立新的專案。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

2. 開啟 .webtest 檔案，並開始記錄。

    ![開啟 .webtest 檔案，然後按一下 [記錄]。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)

3. 執行您想要在測試中模擬的使用者動作：開啟網站、將產品加入購物車等等。然後停止測試。

    ![在 Internet Explorer 中執行 Web 測試錄製器。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    不要讓案例太長。以 100 個步驟和 2 分鐘為限。

4. 編輯本測試以進行下列事項：
 - 加入驗證以檢查收到的文字和回應碼。
 - 移除任何多餘的互動。您也可以移除圖片或廣告或追蹤網站的相依要求。

    請記住您只能編輯此測試指令碼 - 您無法加入自訂程式碼或呼叫其他 Web 測試。請勿在此測試中插入迴圈。您可以使用標準 Web 測試的外掛程式。

5. 在 Visual Studio 中執行測試，以確定可以運作。

    Web 測試執行器會開啟網頁瀏覽器，並重複您已記錄的動作。請確定運作如您所預期。

    ![在 Visual Studio 中，開啟 .webtest 檔案，並按一下 [執行]。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)


#### 2\.將 Web 測試上傳至 Application Insights

1. 在 Application Insights 入口網站中，建立新的 Web 測試。

    ![在 [Web 測試] 刀鋒視窗中，選擇 [加入]。](./media/app-insights-monitor-web-app-availability/16-another-test.png)

2. 選取多重步驟測試，並上傳 .webtest 檔案。

    ![選取 [多步驟 Web 測試]。](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    以進行 ping 測試的相同方式設定測試位置、頻率及警示參數。

就像在單一 url 測試中一樣，以相同的方式檢視您的測試結果和任何失敗。

失敗的常見原因是在測試執行太長。不可執行超過兩分鐘。

別忘了，頁面的所有資源必須正確載入，測試才能成功 (包括指令碼、樣式表、映像等等)。

請注意，Web 測試必須完全包含在 .webtest 檔案中：您無法在測試中使用編碼的函式。


### 將時間和隨機數字插入多重步驟測試中

假設您要測試的工具會從外部來源取得與時間相關的資料 (例如股票)。當您記錄 Web 測試時，您必須使用特定的時間，但您將它們設為測試的參數：StartTime 和 EndTime。

![具有參數的 Web 測試。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

當您執行測試時，希望 EndTime 永遠為目前時間，而 StartTime 為 15 分鐘前。

Web 測試外掛程式提供這種作法。

1. 針對您想要的每個變數參數值，各加入一個 Web 測試外掛程式。在 Web 測試工具列中，選擇 [加入 Web 測試外掛程式]。

    ![選擇 [加入 Web 測試外掛程式]，然後選取類型。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    在此範例中，我們將使用兩個日期時間外掛程式執行個體。一個執行個體設定為 "15 minutes ago"，另一個則設定為 "now"。

2. 開啟每個外掛程式的屬性。為屬性命名，然後將它設為使用目前時間。對其中一個，設定 [加入分鐘] = -15。

    ![設定 [名稱]、[使用目前時間] 和 [加入分鐘]。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)

3. 在 Web 測試參數中，使用 {{plug-in name}} 來參考外掛程式名稱。

    ![在測試參數中，使用 {{plug-in name}}。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

現在將您的測試上傳至入口網站。在每次執行測試時，它會使用動態值。

## 處理登入

如果使用者登入您的應用程式，有許多選項可模擬登入，以便在登入背後測試頁面。您使用的方法取決於應用程式所提供的安全性類型。

在所有情況下，您應該只建立測試用途的帳戶。可能的話，請將帳戶限定為只有唯讀權限。

* 簡單的使用者名稱和密碼：只以一般方式記錄 Web 測試。先刪除 Cookie。
* SAML 驗證。為此，您可以使用適用於 Web 測試的 SAML 外掛程式。
* 用戶端密碼：如果您的應用程式牽涉到用戶端密碼的登入路由，請使用此方法。Azure Active Directory 提供這種方法。 
* 開放驗證：例如，使用您的 Microsoft 或 Google 帳戶登入。許多使用 OAuth 的應用程式都提供替代用戶端密碼，第一個技巧就是調查這一點。如果您的測試必須使用 OAuth 登入，則常用的方式是：
 * 使用 Fiddler 等工具來檢查網頁瀏覽器、驗證網站及您的應用程式之間的流量。 
 * 使用不同的電腦或瀏覽器，或以較長時間間隔 (讓權杖過期) 執行兩次以上的登入。
 * 藉由比較不同的工作階段，識別從驗證網站傳回的權杖，登入之後此權杖會傳遞至您的應用程式伺服器。 
 * 使用 Visual Studio 記錄 Web 測試。 
 * 將權杖參數化，當驗證器傳回權杖時設定參數，然後在查詢網站時使用參數。(Visual Studio 會嘗試將測試參數化，但不會正確地將權杖參數化。)


## <a name="edit"></a> 編輯或停用測試

開啟個別測試來編輯或停用。

![Edit or disable a web test](./media/app-insights-monitor-web-app-availability/19-availEdit.png)

當您在服務上進行維護時，您可能會想要停用 Web 測試。

## 自動化

* [使用 PowerShell 指令碼自動設定 Web 測試](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)。 
* 設定會在產生警示時呼叫的 [Webhook](../azure-portal/insights-webhooks-alerts.md)。

## 有疑問嗎？ 有問題嗎？

* 可以從我的 Web 測試呼叫程式碼嗎？

    否。測試步驟必須在 .webtest 檔案中。而且您不能呼叫其他 Web 測試或使用迴圈。但是這裡有一些您會覺得有用的外掛程式。

* 是否支援 HTTPS？

    我們目前支援 SSL 3.0 和 TLS 1.0。

* 「Web 測試」和「可用性測試」之間有任何差異嗎？

    我們會交替使用這兩個詞彙。

* 我想要在位於防火牆後面執行的內部伺服器上使用可用性測試。

    設定防火牆以允許來自本文結尾之清單中的 IP 位址所發出的要求。

## <a name="video"></a>影片

> [AZURE.VIDEO monitoring-availability-with-application-insights]

## <a name="next"></a>接續步驟

[搜尋診斷記錄][diagnostic]

[疑難排解][qna]


## Web 測試的 IP 位址

如果您需要開啟防火牆以允許 Web 測試，以下是最新的 IP 位址清單。這份清單可能會隨時變更。

開啟連接埠 80 (http) 和 443 (https)。

```

213.199.178.54
213.199.178.55
213.199.178.56
213.199.178.61
213.199.178.57
213.199.178.58
213.199.178.59
213.199.178.60
213.199.178.63
213.199.178.64
207.46.98.158
207.46.98.159
207.46.98.160
207.46.98.157
207.46.98.152
207.46.98.153
207.46.98.156
207.46.98.162
207.46.98.171
207.46.98.172
65.55.244.40
65.55.244.17
65.55.244.42
65.55.244.37
65.55.244.15
65.55.244.16
65.55.244.44
65.55.244.18
65.55.244.46
65.55.244.47
207.46.14.60
207.46.14.61
207.46.14.62
207.46.14.55
207.46.14.63
207.46.14.64
207.46.14.51
207.46.14.52
207.46.14.56
207.46.14.65
157.55.14.60
157.55.14.61
157.55.14.62
157.55.14.47
157.55.14.64
157.55.14.65
157.55.14.43
157.55.14.44
157.55.14.49
157.55.14.50
65.54.66.56
65.54.66.57
65.54.66.58
65.54.66.61
207.46.71.54
207.46.71.52
207.46.71.55
207.46.71.38
207.46.71.51
207.46.71.57
207.46.71.58
207.46.71.37
202.89.228.67
202.89.228.68
202.89.228.69
202.89.228.57
65.54.78.49
65.54.78.50
65.54.78.51
65.54.78.54
94.245.82.32
94.245.82.33
94.245.82.37
94.245.82.38
94.245.72.44
94.245.72.45
94.245.72.46
94.245.72.49
207.46.56.57
207.46.56.58
207.46.56.59
207.46.56.67
207.46.56.61
207.46.56.62
207.46.56.63
207.46.56.64
65.55.82.84
65.55.82.85
65.55.82.86
65.55.82.81
65.55.82.87
65.55.82.88
65.55.82.89
65.55.82.90
65.55.82.91
65.55.82.92
94.245.78.40
94.245.78.41
94.245.78.42
94.245.78.45
70.37.147.43
70.37.147.44
70.37.147.45
70.37.147.48
94.245.66.43
94.245.66.44
94.245.66.45
94.245.66.48

```


<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md

<!---HONumber=AcomDC_0427_2016-->