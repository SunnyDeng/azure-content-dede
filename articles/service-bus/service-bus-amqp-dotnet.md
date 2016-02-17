<properties 
   pageTitle="Service Bus mit .NET und AMQP 1.0 | Microsoft Azure"
   description="Verwenden von Service Bus aus .NET mit AMQP"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" /> 
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="sethm" />

# Verwenden von Service Bus aus .NET mit AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## Herunterladen des Service Bus SDK

Unterstützung für AMQP 1.0 ist im Service Bus SDK ab Version 2.1 verfügbar. Sie können die neuesten Service Bus-Komponenten von [NuGet][] herunterladen.

## Konfigurieren von .NET-Anwendungen für das Verwenden von AMQP 1.0

Mithilfe eines dedizierten SOAP-basierten Protokolls kommuniziert die .NET-Clientbibliothek von Service Bus standardmäßig mit dem Service Bus-Dienst. Wenn Sie anstatt des Standardprotokolls AMQP 1.0 verwenden möchten, ist eine explizite Konfiguration der Service Bus-Verbindungszeichenfolge erforderlich, die im nächsten Abschnitt beschrieben wird. Mit Ausnahme dieser Änderung bleibt der Anwendungscode bei Verwenden von AMQP 1.0 grundsätzlich unverändert.

In der aktuellen Version gibt es ein paar API-Features, die bei Verwendung von AMQP nicht unterstützt werden. Diese nicht unterstützten Features sind weiter unten im Abschnitt [Nicht unterstützte Features, Einschränkungen und Verhaltensunterschiede](#unsupported-features-restrictions-and-behavioral-differences) aufgeführt. Darüber hinaus haben einige der erweiterten Konfigurationseinstellungen eine unterschiedliche Bedeutung, wenn AMQP zum Einsatz kommt.

### Konfiguration mithilfe von "App.config"

Es ist empfehlenswert, für Anwendungen die Konfigurationsdatei "App.config" zu verwenden, um die Einstellungen zu speichern. Bei Service Bus-Anwendungen können Sie die Datei "App.config" verwenden, um die Einstellungen für die Service Bus-**Verbindungszeichenfolge** zu speichern. Es folgt ein Beispiel der Datei "App.config":

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <appSettings>
	        <add key="Microsoft.ServiceBus.ConnectionString"
	             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
	    </appSettings>
	</configuration>

Beim Wert der Einstellung `Microsoft.ServiceBus.ConnectionString` handelt es sich um die Service Bus-Verbindungszeichenfolge, die zum Konfigurieren der Verbindung mit Service Bus verwendet wird. Das Format sieht folgendermaßen aus:

	Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

`[namespace]` und `SharedAccessKey` werden aus dem [klassischen Azure-Portal][] bezogen. Weitere Informationen finden Sie unter [Verwenden von Azure Service Bus-Warteschlangen][].

Wenn Sie AMQP verwenden, fügen Sie `;TransportType=Amqp` an die Verbindungszeichenfolge an. Diese Notation weist die Clientbibliothek an, die Verbindung mit Service Bus über AMQP 1.0 herzustellen.

## Nachrichtenserialisierung

Bei Verwendung des Standardprotokolls sieht das Standardserialisierungsverhalten der .NET-Clientbibliothek die Verwendung des [DataContractSerializer][]-Typs zum Serialisieren einer [BrokeredMessage][]-Instanz für die Übertragung zwischen der Clientbibliothek und dem Service Bus-Dienst vor. Bei Verwendung des AMQP-Transportmodus verwendet die Clientbibliothek das AMQP-Typsystem für die Serialisierung der [im Broker gespeicherten Nachricht][BrokeredMessage] in eine AMQP-Nachricht. Diese Serialisierung ermöglicht, dass die Nachricht von einer empfangenden Anwendung empfangen und interpretiert wird, die möglicherweise auf einer anderen Plattform ausgeführt wird, z. B. einer Java-Anwendung, die die JMS-API für den Zugriff auf Service Bus verwendet.

Wenn Sie eine [BrokeredMessage][]-Instanz erstellen, können Sie ein .NET-Objekt als Parameter für den Konstruktor bereitstellen, der als Text der Nachricht dient. Für Objekte, die primitiven AMQP-Typen zugeordnet werden können, wird der Text in AMQP-Datentypen serialisiert. Wenn das Objekt nicht direkt einem primitiven AMQP-Typ, d. h. einem von der Anwendung definierten benutzerdefinierten Typ, zugeordnet werden kann, wird das Objekt mithilfe von [DataContractSerializer][] serialisiert, und die serialisierten Bytes werden in einer AMQP-Datennachricht gesendet.

Um die Interoperabilität mit Nicht-.NET-Clients zu erleichtern, verwenden Sie nur .NET-Typen, die für den Text der Nachricht direkt in AMQP-Typen serialisiert werden können. Die folgende Tabelle enthält diese Typen und die entsprechende Zuordnung zum AMQP-Typsystem.

| .NET Body-Objekttyp | Zugeordneter AMQP-Typ | AMQP Body-Abschnittstyp |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| bool | boolean | AMQP Value |
| byte | ubyte | AMQP Value |
| ushort | ushort | AMQP Value |
| uint | uint | AMQP Value |
| ulong | ulong | AMQP Value |
| sbyte | byte | AMQP Value |
| short | short | AMQP Value |
| int | int | AMQP Value |
| lang | lang | AMQP Value |
| float | float | AMQP Value |
| double | double | AMQP Value |
| decimal | decimal128 | AMQP Value |
| char | char | AMQP Value |
| DateTime | timestamp | AMQP Value |
| Guid | uuid | AMQP Value |
| byte | binary | AMQP Value |
| string | string | AMQP Value |
| System.Collections.IList | list | AMQP Value: Diese Auflistung kann nur Elemente enthalten, die in dieser Tabelle definiert sind. |
| System.Array | array | AMQP Value: Diese Auflistung kann nur Elemente enthalten, die in dieser Tabelle definiert sind. |
| System.Collections.IDictionary | map | AMQP Value: Diese Auflistung kann nur Elemente enthalten, die in dieser Tabelle definiert sind. Hinweis: nur "String"-Schlüssel werden unterstützt. |
| Uri | Beschriebene Zeichenfolge (siehe die folgende Tabelle) | AMQP Value |
| DateTimeOffset | Lange Beschreibung (siehe die folgende Tabelle) | AMQP Value |
| TimeSpan | Lange Beschreibung (siehe die folgende Tabelle) | AMQP Value |
| Stream | binary | AMQP Data (können mehrere sein). Die "Data"-Abschnitte enthalten die rohen Bytes, die aus dem "Stream"-Objekt gelesen. |
| Other Object | binary | AMQP Data (können mehrere sein). Enthält die serialisierten Binärdaten des Objekts, das "DataContractSerializer" oder einen von der Anwendung bereitgestellten Serialisierer verwendet. |

| .NET-Typ | Zugeordneter beschriebener AMQP-Typ | Hinweise |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| Uri | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` | Uri.AbsoluteUri |
| DateTimeOffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| TimeSpan | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` | TimeSpan.Ticks |

## Nicht unterstützte Features, Einschränkungen und Verhaltensunterschiede

Die folgenden Features der .NET-API von Service Bus werden bei Verwendung von AMQP derzeit nicht unterstützt:

-   Transaktionen

-   Senden mithilfe eines Übertragungsziels

-   Empfangen per Nachrichtensequenznummer

-   Durchsuchen von Nachrichten und Sitzungen

-   Sitzungsstatus

-   Batchbasierte APIs

-   Skaliertes Empfangen

-   Ändern von Abonnementregeln zur Laufzeit

-   Verlängern der Sitzungssperre

Insbesondere die folgenden APIs werden derzeit nicht unterstützt, wenn AMQP verwendet wird:

- [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession][]
- [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)][]

- [Microsoft.ServiceBus.Messaging.MessageSender.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})][]

- [Microsoft.ServiceBus.Messaging.MessageReceiver.Receive(System.Int64)][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.ReceiveBatch][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.Peek][]
- [Microsoft.ServiceBus.Messaging.MessageReceiver.PeekBatch][]

- [Microsoft.ServiceBus.Messaging.QueueClient.Peek][]
- [Microsoft.ServiceBus.Messaging.QueueClient.PeekBatch][]

- [Microsoft.ServiceBus.Messaging.TopicClient.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})][]

- [Microsoft.ServiceBus.Messaging.SubscriptionClient.Receive(System.Int64)][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.ReceiveBatch][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.Peek][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.PeekBatch][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.AddRule][]
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.RemoveRule(System.String)][]

- [Microsoft.ServiceBus.Messaging.MessageSession.GetState][]
- [Microsoft.ServiceBus.Messaging.MessageSession.SetState(System.IO.Stream)][]
- [Microsoft.ServiceBus.Messaging.MessageSession.RenewLock][]

- [Microsoft.ServiceBus.Messaging.BrokeredMessage.RenewLock][]

Es gibt bei Verwenden von AMQP im Vergleich zum Standardprotokoll auch einige kleine Unterschiede beim Verhalten der .NET-API von Service Bus:

-   Die [OperationTimeout][] -Eigenschaft wird ignoriert.

-   `MessageReceiver.Receive(TimeSpan.Zero)` wird als `MessageReceiver.Receive(TimeSpan.FromSeconds(10))` implementiert.

## Steuern von AMQP-Protokolleinstellungen

Die .NET-APIs machen mehrere Einstellungen zum Steuern des Verhaltens des AMQP-Protokolls verfügbar:

-   **MessageReceiver.PrefetchCount**: Steuert die anfänglichen Rechte, die auf einen Link angewendet werden. Der Standardwert ist 0.

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: Steuert die maximale AMQP-Framegröße, die während der Verhandlung zum Herstellen der Verbindung angeboten wird. Der Standardwert ist 65.536 Bytes.

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: Wenn Übertragungen in Batches erfolgen können, bestimmt dieser Wert die maximale Verzögerung für das Senden von Einteilungen. Wird von Absendern/Empfängern standardmäßig vererbt. Einzelne Sender/Empfänger können die Standardeinstellung (20 Millisekunden) überschreiben.

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: Steuert, ob AMQP-Verbindungen über eine SSL-Verbindung hergestellt werden. Der Standardwert ist **true**.

## Nächste Schritte

Möchten Sie mehr erfahren? Nutzen Sie die folgenden Links:

- [Übersicht über Service Bus AMQP]
- [AMQP 1.0-Unterstützung für partitionierte Warteschlangen und Themen von Service Bus]
- [AMQP in Service Bus für Windows Server]

  [Verwenden von Azure Service Bus-Warteschlangen]: service-bus-dotnet-how-to-use-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [Microsoft.ServiceBus.Messaging.MessageSender.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.sendbatch.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.Receive(System.Int64)]: https://msdn.microsoft.com/library/azure/hh322665.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.ReceiveBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.receivebatch.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.completebatch.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.Peek]: https://msdn.microsoft.com/library/azure/jj908731.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.PeekBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.peekbatch.aspx
  [Microsoft.ServiceBus.Messaging.QueueClient.Peek]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx
  [Microsoft.ServiceBus.Messaging.QueueClient.PeekBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx
  [Microsoft.ServiceBus.Messaging.TopicClient.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.sendbatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.Receive(System.Int64)]: https://msdn.microsoft.com/library/azure/hh293110.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.ReceiveBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.receivebatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.completebatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.Peek]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peek.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.PeekBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.AddRule]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.addrule.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.RemoveRule(System.String)]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.removerule.aspx
  [Microsoft.ServiceBus.Messaging.MessageSession.GetState]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx
  [Microsoft.ServiceBus.Messaging.MessageSession.SetState(System.IO.Stream)]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx
  [Microsoft.ServiceBus.Messaging.MessageSession.RenewLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx
  [Microsoft.ServiceBus.Messaging.BrokeredMessage.RenewLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[klassischen Azure-Portal]: http://manage.windowsazure.com
[Übersicht über Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-Unterstützung für partitionierte Warteschlangen und Themen von Service Bus]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP in Service Bus für Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

<!---HONumber=AcomDC_0128_2016-->