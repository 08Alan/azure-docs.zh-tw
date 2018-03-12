---
title: "部署 Azure Machine Learning 服務 (預覽) 的模型教學課程 | Microsoft Docs"
description: "這個完整的教學課程會示範如何使用 Azure Machine Learning 服務 (預覽) 端對端。 這是第三部分，會討論部署模型。"
services: machine-learning
author: raymondl
ms.author: raymondl, j-martens, aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/28/2018
ms.openlocfilehash: 761e7193cc64699e8aa25a1fd625ba45f65eed88
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-classify-iris-part-3-deploy-a-model"></a>教學課程：分類鳶尾花第 3 部分：部署模型
Azure Machine Learning (預覽) 是一套整合的端對端資料科學以及進階分析解決方案，可供專業資料科學家使用。 資料科學家可用來以雲端規模準備資料、開發測試及部署模型。

本教學課程是**三部分系列的第三部分**。 在本教學課程中，您可使用 Machine Learning (預覽) 來：

> [!div class="checklist"]
> * 找出模型檔案。
> * 產生評分指令碼和結構描述檔案。
> * 準備環境。
> * 建立即時 Web 服務。
> * 執行即時 Web 服務。
> * 檢查輸出 Blob 資料。 

本教學課程使用不受時間影響的[鳶尾花資料集](https://en.wikipedia.org/wiki/iris_flower_data_set)。 螢幕擷取畫面是 Windows 專屬，但是 Mac OS 體驗幾乎完全相同。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="prerequisites"></a>先決條件
完成本教學課程系列的前兩個部分：

   * 遵循[資料準備教學課程](tutorial-classifying-iris-part-1.md)來建立 Machine Learning 資源，並安裝 Azure Machine Learning Workbench 應用程式。
   * 遵循[建置模型教學課程](tutorial-classifying-iris-part-2.md)，在 Machine Learning 中建置羅吉斯迴歸模型。

您需要在本機安裝並執行 Docker 引擎。 或者，您可以在 Azure 中部署至 Azure 容器服務叢集。

## <a name="download-the-model-pickle-file"></a>下載模型序列化檔案
在教學課程的上一個部分中，**iris_sklearn.py** 指令碼是在 Machine Learning Workbench 本機執行。 該動作會使用受歡迎的 Python 物件序列化套件 [pickle](https://docs.python.org/2/library/pickle.html) 來序列化羅吉斯迴歸模型。 

1. 開啟 Machine Learning Workbench 應用程式。 然後開啟您在本教學課程系列的前一個部分中所建立的 **myIris** 專案。

2. 開啟專案後，請選取左窗格上的 [檔案] 按鈕 (資料夾圖示)，在您的專案資料夾中開啟檔案清單。

3. 選取 **iris_sklearn.py** 檔案。 Python 程式碼隨即會在 Workbench 內新的文字編輯器索引標籤中開啟。

4. 檢閱 **iris_sklearn.py** 檔案來查看序列化檔案產生的位置。 選擇 Ctrl+F 來開啟 [尋找] 對話方塊，然後在 Python 程式碼中尋找字組 **pickle**。

   這個程式碼片段會示範 pickle 輸出檔案產生的方式。 磁碟上的輸出 pickle 檔案名為 **model.pkl**。 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. 在先前執行的輸出檔案中，找出模型序列化檔案。
   
   執行 **iris_sklearn.py** 指令碼時，模型檔案已使用 **model.pkl** 名稱寫入 **outputs** 資料夾。 此資料夾位於您選擇執行指令碼的執行環境中，而不是在您的本機專案資料夾中。 
   
   a. 若要找出檔案，請選取左窗格上的 [執行] 按鈕 (時鐘圖示)，以開啟 [所有執行] 清單。 

   b. [所有執行] 索引標籤隨即開啟。 在執行的資料表中，選取最近執行的其中一個，其目標為 **local** 且指令碼名稱為 **iris_sklearn.py**。 

   c. [執行屬性] 窗格隨即開啟。 在窗格的右上角區段中，注意 [輸出] 區段。

   d. 若要下載 pickle 檔案，請選取 **model.pkl** 檔案旁的核取方塊，然後選取 [下載]。 請將檔案儲存到您專案資料夾的根目錄。 後續步驟中需要檔案。

   ![下載 pickle 檔案](media/tutorial-classifying-iris/download_model.png)

   在[如何讀取及寫入大型資料檔案](how-to-read-write-files.md)中深入了解 `outputs` 資料夾。

## <a name="get-the-scoring-script-and-schema-files"></a>取得評分指令碼和結構描述檔案
若要部署 Web 服務以及模型檔案，您也需要評分指令碼， 以及 Web 服務輸入資料的選擇性結構描述。 評分指令碼會從目前資料夾載入 **model.pkl** 檔案，並使用它來產生新預測的鳶尾花類別。

1. 開啟 Machine Learning Workbench 應用程式。 然後開啟您在本教學課程系列的前一個部分中所建立的 **myIris** 專案。

2. 開啟專案後，請選取左窗格上的 [檔案] 按鈕 (資料夾圖示)，在您的專案資料夾中開啟檔案清單。

3. 選取 **score_iris.py** 檔案。 Python 指令碼隨即開啟。 這個檔案會用作評分檔案。

   ![評分檔案](media/tutorial-classifying-iris/model_data_collection.png)

4. 若要取得結構描述檔案，請執行指令碼。 選取命令列中的 **local** 環境和 **score_iris.py** 指令碼，然後選取 [執行]。 

5. 此指令碼會在 **Outputs**區段中建立 JSON 檔案，它會擷取模型所需的輸入資料結構描述。

6. 請注意 [專案儀表板] 窗格右邊的 [作業] 窗格。 等候最新的 **score_iris.py** 作業顯示綠色 [已完成] 狀態。 然後選取最新作業執行的超連結 **score_iris.py [1]**，以查看來自 **score_iris.py** 執行的執行詳細資料。 

7. 在 [執行屬性] 頁面上，於 [輸出] 區段中，選取新建立的 **service_schema.json** 檔案。 選取檔案名稱旁邊的核取方塊，然後選取 [下載]。 將檔案儲存到您的專案根資料夾。

8. 返回您開啟 **score_iris.py**指令碼的前一個索引標籤。 使用資料收集可讓您從 Web 服務擷取模型輸入和預測。 下列步驟是資料收集特別有趣的方面。

9. 請在包含模型資料收集功能的 **ModelDataCollector**，因為頂端檢閱檔案匯入類別的程式碼：

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. 在具現化 **ModelDataCollector** 的 **init()** 函式中檢閱下列幾行程式碼：

      ```python
      global inputs_dc, prediction_dc
      inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
      prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
      ```

11. 在收集輸入和預測資料的 **run(input_df)** 函式中檢閱下列幾行程式碼：

      ```python
      global clf2, inputs_dc, prediction_dc
      inputs_dc.collect(input_df)
      prediction_dc.collect(pred)
      ```

現在您已準備好要準備讓您的環境將模型作業化。



## <a name="prepare-to-operationalize-locally"></a>準備在本機作業化
使用_本機模式_部署，於本機電腦上的 Docker 容器中執行。

您可以對開發和測試使用_本機模式_。 Docker 引擎必須在本機執行，才能完成將模型作業化的下列步驟。 您可以在命令的結尾使用 `-h` 旗標來取得命令說明。

>[!NOTE]
>如果您的本機沒有 Docker 引擎，仍可以透過在 Azure 中建立叢集用於部署繼續進行。 只是請務必在本教學課程之後刪除該叢集，使得您不會產生持續性的費用。

1. 開啟命令列介面 (CLI)。
   在 Machine Learning Workbench 應用程式的 [檔案] 功能表上，選取 [開啟命令提示字元]。

   命令提示字元會在目前的專案資料夾位置 **c:\temp\myIris>** 中開啟。

2. 建立環境。 您必須根據環境執行此步驟一次。 例如，對開發環境和實際執行各執行一次。 對此第一個環境使用_本機模式_。 您可以在下列命令中嘗試 `-c` 或 `--cluster` 參數，稍後在_叢集模式_中設定環境。

   下列設定命令要求您具有訂用帳戶的參與者存取權。 如果您沒有該權限，您至少需有所要部署到資源群組的參與者存取權。 若要執行後者，您需要使用 `-g` 旗標，將資源群組名稱指定為設定命令的一部分。 

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   請遵循螢幕上的指示來佈建用於儲存 Docker 映像的儲存體帳戶、列出 Docker 映像的 Azure Container Registry，以及收集遙測的 Azure Application Insights 帳戶。 如果您使用 `-c` 參數，它也會建立 Container Service 叢集。
   
   叢集名稱是可讓您識別環境的一種方式。 位置應該與您從 Azure 入口網站建立之模型管理帳戶的位置相同。

   為了確定該環境已成功設定，請使用下列命令來檢查狀態：

   ```azurecli
   az ml env show -n <deployment environment name> -g <existing resource group name>
   ```

   請先確定「佈建狀態」具有「成功」值 (如下所示)，再於步驟 5 中設定環境：

   ![佈建狀態](media/tutorial-classifying-iris/provisioning_state.png)
 
   
3. 建立模型管理帳戶。 此設定只需要執行一次。
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
4. 設定模型管理帳戶。
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

5. 設定環境。

   設定完成之後，使用下列命令來設定作業化環境所需的環境變數。 使用您先前在步驟 2 使用的相同環境名稱。 使用當設定程序完成時，在命令視窗中輸出的相同資源群組名稱。

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

6. 若要確認您已針對本機 Web 服務部署正確設定您的運作化環境，請輸入下列命令：

   ```azurecli
   az ml env show
   ```

現在您已準備好建立即時 Web 服務。

>[!NOTE]
>您可以針對後續 Web 服務部署重複使用您的模型管理帳戶和環境。 您不需要為每個 Web 服務建立它們。 帳戶或環境可以有多個與其相關聯的 Web 服務。

## <a name="create-a-real-time-web-service-in-one-command"></a>在一個命令中建立即時 Web 服務
1. 如需建立即時 Web 服務，請使用下列命令：

   ```azurecli
   az ml service create realtime -f score_iris.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true -c aml_config\conda_dependencies.yml
   ```
   此命令會產生您可以在稍後使用的 Web 服務識別碼。

   下列參數會搭配 **az ml service create realtime** 命令使用：

   * `-n`：應用程式名稱，必須全部為小寫。

   * `-f`：評分指令碼檔案名稱。

   * `--model-file`：模型檔案。 在此情況下，它是 pickle 的 model.pkl 檔案。

   * `-r`：模型的執行階段。 在此情況下，它是 Python 模型。 有效的執行階段為 `python` 和 `spark-py`。

   * `--collect-model-data true`：此參數可進行資料收集。

   * `-c`：conda 相依性檔案 (其中指定其他套件) 的路徑。

   >[!IMPORTANT]
   >服務名稱 (這也是新的 Docker 映像名稱) 必須全為小寫。 否則，您會收到錯誤。 

2. 執行命令時，模型及評分檔案會上傳到在環境設定時所建立的儲存體帳戶。 部署程序會建置 Docker 映像，其中含有您的模型、結構描述、評分檔案，然後將其推送至 Azure Container Registry：**\<ACR_name\>.azureacr.io/\<imagename\>:\<version\>**。 

   命令會提取該映像到您的電腦本機，然後會啟動以該映像為基礎的 Docker 容器。 如果您的環境是在叢集模式中設定，Docker 容器會改為部署到 Azure 雲端服務 Kubernete 叢集。

   作為部署的一部分，會在您的本機電腦上建立 Web 服務適用的 HTTP REST 端點。 幾分鐘後命令應該會完成，並出現成功訊息，而您的 Web 服務即已備妥可供使用。

3. 如需查看執行中的 Docker 容器，請使用 **docker ps** 命令：

   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-by-using-separate-commands"></a>使用個別命令建立即時 Web 服務
您也可以個別執行步驟，作為以上所示 **az ml service create realtime** 命令的替代方式。 

首先，註冊模型。 然後產生資訊清單、建立 Docker 映像及建立 web 服務。 此逐步解說方法在每個步驟中提供您更大的彈性。 此外，您可以重複使用從先前步驟中產生的實體，並只在需要時重建實體。

1. 藉由提供序列化檔案名稱來登錄模型。

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   此命令會產生模型識別碼。

2. 建立資訊清單。

   若要建立資訊清單，請使用下列命令，並提供來自前一個步驟的模型識別碼輸出：

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
   ```
   此命令會產生資訊清單識別碼。

3. 建立 Docker 映像。

   若要建立 Docker 映像，請使用下列命令，並提供來自前一個步驟的資訊清單識別碼值輸出。 您也可以使用 `-c` 參數，選擇性地包含 conda 相依性。

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID> -c amlconfig\conda_dependencies.yml
   ```
   此命令會產生 Docker 映像識別碼。
   
4. 建立服務。

   若要建立服務，請使用下列命令，並提供來自前一個步驟的模型識別碼輸出：

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   此命令會產生 Web 服務識別碼。

您現在可以開始執行 Web 服務。

## <a name="run-the-real-time-web-service"></a>執行即時 Web 服務

若要測試執行中的 **irisapp** Web 服務，請使用包含四個隨機數字陣列、以 JSON 編碼的記錄。

1. Web 服務包含範例資料。 在本機模式中執行時，您可以呼叫 **az ml service usage realtime** 命令。 該呼叫會擷取有用的範例執行命令，供您用來測試服務。 此呼叫也會擷取評分 URL，讓您用來在您自己的自訂應用程式中將服務納入。

   ```azurecli
   az ml service usage realtime -i <web service ID>
   ```

2. 若要測試服務，請執行傳回的服務執行命令：
    
   ```azurecli
   az ml service run realtime -i <web service ID> -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```

   輸出是 **"2"**，這是預測的類別。 (您的結果可能不同。) 

## <a name="view-the-collected-data-in-azure-blob-storage"></a>檢視 Azure Blob 儲存體中收集的資料

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 找出您的儲存體帳戶。 若要這麼做，請選取 [所有服務]。

3. 在 [搜尋] 方塊中，輸入**儲存體帳戶**，然後選取 Enter。

4. 從 [儲存體帳戶] 搜尋方塊，選取符合您的環境的**儲存體帳戶**資源。 

   > [!TIP]
   > 若要判斷哪一個儲存體帳戶是在使用中：
   > 1. 開啟 Machine Learning Workbench。
   > 2. 選取您正在使用的專案。
   > 3. 從 [檔案] 功能表開啟命令列提示字元。
   > 4. 在命令列提示字元中，輸入 `az ml env show -v`，並檢查 *storage_account* 值。 這是您儲存體帳戶的名稱。

5. [儲存體帳戶] 窗格開啟之後，請選取左邊清單中的 [容器]。 找出名為 **modeldata** 的容器。 
 
   如果您沒有看見任何資料，在第一個 Web 服務要求之後，可能需要等候最多 10 分鐘，才能看到資料傳播至儲存體帳戶。

   資料會透過下列容器路徑流入 Blob：

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. 您可以從 Azure Blob 儲存體取用此資料。 有各種工具可使用 Microsoft 軟體和開放原始碼，例如：

   * Machine Learning：新增 CSV 檔案作為資料來源來開啟 CSV 檔案。

   * Excel：開啟每日的 CSV 檔案作為試算表。

   * [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)：使用從 Blob 中之 CSV 資料提取的資料建立圖表。

   * [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started)：將 CSV 資料載入至 Hive 資料表，並直接針對 Blob 執行 SQL 查詢。

   * [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview)：使用大部分的 CSV 資料建立資料框架。

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```


## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟
在三部分教學課程系列的第三部分中，您已了解如何使用 Machine Learning 來：
> [!div class="checklist"]
> * 找出模型檔案。
> * 產生評分指令碼和結構描述檔案。
> * 準備環境。
> * 建立即時 Web 服務。
> * 執行即時 Web 服務。
> * 檢查輸出 Blob 資料。 

您已成功在各種運算環境中執行定型指令碼、建立模型、將模型序列化，以及透過以 Docker 為基礎的 Web 服務將模型作業化。 

現在，您已準備好要進行進階資料準備：
> [!div class="nextstepaction"]
> [教學課程 4 - 進階資料準備](tutorial-bikeshare-dataprep.md)
