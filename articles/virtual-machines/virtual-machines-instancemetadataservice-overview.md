---
title: "Azure 執行個體中繼資料服務概觀 | Microsoft Docs"
description: "RESTful 介面，用以取得 VM 的計算、網路和近期維護事件的相關資訊。"
services: virtual-machines-windows, virtual-machines-linux,virtual-machines-scale-sets, cloud-services
documentationcenter: virtual-machines
author: harijay
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 03/27/2017
ms.author: harijay
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 7849537ac85f39f2398648bb6302b610d1abf609
ms.contentlocale: zh-tw
ms.lasthandoff: 06/17/2017

---

# <a name="azure-instance-metadata-service"></a>Azure 執行個體中繼資料服務 


Azure 執行個體中繼資料服務提供執行可用於管理和設定虛擬機器之虛擬機器執行個體的相關資訊。
這包括 SKU、網路組態及近期維護事件等資訊。 如需可用資訊類型的詳細資訊，請參閱[中繼資料類別](#instance-metadata-data-categories)。

Azure 的執行個體中繼資料服務是透過 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/)建立之所有 IaaS VM 可存取的 REST 端點。 端點可以在已知的非可路由 IP 位址 (`169.254.169.254`) 取得，該位址只能從 VM 內存取。

### <a name="important-information"></a>重要資訊

這項服務已在全域 Azure 區域中**正式推出**。 目前有 Government、China 和 German Azure Cloud 的公開預覽版。 它會定期接收更新，以公開有關虛擬機器執行個體的新資訊。 此頁面會反映最新的[資料類別](#instance-metadata-data-categories)。

## <a name="service-availability"></a>服務可用性
這項服務適用於所有正式推出的全域 Azure 區域。 這項服務在 Government、China 和 German 區域目前中為公開預覽版。

區域                                        | 可用性？
-----------------------------------------------|-----------------------------------------------
[所有正式推出的全域 Azure 區域](https://azure.microsoft.com/en-us/regions/)     | 正式推出 
[Azure Government](https://azure.microsoft.com/en-us/overview/clouds/government/)              | 預覽狀態 
[Azure China](https://www.azure.cn/)                                                           | 預覽狀態
[Azure Germany](https://azure.microsoft.com/en-us/overview/clouds/germany/)                    | 預覽狀態

當服務可用於其他 Azure 雲端時，此表格就會會更新。

若要試用執行個體中繼資料服務，請從 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) 或 [Azure 入口網站](http://portal.azure.com)的上述區域中建立 VM，並遵循以下的範例。

## <a name="usage"></a>使用量

### <a name="versioning"></a>版本控制
執行個體中繼資料服務已建立版本。 版本是必要項目，且目前版本為 `2017-04-02`。

> [!NOTE] 
> 先前排定事件的預覽版支援作為 API 版本的 {latest}。 此格式將不再受到支援且之後會遭到取代。

當我們新增較新版本時，如果您的指令碼對於特定資料格式有相依性，則因為相容性，仍然可以存取較舊版本。 不過，請注意服務正式推出後，就無法使用目前的預覽版本 (2017-03-01)。

### <a name="using-headers"></a>使用標頭
查詢中繼資料執行個體服務時，您必須提供 `Metadata: true` 標頭以免不小心重新導向要求。

### <a name="retrieving-metadata"></a>擷取中繼資料

執行個體中繼資料適用於使用 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) 建立/管理的執行中 VM。 使用下列要求，存取虛擬機器執行個體的所有資料類別：

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02"
```

> [!NOTE] 
> 所有執行個體中繼資料查詢都會區分大小寫。

### <a name="data-output"></a>資料輸出
根據預設，執行個體中繼資料服務會以 JSON 格式傳回資料 (`Content-Type: application/json`)。 不過，不同的 API 可以依照要求傳回不同格式的資料。
下表是 API 可能支援之其他資料格式的參考。

API | 預設資料格式 | 其他格式
--------|---------------------|--------------
/instance | json | 文字
/scheduledevents | json | 無

若要存取非預設的回應格式，請指定要求的格式作為要求中的 querystring 參數。 例如：

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02&format=text"
```

### <a name="security"></a>安全性
執行個體中繼資料服務端點只能從非可路由 IP 位址上的執行中虛擬機器執行個體內存取。 此外，服務會拒絕任何具有 `X-Forwarded-For` 標頭的要求。
我們也需要要求包含 `Metadata: true` 標頭，以確認直接想要實際要求，而不是非預期重新導向的一部分。 

### <a name="error"></a>錯誤
如果找不到資料元素或要求的格式錯誤，則執行個體中繼資料服務會傳回標準 HTTP 錯誤。 例如：

HTTP 狀態碼 | 原因
----------------|-------
200 確定 |
400 不正確的要求 | 遺漏 `Metadata: true` 標頭
404 找不到 | 要求的元素不存在 
405 不允許的方法 | 僅支援 `GET` 和 `POST` 要求
429 要求太多 | API 目前支援最多每秒 5 個查詢
500 服務錯誤     | 稍後重試

### <a name="examples"></a>範例

> [!NOTE] 
> 所有 API 回應都是 JSON 字串。 下列所有範例回應均列印清晰，很容易閱讀。

#### <a name="retrieving-network-information"></a>擷取網路資訊

**要求**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-04-02"
```

**回應**

> [!NOTE] 
> 回應是 JSON 字串。 下列範例回應均列印清晰，很容易閱讀。

```
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>擷取公用 IP 位址

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-04-02&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>擷取執行個體的所有中繼資料

**要求**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-04-02"
```

**回應**

> [!NOTE] 
> 回應是 JSON 字串。 下列範例回應均列印清晰，很容易閱讀。

```
{
  "compute": {
    "location": "westcentralus",
    "name": "IMDSSample",
    "offer": "UbuntuServer",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "Canonical",
    "sku": "16.04.0-LTS",
    "version": "16.04.201610200",
    "vmId": "5d33a910-a7a0-4443-9f01-6a807801b29b",
    "vmSize": "Standard_A1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.0.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.0.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3AF806EC"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>擷取 Windows 虛擬機器中的中繼資料

**要求**

可以透過 Powershell 公用程式 `curl` 在 Windows 中擷取執行個體中繼資料： 

```
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2017-04-02 | select -ExpandProperty Content
```

或透過 `Invoke-RestMethod` Cmdlet：
    
```
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2017-04-02 -Method get 
```

**回應**

> [!NOTE] 
> 回應是 JSON 字串。 下列範例回應均列印清晰，很容易閱讀。

```
{
  "compute": {
    "location": "westus",
    "name": "SQLTest",
    "offer": "SQL2016SP1-WS2016",
    "osType": "Windows",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "MicrosoftSQLServer",
    "sku": "Enterprise",
    "version": "13.0.400110",
    "vmId": "453945c8-3923-4366-b2d3-ea4c80e9b70e",
    "vmSize": "Standard_DS2"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": [
            
          ]
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="instance-metadata-data-categories"></a>執行個體中繼資料資料類別
可透過執行個體中繼資料服務取得下列資料類別：

資料 | 說明
-----|------------
location | VM 執行所在的 Azure 區域
名稱 | VM 的名稱 
提供項目 | 提供 VM 映像的資訊。 此值只會針對從 Azure 映像庫部署的映像呈現。
publisher | VM 映像的發佈者
sku | VM 映像的特定 SKU  
版本 | VM 映像的版本 
osType | Linux 或 Windows 
platformUpdateDomain |  VM 執行所在的[更新網域](virtual-machines-windows-manage-availability.md)
platformFaultDomain | VM 執行所在的[容錯網域](virtual-machines-windows-manage-availability.md)
vmId | VM 的[唯一識別碼](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
vmSize | [VM 大小](virtual-machines-windows-sizes.md)
ipv4/privateIpAddress | VM 的本機 IPv4 位址 
ipv4/publicIpAddress | VM 的公用 IPv4 位址
subnet/address | VM 的子網路位址
subnet/prefix | 子網路首碼，範例 24
ipv6/ipAddress | VM 的本機 IPv6 位址
macAddress | VM mac 位址 
scheduledevents | 目前為公開預覽版，請參閱 [scheduledevents](virtual-machines-scheduled-events.md)

## <a name="example-scenarios-for-usage"></a>使用方式的範例案例  

### <a name="tracking-vm-running-on-azure"></a>追蹤在 Azure 上執行的 VM

身為服務提供者，您可能需要追蹤執行軟體的 VM 數目，或者具有需要追蹤 VM 唯一性的代理程式。 若要能夠取得 VM 的唯一識別碼，請從執行個體中繼資料服務使用 `vmId` 欄位。

**要求**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-04-02&format=text"
```

**回應**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>容器的位置，資料分割基礎容錯/更新網域 

對於特定案例，不同資料複本的放置相當重要。 例如 [HDFS 複本放置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)，或透過 [orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) 的容器放置，您需要知道 VM 執行所在的 `platformFaultDomain` 和 `platformUpdateDomain`。
您可以直接透過執行個體中繼資料服務查詢此資料。

**要求**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-04-02&format=text" 
```

**回應**

```
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>取得支援案例期間 VM 的相關詳細資訊 

身為服務提供者，您可能會收到支援呼叫，而您想要知道更多 VM 的相關資訊。 要求客戶共用計算中繼資料可以為支援專業人員提供基本資訊，以了解 Azure 上的 VM 種類。 

**要求**

```
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2017-04-02"
```

**回應**

> [!NOTE] 
> 回應是 JSON 字串。 下列範例回應均列印清晰，很容易閱讀。

```
{
  "compute": {
    "location": "CentralUS",
    "name": "IMDSCanary",
    "offer": "RHEL",
    "osType": "Linux",
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "publisher": "RedHat",
    "sku": "7.2",
    "version": "7.2.20161026",
    "vmId": "5c08b38e-4d57-4c23-ac45-aca61037f084",
    "vmSize": "Standard_DS2"
  }
}
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>在 VM 內使用不同語言呼叫中繼資料服務的範例 

語言 | 範例 
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go Lan   | https://github.com/Microsoft/azureimds/blob/master/imdssample.go            
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Javascript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Powershell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
    

## <a name="faq"></a>常見問題集
1. 我收到錯誤 `400 Bad Request, Required metadata header not specified`。 這代表什麼？
   * 執行個體中繼資料服務需要在要求中傳遞標頭 `Metadata: true`。 在 REST 呼叫中傳遞此標頭可允許存取執行個體中繼資料服務。 
2. 為什麼我沒有收到我的 VM 計算資訊？
   * 目前執行個體中繼資料服務僅支援使用 Azure Resource Manager 建立的執行個體。 未來，我們可能會新增雲端服務 VM 支援。
3. 我在一陣子之後回過頭來透過 Azure Resource Manager 建立我的虛擬機器。 為什麼我看不到計算中繼資料資訊？
   * 針對在 2016 年 9 月之後建立的 VM，新增[標記](../azure-resource-manager/resource-group-using-tags.md)才會開始看到計算中繼資料。 針對較舊的 VM (在 2016 年 9 月之前建立)，對 VM 新增/移除擴充功能或資料磁碟，以重新整理中繼資料。
4. 我為何收到錯誤 `500 Internal Server Error`？
   * 請根據指數型輪詢系統重試您的要求。 若問題持續發生，請連絡 Azure 支援。
5. 我要在哪裡共用其他問題/註解？
   * 請在 http://feedback.azure.com 上傳送您的註解。
7. 這是否適用於虛擬機器擴展集執行個體？
   * 是，中繼資料服務適用於擴展集執行個體。 
6. 如何取得服務支援？
   * 若要取得服務支援，請在 Azure 入口網站中針對您無法在長時間重試後取得中繼資料回應的 VM 建立支援問題。 

   ![執行個體中繼資料支援](./media/virtual-machines-instancemetadataservice-overview/InstanceMetadata-support.png)
    
## <a name="next-steps"></a>後續步驟

- 深入了解執行個體中繼資料服務所提供的 [scheduledevents](virtual-machines-scheduled-events.md) API **公開預覽版**。

