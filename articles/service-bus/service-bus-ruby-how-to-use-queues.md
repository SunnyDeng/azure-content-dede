<properties
	pageTitle="Verwenden von Service Bus-Warteschlangen mit Ruby | Microsoft Azure"
	description="Erfahren Sie mehr über die Verwendung von Service Bus-Warteschlangen in Azure. Die Codebeispiele wurden in Ruby geschrieben."
	services="service-bus"
	documentationCenter="ruby"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="12/09/2015"
	ms.author="sethm"/>

# Verwenden von Service Bus-Warteschlangen

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In diesem Leitfaden wird beschrieben, wie Sie Service Bus-Warteschlangen verwenden. Die Beispiele sind in Ruby geschrieben und verwenden das Azure-Gem. Die Szenarien behandeln die Themen **Erstellen von Warteschlangen, Senden und Empfangen von Nachrichten** und **Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

## Was sind Service Bus-Warteschlangen?

Service Bus-Warteschlangen unterstützen ein Kommunikationsmodell namens *Brokermessaging*. Bei der Verwendung von Warteschlangen kommunizieren die Komponenten einer verteilten Anwendung nicht direkt miteinander, sondern tauschen Nachrichten über eine Warteschlange aus, die als Zwischenstufe fungiert. Ein Nachrichtenproducer (Absender) übergibt eine Nachricht an die Warteschlange und setzt seine Funktion fort. Ein Nachrichtenconsumer (Empfänger) ruft die Nachricht asynchron aus der Warteschlange ab und verarbeitet sie. Der Producer muss nicht auf eine Antwort vom Consumer warten, um seine Funktion fortzusetzen und weitere Nachrichten zu schicken. Warteschlangen liefern die Nachrichten im **First In, First Out (FIFO)**-Verfahren an einen oder mehrere Consumer. Die Nachrichten werden also normalerweise in der gleichen Reihenfolge von den Consumern empfangen und verarbeitet, wie sie in die Warteschlange übergeben wurden, und jede Nachricht wird nur von einem Consumer verarbeitet.

![Konzepte für Warteschlangen](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

Service Bus-Warteschlangen sind eine Allzwecktechnologie für viele unterschiedliche Szenarien:

-   Kommunikation zwischen Web- und Workerrollen in [Azure-Anwendungen mit mehreren Ebenen](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
-   Kommunikation zwischen lokalen Apps und von Azure gehosteten Apps in einer [Hybridlösung](service-bus-dotnet-hybrid-app-using-service-bus-relay.md)
-   Kommunikation zwischen Komponenten einer verteilten lokalen Anwendung, die in verschiedenen Organisationen oder Abteilungen einer Organisation laufen

Warteschlangen helfen bei der Skalierung Ihrer Anwendungen und führen zu einer robusteren Architektur.

## Erstellen eines Dienstnamespaces

Um mit der Verwendung von Service Bus-Warteschlangen in Azure beginnen zu können, müssen Sie zuerst einen Dienstnamespace erstellen. Ein Dienstnamespace ist eine Bereichseinheit zur Adressierung von Servicebus-Ressourcen innerhalb Ihrer Anwendung. Da der Namespace über das klassische Azure-Portal nicht mit einer ACS-Verbindung erstellt wird, müssen Sie den Namespace über die Befehlszeilenschnittstelle erstellen.

So erstellen Sie einen Dienstnamespace:

1. Öffnen Sie eine Azure PowerShell-Konsole.

2. Geben Sie den folgenden Befehl ein, um einen Service Bus-Namespace zu erstellen. Geben Sie einen eigenen Namespacewert und dieselbe Region wie für Ihre Anwendung an.

    ```
	New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

    ![Create Namespace](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)
	```

## Abrufen von Anmeldeinformationen zur Verwaltung des Namespace

Wenn Sie Verwaltungsvorgänge ausführen möchten, z. B. die Erstellung einer Warteschlange im neuen Namespace, müssen Sie die Anmeldeinformationen für den Namespace abrufen.

Das PowerShell-Cmdlet, das Sie zum Erstellen des Azure Service Bus-Namespaces ausgeführt haben, gibt den Schlüssel an, mit dem Sie den Namespace verwalten können. Kopieren Sie den Wert **DefaultKey**. Sie verwenden diesen Wert später in diesem Tutorial in Ihrem Code.

![Kopieren des Schlüssels](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

> [AZURE.NOTE]Sie können diesen Wert auch ermitteln, indem Sie sich beim [klassischen Azure-Portal](http://manage.windowsazure.com/) anmelden und zu den Verbindungsinformationen für Ihren Service Bus-Namespace navigieren.

## Erstellen einer Ruby-Anwendung

Erstellen Sie eine Ruby-Anwendung. Anweisungen finden Sie unter [Erstellen einer Ruby-Anwendung in Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus

Um Azure Service Bus zu verwenden, müssen Sie das Ruby-Azure-Paket, das eine Reihe von Bibliotheken enthält, die mit den Speicher-REST-Diensten kommunizieren, herunterladen und verwenden.

### Verwenden von RubyGems zum Abrufen des Pakets

1. Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).

2. Geben Sie "gem install azure" in das Befehlsfenster ein, um das Gem und Abhängigkeiten zu installieren.

### Importieren des Pakets

Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

```
require "azure"
```

## Einrichten einer Azure Service Bus-Verbindung

Das Azure-Modul liest die Umgebungsvariablen **AZURE\_SERVICEBUS\_NAMESPACE** und **AZURE\_SERVICEBUS\_ACCESS\_KEY** nach Informationen aus, die für eine Verbindung zu Ihrem Service Bus-Namespace benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::TableService** mit dem folgenden Code angeben:

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Legen Sie den Wert für den Namespace auf den von Ihnen erstellten Wert statt auf die gesamte URL fest. Verwenden Sie beispielsweise **Beispielnamespace**, anstelle von "Beispielnamespace.servicebus.windows.net".

## Erstellen von Warteschlangen

Das **Azure::ServiceBusService**-Objekt ermöglicht Ihnen, mit Warteschlangen zu arbeiten. Verwenden Sie die **create\_queue()**-Methode, um eine Warteschlange zu erstellen. Im folgenden Beispiel werden eine Warteschlange erstellt oder gegebenenfalls Fehler ausgegeben.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Sie können außerdem ein **Azure::ServiceBus::Queue**-Objekt mit weiteren Optionen übergeben, mit denen Sie die Standardeinstellungen wie z. B. Nachrichtenlebensdauer oder maximale Warteschlangengröße überschreiben können. Das folgende Beispiel zeigt, wie Sie die maximale Warteschlangengröße auf 5 GB bei einer Gültigkeitsdauer von 1 Minute festlegen:

```
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## Senden von Nachrichten an eine Warteschlange

Um eine Nachricht an eine Service Bus-Warteschlange zu senden, ruft Ihre Anwendung die **send\_queue\_message()**-Methode für das **Azure::ServiceBusService**-Objekt auf. Nachrichten, die an die Service Bus-Warteschlangen gesendet werden (und von diesen empfangen werden), sind **Azure::ServiceBus::BrokeredMessage**-Objekte und verfügen über einen Satz von Standardeigenschaften (z. B. **label** und **time\_to\_live**), ein Wörterbuch, in dem benutzerdefinierte anwendungsspezifische Eigenschaften enthalten sind, sowie einen Bestand beliebiger Anwendungsdaten. Eine Anwendung kann den Hauptteil einer Nachricht festlegen, indem sie einen Zeichenkettenwert als Nachricht weitergibt. Erforderliche Standardeigenschaften werden mit den Standardwerten gefüllt.

Das folgende Beispiel zeigt, wie eine Testnachricht an die Warteschlange "test\_queue" mithilfe von **send\_queue\_message()** gesendet wird:

```
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben). Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## Empfangen von Nachrichten aus einer Warteschlange

Nachrichten werden von einer Warteschlange über **receive\_queue\_message()**-Methode des **Azure::ServiceBusService**-Objekts empfangen. Standardmäßig werden Nachrichten gelesen und gesperrt, ohne aus der Warteschlange gelöscht zu werden. Sie können die Nachricht jedoch beim Lesen aus der Warteschlange löschen, indem Sie die Option **:peek\_lock** auf **false** setzen.

Im Standardverhalten sind Empfang und Löschung von Nachrichten zweistufig. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf der **delete\_queue\_message()**-Methode und Bereitstellen der Nachricht durch, die als Parameter gelöscht wird. Die **delete\_queue\_message()**-Methode markiert die Nachricht als verarbeitet, und entfernt sie aus der Warteschlange.

Wenn der **:peek\_lock**-Parameter auf **false** gesetzt ist, wird zum Lesen und Löschen der Nachricht das einfachste Modell verwendet. Dieses sollte für Anwendungen eingesetzt werden, die damit umgehen können, wenn Nachrichten bei Auftreten eines Fehlers nicht verarbeitet werden. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da die Nachricht von Service Bus als verarbeitet gekennzeichnet wurde, wird die vor dem Absturz verarbeitete Nachricht nicht berücksichtigt, wenn die Anwendung neu gestartet und die Verarbeitung von Nachrichten wieder aufgenommen wird.

Im folgenden Beispiel wird veranschaulicht, wie Nachrichten mithilfe von **receive\_queue\_message()** empfangen und verarbeitet werden. In diesem Beispiel wird zuerst eine Nachricht mit **:peek\_lock** gleich **false** empfangen und gelöscht, und anschließend eine Nachricht empfangen und mit **delete\_queue\_message()** gelöscht:

```
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die **unlock\_queue\_message()**-Methode des **Azure::ServiceBusService**-Objekts aufrufen. Dies führt dazu, dass Service Bus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Mit einer innerhalb der Warteschlange gesperrten Nachricht ist außerdem eine Zeitlimitüberschreitung verknüpft. Wenn die Anwendung die Nachricht nicht verarbeiten kann, bevor die Zeitlimitüberschreitung der Sperre abläuft (z. B. falls die Anwendung abstürzt), so entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, sodass sie wieder empfangen werden kann.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor Abrufen der **delete\_queue\_message()**-Methode abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **message\_id**-Eigenschaft der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

## Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Servicebus-Warteschlangen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Übersicht über [Warteschlangen, Themen und Abonnements](service-bus-queues-topics-subscriptions.md).
-   Besuchen Sie das [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby)-Repository auf GitHub.

Einen Vergleich zwischen den in diesem Artikel besprochenen Azure Service Bus-Warteschlangen und den unter [Verwenden des Azure-Warteschlangendiensts](/develop/ruby/how-to-guides/queue-service/) erörterten Azure-Warteschlangen finden Sie unter [Azure-Warteschlangen und Service Bus-Warteschlangen – Vergleich und Gegenüberstellung](service-bus-azure-and-service-bus-queues-compared-contrasted.md).
 

<!---HONumber=AcomDC_1217_2015-->