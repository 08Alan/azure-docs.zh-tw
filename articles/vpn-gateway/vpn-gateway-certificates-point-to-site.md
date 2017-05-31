---
title: "建立並匯出點對站的憑證：PowerShell：Azure | Microsoft Docs"
description: "本文中的步驟用來建立自我簽署的根憑證、匯出公開金鑰，以及使用 PowerShell 在 Windows 10 上產生用戶端憑證。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 1bfcb8683669770d6dd927cbde53a683bbc1d56e
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-powershell"></a>使用 PowerShell 來產生並匯出點對站連線的憑證

本文說明如何建立自我簽署的根憑證和產生用戶端憑證。 本文不包含點對站組態指示或點對站常見問題集。 您可以從下列清單中選取其中一篇「設定點對站」文章來尋找該資訊：

> [!div class="op_single_selector"]
> * [建立自我簽署憑證 - PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [建立自我簽署憑證 - Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [設定點對站 - Resource Manager - Azure 入口網站](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [設定點對站 - Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [設定點對站 - 傳統 - Azure 入口網站](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

點對站連線使用憑證進行驗證。 當您設定點對站連線時時，您需要將根憑證的公開金鑰 (.cer 檔案) 上傳至 Azure。 此外，用戶端憑證必須從根憑證產生，並安裝在與 VNet 連接的每部用戶端電腦上。 用戶端憑證可讓用戶端進行驗證。


> [!NOTE]
> 您必須在執行 Windows 10 的電腦上執行本文中的步驟。 產生憑證所需的 PowerShell Cmdlet 是 Windows 10 作業系統的一部分，在其他 Windows 版本上無法運作。 您只需要這些 Cmdlet 即可產生憑證。 產生憑證之後，您可以將憑證安裝在任何支援的用戶端作業系統上。 如果您無法存取 Windows 10 電腦，則可以使用 makecert 來產生憑證。 不過，使用 makecert 來建立的憑證雖然與點對站相容，但卻不是 SHA-2。 如需 makecert 的相關指示，請參閱[使用 makecert 來建立憑證](vpn-gateway-certificates-point-to-site-makecert.md)。 您使用 PowerShell 或 makecert 來建立的憑證可以安裝在任何[支援的用戶端作業系統](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)上，而不僅僅是安裝在您用來建立這些憑證的作業系統上。
> 
>

## <a name="rootcert"></a>建立自我簽署根憑證

下列步驟說明如何在 Windows 10 上使用 PowerShell 來建立自我簽署根憑證。 這些步驟中所用的 Cmdlet 和參數屬於 Windows 10 作業系統，不屬於 PowerShell 版本。 這並不意謂著您建立的憑證只能安裝在 Windows 10 上。 您可以將它們安裝在任何支援的用戶端上。 如需有關所支援用戶端的資訊，請參閱[點對站常見問題集](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)。

1. 從執行 Windows 10 的電腦，以提高的權限開啟 Windows PowerShell 主控台。
2. 使用下列範例建立自我簽署的根憑證。 下列範例會建立名為 'P2SRootCert' 的自我簽署的根憑證，其自動安裝在 'Certificates-Current User\Personal\Certificates' 中。 您可以開啟 *certmgr.msc* 或 [管理使用者憑證] 來檢視憑證。

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

### <a name="cer"></a>匯出公開金鑰 (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

匯出的 .cer 檔案必須上傳到 Azure。 如需相關指示，請參閱[設定點對站連線](vpn-gateway-howto-point-to-site-rm-ps.md#upload)。

### <a name="export-the-self-signed-root-certificate-and-public-key-to-store-it-optional"></a>匯出自我簽署根憑證和公開金鑰來儲存它 (選擇性)

您可能想要匯出自我簽署的根憑證，並將它安全地儲存。 如有需要，您可以稍後在另一部電腦上安裝這個自我簽署憑證，然後產生更多用戶端憑證，或匯出另一個 .cer 檔案。 若要將自我簽署的根憑證匯出為 .pfx，請選取根憑證，然後使用與[匯出用戶端憑證](#clientexport)所述的相同步驟來匯出。

## <a name="clientcert"></a>產生用戶端憑證 

每個使用點對站連線至 VNet 的用戶端電腦都必須安裝用戶端憑證。 您可以從自我簽署根憑證產生用戶端憑證，然後匯出及安裝用戶端憑證。 如果未安裝用戶端憑證，則驗證會失敗。 

下列步驟將逐步引導您完成從自我簽署的根憑證產生用戶端憑證。 您可以從相同根憑證產生多個用戶端憑證。 當您使用下列步驟產生用戶端憑證時，用戶端憑證會自動安裝在您用來產生憑證的電腦上。 如果您想要在另一部用戶端電腦上安裝用戶端憑證，您可以匯出憑證。

使用下列 PowerShell 步驟來產生用戶端憑證時，必須使用 Windows 10。 這些步驟中所用的 Cmdlet 和參數屬於 Windows 10 作業系統，不屬於 PowerShell 版本。 這並不意謂著您建立的憑證只能安裝在 Windows 10 上。 如需有關所支援用戶端的資訊，請參閱[點對站常見問題集](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)。

### <a name="example-1"></a>範例 1

此範例會使用從上一節宣告的 '$cert' 變數。 如果您在建立自我簽署根憑證之後關閉 PowerShell 主控台，或是在新的 PowerShell 主控台工作階段中建立其他用戶端憑證，請使用[範例 2](#ex2) 中的步驟。

修改並執行範例以產生用戶端憑證。 如果您執行下列範例，但未加以修改，結果會是名為 'P2SChildCert' 的用戶端憑證。  如果您要將子憑證命名為其他名稱，請修改 CN 值。 執行這個範例時，請勿變更 TextExtension。 您產生的用戶端憑證會自動安裝在您電腦的 'Certificates - Current User\Personal\Certificates' 中。

```powershell
New-SelfSignedCertificate -Type Custom -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>範例 2

如果您要建立其他用戶端憑證，或者不是使用您用來建立自我簽署根憑證的相同 PowerShell 工作階段，請使用下列步驟︰

1. 識別安裝在電腦上的自我簽署根憑證。 此 Cmdlet 會傳回安裝於電腦上的憑證清單。

  ```powershell
  Get-ChildItem -Path “Cert:\CurrentUser\My”
  ```
2. 從傳回的清單尋找主體名稱，然後將其旁邊的指紋複製到文字檔。 在下列範例中，有兩個憑證。 CN 名稱是您要從中產生子憑證之自我簽署根憑證的名稱。 在此例中為 'P2SRootCert'。

        Thumbprint                                Subject

        AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
        7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert


3. 使用上一個步驟中的指紋，為根憑證宣告一個變數。 將 THUMBPRINT 替換為您要從中產生子憑證之根憑證的指紋。

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
  ```

  例如，使用上一個步驟中的 P2SRootCert 的指紋，變數會如下所示︰

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
  ```    

4.  修改並執行範例以產生用戶端憑證。 如果您執行下列範例，但未加以修改，結果會是名為 'P2SChildCert' 的用戶端憑證。 如果您要將子憑證命名為其他名稱，請修改 CN 值。 執行這個範例時，請勿變更 TextExtension。 您產生的用戶端憑證會自動安裝在您電腦的 'Certificates - Current User\Personal\Certificates' 中。

  ```powershell
  New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="clientexport"></a>匯出用戶端憑證   

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]
    

## <a name="install"></a>安裝匯出的用戶端憑證

[!INCLUDE [Install client certificate](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="next-steps"></a>後續步驟

繼續使用您的點對站設定。 

* 如需 **Resource Manager** 部署模型步驟，請參閱[設定 VNet 的點對站連線](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。 
* 如需**傳統**部署模型步驟，請參閱[設定 VNet 的點對站 VPN 連線 (傳統)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)。
