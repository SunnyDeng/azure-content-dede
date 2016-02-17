<properties
 pageTitle="Hohe Verfügbarkeit und Notfallwiederherstellung für IoT Hub | Microsoft Azure"
 description="Informationen zu Features zum Erstellen von IoT-Lösungen mit hoher Verfügbarkeit und Notfallwiederherstellung."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="elioda"/>

# Hohe Verfügbarkeit und Notfallwiederherstellung von IoT Hub

Als Azure-Dienst sorgt IoT Hub für hohe Verfügbarkeit, indem Redundanzen auf Azure-Regionsebene bereitgestellt werden, ohne dass zusätzlicher Aufwand für die Lösung anfällt. Azure bietet außerdem eine Reihe von Features zum Erstellen von Lösungen mit Funktionen für die Notfallwiederherstellung oder, falls erforderlich, für die regionsübergreifende Verfügbarkeit. Ihre Lösungen müssen entsprechend entworfen und vorbereitet werden, um diese Features für die Notfallwiederherstellung nutzen zu können, wenn Sie für Geräte oder Benutzer global eine regionsübergreifende hohe Verfügbarkeit ermöglichen möchten. Im Artikel [Azure-Geschäftskontinuität – Technische Anleitung][] werden die integrierten Features von Azure für Geschäftskontinuität und Notfallwiederherstellung beschrieben. Das Dokument [Notfallwiederherstellung und hohe Verfügbarkeit für Azure-Anwendungen][] enthält Architekturinformationen zu Strategien für Azure-Anwendungen in Bezug auf Notfallwiederherstellung und hohe Verfügbarkeit.

## Azure IoT Hub – Notfallwiederherstellung
Zusätzlich zur Hochverfügbarkeit zwischen Regionen implementiert IoT Hub Failovermechanismen für die Notfallwiederherstellung, die keine Benutzereingriffe erfordern. Die IoT Hub-Notfallwiederherstellung löst sich selbst aus und hat ein Recovery Time Objective (RTO) von 2-26 Stunden und die folgenden Recovery Point Objectives (RPOs).

| Funktionalität | RPO |
| ------------- | --- |
| Dienstverfügbarkeit für Registrierungs- und Kommunikationsvorgänge | Möglicher CNAME-Verlust |
| Identitätsdaten in der Geräte-Identitätsregistrierung | 0-5 Minuten Datenverlust |
| D2C-Nachrichten | Alle ungelesenen Nachrichten gehen verloren |
| Vorgangsüberwachungsnachrichten | Alle ungelesenen Nachrichten gehen verloren |
| C2D-Nachrichten | 0-5 Minuten Datenverlust |
| C2D-Feedbackwarteschlange | Alle ungelesenen Nachrichten gehen verloren |

## Regionales Failover mit IoT Hub

Eine ausführliche Erläuterung von Bereitstellungstopologien in IoT-Lösungen würde den Rahmen dieses Artikels sprengen, aber mit Blick auf hohe Verfügbarkeit und Notfallwiederherstellung gehen wir auf das Bereitstellungsmodell für das *regionale Failover* ein.

Bei einem Modell für regionales Failover wird das Back-End der Lösung hauptsächlich an einem Rechenzentrumsstandort ausgeführt, aber eine IoT Hub-Einheit mit Back-End wird zu Failoverzwecken in einer weiteren Rechenzentrumsregion bereitgestellt. Wenn die IoT Hub-Einheit im primären Rechenzentrum ausfällt oder die Netzwerkverbindung des Geräts mit dem primären Rechenzentrum unterbrochen wird, ist dies dann entsprechend abgedeckt. Geräte nutzen jeweils einen sekundären Dienstendpunkt, wenn das primäre Gateway nicht erreichbar ist. Mit einer regionsübergreifenden Failoverfunktion kann die Lösungsverfügbarkeit über die hohe Verfügbarkeit einer einzelnen Region hinweg verbessert werden.

Sie benötigen im Allgemeinen Folgendes, um ein Modell für regionales Failover mit IoT Hub zu implementieren:

* **Sekundäre IoT Hub-Einheit und Logik für Geräterouting:** Bei einer Dienstunterbrechung in der primären Region müssen Geräte eine Verbindung zur sekundären Region herstellen. Da die meisten beteiligten Dienste zustandsorientiert sind, wird der Failoverprozess zwischen Regionen häufig von Lösungsadministratoren ausgelöst. Die beste Möglichkeit, Geräte über den neuen Endpunkt zu informieren und gleichzeitig die Kontrolle über den Prozess zu behalten, besteht darin, für die Geräte eine regelmäßige Prüfung eines *Concierge*-Diensts auf den derzeit aktiven Endpunkt durchführen zu lassen. Der Concierge-Dienst kann eine einfache Webanwendung sein, die repliziert wird und mithilfe von DNS-Umleitungsverfahren (z. B. per [Azure Traffic Manager][]) erreichbar gehalten wird.
* **Identitätsregistrierungsreplikation:** Um verwendet werden zu können, muss die sekundäre IoT Hub-Einheit alle Geräteidentitäten enthalten, für die eine Verbindung mit der Lösung hergestellt werden kann. Für die Lösung sollten georeplizierte Backups von Geräteidentitäten vorgehalten und auf die sekundäre IoT Hub-Einheit hochgeladen werden, bevor der aktive Endpunkt für die Geräte gewechselt wird. Die Funktionen zum Exportieren der Geräteidentität von IoT Hub sind in diesem Zusammenhang sehr nützlich. Weitere Informationen finden Sie unter [IoT Hub-Entwicklerleitfaden – Identitätsregistrierung][].
* **Zusammenführungslogik:** Wenn die primäre Region wieder verfügbar ist, müssen die Status und Daten, die am sekundären Standort erstellt wurden, zurück zur primären Region migriert werden. Dies bezieht sich hauptsächlich auf Geräte-Identitäten und Anwendungsmetadaten, die mit der primären IoT Hub-Einheit und etwaigen anderen anwendungsspezifischen Datenspeichern in der primären Region zusammengeführt werden müssen. Zum Vereinfachen dieses Schritts ist es normalerweise ratsam, idempotente Vorgänge zu nutzen. So werden Nebeneffekte nicht nur für die letztendliche konsistente Verteilung von Ereignissen verringert, sondern auch für Duplikate oder die außerordentliche Bereitstellung von Ereignissen. Außerdem sollte die Anwendungslogik so entworfen werden, dass potenzielle Inkonsistenzen oder ein „geringfügig“ veralteter Zustand toleriert werden. Dies liegt an der zusätzlichen Zeit, die das System für die „Heilung“ basierend auf Recovery Point Objectives (RPO) benötigt. Der folgende Artikel enthält weitere Informationen zu diesem Thema: [Failsafe: Leitfaden zu robusten Cloudarchitekturen][].

## Nächste Schritte

Folgen Sie diesen Links, um mehr über Azure IoT Hub zu erfahren:

- [Erste Schritte mit IoT Hubs (Tutorial)][lnk-get-started]
- [Was ist Azure IoT Hub?][]

[Azure-Geschäftskontinuität – Technische Anleitung]: https://msdn.microsoft.com/library/azure/hh873027.aspx
[Notfallwiederherstellung und hohe Verfügbarkeit für Azure-Anwendungen]: https://msdn.microsoft.com/library/azure/dn251004.aspx
[Failsafe: Leitfaden zu robusten Cloudarchitekturen]: https://msdn.microsoft.com/library/azure/jj853352.aspx
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub-Entwicklerleitfaden – Identitätsregistrierung]: iot-hub-devguide.md#identityregistry

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Was ist Azure IoT Hub?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_0204_2016-->