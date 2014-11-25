<properties linkid="dev-net-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (.NET) - Azure" metaKeywords="Get started Azure Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions C# " description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for .NET applications. " metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Topics/Subscriptions" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# Verwenden von Servicebus-Themen und -Abonnements

<span>In diesem Leitfaden erfahren Sie, wie Sie Service Bus-Themen und
-Abonnements verwenden. Die Beispiele sind in C# geschrieben und verwenden die .NET API. Die
behandelten Szenarien umfassen das **Erstellen von Themen und Abonnements, das
Erstellen von Abonnementfiltern, das Senden von
Nachrichten** an ein Thema, das **Empfangen von Nachrichten von einem Abonnement** und das **Löschen von Themen
und Abonnements**. Weitere Informationen zu Themen und Abonnements
finden Sie im Abschnitt [Nächste Schritte][Nächste Schritte]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <span class="short-header">Konfigurieren der Anwendung</span>Konfigurieren der Anwendung für die Verwendung von Service Bus

Wenn Sie eine Anwendung erstellen, die Service Bus nutzt, müssen Sie
einen Verweis auf die Service Bus-Assembly hinzufügen und
die entsprechenden Namespaces einfügen.

## <span class="short-header">Abrufen des NuGet-Pakets</span>Abrufen des Servicesbus-NuGet-Pakets

Das Service Bus **NuGet**-Paket ist die einfachste Möglichkeit
zum Abrufen der Service Bus-API und Konfigurieren der Anwendung mit
allen Service Bus-Abhängigkeiten. Die NuGet Visual Studio-Erweiterung
ermöglicht eine problemlose Installation und Aktualisierung von Bibliotheken und Tools in Visual Studio
und Visual Studio Express 2012 für das Web. Das Service Bus NuGet-Paket ist die einfachste Möglichkeit
zum Abrufen der Service Bus-API und Konfigurieren der Anwendung
mit allen Service Bus-Abhängigkeiten.

Gehen sie folgendermaßen vor, um das NuGet-Paket in der Anwendung zu installieren:

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf
    **Manage NuGet Packages**.
2.  Suchen Sie nach WindowsAzure", und wählen Sie das Element **Azure
    Service Bus** aus. Klicken Sie auf **Installieren**, um die Installation abzuschließen,
    und schließen Sie das Dialogfeld.

    ![][0]

Sie können nun Code für Service Bus erstellen.

## <span class="short-header">Einrichten der Verbindungszeichenfolge</span>Einrichten einer Service Bus-Verbindungszeichenfolge

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

## <span class="short-header">Erstellen eines Themas</span>Erstellen eines Themas

Verwaltungsvorgänge für Service Bus-Themen und -Abonnements können über die **NamespaceManager**-Klasse durchgeführt werden. Die **NamespaceManager**-Klasse stellt Methoden zum Erstellen, Aufzählen und Löschen von Warteschlangen zur Verfügung.

In diesem Beispiel wird ein **NamespaceManager**-Objekt mit der Azure-Klasse **CloudConfigurationManager** mit einer
Verbindungszeichenfolge erstellt, die aus der Basisadresse eines Service Bus-Dienstnamespace und den entsprechenden
Anmeldeinformationen mit Berechtigungen für dessen Verwaltung besteht. Diese Verbindungszeichenfolge weist die folgende Form auf:

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedSecretIssuer=<issuerName>;SharedSecretValue=<yourDefaultKey>

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

Es gibt Überladungen der **CreateTopic**-Methode, die es Ihnen ermöglichen, die
Eigenschaften des Themas zu optimieren, um beispielsweise den
Standardwert für die Lebensdauer festzulegen, der auf an das Thema gesendete Nachrichten angewendet wird. Diese Einstellungen werden
mithilfe der **TopicDescription**-Klasse angewendet. Das folgende Beispiel zeigt, wie
ein Thema namens "TestTopic" mit einer Maximalgröße von 5 GB
und einer Standard-Nachrichtenlebensdauer von 1 Minute erstellt wird.

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

**Hinweis:** Sie können die **TopicExists**-Methode in **NamespaceManager**
-Objekten verwenden, um zu prüfen, ob ein Thema mit einem
bestimmten Namen bereits innerhalb eines Dienstnamespace vorhanden ist.

## <span class="short-header">Erstellen von Abonnements</span>Erstellen von Abonnements

Sie können auch Themenabonnements mit der **NamespaceManager**
-Klasse erstellen. Abonnements werden benannt und können einen optionalen
Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle
Warteschlange des Abonnements übergeben werden.

### Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

**MatchAll** ist der Standardfilter, der verwendet wird, wenn
beim Erstellen eines neuen Abonnements kein Filter angegeben wird. Bei Verwendung des Filters
**MatchAll** werden alle für das Thema veröffentlichten
Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Im folgenden Beispiel wird
ein Abonnement namens "AllMessages" erstellt und der Standardfilter
**MatchAll** verwendet.

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### Erstellen von Abonnements mit Filtern

Sie können auch Filter einrichten, durch die Sie angeben können, welche
an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist
**SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden
auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere
Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet
werden können, finden Sie in der Syntax [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Mit dem folgenden Beispiel wird ein Abonnement namens "HighMessages" mit einem
**SqlFilter**-Filter erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte
**Messagenumber**-Eigenschaft größer ist als 3:

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
        new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "HighMessages", 
        highMessagesFilter);

Ebenso erstellt das folgende Beispiel ein Abonnement namens
"LowMessages" mit einem **SqlFilter**-, der nur Nachrichten auswählt,
deren Eigenschaft **MessageNumber** kleiner oder gleich 3 ist:

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
        new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "LowMessages", 
        lowMessagesFilter);

Wenn eine Nachricht an "TestTopic" gesendet wird, wird diese
nun stets an die Empfänger des Themenabonnements
"AllMessages" zugestellt. Sie wird selektiv
an die Empfänger der Themenabonnements "HighMessages" und "LowMessages" zugestellt (je nach Inhalt der
Nachricht).

## <span class="short-header">Senden von Nachrichten an ein Thema</span>Senden von Nachrichten an ein Thema

Um eine Nachricht an ein Service Bus-Thema zu senden, erstellt die Anwendung ein
**TopicClient**-Objekt mithilfe der Verbindungzeichenfolge.

Der folgende Code zeigt, wie ein **TopicClient**-Objekt für das oben
erstellte Thema "TestTopic" mithilfe des API-Aufrufs **CreateFromConnectionString** erstellt wird:

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    TopicClient Client = 
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

    Client.Send(new BrokeredMessage());

An Service Bus-Themen gesendete Nachrichten sind Instanzen der
**BrokeredMessage**-Klasse. **BrokeredMessage**-Objekte weisen eine
Reihe von Standardeigenschaften (wie etwa **Label** und **TimeToLive**), ein Wörterbuch,
das zur Aufnahme benutzerdefinierter
anwendungsspezifischer Eigenschaften verwendet wird, und einen Bestand beliebiger Anwendungsdaten auf. Eine Anwendung kann den Textkörper der
Nachricht festlegen, indem ein beliebiges serialisierbares Objekt
an den Konstruktor von **BrokeredMessage** übergeben wird. Das entsprechende **DataContractSerializer**-Objekt wird dann zum Serialisieren des
Objekts verwendet. Alternativ kann ein
**System.IO.Stream**-Objekt zur Verfügung gestellt werden.

Im folgenden Beispiel wird dargelegt, wie fünf Testnachrichten an
den **TopicClient** "TestTopic" gesendet werden, der über den oben angegebene Codeausschnitt erhalten wurde.
Beachten Sie, wie der **MessageNumber**-Eigenschaftswert jeder Nachricht gemäß der
 Iteration der Schleife variiert (dadurch wird bestimmt, welches Abonnement
die Nachricht erhält):

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

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 KB (der Header,
der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält,
kann eine maximale Größe von 64 KB haben). Es gibt keine Beschränkung für die Anzahl der
Nachrichten, die ein Thema enthält. Es gibt jedoch eine Obergrenze
für die Gesamtgröße der Nachrichten eines Themas. Die Warteschlangengröße wird bei der Erstellung
definiert. Die Obergrenze beträgt 5 GB.

## <span class="short-header">Empfangen von Nachrichten aus einem Abonnement</span>Empfangen von Nachrichten aus einem Abonnement

Die einfachste Möglichkeit, Nachrichten aus einem Abonnement zu empfangen, besteht in der Verwendung eines
**SubscriptionClient**-Objekts. **SubscriptionClient**-Objekte können in zwei
verschiedenen Modi verwendet werden: **ReceiveAndDelete** und **PeekLock**.

Bei Verwendung des Modus **ReceiveAndDelete** ist der Nachrichtenempfang
ein Single-Shot-Vorgang. Wenn Service Bus also eine
Leseanforderung für eine Nachricht in einem Abonnement erhält, wird die
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
Nachricht als verarbeitet und entfernt sie aus dem Abonnement.

Das folgende Beispiel zeigt, wie Nachrichten mit dem Standardmodus **PeekLock** empfangen
und verarbeitet werden können. Zum Angeben eines anderen **ReceiveMode**-Werts können Sie eine andere Überladung für **CreateFromConnectionString** verwenden. Mit diesem Beispiel wird eine Endlosschleife erstellt, und Nachrichten werden verarbeitet, sobald sie im Abonnement "HighMessages" eintreffen. Beachten Sie, dass der Pfad des Abonnements
 "HighMessages" in der folgenden Form angegeben
wird: "\<*Themenpfad*\>/subscriptions/\<*Abonnementname*\>".

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client = 
        SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

    Client.Receive();
     
    // Continuously process messages received from the HighMessages subscription 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();

       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("MessageNumber: " + 
                message.Properties["MessageNumber"]);

             // Remove message from subscription
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in subscription
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
Nachricht innerhalb des Abonnements entsperrt und verfügbar macht,
damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe
verarbeitende Anwendung oder eine andere.

Zudem wird einer im Abonnement gesperrten Anwendung ein Zeitlimit
zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des
Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

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

## <span class="short-header">Löschen von Themen und Abonnements</span>Löschen von Themen und Abonnements

Das folgende Beispiel zeigt, wie das Thema namens
**TestTopic** aus dem
**HowToSample**-Dienstnamespace gelöscht wird:

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

Durch das Löschen eines Themas werden auch alle Abonnements
gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden. Der
folgende Code zeigt, wie ein Abonnement namens
**HighMessages** aus dem Thema **TestTopic** gelöscht wird:

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## <span class="short-header">Nächste Schritte</span>Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Service Bus-Themen und -Abonnements vertraut
sind, finden Sie unter diesen Links weitere Informationen:

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements][Warteschlangen, Themen und Abonnements].
-   API-Referenz für [SqlFilter][SqlFilter].
-   Erstellen einer funktionierenden Anwendung, die Nachrichten an eine Service Bus-Warteschlange sendet und
    aus einer Service Bus-Warteschlange empfängt: [.NET-Lernprogramm zu Service
    Bus-Brokermessaging][.NET-Lernprogramm zu Service
    Bus-Brokermessaging].

  [Nächste Schritte]: #nextsteps
  [0]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  [Warteschlangen, Themen und Abonnements]: http://msdn.microsoft.com/de-de/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/de-de/library/microsoft.servicebus.messaging.sqlfilter.aspx
