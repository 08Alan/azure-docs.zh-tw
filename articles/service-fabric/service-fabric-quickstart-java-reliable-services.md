---
title: "建立 Azure Service Fabric Java 應用程式 | Microsoft Docs"
description: "為 Azure 建立使用 Service Fabric 之 Java 應用程式的快速入門範例。"
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: 8f4d121ba76d63b70fa6976125457942a0e98aa9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-java-application"></a>建立 Java 應用程式
Azure Service Fabric 是一個分散式系統平台，可讓您部署及管理微服務與容器。 

本快速入門示範如何使用 Linux 開發人員機器上的 Eclipse IDE，將第一個 Java 應用程式部署到 Service Fabric。 當您完成時，您會有一個投票應用程式，其 Java Web 前端會將投票結果儲存在叢集中具狀態的後端服務。

![應用程式螢幕擷取畫面](./media/service-fabric-quickstart-java/votingapp.png)

在此快速入門中，您可了解如何：

> [!div class="checklist"]
> * 使用 Eclipse 作為 Service Fabric Java 應用程式的工具
> * 將應用程式部署到本機叢集 
> * 將應用程式部署到 Azure 中的叢集
> * 跨多個節點相應放大應用程式

## <a name="prerequisites"></a>先決條件
若要完成本快速入門：
1. [安裝 Service Fabric SDK 和 Service Fabric 命令列介面 (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [安裝 Git](https://git-scm.com/)
3. [安裝 Eclipse](https://www.eclipse.org/downloads/)
4. [設定 Java 環境](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)，務必遵循選擇性步驟來安裝 Eclipse 外掛程式 

## <a name="download-the-sample"></a>下載範例
在命令視窗中執行下列命令，將範例應用程式存放庫複製到本機電腦。
```
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>在本機執行應用程式
1. 執行下列命令來啟動本機叢集：

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    啟動本機叢集需要一些時間。 若要確認該叢集完全啟動，請存取位於 **http://localhost:19080** 的 Service Fabric Explorer。 五個狀況良好的節點表示本機叢集已啟動並執行。 
    
    ![本機叢集狀況良好](./media/service-fabric-quickstart-java/localclusterup.png)

2. 開啟 Eclipse。
3. 按一下 [檔案] -> [從檔案系統中開啟專案...]。 
4. 按一下目錄，然後在您從 Github 資料夾複製的 `service-fabric-java-quickstart` 資料夾中選擇 `Voting` 目錄。 按一下 [完成] (Finish)。 

    ![Eclipse 匯入對話方塊](./media/service-fabric-quickstart-java/eclipseimport.png)
    
5. 在 Eclipse 的 [封裝總管] 中現在有 `Voting` 專案。 
6. 以滑鼠右鍵按一下專案，並選取 [Service Fabric] 下拉式清單下的 [發行應用程式...]。 選擇 **PublishProfiles/Local.json** 作為目標設定檔，並按一下 [發佈]。 

    ![本機發佈對話方塊](./media/service-fabric-quickstart-java/localjson.png)
    
7. 開啟您最愛的網頁瀏覽器，並存取 **http://localhost:8080** 來存取應用程式。 

    ![本機應用程式前端](./media/service-fabric-quickstart-java/runninglocally.png)
    
您現在可以新增一組投票選項，並開始進行投票。 應用程式會執行並將所有資料儲存在 Service Fabric 叢集中，而不需要個別資料庫。

## <a name="deploy-the-application-to-azure"></a>將應用程式部署至 Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>設定 Azure Service Fabric 叢集
若要將應用程式部署到 Azure 中的叢集，請建立您自己的叢集。

合作對象叢集是 Azure 上裝載的免費、限時 Service Fabric 叢集。 這類叢集是由任何人皆可部署應用程式並了解平台的 Service Fabric 小組所執行。 若要存取合作對象叢集，請[遵循指示](http://aka.ms/tryservicefabric)。 

如需在安全的合作對象叢集上執行管理作業，您可以使用 Service Fabric Explorer、CLI 或 Powershell。 若要使用 Service Fabric Explorer，您必須從合作對象叢集網站下載 PFX 檔案，並將憑證匯入憑證存放區 (Windows 或 Mac) 或瀏覽器本身 (Ubuntu)。 合作對象叢集中的自我簽署憑證沒有任何密碼。 

若要使用 Powershell 或 CLI 執行管理作業，您需要 PFX (Powershell) 或 PEM (CLI)。 若要將 PFX 轉換成 PEM 檔案，請執行下列命令：  

```bash
openssl pkcs12 -in party-cluster-1277863181-client-cert.pfx -out party-cluster-1277863181-client-cert.pem -nodes -passin pass:
```

如需建立您自己叢集的資訊，請參閱[在 Azure 上建立您的 Service Fabric 叢集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)。

> [!Note]
> Spring Boot 服務設定為在連接埠 8080 上接聽傳入流量。 請確定您的叢集中已開啟該連接埠。 如果您使用合作對象叢集，此連接埠已開啟。
>

### <a name="add-certificate-information-to-your-application"></a>將憑證資訊新增至應用程式

您必須將憑證指紋新增至應用程式，因為應用程式使用的是 Service Fabric 程式設計模型。 

1. 在安全的叢集上執行時，您需要 ```Voting/VotingApplication/ApplicationManiest.xml``` 檔案中的憑證指紋。 執行下列命令以擷取憑證的指紋。

    ```bash
    openssl x509 -in [CERTIFICATE_FILE] -fingerprint -noout
    ```

2. 在 ```Voting/VotingApplication/ApplicationManiest.xml``` 中的 **Secretscertificate** 標記下，新增下列程式碼片段。 **X509FindValue** 應該是上一個步驟的指紋 (沒有分號)。 

    ```xml
    <Certificates>
        <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
    </Certificates>   
    ```
    
### <a name="deploy-the-application-using-eclipse"></a>使用 Eclipse 部署應用程式
應用程式和叢集備妥後，即可直接從 Eclipse 將應用程式部署到叢集。

1. 開啟 **PublishProfiles** 目錄下的 **Cloud.json** 檔案，並妥善填入 `ConnectionIPOrURL` 和 `ConnectionPort` 欄位。 範例如下： 

    ```bash
    {
         "ClusterConnectionParameters": 
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
    }
    ```

2. 以滑鼠右鍵按一下專案，並選取 [Service Fabric] 下拉式清單下的 [發行應用程式...]。 選擇 **PublishProfiles/Cloud.json** 作為目標設定檔，並按一下 [發佈]。 

    ![雲端發佈對話方塊](./media/service-fabric-quickstart-java/cloudjson.png)

3. 開啟您最愛的網頁瀏覽器，並存取 **http://\<ConnectionIPOrURL>:8080** 來存取應用程式。 

    ![雲端應用程式前端](./media/service-fabric-quickstart-java/runningcloud.png)
    
## <a name="scale-applications-and-services-in-a-cluster"></a>調整叢集中的應用程式和服務
您可以在整個叢集內調整服務，以符合服務上的負載變更。 您可以藉由變更叢集中執行的執行個體數目來調整服務。 您有多種方法來調整您的服務，您可以使用 Service Fabric CLI (sfctl) 中的指令碼或命令。 在此範例中，我們使用 Service Fabric Explorer。

Service Fabric Explorer 會在所有 Service Fabric 叢集中執行，並可從瀏覽器瀏覽至叢集 HTTP 管理連接埠 (19080) 來存取，例如 `http://lnxxug0tlqm5.westus.cloudapp.azure.com:19080`。

若要調整 Web 前端服務，請執行下列步驟：

1. 在您的叢集中開啟 Service Fabric Explorer，例如 `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080`。
2. 按一下樹狀檢視中 **fabric:/Voting/VotingWeb** 節點旁的省略符號 (三個點)，然後選擇 [調整服務]。

    ![Service Fabric Explorer 的 [調整服務]](./media/service-fabric-quickstart-java/scaleservicejavaquickstart.png)

    您現在可以選擇調整 Web 前端服務的執行個體數目。

3. 將數字變更為 **2**，然後按一下 [調整服務]。
4. 按一下樹狀檢視中的 **fabric:/Voting/VotingWeb** 節點，然後展開資料分割節點 (以 GUID 表示)。

    ![Service Fabric Explorer 調整服務完成](./media/service-fabric-quickstart-java/servicescaled.png)

    您現在會看到此服務具有兩個執行個體，而且在樹狀檢視中，您會看到執行個體執行所在的節點。

藉由這項簡單的管理工作，我們會加倍前端服務可用來處理使用者負載的資源。 請務必了解，您不需要多個服務執行個體，就能夠可靠地執行。 如果服務失敗，Service Fabric 可確保新的服務執行個體在叢集中執行。

## <a name="next-steps"></a>後續步驟
在此快速入門中，您已了解如何：

> [!div class="checklist"]
> * 使用 Eclipse 作為 Service Fabric Java 應用程式的工具
> * 將 Java 應用程式部署到本機叢集 
> * 將 Java 應用程式部署到 Azure 中的叢集
> * 跨多個節點相應放大應用程式

* 深入了解[使用 Eclipse 偵錯 Java 上的服務](service-fabric-debugging-your-application-java.md)
* 深入了解[使用 Jenkins 設定連續整合及部署](service-fabric-cicd-your-linux-applications-with-jenkins.md)
* 簽出其他 [Java 範例](https://github.com/Azure-Samples/service-fabric-java-getting-started)