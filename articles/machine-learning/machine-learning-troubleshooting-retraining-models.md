<properties
	pageTitle="針對 Azure Machine Learning 傳統 Web 服務的重新訓練進行疑難排解 | Microsoft Azure"
	description="找出您在為 Azure Machine Learning Web 服務重新訓練模型時所遇到的常見問題，並加以修正。"
	services="machine-learning"
	documentationCenter=""
	authors="VDonGlover"
	manager=""
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/05/2016"
	ms.author="v-donglo"/>

#針對 Azure Machine Learning 傳統 Web 服務的重新訓練進行疑難排解

## 重新訓練概觀

當您將預測性實驗部署為評分 Web 服務時，它會是靜態模型。當有新資料可用或 API 取用者有自己的資料時，模型就必須重新訓練。

如需傳統 Web 服務之重新訓練程序的完整逐步解說，請參閱[以程式設計方式重新訓練機器學習服務模型](machine-learning-retrain-models-programmatically.md)。

## 重新訓練程序

當您需要重新訓練 Web 服務時，您必須另外加入某些部分︰

* 從訓練實驗部署的 Web 服務。實驗必須將 **Web 服務輸出**模組附加至**訓練模型**模組的輸出。

	![將 Web 服務的輸出附加至訓練模型。][image1]

* 加入至評分 Web 服務的新端點。您可以透過程式設計方式，使用《以程式設計方式重新訓練機器學習服務模型》主題中所參照的範例程式碼加入端點，或是透過 Azure 傳統入口網站來加入。

接著，您可以使用訓練 Web 服務的 API 說明頁面中的範例 C# 程式碼重新訓練模型。在評估結果後，如果您感到滿意，就可以使用新加入的端點更新已訓練的模型評分 Web 服務。

準備好所有部分之後，為了重新訓練模型所必須採取的主要步驟如下︰

1.	呼叫訓練 Web 服務︰呼叫的對象為批次執行服務 (BES)，而非求回應服務 (RRS)。您可以使用 API 說明頁面中的範例 C# 程式碼來進行呼叫。
2.	尋找「BaseLocation」、「RelativeLocation」和「SasBlobToken」的值︰這些值會在您對訓練 Web 服務的呼叫輸出中傳回。![顯示重新訓練範例的輸出和 BaseLocation、RelativeLocation 及 SasBlobToken 的值。][image6]
3.	從評分 Web 服務使用訓練好的新模型更新加入的端點︰使用《以程式設計方式重新訓練機器學習服務模型》所提供的範例程式碼，從評分 Web 服務使用訓練好的新模型更新加入到評分模型的新端點。

## 常見障礙
### 檢查是否有正確的 PATCH URL

所使用的 PATCH URL 必須是與加入到評分 Web 服務的新評分端點相關聯的 URL。有兩種方式可取得 PATCH URL：

選項 1︰使用 C#

若要取得正確的 PATCH URL：

1.	執行 [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) 範例程式碼。
2.	從 AddEndpoint 的輸出中找出「HelpLocation」值並複製 URL。

	![addEndpoint 範例之輸出中的 HelpLocation。][image2]

3.	將此 URL 貼到瀏覽器中，瀏覽到提供 Web 服務說明連結的頁面。
4.	按一下 [更新資源] 連結以開啟修補說明頁面。

選項 2︰使用 Azure 入口網站

1.	登入傳統 [Azure 入口網站](https://manage.windowsazure.com)。
2.	開啟 [機器學習服務] 索引標籤。![機器學習服務索引標籤。][image4]
3.	依序按一下工作區名稱和 [Web 服務]。
4.	按一下您要使用的評分 Web 服務 (如果您沒有修改 Web 服務的預設名稱，它的結尾通常是 [Scoring Exp.])。
5.	按一下 [新增端點]
6.	在新增端點之後，按一下端點名稱。然後，按一下 [更新資源] 以開啟修補說明頁面。

![新端點的儀表板。][image3]

PATCH 說明頁面包含必須使用的 PATCH URL，並提供可用來呼叫它的範例程式碼。

![修補 URL。][image5]


### 檢查正在更新的評分端點是否正確
* 請勿修補訓練 Web 服務︰修補作業必須是對評分 Web 服務來執行。
* 請勿修補 Web 服務上的預設端點︰修補作業必須是對所加入的新的評分 Web 服務端點來執行。

檢查端點所加入到的 Web 服務是否正確

您用來重新訓練模型的端點必須位於評分 Web 服務上，而非訓練 Web 服務上。您可以造訪 Azure 傳統入口網站來確認端點位於哪個 Web 服務上。

1.	登入傳統 [Azure 入口網站](https://manage.windowsazure.com)。
2.	開啟 [機器學習服務] 索引標籤。![Machine Learning 工作區 UI。][image4]
3.	選取您的工作區。
4.	按一下 [Web 服務]。
5.	選取您的預測性 Web 服務。
6.	確認新的端點已加入到 Web 服務。

### 檢查 Web 服務所在的工作區以確保它位於正確區域

1.	登入傳統 [Azure 入口網站](https://manage.windowsazure.com)。
2.	在功能表中選取 [機器學習服務]。![機器學習服務區域 UI。][image4]
3.	確認工作區的所在位置。


<!-- Image Links -->

[image1]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/machine-learning-troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-machine-learning-tab.png
[image5]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/machine-learning-troubleshooting-retraining-a-model/retraining-output.png

<!---HONumber=AcomDC_0810_2016---->