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
 ms.date="03/02/2016"
 ms.author="dobett"/>

# Was sind vorkonfigurierte Azure IoT Suite-Lösungen?

Bei den vorkonfigurierten Azure IoT Suite-Lösungen handelt es sich um Implementierungen allgemeiner IoT-Lösungsmuster, die Sie in Azure mit Ihrem Abonnement bereitstellen können. Sie können vorkonfigurierte Lösungen für Folgendes verwenden:

- Als Ausgangspunkt für eigene IoT-Lösungen.
- Zum Erlernen der allgemeinen Muster beim Entwerfen und Entwickeln von IoT-Lösungen.

Bei jeder vorkonfigurierten Lösung handelt es sich um eine vollständige End-to-End-Implementierung, bei der simulierte Geräte zum Generieren von Telemetriedaten verwendet werden.

Sie können nicht nur die Lösungen in Azure bereitstellen und ausführen, sondern auch den kompletten Quellcode herunterladen und die Lösung dann gemäß Ihren speziellen IoT-Anforderungen anpassen und erweitern.

> [AZURE.NOTE] Informationen zum Bereitstellen einer vorkonfigurierten Lösung finden Sie unter [Microsoft Azure IoT Suite][lnk-azureiotsuite]. Der Artikel [Erste Schritte mit den vorkonfigurierten IoT-Lösungen][lnk-preconf-get-started] enthält weitere Informationen zum Bereitstellen und Ausführen der Lösungen.

In der folgenden Tabelle wird gezeigt, welchen IoT-Features die Lösungen zugeordnet sind:

| Lösung | Datenerfassung | Geräteidentität | Befehl und Steuerung | Regeln und Aktionen | Predictive Analytics |
|------------------------|-----|-----|-----|-----|-----|
| [Remoteüberwachung][lnk-remote-monitoring] | Ja | Ja | Ja | Ja | – | | [Vorbeugende Wartung][lnk-predictive-maintenance] | Ja | Ja | Ja | Ja | Ja |

- *Datenerfassung:* Skalierbarer Dateneingang in der Cloud
- *Geräteidentität:* Verwaltung eindeutiger Identitäten von jedem verbundenen Gerät
- *Befehl und Steuerung:* Senden von Nachrichten aus der Cloud an ein Gerät, um zu veranlassen, dass das Gerät bestimmte Aktionen ausführt
- *Regeln und Aktionen:* Das Lösung-Back-End verwendet Regeln, um auf bestimmte D2C (Device-to-Cloud)-Daten zu reagieren.
- *Predictive Analytics:* Die Back-End-Lösung analysiert D2C-Daten, um vorherzusagen, wann bestimmte Aktionen durchgeführt werden sollen. Beispiel: Analysieren von Telemetriedaten eines Flugzeugtriebwerks, um zu bestimmen, wann die Triebwerkwartung erforderlich ist.

## Übersicht über die vorkonfigurierte Lösung zur Remoteüberwachung

Die vorkonfigurierte Lösung zur Remoteüberwachung wird in diesem Artikel beschrieben, weil sie zur Veranschaulichung zahlreicher gängiger Entwurfselemente dient, die auch in den anderen vorkonfigurierten Lösungen enthalten sind.

Im folgenden Diagramm sind die wichtigsten Elemente der Remoteüberwachungslösung dargestellt. Die folgenden Abschnitte enthalten weitere Informationen zu diesen Elementen.

![Architektur der vorkonfigurierten Lösung für Remoteüberwachung][img-remote-monitoring-arch]

## Geräte

Wenn Sie die vorkonfigurierte Lösung für die Remoteüberwachung bereitstellen, werden vier simulierte Geräte in der Lösung vorab bereitgestellt, mit denen ein Kühlgerät simuliert wird. Diese simulierte Geräte verfügen über ein integriertes Temperatur- und Luftfeuchtigkeitsmodell, das Telemetriedaten ausgibt. Diese simulierten Geräte sind zur Veranschaulichung des End-to-End-Datenflusses in der Lösung enthalten. Darüber hinaus bieten sie praktische Telemetriedaten und ein Ziel für Befehle, wenn Sie Back-End-Entwickler sind und die Lösung als Ausgangspunkt für eine benutzerdefinierte Implementierung nutzen.

Bei der ersten Verbindung eines Geräts mit IoT Hub in der vorkonfigurierten Lösung für die Remoteüberwachung werden in der an den IoT Hub gesendeten Geräteinformationsmeldung die Befehle aufgelistet, auf die das Gerät reagieren kann. In der vorkonfigurierten Lösung für die Remoteüberwachung sind das die folgenden Befehle:

- *Ping Device* (Gerät pingen): Das Gerät reagiert auf diesen Befehl mit einer Bestätigung. Dies ist hilfreich, um sicherzustellen, dass das Gerät noch aktiv ist und überwacht wird.
- *Start Telemetry* (Telemetrie starten): Weist das Gerät an, mit dem Senden von Telemetriedaten zu beginnen.
- *Stop Telemetry* (Telemetrie beenden): Weist das Gerät an, das Senden von Telemetriedaten zu beenden.
- *Change Set Point Temperature* (Festgelegte Punkttemperatur ändern): Steuert die simulierten Temperaturtelemetriewerte, die das Gerät sendet. Dies ist hilfreich beim Testen der Back-End-Logik.
- *Diagnostic Telemetry* (Diagnosetelemetrie): Steuert, ob das Gerät die externen Temperaturtelemetriedaten sendet.
- *Change Device State* (Gerätestatus ändern): Legt die Metadateneigenschaft des Gerätezustands fest, den das Gerät meldet. Dies ist hilfreich beim Testen der Back-End-Logik.

Sie können der Lösung weitere simulierte Geräte hinzufügen, die die gleichen Telemetriedaten ausgeben und auf die gleichen Befehle reagieren.

## IoT Hub

Bei dieser vorkonfigurierten Lösung entspricht die IoT Hub-Instanz dem *Cloud-Gateway* in einer typischen [IoT-Lösungsarchitektur][lnk-what-is-azure-iot].

Ein IoT Hub empfängt Telemetriedaten von den Geräten an einem Endpunkt. Ein IoT Hub verwaltet auch gerätespezifische Endpunkte, wobei jedes Gerät die Befehle abrufen kann, die an das Gerät gesendet werden.

Der IoT Hub macht die empfangenen Telemetriedaten über den dienstseitigen Endpunkt für gelesene Telemetriedaten verfügbar.

## Azure Stream Analytics

Die vorkonfigurierte Lösung verwendet drei [Azure Stream Analytics][lnk-asa]-Aufträge (ASA), um den Telemetriedatenstrom von den Geräten zu filtern:


- *DeviceInfo-Auftrag* – Gibt Daten an einen Event Hub aus, der beim ersten Herstellen einer Verbindung mit einem Gerät oder in Reaktion auf den Befehl **Change device state** (Gerätestatus ändern) geräteregistrierungspezifische Meldungen an die Lösungsgeräteregistrierung (eine DocumentDB-Datenbank) weiterleitet. 
- *Telemetrieauftrag* – Sendet alle Telemetrierohdaten zu Cold Storage-Zwecken an den Azure-Blobspeicher und berechnet Telemetrieaggregationen, die im Lösungsdashboard angezeigt werden.
- *Regelauftrag* – Filtert den Telemetriedatenstrom, um Werte zu identifizieren, die Regelschwellenwerte überschreiten, und gibt die Daten an einen Event Hub aus. Wenn eine Regel ausgelöst wird, wird dieses Ereignis in der Dashboardansicht des Lösungsportals in der Tabelle mit dem Alarmverlauf als neue Zeile angezeigt. Außerdem wird basierend auf den im Lösungsportal in den Ansichten „Regeln“ und „Aktionen“ definierten Einstellungen eine Aktion ausgelöst.

Bei dieser vorkonfigurierten Lösung bilden die ASA-Aufträge einen Teil des **IoT-Lösungs-Back-Ends** in einer typischen [IoT-Lösungsarchitektur][lnk-what-is-azure-iot].

## Ereignisprozessor

Bei dieser vorkonfigurierten Lösung bildet der Ereignisprozessor einen Teil des **IoT-Lösungs-Back-Ends** in einer typischen [IoT-Lösungsarchitektur][lnk-what-is-azure-iot].

Die ASA-Aufträge **DeviceInfo** und **Regeln** senden ihre Ausgabe an Event Hubs für die Weitergabe an andere Back-End-Dienste. Die Lösung verwendet eine [EventPocessorHost][lnk-event-processor]-Instanz, die in einem [WebJob][lnk-web-job] ausgeführt wird, um die Nachrichten von diesen Event Hubs zu lesen. Die **EventProcessorHost**-Instanz verwendet die **DeviceInfo**-Daten zum Aktualisieren der Gerätedaten in der DocumentDB-Datenbank und die **Regeldaten** zum Aufrufen der Logik-App sowie zum Aktualisieren der Warnungsanzeige im Lösungsportal.

## Geräteidentitätsregistrierung und DocumentDB

Jeder IoT Hub enthält eine [Geräteidentitätsregistrierung][lnk-identity-registry], in der Geräteschlüssel gespeichert sind. IoT Hub verwendet diese Informationen zum Authentifizieren von Geräten – ein Gerät muss registriert sein und einen gültigen Schlüssel haben, bevor es eine Verbindung mit dem Hub herstellen kann.

Diese Lösung speichert zusätzliche Informationen zu Geräten, beispielsweise ihren Zustand, die unterstützten Befehle und andere Metadaten. Die Lösung verwendet eine DocumentDB-Datenbank zum Speichern dieser lösungsspezifischen Gerätedaten, und das Lösungsportal ruft Daten aus dieser DocumentDB-Datenbank für die Anzeige und Bearbeitung ab.

Die Lösung muss zudem die Informationen in der Geräteidentitätsregistrierung mit dem Inhalt der DocumentDB-Datenbank synchron halten. **EventProcessorHost** verwendet die Daten aus dem **DeviceInfo**-Datenstromanalyseauftrag zum Verwalten der Synchronisierung.

## Lösungsportal

![Lösungsdashboard][img-dashboard]

Das Lösungsportal ist eine webbasierte Benutzeroberfläche, die in der Cloud als Teil der vorkonfigurierten Lösung bereitgestellt wird. Hiermit haben Sie folgende Möglichkeiten:

- Anzeigen von Telemetriedaten und Alarmverlauf in einem Dashboard
- Bereitstellen von neuen Geräten
- Verwalten und Überwachen von Geräten
- Senden von Befehlen an bestimmte Geräte
- Verwalten von Regeln und Aktionen

Bei dieser vorkonfigurierten Lösung bildet das Lösungsportal einen Teil des **IoT-Lösungs-Back-Ends** und einen Teil der **Verarbeitungsverbindung und Business Connectivity** in einer typischen [IoT-Lösungsarchitektur][lnk-what-is-azure-iot].

## Nächste Schritte

Weitere Informationen zu den IoT-Lösungsarchitekturen finden Sie unter [Microsoft Azure IoT services: Reference Architecture][lnk-refarch] (Microsoft Azure IoT-Dienste: Referenzarchitektur).

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
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf

<!---HONumber=AcomDC_0330_2016-->