<properties
   pageTitle="將現有的可執行檔部署至 Azure Service Fabric | Microsoft Azure"
   description="逐步解說如何將現有應用程式封裝為來賓可執行檔，使其可以部署在 Azure Service Fabric 叢集上"
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="06/06/2016"
   ms.author="bscholl;mikhegn"/>

# 將來賓可執行檔部署至 Service Fabric

您可以在 Azure Service Fabric 中執行任何類型的應用程式，例如 Node.js、Java 或原生應用程式。在 Service Fabric 術語中，這些類型的應用程式稱為來賓可執行檔。Service Fabric 將來賓可執行檔視為無狀態服務。因此會根據可用性和其他度量將它們放在叢集的節點上。這篇文章說明如何使用 Visual Studio 或命令列公用程式，將來賓可執行檔封裝並部署至 Service Fabric 叢集。

## 在 Service Fabric 中執行來賓可執行檔的優點

在 Service Fabric 叢集中執行來賓可執行檔有幾個優點：

- 高可用性。Service Fabric 中執行的應用程式天生就有高用性。Service Fabric 會確保應用程式永遠有一個執行個體已啟動並執行中。
- 健康狀況監視。現成可用的 Service Fabric 狀況監控會偵測應用程式是否已啟動並執行，而且會在失敗的情況下提供診斷資訊   
- 應用程式生命週期管理。除了提供無需停機的升級以外，Service Fabric 也可讓您在升級期間發生問題時回復到之前的版本。    
- 密度。您可以在叢集中執行多個應用程式，每個應用程式不必在自己的硬體上執行

本文中涵蓋將來賓可執行檔封裝並部署至 Service Fabric 的基本步驟。

## 應用程式和服務資訊清單檔的快速概觀

在部署來賓可執行檔的過程中，最好先了解 Service Fabric 封裝和部署模型。Service Fabric 封裝部署模型主要依賴兩個 XML 檔案：應用程式和服務資訊清單。ApplicationManifest.xml 和 ServiceManifest.xml 檔案的結構描述定義是和 Service Fabric SDK 及工具一起安裝在 *C:\\Program Files\\Microsoft SDKs\\Service Fabric\\schemas\\ServiceFabricServiceModel.xsd*。

* **應用程式資訊清單**

  應用程式資訊清單用來描述應用程式。其中列出組成該應用程式的服務，以及用來定義應如何部署服務的其他參數 (例如執行個體數目)。

  在 Service Fabric 領域中，應用程式是「可升級的單位」。 應用程式可以升級為單一單位，其中潛在的失敗 (及潛在的回復) 由平台所管理。平台可保證升級程序完全成功，若是失敗，則不會讓應用程式處於不明/不穩定的狀態。

* **服務資訊清單**

  服務資訊清單描述服務的元件。其中包含一些資料，例如服務的名稱和類型 (Service Fabric 用來管理服務的資訊)、其程式碼、組態和資料元件。服務資訊清單還包含一些在服務部署後可用來設定服務的額外參數。

  我們不會詳細說明服務資訊清單中所有可用的不同參數。但我們將會說明在 Service Fabric 上執行來賓可執行檔所需的子集。

## 應用程式封裝檔案結構
為了將應用程式部署至 Service Fabric，應用程式必須遵循預先定義的目錄結構。以下是該結構的範例。

```
|-- ApplicationPackage
	|-- code
		|-- existingapp.exe
	|-- config
		|-- Settings.xml
  |-- data    
  |-- ServiceManifest.xml
|-- ApplicationManifest.xml
```

根目錄包含可定義應用程式的 ApplicationManifest.xml 檔案。對於應用程式包含的每個服務，都有一個子目錄用來包含服務所需的所有構件：ServiceManifest.xml 以及通常有下列 3 個目錄：

- *Code*。此目錄包含服務程式碼。
- *Config*。此目錄包含服務可在執行階段存取的 settings.xml 檔案 (和其他必要檔案)，以擷取特定組態設定。
- *Data*。這是額外的目錄，用來儲存服務可能需要的其他本機資料。注意：資料應用來只儲存短期資料。如果在容錯移轉期間必須重新定位服務，Service Fabric 不會將變更複製/複寫到資料目錄。

注意：如果不需要 `config` 和 `data` 目錄，則不必建立。

## 封裝現有應用程式的程序

在封裝來賓可執行檔時，您可以選擇使用 Visual Studio 專案範本，或是以手動方式建立應用程式封裝。使用 Visual Studio，就可讓新增專案精靈為您建立應用程式封裝的結構和資訊清單檔案。如需如何使用 Visual Studio 封裝來賓可執行檔的逐步指南，請參閱下面的內容。

手動封裝來賓可執行檔的程序是基於下列步驟：

1. 建立封裝目錄結構。
2. 新增應用程式的程式碼和組態檔。
3. 編輯服務資訊清單檔。
4. 編輯應用程式資訊清單檔。

>[AZURE.NOTE] 我們提供可讓您自動建立 ApplicationPackage 的封裝工具。此工具目前為預覽狀態。您可以從[這裡](http://aka.ms/servicefabricpacktool)下載。

### 建立封裝目錄結構
您可以如先前所述開始建立目錄結構。

### 新增應用程式的程式碼和組態檔
建立目錄結構之後，您可以在 code 和 config 目錄之下加入應用程式的程式碼和組態檔。您也可以在 code 和 config 目錄之下建立其他目錄或子目錄。

Service Fabric 會進行應用程式根目錄內容的 xcopy，所以除了建立兩個最上層目錄 code 和 settings 以外，沒有預先定義的結構可使用 (您可以選擇不同的名稱。下一節中有更多詳細資訊)。

>[AZURE.NOTE] 請確定您包含應用程式需要的所有檔案/相依項目。Service Fabric 將會複製叢集中所有節點上的應用程式套件內容，而該叢集即將部署複製應用程式的服務。套件應包含執行應用程式需要的所有程式碼。我們不建議假設已經安裝相依項目。

### 編輯服務資訊清單檔
下一步就是編輯服務資訊清單檔以包含下列資訊：

- 服務類型的名稱。這是 Service Fabric 用來識別服務的識別碼。
- 用來啟動應用程式 (ExeHost) 的命令。
- 必須執行才能安裝/設定應用程式 (SetupEntrypoint) 的任何指令碼。

以下是 `ServiceManifest.xml` 檔案的範例：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

我們來看看您需要更新的不同檔案部分：

### ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- 您可以挑選任何您要用於 `ServiceTypeName` 的名稱。此值在 `ApplicationManifest.xml` 檔案中用來識別服務。
- 您必須指定 `UseImplicitHost="true"`。此屬性會告知 Service Fabric，此服務是以獨立式 (Self-Contained) 應用程式為基礎，所以 Service Fabric 只要將它當作程序啟動並監視其健康狀況即可。

### CodePackage
CodePackage 元素指定服務程式碼的位置 (和版本)。

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` 元素用來在包含服務程式碼的應用程式封裝中指定目錄的名稱。`CodePackage` 也有 `version` 屬性。這可用來指定程式碼的版本，而利用 Service Fabric 的應用程式生命週期管理基礎結構，也可能用來升級服務的程式碼。
### SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntrypoint 元素用來指定任何應在服務的程式碼啟動前執行的可執行檔或批次檔。這是選擇性元素，所以如果不需要初始化/安裝程式則不需納入該元素。每次重新啟動服務時，就會執行 SetupEntrypoint。

只有一個 SetupEntrypoint，所以如果應用程式的 setup/config 需要多個指令碼，則必須在單一批次檔上搭配 setup/config 指令碼。如同 SetupEntryPoint 元素，SetupEntrypoint 可以執行任何類型的檔案：可執行檔、批次檔和 PowerShell Cmdlet。在上述範例中，SetupEntrypoint 是以位於 Code 目錄的 `scripts` 子目錄中的批次檔 LaunchConfig.cmd 為基礎 (假設 WorkingFolder 元素已設為 Code)。

### 進入點

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

服務資訊清單檔中的 `Entrypoint` 元素用來指定如何啟動服務。`ExeHost` 元素指定應用來啟動服務的可執行檔 (和引數)。

- `Program` 指定應執行才能啟動服務的可執行檔名稱。
- `Arguments` 指定應傳遞至可執行檔的引數。這可以是具有引數的參數清單。
- `WorkingFolder` 指定即將啟動之程序的工作目錄。您可以指定兩個值：
	- `CodeBase` 指定工作目錄即將設為應用程式封裝中的 code 目錄 (如下所示結構中的 `Code` 目錄)。
	- `CodePackage` 指定工作目錄即將設為應用程式封裝的根目錄 (`MyServicePkg`)。
- `WorkingFolder` 適合用來設定正確的工作目錄，以便應用程式或初始化指令碼使用相對路徑。

### Endpoints

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
`Endpoint` 元素指定應用程式可以接聽的端點。在此範例中，Node.js 應用程式會接聽連接埠 3000。

## 編輯應用程式資訊清單檔

設定 `Servicemanifest.xml` 檔案之後，您需要對 `ApplicationManifest.xml` 檔進行一些變更，以確保使用正確的服務類型和名稱。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

### ServiceManifestImport

在 `ServiceManifestImport` 元素中，您可以指定要包含在應用程式中的一個或多項服務。`ServiceManifestName` 指定 `ServiceManifest.xml` 檔案所在的目錄名稱，用來參考服務。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### 設定記錄
對於來賓可執行檔，最好能夠查看主控台記錄檔，以查明應用程式和設定指令碼是否顯示任何錯誤。在 `ServiceManifest.xml` 檔案中，可使用 `ConsoleRedirection` 元素設定主控台重新導向。

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection` 可用來將主控台輸出 (stdout 和 stderr) 重新導向到工作目錄，以便用來驗證在 Service Fabric 叢集中設定或執行應用程式期間沒有發生任何錯誤。

	* `FileRetentionCount` 決定工作目錄中儲存多少個檔案。比方說，值為 5 表示工作目錄中會儲存先前 5 次執行的記錄檔。
	* `FileMaxSizeInKb` 指定記錄檔的大小上限。

記錄檔會儲存在服務的其中一個工作目錄中。若要判斷檔案的位置，您必須使用 Service Fabric Explorer 來判斷服務執行所在的節點以及目前使用的工作目錄。本文稍後會說明此程序。

### 部署
最後一個步驟是部署應用程式。下列 PowerShell 指令碼示範如何將應用程式部署至本機開發叢集，並啟動新的 Service Fabric 服務。

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Service Fabric 服務可以各種「組態」部署。 例如，它可部署為單一或多個執行個體，也可以 Service Fabric 叢集的每個節點上有一個服務執行個體的方式部署。

`New-ServiceFabricService` Cmdlet 的 `InstanceCount` 參數用來指定應在 Service Fabric 叢集中啟動多少個服務執行個體。您可以根據要部署的應用程式類型來設定 `InstanceCount` 值。兩個最常見的案例包括：

* `InstanceCount = "1"`。在此情況下，只會在叢集中部署一個服務執行個體。Service Fabric 的排程器會決定即將部署服務的節點。

* `InstanceCount ="-1"`。在此情況下，Service Fabric 叢集中的每個節點上會部署一個服務執行個體。最後的結果會是叢集中的每個節點有一個 (且僅只一個) 服務執行個體。

這是很有用的前端應用程式 (例如 REST 端點) 組態，因為用戶端應用程式只需要「連線」到叢集中的任何節點即可使用端點。當 Service Fabric 叢集的所有節點都連線到負載平衡器時，也可使用此組態，以便將用戶端流量分散於在叢集中所有節點上執行的服務。

### 來查執行中的應用程式

在 Service Fabric Explorer 中，找出執行服務的節點。在此範例中，它是在 Node1 上執行：

![服務執行所在的節點](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

如果您巡覽至節點並瀏覽至應用程式，您會看到基本節點資訊，包括它在磁碟上的位置。

![在磁碟上的位置](./media/service-fabric-deploy-existing-app/locationondisk2.png)

如果您使用 [伺服器總管] 瀏覽至目錄，您可以找到工作目錄和服務的記錄檔資料夾，如下所示。

![記錄檔的位置](./media/service-fabric-deploy-existing-app/loglocation.png)

## 使用 Visual Studio 封裝現有應用程式

Visual Studio 會提供 Service Fabric 服務範本，協助您將來賓可執行檔部署至 Service Fabric 叢集。您必須進行下列步驟以完成發佈︰

1. 選擇 [檔案] -> [新增專案]，然後建立新的 Service Fabric 應用程式
2. 選擇 [來賓可執行檔] 做為服務範本
3. 按一下 [瀏覽] 以選取內含可執行檔的資料夾，並填入其餘參數以建立新的服務
  - [程式碼封裝行為] 可在設定後，將資料夾的所有內容複製到 Visual Studio 專案，因此適合用在可執行檔不會變更時。如果您預期會變更可執行檔，並想要以動態方式取得新組建，則可以選擇改為連結到資料夾。
  - [程式] 會選擇應執行以便啟動服務的可執行檔。
  - [引數] 會指定應傳遞至可執行檔的引數。這可以是具有引數的參數清單。
  - [WorkingFolder] 會選擇即將啟動之程序的工作目錄。您可以指定兩個值：
  	- [CodeBase] 會指定工作目錄即將設為應用程式封裝中的 code 目錄 (如下所示結構中的 `Code` 目錄)。
    - [CodePackage] 會指定工作目錄即將設為應用程式封裝的根目錄 (`MyServicePkg`)。
4. 指定服務的名稱，然後按一下 [確定]
5. 如果服務需要用來進行通訊的端點，您現在可以新增 Protocol、Port 和 Type 到 ServiceManifest.xml 檔案 (舉例來說)︰```<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />```
6. 您現在可以藉由在 Visual Studio 中偵錯方案，嘗試對本機叢集執行封裝和發佈動作。準備好時，即可將應用程式發佈至遠端叢集，或將方案簽入到原始檔控制。

>[AZURE.NOTE] 您可以在 Visual Studio 中建立應用程式專案時，使用連結的資料夾。此資料夾會從專案內連結到來源位置，讓您可以在其來源目的地更新來賓可執行檔，讓這些更新成為組建的應用程式封裝的一部分。

## 後續步驟
在本文中，您已經學會如何封裝來賓可執行檔並部署至 Service Fabric。接下來，您可以閱讀這個主題的其他內容。

- [GitHub 上封裝和部署來賓可執行檔的範例](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication)，包括封裝工具預先發行版本的連結
- [部署多個來賓可執行檔](service-fabric-deploy-multiple-apps.md)
- [使用 Visual Studio 建立第一個 Service Fabric 應用程式](service-fabric-create-your-first-application-in-visual-studio.md)

<!---HONumber=AcomDC_0615_2016-->