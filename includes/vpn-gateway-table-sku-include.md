有 3 種 VPN 閘道 SKU：

* 基本
* 標準
* 高效能

下方資料表顯示閘道類型，以及估計的彙總輸送量。閘道 SKU 之間的定價並不相同。如需定價資訊，請參閱＜[VPN 閘道定價](https://azure.microsoft.com/pricing/details/vpn-gateway/)＞。此資料表適用於資源管理員與傳統部署模型。

|  | **VPN 閘道輸送量** | **VPN 閘道最大 IPsec 通道** | **ExpressRoute 閘道輸送量** | **VPN 閘道與 ExpressRoute 共存** |
| --- | --- | --- | --- | --- |
| **基本 SKU** |100 Mbps |10 |500 Mbps |否 |
| **標準 SKU** |100 Mbps |10 |1000 Mbps |是 |
| **高效能 SKU** |200 Mbps |30 |2000 Mbps |是 |

**注意：**VPN 輸送量是根據相同 Azure 區域中 VNet 之間度量的概略估計。這無法保證您可以針對網際網路上的跨單位連接取得何種數值，但應該用作最大的可能量值。

<!---HONumber=AcomDC_0224_2016-->