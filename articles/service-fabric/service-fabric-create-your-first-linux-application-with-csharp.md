---
title: "在 Linux 上使用 C# 建立您的第一個 Azure 微服務應用程式 | Microsoft Docs"
description: "使用 C 建立和部署 Service Fabric 應用程式#"
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/02/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: c810f3e86ba582943e88f3085f6d9cff2496031c
ms.lasthandoff: 01/24/2017


---
# <a name="create-your-first-azure-service-fabric-application"></a>建立第一個 Azure Service Fabric 應用程式
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
> 
> 

Service Fabric 提供了在 Linux 上建置服務的 .NET Core 和 Java SDK。 在本教學課程中，我們會探討如何建立適用於 Linux 的應用程式以及使用 C# (.NET Core) 建置服務。

## <a name="prerequisites"></a>必要條件
開始之前，請確定您已 [設定 Linux 開發環境](service-fabric-get-started-linux.md)。 如果您使用 Mac OS X，您可以 [使用 Vagrant 在虛擬機器中設定 Linux 一整體環境](service-fabric-get-started-mac.md)。

## <a name="create-the-application"></a>建立應用程式
Service Fabric 應用程式可以包含一或多個服務，而每個服務在提供應用程式的功能時都有特定角色。 適用於 Linux 的 Service Fabric SDK 包含 [Yeoman](http://yeoman.io/) 產生器，可讓您輕鬆建立第一個服務且稍後新增更多服務。 讓我們使用 Yeoman 來建立具有單一服務的應用程式。

1. 在終端機中，輸入下列命令以開始建置樣板︰`yo azuresfcsharp`
2. 為您的應用程式命名。
3. 選擇第一個服務的類型並加以命名。 基於本教學課程的用途，我們會選擇 Reliable Actor 服務。
   
   ![適用於 C 的 Service Fabric Yeoman 產生器#][sf-yeoman]

> [!NOTE]
> 如需選項的詳細資訊，請參閱 [Service Fabric 程式設計模型概觀](service-fabric-choose-framework.md)。
> 
> 

## <a name="build-the-application"></a>建置應用程式
Service Fabric Yeoman 範本包含建置指令碼，可用來從終端機建置應用程式 (在瀏覽至應用程式資料夾後)。

  ```sh
 cd myapp 
 ./build.sh 
  ```

## <a name="deploy-the-application"></a>部署應用程式
建置應用程式後，可以使用 Azure CLI 將它部署到本機叢集。

1. 連接到本機 Service Fabric 叢集。
   
    ```sh
    azure servicefabric cluster connect
    ```
2. 使用範本中所提供的安裝指令碼，將應用程式套件複製到叢集的映像存放區、註冊應用程式類型，以及建立應用程式的執行個體。
   
    ```bash
    ./install.sh
    ```
3. 開啟瀏覽器並瀏覽至位於 http://localhost:19080/Explorer 的 Service Fabric Explorer (如果在 Mac OS X 上使用 Vagrant，請以 VM 的私人 IP 取代 localhost)。
4. 展開 [應用程式] 節點，請注意，您的應用程式類型現在有一個項目，而另一個則是該類型的第一個執行個體。

## <a name="start-the-test-client-and-perform-a-failover"></a>啟動測試用戶端並執行容錯移轉
動作項目專案沒有任何屬於自己的項目。 它們需要其他服務或用戶端傳送訊息給它們。 動作項目範本包含簡單的測試指令碼，您可以用來與動作項目服務互動。

1. 使用監看式公用程式執行指令碼，以查看動作項目服務的輸出。
   
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. 在 Service Fabric Explorer 中，找出裝載動作項目服務主要複本的節點。 在以下的螢幕擷取畫面中是節點 3。
   
    ![在 Service Fabric Explorer 中尋找主要複本][sfx-primary]
3. 按一下您在上一個步驟中找到的節點，然後從 [動作] 功能表選取 [停用 (重新啟動)]  。 這個動作會重新啟動本機叢集中的其中一個節點，強制容錯移轉至在另一個節點上執行的次要複本。 當您執行這個動作時，請留意測試用戶端的輸出，並注意儘管是容錯移轉，計數器仍會繼續增加。

## <a name="adding-more-services-to-an-existing-application"></a>將更多服務新增至現有的應用程式

若要將其他服務新增至已使用 `yo` 建立的應用程式，請執行下列步驟︰ 
1. 將目錄變更為現有應用程式的根目錄。  例如，如果 `MyApplication` 是 Yeoman 所建立的應用程式，則為 `cd ~/YeomanSamples/MyApplication`。
2. 執行 `yo azuresfcsharp:AddService`

## <a name="next-steps"></a>後續步驟
* [深入了解 Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [使用 Azure CLI 與 Service Fabric 叢集互動](service-fabric-azure-cli.md)
* 了解 [Service Fabric 支援選項](service-fabric-support.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png

