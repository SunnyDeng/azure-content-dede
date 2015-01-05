<properties urlDisplayName="Service Bus Topics" pageTitle="Verwenden von Service Bus-Themen(Ruby) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic ruby" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Ruby applications." metaCanonical="" services="service-bus" documentationCenter="Ruby" title="How to Use Service Bus Topics/Subscriptions" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="11/25/2014" ms.author="tomfitz" />





# Verwenden von Servicebus-Themen und -Abonnements

In diesem Leitfaden erfahren Sie, wie Sie Servicebus-Themen und -Abonnements in Ruby-Anwendungen verwenden. Die behandelten Szenarien umfassen das **Erstellen von Themen und Abonnements, das Erstellen von Abonnementfiltern, das Senden von Nachrichten** an ein Thema, das **Empfangen von Nachrichten von einem Abonnement** und das **Löschen von Themen und Abonnements**. Im Abschnitt [Nächste Schritte](#NextSteps) finden Sie weitere Informationen zu Themen und Abonnements.

## Inhaltsverzeichnis
* [Was sind Service Bus-Themen und -Abonnements?](#what-are-service-bus-topics)
* [Erstellen eines Dienstnamespace](#create-a-service-namespace)
* [Abrufen der Standardanmeldeinformationen für den Namespace](#obtain-default-credentials)
* [Erstellen einer Ruby-Anwendung](#create-a-ruby-application)
* [Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus](#configure-your-application-to-use-service-bus)
* [Einrichten einer Azure Service Bus-Verbindung](#setup-a-windows-azure-service-bus-connection)
* [Erstellen eines Themas](#how-to-create-a-topic)
* [Erstellen von Abonnements](#how-to-create-subscriptions)
* [Senden von Nachrichten an ein Thema](#how-to-send-messages-to-a-topic)
* [Empfangen von Nachrichten von einem Abonnement](#how-to-receive-messages-from-a-subscription)
* [Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten](#how-to-handle-application-crashes-and-unreadable-messages)
* [Löschen von Themen und Abonnements](#how-to-delete-topics-and-subscriptions)
* [Nächste Schritte](#NextSteps)

## <a name="what-are-service-bus-topics"></a>Was sind Servicebus-Themen und -Abonnements?

Service Bus-Themen und -Abonnements unterstützen ein Modell zum **Veröffentlichen/Abonnieren von Messaging-Kommunikationen. Bei der Verwendung von Themen und Abonnements kommunizieren die Komponenten einer verteilten Anwendung nicht direkt miteinander, sondern tauschen Nachrichten über ein Thema aus, das als Zwischenstufe fungiert.

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

Anders als bei Service Bus-Warteschlangen, bei denen jede Nachricht von einem einzelnen Consumer verarbeitet wird, bieten Themen und Abonnements eine **1:n**-Kommunikationsform nach dem Muster Veröffentlichen/Abonnieren. Es ist möglich, mehrere Abonnements zu einem Thema anzumelden. Wenn eine Nachricht an ein Thema gesendet wird, steht sie in jedem Abonnement zur Verfügung, wo sie unabhängig von den anderen Abonnements verarbeitet wird.

Ein Themenabonnement ähnelt einer virtuellen Warteschlange, die Kopien der Nachrichten enthält, die an das Thema gesendet wurden. Sie können optional auch Filterregeln für einzelne Abonnements eines Themas anmelden. Auf diese Weise können Sie filtern/einschränken, welche Nachrichten an ein Thema von welchen Themenabonnements empfangen werden.

Mit Servicebus-Themen und -Abonnements können Sie sehr viele Nachrichten an sehr viele Benutzer und Anwendungen verarbeiten.

## <a id="create-a-service-namespace"</a>Create a Service Namespace

To begin using Service Bus queues in Azure, you must first create a service namespace. A service namespace provides a scoping container for addressing Service Bus resources within 
your application. You must create the namespace through the command-line interface because the Portal does not create the service bus with an ACS connection.

To create a namespace:

1. Open an Azure Powershell console.

2. Type the command to create an Azure service bus namespace as shown below. Provide your own namespace value and specify the same region as your application. 

      New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -CreateACSNamespace $true

      ![Create Namespace](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## <a id="obtain-default-credentials"></a>Abrufen der Standardverwaltungsanmeldeinformationen für den Namespace

Wenn Sie Verwaltungsvorgänge ausführen möchten, z. B. die Erstellung einer Warteschlange im neuen Namespace, müssen Sie die Anmeldeinformationen für den Namespace abrufen.

1. Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com/)an.

2. Wählen Sie den von Ihnen erstellten Service Bus-Namespace aus.

     ![Select namespace](./media/service-bus-ruby-how-to-use-topics-subscriptions/selectns.png)

3. Wählen Sie im unteren Bereich **Verbindungsinformationen** aus.

      ![Select connection](./media/service-bus-ruby-how-to-use-topics-subscriptions/selectconnection.png)

4. Kopieren Sie den Standardschlüssel. Sie werden diesen Wert in Ihrem Code verwenden.

       ![Copy key](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

## <a id="create-a-ruby-application"></a>Erstellen einer Ruby-Anwendung

Erstellen Sie eine Ruby-Anwendung. Im Thema [Erstellen einer Ruby-Anwendung in Azure](/de-de/develop/ruby/tutorials/web-app-with-linux-vm/)finden Sie entsprechende Anweisungen.

## <a id="configure-your-application-to-use-service-bus"></a>Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus

Um den Azure-Servicebus zu verwenden, müssen Sie das Ruby-Azure-Paket, das eine Reihe von Bibliotheken enthält, die mit den Speicher-REST-Diensten kommunizieren, herunterladen und verwenden.

### Verwenden von RubyGems zum Abrufen des Pakets

1. Verwenden Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).

2. Geben Sie "gem install azure" in das Befehlsfenster ein, um das Gem und Abhängigkeiten zu installieren.

### Importieren des Pakets

Fügen Sie mit Ihrem bevorzugten Texteditor Folgendes oben in die Ruby-Datei an der Stelle ein, an der Sie den Speicher verwenden möchten:

    require "azure"

## <a id="setup-a-windows-azure-service-bus-connection"></a>Einrichten einer Azure Service Bus-Verbindung

Das Azure-Modul entnimmt den Umgebungsvariablen **AZURE\_SERVICEBUS\_NAMESPACE** und **AZURE\_SERVICEBUS\_ACCESS\_KEY** 
die Informationen, die zum Herstellen einer Verbindung mit Ihrem Azure Service Bus-Namespace benötigt werden. Wenn diese Umgebungsvariablen nicht festgelegt werden, müssen Sie die Kontoinformationen vor dem Verwenden von **Azure::ServiceBusService** mit dem folgenden Code angeben:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

Legen Sie den Wert für den Service Bus-Namespace auf den von Ihnen erstellten Wert statt auf die gesamte URL fest. Verwenden Sie beispielsweise **Beispielnamespace**, nicht Beispielnamespace.servicebus.windows.net. 

## <a id="how-to-create-a-topic"></a>Erstellen eines Themas

Das Objekt **Azure::ServiceBusService** ermöglicht Ihnen das Arbeiten mit Themen. Der folgende Code erstellt ein **Azure::ServiceBusService**-Objekt. Zum Erstellen eines Themas verwenden Sie die Methode **create\_topic()**. Im folgenden Beispiel wird ein Thema erstellt oder ggf. ein Fehler ausgegeben.

	azure_service_bus_service = Azure::ServiceBusService.new
	begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

Sie können außerdem ein **Azure::ServiceBus::Topic**-Objekt mit weiteren Optionen übergeben, mit denen Sie die Standardthemeneinstellungen wie die Nachrichtenlebensdauer oder die maximale Warteschlangengröße überschreiben können. Das folgende Beispiel zeigt, wie Sie die maximale Warteschlangengröße auf 5 GB bei einer Gültigkeitsdauer von 1 Minute festlegen:

	topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## <a id="how-to-create-subscriptions"></a>Erstellen von Abonnements

Themenabonnements werden ebenfalls mit dem **Azure::ServiceBusService**-Objekt erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übergeben werden.

**Hinweis**
Abonnements sind dauerhaft und existieren solange, bis sie oder das mit ihnen verbundene Thema gelöscht werden. Wenn Ihre Anwendung Logik beinhaltet, sollte sie bei der Erstellung eines Abonnements zuerst mithilfe der getSubscription-Methode überprüfen, ob das Abonnement bereits vorhanden ist.

### Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

**MatchAll** ist der Standardfilter, der verwendet wird, wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird. Bei Verwendung des Filters **MatchAll** werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel wird ein Abonnement namens "all-messages" erstellt, für das der Standardfilter **MatchAll** verwendet wird.

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "all-messages")

### <a id="how-to-create-subscriptions"></a>Erstellen von Abonnements mit Filtern

Sie können auch Filter einrichten, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist **Azure::ServiceBus::SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können, stehen in der Syntax [SqlFilter.SqlExpression](http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) zur Verfügung.

Filter können mithilfe der **create\_rule()**-Methode des **Azure::ServiceBusService**-Objekts zu einem Abonnement hinzugefügt werden. Durch diese Methode können Sie neue Filter zu einem vorhandenen Abonnement hinzufügen.

**Hinweis**
Da der Standardfilter automatisch auf alle neuen Abonnements angewendet wird, müssen Sie zuerst den Standardfilter entfernen. Andernfalls überschreibt **MatchAll** alle anderen Filter, die Sie angeben. Sie können die Standardregel mithilfe der **delete\_rule()**-Methode des **Azure::ServiceBusService**-Objekts entfernen.

Mit den folgenden Beispielen wird ein Abonnement namens "high-messages" mit dem Filter **Azure::ServiceBus::SqlFilter** erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte **message\_number**-Eigenschaft größer als 3 ist:

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

Auf ähnliche Weise wird mit dem folgenden Beispiel ein Abonnement namens "low-messages" mit dem Filter **Azure::ServiceBus::SqlFilter** erstellt, der nur Nachrichten auswählt, deren **message_number**-Eigenschaft kleiner oder gleich 3 ist:

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

## <a id="how-to-send-messages-to-a-topic"></a>Senden von Nachrichten an ein Thema

Zum Senden einer Nachricht an ein Service Bus-Thema muss Ihre Anwendung die **send\_topic\_message()**-Methode des **Azure::ServiceBusService**-Objekts verwenden. Nachrichten, die an Service Bus-Themen gesendet werden, sind **Azure::ServiceBus::BrokeredMessage**-Objekte. **Azure::ServiceBus::BrokeredMessage**-Objekte weisen eine Reihe von Standardeigenschaften (wie **label** und **time\_to\_live**), ein Wörterbuch zur Aufnahme benutzerdefinierter anwendungsspezifischer Eigenschaften und einen Bestand von Zeichenfolgendaten auf. Eine Anwendung kann den Nachrichtenkörper festlegen, indem sie einen Zeichenkettenwert an die Methode **send\_topic\_message()** übergibt. Erforderliche Standardeigenschaften werden mit den Standardwerten gefüllt.

Das folgende Beispiel zeigt, wie Sie fünf Testnachrichten an "test-topic" senden. Beachten Sie, dass der benutzerdefinierte **message_number**-Eigenschaftswert jeder Nachricht gemäß der Iteration der Schleife variiert (dadurch wird bestimmt, welches Abonnement die Nachricht erhält):

	5.times do |i|
	  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i, 
	    { :message_number => i })
	  azure_service_bus_service.send_topic_message("test-topic", message)
	end

Servicebus-Themen unterstützen eine maximale Nachrichtengröße von 256 MB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 MB haben). Es gibt keine Beschränkung für die Anzahl der Nachrichten, die ein Thema enthält. Es gibt jedoch eine Obergrenze für die Gesamtgröße der Nachrichten eines Themas. Die Themengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## <a id="how-to-receive-messages-from-a-subscription"></a>Empfangen von Nachrichten aus einem Abonnement

Nachrichten werden von einem Abonnement über die Methode **receive\_subscription\_message()** auf dem **Azure::ServiceBusService**-Objekt empfangen. Standardmäßig werden Nachrichten gelesen(peak) und gesperrt, ohne aus dem Abonnement gelöscht zu werden. Sie können die Nachricht aus dem Abonnement lesen und löschen, indem Sie die Option **peek\_lock** auf **false** setzen.

Im Standardverhalten ist sind Empfang und Löschung von Nachrichten zweistufig. Dadurch können Anwendungen unterstützt werden, die fehlende Nachrichten nicht zulassen können. Wenn Servicebus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert) hat, führt Sie die zweite Phase des Empfangsprozesses durch Aufruf der Methode **delete\_subscription\_message()** und Bereitstellen der Nachricht durch, die als Parameter gelöscht wird. Die Methode **delete\_subscription\_message()** markiert die Nachricht als verarbeitet und entfernt sie aus dem Abonnement.

Wenn der Parameter **:peek\_lock** auf **false** gesetzt ist, wird zum Lesen und Löschen der Nachricht das einfachste Modell verwendet. Dieses sollte für Anwendungen eingesetzt werden, die damit umgehen können, wenn Nachrichten bei Auftreten eines Fehlers nicht verarbeitet werden. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Servicebus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Das folgende Beispiel zeigt, wie Nachrichten mithilfe von **receive\_subscription\_message()** empfangen und verarbeitet werden können. In diesem Beispiel wird zuerst eine Nachricht des Abonnements "low-messages" mit **:peek\_lock** gleich **false** empfangen und gelöscht und anschließend eine Nachricht des Abonnements "high-messages" empfangen und mit **delete\_subscription\_message()** gelöscht:

    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

## <a id="how-to-handle-application-crashes-and-unreadable-messages"></a>Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten

Servicebus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, kann sie die Methode **unlock\_subscription\_message()** des **Azure::ServiceBusService**-Objekts aufrufen. Dies führt dazu, dass Servicebus die Nachricht innerhalb des Abonnements entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitenden Anwendung.

Zudem wird der im Abonnement gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel, wenn die Anwendung abstürzt), entsperrt Servicebus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht aber vor Aufruf der **delete\_subscription\_message()**-Methode abstürzt, wird die Nachricht erneut an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung in dem Szenario nicht zulässig ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies wird häufig durch Verwendung der Eigenschaft **message\_id** der Nachricht erzielt, die über mehrere Zustellversuche hinweg konstant bleibt.

## <a id="how-to-delete-topics-and-subscriptions"></a>Löschen von Themen und Abonnements

Themen und Abonnements sind dauerhaft und müssen explizit über das [Azure-Verwaltungsportal](https://manage.windowsazure.com) oder programmgesteuert gelöscht werden. Das folgende Beispiel zeigt, wie Sie das Thema namens "test-topic" löschen.

	azure_service_bus_service.delete_topic("test-topic")

Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden. Der folgende Code zeigt, wie Sie ein Abonnement namens "high-messages" aus dem Thema "test-topic" löschen:

	azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## <a id="NextSteps"></a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Servicebus-Themen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

-   Weitere Informationen finden Sie in der MSDN-Referenz: [Warteschlangen, Themen und Abonnements](http://msdn.microsoft.com/de-de/library/windowsazure/hh367516.aspx)
-   API-Referenz für [SqlFilter](http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx)
-	Besuchen Sie das Repository [Azure SDK für Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) auf GitHub.

<!--HONumber=35.1-->
