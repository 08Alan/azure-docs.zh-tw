<properties 
	pageTitle="適用於 Windows 服務的 Application Insights" 
	description="使用 Application Insights 分析 Windows 背景服務的使用情况和效能。" 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2016" 
	ms.author="awills"/>

# Windows 背景服務上的 Application Insights

*Application Insights 目前僅供預覽。*

[Visual Studio Application Insights](app-insights-get-started.md) 可讓您監視已部署的應用程式的使用情況和效能。

所有的 Windows 應用程式 - 包括背景服務和背景工作角色 - 都能使用 Application Insights SDK 傳送遙測至 Application Insights。您也可以將 SDK 新增至類別庫專案。

您可以選擇您想要使用哪些標準資料收集器 (例如用來監視效能計數器或相依性呼叫) 或只是使用核心 API 並撰寫您自己的遙測。

先檢查是否正在使用另一種類型的 Windows 應用程式︰

* Web 應用程式︰移至 [ASP.NET 4](app-insights-asp-net.md)、[ASP.NET 5](app-insights-asp-net-five.md)。
* [Azure 雲端服務](app-insights-cloudservices.md)
* 傳統型應用程式︰我們建議使用 [HockeyApp](https://hockeyapp.net)。使用 HockeyApp，您可以管理發佈、即時測試和使用者意見，以及監視使用情況和當機報告。您也可以[從傳統型應用程式將遙測傳送至 Application Insights](app-insights-windows-desktop.md)。 


## <a name="add"></a> 建立 Application Insights 資源


1.  在 [Azure 入口網站][portal] 中，建立新的 Application Insights 資源。針對應用程式類型，選擇 ASP.NET 應用程式。 

    ![按一下 [新增]，然後按一下 [Application Insights]](./media/app-insights-windows-services/01-new.png)


2.  取得檢測金鑰的副本。在您剛才建立的新資源之 [Essentials] 下拉式清單中尋找金鑰。關閉應用程式對應，或向左捲動至資源的概觀刀鋒視窗。

    ![按一下 [Essentials]，選取金鑰，然後按下 CTRL+C](./media/app-insights-windows-services/10.png)

## <a name="sdk"></a>在應用程式中安裝 SDK


1. 在 Visual Studio 中，編輯 Windows 應用程式專案的 NuGet 封裝。

    ![以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 封裝]](./media/app-insights-windows-services/03-nuget.png)

2. 安裝 Application Insights Windows Server 封裝：Microsoft.ApplicationInsights.WindowsServer

    ![搜尋「Application Insights」](./media/app-insights-windows-services/04-ai-nuget.png)

    *可以使用其他封裝嗎？*

    是。如果您只想要使用 API 來傳送您自己的遙測，請選擇核心 API (Microsoft.ApplicationInsights)。Windows Server 封裝會自動包含核心 API 及其他封裝，例如效能計數器收集和相依性監視。


3. 設定您的 InstrumentationKey。

    * 如果您只安裝核心 API 封裝 Microsoft.ApplicationInsights，您必須在程式碼中設定金鑰，例如在 main ()： 

    `TelemetryConfiguration.Active.InstrumentationKey = "` *您的金鑰* `";`

    如果您安裝其中一個其他封裝，您可以使用程式碼設定金鑰，或在 ApplicationInsights.config 中設定：
 
    `<InstrumentationKey>`*您的金鑰*`</InstrumentationKey>`

    如果使用 ApplicationInsights.config，請確定其在方案總管中的屬性已設定為 [建置動作] = [內容]、[複製到輸出目錄] = [複製]。

## <a name="telemetry"></a>插入遙測呼叫

使用任一個 [Application Insights API][api] 來傳送遙測。如果您使用核心 API，不會自動傳送任何遙測。一般您會使用：

* 在切換表單、頁面或索引標籤上的 `TrackPageView(pageName)`
* 其他使用者動作的 `TrackEvent(eventName)`
* 背景工作中的 `TrackMetric(name, value)`，可傳送未附加到特定事件之度量的一般報告。
* [診斷記錄][][diagnostic] 的 `TrackTrace(logEvent)`
* catch 子句中的 `TrackException(exception)`
* `Flush()` 確定所有遙測在關閉應用程式之前都已傳送。只有當您只使用核心 API (Microsoft.ApplicationInsights) 時才可以使用此選項。Web SDK 會自動實作這個行為。(如果您的應用程式會在不一定有網際網路的內容中執行，請參閱[持續性通道](#persistence-channel)。)


#### 遙測初始設定式

若要查看使用者和工作階段的計數，您可以對每個 `TelemetryClient` 執行個體設定值。或者，您可以使用遙測初始設定式來對所有用戶端執行這個加入動作：

```C#

    class UserSessionInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            telemetry.Context.User.Id = Environment.UserName;
            telemetry.Context.Session.Id = Guid.NewGuid().ToString();
        }
        
    }

    static class Program
    {
        ...
        static void Main()
        {
            TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new UserSessionInitializer());
            ...

```



## <a name="run"></a>執行專案

[使用 F5 執行應用程式](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx)並使用它，以產生一些遙測。

在 Visual Studio 中，您可以看見已傳送到的事件計數。

![](./media/app-insights-windows-services/appinsights-09eventcount.png)

事件也會出現在診斷和輸出視窗中。

## <a name="monitor"></a>查看監視資料

返回 Azure 入口網站中的應用程式分頁。

第一個事件會出現在[即時計量串流](app-insights-metrics-explorer.md#live-metrics-stream)中。


## 持續性通道 

如果您的應用程式在不一定有網際網路連線或速度很慢的地方執行，請考慮使用持續性通道，而不使用預設的記憶體中通道。

預設的記憶體中通道將會在應用程式關閉時遺失任何尚未傳送的遙測。雖然您可以使用 `Flush()` 嘗試傳送緩衝區中剩餘的任何資料，但如果沒有網際網路連線，或者如果在完成傳輸之前關閉了應用程式，它還是會遺失資料。

相較之下，持續性通道會緩衝處理檔案中的遙測，再將它傳送至入口網站。`Flush()` 可確保資料會儲存在檔案中。如果任何資料未在應用程式關閉時傳送，它會保留在檔案中。當應用程式重新啟動時，即使沒有網際網路連線，資料也會傳送。在連線可用之前，資料都會視需求累積在檔案中。

### 使用持續性通道

1. 匯入 NuGet 封裝 [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3)。
2. 在適當的初始化位置，將此程式碼納入您的應用程式中：
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. 在您的應用程式關閉之前使用 `telemetryClient.Flush()`，以確定資料已傳送至入口網站或儲存至檔案。

    請注意，flush () 對於持續性通道而言是同步的，但對其他通道而言是非同步的。

 
持續性通道最適合裝置的案例，其中應用程式所產生的事件數目相對較少，而連線通常不可靠。這個通道會先將磁碟的事件寫入到可靠的儲存空間，然後嘗試傳送它。

#### 範例

假設您想要監視未處理的例外狀況。您訂閱 `UnhandledException` 事件。在回呼中，您可以包含對排清的呼叫，以確定會保存遙測。
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

當應用程式關閉時，您會看到 `%LocalAppData%\Microsoft\ApplicationInsights` 中的檔案，其中包含壓縮的事件。
 
下次您啟動此應用程式時，通道將盡可能找出此檔案並傳送遙測至 Application Insights。

#### 測試範例

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


持續性通道的程式碼位於 [github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel) 上。


## <a name="usage"></a>後續步驟

[追蹤應用程式的使用量][knowUsers]

[擷取及搜尋診斷記錄][diagnostic]

[疑難排解][qna]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[knowUsers]: app-insights-overview-usage.md
[api]: app-insights-api-custom-events-metrics.md
[CoreNuGet]: https://www.nuget.org/packages/Microsoft.ApplicationInsights
 

<!---HONumber=AcomDC_0615_2016-->