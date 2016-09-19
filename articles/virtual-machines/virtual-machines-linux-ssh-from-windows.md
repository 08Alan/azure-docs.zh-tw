<properties 
	pageTitle="在 Windows 上使用 SSH 來連線到 Linux 虛擬機器 | Microsoft Azure" 
description="了解如何在 Windows 電腦上產生並使用 SSH 金鑰來連線到 Azure 上的 Linux 虛擬機器。" 
	services="virtual-machines-linux" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/29/2016" 
	ms.author="rasquill"/>

#如何在 Azure 上搭配 Windows 使用 SSH

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md)


本主題描述如何在 Windows 上建立和使用 **ssh-rsa** 和 **.pem** 格式的公用和私密金鑰檔案，您可以使用這些檔案，在 Azure 上以 **ssh** 命令連接至 Linux VM。如果您已經建立 **.pem** 檔案，您可以使用這些檔案來建立 Linux VM (可以使用 **ssh** 來連接)。其他數個命令使用 **SSH** 通訊協定和金鑰檔案安全地執行工作，值得注意的是 **scp** 或[安全複製](https://en.wikipedia.org/wiki/Secure_copy)，可以安全地將檔案複製到支援 **SSH** 連線的電腦，或從中複製。


## 您需要哪些 SSH 和金鑰建立程式？

**SSH** &#8212; 或 [安全殼層](https://en.wikipedia.org/wiki/Secure_Shell) &#8212; 是允許透過不安全的連接安全登入的加密連接通訊協定。它是適用於 Azure 中裝載的 Linux VM 的預設連接通訊協定，除非您設定您的 Linux VM 以啟用其他連接機制。Windows 使用者也可以使用 **ssh** 用戶端實作，連接及管理在 Azure 中的 Linux VM，但是 Windows 電腦通常並未隨附 **ssh** 用戶端，因此您必須選擇一個。

您可以安裝的一般用戶端包括：

- [puTTY and puTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)
- [Git For Windows](https://git-for-windows.github.io/)，隨附於環境和工具

如果您覺得非常奇怪，您也可以嘗試 [**OpenSSH** 工具組至 Windows 的新連接埠](http://blogs.msdn.com/b/powershell/archive/2015/10/19/openssh-for-windows-update.aspx)。但是請注意，這是目前正在開發中的程式碼，您應該在用於實際執行系統之前檢閱程式碼基底。

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 您需要建立哪些金鑰檔案？

Azure 的基本 SSH 安裝程式包含 2048 位元的 **ssh-rsa** 公用和私密金鑰組 (根據預設，**ssh-keygen** 會將這些檔案儲存為 **~/.ssh/id\_rsa** 和 **~/.ssh/id-rsa.pub**，除非變更預設值) 以及從 **id\_rsa** 私密金鑰檔案產生的 `.pem` 檔案，以搭配傳統入口網站的傳統部署模型使用。

以下是部署案例，以及您在每個案例中使用的檔案類型：

1. 無論部署模型為何，任何使用 [Azure 入口網站](https://portal.azure.com)的部署都需要 **ssh-rsa** 金鑰。
2. 需要 .pem 檔案才能使用[傳統入口網站](https://manage.windowsazure.com)來建立 VM。使用 [Azure CLI](../xplat-cli-install.md) 的傳統部署也支援 .pem 檔案。

> [AZURE.NOTE] 如果您打算管理使用傳統部署模型部署的服務，您可能也想要建立 **.cer** 格式檔案以上傳至入口網站 - 雖然這不牽涉到 **ssh** 或連接到 Linux VM，這是本文的主題。若要在 Windows 上建立這些檔案，請輸入：<br /> openssl.exe x509 -outform der -in myCert.pem -out myCert.cer

## 取得 Windows 相關的 ssh-keygen 和 openssl ##

[本節](#What-SSH-and-key-creation-programs-do-you-need)上方列出數個公用程式，它們包含適用於 Windows 的 `ssh-keygen` 和 `openssl`。以下列出一些範例：

###使用 Git for Windows###

1.	從下列位置下載並安裝 Git for Windows：[https://git-for-windows.github.io/](https://git-for-windows.github.io/)
2.	從 [開始] 功能表 > [所有應用程式] > [Git Shell] 執行 Git Bash

> [AZURE.NOTE] 當您執行上述的 `openssl` 命令時，可能會遇到下列錯誤：

        Unable to load config info from /usr/local/ssl/openssl.cnf

解決此問題最簡單的方法是設定 `OPENSSL_CONF` 環境變數。設定這個變數的程序會因您已在 Github 中設定的殼層而有所不同：

**Powershell：**

        $Env:OPENSSL_CONF="$Env:GITHUB_GIT\ssl\openssl.cnf"

**CMD：**

        set OPENSSL_CONF=%GITHUB_GIT%\ssl\openssl.cnf

**Git Bash：**

        export OPENSSL_CONF=$GITHUB_GIT/ssl/openssl.cnf
	

###使用 Cygwin###

1.	從下列位置下載並安裝 Cygwin：[http://cygwin.com/](http://cygwin.com/)
2.	確定已安裝 OpenSSL 封裝及其所有的相依性。
3.	執行 `cygwin`

## 建立私密金鑰##

1.	遵循以上的其中一組指示，以便執行 `openssl.exe`
2.	輸入以下命令：

  ```
  openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem
  ```
3.	畫面應該如下所示：

  ```
  $ openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem
  Generating a 2048 bit RSA private key
  .......................................+++
  .......................+++
  writing new private key to 'myPrivateKey.key'
  -----
  You are about to be asked to enter information that will be incorporated
  into your certificate request.
  What you are about to enter is what is called a Distinguished Name or a DN.
  There are quite a few fields but you can leave some blank
  For some fields there will be a default value,
  If you enter '.', the field will be left blank.
  -----
  Country Name (2 letter code) [AU]:
  ```

4.	回答提出的問題。
5.	它會建立兩個檔案：`myPrivateKey.key` 和 `myCert.pem`。
6.	如果您將直接使用 API，而不使用管理入口網站，請使用下列命令，將 `myCert.pem` 轉換為 `myCert.cer` (DER 編碼的 X509 憑證)：

  ```
  openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
  ```

## 建立 Putty 的 PPK ##

1. 從下列位置下載並安裝 Puttygen： [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Puttygen 可能無法讀取稍早建立的私密金鑰 (`myPrivateKey.key`)。執行下列命令以將它轉譯成 Puttygen 可認得的 RSA 私密金鑰：

		# openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
		# chmod 600 ./myPrivateKey_rsa

	上述命令應會產生一個名為 myPrivateKey\_rsa 的新私密金鑰。

3. 執行 `puttygen.exe`

4. 按一下功能表：[檔案] > [載入私密金鑰]

5. 找出您的私密金鑰，也就是上述命名為 `myPrivateKey_rsa` 的金鑰。您將需要變更檔案篩選，顯示**所有檔案 (*.*)**

6. 按一下 [開啟]。您將看見提示，看起來如下所示：

	![linuxgoodforeignkey](./media/virtual-machines-linux-ssh-from-windows/linuxgoodforeignkey.png)

7. 按一下 [確定]。

8. 按一下以下螢幕擷取畫面強調顯示的 [Save Private Key]：

	![linuxputtyprivatekey](./media/virtual-machines-linux-ssh-from-windows/linuxputtygenprivatekey.png)

9. 將檔案儲存為 PPK。


## 使用 Putty 連線到 Linux 機器 ##

1.	從下列位置下載並安裝 putty： [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	執行 putty.exe
3.	使用管理入口網站的 IP 來填入主機名稱：

	![linuxputtyconfig](./media/virtual-machines-linux-ssh-from-windows/linuxputtyconfig.png)

4.	選取 [Open] 之前，請按一下 [Connection] > [SSH] > [Auth] 索引標籤來選擇您的私密金鑰。請參閱以下螢幕擷取畫面，以了解要填入的欄位：

	![linuxputtyprivatekey](./media/virtual-machines-linux-ssh-from-windows/linuxputtyprivatekey.png)

5.	按一下 [開啟] 以連線到虛擬機器。
 

<!---HONumber=AcomDC_0907_2016-->