---
title: "使用 Apache Storm 和 HBase 分析感應器資料 | Microsoft Docs"
description: "了解如何透過虛擬網路連線至 Apache Storm。 搭配使用 Storm 和 HBase 來處理事件中樞的感應器資料，並使用 D3.js 將其視覺化呈現。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a9a1ac8e-5708-4833-b965-e453815e671f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 5998d785a63d43338240eabfdbbca8008a02e4b7
ms.contentlocale: zh-tw
ms.lasthandoff: 07/13/2017

---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>在 HDInsight (Hadoop) 中使用 Apache Storm、事件中樞和 HBase 分析感應器資料

了解如何使用 Apache Storm on HDInsight 來處理 Azure 事件中樞的感應器資料。 接著將資料儲存至 Apache HBase on HDInsight，並使用 D3.js 以視覺化方式呈現。

本文件中使用的 Azure Resource Manager 範本示範如何在一個資源群組中建立多個 Azure 資源。 此範本會建立一個 Azure 虛擬網路、兩個 HDInsight 叢集 (Storm 和 HBase) 以及一個 Azure Web 應用程式。 即時 Web 儀表板的 node.js 實作會自動部署至 Web 應用程式。

> [!NOTE]
> 本文件中的資訊與範例都需要 HDInsight 3.5 版。
>
> Linux 是唯一使用於 HDInsight 3.4 版或更新版本的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 淘汰](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 請參閱 [取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
  
  > [!IMPORTANT]
  > 您不需刪除現有的 HDInsight 叢集。 本文件中的步驟會建立下列資源：
  > 
  > * Azure 虛擬網路
  > * Storm on HDInsight 叢集 (以 Linux 為基礎，兩個背景工作節點)
  > * HBase on HDInsight 叢集 (以 Linux 為基礎，兩個背景工作節點)
  > * 裝載 Web 儀表板的 Azure Web 應用程式

* [Node.js](http://nodejs.org/)︰用來在您的開發環境上本機預覽 Web 儀表板。
* [Java 和 JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html)︰用來開發 Storm 拓撲。
* [Maven](http://maven.apache.org/what-is-maven.html)︰用來建置和編譯專案。
* [Git](http://git-scm.com/)︰用來從 GitHub 下載專案。
* **SSH 用戶端** ︰用來連接至以 Linux 為基礎的 HDInsight 叢集。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。
    
    > [!NOTE]
    > 您也必須能夠存取 `scp` 命令，該命令用於在本機開發環境與使用 SSH 的 HDInsight 叢集之間複製檔案。


## <a name="architecture"></a>架構

![架構圖表](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

此範例由下列元件組成：

* **Azure 事件中樞**：包含從感應器收集的資料。
* **Storm on HDInsight**：即時處理事件中樞的資料。
* **HBase on HDInsight**：針對 Storm 所處理過的資料，提供持續的 NoSQL 資料存放區。
* **Azure 虛擬網路服務**：確保 Storm on HDInsight 及 HBase on HDInsight 叢集之間能夠安全通訊。
  
  > [!NOTE]
  > 使用 Java HBase 用戶端 API 時，需要虛擬網路。 它不會透過 HBase 叢集的公用閘道來公開。 將 HBase 和 Storm 叢集安裝到相同的虛擬網路，可讓 Storm 叢集 (或虛擬網路上的任何其他系統) 直接使用用戶端 API 存取 HBase。

* **儀表板網站**：即時建立資料圖表的範例儀表板。
  
  * 此網站採用 Node.js，因此可在任何用戶端作業系統上測試，或者也能部署至 Azure 網站。
  * [Socket.io](http://socket.io/) 用於 Storm 拓撲及網站間的即時通訊。
    
    > [!NOTE]
    > 使用 Socket.io 進行通訊是實作細節。 您可以使用任何通訊架構，例如原始 WebSockets 或 SignalR。

  * [D3.js](http://d3js.org/) 用於為傳送至網站的資料繪圖。

> [!IMPORTANT]
> 因為沒有任何支援方法可建立一個同時適用於 Storm 和 HBase 的 HDInsight 叢集，因此必須建立兩個叢集。

拓撲會使用 [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) 類別從事件中樞讀取資料，並使用 [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/releases/1.0.1/javadocs/org/apache/storm/hbase/bolt/HBaseBolt.html) 類別將資料寫入至 HBase。 與網站之間的通訊必須透過 [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java)來達成。

下列圖表說明拓撲的配置：

![拓撲圖](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> 這是簡化的拓撲樣貌。 執行過程中，系統會針對正在讀取之事件中樞的每個分割區，為各個元件建立執行個體。 這些執行個體會分散至叢集的節點上，而資料在節點間路由傳送的情形如下：
> 
> * 從 Spout 傳送至剖析器的資料會經過負載平衡。
> * 從剖析器傳送至儀表板及 HBase的資料會依照裝置識別碼分組，讓相同裝置的訊息一律傳送至相同元件。

### <a name="topology-components"></a>拓撲元件

* **事件中樞 Spout**：Spout 會當作 Apache Storm 0.10.0 版和更新版本的一部分提供。
  
  > [!NOTE]
  > 此範例中使用的事件中樞 Spout 需要 Storm on HDInsight 叢集 3.3 或 3.4 版。 如需如何使用事件中樞搭配舊版 Storm on HDInsight 的相關資訊，請參閱 [使用 Storm on HDInsight 處理 Azure 事件中樞的事件](hdinsight-storm-develop-java-event-hub-topology.md)。

* **ParserBolt.java**：Spout 發出的資料為原始 JSON，有時會一次發出多個事件。 此 Bolt 示範如何讀取 Spout 發出的資料，並將其以包含多個欄位之 Tuple 的形式發至新的串流。
* **DashboardBolt.java**：此元件示範如何使用 Socket.io 用戶端程式庫，讓 Java 將資料即時傳送至 Web 儀表板。
* **Temperature.java**︰這會定義拓撲，並載入來自 **Config.properties** 檔案的組態資料。

## <a name="prepare-your-environment"></a>準備您的環境

使用此範例之前，您必須建立 Azure 事件中樞以供 Storm 拓撲讀取。

### <a name="configure-event-hub"></a>設定事件中樞

事件中樞是此範例的資料來源。 請使用下列步驟來建立事件中樞。

1. 從 [Azure 入口網站](https://portal.azure.com)，選取 [+新增] -> [物聯網] -> [事件中樞]。
2. 在 [建立命名空間]  刀鋒視窗中，執行下列工作︰
   
   1. 輸入命名空間的 **名稱** 。
   2. 選取定價層。  就已足夠。
   3. 選取要使用的 Azure [訂用帳戶]  。
   4. 選取現有的資源群組，或建立一個新的群組。
   5. 選取事件中樞的 [位置]  。
   6. 選取 [釘選到儀表板]，然後按一下 [建立]。

3. 建立程序完成時，就會顯示您的命名空間的 [事件中樞] 刀鋒視窗。 從這裡選取 [+ 新增事件中樞] 。 在 [建立事件中樞] 刀鋒視窗中，輸入 **sensordata** 的名稱，然後選取 [建立]。 讓其他欄位保持使用預設值。
4. 從您的命名空間的 [事件中樞] 刀鋒視窗中，選取 [事件中樞] 。 選取 [sensordata]  項目。
5. 從 sensordata 事件中樞的刀鋒視窗中，選取 [共用存取原則] 。 使用 [+ 新增]  連結新增下列原則︰

    | 原則名稱 | Claims |
    | ----- | ----- |
    | devices | 傳送 |
    | storm | 接聽 |

1. 選取這兩個原則，並記下 [主索引鍵]  值。 在後續步驟中，您會需要這兩個原則的值。

## <a name="download-and-configure-the-project"></a>下載並設定專案。

使用下列項目從 GitHub 下載專案。

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

命令執行完畢後，您會擁有以下目錄架構：

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal.
                hbase-site.xml - connection information for the HBase cluster.
                Config.properties - configuration information for the topology. How to read from Event Hub and the URI to the dashboard.
            src/ - the Java bolts and topology definition.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> 本文件不會深入探究此範例所包含的程式碼。 不過，會附上程式碼的完整註解。

開啟 **hdinsight-eventhub-example/TemperatureMonitor/resources/Config.properties** 檔案，並在下列程式碼行中新增您的事件中樞資訊：

    eventhubspout.username = <shared access policy name that can read from Event Hub>
    eventhubspout.password = <shared access policy key>
    eventhubspout.namespace = <namespace of your Event Hub
    eventhubspout.entitypath = <name of your event hub>
    eventhubspout.partitions.count = 2

資訊新增完畢後，請儲存檔案。

## <a name="compile-and-test-locally"></a>編譯並在本機測試

測試前，您必須先開啟儀表板，檢視拓撲輸出的資料並產生要存放在事件中樞的資料。

> [!IMPORTANT]
> 進行本機測試時，無法使用此拓撲中的 HBase 元件。 這是因為無法從包含叢集的 Azure 虛擬網路外部存取 HBase 叢集的 Java API。

### <a name="start-the-web-application"></a>啟動 Web 應用程式

1. 開啟新的命令提示字元或終端機，然後將目錄變更到 **hdinsight-hdinsight-eventhub-example/dashboard**。 使用下列命令來安裝 Web 應用程式所需的相依性：
   
        npm install

2. 使用下列命令啟動 Web 應用程式：
   
        node server.js
   
    您會看見類似以下文字的訊息：
   
        Server listening at port 3000

3. 開啟 Web 瀏覽器，在網址列輸入 **http://localhost:3000/**。 您應該會看到類似下圖的頁面：
   
    ![Web 儀表板](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)
   
    保持開啟此命令提示字元或終端機。 測試完成後，使用 Ctrl-C 鍵中斷 Web 伺服器。

### <a name="start-generating-data"></a>開始產生資料

> [!NOTE]
> 本節中的步驟採用 Node.js，因此可使用於任何平台上。 如需其他語言的範例，請查看 [SendEvents]  目錄。

1. 開啟新的命令提示字元或終端機，然後將目錄變更到 **hdinsight-eventhub-example/SendEvents/nodejs**。 若要安裝 Web 應用程式所需的相依性，請使用下列命令：
   
        npm install

2. 以文字編輯器開啟 **app.js** 檔案，並新增您稍早取得的事件中樞資訊：
   
        // ServiceBus Namespace
        var namespace = 'YourNamespace';
        // Event Hub Name
        var hubname ='sensordata';
        // Shared access Policy name and key (from Event Hub configuration)
        var my_key_name = 'devices';
        var my_key = 'YourKey';
   
   > [!NOTE]
   > 此範例假設您使用了 **sensordata** 做為事件中樞的名稱，並使用 **devices** 做為具有 **Send** 宣告的原則名稱。

3. 使用以下命令在事件中樞插入新項目：
   
        node app.js
   
    您會看見數行輸出，其中包含傳送至事件中樞的資料：
   
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="start-the-topology"></a>啟動拓撲

1. 開啟新的命令提示字元、殼層或終端機，將目錄變更至 **hdinsight-eventhub-example/TemperatureMonitor**，然後使用下列命令啟動拓撲︰
   
        mvn compile exec:java
   
    此命令會在本機模式中啟動拓撲。 **Config.properties** 檔案中包含的值可提供事件中樞和本機儀表板網站的連線資訊。 啟動之後，拓撲會從事件中樞讀取項目，並將它們傳送到在本機電腦上執行的儀表板。 您應該會看見 Web 儀表板中出現數個線條，如下圖所示：
   
    ![儀表板與資料](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

2. 儀表板執行時，請使用前幾個步驟的 `node app.js` 命令，將新的資料傳送至事件中樞。 由於溫度值是隨機產生，因此圖形必須更新才能顯示大量溫度變更。
   
   > [!NOTE]
   > 在使用 `node app.js` 命令時，您必須位於 **hdinsight-eventhub-example/SendEvents/Nodejs** 目錄。

3. 確認儀表板更新之後，請使用 Ctrl+C 鍵來停止拓撲。 您也可以使用 Ctrl-C 鍵停止本機 Web 伺服器。

## <a name="create-a-storm-and-hbase-cluster"></a>建立 Storm 和 HBase 叢集

本節中的步驟會使用 [Azure Resource Manager 範本](../azure-resource-manager/resource-group-template-deploy.md)，在虛擬網路上建立 Azure 虛擬網路以及 Storm 和 HBase 叢集。 這些範本也會建立 Azure Web 應用程式並在其中部署一份儀表板。

> [!NOTE]
> 使用虛擬網路，在 Storm 叢集上執行的拓撲便可以使用 HBase Java API 直接與 HBase 叢集通訊。

本文件中使用的 Resource Manager 範本位於公用 Blob 容器中，**https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json**。

1. 按一下以下按鈕，在 Azure 入口網站中登入 Azure 並開啟 Resource Manager 範本。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.5.json" target="_blank"><img src="./media/hdinsight-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 從 [自訂部署] 刀鋒視窗，輸入下列值：
   
    ![HDInsight 參數](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
   
   * **基底叢集名稱**︰此值會做為 Storm 和 HBase 叢集的基底名稱。 例如，輸入 **abc** 會建立名為 **storm-abc** 的 Storm 叢集以及名為 **hbase-abc** 的 HBase 叢集。
   * **叢集登入使用者名稱**：Storm 和 HBase 叢集的系統管理員使用者名稱。
   * **叢集登入密碼**：Storm 和 HBase 叢集的系統管理員使用者密碼。
   * **SSH 使用者名稱**︰要針對 Storm 和 HBase 叢集建立的 SSH 使用者。
   * **SSH 密碼**：Storm 和 HBase 叢集的 SSH 使用者的密碼。
   * **位置**︰叢集建立所在的區域。
     
     按一下 [確定]  儲存參數。

3. 使用 [基本] 區段建立資源群組，或選取現有的資源群組。
4. 在 [資源群組位置] 下拉式功能表中，選取與您在 [設定] 區段中針對 [位置] 參數所選取的相同位置。
5. 讀取條款及條件，然後選取 [我同意上方所述的條款及條件]。
6. 最後，核取 [釘選到儀表板]，然後選取 [購買]。 大約需要 20 分鐘的時間來建立叢集。

建立資源後，您會重新導向至資源群組的刀鋒視窗，其中內含叢集和 Web 儀表板。

![Vnet 和叢集的資源群組刀鋒視窗](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> 請注意，HDInsight 叢集的名稱是 **storm-BASENAME** 和 **hbase-BASENAME**，其中 BASENAME 是您提供給範本的名稱。 連接到叢集時，您會在稍後步驟中使用這些名稱。 另請注意，儀表板網站的名稱是 **basename-dashboard**。 本文件稍後將使用此值。

## <a name="configure-the-dashboard-bolt"></a>設定儀表板 Bolt

若要將資料傳送至部署為 Web 應用程式的儀表板，您必須在 **Config.properties** 檔案中修改下面這行：

    dashboard.uri: http://localhost:3000

將 `http://localhost:3000` 變更為 `http://BASENAME-dashboard.azurewebsites.net` 並儲存檔案。 以您在先前步驟中提供的基底名稱取代 **BASENAME** 。 您也可以使用先前建立的資源群組來選取儀表板和檢視 URL。

## <a name="create-the-hbase-table"></a>建立 HBase 資料表

若要將資料儲存在 HBase 中，我們必須先建立資料表。 請預先建立 Storm 需要寫入的資源，因為嘗試從 Storm 拓撲內部建立資源，可能會導致多個執行個體嘗試建立相同的資源。 在拓撲外部建立資源，並使用 Storm 進行讀取/寫入和分析。

1. 使用您在叢集建立期間提供給範本的 SSH 使用者和密碼，透過 SSH 連線到 HBase 叢集。 例如，如果使用 `ssh` 命令進行連線，您應使用下列語法：
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
   
    在此命令中，以建立叢集時所提供的 SSH 使用者名稱取代 **USERNAME**，並以您提供的基底名稱取代 **BASENAME**。 出現提示時，請輸入 SSH 使用者的密碼。

2. 在 SSH 工作階段中，啟動 HBase Shell。
   
        hbase shell
   
    載入殼層後，您會看到 `hbase(main):001:0>` 提示。

3. 在 HBase Shell 中輸入下列命令，以建立將儲存感應器資料的資料表：
   
        create 'SensorData', 'cf'

4. 使用下列命令來確認資料表已建立：
   
        scan 'SensorData'
   
    這會傳回類似下列範例的資訊，指出資料表中有 0 個資料列。
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. 輸入 `exit` 以結束 HBase 殼層：

## <a name="configure-the-hbase-bolt"></a>設定 HBase Bolt

若要從 Storm 叢集寫入至 HBase，您必須將 HBase 叢集的組態詳細資料提供給 HBase bolt。 此範例會使用來自 HBase 叢集的 **hbase-site.xml** 檔案。

### <a name="download-the-hbase-sitexml"></a>下載 hbase-site.xml

從命令提示字元中，使用 SCP 來下載 **hbase-site.xml** 叢集的 datasourcegroups.xml 檔。 在下列範例中，以建立叢集時所提供的 SSH 使用者取代 **USERNAME**，並以您稍早提供的基底名稱取代 **BASENAME**。 出現提示時，請輸入 SSH 使用者的密碼。 以 TemperatureMonitor 專案中此檔案的路徑取代 `/path/to/TemperatureMonitor/resources/hbase-site.xml` 。

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

此命令會將 **hbase-site.xml** 下載到指定的路徑。

### <a name="enable-the-hbase-bolt"></a>啟用 HBase Bolt

若要啟用 HBase Bolt 元件，請開啟 **TemperatureMonitor/src/main/java/com/microsoft/examples/Temperature.java**，並取消下列各行的註解：

    // topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

取消註解程式碼行之後，請儲存檔案。

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>建置、封裝及部署解決方案到 HDInsight

在開發環境中，使用下列步驟將 Storm 拓撲部署至 Storm 叢集。

1. 從 **TemperatureMonitor** 目錄使用下列命令來執行新的組建，並從專案建立 JAR 封裝：
   
        mvn clean compile package
   
    此命令會在專案的 **target** 目錄中建立名為 **TemperatureMonitor-1.0-SNAPSHOT.jar** 的檔案。

2. 使用 scp 將 **TemperatureMonitor-1.0-SNAPSHOT.jar** 檔案上傳至您的 Storm 叢集。 在下列範例中，以建立叢集時所提供的 SSH 使用者取代 **USERNAME**，並以您稍早提供的基底名稱取代 **BASENAME**。 出現提示時，請輸入 SSH 使用者的密碼。
   
        scp target/TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar

   > [!NOTE]
   > 檔案可能需要幾分鐘的時間才能上傳完畢。

3. 上傳檔案後，使用 SSH 連線到叢集。
   
        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net

4. 若要啟動拓撲，請從 SSH 工作階段中使用以下命令：
   
        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature temperature

5. 拓撲啟動後，以瀏覽器開啟您發佈至 Azure 的網站，然後使用 `node app.js` 命令將資料傳送至事件中樞。 您應該會看見顯示資訊的 Web 儀表板更新。
   
    ![儀表板](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>檢視 HBase 資料

使用下列步驟連接到 HBase，並確認已將資料寫入資料表：

1. 使用 SSH 連接到 HBase 叢集。
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net

2. 在 SSH 工作階段中，啟動 HBase Shell。
   
        hbase shell
   
    載入殼層後，您會看到 `hbase(main):001:0>` 提示。

3. 檢視資料表中的資料列︰
   
        scan 'SensorData'
   
    此命令會傳回類似下列文字的資訊，指出資料表中含有資料。
   
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds
   
   > [!NOTE]
   > 此掃描作業最多會從資料表傳回 10 個資料列。

## <a name="delete-your-clusters"></a>刪除叢集
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

若要一次刪除叢集、儲存體和 Web 應用程式，請刪除包含它們的資源群組。

## <a name="next-steps"></a>後續步驟

若需更多搭配 HDInsight 的 Storm 拓撲範例，請參閱 [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)。

如需關於 Apache Storm 的詳細資訊，請參閱 [Apache Storm](https://storm.incubator.apache.org/) 網站 (英文)。

如需關於 HBase on HDInsight 的詳細資訊，請參閱 [HBase 與 HDInsight 概觀](hdinsight-hbase-overview.md)。

如需關於 Socket.io 的詳細資訊，請參閱 [socket.io](http://socket.io/) 網站 (英文)。

如需關於 D3.js 的詳細資訊，請參閱 [D3.js：資料驅動型文件](http://d3js.org/)(英文)。

如需關於以 Java 建立拓撲的詳細資訊，請參閱 [為 Apache Storm on HDInsight 開發 Java 拓撲](hdinsight-storm-develop-java-topology.md)。

如需關於以 .NET 建立拓撲的詳細資訊，請參閱 [使用 Visual Studio 為 Apache Storm on HDInsight 開發 C# 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)。

[azure-portal]: https://portal.azure.com

