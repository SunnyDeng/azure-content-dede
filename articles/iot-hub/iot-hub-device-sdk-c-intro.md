<properties
	pageTitle="Verwenden des Azure IoT-Geräte-SDK für C | Microsoft Azure"
	description="Erfahren Sie mehr über den Beispielcode im Azure IoT-Geräte-SDK für C sowie über die ersten Schritte."
	services="iot-hub"
	documentationCenter=""
	authors="MichelBarnett"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/10/2015"
     ms.author="michelb"/>

# Einführung in das Azure IoT-Geräte-SDK für C

Das **Azure IoT-Geräte-SDK** ist ein Satz von Bibliotheken, die konzipiert wurden, um den Versand von Ereignissen und den Empfang von Nachrichten vom **Azure IoT Hub**-Dienst zu vereinfachen. Es gibt verschiedene Varianten des SDK für die unterschiedlichen Plattformen. In diesem Artikel wird aber das **Azure IoT-Geräte-SDK für C** beschrieben.

Das Azure IoT-Geräte-SDK für C wurde für optimale Portabilität in ANSI C (C99) geschrieben. Dies vereinfacht den Einsatz auf einer Reihe von Plattformen und Geräten – vor allem, wenn es wichtig ist, die Festplatten- und Speichergröße zu reduzieren.

Es gibt eine Vielzahl von Plattformen, auf denen das SDK getestet wurde. (Einzelheiten finden Sie in der [SDK-Dokumentation](https://github.com/Azure/azure-iot-sdks/tree/master/c).) Denken Sie daran, dass der in diesem Artikel beschriebene Code für alle unterstützten Plattformen identisch ist, obwohl in diesem Artikel der Beispielcode für die Windows-Plattform verwendet wird.

In diesem Artikel erhalten Sie einen Einblick in die Architektur des Azure IoT-Geräte-SDK für C. Wir zeigen, wie Sie die Gerätebibliothek initialisieren, Ereignisse an IoT Hub senden und vom IoT Hub Nachrichten empfangen können. Die Informationen in diesem Artikel sollten ausreichen, um mit der Arbeit mit dem SDK zu beginnen. Zugleich erhalten Sie Hinweise darauf, wo Sie weitere Informationen zu den Bibliotheken finden können.

## SDK-Architektur

Das **Azure IoT-Geräte-SDK für C** befindet sich im folgenden GitHub-Repository:

[azure-iot-sdks](https://github.com/Azure/azure-iot-sdks)

Die aktuelle Version der Bibliotheken finden Sie im **master**-Verzeichnis dieses Repository:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

Dieses Repository enthält die gesamte Familie der Azure IoT-Geräte-SDKs. Aber in diesem Artikel geht es um das Azure IoT-Geräte-SDK für *C*, das sich im Ordner **c** befindet.

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

Die zentrale Implementierung des SDK finden Sie im Repository in den Ordnern **common**, **iothub\_client** und **serializer**. Der **common**-Ordner enthält Code, der von allen Bibliotheken gemeinsam verwendet wird. (In der Regel verwenden Sie den Code nicht direkt im Ordner **common**). Die Ordner **iothub\_client** und **serializer** enthalten jedoch Implementierungen der beiden unterschiedlichen Ebenen des SDK, die Sie in Ihrem Code verwenden.

-   **IoTHubClient**: Der **iothub\_client**-Ordner enthält die Implementierung der niedrigsten API-Ebene im SDK, die **IoTHubClient**-Bibliothek. Die **IoTHubClient**-Bibliothek enthält APIs zum Senden von Ereignissen an IoT Hub sowie zum Empfangen von Nachrichten von IoT Hub. Wenn Sie diese Bibliothek verwenden, müssen Sie die Nachrichtenserialisierung implementieren. Andere Details zur Kommunikation mit IoT Hub werden für Sie aber übernommen.

-   **Serialisierungsprogramm**: Der **serializer**-Ordner enthält die Implementierung der Bibliothek des **Serialisierungsprogramms**. Die Bibliothek des **Serialisierungsprogramms** fügt zusätzlich zu den Funktionen von **IoTHubClient** Modellierungsfunktionen hinzu. Wenn Sie die Bibliothek des **Serialisierungsprogramms** verwenden, beginnen Sie zunächst mit der Definition eines Modells, das sowohl die Ereignisse, die Sie an IoT Hub senden möchten, als auch die Nachrichten angibt, die Sie von IoT Hub empfangen möchten. Sobald das Modell definiert ist, bietet das SDK Ihnen eine API-Oberfläche, mit der Sie problemlos mit Ereignissen und Nachrichten arbeiten können, ohne sich um Serialisierungsdetails Gedanken machen zu müssen.

All dies ist einfacher anhand von Codebeispielen zu verstehen. Die folgenden Abschnitte enthalten eine Reihe von Beispielanwendungen, die im SDK enthalten sind. Dies sollte Ihnen eine gute Vorstellung von den verschiedenen Funktionen der Architekturebenen des SDK sowie einen Überblick über die Funktionsweise der APIs geben.

## Vor dem Ausführen der Beispiele

Bevor Sie die Beispiele im Azure IoT-Geräte-SDK für C ausführen können, müssen Sie zwei Aufgaben ausführen: Bereiten Sie die Entwicklungsumgebung vor, und rufen Sie die Anmeldeberechtigungen des Geräts ab. Die [Infodatei](https://github.com/Azure/azure-iot-sdks/tree/master/c) im SDK enthält Anleitungen für beide Aufgaben. Die folgenden Abschnitte enthalten einige zusätzliche Kommentare zu diesen Anweisungen.

### Vorbereiten der Entwicklungsumgebung

Zunächst müssen Sie eine Kopie des SDK von GitHub abrufen und dann die Quelle erstellen. Sie sollten eine Kopie der Quelle aus dem **master**-Verzeichnis des [GitHub-Repository](https://github.com/Azure/azure-iot-sdks) abrufen.

Wenn Sie eine Kopie der Quelle heruntergeladen haben, müssen Sie die Schritte ausführen, die im SDK-Artikel [Vorbereiten Ihrer Entwicklungsumgebung](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md) beschrieben sind. Zu den Aufgaben, die Sie für Windows ausführen müssen, gehören die Vorbereitung der Qpid Proton-Bibliotheken (siehe [Preparing Qpid Proton libraries in Windows](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#preparing-qpid-proton-libraries-in-windows)) sowie die Überprüfung der Umgebung (siehe [Verify your environment](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#verify-your-environment)). Im Folgenden sind einige Tipps angegeben, die Ihnen dabei helfen, das in der Vorbereitungsanleitung beschriebene Verfahren durchzuführen:

-   Legen Sie die **PROTON\_PATH** -Umgebungsvariable beim Erstellen als **system**-Umgebungsvariable fest, wie hier gezeigt:

  ![](media/iot-hub-device-sdk-c-intro/07-EnvironmentVariables.PNG)

-   Wählen Sie bei der Installation des **CMake**-Hilfsprogramms die Option aus, mit der Sie **CMake** dem Systempfad für **alle Benutzer** hinzufügen (Sie können auch den **aktuellen Benutzer** hinzufügen):

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)

-   Achten Sie darauf, dass Sie die richtige Version von Python installieren. Unter Windows sollten Sie in der Regel die x86-Version installieren:

  ![](media/iot-hub-device-sdk-c-intro/09-Python.PNG)

Sie können die Standardoptionen im Installer verwenden.

- Fügen Sie das Python-Verzeichnis der **PATH**-Systemumgebungsvariable hinzu. Unter Windows sieht das Ergebnis wie folgt aus:
- 
![](media/iot-hub-device-sdk-c-intro/16-PythonPath.PNG)

-   Installieren Sie die Git-Befehlszeilentools, bevor Sie die **Developer-Eingabeaufforderung für VS2015** öffnen. Führen Sie folgende Schritte aus, um diese Tools zu installieren:

	1. Starten Sie das **Visual Studio 2015**-Setupprogramm (oder wählen Sie **Microsoft Visual Studio 2015** in der Systemsteuerung unter **Programme und Funktionen** aus, und wählen Sie dann **Ändern** aus).
	
	2. Stellen Sie sicher, dass die Funktion **Git für Windows** im Installationsprogramm ausgewählt ist. Sie sollten außerdem die Option **GitHub-Erweiterung für Visual Studio** aktivieren, um die IDE-Integration bereitzustellen:

  		![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)

	3. Beenden Sie den Assistenten, um die Tools zu installieren.

	4. Fügen Sie das Git-Toolsverzeichnis **bin** zur **PATH**-Systemumgebungsvariable hinzu. Unter Windows sieht das Ergebnis wie folgt aus:

  		![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)

Beim Ausführen des **build\_proton.cmd**-Skripts von der Eingabeaufforderung lädt das Skript die Proton-Quelle aus dem GitHub-Repository herunter und erstellt dann die Proton-Bibliotheken.

Wenn Sie die MQTT-Beispiele ausführen möchten, befolgen Sie die Anweisungen zum Erstellen der Apache MQTT-Bibliothek (siehe SDK-Dokumentation unter [Building the Apache MQTT library in Windows](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md#building-the-apache-mqtt-library-in-windows)). Unabhängig davon müssen Sie zum Schluss **build.cmd** ausführen.

Wenn Sie die vorherigen Schritte durchgeführt haben, können Sie die Beispielanwendungen kompilieren.

### Abrufen von Geräteanmeldeinformationen

Nachdem die Umgebung eingerichtet ist, müssen Sie nur noch einen Satz von Geräteanmeldeinformationen abrufen. Damit ein Gerät auf einen IoT Hub zugreifen kann, müssen Sie das Gerät zuerst der IoT Hub-Geräteregistrierung hinzufügen. Wenn Sie Ihr Gerät hinzufügen, erhalten Sie einen Satz von Geräteanmeldeinformationen, die Sie benötigen, damit das Gerät eine Verbindung zu einem IoT Hub herstellen kann. Die Beispielanwendungen im nächsten Abschnitt erwarten diese Anmeldeinformationen in Form einer **Geräteverbindungszeichenfolge**.

Das [Geräte-Explorer](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer)-Tool (Teil des Azure IoT-Geräte-SDK) verwendet die Azure IoT-Dienstbibliotheken, um verschiedene Funktionen in IoT Hub auszuführen, z. B. das Hinzufügen von Geräten. Wenn Sie den Geräte-Explorer zum Hinzufügen eines Geräts verwenden, erhalten Sie eine entsprechende Verbindungszeichenfolge. Sie benötigen diese Verbindungszeichenfolge, um die Beispielanwendungen auszuführen.

Wenn Sie noch nicht bereits mit dem Prozess vertraut sind, beschreibt das folgende Verfahren, wie Sie den Geräte-Explorer verwenden können, um ein Gerät hinzuzufügen und eine Geräteverbindungszeichenfolge abzurufen.

Öffnen Sie **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** in **Visual Studio 2015**, und erstellen Sie die Projektmappe. Wenn Sie das Programm ausführen, wird diese Schnittstelle angezeigt:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Geben Sie Ihre **IoT Hub-Verbindungszeichenfolge** in das erste Feld ein, und klicken Sie auf **Aktualisieren**. Dadurch wird das Tool konfiguriert, sodass es mit IoT Hub kommunizieren kann.

Sobald die IoT Hub-Verbindungszeichenfolge konfiguriert ist, klicken Sie auf die Registerkarte **Management**:

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Dort verwalten Sie die in Ihrem IoT Hub registrierten Geräte.

Sie können ein Gerät erstellen, indem Sie auf die Schaltfläche **Erstellen** klicken. Es wird ein Dialogfeld angezeigt, in dem bereits mehrere Schlüssel (primär und sekundär) angegeben sind. Sie müssen lediglich eine **Geräte-ID** eingeben und dann auf **Erstellen** klicken.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Nach Erstellung des Geräts wird die Geräteliste mit allen registrierten Geräten aktualisiert, einschließlich des soeben erstellten Geräts. Wenn Sie mit der rechten Maustaste auf das neue Gerät klicken, wird das folgende Menü angezeigt:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Bei Auswahl der Option **Verbindungszeichenfolge für das ausgewählte Gerät kopieren** wird die Verbindungszeichenfolge für das Gerät in die Zwischenablage kopiert. Behalten Sie eine Kopie der Verbindungszeichenfolge bei. Sie werden sie beim Ausführen der in den nächsten Abschnitten beschriebenen Beispielanwendungen benötigen.

Wenn Sie die oben genannten Schritte abgeschlossen haben, können Sie mit der Ausführung des Codes beginnen. Beide Beispiele weisen am Anfang der Hauptquelldatei eine Konstante auf, mit der Sie eine Verbindungszeichenfolge eingeben können. Die entsprechende Zeile aus der **iothub\_client\_sample\_amqp**-Anwendung wird beispielsweise wie folgt angezeigt:

```
static const char* connectionString = "[device connection string]";
```

Wenn Sie der unten stehenden Beschreibung folgen möchten, geben Sie hier Ihre Geräteverbindungszeichenfolge ein und kompilieren die Projektmappe neu. Danach können Sie das Beispiel ausführen.

## IoTHubClient

Innerhalb des **iothub\_client**-Ordners im azure-iot-sdks-Repository gibt es einen **samples**-Ordner mit einer Anwendung namens **iothub\_client\_sample\_amqp**.

Die Windows-Version der **iothub\_client\_sample\_ampq**-Anwendung umfasst die folgende Visual Studio-Projektmappe:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

Die Lösung umfasst ein einziges Projekt. Beachten Sie, dass in dieser Projektmappe vier NuGet-Pakete installiert sind:

  ![](media/iot-hub-device-sdk-c-intro/17-iothub-client-sample-amqp-githubpackages.PNG)

Zum Arbeiten mit dem SDK benötigen Sie immer das Paket **Microsoft.Azure.IoTHub.Common**. Da dieses Beispiel auf AMQP beruht, müssen Sie auch die Pakete **Apache.QPID.Proton.AzureIoT** und **Microsoft.Azure.IoTHub.AmqpTransport** einschließen (für HTTP sind entsprechende Pakete vorhanden). Da im Beispiel die **IoTHubClient**-Bibliothek verwendet wird, müssen Sie auch das **Microsoft.Azure.IoTHub.IoTHubClient**-Paket in unsere Projektmappe einschließen.

Die Implementierung für die Beispielanwendung befindet sich in der **iothub\_client\_sample\_amqp.c**-Quelldatei:

  ![](media/iot-hub-device-sdk-c-intro/13-iothub_client_sample_amqp_c.PNG)

Anhand dieser Beispielanwendung zeigen wir Ihnen Schritt für Schritt, was für die Nutzung der **IoTHubClient**-Bibliothek erforderlich ist.

### Initialisieren der Bibliothek

Um mit der Arbeit mit den Bibliotheken zu beginnen, müssen Sie zuerst ein IoT Hub-Clienthandle erstellen:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Beachten Sie, dass eine Kopie der Geräteverbindungszeichenfolge an diese Funktion (die wir aus dem Geräte-Explorer abgerufen haben) übergeben wird. Wir legen auch das Protokoll fest, das wir verwenden möchten. In diesem Beispiel wird AMQP verwendet, aber HTTP ist auch eine Option.

Wenn Sie ein gültiges **IOTHUB\_CLIENT\_HANDLE** erstellt haben, können Sie damit beginnen, die APIs abzurufen, um Ereignisse an IoT Hub zu senden und Nachrichten von IoT Hub zu empfangen. Lassen Sie uns dies als Nächstes näher betrachten.

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

Beachten Sie den Aufruf der **IoTHubMessage\_Destroy**-Funktion, wenn Sie mit der Nachricht fertig sind. Sie müssen diesen Aufruf durchführen, um die bei der Erstellung der Nachricht zugeordneten Ressourcen freizugeben.

### Empfangen von Nachrichten

Der Empfang einer Nachricht ist ein asynchroner Vorgang. Zuerst registrieren Sie einen Rückruf, der aufgerufen wird, wenn das Gerät eine Nachricht empfängt:

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Der letzte Parameter ist ein ungültiger Zeiger. Im Beispiel handelt es sich um einen Zeiger auf einen integer-Wert. Es könnte sich jedoch auch um einen Zeiger auf eine komplexere Datenstruktur handeln. Dies erlaubt es der Rückruffunktion, auf einem gemeinsamen Zustand mit dem Aufrufer dieser Funktion zu arbeiten.

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

Beachten Sie, dass Sie die **IoTHubMessage\_GetByteArray**-Funktion zum Abrufen der Nachricht verwenden können. In diesem Beispiel ist dies eine Zeichenfolge.

### Aufheben der Initialisierung der Bibliothek

Wenn Sie mit dem Senden von Ereignissen und Empfangen von Nachrichten fertig sind, können Sie die Initialisierung der IoT-Bibliothek aufheben. Geben Sie hierzu den folgenden Funktionsaufruf aus:

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Dadurch werden die zuvor von der **IoTHubClient\_CreateFromConnectionString**-Funktion zugeordneten Ressourcen freigegeben.

Wie Sie sehen, ist es ganz einfach, mit der **IoTHubClient**-Bibliothek Ereignisse zu senden und Nachrichten zu empfangen. Die Bibliothek kümmert sich um die Details für die Kommunikation mit IoT Hub, z. B. die Auswahl des Protokolls (aus Sicht des Entwicklers ist dies eine einfache Konfigurationsoption).

Die **IoTHubClient**-Bibliothek ermöglicht auch eine präzise Kontrolle darüber, wie die Ereignisse serialisiert werden sollen, die Ihr Gerät an IoT Hub sendet. In einigen Fällen ist dies von Vorteil, aber in anderen Fällen ist dies ein Implementierungsdetail, um das Sie sich keine Gedanken machen möchten. Wenn dies der Fall ist, empfiehlt sich unter Umständen der Einsatz der Bibliothek des **Serialisierungsprogramms**, die im nächsten Abschnitt beschrieben wird.

## Serialisierungsprogramm

Im Prinzip basiert die Bibliothek des **Serialisierungsprogramms** auf der **IoTHubClient**-Bibliothek im SDK. Sie nutzt die **IoTHubClient**-Bibliothek für die zugrunde liegende Kommunikation mit IoT Hub, ergänzt sie jedoch durch Modellierungsfunktionen, die die Nachrichtenserialisierung für den Entwickler übernehmen. Die Funktionsweise der Bibliothek wird am besten anhand eines Beispiels verdeutlicht.

Innerhalb des **serializer**-Ordners im azure-iot-sdks-Repository gibt es einen **samples**-Ordner mit einer Anwendung namens **simplesample\_amqp**. Die Windows-Version dieses Beispiels umfasst die folgende Visual Studio-Projektmappe:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

Genau wie im vorherigen Beispiel sind hier verschiedene NuGet-Pakete enthalten:

  ![](media/iot-hub-device-sdk-c-intro/18-simplesample_amqp-githubpackages.PNG)

Die meisten haben wir schon im vorherigen Beispiel gesehen, aber **Microsoft.Azure.IoTHub.Serializer** ist neu. Dieses Paket ist erforderlich, wenn wir die Bibliothek des **Serialisierungsprogramms** verwenden.

Die Implementierung für die Beispielanwendung befindet sich in der Datei **simplesample\_amqp.c**:

  ![](media/iot-hub-device-sdk-c-intro/15-simplesample_amqp_c.PNG)

Die folgenden Abschnitte führen Sie durch die wichtigsten Teile dieses Beispiels.

### Initialisieren der Bibliothek

Für die Arbeit mit der Bibliothek des **Serialisierungsprogramms** müssen Sie die Initialisierungs-APIs aufrufen:

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

Der Aufruf der **serializer\_init**-Funktion ist ein einmaliger Aufruf, der zum Initialisieren der zugrunde liegenden Bibliothek verwendet wird. Anschließend rufen Sie die **IoTHubClient\_CreateFromConnectionString**-Funktion auf, wobei es sich um die gleiche API handelt wie im **IoTHubClient**-Beispiel. Mit diesem Aufruf wird die Geräteverbindungszeichenfolge festgelegt. (Hier wählen Sie auch das gewünschte Protokoll aus.) Beachten Sie, dass in diesem Beispiel AMQP als Transportprotokoll verwendet wird. Die Verwendung von HTTP ist aber auch möglich.

Rufen Sie abschließend die **CREATE\_MODEL\_INSTANCE**-Funktion auf. Beachten Sie, dass **WeatherStation** der Namespace des Modells und **ContosoAnemometer** der Name des Modells ist. Sobald die Modellinstanz erstellt wurde, können Sie damit beginnen, Ereignisse zu senden und Nachrichten zu empfangen. Zunächst ist es aber wichtig zu verstehen, was ein Modell ist.

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

Die beiden Makros **BEGIN\_NAMESPACE** und **END\_NAMESPACE** verwenden den Namespace des Modells als Argument. Es wird erwartet, dass alles zwischen diesen Makros die Definition der Modelle und der Datenstrukturen ist, die die Modelle verwenden.

In diesem Beispiel gibt es ein einzelnes Modell namens **ContosoAnemometer**. Dieses Modell definiert zwei Ereignisse, die Ihr Gerät an IoT Hub senden kann: **DeviceId** und **WindSpeed**. Darüber hinaus werden drei Aktionen (Nachrichten) definiert, die Ihr Gerät empfangen kann: **TurnFanOn**, **TurnFanOff** und **SetAirResistance**. Jedes Ereignis hat einen Typ, und jede Aktion verfügt über einen Namen (und optional einen Satz von Parametern).

Die im Modell definierten Ereignisse und Aktionen definieren eine API-Oberfläche, über die Sie Ereignisse an IoT Hub senden und auf Nachrichten antworten können, die an das Gerät gesendet werden. Dies wird am besten anhand eines Beispiels verdeutlicht.

### Senden von Ereignissen

Das Modell definiert die Ereignisse, die Sie an IoT Hub senden können. In diesem Beispiel ist dies eines der beiden mit dem **WITH\_DATA**-Makro definierten Ereignisse. Wenn Sie beispielsweise ein **WindSpeed**-Ereignis an einen IoT Hub senden möchten, sind dazu einige Schritte notwendig. Zunächst müssen Sie die Daten festlegen, die gesendet werden sollen:

```
myWeather->WindSpeed = 15;
```

Dank des zuvor definierten Modells müssen Sie dazu nur einen Member einer **Struktur** festlegen. Als Nächstes serialisieren Sie das Ereignis, das Sie senden möchten:

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

Dieser Code ist dem der **iothub\_client\_sample\_amqp**-Anwendung sehr ähnlich. Darin haben wir eine Nachricht aus einem Bytearray erstellt und sie dann mit **IoTHubClient\_SendEventAsync** an IoT Hub gesendet. Danach müssen wir einfach das Nachrichtenhandle und den zuvor zugeordneten serialisierten Datenpuffer freigeben.

Der vorletzte Parameter von **IoTHubClient\_SendEventAsync** ist ein Verweis auf eine Rückruffunktion, die aufgerufen wird, wenn die Daten erfolgreich gesendet wurden. Hier ist ein Beispiel für eine Rückruffunktion angegeben:

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Der zweite Parameter ist ein Zeiger auf den Benutzerkontext, also der gleiche Zeiger, den wir an **IoTHubClient\_SendEventAsync** übergeben haben. In diesem Fall ist der Kontext ein einfacher Zähler. Es kann sich aber um jeden beliebigen Kontext handeln.

So einfach ist das Senden von Ereignissen. Jetzt muss nur noch der Empfang von Nachrichten erläutert werden.

### Empfangen von Nachrichten

Der Empfang von Nachrichten funktioniert ähnlich wie die Verarbeitung von Nachrichten in der **IoTHubClient**-Bibliothek. Zunächst registrieren Sie eine Nachrichtenrückruffunktion:

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

Dies ist ein Codebaustein, der für jede Projektmappe identisch ist. Diese Funktion empfängt die Nachricht und übernimmt mithilfe des Aufrufs von **EXECUTE\_COMMAND** die Weiterleitung an die entsprechende Funktion. Welche Funktion zu diesem Zeitpunkt aufgerufen wird, hängt von der Definition der Aktionen in unserem Modell ab.

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

Beachten Sie, dass der Name der Funktion dem Namen der Aktion im Modell entspricht und dass die Parameter der Funktion mit den für die Aktion angegebenen Parametern übereinstimmen. Der erste Parameter ist immer erforderlich und enthält einen Zeiger auf die Instanz unseres Modells.

Wenn das Gerät eine Nachricht empfängt, die dieser Signatur entspricht, wird die entsprechende Funktion aufgerufen. Abgesehen davon, dass Sie die Codebausteine aus **IoTHubMessage** angeben müssen, müssen Sie für den Empfang von Nachrichten daher nur eine einfache Funktion für jede im Modell definierte Aktion definieren.

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

In diesem Artikel wurden die Grundlagen der Arbeit mit Bibliotheken im **Azure IoT-Geräte-SDK für C** beschrieben. Nach dem Lesen dieses Artikels sollten Sie genügend Informationen haben, um zu wissen, was im SDK enthalten ist, und um die Architektur zu verstehen. Dank der Windows-Beispiele sollten Sie in der Lage sein, direkt mit der Arbeit zu beginnen. Im nächsten Artikel wird das SDK näher vorgestellt. Sie erhalten [weitere Informationen zur IoTHubClient-Bibliothek](iot-hub-device-sdk-c-iothubclient.md).

<!---HONumber=Nov15_HO3-->