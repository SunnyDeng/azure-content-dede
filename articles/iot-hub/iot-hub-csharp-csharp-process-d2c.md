<properties
	pageTitle="Verarbeiten von D2C-Nachrichten mit IoT Hub | Microsoft Azure"
	description="In diesem Lernprogramm werden nützliche Verfahren zum Verarbeiten von Gerät-zu-Cloud-Nachrichten (Device-to-Cloud, D2C) mit IoT Hub beschrieben."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/03/2016"
     ms.author="dobett"/>

# Lernprogramm: Verarbeiten von D2C-Nachrichten mit IoT Hub

## Einführung

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von IoT-Geräten und einem Anwendungs-Back-End ermöglicht. Weitere Lernprogramme wie [Erste Schritte mit IoT Hub] und [Senden von C2D-Nachrichten mit IoT Hub] veranschaulichen, wie Sie die grundlegenden Device-to-Cloud- und Cloud-to-Device-Messagingfunktionen von IoT Hub verwenden.

Dieses Lernprogramm baut auf dem Code des Lernprogramms [Erste Schritte mit IoT Hub] auf und veranschaulicht zwei skalierbare Muster, die Sie zum Verarbeiten von D2C-Nachrichten verwenden können:

- Die zuverlässige Speicherung von Nachrichten von einem Gerät an die Cloud in [Azur Blob Storage]. Dies ist ein sehr häufiges Szenario bei der Implementierung der *Cold Path*-Analyse, bei der Sie Daten in Blobs speichern. Die Blobs werden als Eingabe für Analyseprozesse mit Tools wie [Azure Data Factory] oder dem [HDInsight (Hadoop)]-Stapel verwendet.

- Zuverlässige Verarbeitung von *interaktiven* D2C-Nachrichten: D2C-Nachrichten sind interaktiv, wenn es sich um unmittelbare Auslöser für eine Reihe von Aktionen im Anwendungs-Back-End handelt und nicht um *Datenpunkt*-Nachrichten, die an ein Analysemodul übertragen werden. Ein Alarm von einem Gerät, bei dem das Einfügen eines Tickets in ein CRM-System ausgelöst werden muss, ist beispielsweise eine interaktive D2C-Nachricht. Bei Telemetriedaten, z. B. Temperaturangaben, handelt es sich dagegen um eine Datenpunkt-D2C-Nachricht.

Da in IoT Hub ein Event Hubs-kompatibler Endpunkt zum Empfangen von D2C-Nachrichten verfügbar gemacht wird, wird in diesem Lernprogramm eine [EventProcessorHost]-Instanz verwendet, die Folgendes ermöglicht:

* Zuverlässiges Speichern von *Datenpunkt*-Nachrichten in Azure-Blobs
* Weiterleiten von *interaktiven* D2C-Nachrichten an eine [Service Bus-Warteschlange] zur sofortigen Verarbeitung

Service Bus ist eine hervorragende Möglichkeit, um die zuverlässige Verarbeitung von interaktiven Nachrichten sicherzustellen, da Checkpoints pro Nachricht und die Deduplizierung auf Grundlage von Zeitfenstern bereitgestellt werden.

Am Ende dieses Lernprogramms führen Sie drei Windows-Konsolenanwendungen aus:

* **SimulatedDevice**, eine geänderte Version der im Lernprogramm [Erste Schritte mit IoT Hub] erstellten App, die jede Sekunde Datenpunkt-D2C-Nachrichten und alle 10 Sekunden interaktive D2C-Nachrichten sendet.
* **ProcessDeviceToCloudMessages**: Verwendet die [EventProcessorHost]-Klasse zum Abrufen von Nachrichten vom Event Hub-kompatiblen Endpunkt und speichert Datenpunkt-Nachrichten dann zuverlässig in einem Azure-Blob und leitet interaktive Nachrichten an eine Service Bus-Warteschlange weiter.
* **ProcessD2CInteractiveMessages**: Entfernt die interaktiven Nachrichten aus der Service Bus-Warteschlange.

> [AZURE.NOTE] IoT Hub verfügt über SDK-Unterstützung für zahlreiche Geräteplattformen und Sprachen, z. B. C, Java und JavaScript. Schritt-für-Schritt-Anleitungen zum Ersetzen des simulierten Geräts in diesem Lernprogramm durch ein physisches Gerät und allgemeine Informationen zum Verbinden von Geräten mit Azure IoT Hub finden Sie im [Azure IoT Developer Center].

Dieses Lernprogramm kann direkt auf andere Möglichkeiten zum Verarbeiten von Event Hubs-kompatiblen Nachrichten übertragen werden, z. B. Projekte vom Typ [HDInsight (Hadoop)]. Weitere Informationen finden Sie unter [Entwicklungsleitfaden für Azure IoT Hub – Device to Cloud (D2C)].

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

+ Microsoft Visual Studio 2015.

+ Ein aktives Azure-Konto. <br/>Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Konto erstellen. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-DE%2Fdevelop%2Fiot%2Ftutorials%2Fprocess-d2c%2F target="\_blank").

Sie sollten über grundlegende Kenntnisse in Bezug auf [Azure Storage] und [Azure Service Bus] verfügen.


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## Ausführen der Anwendungen

Sie können jetzt die Anwendung ausführen.

1.	Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Projektmappe, und wählen Sie **Startprojekte festlegen**. Wählen Sie **Mehrere Startprojekte** aus, und wählen Sie dann **Starten** als Aktion für die Projekte **ProcessDeviceToCloudMessages**, **SimulatedDevice** und **ProcessD2CInteractiveMessages** aus.

2.	Drücken Sie **F5**, um die drei Konsolenanwendungen zu starten. Die Anwendung **ProcessD2CInteractiveMessages** sollte jede interaktive Nachricht verarbeiten, die von der Anwendung **SimulatedDevice** gesendet wird.

  ![][50]

> [AZURE.NOTE] Um in Ihrer Blobdatei Updates sehen zu können, müssen Sie unter Umständen die Konstante **MAX\_BLOCK\_SIZE** in der **StoreEventProcessor**-Klasse auf einen niedrigeren Wert festlegen, z. B. **1024**. Dies liegt daran, dass es bei den mit dem simulierten Gerät gesendeten Daten einige Zeit dauert, bis die Blockgröße erreicht wird. Bei einer kleineren Blockgröße müssen Sie nicht so lange warten, bis das Blob erstellt und aktualisiert wird. Allerdings sorgt die Verwendung einer höheren Blockgröße für eine bessere Skalierbarkeit der Anwendung.

## Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, wie Sie mit der [EventProcessorHost]-Klasse zuverlässig Datenpunkt-Nachrichten und interaktive D2C-Nachrichten verarbeiten.

Das Lernprogramm [Hochladen von Dateien von Geräten] baut auf diesem Lernprogramm auf. Es wird eine analoge Nachrichtenverarbeitungslogik verwendet, und es wird ein Muster beschrieben, bei dem C2D-Nachrichten zum Durchführen von Dateiuploads von Geräten eingesetzt werden.

Weitere Informationen zu IoT Hub:

* [Übersicht zu IoT Hub]
* [IoT Hub-Entwicklerhandbuch]
* [Anleitungen zu IoT Hub]
* [Unterstützte Geräteplattformen und Sprachen][Supported devices]
* [Azure IoT Developer Center]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Azur Blob Storage]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus-Warteschlange]: ../service-bus/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx



[Entwicklungsleitfaden für Azure IoT Hub – Device to Cloud (D2C)]: iot-hub-devguide.md#d2c

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/



[Senden von C2D-Nachrichten mit IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Hochladen von Dateien von Geräten]: iot-hub-csharp-csharp-file-upload.md

[Übersicht zu IoT Hub]: iot-hub-what-is-iot-hub.md
[Anleitungen zu IoT Hub]: iot-hub-guidance.md
[IoT Hub-Entwicklerhandbuch]: iot-hub-devguide.md
[Erste Schritte mit IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: iot-hub-tested-configurations.md
[Azure IoT Developer Center]: https://azure.microsoft.com/develop/iot

<!---HONumber=AcomDC_0309_2016-->