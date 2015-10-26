<properties
   pageTitle="Leistungsüberlegungen zu Azure Traffic Manager | Microsoft Azure"
   description="Grundlegendes zur Leistung von Traffic Manager und Testen der Leistung auf Websites bei Verwendung von Traffic Manager"
   services="traffic-manager"
   documentationCenter=""
   authors="kwill-MSFT"
   manager="adinah"
   editor="joaoma" />

<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="joaoma" />


# Leistungsüberlegungen zu Traffic Manager


Im Hinblick auf Azure Traffic Manager stellt sich häufig die Frage der potenziellen Leistungsprobleme, zu denen dieser Dienst führen kann. In diesem Zusammenhang treten normalerweise folgende Fragen auf, z. B. "Welche Latenz ergibt sich durch Traffic Manager für meine Website?", "Meine Website war gestern einige Stunden lang langsam – gab es zu diesem Zeitpunkt Probleme mit Traffic Manager?", "Wo befinden sich die Traffic Manager-Server? Ich möchte sichergehen, dass sie sich im gleichen Rechenzentrum wie meine Website befinden, sodass die Leistung nicht beeinträchtigt wird".

Auf dieser Seite werden die direkten Leistungsbeeinträchtigungen erörtert, die sich durch Traffic Manager für Websites ergeben können. Wenn Sie über eine Website im Osten der USA und über eine Website in Asien verfügen und die Traffic Manager-Tests für die Website im Osten der USA fehlschlagen, werden alle Benutzer an die Website in Asien weitergeleitet. Dabei ergeben sich Leistungseinbußen, die aber nicht auf Traffic Manager selbst zurückzuführen sind.

  

## Wichtiger Hinweis zur Funktionsweise von Traffic Manager

[Traffic Manager – Übersicht](traffic-manager-overview.md) ist eine hervorragende Ressource, um Informationen zur Funktionsweise von Traffic Manager zu erhalten. Die Informationen auf dieser Seite sind jedoch sehr umfangreich, und die Suche nach den Schlüsselinformationen im Hinblick auf die Leistung kann sich als schwierig erweisen. Die wichtigen Punkte in der MSDN-Dokumentation sind Schritt 5 und Schritt 6 zu Abbildung 3, die ich hier ausführlicher erläutern werde:

- Traffic Manager dient im Wesentlichen nur einem Zweck – der DNS-Auflösung. Dies bedeutet, dass sich die einzige Leistungsbeeinträchtigung, die Traffic Manager für Websites haben kann, bei der ersten DNS-Suche ergibt.
- Klarstellung zur DNS-Suche durch Traffic Manager. Traffic Manager füllt und aktualisiert regelmäßig die regulären Microsoft DNS-Stammserver auf Grundlage Ihrer Richtlinie und der Testergebnisse. So erfolgt auch die erste DNS-Suche ohne Beteiligung von Traffic Manager, da die DNS-Anforderung von den regulären Microsoft DNS-Stammservern verarbeitet wird. Wenn Traffic Manager "ausfällt" (d. h. ein Fehler in den virtuellen Computern auftritt, die die Richtlinientests und die DNS-Aktualisierung durchführen), hat dies keine Auswirkungen auf den Traffic Manager-DNS-Namen, da die Einträge in den Microsoft DNS-Servern weiterhin beibehalten werden. Als einzige Auswirkungen werden die Tests und Aktualisierungen auf Grundlage der Richtlinie nicht durchgeführt (d. h., wenn der primäre Standort ausfällt, kann Traffic Manager das DNS nicht so aktualisieren, dass es auf den Failoverstandort verweist).
- Der Datenverkehr fließt NICHT durch Traffic Manager. Es gibt keine Traffic Manager-Server, die als Zwischeninstanz zwischen den Clients und dem Azure-gehosteten Dienst fungieren. Nach Beendigung der DNS-Suche wird Traffic Manager vollständig aus der Kommunikation zwischen Client und Server entfernt.
- Die DNS-Suche erfolgt sehr schnell und wird zwischengespeichert. Die erste DNS-Suche hängt von dem Client und den konfigurierten DNS-Servern ab. In der Regel kann ein Client eine DNS-Suche in ca. 50 ms durchführen (siehe http://www.solvedns.com/dns-comparison/). Nach Abschluss der ersten Suche werden die Ergebnisse für die Gültigkeitsdauer des DNS zwischengespeichert. Für Traffic Manager gilt der Standardwert 300 Sekunden.
- Die Traffic Manager-Richtlinie, die Sie auswählen (Leistung, Failover, Roundrobin) hat keinen Einfluss auf die DNS-Leistung. Die Leistungsrichtlinie kann sich negativ auf das Nutzererlebnis auswirken, wenn Sie beispielsweise Benutzer in den USA an einen in Asien gehosteten Dienst weiterleiten. Diese Leistungsbeeinträchtigung wird aber nicht von Traffic Manager verursacht.

  

## Testen der Leistung von Traffic Manager

Es gibt einige öffentlich verfügbare Websites, die Sie verwenden können, um die Leistung und das Verhalten von Traffic Manager zu ermitteln. Diese Websites sind nützlich, um die DNS-Latenz und die gehosteten Dienste zu ermitteln, an die Ihre Benutzer weltweit weitergeleitet werden. Beachten Sie dabei, dass die meisten dieser Tools die DNS-Ergebnisse nicht zwischenspeichern. Dadurch wird beim mehrmaligen Ausführen der Tests die vollständige DNS-Suche angezeigt, während die Clients, die eine Verbindung mit Ihrem Traffic Manager-Endpunkt herstellen, die Leistungsbeeinträchtigung der vollständigen DNS-Suche nur einmal während der Gültigkeitsdauer sehen.


## Beispieltools zum Messen der Leistung


Eines der einfachsten Tools ist WebSitePulse. Wenn Sie die URL eingeben, werden Statistiken wie z. B. die DNS-Auflösungszeit, erstes Byte, letztes Byte und andere Leistungsstatistiken angezeigt. Sie haben die Wahl zwischen drei verschiedenen Standorten zum Testen Ihrer Website. In diesem Beispiel können Sie sehen, dass die erste DNS-Suche bei der ersten Ausführung des Tests 0,204 Sekunden dauert. Wenn dieser Test das zweite Mal auf dem gleichen Traffic Manager-Endpunkt ausgeführt wird, dauert die DNS-Suche 0,002 Sekunden, da die Ergebnisse bereits zwischengespeichert sind.

http://www.websitepulse.com/help/tools.php


![Impuls1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

DNS-Auflösungszeit bei Zwischenspeicherung:


![Impuls2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)



Ein anderes ausgesprochen nützliches Tool zum Abrufen der DNS-Auflösungszeit aus mehreren geografischen Regionen gleichzeitig ist das Tool Check Website von Watchmouse. Wenn Sie die URL eingeben, werden die DNS-Auflösungszeit, die Verbindungszeit und die Verbindungsgeschwindigkeit aus mehreren geografischen Standorten angezeigt. Dies ist auch praktisch zum Testen der Traffic Manager-Leistungsrichtlinie, um zu ermitteln, an welche gehosteten Dienste Ihre Benutzer weltweit weitergeleitet werden.

http://www.watchmouse.com/en/checkit.php


![Impuls1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

http://tools.pingdom.com/ – mit diesem Tool werden eine Website getestet und Leistungsstatistiken für jedes Element auf der Seite in einem Diagramm dargestellt. Wenn Sie zur Registerkarte "Page Analysis" wechseln, wird dort die für die DNS-Suche aufgewendete Zeit in Prozent angezeigt.

 

http://www.whatsmydns.net/ – diese Website führt eine DNS-Suche von 20 verschiedenen geografischen Standorten aus durch und zeigt die Ergebnisse auf einer Karte an. Diese visuelle Darstellung eignet sich hervorragend zum Ermitteln der gehosteten Dienste, mit denen Ihre Clients eine Verbindung herstellen.

 

http://www.digwebinterface.com – ähnelt der Watchmouse-Website, gibt jedoch detailliertere DNS-Informationen an, darunter CNAMEs- und A-Datensätze. Achten Sie darauf, dass Sie die Optionen "Colorize output" und "Stats" aktivieren und unter "Nameservers" die Option "All" auswählen.

## Zusammenfassung

Aus den oben stehenden Informationen wissen wir, dass sich die einzige Leistungsbeeinträchtigung, die Traffic Manager für eine Website hat, bei der ersten DNS-Suche ergibt (die Zeiten variieren, ca. 50 ms im Durchschnitt). Anschließend ergeben sich null Leistungseinbußen für die DNS-Gültigkeitsdauer (Standardwert 300 Sekunden). Und auch dann wird der DNS-Cache nach Ablauf der Gültigkeitsdauer wieder aktualisiert. Daher lautet die Antwort auf die Frage "Welche Latenz ergibt sich durch Traffic Manager für meine Website?" im Grunde "keine Latenz".


## Nächste Schritte


[Informationen zu Traffic Manager-Routingmethoden für Datenverkehr](traffic-manager-load-balancing-methods.md)

[Was ist Traffic Manager?](../traffic-manmager-overview.md)

[Cloud-Dienste](http://go.microsoft.com/fwlink/?LinkId=314074)

[Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Vorgänge für Traffic Manager (REST-API-Referenz)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager-Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=Oct15_HO3-->