---
title: "使用 Azure Batch API 和工具來開發大規模的平行處理解決方案 | Microsoft Docs"
description: "了解可搭配 Azure Batch 服務用來開發解決方案的 API 和工具。"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 405cb202e2b6da0dd24e6f7b8ed55ce8c56e0fe1
ms.lasthandoff: 03/31/2017

---


# <a name="overview-of-batch-apis-and-tools"></a>Batch API 和工具的概觀

使用 Azure Batch 處理平行工作負載通常會透過其中一個 [Batch API](#batch-development-apis)，以程式設計方式進行。 用戶端應用程式或服務可使用 Batch API 來與 Batch 服務進行通訊。 Batch API 可讓您建立和管理計算節點 (虛擬機器或雲端服務) 集區。 您可以接著排程要在這些節點上執行的作業及工作。 

您可以為組織有效率地處理大量工作負載，或提供前端服務給客戶，讓他們可以在一個、數百個或甚至數千個節點上，依需要或依排程執行作業和工作。 您也可以在 [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json)之類的工具所管理的大型工作流程中使用 Azure Batch。

> [!TIP]
> 當您準備鑽研 Batch API 以深入了解它所提供的功能時，請參閱 [適用於開發人員的 Batch 功能概觀](batch-api-basics.md)。
> 
> 

## <a name="azure-accounts-youll-need"></a>您需要的 Azure 帳戶
當您開發 Batch 解決方案時，您將在 Microsoft Azure 中使用下列帳戶。

* **Azure 帳戶和訂用帳戶** - 如果您沒有 Azure 訂用帳戶，您可以啟用 [MSDN 訂戶權益][msdn_benefits]，或是註冊[免費 Azure 帳戶][free_account]。 當您建立帳戶時，會為您建立預設訂用帳戶。
* **Batch 帳戶** - Azure Batch 資源 (包括集區、計算節點、作業和工作) 都與 Azure Batch 帳戶相關聯。 當您的應用程式對 Batch 服務提出要求時，可以使用 Azure Batch 帳戶名稱、帳戶的 URL 及存取金鑰驗證此要求。 您可以在 Azure 入口網站中 [建立 Batch 帳戶](batch-account-create-portal.md) 。
* **儲存體帳戶** - Batch 內建就支援處理 [Azure 儲存體][azure_storage]中的檔案。 幾乎每個 Batch 案例都會使用 Azure Blob 儲存體，來預備工作執行的程式及其處理的資料，以及儲存其產生的輸出資料。 若要建立儲存體帳戶，請參閱 [關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。

## <a name="batch-development-apis"></a>Batch 開發 API
應用程式和服務可以發出直接的 REST API 呼叫，或使用一或多個下列用戶端程式庫來執行和管理 Azure Batch 工作負載。

| API | API 參考資料 | 下載 | 教學課程 | 程式碼範例 | 其他資訊 |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[MSDN][batch_rest] |N/A |- |- | [支援的版本](https://docs.microsoft.com/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[教學課程](batch-dotnet-get-started.md) |[GitHub][api_sample_net] | [版本資訊](https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/Client/changelog.md) |
| **Batch Python** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[教學課程](batch-python-tutorial.md)|[GitHub][api_sample_python] | [讀我檔案](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- | [讀我檔案](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** (預覽) |[github.io][api_java] |[Maven][api_java_jar] |- |[讀我檔案][api_sample_java] | [讀我檔案](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-command-line-tools"></a>Batch 命令列工具

開發 API 所提供的功能也可透過命令列工具取得︰ 

* [Batch PowerShell Cmdlet][batch_ps]：[Azure PowerShell](/powershell/azureps-cmdlets-docs) 模組中的 Batch PowerShell Cmdlet 可讓您使用 PowerShell 來管理 Batch 資源。
* [Azure CLI](/cli/azure/overview)：Azure 命令列介面 (Azure CLI) 是跨平台工具組，可提供用來與許多 Azure 服務 (包括 Batch) 互動的殼層命令。

## <a name="batch-resource-management"></a>Batch 資源管理

適用於 Batch 的 Azure Resource Manager API 會提供 Batch 帳戶的程式設計存取方式。 使用這些 API，您可以透過程式設計方式管理 Batch 帳戶、配額和應用程式套件。  

| API | API 參考資料 | 下載 | 教學課程 | 程式碼範例 |
| --- | --- | --- | --- | --- |
| **Batch Resource Manager REST** |[docs.microsoft.com][api_rest_mgmt] |N/A |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Resource Manager .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [教學課程](batch-management-dotnet.md) |[GitHub][api_sample_net] |


## <a name="batch-tools"></a>Batch 工具
雖然不一定要使用 Batch 建置解決方案，但以下是建置和偵錯 Batch 應用程式和服務時所要使用的一些重要工具。

* [Azure 入口網站][portal]︰您可以在 Azure 入口網站的 Batch 刀鋒視窗中建立、監視和刪除 Batch 集區、作業和工作。 您可以在執行作業時檢視上述和其他資源的狀態資訊，甚至從您集區中的計算節點下載檔案 (例如，在進行疑難排解時下載失敗的工作 `stderr.txt` )。 您也可以下載可用來登入計算節點的遠端桌面 (RDP) 檔案。
* [Azure Batch 總管][batch_explorer]︰Batch 總管可提供與 Azure 入口網站類似的 Batch 資源管理功能，但是在獨立的 Windows Presentation Foundation (WPF) 用戶端應用程式中。 在 [GitHub][github_samples] 上可取得其中一個 Batch .NET 範例應用程式，您可以使用 Visual Studio 2015 或更新版本建置此種應用程式，並在開發及偵錯 Batch 解決方案時，使用它來瀏覽和管理 Batch 帳戶中的資源。 檢視作業、集區和工作詳細資訊、從計算節點下載檔案，以及使用您可透過 Batch 總管下載的遠端桌面 (RDP) 檔案從遠端連接到節點。
* [Microsoft Azure 儲存體總管][storage_explorer]：嚴格來說，雖然儲存體總管不算是 Azure Batch 工具，但卻是您開發和偵錯 Batch 解決方案時的另一個很實用的工具。

## <a name="next-steps"></a>後續步驟

* 請參閱 [適用於開發人員的 Batch 功能概觀](batch-api-basics.md)，這是任何準備使用 Batch 的人員不可或缺的資訊。 本文包含 Batch 服務資源 (例如集區、節點、作業和工作) 的詳細資訊，以及在建置 Batch 應用程式時可使用的許多 API 功能。
* [開始使用適用於 .NET 的 Azure Batch 程式庫](batch-dotnet-get-started.md) ，了解如何使用 C# 和 Batch .NET 程式庫，透過一般的批次工作流程來執行簡單的工作負載。 本文應該是您學習如何使用 Batch 服務的第一站。 另外還有 [Python 版本](batch-python-tutorial.md) 的教學課程。
* 下載 [GitHub 上的程式碼範例][github_samples]，看看 C# 和 Python 如何與 Batch 相互作用，以排程和處理範例工作負載。
* 查看 [Batch 學習路徑][learning_path]，了解您在學習使用 Batch 時可用的資源。


[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/\rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/resourcemanager/azurerm.batch/v2.7.0/azurerm.batch
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

