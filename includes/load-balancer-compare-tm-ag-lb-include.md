## Load Balancer-Unterschiede

Es gibt verschiedene Optionen zur Verteilung des Netzwerkdatenverkehrs mit Microsoft Azure. Diese Optionen funktionieren unterschiedlich, bieten jeweils verschiedene Featuregruppen und unterstützen verschiedene Szenarien. Sie können unabhängig voneinander oder kombiniert verwendet werden.

- Azure Load Balancer arbeitet in der Vermittlungsschicht (Schicht 4 des OSI-Netzwerkreferenzmodells). Er bietet eine Verteilung des Datenverkehrs auf Netzwerkebene über Instanzen einer Anwendung, die in demselben Azure-Rechenzentrum ausgeführt wird.

- Application Gateway funktioniert in der Anwendungsschicht (Schicht 7 des OSI-Netzwerkreferenzmodells). Er fungiert als Reverseproxydienst, beendet die Clientverbindung und leitet Anforderungen an Back-End-Endpunkte weiter.

- 	Traffic Manager funktioniert auf DNS-Ebene. Er verwendet DNS-Antworten, um Endbenutzer-Datenverkehr an global verteilte Endpunkte zu leiten. Clients stellen dann eine direkte Verbindung mit diesen Endpunkten her. In der folgenden Tabelle werden die von den einzelnen Diensten angebotenen Features zusammengefasst:

|Azure-Lastenausgleich |	Application Gateway | Traffic Manager |
|---|---|---|
|Technologie| Vermittlungsschicht (Schicht 4) | Anwendungsschicht (Schicht 7) |	DNS-Ebene |
| Unterstützte Anwendungsprotokolle |	Beliebig | HTTP und HTTPS | 	Alle (Ein HTTP/S-Endpunkt ist für die Endpunktüberwachung erforderlich.) |
| Endpunkte | Azure-VMs und Cloud Services-Rolleninstanzen | Alle internen Azure-IP-Adressen oder öffentlichen Internet-IP-Adressen | Azure-VMs, Cloud Services, Azure-Web-Apps und externe Endpunkte |
| VNet-Unterstützung | Kann sowohl für Anwendungen mit Internetzugriff als auch für interne (VNet-) Anwendungen verwendet werden | Kann sowohl für Anwendungen mit Internetzugriff als auch für interne (VNet-) Anwendungen verwendet werden |	Unterstützt nur Anwendungen mit Internetzugriff |
Endpunktüberwachung | Unterstützt über Tests | Unterstützt über Tests | Unterstützt über HTTP/HTTPS-GET | 
<BR> Azure Load Balancer und Application Gateway leiten Netzwerkdatenverkehr an Endpunkte weiter, werden jedoch im Hinblick auf den verarbeiteten Datenverkehr in verschiedenen Verwendungsszenarien eingesetzt. In der folgenden Tabelle wird der Unterschied zwischen den beiden Lastenausgleichsmodulen verdeutlicht:


| Typ | Azure-Lastenausgleich | Application Gateway |
|---|---|---|
| Protokolle | UDP/TCP | HTTP/HTTPS |
| IP-Reservierung | Unterstützt | Nicht unterstützt | 
| Lastenausgleichsmodus | 5 Tupel (Quell-IP, Quellport, Ziel-IP-Adresse, Zielport, Protokolltyp) | CookieBasedAffinity = false, rules = basic (Roundrobin) | 
| Lastenausgleichsmodus (Quell-IP/persistente Sitzungen) | 2 Tupel (Quell-IP und Ziel-IP), 3 Tupel (Quell-IP, Ziel-IP und Port). Kann je nach Anzahl der virtuellen Computer zentral hoch- oder herunterskaliert werden. | CookieBasedAffinity = true, rules = basic (Roundrobin) für neue Verbindungen. |
| Integritätstests | Standard: Testintervall von 15 Sekunden. Aus der Rotation entfernt: 2 aufeinanderfolgende Fehler. Unterstützt benutzerdefinierte Tests. | Testleerlaufintervall von 30 Sekunden. Entfernt nach fünf aufeinanderfolgenden Live-Datenverkehrsfehlern oder einem einzelnen Testfehler im Leerlaufmodus. Unterstützt benutzerdefinierte Tests. | 
| SSL-Abladung | Nicht unterstützt | Unterstützt | 


  

<!---HONumber=AcomDC_0323_2016-->