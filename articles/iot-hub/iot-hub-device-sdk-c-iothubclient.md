<properties
	pageTitle="Azure IoT-Geräte-SDK für C – IoTHubClient | Microsoft Azure"
	description="Zusätzliche Informationen zur IoTHubClient-Bibliothek im Azure IoT-Geräte-SDK für C"
	services="iot-hub"
	documentationCenter=""
	authors="olivierbloch"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/10/2015"
     ms.author="obloch"/>

# Microsoft Azure IoT-Geräte-SDK für C – weitere Informationen zu IoTHubClient

Im [ersten Artikel](iot-hub-device-sdk-c-intro.md) dieser Serie wurde das **Microsoft Azure IoT-Geräte-SDK für C** vorgestellt. Wie in diesem Artikel beschrieben wurde, gibt es zwei Architekturebenen im SDK. Basis ist die **IoTHubClient**-Bibliothek, die die direkte Kommunikation mit IoT Hub verwaltet. Zudem gibt es die Bibliothek des **Serialisierungsprogramms**, die darauf aufbaut, um Serialisierungsdienste bereitzustellen. In diesem Artikel stellen wir weitere Details zur **IoTHubClient**-Bibliothek zur Verfügung.

Im vorherigen Artikel wurde beschrieben, wie Sie die **IoTHubClient**-Bibliothek verwenden können, um Ereignisse an IoT Hub zu senden und Nachrichten zu empfangen. In diesem Artikel erklären wir Ihnen, wie Sie präziser bestimmen können, *wann* Sie Daten senden und empfangen. Dazu stellen wir Ihnen die **Low-Level-APIs** vor. Wir erläutern zudem, wie Sie Eigenschaften mithilfe der Eigenschaftsverarbeitungsfunktionen in der **IoTHubClient**-Bibliothek an Ereignisse anfügen (und sie von Nachrichten abrufen) können. Außerdem erfahren Sie mehr über die verschiedenen Methoden zum Verarbeiten von Nachrichten vom IoT Hub.

Am Ende dieses Artikels werden verschiedene Themen besprochen, u. a. weitere Informationen zu Geräteanmeldeinformationen und zum Ändern des Verhaltens von **IoTHubClient** über Konfigurationsoptionen.

Zur Erläuterung dieser Themen verwenden wir die **IoTHubClient**-SDK-Beispiele. Sehen Sie sich für weitere Informationen die **iothub\_client\_sample\_http**- und **iothub\_client\_sample\_amqp**-Anwendungen an, die im Azure IoT-Geräte-SDK für C enthalten sind. Alles nachfolgend Beschriebene wird in diesen Beispielen veranschaulicht.

## Die Low-Level-APIs

Im vorherigen Artikel haben wir die grundlegende Funktionsweise des **IotHubClient** im Kontext der **iothub\_client\_sample\_amqp**-Anwendung beschrieben. So wurde beispielsweise erklärt, wie die Bibliothek mit dem folgenden Code initialisiert wurde.

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Zudem haben wir erklärt, wie Ereignisse mit dem folgenden Funktionsaufruf gesendet wurden.

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Im Artikel wurde außerdem beschrieben, wie Sie Nachrichten durch Registrieren einer Rückruffunktion empfangen können.

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Der Artikel hat auch gezeigt, wie Sie mithilfe von Code Ressourcen wie die Folgenden freigeben.

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Für jede dieser APIs gibt es jedoch Begleitfunktionen:

-   IoTHubClient\_LL\_CreateFromConnectionString

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy


Der API-Name all dieser Funktionen beinhaltet die Zeichenfolge "LL". Abgesehen davon sind die Parameter für alle diese Funktionen mit ihren Nicht-LL-Gegenstücken identisch. Das Verhalten dieser Funktionen unterscheidet sich jedoch in einem wichtigen Punkt.

Beim Aufruf von **IoTHubClient\_CreateFromConnectionString** erstellen die zugrunde liegenden Bibliotheken einen neuen Thread, der im Hintergrund ausgeführt wird. Dieser Thread sendet Ereignisse an den IoT Hub und empfängt Nachrichten vom IoT Hub. Bei der Arbeit mit den "LL"-APIs wird kein solcher Thread erstellt. Die Erstellung des Hintergrundthreads stellt eine Vereinfachung für den Entwickler dar. Sie müssen sich nicht darum kümmern, dass Ereignisse explizit an den IoT Hub gesendet und Nachrichten vom IoT Hub empfangen werden; dies erfolgt automatisch im Hintergrund. Im Gegensatz dazu bieten die "LL"-APIs Ihnen bei Bedarf eine explizite Kontrolle über die Kommunikation mit dem IoT Hub.

Sehen Sie sich zur Veranschaulichung das folgende Beispiel an:

Beim Aufruf von **IoTHubClient\_SendEventAsync** wird im Grunde das Ereignis in einem Puffer platziert. Der beim Aufrufen von **IoTHubClient\_CreateFromConnectionString** erstellte Hintergrundthread überwacht diesen Puffer kontinuierlich und sendet alle darin enthaltenen Daten an IoT Hub. Dies geschieht im Hintergrund, während der Hauptthread gleichzeitig andere Aufgaben durchführt.

Gleichermaßen gilt: Beim Registrieren einer Rückruffunktion für Nachrichten mit **IoTHubClient\_SetMessageCallback** rufen Sie das SDK auf, damit der Hintergrundthread die Rückruffunktion aufruft, wenn eine Nachricht empfangen wird – und das unabhängig vom Hauptthread.

Die "LL"-APIs erstellen keinen Hintergrundthread. Stattdessen muss eine neue API aufgerufen werden, um explizit Daten zu senden und vom IoT Hub zu empfangen. Dies wird im folgenden Beispiel veranschaulicht.

Die **iothub\_client\_sample\_http**-Anwendung, die im SDK enthalten ist, zeigt die Low-Level-APIs. In diesem Beispiel werden Ereignisse mit Code wie dem Folgenden an den IoT Hub gesendet:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Die ersten drei Zeilen erstellen die Nachricht, und die letzte Zeile sendet das Ereignis. Wie oben bereits erwähnt, werden die Daten beim Senden eines Ereignisses jedoch einfach in einem Puffer platziert. Beim Aufrufen von **IoTHubClient\_LL\_SendEventAsync** findet keine Übertragung über das Netzwerk statt. Um die Daten tatsächlich an den IoT Hub zu übergeben, müssen Sie **IoTHubClient\_LL\_DoWork** wie im folgenden Beispiel gezeigt aufrufen:

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Dieser Code (aus der **iothub\_client\_sample\_http**-Anwendung) ruft wiederholt **IoTHubClient\_LL\_DoWork** auf. Bei jedem Aufruf von **IoTHubClient\_LL\_DoWork** werden einige Ereignisse aus dem Puffer an den IoT Hub gesendet. Zudem wird eine an das Gerät gesendete Nachricht in der Warteschlange abgerufen. Letzteres bedeutet, dass der Rückruf aufgerufen wird (vorausgesetzt, dass sich eine Nachricht in der Warteschlange befindet), wenn eine Rückruffunktion für Nachrichten registriert wurde. Wir würden eine solche Rückruffunktion mit einem Code ähnlich dem Folgenden registrieren:

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

**IoTHubClient\_LL\_DoWork** wird oftmals in einer Schleife aufgerufen, da bei jedem Aufruf *einige* gepufferte Ereignisse an den IoT Hub gesendet werden und *die nächste* Nachricht in der Warteschlange für das Gerät abgerufen wird. Der Aufruf ist keine Garantie dafür, dass alle gepufferten Ereignisse gesendet oder alle Nachrichten in der Warteschlange abgerufen werden. Wenn Sie alle Ereignisse im Puffer senden und dann mit der Verarbeitung fortfahren möchten, können Sie diese Schleife durch Code wie den Folgenden ersetzen:

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Dieser Code ruft **IoTHubClient\_LL\_DoWork** auf, bis alle Ereignisse im Puffer an den IoT Hub gesendet wurden. Achtung: Das bedeutet nicht, dass alle Nachrichten in der Warteschlange empfangen wurden. Das liegt zum Teil daran, dass die Überprüfung auf „alle“ Nachrichten keine allzu deterministische Aktion ist. Was würde geschehen, wenn Sie „alle“ Nachrichten abrufen und dann eine weitere direkt danach an das Gerät gesendet wird? Eine bessere Möglichkeit für dieses Problem ist ein programmierter Timeout. Die Nachrichtenrückruffunktion könnte beispielsweise jedes Mal einen Timer zurücksetzen, wenn sie aufgerufen wird. Anschließend schreiben Sie die Logik, um die Verarbeitung fortzusetzen, wenn z. B. in den letzten *x* Sekunden keine Nachrichten empfangen wurden.

Stellen Sie nach Abschluss der Übergabe von Ereignissen und des Empfangs von Nachrichten sicher, dass Sie die entsprechende Funktion aufrufen, um die Ressourcen zu bereinigen.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Es gibt im Grunde nur einen Satz von APIs zum Senden und Empfangen von Daten mit einem Hintergrundthread und einen weiteren Satz von APIs, die die gleiche Aufgabe ohne den Hintergrundthread ausführen. Viele Entwickler bevorzugen die Nicht-LL-APIs, aber die Low-Level-APIs sind nützlich, wenn der Entwickler explizite Kontrolle über die Netzwerkübertragungen haben möchte. Einige Geräte erfassen z. B. Daten kontinuierlich und übergeben Ereignisse nur in angegebenen Intervallen (z. B. einmal pro Stunde oder einmal pro Tag). Die Low-Level-APIs geben Ihnen die Möglichkeit, explizit zu steuern, wann Sie Daten an den IoT Hub senden und vom IoT Hub empfangen. Andere bevorzugen die Einfachheit der Low-Level-APIs. Alle Arbeiten finden auf dem Hauptthread statt anstatt im Hintergrund.

Unabhängig davon, für welches Modell Sie sich entscheiden, sollten Sie bei der Wahl der APIs stets konsistent sein. Wenn Sie als Erstes **IoTHubClient\_LL\_CreateFromConnectionString** aufrufen, verwenden Sie für weitere Arbeiten unbedingt nur die entsprechenden Low-Level-APIs:

-   IoTHubClient\_LL\_SendEventAsync

-   IoTHubClient\_LL\_SetMessageCallback

-   IoTHubClient\_LL\_Destroy

-   IoTHubClient\_LL\_DoWork

Das Gleiche gilt auch für den umgekehrten Fall: Wenn Sie mit **IoTHubClient\_CreateFromConnectionString** beginnen, dann verwenden Sie für jede weitere Verarbeitung die Nicht-LL-APIs.

Die **iothub\_client\_sample\_http**-Anwendung im Azure IoT-Geräte-SDK für C bietet ein vollständiges Beispiel für die Low-Level-APIs. Ein vollständiges Beispiel für Nicht-LL-APIs finden Sie unter der **iothub\_client\_sample\_amqp**-Anwendung.

## Verarbeitung von Eigenschaften

Bei der bisherigen Beschreibung des Sendevorgangs von Daten haben wir uns auf den Hauptteil, sprich den Text, der Nachricht bezogen. Betrachten Sie z. B. diesen Code:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

In diesem Beispiel wird eine Nachricht mit dem Text "Hello World" an den IoT Hub gesendet. Der IoT Hub ermöglicht jedoch auch, dass Eigenschaften an jede Nachricht angefügt werden. Eigenschaften sind Name-Wert-Paare, die an die Nachricht angefügt werden können. Wir können beispielsweise den vorherigen Code ändern, um eine Eigenschaft an die Nachricht anzufügen:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Zunächst wird **IoTHubMessage\_Properties** aufgerufen, an die die Verarbeitung der Nachricht übertragen wird. Wir erhalten einen **MAP\_HANDLE**-Verweis, der es uns ermöglicht, Eigenschaften hinzuzufügen. Letzteres wird durch Aufruf von **Map\_AddOrUpdate** erzielt, das einen Verweis auf ein MAP\_HANDLE, den Eigenschaftennamen und den Eigenschaftswert akzeptiert. Mit dieser API können wir so viele Eigenschaften wie gewünscht hinzufügen.

Wenn das Ereignis vom **Event Hub** gelesen wird, kann der Empfänger die Eigenschaften auflisten und die entsprechenden Werte abrufen. In .NET würde dies z. B. durch den Zugriff auf die [Properties-Auflistung des EventData-Objekts](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx) durchgeführt werden.

Im vorherigen Beispiel werden Eigenschaften an ein Ereignis angefügt, das an den IoT Hub gesendet wird. Eigenschaften können jedoch auch an Nachrichten angefügt werden, die vom IoT Hub empfangen wurden. Wenn Eigenschaften aus einer Nachricht abgerufen werden sollen, können wir Code wie den Folgenden in der Nachrichtenrückruffunktion verwenden:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

Durch den Aufruf von **IoTHubMessage\_Properties** wird der **MAP\_HANDLE**-Verweis zurückgegeben. Diesen Verweis übergeben wir dann an **Map\_GetInternals**, um einen Verweis auf ein Array von Name-Wert-Paaren (sowie die Anzahl der Eigenschaften) zu erhalten. An dieser Stelle geht es nur darum, die Eigenschaften aufzulisten, um die gewünschten Werte zu erhalten.

Sie müssen keine Eigenschaften in der Anwendung verwenden. Wenn Sie sie jedoch für Ereignisse festlegen oder aus Nachrichten abrufen müssen, wird dies durch die **IoTHubClient**-Bibliothek vereinfacht.

## Behandlung von Nachrichten

Wie bereits erwähnt reagiert die **IoTHubClient**-Bibliothek auf den Empfang von Nachrichten vom IoT Hub durch den Aufruf einer registrierten Rückruffunktion. Es gibt einen Rückgabeparameter dieser Funktion, der einer zusätzlichen Erläuterung bedarf. Hier ist ein Auszug der Rückruffunktion in der **iothub\_client\_sample\_http**-Beispielanwendung:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
	. . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Beachten Sie, dass der Rückgabetyp **IOTHUBMESSAGE\_DISPOSITION\_RESULT** ist und dass in diesem speziellen Fall **IOTHUBMESSAGE\_ACCEPTED** zurückgegeben wird. Mit dieser Funktion können wir auch andere Werte zurückgeben, die Auswirkungen darauf haben, wie die **IoTHubClient**-Bibliothek auf den Nachrichtenrückruf reagiert. Im Nachfolgenden finden Sie die verfügbaren Optionen:

-   **IOTHUBMESSAGE\_ACCEPTED**: Die Nachricht wurde erfolgreich verarbeitet. Die **IoTHubClient**-Bibliothek ruft die Rückruffunktion nicht erneut mit der gleichen Nachricht auf.

-   **IOTHUBMESSAGE\_REJECTED**: Die Nachricht wurde nicht verarbeitet und soll auch in Zukunft nicht verarbeitet werden. Die **IoTHubClient**-Bibliothek soll die Rückruffunktion nicht erneut mit der gleichen Nachricht aufrufen.

-   **IOTHUBMESSAGE\_ABANDONED**: Die Nachricht wurde nicht erfolgreich verarbeitet, die **IoTHubClient**-Bibliothek soll die Rückruffunktion jedoch erneut mit der gleichen Nachricht aufrufen.

Bei den ersten beiden Rückgabecodes sendet die **IoTHubClient**-Bibliothek eine Nachricht an den IoT Hub und gibt an, dass die Nachricht aus der Warteschlange des Geräts gelöscht und nicht erneut übermittelt werden soll. Das Endergebnis ist dasselbe (die Nachricht wird aus der Warteschlange des Geräts gelöscht), aber darüber hinaus wird weiterhin aufgezeichnet, ob die Nachricht akzeptiert oder abgelehnt wurde. Die Aufzeichnung dieses Unterschieds ist nützlich für Absender der Nachricht, die damit Feedback abrufen und herausfinden können, ob ein Gerät eine bestimmte Nachricht akzeptiert oder abgelehnt hat.

Im zweiten Fall wird eine Nachricht auch an den IoT Hub gesendet, in der jedoch darauf hingewiesen wird, dass die Nachricht erneut zugestellt werden soll. In der Regel verwerfen Sie eine Nachricht, wenn ein Fehler auftritt, Sie die Verarbeitung der Nachricht jedoch erneut versuchen möchten. Im Gegensatz dazu ist eine Ablehnung der Nachricht angemessen, wenn ein nicht behebbarer Fehler auftritt (oder wenn Sie einfach entscheiden, dass Sie die Nachricht nicht verarbeiten möchten).

Beachten Sie auf jeden Fall die unterschiedlichen Rückgabecodes, damit Sie das gewünschte Verhalten der **IoTHubClient**-Bibliothek auslösen können.

## Alternative Geräteanmeldeinformationen

Wie zuvor erläutert, müssen Sie bei der Arbeit mit der **IoTHubClient**-Bibliothek als Erstes ein **IOTHUB\_CLIENT\_HANDLE** durch einen Aufruf wie den Folgenden abrufen:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Bei den Argumenten von **IoTHubClient\_CreateFromConnectionString** handelt es sich um die Verbindungszeichenfolge des Geräts und einen Parameter, der das Protokoll angibt, das für die Kommunikation mit dem IoT Hub verwenden wird. Die Verbindungszeichenfolge hat ein Format, das folgendermaßen aussieht:

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Diese Zeichenfolge enthält vier Arten von Informationen: IoT Hub-Name, IoT Hub-Suffix, Geräte-ID und den gemeinsamen Zugriffsschlüssel. Beim Erstellen der IoT Hub-Instanz im Azure-Portal rufen Sie den vollqualifizierten Domänennamen (FQDN) eines IoT Hubs ab: Damit erhalten Sie zugleich den IoT Hub-Namen (der erste Teil des FQDNs) und das IoT Hub-Suffix (der Rest des FQDNs). Die Geräte-ID und den gemeinsamen Zugriffsschlüssel erhalten Sie, wenn Sie das Gerät beim IoT Hub registrieren (wie im [vorherigen Artikel](iot-hub-device-sdk-c-intro.md) beschrieben).

**IoTHubClient\_CreateFromConnectionString** bietet eine Möglichkeit, die Bibliothek zu initialisieren. Sie können auch ein neues **IOTHUB\_CLIENT\_HANDLE** mithilfe der einzelnen Parameter anstelle der Verbindungszeichenfolge erstellen. Verwenden Sie dafür den folgenden Code:

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Dies hat den gleichen Zweck wie **IoTHubClient\_CreateFromConnectionString**.

Es scheint naheliegend, dass Sie **IoTHubClient\_CreateFromConnectionString** anstelle dieser ausführlichen Initialisierungsmethode verwenden wollen. Bedenken Sie jedoch, dass wir beim Registrieren eines Geräts im IoT Hub eine Geräte-ID und einen Geräteschlüssel (keine Verbindungszeichenfolge) erhalten. Das **Geräte-Manager**-SDK-Tool, das im [vorherigen Artikel](iot-hub-device-sdk-c-intro.md) vorgestellt wurde, verwendet Bibliotheken aus dem **Azure IoT-Dienst-SDK**, um die Verbindungszeichenfolge aus der Geräte-ID, dem Geräteschlüssel und dem IoT Hub-Hostnamen zu erstellen. Daher scheint der Aufruf von **IoTHubClient\_LL\_Create** die bessere Lösung zu sein, da Sie keine Verbindungszeichenfolge generieren müssen. Verwenden Sie die zweckmäßige Methode.

## Konfigurationsoptionen

Die bislang beschriebene Funktionsweise der **IoTHubClient**-Bibliothek stellt das Standardverhalten dar. Sie können jedoch einige Optionen festlegen, um die Funktionsweise der Bibliothek zu ändern. Nutzen Sie dazu die **IoTHubClient\_LL\_SetOption**-API. Betrachten Sie dieses Beispiel:

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Es gibt eine Reihe von Optionen, die häufig verwendet werden:

-   **SetBatching** (boolesch): Bei **true** werden an den IoT Hub gesendete Daten in Batches gesendet. Bei **false** werden Nachrichten einzeln gesendet. Die Standardeinstellung ist **false**.

-   **Timeout** (ganze Zahl ohne Vorzeichen): Dieser Wert wird in Millisekunden dargestellt. Wenn das Senden einer HTTP-Anforderung oder der Empfang einer Antwort länger dauert, kommt es zu einem Timeout der Verbindung.

Die Batchverarbeitung ist eine wichtige Option. Standardmäßig übergibt die Bibliothek Eingangsereignisse einzeln (alles, was Sie an **IoTHubClient\_LL\_SendEventAsync** übergeben, ist ein einzelnes Ereignis). Aber wenn die Batchverarbeitungsoption auf **true** festgelegt ist, erfasst die Bibliothek so viele Ereignisse wie möglich aus dem Puffer (bis zur maximalen Nachrichtengröße, die der IoT Hub akzeptiert). Der Ereignisbatch wird in einem einzigen HTTP-Aufruf an IoT Hub gesendet (die einzelnen Ereignisse werden in einem JSON-Array zusammengefasst). Mit der Batchverarbeitung erzielen Sie in der Regel große Leistungsgewinne, da Sie die Netzwerkroundtrips reduzieren. Zugleich wird die Bandbreite deutlich reduziert, da Sie einen Satz von HTTP-Headern mit einem Ereignisbatch senden, anstatt einen Satz von Headern für jedes einzelne Ereignis zu senden. In der Regel sollten Sie die Batchverarbeitung einsetzen, sofern kein bestimmter Grund dagegen spricht.

## Nächste Schritte

In diesem Artikel wird das Verhalten der **IoTHubClient**-Bibliothek im **Azure IoT-Geräte-SDK für C** ausführlich beschrieben. Diese Informationen sollten Ihnen eine umfassende Übersicht über die Funktionen der **IoTHubClient**-Bibliothek bieten. Im [nächsten Artikel](iot-hub-device-sdk-c-serializer.md) stellen wir ähnliche Details zur Bibliothek des **Serialisierungsprogramms** vor.

<!---HONumber=AcomDC_0211_2016-->