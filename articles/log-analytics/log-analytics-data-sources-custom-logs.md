<properties 
   pageTitle="Log Analytics 中的自訂記錄檔 | Microsoft Azure"
   description="Log Analytics 可從 Windows 和 Linux 電腦上的文字檔收集事件。本文說明如何定義新的自訂記錄檔，以及它們在 OMS 儲存機制中建立的記錄詳細資料。"
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="bwren" />

# Log Analytics 中的自訂記錄檔

Log Analytics 中的「自訂記錄檔」資料來源可讓您從 Windows 和 Linux 電腦上的文字檔案收集事件。許多應用程式會將資訊記錄到文字檔而非標準的記錄服務，例如 Windows 事件記錄檔或 Syslog。在收集之後，您就可以使用 Log Analytics 的[自訂欄位](log-analytics-custom-fields.md)功能將記錄檔中的每一筆記錄剖析成個別欄位。

![自訂記錄檔收集](media/log-analytics-data-sources-custom-logs/overview.png)

要收集的記錄檔必須符合下列準則。

- 記錄檔必須是每行一個項目，或在每個項目開頭使用符合下列其中一種格式的時間戳記。

	YYYY-MM-DD HH:MM:SS <br> M/D/YYYY HH:MM:SS AM/PM <br> Mon DD,YYYY HH:MM:SS
	
- 記錄檔不得使用會以新的項目覆寫檔案的循環更新。

## 定義自訂記錄檔

使用下列程序來定義自訂記錄檔。如需新增自訂記錄檔之範例的逐步解說，請捲動到本文結尾處。

### 步驟 1.開啟自訂記錄檔精靈

自訂記錄檔精靈會在 OMS 入口網站中執行，並可讓您定義要收集的新自訂記錄檔。

1.	在 OMS 入口網站中，移至 [設定]。
2.	依序按一下 [資料] 和 [自訂記錄檔]。
3.	根據預設，所有設定變更都會自動發送給所有代理程式。若是 Linux 代理程式，組態檔會傳送給 Fluentd 資料收集器。如果您想以手動方式在每個 Linux 代理程式上修改這個檔案，只要取消核取 [Apply below configuration to my Linux machines] (將下列設定套用至我的 Linux 機器) 方塊即可。
4.	按一下 [新增+] 開啟自訂記錄檔精靈。

### 步驟 2.上傳和剖析範例記錄檔

一開始您要上傳自訂記錄檔的範例。精靈會剖析並顯示此檔案中的項目以供您驗證。Log Analytics 會使用您指定的分隔符號來識別每一筆記錄。

**新行字元**是預設的分隔符號，並且會用於每行一個項目的記錄檔。如果一行的開頭是其中一種可用格式的日期和時間，則您可以指定**時間戳記**分隔符號，其可支援跨越多行的多個項目。

如果使用時間戳記分隔符號，則儲存在 OMS 中的每一筆記錄的 TimeGenerated 屬性會填入針對記錄檔中的該項目指定的日期/時間。如果使用新行字元分隔符號，則 TimeGenerated 會填入 Log Analytics 收集項目時的日期和時間。

>[AZURE.NOTE]Log Analytics 目前會將使用時間戳記分隔符號從記錄檔中收集到的日期/時間視為 UTC。這很快就會變更為使用代理程式上的時區。
 
1.	按一下 [瀏覽] 並瀏覽至範例檔案。請注意，在某些瀏覽器中，這個按鈕可能標示為 [選擇檔案]。
2.	按 [下一步]。 
3.	自訂記錄檔精靈會上傳檔案並列出其識別的記錄。
4.	變更用來識別新記錄的分隔符號，並選取最能識別記錄檔中的記錄的分隔符號。
5.	按 [下一步]。

### 步驟 3.新增記錄檔收集路徑

您必須在代理程式上定義一個或多個它可以在其中找到自訂記錄檔的路徑。您可以提供該記錄檔的特定路徑和名稱，或者您可以使用萬用字元為該名稱指定路徑。這可支援每天會建立一個新檔案的應用程式或在一個檔案到達特定大小時提供支援。您也可以為單一記錄檔提供多個路徑。

例如，應用程式可能會每天建立一個記錄檔，且名稱中會包含日期，如同 log20100316.txt。這類記錄檔的模式可能是「log*.txt」，其可能會套用到任何遵循應用程式命名配置的記錄檔。

下表提供可用來指定不同記錄檔的有效模式範例。

| 說明 | 路徑 |
|:--|:--|
| Windows 代理程式上的「C:\\Logs」中，副檔名為 .txt 的所有檔案 | C:\\Logs\\*.txt |
| Windows 代理程式上的「C:\\Logs」中，名稱開頭為 log 且副檔名為 .txt 的所有檔案 | C:\\Logs\\log*.txt |
| Windows 代理程式上的「/var/log/audit」中，副檔名為 .txt 的所有檔案 | /var/log/audit/*.txt |
| Windows 代理程式上的「/var/log/audit」中，名稱開頭為 log 且副檔名為 .txt 的所有檔案 | /var/log/audit/log*.txt |
  

1.	選取 Windows 或 Linux 以指定您要新增的路徑格式。
2.	輸入路徑並按一下 [+] 按鈕。
3.	針對任何其他路徑重複此程序。

### 步驟 4.提供記錄檔的名稱和描述

您指定的名稱將用於如上所述的記錄檔類型。它一定會以 \_CL 結尾以將自己辨別為自訂記錄檔。

1.	輸入記錄檔的名稱。**\_CL** 尾碼會自動提供。
2.	新增選擇性的 [描述]。
3.	按 [下一步] 來儲存自訂記錄檔的定義。

### 步驟 5。驗證會收集自訂記錄檔
最多可能需要一小時的時間，新的自訂記錄檔中的初始資料才會出現在 Log Analytics 中。從您定義自訂記錄檔之後，它就會開始從您指定的路徑中所找到的記錄檔收集項目。它不會保留您在建立自訂記錄檔期間上傳的項目，但它會收集它所找出之記錄檔中既有的項目。

一旦 Log Analytics 開始從自訂記錄檔收集，就能透過「記錄檔搜尋」取得其記錄。請使用您提供給自訂記錄檔的名稱來做為查詢中的 [類型]。

>[AZURE.NOTE] 如果搜尋中遺漏 RawData 屬性，您可能需要先關閉再重新開啟瀏覽器。

### 步驟 6.剖析自訂記錄檔項目

整個記錄檔項目會儲存在稱為 **RawData** 的單一屬性中。您很可能會想要將每個項目中的不同資訊片段，分成儲存在記錄中的個別屬性。您可以使用 Log Analytics 的[自訂欄位](log-analytics-custom-fields.md)功能來這麼做。

這裡並未提供用來剖析自訂記錄檔項目的詳細步驟。如需這項資訊，請參閱[自訂欄位](log-analytics-custom-fields.md)文件。

## 資料收集

Log Analytics 會從每個自訂記錄檔收集新的項目，間隔大約為每 5 分鐘。代理程式會記錄它在從中收集項目的每個記錄檔中的位置。如果代理程式離線一段時間，Log Analytics 即會從上次停止的地方收集項目，即使這些項目是在代理程式離線時所建立亦同。

整個記錄檔項目的內容會寫入到稱為 **RawData** 的單一屬性。您可以將此屬性剖析成多個屬性，以在建立自訂記錄檔之後透過定義[自訂欄位](log-analytics-custom-fields.md)來個別分析和搜尋。


## 自訂記錄檔記錄的屬性

自訂記錄檔記錄的類型具有您提供的記錄檔名稱和下表中的屬性。

| 屬性 | 說明 |
|:--|:--|
| TimeGenerated | Log Analytics 收集記錄時的日期和時間。如果記錄檔使用以時間為基礎的分隔符號，則這是從項目收集到的時間。 |
| SourceSystem | 收集記錄的來源代理程式類型。<br> OpsManager – Windows 代理程式，可直接連接或 SCOM <br> Linux – 所有的 Linux 代理程式 |
| RawData | 所收集項目的完整文字。 |
| ManagementGroupName | SCOM 代理程式的管理群組名稱。若為其他代理程式，此為 AOI-<工作區 ID> |


## 使用自訂記錄檔記錄來記錄搜尋

和來自任何其他資料來源的記錄一樣，來自自訂記錄檔的記錄會儲存在 OMS 儲存機制中。其類型會符合您在定義記錄檔時提供的名稱，因此您可以在搜尋中使用 [類型] 屬性，以擷取從特定記錄檔收集而來的記錄。

下表提供從自訂記錄檔擷取記錄之記錄檔搜尋的不同範例。

| 查詢 | 說明 |
|:--|:--|
| Type=MyApp\_CL | 所有事件來自名為 MyApp\_CL 的自訂記錄檔。 |
| Type=MyApp\_CL Severity\_CF=error | 所有事件來自名為 MyApp\_CL、且在名為「Severity\_CF」的自訂欄位中有「錯誤」值的自訂記錄檔。 |




## 新增自訂記錄檔的範例逐步解說

下面的章節會逐步解說建立自訂記錄檔的範例。所收集的範例記錄檔在每一行只有一個項目，其開頭為日期和時間，然後是以逗號分隔的程式碼、狀態及訊息欄位。下面顯示了幾個範例項目。

	2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
	2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
	2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
	2016-03-10 01:38:22 302,Error,Application could not connect to database
	2016-03-10 01:31:34 303,Error,Application lost connection to database

### 上傳和剖析範例記錄檔

我們提供其中一個記錄檔，並可以看到將會收集的事件。在此案例中，新行字元足以做為分隔符號。但如果記錄檔中的單一項目跨越多行，就必須使用時間戳記分隔符號。

![上傳和剖析範例記錄檔](media/log-analytics-data-sources-custom-logs/delimiter.png)

### 新增記錄檔收集路徑

記錄檔將位於「C:\\MyApp\\Logs」。每一天都會建立一個新檔案，且其名稱中包括「appYYYYMMDD.log」模式的日期。此記錄檔的適當模式是「C:\\MyApp\\Logs\\*.log」。

![記錄檔收集路徑](media/log-analytics-data-sources-custom-logs/collection-path.png)

### 提供記錄檔的名稱和描述

我們使用「MyApp\_CL」這個名稱並輸入 [描述]。

![記錄檔名稱](media/log-analytics-data-sources-custom-logs/log-name.png)


### 驗證會收集自訂記錄檔

我們使用「Type=MyApp\_CL」這個查詢，以從收集到的記錄檔傳回所有記錄。

![無自訂欄位的記錄檔查詢](media/log-analytics-data-sources-custom-logs/query-01.png)

### 剖析自訂記錄檔項目

我們使用「自訂欄位」來定義「EventTime」、「程式碼」、「狀態」和「訊息」欄位，而且我們可以在查詢所傳回的記錄中看到差異。

![有自訂欄位的記錄檔查詢](media/log-analytics-data-sources-custom-logs/query-02.png)

## 後續步驟

- 使用[自訂欄位](log-analytics-custom-fields.md)，以將自訂記錄檔中的項目剖析至個別欄位。
- 了解[記錄搜尋](log-analytics-log-searches.md)，以分析從資料來源和解決方案所收集的資料。 

<!---HONumber=AcomDC_0504_2016-->