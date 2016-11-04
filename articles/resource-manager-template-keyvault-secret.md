---
title: 適用於金鑰保存庫中密碼的資源管理員範本 | Microsoft Docs
description: 說明可透過範本部署金鑰保存庫密碼的資源管理員結構描述。
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: tomfitz

---
# 金鑰保存庫密碼範本結構描述
建立儲存於金鑰保存庫的密碼。此資源類型通常會部署為[金鑰保存庫](resource-manager-template-keyvault.md)的子資源。

## 結構描述格式
若要建立金鑰保存庫密碼，可將下列結構描述新增至範本。密碼可定義為金鑰保存庫的子資源或做為最上層資源。將金鑰保存庫部署於同一個範本時，您可以將密碼定義為子資源。若金鑰保存庫未部署於同一個範本，或者您需要藉由在資源類型中執行迴圈來建立多個密碼時，就需要將密碼定義為最上層資源。

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## 值
下表描述您在結構描述中必須設定的值。

| 名稱 | 值 |
| --- | --- |
| 類型 |列舉<br />必要<br />**secrets** (部署為金鑰保存庫的子資源時) 或<br /> **Microsoft.KeyVault/vaults/secrets** (部署為最上層資源時)<br /><br />要建立的資源類型。 |
| apiVersion |列舉<br />必要<br />**2015-06-01** 或 **2014-12-19-preview**<br /><br />要用來建立資源的應用程式開發介面 (API) 版本。 |
| 名稱 |字串<br />必要<br />部署為金鑰保存庫子資源時的單一字組，或部署為要新增至現有金鑰保存庫的最上層資源且格式為 **{金鑰保存庫名稱}/{密碼名稱}** 時的單一字組。<br /><br />要建立的密碼名稱。 |
| 屬性 |物件<br />必要<br />[屬性物件](#properties)<br /><br />指定要建立之密碼值的物件。 |
| dependsOn |陣列<br />選用<br />以逗號分隔的資源名稱或資源唯一識別碼清單。<br /><br />此連結所相依的資源集合。如果將密碼的金鑰保存庫部署於同一個範本，請在這個項目中包含金鑰保存庫的名稱，以確保會優先部署它。 |

<a id="properties" />

### 屬性物件
| 名稱 | 值 |
| --- | --- |
| value |字串<br />必要<br /><br />儲存於金鑰保存庫的密碼值。傳入這個屬性的值時，請使用類型為 **securestring** 的參數。 |

## 範例
第一個範例將密碼部署為金鑰保存庫的子資源。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

第二個範例將密碼部署為最上層資源 (其儲存於現有的金鑰保存庫中)。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## 後續步驟
* 如需金鑰保存庫的一般資訊，請參閱[開始使用 Azure 金鑰保存庫](key-vault/key-vault-get-started.md)。
* 如需部署範本時參考金鑰保存庫密碼的範例，請參閱[在部署期間傳遞安全值](resource-manager-keyvault-parameter.md)。

<!---HONumber=AcomDC_0629_2016-->