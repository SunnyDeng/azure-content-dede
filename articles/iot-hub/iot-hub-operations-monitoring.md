<properties
 pageTitle="IoT Hub-Vorgangsüberwachung"
 description="Eine Übersicht über die IoT Hub-Vorgangsüberwachung, die Benutzern das Überwachen des Status von Vorgängen auf ihrem IoT Hub in Echtzeit ermöglicht."
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

# Einführung in Vorgangsüberwachung

Die IoT Hub-Vorgangsüberwachung ermöglicht Benutzern das Überwachen des Status von Vorgängen auf ihrem IoT Hub in Echtzeit. IoT Hub verfolgt Ereignisse in mehreren Vorgangskategorien. Benutzer können festlegen, dass Ereignisse aus einer oder mehreren Kategorien zur Verarbeitung an einen Endpunkt ihres IoT Hubs gesendet werden. Benutzer können die Daten auf Fehler überwachen oder eine komplexere auf Datenmustern basierende Verarbeitung einrichten.

IoT Hub überwacht vier Ereigniskategorien:

- Geräte-Identitätsvorgänge
- Gerätetelemetrie
- C2D-Befehle
- Verbindungen

## Aktivieren der Vorgangsüberwachung

1. Erstellen Sie einen IoT Hub. Anweisungen zum Erstellen eines IoT Hubs finden Sie in der Anleitung mit den [ersten Schritten][lnk-get-started].

2. Öffnen Sie das Blatt Ihres IoT Hubs. Klicken Sie dort auf **Alle Einstellungen** und dann auf **Vorgangsüberwachung**.

    ![][1]

3. Wählen Sie die Vorgangskategorien aus, die Sie überwachen möchten, und klicken Sie dann auf **Speichern**. Die Ereignisse sind verfügbar für das Lesen aus dem Event Hub-kompatiblen Endpunkt, der in **Überwachungseinstellungen** aufgelistet ist.

    ![][2]

## Ereigniskategorien und ihre Verwendung

Jede Vorgangsüberwachungskategorie dient zum Nachverfolgen eines anderen Typs von Interaktion mit IoT Hub. Jede Überwachungskategorie hat ein Schema, das festlegt, wie Ereignisse in dieser Kategorie strukturiert sind.

### Geräte-Identitätsvorgänge

Die Kategorie für Geräte-Identitätsvorgänge verfolgt Fehler nach, die auftreten, wenn der Benutzer versucht, einen Eintrag in der Identitätsregistrierung seines IoT Hubs zu erstellen, zu aktualisieren oder zu löschen. Das Nachverfolgen dieser Kategorie ist für Bereitstellungsszenarien nützlich.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": “userAgent”,
         "sharedAccessPolicy": "accessPolicy"
    }

### Gerätetelemetrie

Die Kategorie für Gerätetelemetrie dient zum Nachverfolgen von Fehlern, die auf dem IoT Hub auftreten und in Beziehung zur Telemetriepipeline stehen. Dazu gehören Fehler, die beim Senden von Telemetrie-Ereignissen (z. B. Drosselung) und Empfangen von Telemetrie-Ereignissen (z. B. nicht autorisierte Leser) auftreten. Beachten Sie, dass diese Kategorie keine Fehler abfangen kann, die vom Code verursacht werden, der auf dem Gerät selbst ausgeführt wird.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{"scope":"device","type":"sas","issuer":"iothub"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### C2D-Befehle

Die Kategorie der C2D-Befehle dient zum Nachverfolgen von Fehlern, die auf dem IoT Hub auftreten und in Beziehung zur Gerätebefehls-Pipeline stehen. Dazu zählen Fehler, die beim Senden von Befehlen (z. B. nicht autorisierter Absender), Empfangen von Befehlen (Zustellungsanzahl überschritten) und Empfangen von Feedback zu Befehlen (z. B. Feedback abgelaufen) auftreten. Diese Kategorie fängt keine Fehler eines Geräts ab, das einen Befehl nicht ordnungsgemäß verarbeitet, wenn der Befehl erfolgreich übermittelt wurde.

    {
         "messageSizeInBytes": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### Verbindungen

Die Kategorie für Verbindungen verfolgt Fehler nach, wenn Geräte Verbindungen mit dem IoT Hub herstellen oder trennen. Das Nachverfolgen dieser Kategorie ist nützlich zum Bestimmen nicht autorisierter Verbindungsversuche und zum Nachverfolgen, wann eine Verbindung mit Geräten in Bereichen mit schlechter Konnektivität unterbrochen wird.

    {
         "durationMs": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

## Nächste Schritte

Nach diesem Überblick über die Vorgangsüberwachung können Sie die folgenden Links nutzen, um mehr über diese Themen zu erfahren:

- [IoT Hub-Diagnosemetriken][lnk-diagnostic-metrics]
- [Skalieren von IoT Hub][lnk-scaling]
- [Hohe Verfügbarkeit und Notfallwiederherstellung von IoT Hub][lnk-dr]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

<!---HONumber=AcomDC_0309_2016-->