<properties
	pageTitle="Verarbeiten von D2C-Nachrichten mit IoT Hub | Microsoft Azure"
	description="In diesem Lernprogramm werden nützliche Verfahren zum Verarbeiten von Gerät-zu-Cloud-Nachrichten (Device-to-Cloud, D2C) mit IoT Hub beschrieben."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# Lernprogramm: Verarbeiten von D2C-Nachrichten mit IoT Hub

## Einführung

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von IoT-Geräten und einem Anwendungs-Back-End ermöglicht. In vorhergehenden Lernprogrammen ([Erste Schritte mit IoT Hub] und [Senden von C2D-Nachrichten mit IoT Hub]) werden die grundlegenden Funktionen von IoT Hub für Gerät-zu-Cloud- (Device-to-Cloud, D2C) und Cloud-zu-Gerät-Messaging (Cloud-to-Device, C2D) und der Zugriff auf diese Funktionen über Geräte und Cloudkomponenten beschrieben.

Dieses Lernprogramm baut auf den Code in [Erste Schritte mit IoT Hub] auf, in dem zwei Verfahren zum Verarbeiten von D2C-Nachrichten beschrieben werden.

Bei dem ersten Verfahren handelt es sich um das zuverlässige Speichern von D2C-Nachrichten in [Azure-Blobs]. Dieses Szenario kommt sehr häufig bei der Implementierung von *cold-path*-Analysen vor, wenn in Blobs gespeicherte Daten als Eingabe für Analysen mit Tools wie [Azure Data Factory] oder dem [Hadoop]-Stapel verwendet werden.

Bei dem zweiten Verfahren handelt es sich um die zuverlässige Verarbeitung der *interaktiven* D2C-Nachrichten. D2C-Nachrichten werden als *interaktiv* bezeichnet, wenn es sich um unmittelbare Auslöser für eine Reihe von Aktionen im Anwendungs-Back-End handelt und nicht um eine *Datenpunkt*-Nachricht, die an ein Analysemodul übertragen wird. Bei einem Alarm von einem Gerät, der das Einfügen eines Tickets in einem CRM-System auslöst, handelt es sich z. B. um eine *interaktive* D2C-Nachricht, während eine Telemetrienachricht mit Temperaturmesswerten eine *Datenpunkt*-Nachricht darstellt.

Da in IoT Hub ein Event Hubs-kompatibler Endpunkt zum Empfangen von D2C-Nachrichten verfügbar gemacht wird, wird in diesem Lernprogramm mit [EventProcessorHost] eine Ereignisprozessorklasse mit folgenden Merkmalen gehostet:

* Zuverlässiges Speichern von *Datenpunkt*-Nachrichten in Azure-Blobs und
* Weiterleiten von *interaktiven* D2C-Nachrichten an eine [Service Bus-Warteschlange] zur sofortigen Verarbeitung.

[Service Bus][Service Bus Queue] ist eine hervorragende Möglichkeit, um die zuverlässige Verarbeitung von interaktiven Nachrichten sicherzustellen, da Checkpoints pro Nachricht und die Deduplizierung auf Grundlage von Zeitfenstern bereitgestellt werden.

Am Ende dieses Lernprogramms führen Sie drei Windows-Konsolenanwendungen aus:

* **SimulatedDevice**, eine geänderte Version der in [Erste Schritte mit IoT Hub] erstellten Anwendung, die jede Sekunde *Datenpunkt*-Nachrichten vom Gerät in die Cloud und alle 10 Sekunden *interaktive* D2C-Nachrichten sendet.
* **ProcessDeviceToCloudMessages**. Hierbei werden mit [EventProcessorHost] zuverlässig *Datenpunkt*-Nachrichten in einem Azure-Blob gespeichert und *interaktive* Nachrichten an eine Service Bus-Warteschlange weitergeleitet.
* **ProcessD2cInteractiveMessages**. Hiermit werden Nachrichten aus der Warteschlange entfernt.

> [AZURE.NOTE]IoT Hub verfügt durch Azure IoT-Geräte-SDKs über SDK-Unterstützung für zahlreiche Geräteplattformen und Sprachen (u. a. C, Java und Javascript). Im [Azure IoT Developer Center] finden Sie Schritt-für-Schritt-Anweisungen zum Verbinden eines Geräts mit dem Code in diesem Lernprogramm sowie allgemeine Informationen zum Verbinden mit Azure IoT Hub.

> [AZURE.NOTE]Der Inhalt dieses Lernprogramms kann direkt auf andere Möglichkeiten zum Verarbeiten Event Hubs-kompatibler Nachrichten übertragen werden, z. B. [Hadoop]-Projekte wie Storm. Weitere Informationen finden Sie unter [Informationen zu IoT Hub – Kompatibilität mit Event Hubs].

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

+ Microsoft Visual Studio 2015

+ Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fiot%2Ftutorials%2Fprocess-d2c%2F target="\_blank").

Außerdem wird davon ausgegangen, dass Sie über Kenntnisse zu [Azure Storage] und [Azure Service Bus] verfügen.


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## Ausführen der Anwendungen

Sie können jetzt die Anwendung ausführen.

1.	Klicken Sie in Visual Studio mit der rechten Maustaste auf Ihre Projektmappe, und wählen Sie **Startprojekte festlegen** aus. Wählen Sie **Mehrere Startprojekte** aus, und wählen Sie dann die Aktion **Starten** für die Anwendungen **ProcessDeviceToCloudMessages**, **SimulatedDevice** und **ProcessD2cInteractiveMessages** aus.

2.	Drücken Sie **F5**. Sie sollten nun sehen, dass alle Anwendungen gestartet werden, und alle vom simulierten Gerät gesendeten interaktiven Nachrichten sollten vom interaktiven Nachrichtenprozessor verarbeitet werden.

  ![][50]

> [AZURE.NOTE]Damit Sie sehen können, wie die Blob-Datei aktualisiert wird, müssen Sie möglicherweise die `MAX_BLOCK_SIZE`-Konstante in `StoreEventProcessor` auf einen kleinen Wert reduzieren (d. h. `1024`). Dies liegt daran, dass es bei den mit dem simulierten Gerät gesendeten Daten einige Zeit dauern kann, bis die Blockgröße erreicht wird. Nach dieser Änderung sollten Sie sehen, dass im Speichercontainer ein Blob erstellt und aktualisiert wird.

## Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, wie Sie mit [EventProcessorHost] zuverlässig *Datenpunkt*- und *interaktive* D2C-Nachrichten verarbeiten. Eine analoge Logik für die Nachrichtenverarbeitung kann implementiert werden mit:

- In [Hochladen von Dateien von Geräten] wird beschrieben, wie mithilfe von C2D-Nachrichten Dateiuploads von Geräten erleichtert werden.

Weitere Informationen zu IoT Hub:

* [Übersicht zu IoT Hub]
* [IoT Hub-Entwicklerhandbuch]
* [Anleitungen zu IoT Hub]
* [Unterstützte Geräteplattformen und Sprachen][Supported devices]
* [Azure IoT Developer Center]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Azure-Blobs]: https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/
[Azure Data Factory]: https://azure.microsoft.com/en-us/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/en-us/documentation/services/hdinsight/
[Service Bus Queue]: https://azure.microsoft.com/en-us/documentation/articles/service-bus-dotnet-how-to-use-queues/
[Service Bus-Warteschlange]: https://azure.microsoft.com/en-us/documentation/articles/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Transient Fault Handling]: https://msdn.microsoft.com/en-us/library/hh680901(v=pandp.50).aspx

[Informationen zu IoT Hub – Kompatibilität mit Event Hubs]: iot-hub-guidance.md#eventhubcompatible

[Azure Storage]: https://azure.microsoft.com/en-us/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/en-us/documentation/services/service-bus/

[Azure Preview Portal]: https://portal.azure.com/

[Senden von C2D-Nachrichten mit IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Hochladen von Dateien von Geräten]: iot-hub-csharp-csharp-file-upload.md

[Übersicht zu IoT Hub]: iot-hub-what-is-iot-hub.md
[Anleitungen zu IoT Hub]: iot-hub-guidance.md
[IoT Hub-Entwicklerhandbuch]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Erste Schritte mit IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT Developer Center]: http://www.azure.com/develop/iot

<!---HONumber=Oct15_HO3-->