## Erstellen einer Geräteidentität

In diesem Abschnitt erstellen Sie eine Java-Konsolen-App, mit der eine neue Geräteidentität in der Identitätsregistrierung Ihres IoT Hub erstellt wird. Ein Gerät kann nur eine Verbindung mit dem IoT Hub herstellen, wenn in der Geräteidentitätsregistrierung ein Eintrag für dieses Gerät vorhanden ist. Weitere Informationen finden Sie im Abschnitt **Geräteidentitätsregistrierung** des [Entwicklungsleitfadens für IoT Hub][lnk-devguide-identity]. Beim Ausführen dieser Konsolenanwendung werden eine eindeutige Geräte-ID und ein Schlüssel erstellt, mit denen sich das Gerät beim Senden von D2C-Nachrichten (Device-to-Cloud, Gerät-an-Cloud) beim IoT Hub identifizieren kann.

1. Erstellen Sie einen neuen leeren Ordner mit dem Namen „iot-java-get-started“. Erstellen Sie im Ordner „iot-java-get-started“ ein neues Maven-Projekt mit dem Namen **create-device-identity**, indem Sie an der Eingabeaufforderung den folgenden Befehl verwenden. Beachten Sie, dass es sich hierbei um einen einzelnen langen Befehl handelt.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigieren Sie in der Eingabeaufforderung zum neuen Ordner „create-device-identity“.

3. Öffnen Sie mit einem Text-Editor die Datei „pom.xml“ im Ordner „create-device-identity“, und fügen Sie dem Knoten **dependencies** die folgende Abhängigkeit hinzu. Sie können dann das Paket „iothub-service-sdk“ in Ihrer Anwendung verwenden:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.2</version>
    </dependency>
    ```
    
4. Speichern und schließen Sie die Datei „pom.xml“.

5. Öffnen Sie die Datei „create-device-identity\\src\\main\\java\\com\\mycompany\\app\\App.java“ mit einem Text-Editor.

6. Fügen Sie der Datei die folgenden **import**-Anweisungen hinzu:

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Fügen Sie die folgenden Klassenebenenvariablen der **App**-Klasse hinzu, und ersetzen Sie dabei **{yourhubname}** und **{yourhubkey}** durch die zuvor notierten Werte:

    ```
    private static final String connectionString = "HostName={yourhubname}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={yourhubkey}";
    private static final String deviceId = "javadevice";
    
    ```
    
8. Ändern Sie die Signatur der **main**-Methode, um die unten angegebenen Ausnahmen einzufügen:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Fügen Sie den folgenden Code als Textkörper der **main**-Methode hinzu. Mit diesem Code wird ein Gerät mit dem Namen *javadevice* in Ihrer IoT Hub-Identitätsregistrierung erstellt, falls es noch nicht vorhanden ist. Anschließend werden die Geräte-ID und der dazugehörige Schlüssel angezeigt, die Sie später benötigen:

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Speichern und schließen Sie die Datei „App.java“.

11. Führen Sie zum Erstellen der Anwendung **create-device-identity** mit Maven den folgenden Befehl an der Eingabeaufforderung im Ordner „create-device-identity“ aus:

    ```
    mvn clean package -DskipTests
    ```

12. Führen Sie zum Ausführen der Anwendung **create-device-identity** mit Maven den folgenden Befehl an der Eingabeaufforderung im Ordner „create-device-identity“ aus:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Notieren Sie sich die **Geräte-ID** und den **Geräteschlüssel**. Sie benötigen diese Angaben später beim Erstellen einer Anwendung, für die eine Verbindung mit IoT Hub als Gerät hergestellt wird.

> [AZURE.NOTE] Die Identitätsregistrierung im IoT Hub speichert nur Geräteidentitäten, um einen sicheren Zugriff auf den Hub zu ermöglichen. Sie speichert Geräte-IDs und Schlüssel, die als Sicherheitsanmeldeinformationen verwendet werden, sowie ein Aktiviert/Deaktiviert-Kennzeichen, mit dem Sie den Zugriff für ein einzelnes Gerät deaktivieren können. Wenn Ihre Anwendung verlangt, dass weitere gerätespezifische Metadaten gespeichert werden, sollte dafür ein anwendungsspezifischer Speicher verwendet werden. Weitere Informationen finden Sie im [IoT Hub-Entwicklerhandbuch][lnk-devguide-identity].

## Empfangen von Gerät-an-Cloud-Nachrichten

In diesem Abschnitt erstellen Sie eine Java-Konsolen-App, die D2C-Nachrichten (Device-to-Cloud) aus dem IoT Hub liest. Ein IoT Hub macht einen [Event Hubs][lnk-event-hubs-overview]-kompatiblen Endpunkt verfügbar, der Ihnen das Lesen von D2C-Nachrichten ermöglicht. Zur Vereinfachung wird in diesem Tutorial ein einfacher Reader erstellt, der für eine Bereitstellung mit hohem Durchsatz nicht geeignet ist. Im Tutorial [Verarbeiten von Gerät-zu-Cloud-Nachrichten][lnk-processd2c-tutorial] wird gezeigt, wie Sie D2C-Nachrichten in größerem Umfang verarbeiten. Das Tutorial [Erste Schritte mit Event Hubs][lnk-eventhubs-tutorial] enthält weitere Informationen zum Verarbeiten der Nachrichten von Event Hubs und gilt für Endpunkte, die mit IoT Hub Event Hubs kompatibel sind.

1. Erstellen Sie im Ordner „iot-java-get-started“, den Sie im Abschnitt *Erstellen einer Geräteidentität* erstellt haben, ein neues Maven-Projekt mit dem Namen **read-d2c-messages**, indem Sie an der Eingabeaufforderung den folgenden Befehl verwenden. Beachten Sie, dass es sich hierbei um einen einzelnen langen Befehl handelt.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigieren Sie in der Eingabeaufforderung zum neuen Ordner „read-d2c-messages“.

3. Öffnen Sie mit einem Text-Editor die Datei „pom.xml“ im Ordner „read-d2c-messages“, und fügen Sie dem Knoten **dependencies** die folgende Abhängigkeit hinzu. So können Sie das Paket „eventhubs-client“ in Ihrer Anwendung zum Lesen vom Event Hubs-kompatiblen Endpunkt verwenden:

    ```
    <dependency>
      <groupId>com.microsoft.eventhubs.client</groupId>
      <artifactId>eventhubs-client</artifactId>
      <version>1.0</version>
    </dependency>
    ```

4. Speichern und schließen Sie die Datei „pom.xml“.

5. Öffnen Sie die Datei „read-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java“ mit einem Text-Editor.

6. Fügen Sie der Datei die folgenden **import**-Anweisungen hinzu:

    ```
    import java.io.IOException;
    import com.microsoft.eventhubs.client.Constants;
    import com.microsoft.eventhubs.client.EventHubClient;
    import com.microsoft.eventhubs.client.EventHubEnqueueTimeFilter;
    import com.microsoft.eventhubs.client.EventHubException;
    import com.microsoft.eventhubs.client.EventHubMessage;
    import com.microsoft.eventhubs.client.EventHubReceiver;
    import com.microsoft.eventhubs.client.ConnectionStringBuilder;
    ```

7. Fügen Sie der **App**-Klasse die folgenden Klassenebenenvariablen hinzu:

    ```
    private static EventHubClient client;
    private static long now = System.currentTimeMillis();
    ```

8. Fügen Sie die folgende geschachtelte Klasse in der **App**-Klasse hinzu. Die Anwendung erstellt zwei Threads zum Ausführen des **MessageReceiver**-Elements, damit Nachrichten von den beiden Partitionen im Event Hub gelesen werden können:

    ```
    private static class MessageReceiver implements Runnable
    {
        public volatile boolean stopThread = false;
        private String partitionId;
    }
    ```

9. Fügen Sie den folgenden Konstruktor der **MessageReceiver**-Klasse hinzu:

    ```
    public MessageReceiver(String partitionId) {
        this.partitionId = partitionId;
    }
    ```

10. Fügen Sie die folgende **run**-Methode der **MessageReceiver**-Klasse hinzu. Diese Methode erstellt eine **EventHubReceiver**-Instanz zum Lesen von einer Event Hub-Partition. Sie wird in einer Dauerschleife ausgeführt und gibt die Nachrichtendetails an die Konsole aus, bis der Status für **stopThread** „true“ lautet.

    ```
    public void run() {
      try {
        EventHubReceiver receiver = client.getConsumerGroup(null).createReceiver(partitionId, new EventHubEnqueueTimeFilter(now), Constants.DefaultAmqpCredits);
        System.out.println("** Created receiver on partition " + partitionId);
        while (!stopThread) {
          EventHubMessage message = EventHubMessage.parseAmqpMessage(receiver.receive(5000));
          if(message != null) {
            System.out.println("Received: (" + message.getOffset() + " | "
                + message.getSequence() + " | " + message.getEnqueuedTimestamp()
                + ") => " + message.getDataAsString());
          }
        }
        receiver.close();
      }
      catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
      }
    }
    ```

    > [AZURE.NOTE] Bei dieser Methode wird beim Erstellen des Receiver-Elements ein Filter verwendet, damit das Receiver-Element nur Nachrichten liest, die nach Beginn der Ausführung des Receiver-Elements an IoT Hub gesendet werden. Dies ist in einer Testumgebung nützlich, damit Sie den aktuellen Satz an Nachrichten anzeigen können. In einer Produktionsumgebung sollten Sie per Code sicherstellen, dass alle Nachrichten verarbeitet werden. Weitere Informationen finden Sie im Tutorial [Verarbeiten von D2C-Nachrichten mit IoT Hub][lnk-processd2c-tutorial].

11. Ändern Sie die Signatur der **main**-Methode, um die unten angegebenen Ausnahmen einzufügen:

    ```
    public static void main( String[] args ) throws IOException
    ```

12. Fügen Sie der **main**-Methode in der **App**-Klasse den folgenden Code hinzu. Mit diesem Code wird eine **EventHubClient**-Instanz zum Verbinden des Event Hub-kompatiblen Endpunkts mit IoT Hub erstellt. Anschließend werden zwei Threads zum Lesen aus den beiden Partitionen erstellt. Ersetzen Sie **{youriothubkey}**, **{youreventhubcompatiblenamespace}** und **{youreventhubcompatiblename}** durch die Werte, die Sie zuvor notiert haben. Der Wert des Platzhalters **{youreventhubcompatiblenamespace}** stammt vom **Event Hub-kompatiblen Endpunkt** und hat das folgende Format: **xxxxnamespace.servicebus.windows.net**. (Sie sollten also das Präfix ****sb://** und das Suffix **.servicebus.windows.net** vom Even Hub-kompatiblen Endpunktwert im Portal entfernen.)

    ```
    String policyName = "iothubowner";
    String policyKey = "{youriothubkey}";
    String namespace = "{youreventhubcompatiblenamespace}";
    String name = "{youreventhubcompatiblename}";
    try {
      ConnectionStringBuilder csb = new ConnectionStringBuilder(policyName, policyKey, namespace);
      client = EventHubClient.create(csb.getConnectionString(), name);
    }
    catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
    }
    
    MessageReceiver mr0 = new MessageReceiver("0");
    MessageReceiver mr1 = new MessageReceiver("1");
    Thread t0 = new Thread(mr0);
    Thread t1 = new Thread(mr1);
    t0.start(); t1.start();

    System.out.println("Press ENTER to exit.");
    System.in.read();
    mr0.stopThread = true;
    mr1.stopThread = true;
    client.close();
    ```

    > [AZURE.NOTE] In diesem Code wird davon ausgegangen, dass Sie Ihren IoT Hub auf der Ebene F1 (Free) erstellt haben. Ein kostenloser IoT Hub verfügt über zwei Partitionen mit den Bezeichnungen „0“ und „1“. Wenn Sie Ihren IoT Hub mit einem der anderen Tarife erstellt haben, sollten Sie den Code so anpassen, dass ein **MessageReceiver**-Element für jede Partition erstellt wird.

13. Speichern und schließen Sie die Datei „App.java“.

14. Führen Sie zum Erstellen der Anwendung **read-d2c-messages** mit Maven den folgenden Befehl an der Eingabeaufforderung im Ordner „read-d2c-messages“ aus:

    ```
    mvn clean package -DskipTests
    ```



<!-- Links -->

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0323_2016-->