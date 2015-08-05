<properties
	pageTitle="Verwenden von Service Bus-Warteschlangen (Ruby) - Azure"
	description="Erfahren Sie mehr über die Verwendung von Service Bus-Warteschlangen in Azure. Die Codebeispiele wurden in Ruby geschrieben."
	services="service-bus"
	documentationCenter="ruby"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="03/20/2015"
	ms.author="tomfitz"/>




# Verwenden von Service Bus-Warteschlangen

In diesem Leitfaden erfahren Sie, wie Sie die Servicebus-Warteschlangen verwenden können. Die Beispiele sind in Ruby geschrieben und verwenden das Azure-Gem. Die Szenarien behandeln die Themen **Erstellen von Warteschlangen, Senden und Empfangen von Nachrichten** und **Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte](#next-steps).

## Was sind Service Bus-Warteschlangen?

Servicebus-Warteschlangen unterstützen ein Modell für **verwaltete Messaging-Kommunikation**. Bei der Verwendung von Warteschlangen kommunizieren die Komponenten einer verteilten Anwendung nicht direkt miteinander, sondern tauschen Nachrichten über eine Warteschlange aus, die als Zwischenstufe fungiert. Ein Nachrichtenproducer (Absender) übergibt eine Nachricht an die Warteschlange und setzt seine Funktion fort. Ein Nachrichtenconsumer (Empfänger) ruft die Nachricht asynchron aus der Warteschlange ab und verarbeitet sie. Der Producer muss nicht auf eine Antwort vom Consumer warten, um seine Funktion fortzusetzen und weitere Nachrichten zu schicken. Warteschlangen liefern die Nachrichten im **First In, First Out (FIFO)**-Verfahren an einen oder mehrere Consumer. Die Nachrichten werden also normalerweise in der gleichen Reihenfolge von den Consumern empfangen und verarbeitet, wie sie in die Warteschlange übergeben wurden, und jede Nachricht wird nur von einem Consumer verarbeitet.

![Konzepte für Warteschlangen](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

Service Bus-Warteschlangen sind eine Allzwecktechnologie für viele unterschiedliche Szenarien:

-   Kommunikation zwischen Web- und Workerrollen in Azure-Anwendungen mit mehreren Ebenen
-   Kommunikation zwischen lokalen Anwendungen und Azure-Anwendungen in einer Hybridlösung
-   Kommunikation zwischen Komponenten einer verteilten lokalen Anwendung, die in verschiedenen Organisationen oder Abteilungen einer Organisation laufen

Warteschlangen helfen bei der Skalierung Ihrer Anwendungen und führen zu einer robusteren Architektur.

## Erstellen eines Dienstnamespaces
Um mit der Verwendung von Service Bus-Warteschlangen in Azure beginnen zu können, müssen Sie zuerst einen Dienstnamespace erstellen. Ein Dienstnamespace ist eine Bereichseinheit zur Adressierung von Service Bus-Ressourcen innerhalb Ihrer Anwendung. Da Service Bus vom Portal nicht mit einer ACS-Verbindung erstellt wird, müssen Sie den Namespace über die Befehlszeilenschnittstelle erstellen.

So erstellen Sie einen Dienstnamespace:

1. Öffnen Sie eine Azure PowerShell-Konsole.

2. Geben Sie den Befehl zum Erstellen eines Azure Service Bus-Namespaces ein, wie unten dargestellt. Geben Sie einen eigenen Namespacewert und dieselbe Region wie für Ihre Anwendung an.

    New-AzureSBNamespace -Name 'Beispielnamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

    ![Erstellen des Namespaces](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)

## Abrufen von Anmeldeinformationen zur Verwaltung des Namespace
Wenn Sie Verwaltungsvorgänge ausführen möchten, z. B. die Erstellung einer Warteschlange im neuen Namespace, müssen Sie die Anmeldeinformationen für den Namespace abrufen.

Das PowerShell-Cmdlet, das Sie zum Erstellen des Azure Service Bus-Namespaces ausgeführt haben, gibt den Schlüssel an, mit dem Sie den Namespace verwalten können. Kopieren Sie den Wert **DefaultKey**. Sie verwenden diesen Wert später in diesem Lernprogramm in Ihrem Code.

![Kopieren des Schlüssels](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

> [AZURE.NOTE]Sie finden diesen Schlüssel auch, wenn Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com/) anmelden und zu den Verbindungsinformationen für den Service Bus-Namespace navigieren.

## Erstellen einer Ruby-Anwendung

Erstellen Sie eine Ruby-Anwendung. Anweisungen finden Sie unter [Erstellen einer Ruby-Anwendung in Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus

Um den Azure-Servicebus zu verwenden, müssen Sie das Ruby-Azure-Paket, das eine Reihe von Bibliotheken enthält, die mit den Speicher-REST-Diensten kommunizieren, herunterladen und verwenden.

### Verwenden von RubyGems zum Abrufen des Pakets

1. Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).

2. Geben Sie "gem install azure" in das Befehlsfenster ein, um das Gem und Abhängigkeiten zu installieren.

### Importieren des Pakets

Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

    require "azure"

## Einrichten einer Azure Service Bus-Verbindung

Das Azure-Modul liest die Umgebungsvariablen **AZURE_STORAGE_ACCOUNT** und **AZURE_STORAGE_ACCESS_KEY** nach Informationen aus, die für eine Verbindung zum Azure-Speicherkonto benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::TableService** mit dem folgenden Code angeben:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

Legen Sie den Wert für den Service Bus-Namespace auf den von Ihnen erstellten Wert statt auf die gesamte URL fest. Verwenden Sie beispielsweise **Beispielnamespace**, anstelle von "Beispielnamespace.servicebus.windows.net".

## Erstellen von Warteschlangen

Das **Azure::ServiceBusService**ermöglicht Ihnen das Arbeiten mit Warteschlangen. Verwenden Sie die **create_queue()**-Methode, um eine Warteschlange zu erstellen. Im folgenden Beispiel wird eine Warteschlange erstellt oder ggf. ein Fehler ausgegeben.

    azure_service_bus_service = Azure::ServiceBusService.new
    begin
      queue = azure_service_bus_service.create_queue("test-queue")
    rescue
      puts $!
    end

Sie können außerdem ein **Azure::ServiceBus::Queue**-Objekt mit weiteren Optionen übergeben, mit denen Sie die Standardeinstellungen wie z. B. Nachrichtenlebensdauer oder maximale Warteschlangengröße überschreiben können. Das folgende Beispiel zeigt, wie Sie die maximale Warteschlangengröße auf 5 GB bei einer Gültigkeitsdauer von 1 Minute festlegen:

    queue = Azure::ServiceBus::Queue.new("test-queue")
    queue.max_size_in_megabytes = 5120
    queue.default_message_time_to_live = "PT1M"

    queue = azure_service_bus_service.create_queue(queue)

## Senden von Nachrichten an eine Warteschlange

Um eine Nachricht an eine Service Bus-Warteschlange zu senden, ruft Ihre Anwendung die **send_queue_message()**-Methode für das **Azure::ServiceBusService**-Objekt auf. Nachrichten, die an die Servicebus-Warteschlangen gesendet werden (und von diesen empfangen werden), sind **Azure::ServiceBus::BrokeredMessage**-Objekte und verfügen über einen Satz von Standardeigenschaften (z. B. **label** und **time_to_live**), ein Wörterbuch, in dem benutzerdefinierte anwendungsspezifische Eigenschaften enthalten sind, sowie einen Bestand beliebiger Anwendungsdaten. Eine Anwendung kann den Hauptteil einer Nachricht festlegen, indem sie einen Zeichenkettenwert als Nachricht weitergibt. Erforderliche Standardeigenschaften werden mit den Standardwerten gefüllt.

Das folgende Beispiel zeigt, wie eine Testnachricht an die Warteschlange "test_queue" mithilfe von **send_queue_message()** gesendet wird:

    message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
    message.correlation_id = "test-correlation-id"
    azure_service_bus_service.send_queue_message("test-queue", message)

Servicebus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben). Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## Empfangen von Nachrichten aus einer Warteschlange

Nachrichten werden von einer Warteschlange über **receive_queue_message()**-Methode des **Azure::ServiceBusService**-Objekts empfangen. Standardmäßig werden Nachrichten gelesen und gesperrt, ohne aus der Warteschlange gelöscht zu werden. Sie können die Nachricht jedoch beim Lesen aus der Warteschlange löschen, indem Sie die Option **:peek_lock** auf **false** setzen.

Im Standardverhalten ist sind Empfang und Löschung von Nachrichten zweistufig. Dadurch können Anwendungen unterstützt werden, die fehlende Nachrichten nicht zulassen können. Wenn Servicebus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf der **delete_queue_message()**-Methode und Bereitstellen der Nachricht durch, die als Parameter gelöscht wird. Die **delete_queue_message()**-Methode markiert die Nachricht als verarbeitet, und entfernt sie aus der Warteschlange.

Wenn der **:peek_lock**-Parameter auf **false** gesetzt ist, wird zum Lesen und Löschen der Nachricht das einfachste Modell verwendet. Dieses sollte für Anwendungen eingesetzt werden, die damit umgehen können, wenn Nachrichten bei Auftreten eines Fehlers nicht verarbeitet werden. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Servicebus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Das Beispiel unten zeigt, wie Nachrichten mithilfe von **receive_queue_message()** empfangen und verarbeitet werden können. In diesem Beispiel wird zuerst eine Nachricht mit **:peek_lock** gleich **false** empfangen und gelöscht, und anschließend eine Nachricht empfangen und mit **delete_queue_message()** gelöscht:

    message = azure_service_bus_service.receive_queue_message("test-queue",
	  { :peek_lock => false })
    message = azure_service_bus_service.receive_queue_message("test-queue")
    azure_service_bus_service.delete_queue_message(message)

## Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten

Servicebus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die **unlock_queue_message()**-Methode des **Azure::ServiceBusService**-Objekts aufrufen. Dies führt dazu, dass Servicebus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Zudem wird einer in der Warteschlange gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt), entsperrt Servicebus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor Abrufen der **delete_queue_message()**-Methode abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **message_id**-Eigenschaft der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

## Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Servicebus-Warteschlangen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements](http://msdn.microsoft.com/library/windowsazure/hh367516.aspx).
-   Besuchen Sie das [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby)-Repository auf GitHub

Einen Vergleich zwischen den in diesem Artikel besprochenen Azure Servicebus-Warteschlangen und den unter [Verwenden des Azure-Warteschlangendiensts](/develop/ruby/how-to-guides/queue-service/) besprochenen Azure-Warteschlangen finden sie unter [Azure-Warteschlangen und Azure Servicevus-Warteschlangen - Vergleich und Gegenüberstellung](http://msdn.microsoft.com/library/windowsazure/hh767287.aspx)
 

<!---HONumber=July15_HO4-->