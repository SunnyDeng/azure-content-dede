<properties
    pageTitle="Verwenden von Service Bus-Warteschlangen (.NET) - Azure"
    description="Erfahren Sie mehr über die Verwendung von Service Bus-Warteschlangen in Azure. Die Codebeispiele wurden in C# mithilfe der .NET-API geschrieben."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>


<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="07/02/2015"
    ms.author="sethm"/>


# Verwenden von Servicebus-Warteschlangen

In diesem Leitfaden wird beschrieben, wie Sie Service Bus-Warteschlangen verwenden. Die Beispiele sind in C# geschrieben und verwenden die .NET API. Die behandelten Szenarien umfassen das **Erstellen von Warteschlangen** und das **Senden und Empfangen von Nachrichten**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte](#Next-steps).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus

Wenn Sie eine Anwendung erstellen, die den Servicebus verwendet, müssen Sie zu der Servicebus-Assembly eine Referenz hinzufügen und die zugehörigen Namespaces einbinden.

## Hinzufügen des NuGet-Pakets "Service Bus"

Das Service Bus-Paket **NuGet** stellt die einfachste Möglichkeit dar, die Service Bus-API abzurufen und Ihre Anwendung mit allen Service Bus-Abhängigkeiten zu konfigurieren. Die NuGet Visual Studio-Erweiterung ermöglicht eine problemlose Installation und Aktualisierung von Bibliotheken und Tools in Visual Studio und Visual Studio Express. Das NuGet-Paket "Service Bus" stellt die einfachste Möglichkeit zum Abrufen der Service Bus-API und zum Konfigurieren der Anwendung mit allen Service Bus-Abhängigkeiten dar.

Gehen sie folgendermaßen vor, um das NuGet-Paket in der Anwendung zu installieren:

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf **NuGet-Pakete verwalten**.
2.  Suchen Sie nach "Service Bus", und wählen Sie das Element **Microsoft Azure Service Bus** aus. Klicken Sie auf **Installieren**, um die Installation abzuschließen. Schließen Sie danach dieses Dialogfeld.

    ![][7]

Sie können nun Code für Service Bus erstellen.

## Einrichten einer Service Bus-Verbindungszeichenfolge

Service Bus verwendet eine Verbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen. Sie können die Verbindungszeichenfolge in einer Konfigurationsdatei ablegen, statt sie fest zu programmieren:

- Bei der Verwendung von Azure-Clouddiensten empfiehlt es sich, die Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems (Dateien ***\*.csdef** and ***\*.cscfg**) zu speichern.
- Bei der Verwendung von Azure-Websites oder Azure Virtual Machines sollten Sie Ihre Verbindungszeichenfolge mithilfe des .NET-Konfigurationssystems speichern (z. B. die **Web.config**-Datei).

In beiden Fällen können Sie Ihre Verbindungszeichenfolge über die `CloudConfigurationManager.GetSetting`-Methode abrufen, wie später in dieser Anleitung beschrieben.

### Konfigurieren der Verbindungszeichenfolge bei Verwendung von Cloud Services

Azure-Cloud-Dienst-Projekte bieten einen einzigartigen Dienstkonfigurationsmechanismus, mit dem Sie Ihre Konfigurationseinstellungen im Azure-Verwaltungsportal dynamisch ändern können, ohne Ihre Anwendung neu bereitstellen zu müssen. Fügen Sie beispielsweise der Dienstdefinitionsdatei (***\*.csdef**) eine `Setting`-Bezeichnung hinzu, wie nachfolgend gezeigt:

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />

            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Anschließend geben Sie Werte in der Dienstkonfigurationsdatei (***\*.cscfg**) an:

    <ServiceConfiguration serviceName="Azure1">
    ...
        <Role name="MyRole">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString"
                         value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />

            </ConfigurationSettings>
        </Role>
    ...
    </ServiceConfiguration>

Verwenden Sie die aus dem Verwaltungsportal abgerufenen SAS-Schlüsselwerte und -Schlüsselnamen (Shared Access Signature). Das Verfahren wurde im vorherigen Abschnitt beschrieben.

### Konfigurieren der Verbindungszeichenfolge bei Verwendung von Websites oder virtuellen Computern

Bei der Verwendung von Websites oder virtuellen Computern wird es empfohlen, das .NET-Konfigurationssystem zu verwenden (z. B. **Web.config**). Sie speichern die Verbindungszeichenfolge mit dem `<appSettings>`-Element:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />

        </appSettings>
    </configuration>

Verwenden Sie die aus dem Verwaltungsportal abgerufenen SAS-Namenswerte und -Schlüsselwerte. Das Verfahren wurde im vorherigen Abschnitt beschrieben.

## Erstellen von Warteschlangen

Sie können Verwaltungsvorgänge für Service Bus-Warteschlangen über die [`NamespaceManager`-Klasse](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) durchführen. Diese Klasse stellt Methoden zum Erstellen, Aufzählen und Löschen von Warteschlangen zur Verfügung.

In diesem Beispiel wird ein `NamespaceManager`-Objekt mit der Azure-Klasse `CloudConfigurationManager` mit einer Verbindungszeichenfolge erstellt, die aus der Basisadresse eines Service Bus-Dienstnamespace und den entsprechenden SAS-Anmeldeinformationen mit Berechtigungen für dessen Verwaltung besteht. Diese Verbindungszeichenfolge weist die folgende Form auf:

    Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey

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

Es gibt Überladungen der [`CreateQueue`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx)-Methode, die es Ihnen ermöglichen, die Eigenschaften der Warteschlange zu optimieren, um beispielsweise den Standardwert für die Lebensdauer festzulegen, der auf an die Warteschlange gesendete Nachrichten angewendet wird. Diese Einstellungen werden mithilfe der [`QueueDescription`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx)-Klasse angewendet. Im folgenden Beispiel wird dargelegt, wie eine Warteschlange mit der Bezeichnung "TestQueue" mit einer maximalen Größe von 5 GB und einer Standardnachricht "time-to-live" von 1 Minute erstellt wird:

    // Configure queue settings
    QueueDescription qd = new QueueDescription("TestQueue");
    qd.MaxSizeInMegabytes = 5120;
    qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new queue with custom settings
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue(qd);
    }

> [AZURE.NOTE]Sie können die [`QueueExists`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx)-Methode bei [`NamespaceManager`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)-Objekten zur Überprüfung verwenden, ob eine Warteschlange mit einem spezifischen Namen bereits innerhalb eines Dienstnamespace vorhanden ist.

## Senden von Nachrichten an eine Warteschlange

Zum Senden einer Nachricht an eine Service Bus-Warteschlange erstellt Ihre Anwendung ein [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx)-Objekt mit der Verbindungszeichenfolge.

Der folgende Code veranschaulicht, wie ein [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx)-Objekt für die Warteschlange "TestQueue" erstellt wird, die Sie gerade mit dem [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx)-API-Aufruf erstellt haben:

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client =
        QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    Client.Send(new BrokeredMessage());

Nachrichten, die an die Service Bus-Warteschlangen gesendet werden (und von diesen empfangen werden), sind Instanzen der [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)-Klasse. `BrokeredMessage`-Objekte verfügen über einen Satz von Standardeigenschaften (z. B. [`Label`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) und [`TimeToLive`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), ein Wörterbuch, in dem benutzerdefinierte anwendungsspezifische Eigenschaften enthalten sind, sowie einen Bestand beliebiger Anwendungsdaten. Eine Anwendung kann den Textkörper der Nachricht festlegen, indem ein beliebiges serialisierbares Objekt an den Konstruktor des [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)-Objekts übergeben wird. Der entsprechende **DataContractSerializer** wird dann zum Serialisieren des Objekts verwendet. Alternativ kann ein **System.IO.Stream**-Objekt zur Verfügung gestellt werden.

Das folgende Beispiel zeigt, wie fünf Testnachrichten an das [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx)-Objekt "TestQueue" gesendet werden, das im vorherigen Codeausschnitt abgerufen wurde:

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

Service Bus-Warteschlangen unterstützen eine [maximale Nachrichtengröße von 256 KB](service-bus-quotas.md) (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben). Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB. Wenn Partitionierung aktiviert ist, ist die Obergrenze höher. Weitere Informationen finden Sie unter [Partitionieren von Nachrichtenentitäten](https://msdn.microsoft.com/library/azure/dn520246.aspx).

## Empfangen von Nachrichten aus einer Warteschlange

Die empfohlene Methode zum Empfangen von Nachrichten aus einer Warteschlange ist die Verwendung eines [`QueueClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx)-Objekts. `QueueClient`-Objekte können in zwei verschiedenen Modi arbeiten: [`ReceiveAndDelete` und `PeekLock`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Bei Verwendung des Modus **ReceiveAndDelete** ist der Nachrichtenempfang ein Single-Shot-Vorgang. Wenn Service Bus also eine Leseanforderung für eine Nachricht in einer Warteschlange erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurückgegeben. **ReceiveAndDelete** ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da die Nachricht von Service Bus als verarbeitet gekennzeichnet wurde, wird die vor dem Absturz verarbeitete Nachricht nicht berücksichtigt, wenn die Anwendung neu gestartet und die Verarbeitung von Nachrichten wieder aufgenommen wird.

Im Modus **PeekLock** (dies ist der Standardmodus) wird der Empfangsvorgang zu einem zweistufigen Vorgang. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf von [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) für die empfangene Nachricht durch. Wenn Service Bus den `Complete`-Aufruf erkennt, kennzeichnet der Dienst die Nachricht als verarbeitet und entfernt sie aus der Warteschlange.

Das folgende Beispiel zeigt, wie Nachrichten mit dem Standardmodus **PeekLock** empfangen und verarbeitet werden können. Zum Angeben eines anderen [`ReceiveMode`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)-Werts können Sie eine andere Überladung von [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx) verwenden. Dieses Beispiel verwendet den [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx)-Rückruf zum Verarbeiten von Nachrichten, während diese in **TestQueue** eintreffen.

    string connectionString =
      CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
    QueueClient Client =
      QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    // Configure the callback options
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    // Callback to handle received messages
    Client.OnMessage((message) =>
    {
        try
        {
            // Process message from queue
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Test Property: " +
            message.Properties["TestProperty"]);

            // Remove message from queue
            message.Complete();
        }
            catch (Exception)
        {
            // Indicates a problem, unlock message in queue
            message.Abandon();
        }
    }, options);

Dieses Beispiel konfiguriert den [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx)-Rückruf mithilfe eines [`OnMessageOptions`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx)-Objekts. [`AutoComplete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) ist auf **false** festgelegt, um die manuelle Steuerung über den Aufrufzeitpunkt von [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) für die empfangene Nachricht zu aktivieren. [`AutoRenewTimeout`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) ist auf eine Minute festgelegt. Dadurch wartet der Client bis zu eine Minute auf eine Nachricht, bevor der Aufruf abläuft und der Client einen neuen Aufruf startet, um nach Nachrichten zu suchen. Dieser Eigenschaftswert verringert die Anzahl der kostenpflichtigen Aufrufe des Clients, bei denen keine Nachrichten abgerufen werden.

## Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die [`Abandon`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx)-Methode zu der empfangenen Nachricht aufrufen (anstelle der [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)-Methode). Dies führt dazu, dass Service Bus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Mit einer innerhalb der Warteschlange gesperrten Nachricht ist außerdem eine Zeitlimitüberschreitung verknüpft. Wenn die Anwendung die Nachricht nicht verarbeiten kann, bevor die Zeitlimitüberschreitung der Sperre abläuft (z. B. falls die Anwendung abstürzt), so entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, sodass sie wieder empfangen werden kann.

Falls die Anwendung nach der Verarbeitung der Nachricht aber vor Ausgabe der [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)-Anforderung abstürzt, wird die Nachricht erneut an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der [`MessageId`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx)-Eigenschaft der Nachricht erreicht, die über mehrere Übermittlungsversuche hinweg konstant bleibt.

## Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Servicebus-Warteschlangen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Übersicht: [Warteschlangen, Themen und Abonnements][].
-   Erstellen Sie eine Arbeitsanwendung, die Nachrichten an eine Servicebus-Warteschlange sendet und von dort empfängt: [Servicebus für Brokermessaging im .NET-Lernprogramm].
-   Service Bus-Beispiele: Laden Sie sie aus den [Azure-Beispielen][] herunter, oder sehen Sie sich die Übersicht auf [MSDN][] an.

  [What are Service Bus Queues]: #what-queues
  [Create a Service Namespace]: #create-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-creds
  [Configure Your Application to Use Service Bus]: #configure-app
  [How to: Set Up a Service Bus Connection String]: #set-up-connstring
  [How to: Configure your Connection String]: #config-connstring
  [How to: Create a Queue]: #create-queue
  [How to: Send Messages to a Queue]: #send-messages
  [How to: Receive Messages from a Queue]: #receive-messages
  [How to: Handle Application Crashes and Unreadable Messages]: #handle-crashes
  [Azure Management Portal]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Warteschlangen, Themen und Abonnements]: http://msdn.microsoft.com/library/azure/hh367516.aspx
  [Servicebus für Brokermessaging im .NET-Lernprogramm]: http://msdn.microsoft.com/library/azure/hh367512.aspx
  [Azure-Beispielen]: https://code.msdn.microsoft.com/windowsazure/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [MSDN]: https://msdn.microsoft.com/library/azure/dn194201.aspx
 

<!---HONumber=August15_HO6-->