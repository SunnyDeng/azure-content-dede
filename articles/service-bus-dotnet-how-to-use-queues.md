<properties urlDisplayName="Service Bus Queues" pageTitle="Verwenden von Service Bus-Warteschlangen (.NET) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues C#, Azure queues .NET" description="Learn how to use Service Bus queues in Azure. Code samples written in C# using the .NET API." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />





# Einsatz von Servicebus-Warteschlangen

<span>In diesem Leitfaden erfahren Sie, wie Sie Service Bus-Warteschlangen verwenden können. Die Beispiele sind in C\# geschrieben und verwenden die .NET API. Die dargestellten Szenarien beinhalten das **Erstellen von Warteschlangen, das Senden und Empfangen von Nachrichten** sowie das **Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

##Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus

Wenn Sie eine Anwendung erstellen, die Service Bus verwendet, müssen Sie
einen Verweis zur Service Bus-Assembly hinzufügen und die entsprechenden
Namespaces einschließen.

##Abrufen des Service Bus NuGet-Pakets

Das Service Bus **NuGet**-Paket ist die einfachste Möglichkeit zum Abrufen der Service Bus-API und zum Konfigurieren der Anwendung mit allen Service Bus-Abhängigkeiten. Die Visual Studio-Erweiterung für NuGet erleichtert das Installieren und Aktualisieren von Bibliotheken und Tools in Visual Studio und Visual Studio Express 2012 for Web.

Gehen sie folgendermaßen vor, um das NuGet-Paket in der Anwendung zu installieren:

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf **NuGet-Pakete verwalten**.
2.  Suchen Sie nach "WindowsAzure", und wählen Sie das Element **Azure Service Bus** aus. Klicken Sie auf **Installieren**, um die Installation abzuschließen, und schließen Sie dann das Dialogfeld.

    ![][7]

Sie können nun Code für Service Bus erstellen.


##So richten Sie eine Service Bus-Verbindungszeichenfolge ein

Service Bus verwendet eine Verbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen. Sie können die Verbindungszeichenfolge in einer Konfigurationsdatei ablegen, statt sie fest in Code zu programmieren:

- Bei der Verwendung von Azure-Cloud-Diensten empfiehlt es sich, die Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems (Dateien *.csdef und *.cscfg) zu speichern.
- Bei der Verwendung von Azure-Websites oder Azure Virtual Machines sollten Sie Ihre Verbindungszeichenfolge mithilfe des .NET-Konfigurationssystems speichern (z. B. web.config-Datei).

In beiden Fällen können Sie Ihre Verbindungszeichenfolge über die CloudConfigurationManager.GetSetting-Methode abrufen, wie später in diesem Leitfaden beschrieben.

### <a name="config-connstring"> </a>Konfigurieren der Verbindungszeichenfolge bei der Verwendung von Clouddiensten

Die Dienstkonfiguration ist in Azure-Clouddienstprojekten eindeutig und ermöglicht es Ihnen, Konfigurationseinstellungen im Azure-Verwaltungsportal dynamisch zu ändern, ohne die Anwendung erneut bereitzustellen.  Fügen Sie beispielsweise der Dienstdefinitionsdatei (*.csdef) eine Einstellung hinzu, wie nachfolgend gezeigt:

	<ServiceDefinition name="WindowsAzure1">
	...
		<WebRole name="MyRole" vmsize="Small">
	    	<ConfigurationSettings>
	      		<Setting name="Microsoft.ServiceBus.ConnectionString" />
    		</ConfigurationSettings>
  		</WebRole>
	...
	</ServiceDefinition>

Anschließend geben Sie Werte in der Dienstkonfigurationsdatei (*.cscfg) an:

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

Bei der Verwendung von Websites oder Virtual Machines sollten Sie das .NET-Konfigurationssystem verwenden (z. B. web.config).  Sie speichern die Verbindungszeichenfolge mit dem Element <appSettings>:

	<configuration>
	    <appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
			     value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
		</appSettings>
	</configuration>

Verwenden Sie die aus dem Verwaltungsportal abgerufenen Aussteller- und Schlüsselwerte. Das Verfahren wurde im vorherigen Abschnitt beschrieben.

##Erstellen einer Warteschlange

Sie können Verwaltungsvorgänge für Service Bus-Warteschlangen über die Klasse **NamespaceManager** durchführen. Die Klasse **NamespaceManager** stellt Methoden zum Erstellen, Aufzählen und Löschen von Warteschlangen zur Verfügung. 

In diesem Beispiel wird ein **NamespaceManager**-Objekt mit der Azure-Klasse **CloudConfigurationManager** mit einer Verbindungszeichenfolge erstellt, die aus der Basisadresse eines Service Bus-Dienstnamespace und den entsprechenden Anmeldeinformationen mit Berechtigungen für dessen Verwaltung besteht. Diese Verbindungszeichenfolge weist die folgende Form auf: 

	Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]

Beispiel mit den Konfigurationseinstellungen aus dem vorherigen Abschnitt:

	// Create the queue if it does not exist already
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

Es gibt Überladungen der **CreateQueue**-Methode, die es Ihnen ermöglichen, die Eigenschaften der Warteschlange zu optimieren, um beispielsweise den Standardwert für die Lebensdauer festzulegen, der auf an die Warteschlange gesendete Nachrichten angewendet wird. Diese Einstellungen werden mithilfe der Klasse **QueueDescription** angewendet. Im folgenden Beispiel wird dargelegt, wie eine Warteschlange mit der Bezeichnung "TestQueue" mit einer maximalen Größe von 5 GB und einer Standardnachricht "time-to-live" von 1 Minute erstellt wird:

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

**Hinweis:** Sie können die **QueueExists**-Methode bei **NamespaceManager**-Objekten zur Überprüfung verwenden, ob eine Warteschlange mit einem spezifischen Namen bereits innerhalb eines Dienstnamespace vorhanden ist.

##Senden von Nachrichten an eine Warteschlange

Zum Senden einer Nachricht an eine Service Bus-Warteschlange erstellt Ihre Anwendung ein **QueueClient**-Objekt mit der Verbindungszeichenfolge.

Der Code unten zeigt, wie Sie ein **QueueClient**-Objekt
für die Warteschlange "TestQueue" erstellen, die oben mithilfe des **CreateFromConnectionString**-API-Aufrufs erstellt wurde:

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client = 
		QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

	Client.Send(new BrokeredMessage());

Die Nachrichten, die an die Service Bus-Warteschlangen gesendet (und von diesen empfangen) werden, sind Instanzen der **BrokeredMessage**-Klasse. **BrokeredMessage**-Objekte weisen eine Reihe von Standardeigenschaften (wie **Label** und **TimeToLive**), ein Wörterbuch zur Aufnahme benutzerdefinierter anwendungsspezifischer Eigenschaften und einen Bestand beliebiger Anwendungsdaten auf. Eine Anwendung kann den Textkörper der Nachricht festlegen, indem ein beliebiges serialisierbares Objekt an den Konstruktor von **BrokeredMessage** übergeben wird. Das entsprechende **DataContractSerializer**-Objekt wird dann zum Serialisieren des Objekts verwendet. Alternativ kann ein System.IO.Stream-Objekt bereitgestellt werden.  Das folgende Beispiel zeigt, wie fünf Testnachrichten an den **QueueClient** "TestQueue" gesendet werden, der über den oben angegebenen Codeausschnitt abgerufen wurde:

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

##Empfangen von Nachrichten aus einer Warteschlange

Die einfachste Möglichkeit zum Empfangen von Nachrichten aus einer Warteschlange ist die Verwendung eines **QueueClient**-Objekts. Diese Objekte können in zwei verschiedenen Modi verwendet werden: **ReceiveAndDelete** und **PeekLock**.

Bei Verwendung des Modus **ReceiveAndDelete** ist der Nachrichtenempfang ein Single-Shot-Vorgang. Wenn der Service Bus also eine Leseanforderung für eine Nachricht in einer Warteschlange erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurückgegebenen. **ReceiveAndDelete** ist das einfachste Modell und am besten für Szenarien geeignet, in denen eine Anwendung tolerieren kann, dass eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da der Servicebus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Im Modus **PeekLock** (dem Standardmodus) ist der Empfangsprozess ein aus zwei Phasen bestehender Vorgang, der die Unterstützung von Anwendungen ermöglicht, die fehlende Nachrichten nicht tolerieren können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert) hat, führt sie die zweite Phase des Empfangsprozesses durch Aufruf von "Complete" für die empfangene Nachricht durch. Wenn Service Bus den Complete-Aufruf erkennt, kennzeichnet der Dienst die Nachricht als verarbeitet und entfernt sie aus der Warteschlange.

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

##Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die Abandon-Methode zu der empfangenen Nachricht aufrufen (anstelle der **Complete**-Methode). Dies bewirkt, dass der Servicebus die Nachricht innerhalb der Warteschlange entsperren und verfügbar machen kann, damit sie entweder von derselben verbrauchenden Anwendung oder von einer anderen verbrauchenden Anwendung erneut empfangen werden kann.

Mit einer innerhalb der Warteschlange gesperrten Nachricht ist außerdem eine Zeitlimitüberschreitung verknüpft. Wenn die Anwendung die Nachricht nicht verarbeiten kann, bevor die Zeitlimitüberschreitung der Sperre abläuft (z. B. falls die Anwendung abstürzt), so entsperrt der Servicebus die Nachricht automatisch und macht sie verfügbar, so dass sie wieder empfangen werden kann.

Falls die Anwendung nach der Verarbeitung der Nachricht aber vor Ausgabe der **Complete**-Anforderung abstürzt, wird die Nachricht erneut an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung in dem Szenario nicht zulässig ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies wird häufig durch Verwendung der Eigenschaft **MessageId** der Nachricht erzielt, die über mehrere Zustellversuche hinweg konstant bleibt.

##Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Service Bus-Warteschlangen vertraut sind, finden Sie unter den folgenden
Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements.][]
-   Erstellen einer funktionierenden Anwendung, die Nachrichten an
    und aus einer Service Bus-Warteschlange sendet: [Service Bus-Brokermessaging:
    .NET-Lernprogramm].

  [Nächste Schritte]: #next-steps
  [Was sind Service Bus-Warteschlangen?]: #what-queues
  [Erstellen eines Dienstnamespace]: #create-namespace
  [Abrufen der Standardanmeldeinformationen für den Namespace]: #obtain-creds
  [Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus]: #configure-app
  [Vorgehensweise: Einrichten einer Service Bus-Verbindungszeichenfolge]: #set-up-connstring
  [Vorgehensweise: Konfigurieren Ihrer Verbindungszeichenfolge]: #config-connstring
  [Vorgehensweise: Erstellen einer Warteschlange]: #create-queue
  [Vorgehensweise: Senden von Nachrichten an eine Warteschlange]: #send-messages
  [Vorgehensweise: Empfangen von Nachrichten aus einer Warteschlange]: #receive-messages
  [Vorgehensweise: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #handle-crashes
  [Warteschlangenkonzepte]: ./media/service-bus-dotnet-how-to-use-queues/sb-queues-08.png
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Warteschlangen, Themen und Abonnements.]: http://msdn.microsoft.com/de-de/library/windowsazure/hh367516.aspx
  [Service Bus-Brokermessaging: .NET-Lernprogramm]: http://msdn.microsoft.com/de-de/library/windowsazure/hh367512.aspx

<!--HONumber=35.1-->
