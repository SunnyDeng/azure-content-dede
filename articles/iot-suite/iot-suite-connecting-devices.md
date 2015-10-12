<properties
   pageTitle="Verbinden Ihres Geräts mit der vorkonfigurierten Remoteüberwachungslösung von Azure IoT Suite | Microsoft Azure"
   description="Es wird beschrieben, wie Sie Ihr Gerät mit der vorkonfigurierten Remoteüberwachungslösung von Azure IoT Suite verbinden, indem Sie ein Beispiel mit Daten zur Temperatur und Luftfeuchtigkeit verwenden."
   services="iot-hub"
   documentationCenter="na"
   authors="hegate"
   manager="jamesosb"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/29/2015"
   ms.author="hegate"/>


# Verbinden Ihres Geräts mit der Remoteüberwachungslösung von Azure IoT Suite


## Übersicht über das Szenario

In diesem Beispiel verwenden wir drei simulierte Datenquellen: Außentemperatur, Innentemperatur und Luftfeuchtigkeit. Aus Gründen der Einfachheit verwenden wir keine echten Sensoren (die Daten werden im Clientcode selbst generiert), aber als nächsten Schritt empfehlen wir Ihnen, Ihren bevorzugten Sensor zu verbinden und echte Daten zu senden. Weitere Informationen finden Sie unter den Links zu den Ressourcen, die im Abschnitt „Erweitern der Lösung“ angegeben sind.

## Voraussetzungen

### Bereitstellen der IoT Suite

Falls Sie Ihre vorkonfigurierte Lösung für die Remoteüberwachung noch nicht bereitgestellt haben, können Sie dies [hier](www.internetofyourthings.com) durchführen.


### Bereitstellen des Geräts in der Remoteüberwachungslösung
```
Note: if you have already provisioned a device on your solution, you can skip this step.
```
Um das Gerät mit der vorkonfigurierten Lösung zu verbinden, müssen Sie die Anmeldeinformationen des Geräts aus dem Dashboard abrufen. Diese werden in der Clientanwendung verwendet, um das Gerät zu identifizieren. Führen Sie die folgenden Schritte aus:

1.  Klicken Sie in der linken unteren Ecke des Dashboards auf „Gerät hinzufügen“. ![][1]
2.  Klicken Sie unter „Benutzerdefiniertes Gerät“ auf die Schaltfläche „Neu hinzufügen“.

    ![][2]
3.  Wählen Sie Ihre eigene Geräte-ID aus, indem Sie einen Namen eingeben, z. B. realdevice1, und klicken Sie auf „ID überprüfen“, um sicherzustellen, dass der Name nicht bereits verwendet wird. ![][3]

5. Kopieren Sie die bereitgestellten Anmeldeinformationen (Geräte-ID, IoT Hub-Hostname und Geräteschlüssel). Sie benötigen sie später in der Clientanwendung, um das Gerät mit der Lösung zu verbinden. ![][4]
6. Stellen Sie sicher, dass das Gerät im Abschnitt mit den Geräten richtig angezeigt wird. Der Status lautet „Ausstehend“. Dies ist der erwartungsgemäße Status, bis die Verbindung vom Gerät zur Cloud hergestellt wird.

    ![][5]

[1]: ./media/iot-suite-connecting-devices/suite0.png
[2]: ./media/iot-suite-connecting-devices/suite1.png
[3]: ./media/iot-suite-connecting-devices/suite2.png
[4]: ./media/iot-suite-connecting-devices/suite3.png
[5]: ./media/iot-suite-connecting-devices/suite5new.png

Wählen Sie als Nächstes aus, welche Sprache für dieses Beispiel verwendet werden soll. In diesem Lernprogramm haben wir Beispielcode für C und „node.js“ erstellt, aber Sie können die Implementierung auch in C# und Java durchführen.

## Senden von Gerätedaten an die Remoteüberwachungslösung mit C


### Ausführen des Geräts unter Linux

1. Richten Sie die Umgebung ein: Falls Sie das Geräte-SDK noch nicht verwendet haben, können Sie sich [hier](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux) über die Einrichtung der Umgebung unter Linux informieren.

1. Öffnen Sie die Datei **c/serializer/samples/serializer/remote\_monitoring.c** in einem Text-Editor.

2. Suchen Sie in der Datei nach dem folgenden Code: ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
3. Ersetzen Sie „[Device Id]“, „[Device Key]“ durch Ihre Gerätedaten.

4. Verwenden Sie die Gerätedaten unter dem IoT Hub-Hostnamen, um IoTHub-Name und IoTHub-Suffix anzugeben. Zu diesem Zweck müssen Sie eine Aufteilung in IoTHub und IoTHubSuffix vornehmen. Beispiel: Wenn der IoT Hub-Hostname „Contoso.azure-devices.net“ lautet, ist „Contoso“ der IoTHub-Name, und der restliche Text ist das Suffix. Diese sollte wie folgt aussehen:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

5. Speichern Sie die Änderungen, und erstellen Sie die Beispiele. Zum Erstellen des Beispiels können Sie das Skript „build.sh“ im Verzeichnis **c/build\_all/linux** ausführen.

6. Führen Sie die Beispielanwendung **c/serializer/samples/remote\_monitoring/linux/remote\_monitoring** aus.

Visualisieren des registrierten Geräts und der Daten

7. Wechseln Sie zurück zum Dashboard Ihrer Lösung für die Remoteüberwachung. Sie sehen, dass der Status des Geräts in der Liste „Geräte“ in „Wird ausgeführt“ geändert wurde. ![][18]

8. Klicken Sie auf das Dashboard, um die eingehenden Daten zu verfolgen. Das Beispiel ist so konfiguriert, dass 50 Einheiten für die Innentemperatur, 55 Einheiten für die Außentemperatur und 50 Einheiten für die Luftfeuchtigkeit gesendet werden. Bitte beachten Sie, dass im Dashboard standardmäßig nur die Temperatur und die Luftfeuchtigkeit angezeigt werden.

8. Wechseln Sie in den [Abschnitt](#command) „Befehl und Steuerung“, um zu erfahren, wie Sie die Temperatur auf Ihrem Gerät über die Lösung für die Remoteüberwachung ändern.


### Ausführen des Geräts unter Windows


1. Richten Sie die Umgebung ein: Falls Sie das Geräte-SDK noch nicht verwendet haben, können Sie sich [hier](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows) über die Einrichtung der Umgebung unter Windows informieren.

1. Starten Sie eine neue Instanz von Visual Studio 2015. Öffnen Sie die Lösung **remote\_monitoring.sln** im Ordner **c\\serializer\\build\\windows** der lokalen Repositorykopie.

2. Navigieren Sie in Visual Studio im **Projektmappen-Explorer** zum Ordner mit den Beispielen. Öffnen Sie im Projekt **remote\_monitoring** die Datei **remote\_monitoring.c**.

2. Suchen Sie in der Datei nach dem folgenden Code: ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
3. Ersetzen Sie „[Device Id]“, „[Device Key]“ durch Ihre Gerätedaten.

4. Verwenden Sie die Gerätedaten unter dem IoT Hub-Hostnamen, um IoTHub-Name und IoTHub-Suffix anzugeben. Zu diesem Zweck müssen Sie wie folgt eine Aufteilung vornehmen:

    Wenn der IoT Hub-Hostname „Contoso.azure-devices.net“ lautet, ist „Contoso“ der IoTHub-Name, und der restliche Text ist das Suffix. Diese sollte wie folgt aussehen:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **remote\_monitoring**, klicken Sie auf **Debuggen**, und klicken Sie dann auf **Neue Instanz starten**, um das Beispiel zu erstellen und auszuführen. Auf der Konsole werden Nachrichten angezeigt, während die Anwendung Device-to-Cloud (D2C)-Nachrichten an IoT Hub sendet.

Visualisieren des registrierten Geräts und der Daten

7. Wechseln Sie zurück zum Dashboard Ihrer Lösung für die Remoteüberwachung. Sie sehen, dass der Status des Geräts in der Liste „Geräte“ in „Wird ausgeführt“ geändert wurde. ![][18]

8. Klicken Sie auf das Dashboard, um die eingehenden Daten zu verfolgen. Das Beispiel ist so konfiguriert, dass 50 Einheiten für die Innentemperatur, 55 Einheiten für die Außentemperatur und 50 Einheiten für die Luftfeuchtigkeit gesendet werden. Bitte beachten Sie, dass im Dashboard standardmäßig nur die Temperatur und die Luftfeuchtigkeit angezeigt werden.

8. Wechseln Sie in den [Abschnitt](#command) „Befehl und Steuerung“, um zu erfahren, wie Sie die Temperatur auf Ihrem Gerät über die Lösung für die Remoteüberwachung ändern.


8. Wechseln Sie in den Abschnitt „Befehl und Steuerung“, um zu erfahren, wie Sie die Temperatur auf Ihrem Gerät über die Lösung für die Remoteüberwachung ändern.

### Ausführen des Geräts unter mbed

Im Folgenden sind die Schritte zum Verbinden eines [mbed-fähigen Freescale FRDM-K64F](https://developer.mbed.org/platforms/FRDM-K64F/)-Geräts mit Azure IoT Hub beschrieben.


Anforderungen

- Erforderliche Hardware: [mbed-fähiges Freescale K64F](https://developer.mbed.org/platforms/FRDM-K64F/)-Gerät oder vergleichbar.

Anschließen des Geräts

- Verbinden Sie das Board per Ethernet-Kabel mit Ihrem Netzwerk. Dieser Schritt ist erforderlich, da für das Beispiel Internetzugriff benötigt wird.

- Schließen Sie das Gerät mit einem Micro-USB-Kabel an Ihren Computer an. Achten Sie darauf, dass Sie das Kabel an den richtigen USB-Port des Geräts anschließen, wie dies [hier](https://developer.mbed.org/platforms/IBMEthernetKit/) im Abschnitt „Erste Schritte“ gezeigt wird.

- Befolgen Sie die [Anweisungen im mbed-Handbuch](https://developer.mbed.org/handbook/SerialPC), um über den Entwicklungscomputer eine serielle Verbindung mit Ihrem Gerät einzurichten. Wenn Sie unter Windows arbeiten, installieren Sie die Treiber für serielle Windows-Ports, die Sie [hier](http://developer.mbed.org/handbook/Windows-serial-configuration#1-download-the-mbed-windows-serial-port) finden.

Erstellen eines mbed-Projekts und Importieren des Beispielcodes

- Wechseln Sie in Ihrem Webbrowser zur [Entwicklerwebsite](https://developer.mbed.org/) von mbed.org. Wenn Sie sich noch nicht registriert haben, können Sie die Option zum Erstellen eines neuen Kontos verwenden (kostenlos). Melden Sie sich andernfalls mit Ihren Anmeldeinformationen an. Klicken Sie oben rechts auf der Seite dann auf **Compiler**. Sie gelangen auf die Benutzeroberfläche für die Arbeitsbereichsverwaltung.

- Stellen Sie sicher, dass die von Ihnen verwendete Hardwareplattform in der oberen rechten Ecke des Fensters angezeigt wird, oder klicken Sie auf das Symbol in der rechten Ecke, um Ihre Hardwareplattform auszuwählen.

- Klicken Sie im Hauptmenü auf **Importieren**. Klicken Sie anschließend auf **Hier klicken**, um den Import über den URL-Link neben dem mbed-Logo mit dem Globus durchzuführen.

	![][6]

- Geben Sie im Popupfenster den Link für den Beispielcode ein. https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/

	![][7]

- Im mbed-Compiler sehen Sie, dass der Import dieses Projekts verschiedene Bibliotheken umfasst. Einige werden vom Azure IoT-Team verwaltet ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [iothub\_http\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_http_transport/), [proton-c-mbed](https://developer.mbed.org/users/AzureIoTClient/code/proton-c-mbed/)), und bei anderen handelt es sich um Bibliotheken von Drittanbietern, die im Katalog mit den mbed-Bibliotheken verfügbar sind.

	![][8]

- Öffnen Sie „remote\_monitoring\\remote\_monitoring.c“, und suchen Sie in der Datei nach dem folgenden Code: ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
3. Ersetzen Sie „[Device Id]“ und „[Device Key]“ durch Ihre Gerätedaten.

4. Verwenden Sie die Gerätedaten unter dem IoT Hub-Hostnamen, um IoTHub-Name und IoTHub-Suffix anzugeben. Zu diesem Zweck müssen Sie wie folgt eine Aufteilung vornehmen:

    Wenn der IoT Hub-Hostname „Contoso.azure-devices.net“ lautet, ist „Contoso“ der IoTHub-Name, und der restliche Text ist das Suffix. Diese sollte wie folgt aussehen:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```
    ![][9]

Erstellen und Ausführen des Programms

- Klicken Sie auf **Kompilieren**, um das Programm zu erstellen. Sie können etwaige Warnungen ignorieren, aber wenn während der Erstellung Fehler auftreten, sollten Sie diese vor dem Fortfahren beheben.

- Wenn der Erstellungsvorgang erfolgreich ist, wird eine BIN-Datei mit dem Namen Ihres Projekts generiert. Kopieren Sie die BIN-Datei auf das Gerät. Wenn die BIN-Datei auf dem Gerät gespeichert wird, wird die aktuelle Terminalsitzung des Geräts zurückgesetzt. Setzen Sie das Terminal nach dem Herstellen der Verbindung erneut manuell zurück, oder starten Sie ein neues Terminal. Dies ermöglicht, dass das mbed-Gerät zurückgesetzt wird und das Programm ausgeführt werden kann.

- Stellen Sie mit einer SSH-Clientanwendung eine Verbindung mit dem Gerät her, z. B. PuTTY. Sie können ermitteln, welcher serielle Port von Ihrem Gerät verwendet wird, indem Sie dies im Windows-Geräte-Manager überprüfen:

	![][10]

- Klicken Sie in PuTTY auf den Verbindungstyp **Seriell**. Meistens wird die Verbindung für das Gerät über 115200 hergestellt. Geben Sie also diesen Wert in das Feld **Geschwindigkeit** ein. Klicken Sie anschließend auf **Öffnen**:

	![][11]

Das Programm wird gestartet. Unter Umständen müssen Sie das Board zurücksetzen (drücken Sie STRG+UNTBR, oder drücken Sie die Reset-Taste des Boards), falls das Programm nach dem Herstellen der Verbindung nicht automatisch gestartet wird.

Visualisieren Sie das registrierte Gerät und die Daten. Wechseln Sie zurück zum Dashboard Ihrer Lösung für die Remoteüberwachung. Sie sehen, dass der Status des Geräts in der Liste „Geräte“ in „Wird ausgeführt“ geändert wurde. ![][18]

8. Klicken Sie auf das Dashboard, um die eingehenden Daten zu verfolgen. Das Beispiel ist so konfiguriert, dass 50 Einheiten für die Innentemperatur, 55 Einheiten für die Außentemperatur und 50 Einheiten für die Luftfeuchtigkeit gesendet werden. Bitte beachten Sie, dass im Dashboard standardmäßig nur die Temperatur und die Luftfeuchtigkeit angezeigt werden.

8. Wechseln Sie in den [Abschnitt](#command) „Befehl und Steuerung“, um zu erfahren, wie Sie die Temperatur auf Ihrem Gerät über die Lösung für die Remoteüberwachung ändern.



[6]: ./media/iot-suite-connecting-devices/mbed1.png
[7]: ./media/iot-suite-connecting-devices/mbed2a.png
[8]: ./media/iot-suite-connecting-devices/mbed3a.png
[9]: ./media/iot-suite-connecting-devices/suite6.png
[10]: ./media/iot-suite-connecting-devices/mbed5a.png
[11]: ./media/iot-suite-connecting-devices/mbed6.png
[12]: ./media/iot-suite-connecting-devices/mbed7.png

Informationen zu den Befehlen und zur Steuerung finden Sie weiter unten in diesem Lernprogramm im entsprechenden Abschnitt.

## Senden von Gerätedaten an die Remoteüberwachungslösung mit „node.js“



-   Suchen Sie im Repository „azure-iot-sdks“ nach den folgenden Dateien: „packages.json“ (unter „/node/common“) und „remote\_monitoring.js“ (unter „node/device/samples/“). Kopieren Sie sie auf Ihr Gerät, und legen Sie sie in dem gleichen Ordner ab.

- Öffnen Sie die Datei „remote-monitoring.js“, und suchen Sie nach den folgenden Variablen:


   ```
   static const char* deviceId = "[Device Id]";
   static const char* deviceKey = "[Device Key]";
   static const char* hubName = "[IoTHub Name]";
   static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
   ```

-  Ersetzen Sie „[Device Id]“, „[Device Key]“ durch Ihre Gerätedaten.

-  Verwenden Sie die Gerätedaten unter dem IoT Hub-Hostnamen, um IoTHub-Name und IoTHub-Suffix anzugeben. Zu diesem Zweck müssen Sie wie folgt eine Aufteilung vornehmen:

   Wenn der IoT Hub-Hostname „Contoso.azure-devices.net“ lautet, ist „Contoso“ der IoTHub-Name, und der restliche Text ist das Suffix. Diese sollte wie folgt aussehen:


   ```
     static const char* deviceId = "mydevice";
   static const char* deviceKey = "mykey";
   static const char* hubName = "Contoso";
   static const char* hubSuffix = "azure-devices.net";
   ```


- Speichern Sie die Datei . Führen Sie im Zielordner den folgenden Befehl aus:

```
npm install
node .
```

3.  Ersetzen Sie die einzelnen Variablen jeweils durch die Informationen, die Sie im vorherigen Schritt gesammelt haben. Speichern Sie die Änderungen.


4. Öffnen Sie eine Shell (Linux) oder Node.js-Befehlseingabeaufforderung (Windows), und navigieren Sie zum Ordner **node\\samples**. Führen Sie die Beispielanwendung dann mit dem folgenden Befehl aus:

    ```
    node simple_sample_remotemonitoring.js
    ```

Visualisieren Ihres Geräts und der eingehenden Daten

6. Klicken Sie im Portal für die vorkonfigurierte Lösung auf den Abschnitt „Geräte“, um sicherzustellen, dass sich der Status Ihres Geräts in „Wird ausgeführt“ geändert hat und dass alle Herstellerdaten angezeigt werden.

7. Klicken Sie auf das Dashboard, und wählen Sie Ihr Gerät unter „Anzuzeigendes Gerät“ aus. Ihre Telemetriedaten sollten nun über die Lösung für die Remoteüberwachung überwacht werden.


## <a name="command"></a>Steuern Ihres Geräts über das Dashboard (Befehl und Steuerung)

Da Ihr Gerät jetzt verbunden ist und selbst generierte Temperaturdaten sendet, können Sie das Gerät remote über IoT Hub steuern. Sie können mehrere Arten von Befehlen implementieren, die für Ihre geschäftliche Anwendung geeignet sind. In diesem Fall haben wir eine Änderung der Temperatur implementiert, um vorzugeben, dass eine Steuerung über die Lösung erforderlich ist. Gehen Sie wie folgt vor, um den Befehl zu senden:

-  Klicken Sie in der Liste „Geräte“ auf Ihre Geräte-ID (Sie finden den Abschnitt „Geräte“ im Menü auf den linken Seite).

	![][13]

- Klicken Sie im rechten Menü mit den Gerätedetails auf „Befehl senden“.


- Wählen Sie den Befehl aus, den Sie ausführen möchten: In diesem Fall wählen wir „Temperatur festlegen“, da wir die Temperatur für das Gerät ändern möchten. Wählen Sie diesen Befehl und dann den Temperaturwert aus. Klicken Sie auf „Befehl senden“. Die neue Temperatur wird per Pushvorgang auf das Gerät übertragen. Hinweis: Sie sehen, dass das Ergebnis des Befehls im Befehlsverlauf „Ausstehend“ lautet. Dies liegt daran, dass bei diesen Beispielen der Einfachheit halber keine Logik im Gerät implementiert wurde, mit der auf IoT Hub reagiert werden kann. Sie können dies tun, um die Lösung zu erweitern.

	![][14]
- Kehren Sie zum Dashboard zurück, und stellen Sie sicher, dass die aktualisierten Daten ankommen. Sie sollten die aktualisierten Statistiken zur Temperatur sehen, und die neuen Daten sollten im Telemetrieverlauf angezeigt werden. ![][15]



[13]: ./media/iot-suite-connecting-devices/suite4.png
[14]: ./media/iot-suite-connecting-devices/suite7-1.png
[15]: ./media/iot-suite-connecting-devices/suite8a.png
[16]: ./media/iot-suite-connecting-devices/mbed4a.png
[17]: ./media/iot-suite-connecting-devices/suite9.png
[18]: ./media/iot-suite-connecting-devices/suite10.png


## Erweitern der Lösung

Es gibt mehrere Möglichkeiten, die Funktionalität dieses Beispiels zu erweitern: Verbinden Sie einen echten Sensor mit Ihrem Gerät, damit echte Daten gesendet werden, implementieren Sie die Funktionalität für „Befehl und Steuerung“ usw. Verwenden Sie unseren Leitfaden zur Erweiterung der Lösung für die Remoteüberwachung, um weitere Informationen zu erhalten.

<!---HONumber=Oct15_HO1-->