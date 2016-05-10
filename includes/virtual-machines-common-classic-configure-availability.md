


可用性設定組可協助您的虛擬機器在停機期間 (例如維護期間) 仍然保持可用狀態。在可用性設定組中置入二或多個類似設定的虛擬機器，將可針對虛擬機器所執行的應用程式或服務，建立維護其可用性所需的備援。如需其運作方式的詳細資訊，請參閱[管理虛擬機器的可用性][]。

同時使用可用性設定組和負載平衡端點，是協助確保您的應用程式一直可用並有效率執行的最佳做法。如需負載平衡端點的詳細資訊，請參閱 [Azure 基礎結構服務的負載平衡][]。

在傳統的部署模型中，您可以使用下列兩個選項的其中之一，將虛擬機器放入可用性設定組：

- [選項 1：同時建立虛擬機器和可用性設定組][]。然後，將新的虛擬機器加入您在建立這些虛擬機器時的設定組。
- [選項 2：將現有虛擬機器加入至可用性設定組][]。

>[AZURE.NOTE] 在傳統模型中，您要放入相同可用性設定組的虛擬機器必須隸屬於相同的雲端服務。

## <a id="createset"> </a>選項 1：同時建立虛擬機器和可用性設定組##

您可以使用 Azure 傳統入口網站或 Azure PowerShell 命令來執行此作業。

若要使用 Azure 傳統入口網站：

1. 如果您未曾執行過這項操作，請登入 Azure 傳統入口網站。

2. 按一下命令列上的 [新增]。

3. 按一下 [虛擬機器]，然後按一下 [從組件庫]。

4. 使用前兩個畫面來選取映像、使用者名稱和密碼等。如需詳細資訊，請參閱[建立執行 Windows 的虛擬機器][]。

5. 在第三個畫面中，您可以設定網路、儲存體和可用性的資源。執行下列動作：

	1. 選擇適當的雲端服務。保留 [**建立新的雲端服務**] 的設定 (除非您打算將此新虛擬機器新增至現有的虛擬機器雲端服務)。然後，在 [Cloud Service DNS Name] 下輸入名稱。此 DNS 名稱便會成為 URI (用來連絡虛擬機器) 的一部分。雲端服務會以通訊和獨立群組的方式運作。相同雲端服務中的所有虛擬機器可以彼此相互通訊、可以設定使用負載平衡，以及放置在相同的可用性設定組中。

	2. 如果您打算使用虛擬網路，請在 [Region/Affinity Group/Virtual Network] 下指定一個虛擬網路。**重要**：如果您想要讓虛擬機器使用虛擬網路，就必須在建立虛擬機器時將虛擬機器加入虛擬網路。在建立虛擬機器後，您無法將虛擬機器加入至虛擬網路。如需詳細資訊，請參閱[虛擬網路概觀][]。

	3. 建立可用性設定組。在 [可用性設定組] 下，保留 [Create an availability set] 的設定。接著，輸入此設定組的名稱。

	4. 建立預設端點，並視需要新增更多端點。您也可以稍後再新增端點。

	![建立新虛擬機器的可用性集合](./media/virtual-machines-common-classic-configure-availability/VMavailabilityset.png)

6. 在第四個畫面上，按一下您要安裝的擴充功能。擴充程式提供可輕鬆管理虛擬機器的功能，例如執行反惡意程式碼或重設密碼。如需詳細資訊，請參閱 [Azure VM 代理程式與 VM 擴充程式](../articles/virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md)。

7.	按一下箭號以建立虛擬機器和可用性設定組。

	在新虛擬機器的儀表板中，您可以按一下 [設定] 並查看此虛擬機器是否隸屬於新的可用性設定組。

若要使用 Azure PowerShell 命令建立 Azure 虛擬機器，並將它新增至新的或現有的可用性設定組，請參閱[使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](../articles/virtual-machines/virtual-machines-windows-classic-create-powershell.md)

## <a id="addmachine"> </a>選項 2：將現有虛擬機器加入至可用性設定組##

在 Azure 傳統入口網站中，您可以將現有的虛擬機器新增至現有的可用性設定組，或為其建立一個新的可用性設定組。(請注意，相同可用性設定組中的虛擬機器必須隸屬於相同的雲端服務。) 步驟幾乎完全相同。有了 Azure PowerShell，您可以將虛擬機器加入現有的可用性設定組。

1. 如果您未曾執行過這項操作，請登入 Azure 傳統入口網站。

2. 在命令列中，按一下 [**虛擬機器**]。

3. 從虛擬機器的清單中，選取您想要加入至集合的虛擬機器名稱。

4. 從虛擬機器名稱下方的索引標籤中，按一下 [設定]。

5. 在 [設定] 區段中，尋找 [可用性設定組]。執行下列其中一項：

	答：選取 [**建立可用性集合**]，然後輸入集合的名稱。

	B.選取 [**選取可用性集合**]，然後從清單中挑選一個集合。

	![建立現有虛擬機器的可用性集合](./media/virtual-machines-common-classic-configure-availability/VMavailabilityExistingVM.png)

6. 按一下 [儲存]。

若要使用 Azure PowerShell 命令，請開啟系統管理員層級的 Azure PowerShell 工作階段並執行下列命令。在預留位置 (例如 &lt;VmCloudServiceName&gt;) 中，以正確的名稱取代引號中的所有內容，包括 < and > 字元。

	Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE] 系統可能必須重新啟動虛擬機器，以結束將它加入至可用性集合的作業。

## 其他資源

[傳統虛擬機器的文章][]

<!-- LINKS -->
[選項 1：同時建立虛擬機器和可用性設定組]: #createset
[選項 2：將現有虛擬機器加入至可用性設定組]: #addmachine

[Azure 基礎結構服務的負載平衡]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[管理虛擬機器的可用性]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md

[建立執行 Windows 的虛擬機器]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[虛擬網路概觀]: ../articles/virtual-network/virtual-networks-overview.md
[傳統虛擬機器的文章]: https://azure.microsoft.com/documentation/articles/?tag=azure-service-management&service=virtual-machines

<!---HONumber=AcomDC_0427_2016-->