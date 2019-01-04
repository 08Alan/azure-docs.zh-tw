---
title: Azure HDInsight 工具 - 設定 Visual Studio Code 的 PySpark 互動式環境
description: 了解如何使用適用於 Visual Studio Code 的 Azure HDInsight 工具來建立及提交查詢和指令碼。
keywords: VScode,Azure HDInsight 工具,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,互動式 Hive,互動式查詢
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: bf47915ba93a4a3a7dec338395cfe0ce6aa3cdf6
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993822"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>設定 Visual Studio Code 的 PySpark 互動式環境

下列步驟說明如何在執行 [HDInsight: PySpark 互動式] 時安裝 Python 套件。

## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>在 macOS 和 Linux 上設定 PySpark 互動式環境
如果您使用 **python 3.x**，則需要針對下列步驟使用 **pip3** 命令：

1. 確定已安裝 **Python** 和 **pip**。
 
    ![Python pip 版本](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  安裝 Jupyter。
    ```
    sudo pip install jupyter
    ```
   您會在 Linux 和 macOS 上看見下列錯誤訊息：

   ![錯誤 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. 安裝 **libkrb5-dev** (僅適用 Linux)。 您可能會看到下列錯誤訊息：

   ![錯誤 2](./media/set-up-pyspark-interactive-environment/error2.png)
       
   ```Resolve:
   sudo apt-get install libkrb5-dev 
   ```

3. 安裝 **sparkmagic**。
   ```
   sudo pip install sparkmagic
   ```

4. 執行下列程式碼來確定已正確安裝 **ipywidgets**：
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![安裝包裝函式核心](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. 安裝包裝函式核心。 執行 **pip show sparkmagic**。 輸出會顯示 **sparkmagic** 安裝的路徑。 

    ![sparkmagic 位置](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. 移至該位置，然後執行：

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![jupyter kernelspec install](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. 檢查安裝狀態。

    ```
    jupyter-kernelspec list
    ```
    ![jupyter kernelspec list](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    對於可用的核心： 
    - **python2** 和 **pysparkkernel** 對應至 **python 2.x**。 
    - **python3** 和 **pyspark3kernel** 對應至 **python 3.x**。 

8. 重新啟動 VS Code，然後返回執行 [HDInsight: PySpark 互動式] 的指令碼編輯器。

## <a name="next-steps"></a>後續步驟

### <a name="demo"></a>示範
* HDInsight for VS Code：[影片](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>工具和擴充功能
* [使用適用於 Visual Studio Code 的 Azure HDInsight 工具](hdinsight-for-vscode.md)
* [使用 Azure Toolkit for IntelliJ 來建立和提交 Apache Spark Scala 應用程式](spark/apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for IntelliJ 透過 SSH 遠端偵錯 Apache Spark 應用程式](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [使用 Azure Toolkit for IntelliJ 透過 VPN 遠端偵錯 Apache Spark 應用程式](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具建立 Apache Spark 應用程式](spark/apache-spark-eclipse-tool-plugin.md)
* [透過 Hortonworks 沙箱使用 HDInsight Tools for IntelliJ](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [在 HDInsight 上搭配使用 Apache Zeppelin Notebook 和 Apache Spark 叢集](spark/apache-spark-zeppelin-notebook.md)
* [HDInsight Apache Spark 叢集中 Jupyter Notebook 的可用核心](spark/apache-spark-jupyter-notebook-kernels.md)
* [搭配 Jupyter Notebook 使用外部套件](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [在電腦上安裝 Jupyter 並連接到 HDInsight Spark 叢集](spark/apache-spark-jupyter-notebook-install-locally.md)
* [在 Azure HDInsight 中使用 Microsoft Power BI 將 Apache Hive 資料視覺化](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [使用 Apache Zeppelin 在 Azure HDInsight 中執行 Apache Hive 查詢](hdinsight-connect-hive-zeppelin.md)
