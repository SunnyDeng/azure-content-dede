<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Node.js) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Node.js" description="Learn how to use Service Bus queues in Azure. Code samples written in Node.js." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Service Bus Queues" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Einsatz von Servicebus-Warteschlangen

In diesem Leitfaden erfahren Sie, wie Sie die Servicebus-Warteschlangen verwenden können. Die Beispiele sind
in JavaScript geschrieben und verwenden das Azure-Modul Node.js. Die Szenarien
behandeln die Themen **Erstellen von Warteschlangen, Senden und Empfangen von
Nachrichten** und **Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen
finden Sie im Abschnitt [Nächste Schritte][Nächste Schritte].

## Inhaltsverzeichnis

-   [Was sind Servicebus-Warteschlangen?][Was sind Servicebus-Warteschlangen?]
-   [Erstellen eines Dienstnamespace][Erstellen eines Dienstnamespace]
-   [Abrufen der Standard-Anmeldeinformationen für den Namespace][Abrufen der Standard-Anmeldeinformationen für den Namespace]
-   [Erstellen einer Node.js-Anwendung][Erstellen einer Node.js-Anwendung]
-   [Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus][Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus]
-   [Gewusst wie: Erstellen einer Warteschlange][Gewusst wie: Erstellen einer Warteschlange]
-   [Gewusst wie: Senden von Nachrichten an eine Warteschlange][Gewusst wie: Senden von Nachrichten an eine Warteschlange]
-   [Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange][Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange]
-   [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten][Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]
-   [Nächste Schritte][Nächste Schritte]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="create-app"> </a>Erstellen einer Node.js-Anwendung

Erstellen Sie eine leere Node.js-Anwendung. Hinweise zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website][Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website], [Node.js-Clouddienst][Node.js-Clouddienst] (mithilfe von Windows PowerShell) oder [Website mit WebMatrix][Website mit WebMatrix].

## <a name="configure-app"> </a> Konfigurieren der Anwendung zur Verwendung eines Service Bus

Um Azure Service Bus zu verwenden, müssen Sie das
Node.js-Azure-Paket herunterladen und verwenden. Dieses Paket enthält eine Reihe von Bibliotheken,
die mit den Service Bus-REST-Diensten kommunizieren.

### Verwenden von Node-Paket-Manager (NPM) zum Beziehen des Pakets

1.  Verwenden Sie das Befehlsfenster **Windows PowerShell for Node.js**,
    um zum Ordner **c:\\node\\sbqueues\\WebRole1** zu navigieren,
    in dem Sie Ihre Beispielanwendung erstellt haben.

2.  Geben Sie **npm install azure** in das Befehlsfenster ein.
    Die Ausgabe sollte in etwa wie folgt aussehen:

        azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Sie können den Befehl **ls** manuell aufrufen, um sich davon zu überzeugen, dass der Ordner
    **node\_modules** erstellt wurde. Suchen Sie in diesem Ordner
    nach dem Paket **azure**. Dieses Paket enthält die Bibliotheken,
    die Sie für den Zugriff auf Service Bus-Warteschlangen benötigen.

### Importieren des Moduls

Verwenden Sie Editor oder einen anderen Texteditor, um die folgende
Zeile am Anfang der Datei **server.js** der Anwendung einzufügen:

    var azure = require('azure');

### Einrichten einer Azure-Servicebus-Verbindung

Das Azure-Modul entnimmt den Umgebungsvariablen AZURE\_SERVICEBUS\_NAMESPACE und AZURE\_SERVICEBUS\_ACCESS\_KEY die Informationen, die zum Herstellen einer Verbindung mit Ihrem Azure-Service Bus benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt wurden, müssen Sie die Kontoinformationen beim Aufruf von **createServiceBusService** angeben.

Ein Beispiel zum Festlegen der Umgebungsvariablen in einer Konfigurationsdatei für einen Azure-Cloud-Dienst finden Sie unter [Node.js-Clouddienst mit Speicher][Node.js-Clouddienst mit Speicher].

Ein Beispiel zum Festlegen der Umgebungsvariablen im Verwaltungsportal für eine Azure-Website finden Sie unter [Node.js-Webanwendung mit Speicher][Node.js-Webanwendung mit Speicher].

## <a name="create-queue"> </a>Erstellen einer Warteschlange

Das Objekt **ServiceBusService** ermöglicht es Ihnen, mit Warteschlangen zu arbeiten. Der
folgende Code erstellt ein **ServiceBusService**-Objekt. Fügen Sie es am
Anfang der Datei **server.js** hinzu, nach der Anweisung zum
Importieren des Azure-Moduls:

    var serviceBusService = azure.createServiceBusService();

Durch Aufrufen von **createQueueIfNotExists** für das Objekt
**ServiceBusService** wird die angegebene Warteschlange (falls vorhanden)
zurückgegeben, oder es wird eine neue Warteschlange mit dem angegebenen Namen erstellt. Der folgende Code
verwendet **createQueueIfNotExists**, um die Warteschlange "myqueue"
zu erstellen oder eine Verbindung mit dieser Warteschlange herzustellen:

    serviceBusService.createQueueIfNotExists('myqueue', function(error){
        if(!error){
            // Queue exists
        }
    });

**createServiceBusService** unterstützt zudem weitere Optionen, mit
denen Sie Standardeinstellungen für die Warteschlange überschreiben
können, wie zum Beispiel die Gültigkeitsdauer von Nachrichten oder die maximale Warteschlangengröße. Das folgende Beispiel zeigt, wie Sie die
maximale Warteschlangengröße auf 5 GB bei einer Gültigkeitsdauer von 1 Minute festlegen:

    var queueOptions = {
          MaxSizeInMegabytes: '5120',
          DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
        if(!error){
            // Queue exists
        }
    });

### Filter

Mit **ServiceBusService** können Sie optionale Filteroperationen auf Operationen ausführen. Filtervorgänge können Protokollierung, automatische Wiederholung usw. umfassen. Filter sind Objekte, die eine Methode mit einer Signatur implementieren:

        function handle (requestOptions, next)

Nachdem die Vorverarbeitung der Anforderungsoptionen angeschlossen ist, muss die Methode "next" aufrufen und hierbei eine Rückruffunktion mit der folgenden Signatur übergeben:

        function (returnObject, finalCallback, next)

Nachdem das returnObject-Objekt (die Antwort auf die an den Server gesendete Anforderung) verarbeitet wurde, muss in dieser Rückruffunktion entweder "next" aufgerufen werden, wenn die Tabelle vorhanden ist, um weitere Filter zu verarbeiten, oder andernfalls einfach "finallCallback" aufrufen, um den Dienstaufruf zu beenden.

Zwei Filter, die eine Wiederholungslogik implementieren, sind im Azure SDK für Node.js enthalten: **ExponentialRetryPolicyFilter** und **LinearRetryPolicyFilter**. Der folgende Code erstellt ein **ServiceBusService**-Objekt, das den **ExponentialRetryPolicyFilter** verwendet:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="send-messages"> </a>Senden von Nachrichten an eine Warteschlange

Zum Senden einer Nachricht an eine Service Bus-Warteschlange ruft die
Anwendung die Methode **sendQueueMessage** für das Objekt
**ServiceBusService** auf. Nachrichten, die an die
Servicebus-Warteschlangen gesendet (und von diesen empfangen) werden, sind
 **BrokeredMessage**-Objekte und verfügen über einen Satz von
Standardeigenschaften (zum Beispiel **Label** und **TimeToLive**),
ein Wörterbuch, in dem benutzerdefinierte anwendungsspezifische Eigenschaften enthalten sind, sowie einen Bestand beliebiger Anwendungsdaten. Eine Anwendung kann den Hauptteil einer Nachricht
festlegen, indem sie einen Zeichenfolgenwert als Nachricht weitergibt.
Erforderliche Standardeigenschaften werden mit den Standardwerten gefüllt.

Das folgende Beispiel zeigt, wie eine Testnachricht an die Warteschlange
"myqueue" mithilfe von **sendQueueMessage** gesendet wird:

    var message = {
        body: 'Test message',
        customProperties: {
            testproperty: 'TestValue'
        }};
    serviceBusService.sendQueueMessage('myqueue', message, function(error){
        if(!error){
            // message sent
        }
    });

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von
256 KB (der Header, der die standardmäßigen und die
benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben). Bei der Anzahl der Nachrichten, die in einer
Warteschlange aufgenommen werden können, besteht keine Beschränkung.
Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung
definiert. Die Obergrenze beträgt 5 GB.

## <a name="receive-messages"> </a>Empfangen von Nachrichten aus einer Warteschlange

Nachrichten werden von einer Warteschlange über die **receiveQueueMessage**
-Methode auf dem **ServiceBusService**
-Objekt empfangen. Nachrichten werden
standardmäßig aus der Warteschlange gelöscht, wenn sie gelesen wurden.
Sie können jedoch Nachrichten lesen (einen Blick darauf werfen) und sperren,
ohne sie aus der Warteschlange zu löschen, indem Sie den optionalen Parameter **isPeekLock** auf **true** festlegen.

Das Standardverhalten für das Lesen und Löschen der Nachricht als Teil
des Empfangsvorgangs ist das einfachste Modell. Es wird am besten
für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann,
wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich
ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und
dann abstürzt, bevor diese verarbeitet wird. Da die Nachricht von Service Bus als verarbeitet markiert wurde,
wird die vor dem Absturz verarbeitete Nachricht nicht berücksichtigt, wenn die
Anwendung neu gestartet und die Verarbeitung von Nachrichten wieder aufgenommen wird.

Wenn der Parameter **isPeekLock** auf **true** festgelegt ist, wird der
Empfangsvorgang zu einem zweistufigen Vorgang. Dadurch können Anwendungen
unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anforderung
erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht,
sperrt diese, um zu verhindern, dass andere Consumer sie erhalten,
und sendet sie dann an die Anwendung zurück. Nachdem die
Anwendung die Verarbeitung der Nachricht abgeschlossen hat
(oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt sie die zweite
Phase des Empfangsprozesses durch, indem sie die Methode **deleteMessage** aufruft und die zu löschende Nachricht als Parameter bereitstellt. Die Methode **deleteMessage**
markiert die Nachricht als verarbeitet, und entfernt sie aus der Warteschlange.

Das Beispiel unten zeigt, wie Nachrichten mithilfe von
**receiveQueueMessage** empfangen und verarbeitet werden können. In diesem Beispiel wird zuerst
eine Nachricht empfangen und gelöscht, und anschließend eine Nachricht
empfangen, wobei für **isPeekLock** "true" festgelegt ist. Anschließend wird die Nachricht mithilfe von **deleteMessage** gelöscht:

    serviceBusService.receiveQueueMessage('taskqueue', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
        }
    });
    serviceBusService.receiveQueueMessage(queueName, { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                }
            }
        }
    });

## <a name="handle-crashes"> </a> Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Ihnen bei der
ordnungsgemäßen Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn
eine Empfängeranwendung die Nachricht aus einem bestimmten Grund
nicht verarbeiten kann, so kann sie die Methode **unlockMessage**
für das Objekt **ServiceBusService** aufrufen. Dies führt dazu, dass Service Bus die
Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht,
damit sie erneut empfangen werden kann, und zwar entweder durch
dieselbe verarbeitende Anwendung oder eine andere.

Zudem wird einer in der Warteschlange gesperrten Nachricht ein
Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des
Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die
Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und
macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor
Aufrufen der Methode **deleteMessage** abstürzt, wird die Nachricht erneut an
die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig
 als **At Least Once Processing** (Verarbeitung mindestens einmal)
bezeichnet und bedeutet, dass jede Nachricht mindestens
einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung in dem Szenario
nicht zulässig ist, sollten Anwendungsentwickler ihrer Anwendung
zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies wird häufig durch
Verwendung der Eigenschaft **MessageId** der Nachricht erzielt, die über
mehrere Zustellungsversuche hinweg konstant bleibt.

## <a name="next-steps"> </a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Service Bus-Warteschlangen
vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements.][Warteschlangen, Themen und Abonnements.]
-   Besuchen Sie das [Azure SDK für Node][Azure SDK für Node]-Repository auf GitHub.

  [Nächste Schritte]: #next-steps
  [Erstellen eines Dienstnamespace]: #create-a-service-namespace
  [Abrufen der Standard-Anmeldeinformationen für den Namespace]: #obtain-default-credentials
  [Erstellen einer Node.js-Anwendung]: #create-app
  [Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus]: #configure-app
  [Gewusst wie: Erstellen einer Warteschlange]: #create-queue
  [Gewusst wie: Senden von Nachrichten an eine Warteschlange]: #send-messages
  [Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange]: #receive-messages
  [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #handle-crashes
  [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website]: /de-de/develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js-Clouddienst]: /de-de/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Website mit WebMatrix]: /de-de/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js-Clouddienst mit Speicher]: /de-de/develop/nodejs/tutorials/web-app-with-storage/
  [Node.js-Webanwendung mit Speicher]: /de-de/develop/nodejs/tutorials/web-site-with-storage/
  [Warteschlangen, Themen und Abonnements.]: http://msdn.microsoft.com/de-de/library/windowsazure/hh367516.aspx
  [Azure SDK für Node]: https://github.com/WindowsAzure/azure-sdk-for-node
