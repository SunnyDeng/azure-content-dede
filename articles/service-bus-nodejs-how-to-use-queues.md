<properties urlDisplayName="Service Bus Queues" pageTitle="Gewusst wie: Verwenden von Servicebus-Warteschlangen (Node.js) - Azure" metaKeywords="Azure Service Bus-Warteschlangen, Azure-Warteschlangen, Azure Messaging, Azure-Warteschlangen, Node.js" description="Erfahren Sie mehr über die Verwendung von Service Bus-Warteschlangen in Azure. Die Codebeispiele wurden in Node.js geschrieben." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Service Bus Queues" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />






# Einsatz von Servicebus-Warteschlangen

In diesem Leitfaden erfahren Sie, wie Sie die Servicebus-Warteschlangen verwenden können. Die Beispiele sind
in JavaScript geschrieben und nutzen das Node.js-Azure-Modul. Die Szenarien
behandeln die Themen **Erstellen von Warteschlangen, Senden und Empfangen von Nachrichten** und
**Löschen der Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste
Schritte].

## Inhaltsverzeichnis

-   [Was sind Servicebus-Warteschlangen?][]
-   [Erstellen eines Dienstnamespace][]
-   [Abrufen der Standard-Anmeldeinformationen für den Namespace][]
-   [Erstellen einer Node.js-Anwendung](#create-app)
-   [Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus](#configure-app)
-   [Gewusst wie: Erstellen einer Warteschlange](#create-queue)
-   [Gewusst wie: Senden von Nachrichten an eine Warteschlange](#send-messages)
-   [Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange](#receive-messages)
-   [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten](#handle-crashes)
-   [Nächste Schritte](#next-steps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="create-app"> </a>Erstellen einer Node.js-Anwendung

Erstellen Sie eine leere Node.js-Anwendung. Hinweise zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website], [Node.js-Cloud-Dienst][Node.js-Cloud-Dienst] (mithilfe von Windows PowerShell) oder [Web Site mit WebMatrix].

## <a name="configure-app"> </a>Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus

Um Azure Service Bus zu verwenden, müssen Sie Folgendes herunterladen und verwenden:
Das Node.js-Azure-Paket. Dazu gehört ein Satz von praktischen Bibliotheken, die
mit den Service Bus-REST-Diensten kommunizieren.

### Verwenden von Node-Paket-Manager (NPM) zum Beziehen des Pakets

1.  Verwenden Sie das **Windows PowerShell für Node.js**-Befehlsfenster, um
zum Ordner **c:\\node\\sbqueues\\WebRole1** zu wechseln, in dem Sie
    die Beispielanwendung erstellt haben.

2.  Geben Sie **npm install azure** in das Befehlsfenster ein.
    Dies sollte eine Ausgabe ähnlich der folgenden ergeben:

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

3.  Sie können den Befehl **ls** manuell ausführen, um zu überprüfen, ob der Ordner
    **node\_modules** erstellt wurde. In diesem Ordner finden Sie ein
    **azure**-Paket, das die benötigten Bibliotheken für den Zugriff auf
    Servicebus-Warteschlangen enthält.

### Importieren des Moduls

Verwenden Sie Editor oder einen anderen Texteditor, um die folgende Zeile am Anfang der Datei
**server.js** der Anwendung hinzuzufügen:

    var azure = require('azure');

### Einrichten einer Azure-Servicebus-Verbindung

Das Azure-Modul entnimmt den Umgebungsvariablen AZURE\_SERVICEBUS\_NAMESPACE und AZURE\_SERVICEBUS\_ACCESS\_KEY die Informationen, die zum Herstellen einer Verbindung mit Ihrem Azure-Service Bus benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt wurden, müssen Sie die Kontoinformationen beim Aufruf von **createServiceBusService** angeben.

Ein Beispiel zum Festlegen der Umgebungsvariablen in einer Konfigurationsdatei für einen Azure-Cloud-Dienst finden Sie unter [Node.js-Cloud-Dienst mit Speicher].

Ein Beispiel zum Festlegen der Umgebungsvariablen im Verwaltungsportal für eine Azure-Website finden Sie unter [Node.js-Webanwendung mit Speicher]

## <a name="create-queue"> </a>Erstellen einer Warteschlange

Das **ServiceBusService**-Objekt ermöglicht Ihnen das Arbeiten mit Warteschlangen. Der
Der folgende Code erstellt ein **ServiceBusService**-Objekt. Fügen Sie ihn
am Anfang der Datei **server.js** hinzu, nach der Anweisung zum Importieren des Azure
-Moduls:

    var serviceBusService = azure.createServiceBusService();

Durch Aufrufen von **createQueueIfNotExists** für das **ServiceBusService**
-Objekt, wird die angegebene Warteschlange zurückgegeben (falls vorhanden,) oder eine neue
Warteschlange mit dem angegebenen Namen wird erstellt. Der folgende Code nutzt
**createQueueIfNotExists** zur Erstellung oder zum Herstellen einer Verbindung mit der Warteschlange mit der Bezeichnung
"Myqueue":

    serviceBusService.createQueueIfNotExists('myqueue', function(error){
        if(!error){
            // Queue exists
        }
    });

**createServiceBusService** unterstützt zudem weitere Optionen,
mit denen Sie die Standardeinstellungen wie z. B. Nachrichtenlebensdauer
oder maximale Warteschlangengröße überschreiben können. Das folgende Beispiel zeigt, wie Sie die
maximale Warteschlangengröße auf 5 GB bei einer Gültigkeitsdauer von 1 Minute festlegen:

    var queueOptions = {
          MaxSizeInMegabytes: '5120',
          DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
        if(!error){
            // Queue exists
        }
    });

###Filter

Mit **ServiceBusService** können Sie optionale Filteroperationen auf Operationen ausführen. Filtervorgänge können Protokollierung, automatische Wiederholung usw. umfassen. Filter sind Objekte, die eine Methode mit einer Signatur implementieren:

		function handle (requestOptions, next)

Nachdem die Vorverarbeitung der Anforderungsoptionen angeschlossen ist, muss die Methode "next" aufrufen und hierbei eine Rückruffunktion mit der folgenden Signatur übergeben:

		function (returnObject, finalCallback, next)

Nachdem das returnObject-Objekt (die Antwort auf die an den Server gesendete Anforderung) verarbeitet wurde, muss in dieser Rückruffunktion entweder "next" aufgerufen werden, wenn die Tabelle vorhanden ist, um weitere Filter zu verarbeiten, oder andernfalls einfach "finallCallback" aufrufen, um den Dienstaufruf zu beenden.

Zwei Filter, die eine Wiederholungslogik implementieren, sind im Azure SDK für Node.js enthalten: **ExponentialRetryPolicyFilter** und **LinearRetryPolicyFilter**. Der folgende Code erstellt ein **ServiceBusService**-Objekt, das den **ExponentialRetryPolicyFilter** verwendet:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="send-messages"> </a>Senden von Nachrichten an eine Warteschlange

Um eine Nachricht an eine Service Bus-Warteschlange zu senden, ruft Ihre Anwendung die
**sendQueueMessage**-Methode für das **ServiceBusService**-Objekt auf.
Nachrichten, die an Service Bus-Warteschlangen gesendet werden (und von diesem empfangen werden), sind
**BrokeredMessage**-Objekte und verfügen über eine Reihe von Standardeigenschaften (z. B.
 **Label** und **TimeToLive**), ein Wörterbuch zur Aufnahme 
benutzerdefinierter anwendungsspezifischer Eigenschaften und einen Körper mit beliebigen
Anwendungsdaten. Eine Anwendung kann den Text der festlegen, indem
sie einen Zeichenkettenwert als Nachricht weitergibt, und alle erforderlichen
Standardeigenschaften werden mit Standardwerten aufgefüllt.

Das folgende Beispiel zeigt, wie eine Testnachricht an die
Warteschlange mit dem Namen "myqueue" senden und dabei **sendQueueMessage** verwenden:

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

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB (Der Header,
der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine
maximale Größe von 64 KB haben). Es gibt keine Beschränkung für die Anzahl der Nachrichten in
einer Warteschlange aufgenommen, aber es gibt eine Obergrenze für die Gesamtgröße der Nachrichten,
in einer Warteschlange. Die Warteschlangengröße wird bei der Erstellung definiert.
Die Obergrenze beträgt 5 GB.

## <a name="receive-messages"> </a>Empfangen von Nachrichten aus einer Warteschlange

Nachrichten werden aus einer Warteschlange mit der **receiveQueueMessage**
-Methode für das **ServiceBusService**-Objekt empfangen. Standardmäßig werden Nachrichten
aus der Warteschlange gelöscht, die gelesen werden; Sie können die Nachricht jedoch lesen (peek)
und sie sperren, ohne sie aus der Warteschlange zu löschen, durch Festlegen des
optionalen Parameters **isPeekLock** auf **true**.

Das Standardverhalten für das Lesen und Löschen der Nachricht als Teil
des Empfangsvorgangs ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei
denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei
Auftreten eines Fehlers nicht verarbeitet wird. Um dies zu verstehen, betrachten Sie ein Szenario, in dem der
die Empfangsanforderung ausstellt und dann vor der Verarbeitung
abstürzt. Da die Nachricht von Service Bus als verarbeitet gekennzeichnet wurde,
wird die vor dem Absturz verarbeitete Nachricht nicht berücksichtigt, wenn die Anwendung neu gestartet und die Verarbeitung von Nachrichten
wieder aufgenommen wird.

Wenn der Parameter **isPeekLock** auf **true** festgelegt ist, ist der Empfangsprozess ein
aus zwei Phasen bestehender Vorgang, der die Unterstützung von Anwendungen ermöglicht,
die fehlende Nachrichten nicht tolerieren können. Wenn Service Bus eine Anforderung
empfängt, findet er die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern,
dass andere Consumer sie erhalten, und gibt sie dann an die Anwendung zurück.
Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat),
führt Sie die zweite Phase des
Empfangsprozesses durch Aufrufen der **deleteMessage**-Methode aus und
stellt die zu löschende Meldung als Parameter bereit. Die **deleteMessage**-Methode markiert
die Nachricht als verarbeitet, und entfernt sie aus der Warteschlange.

Das folgende Beispiel zeigt, wie Nachrichten mithilfe von
**receiveQueueMessage**.empfangen und verarbeitet werden. In diesem Beispiel wird zuerst
eine Nachricht empfangen und gelöscht und dann eine Nachricht mit **isPeekLock** gleich
true empfangen und anschließend mit **deleteMessage** gelöscht:

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

## <a name="handle-crashes"> </a>Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von
Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine
Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann,
so kann sie die **unlockMessage**-Methode für das
**ServiceBusService**-Objekt aufrufen. Dies bewirkt, dass der Service Bus die Nachricht innerhalb
der Warteschlange entsperren und verfügbar machen kann, 
damit sie entweder von derselben verarbeitenden Anwendung oder von einer anderen verarbeitenden Anwendung erneut empfangen werden kann.
der Anwendung in der Stagingumgebung befindet.

Zudem wird einer im Abonnement gesperrten Anwendung ein Zeitlimit zugeordnet.
Wenn die Anwendung die Nachricht vor Ablauf des
Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt), entsperrt Service
Bus die Nachricht automatisch und macht sie verfügbar, um erneut
empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht abstürzt,
aber bevor die **deleteMessage**-Methode aufgerufen wird, wird die Nachricht
erneut der Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig
als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet,
dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise
erneut zugestellt wird. Wenn das Szenario keine doppelte Verarbeitung tolerieren kann,
sollten Anwendungsentwickler eine zusätzliche Logik zur
Anwendung hinzufügen, um die doppelte Zustellung von Nachrichten zu verarbeiten. Dies wird häufig durch die Verwendung der
**MessageId**-Eigenschaft der Nachricht erzielt, die über
mehrere Zustellungsversuche hinweg konstant bleibt.

## <a name="next-steps"> </a>Nächste Schritte

Da Sie nun mit den Grundlagen zu Service Bus-Warteschlangen vertraut sind, finden Sie weitere Informationen unter
diesen Links.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements.][]
-   Besuchen Sie das [Azure SDK for Node]-Repository auf GitHub.

  [Azure-SDK für Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Nächste Schritte]: #next-steps
  [Was sind Servicebus-Warteschlangen?]: #what-are-service-bus-queues
  [Erstellen eines Dienstnamespace]: #create-a-service-namespace
  [Abrufen der Standard-Anmeldeinformationen für den Namespace]: #obtain-default-credentials
  [Erstellen einer Node.js-Anwendung]: #create-app
  [Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus]: #configure-app
  [Gewusst wie: Erstellen einer Warteschlange]: #create-queue
  [Gewusst wie: Senden von Nachrichten an eine Warteschlange]: #send-messages
  [Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange]: #receive-messages
  [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #handle-crashes
  [Warteschlangenkonzepte]: ../../dotNet/Media/sb-queues-08.png
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  
  
  
  
  
  [Node.js-Cloud-Dienst]: /de-de/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Warteschlangen, Themen und Abonnements.]: http://msdn.microsoft.com/de-de/library/windowsazure/hh367516.aspx
  [Website mit WebMatrix]: /de-de/develop/nodejs/tutorials/web-site-with-webmatrix/
[Vorheriges Verwaltungsportal]: ../../Shared/Media/previous-portal.png
  [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website]: /de-de/develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js-Cloud-Dienst mit Speicher]: /de-de/develop/nodejs/tutorials/web-app-with-storage/
  [Node.js-Webanwendung mit Speicher]: /de-de/develop/nodejs/tutorials/web-site-with-storage/
