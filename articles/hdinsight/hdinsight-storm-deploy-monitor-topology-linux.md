---
title: "在以 Linux 為基礎的 HDInsight 上部署與管理 Apache Storm 拓撲 | Microsoft Docs"
description: "了解如何使用以 Linux 為基礎 HDInsight 上的 Storm Dashboard 部署、監視和管理 Apache Storm 拓撲。 使用適用於 Visual Studio 的 Hadoop 工具。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 35086e62-d6d8-4ccf-8cae-00073464a1e1
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: ca8f3ac0dd5301e1fd06abaf3a292872eb631f47


---
# <a name="deploy-and-manage-apache-storm-topologies-on-hdinsight"></a>部署和管理 HDInsight 上的 Apache Storm 拓撲

在本文件中，您可以了解管理和監視在 Storm on HDInsight 叢集上執行之 Storm 拓撲的基本概念。

> [!IMPORTANT]
> 本文中的步驟需要 HDInsight 叢集上以 Linux 為基礎的 Storm。 Linux 是 HDInsight 3.4 版或更新版本上唯一使用的作業系統。 如需詳細資訊，請參閱 [Windows 上的 HDInsight 取代](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)。 
> 
> 如需部署和監視以 Windows 為基礎的 HDInsight 上的拓撲的詳細資訊，請參閱 [部署和管理以 Windows 為基礎的 HDInsight 上的Apache Storm 拓撲](hdinsight-storm-deploy-monitor-topology.md)


## <a name="prerequisites"></a>必要條件
* **HDInsight 叢集上以 Linux 為基礎的 Storm**：請參閱 [開始使用 Apache Storm on HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md) 以取得建立叢集的步驟

* **了解 SSH 和 SCP**：如需搭配 HDInsight 使用 SSH 的詳細資訊，請參閱下列文章：
  
  * **Linux、Unix 或 OS X 用戶端**：請參閱 [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

  * **Windows 用戶端**：請參閱 [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

* **SCP 用戶端**：與所有 Linux、Unix 和 OS X 系統一起提供。 對於 Windows 用戶端，我們建議 PSCP，可從 [PuTTY 下載頁面](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)取得。

## <a name="start-a-storm-topology"></a>啟動 Storm 拓撲

### <a name="using-ssh-and-the-storm-command"></a>使用 SSH 和 Storm 命令

1. 使用 SSH 連接到 HDInsight 叢集。 將 **USERNAME** 替換為您的 SSH 登入名稱。 將 **CLUSTERNAME** 取代為 HDInsight 叢集名稱：
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    如需有關使用 SSH 連線至 HDInsight 叢集的詳細資訊，請參閱下列文件：
   
   * **Linux、Unix 或 OS X 用戶端**：請參閱 [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

   * **Windows 用戶端**：請參閱 [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 使用下列命令以啟動範例拓撲：
   
        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar storm.starter.WordCountTopology WordCount
   
    這會在叢集上啟動範例 WordCount 拓撲。 它會隨機產生句子並計算句子中每個字詞的出現次數。
   
   > [!NOTE]
   > 將拓撲提交至叢集時，您必須先複製包含叢集的 jar 檔案，再使用 `storm` 命令。 而您可以從檔案所在的用戶端使用 `scp` 命令來完成這個動作。 例如， `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   > 
   > WordCount 範例和其他 Storm 入門範例都已經包含在叢集中，位置是 `/usr/hdp/current/storm-client/contrib/storm-starter/`。
   > 
   > 

### <a name="programmatically"></a>以程式設計方式

您可以用程式設計方式與裝載於叢集中的 Nimbus 服務進行通訊，以對 Storm on HDInsight 部署拓撲。 [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) 提供範例 Java 應用程式，以示範如何透過 Nimbus 服務部署和啟動拓撲。

## <a name="monitor-and-manage-using-the-storm-command"></a>使用 Storm 命令監視和管理

`storm` 公用程式可讓您從命令列使用執行中拓撲。 以下是常用命令的清單。 使用 `storm -h` 以取得完整的命令清單。

### <a name="list-topologies"></a>列出拓撲
使用下列命令來列出所有執行中拓撲：

    storm list

此命令傳回的資訊類似如下：

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>停用和重新啟動
停用拓撲會暫停它，直到刪除或重新啟動。 使用下列項目以停用和重新啟動：

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>刪除執行中拓撲
Storm 拓撲一旦啟動之後，將會繼續執行直到停止。 若要停止拓撲，請使用下列命令：

    storm stop TOPOLOGYNAME

### <a name="rebalance"></a>重新平衡
重新平衡拓撲可以讓系統修訂拓撲的平行處理原則。 例如，如果您已調整叢集的大小以新增更多節點，重新平衡可讓執行中的拓撲使用新的節點。

> [!WARNING]
> 重新平衡拓撲首先會停用拓撲，然後跨叢集平均重新分佈背景工作角色，最後讓拓撲返回發生重新平衡之前的狀態。 因此如果拓撲是作用中，它會再次變成作用中。 如果已停用，它將保持停用。
> 
> 

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-using-the-storm-ui"></a>使用 Storm UI 監視和管理
Storm UI 提供 Web 介面來處理執行中的拓撲，包含在您的 HDInsight 叢集中。 若要檢視 Storm UI，請使用網頁瀏覽器開啟 **https://CLUSTERNAME.azurehdinsight.net/stormui**，其中 **CLUSTERNAME** 是叢集的名稱。

> [!NOTE]
> 如果要求您提供使用者名稱和密碼，請輸入叢集系統管理員 (admin) 和建立叢集時使用的密碼。
> 
> 

### <a name="main-page"></a>主頁面
Storm UI 的主頁面會提供下列資訊：

* **叢集摘要**：Storm 叢集的基本資訊。
* **拓撲摘要**：執行中拓撲的清單。 使用本節中的連結來檢視特定拓撲的詳細資訊。
* **監督員摘要**：Storm 監督員的資訊。
* **Nimbus 組態**：叢集的 Nimbus 組態。

### <a name="topology-summary"></a>拓撲摘要
選取 [拓撲摘要]  區段中的連結會顯示拓撲的下列資訊：

* **拓撲摘要**：拓撲的基本資訊。
* **拓撲動作**：您可以針對拓撲執行的管理動作。
  
  * **啟用**：繼續處理已停用的拓撲。
  * **停用**：暫停執行中的拓撲。
  * **重新平衡**：調整拓撲的平行處理原則。 變更叢集中的節點數目之後，您應該重新平衡執行中拓撲。 這可讓拓撲調整平行處理原則，以彌補叢集中增加或減少的節點數目。
    
    如需詳細資訊，請參閱<a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">了解 Storm 拓撲的平行處理原則</a>。
  * **終止**：在指定的逾時之後結束 Storm 拓撲。
* **拓撲統計資料**：拓撲的統計資料。 使用 [視窗]  資料行中的連結，以設定頁面上其餘項目的時間範圍。
* **Spout**：拓撲所使用的 Spout。 使用本節中的連結檢視特定 Spout 的詳細資訊。
* **Bolt**：拓撲所使用的 Bolt。 使用本節中的連結檢視特定 Bolt 的詳細資訊。
* **拓撲組態**：所選取拓撲的組態。

### <a name="spout-and-bolt-summary"></a>Spout 和 Bolt 摘要
從 [Spout] 或 [Bolt] 區段中選取 Spout 會顯示所選取項目的下列資訊：

* **元件摘要**：Spout 或 Bolt 的基本資訊。
* **Spout/Bolt 統計資料**：Spout 或 Bolt 的統計資料。 使用 [視窗]  資料行中的連結，以設定頁面上其餘項目的時間範圍。
* **輸入統計資料** (僅限 Bolt)：Bolt 所使用輸入資料流的資訊。
* **輸出統計資料**：此 Spout 或 Bolt 所發出資料流的資訊。
* **執行程式**：Spout 或 Bolt 執行個體的資訊。 選取特定執行程式的 [連接埠]  項目，以檢視針對此執行個體所產生之診斷資訊的記錄。
* **錯誤**：此 Spout 或 Bolt 的任何錯誤資訊。

## <a name="rest-api"></a>REST API
Storm UI 是以 REST API 為建置基礎，因此您可以使用 REST API 執行類似的管理和監視功能。 您可以使用 REST API 建立自訂工具來管理和監視 Storm 拓撲。

如需詳細資訊，請參閱 [Storm UI REST API](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html)。 下列資訊專用於搭配使用 REST API 與 Apache Storm on HDInsight。

> [!IMPORTANT]
> Storm REST API 不是透過網際網路公開可用，而是必須使用 HDInsight 叢集前端節點的 SSH 通道來存取。 如需建立及使用 SSH 通道的詳細資訊，請參閱 [使用 SSH 通道來存取 Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie 及其他 Web UI](hdinsight-linux-ambari-ssh-tunnel.md)。
> 
> 

### <a name="base-uri"></a>基底 URI
在以 Linux 為基礎的 HDInsight 叢集上的 REST API 基底 URI 可於前端節點 (位於 **https://HEADNODEFQDN:8744/api/v1/**) 取得；不過，前端節點的網域名稱是在叢集建立期間產生，而且不是靜態。

您可以用幾種不同的方式尋找叢集前端節點的完整網域名稱 (FQDN)：

* **從 SSH 工作階段**：使用命令 `headnode -f` (從 SSH 工作階段到叢集)。
* **從 Ambari Web**：從頁面頂端選取 [服務]，然後選取 [Storm]。 從 [摘要] 索引標籤，選取 [Storm UI 伺服器]。 Storm UI 和 REST API 執行所在節點的 FQDN 會位於頁面頂端。
* **從 Ambari REST API**：使用命令 `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` 來擷取 Storm UI 和 REST API 執行所在節點的相關資訊。 將 **PASSWORD** 取代為叢集的系統管理員密碼。 將 **CLUSTERNAME** 取代為叢集名稱。 在回應中，"host_name" 項目包含節點的 FQDN。

### <a name="authentication"></a>驗證
REST API 的要求必須使用 **基本驗證**，因此請使用 HDInsight 叢集管理員名稱和密碼。

> [!NOTE]
> 因為使用純文字傳送基本驗證，所以您應該 **一律** 使用 HTTPS 來保護與叢集通訊的安全。
> 
> 

### <a name="return-values"></a>傳回值
從 REST API 傳回的資訊只能從叢集或與叢集相同之 Azure 虛擬網路上的虛擬機器內使用。 例如，無法從網際網路存取針對 Zookeeper 伺服器所傳回的完整網域名稱 (FQDN)。

## <a name="next-steps"></a>後續步驟
現在，您已經了解如何使用 Storm 儀表板來部署和監視拓撲、了解如何 [使用 Maven 來開發以 Java 為基礎的拓撲](hdinsight-storm-develop-java-topology.md)。

若需更多範例拓撲的清單，請參閱 [Storm on HDInsight 的範例拓撲](hdinsight-storm-example-topology.md)。




<!--HONumber=Jan17_HO3-->


