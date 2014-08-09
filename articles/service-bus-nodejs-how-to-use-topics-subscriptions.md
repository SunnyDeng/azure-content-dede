<properties linkid="dev-nodejs-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (Node.js) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic Node.js" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Node.js applications." metaCanonical="" services="service-bus" documentationCenter="Node.js" title="How to Use Service Bus Topics/Subscriptions" authors="" solutions="" manager="" editor="" />

Verwenden von Service Bus-Themen/-Abonnements
=============================================

In diesem Leitfaden erfahren Sie, wie Sie Service Bus-Themen und -Abonnements von Node.js-Anwendungen verwenden. Die behandelten Szenarien umfassen das **Erstellen von Themen und Abonnements, Erstellen von Abonnementfiltern, Senden von Nachrichten** an ein Thema, das **Empfangen von Nachrichten von einem Abonnement** und das **Löschen von Themen und Abonnements**. Weitere Informationen zu Themen und Abonnements finden Sie im Abschnitt [Nächste Schritte](#nextsteps).

Inhaltsverzeichnis
------------------

-   [Was sind Service Bus-Themen und -Abonnements?](#what-are-service-bus-topics)
-   [Erstellen eines Dienstnamespace](#create-a-service-namespace)
-   [Abrufen der standardmäßigen Verwaltungsanmeldeinformationen für den Namespace](#obtain-default-credentials)
-   [Erstellen einer Node.js-Anwendung](#create-app)
-   [Konfigurieren der Anwendung zur Verwendung eines Service Bus](#configure-app)
-   [Gewusst wie: Erstellen eines Themas](#create-topic)
-   [Gewusst wie: Erstellen von Abonnements](#create-subscription)
-   [Gewusst wie: Senden von Nachrichten an ein Thema](#send-messages)
-   [Gewusst wie: Empfangen von Nachrichten aus einem Abonnement](#receive-messages)
-   [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten](#handle-crashes)
-   [Gewusst wie: Löschen von Themen und Abonnements](#delete)
-   [Nächste Schritte](#next-steps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

Erstellen einer Node.js-Anwendung
---------------------------------

Erstellen Sie eine leere Node.js-Anwendung. Hinweise zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website](/de-de/develop/nodejs/tutorials/create-a-website-(mac)/), [Node.js-Clouddienst](/de-de/documentation/articles/cloud-services-nodejs-develop-deploy-app/) (mithilfe von Windows PowerShell) oder [Website mit WebMatrix](/de-de/develop/nodejs/tutorials/web-site-with-webmatrix/).

Konfigurieren der Anwendung zur Verwendung eines Service Bus
------------------------------------------------------------

Um Azure Service Bus zu verwenden, müssen Sie das Node.js-Azure-Paket herunterladen und verwenden. Dieses Paket enthält eine Reihe von Bibliotheken, die mit den Service Bus-REST-Diensten kommunizieren.

### Verwenden von Node-Paket-Manager (NPM) zum Beziehen des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle, z. B. **PowerShell** (Windows,) **Terminal** (Mac) oder **Bash** (Unix), und navigieren Sie zu dem Ordner, in dem Sie die Beispielanwendung erstellt haben.

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
        ├── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Sie können den Befehl **ls** manuell aufrufen, um sich davon zu überzeugen, dass der Ordner **node\_modules** erstellt wurde. In diesem Ordner finden Sie ein **azure**-Paket, das die für den Zugriff auf Service Bus-Themen benötigten Bibliotheken enthält.

### Importieren des Moduls

Verwenden Sie Editor oder einen anderen Texteditor, um die folgende Zeile am Anfang der Datei **server.js** der Anwendung einzufügen:

    var azure = require('azure');

### Einrichten einer Azure-Service Bus-Verbindung

Das Azure-Modul entnimmt den Umgebungsvariablen AZURE\_SERVICEBUS\_NAMESPACE und AZURE\_SERVICEBUS\_ACCESS\_KEY die Informationen, die zum Herstellen einer Verbindung mit Ihrem Azure-Service Bus benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt wurden, müssen Sie die Kontoinformationen beim Aufruf von **createServiceBusService** angeben.

Ein Beispiel zum Festlegen der Umgebungsvariablen in einer Konfigurationsdatei für einen Azure-Cloud-Dienst finden Sie unter [Node.js-Clouddienst mit Speicher](/de-de/develop/nodejs/tutorials/web-app-with-storage/).

Ein Beispiel zum Festlegen der Umgebungsvariablen im Verwaltungsportal für eine Azure-Website finden Sie unter [Node.js-Webanwendung mit Speicher](/de-de/develop/nodejs/tutorials/web-site-with-storage/).

Erstellen eines Themas
----------------------

Das Objekt **ServiceBusService** ermöglicht Ihnen das Arbeiten mit Themen. Mit dem folgenden Code wird ein **ServiceBusService**-Objekt erstellt. Fügen Sie ihn am Anfang der Datei **server.js** hinzu, nach der Anweisung zum Importieren des Azure-Moduls:

    var serviceBusService = azure.createServiceBusService();

Durch das Aufrufen von **createTopicIfNotExists** auf dem **ServiceBusService**-Objekt wird das angegebene Thema zurückgegeben (falls vorhanden), oder es wird ein neues Thema mit dem angegebenen Namen erstellt. Folgende Codes verwenden **createTopicIfNotExists**, um ein Thema namens 'MyTopic' zu erstellen oder eine Verbindung dazu herzustellen:

    serviceBusService.createTopicIfNotExists('MyTopic',function(error){
        if(!error){
            // Thema wurde erstellt oder ist vorhanden
            console.log('topic created or exists.');
        }
    });

**createServiceBusService** unterstützt zudem weitere Optionen, mit denen Sie die Standardthemeneinstellungen überschreiben können, wie zum Beispiel die Nachrichtenlebensdauer oder maximale Themengröße. Das folgende Beispiel zeigt, wie Sie die maximale Themengröße auf 5 GB bei einer Lebensdauer von 1 Minute festlegen\_

    var topicOptions = {
            MaxSizeInMegabytes: '5120',
            DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
        if(!error){
            // Thema wurde erstellt oder ist vorhanden
        }
    });

### Filter

Mit **ServiceBusService** können Sie optionale Filteroperationen auf Operationen ausführen. Filtervorgänge können Protokollierung, automatische Wiederholung usw. umfassen. Filter sind Objekte, die eine Methode mit einer Signatur implementieren:

     function handle (requestOptions, next)

Nachdem die Vorverarbeitung der Anforderungsoptionen abgeschlossen ist, muss die Methode "next" aufrufen und hierbei eine Rückruffunktion mit der folgenden Signatur übergeben:

     function (returnObject, finalCallback, next)

Nachdem das returnObject-Objekt (die Antwort auf die an den Server gesendete Anforderung) verarbeitet wurde, muss in dieser Rückruffunktion entweder "next" aufgerufen werden, wenn die Tabelle vorhanden ist, um weitere Filter zu verarbeiten, oder andernfalls einfach "finallCallback" aufrufen, um den Dienstaufruf zu beenden.

Zwei Filter, die eine Wiederholungslogik implementieren, sind im Azure SDK für Node.js enthalten: **ExponentialRetryPolicyFilter** und **LinearRetryPolicyFilter**. Der folgende Code erstellt ein **ServiceBusService**-Objekt, das den **ExponentialRetryPolicyFilter** verwendet:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

Erstellen von Abonnements
-------------------------

Themenabonnements werden ebenfalls mit dem **ServiceBusService**-Objekt erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übergeben werden.

**Hinweis**

Abonnements sind dauerhaft und existieren solange, bis sie oder das mit ihnen verbundene Thema gelöscht werden. Wenn Ihre Anwendung Logik beinhaltet, sollte sie bei der Erstellung eines Abonnements zuerst mithilfe der **getSubscription**-Methode überprüfen, ob das Abonnement bereits vorhanden ist.

### Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

**MatchAll** ist der Standardfilter, der verwendet wird, wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird. Wenn der Filter **MatchAll** verwendet wird, werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel wird ein Abonnement namens "AllMessages" erstellt, für das der Standardfilter **MatchAll** verwendet wird.

    serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
        if(!error){
            // Abonnement erstellt
        }
    });

### Erstellen von Abonnements mit Filtern

Sie können auch Filter einrichten, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist **SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können, finden Sie in der Syntax [SqlFilter.SqlExpression](http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx).

Sie können mithilfe der **createRule**-Methode des **ServiceBusService**-Objekts Filter zu einem Abonnement hinzufügen. Durch diese Methode können Sie neue Filter zu einem vorhandenen Abonnement hinzufügen.

> [WACOM.NOTE]

> Da der Standardfilter automatisch auf alle neuen Abonnements angewendet wird, müssen Sie zuerst den Standardfilter entfernen. Ansonsten wird **MatchAll** alle Filter überschreiben, die Sie angeben. Sie können die Standardregel mithilfe der **deleteRule**-Methode des **ServiceBusService**-Objekts entfernen.

Mit dem folgenden Beispiel wird ein Abonnement namens "HighMessages" mit einem **SqlFilter**-Filter erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte **Messagenumber**-Eigenschaft größer ist als 3:

    serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
        if(!error){
            // Abonnement erstellt
            rule.create();
        }
    });
    var rule={
        deleteDefault: function () {
            serviceBusService.deleteRule('MyTopic',
                'HighMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function () {
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
        handleError: function (error) {
            if(error){
                console.log(error)
            }
        }
    }

Ebenso erstellt das folgende Beispiel ein Abonnement namens "LowMessages" mit einem **SqlFilter**-Filter, der nur Nachrichten auswählt, deren benutzerdefinierte **Messagenumber**-Eigenschaft kleiner oder gleich 3 ist:

    serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
        if(!error){
            // Abonnement erstellt
            rule.create();
        }
    });
    var rule={
        deleteDefault: function () {
            serviceBusService.deleteRule('MyTopic',
                'LowMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function () {
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
        handleError: function (error) {
            if(error){
                console.log(error)
            }
        }
    }

Wenn eine Nachricht an "MyTopic" gesendet wird, wird diese nun stets an die Empfänger des Themenabonnements "AllMessages" zugestellt. Sie wird selektiv an die Empfänger der Themenabonnements "HighMessages" und "LowMessages" zugestellt (je nach Inhalt der Nachricht).

Senden von Nachrichten an ein Thema
-----------------------------------

Um eine Nachricht an ein Service Bus-Thema zu senden, muss die Anwendung die **sendTopicMessage**-Methode des **ServiceBusService**-Objekts verwenden. Nachrichten, die an Service Bus-Themen gesendet werden, sind **BrokeredMessage**-Objekte. **BrokeredMessage**-Objekte weisen eine Reihe von Standardeigenschaften (wie etwa **Label** und **TimeToLive**), ein Wörterbuch, das zur Aufnahme benutzerdefinierter anwendungsspezifischer Eigenschaften verwendet wird, und einen Körper mit Zeichenkettendaten auf. Eine Anwendung kann den Nachrichtenkörper festlegen, indem sie einen Zeichenkettenwert an **sendTopicMessage** weitergibt. Erforderliche Standardeigenschaften werden mit den Standardwerten gefüllt.

Das folgende Beispiel zeigt, wie Sie fünf Testnachrichten an "MyTopic" senden. Beachten Sie, dass der **Messagenumber**-Eigenschaftswert jeder Nachricht gemäß der Iteration der Schleife variiert (dadurch wird bestimmt, welche Abonnements die Nachricht erhalten):

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

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 MB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 MB haben). Es gibt keine Beschränkung für die Anzahl der Nachrichten, die ein Thema enthält. Es gibt jedoch eine Obergrenze für die Gesamtgröße der Nachrichten eines Themas. Die Themengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

Empfangen von Nachrichten aus einem Abonnement
----------------------------------------------

Nachrichten werden von einem Abonnement über die **receiveSubscriptionMessage**-Methode auf dem **ServiceBusService**-Objekt empfangen. Nachrichten werden standardmäßig aus dem Abonnement gelöscht, wenn sie gelesen wurden. Sie können jedoch Nachrichten lesen (einen Blick darauf werfen) und sperren, ohne sie aus dem Abonnement zu löschen, indem Sie den optionalen Parameter **isPeekLock** auf **true** festlegen.

Das Standardverhalten für das Lesen und Löschen der Nachricht als Teil des Empfangsvorgangs ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Service Bus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Wenn der Parameter **isPeekLock** auf **true** festgelegt ist, wird der Empfangsvorgang zu einem zweistufigen Vorgang. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf der **deleteMessage**-Methode und Bereitstellen der Nachricht durch, die als Parameter gelöscht wird. Die **deleteMessage**-Methode markiert die Nachricht als verarbeitet, und entfernt sie aus dem Abonnement.

Das Beispiel unten zeigt, wie Nachrichten mithilfe von **receiveSubscriptionMessage** empfangen und verarbeitet werden können. In diesem Beispiel wird zuerst eine Nachricht des Abonnements "LowMessages" empfangen und gelöscht, und anschließend eine Nachricht des Abonnements "HighMessages" erhalten, wobei für **isPeekLock** "true" festgelegt ist. Anschließend wird die Nachricht durch **deleteMessage** gelöscht:

    serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
        if(!error){
            // Nachricht erhalten und gelöscht
            console.log(receivedMessage);
        }
    });
    serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Nachricht erhalten und gesperrt
            console.log(lockedMessage);
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Nachricht wurde gelöscht
                    console.log('message has been deleted.');
                }
            }
        }
    });

Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten
-------------------------------------------------------------

Service Bus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die **unlockMessage**-Methode zum **ServiceBusService**-Objekt hinzufügen. Dies führt dazu, dass Service Bus die Nachricht innerhalb des Abonnements entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitenden Anwendung.

Zudem wird einer im Abonnement gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor Abrufen der **deleteMessage**-Methode abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung in dem Szenario nicht zulässig ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **MessageId**-Eigenschaft der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

Löschen von Themen und Abonnements
----------------------------------

Themen und Abonnements sind dauerhaft und müssen explizit über das Azure-Verwaltungsportal oder programmgesteuert gelöscht werden. Das Beispiel unten zeigt, wie Sie das Thema namens "MyTopic" löschen:

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden. Der folgende Code zeigt, wie ein Abonnement namens "HighMessages" aus dem Thema "MyTopic" gelöscht wird:

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

Nächste Schritte
----------------

Nachdem Sie nun mit den Grundlagen der Service Bus-Themen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements](http://msdn.microsoft.com/de-de/library/hh367516.aspx).
-   API-Referenz für [SqlFilter](http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx).
-   Besuchen Sie das [Azure SDK für Node](https://github.com/WindowsAzure/azure-sdk-for-node)-Repository auf GitHub.

  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Azure Management Portal]: http://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Queues, Topics, and Subscriptions]: http://msdn.microsoft.com/de-de/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Web Site with WebMatrix]: /de-de/develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: /de-de/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Create and deploy a Node.js application to an Azure Web Site]: /de-de/develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /de-de/develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /de-de/develop/nodejs/tutorials/web-site-with-storage/
