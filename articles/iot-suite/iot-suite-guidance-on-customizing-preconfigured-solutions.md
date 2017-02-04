---
title: "自訂預先設定解決方案 | Microsoft Docs"
description: "提供如何自訂 Azure IoT 套件預先設定解決方案的指引。"
services: 
suite: iot-suite
documentationcenter: .net
author: aguilaaj
manager: timlt
editor: 
ms.assetid: 4653ae53-4110-4a10-bd6c-7dc034c293a8
ms.service: iot-suite
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2016
ms.author: araguila
translationtype: Human Translation
ms.sourcegitcommit: 45fd461defc00c5dc018496b85b8bf85614f03dd
ms.openlocfilehash: 0037b9e28b20c9a85f810cba45aa5b4cbcf6ab6b


---
# <a name="customize-a-preconfigured-solution"></a>自訂預先設定的方案
Azure IoT Suite 提供的預先設定解決方案能夠示範套件中共同運作的服務可提供端對端解決方案。 從這個起點開始，有好幾個地方可以擴充並針對特定案例自訂解決方案。 下列各節說明這些常見的自訂點。

## <a name="finding-the-source-code"></a>尋找原始程式碼
預先設定解決方案的原始程式碼可在以下 GitHub 的儲存機制取得：

* 遠端監視： [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* 預測性維護︰ [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

提供預先設定解決方案原始程式碼的目的，在於示範實作使用 Azure IoT 套件之 IoT 解決方案的端對端功能時，所採用的模式和作法。 您可以找到如何在 GitHub 儲存機制中建置和部署解決方案的詳細資訊。

## <a name="changing-the-preconfigured-rules"></a>變更預先設定規則
遠端監視解決方案包含三個 [Azure 串流分析](https://azure.microsoft.com/services/stream-analytics/)作業，這些作業可實作針對解決方案顯示的裝置資訊、遙測及規則邏輯。

[遠端監視預先設定解決方案逐步解說](iot-suite-remote-monitoring-sample-walkthrough.md)提供這三個串流分析工作和其語法的深入描述。 

您可以直接編輯這些工作以改變邏輯，或新增案例特有的邏輯。 您可以尋找串流分析工作，如下所示︰

1. 移至 [Azure 入口網站] [](https://portal.azure.com)。
2. 瀏覽至名稱與 IoT 解決方案相同的資源群組。 
3. 選取要修改的 Azure 串流分析作業。 
4. 在命令集中選取 [停止] 以停止作業。 
5. 編輯輸入、查詢及輸出。
   
    簡單修改的目的在於變更**規則**作業的查詢，以便使用 **"<"** 而不是 **">"**。 編輯規則時，解決方案入口網站仍會顯示 **">"**，不過因為基礎作業中的變更，您可以發現行為已翻轉。
6. 啟動工作

> [!NOTE]
> 遠端監視儀表板依賴特定資料，因此變更工作可能會造成儀表板失敗。
> 
> 

## <a name="adding-your-own-rules"></a>新增自己的規則
除了變更預先設定的 Azure 串流分析工作，您也可以使用 Azure 入口網站新增工作或新增現有工作的查詢。

## <a name="customizing-devices"></a>自訂裝置
最常見的擴充功能活動之一是使用案例特定的裝置。 使用裝置的方法有數種。 這些方法包括變更模擬裝置以符合您的案例，或使用 [IoT Device SDK][IoT Device SDK] 將實體裝置連接到解決方案。

如需將裝置新增至遠端監視預先設定解決方案的逐步指南，請參閱 [Iot 套件連接裝置](iot-suite-connecting-devices.md)和[遠端監視 C SDK 範例](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring)，其設計是要搭配遠端監視預先設定解決方案。

### <a name="creating-your-own-simulated-device"></a>建立自己的模擬裝置
之前提及的遠端監視解決方案原始程式碼中包含 .NET 模擬器。 此模擬器是解決方案中佈建的模擬器，並且可以變更以傳送不同的中繼資料、遙測或回應給不同的命令。

遠端監視預先設定解決方案中的預先設定模擬器，是發出溫度和濕度遙測的冷卻裝置，當您分接 GitHub 儲存機制後，可以在 [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) 專案中修改模擬器。

### <a name="available-locations-for-simulated-devices"></a>模擬裝置的可用位置
預設的一組位置是在美國華盛頓州的西雅圖市/雷德蒙德市。 您可以在 [SampleDeviceFactory.cs][lnk-sample-device-factory] 變更這些位置。

### <a name="building-and-using-your-own-physical-device"></a>建置並使用自己的 (實體) 裝置
[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 提供用來將各種裝置類型 (語言和作業系統) 連接至 IoT 解決方案中的程式庫。

## <a name="modifying-dashboard-limits"></a>修改儀表板限制
### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>儀表板下拉式清單中顯示的裝置數目
預設值為 200。 您可以在 [DashboardController.cs][lnk-dashboard-controller] 變更這個數字。

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Bing 地圖控制項中要顯示的釘選數目
預設值為 200。 您可以在 [TelemetryApiController.cs][lnk-telemetry-api-controller-01] 變更這個數字。

### <a name="time-period-of-telemetry-graph"></a>遙測圖形的期間
預設值是 10 分鐘。 您可以在 [TelmetryApiController.cs][lnk-telemetry-api-controller-02] 變更此值。

## <a name="manually-setting-up-application-roles"></a>手動設定應用程式角色
以下程序描述如何將 **Admin** 和 **ReadOnly** 應用程式角色新增至預先設定的解決方案中。 請注意，從 azureiotsuite.com 網站佈建的預先設定解決方案已經包含 **Admin** 和 **ReadOnly** 角色。

**ReadOnly** 角色的成員可以看到儀表板和裝置清單，但不能加入裝置、變更裝置屬性或傳送命令。  **Admin** 角色的成員具有解決方案中所有功能的完整存取權。

1. 前往 [Azure 傳統入口網站][lnk-classic-portal]。
2. 選取 **Active Directory**。
3. 按一下您在佈建解決方案時所使用的 AAD 租用戶名稱。
4. 按一下 [應用程式] 。
5. 按一下符合預先設定之方案名稱的應用程式名稱。 如果清單中看不到您的應用程式，請選取 [顯示] 下拉式清單中的 [我公司所擁有的應用程式]，然後按一下核取記號。
6. 在頁面底部，按一下 [管理資訊清單]，然後按一下 [下載資訊清單]。
7. 這會下載一個 .json 檔案到本機電腦。  使用您選擇的文字編輯器開啟此檔案並加以編輯。
8. 在 .json 檔案的第三行，您會看到︰
   
   ```
   "appRoles" : [],
   ```
   使用下列程式碼取代這一行：
   
   ```
   "appRoles": [
   {
   "allowedMemberTypes": [
   "User"
   ],
   "description": "Administrator access to the application",
   "displayName": "Admin",
   "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
   "isEnabled": true,
   "value": "Admin"
   },
   {
   "allowedMemberTypes": [
   "User"
   ],
   "description": "Read only access to device information",
   "displayName": "Read Only",
   "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
   "isEnabled": true,
   "value": "ReadOnly"
   } ],
   ```
9. 儲存更新後的.json 檔案 (可以覆寫現有的檔案)。
10. 在 Azure 管理入口網站中，選取頁面底部的 [管理資訊清單]，然後選取 [上傳資訊清單] 上傳您在上一個步驟儲存的 .json 檔案。
11. 您現在已為您的應用程式新增 **Admin** 和 **ReadOnly** 角色。
12. 若要將其中一個角色指派給您目錄中的使用者，請參閱 [azureiotsuite.com 網站的權限][lnk-permissions]。

## <a name="feedback"></a>意見反應
本文件是否涵蓋您感興趣的自訂內容？ 請在 [User Voice (使用者心聲)](https://feedback.azure.com/forums/321918-azure-iot)中加入功能建議，或在本文下方留言。 

## <a name="next-steps"></a>後續步驟
若要深入了解自訂預先設定的解決方案的選項，請參閱︰

* [將邏輯應用程式連接至 Azure IoT 套件遠端監視預先設定解決方案][lnk-logicapp]
* [搭配使用動態遙測與遠端監視預先設定解決方案][lnk-dynamic]
* [遠端監視預先設定方案中的裝置資訊中繼資料][lnk-devinfo]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com



<!--HONumber=Dec16_HO3-->


