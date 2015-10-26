<properties
	pageTitle="Hochladen von Dateien von Geräten mit IoT Hub | Microsoft Azure"
	description="In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure IoT Hub mit C# Dateien von Geräten hochladen."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# Lernprogramm: Hochladen von Dateien von Geräten in die Cloud mit IoT Hub

## Einführung

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von IoT-Geräten und einem Anwendungs-Back-End ermöglicht. In vorhergehenden Lernprogrammen ([Erste Schritte mit IoT Hub] und [Senden von C2D-Nachrichten mit IoT Hub]) werden die grundlegenden Funktionen von IoT Hub für Gerät-zu-Cloud- (Device-to-Cloud, D2C) und Cloud-zu-Gerät-Messaging (Cloud-to-Device, C2D) und der Zugriff auf diese Funktionen über Geräte und Cloudkomponenten beschrieben. Unter [Verarbeiten von D2C-Nachrichten] wurde eine Möglichkeit für das zuverlässige Speichern von D2C-Nachrichten in Azure-Blob-Speicher beschrieben. Es gibt jedoch Fälle, in denen von Geräten empfangene Daten nicht problemlos relativ kleinen D2C-Nachrichten zugeordnet werden können. Beispiele sind große Dateien mit Bildern, Videos, Vibrationsdatenerfassungen mit hoher Frequenz oder vorverarbeiteten Daten. Diese Dateien werden normalerweise stapelweise mit Tools wie [Azure Data Factory] oder dem [Hadoop]-Stapel verarbeitet. Auch wenn das Hochladen einer Datei von einem Gerät dem Senden von Ereignissen vorgezogen wird, können trotzdem IoT Hub-Funktionen für Sicherheit und Zuverlässigkeit verwendet werden.

Dieses Lernprogramm baut auf dem Code in [Senden von C2D-Nachrichten mit IoT Hub] auf und zeigt, wie mit C2D-Nachrichten auf dem Gerät sicher ein Azure-Blob-URI zum Hochladen der Datei bereitgestellt werden kann und wie mit IoT Hub-Übermittlungsbestätigungen die Verarbeitung der Datei vom App-Back-End ausgelöst werden kann. Die Vorteile dieses Ansatzes liegen in der Wiederverwendung der IoT Hub-Geräteidentität. Der Vorteil der Übermittlungsbestätigung für C2D-Nachrichten liegt darin, dass das Anwendungs-Back-End darüber informiert wird, dass die Datei erfolgreich hochgeladen wurde.

> [AZURE.NOTE]Mit dem hier verwendeten Verfahren können Geräte auch sicher Dateien aus der Cloud herunterladen.

Weitere Informationen zu C2D-Nachrichten und zur IoT Hub-Sicherheit finden Sie im [IoT Hub-Entwicklerhandbuch].

Am Ende dieses Lernprogramms führen Sie zwei Windows-Konsolenanwendungen aus:

* **SimulatedDevice**, eine geänderte Version der in [Senden von C2D-Nachrichten mit IoT Hub] erstellten Anwendung. Diese stellt eine Verbindung mit IoT Hub her und empfängt C2D-Nachrichten mit Azure-Blob-URIs. Für jede empfangene C2D-Nachricht wird ein Dateiupload zum angegebenen Blob-URI ausgelöst.
* **SendCloudToDevice**. Hiermit wird ein Azure-Blob-URI (wie in [Erstellen und Verwenden einer SAS mit dem Blob-Dienst](../storage/storage-dotnet-shared-access-signature-part-2.md) beschrieben) erstellt, in einer C2D-Nachricht über IoT Hub an das simulierte Gerät gesendet und dann die Übermittlungsbestätigung empfangen.

> [AZURE.NOTE]IoT Hub verfügt durch Azure IoT-Geräte-SDKs über SDK-Unterstützung für zahlreiche Geräteplattformen und Sprachen (u. a. C, Java und Javascript). Im [Azure IoT Developer Center] finden Sie Schritt-für-Schritt-Anweisungen zum Verbinden eines Geräts mit dem Code in diesem Lernprogramm sowie allgemeine Informationen zum Verbinden mit Azure IoT Hub. Azure IoT-Dienst-SDKs für Java und Node sind in Kürze verfügbar.

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

+ Microsoft Visual Studio 2015

+ Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fiot%2Ftutorials%2Ffile-upload%2F target="\_blank").


[AZURE.INCLUDE [iot-hub-file-upload-cloud-csharp](../../includes/iot-hub-file-upload-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-file-upload-device-csharp](../../includes/iot-hub-file-upload-device-csharp.md)]

## Ausführen der Anwendungen

Sie können jetzt die Anwendung ausführen.

1.  Klicken Sie in Visual Studio mit der rechten Maustaste auf Ihre Projektmappe, und wählen Sie **Startprojekte festlegen** aus. Wählen Sie **Mehrere Startprojekte** aus, und wählen Sie dann die Aktion **Starten** für die Anwendungen **SimulatedDevice** und **SendCloudToDevice** aus.

2.  Drücken Sie **F5**. Alle Anwendungen sollten gestartet werden. Wählen Sie das Fenster **SendCloudToDevice** aus, und drücken Sie eine Taste. Sie sehen, dass das simulierte Gerät eine Nachricht ausgibt, wenn die Datei hochgeladen wurde. Die Anwendung **SendCloudToDevice** zeigt den erfolgreichen Empfang des Feedbacks an. Sie können im [Azure-Vorschauportal] oder in Visual Studio Server-Explorer überprüfen, ob die Datei in Ihrem Speicherkonto vorhanden ist.

  ![][50]


## Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, mithilfe von C2D-Nachrichten Dateiuploads von Geräten zu vereinfachen. Im folgenden Lernprogramm werden weitere Funktionen und Szenarios für IoT Hubs vorgestellt:

- In [Verarbeiten von D2C-Nachrichten] wird erläutert, wie Sie zuverlässig Telemetriedaten und interaktive Nachrichten von Geräten verarbeiten können.

Weitere Informationen zu IoT Hub:

* [Übersicht zu IoT Hub]
* [IoT Hub-Entwicklerhandbuch]
* [Anleitungen zu IoT Hub]
* [Unterstützte Geräteplattformen und Sprachen][Supported devices]
* [Azure IoT Developer Center]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png

<!-- Links -->

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md

[Azure-Vorschauportal]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/en-us/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/en-us/documentation/services/hdinsight/

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Senden von C2D-Nachrichten mit IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Verarbeiten von D2C-Nachrichten]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[Übersicht zu IoT Hub]: iot-hub-what-is-iot-hub.md
[Anleitungen zu IoT Hub]: iot-hub-guidance.md
[IoT Hub-Entwicklerhandbuch]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Erste Schritte mit IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT Developer Center]: http://www.azure.com/develop/iot

<!---HONumber=Oct15_HO3-->