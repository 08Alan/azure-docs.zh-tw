---
title: "使用 Power Query 將 Excel 連接到 Hadoop | Microsoft Docs"
description: "了解如何利用商業智慧元件和使用 Power Query for Excel 來存取 HDInsight 上的 Hadoop 中儲存的資料。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 01ad2f90-7520-44d9-8c16-4d936faaff9b
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bf45651360a9fe9b5023d46000aadc054ce0ef37


---
# <a name="connect-excel-to-hadoop-by-using-power-query"></a>使用 Power Query 將 Excel 連接到 Hadoop
Microsoft 巨量資料方案的主要功能之一，是將 Microsoft 商業智慧 (BI) 元件與 Azure HDInsight 上的 Hadoop 叢集相整合。 舉例來說，此整合可讓您使用 Microsoft Power Query for Excel 增益集，將 Excel 連接到包含 Hadoop 叢集相關聯資料的 Azure 儲存體帳戶。 本文將逐步解說如何設定及使用 Power Query，以查詢受 HDInsight 管理的 Hadoop 叢集相關聯資料。

> [!NOTE]
> 本文中的步驟雖然可以與 Linux 或 Windows 架構的 HDInsight 叢集搭配使用，但用戶端工作站需要有 Windows。
> 
> 

### <a name="prerequisites"></a>必要條件
開始閱讀本文之前，您必須符合下列必要條件：

* **HDInsight 叢集**。 若要設定叢集，請參閱[開始使用 Azure HDInsight][hdinsight-get-started]。
* **工作站** 。
* **Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone 或 Office 2010 Professional Plus**。

## <a name="install-power-query"></a>安裝 Power Query
Power Query 可讓您將各種來源的資料匯入 Microsoft Excel 中，以輔助 BI 工具 (例如 PowerPivot 和 Power View) 的運作。 特別是，Power Query 可匯入在 HDInsight 叢集上執行的 Hadoop 工作所匯出或產生的資料。

從 [Microsoft 下載中心][powerquery-download]下載並安裝 Microsoft Power Query for Excel。

## <a name="import-hdinsight-data-into-excel"></a>將 HDInsight 資料匯入 Excel 中
Power Query add-in for Excel 可協助您將 HDInsight 叢集中的資料匯入至 Excel，以便使用 PowerPivot 和 Power Map 等 BI 工具來檢查、分析及呈現資料。

**從 HDInsight 叢集匯入資料**

1. 開啟 Excel。
2. 建立新的空白活頁簿。
3. 依序按一下 [Power Query] 功能表、[從其他來源]、[從 Microsoft Azure HDInsight]。
   
    ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
   
    **附註：**如果 [Power Query] 功能表未顯示，請移至 [檔案] > [選項] > [增益集]，然後從頁面底部的下拉式 [管理] 方塊中，選取 [COM 增益集]。 選取 [移至...] 按鈕，並驗證 Power Query for Excel 增益集的方塊已勾選。
   
    **附註：** Power Query 也可讓您從 HDFS 匯入資料，方法是按一下 [從其他來源]。
4. 對於 [帳戶名稱]，輸入叢集相關 Azure Blob 儲存體帳戶的名稱，然後按一下 [確定]。 這可以是 [預設儲存體帳戶](hdinsight-administer-use-management-portal.md#find-the-default-storage-account) 或連結儲存體帳戶。  其格式為 *https://<StorageAccountName>.blob.core.windows.net/*。
5. 對於 [帳戶金鑰]，輸入 Blob 儲存體帳戶的金鑰，然後按一下 [儲存]。 (只有在第一次存取此存放區時需要執行此動作。)
6. 在 [查詢編輯器] 左側的 [ **瀏覽器** ] 窗格中，按兩下 Blob 儲存體容器名稱。 依預設，容器名稱與叢集名稱相同。
7. 在 [名稱] 欄中找出 **HiveSampleData.txt** (資料夾路徑為 **../hive/warehouse/hivesampletable/**)，然後按一下 HiveSampleData.txt 左側的 [二進位]。 HiveSampleData.txt 隨附於所有叢集。 您也可以選擇使用您自己的檔案。
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. 如有需要，您可以將欄名稱重新命名。 請在準備就緒後，按一下 [關閉並載入]。  資料已載入至您的活頁簿：
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>後續步驟
在本文中，您已了解到如何使用 Power Query 將 HDInsight 中的資料擷取至 Excel。 同樣地，您也可以將 HDInsight 中的資料擷取至 Azure SQL Database。 此外也可以將資料上傳至 HDInsight。 若要深入了解，請參閱下列文章：

* [使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 HDInsight][hdinsight-ODBC]
* [將資料上傳至 HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689



<!--HONumber=Nov16_HO3-->


