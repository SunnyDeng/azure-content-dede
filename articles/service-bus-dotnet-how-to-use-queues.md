<properties urlDisplayName="Service Bus Queues" pageTitle="Verwenden von Servicebus-Warteschlangen (.NET) - Azure" metaKeywords="Azure Service Bus-Warteschlangen, Azure-Warteschlangen, Azure Messaging, Azure-Warteschlangen C#, Azure-Warteschlangen, .NET" description="Learn how to use Service Bus queues in Azure. Code samples written in C# using the .NET API." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />





# Einsatz von Servicebus-Warteschlangen

<span>In diesem Leitfaden erfahren Sie, wie Sie die Servicebus-Warteschlangen verwenden können. Die Beispiele sind in C\# geschrieben und verwenden die .NET API. Die Szenarien behandeln die Themen **Erstellen von Warteschlangen, Senden und Empfangen von Nachrichten** und **Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

<h2>Konfigurieren der Anwendung für die Verwendung von Service Bus</h2>

Wenn Sie eine Anwendung erstellen, die den Servicebus verwendet, müssen Sie zu der Servicebus-Assembly eine Referenz hinzufügen und die zugehörigen Namespaces einbinden.

<h2>Abrufen des Service Bus NuGet-Pakets</h2>

Das Service Bus **NuGet**-Paket stellt die einfachste Möglichkeit zum Abrufen der Service Bus-API und Konfigurieren der Anwendung mit allen Service Bus-Abhängigkeiten dar. Die Visual Studio-Erweiterung für NuGet erleichtert das Installieren und Aktualisieren von Bibliotheken und Tools in Visual Studio und Visual Studio Express 2012 for Web.

Gehen sie folgendermaßen vor, um das NuGet-Paket in der Anwendung zu installieren:

1. Klicken Sie im Projektmappen-Explorer, mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf
    **NuGet-Pakete verwalten**.
2. Suchen Sie nach "WindowsAzure", und wählen Sie das **Azure
Service Bus**-Element. Klicken Sie auf **Installieren**, um die Installation abzuschließen.
    Schließen Sie dann das Dialogfeld.

    ![][7]

Sie können nun Code für Service Bus erstellen.

<h2>Gewusst wie: Einrichten einer Service Bus-Verbindungszeichenfolge</h2>

Service Bus verwendet eine Verbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen. Sie können die Verbindungszeichenfolge in einer Konfigurationsdatei ablegen, statt sie fest in Code zu programmieren:

- Bei der Verwendung von Azure-Cloud-Diensten empfiehlt es sich, die Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems (Dateien "*.csdef" und "*.cscfg") zu speichern.
- Bei der Verwendung von Azure-Websites oder Azure Virtual Machines sollten Sie Ihre Verbindungszeichenfolge mithilfe des .NET-Konfigurationssystems speichern (z. B. "web.config-Datei).

In beiden Fällen können Sie die Verbindungszeichenfolge mit der "CloudConfigurationManager.GetSetting"-Methode abrufen, wie später in diesem Leitfaden gezeigt.

### <a name="config-connstring"> </a>Konfigurieren der Verbindungszeichenfolge bei der Verwendung von Cloud-Diensten

Die Dienstkonfiguration ist in Azure-Cloud-Dienstprojekten eindeutig und ermöglicht es Ihnen, Konfigurationseinstellungen im Azure-Verwaltungsportal dynamisch zu ändern, ohne die Anwendung erneut bereitzustellen.  Fügen Sie beispielsweise der Dienstdefinitionsdatei ("*.csdef") eine Einstellung hinzu, wie nachfolgend gezeigt:

	<ServiceDefinition name="WindowsAzure1">
	...
		<WebRole name="MyRole" vmsize="Small">
	    	<ConfigurationSettings>
	      		<Setting name="Microsoft.ServiceBus.ConnectionString" />
    		</ConfigurationSettings>
  		</WebRole>
	...
	</ServiceDefinition>

You then specify values in the service configuration (`*.cscfg`) file:

	<ServiceConfiguration serviceName="WindowsAzure1">
	...
		<Role name="MyRole">
			<ConfigurationSettings>
				<Setting name="Microsoft.ServiceBus.ConnectionString" 
						 value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
			</ConfigurationSettings>
		</Role>
	...
	</ServiceConfiguration>

Verwenden Sie die aus dem Verwaltungsportal abgerufenen Aussteller- und Schlüsselwerte. Das Verfahren wurde im vorherigen Abschnitt beschrieben.

### Konfigurieren der Verbindungszeichenfolge bei Verwendung von Websites oder virtuellen Computern

Bei der Verwendung von Websites oder virtuellen Computern sollten Sie das .NET-Konfigurationssystem verwenden (z. . "web.config").  Sie speichern die Verbindungszeichenfolge mit dem "<appSettings>"-Element:

	<configuration>
	    <appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
			     value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
		</appSettings>
	</configuration>

Verwenden Sie die aus dem Verwaltungsportal abgerufenen Aussteller- und Schlüsselwerte. Das Verfahren wurde im vorherigen Abschnitt beschrieben.

<h2>Erstellen einer Warteschlange</h2>

Sie können Verwaltungsvorgänge für Servicebus-Warteschlangen über die Klasse **NamespaceManager** durchführen. Die **NamespaceManager**-Klasse stellt Methoden zum Erstellen, Aufzählen und Löschen von Warteschlangen zur Verfügung. 

In diesem Beispiel wird ein **NamespaceManager**-Objekt mit der Azure-Klasse **CloudConfigurationManager** mit einer Verbindungszeichenfolge erstellt, die aus der Basisadresse eines Service Bus-Dienstnamespace und den entsprechenden Anmeldeinformationen mit Berechtigungen für dessen Verwaltung besteht. Diese Verbindungszeichenfolge weist die folgende Form auf:

	Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]

For example, given the configuration settings in the previous section:

	// Create the queue if it does not exist already
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

Es gibt Überladungen der **CreateQueue**-Methode, die es Ihnen ermöglichen, die Eigenschaften der Warteschlange zu optimieren, um beispielsweise den Standardwert für die Lebensdauer festzulegen, der auf an die Warteschlange gesendete Nachrichten angewendet wird. Diese Einstellungen werden angewendet, indem die Klasse **QueueDescription** verwendet wird. Im folgenden Beispiel wird dargelegt, wie eine Warteschlange mit der Bezeichnung "TestQueue" mit einer maximalen Größe von 5 GB und einer Standardnachricht "time-to-live" von 1 Minute erstellt wird:

	// Configure Queue Settings
    QueueDescription qd = new QueueDescription("TestQueue");
    qd.MaxSizeInMegabytes = 5120;
    qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

	// Create a new Queue with custom settings
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue(qd);
    }

**Hinweis:**Sie können die **QueueExists**-Methode in **NamespaceManager**-Objekten verwenden, um zu prüfen, ob eine Warteschlange mit einem bestimmten Namen bereits innerhalb eines Dienstnamespace vorhanden ist.

<h2>Senden von Nachrichten an eine Warteschlange</h2>

Um eine Nachricht an eine Servicebus-Warteschlange zu senden, erstellt die Anwendung mit der Verbindungszeichenfolge ein **QueueClient**-Objekt.

Der folgende Code zeigt, wie ein **QueueClient**-Objekt für die oben erstellte Warteschlange "TestQueue" mithilfe des API-Aufrufs **CreateFromConnectionString** erstellt wird:

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client = 
		QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

	Client.Send(new BrokeredMessage());

Die Nachrichten, die an die Service Bus-Warteschlangen gesendet (und von diesen empfangen) werden, sind Instanzen der Klasse **BrokeredMessage**. **BrokeredMessage**-Objekte weisen eine Reihe von Standardeigenschaften (wie etwa **Label** und **TimeToLive**), ein Wörterbuch, das zur Aufnahme benutzerdefinierter anwendungsspezifischer Eigenschaften verwendet wird, und einen Körper beliebiger Anwendungsdaten auf. Eine Anwendung kann den Körper der Nachricht einstellen, indem ein beliebiges serialisierbares Objekt in den Konstruktor der **BrokeredMessage** weitergeleitet wird. Anschließend wird der passende **DataContractSerializer** zur Serialisierung des Objekts verwendet. Alternativ kann ein System.IO**System.IO.Stream**-Objekt zur Verfügung gestellt werden.

Im folgenden Beispiel wird dargelegt, wie fünf Testnachrichten an den **QueueClient** "TestQueue" gesendet werden, der über den oben angegebene Codeausschnitt abgerufen wurde:

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = new BrokeredMessage("Test message " + i);

       // Set some addtional custom app-specific properties
       message.Properties["TestProperty"] = "TestValue";
       message.Properties["Message number"] = i;   

       // Send message to the queue
       Client.Send(message);
     }

Die Servicebus-Warteschlangen unterstützen Nachrichten mit einer maximalen Größe von 256 KB (die Kopfzeile, welche die Standard- und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB aufweisen). Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

<h2>Empfangen von Nachrichten aus einer Warteschlange</h2>

Durch die Verwendung eines **QueueClient**-Objekts lassen sich Nachrichten von einer Warteschlange am einfachsten empfangen. Diese Objekte können in zwei verschiedenen Modi verwendet werden: **ReceiveAndDelete** und **PeekLock**.

Bei Verwendung des **ReceiveAndDelete**-Modus ist der Nachrichtenempfang ein Single-Shot-Vorgang. Dies bedeutet, wenn Service Bus eine Leseanforderung für eine Nachricht in eine Warteschlange erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurück gesendet. **Der ReceiveAndDelete**-Modus ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da der Servicebus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Im Modus **PeekLock** (dem Standardmodus) ist der Empfangsprozess ein aus zwei Phasen bestehender Vorgang, der die Unterstützung von Anwendungen ermöglicht, die fehlende Nachrichten nicht tolerieren können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf von **Complete** für die empfangene Nachricht durch. Wenn der Servicebus den **Complete**-Aufruf erkennt, markiert er die Nachricht als verwendet und entfernt sie aus der Warteschlange.

Das folgende Beispiel zeigt, wie Nachrichten mit dem Standardmodus **PeekLock** empfangen und verarbeitet werden können. Zum Angeben eines anderen **ReceiveMode**-Werts können Sie eine andere Überladung für **CreateFromConnectionString** verwenden. Im vorliegenden Beispiel wird eine Endlosschleife erstellt, und die Nachrichten werden entsprechend ihres Eingangs in die "TestQueue" verarbeitet:

    Client.Receive();
     
    // Continuously process messages sent to the "TestQueue" 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();

       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("Test Property: " + 
				message.Properties["TestProperty"]);

             // Remove message from queue
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in queue
             message.Abandon();
          }
       }
    } 

<h2>Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten</h2>
Service Bus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die **Abandon**-Methode zu der empfangenen Nachricht aufrufen (anstelle der **Complete**-Methode). Dies bewirkt, dass der Servicebus die Nachricht innerhalb der Warteschlange entsperren und verfügbar machen kann, damit sie entweder von derselben verbrauchenden Anwendung oder von einer anderen verbrauchenden Anwendung erneut empfangen werden kann.

Mit einer innerhalb der Warteschlange gesperrten Nachricht ist außerdem eine Zeitlimitüberschreitung verknüpft. Wenn die Anwendung die Nachricht nicht verarbeiten kann, bevor die Zeitlimitüberschreitung der Sperre abläuft (z. B. falls die Anwendung abstürzt), so entsperrt der Servicebus die Nachricht automatisch und macht sie verfügbar, so dass sie wieder empfangen werden kann.

Falls die Anwendung nach der Verarbeitung der Nachricht aber vor Ausgabe der Anforderung **Complete** abstürzt, wird die Nachricht erneut an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung in dem Szenario nicht zulässig ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **MessageId**-Eigenschaft der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

<h2>Nächste Schritte</h2>

Da Sie nun mit den Grundlagen zu Service Bus-Warteschlangen vertraut sind, finden Sie weitere Informationen unter
diesen Links.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements.][]
-   Erstellen einer funktionierenden Anwendung, die Nachrichten an
eine Service Bus-Warteschlange sendet und Nachrichten von dieser empfängt: [.NET-Lernprogramm zu Service Bus-Brokermessaging
].

  [Nächste Schritte]: #next-steps
  [Was sind Servicebus-Warteschlangen]: #what-queues
  [Erstellen eines Dienstnamespace]: #create-namespace
  [Abrufen der Standard-Anmeldeinformationen für den Namespace]: #obtain-creds
  [Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus]: #configure-app
  [Gewusst wie: Einrichten einer Service Bus-Verbindungszeichenfolge]: #set-up-connstring
  [Gewusst wie: Konfigurieren der Verbindungszeichenfolge]: #config-connstring
  [Gewusst wie: Erstellen einer Warteschlange]: #create-queue
  [Gewusst wie: Senden von Nachrichten an eine Warteschlange]: #send-messages
  [Gewusst wie: Empfangen von Nachrichten aus einer Warteschlange]: #receive-messages
  [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #handle-crashes
  [Warteschlangenkonzepte]: ./media/service-bus-dotnet-how-to-use-queues/sb-queues-08.png
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Warteschlangen, Themen und Abonnements.]: http://msdn.microsoft.com/de-de/library/windowsazure/hh367516.aspx
  [.NET-Lernprogramm zu Service Bus-Brokermessaging]: http://msdn.microsoft.com/de-de/library/windowsazure/hh367512.aspx
