<properties
   pageTitle="使用入口網站建立 HDInsight 叢集與 Azure Data Lake Store |Azure"
   description="使用 Azure 入口網站建立和使用 HDInsight Hadoop 叢集與 Azure Data Lake Store"
   services="data-lake-store,hdinsight" 
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/03/2016"
   ms.author="nitinme"/>

# 使用 Azure 入口網站建立 HDInsight 叢集與資料湖存放區

> [AZURE.SELECTOR]
- [使用入口網站](data-lake-store-hdinsight-hadoop-use-portal.md)
- [使用 PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


了解如何使用 Azure 入口網站建立可存取 Azure Data Lake Store 的 HDInsight 叢集 (Hadoop、HBase、Spark 或 Storm)。此版本的一些重要考量：

* **對於 Spark 叢集 (Linux) 和 Hadoop 叢集 (Windows 和 Linux)**，Data Lake Store 只能用來做為額外的儲存體帳戶。這類叢集的預設儲存體帳戶仍是 Azure 儲存體 Blob (WASB)。

* **對於 Storm 叢集 (Windows 和 Linux)**，Data Lake Store 可以用來從 Storm 拓撲寫入資料。資料湖存放區也可以用來儲存參考資料，該資料稍後可以由 Storm 拓撲讀取。如需詳細資訊，請參閱[在 Storm 拓撲中使用 Data Lake Store](#use-data-lake-store-in-a-storm-topology)。

* **對於 HBase 叢集 (Windows 和 Linux)**，您可以使用資料湖存放區做為預設儲存體或額外的儲存體。如需詳細資訊，請參閱[搭配 HBase 叢集使用 Data Lake Store](#use-data-lake-store-with-hbase-clusters)。

> [AZURE.NOTE] 只有 HDInsight 3.2 版和 3.4 版 (適用於 Windows 及 Linux 上的 Hadoop、HBase 及 Storm 叢集) 提供建立可存取 Data Lake Store 之 HDInsight 叢集的選項。針對 Linux 上的 Spark 叢集，此選項僅適用於 HDInsight 3.4 版的叢集。


## 必要條件

開始進行本教學課程之前，您必須具備下列條件：

- **Azure 訂用帳戶**。請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
- **啟用您的 Azure 訂用帳戶**以使用資料湖存放區公開預覽版。請參閱[指示](data-lake-store-get-started-portal.md#signup)。
- **Azure Data Lake Store 帳戶**。遵循[使用 Azure 入口網站開始使用 Azure 資料湖存放區](data-lake-store-get-started-portal.md)的指示。一旦您建立好帳戶，請執行下列工作來上傳一些範例資料。稍後在教學課程中，您將需要這項資料，以從會存取資料湖存放區中資料的 HDInsight 叢集中執行作業。

	* [在資料湖存放區中建立資料夾](data-lake-store-get-started-portal.md#createfolder)。
	* [將檔案上傳至 Data Lake Store](data-lake-store-get-started-portal.md#uploaddata)。如果您正在尋找一些可上傳的範例資料，可以從 [Azure 資料湖 Git 存放庫](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData)取得 **Ambulance Data** 資料夾。

## 使用影片快速學習？

觀看下列影片來了解如何透過存取 Data Lake Store 來佈建 HDInsight 叢集。

* [建立可存取 Data Lake Store 的 HDInsight 叢集](https://mix.office.com/watch/l93xri2yhtp2)
* 一旦設定叢集，[請使用 Hive 和 Pig 指令碼存取 Data Lake Store 中的資料](https://mix.office.com/watch/1n9g5w0fiqv1q)

## 建立可存取 Azure 資料湖存放區的 HDInsight 叢集

在本節中，您會建立 HDInsight Hadoop 叢集，它使用資料湖存放區做為額外的儲存體。在此版本中，對於 Hadoop 叢集，資料湖存放區只能做為叢集的額外儲存體。預設儲存體仍是 Azure 儲存體 Blob (WASB)。所以，我們要先建立叢集所需的儲存體帳戶和儲存體容器。

1. 登入新的 [Azure 入口網站](https://portal.azure.com)。

2. 請依照[在 HDInsight 中建立 Hadoop 叢集](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal)中的步驟，開始佈建 HDInsight 叢集。

3. 在 [選擇性組態] 刀鋒視窗中，按一下 [資料來源]。在 [資料來源] 刀鋒視窗中，指定儲存體帳戶和儲存體容器的詳細資料、將 [位置] 指定為 [美國東部 2]，然後按一下 [叢集 AAD 身分識別]。

	![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "將服務主體新增至 HDInsight 叢集")

4. 在 [叢集 AAD 身分識別] 刀鋒視窗中，您可以選擇選取現有的服務主體或建立一個新的服務主體。

	* **建立新的服務主體**

		* 在 [叢集 AAD 身分識別] 刀鋒視窗中，按一下 [建立新的]、按一下 [服務主體]，然後在 [建立服務主體] 刀鋒視窗中，提供值以建立新的服務主體。在這個過程中，也會建立憑證和 Azure Active Directory 應用程式。按一下 [建立]。

			![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "將服務主體新增至 HDInsight 叢集")

		* 在 [叢集 AAD 身分識別] 刀鋒視窗中，按一下 [管理 ADLS 存取]。窗格中會顯示與訂用帳戶相關聯的資料湖存放區帳戶。不過，您只能為自己建立的帳戶設定權限。選取想要與 HDInsight 叢集產生關聯之帳戶的 READ/WRITE/EXECUTE 權限，然後按一下 [儲存權限]。

			![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "將服務主體新增至 HDInsight 叢集")

		* 在 [叢集 AAD 身分識別] 刀鋒視窗中，按一下 [下載憑證]，以下載與您所建立之服務主體相關聯的憑證。日後在建立其他 HDInsight 叢集時如果您想要使用相同的服務主體，便很適合這麼做。按一下 [選取]。

			![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "將服務主體新增至 HDInsight 叢集")


	* **選擇現有的服務主體**。

		* 在 [叢集 AAD 身分識別] 刀鋒視窗中，按一下 [使用現有的]、按一下 [服務主體]，然後在 [選取服務主體] 刀鋒視窗中，搜尋現有的服務主體。按一下服務主體名稱，然後按一下 [選取]。

			![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "將服務主體新增至 HDInsight 叢集")

		* 在 [叢集 AAD 身分識別] 刀鋒視窗中，上傳與您所選取之服務主體相關聯的憑證 (.pfx)，然後提供憑證密碼。

		* 按一下 [管理 ADLS 存取]。窗格中會顯示與訂用帳戶相關聯的資料湖存放區帳戶。不過，您只能為自己建立的帳戶設定權限。選取想要與 HDInsight 叢集產生關聯之帳戶的 READ/WRITE/EXECUTE 權限，然後按一下 [儲存權限]。

			![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.existing.save.png "將服務主體新增至 HDInsight 叢集")

		* 按一下 [儲存權限]，然後按一下 [選取]。

6. 按一下 [資料來源] 刀鋒視窗上的 [選取]，並使用叢集佈建繼續作業，如[在 HDInsight 中建立 Hadoop 叢集](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal)中所述。

7. 佈建叢集之後，您可以驗證服務主體是與 HDInsight 叢集相關聯。若要這樣做，從 [叢集] 刀鋒視窗中依序按一下 [設定] 和 [叢集 AAD 身分識別]，您應該會看到相關聯的服務主體。

	![將服務主體新增至 HDInsight 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "將服務主體新增至 HDInsight 叢集")

## 在 HDInsight 叢集上執行測試工作以使用 Azure 資料湖存放區

設定 HDInsight 叢集之後，您可以在叢集上執行測試工作，以測試 HDInsight 叢集是否可以存取 Azure 資料湖存放區中的資料。若要這樣做，我們將會執行目標為資料湖存放區的一些 hive 查詢。

### 對於 Linux 叢集

1. 開啟您剛剛佈建的叢集的 [叢集] 刀鋒視窗，然後按一下 [儀表板]。這會為 Linux 叢集開啟 Ambari。存取 Ambari 時，系統會提示您對網站進行驗證。輸入您在建立叢集時所使用的 admin (預設 admin)、帳戶名稱和密碼。

	![啟動叢集儀表板](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "啟動叢集儀表板")

	您也可以在網頁瀏覽器中移至 https://CLUSTERNAME.azurehdinsight.net，直接瀏覽至 Ambari (其中 **CLUSTERNAME** 是您的 HDInsight 叢集名稱)。

2. 開啟 Hive 檢視。從頁面功能表選取方塊組合 (頁面右側的 [管理員] 連結和按鈕旁邊)，以列出可用的檢視。選取 [Hive] 檢視。

	![選取 ambari 檢視](./media/data-lake-store-hdinsight-hadoop-use-portal/selecthiveview.png)

3. 您應該會看到如下所示的頁面：

	![Hive 檢視頁面的影像，包含查詢編輯器區段](./media/data-lake-store-hdinsight-hadoop-use-portal/hiveview.png)

4. 在頁面的 [查詢編輯器] 區段中，將下列 HiveQL 陳述式貼到工作表中：

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

5. 按一下 [查詢編輯器] 底部的 [執行] 按鈕，開始查詢。[查詢程序結果] 區段應該會出現在 [查詢編輯器] 下方並顯示作業相關資訊。

6. 查詢完成後，[查詢程序結果] 區段會顯示作業的結果。[結果] 索引標籤應包含下列資訊：

7. 執行下列查詢來確認已建立資料表。

		SHOW TABLES;

	[結果] 索引標籤應顯示下列資訊：

		hivesampletable
		vehicles

	**vehicles** 是您稍早建立的資料表。**hivesampletable** 是所有 HDInsight 叢集中預設可用的範例資料表。

8. 您也可以執行查詢，從 **vehicles** 資料表擷取資料。

		SELECT * FROM vehicles LIMIT 5;

### 對於 Windows 叢集

1. 開啟您剛剛佈建的叢集的 [叢集] 刀鋒視窗，然後按一下 [儀表板]。

	![啟動叢集儀表板](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "啟動叢集儀表板")

	出現提示時，輸入叢集的系統管理員認證。

2. 這會開啟 Microsoft Azure HDInsight 查詢主控台。按一下 [Hive 編輯器]。

	![開啟 Hive 編輯器](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "開啟 Hive 編輯器")

3. 在 [Hive 編輯器] 中輸入下列查詢，然後按一下 [提交]。

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

	在此 Hive 查詢中，我們會根據儲存在 `adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder` 上資料湖存放區中的資料來建立資料表。這個位置具有您稍早應該已上傳的範例資料檔案。

	底部的 [工作工作階段] 資料表會顯示工作的狀態從 [正在初始化] 變更為 [執行中]，然後變更為 [已完成]。您也可以按一下 [檢視詳細資料] 以查看已完成工作的詳細資訊。

	![建立資料表](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "建立資料表")

4. 執行下列查詢來確認已建立資料表。

		SHOW TABLES;

	按一下對應至此查詢的 [檢視詳細資料]，輸出應該會顯示下列項目：

		hivesampletable
		vehicles

	**vehicles** 是您稍早建立的資料表。**hivesampletable** 是所有 HDInsight 叢集中預設可用的範例資料表。

5. 您也可以執行查詢，從 **vehicles** 資料表擷取資料。

		SELECT * FROM vehicles LIMIT 5;


## 使用 HDFS 命令存取資料湖存放區

一旦您已設定 HDInsight 叢集使用資料湖存放區，您可以使用 HDFS 殼層命令來存取存放區。

### 對於 Linux 叢集

在這一節中，您將 SSH 進入叢集並執行 HDFS 命令。Windows 未提供內建 SSH 用戶端。建議使用 **PuTTY**，您可以從下列位置下載：[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

如需使用 PuTTY 的詳細資訊，請參閱[從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。

連接之後，使用下列 HDFS 檔案系統命令列出資料湖存放區中的檔案。

	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

這樣應該會列出您稍早上傳至資料湖存放區的檔案。

	15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
	Found 1 items
	-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

您也可以使用 `hdfs dfs -put` 命令來將一些檔案上傳至資料湖存放區，然後使用 `hdfs dfs -ls` 以確認是否成功上傳檔案。


### 對於 Windows 叢集

1. 登入新的 [Azure 入口網站](https://portal.azure.com)。

2. 按一下 [瀏覽]，然後依序按一下 [HDInsight 叢集] 和您建立的 HDInsight 叢集。

3. 在 [叢集] 刀鋒視窗中，按一下 [遠端桌面]，然後在 [遠端桌面] 刀鋒視窗中，按一下 [連接]。

	![遠端至 HDI 叢集](./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "建立 Azure 資源群組")

	出現提示時，請輸入為遠端桌面使用者提供的認證。

4. 在遠端工作階段中，啟動 Windows PowerShell，並使用 HDFS 檔案系統命令來列出 Azure 資料湖存放區中的檔案。

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	這樣應該會列出您稍早上傳至資料湖存放區的檔案。

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

	您也可以使用 `hdfs dfs -put` 命令來將一些檔案上傳至資料湖存放區，然後使用 `hdfs dfs -ls` 以確認是否成功上傳檔案。


## 在 Storm 拓撲中使用資料湖存放區

您可以使用資料湖存放區從 Storm 拓撲寫入資料。如需如何達成這個情況的指示，請參閱[搭配使用 Azure Data Lake Store 與 HDInsight 上的 Apache Storm](../hdinsight/hdinsight-storm-write-data-lake-store.md)。

## 搭配 HBase 叢集使用 Data Lake Store

搭配 HBase 叢集，您可以使用 Data Lake Store 做為預設儲存體以及額外的儲存體。若要這樣做：

1.  在 [資料來源] 刀鋒視窗中，針對 [HBase 資料位置] 選取 [Data Lake Store]。
2.  選取您要使用的 Data Lake Store 名稱，或建立一個新的。
3.  最後，指定 Data Lake Store 內的 **HBase 根資料夾** 。如果 Data Lake Store 帳戶沒有根資料夾，請建立一個新的。

	![HBase 與 Data Lake Store](./media/data-lake-store-hdinsight-hadoop-use-portal/hbase-data-lake-store.png "建立 Azure 資源群組")

### 使用 Data Lake Store 做為 HBase 叢集的預設儲存體時的考量

* 您可以針對多個 HBase 叢集使用相同的 Data Lake Store 帳戶。不過，您為叢集提供的 **HBase 根資料夾** (上述螢幕擷取畫面中的步驟 # 4) 必須是唯一的。您**不得**跨兩個不同的 HBase 叢集使用相同的根資料夾。
* 雖然您可以使用 Data Lake Store 帳戶做為預設儲存體，但是 HBase 叢集記錄檔仍會儲存於與叢集相關聯的 Azure 儲存體 Blob (WASB) 中。這是上述螢幕擷取畫面中以藍色方塊反白顯示的項目。



## 另請參閱

* [PowerShell：建立 HDInsight 叢集以使用資料湖存放區](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0608_2016-->