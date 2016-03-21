## Erstellen einer Geräteidentität

In diesem Abschnitt erstellen Sie eine Node.js-Konsolen-App, die eine neue Geräteidentität in der Identitätsregistrierung Ihres IoT Hubs erstellt. Ein Gerät kann nur eine Verbindung mit dem IoT Hub herstellen, wenn in der Geräteidentitätsregistrierung ein Eintrag für dieses Gerät vorhanden ist. Weitere Informationen finden Sie im Abschnitt **Geräteidentitätsregistrierung** des [Entwicklungsleitfadens für IoT Hub][lnk-devguide-identity]. Beim Ausführen dieser Konsolenanwendung werden eine eindeutige Geräte-ID und ein Schlüssel erstellt, mit denen sich das Gerät beim Senden von D2C-Nachrichten (Device-to-Cloud, Gerät-an-Cloud) beim IoT Hub identifizieren kann.

1. Erstellen Sie einen neuen leeren Ordner mit dem Namen **createdeviceidentity**. Erstellen Sie im Ordner **createdeviceidentity** die neue Datei „package.json“, indem Sie an der Eingabeaufforderung den unten angegebenen Befehl verwenden. Übernehmen Sie alle Standardeinstellungen:

    ```
    npm init
    ```

2. Führen Sie an der Eingabeaufforderung im Ordner **createdeviceidentity** den folgenden Befehl aus, um das Paket **azure-iothub** zu installieren:

    ```
    npm install azure-iothub --save
    ```

3. Erstellen Sie mit einem Text-Editor im Ordner **createdeviceidentity** die neue Datei **CreateDeviceIdentity.js**.

4. Fügen Sie am Anfang der Datei **CreateDeviceIdentity.js** die folgende `require`-Anweisung hinzu:

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Fügen Sie der Datei **CreateDeviceIdentity.js** den folgenden Code hinzu, und ersetzen Sie den Platzhalterwert durch die Verbindungszeichenfolge für den IoT Hub, den Sie im vorherigen Abschnitt erstellt haben:

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Fügen Sie den unten angegebenen Code hinzu, um in der Geräteidentitätsregistrierung im IoT Hub eine neue Gerätedefinition zu erstellen. Mit diesem Code wird ein neues Gerät erstellt, wenn die Geräte-ID in der Registrierung nicht vorhanden ist. Andernfalls wird der Schlüssel des vorhandenen Geräts zurückgegeben:

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. Speichern und schließen Sie die Datei **CreateDeviceIdentity.js**.

8. Führen Sie zum Ausführen der Anwendung **create-device-identity** den folgenden Befehl an der Eingabeaufforderung im Ordner „createdeviceidentity“ aus:

    ```
    node CreateDeviceIdentity.js 
    ```

9. Notieren Sie sich die **Geräte-ID** und den **Geräteschlüssel**. Sie benötigen diese Angaben später beim Erstellen einer Anwendung, für die eine Verbindung mit IoT Hub als Gerät hergestellt wird.

> [AZURE.NOTE] Die Identitätsregistrierung im IoT Hub speichert nur Geräteidentitäten, um einen sicheren Zugriff auf den Hub zu ermöglichen. Sie speichert Geräte-IDs und Schlüssel, die als Sicherheitsanmeldeinformationen verwendet werden, sowie ein Aktiviert/Deaktiviert-Kennzeichen, mit dem Sie den Zugriff für ein einzelnes Gerät deaktivieren können. Wenn Ihre Anwendung verlangt, dass weitere gerätespezifische Metadaten gespeichert werden, sollte dafür ein anwendungsspezifischer Speicher verwendet werden. Weitere Informationen finden Sie im [IoT Hub-Entwicklerhandbuch][lnk-devguide-identity].

## Empfangen von Gerät-an-Cloud-Nachrichten

In diesem Abschnitt erstellen Sie eine Node.js-Konsolen-App, die D2C-Nachrichten (Device-to-Cloud) aus dem IoT Hub liest. Ein IoT Hub macht einen [Event Hubs][lnk-event-hubs-overview]-kompatiblen Endpunkt verfügbar, der Ihnen das Lesen von D2C-Nachrichten ermöglicht. Zur Vereinfachung wird in diesem Tutorial ein einfacher Reader erstellt, der für eine Bereitstellung mit hohem Durchsatz nicht geeignet ist. Im Tutorial [Verarbeiten von Gerät-zu-Cloud-Nachrichten][lnk-processd2c-tutorial] wird gezeigt, wie Sie D2C-Nachrichten in größerem Umfang verarbeiten. Das Tutorial [Erste Schritte mit Event Hubs][lnk-eventhubs-tutorial] enthält weitere Informationen zum Verarbeiten der Nachrichten von Event Hubs und gilt für Endpunkte, die mit IoT Hub Event Hubs kompatibel sind.

1. Erstellen Sie einen neuen leeren Ordner mit dem Namen **readdevicetocloudmessages**. Erstellen Sie im Ordner **readdevicetocloudmessages** die neue Datei „package.json“, indem Sie an der Eingabeaufforderung den unten angegebenen Befehl verwenden. Übernehmen Sie alle Standardeinstellungen:

    ```
    npm init
    ```

2. Führen Sie an der Eingabeaufforderung im Ordner **readdevicetocloudmessages** den folgenden Befehl aus, um die Pakete **amqp10** und **bluebird** zu installieren:

    ```
    npm install amqp10 bluebird --save
    ```

3. Erstellen Sie mit einem Text-Editor im Ordner **readdevicetocloudmessages** die neue Datei **ReadDeviceToCloudMessages.js**.

4. Fügen Sie am Anfang der Datei **ReadDeviceToCloudMessages.js** die folgenden `require`-Anweisungen hinzu:

    ```
    'use strict';

    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```

5. Fügen Sie die folgenden Variablendeklarationen hinzu, und ersetzen Sie die Platzhalter durch die Werte, die Sie zuvor notiert haben. Der Wert des Platzhalters **{your event hub-compatible namespace}** stammt vom **Event Hub-kompatiblen Endpunkt** und hat das folgende Format: **xxxxnamespace.servicebus.windows.net**.

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] In diesem Code wird davon ausgegangen, dass Sie Ihren IoT Hub auf der Ebene F1 (Free) erstellt haben. Ein kostenloser IoT Hub verfügt über zwei Partitionen mit den Bezeichnungen „0“ und „1“. Wenn Sie Ihren IoT Hub mit einem der anderen Tarife erstellt haben, sollten Sie den Code so anpassen, dass ein **MessageReceiver**-Element für jede Partition erstellt wird.

6. Fügen Sie die folgende Filterdefinition hinzu. Bei dieser Anwendung wird beim Erstellen eines Receiver-Elements ein Filter verwendet, damit das Receiver-Element nur Nachrichten liest, die nach Beginn der Ausführung des Receiver-Elements an IoT Hub gesendet werden. Dies ist in einer Testumgebung nützlich, da Sie den aktuellen Satz von Nachrichten anzeigen können. In einer Produktionsumgebung sollte durch Ihren Code hingegen sichergestellt werden, dass alle Nachrichten verarbeitet werden. Weitere Informationen hierzu finden Sie im Tutorial [Gewusst wie: Verarbeiten von D2C-Nachrichten mit IoT Hub][lnk-processd2c-tutorial].

    ```
    var filterOffset = new Date().getTime();
    var filterOption;
    if (filterOffset) {
      filterOption = {
      attach: { source: { filter: {
      'apache.org:selector-filter:string': translator(
        ['described', ['symbol', 'apache.org:selector-filter:string'], ['string', "amqp.annotation.x-opt-enqueuedtimeutc > " + filterOffset + ""]])
        } } }
      };
    }
    ```

7. Fügen Sie den folgenden Code hinzu, um die Empfangsadresse und einen AMQP-Client zu erstellen:

    ```
    var uri = protocol + '://' + encodeURIComponent(sasName) + ':' + encodeURIComponent(sasKey) + '@' + eventHubHost;
    var recvAddr = eventHubName + '/ConsumerGroups/$default/Partitions/';
    
    var client = new AMQPClient(Policy.EventHub);
    ```

8. Fügen Sie die folgenden beiden Funktionen hinzu, mit denen die Ausgabe an die Konsole erfolgt:

    ```
    var messageHandler = function (partitionId, message) {
      console.log('Received(' + partitionId + '): ', message.body);
    };
    
    var errorHandler = function(partitionId, err) {
      console.warn('** Receive error: ', err);
    };
    ```

9. Fügen Sie die folgende Funktion hinzu, die als Receiver-Element für eine Partition mit Verwendung des Filters fungiert:

    ```
    var createPartitionReceiver = function(partitionId, receiveAddress, filterOption) {
      return client.createReceiver(receiveAddress, filterOption)
        .then(function (receiver) {
          console.log('Listening on partition: ' + partitionId);
          receiver.on('message', messageHandler.bind(null, partitionId));
          receiver.on('errorReceived', errorHandler.bind(null, partitionId));
        });
    };
    ```

10. Fügen Sie den folgenden Code hinzu, um eine Verbindung mit dem Event Hub-kompatiblen Endpunkt herzustellen und die Receiver-Elemente zu starten:

    ```
    client.connect(uri)
      .then(function () {
        var partitions = [];
        for (var i = 0; i < numPartitions; ++i) {
          partitions.push(createPartitionReceiver(i, recvAddr + i, filterOption));
        }
        return Promise.all(partitions);
    })
    .error(function (e) {
        console.warn('Connection error: ', e);
    });
    ```

11. Speichern und schließen Sie die Datei **ReadDeviceToCloudMessages.js**.

<!-- Links -->

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0309_2016-->