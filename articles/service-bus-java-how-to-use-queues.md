<properties linkid="dev-java-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Java) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Java" description="Learn how to use Service Bus queues in Azure. Code samples written in Java." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Queues" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Einsatz von Servicebus-Warteschlangen

In diesem Leitfaden erfahren Sie, wie Sie die Servicebus-Warteschlangen verwenden können. Die Beispiele wurden
in Java geschrieben und verwenden das [Azure-SDK für Java][Azure-SDK für Java]. Die
Szenarien behandeln die Themen **Erstellen von Warteschlangen**, **Senden und Empfangen von
Nachrichten** und **Löschen von Warteschlangen**.

## Inhaltsverzeichnis

-   [Was sind Servicebus-Warteschlangen?][Was sind Servicebus-Warteschlangen?]
-   [Erstellen eines Dienstnamespace][Erstellen eines Dienstnamespace]
-   [Abrufen der Standard-Anmeldeinformationen für den Namespace][Abrufen der Standard-Anmeldeinformationen für den Namespace]
-   [Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus][Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus]
-   [Gewusst wie: Erstellen eines Anbieters für Sicherheitstoken][Gewusst wie: Erstellen eines Anbieters für Sicherheitstoken]
-   [Gewusst wie: Erstellen einer Warteschlange][Gewusst wie: Erstellen eines Anbieters für Sicherheitstoken]
-   [Gewusst wie: Senden von Nachrichten an eine Warteschlange][Gewusst wie: Senden von Nachrichten an eine Warteschlange]
-   [Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange][Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange]
-   [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten][Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]
-   [Nächste Schritte][Nächste Schritte]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="bkmk_ConfigApp"> </a> Konfigurieren der Anwendung zur Verwendung eines Service Bus

Fügen Sie die folgenden import-Anweisungen am Anfang der Java-Datei ein:

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*; 
    import com.microsoft.windowsazure.services.core.*; 
    import javax.xml.datatype.*;

## <a name="bkmk_HowToCreateQueue"> </a>Erstellen einer Warteschlange

Sie können Verwaltungsvorgänge für Service Bus-Warteschlangen über die Klasse
**ServiceBusContract** durchführen. **ServiceBusContract**-Objekte werden
mit einer entsprechenden Konfiguration erstellt, welche die
Tokenberechtigungen für deren Verwaltung kapselt. Die Klasse **ServiceBusContract**
ist der einzige Kommunikationspunkt mit Azure.

Die Klasse **ServiceBusService** enthält Methoden zum Erstellen, Aufzählen
und Löschen von Warteschlangen. Das folgende Beispiel zeigt, wie Sie mit einem **ServiceBusService**-Objekt
eine Warteschlange mit dem Namen "TestQueue" in einem Namespace mit dem Namen "HowToSample" erstellen können:

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

Mit den Methoden in QueueInfo können Sie Eigenschaften der Warteschlange
einstellen (z. B. den Standardwert "time-to-live",
der auf an die Warteschlange gesendete Nachrichten angewendet wird). Das folgende Beispiel zeigt, wie
eine Warteschlange namens "TestQueue" mit einer maximalen Größe von 5 GB erstellt wird:

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateQueueResult result = service.createQueue(queueInfo);

Sie können die Methode **listQueues** von **ServiceBusContract**
-Objekten verwenden, um zu prüfen, ob eine Warteschlange mit einem spezifischen
Namen bereits innerhalb eines Dienstnamespace vorhanden ist.

## <a name="bkmk_HowToSendMsgs"> </a>Senden von Nachrichten an eine Warteschlange

Um eine Nachricht an eine Service Bus-Warteschlange zu senden, benötigt Ihre Anwendung ein
**ServiceBusContract**-Objekt. Der folgende Code zeigt, wie Sie eine Nachricht
an die zuvor erstellte Warteschlange "TestQueue" im Dienstnamespace
"HowToSample" schicken können:

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

Die Nachrichten, die an die Service Bus-Warteschlangen gesendet (und von
diesen empfangen) werden, sind Instanzen der Klasse **BrokeredMessage**. **BrokeredMessage**-Objekte enthalten
eine Reihe von Standardmethoden (wie etwa **getLabel**, **getTimeToLive**,
**setLabel** und **setTimeToLive**), ein Wörterbuch
für die Aufnahme benutzerdefinierter anwendungsspezifischer
Eigenschaften und einen Bestand beliebiger Anwendungsdaten. Eine Anwendung kann den Hauptteil der
Nachricht einstellen, indem ein beliebiges serialisierbares
Objekt in den Konstruktor der **BrokeredMessage** übergeben wird.
Anschließend wird das passende Serialisierungsprogramm zur Serialisierung des Objekts verwendet. Alternativ kann ein **java.IO.InputStream**
angegeben werden.

Im folgenden Beispiel wird dargelegt, wie fünf Testnachrichten an den
**MessageSender** "TestQueue" gesendet werden, der über den oben angegebene Codeausschnitt erhalten wurde:

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i); 
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von
256 KB (der Header, der die standardmäßigen und die
benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben). Bei der Anzahl der Nachrichten, die in einer
Warteschlange aufgenommen werden können, besteht keine Beschränkung.
Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung
definiert. Die Obergrenze beträgt 5 GB.

## <a name="bkmk_HowToReceiveMsgs"> </a>Empfangen von Nachrichten aus einer Warteschlange

Der einfachste Weg zum Empfangen von Nachrichten aus Warteschlangen sind
**ServiceBusContract**-Objekte. Empfangene Nachrichten können in
zwei verschiedenen Modi verwendet werden: **ReceiveAndDelete** und **PeekLock**.

Bei Verwendung des Modus **ReceiveAndDelete** ist der Nachrichtenempfang
ein Single-Shot-Vorgang. Wenn Service Bus also eine
Leseanforderung für eine Nachricht in einer Warteschlange erhält, wird die
Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurückgesendet. Der (standardmäßig verwendete) Modus **ReceiveAndDelete**
ist das einfachste Modell. Es wird am besten für Szenarien
eingesetzt, bei denen es eine Anwendung tolerieren kann,
wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Stellen Sie sich zum besseren Verständnis ein Szenario vor, bei
dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt,
bevor diese verarbeitet wird. Da die Nachricht von Service Bus als
verarbeitet gekennzeichnet wurde, wird die vor dem Absturz verarbeitete
Nachricht nicht berücksichtigt, wenn die Anwendung neu gestartet und die Verarbeitung von Nachrichten wieder aufgenommen wird.

Im Modus **PeekLock** ist der Empfangsvorgang zweistufig. Dadurch können
Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen
von Nachrichten nicht zulassen können. Wenn Service Bus eine Anforderung erhält, ermittelt der Dienst die
nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass
andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem die Anwendung die
Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks
zukünftiger Verarbeitung zuverlässig gespeichert hat), führt sie die zweite Phase des
Empfangsprozesses durch Aufruf von **Delete** für die empfangene Nachricht durch. Wenn Service Bus den **Delete**-Aufruf
erkennt, markiert er die Nachricht als verarbeitet und entfernt sie aus der Warteschlange.

Das folgende Beispiel zeigt, wie Nachrichten mit dem nicht standardmäßig
verwendeten Modus **PeekLock** empfangen und verarbeitet werden können. Dieses Beispiel
verwendet eine Endlosschleife, und die Nachrichten werden
entsprechend ihres Eingangs in die "TestQueue" verarbeitet:

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

## <a name="bkmk_HowToHandleAppCrashes"> </a> Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Ihnen bei der
ordnungsgemäßen Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn
eine empfangene Anwendung eine Nachricht aus einem beliebigen
Grund nicht verarbeiten kann, kann sie die Methode **unlockMessage** für die empfangene
Nachricht aufrufen (anstelle der Methode **deleteMessage**). Dies führt dazu, dass
Service Bus die Nachricht innerhalb der Warteschlange entsperrt
und verfügbar macht, damit sie erneut empfangen werden kann, und
zwar entweder durch dieselbe verarbeitende Anwendung oder eine andere.

Zudem wird einer in der Warteschlange gesperrten Nachricht ein
Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des
Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die
Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und
macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht aber vor
Ausgabe der Anforderung **deleteMessage** abstürzt, wird die
Nachricht erneut an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird
häufig als **At Least Once Processing** (Verarbeitung mindestens
einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens
einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung in dem Szenario
nicht zulässig ist, sollten Anwendungsentwickler ihrer
Anwendung zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies wird häufig
durch die Verwendung der Methode **getMessageId** der Nachricht
erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

## <a name="bkmk_NextSteps"> </a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Service Bus-Warteschlangen
vertraut sind, finden Sie weitere Informationen im MSDN-Thema [Warteschlangen, Themen und Abonnements][Warteschlangen, Themen und Abonnements].

  [Azure-SDK für Java]: http://azure.microsoft.com/de-de/develop/java/
  [Was sind Servicebus-Warteschlangen?]: #what-are-service-bus-queues
  [Erstellen eines Dienstnamespace]: #create-a-service-namespace
  [Abrufen der Standard-Anmeldeinformationen für den Namespace]: #obtain-default-credentials
  [Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus]: #bkmk_ConfigApp
  [Gewusst wie: Erstellen eines Anbieters für Sicherheitstoken]: #bkmk_HowToCreateQueue
  [Gewusst wie: Senden von Nachrichten an eine Warteschlange]: #bkmk_HowToSendMsgs
  [Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange]: #bkmk_HowToReceiveMsgs
  [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #bkmk_HowToHandleAppCrashes
  [Nächste Schritte]: #bkmk_NextSteps
  [howto-service-bus-queues]: ../includes/howto-service-bus-queues.md
  [Warteschlangen, Themen und Abonnements]: http://msdn.microsoft.com/de-de/library/windowsazure/hh367516.aspx
