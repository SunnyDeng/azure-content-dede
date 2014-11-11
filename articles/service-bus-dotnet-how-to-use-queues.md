<properties linkid="dev-net-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (.NET) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues C#, Azure queues .NET" description="Learn how to use Service Bus queues in Azure. Code samples written in C# using the .NET API." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# Einsatz von Servicebus-Warteschlangen

<span>In diesem Leitfaden erfahren Sie, wie Sie die Servicebus-Warteschlangen verwenden können. Die
Beispiele sind in C# geschrieben und verwenden die .NET-API. Die Szenarien
behandeln die Themen **Erstellen von Warteschlangen, Senden und Empfangen von
Nachrichten** und **Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte][Nächste Schritte]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <span class="short-header">Konfigurieren der Anwendung</span>Konfigurieren der Anwendung für die Verwendung von Service Bus

Wenn Sie eine Anwendung erstellen, die Service Bus nutzt, müssen Sie
einen Verweis auf die Service Bus-Assembly hinzufügen und
die entsprechenden Namespaces einfügen.

## <span class="short-header">Abrufen des NuGet-Pakets</span>Abrufen des Servicesbus-NuGet-Pakets

Das Service Bus **NuGet**-Paket ist die einfachste Möglichkeit
zum Abrufen der Service Bus-API und Konfigurieren der Anwendung mit
allen Service Bus-Abhängigkeiten. Die NuGet Visual Studio-Erweiterung
ermöglicht eine problemlose Installation und Aktualisierung von Bibliotheken und Tools in Visual Studio
und Visual Studio Express 2012 für das Web.

Gehen sie folgendermaßen vor, um das NuGet-Paket in der Anwendung zu installieren:

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf
    **Manage NuGet Packages**.
2.  Suchen Sie nach WindowsAzure", und wählen Sie das Element **Azure
    Service Bus** aus. Klicken Sie auf **Installieren**, um die Installation abzuschließen,
    und schließen Sie das Dialogfeld.

    ![][0]

Sie können nun Code für Service Bus erstellen.

## <span class="short-header">Einrichten der Verbindungszeichenfolge</span>Einrichten einer Servicebus-Verbindungszeichenfolge

Service Bus verwendet eine Verbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen. Sie können die Verbindungszeichenfolge in einer Konfigurationsdatei ablegen, statt sie fest in Code zu programmieren:

-   Bei der Verwendung von Azure Cloud Services empfiehlt es sich, die Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems (`*.csdef`- und `*.cscfg`-Dateien) zu speichern.
-   Bei der Verwendung von Azure-Websites oder Azure Virtual Machines sollten Sie Ihre Verbindungszeichenfolge mithilfe des .NET-Konfigurationssystems speichern (z. B. `web.config`-Datei).

In beiden Fällen können Sie Ihre Verbindungszeichenfolge über die `CloudConfigurationManager.GetSetting`-Methode abrufen, wie später in dieser Anleitung beschrieben.

### <a name="config-connstring"> </a>Konfigurieren der Verbindungszeichenfolge bei Verwendung von Cloud Services

Der Dienstkonfigurationsmechanismus ist spezifisch für Azure Cloud Services
-Projekte und ermöglicht es Ihnen, Konfigurationseinstellungen im
Azure-Verwaltungsportal dynamisch zu ändern, ohne die Anwendung
erneut bereitzustellen. Fügen Sie beispielsweise der Dienstdefinitionsdatei (`*.csdef`) eine Einstellung hinzu, wie nachfolgend gezeigt:

    <ServiceDefinition name="WindowsAzure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Anschließend geben Sie Werte in der Dienstkonfigurationsdatei (`*.cscfg`) an:

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

Verwenden Sie die aus dem Verwaltungsportal abgerufenen Aussteller- und
Schlüsselwerte. Das Verfahren wurde im vorherigen Abschnitt beschrieben.

### Konfigurieren der Verbindungszeichenfolge bei Verwendung von Websites oder virtuellen Computern

Bei der Verwendung von Websites oder virtuellen Computern sollten Sie das .NET-Konfigurationssystem verwenden (`web.config`). Sie speichern die Verbindungszeichenfolge mit dem `<appSettings>`-Element:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
        </appSettings>
    </configuration>

Verwenden Sie die aus dem Verwaltungsportal abgerufenen Aussteller- und
Schlüsselwerte. Das Verfahren wurde im vorherigen Abschnitt beschrieben.

## <span class="short-header">Erstellen einer Warteschlange</span>Erstellen einer Warteschlange

Sie können Verwaltungsvorgänge für Servicebus-Warteschlangen über die Klasse **NamespaceManager** durchführen. Die **NamespaceManager**-Klasse stellt Methoden zum Erstellen, Aufzählen und Löschen von Warteschlangen zur Verfügung.

In diesem Beispiel wird ein **NamespaceManager**-Objekt mit der Azure-Klasse **CloudConfigurationManager** mit einer
Verbindungszeichenfolge erstellt, die aus der Basisadresse eines Service Bus-Dienstnamespace und den entsprechenden
Anmeldeinformationen mit Berechtigungen für dessen Verwaltung besteht. Diese Verbindungszeichenfolge weist die folgende Form auf:

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

Es gibt Überladungen der **CreateQueue**-Methode, die es Ihnen ermöglichen, die
Eigenschaften der Warteschlange zu optimieren, um beispielsweise den Standardwert für die Lebensdauer festzulegen, der auf an die Warteschlange gesendete Nachrichten angewendet wird. Diese Einstellungen werden
mithilfe der **QueueDescription**-Klasse angewendet. Das folgende Beispiel zeigt, wie
eine Warteschlange namens "TestQueue" mit einer Maximalgröße von 5GB
und einer Standard-Nachrichtenlebensdauer von 1 Minute erstellt wird.

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

**Hinweis:** Sie können die **QueueExists**-Methode in **NamespaceManager**
-Objekten verwenden, um zu prüfen, ob eine Warteschlange mit einem
bestimmten Namen bereits innerhalb eines Dienstnamespace vorhanden ist.

## <span class="short-header">Senden von Nachrichten an eine Warteschlange</span>Senden von Nachrichten an eine Warteschlange

Um eine Nachricht an eine Servicebus-Warteschlange zu senden, erstellt die Anwendung mit der Verbindungszeichenfolge ein
**QueueClient**-Objekt.

Der folgende Code zeigt, wie ein **QueueClient**-Objekt für die oben
erstellte Warteschlange "TestQueue" mithilfe des API-Aufrufs **CreateFromConnectionString** erstellt wird:

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client = 
        QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    Client.Send(new BrokeredMessage());

Die Nachrichten, die an die Service Bus-Warteschlangen gesendet (und von
diesen empfangen) werden, sind Instanzen der Klasse **BrokeredMessage**. **BrokeredMessage**-Objekte weisen eine
Reihe von Standardeigenschaften (wie etwa **Label** und **TimeToLive**), ein Wörterbuch,
das zur Aufnahme benutzerdefinierter
anwendungsspezifischer Eigenschaften verwendet wird, und einen Bestand beliebiger Anwendungsdaten auf. Eine Anwendung kann den Textkörper der
Nachricht festlegen, indem ein beliebiges serialisierbares Objekt
an den Konstruktor von **BrokeredMessage** übergeben wird. Das entsprechende **DataContractSerializer**-Objekt wird dann zum Serialisieren des
Objekts verwendet. Alternativ kann ein
**System.IO.Stream**-Objekt zur Verfügung gestellt werden.

Im folgenden Beispiel wird dargelegt, wie fünf Testnachrichten an den
**QueueClient** "TestQueue" gesendet werden, der über den oben angegebene Codeausschnitt abgerufen wurde:

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

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB (der Header,
der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält,
kann eine maximale Größe von 64 KB haben). Bei der Anzahl der Nachrichten, die in einer
Warteschlange aufgenommen werden können, besteht keine Beschränkung.
Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung
definiert. Die Obergrenze beträgt 5 GB.

## <span class="short-header">Nachrichten von einer Warteschlange empfangen</span>Nachrichten von einer Warteschlange empfangen

Durch die Verwendung eines
**QueueClient**-Objekts lassen sich Nachrichten von einer Warteschlange am einfachsten empfangen. Diese Objekte können in zwei
verschiedenen Modi verwendet werden: **ReceiveAndDelete** und **PeekLock**.

Bei Verwendung des Modus **ReceiveAndDelete** ist der Nachrichtenempfang
ein Single-Shot-Vorgang. Wenn Service Bus also eine
Leseanforderung für eine Nachricht in einer Warteschlange erhält, wird die
Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurückgesendet. Der Modus **ReceiveAndDelete** ist das einfachste
Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine
Anwendung tolerieren kann, dass eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich
ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und
dann abstürzt, bevor diese verarbeitet wird. Da die Nachricht von Service Bus als
verarbeitet gekennzeichnet wurde, wird die vor dem Absturz
verarbeitete Nachricht nicht berücksichtigt, wenn die Anwendung neu gestartet
und die Verarbeitung von Nachrichten wieder aufgenommen wird.

Im Modus **PeekLock** (dem Standardmodus) ist der Empfangsprozess ein aus zwei Phasen bestehender Vorgang, der die Unterstützung von Anwendungen
ermöglicht, die fehlende Nachrichten nicht tolerieren können. Wenn Service Bus eine Anforderung erhält,
ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt
diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem
die Anwendung die Verarbeitung der Nachricht abgeschlossen hat
(oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert
hat), führt sie die zweite Phase des Empfangsprozesses durch Aufruf von **Complete** für die empfangene Nachricht durch. Wenn Service Bus
den **Complete**-Aufruf erkennt, kennzeichnet der Dienst die
Nachricht als verarbeitet und entfernt sie aus der Warteschlange.

Das folgende Beispiel zeigt, wie Nachrichten mit dem Standardmodus **PeekLock** empfangen
und verarbeitet werden können. Zum Angeben eines anderen **ReceiveMode**-Werts können Sie eine andere Überladung für **CreateFromConnectionString** verwenden. Im vorliegenden Beispiel wird eine Endlosschleife erstellt, und die Nachrichten werden entsprechend ihres Eingangs in die "TestQueue" verarbeitet:

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

## <span class="short-header">Anwendungsabstürze und nicht lesbare Nachrichten</span>Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen
Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn
eine empfangende Anwendung eine Nachricht aus einem beliebigen
Grund nicht verarbeiten kann, kann sie die Methode **Abandon** für die empfangene
Nachricht aufrufen (anstelle der Methode **Complete**). Dies führt dazu, dass Service Bus die
Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht,
damit sie erneut empfangen werden kann, und zwar entweder durch
dieselbe verarbeitende Anwendung oder eine andere.

Zudem wird einer in der Warteschlange gesperrten Anwendung ein Zeitlimit
zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des
Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung
abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar,
um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht aber vor
Ausgabe der Anforderung **Complete** abstürzt, wird die
Nachricht erneut an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig
 als **At Least Once Processing** (Verarbeitung mindestens einmal)
bezeichnet und bedeutet, dass jede Nachricht mindestens
einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung in dem Szenario
nicht zulässig ist, sollten Anwendungsentwickler ihrer Anwendung
zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies wird häufig durch
Verwendung der Eigenschaft **MessageId** der Nachricht erzielt, die über
mehrere Zustellungsversuche hinweg konstant bleibt.

## <span class="short-header">Nächste Schritte</span>Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Service Bus-Warteschlangen
vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements.][Warteschlangen, Themen und Abonnements.]
-   Erstellen einer funktionierenden Anwendung, die Nachrichten an eine Service Bus-Warteschlange sendet und
    aus einer Service Bus-Warteschlange empfängt: [.NET-Lernprogramm zu Service
    Bus-Brokermessaging][.NET-Lernprogramm zu Service
    Bus-Brokermessaging].

  [Nächste Schritte]: #next-steps
  [create-account-note]: ../includes/create-account-note.md
  [howto-service-bus-queues]: ../includes/howto-service-bus-queues.md
  [0]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Warteschlangen, Themen und Abonnements.]: http://msdn.microsoft.com/de-de/library/windowsazure/hh367516.aspx
