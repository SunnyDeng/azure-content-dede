<properties
   pageTitle="Verbinden eines Geräts mit C unter Windows | Microsoft Azure"
   description="Beschreibt, wie Sie ein Gerät mit der vorkonfigurierten Remoteüberwachungslösung von Azure IoT Suite verbinden, indem Sie eine in C geschriebene Anwendung unter Windows ausführen."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="dobett"/>


# Verbinden Ihres Geräts mit der vorkonfigurierten Remoteüberwachungslösung von IoT Suite

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Erstellen und Ausführen der C-Beispielprojektmappe unter Windows

1. Um das GitHub-Repository *Microsoft Azure IoT-SDKs* zu klonen und das *Microsoft Azure IoT Geräte-SDK für C* zu installieren, führen Sie in Ihrer Windows-Desktopumgebung die Anweisungen unter [Einrichten einer Windows-Entwicklungsumgebung][lnk-setup-windows] aus.

2. Öffnen Sie in Visual Studio 2015 die Projektmappe **remote\_monitoring.sln** aus dem Ordner **c\\serializer\\samples\\remote\_monitoring\\windows** der lokalen Repositorykopie.

3. Öffnen Sie im Projektmappen-Explorer im Projekt **remote\_monitoring** die Datei **remote\_monitoring.c**.

4. Suchen Sie in der Datei nach dem folgenden Code:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

5. Ersetzen Sie **[Device Id]** und **[Device Key]** durch die Gerätewerte aus dem Dashboard der Remoteüberwachungslösung.

6. Ersetzen Sie **[IoTHub Name]** und **[IoTHub Suffix, i.e. azure-devices.net]** durch den IoT Hub-Hostnamen aus dem Dashboard. Wenn Ihr IoT Hub-Hostname also beispielsweise **contoso.azure devices.net** lautet, ersetzen Sie **[IoTHub Name]** durch **contoso** und **[IoTHub Suffix, i.e. azure-devices.net]** durch **azure-devices.net**, wie hier zu sehen:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

7. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **remote\_monitoring**, klicken Sie auf **Debuggen**, und klicken Sie dann auf **Neue Instanz starten**, um das Beispiel zu erstellen und auszuführen. In der Konsole werden Nachrichten angezeigt, während die Anwendung Telemetriebeispieldaten an IoT Hub sendet.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-windows]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows

<!---HONumber=AcomDC_0128_2016-->