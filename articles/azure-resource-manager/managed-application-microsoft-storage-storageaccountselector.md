---
title: "Azure 受管理的應用程式 StorageAccountSelector UI 元素 | Microsoft Docs"
description: "描述 Azure 受管理應用程式的 Microsoft.Storage.StorageAccountSelector UI 元素"
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 2d64cfcd958f17171e9ef1a38777f202bdfda774
ms.contentlocale: zh-tw
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector UI 元素
選取新的或現有儲存體帳戶的控制項。

## <a name="ui-sample"></a>UI 範例
![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

## <a name="schema"></a>結構描述
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>備註
- 如果指定，就會自動驗證 `defaultValue.name` 的唯一性。 如果儲存體帳戶名稱不是唯一的，使用者就必須指定不同的名稱，或選擇現有的儲存體帳戶。
- `defaultValue.type` 的預設值為 **Premium_LRS**。
- `constraints.allowedTypes` 中未指定的任何類型都會加以隱藏，`constraints.excludedTypes` 中未指定的任何類型都會加以顯示。
`constraints.allowedTypes` 和 `constraints.excludedTypes` 都是選擇性的，但不能同時使用。
- 如果 `options.hideExisting` 為 **true**，使用者就無法選擇現有的儲存體帳戶。 預設值為 **false**。


## <a name="sample-output"></a>範例輸出
```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="next-steps"></a>後續步驟
* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](managed-application-createuidefinition-elements.md)。

