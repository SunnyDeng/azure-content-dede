In der folgenden Tabelle sind die Anforderungen für richtlinienbasierte und routenbasierte VPN Gateways aufgeführt. Diese Tabelle betrifft sowohl das Resource Manager-Bereitstellungsmodell als auch das klassische Bereitstellungsmodell. Im klassischen Modell entsprechen richtlinienbasierte VPN Gateways statischen Gateways und routenbasierte Gateways dynamischen Gateways.


| | **Richtlinienbasiertes Basic VPN Gateway** | **Routenbasiertes Basic VPN Gateway** | **Routenbasiertes Standard VPN Gateway** | **Routenbasiertes High Performance VPN Gateway** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
| **Site-to-Site-Konnektivität (S2S)** | Richtlinienbasierte VPN-Konfiguration | Weiterleitungsbasierte VPN-Konfiguration | Weiterleitungsbasierte VPN-Konfiguration | Weiterleitungsbasierte VPN-Konfiguration |
| **Punkt-zu-Standort-Konnektivität (P2S)** | Nicht unterstützt | Unterstützt (gemeinsame Verwendung mit S2S möglich) | Unterstützt (gemeinsame Verwendung mit S2S möglich) | Unterstützt (gemeinsame Verwendung mit S2S möglich) |
| **Authentifizierungsmethode** | Vorinstallierter Schlüssel | Vorinstallierter Schlüssel für S2S-Konnektivität, Zertifikate für P2S-Konnektivität | Vorinstallierter Schlüssel für S2S-Konnektivität, Zertifikate für P2S-Konnektivität | Vorinstallierter Schlüssel für S2S-Konnektivität, Zertifikate für P2S-Konnektivität |
| **Maximale Anzahl von S2S-Verbindungen** | 1 | 10 | 10 | 30 |
| **Maximale Anzahl von P2S-Verbindungen** | Nicht unterstützt | 128 | 128 | 128 |
|**Aktive Routingunterstützung (BGP)** | Nicht unterstützt | Nicht unterstützt | Nicht unterstützt | Nicht unterstützt |
 

<!---HONumber=AcomDC_0224_2016-->