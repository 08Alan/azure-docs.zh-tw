<a id="step-1-navigate-to-the-virtual-network-gateway" class="xliff"></a>

### 步驟 1：瀏覽至虛擬網路閘道

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至 [所有資源]。 
2. 若要開啟虛擬網路閘道刀鋒視窗，請瀏覽至您想要刪除的虛擬網路閘道，然後按一下它。

<a id="step-2-delete-connections" class="xliff"></a>

### 步驟 2︰刪除連線

1. 在虛擬網路閘道的刀鋒視窗中，按一下 [連線] 以檢視與閘道的所有連線。
2. 按一下連線名稱資料列上的 [...] ，然後從下拉式清單選取 [刪除]。
3. 按一下 [是] 確認要刪除連線。 如果您有多個連線，請刪除每個連線。

<a id="step-3-delete-the-virtual-network-gateway" class="xliff"></a>

### 步驟 3：刪除虛擬網路閘道

請注意，如果除了 S2S 組態，您還擁有此 VNet 適用的 P2S 組態，刪除虛擬網路閘道將自動中斷所有的 P2S 用戶端連線，而不會發出任何警告。

1. 在虛擬網路閘道刀鋒視窗上，按一下 [概觀]。
2. 在 [概觀] 刀鋒視窗中，按一下 [刪除] 以刪除閘道。
