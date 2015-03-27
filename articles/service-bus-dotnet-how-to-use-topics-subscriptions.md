<properties 
    pageTitle="Verwenden von Service Bus-Themen (.NET) - Azure" 
    description="Erfahren Sie mehr zur Verwendung von Service Bus-Themen und -Abonnements in Azure. Die Codebeispiele wurden für .NET-Anwendungen geschrieben." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor="mattshel"/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="02/26/2015" 
    ms.author="sethm"/>





# Verwenden von Servicebus-Themen und -Abonnements

In diesem Leitfaden erfahren Sie, wie Sie Service Bus-Themen und -Abonnements verwenden. Die Beispiele sind in C\# geschrieben und verwenden die .NET API. Die behandelten Szenarien umfassen das **Erstellen von Themen und Abonnements, Erstellen von Abonnementfiltern, Senden von Nachrichten** an ein Thema, das **Empfangen von Nachrichten von einem Abonnement** und das **Löschen von Themen und Abonnements**. Weitere Informationen zu Themen und Abonnements finden Sie im Abschnitt [Nächste Schritte][].

[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus

Wenn Sie eine Anwendung erstellen, die Service Bus nutzt, müssen Sie einen Verweis auf die Service Bus-Assembly hinzufügen und die entsprechenden Namespaces einfügen.

## Abrufen des NuGet-Pakets "Service Bus"

Das Service Bus **NuGet**-Paket ist die einfachste Methode zum Abrufen der
Service Bus-API und Konfigurieren der Anwendung mit allen
Service Bus-Abhängigkeiten. Die NuGet Visual Studio-Erweiterung ermöglicht eine problemlose Installation und Aktualisierung von Bibliotheken und Tools in Visual Studio und
Visual Studio Express. Das NuGet-Paket "Service Bus" stellt die einfachste Möglichkeit zum Abrufen der Service Bus-API und zum Konfigurieren der Anwendung mit allen Service Bus-Abhängigkeiten dar.

Gehen sie folgendermaßen vor, um das NuGet-Paket in der Anwendung zu installieren:

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf
    **NuGet-Pakete verwalten**.
2.  Suchen Sie nach "Service Bus", und wählen Sie das **Microsoft Azure
    Service Bus**-Element. Klicken Sie auf **Installieren**, um die Installation abzuschließen.
    Schließen Sie dann das Dialogfeld.

    ![][7]

Sie können nun Code für Service Bus erstellen.

## Einrichten einer Service Bus-Verbindungszeichenfolge

Service Bus verwendet eine Verbindungszeichenfolge zum Speichern von Endpunkten und Anmeldeinformationen. Sie können die Verbindungszeichenfolge in einer Konfigurationsdatei ablegen, statt sie fest in Code zu programmieren:

- Bei der Verwendung von Azure Cloud Services empfiehlt es sich, die Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems (`*.csdef`- und `*.cscfg`-Dateien) zu speichern.
- Bei der Verwendung von Azure-Websites oder Azure Virtual Machines sollten Sie Ihre Verbindungszeichenfolge mithilfe des .NET-Konfigurationssystems speichern (z. B.  `web.config`-Datei).

In beiden Fällen können Sie Ihre Verbindungszeichenfolge über die  `CloudConfigurationManager.GetSetting`-Methode abrufen, wie später in dieser Anleitung beschrieben.

### Konfigurieren der Verbindungszeichenfolge bei Verwendung von Cloud Services

Azure-Cloud-Dienst-Projekte bieten einen einzigartigen Dienstkonfigurationsmechanismus, mit dem Sie Ihre Konfigurationseinstellungen im Azure-Verwaltungsportal dynamisch ändern können, ohne Ihre Anwendung neu bereitstellen zu müssen.  Fügen Sie beispielsweise der Dienstdefinitionsdatei (`*.csdef`) eine Einstellung hinzu, wie nachfolgend gezeigt:

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Anschließend geben Sie Werte in der Dienstkonfigurationsdatei (`*.cscfg`) an:

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

Verwenden Sie die aus dem Verwaltungsportal abgerufenen Schlüsselwerte. Das Verfahren wurde im vorherigen Abschnitt beschrieben.

### Konfigurieren der Verbindungszeichenfolge bei Verwendung von Websites oder virtuellen Computern

Bei der Verwendung von Websites oder virtuellen Computern sollten Sie das .NET-Konfigurationssystem verwenden ( `web.config`).  Sie speichern die Verbindungszeichenfolge mit dem `<appSettings>`-Element:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

Verwenden Sie die aus dem Verwaltungsportal abgerufenen Aussteller- und Schlüsselwerte. Das Verfahren wurde im vorherigen Abschnitt beschrieben.

## Erstellen eines Themas

Verwaltungsvorgänge für Service Bus-Themen und -Abonnements können über die **NamespaceManager**-Klasse durchgeführt werden. Die Klasse **NamespaceManager** stellt Methoden zum Erstellen, Aufzählen und Löschen von Warteschlangen zur Verfügung. 

In diesem Beispiel wird ein **NamespaceManager**-Objekt mit der Azure-Klasse **CloudConfigurationManager** mit einer Verbindungszeichenfolge erstellt, die aus der Basisadresse eines Service Bus-Dienstnamespace und den entsprechenden Anmeldeinformationen mit Berechtigungen für dessen Verwaltung besteht. Diese Verbindungszeichenfolge weist die folgende Form auf:

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey

Beispiel mit den Konfigurationseinstellungen aus dem vorherigen Abschnitt:

    // Create the topic if it does not exist already
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic("TestTopic");
    }

Es gibt Überladungen der **CreateTopic**-Methode, die es Ihnen ermöglichen, die Eigenschaften des Themas zu optimieren, um beispielsweise den Standardwert für die Lebensdauer festzulegen, der auf an das Thema gesendete Nachrichten angewendet wird. Diese Einstellungen werden mithilfe der **TopicDescription**-Klasse angewendet. Das folgende Beispiel zeigt, wie ein Thema namens "TestTopic" mit einer Maximalgröße von 5 GB und einer Standard-Nachrichtenlebensdauer von 1 Minute erstellt wird.

    // Configure Topic Settings
    TopicDescription td = new TopicDescription("TestTopic");
    td.MaxSizeInMegabytes = 5120;
    td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new Topic with custom settings
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic(td);
    }

**Hinweis:** Sie können die **TopicExists**-Methode in **NamespaceManager**-Objekten verwenden, um zu prüfen, ob ein Thema mit einem bestimmten Namen bereits innerhalb eines Dienstnamespace vorhanden ist.

<h2>Erstellen von Abonnements</h2>

Sie können auch Themenabonnements mit der **NamespaceManager**-Klasse erstellen. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übergeben werden.

### Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

**MatchAll** ist der Standardfilter, der verwendet wird, wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird. Bei Verwendung des Filters **MatchAll** werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel wird ein Abonnement namens "AllMessages" erstellt, für das der Standardfilter **MatchAll** verwendet wird.

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### Erstellen von Abonnements mit Filtern

Sie können auch Filter einrichten, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist
**SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können, finden Sie in der Syntax [SqlFilter.SqlExpression][].

Mit dem folgenden Beispiel wird ein Abonnement namens "high-messages" mit einem
**SqlFilter**, der nur Nachrichten auswählt, die über eine benutzerdefinierte
**MessageNumber**-Eigenschaft größer als 3 verfügen:

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
        new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "HighMessages", 
        highMessagesFilter);

Ebenso erstellt das folgende Beispiel ein Abonnement namens
"LowMessages" mit einem **SqlFilter**, der nur Nachrichten auswählt, deren **MessageNumber**-Eigenschaft kleiner oder gleich 3 ist:

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
        new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "LowMessages", 
        lowMessagesFilter);

Wenn eine Nachricht nun an "TestTopic" gesendet wird, wird sie immer an die Empfänger gesendet, die das "AllMessages"-Themenabonnement abonniert haben, und selektiv an die Empfänger, die die
Themenabonnements "HighMessages" und "LowMessages" abonniert haben (abhängig vom Nachrichteninhalt).

## Senden von Nachrichten an ein Thema

Um eine Nachricht an ein Service Bus-Thema zu senden, erstellt Ihre Anwendung ein
**TopicClient**-Objekt mithilfe der Verbindungszeichenfolge.

Der folgende Code zeigt, wie ein **TopicClient**-Objekt für das oben erstellte Thema "TestTopic" mithilfe des API-Aufrufs **CreateFromConnectionString** erstellt wird:

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    TopicClient Client = 
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

    Client.Send(new BrokeredMessage());
 

An Service Bus-Themen gesendete Nachrichten sind Instanzen der
**BrokeredMessage**-Klasse. **BrokeredMessage**-Objekte verfügen über einen Satz von Standardeigenschaften (z. B. **Label** und **TimeToLive**), ein Wörterbuch, in dem benutzerdefinierte anwendungsspezifische Eigenschaften enthalten sind, sowie einen Bestand beliebiger Anwendungsdaten. Eine Anwendung kann den Nachrichtentext festlegen, indem sie ein beliebiges serialisierbares Objekt an den Konstruktor von
**BrokeredMessage** übergibt. Anschließend wird das Objekt mit dem geeigneten **DataContractSerializer** serialisiert. Alternativ kann ein
**System.IO.Stream** bereitgestellt werden.

Das folgende Beispiel zeigt, wie Sie fünf Testnachrichten senden an
"TestTopic" **TopicClient**, den Sie im vorherigen Codeabschnitt erhalten haben:
Beachten Sie, wie der **MessageNumber**-Eigenschaftswert jeder Nachricht gemäß der Iteration der Schleife variiert (dadurch wird bestimmt, welche Abonnements die Nachricht erhalten):

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = 
        new BrokeredMessage("Test message " + i);

       // Set additional custom app-specific property
       message.Properties["MessageNumber"] = i;

       // Send message to the topic
       Client.Send(message);
     }

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 KB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben). Es gibt keine Beschränkung für die Anzahl der Nachrichten, die ein Thema enthält. Es gibt jedoch eine Obergrenze für die Gesamtgröße der Nachrichten eines Themas. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## Empfangen von Nachrichten aus einem Abonnement

Der empfohlene Weg zum Empfangen von Nachrichten aus Abonnements ist die Verwendung eines
**SubscriptionClient**-Objekts. **SubscriptionClient**-Objekte können in zwei verschiedenen Modi verwendet werden: **ReceiveAndDelete** und **PeekLock**.

Bei Verwendung des **ReceiveAndDelete**-Modus ist der Nachrichtenempfang ein Single-Shot-Vorgang. Dies bedeutet, wenn Service Bus eine Leseanforderung für eine Nachricht in einem Abonnement erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurück gesendet. **Der ReceiveAndDelete**-Modus ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da die Nachricht von Service Bus als verarbeitet gekennzeichnet wurde, wird die vor dem Absturz verarbeitete Nachricht nicht berücksichtigt, wenn die Anwendung neu gestartet und die Verarbeitung von Nachrichten wieder aufgenommen wird.

Im Modus **PeekLock** (dem Standardmodus) ist der Empfangsprozess ein aus zwei Phasen bestehender Vorgang, der die Unterstützung von Anwendungen ermöglicht, die fehlende Nachrichten nicht tolerieren können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf von **Complete** für die empfangene Nachricht durch. Wenn Service
Bus den **Complete**-Aufruf erkennt, kennzeichnet der Dienst die Nachricht als verarbeitet und entfernt sie aus dem Abonnement.

Das folgende Beispiel zeigt, wie Nachrichten mit dem 
Standardmodus **PeekLock** empfangen und verarbeitet werden können. Zum Angeben eines anderen **ReceiveMode**-Werts 
können Sie eine andere Überladung für **CreateFromConnectionString** verwenden. Dies 
Beispiel verwendet den **OnMessage**-Rückruf zum Verarbeiten von Nachrichten, während diese 
im Abonnement "HighMessages" eintreffen.

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client = 
        SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

    // Configure the callback options
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    subscriptionClientHigh.OnMessage((message) =>
    {
        try
        {
            // Process message from subscription
            Console.WriteLine("\n**High Messages**");
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Message Number: " +
                message.Properties["MessageNumber"]);

            // Remove message from subscription
            message.Complete();
        }
        catch (Exception)
        {
            // Indicates a problem, unlock message in subscription
            message.Abandon();
        }
    }, options);

Das folgende Beispiel konfiguriert den **OnMessage**-Rückruf mithilfe eines 
**OnMessageOptions**-Objekts. **AutoComplete** ist auf **false** einstellt, um die  manuelle Steuerung für den Aufruf von **Complete** für die empfangene Nachricht zu aktivieren.
**AutoRenewTimeout** ist auf eine Minute eingestellt, wodurch der Client bis zu einer Minute auf eine Nachricht wartet, bis der Aufruf abläuft und der Client einen neuen Aufruf startet, um auf Nachrichten zu prüfen. Dadurch wird die Häufigkeit verringert, mit der der Client fakturierbare Aufrufe startet, die keine Nachrichten abrufen.

## Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine empfangene Anwendung eine Nachricht aus einem beliebigen Grund nicht verarbeiten kann, kann sie die **Abandon**-Methode für die empfangene Nachricht aufrufen (statt der **Complete**-Methode). Dies führt dazu, dass Service Bus die Nachricht innerhalb des Abonnements entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitenden Anwendung.

Zudem wird einer im Abonnement gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt) entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht aber vor Ausgabe der Anforderung **Complete** abstürzt, wird die Nachricht erneut an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (mindestens einmalige Verarbeitung) bezeichnet und bedeutet, dass jede Nachricht mindestens ein Mal verarbeitet wird, unter bestimmten Umständen jedoch ggf. auch erneut übermittelt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der Eigenschaft **MessageId** der Nachricht erreicht, die über mehrere Übermittlungsversuche hinweg konstant bleibt.

## Löschen von Themen und Abonnements

Das folgende Beispiel zeigt, wie das Thema namens **TestTopic** aus dem **HowToSample**-Dienstnamespace gelöscht wird:

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden. Der folgende Code veranschaulicht, wie ein Abonnement namens
**HighMessages** aus dem Thema **TestTopic** gelöscht wird:

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Service Bus-Themen und -Abonnements vertraut sind, finden Sie unter diesen Links weitere Informationen:

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements][].
-   API-Referenz zu [SqlFilter][].
-   Erstellen einer funktionierenden Anwendung, die Nachrichten an und aus einer Service Bus-Warteschlange sendet: [.NET-Lernprogramm zu Service Bus-Brokermessaging][].

  [Nächste Schritte]: #nextsteps
  [Was sind Service Bus-Themen und -Abonnements?]: #what-is
  [Erstellen eines Dienstnamespace]: #create-namespace
  [Abrufen der Standard-Anmeldeinformationen für den Namespace]: #obtain-creds
  [Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus]: #configure-app
  [Gewusst wie: Einrichten einer Service Bus-Verbindungszeichenfolge]: #set-up-connstring
  [Gewusst wie: Konfigurieren Ihrer Verbindungszeichenfolge]: #config-connstring
  [Gewusst wie: Erstellen eines Themas]: #create-topic
  [Gewusst wie: Erstellen von Abonnements]: #create-subscriptions
  [Gewusst wie: Senden von Nachrichten an ein Thema]: #send-messages
  [Gewusst wie: Empfangen von Nachrichten aus einem Abonnement]: #receive-messages
  [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #handle-crashes
  [Gewusst wie: Löschen von Themen und Abonnements]: #delete-topics
  
  [Themakonzepte]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/sb-topics-01.png
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  
  [Warteschlangen, Themen und Abonnements]: http://msdn.microsoft.com/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/microsoft.servicebus.messaging.sqlfilter.aspx
  [.NET-Lernprogramm zu Service Bus-Brokermessaging]: http://msdn.microsoft.com/library/hh367512.aspx

<!--HONumber=47-->
