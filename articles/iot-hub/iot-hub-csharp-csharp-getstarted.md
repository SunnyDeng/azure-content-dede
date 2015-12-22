<properties
	pageTitle="Erste Schritte mit Azure IoT Hub | Microsoft Azure"
	description="In diesem Lernprogramm werden die ersten Schritte für die Verwendung von Azure IoT Hub mit C# beschrieben."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="12/14/2015"
     ms.author="dobett"/>

# Tutorial: Erste Schritte mit Azure IoT Hub

## Einführung

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von IoT-Geräten und einem Lösungs-Back-End ermöglicht. Eines der größten Probleme im Zusammenhang mit IoT-Projekten ist die sichere und zuverlässige Verbindung von Geräten mit dem Lösungs-Back-End. Um diese Herausforderungen zu meistern, bietet IoT Hub:

- Ein zuverlässiges, hyperskalierbares Messaging zwischen Geräten und Cloud (Device-to-Cloud, D2C) sowie zwischen Cloud und Geräten (Cloud-to-Device, C2D)
- Eine sichere Kommunikation unter Verwendung von Zugriffssteuerung und Sicherheitsanmeldeinformationen auf Gerätebasis
- Gerätebibliotheken für die gängigsten Sprachen und Plattformen

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

- Erstellen eines IoT Hubs mit dem Azure-Portal
- Erstellen einer Geräteidentität im IoT Hub
- Erstellen eines simulierten Geräts, das Telemetrie an das Cloud-Back-End sendet und vom Cloud-Back-End Befehle empfängt

Am Ende dieses Tutorials verfügen Sie über drei Windows-Konsolenanwendungen:

* **CreateDeviceIdentity**. Hiermit werden eine Geräteidentität und ein zugehöriger Sicherheitsschlüssel zum Verbinden mit Ihrem simulierten Gerät erstellt.
* **ReadDeviceToCloudMessages**. Hiermit wird die Telemetrie angezeigt, die Ihr simuliertes Gerät sendet.
* **SimulatedDevice**. Hiermit wird mithilfe der zuvor erstellten Geräteidentität eine Verbindung mit Ihrem IoT Hub hergestellt und jede Sekunde eine Telemetrienachricht gesendet.

> [AZURE.NOTE]Im Artikel [IoT Hub-SDKs][lnk-hub-sdks] finden Sie Informationen über die verschiedenen SDKs, mit denen Sie sowohl Anwendungen für Geräte als auch das zugehörige Lösungs-Back-End erstellen können.

Für dieses Tutorial benötigen Sie Folgendes:

+ Microsoft Visual Studio 2015.

+ Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][lnk-free-trial].

## Erstellen eines IoT Hubs

Sie müssen einen IoT Hub erstellen, mit dem Ihr simuliertes Gerät verbunden werden kann. Die folgenden Schritte veranschaulichen, wie Sie diese Aufgabe mit dem Azure-Portal ausführen.

1. Melden Sie sich beim [Azure-Portal][lnk-portal] an.

2. Klicken Sie in der Navigationsleiste auf **Neu**, klicken Sie auf **Internet der Dinge** und dann auf **Azure IoT Hub**.

   	![][1]

3. Wählen Sie auf dem Blatt **IoT Hub** die gewünschte Konfiguration für Ihren IoT Hub.

   	![][2]

    * Geben Sie im Feld **Name** einen Namen für Ihren IoT Hub ein. Wenn der **Name** gültig und verfügbar ist, wird im Feld **Name** ein grünes Häkchen angezeigt.
    * Wählen Sie eine **Preis- und Skalierungsstufe** aus. Für dieses Tutorial ist keine bestimmte Stufe erforderlich.
    * Erstellen Sie in **Ressourcengruppe** eine neue Ressourcengruppe, oder wählen Sie eine vorhandene aus. Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen][lnk-resource-groups].
    * Wählen Sie in **Standort** den Standort aus, an dem Ihr IoT Hub gehostet werden soll.  

4. Wenn Sie die Konfigurationsoptionen für Ihren IoT Hub ausgewählt haben, klicken Sie auf **Erstellen**. Die Erstellung des IoT Hubs kann einige Minuten dauern. Im Startmenü oder im Benachrichtigungsbereich können Sie den Fortschritt überwachen und den Status überprüfen.

    ![][3]

5. Nachdem der IoT Hub erfolgreich erstellt wurde, öffnen Sie das Blatt für den neuen IoT Hub, notieren Sie den **Hostnamen**, und klicken Sie dann auf das **Schlüsselsymbol**.

   	![][4]

6. Klicken Sie auf die Richtlinie **iothubowner**, und kopieren Sie oder notieren Sie die Verbindungszeichenfolge im Blatt **iothubowner**.

   	![][5]

Sie haben nun Ihren IoT Hub erstellt und verfügen über den Hostnamen und die Verbindungszeichenfolge, die Sie für die weiteren Schritte in diesem Tutorial benötigen.

[AZURE.INCLUDE [iot-hub-get-started-cloud-csharp](../../includes/iot-hub-get-started-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-csharp](../../includes/iot-hub-get-started-device-csharp.md)]

## Ausführen der Anwendungen

Sie können nun die Anwendungen ausführen.

1.	Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Projektmappe, und klicken Sie dann auf **Startprojekte festlegen**. Wählen Sie **Mehrere Startprojekte** aus, und wählen Sie dann für die Projekte **ReadDeviceToCloudMessages** und **SimulatedDevice** als **Aktion** die Option **Starten** aus.

   	![][41]

2.	Drücken Sie **F5**, um die Ausführung der beiden Apps zu starten. Die Konsolenausgabe der App **SimulatedDevice** zeigt die Nachrichten, die Ihr simuliertes Gerät an Ihren IoT Hub sendet, und die Konsolenausgabe der App **ReadDeviceToCloudMessages** zeigt die Nachrichten, die Ihr IoT Hub empfängt.

   	![][42]

## Nächste Schritte

In diesem Tutorial haben Sie im Portal einen neuen IoT Hub konfiguriert und anschließend in der Identitätsregistrierung des Hubs eine Geräteidentität erstellt. Sie haben diese Geräteidentität in einem simulierten Gerät verwendet, das D2C-Nachrichten (Device-to-Cloud, Gerät-an-Cloud) an den Hub sendet, und Sie haben eine weitere App erstellt, die die vom Hub empfangenen Nachrichten anzeigt. In den folgenden Tutorials werden weitere Features und Szenarien für IoT Hubs vorgestellt:

- Unter [Senden von C2D-Nachrichten mit IoT Hub][lnk-c2d-tutorial] wird erläutert, wie Sie Nachrichten an Geräte senden und das von IoT Hub generierte Feedback zur Übermittlung verarbeiten.
- In [Verarbeiten von D2C-Nachrichten][lnk-process-d2c-tutorial] wird erläutert, wie Sie zuverlässig Telemetriedaten und interaktive Nachrichten von Geräten verarbeiten können.
- In [Hochladen von Dateien von Geräten][lnk-upload-tutorial] wird beschrieben, wie mithilfe von C2D-Nachrichten Dateiuploads von Geräten erleichtert werden.

In den folgenden Artikeln finden Sie weitere Informationen zu IoT Hub:

* [Übersicht zu IoT Hub](lnk-hub-overview)
* [IoT Hub-Entwicklerhandbuch](lnk-hub-dev-guide)
* [Entwerfen einer IoT Hub-Lösung](lnk-hub-guidance)
* [Unterstützte Geräteplattformen und Sprachen](lnk-supported-devices)
* [Azure IoT Developer Center](lnk-dev-center)

<!-- Images. -->
[1]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub5.png
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png

<!-- Links -->
[Senden von C2D-Nachrichten mit IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Verarbeiten von Gerät-zu-Cloud-Nachrichten]: iot-hub-csharp-csharp-process-d2c.md
[Hochladen von Dateien von Geräten]: iot-hub-csharp-csharp-file-upload.md

[Übersicht zu IoT Hub]: iot-hub-what-is-iot-hub.md
[IoT Hub – Anleitung]: iot-hub-guidance.md
[IoT Hub-Entwicklerhandbuch]: iot-hub-devguide.md
[lnk-supported-devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-dev-center]: http://www.azure.com/develop/iot
[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-resource-groups]: resource-group-portal.md
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_1217_2015-->