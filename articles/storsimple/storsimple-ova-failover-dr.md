---
title: "StorSimple Virtual Array 的災害復原和裝置容錯移轉 | Microsoft Docs"
description: "深入了解如何容錯移轉 StorSimple Virtual Array。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 94a8c3db-8e47-4e9a-917a-29b14a9263aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 78daa5a75b3414e2761333ea6ad91945596553c8
ms.openlocfilehash: 2017fcdc7a5c36c7c00d8bcef5be973ed60e3f82


---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array"></a>StorSimple Virtual Array 的災害復原和裝置容錯移轉
## <a name="overview"></a>概觀
本文說明 Microsoft Azure StorSimple Virtual Array (也稱為 StorSimple 內部部署虛擬裝置) 的災害復原 (包括發生災害時容錯移轉至另一個虛擬裝置所需的詳細步驟)。 容錯移轉可讓您將資料中心的「來源」裝置資料移轉至位於相同或不同地理位置的另一個「目標」裝置。 裝置容錯移轉適用於整個裝置。 在容錯移轉期間，來源裝置的雲端資料會將擁有權變更為目標裝置雲端資料。

裝置容錯移轉是透過災害復原 (DR) 功能協調，並從 [裝置]  頁面起始。 此頁面會以表格列出與 StorSimple Manager 服務連接的所有 StorSimple 裝置。 顯示每個裝置的易記名稱、狀態、佈建與最大容量、類型及模型。

![](./media/storsimple-ova-failover-dr/image15.png)

本文僅適用於 StorSimple Virtual Array。 若要容錯移轉 8000 系列裝置，請移至 [StorSimple 裝置的容錯移轉和災害復原](storsimple-device-failover-disaster-recovery.md)。

## <a name="what-is-disaster-recovery"></a>何謂災害復原？
在災害復原 (DR) 案例中，主要裝置會停止運作。 在此情況下，您可以使用主要裝置當做「來源」，並將另一個裝置指定為「目標」，將與失敗裝置相關聯的雲端資料移至另一個裝置。 這個程序就稱為「容錯移轉」 。 在容錯移轉期間，來源裝置的所有磁碟區或共用都會變更擁有權，並移轉到目標裝置。 不允許篩選資料。

使用熱度圖分層和追蹤，可將 DR 模型化為完整裝置還原。 熱度圖的定義方式，是根據讀取和寫入模式，將熱度值指派給資料。 這個熱度圖接著先將最低熱度資料區塊分層至雲端，同時將高熱 (最常用) 資料區塊保留在本機層中。 在 DR 期間，熱度圖用來還原和解除凍結雲端中的資料。 裝置會擷取最新備份 (如內部決定) 中的所有磁碟區/共用，並從該備份執行還原。 整個 DR 程序是由裝置進行協調。

## <a name="prerequisites-for-device-failover"></a>裝置容錯移轉需求
### <a name="prerequisites"></a>先決條件
所有裝置容錯移轉都必須滿足下列必要條件：

* 來源裝置需要處於 [已停用]  狀態。
* 在 Azure 傳統入口網站中，目標裝置需要顯示為 [使用中]  。 您需要佈建容量相同或更高的目標虛擬裝置。 接著，您應該使用本機 Web UI 來設定並成功註冊虛擬裝置。
  
  > [!IMPORTANT]
  > 請不要嘗試按一下 [完成裝置設定]，透過服務來設定已註冊的虛擬裝置。 您不應該透過服務執行任何裝置設定。
  > 
  > 
* 來源和目標裝置的類型必須相同。 您只能將設定為檔案伺服器的虛擬裝置容錯移轉到另一部檔案伺服器。 這適用於 iSCSI 伺服器。
* 針對檔案伺服器 DR，建議您將目標裝置加入與來源網域相同的網域，以自動解析共用權限。 只有這個版本才支援容錯移轉至相同網域中的目標裝置。

### <a name="other-considerations"></a>其他考量
* 建議您將來源裝置上的所有磁碟區或共用離線。
* 如果是規劃的容錯移轉，建議您備份裝置，然後繼續進行容錯移轉，以將資料遺失降到最低。 如果是未規劃的容錯移轉，將會使用最新備份來還原裝置。
* DR 的可用目標裝置是容量與來源裝置相同或更高的裝置。 與服務連接但空間不足而不符合條件的裝置，無法當成目標裝置使用。

### <a name="dr-prechecks"></a>DR 前置檢查
DR 開始之前，會對裝置執行前置檢查。 這些檢查有助於確保 DR 開始時不會發生任何錯誤。 前置檢查包括：

* 驗證儲存體帳戶
* 檢查與 Azure 的雲端連線
* 檢查目標裝置上的可用空間
* 確認 iSCSI 伺服器來源裝置具有有效的 ACR 名稱、IQN (長度不超過 220 個字元) 以及與磁碟區相關聯的 CHAP 密碼 (長度為 12 和 16 個字元)

如果上述任何前置檢查失敗，則無法繼續進行 DR。 您需要解決這些問題，然後重試 DR。

DR 順利完成之後，來源裝置上雲端資料的擁有權會移轉給目標裝置。 來源裝置就無法再於入口網站中使用。 會封鎖對來源裝置上所有磁碟區/共用的存取，而目標裝置會變成使用中。

> [!IMPORTANT]
> 雖然裝置無法再使用，但是您在主機系統上佈建的虛擬機器仍然會耗用資源。 DR 順利完成之後，您就可以從主機系統中刪除此虛擬機器。
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>容錯移轉至虛擬陣列
建議您先佈建另一個 StorSimple Virtual Array、透過本機 Web UI 進行設定，以及向 StorSimple Manager 服務進行註冊，然後再執行這個程序。

> [!IMPORTANT]
> * 不允許您從 StorSimple 8000 系列裝置容錯移轉到 1200 虛擬裝置。
> * 您可以從在政府機構入口網站中部署，啟用美國聯邦資訊處理標準 (FIPS) 的虛擬裝置容錯移轉到 Azure 傳統入口網站中的虛擬裝置。 反之亦然。
> 
> 

請執行下列步驟以將裝置還原至目標 StorSimple 虛擬裝置。

1. 將主機上的磁碟區/共用離線。 請參閱主機上有關將磁碟區/共用離線的作業系統特定指示。 如果還未離線，則需要移至 [裝置] > [共用] (或 [裝置] > [磁碟區])，將裝置上的所有磁碟區/共用離線。 選取共用/磁碟區，然後按一下頁面底部的 [離線]  。 系統提示您進行確認時，按一下 [是] 。 針對裝置上的所有共用/磁碟區，重複執行這個程序。
2. 在 [裝置] 頁面上，選取進行容錯移轉的來源裝置，然後按一下 [停用]。 
    ![](./media/storsimple-ova-failover-dr/image16.png)
3. 系統將提示您進行確認。 裝置停用是無法復原的永久性程序。 系統也會提醒您將主機上的共用/磁碟區離線。
   
    ![](./media/storsimple-ova-failover-dr/image18.png)
4. 確認之後，將會開始停用。 停用順利完成之後，您將會收到通知。
   
    ![](./media/storsimple-ova-failover-dr/image19.png)
5. 在 [裝置] 頁面上，裝置狀態現在將會變更為 [已停用]。
   
    ![](./media/storsimple-ova-failover-dr/image20.png)
6. 選取已停用的裝置，然後按一下頁面底部的 [容錯移轉] 。
7. 在開啟的 [確認容錯移轉精靈] 中，執行下列動作：
   
   1. 從可用裝置的下拉式清單中，選擇 [目標裝置]。 下拉式清單中只會顯示具有足夠容量的裝置。
   2. 檢閱與來源裝置相關聯的詳細資料 (例如，裝置名稱、總容量，以及將進行容錯移轉之共用的名稱)。
      
       ![](./media/storsimple-ova-failover-dr/image21.png)
8. 核取 [我同意容錯移轉是永久性作業，一旦成功完成容錯移轉，便會刪除來源裝置] 。
9. 按一下核取圖示 ![](./media/storsimple-ova-failover-dr/image1.png)。
10. 容錯移轉工作隨即起始，並通知您。 按一下 [檢視工作]  監視容錯移轉。
    
     ![](./media/storsimple-ova-failover-dr/image22.png)
11. 在 [工作]  頁面中，您會看到針對來源裝置所建立的容錯移轉工作。 此工作會執行 DR 前置檢查。
    
    ![](./media/storsimple-ova-failover-dr/image23.png)
    
     DR 前置檢查成功之後，容錯移轉工作會產生來源裝置上每個共用/磁碟區的還原作業。
    
    ![](./media/storsimple-ova-failover-dr/image24.png)
12. 完成容錯移轉後，移至 [裝置]  頁面。
    
    a. 為容錯移轉程序選取要用來做為目標裝置的 StorSimple 虛擬裝置。
    
    b. 移至 [共用] 頁面 (如果是 iSCSI 伺服器，則為 [磁碟區])。 現在應該會列出舊裝置中的所有共用 (磁碟區)。
    
    ![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png) **提供的影片**

這段影片示範如何將 StorSimple 內部部署虛擬裝置容錯移轉至另一個虛擬裝置。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Virtual-Array-Disaster-Recovery/player]
> 
> 

## <a name="business-continuity-disaster-recovery-bcdr"></a>業務持續性災害復原 (BCDR)
當整個 Azure 資料中心停止運作時，就構成業務持續性災害復原 (BCDR) 狀況。 這會影響您的 StorSimple Manager 服務和相關聯的 StorSimple 裝置。

如果 StorSimple 裝置在發生災害的前一刻才剛註冊，則可能需要刪除這些 StorSimple 裝置。 災害之後，您可以重建並設定這些裝置。

## <a name="errors-during-dr"></a>DR 期間發生錯誤
**DR 期間雲端連線能力中斷**

如果在啟動 DR 之後並在裝置還原完成之前中斷雲端連線能力，則 DR 會失敗並通知您。 用於 DR 的目標裝置則會標記為 [無法使用]。 相同的目標裝置不能再用於之後的 DR。

**沒有相容的目標裝置**

如果可用目標裝置的空間不足，則會看到錯誤，指出沒有相容的目標裝置。

**前置檢查失敗**

如果未滿足其中一個前置檢查，則會看到前置檢查失敗。

## <a name="next-steps"></a>後續步驟
深入了解如何 [使用本機 Web UI 管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。




<!--HONumber=Jan17_HO5-->


