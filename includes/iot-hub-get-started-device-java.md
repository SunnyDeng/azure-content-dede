## Erstellen einer simulierten Geräte-App

In diesem Abschnitt erstellen Sie eine Java-Konsolenanwendung, die ein Gerät simuliert, das D2C-Nachrichten (device to Cloud, Gerät zu Cloud) an einen IoT Hub sendet.

1. Erstellen Sie im Ordner „iot-java-get-started“, den Sie im Abschnitt *Erstellen einer Geräteidentität* erstellt haben, ein neues Maven-Projekt mit dem Namen **simulated-device**, indem Sie an der Eingabeaufforderung den folgenden Befehl verwenden. Beachten Sie, dass es sich hierbei um einen einzelnen langen Befehl handelt:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigieren Sie in der Eingabeaufforderung zum neuen Ordner „simulated-device“.

3. Öffnen Sie mit einem Text-Editor die Datei „pom.xml“ im Ordner „simulated-device“, und fügen Sie dem Knoten **dependencies** die folgenden Abhängigkeiten hinzu. Dadurch können Sie das Paket „iothub-java-client“ zum Kommunizieren mit Ihrem IoT Hub und Serialisieren von Java-Objekten nach JSON in Ihrer Anwendung verwenden:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.0</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Speichern und schließen Sie die Datei „pom.xml“.

5. Öffnen Sie die Datei „simulated-device\\src\\main\\java\\com\\mycompany\\app\\App.java“ mit einem Text-Editor.

6. Fügen Sie der Datei die folgenden **import**-Anweisungen hinzu:

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.Random;
    import javax.naming.SizeLimitExceededException;
    import com.google.gson.Gson;
    ```

7. Fügen Sie die folgenden Variablen auf Klassenebene der **App**-Klasse hinzu, und ersetzen Sie **{youriothubname}** durch den Namen Ihres IoT Hubs und **{yourdeviceid}** (Gerät-ID) und **{yourdevicekey}** (Geräteschlüssel) mit dem Gerätewerten, die Sie im Abschnitt *Erstellen einer Geräteidentität* erstellt haben:

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static boolean stopThread = false;
    ```

    Diese Anwendung verwendet die **Protokoll**-Variable, wenn sie ein **DeviceClient**-Objekt instanziiert. Sie können das HTTPS- oder AMQPS-Protokoll verwenden, um mit IoT Hub zu kommunizieren.

8. Fügen Sie die folgende geschachtelte **TelemetryDataPoint**-Klasse innerhalb der **App** Klasse hinzu, um die Telemetriedaten festzulegen, die Ihr Gerät an den IoT Hub sendet:

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. Fügen Sie die folgende geschachtelte **EventCallback**-Klasse innerhalb der **App** Klasse hinzu, um den Status der Bestätigung anzuzeigen, die der IoT Hub bei der Verarbeitung einer Nachricht vom simulierten Gerät zurückgibt. Diese Methode benachrichtigt zudem den Hauptthread der Anwendung, wenn die Nachricht verarbeitet wurde:

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Fügen Sie die folgende geschachtelte **MessageSender**-Klasse in der **App-Klasse** hinzu. Die **run**-Methode in dieser Klasse generiert Beispiel-Telemetriedaten zum Senden an den IoT Hub und wartet auf eine Bestätigung, bevor die nächste Nachricht gesendet wird:

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;

      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          DeviceClient client;
          client = new DeviceClient(connString, protocol);
          client.open();
        
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = "myFirstDevice";
            telemetryDataPoint.windSpeed = currentWindSpeed;
      
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println(msgStr);
        
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
          client.close();
        } catch (Exception e) {
          e.printStackTrace();
        }
      }
    }
    ```

    Diese Methode sendet eine Sekunde, nachdem der IoT Hub die vorherige Nachricht bestätigt, eine neue D2C-Nachricht. Die Nachricht enthält ein JSON-serialisiertes Objekt mit der Geräte-ID sowie eine zufällig generierte Zahl, um einen Windgeschwindigkeitssensor zu simulieren.

11. Ersetzen Sie die **main**-Methode durch den folgenden Code, der einen Thread zum Senden von D2C-Nachrichten an Ihren IoT Hub erstellt:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
    
      MessageSender ms0 = new MessageSender();
      Thread t0 = new Thread(ms0);
      t0.start(); 
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      ms0.stopThread = true;
    }
    ```

12. Speichern und schließen Sie die Datei „App.java“.

13. Führen Sie zum Erstellen der Anwendung **simulated-device** mit Maven den folgenden Befehl an der Eingabeaufforderung im Ordner „simulated-device“ aus:

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Der Einfachheit halber wird in diesem Tutorial keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren (z. B. einen exponentiellen Backoff), wie im MSDN-Artikel zum [Behandeln vorübergehender Fehler][lnk-transient-faults] beschrieben.

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0309_2016-->