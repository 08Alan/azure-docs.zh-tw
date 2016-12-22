---
title: "在五分鐘內開始使用 Azure 儲存體 | Microsoft Docs"
description: "使用 Azure 儲存體快速入門、Visual Studio 和 Azure 儲存體模擬器，快速掌握 Microsoft Azure Blob、資料表和佇列。 在五分鐘內執行第一個 Azure 儲存體應用程式"
services: storage
documentationcenter: .net
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 582f76f8-c814-4a69-8a5c-1fd0e0d5d8f2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 12ce6b6bccf3ea2aa2945ddd775716f29cf01e1f
ms.openlocfilehash: 47b2623eb3b83220ef8e3cfafde06dab3ac3d22e


---
# <a name="get-started-with-azure-storage-in-five-minutes"></a>在五分鐘內開始使用 Azure 儲存體
## <a name="overview"></a>Overview
很容易就能開始使用 Azure 儲存體進行開發。 本教學課程示範如何取得 Azure 儲存體應用程式，並快速執行。 您可以使用 Azure SDK for .NET 隨附的「快速入門」範本。 這些「快速入門」包含已就緒可執行的程式碼，示範一些基本的 Azure 儲存體程式設計案例。

如果要在鑽研程式碼前深入了解 Azure 儲存體，請參閱 [後續步驟](#next-steps)。

## <a name="prerequisites"></a>必要條件
開始之前，您需要下列先決條件：

1. 若要編譯及建置應用程式，您必須先在電腦上安裝 [Visual Studio](https://www.visualstudio.com/) 的版本。
2. 安裝最新版 [Azure SDK for .NET](https://azure.microsoft.com/downloads/)。 此 SDK 包含 Azure 快速入門範例專案、Azure 儲存體模擬器和 [Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)。
3. 請確定您已在電腦上安裝 [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) ，因為此為我們在本教學課程使用的 Azure 快速入門範例專案所需。

    如果不確定您的電腦中安裝哪個版本的 .NET Framework，請參閱 [如何：判斷安裝的 .NET Framework 版本](https://msdn.microsoft.com/vstudio/hh925568.aspx)。 或者，按 [開始] 按鈕或 Windows 鍵，輸入 [控制台]。 然後，按一下 [程式] > [程式和功能]，並判斷已安裝的程式之間是否列出 .NET Framework 4.5。
4. 您將需要 Azure 訂用帳戶和 Azure 儲存體帳戶。

   * 若要取得 Azure 訂用帳戶，請參閱[免費試用版](https://azure.microsoft.com/pricing/free-trial/)、[購買選項](https://azure.microsoft.com/pricing/purchase-options/)和[會員優惠](https://azure.microsoft.com/pricing/member-offers/) (適用於 MSDN、Microsoft 合作夥伴網路、BizSpark 和其他 Microsoft 方案的成員)。
   * 若要在 Azure 中建立儲存體帳戶，請參閱 [如何建立儲存體帳戶](storage-create-storage-account.md#create-a-storage-account)。

## <a name="run-your-first-azure-storage-application-against-azure-storage-in-the-cloud"></a>針對雲端 Azure 儲存體執行第一個 Azure 儲存體應用程式
一旦您有了帳戶，您就可以在 Visual Studio 中使用其中一個 Azure 快速入門範例專案，建立簡單的 Azure 儲存體應用程式。 本教學課程著重 Azure Storage 儲存體的範例專案：**Azure 儲存體：Blob**、**Azure 儲存體：檔案**、**Azure 儲存體：佇列**和 **Azure 儲存體：資料表**：

1. 啟動 Visual Studio。
2. 從 [檔案] 功能表，按一下 [新增專案]。
3. 在 [新增專案] 對話方塊中，按一下 [已安裝] > [範本] > [Visual C#] > [雲端] > [快速入門] > [資料服務]。
    a. 選擇下列其中一個範本：**Azure 儲存體：Blob**、**Azure 儲存體：檔案**、**Azure 儲存體：佇列**或 **Azure 儲存體：資料表**。
    b. 確定已選取 [.NET Framework 4.5.2] 來做為目標架構。
    c. 指定您專案的名稱，並建立新的 Visual Studio 方案，如下所示：

    ![Azure 快速入門][Image1]

您可能想要在執行應用程式前檢閱原始程式碼。 若要檢閱程式碼，請在 Visual Studio 中選取 [檢視] 功能表上的 [方案總管]。 然後，按兩下 Program.cs 檔案。

現在，執行範例應用程式：

1. 在 Visual Studio 中，選取 [檢視] 功能表上的 [方案總管]。 開啟 App.config 檔案並註解化 Azure 儲存體模擬器的連接字串：

   `<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2. 取消註解 Azure 儲存體服務的連接字串，並提供 App.config 檔案中的儲存體帳戶名稱和存取金鑰：

   `<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

   若要擷取儲存體帳戶存取金鑰，請參閱 [管理儲存體存取金鑰](storage-create-storage-account.md#manage-your-storage-access-keys)。
3. 提供 App.config 檔案中的儲存體帳戶名稱和存取金鑰之後，在 [檔案] 功能表上，按一下 [全部儲存] 以儲存所有的專案檔案。
4. 在 [建置] 功能表上，按一下 [建置方案]。
5. 在 [偵錯] 功能表上，按 **F11** 逐步執行方案，或按 **F5** 執行方案。

## <a name="run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator"></a>針對 Azure 儲存體模擬器在本機執行第一個 Azure 儲存體應用程式
此 [Azure 儲存體模擬器](storage-use-emulator.md) 提供了模擬 Azure Blob、佇列和資料表服務的本機環境，以供進行開發。 您可以使用儲存體模擬器在本機測試儲存體應用程式，而不需建立 Azure 訂用帳戶或儲存體帳戶，也不會產生任何費用。

若要試用，讓我們在 Visual Studio 中使用其中一個 Azure 快速啟動範例專案，建立簡單的 Azure 儲存體應用程式。 本教學課程著重於 **Azure Blob 儲存體**、**Azure 資料表儲存體**和 **Azure 佇列儲存體**範例專案：

1. 啟動 Visual Studio。
2. 從 [檔案] 功能表，按一下 [新增專案]。
3. 在 [新增專案] 對話方塊中，按一下 [已安裝] > [範本] > [Visual C#] > [雲端] > [快速入門] > [資料服務]。
    a. 選擇下列其中一個範本：**Azure 儲存體：Blob**、**Azure 儲存體：檔案**、**Azure 儲存體：佇列**或 **Azure 儲存體：資料表**。
    b. 確定已選取 [.NET Framework 4.5.2] 來做為目標架構。
    c. 指定您專案的名稱，並建立新的 Visual Studio 方案，如下所示：

    ![Azure 快速入門][Image1]

4. 在 Visual Studio 中，選取 [檢視] 功能表上的 [方案總管]。 如果您已經新增其中一個，請開啟 App.config 檔案，並註解化 Azure 儲存體帳戶的連接字串。 然後取消註解化 Azure 儲存體模擬器的連接字串：

   `<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

您可能想要在執行應用程式前檢閱原始程式碼。 若要檢閱程式碼，請在 Visual Studio 中選取 [檢視] 功能表上的 [方案總管]。 然後，按兩下 Program.cs 檔案。

接下來，在 Azure 儲存體模擬器中執行範例應用程式：

1. 按 [開始] 按鈕或 Windows 鍵，搜尋 [Microsoft Azure 儲存體模擬器]，然後啟動此應用程式。 當啟動模擬器時，您會在 [Windows 工作檢視] 區域中看到圖示和通知。
2. 在 Visual Studio 中，按一下 [建置]  on the  。
3. 在 [偵錯] 功能表上，按 **F11** 逐步執行方案，或按 **F5** 從開始到結束執行方案。

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure 儲存體，請參閱下列資源：

* [Microsoft Azure 儲存體簡介](storage-introduction.md)
* [開始使用 Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [以 .NET 開始使用 Azure Blob 儲存體](storage-dotnet-how-to-use-blobs.md)
* [以 .NET 開始使用 Azure 表格儲存體](storage-dotnet-how-to-use-tables.md)
* [以 .NET 開始使用 Azure 佇列儲存體](storage-dotnet-how-to-use-queues.md)
* [在 Windows 上開始使用 Azure 檔案儲存體](storage-dotnet-how-to-use-files.md)
* [使用 AzCopy 命令列公用程式傳輸資料](storage-use-azcopy.md)
* [Azure 儲存體文件](https://azure.microsoft.com/documentation/services/storage/)
* [Microsoft Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Azure 儲存體服務 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png



<!--HONumber=Dec16_HO2-->


