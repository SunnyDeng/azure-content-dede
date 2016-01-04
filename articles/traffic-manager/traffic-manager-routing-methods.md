<properties 
   pageTitle="Traffic Manager – Routingmethoden für Datenverkehr | Microsoft Azure"
   description="Diese Artikel erläutern Ihnen die verschiedenen Routingmethode für Datenverkehr, die von Traffic Manager verwendet werden"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# Traffic Manager-Routingmethoden

Drei Routingmethoden für Datenverkehr sind in Traffic Manager verfügbar. Jedes Traffic Manager-Profil kann immer nur eine Routingmethode für Datenverkehr verwenden. Sie können jedoch jederzeit eine andere Routingmethode für Ihr Profil auswählen.

Beachten Sie unbedingt, dass alle Routingmethoden für Datenverkehr eine Endpunktüberwachung vorsehen. Nachdem Sie bei der Konfiguration des Traffic Manager-Profils die Routingmethode für Datenverkehr angegeben haben, die Ihre Anforderungen am besten erfüllt, konfigurieren Sie die Überwachungseinstellungen. Wenn die Überwachung ordnungsgemäß konfiguriert ist, überwacht Traffic Manager den Status Ihrer Endpunkte, die aus Cloud-Diensten und Websites bestehen, und sendet keinen Datenverkehr an Endpunkte, die wahrscheinlich nicht verfügbar sind. Informationen zur Traffic Manager-Überwachung finden Sie unter [Traffic Manager-Überwachung](traffic-manager-monitoring.md).

Die drei folgenden Traffic Manager-Routingmethoden für Datenverkehr stehen zur Verfügung:

- **Failover:** Wählen Sie „Failover“ aus, wenn sich Endpunkte in demselben oder in verschiedenen Azure-Rechenzentren (im klassischen Azure-Portal als „Regionen“ bezeichnet) befinden und Sie einen primären Endpunkt für den gesamten Datenverkehr verwenden, jedoch Sicherungen für den Fall bereitstellen möchten, dass die primären oder Sicherungsendpunkte nicht verfügbar sind. Weitere Informationen finden Sie unter [Routingmethode für Failoverdatenverkehr](#failover-traffic-routing-method).

- **Round Robin**: Wählen Sie "Roundrobin" aus, wenn Sie die Last auf eine Reihe von Endpunkten in demselben oder in verschiedenen Datencentern verteilen möchten. Weitere Informationen finden Sie unter [Routingmethode für Roundrobin-Datenverkehr](#round-robin-traffic-routing-method).

- **Leistung**: Wählen Sie Leistung aus, wenn sich die Endpunkte an unterschiedlichen geografischen Standorten befinden und anfordernde Clients den "nächstgelegenen" Endpunkt (im Hinblick auf die geringste Wartezeit) verwenden sollen. Weitere Informationen finden Sie unter [Routingmethode für Leistungsdatenverkehr](#performance-traffic-routing-method).

Beachten Sie, dass Azure Websites bereits Funktionen für Failover- und Roundrobin-Datenverkehrs-Routingmethoden für Websites in einem Datencenter unabhängig vom Websitemodus zur Verfügung stellen. Traffic Manager ermöglicht das Angeben von Failover- und Roundrobin-Datenverkehrsrouting für Websites in verschiedenen Datencentern.

>[AZURE.NOTE]Die DNS-Gültigkeitsdauer (TTL) informiert DNS-Clients und -Resolver auf DNS-Servern, wie lange die aufgelösten Namen zwischengespeichert werden sollen. Clients verwenden weiterhin einen bestimmten Endpunkt beim Auflösen des Domänennamens, bis der lokale DNS-Cacheeintrag für den Namen abläuft.

## Routingmethode für Failoverdatenverkehr

Organisationen möchten häufig sicherstellen, dass ihre Dienste zuverlässig funktionieren. Dazu stellen sie Sicherungsprogramme für den Fall bereit, dass der primäre Dienst ausfällt. Der Failover-Lastenausgleich für Dienste wird häufig implementiert, indem eine Reihe identischer Endpunkte bereitgestellt und Datenverkehr an einen primären Dienst gesendet wird. Dabei wird eine Liste mit mindestens einer Sicherung verwendet. Wenn der primäre Dienst nicht verfügbar ist, werden anfordernde Clients an den nächsten Dienst in der Reihenfolge verwiesen. Wenn der erste und der zweite aufgeführte Dienst nicht verfügbar sind, wird der Datenverkehr an den dritten Dienst gesendet usw.

Wenn Sie die Routingmethode für Failoverdatenverkehr konfigurieren, ist die Reihenfolge der ausgewählten Endpunkte von Bedeutung. Mithilfe des klassischen Azure-Portals können Sie die Failoverreihenfolge auf der Seite „Konfiguration“ für das Profil konfigurieren.

Abbildung 1 zeigt ein Beispiel für die Routingmethode für Failoverdatenverkehr für eine Reihe von Endpunkten.

![Traffic Manager-Routingmethode für Failover](./media/traffic-manager-routing-methods/IC750592.jpg)

**Abbildung 1**

Die folgenden nummerierten Schritte entsprechen den Ziffern in Abbildung 1.

1. Traffic Manager empfängt eine eingehende Anforderung von einem Client über DNS und sucht nach dem Profil.
2. Das Profil enthält eine sortierte Liste mit Endpunkten. Traffic Manager überprüft, welcher Endpunkt der erste in der Liste ist. Wenn der Endpunkt (basierend auf der aktuellen Endpunktüberwachung) online ist, wird der DNS-Name dieses Endpunkts in der DNS-Antwort an den Client angegeben. Wenn der Endpunkt offline ist, ermittelt Traffic Manager den nächsten Onlineendpunkt in der Liste. In diesem Beispiel ist CS-A offline (nicht verfügbar), CS-B ist jedoch online (verfügbar).
3. Traffic Manager gibt den Domänennamen von CS-B an den DNS-Server des Clients zurück, der den Domänennamen in eine IP-Adresse auflöst und diese an den Client sendet.
4. Der Client initiiert Datenverkehr an CS-B.

## Routingmethode für Round-Robin-Datenverkehr

Ein gängiges Muster für das Datenverkehrsrouting besteht darin, eine Reihe identischer Endpunkte bereitzustellen und Datenverkehr im Roundrobin-Verfahren an die einzelnen Endpunkte zu senden. Die Methode "Roundrobin" teilt den Datenverkehr auf verschiedene Endpunkte auf. Dabei wird ein fehlerfreier Endpunkt nach dem Zufallsprinzip ausgewählt und kein Datenverkehr an Dienste gesendet, die ausgefallen sind. Weitere Informationen finden Sie unter [Traffic Manager-Überwachung](../traffic-manager-monitoring.md).

Abbildung 2 zeigt ein Beispiel für die Routingmethode für Roundrobin-Datenverkehr für eine Reihe von Endpunkten.

![Traffic Manager-Routingmethode für Roundrobin](./media/traffic-manager-routing-methods/IC750593.jpg)

**Abbildung 2**

Die folgenden nummerierten Schritte entsprechen den Ziffern in Abbildung 2.

1. Traffic Manager empfängt eine eingehende Anforderung von einem Client und sucht nach dem Profil.
2. Das Profil enthält eine Liste von Endpunkten. Traffic Manager wählt einen zufälligen Endpunkt aus dieser Liste aus. Dabei werden alle Offlineendpunkte (nicht verfügbare Endpunkte), die von der Traffic Manager-Endpunktüberwachung ermittelt wurden, ausgeschlossen. In diesem Beispiel ist dies der Endpunkt CS-B.
3. Traffic Manager gibt den Domänennamen von CS-B an den DNS-Server des Clients zurück. Der DNS-Server des Clients löst diesen Domänennamen in eine IP-Adresse auf und sendet diese an den Client.
4. Der Client initiiert Datenverkehr an CS-B.

Das Routing von Roundrobin-Datenverkehr unterstützt auch die gewichtete Verteilung des Netzwerkdatenverkehrs. Abbildung 3 zeigt ein Beispiel für die gewichtete Routingmethode für Roundrobin-Datenverkehr für eine Reihe von Endpunkten.

![Gewichtete Routingmethode für Roundrobin](./media/traffic-manager-routing-methods/IC750594.png)

**Abbildung 3**

Gewichtetes Routing von Roundrobin-Datenverkehr ermöglicht das Verteilen der Last auf verschiedene Endpunkte basierend auf einem zugewiesenen "Gewichtungswert" jedes Endpunkts. Je höher die Gewichtung ist, desto häufiger wird ein Endpunkt zurückgegeben. In den folgenden Szenarien kann diese Methode sinnvoll sein:

- Allmähliches Anwendungsupgrade: Weisen Sie einen Prozentwert des Datenverkehrs zu, der an einen neuen Endpunkt weitergeleitet wird, und erhöhen Sie den Datenverkehr dann im Lauf der Zeit allmählich auf 100 %.
- Anwendungsmigration zu Azure: Erstellen Sie ein Profil mit Azure- und externen Endpunkten, und geben Sie die Gewichtung des Datenverkehrs an, der an jeden Endpunkt weitergeleitet wird.
- Erweiterung in die Cloud für zusätzliche Kapazität: Erweitern Sie eine lokale Bereitstellung schnell in die Cloud, indem Sie diese hinter einem Traffic Manager-Profil zur Verfügung stellen. Wenn Sie zusätzliche Kapazität in der Cloud benötigen, können Sie weitere Endpunkte hinzufügen oder aktivieren. Geben Sie dann an, welcher Teil des Datenverkehrs an jeden Endpunkt gesendet wird.

Zurzeit kann das klassische Azure-Portal nicht zum Konfigurieren von gewichtetem Datenverkehrsrouting verwendet werden. Azure stellt programmgesteuerten Zugriff auf diese Methode mithilfe der zugehörigen Dienstverwaltungs-REST-API und der Azure PowerShell-Cmdlets zur Verfügung.

Informationen zur Verwendung der REST-APIs finden Sie unter [Vorgänge für Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/p/?LinkId=313584).

Informationen zur Verwendung der Azure PowerShell-Cmdlets finden Sie unter [Azure Traffic Manager-Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=400769). Eine Beispielkonfiguration finden Sie unter [Azure Traffic Manager External Endpoints and Weighted Round Robin via PowerShell](http://azure.microsoft.com/blog/2014/06/26/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/) in englischer Sprache im Azure-Blog.

Wenn Sie das Profil von einem einzelnen Client aus testen und das gleichmäßige oder gewichtete Roundrobin-Verhalten beobachten möchten, überprüfen Sie, ob der DNS-Name in die verschiedenen IP-Adressen für die Endpunkte gemäß den gleichmäßigen oder gewichteten Werten im Profil aufgelöst wird. Beim Testen müssen Sie clientseitiges DNS-Caching deaktivieren oder den DNS-Cache zwischen den einzelnen Versuchen löschen, damit sichergestellt ist, dass eine neue Abfrage des DNS-Namens gesendet wird.

## Routingmethode für Leistungsdatenverkehr

Wenn Sie das Routing von Datenverkehr für Endpunkte in verschiedenen Datencentern weltweit einrichten möchten, können Sie eingehenden Datenverkehr an den nächstgelegenen Endpunkt (den Endpunkt mit der kürzesten Wartezeit zwischen dem anfordernden Client und dem Endpunkt) weiterleiten. Normalerweise entspricht der "nächstgelegene" Endpunkt direkt der kürzesten geografischen Distanz. Die Routingmethode für Leistungsdatenverkehr ermöglicht Ihnen eine Verteilung basierend auf Standort und Latenz, kann jedoch nicht die Echtzeitänderungen in der Netzwerkkonfiguration bzw. Last berücksichtigen.

Die Routingmethode für Leistungsdatenverkehr sucht den anfordernden Client und leitet ihn zum nächstgelegenen Endpunkt weiter. Die "Nähe" wird durch eine Internetlatenztabelle ermittelt, welche die Roundtripzeit zwischen verschiedenen IP-Adressen und den einzelnen Azure-Datencentern anzeigt. Diese Tabelle wird in regelmäßigen Abständen aktualisiert und spiegelt nicht die Leistung über das Internet in Echtzeit wider. Diese Methode berücksichtigt nicht die Arbeitsauslastung eines bestimmten Diensts. Traffic Manager überwacht jedoch Ihre Endpunkte auf der Grundlage der ausgewählten Methode und schließt diese nicht in die DNS-Abfrageantworten ein, wenn sie nicht verfügbar sind. Anders ausgedrückt, umfasst das Routing von Leistungsdatenverkehr also auch die Routingmethode für Failoverdatenverkehr.

Abbildung 4 zeigt ein Beispiel für die Routingmethode für Leistungsdatenverkehr für eine Reihe von Endpunkten.

![Traffic Manager-Routingmethode für Leistung](./media/traffic-manager-routing-methods/IC753237.jpg)

**Abbildung 4**

Die folgenden nummerierten Schritte entsprechen den Ziffern in Abbildung 4.

1. Traffic Manager erstellt die Internetlatenztabelle in regelmäßigen Abständen. Die Traffic Manager-Infrastruktur führt Tests aus, um die Roundtripzeiten zwischen verschiedenen Punkten weltweit und den Azure-Datencentern zu bestimmen, in denen Endpunkte gehostet werden.
2. Traffic Manager empfängt eine eingehende Anforderung von einem Client über den lokalen DNS-Server und sucht nach dem Profil.
3. Traffic Manager ermittelt die Zeile in der Internetlatenztabelle für die IP-Adresse der eingehenden DNS-Anforderung. Da der lokale DNS-Server des Benutzers eine iterative DNS-Abfrage ausführt, um den autorisierenden DNS-Server für den Traffic Manager-Profilnamen zu ermitteln, wird die DNS-Abfrage von der IP-Adresse des lokalen DNS-Servers des Clients gesendet.
4. Traffic Manager ermittelt das Datencenter mit der kürzesten Zeitangabe für die Datencenter, die die im Profil definierten Endpunkte hosten. In diesem Beispiel ist dies CS-B.
5. Traffic Manager gibt den Domänennamen von CS-B an den lokalen DNS-Server des Clients zurück, der den Domänennamen in eine IP-Adresse auflöst und diese an den Client sendet.
6. Der Client initiiert Datenverkehr an CS-B.

**Beachten Sie Folgendes:**

- Wenn das Profil mehrere Endpunkte im selben Datencenter enthält, wird der an dieses Datencenter gerichtete Datenverkehr gleichmäßig auf die verfügbaren Endpunkte verteilt, die gemäß der Endpunktüberwachung verfügbar und fehlerfrei sind.
- Wenn alle Endpunkte in einem bestimmten Datencenter gemäß der Endpunktüberwachung nicht verfügbar sind, wird der an diese Endpunkte gerichtete Datenverkehr auf alle verfügbaren Endpunkte verteilt, die im Profil angegeben sind, und nicht auf den oder die nächstgelegenen Endpunkt(e). Auf diese Weise können Sie kaskadierende Fehler vermeiden, die auftreten können, wenn der nächstgelegene Endpunkt überlastet ist.
- Wenn die Internetlatenztabelle aktualisiert wird, stellen Sie ggf. einen Unterschied bei den Mustern und der Auslastung für den Datenverkehr in Ihren Endpunkten fest. Diese Änderungen sollten nur geringfügiger Natur sein.
- Wenn die Routingmethode für Leistungsdatenverkehr mit externen Endpunkten verwendet wird, müssen Sie den Speicherort dieser Endpunkte angeben. Wählen Sie die Azure-Region aus, die Ihrer Bereitstellung am nächsten ist. Weitere Informationen finden Sie unter [Verwalten von Endpunkten in Traffic Manager](traffic-manager-endpoints.md).

## Traffic Manager-Abbildungen

Wenn Sie die Abbildungen in diesem Thema als PowerPoint-Folien in Ihrer eigenen Präsentation zu Traffic Manager verwenden oder für Ihre eigenen Zwecke ändern möchten, lesen Sie [Traffic Manager figures in MSDN documentation](http://gallery.technet.microsoft.com/Traffic-Manager-figures-in-887e7c99) (in englischer Sprache).

## Nächste Schritte

[Traffic Manager-Überwachung](traffic-manager-monitoring.md)

[Erstellen eines Profils](traffic-manager-manage-profiles.md)

[Hinzufügen eines Endpunkts](traffic-manager-endpoints.md)
 

<!---HONumber=AcomDC_1210_2015-->