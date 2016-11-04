---
title: 使用 REST API 來檢視部署作業 | Microsoft Docs
description: 描述如何使用 Azure Resource Manager REST API 來偵測源自「資源管理員」部署的問題。
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 06/13/2016
ms.author: tomfitz

---
# 使用 Azure Resource Manager REST API 來檢視部署作業
> [!div class="op_single_selector"]
> * [入口網站](resource-manager-troubleshoot-deployments-portal.md)
> * [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
> * [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
> * [REST API](resource-manager-troubleshoot-deployments-rest.md)
> 
> 

如果您在將資源部署到 Azure 時收到錯誤，您可能會想要查看有關所執行之部署作業的更多詳細資料。REST API 提供作業，可讓您找出錯誤並判斷可能的修正方法。

[!INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

部署之前先驗證您的範本和基礎結構，即可避免發生一些錯誤。您也可以記錄部署期間的要求和回應資訊，這在後續進行疑難排解時可能會有幫助。如需了解驗證，以及記錄要求和回應資訊，請參閱[使用 Azure Resource Manager 範本部署資源群組](resource-group-template-deploy-rest.md)。

## REST API 疑難排解
1. 使用[建立範本部署](https://msdn.microsoft.com/library/azure/dn790564.aspx)作業來部署您的資源。若要保留可能有助於偵錯的資訊，請將 JSON 要求中的 **debugSetting** 屬性設定為 **requestContent** 和/或 **responseContent**。 
   
        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",      
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }
   
    根據預設，**debugSetting** 值會設定為 **none**。指定 **debugSetting** 值時，請仔細考慮您在部署期間傳遞的資訊類型。如果記錄要求或回應的相關資訊，您可能會公開透過部署作業擷取的機密資料。
2. 利用[取得範本部署的相關資訊](https://msdn.microsoft.com/library/azure/dn790565.aspx)作業，來取得部署相關資訊。
   
        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
   
    在回應中，請特別注意 **provisioningState**、**correlationId** 和 **error** 元素。**correlationId** 可用來追蹤相關的事件，並在與技術支援人員合作來疑難排解問題時非常有用。
   
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  "error": {\r\n    "message": "Conflict",\r\n    "code": "Conflict"\r\n  }\r\n}"}]
            }  
          }
        }
3. 使用[列出所有範本部署作業](https://msdn.microsoft.com/library/azure/dn790518.aspx)的作業，來取得部署作業的相關資訊。
   
        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
   
    回應會根據您在部署期間於 **debugSetting** 屬性中指定的內容，來包含要求和/或回應資訊。
   
        {
          ...
          "properties": 
          {
            ...
            "request":{
              "content":{
                "location":"West US",
                "properties":{
                  "accountType": "Standard_LRS"
                }
              }
            },
            "response":{
              "content":{
                "error":{
                  "message":"Conflict","code":"Conflict"
                }
              }
            }
          }
        }
4. 利用[列出訂用帳戶中的管理事件](https://msdn.microsoft.com/library/azure/dn931934.aspx)作業，從稽核記錄中取得部署的事件。
   
        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}

## 後續步驟
* 如需解決特定部署錯誤的說明，請參閱[針對使用 Azure Resource Manager 將資源部署至 Azure 時常見的錯誤進行疑難排解](resource-manager-common-deployment-errors.md)。
* 若要了解如何使用稽核記錄檔來監視其他類型的動作，請參閱[使用 Resource Manager 來稽核作業](resource-group-audit.md)。
* 若要在執行之前驗證您的部署，請參閱[使用 Azure Resource Manager 範本部署資源群組](resource-group-template-deploy.md)。

<!---HONumber=AcomDC_0615_2016-->