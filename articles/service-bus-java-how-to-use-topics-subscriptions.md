<properties linkid="dev-java-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (Java) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic Java" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Java applications." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Topics/Subscriptions" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />

Verwenden von Service Bus-Themen/-Abonnements
=============================================

In diesem Leitfaden erfahren Sie, wie Sie Service Bus-Themen und -Abonnements verwenden. Die Beispiele wurden in Java geschrieben und verwenden das [Azure-SDK für Java](http://www.windowsazure.com/de-de/develop/java/). Die behandelten Szenarien umfassen das **Erstellen von Themen und Abonnements**, Erstellen von **Abonnementfiltern**, **Senden von Nachrichten an ein Thema**, das **Empfangen von Nachrichten von einem Abonnement** und das **Löschen von Themen und Abonnements**.

Inhaltsverzeichnis
------------------

-   [Was sind Service Bus-Themen und -Abonnements?](#what-are-service-bus-topics)
-   [Erstellen eines Dienstnamespaces](#create-a-service-namespace)
-   [Abrufen der Standard-Anmeldeinformationen für den Namespace](#obtain-default-credentials)
-   [Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus](#bkmk_ConfigYourApp)
-   [Gewusst wie: Erstellen eines Themas](#bkmk_HowToCreateTopic)
-   [Gewusst wie: Erstellen von Abonnements](#bkmk_HowToCreateSubscrip)
-   [Gewusst wie: Senden von Nachrichten an ein Thema](#bkmk_HowToSendMsgs)
-   [Gewusst wie: Empfangen von Nachrichten aus einem Abonnement](#bkmk_HowToReceiveMsgs)
-   [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten](#bkmk_HowToHandleAppCrash)
-   [Gewusst wie: Löschen von Themen und Abonnements](#bkmk_HowToDeleteTopics)
-   [Nächste Schritte](#bkmk_NextSteps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus
----------------------------------------------------------------

Fügen Sie die folgenden import-Anweisungen am Anfang der Java-Datei ein:

    // die folgenden Imports werden für die Service Bus-API benötigt
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Fügen Sie die Azure-Bibliotheken für Java zu Ihrem Buildpfad hinzu und binden Sie die Bibliotheken in die Bereitstellungs-Assembly Ihres Projekts ein.

Erstellen eines Themas
----------------------

Sie können Verwaltungsvorgänge für Service Bus-Themen über die Klasse **ServiceBusContract** durchführen. **ServiceBusContract**-Objekte werden mit einer entsprechenden Konfiguration erstellt, welche die Token-Berechtigungen für deren Verwaltung kapselt. Die Klasse **ServiceBusContract** ist der einzige Kommunikationspunkt mit Azure.

Die **ServiceBusService**-Klasse enthält Methoden zum Erstellen, Aufzählen und Löschen von Themen. Das folgende Beispiel zeigt, wie Sie mit einem **ServiceBusService**-Objekt ein Thema mit dem Namen "TestTopic" in einem Namespace mit dem Namen "HowToSample" erstellen können:

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

Mit den Methoden in **TopicInfo** können Sie Eigenschaften des Themas einstellen (z. B. den Standardwert "time-to-live", der auf an das Thema gesendete Nachrichten angewendet wird). Das folgenden Beispiel zeigt, wie ein Thema mit der Bezeichnung "TestTopic" mit einer maximalen Größe von 5 GB erstellt wird:

    long maxSizeInMegabytes = 5120;  
    TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

Sie können die **listTopics**-Methode von **ServiceBusContract**-Objekten verwenden, um zu prüfen, ob ein Thema mit einem spezifischen Namen bereits innerhalb eines Dienstnamespace vorhanden ist.

Erstellen von Abonnements
-------------------------

Themenabonnements werden ebenfalls mit der **ServiceBusService**-Klasse erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übergeben werden.

### Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

**MatchAll** ist der Standardfilter, der verwendet wird, wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird. Wenn der Filter **MatchAll** verwendet wird, werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel wird ein Abonnement namens "AllMessages" erstellt, für das der Standardfilter **MatchAll** verwendet wird.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### Erstellen von Abonnements mit Filtern

Sie können auch Filter einrichten, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist **SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können, finden Sie in der Syntax SqlFilter.SqlExpression.

Mit dem folgenden Beispiel wird ein Abonnement namens "HighMessages" mit einem **SqlFilter**-Filter erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte **Messagenumber**-Eigenschaft größer ist als 3:

    // Erstellen eines gefilterten Abonnements "HighMessages" 
    SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);
    RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
    ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
    CreateRuleResult ruleResult = 
        service.createRule("TestTopic", "HighMessages", ruleInfo);
    // Standardregel löschen, ansonsten wird die neue Regel nicht aufgerufen.
    service.deleteRule("TestTopic", "HighMessages", "$Default");

Ebenso erstellt das folgende Beispiel ein Abonnement namens "LowMessages" mit
einem SqlFilter, der nur Nachrichten auswählt, deren benutzerdefinierte
Messagenumber-Eigenschaft kleiner oder gleich 3 ist:

    // Erstellen eines gefilterten Abonnements "HighMessages" 
    SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);
    RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
    ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
    CreateRuleResult ruleResult = 
        service.createRule("TestTopic", "LowMessages", ruleInfo);
    // Standardregel löschen, ansonsten wird die neue Regel nicht aufgerufen.
    service.deleteRule("TestTopic", "LowMessages", "$Default");

Wenn eine Nachricht an "TestTopic" gesendet wird, wird diese nun stets an die Empfänger des Themenabonnements "AllMessages" zugestellt. Sie wird selektiv an die Empfänger der Themenabonnements "HighMessages" und "LowMessages" zugestellt (je nach Inhalt der Nachricht).

Senden von Nachrichten an ein Thema
-----------------------------------

Um eine Nachricht an ein Service Bus-Thema zu senden, benötigt Ihre Anwendung ein **ServiceBusContract**-Objekt. Der folgende Code zeigt, wie Sie eine Nachricht an das zuvor erstellte Thema "TestTopic" im Dienstnamespace "HowToSample" schicken können:

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

Nachrichten an Service Bus-Themen sind Instanzen der **BrokeredMessage**-Klasse. **BrokeredMessage**-Objekte enthalten eine Reihe von Standardmethoden (wie etwa **setLabel** und **TimeToLive**), ein Wörterbuch, das zur Aufnahme benutzerdefinierter anwendungsspezifischer Eigenschaften verwendet wird, und einen Körper beliebiger Anwendungsdaten. Eine Anwendung kann den Körper der Nachricht einstellen, indem ein beliebiges serialisierbares Objekt in den Konstruktor der **BrokeredMessage** weitergeleitet wird. Anschließend wird der passende **DataContractSerializer** zur Serialisierung des Objekts verwendet. Alternativ kann ein **java.io.InputStream** angegeben werden.

Im folgenden Beispiel wird dargelegt, wie fünf Testnachrichten an den **MessageSender** "TestTopic" gesendet werden, welche über das oben angegebene Code-Teilstück erhalten wurden: Beachten Sie, wie der **MessageNumber**-Eigenschaftswert jeder Nachricht gemäß der Iteration der Schleife variiert (dadurch wird bestimmt, welche Abonnements die Nachricht erhalten):

    for (int i=0; i<5; i++)  {
        // Nachricht erstellen und Stringnachricht für den Text übergeben
        BrokeredMessage message = new BrokeredMessage("Test message " + i);
        // zusätzliche, appspezifische Eigenschaft setzen
        message.setProperty("MessageNumber", i);
        // Nachricht für das Thema setzen
        service.sendTopicMessage("TestTopic", message);
    }

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 MB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 MB haben). Es gibt keine Beschränkung für die Anzahl der Nachrichten, die ein Thema enthält. Es gibt jedoch eine Obergrenze für die Gesamtgröße der Nachrichten eines Themas. Die Themengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

Empfangen von Nachrichten aus einem Abonnement
----------------------------------------------

Der einfachste Weg zum Empfangen von Nachrichten aus Abonnements sind **ServiceBusContract**-Objekte. Empfangene Nachrichten können in zwei verschiedenen Modi verwendet werden: **ReceiveAndDelete** und **PeekLock**.

Bei Verwendung des **ReceiveAndDelete**-Modus ist der Nachrichtenempfang ein Single-Shot-Vorgang. Dies bedeutet, wenn Service Bus eine Leseanforderung für eine Nachricht erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurück gesendet. **Der ReceiveAndDelete**-Modus ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Service Bus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Im **PeekLock**-Modus ist der Empfangsvorgang zweistufig. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf von **Delete** für die empfangene Nachricht durch. Wenn Service Bus den **Delete**-Aufruf erkennt, markiert er die Nachricht als verwendet und entfernt sie aus dem Thema.

Das folgende Beispiel zeigt, wie Nachrichten mit dem nicht standardmäßig verwendeten **PeekLock**-Modus empfangen und verarbeitet werden können. Das folgende Beispiel verarbeitet Nachrichten aus dem "HighMessages"-Abonnement in einer Schleife und endet, wenn keine Nachrichten mehr vorhanden sind (alternativ könnte der Code auf neue Nachrichten warten).

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
                            // Nachricht des Themas anzeigen.
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
                            // Nachricht löschen.
                System.out.println("Deleting this message.");
                            service.deleteMessage(message);
                        }  
                        else  
                        {        
                            System.out.println("Finishing up - no more messages.");        
                            break; 
                            // Keine weiteren Nachrichten werden verarbeitet.
                // Alternativ könnte auf neue Nachrichten gewartet werden.
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

Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten
-------------------------------------------------------------

Service Bus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine empfangene Anwendung eine Nachricht aus einem beliebigen Grund nicht verarbeiten kann, kann sie die **unlockMessage**-Methode für die empfangene Nachricht aufrufen (anstelle der **deleteMessage**-Methode). Dies führt dazu, dass Service Bus die Nachricht innerhalb des Themas entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Zudem wird einer im Thema gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel, wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht aber vor Ausgabe der **deleteMessage**-Anforderung abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung in dem Szenario nicht zulässig ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **getMessageId**-Methode der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

Löschen von Themen und Abonnements
----------------------------------

Der einfachste Weg zum Löschen von Themen und Abonnements sind **ServiceBusContract**-Objekte. Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden.

    // Abonnements löschen
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Ein Thema löschen
    service.deleteTopic("TestTopic");

Nächste Schritte
================

Nachdem Sie nun mit den Grundlagen der Service Bus-Warteschlangen vertraut sind, finden Sie weitere Informationen im MSDN-Thema [Service Bus-Warteschlangen, Themen und Abonnements](http://msdn.microsoft.com/library/windowsazure/hh367516.aspx).

