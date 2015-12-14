<properties
	pageTitle="Erstellen eines IoT Hub-Java-Clients | Microsoft Azure"
	description="Befolgen Sie dieses Tutorial zum Erstellen eines IoT Hub-Java-Clients, der mithilfe des Microsoft Azure IoT-Geräte-SDKs für Java* mit Azure IoT Hub kommuniziert."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/26/2015"
     ms.author="dobett"/>
     
# Erstellen einer Azure IoT Hub-Clientanwendung mithilfe von Java

In diesem Artikel wird das Erstellen einer Clientanwendung beschrieben, die das [Microsoft Azure IoT-Geräte-SDK für Java][lnk-java-sdk] zur Kommunikation mit Azure IoT Hub verwendet. Im Tutorial erfahren Sie, wie das Projekt mit dem Tool [Maven][apache-maven] erstellt wird. Sie können diese Schritte auf einem Windows- oder Linux-Computer ausführen.

Sie finden die Java-API-Dokumentation [hier][lnk-java-api-docs].

## Installation

Informationen zu erforderlichen Komponenten und zum Einrichten der Entwicklungsumgebung unter Windows oder Linux finden Sie unter [Prepare your development environment][devbox-setup] (in englischer Sprache).

> [AZURE.NOTE]Es ist wichtig, dass Sie die Schritte in [Prepare your development environment][devbox-setup] abschließen, bevor Sie mit diesem Tutorial beginnen, um die erforderlichen Komponenten zu installieren und die notwendigen JAR-Dateien Ihrem lokalen Maven-Repository hinzuzufügen.

## Erstellen des Projekts

1. Führen Sie im Befehlszeilentool folgenden Befehl aus, um ein neues, leeres Maven-Projekt an einem geeigneten Speicherort auf dem Entwicklungscomputer zu erstellen:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=iot-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [AZURE.NOTE]Es handelt sich um einen einzigen, langen Befehl. Achten Sie darauf, ihn zum Einfügen in das Befehlszeilentool vollständig zu kopieren.

    Dieser Befehl erstellt einen Projektordner mit dem Namen *iot-device* und der standardmäßigen Maven-Projektstruktur. Weitere Informationen finden Sie auf der Apache-Website unter [Maven in 5 Minutes][maven-five-minutes] (in englischer Sprache).

2. Öffnen Sie die Datei **pom.xml** im Ordner „iot-device“ in einem Text-Editor.

3. Fügen Sie nach dem bereits vorhandenen Abschnitt den folgenden neuen Abschnitt **dependency** hinzu, um die erforderlichen Client-Bibliotheken einzubinden.

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-client</artifactId>
      <version>1.0.0-preview.4</version>
    </dependency>
    ```

4. Fügen Sie den folgenden Abschnitt **build** nach dem Abschnitt **dependency** hinzu, damit die fertige JAR-Datei die entsprechenden Abhängigkeiten enthält und das Manifest die **main**-Klasse festlegt.

    ```
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-shade-plugin</artifactId>
          <executions>
            <execution>
              <phase>package</phase>
              <goals>
                <goal>shade</goal>
              </goals>
            </execution>
          </executions>
          <configuration>
            <finalName>${artifactId}-${version}-with-deps</finalName>
          </configuration>
        </plugin>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-jar-plugin</artifactId>
          <version>2.6</version>
          <configuration>
            <archive>
              <manifest>
                <addClasspath>true</addClasspath>
                <mainClass>com.mycompany.app.App</mainClass>
              </manifest>
            </archive>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

5. Speichern Sie die Datei **pom.xml**.

## Erstellen der Anwendung

1. Öffnen Sie die Datei **App.java** im Ordner „iot-device/src/main/java/com/mycompany/app“ in einem Text-Editor.

2. Fügen Sie die folgenden **import**-Anweisungen, darunter die IoT-Gerätebibliotheken, nach der **package**-Anweisung hinzu:

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
    import java.util.Scanner;
    
    import javax.naming.SizeLimitExceededException;
    ```

3. Fügen Sie die folgende **EventCallback**-Klasse als geschachtelte Klasse innerhalb der **App**-Klasse hinzu. Die **execute**-Methode in der **EventCallback**-Klasse wird aufgerufen, wenn das Gerät eine Bestätigung von Ihrem IoT-Hub als Antwort auf eine Gerät-zu-Cloud-Nachricht empfängt.

    ```
    protected static class EventCallback
        implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        Integer i = (Integer) context;
        System.out.println("IoT Hub responded to message " + i.toString()
            + " with status " + status.name());
      }
    }
    ```

4. Fügen Sie die folgende **MessageCallback**-Klasse als geschachtelte Klasse innerhalb der **App**-Klasse hinzu. Die **execute**-Methode in der **MessageCallback**-Klasse ermöglicht Ihnen das Senden einer Feedback-Nachricht an den IoT-Hub als Antwort auf eine vom Gerät empfangene Cloud-zu-Gerät-Nachricht.

    ```
    protected static class MessageCallback
        implements com.microsoft.azure.iothub.MessageCallback
    {
      public IotHubMessageResult execute(Message msg,
          Object context)
      {
        System.out.println(
            "Received message with content: " + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));
    
          return IotHubMessageResult.COMPLETE;
      }
    }
    ```

5. Ersetzen Sie die vorhandene **main**-Methode durch den folgenden Code, der folgende Aufgaben übernimmt:

  - Erstellen einer **DeviceClient**-Instanz.
  - Initialisiert das Nachrichten-Callback für Cloud-zu-Gerät-Nachrichten.
  - Öffnet den **DeviceClient**, um ihm das Senden von Gerät-zu-Cloud- und das Empfangen von Cloud-zu-Gerät-Nachrichten zu ermöglichen.
  - Senden von zehn Beispielnachrichten an den IoT-Hub.

    Ersetzen Sie `<your device connection string>` durch eine gültige Verbindungszeichenfolge für das Gerät. Das Bereitstellen eines Geräts und das Abrufen der Verbindungszeichenfolge können über eines der Tools [DeviceExplorer][lnk-device-explorer] oder [IoT Hub Explorer][lnk-iothub-explorer] erfolgen.

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException
    {
      String connString = "<your device connection string>";
      IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    
      DeviceClient client = new DeviceClient(connString, protocol);
    
      MessageCallback messageCallback = new MessageCallback();
      client.setMessageCallback(messageCallback, null);
    
      client.open();
    
      for (int i = 0; i < 10; ++i)
      {
        String msgStr = "Event Message " + Integer.toString(i);
        try
        {
          Message msg = new Message(msgStr);
          msg.setProperty("messageCount", Integer.toString(i));
          System.out.println(msgStr);
    
          EventCallback eventCallback = new EventCallback();
          client.sendEventAsync(msg, eventCallback, i);
        }
        catch (Exception e)
        {
        }
      }
    
      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
    
      client.close();
    }
    ```

6. Zum Kompilieren des Codes und dessen Verpacken in eine JAR-Datei führen Sie den folgenden Befehl auf der Befehlszeile im Projektordner **iot-device** durch:

    ```
    mvn package
    ```

7. Zum Ausführen der Anwendung führen Sie den folgenden Befehl auf der Befehlszeile im Projektordner **iot-device** durch:

    ```
    java -jar target/iot-device-1.0-SNAPSHOT-with-deps.jar
    ```

8. Mit dem Tool [DeviceExplorer][lnk-device-explorer] lassen sich die vom IoT-Hub empfangenen Gerät-zu-Cloud-Nachrichten überwachen und Cloud-zu-Gerät-Nachrichten vom IoT-Hub an das Gerät senden.

## Ändern der Protokollierungsgranularität

Um die Protokollierungsgranularität zu ändern, fügen Sie folgende Zeile in die Datei `config.properties` ein:

```
.level = {LOGGING_LEVEL}
```

> [AZURE.NOTE]Eine Erläuterung der verschiedenen Protokolliergrade finden Sie [hier](http://docs.oracle.com/javase/7/docs/api/java/util/logging/Level.html).

Legen Sie dann die JVM-Eigenschaft `java.util.logging.config.file={Path to your config.properties file}` fest.

Um AMQP-Frames zu protokollieren, legen Sie die Umgebungsvariable `PN_TRACE_FRM=1` in Ihrer Befehlsumgebung fest.


[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-java-api-docs]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[apache-maven]: https://maven.apache.org/index.html
[maven-five-minutes]: https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html
[devbox-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md
[lnk-iothub-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/doc/provision_device.md

<!---HONumber=AcomDC_1203_2015-->