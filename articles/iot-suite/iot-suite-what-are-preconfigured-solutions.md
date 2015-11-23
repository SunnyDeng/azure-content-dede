<properties
 pageTitle="Vorkonfigurierte Azure IoT-Lösungen | Microsoft Azure"
 description="Eine Beschreibung der vorkonfigurierten Azure IoT-Lösungen und ihrer Architektur mit Links zu zusätzlichen Ressourcen."
 services=""
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="na"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/29/2015"
 ms.author="araguila"/>

# Was sind vorkonfigurierte Azure IoT-Suite-Lösungen?

Sie können mit Ihrem Azure-Abonnement vorkonfigurierte Lösungen bereitstellen, die häufige IoT-Szenarios (Internet of Things) in Microsoft Azure implementieren. Sie können vorkonfigurierte Lösungen für Folgendes verwenden:

- Als Ausgangspunkt für eigene IoT-Lösungen.
- Zum Erlernen der am häufigsten verwendeten Muster bei Entwurf und Entwicklung von IoT-Lösungen.

Jede vorkonfigurierte Lösung implementiert ein häufiges IoT-Szenario und ist eine vollständige End-to-End-Implementierung.

Sie können nicht nur die vorkonfigurierten Lösungen in Azure bereitstellen und ausführen, sondern auch den kompletten Quellcode herunterladen, um die Lösung entsprechend Ihren speziellen Anforderungen anzupassen und zu erweitern.

Folgende vorkonfigurierte Lösungen sind verfügbar:

- Remoteüberwachung
- Vorhersagbarer Wartungsbedarf

In der folgenden Tabelle wird gezeigt, wie diese vorkonfigurierten Lösungen bestimmten IoT-Funktionen zugeordnet sind:

| Lösung | Datenerfassung | Geräteidentität | Befehl und Steuerung | Regeln und Aktionen | Predictive Analytics |
|------------------------|----------------|-----------------|---------------------|-------------------|----------------------|
| Remoteüberwachung | Ja | Ja | Ja | Ja | – | | Vorbeugende Wartung | Ja | Ja | Ja | Ja | Ja |

## Übersicht über das Remoteüberwachungsbeispiel

Die Remoteüberwachung ist die einfachste der vorkonfigurierten Lösungen mit vollständiger Funktionalität. In diesem Abschnitt wird der vollständige Satz der vorkonfigurierten Lösungen vorgestellt, um einige der wichtigsten Funktionen der vorkonfigurierten Remoteüberwachungslösung zu beschreiben.

Das folgende Diagramm und die folgenden Abschnitte veranschaulichen die wichtigsten Funktionen der Lösung. Die folgenden Abschnitte enthalten weitere Informationen zu den Elementen in diesem Diagramm.

![Architektur der vorkonfigurierten Lösung für Remoteüberwachung][img-remote-monitoring-arch]

### Gerät

Das Gerät, das vorab mit der vorkonfigurierten Überwachungslösung bereitgestellt wird, ist eine Softwaresimulation eines Kühlers, der Telemetriedaten zu Temperatur und Luftfeuchtigkeit sendet. Das Gerät kann auch auf eine Reihe von Befehlen reagieren, die über IoT Hub vom Lösungsportal gesendet werden. Die folgenden Befehle sind bereits im Simulator implementiert: "Gerät pingen", "Telemetrie starten", "Telemetrie beenden", "Soll-Temperatur ändern", "Diagnosetelemetrie" und "Gerätestatus ändern"

Die Kühler in dieser vorkonfigurierten Lösung entsprechen den **Geräten und Datenquellen** in einer typischen IoT-Lösungsarchitektur.

### IoT Hub

Ein IoT Hub empfängt an einem einzelnen Endpunkt Telemetriedaten von den Kühlern und verwaltet gerätespezifische Endpunkte, an denen Geräte Befehle, z. B. den Befehl "PingDevice", abrufen können.

Der IoT Hub macht die Telemetriedaten, die er über einen Consumergruppen-Endpunkt empfängt, verfügbar.

Die IoT Hub-Instanz in dieser vorkonfigurierten Lösung entspricht dem **Cloud-Gateway** in einer typischen IoT-Lösungsarchitektur.

### Azure Stream Analytics

Die vorkonfigurierte Lösung verwendet [Azure Stream Analytics][]-Aufträge, um den Datenstrom von Ereignissen aus den Kühlern zu filtern. Ein Auftrag sendet alle Telemetriedaten an Azure-Speicherblobs für Cold Storage. Der zweite Auftrag filtert den Ereignisdatenstrom nach Befehlsantwortnachrichten und Gerätestatus-Aktualisierungsnachrichten und sendet diese an einen Azure Event Hub-Endpunkt. Der dritte Auftrag filtert nach ausgelösten Alarmen und zeigt diese in der Dashboardansicht des Lösungsportals in der Alarmverlaufstabelle an.


### Ereignisprozessor

Eine in einem Webauftrag ausgeführte Ereignisprozessorinstanz verarbeitet die Befehlsantwort- und Gerätestatusdaten und speichert diese in einer Azure-DocumentDB-Datenbank.

### Lösungsportal

Das Lösungsportal ist eine webbasierte Benutzeroberfläche, die Folgendes ermöglicht:

- Anzeigen von Telemetrie- und Alarmverlauf im Dashboard
- Bereitstellen von neuen Geräten
- Verwalten und Überwachen von Geräten
- Senden von Befehlen an bestimmte Geräte
- Verwalten von Regeln und Aktionen

> [AZURE.NOTE]In der Geräteansicht des Lösungsportals bleibt die Registrierung der IoT Hub-Geräteidentität mit den umfassenderen Gerätestatusinformationen in der DocumentDB-Datenbank synchronisiert.

## Nächste Schritte

Unter den folgenden Ressourcen erhalten Sie weitere Informationen zu vorkonfigurierten IoT-Lösungen:

- [Übersicht über vorkonfigurierte Azure IoT-Lösungen](iot-suite-overview.md)
- [Erste Schritte mit den vorkonfigurierten IoT-Lösungen](iot-suite-getstarted-preconfigured-solutions.md)

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/

<!---HONumber=Nov15_HO3-->