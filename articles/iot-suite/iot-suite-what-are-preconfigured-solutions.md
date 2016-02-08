<properties
 pageTitle="Vorkonfigurierte Azure IoT-Lösungen | Microsoft Azure"
 description="Eine Beschreibung der vorkonfigurierten Azure IoT-Lösungen und ihrer Architektur mit Links zu zusätzlichen Ressourcen."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/30/2015"
 ms.author="dobett"/>

# Was sind vorkonfigurierte Azure IoT Suite-Lösungen?

Bei den vorkonfigurierten Azure IoT Suite-Lösungen handelt es sich um Implementierungen allgemeiner IoT-Lösungsmuster, die Sie in Microsoft Azure mit Ihrem Azure-Abonnement bereitstellen können. Sie können vorkonfigurierte Lösungen für Folgendes verwenden:

- Als Ausgangspunkt für eigene IoT-Lösungen.
- Zum Erlernen der allgemeinen Muster beim Entwerfen und Entwickeln von IoT-Lösungen.

Mit jeder vorkonfigurierten Lösung wird ein gängiges IoT-Szenario implementiert. Es handelt sich um eine vollständige End-to-End-Implementierung, bei der simulierte Geräte zum Generieren von Telemetriedaten verwendet werden.

Sie können nicht nur die vorkonfigurierten Lösungen in Azure bereitstellen und ausführen, sondern auch den kompletten Quellcode herunterladen und die Lösung dann gemäß Ihren speziellen IoT-Anforderungen anpassen und erweitern.

Folgende vorkonfigurierte Lösungen sind verfügbar:

- [Remoteüberwachung][lnk-remote-monitoring]
- [Vorhersagbarer Wartungsbedarf][lnk-predictive-maintenance]

In der folgenden Tabelle wird gezeigt, wie diese vorkonfigurierten Lösungen bestimmten IoT-Funktionen zugeordnet sind:

| Lösung | Datenerfassung | Geräteidentität | Befehl und Steuerung | Regeln und Aktionen | Predictive Analytics |
|------------------------|-----|-----|-----|-----|-----|
| Remoteüberwachung | Ja | Ja | Ja | Ja | – | | Vorbeugende Wartung | Ja | Ja | Ja | Ja | Ja |

## Übersicht über die vorkonfigurierte Lösung zur Remoteüberwachung

In diesem Abschnitt werden einige wichtige Elemente der vorkonfigurierten Lösung für die Remoteüberwachung beschrieben. Die Remoteüberwachung ist die einfachste aller vorkonfigurierten Lösungen und dient zur Veranschaulichung gängiger Entwurfselemente, die auch in den anderen vorkonfigurierten Lösungen enthalten sind.

Im folgenden Diagramm sind die wichtigsten Elemente der Remoteüberwachungslösung dargestellt. Die folgenden Abschnitte enthalten weitere Informationen zu diesen Elementen.

![Architektur der vorkonfigurierten Lösung für Remoteüberwachung][img-remote-monitoring-arch]

## Geräte

Wenn Sie die vorkonfigurierte Lösung für die Remoteüberwachung bereitstellen, enthält die Bereitstellung Instanzen eines Softwaregerätesimulators, mit dem ein physisches Kühlgerät simuliert wird. Die simulierten Geräte senden Telemetriedaten zu Temperatur und Luftfeuchtigkeit an einen IoT Hub-Endpunkt. Außerdem reagieren die simulierten Geräte auch auf die folgenden Befehle, die vom Lösungsportal über den IoT Hub gesendet werden:

- Ping Device (Gerät pingen)
- Start Telemetry (Telemetrie starten)
- Stop Telemetry (Telemetrie beenden)
- Change Set Point Temperature (Festgelegte Punkttemperatur ändern)
- Diagnostic Telemetry (Diagnosetelemetrie)
- Change Device State (Gerätestatus ändern)

## IoT Hub

Ein IoT Hub empfängt Telemetriedaten von den Kühlgeräten an einem Endpunkt. Ein IoT Hub verwaltet auch gerätespezifische Endpunkte, wobei jedes Gerät die Befehle abrufen kann, z. B. den Befehl **Ping Device**, die an das Gerät gesendet werden.

Der IoT Hub stellt die erhaltenen Telemetriedaten über einen Consumergruppen-Endpunkt zur Verfügung.

Bei dieser vorkonfigurierten Lösung entspricht die IoT Hub-Instanz dem *Cloud-Gateway* in einer typischen [IoT-Lösungsarchitektur][lnk-what-is-azure-iot].

## Azure Stream Analytics

Die vorkonfigurierte Lösung verwendet drei [Azure Stream Analytics][lnk-asa]-Aufträge (ASA), um den Telemetriedatenstrom von den Kühlgeräten zu filtern:

- Auftrag 1 sendet alle Telemetriedaten zu Cold Storage-Zwecken an die Azure Blob Storage-Einheit.
- Auftrag 2 filtert den Telemetriedatenstrom, um Befehlsantwortnachrichten und Gerätestatusnachrichten von den Geräten zu identifizieren, und sendet diese speziellen Nachrichten an einen Azure Event Hub-Endpunkt.
- Auftrag 3 filtert den Telemetriedatenstrom nach Werten, die Alarme auslösen. Wenn ein Wert einen Alarm auslöst, zeigt die Lösung die Benachrichtigung in der Tabelle mit dem Alarmverlauf in der Dashboardanzeige des Lösungsportals an.

Bei dieser vorkonfigurierten Lösung bilden die ASA-Aufträge einen Teil des *IoT-Lösungs-Back-Ends* in einer typischen [IoT-Lösungsarchitektur][lnk-what-is-azure-iot].

## Ereignisprozessor

Eine [EventPocessorHost][lnk-event-processor]-Instanz, die in einem [WebJob][lnk-web-job] ausgeführt wird, verarbeitet die von ASA-Auftrag 2 identifizierten Befehlsantwort- und Gerätestatusnachrichten und speichert diese Informationen dann in einer [Azure DocumentDB][lnk-document-db]-Datenbank.

Bei dieser vorkonfigurierten Lösung bildet der Ereignisprozessor einen Teil des *IoT-Lösungs-Back-Ends* in einer typischen [IoT-Lösungsarchitektur][lnk-what-is-azure-iot].

## Lösungsportal

Das Lösungsportal ist eine webbasierte Benutzeroberfläche, die in der Cloud als Teil der vorkonfigurierten Lösung bereitgestellt wird. Hiermit haben Sie folgende Möglichkeiten:

- Anzeigen von Telemetriedaten und Alarmverlauf in einem Dashboard
- Bereitstellen von neuen Geräten
- Verwalten und Überwachen von Geräten
- Senden von Befehlen an bestimmte Geräte
- Verwalten von Regeln und Aktionen

> [AZURE.NOTE] Im Lösungsportal bleibt die [Registrierung der IoT Hub-Geräteidentität][lnk-identity-registry] mit den umfassenderen Gerätestatusinformationen in der DocumentDB-Datenbank der Lösung synchronisiert.

Bei dieser vorkonfigurierten Lösung bildet das Lösungsportal einen Teil des *IoT-Lösungs-Back-Ends* und einen Teil der *Verarbeitungsverbindung und Business Connectivity* in einer typischen [IoT-Lösungsarchitektur][lnk-what-is-azure-iot].

## Nächste Schritte

Unter den folgenden Ressourcen erhalten Sie weitere Informationen zu vorkonfigurierten IoT-Lösungen:

- [Übersicht über vorkonfigurierte Azure IoT-Lösungen][lnk-suite-overview]
- [Erste Schritte mit den vorkonfigurierten IoT-Lösungen][lnk-preconf-get-started]

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: web-sites-create-web-jobs.md
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-identity-registry]: iot-hub-devguide.md#device-identity-registry
[lnk-suite-overview]: iot-suite-overview.md
[lnk-preconf-get-started]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_0128_2016-->