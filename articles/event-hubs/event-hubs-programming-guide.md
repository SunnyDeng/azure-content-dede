<properties 
   pageTitle="Programmierleitfaden für Ereignis-Hubs"
   description="Beschreibt die Programmierung mit Azure Event Hubs unter Verwendung des Azure .NET SDK."
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/10/2015"
   ms.author="sethm" />

# Programmierleitfaden für Event Hubs

In diesem Thema wird die Programmierung mit Azure Event Hubs unter Verwendung des Azure .NET SDK beschrieben. Hierbei wird ein grundlegendes Verständnis von Event Hubs vorausgesetzt. Eine konzeptuelle Übersicht über Event Hubs finden Sie unter [Übersicht über Event Hubs](event-hubs-overview.md).

## Veröffentlichen von Ereignissen: Ereignisherausgeber

Das Senden von Ereignissen an einen Event Hub wird entweder mit HTTP POST oder über eine AMQP 1.0-Verbindung erreicht. Welches Verfahren gewählt wird, hängt vom jeweils vorliegenden Szenario ab. AMQP 1.0-Verbindungen werden als vermittelte Verbindungen in Service Bus gemessen und besser für Fälle mit häufigeren höheren Nachrichtenvolumen und geringeren Latenzanforderungen geeignet, da sie über einen dauerhaften Nachrichtenkanal verfügen.

Event Hubs werden mit der [NamespaceManager](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx)-Klasse erstellt und verwaltet. Beim Verwenden der per .NET verwalteten APIs sind die Hauptkonstrukte für die Veröffentlichung von Daten auf Event Hubs die Klassen [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx) und [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx). Die [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx)-Klasse stellt den AMQP-Kommunikationskanal über die Ereignisse bereit, die an den Event Hub gesendet werden. Die [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx)-Klasse stellt ein Ereignis dar und wird verwendet, um Nachrichten auf einem Event Hub zu veröffentlichen. Diese Klasse enthält den Text, einige Metadaten sowie Headerinformationen zum Ereignis. Andere Eigenschaften werden dem [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx)-Objekt hinzugefügt, wenn es einen Event Hub durchläuft.

## Erste Schritte

Die .NET-Klassen, die Event Hubs unterstützen, sind Teil der Assembly „Microsoft.ServiceBus.dll“. Der einfachste Weg zum Verweisen auf die Service Bus-API und Konfigurieren Ihrer Anwendung mit allen Service Bus-Abhängigkeiten ist das Herunterladen des Service Bus-NuGet-Pakets. Weitere Informationen finden Sie unter [Verwenden des NuGet-Service Bus-Pakets](https://msdn.microsoft.com/library/dn741354.aspx). Alternativ dazu können Sie die [Paket-Manager-Konsole](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) in Visual Studio verwenden. Geben Sie hierzu im Fenster der [Paket-Manager-Konsole](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) den folgenden Befehl ein:

```powershell
Install-Package WindowsAzure.ServiceBus
```

## Erstellen eines Ereignis-Hubs

Sie können die [NamespaceManager](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx)-Klasse verwenden, um Event Hubs zu erstellen. Beispiel:

```c
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

In den meisten Fällen ist es ratsam, die [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx)-Methoden zu verwenden, um bei einem Neustart des Diensts die Generierung von Ausnahmen zu vermeiden. Beispiel:

```
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

Alle Event Hub-Erstellungsvorgänge, z. B. [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx), erfordern für den jeweiligen Namespace Verwaltungsberechtigungen (**Manage**). Falls Sie die Berechtigungen für Ihre Veröffentlichungs- oder Consumeranwendungen beschränken möchten, können Sie diese Aufrufe von Erstellungsvorgängen im Produktionscode vermeiden, wenn Sie Anmeldeinformationen mit begrenzten Berechtigungen verwenden.

Die [EventHubDescription](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubdescription.aspx)-Klasse enthält Details zu einem Event Hub, z. B. Autorisierungsregeln, Intervall der Nachrichtenaufbewahrung, Partitions-IDs, Status und Pfad. Sie können diese Klasse verwenden, um die Metadaten für einen Event Hub zu aktualisieren.

## Erstellen eines Event Hub-Clients

Die Hauptklasse für die Interaktion mit Event Hubs ist [Microsoft.ServiceBus.Messaging.EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx). Mit dieser Klasse werden Funktionen für Absender und Empfänger bereitgestellt. Sie können diese Klasse mit der [Create](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.create.aspx)-Methode instanziieren. Dies wird im folgenden Beispiel veranschaulicht.

```
var client = EventHubClient.Create(description.Path);
```

In dieser Methode werden die Service Bus-Verbindungsinformationen aus dem Abschnitt `appSettings` der Datei „App.config“ verwendet. Ein Beispiel für den `appSettings`-XML-Code, der zum Speichern der Service Bus-Verbindungsinformationen verwendet wird, finden Sie in der Dokumentation zur [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.create.aspx)-Methode.

Eine weitere Option ist die Erstellung des Clients über eine Verbindungszeichenfolge. Diese Option funktioniert gut, wenn Azure-Workerrollen verwendet werden, weil Sie die Zeichenfolge in den Konfigurationseigenschaften für den Worker speichern können. Beispiel:

```
EventHubClient.CreateFromConnectionString("your_connection_string");
```

Die Verbindungszeichenfolge hat das gleiche Format wie in der Datei „App.config“ für die vorherigen Methoden:

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=Manage;SharedAccessKey=[key]
```

Schließlich ist es auch möglich, ein [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx)-Objekt aus einer [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx)-Instanz zu erstellen. Dies ist im folgenden Beispiel dargestellt.

```
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

Es ist wichtig zu beachten, dass für zusätzliche [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx)-Objekte, die aus einer Messagingfactory-Instanz erstellt werden, die gleiche zugrunde liegende TCP-Verbindung wiederverwendet wird. Daher verfügen diese Objekte über einen clientseitigen Grenzwert für den Durchsatz. Bei der [Create](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.create.aspx)-Methode wird eine einzelne Messagingfactory wiederverwendet. Wenn Sie für einen einzelnen Absender einen sehr hohen Durchsatz benötigen, können Sie mehrere Messagingfactorys und ein [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx)-Objekt aus jeder Messagingfactory erstellen.

## Senden von Ereignissen an einen Event Hub

Sie senden Ereignisse an einen Event Hub, indem Sie eine [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx)-Instanz erstellen und per [Send](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.send.aspx)-Methode senden. Diese Methode verwendet einen einzelnen [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx)-Instanzparameter und sendet ihn synchron an einen Event Hub.

## Ereignisserialisierung

Die [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx)-Klasse verfügt über [vier überladene Konstruktoren](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx), für die verschiedene Parameter verwendet werden können, z. B. ein Objekt und Serialisierungsprogramm, ein Bytearray oder ein Datenstrom. Es ist auch möglich, die [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx)-Klasse zu instanziieren und den Textdatenstrom danach festzulegen. Wenn Sie JSON mit [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) verwenden, können Sie mit **Encoding.UTF8.GetBytes()** das Bytearray für eine JSON-codierte Zeichenfolge abrufen.

## Partitionsschlüssel

Die [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx)-Klasse enthält eine [PartitionKey](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.partitionkey.aspx)-Eigenschaft, mit der der Absender einen Wert angeben kann, der gehasht ist, um eine Partitionsanweisung zu erzeugen. Mit einem Partitionsschlüssel wird sichergestellt, dass alle Ereignisse mit dem gleichen Schlüssel an dieselbe Partition im Event Hub gesendet werden. Gemeinsame Partitionsschlüssel enthalten Benutzersitzungs-IDs und eindeutige Absender-IDs. Die [PartitionKey](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.partitionkey.aspx)-Eigenschaft ist optional und kann bereitgestellt werden, wenn die Methode [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) oder [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) verwendet wird. Wenn Sie keinen Wert für [PartitionKey](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) angeben, werden gesendete Ereignisse per Roundrobin-Modell an Partitionen verteilt.

## Sendevorgänge für Batchereignisse

Das Senden von Ereignissen in Batches kann den Durchsatz erheblich erhöhen. Die [SendBatch](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx)-Methode verwendet einen **IEnumerable**-Parameter vom Typ [EventData](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventdata.aspx) und sendet den gesamten Batch als atomischen Vorgang an den Event Hub.

```
public void SendBatch(IEnumerable<EventData> eventDataList);
```

Es ist wichtig zu beachten, dass ein einzelner Batch den Grenzwert von 256 KB für ein Ereignis nicht überschreiten darf. Darüber hinaus wird für jede Nachricht im Batch die gleiche Herausgeberidentität (Publisher Identity) verwendet. Der Absender ist dafür verantwortlich sicherzustellen, dass die maximale Ereignisgröße für den Batch nicht überschritten wird. Bei einer Überschreitung wird ein **Send**-Fehler für den Client generiert.

## Asynchrones Senden und Senden mit Skalierung

Sie können Ereignisse auch asynchron an einen Event Hub senden. Beim asynchronen Senden kann die Rate erhöht werden, mit der ein Client Ereignisse senden kann. Sowohl die [Send](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.send.aspx)-Methode als auch die [SendBatch](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx)-Methode ist in asynchronen Versionen verfügbar, die ein [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx)-Objekt zurückgeben. Mit dieser Vorgehensweise kann zwar der Durchsatz erhöht werden, aber sie kann auch dazu führen, dass der Client auch dann weiter Ereignisse sendet, während er durch den Event Hubs-Dienst gedrosselt wird. Dies kann bei einer falschen Implementierung zur Folge haben, dass für den Client Fehler oder verloren gegangene Nachrichten auftreten. Darüber hinaus können Sie die [RetryPolicy](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.cliententity.retrypolicy.aspx)-Eigenschaft auf dem Client verwenden, um Wiederholungsversuche des Clients zu steuern.

## Erstellen eines Elements für das Senden an Partitionen

Am häufigsten werden Ereignisse zwar mit einem Partitionsschlüssel an einen Event Hub gesendet, aber es kann auch vorkommen, dass Sie Ereignisse direkt an eine bestimmte Partition senden möchten. Beispiel:

```
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.createpartitionedsender.aspx) gibt ein [EventHubSender](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubsender.aspx)-Objekt zurück, das Sie verwenden können, um Ereignisse auf einer bestimmten Event Hub-Partition zu veröffentlichen.

## Verwenden von Ereignissen: Ereignisconsumer

Der Event Hubs-Dienst verfügt über zwei primäre Modelle für die Ereignisnutzung: direkte Empfänger und allgemeinere Abstraktionen, z. B. [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx). Direkte Empfänger sind verantwortlich für ihre eigene Koordinierung des Zugriffs auf Partitionen innerhalb einer Consumergruppe.

### Direkter Consumer

Die direkteste Möglichkeit zum Lesen aus einer Partition innerhalb einer Consumergruppe ist die Verwendung der [EventHubReceiver](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubreceiver.aspx)-Klasse. Um eine Instanz dieser Klasse zu erstellen, müssen Sie eine Instanz der [EventHubConsumerGroup](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubconsumergroup.aspx)-Klasse verwenden. Im folgenden Codebeispiel muss die Partitions-ID angegeben werden, wenn der Empfänger für die Consumergruppe erstellt wird.

```
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

Die [CreateReceiver](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubconsumergroup.createreceiver.aspx)-Methode verfügt über mehrere Überladungen, mit denen die Erstellung der Leseeinheit gesteuert wird. Diese Methoden umfassen das Angeben eines Offsets als Zeichenfolge oder Zeitstempel sowie die Möglichkeit zum Festlegen, ob der angegebene Offset in den zurückgegebenen Datenstrom eingebunden oder ob er danach gestartet werden soll. Nach der Erstellung des Empfängers können Sie beginnen, Ereignisse für das zurückgegebene Objekt zu empfangen. Die [Receive](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubreceiver.receive.aspx)-Methode hat vier Überladungen, mit denen die Parameter des Empfangsvorgangs gesteuert werden, z. B. Batchgröße und Wartezeit. Sie können die asynchronen Versionen dieser Methoden verwenden, um den Durchsatz eines Consumers zu erhöhen. Beispiel:

```
bool receive = true;
string myOffset;
while(receive)
{
    var message = receiver.Receive();
    myOffset = message.Offset;
    string body = Encoding.UTF8.GetString(message.GetBytes());
    Console.WriteLine(String.Format("Received message offset: {0} \nbody: {1}", myOffset, body));
}
```

Im Hinblick auf eine bestimmte Partition werden die Nachrichten in der Reihenfolge empfangen, in der sie an den Event Hub gesendet wurden. Der Offset ist ein Zeichenfolgentoken, das zum Identifizieren einer Nachricht in einer Partition verwendet wird.

Es ist wichtig zu beachten, dass mit einer einzelnen Partition innerhalb einer Consumergruppe nicht mehr als fünf Leseeinheiten gleichzeitig verbunden sein können. Wenn Leseeinheiten verbunden oder getrennt werden, können die dazugehörigen Sitzungen noch einige Minuten lang aktiv bleiben, bevor der Dienst die Trennung erkennt. Während dieses Zeitraums kann für die erneute Herstellung der Verbindung mit einer Partition ein Fehler auftreten. Ein vollständiges Beispiel für das Schreiben eines direkten Empfängers für Event Hubs finden Sie im Beispiel [Service Bus Event Hubs – Direkte Empfänger](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6).

### Ereignisprozessorhost

Die [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx)-Klasse verarbeitet Daten aus Event Hubs. Sie sollten diese Implementierung verwenden, wenn Sie Ereignisleser auf der .NET-Plattform erstellen. Mit [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) wird eine threadsichere Laufzeitumgebung mit mehreren Prozessen für Ereignisprozessorimplementierungen bereitgestellt, die auch die Erstellung von Prüfpunkten und die Leaseverwaltung für Partitionen ermöglicht.

Sie können [IEventProcessor](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.aspx) implementieren, um die [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx)-Klasse zu verwenden. Diese Schnittstelle enthält drei Methoden:

- [OpenAsync](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.openasync.aspx)

- [CloseAsync](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.closeasync.aspx)

- [ProcessEventsAsync](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.processeventsasync.aspx)

Instanziieren Sie zum Starten der Ereignisverarbeitung die [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx)-Klasse, und geben Sie die entsprechenden Parameter für Ihren Event Hub an. Rufen Sie anschließend [RegisterEventProcessorAsync](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.registereventprocessorasync.aspx) auf, um Ihre [IEventProcessor](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.aspx)-Implementierung bei der Laufzeit zu registrieren. An diesem Punkt versucht der Host, einen Lease für jede Partition im Event Hub zu erhalten, indem er einen „gierigen“ Algorithmus verwendet. Diese Leases gelten für einen bestimmten Zeitraum und müssen anschließend erneuert werden. Wenn neue Knoten (hier: Workerinstanzen) in den Onlinezustand versetzt werden, geben sie Leasereservierungen heraus. Im Laufe der Zeit wird die Arbeitsauslastung dann auf die Knoten verteilt, da jeder Knoten versucht, mehr Leases zu erlangen.

![Ereignisprozessorhost](./media/event-hubs-programming-guide/IC759863.png)

Im Laufe der Zeit wird somit ein Gleichgewicht erreicht. Diese dynamische Funktion ermöglicht, dass die CPU-basierte automatische Skalierung sowohl beim zentralen Herunterskalieren als auch beim zentralen Hochskalieren auf Consumer angewendet wird. Da Event Hubs nicht über ein direktes Konzept in Bezug auf die Nachrichtenanzahl verfügen, ist die durchschnittliche CPU-Auslastung häufig das beste Verfahren zum Messen der Back-End- bzw. Consumerskalierung. Wenn Herausgeber beginnen, mehr Ereignisse zu veröffentlichen, als von den Consumern verarbeitet werden können, kann der CPU-Anstieg auf den Consumern verwendet werden, um eine automatische Skalierung in Bezug auf die Anzahl der Workerinstanzen auszulösen.

Außerdem implementiert die [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx)-Klasse ein auf Azure Storage-basiertes Verfahren für die Prüfpunktausführung. Bei diesem Verfahren wird der Offset pro Partition gespeichert, damit jeder Consumer ermitteln kann, wie der letzte Prüfpunkt des vorherigen Consumers lautete. Da Partitionen per Lease zwischen Knoten wechseln, ist dies das Synchronisierungsverfahren, das die Auslastungsverteilung ermöglicht.

## Herausgebersperrung

Zusätzlich zu den erweiterten Laufzeitfunktionen von [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) ermöglichen Event Hubs die Herausgebersperrung, damit spezielle Herausgeber blockiert werden und keine Ereignisse an einen Event Hub senden können. Diese Funktionen sind besonders nützlich, wenn das Token eines Herausgebers gefährdet ist oder eine Software ein unangemessenes Verhalten bewirkt. In diesen Fällen kann die Identität des Herausgebers, die Teil des SAS-Tokens ist, blockiert werden, um das Veröffentlichen von Ereignissen zu verhindern.

Weitere Informationen zum Sperren von Herausgebern und zum Senden an Event Hubs als Herausgeber finden Sie im Beispiel [Service Bus Ereignis Hubs – Sicheres Veröffentlichen in größerem Umfang](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## Nächste Schritte

Weitere Informationen zu Event Hubs-Szenarien finden Sie unter diesen Links:

- [Übersicht über die Event Hubs-API](event-hubs-api-overview.md)
- [Übersicht über Event Hubs](event-hubs-overview.md)
- [Event Hubs-Codebeispiele](http://code.msdn.microsoft.com/site/search?query=event hub&f[0].Value=event hub&f[0].Type=SearchText&ac=5)
- [Referenz zur Ereignisprozessorhost-API](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx)

<!---HONumber=July15_HO3-->