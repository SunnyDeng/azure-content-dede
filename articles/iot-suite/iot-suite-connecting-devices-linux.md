<properties
   pageTitle="Anschließen eines Geräts mit C unter Linux | Microsoft Azure"
   description="Es wird beschrieben, wie Sie ein Gerät mit der vorkonfigurierten Remoteüberwachungslösung von Azure IoT Suite verbinden, indem Sie eine in C geschriebene Anwendung unter Linux ausführen."
   services=""
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="dobett"/>


# Verbinden Ihres Geräts mit der vorkonfigurierten Remoteüberwachungslösung von IoT Suite

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Erstellen und Ausführen der C-Beispielprojektmappe unter Linux

1. Um das GitHub-Repository *Microsoft Azure IoT-SDKs* zu klonen und das *Microsoft Azure IoT Geräte-SDK für C* zu installieren, führen Sie in Ihrer Linux-Desktopumgebung die Anweisungen unter [Einrichten einer Linux-Entwicklungsumgebung][lnk-setup-linux] aus.

2. Öffnen Sie die Datei **c/serializer/samples/remote\_monitoring.c** in einem Text-Editor.

3. Suchen Sie in der Datei nach dem folgenden Code:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

4. Ersetzen Sie **[Geräte-Id]** und **[Geräteschlüssel]** durch die Werte für Ihr Gerät aus dem Dashboard der Remoteüberwachungslösung.

5. Verwenden Sie den Wert für **IoT Hub Hostname** aus dem Dashboard, um **[IoTHub Name]** und **[IoTHub Suffix, d. h. azure-devices.net]** zu ersetzen. Wenn Ihr Wert für **IoT Hub Hostname** beispielsweise **contoso.azure devices.net** lautet, dann ersetzen Sie **[IoTHub Name]** durch **contoso** und **[IoTHub Suffix, d. h. azure-devices.net]** durch **azure-devices.net**, wie unten dargestellt:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. Speichern Sie die Änderungen, und erstellen Sie die Beispiele. Zum Erstellen des Beispiels können Sie das Skript „build.sh“ im Verzeichnis **c/build\_all/linux** ausführen. Das Skript erstellt den Ordner **cmake** zum Speichern der kompilierten Beispielprogramme.

7. Führen Sie die Beispielanwendung **cmake/serializer/samples/remote\_monitoring/remote\_monitoring** aus.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-linux]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux

<!---HONumber=Nov15_HO4-->