<properties
	pageTitle="Einführung in das Azure IoT-Geräte-SDK für C"
	description="Erfahren Sie mehr über den Beispielcode im Azure IoT-Geräte-SDK für C sowie über die ersten Schritte."
	services="iot-hub"
	documentationCenter="na"
	authors="MichelBarnett"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="michelb"/>

# Einführung in das Azure IoT-Geräte-SDK für C

Das **Azure IoT-Geräte-SDK** ist ein Satz von Bibliotheken, die konzipiert wurden, um den Versand von Ereignissen und den Empfang von Nachrichten vom **Azure IoT Hub**-Dienst zu vereinfachen. Es gibt verschiedene Varianten des SDK für die unterschiedlichen Plattformen. In diesem Artikel liegt der Fokus jedoch auf dem **Azure IoT-Geräte-SDK für C**.

Das Azure IoT-Geräte-SDK für C wurde für optimale Portabilität in ANSI C (C99) geschrieben. Dies vereinfacht den Einsatz auf einer Reihe von Plattformen und Geräten – vor allem, wenn es wichtig ist, die Festplatten- und Speichergröße zu reduzieren.

Es gibt eine Vielzahl von Plattformen, auf denen das SDK getestet wurde. (Einzelheiten finden Sie in der [SDK-Dokumentation](https://github.com/Azure/azure-iot-sdks/tree/master/c)). Denken Sie daran, dass, obwohl in diesem Artikel der Beispielcode für die *Windows*-Plattform verwendet wird, der unten beschriebene Code für alle unterstützten Plattformen identisch ist.

In diesem Artikel erhalten Sie einen Einblick in die Architektur des Azure IoT-Geräte-SDK für C. Wir zeigen, wie Sie die Gerätebibliothek initialisieren, Ereignisse an IoT Hub senden und vom IoT Hub Nachrichten empfangen können. Die Informationen in diesem Artikel sollten ausreichen, um mit der Arbeit mit dem SDK zu beginnen. Zugleich erhalten Sie Hinweise darauf, wo Sie weitere Informationen zu den Bibliotheken finden können.


## SDK-Architektur

Das **Azure IoT-Geräte-SDK für C** befindet sich im folgenden GitHub-Repository:

[azure-iot-sdks](https://github.com/Azure/azure-iot-sdks)

Die aktuelle Version der Bibliotheken finden Sie im **master**-Verzeichnis dieses Repository:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

Dieses Repository enthält die gesamte Familie der Azure IoT-Geräte-SDKs. Aber in diesem Artikel geht es um das Azure IoT-Geräte-SDK für *C*, das sich im **c**-Ordner befindet.

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

Die zentrale Implementierung des SDK finden Sie im Repository in den Ordnern **common**, **iothub\_client** und **serializer**. Der **common**-Ordner enthält Code, der von allen Bibliotheken gemeinsam verwendet wird. (In der Regel verwenden Sie den Code nicht direkt im Ordner **common**). Die Ordner **iothub\_client** und **serializer** enthalten jedoch Implementierungen der beiden unterschiedlichen Ebenen des SDK, die Sie in Ihrem Code verwenden.

-   **IoTHubClient**: Der **iothub\_client**-Ordner enthält die Implementierung der niedrigsten API-Ebene in der Bibliothek. Diese Ebene enthält APIs für das Senden von Ereignissen an IoT Hub sowie zum Empfangen von Nachrichten vom IoT Hub. Wenn Sie diese Ebene verwenden, sind Sie für das Implementieren der Nachrichtenserialisierung verantwortlich, aber andere Details für die Kommunikation mit IoT Hub werden für Sie übernommen.

-   **Serialisierungsprogramm**: Der **serializer**-Ordner enthält die Implementierung einer Bibliothek auf der Basis von **IoTHubClient**. Die Bibliothek des **Serialisierungsprogramms** fügt zusätzlich zu den Funktionen von **IoTHubClient** vor allem Modellierungsfunktionen hinzu. Wenn Sie die Bibliothek des **Serialisierungsprogramms** verwenden, beginnen Sie zunächst mit der Definition eines Modells, das die Ereignisse angibt, die Sie an IoT Hub senden möchten, sowie die Nachrichten, die Sie davon empfangen möchten. Sobald das Modell definiert ist, bietet das SDK Ihnen eine API-Oberfläche, mit der Sie problemlos mit Ereignissen und Nachrichten arbeiten können, ohne sich um Serialisierungsdetails Gedanken machen zu müssen.

All dies ist einfacher anhand einiger Codebeispiele zu verstehen. Die folgenden Abschnitte enthalten eine Reihe von Beispielanwendungen, die im SDK enthalten sind. Dies sollte Ihnen eine gute Vorstellung von den verschiedenen Funktionen der Architekturebenen des SDK sowie einen Überblick über die Funktionsweise der APIs geben.

## Vor dem Ausführen der Beispiele

Bevor Sie die Beispiele im Azure IoT-Geräte-SDK für C ausführen können, müssen Sie zwei Aufgaben ausführen: Bereiten Sie die Entwicklungsumgebung vor, und rufen Sie die Anmeldeberechtigungen des Geräts ab. Die [Infodatei](https://github.com/Azure/azure-iot-sdks/tree/master/c) im SDK enthält Anleitungen für beide Aufgaben. Die folgenden Abschnitte enthalten jedoch einige zusätzliche Kommentare für diese Anweisungen.

### Vorbereiten der Entwicklungsumgebung

Zunächst müssen Sie eine Kopie des SDK von GitHub abrufen und dann die Quelle erstellen. Sie sollten eine Kopie der Quelle aus dem **master**-Verzeichnis des GitHub-Repository abrufen:

<https://github.com/Azure/azure-iot-sdks>

Wenn Sie eine Kopie der Quelle heruntergeladen haben, müssen Sie einige Schritte durchführen, bevor Sie den Code ausführen können. Eine Zusammenfassung dieser Schritte finden Sie im SDK-Artikel unter [Prepare your development environment](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md). Ein Großteil der Aufgaben, die Sie für Windows durchführen müssen, hat mit der [Vorbereitung der QPID Proton-Bibliotheken](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#rebuilding-the-qpid-proton-library-in-windows) zu tun. Aber hier sind einige Tipps, die Ihnen dabei helfen, das in der Vorbereitungsanleitung beschriebene Verfahren durchzuführen:

-   Legen Sie die **PROTON\_PATH** -Umgebungsvariable beim Erstellen als **system**-Umgebungsvariable fest, wie hier gezeigt:

  ![](media/iot-hub-device-sdk-c-intro/07-EnvironmentVariables.PNG)

-   Wählen Sie bei der Installation des **CMake**-Hilfsprogramms die Option aus, mit der Sie **CMake** dem Systempfad für **alle Benutzer** hinzufügen (Sie können auch den **aktuellen Benutzer** hinzufügen):

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)

-   Achten Sie darauf, dass Sie die richtige Version von Python installieren. Unter Windows sollten Sie in der Regel die x86-Version installieren:

  ![](media/iot-hub-device-sdk-c-intro/09-Python.PNG)

Sie können die Standardoptionen im Installer verwenden.

-   Installieren Sie die *Git-Befehlszeilentools*, bevor Sie die **Developer-Eingabeaufforderung für VS2015** öffnen. Führen Sie folgende Schritte aus, um diese Tools zu installieren:

	1. Starten Sie das **Visual Studio 2015**-Setupprogramm (oder wählen Sie **Microsoft Visual Studio 2015** in der Systemsteuerung **unter Programme und Funktionen** und dann **Ändern** aus).
	2. Stellen Sie sicher, dass die Funktion **Git für Windows** im Installer aktiviert ist: ![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)
	3. Beenden Sie den Assistenten, um die Tools zu installieren.
	4. Fügen Sie das Git-Toolsverzeichnis **bin** der **PATH**-Systemumgebungsvariable hinzu. Unter Windows sieht das Ergebnis folgendermaßen aus: ![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)

Beim Ausführen des **build\_proton.cmd**-Skripts über die Eingabeaufforderung, ruft das Skript die Proton-Quelle aus dem GitHub-Repository ab und erstellt dann die Proton-Bibliotheken.

Wenn Sie die vorherigen Schritte abgeschlossen haben, können Sie die Windows-Beispiele erstellen, die auf AMQP basieren, indem Sie die entsprechende Visual Studio-Projektmappendatei öffnen und dann **Projektmappe erstellen** aus dem Menü **Erstellen** auswählen.

### Abrufen von Geräteanmeldeinformationen

Nachdem die Umgebung eingerichtet ist, müssen Sie nur noch einen Satz von Geräteanmeldeinformationen abrufen. Damit ein Gerät auf einen IoT Hub zugreifen kann, müssen Sie das Gerät zuerst der IoT Hub-Geräteregistrierung hinzufügen. Wenn Sie Ihr Gerät hinzufügen, erhalten Sie einen Satz von Geräteanmeldeinformationen, die Sie benötigen, damit das Gerät eine Verbindung zu einem IoT Hub herstellen kann. Die Beispielanwendungen im nächsten Abschnitt erwarten diese Anmeldeinformationen in Form einer **Geräteverbindungszeichenfolge**.

Das [Geräte-Explorer](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer)-Tool (Teil des Azure IoT-Geräte-SDK) verwendet die Azure IoT-Dienstbibliotheken, um verschiedene Funktionen im IoT Hub auszuführen, darunter das Hinzufügen von Geräten. Wenn Sie den Geräte-Explorer verwenden, um ein Gerät hinzuzufügen, erhalten Sie eine entsprechende Verbindungszeichenfolge; mit dieser Verbindungszeichenfolge können Sie die Beispielanwendungen ausführen.

Wenn Sie noch nicht bereits mit dem Prozess vertraut sind, beschreibt das folgende Verfahren, wie Sie den Geräte-Explorer verwenden können, um ein Gerät hinzuzufügen und eine Geräteverbindungszeichenfolge abzurufen.

Öffnen Sie **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** in **Visual Studio 2015**, und erstellen Sie die Projektmappe. Wenn Sie das Programm ausführen, wird diese Schnittstelle angezeigt:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Geben Sie Ihre **IoT Hub-Verbindungszeichenfolge** in das erste Feld ein, und klicken Sie auf die Schaltfläche **Aktualisieren**. Dadurch wird das Tool konfiguriert, sodass es mit IoT Hub kommunizieren kann.

Sobald die IoT Hub-Verbindungszeichenfolge konfiguriert ist, klicken auf die Registerkarte **Management**:

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Dort verwalten Sie die in Ihrem IoT Hub registrierten Geräte.

Erstellen Sie ein Gerät, indem Sie auf die Schaltfläche **Erstellen** klicken. Es wird ein Dialogfeld angezeigt, in dem mehrere Schlüssel (primär und sekundär) angegeben sind. Sie müssen lediglich eine **Geräte-ID** eingeben und auf die Schaltfläche **Erstellen** klicken.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Nach Erstellung des Geräts wird die Geräteliste mit allen registrierten Geräten aktualisiert, einschließlich des soeben erstellten. Wenn Sie mit der rechten Maustaste auf das neue Gerät klicken, wird das folgende Menü angezeigt:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Bei Auswahl der Option **Verbindungszeichenfolge für das ausgewählte Gerät kopieren** wird die Verbindungszeichenfolge für das Gerät in die Zwischenablage kopiert. Bewahren Sie eine Kopie auf. Sie werden sie beim Ausführen der unten beschriebenen Beispielanwendungen benötigen.

Wenn Sie die oben genannten Schritte abgeschlossen haben, können Sie mit der Ausführung des Codes beginnen. In den folgenden Abschnitten werden Sie Schritt für Schritt durch zwei Beispielprojektmappen im Azure Geräte-SDK für C geführt, um die Funktionen der Bibliotheken zu veranschaulichen. Beachten Sie, dass beide unten stehenden Beispiele eine Konstante am Anfang der wichtigsten Quelldatei aufweisen, mit der Sie eine Verbindungszeichenfolge eingeben können. Hier ist z. B. die entsprechende Zeile aus der **iothub\_client\_sample\_amqp**-Anwendung.

```
static const char* connectionString = "[device connection string]";
```

Wenn Sie der unten stehenden Beschreibung folgen möchten, geben Sie hier Ihre Geräteverbindungszeichenfolge ein und kompilieren die Projektmappe neu. Danach sind Sie in der Lage, das Beispiel auszuführen.

## IoTHubClient

Innerhalb des **iothub\_client**-Ordners im azure-iot-sdks-Repository gibt es einen **samples**-Ordner mit einer Anwendung namens **iothub\_client\_sample\_amqp**.

Die Windows-Version der **iothub\_client\_sample\_ampq**-Anwendung umfasst die folgende Visual Studio-Projektmappe:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

Das **Iothub\_client\_sample\_amqp**-Projekt umfasst vier Projekte. Den Beispielcode finden Sie im **iothub\_client\_sample\_amqp**-Projekt. Die Projektmappe enthält jedoch auch die Projekte **common**, **iothub\_amqp\_transport** und **iothub\_client** aus dem Azure IoT-Geräte-SDK für C. Bei der Arbeit mit dem SDK benötigen Sie immer das **common**-Projekt. Da dieses Beispiel auf AMQP beruht, müssen wir auch das **iothub\_amqp\_transport**-Projekt berücksichtigen (es gibt ein entsprechendes Projekt für HTTP). Und da das Beispiel die **IoTHubClient**-Bibliotheken verwendet, müssen wir auch das **iothub\_client**-Projekt in unsere Projektmappe einschließen.

Die Implementierung für die Beispielanwendung befindet sich in der **iothub\_client\_sample\_amqp.c**-Quelldatei:

  ![](media/iot-hub-device-sdk-c-intro/13-iothub_client_sample_amqp_c.PNG)

Anhand dieser Beispielanwendung zeigen wir Ihnen Schritt für Schritt, was für die Nutzung der **IoTHubClient**-Bibliothek erforderlich ist.


### Initialisieren der Bibliothek

Um mit der Arbeit mit den Bibliotheken zu beginnen, müssen Sie zuerst ein IoT Hub-Clienthandle erstellen:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Beachten Sie, dass eine Kopie der Geräteverbindungszeichenfolge an diese Funktion (die wir aus dem Geräte-Explorer abgerufen haben) übergeben wird. Wir legen auch das Protokoll fest, das wir verwenden möchten: In diesem Beispiel verwenden wird AMQP, aber HTTP ist ebenfalls möglich.

Wenn Sie ein gültiges **IOTHUB\_CLIENT\_HANDLE** haben, können Sie damit beginnen, die APIs abzurufen, um Ereignisse zu senden und Nachrichten von IoT Hub zu empfangen. Lassen Sie uns dies als Nächstes näher betrachten.

### Senden von Ereignissen

Für das Senden von Ereignissen an IoT Hub müssen Sie die folgenden Schritte ausführen:

Erstellen Sie zunächst eine Nachricht:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_Over_AMQP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText);
```

Senden Sie dann die Nachricht

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Jedes Mal, wenn Sie eine Nachricht senden, geben Sie einen Verweis auf eine Rückruffunktion an, die aufgerufen wird, wenn die Daten gesendet werden:

```
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %d with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Beachten Sie den Aufruf der **IoTHubMessage\_Destroy**-Funktion, wenn Sie mit der Nachricht fertig sind. Dies ist erforderlich, um die bei der Erstellung der Nachricht zugeordneten Ressourcen freizugeben.

### Empfangen von Nachrichten

Der Empfang einer Nachricht ist ein asynchroner Vorgang. Zuerst registrieren Sie einen Rückruf, der aufgerufen wird, wenn das Gerät eine Nachricht empfängt:

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Der letzte Parameter ist ein ungültiger Verweis. Im Beispiel handelt es sich um einen Verweis auf eine ganze Zahl. Es könnte sich jedoch auch um einen Verweis auf eine komplexere Datenstruktur handeln. Dies erlaubt es der Rückruffunktion, auf einem gemeinsamen Zustand mit dem Aufrufer dieser Funktion zu arbeiten.

Wenn das Gerät eine Nachricht empfängt, wird die registrierte Rückruffunktion aufgerufen:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) == IOTHUB_MESSAGE_OK)
    {
        (void)printf("Received Message [%d] with Data: <<<%.*s>>> & Size=%d\r\n", *counter, (int)size, buffer, (int)size);
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Beachten Sie, wie Sie mithilfe der **IoTHubMessage\_GetByteArray**-Funktion die Nachricht abrufen können, in diesem Beispiel eine Zeichenfolge.

### Aufheben der Initialisierung der Bibliothek

Wenn Sie alle Ereignisse gesendet und Nachrichten empfangen haben, können Sie die Initialisierung der IoT-Bibliothek aufheben. Verwenden Sie dazu den folgenden Funktionsaufruf:

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Dadurch werden die zuvor von der **IoTHubClient\_CreateFromConnectionString**-Funktion zugeordneten Ressourcen freigegeben.

Wie Sie sehen können, ist es einfach, mit der **IoTHubClient**-Bibliothek Ereignisse zu senden und Nachrichten zu empfangen. Die Bibliothek kümmert sich um die Details für die Kommunikation mit IoT Hub, darunter die Auswahl des Protokolls (was aus Sicht des Entwicklers eine einfache Konfigurationsoption ist).

Die **IoTHubClient**-Bibliothek bietet Ihnen auch präzise Kontrolle darüber, wie die Ereignisse serialisiert werden sollen, die Ihr Gerät an IoT Hub sendet. In einigen Fällen ist dies von Vorteil, aber in anderen Fällen ist dies ein Implementierungsdetail, um das Sie sich keine Gedanken machen möchten. Wenn dies der Fall ist, empfiehlt sich womöglich der Einsatz der Bibliothek des **Serialisierungsprogramms**, die wir als Nächstes erläutern werden.

## Serialisierungsprogramm

Im Prinzip basiert die Bibliothek des **Serialisierungsprogramms** auf der **IoTHubClient**-Bibliothek im SDK. Sie nutzt die **IoTHubClient**-Bibliothek für die zugrunde liegende Kommunikation mit IoT Hub, ergänzt sie jedoch durch Modellierungsfunktionen, die die Nachrichtenserialisierung für den Entwickler übernehmen. Die Funktionsweise der Bibliothek wird am besten anhand eines Beispiels verdeutlicht.

Innerhalb des **iothub\_client**-Ordners im azure-iot-sdks-Repository gibt es einen **samples**-Ordner mit einer Anwendung namens **simplesample\_amqp**. Die Windows-Version dieses Beispiels umfasst die folgende Visual Studio-Projektmappe:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

Das Beispiel selbst ist das **simplesample\_amqp**-Projekt. Wie bereits gezeigt, umfasst die Lösung die Projekte **common**, **iothub\_amqp\_transport** und **iothub\_client** vom Azure IoT-Geräte-SDK für C. Diese Beispielprojektmappe enthält jedoch auch ein zusätzliches Projekt mit dem Namen **serializer**: Dies ist die Bibliothek, die neben den Funktionen der **IoTHubClient**-Bibliothek Modellierungsunterstützung bietet.

Die Implementierung der Beispielanwendung befindet sich in der **iothub\_client\_sample\_amqp.c**-Quelldatei:

  ![](media/iot-hub-device-sdk-c-intro/15-simplesample_amqp_c.PNG)

Die folgenden Abschnitte führen Sie durch die wichtigsten Teile dieses Beispiels.

### Initialisieren der Bibliothek

Für die Arbeit mit Bibliotheken müssen Sie die Initialisierungs-APIs aufrufen:

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

Der Aufruf der **serializer\_init**-Funktion ist ein einmaliger Aufruf, um die zugrunde liegenden Bibliothek zu initialisieren. Anschließend rufen Sie die **IoTHubClient\_CreateFromConnectionString**-Funktion auf, wobei es sich um die gleiche API handelt wie im **IoTHubClient**-Beispiel. Mit diesem Aufruf wird die Geräteverbindungszeichenfolge festgelegt. (Hier wählen Sie auch das gewünschte Protokoll aus.) Beachten Sie, dass dieses Beispiel AMQP als Transportprotokoll verwendet; Sie könnten auch HTTP verwenden.

Rufen Sie abschließend die **CREATE\_MODEL\_INSTANCE**-Funktion auf. **WeatherStation** ist der Namespace des Modells, und **ContosoAnemometer** ist der Name des Modells. Sobald das Modell definiert ist, können Sie damit beginnen, Ereignisse zu senden und Nachrichten zu empfangen. Zunächst ist es jedoch wichtig, dass Sie verstehen, was ein Modell ist.

### Definieren des Modells

Ein Modell in der Bibliothek des **Serialisierungsprogramms** definiert die Ereignisse, die das Gerät an IoT Hub senden kann, sowie die Nachrichten, in der Modellierungssprache so genannte *Aktionen*, die es empfangen kann. Sie definieren ein Modell mit einem Satz von C-Makros, wie in der **simplesample\_amqp**-Beispielanwendung gezeigt:

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Die **BEGIN\_NAMESPACE** und **END\_NAMESPACE**-Makros verwenden beide den Namespace des Modells als Argument. Es wird erwartet, dass alles zwischen diesen Makros die Definition der Modelle und der Datenstrukturen ist, die die Modelle verwenden.

In diesem Beispiel gibt es ein einzelnes Modell namens **ContosoAnemometer**. Dieses Modell definiert zwei Ereignisse, die Ihr Gerät an IoT Hub senden kann: **DeviceId** und **WindSpeed**. Darüber hinaus werden drei Aktionen (Nachrichten) definiert, die Ihr Gerät empfangen kann: **TurnFanOn**, **TurnFanOff** und **SetAirResistance**. Jedes Ereignis hat einen Typ, und jede Aktion verfügt über einen Namen (und optional einen Satz von Parametern).

Die im Modell definierten Ereignisse und Aktionen definieren eine API-Oberfläche, über die Sie Ereignisse an IoT Hub senden und auf Nachrichten antworten können, die an das Gerät gesendet werden. Dies wird am besten anhand eines Beispiels verdeutlicht.

### Senden von Ereignissen

Das Modell definiert die Ereignisse, die Sie an IoT Hub senden können. Für dieses Beispiel bedeutet dies eines der beiden mit dem **WITH\_DATA**-Makro definierten Ereignissen. Wenn Sie beispielsweise ein **WindSpeed**-Ereignis an einen IoT Hub senden möchten, sind dazu einige Schritte notwendig. Zunächst müssen Sie die Daten festlegen, die gesendet werden sollen:

```
myWeather->WindSpeed = 15;
```

Dank des zuvor definierten Modells müssen Sie dazu nur einen Member einer Struktur festlegen. Als Nächstes müssen Sie das Ereignis serialisieren, das Sie senden möchten:

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

Mit diesem Code wird das Ereignis für einen Puffer serialisiert (Verweis mittels **destination**). Abschließend senden Sie das Ereignis mit folgendem Code an IoT Hub:

```
sendMessage(iotHubClientHandle, destination, destinationSize);
```

Dies ist eine Hilfsfunktion der Beispielanwendung, mit der die serialisierten Ereignisse an IoT Hub gesendet werden:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
    messageTrackingId++;
}
```

Dieser Code ist dem der **Iothub\_client\_sample\_amqp**-Anwendung sehr ähnlich. Dort haben wir eine Nachricht aus einem Bytearray erstellt und sie dann mit **IoTHubClient\_SendEventAsync** an IoT Hub gesendet. Danach müssen wir einfach das Nachrichtenhandle und den zuvor zugeordneten serialisierten Datenpuffer freigeben.

Der vorletzte Parameter von **IoTHubClient\_SendEventAsync** ist ein Verweis auf eine Rückruffunktion, die aufgerufen wird, wenn die Daten erfolgreich gesendet wurden. Hier ist ein Beispiel für eine Rückruffunktion:

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Der zweite Parameter ist ein Verweis auf den Benutzerkontext, sprich der gleiche Verweis, den wir an **IoTHubClient\_SendEventAsync** übergeben haben. In diesem Fall ist der Kontext ein einfacher Zähler; es kann sich jedoch um jeden beliebigen Kontext handeln.

So einfach ist das Senden von Ereignissen. Jetzt muss nur noch der Empfang von Nachrichten erläutert werden.

### Empfangen von Nachrichten

Der Empfang von Nachrichten hat eine ähnliche Funktionsweise wie Nachrichten in der **IoTHubClient**-Bibliothek. Zunächst registrieren Sie eine Nachrichtenrückruffunktion:

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Anschließend schreiben Sie die Rückruffunktion, die beim Empfang einer Nachricht aufgerufen wird:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Dies ist ein Codebaustein, sprich er ist für jede Projektmappe identisch. Diese Funktion empfängt die Nachricht und übernimmt mithilfe des Aufrufs von **EXECUTE\_COMMAND** die Weiterleitung an die entsprechende Funktion. Welche Funktion zu diesem Zeitpunkt aufgerufen wird, hängt im Grunde von der Definition der Aktionen in unserem Modell ab.

Wenn Sie eine Aktion in Ihrem Modell definieren, müssen Sie eine entsprechende Funktion implementieren, die aufgerufen wird, wenn das Gerät eine Nachricht empfängt. Ein Beispiel – Ihr Modell definiert folgende Aktion:

```
WITH_ACTION(SetAirResistance, int, Position)
```

In diesem Fall müssen Sie eine Funktion mit folgender Signatur definieren:

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Beachten Sie, dass der Name der Funktion dem Namen der Aktion im Modell entspricht und dass die Parameter der Funktion mit den für die Aktion angegebenen Parametern übereinstimmen. Der erste Parameter ist immer erforderlich und enthält einen Verweis auf die Instanz unseres Modells.

Wenn das Gerät eine Nachricht empfängt, die dieser Signatur entspricht, wird die entsprechende Funktion aufgerufen. Abgesehen davon, dass Sie die Codebausteine aus **IoTHubMessage** angeben müssen, müssen Sie für den Empfang von Nachrichten nur eine einfache Funktion für jede im Modell definierte Aktion definieren.

### Aufheben der Initialisierung der Bibliothek

Wenn Sie mit dem Senden von Daten und Empfangen von Nachrichten fertig sind, können Sie die Initialisierung der IoT-Bibliothek aufheben:

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Jede dieser drei Funktionen ist auf die drei zuvor beschriebenen Initialisierungsfunktionen abgestimmt. Durch Aufrufen dieser APIs können Sie sicherstellen, dass Sie zuvor zugeordnete Ressourcen freigeben.

## Nächste Schritte

Dieser Artikel beschreibt die Grundlagen der Arbeit mit Bibliotheken im **Azure IoT-Geräte-SDK für C**. Nach Lesen dieses Artikels sollten Sie genügend Informationen haben, um zu wissen, was im SDK enthalten ist, und um die Architektur zu verstehen. Dank der Windows-Beispiele sollten Sie in der Lage sein, direkt mit der Arbeit zu beginnen. Im nächsten Artikel wird das SDK näher vorgestellt. Sie erhalten [weitere Informationen zur IoTHubClient-Bibliothek](iot-hub-device-sdk-c-iothubclient.md).

<!---HONumber=Oct15_HO1-->