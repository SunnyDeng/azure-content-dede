<properties urlDisplayName="Service Bus Topics" pageTitle="Gewusst wie: Verwenden von Service Bus-Themen (Java) - Azure" metaKeywords="Erste Schritte Azure Service Bus-Themen, Erste Schritte Service Bus-Themen, Azure Messagingfunktionen veröffentlichen/abonnieren, Messaging, Azure-Messaging-Themen und Abonnements, Service Bus-Thema Java" description="Erfahren Sie mehr zur Verwendung von Service Bus-Themen und -Abonnements in Azure. Die Codebeispiele wurden für Java-Anwendungen geschrieben." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Topics/Subscriptions" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Verwenden von Servicebus-Themen und -Abonnements

In diesem Leitfaden erfahren Sie, wie Sie Service Bus-Themen und
-Abonnements verwenden. Die Beispiele wurden in Java geschrieben und verwenden das [Azure-SDK für Java][]. Die aufgeführten Szenarien umfassen das **Erstellen von Themen
und Abonnements**, **Erstellen von Abonnementfiltern**, **Senden
von Nachrichten an ein Thema**, **das Empfangen von Nachrichten aus einem Abonnement** und
**das Löschen von Themen und Abonnements**.

## Inhaltsverzeichnis

-   [Was sind Service Bus-Themen und -Abonnements?][]
-   [Erstellen eines Dienstnamespace][]
-   [Abrufen der Standard-Anmeldeinformationen für den Namespace][]
-   [Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus][]
-   [Gewusst wie: Erstellen eines Themas][]
-   [Gewusst wie: Erstellen von Abonnements][]
-   [Gewusst wie: Senden von Nachrichten an ein Thema][]
-   [Gewusst wie: Empfangen von Nachrichten aus einem Abonnement][]
-   [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten][]
-   [Gewusst wie: Löschen von Themen und Abonnements][]
-   [Nächste Schritte][]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a name="bkmk_ConfigYourApp"> </a>Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus

Fügen Sie die folgenden import-Anweisungen am Anfang der Java-Datei ein:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Fügen Sie die Azure-Bibliotheken für Java zu Ihrem Buildpfad hinzu und binden Sie die Bibliotheken in die Bereitstellungs-Assembly Ihres Projekts ein.

## <a name="bkmk_HowToCreateTopic"> </a>Erstellen eines Themas

Sie können Verwaltungsvorgänge für Service Bus-Themen über die
**ServiceBusContract**-Klasse. Ein **ServiceBusContract**-Objekt wird
mit einer entsprechenden Konfiguration erstellt, die die
Token-Berechtigungen für die Verwaltung kapselt, und die **ServiceBusContract**-Klasse ist
der einzige Kommunikationspunkt mit Azure.

Die **ServiceBusService**-Klasse enthält Methoden zum Erstellen, Auflisten
und Löschen von Themen. Das folgende Beispiel zeigt, wie ein **ServiceBusService**-Objekt
verwendet werden kann, um ein Thema namens "TestTopic" mit einem Namespace mit dem Namen "HowToSample" zu erstellen:

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

TEs gibt Methoden auf **TopicInfo**, mit denen die Eigenschaften des Themas
optimiert werden können. (Zum Beispiel: Der Standardwert von "time-to-live" wird auf
an das Thema gesendete Nachrichten angewendet.) Das folgende Beispiel zeigt wie
Sie ein Thema mit dem Namen "TestTopic" mit einer maximalen Größe von 5GB erstellen:

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

Beachten Sie, dass Sie die **listTopics**-Methode bei
**ServiceBusContract**-Objekten verwenden können, um zu überprüfen, ob ein Thema mit einem angegebenen Namen
innerhalb eines Dienstnamespace bereits vorhanden ist.

## <a name="bkmk_HowToCreateSubscrip"> </a>Erstellen von Abonnements

Themenabonnements werden ebenfalls mit der **ServiceBusService**-
Klasse erstellt. Abonnements werden benannt und können einen optionalen Filter haben, der
den Satz an Nachrichten beschränkt, die an die virtuelle
Warteschlange des Abonnements übergeben werden.

### Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

Der **MatchAll**-Filter ist der Standardfilter, der verwendet wird, wenn kein Filter
angegeben ist, wenn ein neues Abonnement erstellt wird. Wenn der **MatchAll**-
Filter verwendet wird, werden alle für das Thema veröffentlichten Nachrichten in der
virtuellen Warteschlange des Abonnements veröffentlicht. Das folgende Beispiel erstellt ein
Abonnement namens "AllMessages" und verwendet den standardmäßigen **MatchAll**-
Filter.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### Erstellen von Abonnements mit Filtern

Sie können auch Filter einrichten, durch die Sie angeben können, welche
an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist
**SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter operieren
über die Eigenschaften der Nachrichten, die für das Thema veröffentlicht werden. Weitere
Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können,
finden Sie in der Syntax "SqlFilter.SqlExpression".

Mit dem folgenden Beispiel wird ein Abonnement namens "high-messages" mit einem
**SqlFilter** erstellt, der nur Nachrichten auswählt, die über eine benutzerdefinierte
**MessageNumber**-Eigenschaft größer als 3 verfügen:

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

Ebenso erstellt das folgende Beispiel ein Abonnement namens
"LowMessages" mit   
 einem "SqlFilter", der nur Nachrichten auswählt, die über eine "MessageNumber"
-Eigenschaft kleiner   
 als oder gleich 3 verfügen:

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


Wenn eine Nachricht nun an "TestTopic" gesendet wird, wird sie immer
an die Empfänger mit einem Abonnement für das Thema "AllMessages"
und selektiv an die Empfänger der
Themenabonnements "HighMessages" und "LowMessages" gesendet (abhängig vom
Nachrichteninhalt).

## <a name="bkmk_HowToSendMsgs"> </a>Senden von Nachrichten an ein Thema

Um eine Nachricht an ein Service Bus-Thema zu senden, benötigt Ihre Anwendung ein
**ServiceBusContract**-Objekt. Der folgende Code zeigt, wie Sie eine
Nachricht für das oben erstellte Thema "TestTopic"im
Dienstnamespace "HowToSample" senden:

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

Nachrichten an Service Bus-Themen sind Instanzen
**BrokeredMessage**-Klasse. **BrokeredMessage**-Objekte enthalten eine Reihe von
Standardmethoden (wie **setLabel** und **TimeToLive**), ein Wörterbuch,
das zur Aufnahme benutzerdefinierter anwendungsspezifischer Eigenschaften verwendet wird und einen Körper
mit beliebigen Anwendungsdaten. Eine Anwendung kann den Text der
Nachricht festlegen, indem sie ein beliebiges serialisierbares Objekt an den Konstruktor von
**BrokeredMessage** übergibt. Der passende **DataContractSerializer** wird
dann verwendet, um das Objekt zu serialisieren. Alternativ kann ein
**java.io.InputStream** bereitgestellt werden.

Das folgende Beispiel zeigt, wie Sie fünf Testnachrichten senden an
"TestTopic", **MessageSender** den Sie im vorherigen Codeabschnitt erhalten haben:
Beachten Sie, wie der Eigenschaftswert **MessageNumber** jeder Nachricht variiert, je nach
Iteration der Schleife (Dadurch werden die erhaltenden Abonnements
bestimmt):

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 MB. (Der Header,
der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine
maximale Größe von 64 KB haben). Es gibt keine Beschränkung für die Anzahl der Nachrichten in
einem Thema, aber es gibt eine Obergrenze für die Gesamtgröße der Nachrichten
in einem Thema. Diese Themagröße wird zum Zeitpunkt der Erstellung definiert, mit einer
Obergrenze von 5 GB.

## <a name="bkmk_HowToReceiveMsgs"> </a>Empfangen von Nachrichten aus einem Abonnement

Der einfachste Weg zum Empfangen von Nachrichten aus Abonnements sind
**ServiceBusContract**-Objekte. Empfangene Nachrichten können in zwei verschiedenen
Modi verwendet werden: **ReceiveAndDelete** und **PeekLock**.

Bei Verwendung des **ReceiveAndDelete**-Modus ist der Nachrichtenempfang ein Single-Shot-Vorgang.
Dies bedeutet, wenn Service Bus eine Leseanforderung für eine Nachricht
in einem Abonnement erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die 
Anwendung zurück gesendet. Der **ReceiveAndDelete**-Modus ist das einfachste Modell.
Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine
Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dies zu verstehen, sollten Sie sich ein
Szenario vorstellen, in dem der Consumer die Empfangsanforderung ausstellt und dann
abstürzt, bevor diese verarbeitet wird. Da Service Bus die Nachricht
als verarbeitet gekennzeichnet hat, hat die Anwendung, wenn sie neu gestartet wurde und beginnt,
wieder Nachrichten zu verarbeiten, die Nachricht ausgelassen, die
vor dem Absturz verarbeitet wurde.

Im **PeekLock**-Modus ist der Nachrichtenempfang zweistufig.
Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von 
Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, wird die nächste Nachricht ermittelt,
die verarbeitet werden soll, und wird gesperrt, um zu verhindern, dass sie von anderen Consumern empfangen wird.
Dann wird sie an die Anwendung zurückgegeben. Nachdem die Anwendung die
Verarbeitung der Nachricht beendet hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat),
führt Sie die zweite Phase des Empfangsprozesses durch Aufruf von **Delete**
für die empfangene Nachricht aus. Wenn Service Bus den **Delete**-Aufruf erkennt, wird
die Nachricht als verarbeitet markiert und aus dem Thema entfernt.

Das folgende Beispiel zeigt, wie Nachrichten mithilfe des
**PeekLock**-Modus (nicht des Standardmodus) empfangen und verarbeitet werden können.. Das folgende Beispiel
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

## <a name="bkmk_HowToHandleAppCrash"> </a>Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von
Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine
Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann,
so kann sie die **unlockMessage**-Methode für die empfangene Nachricht aufrufen
(statt der **deleteMessage**-Methode). Dies bewirkt, dass Service Bus
die Nachricht innerhalb des Themas entsperrt und erneut
empfangbar macht, entweder durch dieselbe verarbeitende Anwendung oder durch eine andere
verarbeitende Anwendung.

Zudem wird einer im Abonnement gesperrten Anwendung ein Zeitlimit zugeordnet.
Wenn die Anwendung die Nachricht vor
Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt), entsperrt Service Bus
die Nachricht automatisch und macht sie verfügbar, um erneut
empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht abstürzt,
aber bevor die **deleteMessage**-Anforderung ausgestellt wird, dann wird die Nachricht
erneut der Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig
als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet,
dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise
erneut zugestellt wird. Wenn das Szenario keine doppelte Verarbeitung tolerieren kann,
sollten Anwendungsentwickler eine zusätzliche Logik zur
Anwendung hinzufügen, um die doppelte Zustellung von Nachrichten zu verarbeiten. Dies wird häufig
durch die Verwendung der **getMessageId**-Methode der Nachricht erzielt, die
über mehrere Zustellungsversuche hinweg konstant bleibt.

## <a name="bkmk_HowToDeleteTopics"> </a>Löschen von Themen und Abonnements

Der einfachste Weg zum Löschen von Themen und Abonnements sind
**ServiceBusContract**-Objekte. Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die
für das Thema registriert sind. Abonnements können auch unabhängig gelöscht werden.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

# <a name="bkmk_NextSteps"> </a>Nächste Schritte

Da Sie nun mit den Grundlagen zu Service Bus-Warteschlangen vertraut sind, finden Sie weitere Informationen im MSDN
im Thema [Service Bus-Warteschlangen, -Themen und -Abonnements][].

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
  [Service Bus-Themendiagramm]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com/
  [Screenshot des Service Bus-Knotens]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [Erstellen eines neuen Namespace ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [Screenshot der Namespaceliste]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [Screenshot des Eigenschaftenbereichs]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [Screenshot des Standardschlüssels]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [Service Bus-Warteschlangen, -Themen und -Abonnements]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
