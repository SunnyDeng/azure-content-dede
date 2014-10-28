<properties linkid="dev-java-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (Java) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic Java" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Java applications." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Topics/Subscriptions" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Verwenden von Servicebus-Themen und -Abonnements

In diesem Leitfaden erfahren Sie, wie Sie Service Bus-Themen und
-Abonnements verwenden. Die Beispiele wurden in Java geschrieben und verwenden das [Azure-SDK für Java][Azure-SDK für Java]. Die behandelten Szenarien umfassen das **Erstellen von Themen
und Abonnements**, **Erstellen von Abonnementfiltern**, **Senden
von Nachrichten an ein Thema**, das **Empfangen von Nachrichten von einem Abonnement** und das
**Löschen von Themen und Abonnements**.

## Inhaltsverzeichnis

-   [Was sind Service Bus-Themen und -Abonnements?][Was sind Service Bus-Themen und -Abonnements?]
-   [Erstellen eines Dienstnamespace][Erstellen eines Dienstnamespace]
-   [Abrufen der Standard-Anmeldeinformationen für den Namespace][Abrufen der Standard-Anmeldeinformationen für den Namespace]
-   [Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus][Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus]
-   [Gewusst wie: Erstellen eines Themas][Gewusst wie: Erstellen eines Themas]
-   [Gewusst wie: Erstellen von Abonnements][Gewusst wie: Erstellen von Abonnements]
-   [Gewusst wie: Senden von Nachrichten an ein Thema][Gewusst wie: Senden von Nachrichten an ein Thema]
-   [Gewusst wie: Empfangen von Nachrichten aus einem Abonnement][Gewusst wie: Empfangen von Nachrichten aus einem Abonnement]
-   [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten][Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]
-   [Gewusst wie: Löschen von Themen und Abonnements][Gewusst wie: Löschen von Themen und Abonnements]
-   [Nächste Schritte][Nächste Schritte]

[WACOM.INCLUDE [howto-service-bus-topics][howto-service-bus-topics]]

## <a name="bkmk_ConfigYourApp"> </a> Konfigurieren der Anwendung zur Verwendung eines Service Bus

Fügen Sie die folgenden import-Anweisungen am Anfang der Java-Datei ein:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Fügen Sie die Azure-Bibliotheken für Java zu Ihrem Buildpfad hinzu und binden Sie die Bibliotheken in die Bereitstellungs-Assembly Ihres Projekts ein.

## <a name="bkmk_HowToCreateTopic"> </a>Erstellen eines Themas

Sie können Verwaltungsvorgänge für Service Bus-Themen über die Klasse
**ServiceBusContract** durchführen. **ServiceBusContract**-Objekte werden
mit einer entsprechenden Konfiguration erstellt, welche die
Token-Berechtigungen für deren Verwaltung kapselt. Die Klasse **ServiceBusContract** ist der
einzige Kommunikationspunkt mit Azure.

Die **ServiceBusService**-Klasse enthält Methoden zum Erstellen, Aufzählen
und Löschen von Themen. Das folgende Beispiel zeigt, wie Sie mit einem **ServiceBusService**-Objekt
ein Thema mit dem Namen „TestTopic“ in einem Namespace mit dem Namen „HowToSample“ erstellen können:

    Configuration config = 
        ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

    ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
    try  
    {
        CreateTopicResult result = service.createTopic(topicInfo);
    }
    catch (ServiceException e) {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Mit den Methoden in**TopicInfo** können Sie Eigenschaften des Themas
einstellen (z. B. den Standardwert „time-to-live“, der auf an das
Thema gesendete Nachrichten angewendet wird). Das folgenden Beispiel zeigt, wie
ein Thema mit der Bezeichnung "TestTopic" mit einer maximalen Größe von 5 GB erstellt wird:

    long maxSizeInMegabytes = 5120;  
    TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

Sie können die **listTopics**-Methode von
**ServiceBusContract**-Objekten verwenden, um zu prüfen, ob ein Thema mit einem spezifischen Namen
bereits innerhalb eines Dienstnamespace vorhanden ist.

## <a name="bkmk_HowToCreateSubscrip"> </a> Erstellen von Abonnements

Themenabonnements werden ebenfalls mit der **ServiceBusService**
-Klasse erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen,
der die Nachrichten einschränkt, die an die virtuelle
Warteschlange des Abonnements übergeben werden.

### Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

**MatchAll** ist der Standardfilter, der verwendet wird, wenn beim Erstellen eines neuen Abonnements kein Filter
angegeben wird. Wenn der Filter **MatchAll**
 verwendet wird, werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des
Abonnements gestellt. Mit dem folgenden Beispiel wird ein
Abonnement namens "AllMessages" erstellt, für das der Standardfilter **MatchAll**
 verwendet wird.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### Erstellen von Abonnements mit Filtern

Sie können auch Filter einrichten, durch die Sie angeben können, welche an ein Thema gesendeten
Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist
**SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden
auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere
Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können,
finden Sie in der Syntax SqlFilter.SqlExpression.

Mit dem folgenden Beispiel wird ein Abonnement namens "HighMessages" mit einem
**SqlFilter**-Filter erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte
**Messagenumber**-Eigenschaft größer ist als 3:

    // Create a "HighMessages" filtered subscription  
    SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);
    RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
    ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
    CreateRuleResult ruleResult = 
        service.createRule("TestTopic", "HighMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "HighMessages", "$Default");

Im folgenden Beispiel wird in ähnlicher Weise ein Abonnement namens
„LowMessages“ mit
einem SqlFilter erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte Messagenumber-
Eigenschaft kleiner
oder gleich 3 ist:

    // Create a "LowMessages" filtered subscription
    SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);
    RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
    ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
    CreateRuleResult ruleResult = 
        service.createRule("TestTopic", "LowMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "LowMessages", "$Default");

Wenn eine Nachricht an „TestTopic“ gesendet wird, wird diese nun stets
an die Empfänger des Themenabonnements
 "AllMessages" zugestellt. Sie wird selektiv an die Empfänger der Themenabonnements
„HighMessages“ und „LowMessages“ zugestellt (je nach
Inhalt der Nachricht).

## <a name="bkmk_HowToSendMsgs"> </a> Senden von Nachrichten an ein Thema

Um eine Nachricht an ein Service Bus-Thema zu senden, benötigt Ihre Anwendung ein
**ServiceBusContract**-Objekt. Der folgende Code zeigt, wie Sie eine
Nachricht an das zuvor erstellte Thema „TestTopic“ im Dienstnamespace
„HowToSample“ schicken können:

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

Nachrichten an Service Bus-Themen sind Instanzen der
**BrokeredMessage**-Klasse. **BrokeredMessage**-Objekte enthalten eine Reihe von
Standardmethoden (wie etwa **setLabel** und **TimeToLive**), ein Wörterbuch,
das zur Aufnahme benutzerdefinierter anwendungsspezifischer Eigenschaften verwendet wird, und einen Körper
beliebiger Anwendungsdaten. Eine Anwendung kann den Körper der
Nachricht einstellen, indem ein beliebiges serialisierbares Objekt in den Konstruktor von
**BrokeredMessage** übergeben wird. Anschließend wird der passende **DataContractSerializer** zur
Serialisierung des Objekts verwendet. Alternativ kann ein
**java.io.InputStream** angegeben werden.

Das folgende Beispiel zeigt, wie Sie fünf Testnachrichten an den
"TestTopic" **MessageSender** senden, welcher über das oben angegebene Code-Teilstück erhalten wurde.
Beachten Sie, wie der **MessageNumber**-Eigenschaftswert jeder Nachricht gemäß
der Iteration der Schleife variiert (dadurch wird bestimmt, welche Abonnements die Nachricht
erhalten):

    for (int i=0; i<5; i++)  {
        // Create message, passing a string message for the body
        BrokeredMessage message = new BrokeredMessage("Test message " + i);
        // Set some additional custom app-specific property
        message.setProperty("MessageNumber", i);
        // Send message to the topic
        service.sendTopicMessage("TestTopic", message);
    }

Servicebus-Themen unterstützen eine maximale Nachrichtengröße von 256 MB (der Header,
der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine
maximale Größe von 64 MB haben). Es gibt keine Beschränkung für die Anzahl der Nachrichten,
die ein Thema enthält. Es gibt jedoch eine Obergrenze für die Gesamtgröße
der Nachrichten eines Themas. Die Themengröße wird bei der Erstellung definiert.
Die Obergrenze beträgt 5 GB.

## <a name="bkmk_HowToReceiveMsgs"> </a> Empfangen von Nachrichten aus einem Abonnement

Der einfachste Weg zum Empfangen von Nachrichten aus Abonnements sind
**ServiceBusContract**-Objekte. Empfangene Nachrichten können in zwei
verschiedenen Modi verwendet werden: **ReceiveAndDelete** und **PeekLock**.

Bei Verwendung des **ReceiveAndDelete**-Modus ist der Nachrichtenempfang ein Single-Shot-
Vorgang. Dies bedeutet, wenn Service Bus eine Leseanforderung für eine
Nachricht erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die
Anwendung zurück gesendet. **Der ReceiveAndDelete**-Modus ist das einfachste Modell.
Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine
Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein
Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann
abstürzt, bevor diese verarbeitet wird. Da Servicebus die
Nachricht als verwendet markiert hat, wird er jene
Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der
Verwendung von Nachrichten beginnt.

Im **PeekLock**-Modus ist der Empfangsvorgang zweistufig. Dadurch können Anwendungen
unterstützt werden, die das Auslassen bzw. Fehlen von
Nachrichten nicht zulassen können. Wenn Servicebus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht,
sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie
dann an die Anwendung zurück. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen
hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie
die zweite Phase des Empfangsprozesses durch Aufruf von **Delete**
 für die empfangene Nachricht durch. Wenn Service Bus den **Delete**-Aufruf erkennt,
markiert er die Nachricht als verwendet und entfernt sie aus dem Thema.

Das folgende Beispiel zeigt, wie Nachrichten mit dem nicht standardmäßig verwendeten **PeekLock**-Modus empfangen und
verarbeitet werden können. Das folgende Beispiel
verarbeitet Nachrichten aus dem "HighMessages"-Abonnement in einer Schleife und endet, wenn keine Nachrichten mehr vorhanden sind (alternativ könnte der Code auf neue Nachrichten warten).

    try
    {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

        while(true)  { 
            ReceiveSubscriptionMessageResult  resultSubMsg = 
                service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
            BrokeredMessage message = resultSubMsg.getValue();
            if (message != null && message.getMessageId() != null)
            {
                System.out.println("MessageID: " + message.getMessageId());    
                // Display the topic message.
                System.out.print("From topic: ");
                byte[] b = new byte[200];
                String s = null;
                int numRead = message.getBody().read(b);
                while (-1 != numRead)
                {
                    s = new String(b);
                    s = s.trim();
                    System.out.print(s);
                    numRead = message.getBody().read(b);
                }
                System.out.println();
                System.out.println("Custom Property: " + 
                    message.getProperty("MessageNumber"));
                // Delete message.
                System.out.println("Deleting this message.");
                service.deleteMessage(message);
            }  
            else  
            {        
                System.out.println("Finishing up - no more messages.");        
                break; 
                // Added to handle no more messages.
                // Could instead wait for more messages to be added.
            }
        }
    }
    catch (ServiceException e) {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }
    catch (Exception e) {
        System.out.print("Generic exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    } 

## <a name="bkmk_HowToHandleAppCrash"> </a> Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten

Servicebus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von
Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine
empfangene Anwendung eine Nachricht aus einem beliebigen Grund nicht verarbeiten kann,
kann sie die **unlockMessage**-Methode für die empfangene Nachricht aufrufen
(anstelle der **deleteMessage**-Methode). Dies führt dazu, dass Servicebus
die Nachricht innerhalb des Themas entsperrt und verfügbar macht, damit sie
erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere
verarbeitende Anwendung.

Zudem wird einer im Thema gesperrten Anwendung ein
Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits
nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt), entsperrt Service
Bus die Nachricht automatisch und macht sie verfügbar, um erneut
empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht
aber vor Ausgabe der **deleteMessage**-Anforderung abstürzt, wird die Nachricht wieder an die Anwendung
zugestellt, wenn diese neu gestartet wird. Dies wird häufig
als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal
verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise
erneut zugestellt wird. Wenn eine doppelte Verarbeitung in dem Szenario nicht zulässig ist,
sollten Anwendungsentwickler ihrer
Anwendung zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies wird häufig
durch die Verwendung der **getMessageId**-Methode der Nachricht erzielt, die über mehrere
Zustellungsversuche hinweg konstant bleibt.

## <a name="bkmk_HowToDeleteTopics"> </a>Löschen von Themen und Abonnements

Der einfachste Weg zum Löschen von Themen und Abonnements sind
**ServiceBusContract**-Objekte. Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert
sind. Abonnements können auch unabhängig gelöscht werden.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
    service.deleteTopic("TestTopic");

# <a name="bkmk_NextSteps"> </a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Service Bus-Warteschlangen vertraut sind, finden Sie weitere Informationen im MSDN-
Thema [Service Bus-Warteschlangen, Themen und Abonnements][Service Bus-Warteschlangen, Themen und Abonnements].

  [Azure-SDK für Java]: http://www.windowsazure.com/de-de/develop/java/
  [Was sind Service Bus-Themen und -Abonnements?]: #what-are-service-bus-topics
  [Erstellen eines Dienstnamespace]: #create-a-service-namespace
  [Abrufen der Standard-Anmeldeinformationen für den Namespace]: #obtain-default-credentials
  [Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus]: #bkmk_ConfigYourApp
  [Gewusst wie: Erstellen eines Themas]: #bkmk_HowToCreateTopic
  [Gewusst wie: Erstellen von Abonnements]: #bkmk_HowToCreateSubscrip
  [Gewusst wie: Senden von Nachrichten an ein Thema]: #bkmk_HowToSendMsgs
  [Gewusst wie: Empfangen von Nachrichten aus einem Abonnement]: #bkmk_HowToReceiveMsgs
  [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #bkmk_HowToHandleAppCrash
  [Gewusst wie: Löschen von Themen und Abonnements]: #bkmk_HowToDeleteTopics
  [Nächste Schritte]: #bkmk_NextSteps
  [howto-service-bus-topics]: ../includes/howto-service-bus-topics.md
  [Service Bus-Warteschlangen, Themen und Abonnements]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
