#### Einschränkungen für ExpressRoute

Für ExpressRoute-Ressourcen gelten pro Abonnement die folgenden Einschränkungen.

| Ressource | Standardlimit |
|---|---|
| ExpressRoute-Schaltkreise pro Abonnement | 10 |
| ExpressRoute-Schaltkreise pro Region pro Abonnement für ARM | 10 |
| Maximale Anzahl von Routen für Private Peering in Azure mit ExpressRoute-Standard | 4\.000 |
| Maximale Anzahl von Routen für Private Peering in Azure mit ExpressRoute-Premium-Add-on | 10\.000 |
| Maximale Anzahl von Routen für Public Peering in Azure mit ExpressRoute-Standard | 200 |
| Maximale Anzahl von Routen für Public Peering in Azure mit ExpressRoute-Premium-Add-on | 200 |
| Maximale Anzahl von Routen für Microsoft-Peering in Azure mit ExpressRoute-Standard | 200 |
| Maximale Anzahl von Routen für Microsoft-Peering in Azure mit ExpressRoute-Premium-Add-on | 200 |
| Anzahl der pro ExpressRoute-Schaltkreis zulässigen virtuellen Netzwerkverbindungen | siehe Tabelle unten |

#### Anzahl der virtuellen Netzwerke pro ExpressRoute-Schaltkreis

| **Verbindungsgröße** | **Anzahl der VNet-Links – Standard** | **Anzahl der VNet-Links – Premium-Add-on** |
|---|---|---|
| 10 MBit/s | 10 | Nicht unterstützt |
| 50 MBit/s | 10 | 20 |
| 100 MBit/s | 10 | 25 |
| 200 MBit/s | 10 | 25 |
| 500 MBit/s | 10 | 40 |
| 1 GBit/s | 10 | 50 |
| 2 GBit/s | 10 | 50 |
| 5 GBit/s | 10 | 50 |
| 10 GBit/s | 10 | 100 |

<!---HONumber=Sept15_HO4-->