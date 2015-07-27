<properties
	pageTitle="Verwenden von Service Bus-Themen(Ruby) - Azure"
	description="Erfahren Sie mehr zur Verwendung von Service Bus-Themen und -Abonnements in Azure. Die Codebeispiele wurden für Ruby-Anwendungen geschrieben."
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





# Verwenden von Servicebus-Themen und -Abonnements

In diesem Leitfaden erfahren Sie, wie Sie Servicebus-Themen und -Abonnements in Ruby-Anwendungen verwenden. Die behandelten Szenarien umfassen das **Erstellen von Themen und Abonnements, Erstellen von Abonnementfiltern, Senden von Nachrichten** an ein Thema, das **Empfangen von Nachrichten von einem Abonnement** und das **Löschen von Themen und Abonnements**. Weitere Informationen zu Themen und Abonnements finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

## Was sind Service Bus-Themen und -Abonnements?

Servicebus-Themen und -Abonnements unterstützen ein Modell zum **Veröffentlichen/Abonnieren von Messaging-Kommunikationen**. Bei der Verwendung von Themen und Abonnements kommunizieren die Komponenten einer verteilten Anwendung nicht direkt miteinander, sondern tauschen Nachrichten über ein Thema aus, das als Zwischenstufe fungiert.

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

Anders als bei Servicebus-Warteschlangen, wo jede Nachricht von einem einzelnen Consumer verarbeitet wird, bieten Themen und Abonnements eine **1:n**-Kommunikationsform mit einem Veröffentlichungs- und Abonnementsmuster. Es ist möglich, mehrere Abonnements zu einem Thema anzumelden. Wenn eine Nachricht an ein Thema gesendet wird, steht sie in jedem Abonnement zur Verfügung, wo sie unabhängig von den anderen Abonnements verarbeitet wird.

Ein Themenabonnement ähnelt einer virtuellen Warteschlange, die Kopien der Nachrichten enthält, die an das Thema gesendet wurden. Sie können optional auch Filterregeln für einzelne Abonnements eines Themas anmelden. Auf diese Weise können Sie filtern/einschränken, welche Nachrichten an ein Thema von welchen Themenabonnements empfangen werden.

Mit Servicebus-Themen und -Abonnements können Sie sehr viele Nachrichten an sehr viele Benutzer und Anwendungen verarbeiten.

## Erstellen eines Dienstnamespaces

Um mit der Verwendung von Servicebus-Warteschlangen in Azure beginnen zu können, müssen Sie zuerst einen Dienstnamespace erstellen. Ein Dienstnamespace ist eine Bereichseinheit zur Adressierung von Service Bus-Ressourcen innerhalb Ihrer Anwendung. Da Service Bus vom Portal nicht mit einer ACS-Verbindung erstellt wird, müssen Sie den Namespace über die Befehlszeilenschnittstelle erstellen.

So erstellen Sie einen Namespace

1. Öffnen Sie eine Azure PowerShell-Konsole.

2. Geben Sie den Befehl zum Erstellen eines Azure Service Bus-Namespaces ein, wie unten dargestellt. Geben Sie einen eigenen Namespacewert und dieselbe Region wie für Ihre Anwendung an.

      New-AzureSBNamespace -Name 'Beispielnamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

      ![Erstellen des Namespaces](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## Abrufen der Standardverwaltungsanmeldeinformationen für den Namespace

Wenn Sie Verwaltungsvorgänge ausführen möchten, z. B. die Erstellung einer Warteschlange im neuen Namespace, müssen Sie die Anmeldeinformationen für den Namespace abrufen.

Das PowerShell-Cmdlet, das Sie zum Erstellen des Azure Service Bus-Namespaces ausgeführt haben, gibt den Schlüssel an, mit dem Sie den Namespace verwalten können. Kopieren Sie den Wert **DefaultKey**. Sie verwenden diesen Wert später in diesem Lernprogramm in Ihrem Code.

      ![Copy key](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [AZURE.NOTE]Sie finden diesen Schlüssel auch, wenn Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com/) anmelden und zu den Verbindungsinformationen für den Service Bus-Namespace navigieren.

## Erstellen einer Ruby-Anwendung

Erstellen Sie eine Ruby-Anwendung. Anweisungen finden Sie unter [Erstellen einer Ruby-Anwendung in Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus

Um den Azure-Servicebus zu verwenden, müssen Sie das Ruby-Azure-Paket, das eine Reihe von Bibliotheken enthält, die mit den Speicher-REST-Diensten kommunizieren, herunterladen und verwenden.

### Verwenden von RubyGems zum Abrufen des Pakets

1. Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).

2. Geben Sie "gem install azure" in das Befehlsfenster ein, um das Gem und Abhängigkeiten zu installieren.

### Importieren des Pakets

Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

    require "azure"

## Einrichten einer Azure-Servicebus-Verbindung

Das Azure-Modul liest die Umgebungsvariablen **AZURE_STORAGE_ACCOUNT** und **AZURE_STORAGE_ACCESS_KEY** nach Informationen aus, die für eine Verbindung zum Azure-Speicherkonto benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::TableService** mit dem folgenden Code angeben:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

Legen Sie den Wert für den Service Bus-Namespace auf den von Ihnen erstellten Wert statt auf die gesamte URL fest. Verwenden Sie beispielsweise **Beispielnamespace**, anstelle von "Beispielnamespace.servicebus.windows.net".

## Erstellen eines Themas

Das **Azure::ServiceBusService**ermöglicht Ihnen das Arbeiten mit Themen. Der folgende Code erstellt ein **Azure::ServiceBusService**-Objekt. Verwenden Sie die **create_topic()**-Methode, um ein Thema zu erstellen. Im folgenden Beispiel wird ein Thema erstellt oder ggf. ein Fehler ausgegeben.

	azure_service_bus_service = Azure::ServiceBusService.new
	begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

Sie können außerdem ein **Azure::ServiceBus::Topic**-Objekt mit weiteren Optionen übergeben, mit denen Sie die Standardthemeneinstellungen wie z. B. Nachrichtenlebensdauer oder maximale Warteschlangengröße überschreiben können. Das folgende Beispiel zeigt, wie Sie die maximale Warteschlangengröße auf 5 GB bei einer Gültigkeitsdauer von 1 Minute festlegen:

	topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## Erstellen von Abonnements

Themenabonnements werden ebenfalls mit dem **Azure::ServiceBusService**-Objekt erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übermittelt werden.

Abonnements sind persistent und bleiben erhalten, bis sie selbst oder die mit ihnen verbundenen Themen gelöscht werden. Wenn Ihre Anwendung Logik beinhaltet, sollte sie bei der Erstellung eines Abonnements zuerst mithilfe der getSubscription-Methode überprüfen, ob das Abonnement bereits vorhanden ist.

### Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

**MatchAll** ist der Standardfilter, der verwendet wird, wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird. Wenn der Filter **MatchAll** verwendet wird, werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel wird ein Abonnement namens "all-messages" erstellt, für das der Standardfilter **MatchAll** verwendet wird.

	subscription = azure_service_bus_service.create_subscription("test-topic",
	  "all-messages")

### <a id="how-to-create-subscriptions"></a>Erstellen von Abonnements mit Filtern

Sie können auch Filter einrichten, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist **Azure::ServiceBus::SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können, finden Sie in der Syntax [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx).

Sie können mithilfe der **create_rule()**-Methode des **Azure::ServiceBusService**-Objekts Filter zu einem Abonnement hinzufügen. Durch diese Methode können Sie neue Filter zu einem vorhandenen Abonnement hinzufügen.

Da der Standardfilter automatisch auf alle neuen Abonnements angewendet wird, müssen Sie zuerst den Standardfilter entfernen. Ansonsten überschreibt **MatchAll** alle Filter, die Sie angeben. Sie können die Standardregel mithilfe der **delete_rule()**-Methode des **Azure::ServiceBusService**-Objekts entfernen.

Mit dem folgenden Beispiel wird ein Abonnement namens "high-messages" mit einem **Azure::ServiceBus::SqlFilter**-Filter erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte **message_number**-Eigenschaft größer ist als 3:

	subscription = azure_service_bus_service.create_subscription("test-topic",
	  "high-messages")
	azure_service_bus_service.delete_rule("test-topic", "high-messages",
	  "$Default")

	rule = Azure::ServiceBus::Rule.new("high-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "high-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({
	  :sql_expression => "message_number > 3" })
	rule = azure_service_bus_service.create_rule(rule)

Ebenso erstellt das folgende Beispiel ein Abonnement namens "low-messages" mit einem **Azure::ServiceBus::SqlFilter**-Filter, der nur Nachrichten auswählt, deren **message_number**-Eigenschaft kleiner oder gleich 3 ist:

	subscription = azure_service_bus_service.create_subscription("test-topic",
	  "low-messages")
	azure_service_bus_service.delete_rule("test-topic", "low-messages",
	  "$Default")

	rule = Azure::ServiceBus::Rule.new("low-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "low-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({
	  :sql_expression => "message_number <= 3" })
	rule = azure_service_bus_service.create_rule(rule)

Wenn eine Nachricht an "test-topic" gesendet wird, wird diese nun stets an die Empfänger des Themenabonnements "all-messages" zugestellt. Sie wird selektiv an die Empfänger der Themenabonnements "high-messages" und "low-messages" zugestellt (je nach Inhalt der Nachricht).

## Senden von Nachrichten an ein Thema

Um eine Nachricht an ein Servicebus-Thema zu senden, muss die Anwendung die **send_topic_message()**-Methode des **Azure::ServiceBusService**-Objekts verwenden. Nachrichten, die an Servicebus-Themen gesendet werden, sind **Azure::ServiceBus::BrokeredMessage**-Objekte. **Azure::ServiceBus::BrokeredMessage**-Objekte weisen eine Reihe von Standardeigenschaften (wie etwa **label** und **time_to_live**), ein Wörterbuch zur Aufnahme benutzerdefinierter anwendungsspezifischer Eigenschaften und einen Körper mit Zeichenkettendaten auf. Eine Anwendung kann den Nachrichtenkörper festlegen, indem sie einen Zeichenkettenwert an **send_topic_message()** weitergibt. Erforderliche Standardeigenschaften werden mit den Standardwerten gefüllt.

Das folgende Beispiel zeigt, wie Sie fünf Testnachrichten an "test-topic" senden. Beachten Sie, dass der benutzerdefinierte **message_number**-Eigenschaftswert jeder Nachricht gemäß der Iteration der Schleife variiert (dadurch wird bestimmt, welches Abonnement die Nachricht erhält):

	5.times do |i|
	  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
	    { :message_number => i })
	  azure_service_bus_service.send_topic_message("test-topic", message)
	end

Servicebus-Themen unterstützen eine maximale Nachrichtengröße von 256 MB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 MB haben). Es gibt keine Beschränkung für die Anzahl der Nachrichten, die ein Thema enthält. Es gibt jedoch eine Obergrenze für die Gesamtgröße der Nachrichten eines Themas. Die Themengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## Empfangen von Nachrichten aus einem Abonnement

Nachrichten werden von einem Abonnement über die **receive_subscription_message()**-Methode auf dem **Azure::ServiceBusService**-Objekt empfangen. Standardmäßig werden Nachrichten gelesen(peak) und gesperrt, ohne aus dem Abonnement gelöscht zu werden. Sie können die Nachricht aus dem Abonnement löschen, indem Sie die Option **peek_lock** auf **false** setzen.

Im Standardverhalten ist sind Empfang und Löschung von Nachrichten zweistufig. Dadurch können Anwendungen unterstützt werden, die fehlende Nachrichten nicht zulassen können. Wenn Servicebus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufruf der **delete_subscription_message()**-Methode und Bereitstellen der Nachricht durch, die als Parameter gelöscht wird. Die **delete_subscription_message()**-Methode markiert die Nachricht als verarbeitet, und entfernt sie aus dem Abonnement.

Wenn der **:peek_lock**-Parameter auf **false** gesetzt ist, wird zum Lesen und Löschen der Nachricht das einfachste Modell verwendet. Dieses sollte für Anwendungen eingesetzt werden, die damit umgehen können, wenn Nachrichten bei Auftreten eines Fehlers nicht verarbeitet werden. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Servicebus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Das folgende Beispiel zeigt, wie Nachrichten mithilfe von **receive_subscription_message()** empfangen und verarbeitet werden können. In diesem Beispiel wird zuerst eine Nachricht des Abonnements "low-messages" mit **:peek_lock** gleich **false** empfangen und gelöscht, und anschließend eine Nachricht des Abonnements "high-messages" empfangen und mit **delete_subscription_message()** gelöscht:

    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

## Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten

Servicebus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die **unlock_subscription_message()**-Methode des **Azure::ServiceBusService**-Objekts aufrufen. Dies führt dazu, dass Servicebus die Nachricht innerhalb des Abonnements entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitenden Anwendung.

Zudem wird der im Abonnement gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel, wenn die Anwendung abstürzt), entsperrt Servicebus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor Abrufen der **delete_subscription_message()**-Methode abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **message_id**-Eigenschaft der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

## Löschen von Themen und Abonnements

Themen und Abonnements sind dauerhaft und müssen explizit über das [Azure-Verwaltungsportal](https://manage.windowsazure.com) oder programmgesteuert gelöscht werden. Das folgende Beispiel zeigt, wie Sie das Thema namens "test-topic" löschen.

	azure_service_bus_service.delete_topic("test-topic")

Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden. Der folgende Code zeigt, wie Sie ein Abonnement namens "high-messages" aus dem Thema "test-topic" löschen:

	azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Servicebus-Themen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements](http://msdn.microsoft.com/library/windowsazure/hh367516.aspx).
-   API-Referenz für [SqlFilter](http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx)
-	Besuchen Sie das [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby)-Repository auf GitHub
 

<!---HONumber=July15_HO3-->