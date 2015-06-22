<properties 
	pageTitle="Verwenden von Service Bus-Warteschlangen (Java) - Azure" 
	description="Erfahren Sie mehr über die Verwendung von Service Bus-Warteschlangen in Azure. Die Codebeispiele wurden in Java geschrieben." 
	services="service-bus" 
	documentationCenter="java" 
	authors="sethmanheim" 
	manager="timlt" 
	/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>

# Verwenden von Service Bus-Warteschlangen

In diesem Leitfaden wird beschrieben, wie Sie Service Bus-Warteschlangen verwenden. Die Beispiele wurden in Java geschrieben und verwenden das [Azure-SDK für Java][]. Die dargestellten Szenarien beinhalten das **Erstellen von Warteschlangen**, das **Senden und Empfangen von Nachrichten** sowie das **Löschen von Warteschlangen**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus

Fügen Sie die folgenden import-Anweisungen am Anfang der Java-Datei ein:

	// Include the following imports to use service bus APIs
	import com.microsoft.windowsazure.services.serviceBus.*;
	import com.microsoft.windowsazure.services.serviceBus.models.*; 
	import com.microsoft.windowsazure.services.core.*; 
	import javax.xml.datatype.*;
	
## Erstellen von Warteschlangen

Sie können Verwaltungsvorgänge für Service Bus-Warteschlangen über die Klasse **ServiceBusContract** durchführen. **ServiceBusContract**-Objekte werden mit einer entsprechenden Konfiguration erstellt, welche die Token-Berechtigungen für deren Verwaltung kapselt. Die Klasse **ServiceBusContract** ist der einzige Kommunikationspunkt mit Azure.

Die **ServiceBusService**-Klasse enthält Methoden zum Erstellen, Aufzählen und Löschen von Warteschlangen. Das folgende Beispiel zeigt, wie Sie mit einem **ServiceBusService**-Objekt eine Tabelle mit dem Namen "TestQueue" in einem Namespace mit dem Namen "HowToSample" erstellen können:

    Configuration config = 
    	ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

    ServiceBusContract service = ServiceBusService.create(config);
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    try
    {     
		CreateQueueResult result = service.createQueue(queueInfo);
    }
	catch (ServiceException e)
	{
	    System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Mit den Methoden in QueueInfo können Sie Eigenschaften der Warteschlange einstellen (z. B. den Standardwert "time-to-live", der auf an die Warteschlange gesendete Nachrichten angewendet wird). Das folgenden Beispiel zeigt, wie eine Warteschlange mit der Bezeichnung "TestQueue" mit einer maximalen Größe von 5 GB erstellt wird:

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateQueueResult result = service.createQueue(queueInfo);

Beachten Sie: Sie können die **listQueues**-Methode in **ServiceBusContract**-Objekten verwenden, um zu prüfen, ob eine Warteschlange mit einem bestimmten Namen bereits innerhalb eines Dienstnamespace vorhanden ist.

## Senden von Nachrichten an eine Warteschlange

Um eine Nachricht an eine Service Bus-Warteschlange zu senden, benötigt Ihre Anwendung ein **ServiceBusContract**-Objekt. Der folgende Code zeigt, wie Sie eine Nachricht an die zuvor erstellte Warteschlange "TestQueue" im
Dienstnamespace "HowToSample" senden:

    try
    {
        BrokeredMessage message = new BrokeredMessage("MyMessage");
        service.sendQueueMessage("TestQueue", message);
    }
    catch (ServiceException e) 
    {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Die Nachrichten, die an die Service Bus-Warteschlangen gesendet (und von diesen empfangen) werden, sind Instanzen der Klasse **BrokeredMessage**. **BrokeredMessage**-Objekte enthalten eine Reihe von Standardmethoden (wie etwa **getLabel** und **getTimeToLive**, **setLabel** und **setTimeToLive**), ein Wörterbuch, für die Aufnahme benutzerdefinierter anwendungsspezifischer Eigenschaften und einen Körper beliebiger Anwendungsdaten. Eine Anwendung kann den Körper der Nachricht einstellen, indem ein beliebiges serialisierbares Objekt in den Konstruktor von **BrokeredMessage** übergeben wird. Anschließend wird der passende Serialisierer zur Serialisierung des Objekts verwendet. Alternativ kann ein **java.IO.InputStream** angegeben werden.

Im folgenden Beispiel wird dargelegt, wie fünf Testnachrichten an den **MessageSender** "TestQueue" gesendet werden, welche über das oben angegebene Code-Teilstück erhalten wurden:

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i); 
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben). Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## Empfangen von Nachrichten aus einer Warteschlange

Der einfachste Weg zum Empfangen von Nachrichten aus Warteschlangen sind **ServiceBusContract**-Objekte. Empfangene Nachrichten können in zwei verschiedenen Modi verwendet werden: **ReceiveAndDelete** und **PeekLock**.

Bei Verwendung des **ReceiveAndDelete**-Modus ist der Nachrichtenempfang ein Single-Shot-Vorgang. Dies bedeutet, wenn Service Bus eine Leseanforderung für eine Nachricht in eine Warteschlange erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurück gesendet. **Der (standardmäßig verwendete) ReceiveAndDelete**-Modus ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird.
Da Service Bus die Nachricht als verwendet markiert hat, wird diese Nachricht verpasst, die vor dem Absturz verwendet wurde, wenn die Anwendung neu gestartet wird und erneut mit der Verwendung von Nachrichten beginnt.

Im **PeekLock**-Modus ist der Empfangsvorgang zweistufig. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf von **Delete** für die empfangene Nachricht durch. Wenn Service Bus den **Delete**-Aufruf erkennt, markiert er die Nachricht als verarbeitet und entfernt sie aus der Warteschlange.

Das folgende Beispiel zeigt, wie Nachrichten mit dem nicht standardmäßig verwendeten Modus **PeekLock** empfangen und verarbeitet werden können. Dieses Beispiel verwendet eine Endlosschleife, und die Nachrichten werden entsprechend ihres Eingangs in die "TestQueue" verarbeitet:

    	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);
	
		while(true)  { 
	         ReceiveQueueMessageResult resultQM = 
	     			service.receiveQueueMessage("TestQueue", opts);
		    BrokeredMessage message = resultQM.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());    
			    // Display the queue message.
			    System.out.print("From queue: ");
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
			        message.getProperty("MyProperty"));
			    // Remove message from queue.
			    System.out.println("Deleting this message.");
			    //service.deleteMessage(message);
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

Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine empfangene Anwendung eine Nachricht aus einem beliebigen Grund nicht verarbeiten kann, kann sie die **unlockMessage**-Methode für die empfangene Nachricht aufrufen (anstelle der **deleteMessage**-Methode). Dies führt dazu, dass Service Bus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Zudem wird einer in der Warteschlange gesperrten Nachricht ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel, wenn die Anwendung abstürzt), entsperrt Service
Bus die Nachricht automatisch und macht sie verfügbar, damit sie erneut empfangen werden kann.

Falls die Anwendung nach der Verarbeitung der Nachricht aber vor Ausgabe der Anforderung **deleteMessage** abstürzt, wird die Nachricht erneut an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (mindestens einmalige Verarbeitung) bezeichnet und bedeutet, dass jede Nachricht mindestens ein Mal verarbeitet wird, unter bestimmten Umständen jedoch ggf. auch erneut übermittelt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **getMessageId**-Methode der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

## Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Service Bus-Warteschlangen vertraut sind, finden Sie weitere Informationen im MSDN-Thema [Warteschlangen, Themen und Abonnements][].

  [Azure-SDK für Java]: http://azure.microsoft.com/develop/java/
  [Was sind Service Bus-Warteschlangen?]: #what-are-service-bus-queues
  [Erstellen eines Dienstnamespaces]: #create-a-service-namespace
  [Abrufen der Standard-Anmeldeinformationen für den Namespace]: #obtain-default-credentials
  [Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus]: #bkmk_ConfigApp
  [Gewusst wie: Erstellen eines Anbieters für Sicherheitstoken]: #bkmk_HowToCreateQueue
  [Gewusst wie: Senden von Nachrichten an eine Warteschlange]: #bkmk_HowToSendMsgs
  [Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange]: #bkmk_HowToReceiveMsgs
  [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #bkmk_HowToHandleAppCrashes
  [Nächste Schritte]: #bkmk_NextSteps
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com/
  [Warteschlangen, Themen und Abonnements]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx

<!--HONumber=47-->
 