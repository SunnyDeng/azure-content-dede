| | **VPN Gateway-Durchsatz (1)** | **Max. IPsec-Tunnel für VPN Gateway (2)** | **ExpressRoute-Gateway-Durchsatz** | **Gemeinsame Verwendung von VPN-Gateway und ExpressRoute**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **Basic-SKU** | 100 MBit/s | 10 | 500 MBit/s | Nein |
| **Standard-SKU** | 100 MBit/s | 10 | 1\.000 MBit/s | Ja |
| **High-Performance-SKU** | 200 MBit/s | 30 | 2\.000 MBit/s | Ja |

- (1) Der VPN-Durchsatz ist eine grobe Schätzung, die auf Messungen zwischen den VNets einer Azure-Region basiert. Dies ist keine Garantie für den Wert, den Sie für standortübergreifende Verbindungen über das Internet erzielen können. Die Angabe sollte stattdessen als maximal möglicher Wert angesehen werden.
- (2) Die Anzahl der Tunnel bezieht sich auf das unten angegebene routenbasierte VPN. Ein richtlinienbasiertes VPN kann nur einen Site-to-Site-VPN-Tunnel unterstützen.

<!---HONumber=AcomDC_0323_2016-->