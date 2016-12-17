---
title: "如何在 Linux VM 上安裝 Apache Qpid Proton-C | Microsoft Docs"
description: "如何使用 Azure 虛擬機器建立 CentOS Linux VM，以及如何建置和安裝 Apache Qpid Proton-C 程式庫。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3158d69f-c409-494d-9a6c-32613c1a3c74
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 226703f9b4a4b295231ecd48d719638321ae55ef


---
# <a name="install-apache-qpid-proton-c-on-an-azure-linux-vm"></a>在 Azure Linux VM 上安裝 Apache Qpid Proton-C
[!INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

本節說明如何使用 Azure 虛擬機器建立 CentOS Linux VM，以及如何下載、建置和安裝 Apache Qpid Proton-C 程式庫以及 Python 和 PHP 語言繫結。 完成這些步驟之後，您可以執行本指南隨附的 Python 和 PHP 範例。

第一個步驟是使用 [Azure 傳統入口網站][Azure 傳統入口網站]執行。 下列螢幕擷取畫面顯示如何建立名為 "scott-centos" 的 CentOS VM：

![Azure Linux VM 上的 Proton][0]

佈建之後，入口網站會顯示下列資訊：

![Azure Linux VM 上的 Proton][1]

為了登入電腦，您必須知道 SSH 的端點連接埠。 您可以從 [Azure 傳統入口網站][Azure 傳統入口網站]取得此值，方法是選取新建立的 VM，然後按一下 [端點] 索引標籤。 下列螢幕擷取畫面顯示這部電腦的公開 SSH 連接埠為 57146。

![Azure Linux VM 上的 Proton][2]

您現在可以使用 SSH 連線到電腦。 此範例使用 PuTTY 工具，如下列螢幕擷取畫面所示：

![Azure Linux VM 上的 Proton][3]

針對 Python 和 PHP 應用程式，此範例會使用 Apache 的 Proton 用戶端程式庫。 可從 [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html) 下載這些程式庫。 散發套件中的讀我檔案說明安裝相依性和建置 Proton 所需的步驟。 以下是步驟的摘要：

1. 編輯 yum 設定檔 (/etc/yum.conf) 並且將排除更新核心標頭 (\# exclude=kernel\*) 註解排除。 這是安裝 gcc 編譯器的必要動作。
2. 安裝必要條件套件：
   
   ```
   # required dependencies 
   yum install gcc cmake libuuid-devel
   
   # dependencies needed for ssl support
   yum install openssl-devel
   
   # dependencies needed for bindings
   yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
   
   # dependencies needed for python docs
   yum install epydoc
   ```
3. 下載 Proton 程式庫：
   
   ```
   [azureuser@this-user ~]$ wget http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
   --2016-04-17 14:45:03--  http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
   Resolving apache.panu.it (apache.panu.it)... 81.208.22.71
   Connecting to apache.panu.it (apache.panu.it)|81.208.22.71|:80... connected.
   HTTP request sent, awaiting response... 200 OK
   Length: 868226 (848K) [application/x-gzip]
   Saving to: ‘qpid-proton-0.9.tar.gz’
   
   qpid-proton-0.9.tar.gz                               
   
   100%[====================================================================================================================>] 847.88K   102KB/s    in 8.4s    
   
   2016-04-17 14:45:12 (101 KB/s) - ‘qpid-proton-0.9.tar.gz’ saved [868226/868226]
   ```
4. 從散發封存解壓縮 Proton 程式碼：
   
   ```
   tar xvfz qpid-proton-0.9.tar.gz
   ```
5. 使用取自讀我檔案的下列步驟建置和安裝程式碼中：
   
   ```
   From the directory where you found this README file:    
   
   mkdir build cd build
   
   # Set the install prefix. You may need to adjust depending on your        
   # system.        
   cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
   
   # Omit the docs target if you do not wish to build or install        
   # documentation.        
   make all docs
   
   # Note that this step will require root privileges.        
   make install
   ```

執行這些步驟之後，Proton 會安裝在電腦上並可供使用。

## <a name="next-steps"></a>後續步驟
準備好進行深入了解嗎？ 請造訪下列連結：

* [服務匯流排 AMQP 概觀][服務匯流排 AMQP 概觀]

[服務匯流排 AMQP 概觀]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[Azure 傳統入口網站]: http://manage.windowsazure.com





<!--HONumber=Nov16_HO3-->


