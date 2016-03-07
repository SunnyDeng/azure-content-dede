Es gibt drei VPN-Gateway-SKUs:

- Basic
- Standard
- Leistung

In der folgenden Tabelle sind die Gatewaytypen und der geschätzte zusammengefasste Durchsatz angegeben. Für die einzelnen Gateway-SKUs gelten unterschiedliche Preise. Informationen zu den Preisen finden Sie unter [VPN-Gateway – Preise](https://azure.microsoft.com/pricing/details/vpn-gateway/). Diese Tabelle betrifft sowohl das Resource Manager-Bereitstellungsmodell als auch das klassische Bereitstellungsmodell.


| | **VPN-Gateway-Durchsatz** | **Max. IPsec-Tunnel für VPN-Gateway** | **ExpressRoute-Gateway-Durchsatz** | **Gemeinsame Verwendung von VPN-Gateway und ExpressRoute**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **Basic-SKU** | 100 MBit/s | 10 | 500 MBit/s | Nein |
| **Standard-SKU** | 100 MBit/s | 10 | 1\.000 MBit/s | Ja |
| **High-Performance-SKU** | 200 MBit/s | 30 | 2\.000 MBit/s | Ja |


**Hinweis:** Der VPN-Durchsatz ist eine grobe Schätzung, die auf Messungen zwischen den VNets einer Azure-Region basiert. Dies ist keine Garantie für den Wert, den Sie für standortübergreifende Verbindungen über das Internet erzielen können. Die Angabe sollte stattdessen als maximal möglicher Wert angesehen werden.

<!---HONumber=AcomDC_0224_2016-->