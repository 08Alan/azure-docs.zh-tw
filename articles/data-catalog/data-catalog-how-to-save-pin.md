---
title: "如何儲存搜尋和釘選資料資產 | Microsoft Docs"
description: "強調 Azure 資料目錄中用於儲存資料來源和資料資產以供稍後重複使用之功能的操作說明文章。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 05/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f017776480466979d7f2f9edec2b3ac5caca2321
ms.contentlocale: zh-tw
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-save-searches-and-pin-data-assets"></a>如何儲存搜尋和釘選資料資產
## <a name="introduction"></a>簡介
Microsoft Azure 資料目錄提供用來探索資料來源的功能。 使用者可以快速搜尋和篩選目錄來找出資料來源並了解其預定目的，以更輕鬆地尋找手邊工作的正確資料。

但是如果使用者需要定期使用相同的資料呢？ 如果使用者定期貢獻其知識到目錄中的相同資料來源呢？ 在這些情況下，必須重複發出相同的搜尋會很沒有效率，而這時已儲存的搜尋和已釘選的資料資產就能幫得上忙。

## <a name="saved-searches"></a>已儲存的搜尋
Azure 資料目錄中的已儲存的搜尋是可重複使用的每個使用者搜尋定義。 使用者在定義搜尋後 (包括搜尋詞彙、標記與其他篩選器)，就能將其儲存起來以供稍後使用。 已儲存的搜尋定義接著可以在稍後的日期重新執行，以傳回任何符合其搜尋準則的資料資產。

### <a name="creating-a-saved-search"></a>建立已儲存的搜尋
若要建立已儲存的搜尋，請先輸入要重複使用的搜尋準則。 然後按一下 Azure 資料目錄入口網站的 [目前的搜尋] 方塊中的 [儲存] 連結。

 ![選取 [儲存] 以儲存目前的搜尋設定](./media/data-catalog-how-to-save-pin/01-save-option.png)

出現提示時，輸入已儲存的搜尋的名稱。 請為搜尋所將傳回的資料資產選擇有意義且能描述其用途的名稱。

 ![提供已儲存的搜尋的名稱](./media/data-catalog-how-to-save-pin/02-name.png)

### <a name="managing-saved-searches"></a>管理已儲存的搜尋
使用者在儲存一個或多個搜尋後，Azure 資料目錄入口網站的 [目前的搜尋] 方塊下就會出現 [已儲存的搜尋] 選項。 將其展開就會顯示已儲存的搜尋的完整清單。

 ![已儲存的搜尋的清單](./media/data-catalog-how-to-save-pin/03-list.png)

在清單中選取已儲存的搜尋會讓搜尋開始執行。

選取下拉式功能表會提供一組管理選項：

 ![用於管理已儲存的搜尋的選項](./media/data-catalog-how-to-save-pin/04-managing.png)

選取 [重新命名] 會提示使用者輸入已儲存的搜尋的新名稱。 但搜尋定義不會變更。

選取 [刪除] 會提示使用者進行確認，然後將會在使用者的清單中移除已儲存的搜尋。

選取 [儲存為預設值] 會將所選之已儲存的搜尋標記為使用者的預設搜尋。 如果使用者在 Azure 資料目錄首頁執行「空白」搜尋，就會執行使用者的預設搜尋。 此外，標記為預設值的搜尋會出現在已儲存的搜尋清單頂端。

### <a name="organizational-saved-searches"></a>組織已儲存的搜尋
每位使用者可以儲存搜尋以供自己使用。 資料目錄管理員也可以儲存搜尋，以供組織內的所有使用者使用。 在儲存搜尋時，系統會提供系統管理員在公司內共用儲存之搜尋的選項。 如果選取此選項，儲存的搜尋便會納入所有使用者的可用搜尋清單中。

 ![組織已儲存的搜尋](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>已釘選的資料資產
已儲存的搜尋可讓使用者儲存並重複使用搜尋定義。因為目錄內容會變更，因此搜尋所傳回的資料資產在經過一段時間後可能也會變更。 已釘選的資料資產可讓使用者明確地識別特定資料資產，以便更輕鬆地存取資料資產而不必使用搜尋。

釘選資料資產很簡單，使用者只要按一下資料資產的 [釘選] 圖示就能將它加入至已釘選清單。 此圖示會出現在圖格檢視之資產圖格的角落，以及 Azure 資料目錄入口網站的清單檢視最左邊的資料行中。

![釘選資料資產](./media/data-catalog-how-to-save-pin/05-pinning.png)

取消釘選資產也同樣直接，使用者只要再次按一下 [釘選] 圖示即可切換所選資產的設定。

![取消釘選資料資產](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="my-assets"></a>「我的資產」
Azure 資料目錄入口網站首頁有 [我的資產] 區段，其會顯示目前的使用者感興趣的資產。 此區段同時包含已釘選的資產和已儲存的搜尋。

![首頁上的 [我的資產]](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>摘要
Azure 資料目錄提供各種功能讓使用者能夠更容易地探索所需的資料來源，讓他們可以花較少的時間尋找資料並有更多時間使用資料。 已儲存的搜尋和已釘選的資料資產是以這些核心功能為基礎所建立，因此使用者可以輕鬆地識別所要重複使用的資料來源。

