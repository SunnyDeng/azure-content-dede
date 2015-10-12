<properties
	pageTitle="Erste Schritte mit IoT Hub | Microsoft Azure"
	description="In diesem Lernprogramm werden die ersten Schritte für die Verwendung von Azure IoT Hub mit C# beschrieben."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="kevinmil"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# Erste Schritte mit IoT Hub

## Einführung

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von IoT-Geräten und einem Anwendungs-Back-End ermöglicht. Eines der größten Probleme im Zusammenhang mit IoT-Projekten ist die sichere und zuverlässige Verbindung von Geräten mit dem Back-End der Anwendung. Um diesen Fall zu vereinfachen, bietet Azure IoT Hub zuverlässiges Gerät-zu-Cloud- (Device-to-Cloud, D2C) und Cloud-zu-Gerät-Messaging (Cloud-to-Device, C2D) mit Hyper-Skalierung, ermöglicht die sichere Kommunikation durch gerätespezifische Sicherheitsanmeldeinformationen und Zugriffssteuerung und umfasst Gerätebibliotheken für die am häufigsten verwendeten Sprachen und Plattformen.

Dieses Lernprogramm zeigt, wie Sie mit dem Azure-Portal einen IoT Hub erstellen. Außerdem wird beschrieben, wie Sie eine Geräteidentität in Ihrem IoT Hub erstellen, ein simuliertes Gerät zum Senden von D2C-Nachrichten erstellen und diese Nachrichten vom Cloud-Back-End empfangen.

Am Ende dieses Lernprogramms haben Sie drei Windows-Konsolenanwendungen erstellt:

* **CreateDeviceIdentity**. Hiermit werden eine Geräteidentität und ein zugehöriger Sicherheitsschlüssel zum Verbinden mit dem simulierten Gerät erstellt.
* **ReadDeviceToCloudMessages**. Hiermit werden D2C-Nachrichten gelesen und ihr Inhalt angezeigt.
* **SimulatedDevice**. Hiermit wird mithilfe der zuvor erstellten Geräteidentität eine Verbindung mit Ihrem IoT Hub hergestellt und jede Sekunde eine D2C-Nachricht gesendet.

> [AZURE.NOTE]IoT Hub verfügt durch Azure IoT-Geräte-SDKs über SDK-Unterstützung für zahlreiche Geräteplattformen und Sprachen (u. a. C, Java und Javascript). Im [Azure IoT Developer Center] finden Sie Schritt-für-Schritt-Anweisungen zum Verbinden eines Geräts mit dem Code in diesem Lernprogramm sowie allgemeine Informationen zu Azure IoT Hub. Azure IoT-Dienst-SDKs für Java und Node sind in Kürze verfügbar.

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

+ Microsoft Visual Studio 2015

+ Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-DE%2Fdevelop%2Fiot%2Ftutorials%2Fgetstarted%2F target="\_blank").

## Erstellen eines IoT Hubs

1. Melden Sie sich beim [Azure-Vorschauportal] an.

2. Klicken Sie in der Jumpbar auf **Neu**, klicken Sie dann auf **Internet der Dinge**, und klicken Sie dann auf **IoT Hub**.

   	![][1]

3. Geben Sie auf dem Blatt **Neuer IoT Hub** die gewünschte Konfiguration für den IoT Hub an.

   	![][2]

    * Geben Sie im Feld **Name** einen Namen zur Identifizierung Ihres IoT Hubs ein. Wenn der Wert für **Name** überprüft wurde, wird im Feld **Name** ein grünes Häkchen angezeigt.
    * Ändern Sie die **Preis- und Skalierungsstufen** nach Bedarf. Für dieses Lernprogramm ist keine bestimmte Stufe erforderlich.
    * Erstellen Sie im Feld **Ressourcengruppe** eine neue Ressourcengruppe, oder wählen Sie eine vorhandene Ressourcengruppe aus. Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](resource-group-portal.md).
    * Unter **Standort** können Sie einen geografischen Standort angeben, an dem Ihr IoT Hub gehostet wird.  


4. Klicken Sie nach der Konfiguration der Optionen für den neuen IoT Hub auf **Erstellen**. Die Erstellung des IoT Hubs kann einige Minuten dauern. Sie können den Fortschritt über das Startmenü überwachen, um den Status zu überprüfen. Sie können den Status auch im Abschnitt "Benachrichtigungen" überwachen.

    ![][3]


5. Nachdem der IoT Hub erfolgreich erstellt wurde, öffnen Sie das Blatt für den neuen IoT Hub, notieren Sie den URI, und wählen Sie das **Schlüsselsymbol** oben aus.

   	![][4]

6. Wählen Sie die Richtlinie für den freigegebenen Zugriff **Iothubowner** aus, und kopieren und notieren Sie die Verbindungszeichenfolge auf dem Blatt rechts.

   	![][5]

Der IoT Hub wurde erstellt, und Ihnen liegen der URI und die Verbindungszeichenfolge vor, die Sie für dieses Lernprogramm benötigen.

[AZURE.INCLUDE [iot-hub-get-started-cloud-csharp](../../includes/iot-hub-get-started-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-csharp](../../includes/iot-hub-get-started-device-csharp.md)]

## Ausführen der Anwendungen

Sie können jetzt die Anwendung ausführen.

1.	Klicken Sie in Visual Studio mit der rechten Maustaste auf Ihre Projektmappe, und wählen Sie **Startprojekte festlegen** aus. Wählen Sie **Mehrere Startprojekte** aus, und wählen Sie dann die Aktion **Starten** für die Anwendungen **ProcessDeviceToCloudMessages** und **SimulatedDevice** aus.

   	![][41]

2.	Drücken Sie **F5**. Sie sollten sehen, dass beide Anwendungen gestartet werden und dass die Nachrichten von der simulierten Anwendung gesendet und von der Prozessor-Anwendung empfangen werden.

   	![][42]

## Nächste Schritte

In diesem Lernprogramm haben Sie einen neuen IoT Hub eingerichtet, eine Geräteidentität in der Identitätsregistrierung des Hubs erstellt und mit dieser Identität ein simuliertes Gerät programmiert, das Gerät-zu-Cloud-Nachrichten (Device-to-Cloud, D2C) sendet. In den folgenden Lernprogrammen werden weitere Funktionen und Szenarios für IoT Hubs vorgestellt:

- Unter [Senden von C2D-Nachrichten mit IoT Hub] wird erläutert, wie Sie Nachrichten an Geräte senden und das von IoT Hub generierte Feedback zur Übermittlung verarbeiten.
- In [Verarbeiten von D2C-Nachrichten] wird erläutert, wie Sie zuverlässig Telemetriedaten und interaktive Nachrichten von Geräten verarbeiten können.
- In [Hochladen von Dateien von Geräten] wird beschrieben, wie mithilfe von C2D-Nachrichten Dateiuploads von Geräten erleichtert werden.

Weitere Informationen zu IoT Hub:

* [Übersicht zu IoT Hub]
* [IoT Hub-Entwicklerhandbuch]
* [Anleitungen zu IoT Hub]
* [Unterstützte Geräteplattformen und Sprachen][Supported devices]
* [Azure IoT Developer Center]

<!-- Images. -->
[1]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub5.png

[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png

<!-- Links -->
[Azure-Vorschauportal]: https://portal.azure.com/

[Senden von C2D-Nachrichten mit IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Verarbeiten von D2C-Nachrichten]: iot-hub-csharp-csharp-process-d2c.md
[Hochladen von Dateien von Geräten]: iot-hub-csharp-csharp-file-upload.md

[Übersicht zu IoT Hub]: iot-hub-what-is-iot-hub.md
[Anleitungen zu IoT Hub]: iot-hub-guidance.md
[IoT Hub-Entwicklerhandbuch]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT Developer Center]: http://www.azure.com/iotdev



 

<!---HONumber=Oct15_HO1-->