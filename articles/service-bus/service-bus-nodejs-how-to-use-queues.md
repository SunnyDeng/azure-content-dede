<properties 
	pageTitle="Verwenden von Service Bus-Warteschlangen (Node.js) | Microsoft Azure"
	description="Erfahren Sie mehr über die Verwendung von Service Bus-Warteschlangen in Azure aus einer Node.js-App."
	services="service-bus"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags 
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="07/06/2015"
	ms.author="mwasson"/>

# Verwenden von Service Bus-Warteschlangen

In diesem Leitfaden wird beschrieben, wie Sie Service Bus-Warteschlangen verwenden. Die Beispiele sind in JavaScript geschrieben und verwenden das Azure-Modul Node.js. Die Szenarien behandeln die Themen **Erstellen von Warteschlangen, Senden und Empfangen von Nachrichten** und **Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte].

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Erstellen einer Node.js-Anwendung

Erstellen Sie eine leere Node.js-Anwendung. Anleitungen zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website] oder [Node.js-Clouddienst][Node.js Cloud Service] (mithilfe von Windows PowerShell).

## Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus

Damit Azure Service Bus verwendet werden kann, laden Sie das Node.js-Azure-Paket herunter und verwenden es. Dieses Paket enthält eine Reihe von Bibliotheken, die mit den Service Bus-REST-Diensten kommunizieren.

### Verwenden von Node Package Manager (NPM) zum Beziehen des Pakets

1.  Verwenden Sie das Befehlsfenster von **Windows PowerShell für Node.js**, um zum Ordner **c:\\\node\\\sbqueues\\\WebRole1** zu navigieren, in dem Sie Ihre Beispielanwendung erstellt haben.

2.  Geben Sie **npm install azure** in das Befehlsfenster ein. Die Ausgabe sollte in etwa wie folgt aussehen:

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

3.  Sie können den Befehl **ls** manuell ausführen, um zu überprüfen, ob der Ordner **node\_modules** erstellt wurde. Suchen Sie in diesem Ordner nach dem Paket **azure**. Dieses Paket enthält die Bibliotheken, die Sie benötigen, um auf Service Bus-Warteschlangen zuzugreifen.

### Importieren des Moduls

Verwenden Sie Editor oder einen anderen Texteditor, um die folgende Zeile am Anfang der Datei **server.js** der Anwendung einzufügen:

    var azure = require('azure');

### Einrichten einer Azure Service Bus-Verbindung

Das Azure-Modul liest die Umgebungsvariablen AZURE\_STORAGE\_ACCOUNT und AZURE\_STORAGE\_ACCESS\_KEY nach Informationen aus, die für eine Verbindung zu Service Bus benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt wurden, müssen Sie beim Aufruf von **createServiceBusService** die Kontoinformationen angeben.

Ein Beispiel zum Festlegen der Umgebungsvariablen in einer Konfigurationsdatei für einen Azure-Clouddienst finden Sie unter [Node.js-Clouddienst mit Speicher].

Ein Beispiel zum Festlegen der Umgebungsvariablen im Verwaltungsportal für eine Azure-Website finden Sie unter [Node.js-Webanwendung mit Speicher].

## Erstellen von Warteschlangen

Das **ServiceBusService**-Objekt ermöglicht es Ihnen, mit Warteschlangen zu arbeiten. Der folgende Code erstellt ein **ServiceBusService**-Objekt. Fügen Sie ihn am Anfang der Datei **server.js** ein, und zwar nach der Anweisung zum Importieren des Azure-Moduls:

    var serviceBusService = azure.createServiceBusService();

Durch Aufrufen von **createQueueIfNotExists** auf dem **ServiceBusService**-Objekt wird die angegebene Warteschlange (falls vorhanden) zurückgegeben, oder es wird eine neue Warteschlange mit dem angegebenen Namen erstellt. Der folgende Code verwendet **createQueueIfNotExists**, um die Warteschlange "myqueue" zu erstellen oder eine Verbindung zu dieser Warteschlange herzustellen:

    serviceBusService.createQueueIfNotExists('myqueue', function(error){
        if(!error){
            // Queue exists
        }
    });

**createServiceBusService** unterstützt zudem weitere Optionen, mit denen Sie Standardeinstellungen für die Warteschlange überschreiben können, wie zum Beispiel die Gültigkeitsdauer von Nachrichten oder die maximale Warteschlangengröße. Das folgende Beispiel legt die maximale Warteschlangengröße auf 5 GB bei einer Gültigkeitsdauer von 1 Minute fest:

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

Mit **ServiceBusService** können Sie optionale Filteroperationen auf Operationen ausführen. Filtervorgänge können Protokollierung, automatische Wiederholung usw. umfassen. Filter sind Objekte, die eine Methode mit der Signatur implementieren:

		function handle (requestOptions, next)

Nachdem die Vorverarbeitung der Anforderungsoptionen angeschlossen ist, muss die Methode `next` aufrufen und hierbei eine Rückruffunktion mit der folgenden Signatur übergeben:

		function (returnObject, finalCallback, next)

Nachdem das **returnObject**-Objekt (die Antwort auf die an den Server gesendete Anforderung) verarbeitet wurde, muss in dieser Rückruffunktion entweder `next` aufgerufen werden, wenn die Tabelle vorhanden ist, um weitere Filter zu verarbeiten, oder es muss einfach `finalCallback` aufgerufen werden, um den Dienstaufruf zu beenden.

Zwei Filter, die eine Wiederholungslogik implementieren, sind im Azure SDK für Node.js enthalten: **ExponentialRetryPolicyFilter** und **LinearRetryPolicyFilter**. Der folgende Code erstellt ein **ServiceBusService**-Objekt, das den **ExponentialRetryPolicyFilter** verwendet:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## Senden von Nachrichten an eine Warteschlange

Um eine Nachricht an eine Service Bus-Warteschlange zu senden, ruft Ihre Anwendung die **sendQueueMessage**-Methode für das **ServiceBusService**-Objekt auf. Nachrichten, die an die Service Bus-Warteschlangen gesendet werden (und von diesen empfangen werden), sind **BrokeredMessage**-Objekte und verfügen über einen Satz von Standardeigenschaften (z. B. **Label** und **TimeToLive**), ein Wörterbuch, in dem benutzerdefinierte anwendungsspezifische Eigenschaften enthalten sind, sowie einen Bestand beliebiger Anwendungsdaten. Eine Anwendung kann den Text der Nachricht festlegen, indem eine Zeichenfolge als Nachricht übergeben wird. Alle erforderlichen Standardeigenschaften werden mit Standardwerten aufgefüllt.

Das folgende Beispiel zeigt, wie mithilfe von **sendQueueMessage** eine Testnachricht an die Warteschlange `myqueue` gesendet wird:

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

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben). Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## Empfangen von Nachrichten aus einer Warteschlange

Nachrichten werden von einer Warteschlange über die **receiveQueueMessage**-Methode auf dem **ServiceBusService**-Objekt empfangen. Nachrichten werden standardmäßig aus der Warteschlange gelöscht, wenn sie gelesen wurden. Sie können jedoch Nachrichten lesen (einen Blick darauf werfen) und sperren, ohne sie aus der Warteschlange zu löschen, indem Sie den optionalen Parameter **isPeekLock** auf **true** festlegen.

Das Standardverhalten für das Lesen und Löschen der Nachricht als Teil des Empfangsvorgangs ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Service Bus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Wenn der Parameter **isPeekLock** auf **true** festgelegt ist, wird der Empfangsvorgang zu einem zweistufigen Vorgang. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf der **deleteMessage**-Methode und Bereitstellen der Nachricht durch, die als Parameter gelöscht wird. Die **deleteMessage**-Methode markiert die Nachricht als verarbeitet, und entfernt sie aus der Warteschlange.

Im folgenden Beispiel wird veranschaulicht, wie Nachrichten mithilfe von **receiveQueueMessage** empfangen und verarbeitet werden. In diesem Beispiel wird zuerst eine Nachricht empfangen und gelöscht und anschließend eine Nachricht empfangen, wobei für **isPeekLock** der Wert **true** festgelegt ist. Anschließend wird die Nachricht mithilfe von **deleteMessage** gelöscht:

    serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
        }
    });
    serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                }
            });
        }
    });

## Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die **unlockMessage**-Methode zum **ServiceBusService**-Objekt hinzufügen. Dies führt dazu, dass Service Bus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Zudem wird einer in der Warteschlange gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel, wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor Abrufen der **deleteMessage**-Methode abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **MessageId**-Eigenschaft der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

## Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Servicebus-Warteschlangen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements.][]
-   Besuchen Sie das [Azure SDK für Node]-Repository auf GitHub.

  [Azure SDK für Node]: https://github.com/Azure/azure-sdk-for-node
  [Nächste Schritte]: #next-steps
  [What are Service Bus Queues?]: #what-are-service-bus-queues
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #create-app
  [Configure Your Application to Use Service Bus]: #configure-app
  [How to: Create a Queue]: #create-queue
  [How to: Send Messages to a Queue]: #send-messages
  [How to: Receive Messages from a Queue]: #receive-messages
  [How to: Handle Application Crashes and Unreadable Messages]: #handle-crashes
  [Queue Concepts]: ../../dotNet/Media/sb-queues-08.png
  [Azure Management Portal]: http://manage.windowsazure.com
  
  [Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Warteschlangen, Themen und Abonnements.]: http://msdn.microsoft.com/library/azure/hh367516.aspx
  [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Node.js-Clouddienst mit Speicher]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [Node.js-Webanwendung mit Speicher]: ../storage/storage-nodejs-how-to-use-table-storage.md
 

<!---HONumber=August15_HO9-->