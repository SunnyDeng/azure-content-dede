<properties
	pageTitle="Senden von Cloud-zu-Gerät-Nachrichten mit IoT Hub | Microsoft Azure"
	description="In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure IoT Hub mit C# Cloud-zu-Gerät-Nachrichten senden."
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

# Lernprogramm: Senden von Cloud-zu-Gerät-Nachrichten mit IoT Hub

## Einführung

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von IoT-Geräten und einem Anwendungs-Back-End ermöglicht. Im Lernprogramm [Erste Schritte mit IoT Hub] erfahren Sie, wie ein IoT Hub erstellt, eine Geräteidentität im Hub bereitgestellt und ein simuliertes Gerät programmiert wird, das Gerät-zu-Cloud-Nachrichten sendet.

Dieses Lernprogramm baut auf [Erste Schritte mit IoT Hub] auf und zeigt, wie Cloud-zu-Gerät-Nachrichten an ein einzelnes Gerät gesendet und Übermittlungsbestätigungen (*Feedback*) von IoT Hub angefordert und vom Cloud-Back-End der Anwendung empfangen werden.

Weitere Informationen zu Cloud-zu-Gerät-Nachrichten finden Sie im [Entwicklungsleitfaden für IoT Hub][IoT Hub Developer Guide - C2D].

Am Ende dieses Lernprogramms führen Sie zwei Windows-Konsolenanwendungen aus:

* **SimulatedDevice**, eine abgewandelte Version der in [Erste Schritte mit IoT Hub] erstellten App, die eine Verbindung mit IoT Hub herstellt und Cloud-zu-Gerät-Nachrichten empfängt.
* **SendCloudToDevice**, die über IoT Hub eine Cloud-zu-Gerät-Nachricht an das simulierte Gerät sendet und dann die zugehörige Übermittlungsbestätigung empfängt.

> [AZURE.NOTE]IoT Hub bietet durch Azure IoT-Geräte-SDKs Unterstützung für zahlreiche Geräteplattformen und Sprachen (u. a. C, Java und JavaScript). Im [Azure IoT Developer Center] finden Sie Schritt-für-Schritt-Anweisungen zum Verbinden eines Geräts mit dem Code in diesem Lernprogramm sowie allgemeine Informationen zum Verbinden mit Azure IoT Hub. Azure IoT-Dienst-SDKs für Java und Node sind in Kürze verfügbar.

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

+ Microsoft Visual Studio 2015

+ Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-DE%2Fdevelop%2Fiot%2Ftutorials%2Fc2d%2F target="\_blank").

[AZURE.INCLUDE [iot-hub-c2d-device-csharp](../../includes/iot-hub-c2d-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-c2d-cloud-csharp](../../includes/iot-hub-c2d-cloud-csharp.md)]

## Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, wie Cloud-zu-Gerät-Nachrichten gesendet und empfangen werden. In den folgenden Lernprogrammen werden weitere IoT Hub-Funktionen und -Szenarien vorgestellt:

- Unter [Verarbeiten von Gerät-zu-Cloud-Nachrichten] wird erläutert, wie Sie Telemetriedaten und interaktive Nachrichten von Geräten zuverlässig verarbeiten.
- Unter [Hochladen von Dateien von Geräten] wird ein Muster beschrieben, bei dem mithilfe von Cloud-zu-Gerät-Nachrichten Dateiuploads von Geräten ermöglicht werden.

Weitere Informationen zu IoT Hub:

* [Übersicht zu IoT Hub]
* [IoT Hub-Entwicklerhandbuch]
* [Anleitungen zu IoT Hub]
* [Unterstützte Geräteplattformen und Sprachen][Supported devices]
* [Azure IoT Developer Center]

<!-- Images. -->

<!-- Links -->

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

[Azure portal]: https://portal.azure.com/

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Verarbeiten von Gerät-zu-Cloud-Nachrichten]: iot-hub-csharp-csharp-process-d2c.md
[Hochladen von Dateien von Geräten]: iot-hub-csharp-csharp-file-upload.md

[Übersicht zu IoT Hub]: iot-hub-what-is-iot-hub.md
[Anleitungen zu IoT Hub]: iot-hub-guidance.md
[IoT Hub-Entwicklerhandbuch]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Erste Schritte mit IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT Developer Center]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_1203_2015-->