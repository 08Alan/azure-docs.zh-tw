---
title: "如何建立媒體處理器 | Microsoft Docs"
description: "了解如何建立媒體處理器元件，為 Azure 媒體服務的媒體內容進行編碼、格式轉換、加密或解密。 程式碼範例以 C# 撰寫，並使用 Media Services SDK for .NET。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 88f6e1da090eb6088e54c6f81d0f83b1737d3c2c
ms.lasthandoff: 04/12/2017


---
# <a name="how-to-get-a-media-processor-instance"></a>如何：取得媒體處理器執行個體
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Overview
在媒體服務中，媒體處理器是可處理特定處理工作的元件，例如編碼、格式轉換、加密或解密媒體內容。 您通常會在建立媒體內容的編碼、加密或格式轉換工作時建立媒體處理器。

下表提供每個可用媒體處理器的名稱和說明。

| 媒體處理器名稱 | 說明 | 相關資訊 |
| --- | --- | --- |
| Media Encoder Standard |提供隨選編碼的標準功能。 |[Azure 隨選媒體編碼器的概觀和比較](media-services-encode-asset.md) |
| Media Encoder Premium Workflow |可讓您使用 Media Encoder Premium Workflow 執行編碼工作。 |[Azure 隨選媒體編碼器的概觀和比較](media-services-encode-asset.md) |
| Azure Media Indexer |可讓您的媒體檔案和內容可供搜尋，以及產生隱藏式輔助字幕和關鍵字。 |[Azure Media Indexer](media-services-index-content.md) |
| Azure Media Hyperlapse (預覽) |可讓您使用影片穩定讓影片中的「巔簸」變得平滑。 也可讓您將內容加速至可使用的片段。 |[Azure Media Hyperlapse](media-services-hyperlapse-content.md) |
| Azure Media Encoder |取代 | |
| Storage Decryption |取代 | |
| Azure Media Packager |取代 | |
| Azure Media Encryptor |取代 | |

## <a name="get-media-processor"></a>取得媒體處理器
下列方法將說明如何取得媒體處理器執行個體。 此程式碼範例假設會使用名為 **_context** 的模組層級變數來參考伺服器內容，如[做法：以程式設計方式連接到媒體服務](media-services-dotnet-connect-programmatically.md)一節所述。

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>後續步驟
既然您已了解如何取得媒體處理器執行個體，請移至 [如何為資產編碼](media-services-dotnet-encode-with-media-encoder-standard.md) 主題，以了解如何使用媒體編碼器標準將資產編碼。


