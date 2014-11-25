<properties linkid="dev-ruby-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Ruby) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Ruby" description="Learn how to use Service Bus queues in Azure. Code samples written in Ruby." metaCanonical="" services="service-bus" documentationCenter="Ruby" title="How to Use Service Bus Queues" authors="guayan" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# Einsatz von Servicebus-Warteschlangen

In diesem Leitfaden erfahren Sie, wie Sie die Servicebus-Warteschlangen verwenden können. Die Beispiele sind
in Ruby geschrieben und verwenden das Azure-Gem. Die Szenarien
behandeln die Themen **Erstellen von Warteschlangen, Senden und Empfangen von
Nachrichten** und **Löschen von Warteschlangen**. Weitere Informationen zu Warteschlangen finden Sie im Abschnitt [Nächste Schritte][Nächste Schritte].

## Inhaltsverzeichnis

-   [Was sind Servicebus-Warteschlangen?][Was sind Servicebus-Warteschlangen?]
-   [Erstellen eines Dienstnamespace][Erstellen eines Dienstnamespace]
-   [Abrufen der Standard-Anmeldeinformationen für den Namespace][Abrufen der Standard-Anmeldeinformationen für den Namespace]
-   [Erstellen einer Ruby-Anwendung][Erstellen einer Ruby-Anwendung]
-   [Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus][Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus]
-   [Einrichten einer Azure-Servicebus-Verbindung][Einrichten einer Azure-Servicebus-Verbindung]
-   [Erstellen einer Warteschlange][Erstellen einer Warteschlange]
-   [Senden von Nachrichten an eine Warteschlange][Senden von Nachrichten an eine Warteschlange]
-   [Empfangen von Nachrichten aus einer Warteschlange][Empfangen von Nachrichten aus einer Warteschlange]
-   [Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten][Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]
-   [Nächste Schritte][Nächste Schritte]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <span id="create-a-ruby-application"></span></a>Erstellen einer Ruby-Anwendung

Erstellen Sie eine Ruby-Anwendung. Anweisungen finden Sie im Thema zum [Erstellen einer Ruby-Anwendung in Azure][Erstellen einer Ruby-Anwendung in Azure].

## <span id="configure-your-application-to-use-service-bus"></span></a> Konfigurieren der Anwendung zur Verwendung eines Service Bus

Um den Azure-Servicebus zu verwenden, müssen Sie das Ruby-Azure-Paket, das eine Reihe von Bibliotheken enthält, die mit den Speicher-REST-Diensten kommunizieren, herunterladen und verwenden.

### Verwenden von RubyGems zum Abrufen des Pakets

1.  Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).

2.  Geben Sie "gem install azure" in das Befehlsfenster ein, um das Gem und Abhängigkeiten zu installieren.

### Importieren des Pakets

Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

    require "azure"

## <span id="setup-a-windows-azure-service-bus-connection"></span></a>Einrichten einer Azure Service Bus-Verbindung

Das Azure-Modul liest die Umgebungsvariablen **AZURE\_STORAGE\_ACCOUNT** und **AZURE\_STORAGE\_ACCESS\_KEY**
 nach Informationen aus, die für eine Verbindung zum Azure-Speicherkonto benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::TableService** mit dem folgenden Code angeben:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

## <span id="how-to-create-a-queue"></span></a>Erstellen einer Warteschlange

Das **Azure::ServiceBusService**ermöglicht Ihnen das Arbeiten mit Warteschlangen. Verwenden Sie die **create\_queue()**-Methode, um eine Warteschlange zu erstellen. Im folgenden Beispiel wird eine Warteschlange erstellt oder ggf. ein Fehler ausgegeben.

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

## <span id="how-to-send-messages-to-a-queue"></span></a>Senden von Nachrichten an eine Warteschlange

Um eine Nachricht an eine Servicebus-Warteschlange zu senden, ruft Ihre Anwendung die **send\_queue\_message()**-Methode auf dem **Azure::ServiceBusService**-Objekt auf. Nachrichten, die an die Servicebus-Warteschlangen gesendet werden (und von diesen empfangen werden), sind **Azure::ServiceBus::BrokeredMessage**-Objekte und verfügen über einen Satz von Standardeigenschaften (z. B. **label** und **time\_to\_live**), ein Wörterbuch, in dem benutzerdefinierte anwendungsspezifische Eigenschaften enthalten sind, sowie einen Bestand beliebiger Anwendungsdaten. Eine Anwendung kann den Hauptteil einer Nachricht festlegen, indem sie einen Zeichenkettenwert als Nachricht weitergibt. Erforderliche Standardeigenschaften werden mit den Standardwerten gefüllt.

Das folgende Beispiel zeigt, wie eine Testnachricht an die Warteschlange "test\_queue" mithilfe von **send\_queue\_message()** gesendet wird:

    message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
    message.correlation_id = "test-correlation-id"
    azure_service_bus_service.send_queue_message("test-queue", message)

Servicebus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben). Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## <span id="how-to-receive-messages-from-a-queue"></span></a>Empfangen von Nachrichten aus einer Warteschlange

Nachrichten werden von einer Warteschlange über **receive\_queue\_message()**-Methode des **Azure::ServiceBusService**-Objekts empfangen. Standardmäßig werden Nachrichten gelesen und gesperrt, ohne aus der Warteschlange gelöscht zu werden. Sie können die Nachricht jedoch beim Lesen aus der Warteschlange löschen, indem Sie die Option **:peek\_lock** auf **false** setzen.

Im Standardverhalten ist sind Empfang und Löschung von Nachrichten zweistufig. Dadurch können Anwendungen unterstützt werden, die fehlende Nachrichten nicht zulassen können. Wenn Servicebus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf der **delete\_queue\_message()**-Methode und Bereitstellen der Nachricht durch, die als Parameter gelöscht wird. Die **delete\_queue\_message()**-Methode markiert die Nachricht als verarbeitet, und entfernt sie aus der Warteschlange.

Wenn der **:peek\_lock**-Parameter auf **false** gesetzt ist, wird zum Lesen und Löschen der Nachricht das einfachste Modell verwendet. Dieses sollte für Anwendungen eingesetzt werden, die damit umgehen können, wenn Nachrichten bei Auftreten eines Fehlers nicht verarbeitet werden. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Servicebus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Das Beispiel unten zeigt, wie Nachrichten mithilfe von **receive\_queue\_message()** empfangen und verarbeitet werden können. In diesem Beispiel wird zuerst eine Nachricht mit **:peek\_lock** gleich **false** empfangen und gelöscht, und anschließend eine Nachricht empfangen und mit **delete\_queue\_message()** gelöscht:

    message = azure_service_bus_service.receive_queue_message("test-queue", 
      { :peek_lock => false })
    message = azure_service_bus_service.receive_queue_message("test-queue")
    azure_service_bus_service.delete_queue_message(message)

## <span id="how-to-handle-application-crashes-and-unreadable-messages"></span></a> Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten

Servicebus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die **unlock\_queue\_message()**-Methode des **Azure::ServiceBusService**-Objekts aufrufen. Dies führt dazu, dass Servicebus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Zudem wird einer in der Warteschlange gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt), entsperrt Servicebus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor Abrufen der **delete\_queue\_message()**-Methode abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung in dem Szenario nicht zulässig ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **message\_id**-Eigenschaft der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

## <span id="next-steps"></span></a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Servicebus-Warteschlangen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements][Warteschlangen, Themen und Abonnements]
-   Besuchen Sie das [Azure SDK for Ruby][Azure SDK for Ruby]-Repository auf GitHub

Einen Vergleich zwischen den in diesem Artikel besprochenen Azure Servicebus-Warteschlangen und den unter [Verwenden des Azure-Warteschlangendiensts][Verwenden des Azure-Warteschlangendiensts] besprochenen Azure-Warteschlangen finden sie unter [Azure-Warteschlangen und Azure Servicevus-Warteschlangen - Vergleich und Gegenüberstellung][Azure-Warteschlangen und Azure Servicevus-Warteschlangen - Vergleich und Gegenüberstellung]

  [Nächste Schritte]: #next-steps
  [Erstellen eines Dienstnamespace]: #create-a-service-namespace
  [Abrufen der Standard-Anmeldeinformationen für den Namespace]: #obtain-default-credentials
  [Erstellen einer Ruby-Anwendung]: #create-a-ruby-application
  [Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus]: #configure-your-application-to-use-service-bus
  [Einrichten einer Azure-Servicebus-Verbindung]: #setup-a-windows-azure-service-bus-connection
  [Erstellen einer Warteschlange]: #how-to-create-a-queue
  [Senden von Nachrichten an eine Warteschlange]: #how-to-send-messages-to-a-queue
  [Empfangen von Nachrichten aus einer Warteschlange]: #how-to-receive-messages-from-a-queue
  [Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #how-to-handle-application-crashes-and-unreadable-messages
  [Erstellen einer Ruby-Anwendung in Azure]: /de-de/develop/ruby/tutorials/web-app-with-linux-vm/
  [Warteschlangen, Themen und Abonnements]: http://msdn.microsoft.com/de-de/library/windowsazure/hh367516.aspx
  [Azure SDK for Ruby]: https://github.com/WindowsAzure/azure-sdk-for-ruby
  [Verwenden des Azure-Warteschlangendiensts]: /de-de/develop/ruby/how-to-guides/queue-service/
  [Azure-Warteschlangen und Azure Servicevus-Warteschlangen - Vergleich und Gegenüberstellung]: http://msdn.microsoft.com/de-de/library/windowsazure/hh767287.aspx
