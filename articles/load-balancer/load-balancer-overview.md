<properties
   pageTitle="Übersicht über Azure-Lastenausgleich | Microsoft Azure"
   description="Übersicht über Features, Architektur und Implementierung des Azure Load Balancers. Dieser Artikel dient dem Verständnis der Arbeitsweise und der Nutzung des Load Balancers in der Cloud."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="joaoma" />


# Übersicht über Azure Load Balancer

Der Azure Load Balancer bietet hohe Verfügbarkeit und Netzwerkleistung für Ihre Anwendungen. Es handelt sich um einen Lastenausgleich des Layer-4-Typs (TCP, UDP), der eingehenden Datenverkehr zwischen funktionierenden Dienstinstanzen in Clouddiensten oder auf virtuelle Computer verteilt, die in einem Lastenausgleich definiert wurden.

Es gibt folgende Konfigurationsmöglichkeiten:

- Lastenausgleich des eingehenden Internetdatenverkehrs für virtuelle Computer. Wir bezeichnen dies als [Lastenausgleich für Internetzugriff](load-balancer-internet-overview.md).
- Lastenausgleich für Datenverkehr zwischen virtuellen Computern in einem virtuellen Netzwerk, zwischen virtuellen Computern in Clouddiensten oder zwischen lokalen und virtuellen Computern in einem standortübergreifenden virtuellen Netzwerk. Wir bezeichnen dies als [internen Lastenausgleich](load-balancer-internal-overview.md).
- Weiterleiten von externem Datenverkehr an eine bestimmte Instanz eines virtuellen Computers.

## Load Balancer in den beiden Bereitstellungsmodellen

Alle Ressourcen in der Cloud benötigen eine öffentliche IP-Adresse, damit sie im Internet erreichbar sind. Die Cloudinfrastruktur in Microsoft Azure verwendet nicht routbare IP-Adressen innerhalb ihrer Ressourcen. Für die Kommunikation mit dem Internet nutzt sie die Netzwerkadressübersetzung (NAT) mit öffentlichen IP-Adressen.

### Klassisches Azure

Im klassischen Azure-Bereitstellungsmodell werden einem Clouddienst eine öffentliche IP-Adresse und ein FQDN zugewiesen. In einem Clouddienst bereitgestellte virtuelle Computer können gruppiert werden, um einen Lastenausgleich zu verwenden. Der Azure Load Balancer übernimmt die Portübersetzung und den Lastenausgleich im Netzwerkdatenverkehr, wobei die öffentliche IP-Adresse für den Clouddienst genutzt wird.

Im klassischen Bereitstellungsmodell erfolgt die Portübersetzung über Endpunkte, die eine 1:1-Beziehung zwischen dem öffentlichen zugewiesenen Port der öffentlichen IP-Adresse und dem lokalen Port bilden, der zum Senden von Datenverkehr an einen bestimmten virtuellen Computer zugewiesen wurde.

Der Lastenausgleich erfolgt über vom Load Balancer festgelegte Endpunkte. Diese Endpunkte haben eine 1:n-Beziehung zwischen der öffentlichen IP-Adresse und den lokalen Ports, die allen virtuellen Computer in der Gruppe zugewiesen sind, die auf den Netzwerkdatenverkehr mit Lastenausgleich reagiert.

Die Domänenbezeichnung für die öffentliche IP-Adresse, die ein Load Balancer in diesem Bereitstellungsmodell verwenden würde, ist `<cloud service name>.cloudapp.net`.

Dies ist eine grafische Darstellung des Load Balancers in einem klassischen Bereitstellungsmodell:

![Load Balancer im klassischen Bereitstellungsmodell](./media/load-balancer-overview/asm-lb.png)

### Azure-Ressourcen-Manager

Das Konzept des Load Balancers ändert sich für das Azure-Ressourcen-Manager-Bereitstellungsmodell, da für einen Clouddienst keine Notwendigkeit besteht, einen Lastenausgleich zu erstellen.

Im Ressourcen-Manager ist eine öffentliche IP-Adresse eine eigene Ressource und kann einer Domänenbezeichnung oder einem DNS-Namen zugeordnet werden. Die öffentliche IP-Adresse ist in diesem Fall der Load Balancer-Ressource zugeordnet. Auf diese Weise verwenden Load Balancer-Regeln und eingehende NAT-Regeln die öffentliche IP-Adresse als Internetendpunkt für die Ressourcen, die Netzwerkverkehr mit Lastenausgleich empfangen.

Eine Netzwerkschnittstellen-Ressource enthält die IP-Adresskonfiguration (private oder öffentliche IP) für einen virtuellen Computer. Nachdem eine NIC einem IP-Adresspool des Load Balancer-Back-Ends hinzugefügt wurde, beginnt der Load Balancer basierend auf den erstellten Lastenausgleichsregeln mit dem Senden des Netzwerkdatenverkehrs mit Lastenausgleich.

Dies ist eine grafische Darstellung des Load Balancers im Ressourcen-Manager:

![Load Balancer im Ressourcen-Manager](./media/load-balancer-overview/arm-lb.png)

## Lastenausgleichsfunktionen

### Hash-basierte Verteilung

Der Azure Load Balancer verwendet einen Verteilungsalgorithmus auf Hashbasis. Standardmäßig wird ein 5-Tupel-Hash (Quell-IP, Quellport, IP-Zieladresse, Zielport Protokolltyp) zum Zuordnen des Datenverkehrs an verfügbare Server verwendet. Dabei wird Bindung nur in einer Transportsitzung angeboten. Pakete in derselben TCP- oder UDP-Sitzung werden an die gleiche Instanz der Datencenter-IP (DIP) hinter dem Lastenausgleichs-Endpunkt geleitet. Wenn der Client die Verbindung schließt und erneut öffnet oder eine neue Sitzung über die gleiche Quell-IP startet, wird der Quellport geändert. Dadurch kann der Datenverkehr an einen anderen DIP-Endpunkt geleitet werden.


Weitere Informationen finden Sie unter [Load Balancer-Verteilungsmodus](load-balancer-distribution-mode.md)

Die folgende Grafik veranschaulicht eine Hash-basierte Verteilung:

![Hash-basierte Verteilung](./media/load-balancer-overview/load-balancer-distribution.png)

### Portweiterleitung

Mit dem Azure Load Balancer steuern Sie, wie die eingehende Kommunikation verwaltet wird. Diese Kommunikation kann Datenverkehr umfassen, der von Internethosts oder virtuellen Computern in anderen Clouddiensten oder virtuellen Netzwerken initialisiert wird. Diese Steuerung wird durch einen Endpunkt (auch als Eingabeendpunkt bezeichnet) abgebildet.

Ein Endpunkt mit einem öffentlichen Port überwacht Datenverkehr und leitet diesen an einen internen Port. Sie können dieselben Ports einem internen oder externen Endpunkt zuordnen oder einen anderen Port verwenden. Beispiel: Sie können einen Webserver konfigurieren, der an Port 81 lauscht, während Port 80 dem öffentlichen Endpunkt zugeordnet ist. Die Erstellung eines öffentlichen Endpunkts löst die Erstellung einer Azure Load Balancer-Instanz aus.

Die Standardnutzung und -konfiguration von Endpunkten auf einem virtuellen Computer, der über das Azure-Portal erstellt wurde, sind auf das RDP- (Remotedesktopprotokoll) und Windows PowerShell-Sitzungsdatenverkehr ausgerichtet. Über diese Endpunkte können Sie den virtuellen Computer remote über das Internet verwalten.


### Automatische Neukonfiguration

Der Azure Load Balancer konfiguriert sich selbst sofort, wenn Sie Instanzen nach oben oder unten skalieren. Diese Neukonfiguration kann z. B. vorkommen, wenn Sie die Anzahl der Instanzen für die Web-/Workerrolle erhöhen oder wenn Sie weitere virtuelle Computer in die gleiche Gruppe mit Lastenausgleich aufnehmen.


### Dienstüberwachung
Der Azure Load Balancer bietet die Möglichkeit, die Integrität der verschiedenen Serverinstanzen zu testen. Wenn ein Test nicht reagiert, beendet der Load Balancer das Senden neuer Verbindungen an die fehlerhaften Instanzen. Vorhandene Verbindungen sind nicht betroffen.

Drei Testarten werden unterstützt:

- Gast-Agent-Test (nur PaaS-VMs). Der Azure Load Balancer nutzt den Gast-Agent auf dem virtuellen Computer. Er lauscht und antwortet nur dann mit dem HTTP-OK-Code 200, wenn die Instanz sich im Zustand „Bereit“ befindet (d. h. nicht in einem Zustand wie „Beschäftigt“, „Wiederverwendung“ oder „Wird beendet“). Wenn der Gast-Agent nicht mit dem HTTP-OK-Code 200 antwortet, kennzeichnet der Azure Load Balancer die Instanz als nicht reagierend und sendet keinen weiteren Datenverkehr an diese Instanz. Der Load Balancer pingt die Instanz weiterhin. Wenn der Gast-Agent mit dem HTTP-Code 200 antwortet, sendet der Load Balancer wieder Datenverkehr an diese Instanz. Wenn Sie eine Webrolle verwenden, wird Ihr Websitecode in der Regel in „w3wp.exe“ ausgeführt. Dieses Programm wird nicht von der Azure-Fabric oder vom Gast-Agent überwacht. Das bedeutet, dass Fehler in „w3wp.exe“ (z. B. HTTP 500-Antworten) nicht an den Gast-Agent gemeldet werden und der Load Balancer nicht erkennen kann, dass diese Instanz aus der Rotation entfernt werden soll.

- Benutzerdefinierter HTTP-Test. Der benutzerdefinierte Load Balancer-Test setzt den (Gast-Agent-) Standardtest außer Kraft. Sie können damit Ihre eigene Logik zum Ermitteln der Integrität der Rolleninstanz erstellen. Der Load Balancer testet regelmäßig Ihren Endpunkt (standardmäßig alle 15 Sekunden). Die Instanz wird als rotierend betrachtet, wenn sie innerhalb des Zeitlimits (standardmäßig 31 Sekunden) mit einem TCP-ACK oder HTTP-Code 200 reagiert. Dies kann für die Implementierung Ihrer eigenen Logik zum Entfernen von Instanzen aus der Load Balancer-Rotation nützlich sein. Beispielsweise können Sie die Instanz so konfigurieren, dass sie einen anderen Status als 200 zurückgibt, wenn die Instanz bei über 90 % CPU-Auslastung liegt. Für Webrollen, die „w3wp.exe“ verwenden, wird die Website außerdem automatisch überwacht, da Fehler im Websitecode einen anderen Status als 200 an den Load Balancer-Test zurückgeben.

- Benutzerdefinierter TCP-Test. TCP-Tests basieren auf der erfolgreichen Herstellung von TCP-Sitzungen an einem definierten Testport.

Weitere Informationen finden Sie unter [Load Balancer-Integritätstest](https://msdn.microsoft.com/library/azure/jj151530.aspx).

### Quell-NAT


Auf den gesamten von Ihrem Dienst ausgehenden Internetdatenverkehr wird Quell-NAT (SNAT, Source Network Address Translation) mit der gleichen VIP-Adresse wie für eingehenden Datenverkehr angewendet. SNAT bietet wichtige Vorteile:

- Sie ermöglicht einfache Upgrades und Notfallwiederherstellung von Diensten, da die VIP-Adresse dynamisch einer anderen Instanz des Diensts zugeordnet werden kann.

- Sie vereinfacht die ACL-Verwaltung, da die ACL im Hinblick auf VIPs ausgedrückt werden kann. Daher müssen Sie keine Änderungen vornehmen, wenn Sie Dienste zentral hoch- oder herunterskalieren oder erneut bereitstellen.

Die Azure Load Balancer-Konfiguration unterstützt vollständige Cone-NAT für UDP. Vollständige Cone-NAT ist ein NAT-Typ, bei dem der Port eingehende Verbindungen von jedem externen Host (als Reaktion auf eine externe Anforderung) erlaubt.


>[AZURE.NOTE] Für jede neue ausgehende Verbindung, die von einem virtuellen Computer initiiert wird, wird auch ein ausgehender Port vom Azure Load Balancer zugeordnet. Dem externen Host wird eingehender Datenverkehr als einer virtuellen IP (VIP) zugewiesener Port angezeigt. Wenn für Ihre Szenarien eine große Anzahl von ausgehenden Verbindungen benötigt wird, empfehlen wir, dass die VMs öffentliche IP-Adressen auf Instanzebene verwenden, damit sie eine dedizierte ausgehende IP-Adresse für SNAT aufweisen. Dies reduziert das Risiko von Portauslastung.
>
>Die maximale Anzahl von Ports, die für VIPs oder öffentliche IPs auf Instanzebene (PIPs) verwendet werden können, beträgt 64.000. Dies ist eine standardmäßige Einschränkung für TCP.


**Unterstützung für mehrere IP-Adressen mit Lastenausgleich für virtuelle Computer**

Sie können einer Gruppe von virtuellen Computern mehr als eine öffentliche IP-Adresse mit Lastenausgleich zuweisen. Mit dieser Funktion können Sie mehrere SSL-Websites und/oder mehrere Listener für SQL Server-AlwaysOn-Verfügbarkeitsgruppen in der gleichen Gruppe virtueller Computer hosten. Weitere Informationen finden Sie unter [Mehrere VIPs pro Clouddienst](load-balancer-multivip.md).

**Vorlagenbasierte Bereitstellungen über den Azure-Ressourcen-Manager**

Der Azure-Ressourcen-Manager ist das neue Verwaltungsframework für Dienste in Azure. Der Azure Load Balancer kann jetzt mithilfe von APIs und Tools im Ressourcen-Manager verwaltet werden. Weitere Informationen zum Ressourcen-Manager finden Sie unter [IaaS ist jetzt noch einfacher mit Azure-Ressourcen-Manager](https://azure.microsoft.com/blog/2015/04/29/iaas-just-got-easier-again/).

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## Nächste Schritte

[Lastenausgleich für Internetzugriff (Übersicht)](load-balancer-internet-overview.md)

[Interner Lastenausgleich (Übersicht)](load-balancer-internal-overview.md)

[Erste Schritte zum Erstellen eines Lastenausgleichs für den Internetzugriff](load-balancer-get-started-internet-arm-ps.md)

<!---HONumber=AcomDC_0323_2016-->