---
title: "在 Azure Cloud Shell 中執行 Ansible"
description: "了解如何在 Azure Cloud Shell 中執行各種 Ansible 工作"
ms.service: ansible
keywords: "ansible, azure, devops, bash, cloudshell, 腳本"
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: d5a818616d382954d0880bcae58bb13b632ad757
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2018
---
# <a name="run-ansible-in-the-azure-cloud-shell"></a>在 Azure Cloud Shell 中執行 Ansible

在本教學課程中，您會了解如何在 Azure Cloud Shell 中執行各種 Ansible 工作。 這些工作包括連線至虛擬機器，以及建立 Ansible 腳本來建立並刪除 Azure 資源群組。

## <a name="prerequisites"></a>先決條件

- **Azure 訂用帳戶** - 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

- **Azure 認證** - [建立 Azure 認證和設定 Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

- **設定 Azure Cloud Shell** - 如果您不熟悉 Azure Cloud Shell，[Azure Cloud Shell 中 Bash 的快速入門](https://docs.microsoft.com/en-us/azure/cloud-shell/quickstart)一文會說明如何啟動及設定 Cloud Shell。

## <a name="use-ansible-to-connect-to-a-vm"></a>使用 Ansible 連線至 VM
Ansible 已建立名為 [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) 的 Python 指令碼，會向 Azure Resource Manager 提出 API 要求，以產生您 Azure 資源的動態清單。 下列步驟將引導您使用 `azure_rm.py` 指令碼，以連線到 Azure 虛擬機器：

1. 開啟 Azure Cloud Shell。

1. 如果您沒有要使用的虛擬機器，請將下列命令輸入 Cloud Shell 中，以建立用來測試的虛擬機器：

  ```azurecli-interactive
  az group create --resource-group ansible-test-rg --location eastus
  ```

  ```azurecli-interactive
  az vm create --resource-group ansible-test-rg --name ansible-test-vm --image UbuntuLTS --generate-ssh-keys
  ```

1. 使用 GNU `wget` 命令來取出 `azure_rm.py` 指令碼：

  ```azurecli-interactive
  wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
  ```

1. 使用 `chmod` 命令將存取權限變更為 `azure_rm.py` 指令碼。 下列命令會使用 `+x` 參數，以允許執行 (執行) 指定的檔案 (`azure_rm.py`)：

  ```azurecli-interactive
  chmod +x azure_rm.py
  ```

1. 使用 [ansible 命令](https://docs.ansible.com/ansible/2.4/ansible.html)連線到您的虛擬機器： 

  ```azurecli-interactive
  ansible -i azure_rm.py ansible-test-vm -m ping
  ```

  一旦連線後，您應該會看到類似輸出的結果：

  ```Output
  The authenticity of host 'nn.nnn.nn.nn (nn.nnn.nn.nn)' can't be established.
  ECDSA key fingerprint is SHA256:&lt;some value>.
  Are you sure you want to continue connecting (yes/no)? yes
  test-ansible-vm | SUCCESS => {
      "changed": false,
      "failed": false,
      "ping": "pong"
  }
  ```

1. 如果您已在本節中建立資源群組和虛擬機器

  ```azurecli-interactive
  az group delete -n <resourceGroup>
  ```

## <a name="run-a-playbook-in-cloud-shell"></a>在 Cloud Shell 中執行腳本
[ansible-playbook](https://docs.ansible.com/ansible/2.4/ansible-playbook.html) 命令會執行 Ansible 腳本，在目標主機上執行工作。 本節會引導您使用 Cloud Shell 來建立和執行兩個腳本 - 一個腳本用來建立資源群組，第二個腳本用來刪除資源群組。 

1. 建立名為 `rg.yml` 的檔案，如下所示：

  ```azurecli-interactive
  vi rg.yml
  ```

1. 將下列內容複製到 Cloud Shell 視窗 (現在託管 VI 編輯器的執行個體) 中：

  ```yml
  - name: My first Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
          name: demoresourcegroup
          location: eastus
  ```

1. 輸入 `:wq` 並按下 &lt;Enter> 來儲存檔案及結束 VI 編輯器。

1. 使用 `ansible-playbook` 命令以執行 `rg.yml` 腳本：

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. 您應該會看到類似下面輸出的結果：

  ```Output
  PLAY [My first Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Create a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

1. 驗證部署：

  ```azurecli-interactive
  az group show -n demoresourcegroup
  ```

1. 現在您已建立資源群組，請建立第二個 Ansible 腳本來刪除資源群組：

  ```azurecli-interactive
  vi rg2.yml
  ```

1. 將下列內容複製到 Cloud Shell 視窗 (現在託管 VI 編輯器的執行個體) 中：

  ```yml
  - name: My second Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
          name: demoresourcegroup
          state: absent
  ```

1. 輸入 `:wq` 並按下 &lt;Enter> 來儲存檔案及結束 VI 編輯器。

1. 使用 `ansible-playbook` 命令以執行 `rg2.yml` 腳本：

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. 您應該會看到類似下面輸出的結果：

  ```Output
  The output is as following. 
  PLAY [My second Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Delete a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [使用 Ansible 在 Azure 中建立基本虛擬機器](/azure/virtual-machines/linux/ansible-create-vm)