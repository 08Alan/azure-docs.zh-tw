---
title: "透過適用於 Azure CLI 2.0 的 IoT 擴充功能管理 Azure IoT 裝置 | Microsoft Docs"
description: "使用 Azure CLI 2.0 的 IoT 擴充功能工具來管理 Azure IoT 中樞裝置，採用直接方法和對應項所需的屬性管理選項。"
services: iot-hub
documentationcenter: 
author: chrissie926
manager: timlt
tags: 
keywords: "azure iot 裝置管理, azure iot 中樞裝置管理, 裝置管理 iot, iot 中樞裝置管理"
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 760a6a30513308aa59c5e253e3b91e28cf9e3241
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2018
---
# <a name="use-the-iot-extension-for-azure-cli-20-for-azure-iot-hub-device-management"></a>使用 Azure CLI 2.0 的 IoT 擴充功能來管理 Azure IoT 中樞裝置

![端對端圖表](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure CLI 2.0 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension)是新增到 [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) 功能的全新開放原始碼 IoT 擴充功能，包含能與 Azure Resource Manager 和管理端點互動的命令。 Azure CLI 2.0 包含能與 Azure Resource Manager 和管理端點互動的命令。 例如，您可以使用 Azure CLI 2.0 來建立 Azure VM 或 IoT 中樞。 CLI 擴充功能可讓 Azure 服務強化 Azure CLI，讓您能存取其他服務專屬的功能。 IoT 擴充功能可讓 IoT 開發人員命令列存取所有的 IoT 中樞、IoT Edge 與 IoT 中樞裝置佈建服務功能。

| 管理選項          | Task                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| 直接方法             | 使裝置執行動作，例如啟動或停止傳送訊息，或是將裝置重新開機。                                        |
| 對應項的所需屬性    | 讓裝置進入特定狀態，例如將 LED 設定為綠色，或將遙測傳送間隔設定為 30 分鐘。         |
| 對應項的報告屬性   | 取得裝置的報告狀態。 例如，裝置會回報 LED 現在正閃爍不停。                                    |
| 對應項標記                  | 在雲端儲存裝置特定的中繼資料。 例如，販賣機的部署位置。                         |
| 裝置對應項查詢        | 查詢所有裝置對應項以擷取具有任意條件的這些裝置，例如識別可供使用的裝置。 |

如需差異的詳細說明和使用這些選項的相關指引，請參閱[裝置對雲端通訊指引](iot-hub-devguide-d2c-guidance.md)和[雲端對裝置通訊指引](iot-hub-devguide-c2d-guidance.md)。

> [!NOTE]
> 「裝置對應項」是存放裝置狀態資訊 (中繼資料、組態和條件) 的 JSON 文件。 IoT 中樞會為其連線的每個裝置保存裝置對應項。 如需裝置對應項的詳細資訊，請參閱[開始使用裝置對應項](iot-hub-node-node-twin-getstarted.md)。

## <a name="what-you-learn"></a>您學到什麼

您會學到在開發電腦上使用 Azure CLI 2.0 的 IoT 擴充功能並搭配各種管理選項。

## <a name="what-you-do"></a>您要做什麼

搭配各種管理選項執行 Azure CLI 2.0 和 Azure CLI 2.0 的 IoT 擴充功能。

## <a name="what-you-need"></a>您需要什麼

- 完成涵蓋下列需求的[設定裝置](iot-hub-raspberry-pi-kit-node-get-started.md)教學課程︰
  - 有效的 Azure 訂用帳戶。
  - 位於您訂用帳戶中的 Azure IoT 中樞。
  - 將訊息傳送到您 Azure IoT 中樞的用戶端應用程式。

- 請確定在本教學課程期間，您的裝置是和用戶端應用程式一起執行。

- [Python 2.7x 或 Python 3.x](https://www.python.org/downloads/)

- 安裝 Azure CLI 2.0。 在 Windows 上進行安裝的最簡單方式，就是下載並安裝 [MSI](https://aka.ms/InstallAzureCliWindows)。 您也可以遵循 [Microsoft Docs](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) 上的安裝指示，在環境中設定 Azure CLI 2.0。 您的 Azure CLI 2.0 版本至少必須是 2.0.24 或更新版本。 使用 `az –version` 進行驗證。 

- 安裝 IoT 擴充功能。 最簡單的方式就是執行 `az extension add --name azure-cli-iot-ext`。 [IoT 擴充功能讀我檔案](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md)說明安裝此擴充功能的數種方式。


## <a name="login-to-your-azure-account"></a>登入您的 Azure 帳戶

執行下列命令來登入 Azure 帳戶：

```bash
az login
```

## <a name="use-the-iot-extension-for-azure-cli-20-with-direct-methods"></a>使用 Azure CLI 2.0 的 IoT 擴充功能並搭配直接方法

```bash
az iot hub invoke-device-method --device-id <your device id> --hub-name <your hub name> --method-name <the method name> --method-payload <the method payload>
```

## <a name="use-the-iot-extension-for-azure-cli-20-with-twins-desired-properties"></a>使用 Azure CLI 2.0 的 IoT 擴充功能並搭配對應項所需的屬性

透過執行下列命令，設定需要的屬性間隔 = 3000：

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.desired.interval = 3000
```

此屬性可讀取自裝置。

## <a name="use-the-iot-extension-for-azure-cli-20-with-twins-reported-properties"></a>使用 Azure CLI 2.0 的 IoT 擴充功能並搭配對應項所報告的屬性

執行下列命令來取得裝置的報告屬性：

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.reported.interval = 3000
```

其中一個屬性是 $metadata.$lastUpdated，它會顯示此裝置上一次傳送或接收訊息的時間。

## <a name="use-the-iot-extension-for-azure-cli-20-with-twins-tags"></a>使用 Azure CLI 2.0 的 IoT 擴充功能並搭配對應項的標記

執行下列命令來顯示裝置的標記和屬性：

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

執行下列命令來將欄位 role = temperature&humidity 新增至裝置：

```bash
az iot hub device-twin update --hub-name <your hub name> --device-id <your device id> --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="use-the-iot-extension-for-azure-cli-20-with-device-twins-queries"></a>使用 Azure CLI 2.0 的 IoT 擴充功能並搭配裝置對應項查詢

藉由執行下列命令，查詢具有標記 role = 'temperature&humidity' 的裝置：

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

藉由執行下列命令，查詢具有標記 role = 'temperature&humidity' 以外的所有裝置：

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>後續步驟

您已了解如何監視裝置到雲端的訊息，以及在 IoT 裝置和 Azure IoT 中樞之間傳送雲端到裝置的訊息。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]