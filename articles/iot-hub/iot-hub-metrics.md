<properties
 pageTitle="IoT Hub-Diagnosemetriken"
 description="Übersicht über Azure IoT Hub-Metriken, die es Benutzern ermöglichen, die allgemeine Integrität ihrer Ressource zu beurteilen"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="nberdy"/>

# Einführung in Diagnosemetriken

Diagnosemetriken sind Teil der Azure-Metriken, die Ihnen bessere Daten zum Zustand der Azure-Ressourcen in Ihrem Abonnement liefern. Anhand von Metriken können Benutzer die allgemeine Integrität des Diensts und der mit ihm verbundenen Geräte bewerten. Benutzerorientierte Statistiken sind wichtig, da Benutzer mit ihrer Hilfe erkennen können, was mit ihrem IoT Hub passiert, und Probleme beheben können, ohne den Azure-Support zu kontaktieren.

Diagnosemetriken werden im Azure-Portal aktiviert.

## Aktivieren von Diagnosemetriken

1. Erstellen Sie einen IoT Hub. Anweisungen zum Erstellen eines IoT Hubs finden Sie in der Anleitung mit den [ersten Schritten][lnk-get-started].

2. Öffnen Sie das Blatt Ihres IoT Hubs. Klicken Sie dort auf **Alle Einstellungen** und dann auf **Diagnose**.

    ![][1]

3. Konfigurieren Sie die Diagnose durch Festlegen des Status auf **Ein** und Auswählen eines Speicherkontos zum Speichern der Diagnosedaten. Aktivieren Sie das Kontrollkästchen „Metriken“, und klicken Sie dann auf **Speichern**. Beachten Sie, dass das Speicherkonto vorab erstellt werden muss und die Speicherung separat in Rechnung gestellt wird.

    ![][2]

4. Kehren Sie, nachdem Sie die Diagnose eingerichtet haben, zum Hauptblatt des IoT Hubs zurück. Metrikdaten werden im Abschnitt **Überwachung** des Blatts erfasst. Durch Klicken auf das Diagramm wird der Metrikbereich geöffnet, in dem Sie eine Übersicht der Metrikdaten für Ihren IoT Hub anzeigen und die Auswahl der im Diagramm gezeigten Metriken bearbeiten können. Sie können auch Warnungen auf Grundlage von Metrikwerten konfigurieren.

    ![][3]

## Metriken und ihre Verwendung

IoT Hub bietet mehrere Metriken, um Ihnen einen Überblick über die Integrität Ihres Hubs und die Gesamtzahl der mit ihm verbundenen Geräte zu verschaffen. Sie können Informationen aus mehreren Metriken kombinieren, um sich ein umfassenderes Bild des Zustands des IoT Hubs zu machen. In der folgenden Tabelle werden die Metriken beschrieben, die für IoT Hubs nachverfolgt werden. Außerdem erfahren Sie, in welchem Bezug die einzelnen Metriken zum allgemeinen Status des IoT Hubs stehen.

| Metrik | Beschreibung der Metrik | Zweck der Metrik |
| ---- | ---- | ---- |
| d2c.telemetry.Ingress.allProtocol | Anzahl der Nachrichten, die an alle Geräte gesendet wurden | Übersichtsdaten zu gesendeten Nachrichten |
| d2c.telemetry.ingress.success | Anzahl aller erfolgreichen Nachrichten an den Hub | Übersicht über den erfolgreichen Nachrichteneingang in den Hub |
| c2d.commands.egress.complete.success | Anzahl aller Befehlsnachrichten, die vom empfangenden Gerät auf allen Geräten abgeschlossen wurden | Bietet zusammen mit den Metriken zu Abbrüchen und Zurückweisungen eine Übersicht über die allgemeine C2D-Quote erfolgreicher Befehle |
| c2d.commands.egress.abandon.success | Anzahl aller Nachrichten, die vom empfangenden Gerät auf allen Geräten erfolgreich abgebrochen wurden | Hinweis auf potenzielle Probleme, wenn Nachrichten häufiger als erwartet abgebrochen werden |
| c2d.commands.egress.reject.success | Anzahl aller Nachrichten, die vom empfangenden Gerät auf allen Geräten erfolgreich zurückgewiesen wurden | Hinweis auf potenzielle Probleme, wenn Nachrichten häufiger als erwartet zurückgewiesen werden |
| devices.totalDevices | Durchschnittliche, minimale und maximale Anzahl von Geräten, die beim IoT Hub registriert sind | Die Anzahl der Geräte, die beim Hub registriert sind |
| devices.connectedDevices.allProtocol | Durchschnittliche, minimale und maximale Anzahl gleichzeitig verbundener Geräte | Übersicht über die Anzahl der Geräte, die mit dem Hub verbunden sind |

## Nächste Schritte

Nachdem Sie einen Überblick über Diagnosemetriken erhalten haben, können Sie die folgenden Links nutzen, um mehr über diese Themen zu erfahren:

- [IoT Hub-Vorgangsüberwachung][lnk-operations-monitoring]
- [Skalieren von IoT Hub][lnk-scaling]
- [Hohe Verfügbarkeit und Notfallwiederherstellung von IoT Hub][lnk-dr]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

<!---HONumber=AcomDC_0204_2016-->