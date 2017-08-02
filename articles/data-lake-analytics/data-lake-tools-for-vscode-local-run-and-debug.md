---
title: "Azure Data Lake Tools - 使用 Visual Studio Code 來進行 U-SQL 本機執行和本機偵錯 | Microsoft Docs"
description: "了解如何使用 Azure Data Lake Tools for Visual Studio Code 來進行本機執行和本機偵錯。"
Keywords: "VScode,Azure Data Lake Tools,本機執行,本機偵錯,預覽儲存體檔案,上傳至儲存體路徑"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: DJ
editor: jejiang
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: e5e767a6f31be052c9bae059b49b31d59fdfd894
ms.contentlocale: zh-tw
ms.lasthandoff: 07/17/2017

---

# <a name="u-sql-local-run-and-local-debug-with-visual-studio-code"></a>使用 Visual Studio Code 來進行 U-SQL 本機執行和本機偵錯

## <a name="prerequisites"></a>必要條件
- Azure Data Lake Tools for Visual Studio Code。 如需相關指示，請參閱[使用 Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
- C# for Visual Studio Code (如果您想要執行 U-SQL 本機偵錯)。

   ![Data Lake Tools for Visual Studio Code 安裝 vscodeCsharp](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-install-ms-vscodecsharp.png)
   
   > [!NOTE]
   > U-SQL 本機執行和偵錯功能目前僅支援 Windows 使用者。 


## <a name="set-up-u-sql-local-run-environment"></a>設定 U-SQL 本機執行環境

1. 按 **CTRL+SHIFT+P** 來開啟命令選擇區，然後輸入 **ADL: Download LocalRun Dependency** 來下載套件。  

   ![DownloadLocalRun](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/DownloadLocalRun.png)

2. 從下面輸出面板中顯示的路徑找出相依性套件，然後安裝 BuildTools 及安裝 Win10SDK 10240。 例如：  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency
`  
  ![LocateDependency](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

- 安裝 BuildTools - 依照精靈指示來進行安裝。   

  ![InstallBuildTools](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)
- 安裝 Win10SDK 10240 - 依照安裝指示來進行安裝。  

  ![InstallWin10SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)
3. 設定環境變數，將 **SCOPE_CPP_SDK** 環境變數設定為：  
`C:\Users\xxx\.vscode\extensions\usqlextpublisher.usql-vscode-ext-x.x.x\LocalRunDependency\CppSDK_3rdparty
`  
請務必重新啟動 OS 來讓環境變數設定生效。  

   ![ConfigSCOPE_CPP_SDK](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/ConfigScopeCppSDk.png)

## <a name="start-local-run-service-and-submit-u-sql-job-to-local-account"></a>啟動本機執行服務並將 U-SQL 作業提交給本機帳戶 
如果您是初次使用者，系統會提示您使用 **ADL: Download Localrun Dependency** 來下載套件 (如果尚未下載的話)。
1. 按 **CTRL+SHIFT+P** 來開啟「命令選擇區」，然後輸入 **ADL: Start Local Run Service**。
2. 第一次使用時，請接受使用者授權合約。 

   ![接受使用者授權合約 ](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. 隨即會顯示 CMD 主控台。 如果您是初次使用者，就必須輸入 3，然後輸入用於資料輸入和輸出的本機資料夾路徑。 針對其他選項，您可以直接使用預設值。 

   ![Data Lake Tools for Visual Studio Code 本機執行 CMD](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. 按 CTRL+SHIFT+P 來開啟「命令選擇區」並輸入 **ADL: Submit Job**，然後選取 [Local] \(本機\) 以將作業提交給您的本機帳戶。

   ![Data Lake Tools for Visual Studio Code 選取本機](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. 提交作業之後，您可以按一下輸出視窗中的 jobUrl 來查看提交詳細資料。 您也可以從 CMD 主控台檢視作業提交狀態，如果想要了解更多作業詳細資料，請在 CMD 主控台中輸入 7。

   ![Data Lake Tools for Visual Studio Code 本機執行輸出](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Data Lake Tools for Visual Studio Code 本機執行 CMD 狀態](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 



## <a name="start-local-debug-for-u-sql-job"></a>開始 U-SQL 作業的本機偵錯  
如果您是初次使用者，系統會提示您使用 **ADL: Download Localrun Dependency** 來下載套件 (如果尚未下載的話)。
  
1. 按 **CTRL+SHIFT+P** 來開啟「命令選擇區」，然後輸入 **ADL: Start Local Run Service**。 隨即會顯示 CMD 視窗。 請確定已設定 **DataRoot**。
3. 在您的 C# 程式碼後置中設定中斷點。
4. 返回指令碼編輯器，按 **CTRL+SHIFT+P** 來開啟「命令選擇區」，然後輸入 **Local Debug** 來啟動您的本機偵錯服務。

![Data Lake Tools for Visual Studio Code 本機偵錯結果](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>後續步驟
- 若要了解如何使用 Azure Data Lake Tools for Visual Studio Code，請參閱[使用 Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)。
- 如需 Data Lake Analytics 的入門資訊，請參閱[教學課程︰開始使用 Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)。
- 如需使用 Data Lake Tools for Visual Studio 的相關資訊，請參閱[教學課程：使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
- 如需有關開發組件的資訊，請參閱[針對 Azure Data Lake Analytics 作業開發 U-SQL 組件](data-lake-analytics-u-sql-develop-assemblies.md)。
