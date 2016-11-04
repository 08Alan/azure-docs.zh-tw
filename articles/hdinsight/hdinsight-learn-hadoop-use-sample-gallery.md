---
title: 使用範例資源庫了解 HDInsight 中的 Hadoop | Microsoft Docs
description: 從 HDInsight Getting Started Gallery 執行範例應用程式，快速了解 Hadoop。使用範例資料或提供自己的資料。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: jgao

---
# 使用 Azure HDInsight Getting Started Gallery 了解 Hadoop
只有以 Windows 為基礎的 HDInsight 叢集才能使用 Getting Started Gallery。此資源庫在 HDInsight 中執行範例應用程式，提供簡單且快速的方式以了解 Hadoop。有些範例具備範例資料。您可以為其餘範例提供自己的資料。目前有下列六個範例 (還會推出更多)：

* 含有 Azure 資料的方案
  * Microsoft Azure 網站記錄分析
  * Microsoft Azure 儲存體分析
* 含範例資料的方案
  * 感應器資料分析
  * Twitter 趨勢分析
  * 網站記錄分析
  * Mahout 影片建議

![HDInsight Hadoop、Storm 和 HBase Getting Started Gallery 方案都含有範例資料。][hdinsight.sample.gallery]

下列視訊顯示如何執行 Twitter 趨勢分析範例：

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.youtube.com/embed/7ePbHot1SN4></a></center>

您可以藉由瀏覽至 http://<您的 HDInsight 叢集名稱>.azurehdinsight.net/，或是從 Azure 入口網站來存取儀表板。

**從 Getting Started Gallery 執行範例**

1. 登入 [Azure 入口網站][azure.portal]。
2. 在左側功能表中按一下 [瀏覽]，然後依序按一下 [HDInsight 叢集] 和您的叢集名稱。
3. 從頂端功能表按一下 [儀表板]。
4. 輸入 HTTP 使用者 (又稱為叢集使用者) 的使用者名稱和密碼。
5. 按一下頁面頂端的 [Getting Started Gallery]。
6. 按一下其中一個範例。每個範例都提供詳細的執行步驟。下列影像顯示 Twitter 趨勢分析範例：
   
    ![HDInsight Twitter 趨勢分析範例][hdinsight.twitter.sample]

## 後續步驟
其他了解 HDInsight 的方式包括：

* [HDInsight 學習地圖][hdinsight.learn.map]
* [HDInsight 資訊圖][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]: https://portal.azure.com

<!---HONumber=AcomDC_0914_2016-->