<properties 
	pageTitle="Verwenden von Service Bus-Themen (Java) - Azure" 
	description="Erfahren Sie mehr zur Verwendung von Service Bus-Themen und -Abonnements in Azure. Die Codebeispiele wurden für Java-Anwendungen geschrieben." 
	services="service-bus" 
	documentationCenter="java" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>

# Verwenden von Service Bus-Themen/-Abonnements

In diesem Leitfaden erfahren Sie, wie Sie Service Bus-Themen und -Abonnements verwenden. Die Beispiele wurden in Java geschrieben und verwenden das [Azure-SDK für Java][]. Die behandelten Szenarien umfassen das **Erstellen von Themen und Abonnements**, **Erstellen von Abonnementfiltern**, **Senden von Nachrichten an ein Thema**, **Empfangen von Nachrichten von einem Abonnement** und
**das Löschen von Themen und Abonnements**.

[AZURE.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus

Fügen Sie die folgenden import-Anweisungen am Anfang der Java-Datei ein:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Fügen Sie die Azure-Bibliotheken für Java zu Ihrem Buildpfad hinzu und binden Sie die Bibliotheken in die Bereitstellungs-Assembly Ihres Projekts ein.

## Erstellen eines Themas

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

Mit den Methoden in **TopicInfo** können Sie Eigenschaften des Themas einstellen (z. B. den Standardwert "time-to-live", der auf an das Thema gesendete Nachrichten angewendet wird). Das folgenden Beispiel zeigt, wie ein Thema mit der Bezeichnung "TestTopic" mit einer maximalen Größe von 5 GB erstellt wird:

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

Beachten Sie, dass Sie die **listTopics**-Methode bei
**ServiceBusContract**-Objekten verwenden können, um zu überprüfen, ob ein Thema mit einem bestimmten Namen bereits innerhalb eines Dienstnamespace vorhanden ist.

## Erstellen von Abonnements

Themenabonnements werden ebenfalls mit der **ServiceBusService**-Klasse erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übergeben werden.

### Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

**MatchAll** ist der Standardfilter, der verwendet wird, wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird. Bei Verwendung des Filters **MatchAll** werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel wird ein Abonnement namens "AllMessages" erstellt, für das der Standardfilter **MatchAll** verwendet wird.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### Erstellen von Abonnements mit Filtern

Sie können auch Filter einrichten, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist **SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können, finden Sie in der Syntax SqlFilter.SqlExpression.

Mit dem folgenden Beispiel wird ein Abonnement namens "HighMessages" mit einem **SqlFilter**-Filter erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte **MessageNumber**-Eigenschaft größer ist als 3:

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

Im folgenden Beispiel wird in ähnlicher Weise ein Abonnement namens "LowMessages" mit einem SqlFilter erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte Messagenumber-Eigenschaft kleiner oder gleich 3 ist:

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


Wenn eine Nachricht an "TestTopic" gesendet wird, wird diese nun stets an die Empfänger des Themenabonnements "AllMessages" zugestellt. Sie wird selektiv an die Empfänger der Themenabonnements "HighMessages" und "LowMessages" zugestellt (je nach Inhalt der Nachricht).

## Senden von Nachrichten an ein Thema

Um eine Nachricht an ein Service Bus-Thema zu senden, benötigt Ihre Anwendung ein
**ServiceBusContract**-Objekt. Der folgende Code zeigt, wie Sie eine Nachricht für das zuvor erstellte Thema "TestTopic" im
Dienstnamespace "HowToSample" senden:

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

Nachrichten an Service Bus-Themen sind Instanzen der
**BrokeredMessage**-Klasse. **BrokeredMessage**-Objekte enthalten eine Reihe von Standardmethoden (wie **setLabel** und **TimeToLive**), ein Wörterbuch, das zur Aufnahme benutzerdefinierter anwendungsspezifischer Eigenschaften verwendet wird, und einen Körper beliebiger Anwendungsdaten. Eine Anwendung kann den Textkörper der Nachricht festlegen, indem ein beliebiges serialisierbares Objekt an den Konstruktor von **BrokeredMessage** übergeben wird. Das entsprechende **DataContractSerializer**-Objekt wird dann zum Serialisieren des Objekts verwendet. Alternativ kann ein
**java.io.InputStream** bereitgestellt werden.

Im folgenden Beispiel wird dargelegt, wie fünf Testnachrichten an den **MessageSender** "TestTopic" gesendet werden, welche über das oben angegebene Code-Teilstück erhalten wurden:
Beachten Sie, wie der **MessageNumber**-Eigenschaftswert jeder Nachricht gemäß der Iteration der Schleife variiert (dadurch wird bestimmt, welche Abonnements die Nachricht erhalten):

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

Servicebus-Themen unterstützen eine maximale Nachrichtengröße von 256 MB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 MB haben). Die Anzahl der Nachrichten, die ein Thema enthält, ist nicht einegschränkt. Es gilt jedoch ein Grenzwert für die Gesamtgröße der Nachrichten in einem Thema. Die Themengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## Empfangen von Nachrichten aus einem Abonnement

Der einfachste Weg zum Empfangen von Nachrichten aus Abonnements sind **ServiceBusContract**-Objekte. Empfangene Nachrichten können in zwei verschiedenen Modi verwendet werden: **ReceiveAndDelete** und **PeekLock**.

Bei Verwendung des **ReceiveAndDelete**-Modus ist der Nachrichtenempfang ein Single-Shot-Vorgang. Dies bedeutet, wenn Service Bus eine Leseanforderung für eine Nachricht erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurück gesendet. **Der ReceiveAndDelete**-Modus ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Service Bus die Nachricht als verwendet markiert hat, wird diese Nachricht verpasst, die vor dem Absturz verwendet wurde, wenn die Anwendung neu gestartet wird und erneut mit der Verwendung von Nachrichten beginnt.

Im **PeekLock**-Modus ist der Empfangsvorgang zweistufig. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf von **Delete** für die empfangene Nachricht durch. Wenn Service Bus den **Delete**-Aufruf erkennt, markiert er die Nachricht als verwendet und entfernt sie aus dem Thema.

Das folgende Beispiel zeigt, wie Nachrichten mit dem nicht standardmäßig verwendeten Modus **PeekLock** empfangen und verarbeitet werden können. Das folgende Beispiel verarbeitet Nachrichten aus dem "HighMessages"-Abonnement in einer Schleife und endet, wenn keine Nachrichten mehr vorhanden sind (alternativ könnte der Code auf neue Nachrichten warten).

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

## Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine empfangene Anwendung eine Nachricht aus einem beliebigen Grund nicht verarbeiten kann, kann sie die **unlockMessage**-Methode für die empfangene Nachricht aufrufen (anstelle der **deleteMessage**-Methode). Dies führt dazu, dass Service Bus die Nachricht innerhalb des Themas entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Zudem wird einer im Thema gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel, wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht aber vor Ausgabe der Anforderung **deleteMessage** abstürzt, wird die Nachricht erneut an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (mindestens einmalige Verarbeitung) bezeichnet und bedeutet, dass jede Nachricht mindestens ein Mal verarbeitet wird, unter bestimmten Umständen jedoch ggf. auch erneut übermittelt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **getMessageId**-Methode der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

## Löschen von Themen und Abonnements

Der einfachste Weg zum Löschen von Themen und Abonnements sind **ServiceBusContract**-Objekte. Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

## Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Service Bus-Warteschlangen vertraut sind, finden Sie weitere Informationen im MSDN-Thema [Service Bus-Warteschlangen, -Themen und -Abonnements][].

  [Azure-SDK für Java]: http://azure.microsoft.com/develop/java/
  [Was sind Service Bus-Themen und -Abonnements?]: #what-are-service-bus-topics
  [Erstellen eines Dienstnamespaces]: #create-a-service-namespace
  [Abrufen der Standard-Anmeldeinformationen für den Namespace]: #obtain-default-credentials
  [Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus]: #bkmk_ConfigYourApp
  [Gewusst wie: Erstellen eines Themas]: #bkmk_HowToCreateTopic
  [Gewusst wie: Erstellen von Abonnements]: #bkmk_HowToCreateSubscrip
  [Gewusst wie: Senden von Nachrichten an ein Thema]: #bkmk_HowToSendMsgs
  [Gewusst wie: Empfangen von Nachrichten aus einem Abonnement]: #bkmk_HowToReceiveMsgs
  [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #bkmk_HowToHandleAppCrash
  [Gewusst wie: Löschen von Themen und Abonnements]: #bkmk_HowToDeleteTopics
  [Nächste Schritte]: #bkmk_NextSteps
  [Service Bus-Themendiagramm]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com/
  [Screenshot des Service Bus-Knotens]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [Erstellen eines neuen Namespace ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [Screenshot der Namespaceliste]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [Screenshot des Eigenschaftenbereichs]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [Screenshot des Standardschlüssels]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [Service Bus-Warteschlangen, -Themen und -Abonnements]: http://msdn.microsoft.com/library/hh367516.aspx

<!--HONumber=47-->
