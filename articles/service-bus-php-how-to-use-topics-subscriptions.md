<properties urlDisplayName="Service Bus Topics" pageTitle="Verwenden von Service Bus-Themen (PHP) - Azure" metaKeywords="" description="Learn how to use Service Bus topics with PHP in Azure." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Topics/Subscriptions" authors="sethm" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/13/2014" ms.author="sethm" />


# Verwenden von Servicebus-Themen und -Abonnements

In diesem Leitfaden erfahren Sie, wie Sie Service Bus-Themen und -Abonnements verwenden. Die Beispiele wurden in PHP geschrieben und verwenden das [Azure-SDK für PHP][download-sdk]. Die behandelten Szenarien umfassen das **Erstellen von Themen und Abonnements**, das **Erstellen von Abonnementfiltern**, das **Senden von Nachrichten an ein Thema**, das **Empfangen von Nachrichten von einem Abonnement** und das **Löschen von Themen und Abonnements**.

## Inhaltsverzeichnis

-   [Was sind Service Bus-Themen und -Abonnements?](#what-are-service-bus-topics)
-   [Erstellen eines Dienstnamespace](#create-a-service-namespace)
-   [Abrufen der Standard-Anmeldeinformationen für den Namespace](#obtain-default-credentials)
- 	[Erstellen einer PHP-Anwendung](#CreateApplication)
-	[Abrufen der Azure-Clientbibliotheken](#GetClientLibrary)
-   [Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus](#ConfigureApp)
-   [Gewusst wie: Erstellen eines Themas](#CreateTopic)
-   [Gewusst wie: Erstellen eines Abonnements](#CreateSubscription)
-   [Gewusst wie: Senden von Nachrichten an ein Thema](#SendMessage)
-   [Gewusst wie: Empfangen von Nachrichten aus einem Abonnement](#ReceiveMessages)
-   [Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten](#HandleCrashes)
-   [Gewusst wie: Löschen von Themen und Abonnements](#DeleteTopicsAndSubscriptions)
-   [Nächste Schritte](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

##<a id="CreateApplication"></a>Erstellen einer PHP-Anwendung

Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung, die auf den Azure-BLOB-Dienst zugreift, ist das Referenzieren von Klassen im [Azure-SDK für PHP][download-sdk] aus dem Code heraus. Sie können die Anwendung mit beliebigen Entwicklungstools erstellen, unter anderem auch mit Notepad.

> [WACOM.NOTE]
> In Ihrer PHP-Installation muss außerdem die <a href="http://php.net/openssl">OpenSSL-Erweiterung</a> installiert und aktiviert sein.

In diesem Leitfaden verwenden Sie Dienstfunktionen, die lokal innerhalb einer PHP-Anwendung oder im Code, der innerhalb einer Azure-Webrolle, -Workerrolle oder -Website ausgeführt wird, aufgerufen werden können.

##<a id="GetClientLibrary"></a>Abrufen der Azure-Clientbibliotheken

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureApp"></a>Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus

Um die APIs für Azure Service Bus-Themen zu verwenden, benötigen Sie Folgendes:

1. Verweisen Sie mithilfe der [require_once][require-once]-Anweisung auf die Autoloaderdatei und
2. Verweisen Sie auf alle Klassen, die Sie möglicherweise verwenden.

Das folgende Beispiel zeigt, wie die Autoloaderdatei eingeschlossen und die **ServiceBusService**-Klasse referenziert wird.

	> [WACOM.NOTE]
	> In diesem Beispiel (und in anderen Beispielen in diesem Artikel) wird angenommen, dass Sie die PHP-Clientbibliotheken für Azure über Composer installiert haben. Falls Sie die Bibliotheken manuell oder als PEAR-Paket installiert haben, müssen Sie auf die Autoloaderdatei <code>WindowsAzure.php</code> verweisen.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


In den Beispielen weiter unten wird die "require_once"-Anweisung immer angezeigt, aber nur die Klassen, die für die Ausführung des Beispiels erforderlich sind, werden referenziert.

##<a id="ConnectionString"></a>Einrichten einer Azure Service Bus-Verbindung

Um einen Azure Service Bus-Client zu instanzieren, benötigen Sie zunächst eine gültige Verbindungszeichenfolge mit dem folgenden Format:

	Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

Wobei der Endpunkt normalerweise das Format [https://yourNamespace.servicebus.windows.net] hat.

Um einen Azure-Dienstclient zu erstellen, müssen Sie die **ServicesBuilder**-Klasse verwenden. Sie können:

* Die Verbindungszeichenfolge direkt an die Klasse weitergeben oder
* den **CloudConfigurationManager (CCM)** verwenden, um mehrere externe Quellen für die Verbindungszeichenfolge zu überprüfen:
	* Standardmäßig verfügt sie über Unterstützung für eine externe Quelle - Umgebungsvariablen.
	* Sie können neue Quellen durch Erweitern der **ConnectionStringSource**-Klasse hinzufügen.

Für die hier erläuterten Beispiele wird die Verbindungszeichenfolge direkt weitergegeben.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	
	$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

##<a id="CreateTopic"></a>Gewusst wie: Erstellen eines Themas

Sie können Verwaltungsvorgänge für Service Bus-Themen über die
**ServiceBusRestProxy**-Klasse. Ein **ServiceBusRestProxy**-Objekt wird
über die **ServicesBuilder::createServiceBusService**-Factorymethode mit einer entsprechenden Verbindungszeichenfolge erstellt, welche die Token-Berechtigungen für deren Verwaltung kapselt.

Das Beispiel unten zeigt, wie Sie einen **ServiceBusRestProxy** instanziieren und **ServiceBusRestProxy->createTopic** aufrufen, um ein Thema namens "mytopic" in einem Dienstnamespace "MySBNamespace" zu erstellen:

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\TopicInfo;
	
	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{		
		// Create topic.
		$topicInfo = new TopicInfo("mytopic");
		$serviceBusRestProxy->createTopic($topicInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/de-de/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

	> [WACOM.NOTE]
	> You can use the <b>listTopics</b> method on <b>ServiceBusRestProxy</b>-Objekte, um zu überprüfen, ob ein Thema mit einem bestimmten Namen bereits innerhalb eines Dienstnamespace vorhanden ist.

##<a id="CreateSubscription"></a>Gewusst wie: Erstellen eines Abonnements

Themenabonnements werden ebenfalls mit der **ServiceBusRestProxy->createSubscription**-Methode erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übergeben werden.

### Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

**MatchAll** ist der Standardfilter, der verwendet wird, wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird. Wenn der Filter **MatchAll** verwendet wird, werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel wird ein Abonnement namens "mysubscription" erstellt, für das der Standardfilter **MatchAll** verwendet wird.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{
		// Create subscription.
		$subscriptionInfo = new SubscriptionInfo("mysubscription");
		$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/de-de/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

### Erstellen von Abonnements mit Filtern

Sie können auch Filter einrichten, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen. Der von Abonnements unterstützte flexibelste Filtertyp ist **SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen über SqlFilters finden Sie in der [Eigenschaft SqlFilter.SqlExpression][sqlfilter].

	> [WACOM.NOTE]
	> Jede Regel in einem Abonnement verarbeitet eingehende Nachrichten unabhängig und fügt ihre Ergebnisnachrichten zu dem Abonnement hinzu. Zusätzlich existiert für jedes neue Abonnement eine Standard-<b>Regel</b> mit einem Filter, der alle Nachrichten aus einem Thema zum Abonnement hinzufügt. Wenn Sie nur Nachrichten erhalten wollen, die auf Ihren Filter passen, müssen Sie die Standardregel entfernen. Sie können die Standardregel anhand der <b>ServiceBusRestProxy->deleteRule</b>-Methode entfernen.

Mit dem folgenden Beispiel wird ein Abonnement namens "HighMessages" mit einem **SqlFilter**-Filter erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte **MessageNumber**-Eigenschaft größer ist als 3 (Informationen zum Hinzufügen benutzerdefinierter Eigenschaften zu Nachrichten finden Sie unter [Gewusst wie: Senden von Nachrichten an ein Thema](#SendMessage)):

	$subscriptionInfo = new SubscriptionInfo("HighMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

  	$ruleInfo = new RuleInfo("HighMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber > 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);

Note that the code above requires the use of an additional namespace: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Similarly, the following example creates a subscription named "LowMessages" with a SqlFilter that only selects messages that have a MessageNumber property less than or equal to 3:

	$subscriptionInfo = new SubscriptionInfo("LowMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

  	$ruleInfo = new RuleInfo("LowMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber <= 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);

Wenn jetzt eine Nachricht an das Thema "mytopic"gesendet wird, wird diese nun stets an die Empfänger des Abonnements "mysubscription" zugestellt. Sie wird selektiv an die Empfänger der Themenabonnements "HighMessages" und "LowMessages" zugestellt (je nach Inhalt der Nachricht).

##<a id="SendMessage"></a>Gewusst wie: Senden von Nachrichten an ein Thema

Um eine Nachricht an ein Service Bus-Thema zu senden, ruft Ihre Anwendung die **ServiceBusRestProxy->sendTopicMessage**-Methode auf. Der folgende Code zeigt, wie Sie eine Nachricht an das oben im Dienstnamespace
"MySBNamespace" erstellte Thema "mytopic" senden können.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\BrokeredMessage;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message");
	
		// Send message.
		$serviceBusRestProxy->sendTopicMessage("mytopic", $message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/de-de/library/windowsazure/hh780775
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

An Service Bus-Themen gesendete Nachrichten sind Instanzen der **BrokeredMessage**-Klasse. **BrokeredMessage**-Objekte enthalten eine Reihe von Standardeigenschaften und -methoden (wie etwa **getLabel** und **getTimeToLive**, **setLabel** und **setTimeToLive**) sowie Eigenschaften für die Aufnahme benutzerdefinierter anwendungsspezifischer Eigenschaften. Im folgenden Beispiel sehen Sie, wie fünf Testnachrichten an das oben erstellte Thema "mytopic" gesendet werden. Die **setProperty**-Methode wird verwendet, um zu jeder Nachricht eine benutzerdefinierte Eigenschaft ("MessageNumber") hinzuzufügen. Beachten Sie, wie die Eigenschaft "MessageNumber" in jeder Nachricht variiert. (Auf diese Weise können Sie bestimmen, welche Abonnements sie erhalten. Siehe oben im Abschnitt [Gewusst wie: Ein Abonnement erstellen](#CreateSubscription)):

	for($i = 0; $i < 5; $i++){
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message ".$i);
			
		// Set custom property.
		$message->setProperty("MessageNumber", $i);
			
		// Send message.
		$serviceBusRestProxy->sendTopicMessage("mytopic", $message);
	}

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB (der Header, der die standardmäßigen und die benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben). Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Obergrenze für die Warteschlangengröße beträgt 5 GB.

##<a id="ReceiveMessages"></a>Gewusst wie: Empfangen von Nachrichten aus einem Abonnement

Der einfachste Weg zum Empfangen von Nachrichten aus einem Abonnement ist die **ServiceBusRestProxy->receiveSubscriptionMessage**-Methode. Empfangene Nachrichten können in zwei verschiedenen Modi verwendet werden: **ReceiveAndDelete** (Standard) und **PeekLock**.

Bei Verwendung des **ReceiveAndDelete**-Modus ist der Nachrichtenempfang ein Single-Shot-Vorgang. Dies bedeutet, wenn Service Bus eine Leseanforderung für eine Nachricht in einem Abonnement erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurück gesendet. **Der ReceiveAndDelete**-Modus ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Service Bus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Im **PeekLock**-Modus ist der Nachrichtenempfang zweistufig. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt sie die zweite Phase des Empfangsprozesses durch Aufruf von **ServiceBusRestProxy->deleteMessage** durch. Wenn Service Bus den **deleteMessage**-Aufruf erkennt, markiert er die Nachricht als verwendet und entfernt sie aus der Warteschlange.

Das folgende Beispiel zeigt, wie eine Nachricht mit dem nicht standardmäßig verwendeten **PeekLock**-Modus empfangen und verarbeitet werden kann. 

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Set receive mode to PeekLock (default is ReceiveAndDelete)
		$options = new ReceiveMessageOptions();
		$options->setPeekLock();
	
		// Get message.
		$message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", 
																	"mysubscription", 
																	$options);
		echo "Body: ".$message->getBody()."<br />";
		echo "MessageID: ".$message->getMessageId()."<br />";
		
		/*---------------------------
			Process message here.
		----------------------------*/
		
		// Delete message. Not necessary if peek lock is not set.
		echo "Deleting message...<br />";
		$serviceBusRestProxy->deleteMessage($message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/de-de/library/windowsazure/hh780735
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="HandleCrashes"></a>Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Ihnen bei der ordnungsgemäßen Behebung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht helfen. Wenn eine empfangene Anwendung eine Nachricht aus einem beliebigen Grund nicht verarbeiten kann, kann sie die **unlockMessage**-Methode für die empfangene Nachricht aufrufen (anstelle der **deleteMessage**-Methode). Dies führt dazu, dass Service Bus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Zudem wird einer in der Warteschlange gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht aber vor Ausgabe der Anforderung **deleteMessage** abstürzt, wird die Nachricht erneut an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** (Verarbeitung mindestens einmal) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung in dem Szenario nicht zulässig ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **getMessageId**-Methode der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

##<a id="DeleteTopicsAndSubscriptions"></a>Löschen von Themen und Abonnements

Verwenden Sie zum Löschen eines Themas oder Abonnements die **ServiceBusRestProxy->deleteTopic**-Methode bzw. die **ServiceBusRestProxy->deleteSubscripton**-Methode. Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind.

Im folgenden Beispiel wird gezeigt, wie ein Thema ("mytopic") und die darin registrierten Abonnements gelöscht werden.

    require_once 'vendor\autoload.php';

	use WindowsAzure\ServiceBus\ServiceBusService;
	use WindowsAzure\ServiceBus\ServiceBusSettings;
	use WindowsAzure\Common\ServiceException;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{		
		// Delete topic.
		$serviceBusRestProxy->deleteTopic("mytopic");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/de-de/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Wenn Sie die **deleteSubscription**-Methode verwenden, können Sie ein Abonnement unabhängig löschen:

	$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");

##<a id="NextSteps"></a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Service Bus-Warteschlangen vertraut sind, finden Sie weitere Informationen im MSDN-Thema [Warteschlangen, Themen und Abonnements][].

[download-sdk]: http://go.microsoft.com/fwlink/?LinkId=252473
[Was sind Service Bus-Themen und -Abonnements?]: #bkmk_WhatAreSvcBusTopics
[Erstellen eines Dienstnamespace]: #bkmk_CreateSvcNamespace
[Abrufen der Standard-Anmeldeinformationen für den Namespace]: #bkmk_ObtainDefaultMngmntCredentials
[Konfigurieren Ihrer Anwendung für die Verwendung von Servicebus]: #bkmk_ConfigYourApp
[Gewusst wie: Erstellen eines Themas]: #bkmk_HowToCreateTopic
[Gewusst wie: Erstellen von Abonnements]: #bkmk_HowToCreateSubscrip
[Gewusst wie: Senden von Nachrichten an ein Thema]: #bkmk_HowToSendMsgs
[Gewusst wie: Empfangen von Nachrichten aus einem Abonnement]: #bkmk_HowToReceiveMsgs
[Gewusst wie: Umgang mit Anwendungsabstürzen und nicht lesbaren Nachrichten]: #bkmk_HowToHandleAppCrash
[Gewusst wie: Löschen von Themen und Abonnements]: #bkmk_HowToDeleteTopics
[Nächste Schritte]: #bkmk_NextSteps
[Service Bus-Themendiagramm]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
[Azure-Verwaltungsportal]: http://manage.windowsazure.com/
[Screenshot des Service Bus-Knotens]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
[Screenshot des Erstellens eines neuen Namespace]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
[Screenshot der Namespaceliste]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
[Screenshot des Eigenschaftenbereichs]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
[Screenshot des Standardschlüssels]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
[Warteschlangen, Themen und Abonnements]: http://msdn.microsoft.com/de-de/library/windowsazure/hh367516.aspx
[Screenshot der verfügbaren Namespaces]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[sqlfilter]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx

[require-once]: http://php.net/require_once
