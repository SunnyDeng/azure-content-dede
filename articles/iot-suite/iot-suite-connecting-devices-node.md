<properties
   pageTitle="Herstellen einer Geräteverbindung mit Node.js | Microsoft Azure"
   description="Hier erfahren Sie, wie Sie ein Gerät mit der vorkonfigurierten Remoteüberwachungslösung von Azure IoT Suite verbinden, indem Sie eine in Node.js geschriebene Anwendung ausführen."
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

## Erstellen und Ausführen der node.js-Beispiellösung

1. Um das GitHub-Repository *Microsoft Azure IoT-SDKs* zu klonen und das *Microsoft Azure IoT Geräte-SDK für Node.js* zu installieren, führen Sie in Ihrer Linux-Desktopumgebung die Anweisungen unter [Vorbereiten Ihrer Entwicklungsumgebung][lnk-github-prepare] aus.

2. Kopieren Sie in Ihrer lokalen Kopie des Repositorys [Azure-iot-sdks][lnk-github-repo] die folgenden beiden Dateien aus dem Ordner „node/device/samples“ in einen Ordner auf Ihrem Gerät:

  - packages.json
  - remote\_monitoring.js

3. Öffnen Sie die Datei „remote\_monitoring.js“, und suchen Sie nach der folgenden Variablen:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. Ersetzen Sie **[IoT Hub device connection string]** durch Ihre Geräteverbindungszeichenfolge. Die Werte für IoT Hub-Hostname, Geräte-ID und Geräteschlüssel finden Sie auf dem Dashboard der Remoteüberwachungslösung. Eine Geräteverbindungszeichenfolge hat folgendes Format:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

5. Wenn der IoT Hub-Hostname **contoso** und die Geräte-ID **mydevice** lautet, ergibt sich folgende Verbindungszeichenfolge:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

6. Speichern Sie die Datei. Führen Sie die folgenden Befehle im Ordner mit diesen Dateien aus:

    ```
    npm install
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-node-installers]: https://nodejs.org/download/
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/doc/devbox_setup.md

<!---HONumber=AcomDC_0128_2016-->