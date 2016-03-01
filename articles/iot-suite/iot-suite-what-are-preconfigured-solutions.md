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
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/19/2016"
 ms.author="dobett"/>

# Was sind vorkonfigurierte Azure IoT Suite-Lösungen?

Bei den vorkonfigurierten Azure IoT Suite-Lösungen handelt es sich um Implementierungen allgemeiner IoT-Lösungsmuster, die Sie in Microsoft Azure mit Ihrem Azure-Abonnement bereitstellen können. Sie können vorkonfigurierte Lösungen für Folgendes verwenden:

- Als Ausgangspunkt für eigene IoT-Lösungen.
- Zum Erlernen der allgemeinen Muster beim Entwerfen und Entwickeln von IoT-Lösungen.

Mit jeder vorkonfigurierten Lösung wird ein gängiges IoT-Szenario implementiert. Es handelt sich um eine vollständige End-to-End-Implementierung, bei der simulierte Geräte zum Generieren von Telemetriedaten verwendet werden.

Sie können nicht nur die Lösungen in Azure bereitstellen und ausführen, sondern auch den kompletten Quellcode herunterladen und die Lösung dann gemäß Ihren speziellen IoT-Anforderungen anpassen und erweitern.

> [AZURE.NOTE] Der Artikel [Erste Schritte mit den vorkonfigurierten IoT-Lösungen][lnk-preconf-get-started] beschreibt das Bereitstellen und Ausführen der Lösungen.

In der folgenden Tabelle wird gezeigt, welchen IoT-Features die Lösungen zugeordnet sind:

| Lösung | Datenerfassung | Geräteidentität | Befehl und Steuerung | Regeln und Aktionen | Predictive Analytics |
|------------------------|-----|-----|-----|-----|-----|
| [Remoteüberwachung][lnk-remote-monitoring] | Ja | Ja | Ja | Ja | – | | [Vorbeugende Wartung][lnk-predictive-maintenance] | Ja | Ja | Ja | Ja | Ja |

## Übersicht über die vorkonfigurierte Lösung zur Remoteüberwachung

Die vorkonfigurierte Lösung zur Remoteüberwachung wird in diesem Artikel beschrieben, weil sie die einfachste aller vorkonfigurierten Lösungen ist und zur Veranschaulichung gängiger Entwurfselemente dient, die auch in den anderen vorkonfigurierten Lösungen enthalten sind.

Im folgenden Diagramm sind die wichtigsten Elemente der Remoteüberwachungslösung dargestellt. Die folgenden Abschnitte enthalten weitere Informationen zu diesen Elementen.

![Architektur der vorkonfigurierten Lösung für Remoteüberwachung][img-remote-monitoring-arch]

## Geräte

Wenn Sie die vorkonfigurierte Lösung für die Remoteüberwachung bereitstellen, werden vier simulierte Geräte in der Lösung vorab bereitgestellt, mit denen ein Kühlgerät simuliert wird. Diese simulierte Geräte verfügen über ein integriertes Temperatur- und Luftfeuchtigkeitsmodell, das Telemetriedaten ausgibt.

Bei der ersten Verbindung eines Geräts mit IoT Hub in der vorkonfigurierten Lösung für die Remoteüberwachung werden in der an den IoT Hub gesendeten Geräteinformationsmeldung die Befehle aufgelistet, auf die das Gerät reagieren kann. In der vorkonfigurierten Lösung für die Remoteüberwachung sind das die folgenden Befehle:

- *Gerät pingen* Das Gerät reagiert auf diesen Befehl mit einer Bestätigung. Dies ist hilfreich, um sicherzustellen, dass das Gerät noch aktiv ist und überwacht wird.
- *Telemetrie starten* Weist das Gerät an, mit dem Senden von Telemetriedaten zu beginnen.
- *Telemetrie beenden* Weist das Gerät an, das Senden von Telemetriedaten zu beenden.
- *Festgelegte Punkttemperatur ändern* Steuert die simulierten Temperaturtelemetriewerte, die das Gerät sendet. Dies ist hilfreich beim Testen.
- *Diagnosetelemetrie* Steuert, ob das Gerät die externen Temperaturtelemetriedaten sendet.
- *Gerätestatus ändern* Legt die Metadateneigenschaft des Gerätezustands fest, den das Gerät meldet. Dies ist hilfreich beim Testen.

Sie können der Lösung weitere simulierte Geräte hinzufügen, die die gleichen Telemetriedaten ausgeben und auf die gleichen Befehle reagieren.

## IoT Hub

Ein IoT Hub empfängt Telemetriedaten von den Kühlgeräten an einem Endpunkt. Ein IoT Hub verwaltet auch gerätespezifische Endpunkte, wobei jedes Gerät die Befehle abrufen kann, die an das Gerät gesendet werden.

Der IoT Hub stellt die erhaltenen Telemetriedaten über einen Consumergruppen-Endpunkt zur Verfügung.

Bei dieser vorkonfigurierten Lösung entspricht die IoT Hub-Instanz dem *Cloud-Gateway* in einer typischen [IoT-Lösungsarchitektur][lnk-what-is-azure-iot].

## Azure Stream Analytics

Die vorkonfigurierte Lösung verwendet drei [Azure Stream Analytics][lnk-asa]-Aufträge (ASA), um den Telemetriedatenstrom von den Kühlgeräten zu filtern:


- *DeviceInfo-Auftrag* – sendet geräteregistrierungspezifische Meldungen an die Geräteregistrierung der Lösung (eine DocumentDB-Datenbank).
- *Telemetrieauftrag* – sendet alle Telemetrierohdaten zu Cold Storage-Zwecken an den Azure-Blobspeicher und berechnet Telemetrieaggregationen, die im Lösungsdashboard angezeigt werden.
- *Regelauftrag* – filtert den Telemetriedatenstrom, um Werte zu identifizieren, die Regelschwellenwerte überschreiten. Wenn eine Regel ausgelöst wird, wird dieses Ereignis in der Dashboardansicht des Lösungsportals in der Tabelle mit dem Alarmverlauf als neue Zeile angezeigt. Außerdem wird basierend auf den im Lösungsportal in den Ansichten „Regeln“ und „Aktionen“ definierten Einstellungen eine Aktion ausgelöst.

Bei dieser vorkonfigurierten Lösung bilden die ASA-Aufträge einen Teil des *IoT-Lösungs-Back-Ends* in einer typischen [IoT-Lösungsarchitektur][lnk-what-is-azure-iot].

## Ereignisprozessor

Eine [EventPocessorHost][lnk-event-processor]-Instanz, die in einem [WebJob][lnk-web-job] ausgeführt wird, verarbeitet die Ausgabe aus den DeviceInfo- und Regelaufträgen.

Bei dieser vorkonfigurierten Lösung bildet der Ereignisprozessor einen Teil des *IoT-Lösungs-Back-Ends* in einer typischen [IoT-Lösungsarchitektur][lnk-what-is-azure-iot].

## Lösungsportal

![Lösungsdashboard][img-dashboard]

Das Lösungsportal ist eine webbasierte Benutzeroberfläche, die in der Cloud als Teil der vorkonfigurierten Lösung bereitgestellt wird. Hiermit haben Sie folgende Möglichkeiten:

- Anzeigen von Telemetriedaten und Alarmverlauf in einem Dashboard
- Bereitstellen von neuen Geräten
- Verwalten und Überwachen von Geräten
- Senden von Befehlen an bestimmte Geräte
- Verwalten von Regeln und Aktionen

> [AZURE.NOTE] In der vorkonfigurierten Lösung bleibt die [Registrierung der IoT-Hub Geräteidentität][lnk-identity-registry] mit der Registrierung (DocumentDB-Datenbank) der Geräteidentität der Lösung synchronisiert, in der umfangreichere Gerätemetadaten gespeichert werden.

Bei dieser vorkonfigurierten Lösung bildet das Lösungsportal einen Teil des *IoT-Lösungs-Back-Ends* und einen Teil der *Verarbeitungsverbindung und Business Connectivity* in einer typischen [IoT-Lösungsarchitektur][lnk-what-is-azure-iot].

## Nächste Schritte

Unter den folgenden Ressourcen erhalten Sie weitere Informationen zu vorkonfigurierten IoT-Lösungen:

- [Erste Schritte mit den vorkonfigurierten IoT-Lösungen][lnk-preconf-get-started]
- [Übersicht über die vorkonfigurierte Lösung für vorhersagbaren Wartungsbedarf][lnk-predictive-maintenance]

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
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

<!---HONumber=AcomDC_0224_2016-->