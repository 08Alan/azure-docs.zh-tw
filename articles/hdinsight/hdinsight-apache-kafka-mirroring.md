---
title: "鏡像處理 Apache Kafka on HDInsight 叢集 | Microsoft Docs"
description: "了解如何使用 Kafka 的鏡像功能，藉由將主題鏡像處理至次要叢集來維護 Kafka on HDInsight 的複本。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: c7517f61944b9fdb02a3589d7c9cd83355dae6d8
ms.lasthandoff: 04/20/2017

---
# <a name="use-mirrormaker-to-create-a-replica-of-a-kafka-on-hdinsight-cluster-preview"></a>使用 MirrorMaker 建立 Kafka on HDInsight 叢集的複本 (預覽)

Apache Kafka 包含鏡像功能，可讓您將主題從一個 Kafka 叢集複寫至另一個叢集。 例如，在不同 Azure 區域中的 Kafka 叢集間複寫記錄。

鏡像功能可以當作連續程序執行，或間歇地做為在叢集間移轉資料的方法。

> [!WARNING]
> 但不能將鏡像功能視為達成容錯的方法。 主題中的項目位移在來源與目的地叢集之間有所不同，所以用戶端無法交替使用這兩者。
> 
> 如果您很擔心容錯，您應該為叢集內的主題設定複寫。 如需詳細資訊，請參閱[開始使用 Kafka on HDInsight](hdinsight-apache-kafka-get-started.md)。

## <a name="prerequisites"></a>必要條件

* Azure 虛擬網路︰來源和目的地 Kafka 叢集必須能夠直接彼此通訊。 HDInsight 不會在網際網路上公開 Kafka API，所以來源和目的地叢集必須位於相同的 Azure 虛擬網路中。

* 兩個 Kafka 叢集︰本文件使用 Azure Resource Manager 範本，在 Azure 虛擬網路內建立兩個 Kafka on HDInsight 叢集。

## <a name="how-does-mirroring-work"></a>鏡像功能的運作方式

鏡像功能的運作方式是使用 MirrorMaker 工具 (Apache Kafka 的一部分)，取用來源叢集上主題中的記錄，然後在目的地叢集上建立本機複本。 MirrorMaker 會使用一個 (或多個) *取用者*從來源叢集讀取資料，以及使用一個*產生者*來將資料寫入本機 (目的地) 叢集。

下圖說明鏡像程序：

![鏡像程序圖表](./media/hdinsight-apache-kafka-mirroring/kafka-mirroring.png)

來源與目的地叢集的節點與磁碟分割數目可能有所不同，且主題中的位移也會不同。 鏡像功能會維護用於資料分割的金鑰值，因此會根據每個金鑰保留記錄順序。

### <a name="mirroring-between-networks"></a>網路之間的鏡像功能

如果您需要在不同網路中的 Kafka 叢集之間進行鏡像處理，有下列額外考量︰

* **閘道**：網路必須能夠在 TCPIP 層級進行通訊。

* **名稱解析**︰每個網路中的 Kafka 叢集必須能夠使用主機名稱彼此連接。 這可能會要求每個網路中的網域名稱系統 (DNS) 伺服器設定成將要求轉送到其他網路。 
  
    建立 Azure 虛擬網路 (而不是使用網路提供的自動 DNS) 時，您必須指定自訂 DNS 伺服器和伺服器的 IP 位址。 建立虛擬網路之後，您就必須建立使用該 IP 位址的 Azure 虛擬機器，然後在其上安裝和設定 DNS 軟體。
  
    > [!WARNING]
    > 先建立和設定自訂 DNS 伺服器，然後再將 HDInsight 安裝到虛擬網路中。 HDInsight 不需要進行其他設定，即可使用針對虛擬網路設定的 DNS 伺服器。

如需有關如何連接兩個 Azure 虛擬網路的詳細資訊，請參閱[設定 VNet 對 VNet 連線](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。

## <a name="create-kafka-clusters"></a>建立 Kafka 叢集

Apache Kafka on HDInsight 不提供透過公用網際網路存取 Kafka 服務。 任何 Kafka 相關項目必須位於與 Kafka 叢集中節點相同的 Azure 虛擬網路。 例如，Kafka 來源和目的地叢集均位於 Azure 虛擬網路中。 下圖顯示叢集之間的通訊流動方式︰

![Azure 虛擬網路中的來源和目的地 Kafka 叢集圖表](./media/hdinsight-apache-kafka-mirroring/spark-kafka-vnet.png)

> [!NOTE]
> Kafka 本身受限於虛擬網路內的通訊，但叢集上的 SSH 和 Ambari 等其他服務可以透過網際網路存取。 如需有關適用於 HDInsight 的公用連接埠詳細資訊，請參閱 [HDInsight 所使用的連接埠和 URI](hdinsight-hadoop-port-settings-for-services.md)。

雖然您可以手動建立 Azure 虛擬網路和 Kafka 叢集，但使用 Azure Resource Manager 範本更輕鬆。 使用下列步驟將 Azure 虛擬網路和兩個 Kafka 叢集部署到 Azure 訂用帳戶。

1. 使用以下按鈕，在 Azure 入口網站中登入 Azure 並開啟範本。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure Resource Manager 範本位於 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet.json**。

2. 使用下列資訊來填入 [自訂部署] 刀鋒視窗上的項目︰
    
    ![HDInsight 自訂部署](./media/hdinsight-apache-kafka-mirroring/parameters.png)
    
    * **資源群組**：建立群組或選取現有的群組。 此群組包含 HDInsight 叢集。

    * **位置**：選取在地理上靠近您的位置。 此位置必須符合 [設定] 區段中的位置。
     
    * **基底叢集名稱**︰此值會做為 Kafka 叢集的基底名稱。 例如，輸入 **hdi** 可建立名為 **source-hdi** 和 **dest-hdi** 的叢集。

    * **叢集登入使用者名稱**：來源和目的地 Kafka 叢集的系統管理員使用者名稱。

    * **叢集登入密碼**：來源和目的地 Kafka 叢集的系統管理員使用者密碼。

    * **SSH 使用者名稱**：建立來源和目的地 Kafka 叢集的 SSH 使用者。

    * **SSH 密碼**：來源和目的地 Kafka 叢集的 SSH 使用者密碼。

    * **位置**︰叢集建立所在的區域。

3. 讀取**條款及條件**，然後選取 [我同意上方所述的條款及條件]。

4. 最後，核取 [釘選到儀表板]，然後選取 [購買]。 大約需要 20 分鐘的時間來建立叢集。

建立資源後，您會重新導向至資源群組的刀鋒視窗，其中內含叢集和 Web 儀表板。

![Vnet 和叢集的資源群組刀鋒視窗](./media/hdinsight-apache-kafka-mirroring/groupblade.png)

> [!IMPORTANT]
> 請注意，HDInsight 叢集的名稱是 **source-BASENAME** 和 **dest-BASENAME**，其中 BASENAME 是您提供給範本的名稱。 連接到叢集時，您會在稍後步驟中使用這些名稱。

## <a name="create-topics"></a>建立主題

1. 使用 SSH 連接到**來源**叢集：
   
        ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
   
    將 **sshuser** 替換為建立叢集時所使用的 SSH 使用者名稱。 將 **BASENAME** 替換為建立叢集時使用的基底名稱。
   
    如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 使用下列命令來尋找 Zookeeper 主機、設定 `SOURCE_ZKHOSTS` 變數，然後建立數個名為 `testtopic` 的新主題：
   
    ```bash
    SOURCE_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. 使用下列命令確認已建立主題：
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```
   
    回應包含 `testtopic`。

4. 使用下列命令來檢視這個 (**來源**) 叢集的 Zookeeper 主機資訊︰
   
    ```bash
    echo $SOURCE_ZKHOSTS
    ```
   
 此命令會傳回類似以下文字的資訊：
   
       zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk6-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181
   
 請儲存此資訊。 此資訊使用於下一節。

## <a name="configure-mirroring"></a>設定鏡像功能

1. 使用不同的 SSH 工作階段連接到**目的地**叢集：
   
        ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
   
    將 **sshuser** 替換為建立叢集時所使用的 SSH 使用者名稱。 將 **BASENAME** 替換為建立叢集時使用的基底名稱。
   
    如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 使用下列命令來建立 `consumer.properties` 檔案，其中描述如何與**來源**叢集通訊︰
   
    ```bash
    nano consumer.properties
    ```
   
    使用下列文字做為 `consumer.properties` 檔案的內容：
   
        zookeeper.connect=SOURCE_ZKHOSTS
        group.id=mirrorgroup
   
    以**來源**叢集中的 Zookeeper 主機資訊取代 **SOURCE_ZKHOSTS**。
   
    此檔案描述從來源 Kafka 叢集讀取資料時所要使用的取用者資訊。 如需取用者組態詳細資訊，請參閱 kafka.apache.org 上的[取用者組態](https://kafka.apache.org/documentation#consumerconfigs)。
   
    使用 **Ctrl + X**、**Y** 和 Enter 鍵來儲存檔案。

3. 在設定可與目的地叢集通訊的產生者之前，您必須尋找**目的地**叢集的訊息代理程式主機。 請使用下列命令來擷取此資訊：
   
    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
    echo $DEST_BROKERHOSTS
    ```
   
    這些命令會傳回類似以下的資訊：
   
        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. 使用下列命令來建立 `producer.properties` 檔案，其中描述如何與**目的地**叢集通訊︰

    ```bash
    nano producer.properties
    ```

    使用下列文字做為 `producer.properties` 檔案的內容：
   
        bootstrap.servers=DEST_BROKERS
        compression.type=none
   
    以上一個步驟中的訊息代理程式資訊取代 **DEST_BROKERS**。 
   
    如需產生者組態詳細資訊，請參閱 kafka.apache.org 上的[者組態](https://kafka.apache.org/documentation#producerconfigs)。

## <a name="start-mirrormaker"></a>啟動 MirrorMaker

1. 在連往**目的地**叢集的 SSH 連線中，使用下列命令來啟動 MirrorMaker 程序：
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```
   
    此範例中使用的參數：
   
    * **--consumer.config**︰指定包含取用者屬性的檔案。 這些屬性用來建立可從*來源* Kafka 叢集讀取資料的取用者。

    * **--producer.config**︰指定包含產生者屬性的檔案。 這些屬性用來建立可寫入*目的地* Kafka 叢集的產生者。

    * **--whitelist**：MirrorMaker 從來源叢集複寫至目的地的主題清單。
    
    * **--num.streams**︰要建立的取用者執行緒數目。
     
    啟動時，MirrorMaker 會傳回類似以下文字的資訊：
        
    ```
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. 從連往**來源**叢集的 SSH 連線中，使用下列命令來啟動產生者，然後傳送訊息到主題：
    
    ```bash
    sudo apt -y install jq
    SOURCE_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

 當您抵達有游標的空白行時，請輸入一些文字訊息。 這些文字會傳送到**來源**叢集上的主題。 完成後，使用 **Ctrl + C** 結束產生者程序。

3. 在連往**目的地**叢集的 SSH 連線中，使用 **Ctrl + C** 來結束 MirrorMaker 程序。 然後使用下列命令確認已建立 `testtopic` 主題，而且主題中的資料已複寫到這個鏡像︰
    
    ```bash
    DEST_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $DEST_ZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```
    
  主題清單現在包含 `testtopic`，它是在 MirrorMaster 將主題從來源叢集鏡射至目的地時所建立。 從主題中擷取的訊息與在來源叢集上所輸入的相同。

## <a name="delete-the-cluster"></a>刪除叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

因為本文件中的步驟會在相同的 Azure 資源群組中建立兩個叢集，您可以在 Azure 入口網站中刪除資源群組。 刪除資源群組，即可移除依循本文件建立的所有資源、Azure 虛擬網路，以及叢集所使用的儲存體帳戶。

## <a name="next-steps"></a>後續步驟

在本文件中，您已學會如何使用 MirrorMaker 建立 Kafka 叢集的複本。 使用下列連結來探索使用 Kafka 的其他方式︰

* [Apache Kafka MirrorMaker 文件](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (網址為 cwiki.apache.org)。
* [開始使用 Apache Kafka on HDInsight](hdinsight-apache-kafka-get-started.md)
* [使用 Apache Spark 搭配 Kafka on HDInsight](hdinsight-apache-spark-with-kafka.md)
* [使用 Apache Storm 搭配 Kafka on HDInsight](hdinsight-apache-storm-with-kafka.md)
* [透過 Azure 虛擬網路連線到 Kafka](hdinsight-apache-kafka-connect-vpn-gateway.md)

