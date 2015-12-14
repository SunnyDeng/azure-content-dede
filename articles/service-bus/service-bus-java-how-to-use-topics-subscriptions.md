<properties
	pageTitle="Verwenden von Service Bus-Themen mit Java | Microsoft Azure"
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
	ms.date="10/07/2015"
	ms.author="sethm"/>

# Verwenden von Service Bus-Themen und -Abonnements

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In diesem Leitfaden erfahren Sie, wie Sie Service Bus-Themen und -Abonnements verwenden. Die Beispiele wurden in Java geschrieben und verwenden das [Azure-SDK für Java][]. Die behandelten Szenarien umfassen das **Erstellen von Themen und Abonnements**, das **Erstellen von Abonnementfiltern**, das **Senden von Nachrichten an ein Thema**, das **Empfangen von Nachrichten von einem Abonnement** und das **Löschen von Themen und Abonnements**.

[AZURE.INCLUDE [service-bus-java-how-to-create-topic](../../includes/service-bus-java-how-to-create-topic.md)]

## Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus

Stellen Sie sicher, dass Sie das [Azure SDK für Java][] vor dem Erstellen dieses Beispiels installiert haben. Wenn Sie Eclipse verwenden, können Sie das [Azure Toolkit für Eclipse][] installieren, das das Azure SDK für Java enthält. Sie können dann Ihrem Projekt die **Microsoft Azure-Bibliotheken für Java** hinzufügen:

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Fügen Sie die folgenden import-Anweisungen am Anfang der Java-Datei ein:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    import com.microsoft.windowsazure.core.*;
    import javax.xml.datatype.*;

Fügen Sie die Azure-Bibliotheken für Java zu Ihrem Buildpfad hinzu und binden Sie die Bibliotheken in die Bereitstellungs-Assembly Ihres Projekts ein.

## Erstellen eines Themas

Sie können Verwaltungsvorgänge für Service Bus-Themen über die Klasse **ServiceBusContract** durchführen. **ServiceBusContract**-Objekte werden mit einer entsprechenden Konfiguration erstellt, die die SAS-Token mit Berechtigungen für deren Verwaltung kapselt. Die **ServiceBusContract**-Klasse ist der einzige Kommunikationspunkt mit Azure.

Die **ServiceBusService**-Klasse enthält Methoden zum Erstellen, Aufzählen und Löschen von Themen. Das folgende Beispiel zeigt, wie Sie mit einem **ServiceBusService**-Objekt ein Thema mit dem Namen `TestTopic` in einem Namespace mit dem Namen `HowToSample` erstellen können:

    Configuration config =
    	ServiceBusConfiguration.configureWithSASAuthentication(
          "HowToSample",
          "RootManageSharedAccessKey",
          "SAS_key_value",
          ".servicebus.windows.net"
          );

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

Mit den Methoden in **TopicInfo** können Sie Eigenschaften des Themas einstellen (z. B. den Standardwert "time-to-live" (TTL), der auf an das Thema gesendete Nachrichten angewendet wird). Das folgende Beispiel zeigt, wie ein Thema mit der Bezeichnung `TestTopic` mit einer maximalen Größe von 5 GB erstellt wird:

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateTopicResult result = service.createTopic(topicInfo);

Sie können die **listTopics**-Methode von **ServiceBusContract**-Objekten verwenden, um zu prüfen, ob ein Thema mit einem spezifischen Namen bereits innerhalb eines Dienstnamespace vorhanden ist.

## Erstellen von Abonnements

Abonnements von Themen werden ebenfalls mit der **ServiceBusService**-Klasse erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übergeben werden.

### Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

**MatchAll** ist der Standardfilter, der verwendet wird, wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird. Wenn der Filter **MatchAll** verwendet wird, werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel wird ein Abonnement namens "AllMessages" erstellt, für das der Standardfilter **MatchAll** verwendet wird.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result =
        service.createSubscription("TestTopic", subInfo);

### Erstellen von Abonnements mit Filtern

Sie können auch Filter einrichten, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist [SqlFilter][], der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können, finden Sie in der Syntax [SqlFilter.SqlExpression][].

Mit dem folgenden Beispiel wird ein Abonnement namens `HighMessages` mit einem [SqlFilter][]-Objekt erstellt, das nur Nachrichten auswählt, deren benutzerdefinierte **MessageNumber**-Eigenschaft größer ist als 3:

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

Ebenso erstellt das folgende Beispiel ein Abonnement namens `LowMessages` mit einem [SqlFilter][]-Objekt, das nur Nachrichten auswählt, deren benutzerdefinierte **MessageNumber**-Eigenschaft kleiner oder gleich 3 ist:

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


Wenn jetzt eine Nachricht an `TestTopic` gesendet wird, wird diese stets an das `AllMessages`-Abonnement zugestellt. Sie wird selektiv an die Empfänger der Abonnements `HighMessages` und `LowMessages` zugestellt (je nach Inhalt der Nachricht).

## Senden von Nachrichten an ein Thema

Um eine Nachricht an ein Service Bus-Thema zu senden, benötigt Ihre Anwendung ein **ServiceBusContract**-Objekt. Der folgende Code zeigt, wie Sie eine Nachricht an das zuvor erstellte Thema `TestTopic` im Namespace `HowToSample` senden können.

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

Nachrichten an Service Bus-Themen sind Instanzen der [BrokeredMessage][]-Klasse. [BrokeredMessage][]*-Objekte enthalten eine Reihe von Standardmethoden (wie etwa **setLabel** und **TimeToLive**), ein Wörterbuch, das zur Aufnahme benutzerdefinierter anwendungsspezifischer Eigenschaften verwendet wird, und einen Körper beliebiger Anwendungsdaten. Eine Anwendung kann den Körper der Nachricht einstellen, indem ein beliebiges serialisierbares Objekt in den Konstruktor der [BrokeredMessage][] weitergeleitet wird. Anschließend wird der passende **DataContractSerializer** zur Serialisierung des Objekts verwendet. Alternativ kann ein **java.io.InputStream** angegeben werden.

Das folgende Beispiel veranschaulicht, wie fünf Testnachrichten an den **MessageSender** `TestTopic` gesendet werden, der im vorherigen Codeausschnitt abgerufen wurde. Beachten Sie, wie der **MessageNumber**-Eigenschaftswert jeder Nachricht gemäß der Iteration der Schleife variiert (dadurch wird bestimmt, welche Abonnements die Nachricht erhalten):

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 MB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 MB haben). Es gibt keine Beschränkung für die Anzahl der Nachrichten, die ein Thema enthält. Es gibt jedoch eine Obergrenze für die Gesamtgröße der Nachrichten eines Themas. Die Themengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## Empfangen von Nachrichten aus einem Abonnement

Der einfachste Weg zum Empfangen von Nachrichten aus Abonnements sind **ServiceBusContract**-Objekte. Empfangene Nachrichten können in zwei unterschiedlichen Modi funktionieren: **ReceiveAndDelete** und **PeekLock**.

Bei Verwendung des **ReceiveAndDelete**-Modus ist der Nachrichtenempfang ein Single-Shot-Vorgang. Dies bedeutet, wenn Service Bus eine Leseanforderung für eine Nachricht erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurück gesendet. **Der ReceiveAndDelete**-Modus ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Service Bus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Im **PeekLock**-Modus ist der Empfangsvorgang zweistufig. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf von **Delete** für die empfangene Nachricht durch. Wenn Service Bus den **Delete**-Aufruf erkennt, markiert er die Nachricht als verwendet und entfernt sie aus dem Thema.

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

Falls die Anwendung nach der Verarbeitung der Nachricht aber vor Ausgabe der **deleteMessage**-Anforderung abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **getMessageId**-Methode der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

## Löschen von Themen und Abonnements

Der einfachste Weg zum Löschen von Themen und Abonnements sind **ServiceBusContract**-Objekte. Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

## Nächste Schritte

Nachdem Sie nun mit den Grundlagen von Service Bus-Warteschlangen vertraut sind, finden Sie weitere Informationen unter [Service Bus-Warteschlangen, -Themen und -Abonnements][].

  [Azure SDK für Java]: http://azure.microsoft.com/develop/java/
  [Azure-SDK für Java]: http://azure.microsoft.com/develop/java/
  [Azure Toolkit für Eclipse]: https://msdn.microsoft.com/de-DE/library/azure/hh694271.aspx
  [Azure classic portal]: http://manage.windowsazure.com/
  [Service Bus-Warteschlangen, -Themen und -Abonnements]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

<!---HONumber=AcomDC_1203_2015-->