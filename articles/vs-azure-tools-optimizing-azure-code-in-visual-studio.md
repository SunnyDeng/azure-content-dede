<properties
   pageTitle="Optimieren des Azure-Codes in Visual Studio | Microsoft Azure"
   description="Erfahren Sie, wie Sie Azure-Tools zur Codeoptimierung in Visual Studio nutzen können, um Ihren Code robuster und leistungsstärker zu machen."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="12/19/2015"
   ms.author="tarcher" />

# Optimieren des Azure-Codes

Wenn Sie Apps programmieren, für die Microsoft Azure verwendet wird, sollten Sie einige Hinweise zur Codeerstellung beachten. So vermeiden Sie Probleme mit der Skalierbarkeit, dem Verhalten und der Leistung von Apps in einer Cloudumgebung. Microsoft stellt ein Azure-Tool für die Codeanalyse bereit, mit dem mehrere dieser häufig auftretenden Probleme erkannt und behoben werden können. Sie können das Tool in Visual Studio über NuGet herunterladen.

## Regeln für die Azure-Codeanalyse

Im Azure-Tool für die Codeanalyse werden die folgenden Regeln verwendet, um den Azure-Code automatisch zu kennzeichnen, wenn bekannte Probleme erkannt werden, die sich auf die Leistung auswirken. Erkannte Probleme werden als Warnungen oder Compilerfehler angezeigt. Codefehlerbehebungen oder Vorschläge zur Lösung der Warnung oder des Fehlers werden häufig durch das Glühbirnen-Symbol angegeben.

## Vermeiden der Verwendung des standardmäßigen Sitzungszustandsmodus (In Bearbeitung)

### ID

AP0000

### Beschreibung

Wenn Sie den standardmäßigen Sitzungszustandsmodus (In Bearbeitung) für Cloudanwendungen verwenden, können Sie den Sitzungszustand verlieren.

Ihre Ideen und Feedback hierzu können Sie uns unter [Feedback zur Azure-Codeanalyse](http://go.microsoft.com/fwlink/?LinkId=403771) gern mitteilen.

### Grund

Standardmäßig lautet der in der Datei „web.config“ angegebene Sitzungszustandsmodus „In Bearbeitung“. Außerdem wird der Sitzungszustandsmodus automatisch auf „In Bearbeitung“ festgelegt, wenn in der Konfigurationsdatei kein Eintrag angegeben ist. Im Modus „In Bearbeitung“ werden Sitzungszustände im Arbeitsspeicher auf dem Webserver gespeichert. Wenn eine Instanz neu gestartet wird oder eine neue Instanz für den Lastenausgleich oder die Failoverunterstützung verwendet wird, wird der im Arbeitsspeicher auf dem Webserver befindliche Sitzungszustand nicht gespeichert. Diese Situation verhindert, dass die Anwendung in der Cloud skalierbar ist.

Der ASP.NET-Sitzungszustand unterstützt mehrere unterschiedliche Speicheroptionen für Sitzungszustandsdaten: „InProc“, „StateServer“, „SQLServer“, „Custom“ und „Off“. Es wird empfohlen, den benutzerdefinierten Modus (Custom) zum Hosten von Daten in einem externen Sitzungszustandsspeicher zu verwenden, z. B. [Azure-Sitzungszustandsanbieter für Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### Lösung

Eine empfohlene Lösung ist das Speichern des Sitzungszustands unter einem Managed Cache Service. Informieren Sie sich, wie Sie [Azure-Sitzungszustandsanbieter für Redis](http://go.microsoft.com/fwlink/?LinkId=401521) zum Speichern des Sitzungszustands verwenden. Sie können den Sitzungszustand auch an anderen Orten speichern, um sicherzustellen, dass die Anwendung in der Cloud skalierbar ist. Weitere Informationen zu alternativen Lösungen finden Sie unter [Sitzungszustandsmodi](https://msdn.microsoft.com/library/ms178586).

## Run-Methode sollte nicht asynchron sein

### ID

AP1000

### Beschreibung

Erstellen Sie asynchrone Methoden (z. B. [await](https://msdn.microsoft.com/library/hh156528.aspx)) außerhalb der [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)-Methode, und rufen Sie dann die asynchronen Methoden aus [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) auf. Wenn Sie die [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)-Methode als asynchron deklarieren, wird die Workerrolle in eine Neustartschleife versetzt.

Ihre Ideen und Feedback hierzu können Sie uns unter [Feedback zur Azure-Codeanalyse](http://go.microsoft.com/fwlink/?LinkId=403771) gern mitteilen.

### Grund

Wenn Sie asynchrone Methoden in der [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)-Methode aufrufen, recycelt die Clouddienst-Runtime die Workerrolle. Wenn eine Workerrolle gestartet wird, wird die gesamte Programmausführung innerhalb der [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)-Methode durchgeführt. Das Beenden der [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)-Methode bewirkt, dass die Workerrolle neu gestartet wird. Wenn die Workerrollen-Runtime auf die asynchrone Methode trifft, werden alle Vorgänge nach der asynchronen Methode abgearbeitet, und dann wird zum Anfang zurückgekehrt. Dies bewirkt, dass die Workerrolle für die [[[[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)-Methode beendet und neu gestartet wird. Beim nächsten Durchlauf der Ausführung trifft die Workerrolle erneut auf die asynchrone Methode und wird neu gestartet, sodass auch die Workerrolle erneut recycelt wird.

### Lösung

Ordnen Sie alle asynchronen Vorgänge außerhalb der [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)-Methode an. Rufen Sie anschließend die umgestaltete asynchrone Methode aus der [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)-Methode auf, z. B. „RunAsync().wait“. Das Azure-Tool für die Codeanalyse ist hilfreich, um dieses Problem zu beheben.

Der folgende Codeausschnitt veranschaulicht die Codefehlerbehebung für dieses Problem:

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## Verwenden der Authentifizierung „Service Bus Shared Access Signature“

### ID

AP2000

### Beschreibung

Verwenden Sie Shared Access Signature (SAS) für die Authentifizierung. Access Control Service (ACS) gilt für die Service Bus-Authentifizierung als veraltet.

Ihre Ideen und Feedback hierzu können Sie uns unter [Feedback zur Azure-Codeanalyse](http://go.microsoft.com/fwlink/?LinkId=403771) gern mitteilen.

### Grund

Aus Sicherheitsgründen wird die ACS-Authentifizierung für Azure Active Directory durch die SAS-Authentifizierung ersetzt. Informationen zum Übergangsplan finden Sie unter [Azure Active Directory ist die Zukunft von ACS](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx) (in englischer Sprache).

### Lösung

Verwenden Sie die SAS-Authentifizierung in Ihren Apps. Das folgende Beispiel zeigt, wie Sie ein vorhandenes SAS-Token zum Zugreifen auf einen Service Bus-Namespace oder eine Entität verwenden.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Weitere Informationen finden Sie in den folgenden Themen.

- Eine Übersicht hierzu finden Sie unter [SAS-Authentifizierung (Shared Access Signature) mit Service Bus](https://msdn.microsoft.com/library/dn170477.aspx).

- [Verwenden von SAS-Authentifizierung (Shared Access Signature) mit Service Bus](https://msdn.microsoft.com/library/dn205161.aspx)

- Ein Beispielprojekt finden Sie unter [Verwenden der SAS-Authentifizierung (Shared Access Signature) mit Service Bus-Abonnements](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c).

## Erwägen der Verwendung der OnMessage-Methode zum Vermeiden einer „Empfangsschleife“

### ID

AP2002

### Beschreibung

Um das Auftreten einer „Empfangsschleife“ zu vermeiden, ist das Aufrufen der **OnMessage**-Methode eine bessere Lösung für das Empfangen von Nachrichten als das Aufrufen der **Receive**-Methode. Wenn Sie die **Receive**-Methode verwenden müssen und nicht die Standardeinstellung für die Serverwartezeit festlegen, sollten Sie sicherstellen, dass die Serverwartezeit mehr als eine Minute beträgt.

Ihre Ideen und Feedback hierzu können Sie uns unter [Feedback zur Azure-Codeanalyse](http://go.microsoft.com/fwlink/?LinkId=403771) gern mitteilen.

### Grund

Beim Aufrufen von **OnMessage** startet der Client einen internen Nachrichtensystemvorgang, bei dem die Warteschlange bzw. das Abonnement ständig abgefragt werden. Dieses Nachrichtensystem enthält eine Endlosschleife, die einen Aufruf zum Empfangen von Nachrichten ausgibt. Wenn die Zeitüberschreitung für den Aufruf erreicht ist, wird ein neuer Aufruf ausgegeben. Das Timeoutintervall wird vom Wert der [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx)-Eigenschaft des verwendeten [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)-Elements bestimmt.

Der Vorteil der Verwendung von **OnMessage** gegenüber **Receive** besteht darin, dass Benutzer nicht folgende Schritte ausführen müssen: manuelles Abfragen von Nachrichten, Behandeln von Ausnahmen, paralleles Verarbeiten mehrerer Nachrichten und Abschließen der Nachrichten.

Wenn Sie **Receive** ohne Verwendung des Standardwerts aufrufen, sollten Sie sicherstellen, dass der Wert *ServerWaitTime* mehr als eine Minute beträgt. Mit dem Festlegen von *ServerWaitTime* auf mehr als eine Minute wird verhindert, dass für den Server eine Zeitüberschreitung auftritt, bevor die Nachricht vollständig empfangen wurde.

### Lösung

Die empfohlene Verwendung wird in den folgenden Codebeispielen veranschaulicht. Weitere Informationen finden Sie unter [QueueClient.OnMessage Method (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx) und [QueueClient.Receive Method (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Sehen Sie sich die Informationen zum Entwurfsmuster unter [Einführung in asynchrone Nachrichten](https://msdn.microsoft.com/library/dn589781.aspx) an, um die Leistung der Azure-Messaginginfrastruktur zu verbessern.

Im Folgenden finden Sie ein Beispiel für die Verwendung von **OnMessage** zum Empfangen von Nachrichten.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

Im Folgenden finden Sie ein Beispiel für die Verwendung von **Receive** mit der standardmäßigen Serverwartezeit.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

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
```

Im Folgenden finden Sie ein Beispiel für die Verwendung von **Receive** mit einer nicht standardmäßigen Serverwartezeit.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

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
```
## Verwenden asynchroner Service Bus-Methoden

### ID

AP2003

### Beschreibung

Verwenden Sie asynchrone Service Bus-Methoden zur Verbesserung der Leistung beim Brokermessaging.

Ihre Ideen und Feedback hierzu können Sie uns unter [Feedback zur Azure-Codeanalyse](http://go.microsoft.com/fwlink/?LinkId=403771) gern mitteilen.

### Grund

Die Verwendung asynchroner Methoden ermöglicht die Parallelität von Anwendungsprogrammen, da die Ausführung der einzelnen Aufrufe keine Blockierung des Hauptthreads bewirkt. Beim Verwenden von Service Bus-Messagingmethoden nimmt das Durchführen eines Vorgangs (Senden, Empfangen, Löschen usw.) Zeit in Anspruch. Dieser Zeitraum umfasst die Verarbeitung des Vorgangs durch den Service Bus-Dienst sowie die Latenz der Anforderung und der Antwort. Die Vorgänge müssen parallel ausgeführt werden, um die Anzahl von Vorgängen pro Zeitraum zu erhöhen. Weitere Informationen finden Sie unter [Bewährte Methoden für Leistungsoptimierungen mithilfe von Service Bus-Brokermessaging](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### Lösung

Informationen zur Verwendung der empfohlenen asynchronen Methode finden Sie unter [QueueClient-Klasse (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx).

Sehen Sie sich die Informationen zum Entwurfsmuster unter [Einführung in asynchrone Nachrichten](https://msdn.microsoft.com/library/dn589781.aspx) an, um die Leistung der Azure-Messaginginfrastruktur zu verbessern.

## Partitionieren von Service Bus-Warteschlangen und -Themen

### ID

AP2004

### Beschreibung

Partitionieren Sie Service Bus-Warteschlangen und -Themen, um eine bessere Leistung in Verbindung mit dem Service Bus-Messaging zu erzielen.

Ihre Ideen und Feedback hierzu können Sie uns unter [Feedback zur Azure-Codeanalyse](http://go.microsoft.com/fwlink/?LinkId=403771) gern mitteilen.

### Grund

Die Partitionierung von Service Bus-Warteschlangen und -Themen führt zu einer Erhöhung von Leistungsdurchsatz und Dienstverfügbarkeit, da der Gesamtdurchsatz einer partitionierten Warteschlange oder eines Themas nicht mehr durch die Leistung eines einzelnen Nachrichtenbrokers oder Nachrichtenspeichers beschränkt wird. Außerdem führt ein vorübergehender Ausfall eines Nachrichtenspeichers nicht dazu, dass eine partitionierte Warteschlange oder ein Thema nicht verfügbar ist. Weitere Informationen finden Sie unter [Partitionieren von Nachrichtenentitäten](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### Lösung

Der folgende Codeausschnitt veranschaulicht die Partitionierung von Nachrichtenentitäten.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Weitere Informationen finden Sie unter [Partitionierte Service Bus-Warteschlangen und -Themen | Microsoft Azure-Blog](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/). Sehen Sie sich außerdem das Beispiel [Microsoft Azure Service Bus – Partitionierte Warteschlange](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f) an.

## Vermeiden des Festlegens von SharedAccessStartTime

### ID

AP3001

### Beschreibung

Sie sollten das Verwenden von SharedAccessStartTimeset zum Angeben der aktuellen Zeit vermeiden, um die Shared Access-Richtlinie sofort zu starten. Sie müssen diese Eigenschaft nur festlegen, wenn Sie die Shared Access-Richtlinie zu einem späteren Zeitpunkt starten möchten.

Ihre Ideen und Feedback hierzu können Sie uns unter [Feedback zur Azure-Codeanalyse](http://go.microsoft.com/fwlink/?LinkId=403771) gern mitteilen.

### Grund

Die Uhrsynchronisierung verursacht einen leichten Zeitunterschied zwischen Rechenzentren. Es erscheint beispielsweise logisch, dass das Festlegen der Startzeit einer SAS-Speicherrichtlinie auf die aktuelle Uhrzeit per „DateTime.Now“ oder mit einer ähnlichen Methode dazu führt, dass die SAS-Richtlinie sofort wirksam wird. Die leichten Zeitunterschiede zwischen Rechenzentren können hierbei aber zu Problemen führen, da einige Rechenzentrumzeiten etwas zurückhinken, während andere bereits leicht voraus sind. Daher läuft die SAS-Richtlinie unter Umständen schnell ab (oder sogar sofort), wenn die Lebensdauer der Richtlinie zu kurz ist.

Eine ausführliche Anleitung zur Verwendung von Shared Access Signature für Azure-Speicher finden Sie unter [Einführung in Tabellen-SAS (Shared Access Signature), Warteschlangen-SAS und Blob-SAS-Update – Microsoft Azure Storage-Teamblog – Homepage der Website – MSDN-Blogs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx) (in englischer Sprache).

### Lösung

Entfernen Sie die Anweisung, mit der die Startzeit der Shared Access-Richtlinie festgelegt wird. Das Codeanalysetool von Azure enthält eine Lösung für dieses Problem. Weitere Informationen zur Sicherheitsverwaltung finden Sie unter dem Entwurfsmuster [Valet-Schlüsselmuster](https://msdn.microsoft.com/library/dn568102.aspx).

Der folgende Codeausschnitt veranschaulicht die Codefehlerbehebung für dieses Problem:

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## Ablaufzeit der SAS-Richtlinie muss mehr als fünf Minuten betragen

### ID

AP3002

### Beschreibung

Für Uhren zwischen Rechenzentren an verschiedenen Standorten können aufgrund einer Bedingung, die als Uhrabweichung (Clock Skew) bezeichnet wird, bis zu fünf Minuten Unterschied herrschen. Um zu verhindern, dass das SAS-Richtlinientoken früher als geplant abläuft, sollten Sie die Ablaufzeit auf mehr als fünf Minuten festlegen.

Ihre Ideen und Feedback hierzu können Sie uns unter [Feedback zur Azure-Codeanalyse](http://go.microsoft.com/fwlink/?LinkId=403771) gern mitteilen.

### Grund

Die Rechenzentren an verschiedenen Standorten weltweit werden mithilfe eines Uhrsignals synchronisiert. Da es einige Zeit dauert, bis das Uhrsignal die unterschiedlichen Standorte erreicht hat, kann es zu einer Zeitvarianz zwischen Rechenzentren an den einzelnen geografischen Orten kommen, obwohl scheinbar alles synchronisiert ist. Der Zeitunterschied kann sich auf die Startzeit und das Ablaufintervall der Shared Access-Richtlinie auswirken. Geben Sie daher die Startzeit nicht an, um sicherzustellen, dass die Shared Access-Richtlinie sofort wirksam wird. Stellen Sie außerdem sicher, dass die Ablaufzeit mehr als fünf Minuten lang ist, um eine frühe Zeitüberschreitung zu verhindern.

Weitere Informationen zur Verwendung von Shared Access Signature für Azure-Speicher finden Sie unter [Einführung in Tabellen-SAS (Shared Access Signature), Warteschlangen-SAS und Blob-SAS-Update – Microsoft Azure Storage-Teamblog – Homepage der Website – MSDN-Blogs](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx) (in englischer Sprache).

### Lösung

Weitere Informationen zur Sicherheitsverwaltung finden Sie unter dem Entwurfsmuster [Valet-Schlüsselmuster](https://msdn.microsoft.com/library/dn568102.aspx).

Im folgenden Beispiel wird keine Startzeit für die Shared Access-Richtlinie angegeben.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Im folgenden Beispiel wird die Startzeit für eine Shared Access-Richtlinie mit einer Richtlinienablaufdauer angegeben, die größer als fünf Minuten ist.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Weitere Informationen finden Sie unter [Erstellen und Verwenden einer SAS (Shared Access Signature)](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## Verwenden von CloudConfigurationManager

### ID

AP4000

### Beschreibung

Durch die Verwendung der [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx)-Klasse für Projekte, z. B. Azure Website und Azure Mobile Services, ergeben sich keine Laufzeitprobleme. Es ist jedoch eine bewährte Methode, Cloud[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) als einheitliches Verfahren zum Verwalten von Konfigurationen für alle Azure-Cloudanwendungen zu verwenden.

Ihre Ideen und Feedback hierzu können Sie uns unter [Feedback zur Azure-Codeanalyse](http://go.microsoft.com/fwlink/?LinkId=403771) gern mitteilen.

### Grund

CloudConfigurationManager liest die für die Anwendungsumgebung passende Konfigurationsdatei.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### Lösung

Gestalten Sie Ihren Code so um, dass die [CloudConfigurationManager-Klasse](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx) verwendet wird. Eine Codebehebung für dieses Problem ist mit dem Codeanalysetool von Azure möglich.

Der folgende Codeausschnitt veranschaulicht die Codefehlerbehebung für dieses Problem: Ersetzen von

`var settings = ConfigurationManager.AppSettings["mySettings"];`

durch

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Hier ist ein Beispiel dafür angegeben, wie Sie die Konfigurationseinstellung in einer App.config- oder Web.config-Datei speichern. Fügen Sie die Einstellungen dem appSettings-Abschnitt der Konfigurationsdatei hinzu. Unten ist die Datei „Web.config“ zum vorherigen Codebeispiel angegeben.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## Vermeiden der Verwendung von hartcodierten Verbindungszeichenfolgen

### ID

AP4001

### Beschreibung

Wenn Sie hartcodierte Verbindungszeichenfolgen verwenden und diese später aktualisieren möchten, müssen Sie Änderungen an Ihrem Quellcode vornehmen und die Anwendung neu kompilieren. Falls Sie Ihre Verbindungszeichenfolgen in einer Konfigurationsdatei speichern, können Sie diese später aber ändern, indem Sie einfach die Konfigurationsdatei aktualisieren.

Ihre Ideen und Feedback hierzu können Sie uns unter [Feedback zur Azure-Codeanalyse](http://go.microsoft.com/fwlink/?LinkId=403771) gern mitteilen.

### Grund

Das Hartcodieren von Verbindungszeichenfolgen ist keine bewährte Methode, da dies zu Problemen führt, wenn Verbindungszeichenfolgen schnell geändert werden müssen. Wenn das Projekt in die Quellcodeverwaltung eingecheckt sein muss, führen hartcodierte Verbindungszeichenfolgen außerdem zu Schwachstellen in Bezug auf die Sicherheit, da die Zeichenfolgen im Quellcode angezeigt werden können.

### Lösung

Speichern Sie Verbindungszeichenfolgen in den Konfigurationsdateien oder in Azure-Umgebungen.

- Verwenden Sie für eigenständige Anwendungen die Datei „app.config“, um die Einstellungen für die Verbindungszeichenfolge zu speichern.

- Verwenden Sie für IIS-gehostete Webanwendungen die Datei „web.config“ zum Speichern der Verbindungszeichenfolgen.

- Verwenden Sie für ASP.NET vNext-Anwendungen die Datei „configuration.json“ zum Speichern der Verbindungszeichenfolgen.

Informationen zur Verwendung von Konfigurationsdateien wie „web.config“ oder „app.config“ finden Sie unter [ASP.NET-Webkonfigurationsrichtlinien](https://msdn.microsoft.com/library/vstudio/ff400235(v=vs.100).aspx). Weitere Informationen zur Funktionsweise von Azure-Umgebungsvariablen finden Sie unter [Azure-Websites: Funktionsweise von Anwendungs- und Verbindungszeichenfolgen](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Informationen zum Speichern der Verbindungszeichenfolge in der Quellcodeverwaltung finden Sie unter [Vermeiden des Einfügens von vertraulichen Informationen (z. B. Verbindungszeichenfolgen) in Dateien, die im Quellcode-Repository gespeichert werden](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control).

## Verwenden der Konfigurationsdatei für die Diagnose

### ID

AP5000

### Beschreibung

Anstatt Diagnoseeinstellungen in Ihrem Code zu konfigurieren, z. B. mithilfe der Microsoft.WindowsAzure.Diagnostics-Programmier-API, sollten Sie die Diagnoseeinstellungen in der Datei „diagnostics.wadcfg“ konfigurieren. (Oder in „diagnostics.wadcfgx“, wenn Sie Azure SDK 2.5 verwenden.) Auf diese Weise können Sie Diagnoseeinstellungen ändern, ohne den Code neu kompilieren zu müssen.

Ihre Ideen und Feedback hierzu können Sie uns unter [Feedback zur Azure-Codeanalyse](http://go.microsoft.com/fwlink/?LinkId=403771) gern mitteilen.

### Grund

Vor der Veröffentlichung von Azure SDK 2.5 (mit Verwendung von Azure Diagnostics 1.3) konnte Azure Diagnostics (WAD) mit mehreren unterschiedlichen Methoden konfiguriert werden: durch das Hinzufügen zum Konfigurations-Blob im Speicher, per imperativem Code, per deklarativer Konfiguration oder über die Standardkonfiguration. Allerdings ist die bevorzugte Methode zum Konfigurieren der Diagnose die Verwendung einer XML-Konfigurationsdatei („diagnostics.wadcfg“ oder „diagnositcs.wadcfgx“ für SDK 2.5 und höher) im Anwendungsprojekt. Bei diesem Ansatz wird die Konfiguration vollständig über die Datei „diagnostics.wadcfg“ definiert und kann je nach Bedarf aktualisiert und neu bereitgestellt werden. Das Mischen der Konfigurationsdatei „diagnostics.wadcfg“ mit den programmgesteuerten Methoden der Konfigurationsfestlegung mit den Klassen [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx) oder [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx) kann zu Verwirrung führen. Weitere Informationen finden Sie unter [Initialisieren oder Ändern der Azure-Diagnosekonfiguration](https://msdn.microsoft.com/library/azure/hh411537.aspx).

Ab WAD 1.3 (Teil von Azure SDK 2.5) ist es nicht mehr möglich, Code zum Konfigurieren der Diagnose zu verwenden. Sie können die Konfiguration daher nur angeben, wenn Sie die Diagnoseerweiterung anwenden oder aktualisieren.

### Lösung

Verwenden Sie den Designer für die Diagnosekonfiguration, um die Diagnoseeinstellungen in die Diagnosekonfigurationsdatei zu verschieben („diagnositcs.wadcfg“ oder „diagnositcs.wadcfgx“ für SDK 2.5 und höher). Es wird empfohlen, [Azure SDK 2.5](http://go.microsoft.com/fwlink/?LinkId=513188) zu installieren und die aktuelle Diagnosefunktion zu verwenden.

1. Wählen Sie im Kontextmenü für die Rolle, die Sie konfigurieren möchten, die Option „Eigenschaften“ und dann die Registerkarte „Konfiguration“.

1. Stellen Sie im Abschnitt **Diagnose** sicher, dass das Kontrollkästchen **Diagnose aktivieren** aktiviert ist.

1. Wählen Sie die Schaltfläche **Konfigurieren**.

  ![Zugreifen auf die Option „Diagnose aktivieren“](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

  Weitere Informationen finden Sie unter [Konfigurieren der Diagnose für Azure Cloud Services und Virtual Machines](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).


## Vermeiden der Deklaration von DbContext-Objekten als „statisch“

### ID

AP6000

### Beschreibung

Vermeiden Sie die Deklaration von DBContext-Objekten als „statisch“, um Arbeitsspeicher zu sparen.

Ihre Ideen und Feedback hierzu können Sie uns unter [Feedback zur Azure-Codeanalyse](http://go.microsoft.com/fwlink/?LinkId=403771) gern mitteilen.

### Grund

DBContext-Objekte enthalten die Abfrageergebnisse für jeden Aufruf. Statische DBContext-Objekte werden erst verworfen, wenn die Anwendungsdomäne entladen wird. Aus diesem Grund kann ein statisches DBContext-Objekt große Mengen an Arbeitsspeicher belegen.

### Lösung

Deklarieren Sie DBContext als lokale Variable oder nicht statisches Instanzenfeld, verwenden Sie es für eine Aufgabe, und lassen Sie es dann zu, dass es nach der Verwendung verworfen wird.

Im folgenden Beispiel wird anhand der MVC-Controller-Klasse veranschaulicht, wie das DBContext-Objekt verwendet wird.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## Nächste Schritte

Weitere Informationen zur Optimierung und Problembehandlung von Azure-Apps finden Sie unter [Problembehandlung von Web-Apps in Azure App Service in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

<!---HONumber=AcomDC_0128_2016-->