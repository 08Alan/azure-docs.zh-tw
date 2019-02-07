---
title: Azure PowerShell 指令碼範例 - 備份 Azure 虛擬機器 | Microsoft Docs
description: Azure PowerShell 指令碼範例 - 備份 Azure 虛擬機器
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 915c5f6c8e8de1b5a7a7590ba41125cbff7b8f36
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497633"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>使用 PowerShell 備份已加密的 Azure 虛擬機器

此指令碼會針對已加密的 Azure 虛擬機器，建立具有異地備援儲存體 (GRS) 的復原服務保存庫。 預設保護原則會套用到保存庫。 此原則會產生虛擬機器的每日備份，並將每個備份保留 30 天。 此指令碼也會觸發虛擬機器的初始復原點，並將該復原點保留 365 天。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、VM 和所有相關資源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立部署。 下表中的每個項目都會連結至命令特定的文件。


| 命令 | 注意 | 
|---|---| 
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 建立用來存放所有資源的資源群組。 | 
| [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/New-AzureRmRecoveryServicesVault) | 建立復原服務保存庫來儲存備份。 | 
| [Set-AzureRmRecoveryServicesBackupProperties](/powershell/module/azurerm.recoveryservices/Set-AzureRmRecoveryServicesBackupProperties) | 在復原服務保存庫上設定備份儲存體屬性。 | 
| [New-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)| 在復原服務保存庫中，使用排程原則和保留原則來建立保護原則。 | 
| [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) | 在 Key Vault 上設定權限，以授與服務主體對加密金鑰的存取權。 | 
| [Enable-AzureRmRecoveryServicesBackupProtection](/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection) | 使用指定的備份保護原則來啟用項目的備份。 | 
| [Set-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy)| 修改現有的備份保護原則。 | 
| [Backup-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem) | 針對未繫結到備份排程的受保護 Azure 備份項目，開始進行備份。 |
| [Wait-AzureRmRecoveryServicesBackupJob](/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob) | 等待 Azure 備份作業完成。 | 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 移除資源群組及其內含的所有資源。 | 

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

