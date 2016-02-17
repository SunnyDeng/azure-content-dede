<properties
 pageTitle="Exemplarische Vorgehensweise zur vorkonfigurierten Lösung für Remoteüberwachung | Microsoft Azure"
 description="Eine Beschreibung der vorkonfigurierten Lösung für Remoteüberwachung von Azure IoT und deren Architektur."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2015"
 ms.author="stevehob"/>

# Exemplarische Vorgehensweise zur vorkonfigurierten Lösung für Remoteüberwachung

## Einführung

Die vorkonfigurierten Lösung für Remoteüberwachung der IoT Suite ist eine einfache End-to-End-Überwachungslösung für ein Geschäftsszenario, bei dem mehrere Computer an Remotestandorten betrieben werden. Die Lösung kombiniert wichtige Dienste der Azure IoT Suite, um eine generische Implementierung des Geschäftsszenarios bereitzustellen, und sie dient als Ausgangspunkt für Kunden, die diese Art von IoT-Lösung für ihre eigenen spezifischen Geschäftsanforderungen implementieren möchten.

## Logische Architektur

Das folgende Diagramm beschreibt die logischen Komponenten der vorkonfigurierten Lösung:

![](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


### Simulierte Geräte

In der vorkonfigurierten Lösung ist das simulierte Gerät ein Kühlgerät (z. B. die Klimaanlage eines Gebäudes oder die Lüftungsanlage einer Anlage). Jedes simulierte Gerät sendet die folgenden Telemetriemeldungen an IoT Hub:


| Message | Beschreibung |
|----------|-------------|
| Starten | Wenn das Gerät gestartet wird, sendet es eine **device-info**-Meldung mit Informationen über sich selbst. Dazu gehören die Geräte-ID, Gerätemetadaten, eine Liste von Befehlen, die das Gerät unterstützt, und die aktuelle Konfiguration des Geräts. |


Die simulierten Geräte senden die folgenden Geräteeigenschaften als Metadaten:

| Eigenschaft | Zweck |
|------------------------|--------- |
| Geräte-ID | Die ID, die entweder bereitgestellt oder zugewiesen wird, wenn ein Gerät in der Lösung erstellt wird. |
| Hersteller | Gerätehersteller |
| Modellnummer | Modellnummer des Geräts |
| Seriennummer | Seriennummer des Geräts |
| Firmware | Aktuelle Version der Firmware auf dem Gerät |
| Plattform | Plattformarchitektur des Geräts |
| Prozessor | Prozessor des Geräts |
| Installierter RAM | Größe des auf dem Gerät installierten RAM |
| Für Hub aktivierter Zustand | IoT Hub-Zustandseigenschaft des Geräts |
| Erstellungszeit | Uhrzeit der Erstellung des Geräts in der Lösung |
| Aktualisierungszeit | Letzter Zeitpunkt, zu dem die Eigenschaften für das Gerät aktualisiert wurden |
| Breitengrad | Breitengrad des Standorts des Geräts |
| Längengrad | Längengrad des Standorts des Geräts |

Der Simulator füllt diese Eigenschaften in simulierten Geräten mit Beispielwerten. Jedes Mal, wenn der Simulator ein simuliertes Gerät initialisiert, sendet das Gerät vordefinierte Metadaten an IoT Hub. Beachten Sie, dass damit alle Aktualisierungen von Metadaten, die im Geräteportal vorgenommen werden, überschrieben werden.


Die simulierten Geräte können die folgenden Befehle verarbeiten, die von einem IoT Hub gesendet werden:

| Befehl | Beschreibung |
|------------------------|-----------------------------------------------------|
| PingDevice | Sendet einen _Ping_ an das Gerät, um zu überprüfen, ob es aktiv ist. |
| StartTelemetry | Startet das Senden von Telemetriedaten auf dem Gerät |
| StopTelemetry | Beendet das Senden von Telemetriedaten auf dem Gerät |
| ChangeSetPointTemp | Ändert den festgelegten Punktwert, um den die zufälligen Daten generiert werden |
| DiagnosticTelemetry | Löst aus, dass der Gerätesimulator einen zusätzlichen Telemetriewert (externalTemp) sendet |
| ChangeDeviceState | Ändert eine erweiterte Zustandseigenschaft für das Gerät und sendet die Meldung mit Geräteinformationen vom Gerät |


Die Bestätigung der Gerätebefehle erfolgt über den IoT Hub.


### Azure Stream Analytics-Aufträge

**Auftrag 1: Telemetrie** verarbeitet den eingehenden Datenstrom mit Gerätetelemetriedaten mit zwei Befehlen. Der erste Befehl sendet alle Telemetriemeldungen von den Geräten an den permanenten Blobspeicher. Mit dem zweiten Befehl werden durchschnittliche, minimale und maximale Luftfeuchtigkeitswerte für ein gleitendes Fenster von fünf Minuten berechnet. Diese Daten werden auch an den Blobspeicher gesendet.

**Auftrag 2: Geräteinformationen** filtert Meldungen mit Geräteinformationen aus dem eingehenden Meldungsdatenstrom und sendet diese an einen Event Hub-Endpunkt. Ein Gerät sendet Meldungen mit Geräteinformationen beim Start und als Antwort auf den Befehl **SendDeviceInfo**.

**Auftrag 3: Regeln** wertet eingehende Telemetriewerte zu Temperatur und Feuchtigkeit anhand von Schwellenwerten pro Gerät aus. Schwellenwerte werden im Regel-Editor festgelegt, der zur Lösung gehört. Jedes Gerät-Wert-Paar wird nach dem Zeitstempel in einem Blob gespeichert, der in Stream Analytics als **Verweisdaten** gelesen wird. Der Auftrag vergleicht alle nicht leeren Werte mit dem für das Gerät festgelegten Schwellenwert. Wenn er die Bedingung „>“ überschreitet, gibt der Auftrag ein **Alarm**-Ereignis aus. Damit wird angezeigt, dass der Schwellenwert überschritten wurde. Zudem werden das Gerät, der Wert und Zeitstempelwerte bereitgestellt.

### Ereignisprozessor

Der **Ereignisprozessor** verarbeitet Meldungen mit Geräteinformationen und Antworten auf Befehle. Er verwendet Folgendes:

- Meldungen mit Geräteinformationen, die die Registrierung des Geräts (gespeichert in der DocumentDB-Datenbank) mit den aktuellen Geräteinformationen aktualisieren.
- Meldungen mit Antworten auf Befehle, um den Befehlsverlauf des Geräts zu aktualisieren (gespeichert in der DocumentDB-Datenbank).

## Exemplarische Vorgehensweise

In diesem Abschnitt werden die Komponenten der Lösung erläutert, der beabsichtigte Anwendungsfall beschrieben und Beispiele bereitgestellt.

### Dashboard der Remoteüberwachung
Diese Seite in der Webanwendung verwendet PowerBI-JavaScript-Steuerelemente (siehe [PowerBI-visuals repo](https://www.github.com/Microsoft/PowerBI-visuals) (in englischer Sprache)), um die Ausgabedaten der Stream Analytics-Aufträge im Blobspeicher zu visualisieren.


### Geräteverwaltungsportal

Diese Web-App ermöglicht Folgendes:

- Bereitstellen eines neuen Geräts, wobei die eindeutige Geräte-ID festgelegt und der Authentifizierungsschlüssel generiert wird.
- Verwalten von Geräteeigenschaften, dies umfasst das Anzeigen vorhandener Eigenschaften und das Aktualisieren mit neuen Eigenschaften.
- Senden von Befehlen an ein Gerät.
- Anzeigen des Befehlsverlaufs für ein Gerät.

### Beobachten des Verhaltens der Cloudlösung
Sie können die bereitgestellten Ressourcen anzeigen, indem Sie zum [Azure-Portal](https://portal.azure.com) wechseln und zur Ressourcengruppe mit dem von Ihnen angegebenen Lösungsnamen navigieren.

![](media/iot-suite-remote-monitoring-sample-walkthrough/azureportal_01.png)

Beim ersten Ausführen des Beispiels sind vier vorkonfigurierte, simulierte Geräte vorhanden:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_01.png)

Sie können mit dem Geräteverwaltungsportal ein neues simuliertes Gerät hinzufügen:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_02.png)

Anfangs ist der Status des neuen Geräts im Geräteverwaltungsportal **Ausstehend**:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_03.png)

Wenn die App das Bereitstellen des simulierten Geräts beendet hat, ändert sich der Status des Geräts im Geräteverwaltungsportal in **Wird ausgeführt**, wie im folgenden Screenshot gezeigt. Der Stream Analytics-Auftrag **DeviceInfo** sendet Gerätestatusinformationen vom Gerät an das Geräteverwaltungsportal.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_04.png)

Mithilfe des Lösungsportals können Sie Befehle wie **ChangeSetPointTemp** an das Gerät senden:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_05.png)

Wenn das Gerät meldet, dass es den Befehl erfolgreich ausgeführt hat, wechselt der Status zu **Erfolg**:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_06.png)

Mit dem Lösungsportal können Sie nach Geräten mit bestimmten Merkmalen wie z. B. einer Modellnummer suchen:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_07.png)

Sie können ein Gerät deaktivieren, und nachdem es deaktiviert wurde, können Sie es entfernen:

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_08.png)

<!---HONumber=AcomDC_0128_2016-->