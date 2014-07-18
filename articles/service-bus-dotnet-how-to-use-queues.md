<properties linkid="dev-net-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (.NET) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues C#, Azure queues .NET" description="Learn how to use Service Bus queues in Azure. Code samples written in C# using the .NET API." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Queues" authors="sethm" solutions="" manager="dwrede" editor="mattshel" />

Verwenden der Servicebus-Warteschlangen
=======================================

In diesem Leitfaden erfahren Sie, wie Sie die Servicebus-Warteschlangen verwenden können. Die Beispiele sind in C\# geschrieben und verwenden die .NET API. Die Szenarien behandeln die Themen **Erstellen von Warteschlangen, Senden und Empfangen von Nachrichten** und **Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

Konfigurieren der AnwendungKonfigurieren der Anwendung zum Verwenden des Servicebus
-----------------------------------------------------------------------------------

Wenn Sie eine Anwendung erstellen, die den Servicebus verwendet, müssen Sie zu der Servicebus-Assembly eine Referenz hinzufügen und die zugehörigen Namespaces einbinden.

Abrufen des NuGet-PaketsAbrufen des Servicebus-NuGet-Pakets
-----------------------------------------------------------

Das Servicebus-**NuGet**-Paket stellt die einfachste Möglichkeit zum Abrufen der Servicebus-API und zum Konfigurieren der Anwendung mit sämtlichen der Servicebus-Abhängigkeiten dar. Mit der NuGet Visual Studio-Erweiterung können Sie Bibliotheken und Tools in Visual Studio und Visual Studio Express 2012 auf einfache Weise für das Web installieren und aktualisieren. Das Servicebus-NuGet-Paket stellt die einfachste Möglichkeit zum Abrufen der Servicebus-API und zum Konfigurieren der Anwendung mit sämtlichen der Servicebus-Abhängigkeiten dar.

Um das NuGet-Paket in der Anwendung zu installieren, gehen Sie folgendermaßen vor:

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Referenzen**. Klicken Sie anschließend auf **Manage NuGet Packages**.
2.  Suchen Sie nach "WindowsAzure", und wählen Sie das Element **Azure-Servicebus**. Klicken Sie auf **Installieren**, um die Installation abzuschließen. Beenden Sie danach diesen Dialog.

    ![](./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png)

Sie können nun Code in Bezug auf den Servicebus schreiben.

Einrichten der VerbindungszeichenfolgeEinrichten einer Servicebus-Verbindungszeichenfolge
-----------------------------------------------------------------------------------------

Der Servicebus verwendet eine Verbindungszeichenfolge, um Endpunkte und Anmeldeinformationen zu speichern. Sie können die Verbindungszeichenfolge in einer Konfigurationsdatei ablegen, anstatt sie fest in Code zu programmieren:

-   Bei der Verwendung von Azure-Clouddiensten empfiehlt es sich, die Verbindungszeichenfolge mithilfe des Azure-Dienstkonfigurationssystems (Dateien `*.csdef` und `*.cscfg`) zu speichern.
-   Bei der Verwendung von Azure-Websites oder Azure Virtual Machines empfiehlt es sich, die Verbindungszeichenfolge mithilfe des .NET-Konfigurationssystems (z. B. Datei `web.config`) zu speichern.

In beiden Fällen können Sie die Verbindungszeichenfolge mit der `CloudConfigurationManager.GetSetting`-Methode abrufen, wie weiter unten in diesem Leitfaden gezeigt.

### Konfigurieren der Verbindungszeichenfolge bei der Verwendung von Cloud Services

Die Dienstkonfiguration ist in Azure-Clouddienst-Projekten eindeutig und ermöglicht es Ihnen, Konfigurationseinstellungen im Azure-Verwaltungsportal dynamisch zu ändern, ohne die Anwendung erneut bereitstellen zu müssen. Fügen Sie beispielsweise eine Einstellung zu Ihrer Dienstdefinitionsdatei (`*.csdef`) wie unten dargestellt hinzu:

    <ServiceDefinition name="WindowsAzure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>

&lt;/WebRole\>

    ...
    </ServiceDefinition>

Danach können Sie die Werte in der Dienstkonfigurationsdatei (`*.cscfg`) festlegen:

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

Verwenden Sie die aus dem Verwaltungsportal abgerufenen Aussteller- und Schlüsselwerte wie im vorigen Abschnitt beschrieben.

### Konfigurieren der Verbindungszeichenfolge bei der Verwendung von Websites oder Virtual Machines

Bei der Verwendung von Websites oder Virtual Machines empfiehlt es sich, das .NET-Konfigurationssystem (z. B. `web.config`) zu verwenden. Sie können die Verbindungszeichenfolge mit dem Element `<appSettings>` speichern:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
        </appSettings>
    </configuration>

Verwenden Sie die aus dem Verwaltungsportal abgerufenen Aussteller- und Schlüsselwerte wie im vorigen Abschnitt beschrieben.

Erstellen einer WarteschlangeErstellen einer Warteschlange
----------------------------------------------------------

Sie können Verwaltungsvorgänge für Servicebus-Warteschlangen über die Klasse **NamespaceManager** durchführen. Die Klasse **NamespaceManager** stellt Methoden zum Erstellen, Spezifizieren und Löschen von Warteschlangen bereit.

Im vorliegenden Beispiel wird ein **NamespaceManager**-Objekt unter Verwendung der Azure-Klasse **CloudConfigurationManager** mit einer Verbindungszeichenfolge aufgebaut, die aus der Basisadresse des Servicebus-Dienstnamespace und den entsprechenden Anmeldeinformationen besteht, für welche Genehmigungen zum Verwalten der Zeichenfolge vorliegen. Die Form der Verbindungszeichenfolge lautet wie folgt:

    Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]

Beispielsweise gelten die Konfigurationseinstellungen aus dem vorigen Abschnitt:

    // Create the queue if it does not exist already
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

Die Überlasten der **CreateQueue**-Methode ermöglichen es Ihnen, die Eigenschaften der Warteschlange abzustimmen (z. B., um den Standardwert "time-to-live" einzustellen, der auf an die Warteschlange gesendete Nachrichten angewendet wird). Diese Einstellungen werden angewendet, indem die Klasse **QueueDescription** verwendet wird. Im folgenden Beispiel wird dargelegt, wie eine Warteschlange mit der Bezeichnung "TestQueue" mit einer maximalen Größe von 5 GB und einer Standardnachricht "time-to-live" von 1 Minute erstellt wird:

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

Senden von Nachrichten an eine WarteschlangeSenden von Nachrichten an eine Warteschlange
----------------------------------------------------------------------------------------

Um eine Nachricht an eine Servicebus-Warteschlange zu senden, erstellt die Anwendung mit der Verbindungszeichenfolge ein **QueueClient**-Objekt.

Der unten angegebene Code zeigt auf, wie ein **QueueClient**-Objekt für die Warteschlange "TestQueue" zu erstellen ist, welche oben mit dem API-Aufruf **CreateFromConnectionString** erstellt wurde:

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client = 
        QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

    Client.Send(new BrokeredMessage());

Die Nachrichten, die an die Servicebus-Warteschlangen gesendet werden (und von diesen empfangen werden), sind Instanzen der Klasse **BrokeredMessage**. **BrokeredMessage**-Objekte weisen eine Reihe von Standardeigenschaften (wie etwa **Label** und **TimeToLive**), ein Wörterbuch, das zur Aufnahme benutzerdefinierter anwendungsspezifischer Eigenschaften verwendet wird, und einen Körper beliebiger Anwendungsdaten auf. Eine Anwendung kann den Körper der Nachricht einstellen, indem ein beliebiges serialisierbares Objekt in den Konstruktor der **BrokeredMessage** weitergeleitet wird. Anschließend wird der passende **DataContractSerializer** zur Serialisierung des Objekts verwendet. Alternativ kann ein **System.IO.Stream** bereitgestellt werden.

Im folgenden Beispiel wird dargelegt, wie fünf Testnachrichten an die "TestQueue" **QueueClient** gesendet werden, welche über das oben angegebene Code-Teilstück erhalten wurden:

     for (int i=0; i<5 data-morhtml="true"; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = new BrokeredMessage("Test message " + i);

       // Set some additional custom app-specific properties
       message.Properties["TestProperty"] = "TestValue";
       message.Properties["Message number"] = i;   

       // Send message to the queue
       Client.Send(message);
     }

Die Servicebus-Warteschlangen unterstützen Nachrichten mit einer maximalen Größe von 256 KB (die Kopfzeile, welche die Standard- und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB aufweisen). Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die entsprechende Warteschlangengröße wird zur Zeit der Erstellung mit einer Obergrenze von 5 GB festgelegt.

Nachrichten von einer Warteschlange empfangenNachrichten von einer Warteschlange empfangen
------------------------------------------------------------------------------------------

Durch die Verwendung eines **QueueClient**-Objekts lassen sich Nachrichten von einer Warteschlange am einfachsten empfangen. Die Objekte können in zwei unterschiedlichen Modi funktionieren: **ReceiveAndDelete** und **PeekLock**.

Bei der Verwendung des Modus **ReceiveAndDelete** stellt der Empfangsvorgang einen Single-Shot-Vorgang dar. Dies bedeutet, dass der Servicebus eine Nachricht als verwendet markiert, wenn er zuvor eine Leseanforderung für diese betreffende Nachricht in einer Warteschlange empfangen hat. Der Servicebus gibt die Nachricht anschließend an die Anwendung zurück. Der Modus **ReceiveAndDelete** stellt das einfachste Modell dar und funktioniert bei jenen Szenarien am besten, bei denen es eine Anwendung zulassen kann, dass eine Nachricht im Fall eines Fehlers nicht verarbeitet wird. Um dies zu verstehen, sei ein Szenario betrachtet, bei dem der Consumer die Empfangsanforderung ausstellt und dann vor deren Verarbeitung abstürzt. Da der Servicebus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Im Modus **PeekLock** (dies ist der Standardmodus) wird der Empfangsvorgang zu einem zweistufigen Vorgang. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn der Servicebus eine Anforderung empfängt, findet er die nächste zu verwendende Nachricht, sperrt diese, um einen Empfang durch andere Consumer zu verhindern, und gibt sie anschließend an die Anwendung zurück. Nachdem die Anwendung die Verarbeitung der Nachricht beendet hat (oder diese verlässlich für eine zukünftige Verarbeitung gespeichert hat), schließt sie die zweite Stufe des Empfangsvorgangs ab, indem sie für die empfangene Nachricht **Complete** aufruft. Wenn der Servicebus den **Complete**-Aufruf erkennt, markiert er die Nachricht als verwendet und entfernt sie aus der Warteschlange.

Das untere Beispiel zeigt, wie Nachrichten mit dem Standardmodus **PeekLock** empfangen und verarbeitet werden können. Um einen abweichenden **ReceiveMode**-Wert festzulegen, können Sie eine andere Überlast für **CreateFromConnectionString** verwenden. Im vorliegenden Beispiel wird eine Endlosschleife erstellt, und die Nachrichten werden entsprechend ihres Eingangs in die "TestQueue" verarbeitet:

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

Anwendungsabstürze und unlesbare NachrichtenBehandlung von Anwendungsabstürzen und unlesbaren Nachrichten
---------------------------------------------------------------------------------------------------------

Der Servicebus stellt Funktionen bereit, um möglichst schonend etwaige Fehler in der Anwendung beheben oder Schwierigkeiten beim Verarbeiten einer Nachricht ausräumen zu können. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die **Abandon**-Methode zu der empfangenen Nachricht aufrufen (anstelle der **Complete**-Methode). Dies bewirkt, dass der Servicebus die Nachricht innerhalb der Warteschlange entsperren und verfügbar machen kann, damit sie entweder von derselben verbrauchenden Anwendung oder von einer anderen verbrauchenden Anwendung erneut empfangen werden kann.

Mit einer innerhalb der Warteschlange gesperrten Nachricht ist außerdem eine Zeitlimitüberschreitung verknüpft. Wenn die Anwendung die Nachricht nicht verarbeiten kann, bevor die Zeitlimitüberschreitung der Sperre abläuft (z. B. falls die Anwendung abstürzt), so entsperrt der Servicebus die Nachricht automatisch und macht sie verfügbar, so dass sie wieder empfangen werden kann.

Falls die Anwendung nach dem Verarbeiten der Nachricht jedoch vor der Ausstellung der **Complete**-Anforderung abstürzt, wird die Nachricht erneut an die Anwendung geliefert, sobald diese neu gestartet wird. Das wird oftmals als **At Least Once Processing** bezeichnet. Dies bedeutet, dass jede Nachricht mindestens einmal verarbeitet und in bestimmten Situationen dieselbe Nachricht erneut geliefert wird. Wenn in dem Szenario keine doppelte Verarbeitung zugelassen ist, so sollten die Anwendungsentwickler eine zusätzliche Logik zu der Anwendung hinzufügen, damit eine doppelte Nachrichtenauslieferung bewältigt werden kann. Dies wird oftmals durch die Verwendung der **MessageId**-Eigenschaft der Nachricht erreicht, die während der Auslieferungsversuche konstant bleibt.

Nächste SchritteNächste Schritte
--------------------------------

Nachdem Sie nun mit den Grundlagen der Servicebus-Warteschlangen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements.](http://msdn.microsoft.com/en-us/library/windowsazure/hh367516.aspx)
-   Erstellen Sie eine Arbeitsanwendung, die Nachrichten an eine Servicebus-Warteschlange sendet und von dort empfängt: [Servicebus für Brokermessaging im .NET-Lernprogramm](http://msdn.microsoft.com/en-us/library/windowsazure/hh367512.aspx).

