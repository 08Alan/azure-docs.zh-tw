<properties
   pageTitle="Azure 資源管理員概觀 | Microsoft Azure"
   description="描述如何使用 Azure 資源管理員在 Azure 上進行資源的部署、管理及存取控制。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/27/2016"
   ms.author="tomfitz"/>

# Azure 資源管理員概觀

應用程式的基礎結構通常由許多元件所組成 – 或許是虛擬機器、儲存體帳戶和虛擬網路，或者 web 應用程式、資料庫、資料庫伺服器和協力廠商服務。您看不到這些元件做為個別的實體，而是看到它們做為單一實體相關且彼此相依的組件。您會想要將其當成群組來部署、管理和監視。Azure 資源管理員可讓您將方案中的資源做為群組使用。您可以透過單一、協調的作業來部署、更新或刪除方案的所有資源。您會使用部署的範本，且該範本可以用於不同的環境，例如測試、預備和生產環境。資源管理員會提供安全性、稽核和標記功能，以協助您在部署後管理您的資源。

## 使用資源管理員的優點

資源管理員會提供數個優點：

- 您可以以群組形式部署、管理及監視方案的所有資源，而不是個別處理這些資源。
- 您可以在整個方案週期重複部署方案，並確信您的資源會部署在一致的狀態中。
- 您可以使用宣告式範本來定義您的部署。
- 您可以定義之間的相依性，使得以正確的順序部署資源。
- 因為角色型存取控制 (RBAC) 會原生整合至管理平台，您可以將存取控制套用至資源群組中的所有服務。
- 您可以將標籤套用至資源，以便以邏輯方式組織訂用帳戶中的所有資源。
- 您可以檢視整個群組或共用相同標記之資源群組的彙總成本，為您的組織釐清計費。

資源管理員提供一個部署和管理方案的新方式。如果您使用較舊的部署模型並想要了解這些變更，請參閱[瞭解資源管理員部署和傳統部署](resource-manager-deployment-model.md)。

## 指引

下列建議將協助您在使用您的方案時充分利用資源管理員。

1. 透過資源管理員範本中的宣告式語法定義和部署基礎結構，而非透過命令式指令。
2. 在範本中定義所有的部署和設定步驟。您在設定方案時應該沒有手動步驟。
3. 執行命令式指令來管理您的資源，例如啟動或停止應用程式或機器。
4. 利用與資源群組中相同的生命週期排列資源。將標記用於資源的所有其他組織方式。

## 資源群組

資源群組是保留應用程式相關資源的容器。資源群組可以包含應用程式的所有資源，或只保留以邏輯方式分組在一起的資源。您可以決定如何根據對組織最有利的方式，將資源配置到資源群組。

定義資源群組時，必須考慮一些重要因素：

1. 群組中的所有資源應該共用相同的生命週期。您將一起部署、更新和刪除它們。如果類似資料庫伺服器這樣的資源必須存在於不同的部署週期，它應該位於另一個資源群組中。
2. 每個資源只能存在於一個資源群組中。
3. 您可以隨時在資源群組中新增或移除資源。
4. 您可以將資源從一個資源群組移動到另一個群組。如需詳細資訊，請參閱[將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。
4. 資源群組可以包含位於不同區域中的資源。
5. 資源群組可以用來設定系統管理動作的存取控制範圍。
6. 資源可以連結至另一個資源群組中的資源，前提是兩個資源必須與彼此互動，但它們沒有共用相同的生命週期 (例如，多個應用程式連接至一個資料庫)。如需詳細資訊，請參閱[連結 Azure 資源管理員中的資源](resource-group-link-resources.md)。

## 資源提供者

資源提供者是一項服務，提供資源讓您可透過資源管理員進行部署及管理。每個資源提供者都會提供 REST API 作業，以便能運用資源。例如，如果想要部署 Azure 金鑰保存庫來儲存金鑰和密碼，您會使用 **Microsoft.KeyVault** 資源提供者。此資源提供者提供名為 **vaults** 的資源類型來建立金鑰保存庫，以及名為 **vaults/secrets** 的資源類型來建立金鑰保存庫中的密碼。您可以查看 REST API 作業 (例如[金鑰保存庫 REST API 作業](https://msdn.microsoft.com/library/azure/dn903609.aspx))，藉此了解資源提供者。

若要部署和管理基礎結構，您需要了解資源提供者的詳細資料；例如提供的資源類型為何、REST API 作業的版本號碼、支援的作業以及設定要建立的資源類型值時要使用的結構描述。如需了解支援的資源提供者，請參閱[資訊管理員提供者、區域、API 版本及結構描述](resource-manager-supported-services.md)。

## 範本部署

利用資源管理員，您可以建立定義應用程式之部署和設定的簡單範本 (以 JSON 格式)。此範本就是所謂的資源管理員範本，並提供定義部署的宣告方式。藉由使用範本，您可以在整個應用程式週期重複部署應用程式，並確信您的資源會部署在一致的狀態中。

在此範本中，您會定義應用程式的基礎結構、如何設定基礎結構，以及如何將應用程式程式碼發佈至該基礎結構。您不需要擔心部署的順序，因為 Azure 資源管理員會分析相依性，確保以正確的順序建立資源。如需詳細資訊，請參閱[定義 Azure 資源管理員範本中的相依性](resource-group-define-dependencies.md)。

當您從 Marketplace 建立方案，方案會自動包含部署範本。您不必從頭建立您的範本，因為您可以從方案的範本開始，並自訂範本以符合您的特定需求。將資源群組的目前狀態匯出至範本，或檢視特定部署所用的範本，即可擷取現有資源群組的範本。檢視匯出的範本有助於了解範本語法。若要深入了解如何使用匯出的範本，[從現有資源匯出 Azure Resource Manager 範本](resource-manager-export-template.md)。

您不需要在單一的範本中定義整個基礎結構。通常的合理作法是將您的部署需求分成一組有目標及特定目的的範本。您可以輕鬆地將這些份本重複使用於不同的方案。若要部署特定的方案，您會建立連結所有必要範本的主版範本。如需詳細資訊，請參閱[透過 Azure 資源管理員使用連結的範本](resource-group-linked-templates.md)。

您也可以使用範本進行基礎結構的更新。例如，您可以將新的資源新增至您的應用程式，並將組態規則新增至已部署的資源。如果此範本會指定建立新的資源，但該資源已經存在，Azure 資源管理員會執行更新，而不必建立新資產。Azure 資源管理員會將現有資產更新為和新資產相同的狀態。或者，您可以指定資源管理員刪除範本中任何未指定的資源。若要了解部署時的各種選項，請參閱[使用 Azure 資源管理員範本部署應用程式](resource-group-template-deploy.md)。

您可以指定範本中的參數，以允許部署中的自訂和彈性。例如，您可以傳遞可修改測試環境部署的參數值。藉由指定參數，您可以將相同的部署範本用於所有的應用程式環境。

當您需要其他作業 (例如安裝不包含在安裝程式的特定軟體) 時，資源管理員會提供案例的延伸模組。如果您已經使用組態管理服務，例如 DSC、Chef 或 Puppet，您可以透過使用擴充功能繼續使用該服務。

最後，範本會成為應用程式原始碼的一部分。您可以檢查您的原始程式碼存放庫，並隨著您的應用程式發展加以更新。您可以透過 Visual Studio 編輯範本。

如需關於定義範本的詳細資訊，請參閱[編寫 Azure 資源管理員範本](resource-group-authoring-templates.md)。

如需建立範本的逐步指示，請參閱 [逐Resource Manager 範本步解說](resource-manager-template-walkthrough.md)。

如需將您的方案部署到不同環境的指引，請參閱 [Microsoft Azure 中的開發和測試環境](solution-dev-test-environments.md)。

## 標記

資源管理員提供標記的功能，可讓您根據管理或計費需求將資源分類。當您有複雜的資源群組和資源集合，而且必須以最有利的方式視覺化資產，您可能會想要使用標記。例如，您可以標記在組織中具有類似角色，或屬於相同部門的資源。如果不使用標記，貴組織中的使用者可建立多個資源，如此對於日後的身分識別及管理來說可能十分困難。例如，您會希望刪除特定專案的所有資源，但如果這些資源並未標記為該專案所有，您就需要手動加以尋找。標記是降低訂用帳戶不必要成本的重要方法。

資源不需要位於相同的資源群組即可共用標記。您可以建立自己的標記分類，以確保組織中的所有使用者都使用常見的標記，而不是使用者意外套用稍有不同的標記 (例如 "dept" 而不是 "department")。

如需標記的詳細資訊，請參閱[使用標記來組織您的 Azure 資源](resource-group-using-tags.md)。您可以建立[自訂原則](#manage-resources-with-customized-policies)，表示必須在部署時在資源中加入標記。

## 存取控制

資源管理員可讓您控制哪些人能存取組織的特定動作。以原生方式將 OAuth 和角色型存取控制 (RBAC) 整合至管理平台，並將存取控制套用至資源群組中的所有服務。您可以將使用者新增至預先定義的平台和資源專用的角色，並將這些角色套用至訂用帳戶、資源群組或資源以限制存取。例如，您可以利用稱為 SQL DB 參與者的預先定義角色 (可讓使用者管理資料庫，但無法管理資料庫伺服器或安全性原則)。您在組織中新增的使用者需要 SQL DB 參與者的這類存取，並將該角色套用至訂用帳戶、資源群組或資源。

資源管理員會自動記錄使用者的動作以進行稽核。如需使用稽核記錄檔的詳細資訊，請參閱[使用資源管理員來稽核作業](resource-group-audit.md)。

如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](./active-directory/role-based-access-control-configure.md)。[RBAC：內建角色](./active-directory/role-based-access-built-in-roles.md)主題包含內建角色與允許動作的清單。內建角色包含擁有者、讀取者和參與者等一般角色，以及虛擬機器參與者、虛擬網路參與者和 SQL 安全性管理員等服務專屬角色 (僅列舉幾個可用的角色)。

您也可以明確地鎖定重要的資源，以防止使用者刪除或修改它們。如需詳細資訊，請參閱[使用 Azure 資源管理員來鎖定資源](resource-group-lock-resources.md)。

如需最佳做法，請參閱 [Azure 資源管理員的安全性考量](best-practices-resource-manager-security.md)

## 使用自訂的原則來管理資源

資源管理員可讓您建立自訂的原則，以便管理您的資源。您建立的原則類型可以包含下列各種案例：在資源上強制執行命名慣例、限制可部署哪些資源的類型和執行個體、限制哪個區域可以託管資源類型，或要求資源上有標記值以便依部門進行計費。您可建立原則來協助降低成本，並維護訂用帳戶中的一致性。如需詳細資訊，請參閱[使用原則來管理資源和控制存取](resource-manager-policy.md)。

## 一致的管理層

資源管理員透過 Azure PowerShell、Azure CLI for Mac、Linux 和 Windows、Azure 入口網站或 REST API 提供完全相容的作業。您可以使用最適合您的介面，而且可以在介面之間快速移動，而不會造成混淆。入口網站甚至會顯示在網站外部採取動作的通知。

如需 PowerShell 的相關資訊，請參閱 [Azure PowerShell 搭配資源管理員使用](powershell-azure-resource-manager.md)和 [Azure 資源管理員 Cmdlet](https://msdn.microsoft.com/library/azure/dn757692.aspx)

如需 Azure CLI 的相關資訊，請參閱 [Azure CLI for Mac、Linux 和 Windows 搭配 Azure 資源管理使用](xplat-cli-azure-resource-manager.md)。

如需 REST API 的相關資訊，請參閱 [Azure 資源管理員 REST API 參考](https://msdn.microsoft.com/library/azure/dn790568.aspx)。若要檢視已部署資源的 REST 作業，請參閱[使用 Azure 資源總管來檢視及修改資源](resource-manager-resource-explorer.md)。

如需使用入口網站的相關資訊，請參閱[使用 Azure 入口網站來管理您的 Azure 資源](./azure-portal/resource-group-portal.md)。

Azure 資源管理員支援跨原始資源共用 (CORS)。利用 CORS，您可以從位於不同網域的 Web 應用程式呼叫資源管理員 REST API 或 Azure 服務 REST API。若沒有 CORS 支援，網頁瀏覽器將會阻止某個網域中的應用程式存取另一個網域中的資源。資源管理員會對所有具備有效驗證認證的要求啟用 CORS。

## SDK 與範例

Azure SDK 可供多個語言和平台使用。這些語言實作都是透過其生態系統的封裝管理員和 GitHub 提供。

每個 SDK 中的程式碼都是從 Azure RESTful API 規格產生。這些規格是開放原始碼，以 Swagger 2.0 規格為基礎。SDK 程式碼是透過稱為 AutoRest 的開放原始碼專案所產生。AutoRest 會將這些 RESTful API 規格轉換成多種語言版本的用戶端程式庫。如果您想要改善 SDK 中所產生之程式碼的任何層面，用來建立 SDK 的完整工具集皆開放免費取得，且是根據廣為採用的 API 規格格式。

**範例**︰以您所選的語言即刻開始使用。

- [.NET](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=dotnet) 敬請期待
- [Java](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=java) 敬請期待
- [Node.js](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=nodejs)
- [Python](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=python)
- [PHP](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=php) 敬請期待
- [Ruby](https://azure.microsoft.com/documentation/samples/?service=azure-resource-manager&platform=ruby)

**開放原始碼 SDK 儲存機制**︰歡迎提供意見、問題並提取要求。

- [.NET](https://github.com/Azure/azure-sdk-for-net)
- [Java](https://github.com/Azure/azure-sdk-for-java)
- [Node.js](https://github.com/Azure/azure-sdk-for-node)
- [PHP](https://github.com/Azure/azure-sdk-for-php)
- [Python](https://github.com/Azure/azure-sdk-for-python)
- [Ruby](https://github.com/Azure/azure-sdk-ruby)

> [AZURE.NOTE] 如果 SDK 未提供必要的功能，您也可以直接呼叫 [Azure REST API](https://msdn.microsoft.com/library/azure/dn790568.aspx)。

## 後續步驟

- 若要深入了解如何使用匯出的範本，[從現有資源匯出 Azure Resource Manager 範本](resource-manager-export-template.md)。
- 如需建立範本的逐步解說，請參閱 [Resource Manager 範本逐步解說](resource-manager-template-walkthrough.md)。
- 若要了解您可以在範本中使用的函式，請參閱[範本函式](resource-group-template-functions.md)
- 如需有關如何搭配使用 Visual Studio 與 Resource Manager 的詳細資訊，請參閱[透過 Visual Studio 建立和部署 Azure 資源群組](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

以下是此概觀的示範影片。

[AZURE.VIDEO azure-resource-manager-overview]

<!---HONumber=AcomDC_0713_2016-->