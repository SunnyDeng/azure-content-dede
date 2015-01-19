<properties urlDisplayName="Service Bus Topics" pageTitle="Verwenden der Service Bus-Themen (Node.js) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic Node.js" description="Erfahren Sie, wie in Azure Service Bus-Themen und Abonnements verwenden. Codebeispiele wurden für Node.js-Anwendungen geschrieben." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Service Bus Topics/Subscriptions" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />






# Verwenden von Service Bus-Themen/-Abonnements

In diesem Leitfaden erfahren Sie, wie Sie Service Bus-Themen und -Abonnements aus Node.js-Anwendungen verwenden. Die behandelten Szenarien umfassen das **Erstellen von Themen und Abonnements, Erstellen von Abonnementfiltern, Senden von Nachrichten** an ein Thema, das **Empfangen von Nachrichten von einem Abonnement** und das **Löschen von Themen und Abonnements**. Weitere Informationen zu Themen und Abonnements finden Sie im Abschnitt [Nächste Schritte][].

## Inhaltsverzeichnis

-   [Was sind Service Bus-Themen und -Abonnements?][]
-   [Erstellen eines Dienstnamespaces][]
-   [Abrufen der Standard-Verwaltungsanmeldeinformationen für den Namespace][]
-   [Erstellen einer Node.js-Anwendung](#create-app)
-   [Konfigurieren der Anwendung für die Verwendung von Service Bus](#configure-app)
-   [Vorgehensweise: Erstellen eines Themas](#create-topic)
-   [Vorgehensweise: Erstellen von Abonnements](#create-subscription)
-   [Vorgehensweise: Senden von Nachrichten an ein Thema](#send-messages)
-   [Vorgehensweise: Empfangen von Nachrichten aus einem Abonnement](#receive-messages)
-   [Vorgehensweise: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten](#handle-crashes)
-   [Vorgehensweise: Löschen von Themen und Abonnements](#delete)
-   [Nächste Schritte](#next-steps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

##<a name="create-app"></a> Erstellen einer Node.js-Anwendung

Erstellen Sie eine leere Node.js-Anwendung. Anleitungen zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website], [Node.js-Cloud-Dienst][Node.js Cloud Service] (mithilfe von Windows PowerShell) oder [Website mit WebMatrix].

##<a name="configure-app"></a> Konfigurieren der Anwendung für die Verwendung von Service Bus

Damit Azure Service Bus verwendet werden kann, müssen Sie das Node.js-Azure-Paket herunterladen und verwenden. Dieses Paket enthält eine Reihe von Bibliotheken, die mit den Service Bus-REST-Diensten kommunizieren.

### Verwenden des Node-Paket-Managers (NPM) zum Abrufen des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle, z. B. **PowerShell** (Windows,) **Terminal** (Mac) oder **Bash** (Unix), und navigieren Sie zu dem Ordner, in dem Sie die Beispielanwendung erstellt haben.

2.  Geben Sie **npm install azure** in das Befehlsfenster ein. Die Ausgabe dieses Befehls sollte wie folgt aussehen:

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

3.  Sie können den Befehl **ls** manuell ausführen, um sich davon zu überzeugen, dass der Ordner **node\_modules** erstellt wurde. In diesem Ordner finden Sie ein **azure**-Paket, das die für den Zugriff auf Service Bus-Themen benötigten Bibliotheken enthält.

### Importieren des Moduls

Verwenden Sie Editor oder einen anderen Texteditor, um die folgende Zeile am Anfang der Datei **server.js** der Anwendung einzufügen:

    var azure = require('azure');

### Einrichten einer Azure Service Bus-Verbindung

Das Azure-Modul entnimmt den Umgebungsvariablen "AZURE\_SERVICEBUS\_NAMESPACE" und "AZURE\_SERVICEBUS\_ACCESS\_KEY" die Informationen, die zum Herstellen einer Verbindung mit Azure Service Bus benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt wurden, müssen Sie die Kontoinformationen beim Aufruf von **createServiceBusService** angeben.

Ein Beispiel zum Festlegen der Umgebungsvariablen in einer Konfigurationsdatei für einen Azure-Cloud-Dienst finden Sie unter [Node.js-Cloud-Dienst mit Speicher].

Ein Beispiel zum Festlegen der Umgebungsvariablen im Verwaltungsportal für eine Azure-Website finden Sie unter [Node.js-Webanwendung mit Speicher].

##<a name="create-topic"></a> Erstellen eines Themas

Das **ServiceBusService**-Objekt ermöglicht Ihnen das Arbeiten mit Themen. Der folgende Code erstellt ein **ServiceBusService**-Objekt. Fügen Sie ihn am Anfang der Datei **server.js** hinzu, nach der Anweisung zum Importieren des Azure-Moduls:

    var serviceBusService = azure.createServiceBusService();

Durch Aufrufen von **createTopicIfNotExists** für das **ServiceBusService**-Objekt
wird das angegebene Thema zurückgegeben (falls vorhanden), oder es wird ein neues Thema mit dem angegebenen Namen erstellt. Der folgende Code verwendet **createTopicIfNotExists**, um ein Thema namens "MyTopic" zu erstellen oder eine Verbindung mit diesem herzustellen:

    serviceBusService.createTopicIfNotExists('MyTopic',function(error){
        if(!error){
            // Topic was created or exists
            console.log('topic created or exists.');
        }
    });

**createServiceBusService** unterstützt zudem weitere Optionen, die
es Ihnen ermöglichen, Standardthemaeinstellungen wie z. B. den TTL-Wert der Nachricht
oder die maximale Themagröße außer Kraft zu setzen. Das folgende Beispiel zeigt, wie
die maximale Themagröße auf 5 GB und der TTL-Wert auf 1 Minute festgelegt wird:

    var topicOptions = {
            MaxSizeInMegabytes: '5120',
            DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
        if(!error){
            // topic was created or exists
        }
    });

###Filter

Mithilfe von **ServiceBusService** können Sie optionale Filtervorgänge auf Vorgänge anwenden. Filtervorgänge können Protokollierung, automatische Wiederholungsversuche usw. umfassen. Filter sind Objekte, die eine Methode mit einer Signatur implementieren:

		function handle (requestOptions, next)

Nachdem die Vorverarbeitung der Anforderungsoptionen angeschlossen ist, muss die Methode "next" aufrufen und dabei einen Rückruf mit der folgenden Signatur übergeben:

		function (returnObject, finalCallback, next)

Nachdem das returnObject-Objekt (die Antwort auf die an den Server gesendete Anforderung) verarbeitet wurde, muss der Rückruf "next" aufrufen, wenn das Objekt vorhanden ist, um weitere Filter zu verarbeiten, oder andernfalls einfach "finallCallback" aufrufen, um den Dienstaufruf zu beenden.

Zwei Filter, die eine Wiederholungsversuchlogik implementieren, sind im Azure SDK für Node.js enthalten: **ExponentialRetryPolicyFilter** und **LinearRetryPolicyFilter**. Der folgende Code erstellt ein **ServiceBusService**-Objekt, das den **ExponentialRetryPolicyFilter** verwendet:

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

##<a name="create-subscription"></a> Erstellen von Abonnements

Themenabonnements werden ebenfalls mit dem **ServiceBusService**-Objekt erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übermittelt werden.

<div class="dev-callout">
<strong>Hinweis</strong>
<p>Abonnements sind dauerhaft und so lange vorhanden, bis sie oder das mit ihnen zugeordnete Thema gelöscht werden. Wenn Ihre Anwendung Logik beinhaltet, sollte sie bei der Erstellung eines Abonnements zuerst überprüfen, ob das Abonnement bereits vorhanden ist, indem die Methode <strong>"getSubscription"</strong> verwendet wird.</p>
</div>

### Erstellen eines Abonnements mit dem Standardfilter ("MatchAll")

**MatchAll** ist der Standardfilter, der verwendet wird, wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird. Wenn der Filter **MatchAll** verwendet wird, werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel wird ein Abonnement namens "AllMessages" erstellt, für das der Standardfilter **MatchAll** verwendet wird.

    serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
        if(!error){
            // subscription created
        }
    });

### Erstellen von Abonnements mit Filtern

Sie können auch Filter einrichten, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist **SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können, finden Sie in der Syntax [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Sie können einem Abonnement mithilfe der Methode **createRule** des **ServiceBusService**-Objekts Filter hinzufügen. Mithilfe dieser Methode können Sie einem vorhandenen Abonnement neue Filter hinzufügen.

> [WACOM.NOTE]

> Da der Standardfilter automatisch auf alle neuen Abonnements angewendet wird, müssen Sie zuerst den Standardfilter entfernen, oder <strong>"MatchAll"</strong> setzt alle anderen Filter außer Kraft, die Sie ggf. angeben. Sie können die Standardregel entfernen, indem Sie die Methode <strong>"deleteRule"</strong> des <strong>ServiceBusService-Objekts</strong> verwenden.

Mit dem folgenden Beispiel wird ein Abonnement namens "HighMessages" mit einem **SqlFilter**-Filter erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte Eigenschaft **messagenumber** größer als 3 ist:

    serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
        if(!error){
            // subscription created
            rule.create();
        }
    });
    var rule={
        deleteDefault: function(){
            serviceBusService.deleteRule('MyTopic',
                'HighMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function(){
            var ruleOptions = {
                sqlExpressionFilter: 'messagenumber > 3'
            };
            rule.deleteDefault();
            serviceBusService.createRule('MyTopic', 
                'HighMessages', 
                'HighMessageFilter', 
                ruleOptions, 
                rule.handleError);
        },
        handleError: function(error){
            if(error){
                console.log(error)
            }
        }
    }

Anlog dazu erstellt das folgende Beispiel ein Abonnement namens
"LowMessages" mit einem **SqlFilter**, der nur Nachrichten auswählt,
deren Eigenschaft **Messagenumber** kleiner oder gleich 3 ist:

    serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
        if(!error){
            // subscription created
            rule.create();
        }
    });
    var rule={
        deleteDefault: function(){
            serviceBusService.deleteRule('MyTopic',
                'LowMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function(){
            var ruleOptions = {
                sqlExpressionFilter: 'messagenumber <= 3'
            };
            rule.deleteDefault();
            serviceBusService.createRule('MyTopic', 
                'LowMessages', 
                'LowMessageFilter', 
                ruleOptions, 
                rule.handleError);
        },
        handleError: function(error){
            if(error){
                console.log(error)
            }
        }
    }

Wenn eine Nachricht an "MyTopic" gesendet wird, wird diese nun stets an die Empfänger des Themenabonnements "AllMessages" übermittelt. Sie wird selektiv an die Empfänger der Themenabonnements "HighMessages" und "LowMessages" übermittelt (abhängig vom Inhalt der Nachricht).

##<a name="send-messages"></a> Senden von Nachrichten an ein Thema

Wenn Sie eine Nachricht an ein Service Bus-Thema senden möchten, muss die Anwendung die Methode **sendTopicMessage** des **ServiceBusService**-Objekts verwenden. Nachrichten, die an Service Bus-Themen gesendet werden, sind **BrokeredMessage**-Objekte. **BrokeredMessage**-Objekte weisen eine Reihe von Standardeigenschaften (etwa **Label** und **TimeToLive**), ein Wörterbuch, das zum Speichern benutzerdefinierter anwendungsspezifischer Eigenschaften verwendet wird, und Nachrichtentext mit Zeichenfolgendaten auf. Eine Anwendung kann den Nachrichtentext festlegen, indem sie einen Zeichenfolgenwert an **sendTopicMessage** übergibt. Erforderliche Standardeigenschaften werden mit den Standardwerten aufgefüllt.

Das folgende Beispiel zeigt, wie fünf Testnachrichten an "MyTopic" gesendet werden. Beachten Sie, dass der Eigenschaftenwert **messagenumber** jeder Nachricht gemäß der Iteration der Schleife variiert (auf diese Weise wird bestimmt, welche Abonnements die Nachricht erhalten):

    var message = {
        body: '',
        customProperties: {
            messagenumber: 0
        }
    }

    for (i = 0;i < 5;i++) {
        message.customProperties.messagenumber=i;
        message.body='This is Message #'+i;
        serviceBusService.sendTopicMessage(topic, message, function(error) {
          if (error) {
            console.log(error);
          }
        });
    }

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 MB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, darf maximal 64 MB groß sein). Die Anzahl der Nachrichten, die ein Thema enthält, ist nicht einegschränkt. Es gilt jedoch ein Grenzwert für die Gesamtgröße der Nachrichten in einem Thema. Die Themengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

##<a name="receive-messages"></a> Empfangen von Nachrichten aus einem Abonnement

Nachrichten werden von einem Abonnement über die Methode **receiveSubscriptionMessage** für das **ServiceBusService**-Objekt empfangen. Nachrichten werden standardmäßig aus dem Abonnement gelöscht, wenn sie gelesen wurden. Sie können jedoch Nachrichten lesen (Peek) und sperren, ohne sie aus dem Abonnement zu löschen, indem Sie den optionalen Parameter **isPeekLock** auf **true** festlegen.

Das Standardverhalten für das Lesen und Löschen der Nachricht als Teil des Empfangsvorgangs ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Stellen Sie sich zum besseren Verständnis ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wurde. Da Service Bus die Nachricht als verwendet markiert hat, wird diese Nachricht verpasst, die vor dem Absturz verwendet wurde, wenn die Anwendung neu gestartet wird und erneut mit der Verwendung von Nachrichten beginnt.

Wenn der Parameter **isPeekLock** auf **true** festgelegt ist, wird der Empfangsvorgang zu einem zweistufigen Vorgang. Auf diese Weise können Anwendungen unterstützt werden, die das Verpassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt sie die zweite Phase des Empfangsvorgangs durch Aufrufen der Methode **deleteMessage** und Bereitstellen der zu löschenden Nachricht als Parameter aus. Die Methode **deleteMessage** markiert die Nachricht als verarbeitet und entfernt sie aus dem Abonnement.

Das Beispiel unten zeigt, wie Nachrichten mithilfe von **receiveSubscriptionMessage** empfangen und verarbeitet werden können. In diesem Beispiel wird zuerst eine Nachricht des Abonnements "LowMessages" empfangen und gelöscht und anschließend eine Nachricht des Abonnements "HighMessages" erhalten, wobei **isPeekLock** auf "true" festgelegt ist. Anschließend wird die Nachricht durch **deleteMessage** gelöscht:

    serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        }
    });
    serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            console.log(lockedMessage);
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                    console.log('message has been deleted.');
                }
            }
        }
    });

##<a name="handle-crashes"></a> Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, kann sie die Methode **unlockMessage** dem **ServiceBusService**-Objekt hinzufügen. Dies führt dazu, dass Service Bus die Nachricht innerhalb des Abonnements entsperrt und zur Verfügung stellt, damit sie erneut empfangen werden kann - durch die gleiche verarbeitende Anwendung oder durch eine andere verarbeitenden Anwendung.

Zudem wird einer im Abonnement gesperrten Anwendung ein Timeout zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrentimeouts nicht verarbeiten kann (z. B., wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und stellt sie zur Verfügung, damit sie erneut empfangen werden kann.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor dem Aufruf der Methode **deleteMessage** abstürzt, wird die Nachricht erneut an die Anwendung übermittelt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (mindestens einmalige Verarbeitung) bezeichnet und bedeutet, dass jede Nachricht mindestens ein Mal verarbeitet wird, unter bestimmten Umständen jedoch ggf. auch erneut übermittelt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der Eigenschaft **MessageId** der Nachricht erreicht, die über mehrere Übermittlungsversuche hinweg konstant bleibt.

##<a name="delete"></a> Löschen von Themen und Abonnements

Themen und Abonnements sind dauerhaft und müssen explizit gelöscht werden -
über das Azure-Verwaltungsportal oder programmgesteuert.
Das Beispiel unten zeigt, wie Sie das Thema namens "MyTopic" löschen:

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig voneinander gelöscht werden. Der folgende Code zeigt, wie ein Abonnement namens "HighMessages" aus dem Thema "MyTopic" gelöscht wird:

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

##<a name="next-steps"></a> Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Service Bus-Themen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements][].
-   API-Referenz zu [SqlFilter][].
-   Besuchen Sie das [Azure SDK für Node]-Repository auf GitHub.

  [Azure SDK für Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Nächste Schritte]: #nextsteps
  [Was sind Service Bus-Themen und -Abonnements?]: #what-are-service-bus-topics
  [Erstellen eines Dienstnamespaces]: #create-a-service-namespace
  [Abrufen der Standard-Verwaltungsanmeldeinformationen für den Namespace]: #obtain-default-credentials
  [Erstellen einer Node.js-Anwendung]: #Create_a_Nodejs_Application
  [Konfigurieren der Anwendung für die Verwendung von Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [Vorgehensweise: Erstellen eines Themas]: #How_to_Create_a_Topic
  [Vorgehensweise: Erstellen von Abonnements]: #How_to_Create_Subscriptions
  [Vorgehensweise: Senden von Nachrichten an ein Thema]: #How_to_Send_Messages_to_a_Topic
  [Vorgehensweise: Empfangen von Nachrichten aus einem Abonnement]: #How_to_Receive_Messages_from_a_Subscription
  [Vorgehensweise: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Vorgehensweise: Löschen von Themen und Abonnements]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Warteschlangen, Themen und Abonnements]: http://msdn.microsoft.com/de-de/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Website mit WebMatrix]: /de-de/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js-Cloud-Dienst]: /de-de/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Erstellen und Bereitstellen einer Node.js-Anwendung für eine Azure-Website]: /de-de/develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js-Cloud-Dienst mit Speicher]: /de-de/develop/nodejs/tutorials/web-app-with-storage/
  [Node.js-Webanwendung mit Speicher]: /de-de/develop/nodejs/tutorials/web-site-with-storage/

<!--HONumber=35.2-->
