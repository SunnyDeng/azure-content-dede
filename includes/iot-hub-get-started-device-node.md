## Erstellen einer simulierten Geräte-App

In diesem Abschnitt erstellen Sie eine Node.js-Konsolenanwendung, die ein Gerät simuliert, mit dem D2C-Nachrichten (Device-to-Cloud, Gerät-an-Cloud) an einen IoT Hub gesendet werden.

1. Erstellen Sie einen neuen leeren Ordner mit dem Namen **simulateddevice**. Erstellen Sie im Ordner **simulateddevice** die neue Datei „package.json“, indem Sie an der Eingabeaufforderung den unten angegebenen Befehl verwenden. Übernehmen Sie alle Standardeinstellungen:

    ```
    npm init
    ```

2. Führen Sie an der Eingabeaufforderung im Ordner **simulateddevice** den folgenden Befehl aus, um das Paket **azure-iot-device-amqp** zu installieren:

    ```
    npm install azure-iot-device-amqp --save
    ```

3. Erstellen Sie mit einem Text-Editor im Ordner **simulateddevice** die neue Datei **SimulatedDevice.js**.

4. Fügen Sie am Anfang der Datei **SimulatedDevice.js** die folgenden `require`-Anweisungen hinzu:

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Fügen Sie die Variable **connectionString** hinzu, und verwenden Sie sie zum Erstellen eines Geräteclients. Ersetzen Sie **{youriothubname}** durch den Namen Ihres IoT Hubs und **{yourdeviceid}** sowie **{yourdevicekey}** durch die Gerätewerte, die Sie im Abschnitt *Erstellen einer Geräteidentität* generiert haben:

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Fügen Sie die folgende Funktion hinzu, um die Ausgabe der Anwendung anzuzeigen:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Erstellen Sie einen Rückruf, und verwenden Sie die Funktion **setInterval**, um jede Sekunde eine neue Nachricht an Ihren IoT Hub zu senden:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'mydevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 2000);
      }
    };
    ```

8. Öffnen Sie die Verbindung mit dem IoT Hub, und beginnen Sie mit dem Senden von Nachrichten:

    ```
    client.open(connectCallback);
    ```

9. Speichern und schließen Sie die Datei **SimulatedDevice.js**.

> [AZURE.NOTE] Der Einfachheit halber wird in diesem Tutorial keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren (z. B. einen exponentiellen Backoff), wie im MSDN-Artikel zum [Behandeln vorübergehender Fehler][lnk-transient-faults] beschrieben.

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0309_2016-->